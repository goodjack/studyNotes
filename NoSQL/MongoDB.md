| mongo命令                         | 说明                                    |
| --------------------------------- | --------------------------------------- |
| show dbs                          | 显示已有数据库                          |
| use 数据库名                      | 使用数据库，若不存在就会创建            |
| show collections                  | 显示数据库中的集合（相当于MySQL中的表） |
| db                                | 显示当前使用的库                        |
| db.dropDatabase()                 | 删除数据库，在删除前需要进入数据库      |
| db.createCollection(name,options) | 创建集合                                |
|                                   |                                         |
|                                   |                                         |



### CURD

| 命令                         | 说明                                             |
| ---------------------------- | ------------------------------------------------ |
| db.集合.insert()             | 插入数据                                         |
| db.集合.find()               | 查询所有数据                                     |
| db.集合.drop()               | 集合删除                                         |
| db.集合.findOne()            | 查询                                             |
| db.集合.update(filter,value) | 修改文件数据，第一个是查询条件，第二个是修改的值 |
| db.集合.remove(filter)       | 删除文件数据                                     |

`db.inventory.find( { status: "A", qty: { $lt: 30 } } )`  = `SELECT * FROM inventory WHERE status = "A" AND qty < 30`

`db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )` = `SELECT * FROM inventory WHERE status = "A" OR qty < 30`



