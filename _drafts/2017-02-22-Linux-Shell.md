여기서는 리눅스 (linux) 의 쉘 (Shell) 에 대해서 정리합니다. 나중에 리눅스 쉘로 분리할지 아니면 OS 와 상관없이 쉘로만 정리할지 생각해 봅니다.

일단 리눅스 쉘을 이해하는데는 [unix / linux: shell (쉘)을 이해하자.](http://blogger.pe.kr/300) 라는 글이 상당히 좋은 것 같습니다. [^blogger-300]

### 들어가며 

#### 쉘 이란 무엇인가?

쉘이란 기본으로 운영 체제에서 사용자가 입력하는 명령을 읽고 해석하여 대신 실행해주는 프로그램을 말합니다.  

#### 쉘의 환경변수

bash 에서는 env 명령으로 환경 변수를 확인할 수 있습니다. PATH 등이 환경 변수입니다. 

#### 환경 변수의 설정

쉘의 환경 변수는 로그인 할 때 설정됩니다. 사용자 환경은 profile 에서 설정하는데 profile 에는 글로벌 profile 과 계정별 profile 이 있습니다. 그리고 이 프로파일의 위치와 이름은 운영 체제와 쉘의 종류에 따라 조금씩 다릅니다. 

sh 와 ksh 는 **/etc/profile** 과 사용자 홈 디렉토리의 **.profile** 입니다. 

bash 는 **/etc/profile** 과 **/etc/bashrc** 파일과 사용자 홈 디렉토리의 **.bashrc** 파일입니다. 

csh 는 **/etc/csh.login** 과 사용자 홈 디렉토리의 **.cshrc** 입니다.  

### 사용 가능한 쉘 확인하기

#### 1. /etc/shells 파일 열어보기

리눅스에서 사용 가능한 쉘을 확인하는 방법은 아래와 같습니다. [^milvus-32] 

```
$ cat /etc/shells
```

이를 보면 쉘과 관련한 설정은 **/etc** 폴더의 **shells** 라는 파일에 저장되어 있음을 알 수 있습니다. 결과는 아래와 유사합니다.

```
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
...
```

#### 2. /etc/passwd 파일 열어보기

**/etc/passwd** 파일은 실제로는 꽤 방대합니다. 따라서 파일 내용 전체를 보기보다는 필터를 거쳐서 확인하는 것이 좋습니다.

다음과 같이 `grep` 명령을 사용해서 **/etc/passwd** 파일 중에서 `root` 가 있는 부분만 확인합니다.

```
root: ... /root:/bin/bash
```

그러면 `/bin/bash` 를 사용하는 것을 확인할 수 있습니다.

### 현재 사용 중인 쉘 확인하기

#### `echo` 명령 사용하기 

현재 사용중인 쉘을 확인하려면 다음과 같이 `echo` 명령을 사용하면 됩니다. [^echo]

```
$ echo $SHELL

/bin/bash
```

지금은 bash 쉘을 사용하고 있음을 알 수 있습니다.

### 쉘 변경하기

#### 1. `chsh` 명령 사용하기

쉘 변경은 `chsh` 명령을 사용하면 됩니다.

```
$ chsh

Password:
```

위와 같이 비밀 번호를 요구하는데 비밀 번호를 입력하고 넘어갑니다.

```
Changing the login shell for ...
Enter the new value, or press ENTER for the default
		Login Shell [/bin/bash]:
```

그러면 위와 같이 현재 로그인(Login) 쉘을 보여주면서 새로운 쉘을 입력하라고 나타납니다. 여기에 변경할 쉘 이름을 지정하면 되는데 앞서 **/etc/shells** 파일에 출력된 것 중 하나를 입력하면 됩니다. 예를 들면, `/bin/sh` 와 같이 입력하면 됩니다. 

이렇게 하고 `$ echo $SHELL` 명령으로 확인해 보면 아직 쉘이 그대로 인 것을 볼 수 있는데, 이는 쉘의 변경은 로그인 과정에서 일어나기 때문입니다. 따라서, 쉘의 변경을 확인하려면 컴퓨터를 로그아웃 하고 다시 로그인 해야 합니다.

#### 2. `chsh -s` 옵션 사용하기

쉘 변경 과정은 다음과 같이 옵션을 줘서 한번에 끝낼 수도 있습니다. [^hostway-8170]

```
$ chsh -s /bin/bash
```

위와 같이 하면 비밀 번호를 입력하고 쉘은 변경됩니다.

#### 3. /etc/passwd 파일 변경하기

[리눅스: SHELL확인/바꾸기](http://egloos.zum.com/Esunny/v/4077463) 글을 보면 쉘 변경은 `chsh` 명령 말고도, **/etc/passwd** 파일에서 `root` 에 해당 하는 부분을 직접 변경해도 되는 것 같습니다. 하지만 직접 파일 내용을 보면 굳이 무리해서 그럴 필요는 없을 것 같습니다. [^egloos-4077463]

가능한 `chsh` 명령을 사용하는 것이 좋을 것 같습니다.

### 로그인 쉘과 비로그인 쉘

[리눅스 profile, bashrc 와 login shell vs non-login shell 의 이해](http://webdir.tistory.com/126) 글을 참고 합니다. [^webdir-126]

### 고찰하기

**/etc/passwd** 와 **/etc/shells** 파일들의 역할에 대해서 알아볼 필요가 있을 것 같습니다. **/etc/passwd** 에 대해서는 [UNIX / Linux: 사용자 정보, 그룹 정보](http://eunguru.tistory.com/88) 글이 아주 좋은 것 같습니다.[^eunguru-88]

더 나아가서 리눅스 부팅 과정에서 어떤 파일들이 영향을 미치는지 파일들간의 적용 순서는 어떻게 되는지 확인이 필요할 것 같습니다.

### 참고 자료

[^blogger-300]: [unix / linux: shell (쉘)을 이해하자.](http://blogger.pe.kr/300) 글이 아주 좋은데 나중에 쉘 자체에 대해서 정리해야 할 것 같습니다.

[^milvus-32]: [리눅스 쉘 확인 및 변경 방법](http://milvus.tistory.com/32)

[^echo]: echo 명령은 뒤에 나오는 환경 변수를 인자를 읽어 와서 표준 출력으로 보여주는 명령입니다.

[^egloos-4077463]: [리눅스: SHELL확인/바꾸기](http://egloos.zum.com/Esunny/v/4077463) 글을 보면 쉘 변경은 `chsh` 명령 말고도, **/etc/passwd** 파일에서 `root` 에 해당 하는 부분을 직접 변경해도 되는 것 같습니다. 하지만 직접 파일 내용을 보면 굳이 무리해서 그럴 필요는 없을 것 같습니다.

[^hostway-8170]: [사용 가능한 쉘 확인 및 변경법](http://faq.hostway.co.kr/Linux_ETC/8170) 에는 몇 가지 추가적인 방법에 대해서 설명하고 있습니다.

[^eunguru-88]: [UNIX / Linux: 사용자 정보, 그룹 정보](http://eunguru.tistory.com/88) 글을 보면 /etc/passwd 파일은 시스템 관리자가 사용자 계정을 만들 때마다 해당 사용자와 관련된 정보를 별도로 저장하는 파일이라고 합니다. 즉 이 파일은 개별 사용자에 대한 정보들로 이루어져 있습니다. 콜론 (`:`) 구분자를 사용하여 7개의 필드로 구분됩니다. 내용이 알차서 이 참고 자료를 활용해서 정리하면 좋을 것 같습니다.

[^webdir-126]: [리눅스 profile, bashrc 와 login shell vs non-login shell 의 이해](http://webdir.tistory.com/126)
