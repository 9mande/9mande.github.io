---
title: Operating System Structures
date: 2023-01-25 15:47:00 +0900
categories: [OS]
tags: [os, system]
---

## Operating System Services

OS는 프로그램이 실행될 수 있는 환경을 제공한다.

OS는 유저와 프로그램에게 서비스를 제공한다.

운영체제가 제공하는 서비스는 이런 종류가 있다.

- 유저에게 도움을 주는 서비스
    - 유저 인터페이스User Interface
        - CLI, GUI, Batch로 구분된다.
    - 프로그램 실행
        - 프로그램을 메모리에 로드하고 실행하는 것
    - 입출력
        - 프로그램이 파일이나 입출력 장치에 입출력을 수행할 수 있게 한다.
    - 파일 시스템 조작
        - 프로그램이 파일과 디렉토리를 읽고, 쓰고, 생성, 삭제, 검색, 리스팅, 권한을 관리할 수 있도록 한다.
    - 통신
        - 같은 컴퓨터나 네트워크 상 다른 컴퓨터 사이에서 프로세스 간 통신을 할 수 있도록 한다.
    - 에러 검출
        - CPU나 메모리 하드웨어, 입출력 장치, 유저 프로그램에서 발생하는 에러를 처리한다.
        - 각 에러에 대해 OS는 정확하고 일관되게 연산을 수행해야 한다.
        - 디버깅 또한 유저가 시스템을 효율적으로 사용하게 도와준다.
- 시스템(스스로)에 도움이 되는 서비스
    - 자원 할당
        - CPU 사이클, 메인 메모리, 파일 저장 공간, 입출력 장치들 등 많은 자원들이 있다.
        - 여러 유저나 여러 작업이 동시에 수행될 때, 자원이 각자에 할당되어야 한다.
    - 회계Accounting
        - 어떤 유저가 얼마나, 어떤 종류의 자원을 사용하는 지 추적한다.
    - Protection and Security
        - Protection은 시스템 자원에 대한 접근을 통제하는 것
        - Security는 외부 입출력 장치나 valid하지 않은 접근 시도로부터 방어하는 것.

        
## User Operating System Interface

### CLI

- 커널에 구현되어있기도, 시스템 프로그램에 구현되어 있기도 함
- 여러 구현이 첨가되어 있기도 함 (쉘shell)
- 유저에게서 명령command을 받아와 실행
- 내장 커맨드도 있고, 프로그램의 이름을 받기도 함
    - 후자의 경우, 추가되는 요소들에 의해 쉘을 수정할 필요가 없음

### GUI

- 책상을 은유한 인터페이스
    - 마우스, 키보드, 모니터
    - 아이콘들은 파일, 프로그램, 동작 등을 표현함
    - 인터페이스 안의 오브젝트 위에 마우스 버튼이 다양하고 다양한 동작을 수행할 수 있음

## System Calls

- 시스템 콜은 OS에 의해 제공되는 서비스에 대한 인터페이스
- 주로 C나 C++로 작성됨
- 직접적으로 사용하기 보다는 high level API를 이용해 사용함
- 예시
    - Win32 API for Windows
    - POSIX API for POSIX based systems (UNIX, Linux, Mac OS X)
    - Java API for JVM

### System Call Implementation

- 각 시스템 콜에는 번호가 부여되어 있다.
    - System call interface에는 이 번호로 참조할 수 있는 표가 있다.
- 시스템 콜 인터페이스는 OS 커널에서 시스템 콜을 실행하고, 상태와 반환 값을 반환한다.
- 호출자는 시스템 콜의 구현에 대해 알 필요가 없다.
    - API만 따르면 된다.
    - API에 의해, 프로그래머에게 OS 인터페이스에 대한 세부적인 내용은 숨겨짐
        - 런타임 지원 라이브러리에 의해 관리된다. (여러 기능들이 라이브러리에 내장되고, 컴파일러에 포함되어 있다)

### System Call Parameter Passing

