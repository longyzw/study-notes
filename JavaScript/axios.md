# Axios封装

## 通用请求封装

```javascript
// request.js

import axios from 'axios'

// 全局配置
axios.defaults.headers['Content-Type'] = 'application/json'
axios.defaults.baseURL = 'https://www.test.com'
axios.defaults.timeOut = 25000

// 添加请求拦截器
axios.interceptors.request.use((config) => {
    // do something
    return config
}, (error) => {
    const requestKey = getReqKey(error.config)
    removeReqKey(requestKey)
    // 对请求错误做些什么
    return Promise.reject(error)
})

// 添加响应拦截器
axios.interceptors.response.use((response) => {
    // do something
    return response
}, (error) => {
    // do something
    return Promise.reject(error)
})

export default axios
```

## 取消重复请求

```javascript
import qs from 'qs'

const pendingRequest = new Map() // 请求对象
const CancelToken = axios.CancelToken

// 获取请求key
function getReqKey (config) {
  // 请求方式、请求地址、请求参数生成的字符串来作为是否重复请求的依据
  if (!config) return ''
  const { url, data } = config // 解构出来这些参数
  const _data = typeof data === 'object' ? qs.stringify(data) : data
  const requestKey = [url, _data].join('&')
  return requestKey
}

// 取消重复请求
function removeReqKey (key) {
  const cancelToken = pendingRequest.get(key)
  if (!cancelToken) return
  cancelToken(key) // 取消之前发送的请求
  pendingRequest.delete(key) // 请求对象中删除requestKey
}

axios.interceptors.request.use((config) => {
    // ...
    // 获取请求key
    const requestKey = getReqKey(qs.parse(qs.stringify(config)))
    if (pendingRequest.has(requestKey)) { // 是重复请求
      removeReqKey(requestKey) // 取消
    }
    config.cancelToken = new CancelToken(function executor (cancel) {
      pendingRequest.set(requestKey, cancel) // 设置
    })
    // ...
})

axios.interceptors.response.use((config) => {
    // ...
    const requestKey = getReqKey(response.config)
    removeReqKey(requestKey)
    // ...
}, (error) => {
    // ...
    const requestKey = getReqKey(error.config)
    removeReqKey(requestKey)
    // ...
})
```

