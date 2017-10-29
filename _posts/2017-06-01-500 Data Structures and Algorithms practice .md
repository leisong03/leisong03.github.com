---
layout: post
title:  "500 Data Structures and Algorithms practice problems and their solutions"
date:   2017-08-07 20:00:00 +0800
categories: Java 
tags: Java
author: JiuYang Chen
---



* content
{:toc}





`500 Data Structures and Algorithms practice problems and their solutions`

[500 Data Structures and Algorithms practice problems and their solutions](https://techiedelight.quora.com/500-Data-Structures-and-Algorithms-practice-problems-and-their-solutions)

## 001`Find pair with given sum in the array`

### `My solutions`

遍历`arr`,比对取合适的值

* Time complexity `o(n²)`

* auxiliary space `o(1)`


```java

package test;

public class d001 {

    /*Q: Input: arr = [8,7,2,5,3,1]; 
     *   sum = 10;
     *
     *  Output: pair found at index 0 and 2(8+2)
     *  or
     *  pair found at index 1 and 4(7+3)    
     * 	
     */
	
	public static void main(String args[]){
		int [] arr = {8,7,2,5,3,1}; 
		int sum = 10;
		findPair(arr,sum);
	}
	
	public static void findPair(int [] arr,int sum){
		String str = "";
		int length = arr.length;
		for(int i = 0;i < length ;i++){
			for(int j = i + 1;j < length;j++){
					if(arr[i]+arr[j] == sum){
						if(str.equals("")){
							str +=" pair found at index"+i+" and "+j+"("+arr[i]+"+"+arr[j]+")\n";
						}else{
							str +=" or\n pair found at index"+i+" and "+j+"("+arr[i]+"+"+arr[j]+")\n";
						}
					}
			}
		}
	if(str.equals(""))str+="pair not found";
	System.out.println(str);	
	}
}


```

### better

使用`Arrays.sort`对`arr`进行排序,从两端开始遍历`max`、`min`。如果两个值大于给定的`sum`,取更小的`max`。如果小于`sum`，取更大的`min`。

* Time complexity `o(nlogn)`

* auxiliary space `o(1)`

### best

使用`hash map`将`arr`值存入`map`中,遍历存在不同于当前元素并且值为`(sum - arr[i])`的元素。



* Time complexity `o(n)`

* auxiliary space `o(1)`

> `hash` 表的查找效率近似于`o(1)`

## 002`print all sub-array with 0 sum`

### `My solutions`












