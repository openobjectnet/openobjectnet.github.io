---
title:  "리눅스 기초 정리"
search: false
categories: 
  - linux
toc: true  
tags:
  - linux
author: 오유민 
---

# Intro
안녕하세요 오유민 사원입니다. 리눅스 서버에 프로젝트 배포 멘토링을 하면서 리눅스의 기초에 대해 공부한 내용을 정리하였습니다.   

# __Linux 기초__

## 목차
1. 리눅스 운영체제
2. 리눅스 기본 명령어



## 리눅스 운영체제 
> 운영체제란 사용자가 컴퓨터를 쉽게 다룰 수 있게 해주는 인터페이스로, 커널과 시스템 프로그램으로 구성되어있다.  
운영체제는 사용자 영역과 커널영역으로 나뉘어 사용자가 응용프로그램을 이용하기위한 공간, 자원을 관리하는 공간으로 사용한다.   
  
__커널__ 은 운영체제의 핵심으로 메모리 관리, 디바이스 관리, 프로세스 관리, 시스템 콜 등의 자원관리를 담당한다.

1. 메모리 관리
    >시스템이 가지고 있는 물리적 메모리보다 더 많이 프로그램에 할당할 수 있도록 가상 메모리 사용한다.  
    이때, 가상 메모리 영역을 __스왑__ 영역이라 하는데, 메모리의 프로그램을 보조 기억장치로 보내는 것을 __스왑아웃__ 메모리에 다시 가져오는 것은 __스왑인__이라 한다.  

2. 디바이스 관리
    >하드웨어의 입출력을 관리하기 위해 입출력 요청 검증, 입출력 요청 작업의 스케줄링, 입출력 제어기 관리, 인터럽트 요청 및 처리의 기능을 수행한다.  

3. 프로세스 관리
    >프로세스 스케줄러를 이용하여 하나의 CPU를 여러 프로세스가 이용할 수 있도록 각 프로세스에 pid(프로세스 id)를 부여하여 제어한다.  
        >* 프로세스 : 실행중인 프로그램(구성 = 프로그램에 필요한 데이터 + 자원 + 스레드)
        >* 스레드 : 프로세스 내에서 실행되는 흐름의 단위  
    >  

4. 시스템콜
    > 시스템콜은 커널이 제공하는 서비스(프로세스/파일 생성/삭제, 값 확인, 지정 등)를 프로그램의 요청에 따라 수행하는 방법이다.
  
전체적으로 사용자가 물리적 하드웨어에 접근하고 사용할 수 있도록 커널이 관리한다.
  
  ----------------------------------------

### 리눅스의 특징  
1. 소스코드가 공개 된 자유 소프트웨어로 오픈소스 개발이 쉽다.
2. 다중 사용자, 멀티태스킹, 다중 스레드를 지원한다.
    >여러 사람이 하나의 리눅스 시스템에 접속하여 다수의 프로그램을 동시실행이 가능하다.  

-----------------------------------------
### 리눅스의 디렉토리 구성 
root  
&nbsp;&nbsp;&nbsp;|------ __/bin__ 기본 커맨드를 저장(cat, chmod, chown 등) -> 모든 계정  
&nbsp;&nbsp;&nbsp;|------ __/boot__ 운영체제 시작에 필요한 파일  
&nbsp;&nbsp;&nbsp;|------ __/dev__ 하드디스크, 키보드, 디바이스 파일을 저장(core파일은 비정상적으로 종료되었을 때 프로그램의 상태를 기록)  
&nbsp;&nbsp;&nbsp;|------ __/etc__ 설정 파일(apt-get으로 설치시 기본 설치 디렉토리)  
&nbsp;&nbsp;&nbsp;|------ __/home__ 사용자 홈 디렉토리(계정 추가 시)  
&nbsp;&nbsp;&nbsp;|------ __/lib__ 공유 라이브러리  
&nbsp;&nbsp;&nbsp;|------ __/mnt__ 마운트 포인트용 디렉토리(마운트: 윈도우는 자동 pnp를 지원하나 리눅스는 직접 다른 디스크나 장치를 연결하는 작업이 필요)  
&nbsp;&nbsp;&nbsp;|------ __/media__ CD/DVD-ROM용 마운트 포인트  
&nbsp;&nbsp;&nbsp;|------ __/opt__ 기본 이외에 추가적인 소프트웨어 설치  
&nbsp;&nbsp;&nbsp;|------ __/proc__ 커널이나 프로세스 정보가 저장(저장된 숫자 = pid)  
&nbsp;&nbsp;&nbsp;|------ __/root__ root(시스템 관리자=슈퍼유저)용 디렉토리  
&nbsp;&nbsp;&nbsp;|------ __/sbin__ 시스템관리용 커맨드(reboot, ifconfig 등) -> root 권한  
&nbsp;&nbsp;&nbsp;|------ __/srv__ 서비스 디렉토리(ftp, cvs 등 시스템에서 얻은 데이터 저장)  
&nbsp;&nbsp;&nbsp;|------ __/tmp__ 프로그램을 설치할 때 임시저장되는 디렉토리 ->누구나 읽고, 쓰고, 실행할 수 있으므로 보안 유의  
&nbsp;&nbsp;&nbsp;|------ __/usr__ 프로그램, 커널 소스 저장(/usr/local은 시스템관리자가 어플리케이션을 설치하는 디렉토리)  
&nbsp;&nbsp;&nbsp;|------ __/var__ 로그 등 가변적인 파일(시스템이 가동되면서 변화하는 파일)
#

