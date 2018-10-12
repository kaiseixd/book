# 树的遍历
## 深度优先

```js
// 前序遍历
// 递归
function _pre (node) {
  if (node) {
    console.log(node.value)
    _pre(node.left)
    _pre(node.right)
  }
}

// 迭代
function _pre (node) {

}
```

## 广度优先

```js
function breadthTraversal (root) {
  if (!root) return null
  let queue = []
  queue.push(root)
  while (queue.length) {
    let node = queue.shift()
    console.log(node.value)
    if (node.left) queue.push(node.left)
    if (node.right) queue.push(node.right)
  }
}
```

# 排序
## 快速排序

1. 从数列中挑出一个元素，称为"基准"（pivot），
2. 重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition) 操作。
3. [递归](https://zh.wikipedia.org/wiki/%E9%80%92%E5%BD%92)地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。

时间复杂度平均情况：O(n\log n) 最快：O(n^{2})  空间复杂度:  O(\log n)

```javascript
var quickSort = function(arr) {
   console.time('2.快速排序耗时');
　　if (arr.length <= 1) { return arr; }
　　var pivot = arr.splice(0, 1)[0];
　　var left = [];
　　var right = [];
　　for (var i = 0; i < arr.length; i++) {
　　　　if (arr[i] < pivot) {
　　　　　　left.push(arr[i]);
　　　　} else {
　　　　　　right.push(arr[i]);
　　　　}
　　}
	console.timeEnd('2.快速排序耗时');
　　return quickSort(left).concat([pivot], quickSort(right));
};

var arr=[3,44,38,5,47,15,36,26,27,2,46,4,19,50,48];
console.log(quickSort(arr));//[2, 3, 4, 5, 15, 19, 26, 27, 36, 38, 44, 46, 47, 48, 50]
```