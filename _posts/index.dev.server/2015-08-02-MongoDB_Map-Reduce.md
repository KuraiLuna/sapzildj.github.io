---
title: "MongoDB Map-Reduce"
layout: post
category : dev.server
tags : [centos, mapreduce, mongodb]
---
{% include JB/setup %}

개요
----

MongoDB의 Map-Reduce기능을 이용해서 통계적 연산의 결과를 확인해보자.
여기에서는 13601건의 로또 당첨점 데이터로 부터 각 판매점 별로 1등/2등
당첨 횟수를 각각 구해본다.

준비
----

-   로또 당첨점 데이터 한 개는 필요 없는 field를 제외하면 아래와 같다.

```javascript
{
  "marketaddress": "강원 원주시 관설동 1771-6 패밀리마트관설에버빌점",
  "marketname": "대박로또복권",
  "ranking": 1
}
 
```

-   marketaddress = 판매점 주소
-   marketname = 판매점 이름
-   ranking = 1등이면 1, 2등이면 2

mapReduce 함수 정의
-------------------

-   mongo 콘솔에 함수명을 입력하면 해당 함수를 확인 가능함.
-   3번째 파라미터를 string으로 넘기면 out collection 이름으로 자동으로
    인식되는 것을 알 수 있음.

```javascript
> db.lottowinningmarket.mapReduce
function (map, reduce, optionsOrOutString) {
    var c = {mapreduce:this._shortName, map:map, reduce:reduce};
    assert(optionsOrOutString, "need to supply an optionsOrOutString");
    if (typeof optionsOrOutString == "string") {
        c.out = optionsOrOutString;
    } else {
        Object.extend(c, optionsOrOutString);
    }
    var raw = this._db.runCommand(c);
    if (!raw.ok) {
        __mrerror__ = raw;
        throw "map reduce failed:" + tojson(raw);
    }
    return new MapReduceResult(this._db, raw);
}
 
```

Coding
------

```javascript
// 동일한 이름의 collection이 있었다면 삭제
// mapReduce 함수의 option을 이용하는 방법도 있지만 이 방법이 더 맘에 들어서
db.lottowinningmarket.drop();

db.lottowinningmarket.mapReduce(
function () {
     var cnt1 = 0;
     var cnt2 = 0;
     if (this.ranking == 1) {
          cnt1++;
     } else {
          cnt2++;
     }
     // 판매점 주소를 키로 사용하려고 하는데 중간에 공백으로 인해 다른 판매점으로 처리되지 않도록 공백을 제거함.
     emit(this.marketaddress.replace(/ /g,''), {"win1_count": cnt1, "win2_count": cnt2, "marketname": this.marketname});
},
function (key, emits) {
     var win1_cnt = 0;
     var win2_cnt = 0;
     for (var i in emits) {
          if (emits[i].win1_count > 0) {
               win1_cnt++;
          } else if (emits[i].win2_count > 0) {
               win2_cnt++;
          }
     }

     return {
               "win1_count": win1_cnt,
               "win2_count": win2_cnt,
               "marketname": emits[0].marketname
               };
},
"lottowinningmarket_totals"
);

// 당첨 횟수가 높은 판매점 순으로 빠르게 가져오기 위해 인덱스 추가
db.lottowinningmarket_totals.ensureIndex({"value.win1_count": -1, "value.win2_count": -1});
```

-   조심해야 할 부분은 map함수의 2번째 파라미터와 reduce함수의 return
    json은 형태가 같아야 한다는 점이다.
    -   reduce 함수는 맵 단계나 이전 리듀스 단계의 결과를 반복적으로
        호출할 수 있어야
        한다.([http://www.yes24.com/24/goods/5161478?scode=032&OzSrank=4
        MongoDB
        완벽가이드](http://www.yes24.com/24/goods/5161478?scode=032&OzSrank=4 MongoDB 완벽가이드 "wikilink"),
        p123)

결과
----

-   mongo 콘솔 결과

```javascript
{
    "result" : "lottowinningmarket_totals",
    "timeMillis" : 1992,
    "counts" : {
        "input" : 13601,
        "emit" : 13601,
        "reduce" : 3060,
        "output" : 6256
    },
    "ok" : 1,
}
```

-   결과로 생긴 lottowinningmarket\_totals collection의 document를
    열어보면..

```javascript
{
  "_id": "경북안동시북문동23-7번지",
  "value": {
    "win1_count": 3,
    "win2_count": 1,
    "marketname": "행운의집"
  }
}
```

-   win1\_count: 3 이므로 1등 3번 당첨, win2\_count: 1 이므로 2등 1번
    당첨 되었다는 것을 알 수 있다.

참고
----

-   친절하게 그림까지 보여주며 잘 설명된 공식 홈페이지 =
    <http://docs.mongodb.org/manual/core/map-reduce/>
-   공식 홈페이지의 mapReduce 함수 정의 =
    <http://docs.mongodb.org/manual/reference/method/db.collection.mapReduce/>
-   하나의 document에서 여러 번 emit 호출 예제 =
    <http://isurues.wordpress.com/2013/05/28/mongodb-map-reduce-sample/>
-   mongodb mapreduce 성능 향상 팁 =
    <http://edgystuff.tumblr.com/post/54709368492/how-to-speed-up-mongodb-map-reduce-by-20x>
-   소장하고 있는 유일한 mongodb 책, MongoDB완벽가이드 =
    <http://www.yes24.com/24/goods/5161478?scode=032&OzSrank=1>
-   Javascript online editor = <http://jsbin.com/>
    -   텍스트 에디터로 코딩하다 오타나서 한참을 고생했는데, 이 녀석이
        이때 많은 도움이 되었음.
