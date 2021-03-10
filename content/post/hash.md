---
title: "Hash"
date: 2021-03-10T16:52:07+09:00
draft: false
toc: false
comments: true
categories:
- C#
tags:
- C#
- Hash
---
#### 목적
Hashtable, DictionaryBase 등 해시 기반 컬렉션에 개체 삽입 및 식별하는데 사용

> A hash code is a numeric value that is used to insert and identify an object in a hash-based collection such as the Dictionary<TKey,TValue> class, the Hashtable class, or a type derived from the DictionaryBase class.

#### 특징

1. Hash는 유니크하지 않다.  
* 고유 식별자로 사용하면 안된다.
* 동일한 해시 코드가 동일한 값을 보장하지도 않는다.  
  -> 오브젝트의 equals 여부를 해시코드로 비교하면 안된다.

2. 가능한 해시 코드 값은 2^32개뿐.(부호있는 32비트 정수 값을 반환)  

       public virtual int GetHashCode();

3. 왜 사용하냐?
* key 값을 직접 엑세스하려면 배열 크기가 엄청 커짐.  
 낭비되는 공간을 줄이기 위해 해시 코드를 사용한다.