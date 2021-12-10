## Shell Environment

Linux 환경에서의 Shell Environment 를 알아보자

- Road map
- Shell
- Bash
- Advantages and Disadvantages
- Shell Variables
- Environment Variables
- Built-in Environment variables



### Terminology

Disk에는 크게 두 가지로 나눌 수 있다. 

1. Kernel: 운영체제의 핵심적인 프로그램, 부팅될 때 제일 먼저 메모리에 로딩된다.
2. Utility: 실행타임 [run-time]에 로딩된다. 프로그램이다.



test.c 파일을 만들고 그 파일을 컴파일 하면, binary 형태의 실행파일이 존재한다. 

Utility 중에 Shell이 존재한다. Shell 도 on-demand 실행을 한다고 하면 동작을한다.

Shell을 이용해 test.c파일을 컴파일해서 나온 a.out 파일을 넘기면 [./a.out] 

Shell이 fork를 해서 새로운 프로세스를 만들어서 메모리에 a.out을 올려준다.

Shell 도 하나의 Utility인데, **다른 Utility들을 관리하기 위한 Utility이다.**



Shell은 사용자로부터 타입된 커맨드를 읽고, 입력된 커맨드를 Interpret 해서 다른 utility를 실행하도록 커널에 요청한다.

Shell은 중간다리 역할을 하는 것.



### Shell

- Special purpose utility

<img src="./readmeImg/shell/shell.png" alt="shell" style="zoom:50%;" /> 

다양한 shell 종류가 존재한다. 위에서 아래로 발전순서이다.

현재 사용하는 **bash** shell은 위에 기능들을 다 포함하고 있는 shell이다.

- Bourn shell은 최초 unix programming을 개발할 때 만들어졌다. Unix의 default 기능들을 사용할 수 있는 Shell이다. Bell Labs의 Stephen R. Bourne가 만듬
- C shell은 C 언어와 비슷한 형식으로 발전시킨 c shell이 있다. Bill Joy가 만들어는데 이분이 그 유명한 vi를 만든분.
- David Korn이 Korn Shell을 만듬. BournShell과 C Shell의 모든 기능을 다 포함하면서 발전시킴
- GNU project에서 Brian Fox 라는 분인 Bourne again Shell을 만듬
  - 현재는 대부분의 기능을 다 포함하고, Linux distribution [ubuntu 등]에서 default shell로 사용된다.



### Bash

$ : prompt라고 한다.

```bash
jaemincho$ whoami
jaemincho
```

Bash는 명령어 -> 결과를 보여주며 사용자와 상호작용을 하는  **CLI [Command Line Interpreter]**라고 한다.

명령어 [command]는 utility가 될 수 있고, 또는 shell 자체 내장 명령어일 수 있다

```bash
jaemincho$ bash --version
GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin20)
Copyright (C) 2007 Free Software Foundation, Inc.
```



### Advantages and Disadvantages

Linux shell에서 실행 가능한 다양한 커맨드들과 묶음으로 shell program을 만들 것인데,

##### Shell Program의 장점 [advantages]

- Easy to Use
  - 여러 존재하는 Utility 들을 조합해서 빠르게 프로토타입으로 만들 수 있다.
- Glue code
  - 다양한 환경에 맞춰서 사용될 수 있다.
  - 자바, C, web 등 서로 상호작용을 하면서 프로그램을 구성하는데 그 중간역할을 Shell이 해줄수있다.
- 자동화 하는데 좋다

##### Disadvantages

- 느리다
  - 불필요하게 새로운 프로세스를 만들고, 통신하는 등 overhead가 존재할 수 있다.
  - 그래서 복잡하거나 성능이 중요한 (complex / performance-sensitive) 프로그램에 적합하지 않다.
- 문법이 컴파일 기반이 아니기 때문에 에러가 발생할 수 있다.
  - rm -rf test * 를 rm -rf test*로 쓰면 모든 파일이 다 지워진다.. 이런 오류 같은거 조심해야한다. 



