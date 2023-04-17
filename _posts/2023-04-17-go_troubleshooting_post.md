---
layout: post
title: "첫 포스트"
date: "2023-04-17 00:00:00 +0900"
categories: go
last_modified_at: "2023-04-17 00:00:00 +0900"
---

Error loading workspace: gopls was not able…
--
VS Code에서 go 익스텐션 설치 후 간단한 코드를 작성했더니 위와 같은 에러가 발생했다

```
Error loading workspace: gopls was not able to find modules in your workspace. When outside of GOPATH, gopls needs to know which modules you are working on. You can fix this by opening your workspace to a folder inside a Go module, or by using a go.work file to specify multiple modules. See the documentation for more information on setting up your workspace: [https://github.com/golang/tools/blob/master/gopls/doc/workspace.md](https://github.com/golang/tools/blob/master/gopls/doc/workspace.md)
```

settings.json 파일을 변경해서 조치완료

![Foo](/assets/img/gopls.png)

좌측하단 톱니바퀴 → Setting에서 gopls를 검색

Edit in settings.json 를 클릭하여 settings.json 파일에 아래와 같이 추가한다

```
"experimentalWorkspaceModule": true,
```
![Bar](/assets/img/json.png)