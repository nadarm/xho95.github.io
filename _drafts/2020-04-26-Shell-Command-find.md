---
layout: post
comments: true
title:  "macOS: Shell Command find (find 명령)"
date:   2020-04-26 10:00:00 +0900
categories: macOS CLI shell command find
---

### 사용법

루트 디렉토리를 기준으로 그 이하의 디렉토리에서 이름이 **socket.h** 인 파일을 찾으려면 아래와 같이 하면 됩니다. [^daum-241]

```
$ find / -name "socket.h"
```

> `-print` 옵션이 있는데, 어떤 의미인지 알아봅니다.

### 고찰하기

쉘 명령의 경우 운영 체제별로 같은 것이 있고 다른 것이 있는데, 각 명령마다 어떤 운영 체제에서 사용할 수 있는지 표기할 필요가 있습니다.

때때로 Docker 같은 일종의 프레임웍 (?) 에서도 사용할 수 있는 명령이 있는데 이들도 정리하도록 합니다. 명령에 따라 CLI 를 지원하는 곳에서 공통으로 사용할 수 있는 명령이 있을 수 있습니다.

### 참고 자료

[^daum-241]: [리눅스 쉘에서 파일찾기 - 하위폴더 포함](http://blog.daum.net/_blog/BlogTypeView.do?blogid=0R4ed&articleno=241&categoryId=28&regdt=20110721151540)
