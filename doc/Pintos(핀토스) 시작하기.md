# Pintos(핀토스) 시작하기

지난 학기에 운영체제 수업을 듣고 배운 내용을 복습하고 더 탄탄히 하는데는 직접 프로젝트를 해 보는 것이 도움이 될 것 같아 여러 프로젝트들 중 **Pintos**를 선택하여 진행하게 되었다. 항상 그렇듯이 초기 환경설정에서 많은 시행착오를 겪게 되는데 누군가에게 도움이 되기를 바라며 정리해보았다.

## Pintos란 💬

**Pintos(핀토스)**는 80x86 아키텍처를 위한 단순한 운영체제 프레임워크로 2004년 Stanford 대학교의 Ben Pfaff가 교육용으로 개발하였다. 커널 스레드, 사용자 프로그램 로딩 및 실행, 파일 시스템을 매우 단순하게 수행하는 방식으로 지원한다. 실제 x86 하드웨어에서 구동이 가능하지만 Bochs 또는 QEMU와 같은 x86 에뮬레이터 위에 구동되기도 한다.

## Pintos를 사용하는 이유 ❗

1. 커널 스레드와 메모리 관리, 파일 시스템 접근 등을 포함하여 **실제 운영체제의 중요한 부분을 직접 구현하도록 요구**함으로써 학부생들에게 운영체제의 설계 및 구현 개념을 소개하고 경험할 수 있도록 한다. 추가적으로 가상 메모리 구현도 할 수 있는데 이러한 구현 경험은 매우 중요하다.
2. 리눅스와 같은 상용 운영체제는 약 100만 라인 정도로 매우 크기 때문에 컴파일하는데 최소 1시간 이상걸리며, 이 중 80% 이상이 다양한 하드웨어 지원을 위한 디바이스 드라이버 코드에 해당한다. 반면에 **Pintos는 간단해서 이해하기 좋고 컴파일도 쉬운 운영체제**이다.

## Pintos 시작하기 👩🏻‍💻

