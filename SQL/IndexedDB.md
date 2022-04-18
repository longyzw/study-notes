# IndexedDB

## 一、使用场景

- 大量数据，请求耗性能；
- 即时聊天内容需要本地存储的；
- 数据结构复杂，其他方式不方便满足存储的。

浏览器兼容性，[点击查看](https://caniuse.com/?search=IndexedDB)

## 二、特点

### 1、 非关系型数据库

   以键值对的形式存储。

### 2、 持久化存储

   即使清除浏览器缓存，存储的数据也会一直存在，除非用户手动删除该数据库。

### 3、 异步操作

   数据库执行以异步方式进行，不会同localStorage一样锁死浏览器。

### 4、 支持事务

   IndexedDB支持事务(transaction)，即使在操作过程中有一步发生错误也会回滚到操作之前的状态。

### 5、 同源策略

   每个域名端口下的数据库都是单独存在，不能跨域访问。

### 6、 存储空间大

   存储空间根据用户磁盘空间而定，理论上没有上限。

## 三、重要概念

### 1、仓库 - objectStore

IndexedDB没有表的概念，所有数据存储在对应的仓库objectStore中，可以理解为一个objectStore对应的就是一张表。

### 2、索引 - index

添加索引可以加快查询的效率，在创建store时添加对应的索引，后续增删改查都可以通过索引来定位，且索引不能为空。

### 3、游标 - cursor

游标相当于指针，当按照特定条件进行数据筛选时就需要用游标，让游标一步一步往下执行，筛选出符合条件的数据。

注：IndexedDB只能通过 主键、索引、游标 查询。

### 4、事务

对数据库操作时，只要发生错误就会回滚到之前的状态，确保数据的准确性。

## 四、数据库操作

### 1、创建或连接数据库

创建js文件：IndexedDB.js

```javascript
const dbName = 'name' // 数据库名称
const storeNames = ['history'] // 存储库名称
const dbVersion = 1 // 数据库版本号，更新结构时需要改变版本号(整数，且比之前的数大)

class DB {
    // 数据库实例
    static instance = null;
    constructor () {
      // 实例对象
      this.db = null
      // 是否已打开数据库连接
      this.isOpen = false
    }

	// 打开数据库连接
    open () {
      const db = window.indexedDB.open(dbName, dbVersion)
      // 连接成功
      db.onsuccess = (event) => this.openSuccess(event)
      // 连接失败
      db.onerror = (event) => this.openError(event)
    }

	// 数据库开启成功，存储当前实例
    openSuccess (event) {
      this.db = event.target.result
      this.isOpen = true
    }

    // 数据库开启失败
    openError (event) {
      console.error('database failed to open:', event.target.error)
    }

	// 暴露实例
    static getInstance () {
      if (DB.instance instanceof DB) {
      } else {
        DB.instance = new DB()
        if (!DB.instance.isOpen) {
          DB.instance.open()
        }
      }
      return DB.instance
    }
}

export default DB.getInstance()
```

使用：

import DB from '~/IndexedDB.js'

const db = DB

通过db.方法名调用封装的方法

### 2、创建或更新数据库版本

```javascript
class DB {
    static instance = null;
    constructor () {
      this.db = null
    }

	// 打开数据库连接
    open () {
      // 创建和更新数据库版本号
      db.onupgradeneeded = (event) => this.onupgradeneeded(event)
    }

	// 创建和更新数据库版本号
    onupgradeneeded (event) {
      const oldNames = [...event.target.result.objectStoreNames]
      this.db = event.target.result
      // 创建对应名称的对象仓库
      storeNames.filter(item => !oldNames.includes(item)).map(item => this.createStore(item))
      // 删除历史多余的对象仓库
      oldNames.filter(item => !storeNames.includes(item)).map(item => this.db.deleteObjectStore(item))
      this.isOpen = true
    }

	// 创建对象仓库
    createStore (name) {
      const db = this.db
      // 根据对应仓库创建不同的主键和索引，判断name
      // 创建对应仓库，指定主键
      const objectStore = db.createObjectStore(name, { keyPath: 'id' })
      // 创建索引
      objectStore.createIndex('indexName', 'indexName', { unique: false })
      ......
    }
}
```



### 3、插入数据

#### 1）、通过 add 方法

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 新增数据
     * @param {string} storeName 仓库名称
     * @param {string} data 数据，必须包含声明的主键或索引
     */
    add(storeName, data) {
        const request = this.db.transaction(storeName, 'readWrite').objectStore(storeName).add(data);
        request.onsuccess = (event) => {
            console.log('数据写入成功')
        }
        request.onerror = (event) => {
            console.log('数据写入失败')
        }
    }
}
```

使用：

db.add(storeName, data)

#### 2）、通过 put 方法

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 更新数据，如果不存在则新增
     * @param {string} storeName 仓库名称
     * @param {string} data 数据，必须包含声明的主键或索引
     */
    thenAdd(storeName, data) {
        const request = this.db.transaction(storeName, 'readWrite').objectStore(storeName).put(data);
        request.onsuccess = (event) => {
            console.log('数据写入成功')
        }
        request.onerror = (event) => {
            console.log('数据写入失败')
        }
    }
}
```

