# 通用npm插件

## 一、并行运行命令

[npm仓库地址](https://www.npmjs.com/package/concurrently)

### 1、安装

```sh
yarn add concurrently
```

### 2、使用

```
"start": "concurrently \"command1 arg\" \"command2 arg\""
```

## 二、浏览器指纹

[npm仓库地址](https://www.npmjs.com/package/@fingerprintjs/fingerprintjs)

### 1、安装

```sh
yarn add @fingerprintjs/fingerprintjs
```

### 2、使用

```javascript
import FingerprintJS from '@fingerprintjs/fingerprintjs'

async function getFP() {
  const fp = await (await FingerprintJS.load()).get()
  return fp.visitorId
}
await getFP() // => 7a9e26a78bf05fb9aa1016b7f0a56457
```

## 三、切换全屏

[npm仓库地址](https://www.npmjs.com/package/screenfull)

### 1、安装

```sh
yarn add screenfull
```

### 2、使用

```javascript
import screenfull from 'screenfull'

// 监听全屏变化
screenfull.on('change', () => {
  console.log(screenfull.isFullscreen) // => 全屏状态
})

// 切换全屏状态
function toggleScreen() {
  screenfull.toggle()
}
```

## 四、Excel导出

[npm仓库地址](https://www.npmjs.com/package/xlsx)

### 1、安装

```sh
yarn add xlsx
```

### 2、基础封装

创建`excel.ts`

```typescript
import { utils, writeFile } from 'xlsx'
import type { BookType } from 'xlsx';

/** 导出页声明 */
interface SheetOption {
    /** 表头信息 */
    tHeader: string[],
    /** 表头关联key信息 */
    keys: string[],
    /** 数据 */
    data: any,
    /**
     * 当页导出名称
     * @default 'sheet'+<当前页数>
     */
    name?: string
}

interface ExportParams {
    /** 文件名 */
    filename: string,
    sheet: Array<SheetOption>,
    /** 导出文件名默认添加时间戳 */
    useUnix?: boolean,
    /**
     * 文件导出格式
     * @default 'xlsx'
     */
    bookType?: BookType,
    /** 导出完成时回调 */
    success?: Function,
    /** 导出报错时回调 */
    error?: Function,
}
    
// 异常枚举
const errorEnum = {
    1: `位置 => ExportExcel方法；
        原因 => 表头和key个数不一致`
}

/** 导出Excel */
export const ExportExcel = ({
    filename = '',
    sheet = [],
    useUnix = true,
    bookType = 'xlsx',
    success
}: ExportParams) => {
    //创建book
    const wb = utils.book_new();
    sheet.map((item, i) => {
        const { tHeader, keys, data } = item
        if(tHeader.length !== keys.length) {
            if(error) error(errorEnum[1])
            throw(errorEnum[1])
        }
        const headerReplace = Object.fromEntries(tHeader.map((el, i) => [keys[i], el]))
        const sheet = utils.json_to_sheet([headerReplace, ...data], {
            header: keys,
            skipHeader: true,
        });
        
        // sheet写入book
        utils.book_append_sheet(wb, sheet, item?.name || `sheet${i+1}`);
    })
    // 处理文件名称
    const outName = `${filename}${useUnix === false ? '' : (' - '+Date.now())}.${bookType}`
    // 输出
    writeFile(wb, outName);
    // 执行导出成功回调
    if(success) success()
}
```

### 3、基础使用

- 使用格式

```typescript
import { ExportExcel } from '~/excel'

ExportExcel({
    filename: '导出文件名',
    sheet: [
        {
            tHeader: [], // 导出表头
            keys: [], // 表头对应key
            data: [], // 要导出的数据
            name: '当页名称，SheetName'
        }
    ],
    useUnix: true, // 导出文件名是否自带时间戳，默认:true
    bookType: 'xlsx', // 导出类型，默认:'xlsx'
    success: () => {}, // 成功回调
})
```

- 示例

```typescript
function doExport() {
  const data = [
    { name: '张三', sex: '男', age: 18, register: Date.now().toString(), score: 87 },
    { name: '李四', sex: '女', age: 22, register: Date.now().toString(), score: 67 }
  ];
  const data2 = [
    { name: '王五', sex: '男', age: 18, register: Date.now().toString(), score: 56 },
    { name: '老李', sex: '女', age: 22, register: Date.now().toString(), score: 82 }
  ];
  const options = {
    filename: '用户信息',
    sheet: [
      {
        tHeader: ['姓名', '性别', '年龄', '注册时间', '总分'],
        keys: ['name', 'sex', 'age', 'register', 'score'],
        data: data,
        name: '一班'
      },
      {
        tHeader: ['姓名', '性别', '年龄', '注册时间', '总分'],
        keys: ['name', 'sex', 'age', 'register', 'score'],
        data: data2,
        name: '二班'
      }
    ],
    success: () => {
      console.log('调用成功')
    }
  }
  ExportExcel(options)
}
```

### 4、进阶封装 - 合并单元格

- 封装 =>修改封装文件

```typescript
/** 导出页声明 */
interface SheetOption {
    /** ... */
    /** 单元格合并列表 */
    merges?: string[];
}

/** 导出Excel */
export const ExportExcel = ({
    /** ... */
}: ExportParams) => {
    // ...
    options.sheet.map((item, i) => {
        const { tHeader, keys, merges } = item
        // 转换json数据

        // 处理合并单元格
        if(merges && merges.length) {
            if (!workSheet['!merges']) workSheet['!merges'] = [];
            merges.map(el => {
                workSheet['!merges']?.push(utils.decode_range(el))
            })
        }
        
        // sheet写入book
    })
    // ...
}
```

- 使用 => 修改导出数据

```typescript
function doExport() {
  const options = {
    /** ... */
    sheet: [
      {
        /** ... */
        merges: ['D2:D3', 'B2:C2']
      },
      {
        /** ... */
        merges: ['B3:C3']
      }
    ]
  }
  ExportExcel(options)
}
```

### 5、进阶封装 - 列宽设置

- 封装 =>修改封装文件

```typescript
/** 导出页声明 */
interface SheetOption {
    /** ... */
    /** 列宽 */
    cols?: (string | number)[];
}

// 获取占据字符长度
function getCellMaxWidth(data: (string | number)[]) {
    // 设置最大长度值
    let maxLen = 0
    data.map(item => {
        if(!item) {
            maxLen = 10
        } else {
            // 将值按字符裁剪为数组
            const strArr = item.toString().split('')
            // 匹配中文字符
            const pattern = new RegExp("[\u4E00-\u9FA5]+")
            // 转换为长度，中文2，其余1
            const chatArr = strArr.map(el => pattern.test(el) ? 2 : 1)
            const total = chatArr.reduce((a, b) => (a + b), 0)
            if(maxLen) {
                if(total > maxLen) {
                    maxLen = total
                }
            } else {
                maxLen = total
            }
        }
    })
    maxLen = maxLen + 2
    return maxLen < 10 ? 10 : maxLen
}

/** 导出Excel */
export const ExportExcel = ({
    /** ... */
}: ExportParams) => {
    // ...
    options.sheet.map((item, i) => {
        const { tHeader, keys, data, merges, cols } = item
        // 转换json数据

        // 处理合并单元格
        
        // 计算自适应列宽
        const colWidths = new Array(tHeader.length).fill(0)
        keys.map((el, n) => {
            colWidths[n] = getCellMaxWidth(data.map((ls: object) => ls[el as keyof typeof ls] || ''))
        })
        
        // 替换传递列宽
        if(cols && cols.length) {
            // 处理手动设置的列宽
            colWidths.map((el, n) => (colWidths[n] = (parseInt(cols[n] as string) / 12) || el))
        }

        // 设置列宽
        workSheet['!cols'] = [];
        colWidths.map(el => {
            workSheet['!cols']?.push({
                wch: el
            })
        })
        
        // sheet写入book
    })
    // ...
}
```

- 使用 => 修改导出数据

```typescript
function doExport() {
  const options = {
    /** ... */
    sheet: [
      {
        /** ... */
        cols: ['', 40, '', 100],
      },
      {
        /** ... */
      }
    ]
  }
  ExportExcel(options)
}
```

## 五、Excel导入

### 1、安装

```sh
yarn add xlsx
```

### 2、方法封装

```typescript
import { read, utils } from 'xlsx'

export type SheetDataParams = {
    sheetName: string, // 导入页名称
    data: any[], // 导入的json数据
}

interface ImportParams {
    /** 导出完成时回调 */
    success?: Function,
    /** 导出报错时回调 */
    error?: Function,
}


// 异常枚举
const errorEnum = {
    1: `位置 => ImportExcel方法；
        原因 => 没有文件`,
    2: `位置 => ImportExcel方法；
        原因 => 文件类型不支持`
}

/** 导入Excel */
export const ImportExcel = ({
    success,
    error
}: ImportParams) => {
    const myInput = document.createElement('input')
    myInput.id = 'myInput'
    myInput.style.display = 'none'
    myInput.type = 'file'
    myInput.accept = '.xls,.xlsx'
    // 执行导入
    myInput.click()
    // 监听文件上传
    myInput.onchange = function() {
        
        if(!myInput.files) {
            if(error) error(errorEnum[1])
            throw(errorEnum[1])
        }
        
        const file = myInput.files[0]

        const startIndex = file.name.lastIndexOf('.') > -1 ? file.name.lastIndexOf('.') + 1 : 0
        const fileType = file.name.slice(startIndex,)
        if(!['xls', 'xlsx'].includes(fileType)) {
            if(error) error(errorEnum[2])
            throw(errorEnum[2])
        }

        // 读取文件
        const reader = new FileReader()

        // 导入时
        reader.onload = e => {
            const data = e.target?.result

            const result = read(data, {
                type: "binary"
            });

            // 声明返回的数据
            const sendData: Array<SheetDataParams> = []

            if(result.SheetNames) {
                result.SheetNames.map((item, i) => {
                    sendData.push({
                        sheetName: item,
                        data: utils.sheet_to_json(
                            result.Sheets[result.SheetNames[i]]
                        )
                    })
                })
            }

            // 数据转换完成并返回
            if(success) success(sendData)
        };

        reader.readAsBinaryString(file)
    }
}
```

### 3、使用

```typescript
import { ImportExcel } from './utils/excelImport'
import type { SheetDataParams } from './utils/excelImport'

ImportExcel({
    success: (res: Array<SheetDataParams>) => {
        console.log('导入数据json格式：', res);
    },
    error: (error) => {
    	console.log('导入失败：', error)
	}
})
```

