---
title: "[标准库 | database/sql]"
date: 2019-10-14T02:02:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["golang"]
categories: ["golang/标准库"]
author: "facedamon"
---

# 概述
**`sql.DB`不是一个连接**，它是数据库的抽象接口。它可以根据driver打开关闭数据库，管理连接池。正在是哟个的连接被标记为繁忙，用完后回到连接池等待下次使用。所以，如果你没有把连接释放回连接池，会导致过多连接使用系统资源耗尽。

### 使用DB
1. 导入driver
这里使用的是mysql driver
```
import (
    "database/sql"
    _ "github.com/go-sql-driver/mysql"
)
```
2. 连接DB
```
func main(){
    db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/hello")
    if err != nil {
        log.Fatal(err)
    }
    defer db.close
}
```
`sql.Open`的第一个参数是Driver名称，第二个参数是driver连接数据库的信息，各个driver可能不同。DB不是连接，并且只有当需要时才会创建连接，如果想立即验证连接，需要用Ping()方法，如下：
```
err = db.Ping()
if err != nil {
    // do something here
}
```
sql.DB的设计就是用来作为长连接使用的。不需要频繁的Open，Close。比较好的做法是，为每个不同的datastore建一个DB对象。保持这些对象Open。`如果需要短连接，那么把DB作为参数传入function，而不是在function中Open，Close。`

### 读取DB
如果方法包含Query，那么这个方法是用于查询并返回rows的。其它情况用该用Exec().
```
var (
    id int
    name string
)
rows, err := db.Query("select id, name from users where id = ?", 1)
if err != nil {
    log.Fatal(err)
}
defer rows.Close
for rows.Next(){
    err := rows.Scan(&id, &name)
    if err != nil {
        log.Fatal(err)
    }
    log.Println(id, name)
}
err = rows.Err()
if err != nil {
    log.Fatal(err)
}
```
上面代码的过程为：`db.Query()`表示向数据库发送一个query，`defer rows.close()`非常重要，遍历rows使用rows.Next(),把遍历到的数据存入变量使用rows.Scan,在遍历完成后检查error。有几点需要注意：

1. 检查遍历是否有error
2. 结果集rows未关闭前，底层的连接处于繁忙状态。当遍历读到最后一条记录时，会发生一个内部EOF错误，自动调用rows.close，但是如果提前退出循环，rows不会关闭，连接不会回到连接池。所以手动关闭非常重要。**rows.close可以多次调用，是无害操作。**

### 单行Query
err在Scan后才产生，所以可以如下写：
```
var name string
err = db.QueryRow("select name from users where id = ?", 1).Scan(&name)
if err != nil {
    log.Fatal(err)
}
fmt.Println(name)
```
### 修改数据，事务
**一般用Prepared Statements和Exec完成Insert，Update， Delete操作。**
```
stmt, err := db.Prepare("insert into users(name) values(?)")
if err != nil {
    log.Fatal(err)
}
res, err := stmt.Exec("Dolly")
if err != nil {
    log.Fatal(err)
}
lastId, err := res.LastInsertId()
if err != nil {
    log.Fatal(err)
}
rowCount, err := res.RowsAffected()
if err != nil {
    log.Fatal(err)
}
log.Printf("ID =  %d, affected = %d\n", lastId, rowCount)
```
### 事务
`db.Begin`开始事务，`Commit`或`Rollback关闭事务`。`Tx`从连接池中取出一个连接，在关闭之前都是使用这个连接。Tx不能和DB层的BEGIN，COMMIT混合使用。

如果你需要通过多条语句修改连接状态，你必须使用Tx，例如：

- 创建仅对单个连接可见的临时表
- 设置变量，例如 SET @var := somevalue
- 改变连接选项，例如字符集，超时

### PrePared Statements
在数据库层面，Prepared Statements是和单个数据库连接绑定的。客户端发送一个占位符的statement到服务器，服务器返回一个statement ID， 然后客户端发送ID和参数来执行statement。

在Go中，连接不直接暴露，你不能为连接绑定statement，而是只能为DB或Tx绑定。database/sql包有自动重试功能。当你生成一个Prepared Statement

1. 自动在连接池中绑定一个到空闲连接
2. Stmt对象记住绑定了哪个连接
3. 执行Stmt时，尝试使用该连接。如果不可以，例如连接被关闭或繁忙中，会自动re-prepare,绑定到另一个连接。

这就导致在高并发的场景，过度使用statement可能导致statement泄漏，statement持续重复prepare和re-prepare的过程，甚至会达到服务器端statement数量上限。

某些操作使用了PS，例如：db.Query(sql, param1,param2)冰鞋在最后自动关闭statement，有些场景不适合statement： 