使用：

db.thenAdd(storeName, data)

### 4、查询数据

#### 1）、通过主键查询

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 查询数据，根据主键
     * @param {string} storeName 仓库名称
     * @param {string} key 主键值
     */
    get(storeName, key) {
        const request = this.db.transaction(storeName, 'readonly').objectStore(storeName).get(key);
        request.onsuccess = (event) => {
            console.log('数据查询成功')
        }
        request.onerror = (event) => {
            console.log('数据查询失败')
        }
    }
}
```

使用：

db.get(storeName, key)

#### 2）、查询全部数据

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 查询数据，根据主键
     * @param {string} storeName 仓库名称
     */
    getAll(storeName) {
        const request = this.db.transaction(storeName, 'readonly').objectStore(storeName).getAll();
        request.onsuccess = (event) => {
            console.log('数据查询成功')
        }
        request.onerror = (event) => {
            console.log('数据查询失败')
        }
    }
}
```

使用：

db.getAll(storeName)

#### 3）、通过游标查询

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 查询数据，根据游标
     * @param {string} storeName 仓库名称
     * @param {object} 筛选条件：如分页，范围等
     */
    getCursor(storeName, data) {
        // 假设只根据分页条件查询
        const { page, limit } = data
        const list = [] // 存储符合条件的数据
        let counter = 0 // 计数器
        let advanced = true // 是否跳过多少条查询
        const request = this.db.transaction(storeName, 'readonly').objectStore(storeName).openCursor(null, 'prev'); // prev => 反方向；next => 正方向
        request.onsuccess = (event) => {
           let cursor = event.target.result
           if (page > 1 && advanced) {
               advanced = false
               cursor.advance((page - 1) * limit) // 跳过多少条
               return
           }
            if (cursor) {
                list.push(cursor.value)
                counter++
                if (counter < limit) {
                    cursor.continue()
                } else {
                    cursor = null
                    console.log('分页查询结果', list)
                }
            } else {
                console.log('分页查询结果', list)
            }
        }
        request.onerror = (event) => {
            console.log('数据查询失败')
        }
    }
}
```

使用：

db.getCursor(storeName, data)

### 5、修改数据

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 更新数据
     * @param {string} storeName 仓库名称
     * @param {string} data 数据，必须包含声明的主键或索引
     */
    put(storeName, data) {
        const request = this.db.transaction(storeName, 'readWrite').objectStore(storeName).put(data);
        request.onsuccess = (event) => {
            console.log('数据写入成功')
        }
        request.onerror = (event) => {
            console.log('数据写入失败')
        }
    }
}
```

使用：

db.put(storeName, data)

### 6、删除数据

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 删除数据
     * @param {string} storeName 仓库名称
     * @param {string} keys 单主键或主键集合用 ','隔开： "3" / "3,19,37,28"
     */
    delete(storeName, keys) {
        if(!keys) return;
        const ids = keys.split(',')
        const store = this.db.transaction(storeName, 'readWrite').objectStore(storeName);
        if(ids.length === 1) {
            // 单一主键直接删除
            store.delete(ids[0]).onsuccess = (event) => {
                console.log('删除成功')
            }
        } else {
            // 多个主键使用游标删除
            store.openCursor().onsuccess = (event) => {
                const cursor = event.target.result
                if (cursor) {
                  if (ids.includes(cursor.primaryKey)) {
                    cursor.delete()
                  }
                  cursor.continue()
                } else {
                  console.log('删除成功')
                }
            }
        }
    }
}
```

使用：

db.delete(storeName, data)

### 7、关闭数据库

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 关闭数据库
     */
    close(storeName, data) {
        db.close();
  		console.log("数据库已关闭");
    }
}
```

使用：

db.close()

### 8、删除数据库

```javascript
class DB {
    constructor () {
      this.db = null
    }
    /**
     * 删除数据库
     * @param {string} dbName 数据库名称
     */
    deleteDB(dbName) {
        const request = window.indexedDB.deleteDatabase(dbName)
        request.onsuccess = (event) => {
            console.log('数据库删除成功')
        }
        request.onerror = (event) => {
            console.log('数据库删除失败')
        }
    }
}
```

