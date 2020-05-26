# ES6 for in与for of 的使用方法及其区别
```js
// for in遍历的是数组的索引（即键名），而for of遍历的是数组元素值。
let arr = [1,2,3,4,5,6,7]
for(let index of arr){
 //   console.log(index)//1 2 3 4 5 6 7
	
}
for(let index in arr){
   // console.log(index)//0 1 2 3 4 5 6
	//console.log(arr)//1 2 3 4 5 6 7
	//console.log(arr[index])
}
//遍历对象 通常用for in来遍历对象的键名
let obj = {
	a:1,
	b:2,
	c:3
}
for(let key in obj){
	console.log(key)//a b c 
	//console.log(obj[key])//1 2 3
}
```
## forEach
1. 三个参数，第一个value, 第二个 index, 第三个数组体。
2. 适用于 数组，set，map，不适应于 字符串，Object。
3. 无法修改和删除集合数据，效率和for循环相同，不用关心集合下标的返回。
4. 不能终止循环，break，continue不能使用。
```js
let arr = [1, "ding", null, 5, true, undefined];
arr.forEach(function (value, index, a) {
	console.log("value:", value, "index:", index, "a:", a);
})
```
## for in
1. 索引为字符串
2. 多用于遍历对象，json，数组，无顺序，增加了转换过程所以开销比较大
3. 可扩展属性也会遍历
4. 支持break, continue
```js
for (let item in obj) {
	console.log(item, ":", obj[item]);
	if (item == "age") delete obj[item]; //删除属性成功
}
console.log("obj:", obj);
```
是目前遍历数组最好的方法，可以用在set，map，类数组，字符串上，但是不支持原生的Object遍历。
支持break, continue
