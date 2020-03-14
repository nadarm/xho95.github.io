---
layout: post
comments: true
title:  "Jekyll: Google Search Console and Analytics"
date:   2020-03-15 11:30:00 +0900
categories: Jekyll Blog Google Search Console
---

Goole Search Console 에서 DNS record 는 따로 DNS 가 있는 경우 사용하는 것입니다.[^google-domain] 즉, 따로 도메인 이름이 있을 때 쓰는 기능입니다. 다만 좀 더 확인 필요합니다.

HTML tag 방식을 사용할 수 있으며, 이 때는 `_config.yml` 파일에서 전체 적용 가능합니다.

Google Analytics 도 Search Console 과 마찬가지로 추적 코드는 `<body>` 영역이 아니라 `<head>` 영역에 있어야 합니다.

설정은 Google Search Console 로 이동한 다음, **Settings > Ownership verification** 메뉴에서 할 수 있습니다.

제대로 설정한 다음에 **Verify** 버튼을 누르면 아래와 같이 성공했다는 메시지를 받습니다.

### 참고 자료

[^google-domain]: [How to setup google domain for github pages](https://dev.to/trentyang/how-to-setup-google-domain-for-github-pages-1p58)


## 아래 내용은 확인이 필요합니다.

### Jekyll 블로그에 Google Analytics 달기

Jekyll 블로그에 Goole Analytics를 달아봅니다.

먼저 구글 계정을 만들거나 로그인 합니다.

실제 다는 방법은 외국 글을 참고한 것 같습니다.

> 나중에 정리해야 합니다.

### 데이터 분석하기

관련 자료를 정리합니다.

#### 시각화 자료

[Data Studio](https://www.google.com/analytics/data-studio/) 같은 도구를 사용하면 됩니다. [^data-studio]

### 참고 자료

[Accounts](https://analytics.google.com/analytics/web/?authuser=0#provision/SignUp/)

[Google Analytics setup for Jekyll](https://michaelsoolee.com/google-analytics-jekyll/) : Google Analytics를 Jekyll 블로그에 다는 방법을 설명한 글입니다.

[GitHub에 Google Analytics 달기](http://www.kmshack.kr/tag/구글-애널리틱스/) GitHub에 Google Analytics를 넣는 부분이 잘 설명되어 있는 것 같습니다.

[Google Analytics](http://www.google.com/analytics/ce/nrs/)
[GitHub에 Google Analytics 달기](http://www.kmshack.kr/tag/구글-애널리틱스/)

[Jekyll블로그에 Google Analytics 추가](https://dev-juyoung.github.io/jekyll/2016/08/04/google-analytics.html)

[Jekyll을 이용한 Github pages 만들기 - 심화/Google Analytics 적용](http://loustler.io/2016/09/26/github_pages_blog_google_analytics/) : 실제 코드 설명까지 되어 있어서 참고하면 좋을 것 같습니다.

[Google 웹로그 분석](http://www.google.com/analytics/)

[구글 아널리틱스를 이용하여 블로그를 완벽하게 분석하는 방법 / Google Analytics](http://www.erzsamatory.net/42) Google Analytics의 사용 방법에 대한 설명이 잘 되어 있는 것 같습니다.

[GA로 블로그 분석하기](http://www.boxnwhis.kr/2015/03/18/analyzing_blog_using_ga.html) : GA를 활용하는 방법에 대해서 잘 나와있습니다.

[^data-studio]: [Data Studio](https://www.google.com/analytics/data-studio/) : 구글에서 만든 데이터 시각화 도구인데 Analytics 와도 연동이 되는 것 같습니다.
