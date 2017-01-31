---
title: React Native 일기장 앱 개발 (3) - realm DB 연동 클래스
date: 2017-01-14 16:19:20
categories:
  - 개발
  - React Native
tags:
  - React Native
  - App
---

모바일 데이터베이스인 [realm][realm]을 연동할 클래스를 만들자

<!-- more -->

_아이맥 하드가 급 사망하여 윈도우 PC로 대체하였다.. 이번포스팅 부터는 android 기반으로 진행한다._

# Realm

일기를 저장하기 위해서는 데이터베이 연동이 필요하다
모바일에서 주로 사용하는 데이터베이스는 `sqlite` 이 있지만
모바일 환경에 더 최적화 되었다고 하는 `realm(렘)`을 사용해보자.

## Realm 설치

```sh
npm install --save realm
react-native link realm # React Native >= 0.31.0
```

다른 node 모듈처럼 `npm`(혹은 `yarn`)으로 설치하고,
link 명령어가 native 코드에 필요한 import, new 등의 작업을 해준다.
(android는, `android\app\src\main\java\com\diary` 안의 java 파일에 realm 관련 import 구문이 추가된 것을 볼 수 있다)

### error fix (windows)

realm 사용시 두가지 주의할 점이 있다.
_포스팅은 0.14버전 기준으로, 이후 버전은 다를 수 있다_

1. cmd, powershell, babun 등 쉘은 반드시 **관리자** 권한으로 실행해야 한다.
2. `node_modules\realm\android\build.gradle`의 task send()안의 71 번째 줄 version을 version = '1' 로 변경한다.

2번은 하드코딩 하지 않고, npm을 path까지 적으면 된다고도 한다. 해보진 않았다.

## DiaryRealm
realm DB에 데이터를 생성, 수정, 삭제, 조회하는 모든 기능은 `DiaryRealm.js` 한 파일에 구현할 것이다.
src 디렉토리 밑에 db 디렉토리를 생성하고, DiaryRealm.js 파일을 생성하자.

`src\db\DiaryRealm.js`

### Model Schema
첫번째로 일기 DB를 표현할 모델을 만들자.
```javascript
const DiarySchema = {
  name: 'Diary',
  primaryKey: 'id',
  properties: {
    id: 'int',
    year: { type: 'int', indexed: true },
    month: { type: 'int', indexed: true },
    day: 'int',
    content: 'string'
  }
};
```
모델을 정의하는 schema에 대한 내용은 [문서][realm-doc-model]를 참고하자.
name에 테이블명을 정의하고 pk, DB의 컬럼에 해당하는 property를 정의한다.

일기는 id와 숫자 타입의 연, 월, 일, 그리고 string 타입의 일기 내용 문자열을 property로 갖는다.
연,월,일을 date 타입으로 하지 않은건, realm 이 아직 date 타입엔 index를 지원하지 않고,
검색 조건에 date를 between 형식으로 비교하는 것도 안되는 것 같아서 연-월-일로 나눠 두었다.

### 새로 생성할 일기의 ID값 구하기
PK인 id 값은 고유해야 하므로, 일기를 쓸때마다 매번 달라져야 한다.
DB의 시퀀스 처럼 자동으로 증가하는 기능은 없는 것 같아서 함수로 구현했다.
```javascript
getNextId(){
  var realm = this.realm;
  return realm.objects('Diary').length+1;
}
```
모든 Diary 데이터 개수에 1을 더하는 형태이다.

### Create, 일기 데이터 생성
```javascript
create(data){
  var realm = this.realm;
  realm.write(()=> {
    realm.create('Diary', data);
  });
}
```
데이터 생성은 realm의 create 함수를 호출한다. 

data 파라미터는 schema와 동일한, 아래와 같은 형태이면 된다.
``` javascript
var diary_data = {
  id: 1,
  year: 2017,
  month: 0,
  day: 15,
  content: '첫번째 데이터'
};
```