## 리눅스 기본 명령어  

>__pwd__ : 현재 디렉토리  
>__ls__ : 현재 디렉토리의 파일/폴더(__-al__ : 숨겨진 폴더/파일까지)   
>__cp__ 복사할 파일/폴더 복사본 저장이름 : 폴더/파일복사(폴더는 __-r__ 옵션)  
>__mv__ 옮길파일/폴더 이동시킬 경로 : 폴더/파일 이동  
>__mkdir__ 생성할 디렉토리명 : 디렉토리 생성  
>__rm__ 삭제할 파일/디렉토리(__-r__ 옵션 필수, __-rf__ 는 하위디렉토리까지 모두 삭제) : 파일/폴더 삭제  
>__touch__ 파일명 : 파일이 없으면 빈 생성  
>__cat__ 파일명 : 파일 내용 단순 출력
>__cat__ 파일명 파일명 > 생성파일명 : 두 파일내용을 붙여서 파일 생성  
>__cat__ 파일명 >> 생성파일명 : 결과값을 파일에 저장  
>__head__ -n 파일명 : 상위 n줄 만큼 보여줌(__-F__ 옵션: 파일내용을 계속 띄워두고 업데이트 된 내용을 갱신)  
>__tail__ -n 파일명 : 하위 -n줄 만큼 보여줌  
>__find__ 검색 경로 -name '파일명/*.확장자명' : 검색경로 내의 파일명/확장자명을 가진 파일 검색  
>__find__ 검색 경로 -type d/f : 디렉토리만/파일만 검색  
>__chmod__ 부여할 권한 파일명 : 사용권한 부여  
>__chown__ 소유권자:그룹 소유권변경할 파일명 : 파일의 소유권자와 그룹을 변경(__-R__ : 하위 경로의 소유자와 그룹 모두 변경)  
>__more__ 옵션 
    >* 스페이스바 : 다음 페이지
    >* 엔터 : 다음 줄
    >* v : vi편집기로 전환
    >* b : 이전 페이지
    >* q : 나가기  

>__tar__ cvf 파일명.tar : tar로 된 파일 압축  
>__tar__ xvf 파일명.tar : tar로 된 파일 압축풀기  
>__less__ 파일명 : 파일 보기  
>__vi__ 파일명 : 파일이 없으면 새 파일을 생성한 후 편집기 열기(__-b__ 옵션: 바이너리 모드로 열기)  
>__vi__ 옵션 
    >* 쓰기 : __i__ 커서위치에 쓰기, __a__ 커서 다음위치에 쓰기, __A__ 줄 맨뒤에 쓰기  
    >* 이동 : __w__ 다음단어 첫글자로 이동, __W__ 공백기준 다음단어 첫글자로 이동, __b__ 단어 첫글자의 이전, __B__ 공백기준 이전, __gg__ 문서 맨앞으로 이동, __G__ 문서 맨아래로 이동  
    >* 편집 : __dd__ 현재 줄 자르기, __yy__ 현재 줄 복사, __p__ 붙여넣기, __cc__ 잘라내기    

>__ln__ -s 원본파일 심볼릭링크이름 : 바로가기같은 기능  
>__ps__ : 사용자와 관련된 프로세스 출력  
>__ps__ -e : 커널 프로세스를 제외한 모든 프로세스 출력  
>__ps__ -f : + UID, PID, PPID(부모프로세스 id) 등을 함께 출력  
>__ps__ -ef | grep '포르세스명' : 해당 프로세스만 필터링하여 출력  