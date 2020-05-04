여기서는 맥에서 Postgresql을 사용하는 방법에 대해서 정리합니다. 

[PostgreSQL](https://www.postgresql.org)은 Django와의 연동이 가장 편하다고 책에 소개가 되어 있으며, 오픈 소스 데이터베이스이기 때문에 사용하게 되었습니다. [^postgresql] 

> 소개된 책도 나중에 링크를 연결해야 합니다.

### PostgreSQL 설치

소스를 받아서 설치하는 방법도 있지만,[^moncefbelyamani] [^multicoder] [^junho85] Sierra에서 문제를 조금 겪은 후, 결국 [Postgres.app](http://postgresapp.com)과 pgAdmin 조합을 사용하기로 결정했습니다.  [^pgAdmin] 지금은 Sierra 문제가 해결된 듯이 보이지만 굳이 잘되는 것을 새로 교체할 필요는 없으므로 Postgres.app과 pgAdmin 조합에 대해서 설명합니다.

> 위에서 Sierra 문제라고 한 것은, 한 때 Homebrew에서 Sierra 지원이 잘 안된 적이 있어서 그런 것인데, (Homebrew 글의 이슈 부분을 참고하시기 바랍니다.) 지금은 해결된 것 같습니다. 따라서 지금은 참고 자료를 보고 소스로 설치해도 큰 문제가 없을 것으로 생각됩니다. 

Postgres.app으로 PostgreSQL을 설치하는 방법은 엄청 쉽습니다. 그냥 Postgres.app을 다운 받아서 Application 폴더로 옮기면 사실상 설치가 끝나며, 바로 Postgresql을 사용할 수 있게 됩니다. [^Postgres.app]

> Postgres.app을 사용하는 것은 [OS X 에서 Postgresql 설치하는 두가지 방법](http://jonnung.blogspot.kr/2014/12/osx-postgresql-install.html)이라는 블로그 글에서도 속 편하다고 설명이 되어 있습니다. [^jonnung] [맥에서 유용한 Postgresql.app 과 pgadmin](http://abh0518.net/tok/?p=75)이라는 글도 있습니다.  [^abh0518]
> 
> 나중에 글을 전체적으로 정리해야할 것 같습니다. 

### PostgreSQL 사용법 

PostgreSQL의 사용법은 [PostgreSQL 강좌 1 -설치 및 기본 사용법-](http://www.linuxlab.co.kr/docs/97-11-4.htm) 이라는 글에 잘 정리되어 있습니다. [^linuxlab] 

[PostgreSQL 설치와 DB 만들기 (CentOS 6.6)](http://blogger.pe.kr/503)라는 글에도 간단하게 설명되어 있습니다. [^blogger-503]

#### DB 생성

PostgreSQL를 운영하려면 사용할 데이터 베이스와 사용자를 등록해야 한다. 
/usr/local/pgsql/bin 에 보면 이와 관련된 프로그램이 준비되어 있다. test라는 데이터베이스를 만들려면 createdb명령을 사용하면 된다. (말을 나의 것으로 바꾸자.)

```
$ createdb test
```

#### user 생성

linuxer라는 사용자를 등록하려면 다음과 같이 하면 된다.

```
$ createuser linuxer
```

#### 기타

createdb와 createuser에 반대되는 명령은 destrotdb와 destroyuser 명령이다. 

#### psql 방식(?)과 비교

위의 명령어들은 아래와 같이 직접 sql을 사용해서도 동일한 작업을 할 수 있는 것 같습니다.

db 생성은 create database sql 구문을 사용합니다. 

```
# create database db_name owner auth_name;
```

db 삭제는 drop database sql 구문을 사용합니다. 

```
# drop database db_name;
```

postgresql 공식 문서의 도움말을 보면 하나는 sql을 사용하는 버전이고, 하나는 app의 명령을 사용하는 버전인 듯 합니다. 결국 실질적으로는 큰 차이가 없는 것이 아닐까 생각됩니다. [^sql-createdatabase] [^app-createdb]

#### 한글 설명서

[한국 PostgreSQL](http://postgresql.kr)이란 곳에서 문서를 한글로 번역중입니다. [^postgresql-kr]

[PostgreSQL 9.6.1 문서 - 1장. 시작하기](http://postgresql.kr/docs/9.6/tutorial-createdb.html)에서 데이터베이스 생성 등에 대한 내용이 정리되어 있습니다. [^postgresql-kr-createdb]

### Django와 연동하기

나중에 정리해야 합니다. 

### pgAdmin 4 사용하기

위에서 한 DB 및 User 생성 등은 [pgAdmin 4](https://www.pgadmin.org) 라는 앱을 설치해서 사용하면 됩니다.

단일한 앱으로 되어 있기 때문에 업데이트도 무척 편한데, 일단 해당 버전을 내려받은 다음 응용 프로그램 폴더에서 기존 앱을 새 앱으로 바로 대체하면 업데이트가 됩니다. 

### 고찰하기

위의 글에서 보듯이 PostgreSQL은 PostgreSQL 자체 명령이나 SQL 구문 모두 사용할 수 있는 것 같습니다. 

각각의 경우의 차이점이나 효율 등에 대해서 살펴볼 필요가 있을 것 같습니다.

또, 최근에 Postgres.app이 2.0 버전이 나왔는데 사용법이 이전과 많이 다른 것 같습니다. 정리가 필요할 것 같습니다. 

### 참고 자료

[^postgresql]: [PostgreSQL](https://www.postgresql.org) : PostgreSQL 공식 홈페이지입니다.

[^moncefbelyamani]: [How to install PostgreSQL on a Mac with Homebrew and Lunchy](https://www.moncefbelyamani.com/how-to-install-postgresql-on-a-mac-with-homebrew-and-lunchy/)

[^multicoder]: [postgresql, postGIS, pgAdmin 설치 (mac pro)](https://multicoder.wordpress.com/2015/06/17/postgresql-postgis-pgadmin-설치-mac-pro/)

[^junho85]: [맥북(osx) 에 postgresql 설치](http://junho85.pe.kr/348)

[^jonnung]: [OS X 에서 Postgresql 설치하는 두가지 방법](http://jonnung.blogspot.kr/2014/12/osx-postgresql-install.html)

[PostgreSQL 9.6.0 Documentation](https://www.postgresql.org/docs/9.6/static/index.html)

[Setting Up PostgreSQL on Mac OSX](https://www.tunnelsup.com/setting-up-postgres-on-mac-osx/) : 맥에서 처음에 PostgreSQL 설정하는 방법에 대해서 설명한 글입니다.

[^pgAdmin]: [pgAdmin](https://www.pgadmin.org/download/macos4.php) : PostgreSQL의 도구인 pgAdmin을 다운 받을 수 있는 곳입니다. 

[^Postgres.app]: [Postgres.app](http://postgresapp.com) : PostgreSQL 서버를 시작하고 끄기 편하게 해주는 앱인 것 같습니다. 자체 쉘도 지원하고 있습니다.

[^blogger-503]: [PostgreSQL 설치와 DB 만들기 (CentOS 6.6)](http://blogger.pe.kr/503) : PostgreSQL에서 psql로 DB를 생성하는 방법이 아주 간단하게 나와 있습니다. 

[^linuxlab]: [PostgreSQL 강좌 1 -설치 및 기본 사용법-](http://www.linuxlab.co.kr/docs/97-11-4.htm) : KLUG 회장님이신 한동훈님이 쓰신 글입니다. 글이 길지만 읽어볼만한 가치가 충분한 좋은 글인 것 같습니다. 

[^abh0518]: [맥에서 유용한 Postgresql.app 과 pgadmin](http://abh0518.net/tok/?p=75)

[^postgresql-kr]: [한국 PostgreSQL](http://postgresql.kr)

[^postgresql-kr-createdb]: [PostgreSQL 9.6.1 문서 - 1장. 시작하기](http://postgresql.kr/docs/9.6/tutorial-createdb.html)

[^sql-createdatabase]: [CREATE DATABASE](https://www.postgresql.org/docs/current/static/sql-createdatabase.html)

[^app-createdb]: [createdb](https://www.postgresql.org/docs/9.6/static/app-createdb.html)

[Postgres Guide](http://postgresguide.com)

[^pgadmin]: [pgAdmin 4](https://www.pgadmin.org)

[PostgreSQL](https://ko.wikipedia.org/wiki/PostgreSQL)