### Update, 일기 데이터 수정
수정은 생성과 동일한 함수를 사용한다.
optional 인자인 세번째 인자 값에 `true` 를 호출하면 update로 동작한다.
```javascript
update(data){
  var realm = this.realm;
  realm.write(()=> {
    realm.create('Diary', data, true);
  });
}
```

### Filtering, Sorting, 일기 데이터 조회
일기 목록은 한번에 한 달의 목록만 조회할 것이다.
조회할 연, 월을 인자로 전달받게 하였다.
```javascript
getDiaryByYearMonth(year, month){
  var realm = this.realm;
  return realm.objects('Diary').filtered('year == $0 and month == $1', year, month).sorted('day', true);
}
```
조건을 지정하여 조회하는 메소드가 `filtered` 이고, 정렬 메소드가 `sorted` 이다. 
전체 Diary 중에 인자로 전달받은 year와 month를 각각 $0, $1에 지정하여 조회하도록 하였다.

날짜별로 정렬할 것이므로 `sorted`에 정렬 기준인 `day`를, 역순으로 정렬하기 위해 `true`를 전달한다.
`sorted('day')` 처럼 정렬 기준만 쓰거나, 두번째 인자로 `false`를 쓰면 날짜 순서대로 정렬이 된다.

### Delete, 일기 데이터 삭제
삭제할 데이터의 `id`값을 전달받고, `delete`메소드를 호출하여 삭제한다.
```javascript
delete(diaryId){
  var realm = this.realm;
  res = realm.delete(realm.objects('Diary').filtered('id == $0', diaryId));
}
```

## DiaryRealm
DB의 기본 기능인 CRUD에 대한 구현이 다 되었다.
전체 파일 내용은 이렇다.
{% gist d435b7633e3f4268ee1eb179abc1ac7d DiaryRealm.js %}

잘 연동되나 확인해보자.
두개 파일만 수정하면 된다.

## DiaryListView 수정
리스트뷰에서 수정할 부분은 `state`의 `datasource`를 지정하는 곳과 `renderRow` 이다.
{% gist e22ea01d28fe83bfd453583d3ea1a5b5 DiaryListView.js %}

임시 일기 데이터인 `TempDiaryContents` 를 불러오던 코드를 삭제하고,
`realm`의 `getDiaryByYearMonth` 메소드 결과값을 datasource로 설정하도록 변경하였다.
_javascript의 월(month)은 0~11이어서 1월을 0으로 사용하였다_

2017년 1월을 하드코딩 해서 사용중인데.. 날짜를 선택하는 기능도 다음에 추가될 것이다.

`renderRow`에서 바뀐건 타이틀 텍스트 부분이다. 
위에서 정의한 schema에 `title` 이라는 property가 없으므로, 
`rowData.title` 를 `rowData.month+1 - rowData.day` 로 변경하였다.

## WriteButton 수정

{% gist 40fc61aaef6fe8930447e12575b41819 WriteButton.js %}

WriteButton 터치시 Alert이 나타나던 부분을 삭제하고, 임의로 3개의 데이터를 생성하도록 변경하였다.
id값은 매번 `getNextId`를 호출하여 새로운 값을 받아오도록 하였다.

## 확인

`react-native run-android`로 에뮬레이터에 앱을 실행하고,

![before](before.png)

글쓰기 버튼 (+)을 누르면, 아무 변화가 없다..
_데이터가 추가되면 리스트가 자동 갱신 되어야 하는데, 이 기능은 다음 포스팅에서.._

키보드의 R 키를 두번 눌러 페이지를 리로드 하면 잘 보인다.
![after](after.png)

realm 연동이 되었으니, 다음엔 글쓰기 화면을 만들어 보자.

오늘은 이만!

#### 참고 - console.log
`console.log` 로 출력하는 값은 바로 볼 수가 없는데,
`react-native log-android` 명령을 실행하면 이곳에 나타난다.

중간중간 값을 찍으면서 진행하고자 한다면, `log-android`나 `log-ios`를 사용하자.

[realm]: <https://realm.io>
[realm-doc-model]: <https://realm.io/docs/javascript/latest/#models>
