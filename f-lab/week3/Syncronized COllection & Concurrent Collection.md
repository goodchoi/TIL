# 📍main topic : Java Synchronized Collection과 Concurrent Collection을 비교하시오

## 자바 5 이전
자바 5 이전에서 컬렉션의 동시성문제를 해결하기 위해 가장 많이 사용되었던 것은 `Syncronized`였다.

`Synchronized Collection`으로 분류될 수 있는 대표적인 예시는 다음과 같다.

+ `Vector`
+ `HashTable`
+ `Collections.SyncronizedXXX`

`Synchronized Collection`은 모든 메서드들에 `syncronized`를 통해 동시성처리를 한다는 공통점이 있다.


```java
public static <T> Collection<T> synchronizedCollection(Collection<T> c) {
    //...
    public boolean add(E e) {
        synchronized (mutex) {return c.add(e);}
    }
    public boolean remove(Object o) {
        synchronized (mutex) {return c.remove(o);}
    }
    public boolean retainAll(Collection<?> coll) {
        synchronized (mutex) {return c.retainAll(coll);}
    }
    public void clear() {
        synchronized (mutex) {c.clear();}
    }
    //...
```
`Synchronized Collection` 은 `Collections` 클래스에 내부 클래스로 정의 되어 있으며
위와 같이 컬렉션을 상속받은 모든 자료구조에 대해 `SyncronizedCollection`을 사용할 수 있고,
이떄 `mutex`는 자료구조 그 객체가 된다.
위와 같은 형식의 동기화를 `Vector`와 `HashTable`에서도 똑같이 적용한다.
그렇다면 이방식의 문제점은 무엇일까?

### 단점
모든 메서드에 대해 `Synchronized`키워드가 사용되므로 결국 불필요한 오버헤드가 발생한다.
<br> <u>[오버헤드 1]</u> 모든 메서드에 대한 락의 획득 해제 하는 과정이 수반되므로 
이러한 과정이 필요하지 않는 단일 스레드 환경에서 성능 저하가 발생한다.
+ 예시 : Array List vs Vector

<u>[오버헤드 2]</u> 과도한 동기화로 인한 교착 상태를 발생시킬 수 있다.


## 자바 5 이후
자바 5에서 `concurrent`패키지에 자료구조의 고수준의 동기화를 지원하는 클래스들이 추가되었다.
여기서 고수준의 동기화란 `lock`구현체,`CAS`알고리즘등을 말한다.


| 컬렉션 종류    |컬렉션|
|-----------|-----|
| List      |CopyOnWriteArrayList|
| Map       |ConcurrentMap, ConcurrentHashMap|
| Set	      |CopyOnWriteArraySet|
| SortedMap |	ConcurrentSkipListMap|
|SortedSet|	ConcurrentSkipListSet|
|Queue	|ConcurrentLinkedQueue|

여기서는 `CopyOnWriteArrayList`와 `ConcuurrentHashMap`을 중점적으로 살펴보자.

### CopyOnWriteArrayList

add,set,remove 등의 쓰기 동작시 원본배열을 복사해서(`copy`) 임시배열을 만들고,
임시배열에서 쓰기동작을 수행한 후 원본배열을 갱신한다.
따라서 쓰기 동작에서는 `lock`을 사용할 수 밖에 없겠지만, 읽기 동작은 `lock`에서 자유로워진다.

<br>
CopyOnWriteArrayList의 add 메서드

```java
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```
구현이 그렇게 어렵지 않다. 기존 자바에서 `synchronized`를 사용한것을
`Lock`의 구현체인 `ReentrantLock`을 사용하여 락을 획득 / 해제를 구현했고,
기존 원본 배열의 길이보다 1만큼 큰 배열을 만들고 배열의 끝에 파라미터를 추가하는 것이다.
<br>

>재밌는 점은 `ArrayList`의 `add`는 불필요한 `copy`의 반복을 최소화하고 동적으로 배열의 길이를 증가시키는 작업이 `add`메서드에 수반되는데
`CopyOnWriteArrayList`에서는 그 과정이 생략된다. 왜냐하면 모든 쓰기 작업에서 새로운 배열을 
`copy`하는 작업이 수반되기 때문이다.(무의미해진다)

따라서 위와 같은 작업 덕분에 get메서드는 다음과 같이 정의된다.
```java
    public E get(int index) {
        return get(getArray(), index);
    }
```
사실상 동기화를 처리하지않는 `ArrayList`와 같아진다.
즉, 모든 스레드들이 `get`메서드를 동시적으로 수행할 수 있는 것이다.

### SynchronizedList vs CopyOnWriteList
`CopyOnWriteList`가 먼저 나왔고 읽기 동작의 performance가 좋다고 해서 우위에 있는 것이 아니다.
왜냐하면 쓰기 동작의 `copy`가 상당한 오버헤드를 발생시키기 때문이다.
즉, *쓰기 동작이 적고 읽기 동작이 웓등히 많은 상황 그리고 리스트의 크기가 작은 경우에
적용하는 것이 유리하다.* (다중스레드 환경을 가정하고 있다)

### ConcurrentHashMap
`ConcurrnetHashMap`에서는 동기화 블럭의 범위를 최소화 `CAS`알고리즘 등을 통해
동기화에서 오는 오버헤드를 줄인다.
이때도 읽기 작업에서는 synchronized가 없기 때문에 동시적으로 수행할 수 있다.
따라서 쓰기 메서드 중 하나인 `putVal`을 이해하는 것이 가장 중요하다.

`ConcurrentHashMap`의 `putVal()`

```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
                        if (fh >= 0) {
                            binCount = 1;
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
                        else if (f instanceof TreeBin) {
                            Node<K,V> p;
                            binCount = 2;
                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                           value)) != null) {
                                oldVal = p.val;
                                if (!onlyIfAbsent)
                                    p.val = value;
                            }
                        }
                    }
                }
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    }
```

##### 설명 추가바람.