1. 数据库不支持，例如：Sphinx, MemSql.它们支持Mysql wire protocol，但不支持 binary protocol。
2. statement不需要重用很多次，并且有其它方案保证安全。

### 在Transaction中使用PS
PS在Tx中唯一绑定一个连接，不会re-prepare。

Tx和statement不能分离，在DB中创建的statement也不能在Tx中使用，因为它们必定不是使用同一个连接使用Tx必须十分小心，例如下面的代码：
```
tx, err := db.Begin()
if err != nil {
    log.Fatal(err)
}
defer tx.Rollback()
stmt,err := tx.Prepare("insert into foo values(?)")
if err != nil {
    log.Fatal(err)
}
defer stmt.Close() //danger!
for i := 0; i < 10; i++ {
    _, err = stmt.Exec(i)   
    if err != nil {
        log.Fatal(err)
    }
}
err = tx.Commit()
if err != nil {
    log.Fatal(err)
}
// stmt.Close() runs here!
```
`*sql.Tx`一旦释放，连接就回到连接池中，这里stmt在关闭时就无法找到连接。所以必须在tx commit或rollback之前关闭statement。
### 处理Error
如果循环中发生错误会自动运行rows.Close(),用rows.Err()接收这个错误，Close方法可以多次调用，循环之后判断error是非常必要的。
```
for rows.Next(){
    // ...
}
if err = rows.Err();err != nil {
    // handle the error here
}
```
### 关闭Resultsets时的error
如果你在rows遍历结束之前退出循环，必须手动关闭REsultset，并且接收error。
```
for rows.Next() {
    //...
    break;// whoops. rows is not closed! memory leak
}
// do the usual "if err = rows.Err()" [omitted here]...
// it`s always safe to close here:
if err = rows.Close(); err != nil {
    // but ehat should we do if there`s an error?
    log.Println(err)
}
```
### QUeryRow()的error
```
var name string
err = db.QueryRow("select name from users where id = ?", 1).Scan(&name)
if err != nil {
    log.Fatal(err)
}
fmt.Println(name)
```
如果id为1的不存在，err为sql.ErrNoRows,一般应用中不存在的情况都需要单独处理。此外，query返回的错误都会延迟到scan被调用，所以应该写出如下代码：
```
var name string
err = db.QueryRow("select name from users where id = ?", 1).Scan(&name)
if err != nil {
    if err == sql.ErrNoRows {
        // there were no rows, but otherwise no error occurred
    }else{
        log.Fatal(err)
    }
}
fmt.Println(name)
```
把空结果当作error处理是为了强行让程序员处理结果为空的情况。
### 分析数据库Error
各个数据库处理方式不太也有，mysql为例：
```
if driverErr, ok := err.(*mysql.MySQLError); ok {
    // now the error number is accessible directly
    if driverErr.Number == 1045 {
        // Handle the permission-denied error
    }
}
```
mysqlerror, number都是DB特异的，别的数据库可能是别的类型或字段。这路的数字可以替换为常量。
## 连接错误
### NULL值处理
简单说就是设计数据库的时候不要出现null，处理起来非常费力。null的type很有限，例如没有`sql.NullUnit64`;null值没有默认零值。
```
for rows.Next() {
    var s sql.NullString
    err := rows.Scan(&s)
    if s.Valid{
        // use s.String
    } else {
        // null value
    }
}
```
### 未知Column
`rows.Columns()`的使用，用于处理不能得知结果字段个数或类型的情况，例如：
```
clos, err := rows.Columns()
if err != nil {
    // handler the err
} else {
    dest := []interface{}{
        new(unit64), //id
        new(string), //host
        new(string), //user
        new(string), //db
        new(string), //command
        new(uint32), //time
        new(string), //state
        new(string), //info
    }
    if len(cols) == 11{
        // Percona server
    } else if len(cols) > 8 {
        // handle this case
    }
    err = rows.Scan(dest...)
    // work with the values in dest
}
```
```
cols, err := rows.Columns() // remember to check err afterwards
vals := make([]interface{}, len(cols))
for i, _ := range cols {
    vals[i] = new(sql.RawBytes)
}
for rows.Next(){
    err = rows.Scan(vals...)
    //now you can check each element of vals for nil-ness,
    // and you can use type introspection and type assertions
    // to fetch the column into a typed variable.
}
```
# 关于连接池

1. 避免错误操作，例如LOCK TABLE后用INSERT会死锁，因为两个操作不是同一个连接，insert的操作没有table lock。
2. 当需要连接，且连接池中没有可用连接时，新的连接会被创建。
3. 默认没有连接上限，你可以设置一个，但这可能会导致数据库产生错误“too many connections”.
4. `db.SetMaxIdleConns(N)`设置最大空闲连接数
5. `db.SetMaxOpenConns(N)`设置最大打开连接数
6. 长时间保持空闲连接可能会导致db timeout