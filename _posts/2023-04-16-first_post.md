---
layout: post
title: "첫 포스트"
date: "2023-04-16 00:00:00 +0900"
categories: jekyll
last_modified_at: "2023-04-16 00:00:00 +0900"
---

Github 을 통한 블로그 제작 
--
생각보다 우여곡절이 많았음ㅠㅠ<br/>
다합쳐서 5시간 정도?<br/>
참고는 [https://supermemi.tistory.com/145](https://supermemi.tistory.com/145) 이 링크에서 전반적인 틀을 보고 진행했음<br/>

중간중간에 당연히 에러는 계속 발생하고 수정, 배포만 몇백번은 한 듯
내용에 포함되어 있지 않은 Git 설치 및 로그인은 사전에 진행했다<br/>

1. **Jekyll 설치 및 다운로드** 
- 당연하겠지만 여기서도 오류발생 [https://jekyllrb-ko.github.io/](https://jekyllrb-ko.github.io/) 공식 홈페이지에서 recommend로 되어있는 최신버전 +dev를 찾아서 다운로드를 진행했음
- 설치까진 쭉쭉 가더니 나중에 gem install jekyll 부분에서 오류가 발생함<br/>

```
gem install bundler
gem install jekyll
```

->오류내용을 보아하니 설치경로 관련 문제로 에러가 발생하는걸로 보였고 내 PC는 한글로 된 경로명이 포함되어 있었음
영문판으로 설치된 노트북에서 테스트해보니 쭉쭉 잘되는걸 확인 후 눈물겨운 삽질 시작
환경변수 경로 변경,, Ruby 내에서 설치경로 변경 등등 검색하고 이것저것 해봐도 모두 실패
- 해결: 기존 설치되어 있는 정보 모두 삭제 후 admin 계정으로 새로 계정을 추가한 뒤 해당 계정에서 설치를 진행해서 정상적으로 성공 ( 삭제 안하고 해서 삽질 +1)

설치 이후에는 기본 페이지로 테스트 해본 뒤 본격적으로  jekyll을 이용해서 테마를 변경한다

배포되고 있는 테마가 많아서 고민하다가 선택했음

그 다음에는 쭉쭉 진행이 되다 진행이 막힌경우가 생겼다

git add , push 가 정상적으로 되지 않았던건데 이게 git을 처음에 모르는 상태라면 상당히 헷갈린다

현재 시점 기준 가입 시 main과 master 두개의 브런치가 생기는데 

나같은 경우 변경된 정보가 master에 저장되고 main으로 화면표출이 되는 상황이였음

이걸 알고 해결하기까지 3시간정도 소요된것 같다

검색에도 master와 main이 번갈아가며 나오니 이 내용을 숙지하고 검색내용을 보는게 훨씬좋다

__※그냥 생각없이 복붙하다간 꼬일거같음__

- 참고로 변화값이 있어야 add나 push가 가능하다

→ pull로 원격저장소에 있는값을 땡겨오거나 다른폴더나 txt 파일을 생성해서 add push를 진행했다

- 해결 : master : 원하는값 main : 변경되기 직전의 값
matser의 값을 main에 덮어쓰기로 조치를 진행했고 이후에 main을 기본 브랜치로 지정했다.
- 

에러내용 : "There isn't anything to compare. Nothing to compare, branches are entirely different commit histories”
