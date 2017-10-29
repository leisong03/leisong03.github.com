---
layout: post
title:  "高效的数独算法-位运算"
date:   2017-04-25 20:00:00 +0800
categories: Java 
tags: Java
author: JiuYang Chen
---



* content
{:toc}





本篇介绍一种高效的`数独算法`通过位运算。

## 简介

> 原文: [https://my.oschina.net/u/1859679/blog/868056](https://my.oschina.net/u/1859679/blog/868056)

`Java`版本


```java

public class shudu {

    // 数独题目 ‘0’为空
	private static int[] shuduNum = {0,0,3,6,0,0,0,0,1,0,7,0,0,8,0,0,0,0,6,0,0,3,0,9,7,0,0,7,5,0,0,4,0,6,0,3,0,0,0,0,6,7,8,9,0,0,9,0,0,0,0,0,0,0,0,8,7,0,0,0,0,0,0,0,1,5,0,9,0,0,0,0,4,0,0,1,0,0,0,0,0};
	private static int[] tempNum = new int[81];
	
	// 列状态
	private static int[] statusH = new int[9];
	// 行状态
	private static int[] statusV = new int[9];
	// 九宫格状态
	private static int[] statusB = new int[9];
	// 上一次填数位置
	private static int tempSp = 0 ;
	
	// 最大状态值 `1 1111 1111`
	private static int STATUS_MAX_VALUE = 511;
	
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		   
		   // 打印出当前数独题目
		   printSudoku(shuduNum) ;
		   // 数独算法初始化
		   initShudo() ;   
           // 开始解题		   
		   tryAns2() ;
           // 打印出数独题目答案		   
		   printSudoku(shuduNum) ;
	}

	private static void initShudo(){
		
		// 将所有的状态置为0
		for(int i = 0;i<9;i++){
			statusH[i] = 0;
			statusV[i] = 0;
			statusB[i] = 0;
		}
		
		// 遍历数独题目中不为0的数,重置对应的行、列、九宫格状态
		for(int i = 0;i<81;i++){
		
			 int indexH = i%9; 
			 
		   	 int indexV= i/9;	
			 
		   	 int indexB=((i/9)/3)*3+(i%9)/3;
			 
		   	 if(shuduNum[i] > 0 ){
		   		int number = shuduNum[i];
				// 通过位操作重置状态
		   		markStatus(indexV,indexH,indexB,number);
		   	 }
		   	 
			}
		
	}
	
	private static void markStatus(int indexV,int indexH,int indexB,int number){
	     // 将第‘number’位,置位‘1’
		if (number<1)return;
		
		/* ‘|=’ 位或 
		 *  以`statusV[indexV]|=(1<<(number-1))` , `statusV[indexV] = 0 0000 0000` `number = 4`为例,
		 *
		 *  				  (1<<(number-1)) = 0 0000 0001 << 3 = 0 0000 1000
		 *   statusV[indexV]|=(1<<(number-1)) = 0 0000 0000 & 0 0000 1000 = 0 0000 1000
		 *
		 */
		 
		statusV[indexV]|=(1<<(number-1));
    	statusH[indexH]|=(1<<(number-1));
    	statusB[indexB]|=(1<<(number-1));
	}
	
	private static void tryAns2(){
		
		// 获取第一个空值 `0`
		int sp = getNextBlank(-1);
		
		do{
			int indexH =sp%9;      					
	   	  	int indexV= sp/9;						
	   	  	int indexB=((sp/9)/3)*3+(sp%9)/3;
	   	  	
	   	  	int skipValue  = shuduNum[sp];
			
	   	 resetStatus(indexV,indexH,indexB,skipValue);
	   	 
	   	int number = findNumber(indexV,indexH,indexB,skipValue);
	   	
	   	if(number == -1){
	   		shuduNum[sp] = 0;
	   		sp= pop() ;
	   		if (sp==-1)
   	  		{
   	  			System.out.println("not cycle last sp,last sp ==-1");
   	  		}
	   		continue;
	   	}
	   	
	   	shuduNum[sp]=number;
   	  	// 标记状态
    	markStatus(indexV,indexH,indexB,number);
    	push(sp);
    	sp= getNextBlank(sp) ;
		}while(sp >= 0 && sp < 81 );
		
		
	}
	
	
	private static int getNextBlank(int sp) {
		   do {
		      sp++ ;
		   } while(sp<81 && shuduNum[sp]>0) ;
		   return(sp) ;
		}
	
	private static  void resetStatus(int indexV,int indexH,int indexB,int number){
	
		// 将第‘number’位,置位‘0’
		if (number<1)
		{
			return;
		}
		
		/* ‘&=’ 位与 
		 *  以`statusV[indexV]&=~(1<<(number-1))` , `statusV[indexV] = 0 0000 1000` `number = 4`为例,
		 *
		 *  				  (1<<(number-1)) = 0 0000 0001 << 3 = 0 0000 1000
		 *                   ~(1<<(number-1)) = 1 1111 0111
		 *  statusV[indexV]&=~(1<<(number-1)) = 0 0000 1000 & 1 1111 0111 = 0 0000 0000
		 *
		 */
	  	statusV[indexV]&=~(1<<(number-1));
		statusH[indexH]&=~(1<<(number-1));
		statusB[indexB]&=~(1<<(number-1));
	}
	
	private static int findNumber(int indexV,int indexH,int indexB,int skipValue){
		
		int status = statusV[indexV]|statusH[indexH]|statusB[indexB];
		if (skipValue>0){
			status = status|((1<<skipValue)-1);							
		}

		if (status>=STATUS_MAX_VALUE)return -1;
		
		//把右起第一个0变成1
		int nextStatus = status|(status+1);

		//获取差值
		int difValue = nextStatus^status;

		//获取logn
		for (int i = 0; i < 9; ++i){
			if ((difValue>>i)==1)return i+1;
			
		}
		return -1;
	}
	
	private static int pop(){
		if(tempSp<=0) return(-1) ;
		   else return(tempNum[--tempSp]) ;
	
	}
	
	private static void push(int sp) {
		   tempNum[tempSp++]= sp ;
		}
	
	private static void  printSudoku(int[] prn) {
		   for(int i=0; i<81; i++) {
			   System.out.print(prn[i]+"  ");
		      if(i%9==8) System.out.println("\n");
		   }
		}

}

```


![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1ffzw1562x7j20640ih0sq.jpg)








