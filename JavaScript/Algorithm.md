# 算法

## 排序算法

### 冒泡排序

比较相邻两个元素的大小，如果是逆序，则交换位置

```javascript
function bubbleSort(arr) {
    for (let i = 1; i < arr.length; i++) {
        for (let j = i; j > 0; j--) {
            if(arr[j-1] > arr[j]) {
                [arr[j-1], arr[j]] = [arr[j], arr[j-1]]
            }
        }
    }
    return arr
}
const arr = [6,5,3,1,8,7,2,4]
console.log(bubbleSort(arr)); // [1,2,3,4,5,6,7,8]
```

### 插入排序

取出第i个元素，在序列中找到一个合适的位置并将她插入到该位置上即可

```javascript
function insertSort(arr) {
    for (let i = 1; i < arr.length; i++) {
        let j = i - 1
        if(arr[i] < arr[j]) {
            const temp = arr[i]
            while (j >= 0 && temp < arr[j]) {
                arr[j+1] = arr[j]
                j--
            }
            arr[j+1] = temp
        }
    }
    return arr
}
const arr = [6,5,3,1,8,7,2,4]
console.log(insertSort(arr)); // [1,2,3,4,5,6,7,8]
```

### 选择排序

在第i次选择操作中，通过n-i次键值间比较，从n-i+1个记录中选出键值最小的记录，并和第i（1小于等于1小于等于n-1）个记录交换

```javascript
function selectSort(arr) {
    for (let i = 0; i < arr.length; i++) {
        const min = Math.min(...arr.slice(i))
        let index = arr.indexOf(min);
        [arr[i], arr[index]] = [arr[index], arr[i]]
    }
    return arr
}
const arr = [6,5,3,1,8,7,2,4]
console.log(selectSort(arr)); // [1,2,3,4,5,6,7,8]
```

