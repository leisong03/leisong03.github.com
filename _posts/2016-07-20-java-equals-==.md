---
layout: post
title:  "Java equals 和 =="
date:   2016-07-20 22:00:00 +0800
categories: Java
tags: Java 
author: JiuYang Chen
---

* content
{:toc}




## `equals` 和 `==` 

### `==` 

* 基本数据类型 byte,short,char,int,long,float,double,boolean
`==`比较的是它们的值

* 复合数据类型（类）
`==`比较的是它们的在内存中的存放位置


### `equals`

对于复合数据类型（类）使用`equals`来比较它们的内容是否一致

`String`,`Integer`,`Date`在这些类当中`equals`有其自身的实现。

#### String.class

```java
        
        //先使用`==`进行比较内存地址 如果`true` 则`return`
        
		public boolean equals(Object obj) {
		        return (this == obj);
		    } 
		
		//如果内存地址不相同则比较内容
		   
		public boolean equals(Object anObject) {
		        if (this == anObject) {
		            return true;
		        }
		        if (anObject instanceof String) {
		            String anotherString = (String)anObject;
		            int n = value.length;
		            if (n == anotherString.value.length) {
		                char v1[] = value;
		                char v2[] = anotherString.value;
		                int i = 0;
		                while (n-- != 0) {
		                    if (v1[i] != v2[i])
		                        return false;
		                    i++;
		                }
		                return true;
		            }
		        }
		        return false;
		}

```

#### Integer.class

```java

		 public boolean equals(Object obj) {
		        if (obj instanceof Integer) {
		            return value == ((Integer)obj).intValue();
		        }
		        return false;
		    }



```

#### Date.class

```java

	  public boolean equals(Object obj) {
	        return obj instanceof Date && getTime() == ((Date) obj).getTime();
	    }

```

### 关于内存池

基于以上的理论基础上，我测试了一下发现。

```java

	String str1 = "string";
	String str2 = "string";
	
	System.out.println(date == date2);
 
    output: true  ??? 说好的比较地址呢
```

```java

	String str1 = "string";
	String str2 = new String("string");
	
	System.out.println(date == date2);
 
    output: false  正常情况
    
    String str1 = new String("string");
	String str2 = new String("string");
	
	System.out.println(date == date2);
 
    output: false  正常情况
```

查看了一下源码`intern()`函数给出了如下注释

```

     * When the intern method is invoked, if the pool already contains a
     * string equal to this {@code String} object as determined by
     * the {@link #equals(Object)} method, then the string from the pool is
     * returned. Otherwise, this {@code String} object is added to the
     * pool and a reference to this {@code String} object is returned.


```

大体的意思是放你当没有使用`new`关键字创建一个字符串`String str1 = "string"`的时候，会先检查内存池如果已经包含该字符串
如果存在则 返回内存池中的该字符串的引用。如果没有再创建一个新的。

而使用关键字`new`则会默认创建一个新的`String`所以拥有不同的内存地址。