시스템 콜에서 매개변수를 전달하는 법

3가지 방법이 있음

- 레지스터에 담아 전달
- 매개변수를 메모리의 한 블럭이나 테이블에 담아 그 블럭의 주소를 레지스터에 담아 전달
    - Linux와 Solaris에서 사용하는 방법이다
- 스택에 push되고, OS에 의해 pop 되는 방법

블럭이나 스택을 사용할 때, 매개변수의 개수는 제한되지 않는다.

### Type of System Calls

시스템 콜의 종류

- 파일 관리
    - 파일 생성, 삭제
    - 파일 열기, 닫기
    - 읽기, 쓰기, 위치 변경
    - 파일 속성을 get/set
- 장치 관리
    - 장치 request와 release
    - 읽기, 쓰기, 위치 변경
    - 장치 속성을 get/set
    - 논리적으로 장치를 연결하거나 분리
- 정보 유지
    - 시간이나 날짜를 get/set
    - 시스템 데이터를 get/set
    - 프로세스와 파일, 장치 속성을 get/set
- 통신
    - 연결을 생성, 삭제
    - 메시지를 전송, 수신
    - Shared-memory model 생성, 메모리 구역에 대한 접근을 받아 처리
    - 상태 정보를 전송
    - 원격 장비를 연결하거나 분리
- 보호
    - 자원에 대한 접근 통제
    - 권한 get/set
    - 유저의 접근을 Allow/deny

## System Programs

시스템 프로그램은 프로그램을 개발하고 실행하는 데에 편리한 환경을 제공한다.

사실상 사용자 view에서의 OS는 시스템 프로그램이고, 실제 시스템 콜은 아니다. 몇몇은 단순히 시스템 콜에 대한 인터페이스를 제공하더라도, 보통 더 복잡하다.

아래 항목으로 구분할 수 있다.

- **파일 조작manipulation**
    - 생성, 삭제, 복사, rename, print, dump, list, 파일과 디렉토리 조작
- **Status information**
    - date, time, 사용 가능 메모리, 디스크 공간, 사용자 수
    - 성능, 로깅, 디버깅 정보
    - 일반적으로 이런 정보들을 format하고 print한다.
    - 몇몇 시스템은 레지스트리registry 라는 것을 구현해서 configuration 정보를 검색하고, 저장한다.
- **파일 수정**
    - 텍스트 에디터
    - 파일 중의 텍스트 검색, 수정하는 특별한 커맨드들
- **프로그래밍 언어 지원**
    - 컴파일러, 어셈블러, 디버거, 인터프리터 등
- **프로그램 로드, 실행**
    - Absolute loders, relocatable loaders, linkage editors, overlay loaders, debugging systems for higher-level and machine language
- **통신**
    - 프로세스, 유저, 컴퓨터 시스템 간 가상 연결을 생성하는 데 대한 메커니즘을 제공
- **Background services**
    - Launch at boot
        - 시스템 startup 을 위해 실행되고 terminate되는 것들도 있고
        - system boot 부터 shutdown 까지 진행되는 것들도 있다
    - 디스크 체크, 프로세스 스케줄링, 에러 로깅, printing 과 같은 서비스를 제공한다.
    - 유저 context에서 동작한다. kernel context 아님
    - services, subsystems, daemons가 얘네들이다!
- **Application programs**
    - 시스템에 속한 게 아님
    - 유저에 의해 실행됨

## OS Design and Implementation

User goals

- Convenient to use, easy to learn, reliable, safe, fast

System goals

- Easy to design, implement, and maintain
- flexible, reliable, error-free, efficient

Important to seperate Policy(What) and Mechanism(How)

- Policy decision이 나중에 바뀌는 상황에서 maximum flexibility를 제공하기 위해

### Implementation

Early OSes: assembly language → Now C, C++

Actually, usually a mix of languages

- Lowest levels in assembly
- Main body in C
- System programs in C, C++, scripting languages like PERL, Python, shell scripts

More high-level language easier to port to other hardware

