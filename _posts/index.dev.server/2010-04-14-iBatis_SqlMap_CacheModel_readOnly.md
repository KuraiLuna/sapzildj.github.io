---
title: "iBatis SqlMap CacheModel을 통해 캐싱된 객체는 readOnly=true이면 안전한가"
layout: post
category : dev.server
tags : [orm,ibatis,cache]
---
{% include JB/setup %}

개요
----

iBatis에서 제공하는 SqlMap xml상에서 정의된 sqlmap query에 대해

CacheModel정의 만으로 손쉽게 쿼리에 대한 결과를 캐싱 할 수가 있다.(참조)

 

그리고 성능상으로 크리티컬하게 이슈가 있지 않는 이상

로직을 담고 있는 BO객체에서 캐싱 로직을 담고 있는 객체와 연관되는 것은

캐싱 때문에 로직이 변경되는 상황이 생기므로

개인적으로 CacheModel을 통한 캐싱을 더 선호한다.

 

그런데 팀원 분께서 캐싱된 결과값에 대한 변조가 가능하다는 제보를 해주셨다.(sqlmap의 cacheModel의 readOnly 어트리뷰트의 디폴트 값은 true임)

iBatis를 쓰는 입장에서는 당연히 캐싱된 객체의 복제본이 리턴될 것이라고 생각하지만

실상은 그렇지 않았던 것이다.

 

코드 확인
-------

확인한 버전 = jdk 1.4기반의 ibatis-2.3.0.677

iBatis의 캐싱된 객체를 가져오는 부분 중요한 부분만 코드를 보면 이렇다.

- **LRU**

```java
 /**
   * Default constructor
   */
  public LruCacheController() {
    this.cacheSize = 100;
    this.cache = Collections.synchronizedMap(new HashMap());
    this.keyList = Collections.synchronizedList(new LinkedList());
  }
  
  /**
   * Get an object out of the cache.
   *
   * @param cacheModel The cache model
   * @param key        The key of the object to be returned
   * @return The cached object (or null)
   */
  public Object getObject(CacheModel cacheModel, Object key) {
    Object result = cache.get(key);
    keyList.remove(key);
    if (result != null) {
      keyList.add(key);
    }
    return result;
  }
```

- **FIFO**

```java
 /**
   * Default constructor
   */
  public FifoCacheController() {
    this.cacheSize = 100;
    this.cache = Collections.synchronizedMap(new HashMap());
    this.keyList = Collections.synchronizedList(new LinkedList());
  }
  
  /**
   * Get an object out of the cache.
   *
   * @param cacheModel The cache model
   * @param key        The key of the object to be returned
   * @return The cached object (or null)
   */
  public Object getObject(CacheModel cacheModel, Object key) {
    return cache.get(key);
  }
```

- **OSCACHE**

```java
 private static final GeneralCacheAdministrator CACHE = new GeneralCacheAdministrator();
 
 public Object getObject(CacheModel cacheModel, Object key) {
    String keyString = key.toString();
    try {
      int refreshPeriod = (int) (cacheModel.getFlushIntervalSeconds());
      return CACHE.getFromCache(keyString, refreshPeriod);
    } catch (NeedsRefreshException e) {
      CACHE.cancelUpdate(keyString);
      return null;
    }
  }
```


즉, 객체를 복제해서 리턴하지 않는 것은 물론, 그 어디에도 캐싱된 결과에 대한 어떤 변조를 막는 코드가 없다.

하다 못해 List를 Collections.unmodifiableList로 추가/삭제를 막는 정도의 간단한 처리도 안한다.


결론
---
iBatis의 sqlmap상에서의 cacheModel에서 readOnly어트리뷰트(=true)는

캐싱된 객체를 읽기 전용으로 사용할 것이라고 알려주는 역할만 할 뿐 그것을 강제하진 않는다.

 

단순히 생각해보면 성능상의 이슈가 아닐 까 생각을 해본다.

매번 CacheStatement로 접근할 때마다 객체를 복사해서 리턴한다면 엄청난 성능저하가 있을 것이다.

차라리 **프레임웍을 쓰는 클라이언트 코드 개발자들이 정확히 알고 잘 써주는 게 여러가지로 나을 것 같다는 결론**.


보너스
-----
readOnly=false인 경우는 그럼 어떻게 처리를 할까?

캐싱 객체에 접근하는 CachingStatement.executeQueryForList, executeQueryForObject에서는

캐싱된 객체를 가져오기 위한 캐시 키를 얻기 위해 CachingStatement.getCacheKey() 메소드를 호출하는 데 이때 이런 식으로 처리를 한다.

```java
  public CacheKey getCacheKey(StatementScope statementScope, Object parameterObject) {
    CacheKey key = statement.getCacheKey(statementScope, parameterObject);
    if (!cacheModel.isReadOnly() && !cacheModel.isSerialize()) {
      key.update(statementScope.getSession());
    }
    return key;
  }
```

즉, readOnly=false 이거나 serialize=false 인 경우에 iBatis에서 statement를 실행하는 단위(세션)을 통해

캐시 키를 한번더 해싱하도록 해서 다른 객체가 리턴되도록 한다.

이렇게 하면 캐싱 객체를 변조 했을 때 영향은 안 받겠지만 캐싱 효과가 많이 떨어질 것으로 보인다.


Last Updated : 2010년 4월 14일 오전 1시 1분 0초 GMT+9