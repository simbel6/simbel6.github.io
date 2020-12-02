---
title: N 皇后
categories:
  - 前端
  - 数据结构和算法
tags:
  - 算法
  - 回溯
  - 递归
abbrlink: a2999a55
---

## N 皇后

```js
const solveNQueens = function (n) {
  // 已摆放皇后的列下标
  const cols = new Set();

  // 已摆放皇后的左斜线 右上→左下
  // 计算某个坐标是否在这个左斜线的方式是「行下标 + 列下标」是否相等
  const left = new Set();

  // 已摆放皇后的右斜线 左上→右下
  // 计算某个坐标是否在这个右斜线的方式是「行下标 - 列下标」是否相等
  const right = new Set();

  // 解法结果数组
  const result = [];

  valid(0, n, cols, left, right, result, []);

  // 解法数组绘制成目标数组
  return draw(result, n);
};

const valid = function (row, n, cols, left, right, result, temp) {
  // 终止条件，最后一行都走完了，说明找到了一组，把它加入到集合result中
  if (row === n) {
    result.push([...temp]);
    temp = [];
  }
  for (let i = 0; i < n; i++) {
    if (!cols.has(i) && !right.has(row - i) && !left.has(row + i)) {
      // 在列上不冲突、在左斜线上不冲突、在右斜线上不冲突，先记录当前已选位置，进入下一轮递归
      cols.add(i);
      left.add(row + i);
      right.add(row - i);
      temp[row] = i;
      valid(row + 1, n, cols, left, right, result, temp);

      // 递归出栈后，在状态中清除这个位置的记录，下一轮循环应该是一个全新的开始。
      cols.delete(i);
      left.delete(row + i);
      right.delete(row - i);
    }
  }
};

function draw(arr, n) {
  let res = [];
  arr.forEach((item) => {
    let sub = [];
    item.forEach((i) => {
      let temp = Array(n).fill(".");
      temp[i] = "Q";
      sub.push(temp.join(""));
    });
    res.push(sub);
  });
  return res;
}
```