- But slower

Emulation can allow an OS to run on non-native hardware

## OS Structure

Various ways to structure ones

- Simple structure like MS-DOS
    - MS-DOS - written to provide the most functionality in the least space.
- More complex - UNIX
    - System programs
    - The kernel
        - Consists of everything below the system-call interface and above the physical hardware
        - Provides the file system, CPU scheduling, memory management, and other operating-system functions; a large number of functions for one level
- Layered approach- an abstraction
    - The bottom layer is the hardware, the highest is the user interface.
    - With modularity, layers are selected such that each uses functions(operations) and services of only lower-level layers.
- Microkernel - Mach
    - Moves as much from kernel into user space
    - Mach is example of microkernel
        - Mac OS X kernel (Darwin) partly based on Mach
    - Communication takes place between user modules using message passing
    - Benefits:
        - Easier to extend
        - Easier to port the OS to new architectures
        - More reliable (minimum code running in kernel mode, kernel does just IPC, Memory management, CPU scheduling, …)
        - More secure
    - Detriments:
        - Performance overhead of user space to kernel space communication

### Modules

Many modern operating systems implement loadable kernel modules.

- Uses object oriented approach
- Each core component is separate
- Each talks to the others over known interfaces
- Each is loadable as needed within the kernel

Overall, similar to layers but with more flexible

- Linux, Solaris, etc

### Hybrid Systems

Most modern operating systems are actually not one pure model.

- Hybrid combines multiple approaches to address performance, security, usability needs
- Linux and Solaris kernels in kernel address space → monolithic, plus modular for dynamic loading of functionality
- Windows mostly monolithic, plus microkernel for different subsystem personalities
- Apple Mac OS X hybrid, Aqua UI plus Cocoa programming environment
    - Below is kernel consisting of Mach microkernel and BSD Unix parts, plus I/O kit and dynamically loadable modules (called kernel extensions)
- iOS
    - Structured on Mac OS X, added functionality
    - Does not run OS X applications natively
        - Also runs on different CPU architecture (ARM vs Intel)
        - Cocoa touch - Objective-C API for developing apps
        - Media services layer for graphic, audio, video
        - Core OS, based on Mac OS X kernel
- Android
    - Developed by Open Handset Alliance
    - Similar stack to IOS
    - Based on Linux kernel but modified
        - Provides process, memory, device-driver management
        - Adds power management
    - Runtime environment includes core set of libraries and Dalvik virtual machine
        - Apps developed in Java plus Android API
            - Java class files compiled to Java bytecode then translated to executable than runs in Dalvik VM
        - Libraries include frameworks for web browser (webkit), database(SQLite), multimedia, smaller libc

## OS Debugging

Debugging - finding and fixing errors, or bugs

OS generate **log** files containing error information

Failure of an application can generate **core dump** file capturing memory of the process

OS failure can generate **crash dump** file containing kernel memory

Beyond crashes, performance tuning can optimize system performance

- Sometimes using **trace listings** of activities, recorded for analysis
- **Profiling** is periodic sampling of instruction pointer to look for statistical trends

**Kernighan’s Law** : Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smar enough to debug it.

Performance Tuning

## OS Generation

OSes are designed to run on any of a class of machines; the system must be configured for each specific computer site

**SYSGEN** program obtains information concerning the specific configuration of the hardware system

- Used to build system-specific compiled kernel or system-tuned
- Can general more efficient code than one general kernel

## System Boot

When power initialized on system, execution starts at a fixed memory location

- Firmware ROM used to hold initial boot code

Operating system must be made available to hardware so hardware can start it

- Small piece of code - **bootstrap loader** stored in **ROM** or **EEPROM** locates the kernel, loads it into memory, and starts it
- Sometimes two-step process where **boot block** at fixed location loaded by ROM code, which loads bootstrap loader from disk

Common bootstrap loader, **GRUB**(GReat Unified Bootloader) allows selection of kernel from multiple disks, versions, kernel options

Kernel loads and system is then **running**