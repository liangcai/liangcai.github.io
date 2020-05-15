---
title: chrome插件开发 chrome.storage api存储相关问题
date: 2020-05-16 09:25:00
cover: true
coverImg: 
categories: web
tags:
	- chrome插件开发
---

###  使用Storage Area Explorer插件

[Storage Area Explorer chrome 市场页面](https://chrome.google.com/webstore/detail/storage-area-explorer/ocfjjjjhkpapocigimmppepjgfdecjkb)

在任意插件页面打开开发这工具就可以使用，方便查看和修改chrome.storage中存储的数据

直接在终端查看存储的数据

```
chrome.storage.local.get(function(result){console.log(result)})
```



### Manifets中申明权限

```
"permissions": [
	"storage"
],
```



### chrome.storage api

#### 存入storeage.local和获取值 (storage.sync操作和storage.local相同)

```javascript
chrome.storage.local.set({key: value}, () => {
	console.log('Value is set to ' + value)
})

// 注意取值的参数可以是一个Array，指定多个键，建议只用Array参数，单个key用string做参数获取到的结果一样，仍然是用result.key取值
chrome.storage.local.get(['key'], function(result) {
    /** 检查某个数据是否已存在chrome.storage,如果key不存在，返回的就是空对象 {}
    if(key in result) {
   		do some thing      
    } 
    */
	console.log('Value currently is ' + result.key);
})
```

- 使用storage.sync的时候，如果用户启用了同步功能，存储的数据在用户登录浏览器的情况下会自动通过chrome同步
- chrome.storage最多5mb的存储容量，有调用频率限制，不要存储大量的信息，数据不加密，不要存储敏感信息

#### 跟踪chrome.storage中存储的数据对象所做的修改

添加listener到onChanged事件，可以在storage中数据变化时触发操作

```javascript
chrome.storage.onChanged.addListener((changes, namespace) => {
	for (var key in changes) {
	let storageChange = changes[key];
	console.log(`Storage key ${key} in namespace ${namespace} changed. Old value was ${storageChange.oldValue}, new value is ${storageChange.newValue}`)
	}
})
```



###  为什么使用chrome.storage而不是localstorage

Chrome.storage和localstorage功能类似但是区别如下:

- 使用chrome.storage.sync的情况下，数据可以通过chrome浏览器同步
- 插件的content scripts可以直接访问存储的数据而无需通过background page.
- 即使在使用浏览器隐身模式时，用户的插件的设置也可以保持
- 支持批量的异步读写操作，效率高于阻塞和串行的localstorage API
- 数据可以作为对象存储（localstorage只能存储为string)
- 可以读取管理员为扩展程序配置的企业策略