공식 문서에 의하면 특정 버전 이후의 최신 버전들도 이용 가능하다고 써져 있지만 환경 셋팅 과정이 원활하게 진행되지 않았다. 찾아보니 다른 분들도 이 단계에서 많은 시행착오를 겪으셨다고 하여 한 [블로그](https://bowbowbow.tistory.com/9?category=175252)를 참고하여 해당 포스팅에서 제시하는 버전으로 설치했고 덕분에 비교적 빠르게 문제를 해결하여 진행할 수 있었다.

> 필자는 Windows 10 64bits에서 진행하므로 가상머신 환경이 필요하다.

### 1. Ubuntu(우분투) 설치하기

[VirtualBox](http://www.virtualbox.org)에 리눅스 가상머신을 설치하고, 이미지 파일은 **반드시** [**ubuntu-12.04.5-desktop-i386.iso**](https://releases.ubuntu.com/12.04/ubuntu-12.04.5-desktop-i386.iso)를 준비해야 한다. 자세한 방법은 구글링을 통해 쉽게 알 수 있다.

> 다소 시간이 걸리니 인터넷 속도가 빠른 환경에서 설치하거나 다른 일을 하며 여유를 두는 편이 좋다.⏳

![우분투 설치하기](https://cdn-images-1.medium.com/max/1000/1*T5GsBShGnPeE6deggCOr-g.jpeg)

![디스크 설정하기](https://cdn-images-1.medium.com/max/750/1*RdgCjZqpxqBoh78IeLVPMA.png)

### 2. **gcc-4.4 설치하기**

우분투 설치가 완료되었으면 터미널에서 다음 명령을 통해 gcc 컴파일러를 설치한다.

```
sudo apt-get install gcc-4.4
sudo mv /usr/bin/gcc-4.4 /usr/bin/gcc
```

### 3. **QEMU 설치하기**

핀토스는 가상환경 에뮬레이터로 Bochs와 QEMU 모두 사용 가능하다. 공식 문서에 의하면 Bochs가 느리기 때문에 QEMU를 이용할 수 없는 경우 Bochs를 이용하라고 서술하였으나 무슨 이유에서인지 Bochs를 기준으로 설명되어 있다.🙄 Bochs를 설치하는 방법은 공식 문서와 타 블로그에 설명이 많이 되어있으므로 설명을 생략하고 필자는 속도가 빠른 QEMU로 진행하기로 결정하여 이를 바탕으로 설명한다.

```
sudo apt-get install qemu
```

### 4. **Pintos 설치하기**

1. Pintos 소스 코드를 다운 받아 적절한 위치에 압축을 푼다.

```
wget http://www.stanford.edu/class/cs140/projects/pintos/pintos.tar.gz
```

스탠포드 대학교에서는 실습서버에서 진행하므로 SSH로 학교 계정을 사용해 접속하여 실설치를 진행하지만 로컬에서 돌리기 때문에 위 방식으로는 진행할 수 없었다.😥

이 때, 두 가지 해결 방법이 있다.

- [Pintos 소스 코드](http://www.stanford.edu/class/cs140/projects/pintos/pintos.tar.gz)를 직접 다운받은 후 파일을 가상머신에 옮겨 진행하는 방법 — [로컬 파일을 가상머신과 공유하는 방법](https://www.howtogeek.com/189974/how-to-share-your-computers-files-with-a-virtual-machine/)
- `wget` 명령을 사용하는 방법(출처:[멍멍멍](https://bowbowbow.tistory.com/9?category=175252)님 블로그)

```
wget -O pintos.tar.gz http://bowbowbow.tistory.com/attachment/cfile9.uf@216A3B4956D876CB03A881.gz
```

> 후자가 훨씬 간편하다.👍🏻

다음 명령어로 압축을 풀 수 있다.

```
tar xvf pintos.tar.gz
```

2. 어느 위치에서나 pintos를 실행할 수 있도록 하기 위해 환경변수 설정을 한다.

> 필자는 vi를 사용하지만, 본인이 사용하는 텍스트 에디터를 사용하여 편집하면 된다.

```
sudo vi ~/.bashrc
```

마지막 라인에 핀토스를 설치한 경로를 추가한다.

> 필자는 **/home/jhlee/pintos**에 설치하여 이를 바탕으로 포스팅을 작성하였으므로, **본인의 환경에 맞게 경로를 수정하여 적용**해야 한다. 이후 경로 설정에서도 이점을 유의해야 한다.☝🏻 핀토스 설치 경로는 pintos 디렉토리에서 **pwd** 명령어를 통해 쉽게 확인할 수 있다.

```
export PATH="$PATH:/home/jhlee/pintos/src/utils"
```

혹시 기존에 설정해 둔 환경변수가 있다면 `:`를 구분자로 여러 디렉토리를 추가할 수 있다. (참고 : [How to add multiple path in $PATH variable linux?](https://stackoverflow.com/questions/21131590/how-to-add-multiple-path-in-path-variable-linux/21131629))

아래 명령을 통해 `.bashrc` 파일을 재로드하여 수정사항을 적용시킨다.

```
source ~/.bashrc
```

### 5. **GDBMACROS 설정하고 컴파일하기**

> Makefile과 pintos-gdb는 **pintos/src/utils**에 위치한다.

1. `Makefile`를 텍스트 에디터로 열고 `LDFLAGS = -lm`을 `LDLIBS = -lm`으로 수정한다.
2.  `pintos-gdb`를 텍스트 에디터로 열고 GDBMACROS 변수를 아래와 같이 수정한다.

```
GDBMACROS=/home/jhlee/pintos/src/misc/gdb-macros
```

3. 현재 utils 디렉토리가 아닌 경우 utils 폴더로 이동 후 `make` 명령어를 통해 컴파일한다.

### 6. **시뮬레이터 설정하고 컴파일하기**

1. **pintos/src/threads**에 위치한 Make.vars를 텍스트 에디터로 열어 마지막 줄의 `SIMULATOR = --qemu`로 수정한다.
2. 현재 threads 디렉토리가 아닌 경우 threads 폴더로 이동 후 `make` 명령어를 통해 컴파일한다.

### 7. **pintos와 Pintos.pm 수정하기**

1. **pintos/src/utils**에 위치한 `pintos`를 텍스트 에디터로 열어

- 103번째 줄을`$sim = “qemu”  if !defined $sim;`로 수정하고
- 259번째 줄을 `my $name = find_file ('/home/jhlee/pintos/src/threads/build/kernel.bin');` 과 같은 절대경로로 수정한다.

> vi 에디터를 사용할 경우 `:103`, `:259`와 같이 `:line_number`를 사용하면 빠르고 편하게 원하는 곳으로 이동할 수 있다.

2. 같은 디렉토리에 위치한 `Pintos.pm`을 텍스트 에디터로 열어 362번째 줄을 `$name = find_file ("/home/jhlee/pintos/src/threads/build/loader.bin") if !defined $name;` 와 같은 절대경로로 수정한다.

#### 8. 실행가능한 QEMU **링크 생성하기**

다음 명령어로 링크 파일을 만든 후 변경사항이 적용되도록 터미널을 재시작한다.

```
sudo ln -s /usr/bin/qemu-system-i386 /usr/bin/qemu
```

#### 9. **Pintos 실행하기**

다음 명령어를 통해 결과가 아래의 스크린샷과 같이 나온다면 정상적으로 Pintos가 설치된 것이다.🎉

```
pintos -- run alarm-multiple
```

![img](https://cdn-images-1.medium.com/max/1000/1*YsrpDYmG2LdU7gcoGWaiog.jpeg)

#### 📄 References

- 공식 문서 : [Pintos Projects](https://web.stanford.edu/class/cs140/projects/pintos/pintos.html)
- [PintOs 시작하기](https://bowbowbow.tistory.com/9?category=175252)(멍멍멍)
- [Installing PintOS on QEMU](https://cspracs.wordpress.com/installing-pintos-on-qemu/)
- [Pintos Project ](http://esos.hanyang.ac.kr/files/courseware/undergraduate/PINTOS/Pintos_all.pdf)— ESOS LAB(PDF)