## Mongodb的用法

启动mongodb：`./mongod --dbpath ./data/db `

意外关机：`./mongod --repair --dbpath ./data/db`

> 这里的--repair可以对/data/db内的xxx.lock文件进行清空操作，不然，数据文件被锁住，导致无法启动mongod