# TypeScript

## 一、类型

### 1、基础类型

- #### boolean

  ```typescript
  let isOpen: boolean = true
  ```

- #### number

  ```typescript
  let age: number = 18
  ```

- #### string

  ```typescript
  let title: string = "Hello"
  ```

- #### 数组

  ```typescript
  // 方法一
  let Arr1: number[] = [1, 2, 13]
  let Arr1: (number | string)[] = [1, "2", 13]
  // 方法二
  let Arr2: Array<string> = ["张三", "李四"]
  let Arr2: Array<string | number> = ["张三", 14, "李四"]
  ```

- #### 元组

  ```typescript
  let a: [string, number] = ["Hello", 18]
  ```

- 

## 二、声明