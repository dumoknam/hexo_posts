---
title: React Native 일기장 앱 개발 (End) - 글쓰기 기능
date: 2017-03-20 23:59:59
categories:
  - 개발
  - React Native
tags:
  - React Native
  - App
---

일기 쓰기 화면과 저장 기능을 만들자

<!-- more -->

_한달간 많은 일이 있었다. 조금 정리가 되어 공부 재개!_

___
# 기존 코드 정리
글쓰기 창 구현 전에 기존 컴포넌트들을 정리하는 작업부터 진행해보자.

## DiaryListView

리스트 뷰의 일기목록은 하드코딩으로 항상 1월 데이터만 나오게 되어 있었다.
현재 월의 일기가 나오도록 realm 데이터를 읽는 부분을 바꿔보자.
`ListView`의 `DataSource`를 설정하기 위해 호출하는 `realm`의 `getDiaryByYearMonth`의 파라미터를 변경하여야 한다. 아래와 같이 현재 날짜의 연도, 월을 전달하도록 하자.
```javascript
let diaryData = diaryRealm.getDiaryByYearMonth(new Date().getFullYear(),new Date().getMonth());
```

## DiaryTitleView

`ListView`의 `Title`을 표시하는 부분도 변경이 필요하다.
`headerText`가 현재 연-월 이 되도록 변경하자.
```javascript
var headerText = [new Date().getFullYear(),new Date().getMonth()+1].join(' - ');
```

## TempDiaryContents

임시 일기 데이터는 더이상 필요 없다. `TempDiaryContents.js` 파일을 삭제하자.

___
# Modal
첫 포스트에서 만들었던 글쓰기 `+` 버튼을 누를때
새로운 창이 뜨도록 할 것이다.
이런 기능을 구현하기 적당한 것이 [Modal View][react-native-modal-link]이다.

구현 방법은 이렇다.
Modal 은 화면에 표시된느 여부를 `modalVisible` 이란 이름의 `prop`으로 관리한다. 이 값을 글쓰기 `+`버튼을 누를때 `true`로 전달하여 나타나게 하고, 글쓰기 취소를 누르면 `false`로 변경되게 하여 화면에서 사라지게 할 것이다.

우선 `src\components\WriteModalView.js` 파일을 생성하자.

## WriteButton

글쓰기 버튼의 구현 내용은 아래와 같이 변경되었다.
{% gist 5dbabe7ad3b30c3bbd431d3bc3321120 WriteButton.js %}

`WriteButton`의 자식으로 `WritemodalView`를 두었고, 자식의 `prop`을 `WriteButton`이 `state`로 관리하게 된다.
- `WriteButton`의 `state`인 `modalVisible`변수 --> `WriteModalView`의 `prop`인 `modalVisible`변수
- `WriteButton`의 `state`인 `hideModal`함수 --> `WriteModalView`의 `prop`인 `hideModal`함수

자식인 `WriteModalView`에서 `modalVisible`을 변경할 수 있게 하려고
`hideModal`이란 함수를 만들어서 `WriteModalView`의 `prop`으로 전달하였다.
자식인 `WriteModalView`에서 `hideModal`함수를 호출하게 되면
부모인 `WriteButton`의 `state`인 `modalVisible`이 변경될 것이고,
이것이 다시 자식인 `WriteModalView`의 `prop`인 `modalVisible`을 변경시킬 것이다.
_구조에 대한 이해가 어려울 수 있다_


## WriteModalView

글쓰기 Modal View의 구현 내용은 다음과 같다.
{% gist 17044097f89338ca814d4b6ff01e24e3 WriteModalView.js %}

각 라인별로 역할을 살펴보자.
1. 1~3 라인: 사용할 컴포넌트 들을 import 하는 부분이다.
2. 4 라인: db 연동이 필요하기 때문에 realm도 import 한다.
3. 6~14라인: 날짜를 다루는데 사용할 함수를 구현해 두었다.
4. 30~62라인: `render()`함수 구현 부분이다. 눈여겨 볼 부분은 `Modal` 컴포넌트를 설정한 33~37라인과 사용자가 입력한 내용을 `contextText`라는 `state`로 저장하는 46라인이다. 글쓰기와 글쓰기 취소는 두개 버튼을 두어서 구현하였다. 글쓰기를 누르면 `write`함수를 호출하고, 안쓰기를 누르면 `modalVisible`을 변경하여 화면을 사라지게 만들었다.
5. 17~29라인: 핵심 기능인 글쓰기 `write` 함수이다. 현재 날짜와, 사용자가 입력한 내용(`contentText`)을 가져와서, realm에 등록한다.

## 확인

글쓰기 버튼을 누르면 아래 화면이 나온다
![check_01](last_01.png)

막 써보자
![check_02](last_02.png)

R키를 두번 눌러서 새로고침 하면 등록한 내용이 잘 표시되는 것을 볼 수 있다.
![check_03](last_03.png)

___
# Ending

여러 일이 겹치면서 아주아주 더디게 진행하던 `react native` 첫번째 앱은 글쓰기 구현을 마지막으로 중단한다.
기능을 추가하자면 끝도 없이 많지만 `realm`을 연동한 대강의 구조를 구현했다는데에 의의를 둔다.
`react native`에 `Map`, `AR`, `firebase` 등등 다른 모듈을 사용한 새 앱을 또 만들어 볼 것이다.

그럼 오늘은 이만!

[react-native-modal-link]: <https://facebook.github.io/react-native/docs/modal.html>
