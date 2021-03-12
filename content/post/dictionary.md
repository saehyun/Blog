---
title: "Dictionary"
date: 2021-03-12T17:14:08+09:00
draft: false
toc: false
comments: true
categories:
- csharp
tags:
- csharp
- Dictionary
---
## Dictionary?  
Key, Value 값을 가진 Collection. (Hashtable로 구현 됨)

#### 특징
해시함수를 통해 나온 키값을 bucket이라는 array에 저장한다.  
이때, 해시는 고유하지 않으므로 충돌(Collision)이 날 수 있다.  
-> Collision Resolution을 통해 빈 버켓을 찾음)

#### Collision Resolution
C#은 Chaining 방식으로 충돌을 해결한다.

![sc](https://docs.microsoft.com/en-us/previous-versions/images/ms379571.datastructures20_2fig11(en-us,vs.80).gif)

#### Time Complexity
시간복잡도가 O(1)에 가까운게 bucket array 해시값으로 바로 접근하기 때문에 가능.
 
TryGetValue() 구현을 보면 아래와 같다.

    public bool TryGetValue(TKey key, out TValue value) {
        int i = FindEntry(key);
        if (i >= 0) {
            value = entries[i].value;
            return true;
        }
        value = default(TValue);
        return false;
    }

우선, key 값으로 진입점을 찾고, Entry array에서 value를 꺼내온다.

    private int FindEntry(TKey key) {
        if( key == null) {
            ThrowHelper.ThrowArgumentNullException(ExceptionArgument.key);
        }
 
        if (buckets != null) {
            int hashCode = comparer.GetHashCode(key) & 0x7FFFFFFF;
            for (int i = buckets[hashCode % buckets.Length]; i >= 0; i = entries[i].next) {
                if (entries[i].hashCode == hashCode && comparer.Equals(entries[i].key, key)) return i;
            }
        }
        return -1;
    }
	
bucket array를 loop를 돌려 index 값을 찾고 entry array에서 value를 얻어온다.

    private struct Entry {
        public int hashCode;    // Lower 31 bits of hash code, -1 if unused
        public int next;        // Index of next entry, -1 if last
        public TKey key;           // Key of entry
        public TValue value;         // Value of entry
    }