### Shell Variables

Shell 변수들.

Shell을 포함한 Linux Utility 프로그램들은 수행에 필요한 정보들이 있다.

예를들어)

- 현재 작업 위치 [current working directory]: pwd

  - ```bash
    (base) jojaemin-uiMacBookPro:~ jaemincho$ pwd
    /Users/jaemincho
    ```

- 명령어를 어디서 찾을 것인지: path

- home directory: Home

- 사용자가 변수를 정의할 수 도 있다.

즉, 프로그램 동작 방식에 영향을 미치는 값들의 모임을 Shell variable 이라고 할 수 있다.

Shell에서는 Shell 변수 이름, = , 값들의 쌍으로 정의가 된다. ex) PWD = /home/user/

Shell 변수를 만들고, 사용하고, 해지하고 할 수 있다. 어떤 종류가 있는지 알아보자



##### Environment variable

- shell variable들 중 **자식에게 되물림 되는 변수를** 환경 변수 [environment variable] 이라고 한다.
- 즉 환경변수는, 자식 프로세스에게 전달된다.
  - 부모 프로세스로부터 copy된 shell 변수 = 환경변수
- copy 된다는 것 !
  - 부모 프로세스에게 받은 환경 변수들을 수정을 하면, 부모 프로세스에게는 영향을 끼치지 않는다.
  - 하지만 또 다른 자식 프로세스들에겐 영향을 준다.

##### Built-in environment variable

- Linux는 미리 정의한 환경변수들이 있다. 이것들을 built-in 내장 환경변수라고 한다

- PATH, HOME, PWD 등등이 예시이다.

- 관습적으로 대부분 대문자로 표시한다.

  

환경변수가 아닌 변수들을 **local variable** 이라한다.

- 현재 프로그램/프로세스에만 영향을 주는 변수들 이다.



### Shell Variables: set

shell 변수를 어떻게 만들고 사용하는지에 대해 알아보자

<img src="./readmeImg/shell/set.png" alt="set" style="zoom:50%;" /> 

Shell 변수를 처음 할당시에 만들어지는데, 

varname=value, 즉 변수 이름과 = 값을 넣으면 값을 할당시에 변수가 만들어진다.

기본적으로 **data type이 정의되있지 않고, 문자열로 취급한다** - untyped script

- vehicle=BUS (OK)
- vehicle =BUS
  - 에러! 공백문자 넣으면 안된다.
  - vehicle: command not found
- Vehicle=TAXI
  - C와 동일하게 대 소문자를 구별한다.
- 변수의 제일 앞글자는 **_ (underscore) 이거나 알파벳만 가능하다 **
  - _my=BUS
  - my=BUS

- **echo $varname**  명령어를 사용해서 그 값을 출력해볼 수 있다.

```bash
(base) jojaemin-uiMacBookPro:~ jaemincho$ vehicle=BUS
(base) jojaemin-uiMacBookPro:~ jaemincho$ Vehicle=TAXI
(base) jojaemin-uiMacBookPro:~ jaemincho$ echo $vehicle
BUS
(base) jojaemin-uiMacBookPro:~ jaemincho$ echo $Vehicle
TAXI
```

Set 명령어: 현재 shell 변수들을 다 출력해주는 명령어이다. 

- shell variable들을 위한 명령어이기도 하지만, 다른용도도 존재한다.



- " " 를 이용해 문자열 출력이 가능하다

```bash
(base) jojaemin-uiMacBookPro:~ jaemincho$ myname=JAEMIN
(base) jojaemin-uiMacBookPro:~ jaemincho$ echo $myname
JAEMIN
(base) jojaemin-uiMacBookPro:~ jaemincho$ echo "my name is $myname"
my name is JAEMIN
```

${변수명} 또는 $변수명 으로 값을 참조되서 출력되는데 이를 **변수 치환이라 한다.**



