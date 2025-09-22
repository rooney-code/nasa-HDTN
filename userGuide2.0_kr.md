Document No:  HDTN-USER-036 

Space Operations Mission Directorate (SOMD) 

Space Communications and Navigation (SCaN) 

High-Rate Delay Tolerant Networking (HDTN) Project 

John H. Glenn Research Center 
CAGE Code No.: 1QFP5 
21000 Brookpark Road, Cleveland, Ohio 44135 

HDTN User Guide for V2.0.0

---

# 서문 (Preface)

미래 우주 임무에서 더 많은 과학 데이터를 전송할 수 있도록, NASA의 **Space Communications and Navigation (SCaN)** 프로그램은 새로운 통신 기술을 개발 중이다.  
이 중 NASA Glenn Research Center(GRC)의 **High-Rate Delay Tolerant Networking (HDTN)** 프로젝트는, 서로 다른 전송 속도로 동작하는 통신 시스템과 우주선 탑재체 간 데이터를 고속으로 전달할 수 있는 **신뢰성 있는 인터네트워킹 경로**를 제공한다.  

이 문서는 HDTN 아키텍처를 초기화하거나 활용하기 위해 **설정 파일(configuration file)** 과 **연락 계획(contact plan)** 을 어떻게 구성해야 하는지를 사용자에게 안내한다.  
또한 HDTN의 각 구성 요소(component)를 **맞춤형 애플리케이션**이나 **외부 애플리케이션**과 연결하기 위한 **외부 통신 경로(external communication lanes)** 에 대해서도 설명한다.  

---

## 문서 기여자

### 정부 기여자 (Government Contributors)
- Stephanie Booth  
- Rachel Dudukovich  
- Nadia Kortas  
- Ethan Schweinsberg  
- Brian Tomko  
- Blake LaFuente  
- Timothy Recker  
- Prash Choksi  
- Shaun McKeehan  

### 비정부 기여자 (Non-Government Contributors)
- Evan Danish  
- Tad Kollar  

# 문서 이력 로그 (Document History Log)

| 상태 (Status) | 문서 버전 (Revision) | 발효일 (Effective Date) | 변경 내용 (Description) |
|---------------|-----------------------|--------------------------|--------------------------|
| **Baseline**  | 1.0.0                | 2023-01-18               | - User Guide v1.0.0 최초 릴리스 |
| **Revision**  | 1.1.0                | 2023-12-08               | **Fixed**<br>• 모든 `volatile` 변수 → `std::atomic`으로 교체<br>• 수렴 계층 텔레메트리 변수/카운터 `std::atomic` 적용<br>• 라우팅 라이브러리 contact plan 갱신 반영<br>• 라우터 링크 상태 동기화 버그 수정<br>• custody transfer 관련 버그 수정<br><br>**Added**<br>• BPSec 지원 (`--bpsec-config-file`)<br>• `slip_over_uart` 수렴 계층 추가<br>• `bpgen`에 `--cla-rate` 인자 추가 (LTP/UDP 전송률 제어)<br>• `neighborDepletedStorageDelaySeconds` 옵션 추가<br>• API 호출로 설정/통계 조회 지원<br>• FPrime 지원 도구 `BpSendPacket`, `BpReceivePacket` 추가<br><br>**Changed**<br>• 모든 번들 데이터 타입을 `padded_vector_uint8_t`로 변경<br>• `BundleViewV6`, `BundleViewV7` 블록 헤더 재사용 지원<br>• 라우터와 스케줄러 통합<br>• 라우팅 로직 개선 (연결 실패 시 경로 재계산)<br>• `-bundle-rate` 인자를 실수형으로 변경<br>• BPv7에서 BPv6 스타일 우선순위 선택적 지원<br><br>**Removed**<br>• `finalDestinationEidUris`, `udpRateBps`, `ltpMaxSendRateBitsPerSecOrZeroToDisable` 필드 제거 (contact plan/명령줄 인자로 대체) |
| **Canceled**  | 1.2.0                | -                        | **Fixed**<br>• `BpSourcePattern`이 SlipOverUart, BpOverEncap에서 custody-signal이 아닌 번들 수신 가능<br>• `BundleView v6/v7` 혼합 사용 버그 수정<br>• HDTN 공유 라이브러리 빌드 전 플랫폼 지원<br><br>**Fixed/Added**<br>• LTP induct/outduct hostname 오류 처리 개선<br>• Apple `UdpBatchSender` syscall 지원<br>• Windows 빌드: warning level 4 (모든 경고 오류 처리)<br>• Linux 빌드: `-Wall`, `-Wextra`, `-Wpedantic` 적용<br>• LTP Red/Green callback 버그 수정<br>• BPSec 디코딩 타입 캐스팅 오류 수정<br>• 신규 플랫폼 지원 (macOS M2/Intel, FreeBSD, OpenBSD, Linux ARM64)<br>• Git commit hash 로그 출력<br>• CivetWeb 기반 웹 UI 빌드 옵션<br>• `bp_over_encap_local_stream`, `ltp_over_encap_local_stream` 수렴 계층 추가<br>• `EncapRepeater.cpp` 데모 애플리케이션 추가<br>• `enforceBundlePriority` 옵션 추가 (우선순위 엄격 적용) |
| **Canceled**  | 1.2.1                | -                        | **Fixed**<br>• Egress 경합(race condition) 버그 수정<br>• Outduct 미존재 시 bundle 반환 처리<br>• Router link up/down API 사용 시 경로 재계산 문제 해결<br><br>**Changed**<br>• `LOG_TO_ERROR_FILE=ON` 시 fatal-level 로그 별도 저장 |
| **Revision**  | 1.3.0                | 2024-05-23               | - RTP 기반 멀티미디어 스트리밍 지원<br>- 웹 UI에 구성 파일 편집기 추가<br>- API 기반 라우팅 재계산 이슈 수정<br>- NASA Class B 규격 준수 준비 진행 (현재 Class D) |
| **Canceled**  | 1.3.1                | 2024-11-27               | **Changed**<br>• LaTeX 가이드 → Word 포맷으로 이전<br>• 문서 템플릿 변경 (CM template)<br>• Section 1.0 문구 수정<br>• Convergence Layers/라우팅 프로토콜 요약<br>• Section 20.2 용어 변경<br>• 그림 번호 체계 변경<br>• 저자 정보 위치 변경 (TM 표지/서문)<br>• Section 5.4.2 보강<br>• Chapter 13 서론 명확화<br>• GUI 그림 버전 반영<br><br>**Removed**<br>• Section 3.1~3.6<br>• Section 10 세부항목 → 요약<br>• Section 15.1<br>• Section 20.2.1~20.2.2<br><br>**Added**<br>• macOS 설치 및 의존성 추가 |
| **Revision**  | 2.0.0                | 2025-07-18               | **Changed**<br>• Section 1.0 전체 재작성<br>• Windows 11 지원 추가<br>• Section 5.1 가독성 개선<br>• `max-bundle-size-bytes`, `max-rx-bundle-size-bytes` 설명 업데이트 (`BpSendFile`, `BpSendPacket`, `BpReceiveFile`, `BpReceivePacket`) |

---

# 1. 고속 지연 허용 네트워킹(HDTN) 개요

고속 지연 허용 네트워킹(High-Rate Delay Tolerant Networking, HDTN) 프로젝트는 기존의 지연 허용 네트워킹(Delay Tolerant Networking, DTN)을 기반으로 하지만, 최신 하드웨어 플랫폼을 활용하여 지연(latency)을 크게 줄이고 처리량(throughput)을 향상시킨다.  
이는 DTN의 핵심 동작(즉, 저장(store), 운반(carry), 전달(forward))을 서로 다른 링크(disparate links)에서 **번들 프로토콜(Bundle Protocol, BP)**을 통해 수행할 수 있도록 해준다.  

HDTN은 **대규모 병렬 파이프라인(massively parallel pipelined)** 및 **메시지 지향(message-oriented) 아키텍처**를 채택하여, 시스템 자원이 증가함에 따라 유연하고 안정적으로 확장될 수 있는 효율적인 DTN 구조를 구현한다.  

본 프로젝트와 관련된 문의나 의견은 [HDTN GitHub 페이지](https://github.com/nasa/HDTN)에 기재된 기여자(contributors)에게 연락할 수 있다.

---

# 2. 문서 (Documents)

이 장에 나열된 문서들은 본 문서를 작성하는 데 참고되는 표준, 명세, 핸드북 및 기타 간행물이다.

## 2.1 적용 문서 (Applicable Documents)

적용 문서는 본문에서 인용되거나, 본 문서에서 설명된 활동 수행과 직접적으로 관련되고 필수적인 조항을 포함한다.

**표 2-1: 적용 문서**

| 문서 번호 (Document Number) | 리비전 (Revision) | 문서 제목 (Document Title) | 발효일 (Effective Date) | 최종 업데이트 (Last Updated) |
|-----------------------------|--------------------|-----------------------------|--------------------------|-------------------------------|
| HDTN-REQ-008                | A                  | HDTN 소프트웨어 요구사항 명세서 (SRS) | 2024-09-11 | 2024-09-11 |
| RFC 4838                    | 1                  | Delay-Tolerant Networking Architecture | 2007-04-01 | 2007-04-01 |
| RFC 5050                    | 1                  | Bundle Protocol Specification | 2007-11-01 | 2007-11-01 |
| RFC 7116                    | 2                  | LTP, CBHE, and Bundle Protocol IANA Registries | 2014-02-01 | 2022-06-22 |
| RFC 7242                    | 1                  | DTN TCP Convergence Layer Protocol | 2014-06-13 | 2014-06-13 |
| RFC 9171                    | 2                  | Bundle Protocol Version 7 | 2022-01-01 | 2022-12-14 |
| RFC 9174                    | 1                  | DTN TCP Convergence Layer Protocol Version 4 | 2022-01-31 | 2022-01-31 |

---

## 2.2 참고 문서 (Reference Documents)

참고 문서는 본 문서의 의도를 이해하는 데 도움이 될 수 있지만, 본 문서에서 정의된 프로세스를 실행하는 데 반드시 필요하지는 않다.

- NASA. (2024). NASA/HDTN: High-rate Delay Tolerant Network (HDTN) Software. [GitHub Repository](https://github.com/nasa/HDTN)  
- OpenJS Foundation. (2024). [Node.js 다운로드](https://nodejs.org/en/download/package-manager)  
- Fraire, J. (n.d.). *dtnsim* [Bitbucket Repository]. [링크](https://bitbucket.org/lcd-unc-ar/dtnsim/src/master/)  
- NASA. (2022). [HDTN 시뮬레이터 설치 및 구성](https://docs.google.com/document/d/1KrKyO_pr-v9CeS5n_ectpfWtwkYL40PdLICGh-24zZY/edit?tab=t.0#heading=h.1fly6dvqx3j3)  
- NASA/HDTN. (2023). [runscript_distributed.sh](https://github.com/nasa/HDTN/blob/master/tests/test_scripts_linux/runscript_distributed.sh)  
- NASA/HDTN. (2023). [HDTN_DISTRIBUTED_DEFAULTS.JSON](https://github.com/nasa/HDTN/blob/master/config_files/hdtn/hdtn_distributed_defaults.json)  
- NASA/HDTN. (2023). [Routing_Test](https://github.com/nasa/HDTN/tree/master/tests/test_scripts_linux/Routing_Test)  
- NASA/HDTN. (2024). [integrated_tests.cpp](https://github.com/nasa/HDTN/blob/master/tests/integrated_tests/src/integrated_tests.cpp)  
- Vernyi, K., & Raible, D. (2023). [4K 고해상도 비디오 및 오디오 스트리밍 연구 – NASA NTRS](https://ntrs.nasa.gov/citations/20230018166)  
- NASA/HDTN. (2024a). [LtpEngineConfig.h](https://github.com/nasa/HDTN/blob/master/common/ltp/include/LtpEngineConfig.h)  

--- 

# 3. 아키텍처 (Architecture)

HDTN은 C++로 작성되었다. 아키텍처는 모듈화(modular) 되도록 설계되었으며, 각각의 모듈 단위는 Module(모듈)이라 부른다.  

HDTN의 주요 모듈은 다음과 같다:

- **Ingress**: 수신된 번들을 처리한다.  
- **Storage**: 번들을 디스크에 저장한다.  
- **Router**: 번들의 경로를 계산하고, 언제 번들을 전달할 수 있을지를 결정한다.  
- **Egress**: 번들을 올바른 Outduct 및 다음 홉(next hop)으로 전달한다.  
- **Telemetry Command Interface**: HDTN의 동작 및 데이터를 표시한다.  
- **Libraries**: HDTN 코어 모듈(예: 라우팅, BPSec 라이브러리)에 필요한 공통 알고리즘과 기능을 구현한다.  

**그림 3-1**은 HDTN 모듈과 이들 간의 상호작용을 보여준다.  
![alt text](image.png)

---

# 4. 빌드 환경 요구사항 (Build Environment Requirements)

이 장에서는 HDTN 실행 환경, 테스트된 플랫폼, 아키텍처 및 의존성(dependencies)에 대해 설명한다.

---

## 4.1 테스트된 플랫폼 (Tested Platforms)

- **운영체제 (OS)**
  - Linux  
    • Ubuntu Desktop 18.04, 18.10, 20.04.2, 20.10  
    • Ubuntu Server 20.04, 20.10, 22.04  
    • Debian 10, 11, 12  
    • Raspbian  
    • Red Hat Enterprise Linux (RHEL) 8  
    • Oracle Enterprise Linux (OEL) 8  
  - Windows  
    • Windows 11 (64-bit)  
    • Windows 10 (64-bit)  
    • Windows Server 2022 (64-bit)  
    • Windows Server 2019 (64-bit)  
  - macOS 14  
  - FreeBSD 14  
  - OpenBSD 7  

- **칩셋 (Chipset)**
  - ARM32  
  - ARM64  
  - x86_64  

---

## 4.2 의존성 (Dependencies)

### 4.2.1 Linux Dependencies

**표 4-1: 상용 소프트웨어(COTS) 유틸리티 및 지원 파일**

| 개발자 (Developer) | 유틸리티/라이브러리 | 버전 | 출시일 | 소스(Source) | 비고(Notes) |
|--------------------|----------------------|-------|----------|---------------|--------------|
| CMake.org          | CMake               | 3.16.3 | 2020-01-21 | [CMake GitHub](https://gihub.com/Kitware/CMake) | - |
| Boost.org          | Boost               | 1.66.0 | 2017-12-18 | [Boost.org](https://www.boost.org) | RHEL 8 포함 |
| Boost.org          | Boost               | 1.69.0 | 2018-12-12 | [Boost.org](https://www.boost.org) | TCPCLv4 TLS v1.3 필요 |
| Boost.org          | Boost               | 1.70.0 | 2019-04-12 | [Boost.org](https://www.boost.org) | Web UI HTTPS/WSS 지원 필요 |
| ZeroMQ.org         | ZeroMQ              | 4.34   | 2021-01-17 | [libzmq GitHub](https://github.com/zeromq/libzmq) | - |
| GNU.org            | gcc                 | 9.3.0  | 2020-03-12 | [GNU GCC](https://gcc/gnu.org) | Debian 8.3.0-6 |
| OpenSSL.org        | OpenSSL             | 1.1.1f | 2020-03-31 | [OpenSSL](https://www.openssl.org) | 선택 사항; BPSec 지원 필요 |
| Adam Kennedy       | Strawberry Perl     | 5.32.1.1 | 2021-01-24 | [Strawberry Perl](https://strawberryperl.com) | - |
| GStreamer Team     | GStreamer           | 1.20.0 | 2022-02-03 | [GStreamer](https://gstreamer.freedesktop.org) | 스트리밍 지원 필요 |

---

### 4.2.2 macOS Dependencies

- Homebrew 설치 (macOS용 패키지 매니저):  
  ```bash
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```
  > NOTE: Apple Silicon 기반 Mac → /opt에 설치됨
    Intel 기반 Mac → /usr/local에 설치됨
    본 문서에서는 /opt 기준으로 설명함
  
- .zprofile에 추가:
  ```
  eval "$(/opt/homebrew/bin/brew shellenv)"
  export CPATH="$HOMEBREW_PREFIX/include"
  ```
- ZeroMQ, OpenSSL, Boost 설치:
  ```
  brew install zmq
  brew install openssl gnutls
  brew install boost@1.85
  ```

### 4.2.3 Windows Dependencies
HDTN은 Windows에서 Visual Studio 컴파일러 9가지 조합을 지원:
- 버전(Version): 2022, 2019, 2017 (단, 2017은 15.7 및 15.9만 테스트됨)
- 에디션(Edition): Enterprise, Professional, Community

## 4.3 제한 사항 (Limitations)
- Ubuntu 배포판은 종종 구버전 CMake를 설치하는 경우가 있으며, 이는 호환되지 않을 수 있음.
- 일부 프로세서는 하드웨어 가속 또는 RDSEED 명령어를 지원하지 않음.(참고: CMake 파일에서는 두 옵션 모두 기본값으로 "ON" 상태임) 교차 컴파일하지 않는 경우 CMake가 이를 자동으로 감지함.

---

# 5. HDTN 빌드 절차 (Build Procedures)
## 5.1 HDTN CMake 관련 참고사항 (Notes on HDTN CMake)
- HDTN의 모든 모듈/라이브러리는 각각 **자체 CMakeLists.txt 파일**을 가진다.  
- 루트 `CMakeLists.txt` 파일은 `add_subdirectory` 명령어를 사용하여 모든 모듈/라이브러리를 HDTN 프로젝트에 포함한다.  
- `make install`을 수행하면, 패키지 설정 정보(package config info)는 **설치 디렉터리 (`install root/lib/cmake`)**에 export 된다.  
  - 예시는 `tests/unit_tests_import_installation/CMakeLists.txt`에서 확인할 수 있다.  
- 패키지 설정 정보는 HDTN 코드베이스 전체가 아니라 특정 부분(예: 특정 convergence layer의 라이브러리)만 사용하는 **사용자 정의 소프트웨어 프로젝트 작성 시 활용**된다.  
- HDTN CMake 최적화 (Optimization)
  - **컴파일러 지원 확인**: 최신 C++ 표준 지원 여부를 테스트한다.  
  - **하드웨어 명령어 활용**: 컴파일러와 CPU를 검사하여 특정 x86 하드웨어 명령어를 지원하면 이를 활용한다.  
  - **운영 환경**: CMake의 `Release mode` 사용 권장.  
  - **문제 해결/디버깅**: `Debug mode`는 Section 19.0에 설명됨.  
- HDTN CMake 라이브러리 빌드 옵션 (Library Build Options)
  - HDTN 라이브러리는 **정적(static)** 또는 **공유(shared)** 라이브러리로 빌드 가능하다.  
  - `CMake`의 `GENERATE_EXPORT_HEADER`를 사용한다.  
    - Windows 환경에서 `.dll` 파일을 빌드/사용할 때 필요하다.  
    - GCC C++의 visibility 지원을 사용할 때도 필요하다.  
## 5.2 Linux에서 HDTN 빌드하기 (Build HDTN on Linux)
다음 명령어를 사용하여 필요한 의존성을 설치할 수 있다.

- **Debian/Ubuntu**
  ```bash
  sudo apt-get install cmake build-essential libzmq3-dev
  sudo apt-get install libboost-dev libboost-all-dev openssl libssl-dev
  sudo apt install git-all
  ```

- **RHEL/OEL**
  ```bash
  sudo dnf install epel-release
  sudo yum install cmake boost-devel zeromq zeromq-devel
  sudo dnf install git-all
  ```

“Release mode”에서 HDTN을 빌드하려면 다음 단계를 수행한다. (NOTE : -DCMAKE_BUILD_TYPE을 지정하지 않으면 기본적으로 HDTN은 “Release mode”로 빌드된다.)

  ```bash
  git clone https://github.com/nasa/HDTN.git
  export HDTN_SOURCE_ROOT=/home/username/HDTN   # HDTN 소스 경로 지정
  cd $HDTN_SOURCE_ROOT
  mkdir build
  cd build
  cmake ..
  ```
- CMake 설정
    - 정적 라이브러리(Static) 빌드
      ```
      BUILD_SHARED_LIBS:BOOL=OFF
      CMAKE_CXX_FLAGS_RELEASE:STRING=-O3 -DNDEBUG
      ```
    - 공유 라이브러리(Shared) 빌드
      ```
      BUILD_SHARED_LIBS:BOOL=ON
      CMAKE_CXX_FLAGS_RELEASE:STRING=-O3 -DNDEBUG -fPIC
      ```

```bash
make
```
> NOTE : make -j8 옵션을 추가하면 (make -j8) 빌드 속도가 빨라지지만, 최소 8코어 이상의 시스템이 필요하다.

### 5.2.1 선택적 x86 하드웨어 가속 (Optional x86 Hardware Acceleration)
HDTN 빌드 환경은 기본적으로 다음 CMakeCache 변수를 "On"으로 설정한다.
- USE_X86_HARDWARE_ACCELERATION
- LTP_RNG_USE_RDSEED

> Notes
  네이티브 빌드 시 (cross-compile 아님) HDTN CMake 빌드 환경은 CPU 명령어 집합과 컴파일러를 확인하여, 지원되는 하드웨어 가속 기능을 자동으로 활성화한다.
  → 지원되는 경우, CMake가 자동으로 컴파일러 정의를 추가하여 가속 기능을 활성화한다.

  크로스 컴파일 시 HDTN CMake 빌드 환경은 컴파일러만 확인하며, 대상 프로세서가 해당 명령어를 실제로 지원하는지는 사용자가 확인해야 한다.
  → 컴파일 가능한 경우에만 CMake가 정의를 추가한다.

  CMakeCache.txt에서 USE_X86_HARDWARE_ACCELERATION 및 LTP_RNG_USE_RDSEED를 "Off"로 설정하여 하드웨어 가속 기능을 비활성화할 수 있다.

  ARM 또는 비 x86-64 플랫폼 빌드 시 USE_X86_HARDWARE_ACCELERATION 및 LTP_RNG_USE_RDSEED를 반드시 "Off"로 설정해야 한다.

만약 CPU가 지원하고 USE_X86_HARDWARE_ACCELERATION이 "On"이면 다음 기능들이 활성화될 수 있다:
  - 빠른 SDNV 인코딩/디코딩 (BPv6, TCPCLv3, LTP) → SSE, SSE2, SSE3, SSSE3, SSE4.1, POPCNT, BMI1, BMI2 필요
  - 빠른 32바이트 단위 배치 SDNV 디코딩 (아직 HDTN에 통합되지 않았으며, common/util/Sdnv 라이브러리에서 제공됨) → AVX, AVX2 + 위의 “Fast SDNV” 지원 필요
  - 빠른 CBOR 인코딩/디코딩 (BPv7) → SSE, SSE2 필요
  - 최적화된 TCPCLv4 로드/스토어 → SSE, SSE2 필요
  - 빠른 CRC32C (BPv7 및 저장소 해시 함수) → SSE4.2 필요
  - 저장소 컨트롤러 최적화 → BITTEST 사용 (없으면 ANDN + BMI1)
  
만약 LTP_RNG_USE_RDSEED가 "On"이고 CPU가 지원한다면 LTP의 난수 발생기에 추가적인 무작위성(randomness) 소스로 RDSEED 명령어를 사용한다. 단, 이를 끄면 LTP 성능이 더 빨라질 수도 있다.

### 5.2.2 스토리지 용량 컴파일 파라미터 (Storage Capacity Compilation Parameters)

HDTN 빌드 환경은 기본적으로 두 개의 **CMake cache 변수**를 설정한다:

- `STORAGE_SEGMENT_ID_SIZE_BITS`  
- `STORAGE_SEGMENT_SIZE_MULTIPLE_OF_4KB`
---

#### 1) STORAGE_SEGMENT_ID_SIZE_BITS
- 이 플래그는 **권장 기본값인 32 또는 64**로 설정해야 한다.  
- 스토리지 모듈의 `segment_id_t`의 크기/타입을 결정한다.  
- 값을 **32**로 설정하면 RAM 사용량을 크게 줄일 수 있다.
- 값이 32일 경우
  - 최대 세그먼트 수 `S` 공식 : S = min(UINT32_MAX, 2^32) ≈ 4.3 billion (약 43억 개)
  - `segment_id_t = uint32_t` 이므로, 약 **43억 개 세그먼트**를 할당할 수 있다.  
  - 세그먼트 할당기(segment allocator)가 43억 개 세그먼트를 사용할 경우, 약 **533 MB RAM**을 사용한다.  
  - 최소 블록 크기(4KB)를 곱하면, 약 **17 TB 번들 저장 용량**이 된다.  
  - `totalStorageCapacityBytes` 변수를 HDTN JSON 설정에서 올바르게 지정하여, 세그먼트 할당기가 실제 필요한 메모리만 사용하도록 해야 한다.

- 값이 64일 경우
  - 최대 세그먼트 수 `S` 공식: S = min(UINT64_MAX, 2^64) ≈ 68.7 billion (약 687억 개)
  - `segment_id_t = uint64_t` 이므로, 약 **687억 개 세그먼트**를 할당할 수 있다.  
  - 최소 블록 크기(4KB)를 곱하면, 약 **281 TB 번들 저장 용량**이 된다.

---

#### 2) STORAGE_SEGMENT_SIZE_MULTIPLE_OF_4KB
- 반드시 **1 이상의 정수**로 설정해야 한다.  
- 4096바이트(4KB) 표준 블록 크기를 기준으로, 번들 저장 최소 증가 단위를 결정한다.  
- 기본값은 `1`이며, **이 값이 권장된다**.
- 예시 (Examples):
  - `STORAGE_SEGMENT_SIZE_MULTIPLE_OF_4KB = 1`  
    - 블록 크기 = 4KB × 1 = **4KB**  
    - 번들 크기 1KB → 4KB 필요  
    - 번들 크기 6KB → 8KB 필요  
  - `STORAGE_SEGMENT_SIZE_MULTIPLE_OF_4KB = 2`  
    - 블록 크기 = 4KB × 2 = **8KB**  
    - 번들 크기 1KB → 8KB 필요  
    - 번들 크기 6KB → 8KB 필요  
    - 번들 크기 9KB → 16KB 필요  
    - 이 경우, `STORAGE_SEGMENT_ID_SIZE_BITS=32`라면 번들 저장 용량이  
  약 **17TB → 34TB**로 잠재적으로 두 배가 될 수 있다.

---

**추가 참고**: 스토리지 동작 방식은 HDTN 메인 저장소의  
[`module/storage/doc/storage.pptx`](https://github.com/nasa/HDTN/tree/master/module/storage/doc/storage.pptx) 문서를 참고할 수 있다.

---

## 5.3 Mac에서 HDTN 빌드 (Build HDTN on Mac)
Section 4.2.2에서 설명한 **macOS 의존성(Dependencies)**이 설치되어 있어야 한다.

```bash
1. mkdir -p build
2. export LDFLAGS=-L$HOMEBREW_PREFIX/opt/boost@1.85/lib
3. export CPPFLAGS=-I$HOMEBREW_PREFIX/opt/boost@1.85/include
4. cmake -S . -B build \
   -DCMAKE_CXX_FLAGS="-Werror" \
   -DCMAKE_C_FLAGS="-Werror" \
   -DUSE_X86_HARDWARE_ACCELERATION:BOOL=ON \
   -DLTP_RNG_USE_RDSEED:BOOL=OFF \
   -Dlibzmq_LIB:FILEPATH=$HOMEBREW_PREFIX/lib/libzmq.dylib \
   -Dlibzmq_INCLUDE:PATH=$HOMEBREW_PREFIX/include
5. make -C build/ -j8
6. export HDTN_SOURCE_ROOT=$PWD
7. ./build/tests/unit_tests/unit-tests
```
---
## 5.4 Windows에서 HDTN 및 의존성 빌드 (Build HDTN on Windows with its Dependencies)
Windows에서 HDTN 빌드를 위해 필요한 것:
- 지원되는 Visual Studio 컴파일러 (C++ 개발 옵션 포함 설치 필요)
- PowerShell (Visual Studio Code + PowerShell 확장 추천)
- 7-Zip
- Perl (OpenSSL 빌드에 필요, perl.exe가 Path 환경 변수에 포함되어야 함 → Windows용 Strawberry Perl 테스트 완료됨)
---

HDTN 및 의존성을 Release 모드 + 공유 라이브러리(.dll) 로 빌드하려면,
building_on_windows\hdtn_windows_cicd_unit_test.ps1 PowerShell 스크립트를 실행한다. (작업 디렉토리는 상관 없음)
빌드가 완료되면, HDTN 및 의존성은 C:\hdtn_build_x64_release_vs2022 (설치된 Visual Studio 버전에 따라 vs2019 또는 vs2017 접미사 사용) 경로에 설치된다.
스크립트는 빌드 후 HDTN의 유닛 테스트도 실행한다. 완료 후 메시지 출력:
```
Remember, HDTN was built as a shared library, so you must prepend the following to your Path so that Windows can find the DLL’s of HDTN and its dependencies:

HDTN은 공유 라이브러리(shared library)로 빌드 되었으므로,Windows가 HDTN 및 그 의존성의 DLL 파일을 찾을 수 있도록 아래 경로들을 반드시 Path 환경 변수의 맨 앞에 추가 해야 한다:
```
여기서 네 개의 디렉토리가 출력되며, 이를 환경 변수 Path에 추가해야 PowerShell 외부에서 HDTN을 사용할 수 있다.
- Windows 시작 메뉴에서 env 입력 → 계정의 환경 변수 편집 실행
- Path 선택 → 편집 → 출력된 네 개 디렉토리 추가 (단, Visual Studio 내에서 HDTN 소스코드를 수정하는 경우 hdtn_install\lib 디렉토리는 추가하지 말 것)
- HDTN 소스코드를 수정하지 않는 일반 사용자라면 아래 디렉토리도 Path에 추가:
C:\hdtn_build_x64_release_vs2022\hdtn_install\bin
- OK → NEW 선택
- 환경 변수 HDTN_SOURCE_ROOT 추가
- 변수 값을 소스 루트(README.md가 위치한 폴더)로 설정
- OK → OK 선택
---

HDTN 소스코드를 수정하지 않는 일반 사용자는, HDTN_SOURCE_ROOT\tests\test_scripts_windows 내 .bat 파일 예제 테스트 실행 가능.
단, 이 스크립트들은 개발자를 위해 작성되었으므로 수정이 필요할 수 있다:

예:
%HDTN_BUILD_ROOT%\common\bpcodec\apps\bpgen-async.exe
→ 이를 bpgen-async.exe 로 수정해야 한다.

또한 .bat 스크립트는 HDTN_SOURCE_ROOT\config_files 내 JSON 설정 파일을 참조하므로, 필요에 따라 수정 가능하다.

---

### 5.4.1 HDTN 개발자 (HDTN Developers)

만약 개발자가 Visual Studio 내에서 HDTN 소스 코드를 수정하려는 경우,  
`C:\hdtn_build_x64_release_vs2022\hdtn_install` 디렉터리를 삭제하고 다음 단계를 진행한다.

1. Visual Studio 2022 실행 → HDTN을 프로젝트로 열기:
   - **File >> Open >> CMake**
   - HDTN 루트의 `CMakeLists.txt` 열기
   - 상단의 드롭다운 설정이 `x64-Release`로 되어 있는지 확인 (아닐 경우 **Manage Configurations**에서 수정)

2. **Project >> View CMakeCache.txt** 실행 후 아래 항목 추가 (vs2022 디렉터리 접미사는 환경에 따라 다를 수 있음):
   - `BOOST_INCLUDEDIR:PATH=C:\hdtn_build_x64_release_vs2022\boost_1_78_0_install`
   - `BOOST_LIBRARYDIR:PATH=C:\hdtn_build_x64_release_vs2022\boost_1_78_0_install\lib64`
   - `BOOST_ROOT:PATH=C:\hdtn_build_x64_release_vs2022\boost_1_78_0_install`
   - `OPENSSL_INCLUDE_DIR:PATH=C:\hdtn_build_x64_release_vs2022\openssl-1.1.1s\install\include`
   - `OPENSSL_ROOT_DIR:PATH=C:\hdtn_build_x64_release_vs2022\openssl-1.1.1s\install`
   - `libzmq_INCLUDE:PATH=C:\hdtn_build_x64_release_vs2022\libzmq_v4.3.4_install\include`
   - `libzmq_LIB:FILEPATH=C:\hdtn_build_x64_release_vs2022\libzmq_v4.3.4_install\lib\libzmq-v143-mt-4_3_4.lib`  
     (환경에 따라 v141, v142일 수 있음)
   - `BUILD_SHARED_LIBS:BOOL=ON`

3. **Project >> Configure Cache** 실행
   - Visual Studio에서 열린 `CMakeCache.txt` 탭을 오른쪽 클릭 → "Open Containing Folder"
   - 탐색기 상단의 경로 복사
   - Windows 시작 메뉴에서 `env` 검색 → **Edit environmental variables for your account** 실행
   - 새 변수 추가:
     - `HDTN_BUILD_ROOT` → 예:  
       `C:\Users\username\CMakeBuilds\17e7ec0d-5e2f-4956-8a91-1b32467252b0\build\x64-Release`
     - `HDTN_INSTALL_ROOT` → 위 경로에서 `build`를 `install`로 변경:  
       `C:\Users\username\CMakeBuilds\17e7ec0d-5e2f-4956-8a91-1b32467252b0\install\x64-Release`
   - Click OK
4. `Path` 환경 변수 수정:
   - `Path` 항목을 더블클릭 →  
     `HDTN_INSTALL_ROOT\lib` 추가 (예:  
     `C:\Users\username\CMakeBuilds\17e7ec0d-5e2f-4956-8a91-1b32467252b0\install\x64-Release\lib`)  
   - 이는 HDTN이 **공유 라이브러리(shared library)** 로 빌드되며, 여러 `.dll` 파일을 찾을 수 있도록 Windows 환경에서 필요하다.

5. Visual Studio 재시작 → 환경 변수 반영 후 HDTN 빌드:
   - **Build >> Build All**
   - **Build >> Install HDTN**
   - `HDTN_SOURCE_ROOT\tests\test_scripts_windows` 내 `unit_tests.bat` 실행

6. Web GUI 예제 실행:
   - `test_tcpcl_fast_cutthrough_oneprocess.bat` 실행
   - 웹 브라우저에서 [https://localhost:8086](https://localhost:8086) 접속
   - 종료 시에는 각 cmd 창에서 **Ctrl+C** 입력 후 종료
> NOTE:HDTN은 `BUILD_SHARED_LIBS=ON`으로 설정되어 공유 라이브러리로 빌드된다.  
> 따라서 소스 코드 변경 후에는 반드시 **Build >> Install HDTN** 단계를 수행해야 변경 사항이 반영된다.  

---
## 5.4.2 설치된 HDTN 라이브러리를 자체 프로젝트에서 사용하는 개발자용 설정 (Setup Instructions for Developers Using Installed HDTN Libraries within their own Projects)
HDTN은 **현대식 CMake**를 사용한다.  
HDTN이 설치되면 적절한 CMake 패키지가 함께 설치되며, 개발자는 이를 import하여 사용할 수 있다.  

- 예제: `HDTN_SOURCE_ROOT\tests\unit_tests_import_installation\CMakeLists.txt`  
  → 설치된 라이브러리와 헤더를 가져와 HDTN 단위 테스트를 빌드하는 예시 제공  

모든 라이브러리는 **공유(shared)** 또는 **정적(static)** 라이브러리로 import 가능하다.  

**그림 5-1**은 LTP 라이브러리를 import하는 cmake 예시를 보여준다.
![Figure 5-1](image-4.png)

---
## 5.5 ARM 환경에서 HDTN 빌드 (Build HDTN on ARM)

HDTN은 기본적으로 **x86 하드웨어 최적화** 옵션을 적용하여 컴파일된다.  
ARM용으로 빌드하려면 이러한 최적화를 비활성화해야 한다.  
Ubuntu에서 실행되는 ARM 플랫폼(예: Raspberry Pi 4, Nvidia Jetson Nano)에서 HDTN을 빌드하려면,  
**섹션 4.2와 5.2**의 Ubuntu 의존성 설치 절차를 먼저 따른 후 다음 명령어를 실행한다:

```bash
cd HDTN
mkdir build && cd build
cmake .. -DCMAKE_SYSTEM_PROCESSOR=arm
make -j
sudo make install -j
```
이 명령어는 Raspberry Pi 4나 Nvidia Jetson Nano와 같은 모든 ARM 기반 Ubuntu 플랫폼에서 동작한다. Raspberry Pi 4에서의 컴파일 시간은 약 30분 ~ 1시간 소요된다. 메모리가 제한된 장치에서는 gcc가 모든 RAM을 소모하지 않도록 make -j2와 같이 1~2 코어만 사용하여 빌드하는 것을 권장한다.

### 5.5.1 디버깅 오류 / 문제 해결 (Debugging Errors/Problems)
- cpuid.h를 찾을 수 없다는 오류(HDTN/common/util/src/CpuFlagDetection.cpp 관련)가 발생하면  
  → CMakeList.txt 파일의 수정 내용을 다시 확인
- recompile with -fPIC(다시 -fPIC 옵션으로 컴파일하라는) 오류가 발생하면  
  → CMakeCache.txt 파일의 수정 내용을 다시 확인
- runscript.sh not found(스크립트 파일을 찾을 수 없다는) 오류가 발생하면  
  → 다음 명령어 실행:  
    `export HDTN_SOURCE_ROOT=/home/user/HDTN`
- no tcpdump(패킷 캡처 도구가 없다는) 오류가 발생하면  
  → 다음 명령어 실행:  
    `sudo apt install tcpdump`
- 런타임 오류가 발생하면  
  → HDTN/logs 디렉터리의 로그 파일 확인.  
    (기본적으로 로그 파일은 생성되지 않으나, 섹션 19.1의 안내에 따라 생성할 수 있다.)

---

## 5.6 x86에서 ARM 빌드하기 (Building for ARM on x86)
### 5.6.1 x86 데스크톱에서 ARM Chroot 설정 (Setting up ARM Chroot on x86 Desktop)
다음 명령어들을 실행한다:
- `sudo apt install qemu-user-static`  
- `sudo apt install debootstrap`  
- `sudo qemu-debootstrap --variant=buildd --arch arm64 focal /var/chroot/ http://ports.ubuntu.com/`  

  - 위 명령어에서 **focal**은 Ubuntu 릴리스 이름이며 필요에 따라 변경해야 한다.  
  - 위 마지막 명령어는 `/var/chroot` 위치에 armhf 운영체제를 생성한다. 사용자가 다른 위치로 옮길 수는 있으나, `/home/` 및 사용자 디렉터리 외부에 두는 것이 권장된다.  

- Chroot 환경으로 들어가려면 다음 명령어를 사용한다:  
  `sudo chroot /var/chroot`

이 시점에서 사용자는 ARM 환경에 들어오게 된다. 이후 명령어들은 ARM 환경에 들어간 후에 실행해야 한다.

---
### 5.6.2 Chroot 환경에서 HDTN 의존성 설정 (Setting up HDTN Dependencies in the Chroot Environment)

> **주의:** chroot 환경에는 `sudo`가 존재하지 않는다.

```bash
apt install make cmake build-essential software-properties-common
add-apt-repository universe
apt update
apt install libboost-dev libboost-all-dev libzmq3-dev openssl libssl-dev
```
---
### 5.6.3 HDTN 컴파일 (Compiling HDTN)
- Github에서 최신 HDTN을 다운로드한다.
- 홈 디렉터리에 압축을 푼다.
  > 참고: Windows Subsystem for Linux(WSL)에서는 ARM 에뮬레이터 디렉터리에 직접 쓸 수 없다.
- 다음 명령을 실행한다.
  ```bash
  sudo mv HDTN /var/chroot/home
  sudo chroot /var/chroot
  cd home/HDTN
  mkdir build
  cd build
  cmake .. -DCMAKE_SYSTEM_PROCESSOR=arm
  make -j1
  ```
  > 다중 스레드를 사용할 경우 경쟁 조건(race condition)이 발생한다.

### 5.6.4 유용한 명령어 (Useful Commands)
- readelf -h executable
  > 실행 파일 헤더를 읽는다.
- apt install cmake-curses-gui
  > 정적 빌드를 위한 CMakeCache.txt 편집기를 설치한다.
- ccmake ..
  >CMakeCache.txt 편집기를 실행한다.

  ---

# 6. HDTN 실행 (Running HDTN)

> **주의:** 설정 파일(Section 13.0 참조)이 올바른지 확인해야 한다.  
- outduct의 `remotePort`가 induct의 `boundPort`와 동일한지 확인  
- 일관된 `convergenceLayer`가 사용되는지 확인  
- outduct의 `remoteHostname`이 올바른 IP 주소를 가리키는지 확인  

`tcpdump`를 사용하여 HDTN ingress, storage, egress를 테스트할 수 있다.  
생성된 pcap 파일은 Wireshark로 열 수 있다:

```bash
sudo tcpdump -i lo -vv -s0 port 4558 -w hdtn-traffic.pcap
```
다른 터미널에서 실행 : ./runscript.sh
> NOTE : Contact Plan은 각 노드의 향후 연결을 나열하며, module/router/contact_plans/contactPlan.json에 있다. 이파일에는 송신노드와 수신노드, 시작및 종료시간, 데이터 전송 속도등이 포함되어 있다.  
라우터는 Contact Plan의 일정에 따라 링크(연결) 사용 가능 이벤트를 Ingress와 Storage 모듈에 전달한다.  
Ingress 모듈이 특정 목적지에 대한 Link Available(링크 사용 가능) 이벤트를 받으면, 해당 번들을 직접 Egress로 전송한다.
반대로, 링크가 Unavailable(사용 불가) 상태일 때는 번들을 Storage(저장소)로 보낸다.
Storage 모듈은 Link Available 이벤트를 받으면 해당 목적지로 번들을 방출(전송)한다.  
Link Down(링크 종료) 이벤트를 받으면, Storage는 번들 방출을 중단한다.

추가적인 테스트 스크립트들은 `test_scripts_linux` 및 `test_scripts_windows` 디렉터리에 있으며, 모든 convergence layer 시나리오를 테스트하는 데 사용할 수 있다.  

---

## 6.1 디렉터리 구조 (Directory Structure)

- **common/**       --- 공용 라이브러리 및 유틸리티  
- **module/**       --- HDTN 핵심 모듈  
  - **egress/**     --- 번들 트래픽을 전달하는 CL 어댑터  
  - **ingress/**    --- 번들 형식으로 트래픽을 수신하는 CL 어댑터  
  - **storage/**    --- 번들을 저장  
  - **router/**     ---링크 상태 및 라우트를 다른 모듈로 전송  
  - **hdtn_one_process/**   --- 주요 프로세스를 하나의 HDTN 프로세스로 통합  
  - **udp_delay_sim/**      --- 긴 지연을 시뮬레이션하는 프록시  
  - **telem_cmd_interface/**  --- 통계 표시 및 설정을 위한 웹 인터페이스  

- **config_files/** --- HDTN 설정 파일  
- **tests/**        --- 테스트 코드  

---

## 6.2 단위 테스트 (Unit Tests)

HDTN 빌드 완료 후(Section 4.0 참조), 빌드 디렉터리 내에서 다음 명령어 실행:  

```bash
./tests/unit_tests/unit-tests
```
## 6.3 통합 테스트 (Integrated Tests)
HDTN 빌드 완료 후(Section 4.0 참조), 빌드 디렉터리 내에서 다음 명령어 실행:

```bash
./tests/integrated_tests/integrated-tests
```
---
# 7. 그래픽 사용자 인터페이스 (Graphical User Interface)
## 7.1 웹 인터페이스 실행 (Running the Web Interface)
이 저장소에는 HDTN 엔진의 통계를 표시하는 웹 기반 사용자 인터페이스를 실행할 수 있는 코드가 포함되어 있다. 이 웹 인터페이스는 Boost 표준 설치에 포함된 헤더 전용 라이브러리인 Boost Beast에 의존한다. 

웹 인터페이스는 http와 https를 모두 지원하므로 OpenSSL이 필요하며, 따라서 WS(WebSocket)와 WSS(WebSocket Secure)도 모두 지원한다.  
웹 인터페이스는 기본적으로 컴파일된다. HDTNOneProcess가 실행될 때마다 웹 페이지는 http://localhost:8086 에서 접근할 수 있습니다.

웹 인터페이스 실행을 막으려면, 일반적인 Linux 빌드 절차를 따르되 `cmake` 명령어만 다음과 같이 변경한다:  

```bash
cmake -DUSE_WEB_INTERFACE:BOOL=OFF ..
```
---

## 7.2 통계 페이지 (Statistics Page)  
이 페이지는 HDTN의 실시간 텔레메트리(상태 정보)를 표시합니다.  
상단에는 세 개의 박스가 있으며, 각각 현재 데이터 전송 속도(Mbps), 평균 데이터 전송 속도, 그리고 도달한 최대 데이터 전송 속도를 보여줍니다.  
이 모든 값은 Ingress 모듈에서 측정됩니다.

그 아래에는 세 개의 그래프가 있습니다.  
첫 번째와 두 번째 그래프는 Ingress 모듈과 Egress 모듈의 데이터 전송 속도(Mbps)를 보여줍니다.  
세 번째 그래프는 파이 차트로, Ingress가 수신한 데이터 번들이 어디로 전달되었는지(저장소 모듈로 보냈는지, 직접 Egress로 보냈는지)를 나타냅니다.  
만약 번들이 Storage에서 Egress로 전달된 경우, 파이 차트에서는 Egress로 전달된 것으로 집계됩니다.

![그림7-1](image-1.png)

그래프 아래에는 HDTN의 여러 구성요소별 통계가 카드 형태로 표시됩니다. 현재는 Ingress와 Egress에 대한 통계 표시 입니다.

---

## 7.3 시스템 뷰 페이지 (System View Page)
이 페이지는 통계 페이지 좌측 상단의 System View GUI 버튼을 클릭하여 접근할 수 있습니다. 그림 7-2와 같이 이 페이지는 HDTN의 다양한 모듈들을 그래픽으로 보여주며, 번들 데이터가 어디서 오고 어디로 가는지에 대한 정보도 함께 표시합니다. 
- 상단에는 사용자가 정보를 더 잘 볼 수 있도록 조정 가능한 설정들이 있습니다.
- 페이지의 왼쪽에는 데이터가 수신되는 IP주소와 IPN 번호가 표시됩니다.
- 페이지의 오른쪽에는 이 HDTN 노드에서 데이터가 전송되는 노드와 IPN 번호가 표시됩니다.
 - 그래픽에서는 데이터가 Ingress로 들어올 때, HDTN의 각 모듈(Ingress, Storage, Egress) 사이를 이동할때, 그리고 Engress를 통해 나갈때의 데이터 전송 속도가 표시됩니다. 
 - Storage 그래픽에는 사용중인 저장 공간의 비율과 용량이 표시됩니다.
 - 또한 사용자는 각 HDTN 모듈 위에 마우스를 올리면 해당 모듈의 데이터가 팝업 그래픽으로 표시되는 기능을 사용할 수 있습니다. 그림 7-3은 Storage 모듈에 대한 이 정보를 예시로 보여줍니다.
![Figure 7-2](image-2.png)
![Figure 7-3](image-3.png)

## 7.4 설정 페이지 (Config Page )
이 페이지는 아직 구성되지 않았습니다.

## 7.5 통계 로깅(Statistics Logging )
HDTN 텔레메트리는 **CMake 옵션 `DO_STATS_LOGGING`**을 사용하여 자동으로 CSV 파일로 기록될 수 있습니다. 이를 활성화 하는 명령어는 다음과 같습니다:
``` bash
cmake -DDO_STATS_LOGGING:BOOL=ON. 
```
- 로그 파일은 소스코드 루트의 /stats 디렉토리에 생성됩니다. 
- 통계정보는 1초간격으로 기록됩니다. 
- 현재 지원되는 통계항목은 다음과 같습니다:
  * ingress_data_rate_mbps 
  * ingress_total_bytes_sent 
  * ingress_bytes_sent_egress 
  * ingress_bytes_sent_storage 
  * storage_used_space_bytes 
  * storage_free_space_bytes 
  * storage_bundle_bytes_on_disk 
  * storage_bundles_erased 
  * storage_bundles_rewritten_from_failed_egress_send 
  * storage_bytes_sent_to_egress_cutthrough 
  * storage_bytes_sent_to_egress_from_dis 
  * egress_data_rate_mbps 
  * egress_total_bytes_sent_success 
  * egress_total_bytes_attempted 

---

# 8. API 시작하기 (Getting Started with the API)

HDTN API는 HDTN API는 **ZeroMQ 기반 메시징 API**로, **요청-응답(request-reply) 메시징 패턴**을 따른다. 이 API는 ZeroMQ 메시징 라이브러리를 활용하여 HDTN과 다른 소프트웨어 시스템 간의 효율적이고 유연한 통신을 가능하게 한다.  
사용자는 HDTN의 **구성(configuration)** 및 **통계(statistics)** 정보를 요청할 수 있다.  
예제로, `tests/tests_scripts_linux` 디렉터리에는 **Python 스크립트** `run_api_commands.py`가 제공되며, 이 스크립트는 지원되는 모든 API 호출을 포함하고 있다.  

API 호출은 ZeroMQ 라이브러리를 지원하는 모든 프로그래밍 언어에서 사용할 수 있으며,  
요청과 응답은 **JSON 문자열(JSON-encoded strings)** 형식으로 이루어진다.  
---
## 8.1 API 호출 (API Calls)
### 8.1.1 HDTN 버전 (HDTN Version)
- 호출(Call): get_hdtn_version
- 설명(Description): 현재 HDTN 버전을 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_hdtn_version"
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 현재 HDTN 버전이 JSON 문자열로 반환됩니다.

---

### 8.1.2 HDTN 설정 (HDTN Configuration) 
- 호출(Call): get_hdtn_config
- 설명(Description): 현재 활성화된 HDTN 설정 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_hdtn_config" 
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 현재 활성화된 HDTN 설정 정보가 JSON 문자열로 반환됩니다.

---

### 8.1.3 스토리지 (Storage)
- 호출(Call): get_storage
- 설명(Description): HDTN의 스토리지 통계 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_storage"  
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 HDTN의 스토리지 통계 정보가 JSON 문자열로 반환됩니다.
---

### 8.1.4 만료 예정 스토리지 (Expiring Storage)
- 호출(Call): get_expiring_storage
- 설명(Description): HDTN의 만료 예정 스토리지 통계 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_expiring_storage",
    "priority": INTEGER,
    "thresholdSecondsFromNow": INTEGER
  }
  ```
- 매개변수(Parameter):
  - priority: 우선순위에 따라 만료된 번들을 검색합니다.
    - 입력값: 0, 1, 또는 2
  - thresholdSecondsFromNow: 몇 초 후에 만료될 번들을 검색할지 지정합니다.
    - 입력값: 양의 정수
- 반환 값(Return Value): 호출이 성공하면 HDTN의 만료 예정 스토리지 통계 정보가 JSON 문자열로 반환됩니다.
- 예시(Example):
  ```json
  {
    "apiCall": "get_expiring_storage",
    "priority": 1,
    "thresholdSecondsFromNow": 600
  }
  ```
---

### 8.1.5 Inducts 
- 호출(Call): get_inducts
- 설명(Description): HDTN의 모든 Inducts 통계 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_inducts"
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 HDTN의 모든 Inducts 통계 정보가 JSON - 문자열로 반환됩니다.

---
### 8.1.6 Outducts
- 호출(Call): get_outducts
- 설명(Description): HDTN의 모든 Outducts 통계 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_outducts"
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 HDTN의 모든 Outducts 통계 정보가 JSON 문자열로 반환됩니다.
---
### 8.1.7. Outduct 최대 송신 속도 설정 (Maximum Send Rate for an Outduct)

- 호출(Call): set_max_send_rate
- 설명(Description): HDTN의 특정 Outduct에 대해 초당 비트 단위로 최대 전송 속도를 설정합니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "set_max_send_rate",
    "rateBitsPerSec": INTEGER,
    "outduct": INTEGER
  }
  ```
- 매개변수(Parameter):
  - rateBitsPerSec: 최대 전송 속도를 초당 비트 단위로 지정합니다. 0을 입력하면 상한이 설정되지 않습니다.
    - 입력값: 0 또는 양의 정수
  - outduct: 최대 전송 속도를 적용할 Outduct 번호를 지정합니다.
    - 입력값: 양의 정수
- 반환 값(Return Value): 호출이 성공하면 성공 응답이 JSON 문자열로 반환됩니다.
- 예시(Example):
  ```json
  {
    "apiCall": "set_max_send_rate",
    "rateBitsPerSec": 64000,
    "outduct": 1
  }
  ```
---

### 8.1.8 BP 보안 설정 (BP Security Configuration)
- 호출(Call): get_bpsec_config
- 설명(Description): HDTN의 BP 보안 통계 정보를 가져오는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "get_bpsec_config"
  }
  ```
- 매개변수(Parameter): 없음
- 반환 값(Return Value): 호출이 성공하면 HDTN의 BP 보안 통계 정보가 JSON 문자열로 반환됩니다.

---

### 8.1.9 Contact Plan 업로드 (Upload Contact Plan)
- 호출(Call): upload_contact_plan
- 설명(Description): HDTN에 Contact Plan을 업로드하는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "upload_contact_plan",
    "contactPlanJson": JSON STRING
  }
  ```
- 매개변수(Parameter):
  - contactPlanJson: 적용할 Contact Plan을 JSON 문자열로 지정합니다.
    - 입력값: JSON 문자열
- 반환 값(Return Value): 호출이 성공하면 성공 응답이 JSON 문자열로 반환됩니다.
- 예시(Example): 2개 이상 노드 contact Plan(원문에는 2개로 표현됨)
  ```json
  {
    "apiCall": "upload_contact_plan",
    "contactPlanJson": {
      "contacts": [
        {
          "contact": 0,
          "source": 1,
          "dest": 10,
          "startTime": 0,
          "endTime": 2000,
          "rateBitsPerSec": 0,
          "owlt": 1
        },
        {
          "contact": 1,
          "source": 10,
          "dest": 20,
          "startTime": 0,
          "endTime": 2000,
          "rateBitsPerSec": 0,
          "owlt": 1
        },
        {
          "contact": 2,
          "source": 20,
          "dest": 2,
          "startTime": 0,
          "endTime": 2000,
          "rateBitsPerSec": 0,
          "owlt": 1
        }
      ]
    }
  }
  ```

---
### 8.1.10 Ping
- 호출(Call): ping
- 설명(Description): HDTN 노드의 특정 서비스를 Ping하는 데 사용됩니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "ping",
    "bpVersion": INTEGER,
    "nodeId": INTEGER,
    "serviceId": INTEGER
  }
  ```
- 매개변수(Parameter):
  - bpVersion: Ping에 사용할 번들 프로토콜 버전을 지정합니다.
    -입력값: 6 또는 7
  - nodeId: 노드 번호를 지정합니다.
    - 입력값: 양의 정수
  - serviceId: Ping할 서비스를 지정합니다.
    - 입력값: 양의 정수
- 반환 값(Return Value): 호출이 성공하면 Ping 응답이 JSON 문자열로 반환됩니다.
- 예시(Example):
  ```json
  {
    "apiCall": "ping",
    "bpVersion": 7,
    "nodeId": 2,
    "serviceId": 1
  }
  ```
---
### 8.1.11 링크 비활성화 (Take Link Down)
- 호출(Call): set_link_down
- 설명(Description): OutductVector에서 링크를 비활성화합니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "set_link_down",
    "outductIndex": INTEGER
  }
  ```
- 매개변수(Parameter):
  - outductIndex: Outduct 목록에서 비활성화할 링크의 인덱스를 지정합니다.
    - 입력값: 0 또는 양의 정수
- 반환 값(Return Value): 호출이 성공하면 성공 응답이 JSON 문자열로 반환됩니다.
- 예시(Example):
  ```json
  {
    "apiCall": "set_link_down",
    "outductIndex": 1
  }
  ```
---

### 8.1.12 링크 활성화 (Bring Link Up)
- 호출(Call): set_link_up
- 설명(Description): OutductVector에서 링크를 활성화합니다.
- 구문(Syntax):
  ```json
  {
    "apiCall": "set_link_up",
    "outductIndex": INTEGER
  }
  ```
- 매개변수(Parameter):
  - outductIndex: Outduct 목록에서 활성화할 링크의 인덱스를 지정합니다.
    -입력값: 0 또는 양의 정수
- 반환 값(Return Value): 호출이 성공하면 성공 응답이 JSON 문자열로 반환됩니다.
- 예시(Example):
  ```json
  {
    "apiCall": "set_link_up",
    "outductIndex": 0
  }
  ```
---
# 9. 명령줄 인터페이스 (Command Line Interface)
HDTN 빌드 과정에서 **명령줄 인터페이스(CLI) 애플리케이션**이 자동으로 생성된다.  
컴파일 후 `build/module/cli` 폴더에 위치하며, HDTN API와 상호작용하여 소프트웨어를 **설정 및 제어**하는 데 사용된다.

---
## 9.1 사용법 (Usage)

HDTN CLI는 `hdtn-cli` 명령으로 실행된다. CLI가 동작하려면 HDTN이 실행 중이어야 한다. 현재는 지원되는 명령 옵션이 제한적이다.  
옵션 목록을 확인하려면 다음을 실행한다:
```bash
hdtn-cli --help
```
시작방법 : 
1. HDTN 빌드 (README.md의 지침 참조).
2. HDTN 설치 (README.md의 지침 참조).
3. HDTN 인스턴스 실행 (README.md의 지침 참조).
4. 명령 목록을 보려면 hdtn-cli --help 실행.
5. 원하는 CLI 명령을 실행하려면 hdtn-cli <command1> <command2> ... 실행.

---
### 9.2 예시 (Examples)
다음은 CLI를 사용하여 HDTN을 구성하고 제어하는 방법을 보여준다.

- 사용 가능한 옵션 확인
  ```bash
    hdtn-cli --help
  ```
  - 옵션
    |  명령  |  설명  |
    |-----------------------------|--------------------|
    | --help | 도움말 출력 |
    | --hostname arg (=127.0.0.1) | HDTN 호스트명 |
    | --port arg (=10305) | HDTN 포트 |
    | --contactPlanFile arg  | 로컬 Contact Plan 파일 |
    | --contactPlanJson arg  | Contact Plan JSON 문자열 |
    | --outduct[0].rateBps arg | 첫 번째 Outduct 전송률 제한 (bps) |
    |--outduct[1].rateBps arg | 두 번째 Outduct 전송률 제한 (bps) |

- 새로운 Contact Plan 파일 업로드
  ```bash
    hdtn-cli --contactPlanFile /home/user/contact_plan.json
  ```
- JSON으로 Contact Plan 업로드
  ```bash
    hdtn-cli --contactPlanJson '{"contacts":[{"contact":0,"source":1,"dest":10,"startTime":0,"endTime":2000,"rateBitsPerSec":0,"owlt":1}]}'
  ```
- 첫 번째 Outduct 전송률을 1 Mbps로 제한
  ```bash
    hdtn-cli --outduct[0].rateBps 1000000
  ```
- 첫 번째와 두 번째 Outduct 전송률을 각각 1 Mbps로 제한
  ```bash
    hdtn-cli --outduct[0].rateBps 1000000 --outduct[1].rateBps 1000000
  ```
---
# 10. 시뮬레이션 (SIMULATIONS)
HDTN은 OMNeT++ 시뮬레이션 프레임워크를 기반으로 구축된 지연 허용 네트워크용 오픈소스 시뮬레이터인 DtnSim을 사용하여 시뮬레이션할 수 있다. 
> NOTE : OMNeT++ 라이선스가 필요할 수 있습니다. 자세한 내용은 [OMNeT++ 라이선스 FAQ](https://omnest.com/licensingfaq)를 참조하세요.

[DtnSim의 공식 저장소](https://bitbucket.org/lcd-unc-ar/dtnsim/src/master/)에서 “support-hdtn” 브랜치를 사용해야 합니다.

HDTN과 DtnSim을 사용한 시뮬레이션은 현재 **Linux(Debian 및 Ubuntu)**에서만 테스트되었다. 소프트웨어 설치를 위해 HDTN 및 DtnSim의 README 지침을 따른다. 설치 단계에 대한 자세한 내용은[관련 문서](Installing_Configuring_HDTN_Simulator.md) 를 참고한다.
 
---
# 11. HDTN 애플리케이션 (HDTN Applications) 
HDTN은 독립 모듈로 사용하거나 HDTN 과 함께 사용할 수 있는 여러 애플리케이션을 제공합니다. 현재 제공되는 HDTN 애플리케이션은 다음과 같습니다.

- BPGen: 번들을 생성하며, 수신 도구인 BPSink와 함께 사용됩니다.
- BPSink: 번들을 수신하고 검증하며, 생성 도구인 BPGen과 함께 사용됩니다.
- BPSendFile: 단일 파일 또는 디렉터리(재귀 포함)를 전송합니다.
- BPReceiveFile: 번들을 임의의 순서로 수신하고, 필요한 경우 조각을 재조립하여 사용자 지정 디렉터리에 파일을 저장합니다.
- BPSendStream: 실시간 전송 프로토콜(RTP) 패킷에서 번들을 생성합니다.
- BPRecvStream: RTP 패킷을 포함하는 번들을 수신하고 RTP 패킷을 출력합니다.
- BPing: 핑 번들을 생성합니다.
- BPSendPacket: UDP 또는 STCP 페이로드 데이터를 번들로 생성합니다.
- BPReceivePacket: UDP 또는 STCP 페이로드 데이터를 포함하는 번들을 수신하고 출력합니다.

그림 11-1은 이러한 애플리케이션이 HDTN의 모듈과 연결되는 방식을 보여줍니다.
![그림 11-1](image-5.png)

---
# 12. 실행 스크립트 (RUNSCRIPT)
HDTN의 각 인스턴스는 하나의 런스크립트 파일(JSON 형식)을 필요로 한다.  
각 파일은 다음 순서로 구성된다:
1. **경로 변수(Path Variables)** 지정  
2. **Egress (Outduct)** 및 **bpsink** 실행 (번들을 수신하는 방법)  
3. **Ingress** 실행  
4. **bpgen** 실행 (번들을 생성하는 프로세스)  
5. (선택 사항) 정리 절차(cleanup procedure)  

런스크립트에는 두 가지 설정 방식이 있다:  
- **Cut-through** (Storage를 우회)  
- **Storage + Router** 사용  

Cut-through 옵션은 링크가 활성화되어 있어야 하며, **BPv6 custody**가 없어야 한다.  

> 참고: `Egress`와 `Ingress`를 별도로 실행하지 않으려면, `hdtn-one-process` 명령을 사용하여 두 모듈을 하나로 통합할 수 있다. 예제 런스크립트는 `HDTN/runscript.sh`에서 확인할 수 있다.  

---

## 12.1 경로 변수 (Path Variables)

런스크립트 파일의 **경로 변수**는 필요한 모든 설정 파일의 위치를 지정한다. 이 섹션만 수정하면 설정 파일을 변경할 때 다른 섹션을 수정할 필요가 없다. 
경로 변수에는 다음 항목들이 포함된다:
- config files  
- hdtn config  
- bpsec config  
- sink config  
- gen config  

이 섹션의 끝에는 $HDTN_SOURCE_ROOT로 디렉터리를 변경하는 명령어가 포함됩니다.
이는 실행 스크립트가 HDTN 소스 루트 디렉터리(HDTN이 실행되는 위치)에 있지 않을 경우를 대비한 것입니다.
```bash
cd $HDTN_SOURCE_ROOT
```
---

## 12.2 BpSink

BpSink의 일반적인 예시는 다음과 같다. 

```bash
./build/common/bpcodec/apps/bpsink-async --my-uri-eid=ipn:2.1 --inducts-config-file=$sink_config --bpsec-config-file=$bpsec_config &
```
이 명령은 3가지 주요 부분으로 구성된다:
- HDTN 내 bpsink 실행 파일의 위치
- 수신 노드의 엔드포인트 ID (EID)
- inducts 설정 파일의 위치
> ipn:2.1 → 두 번째 엔드포인트가 수신 노드임을 의미한다.

> $sink_config는 경로 변수와 연결된다.
> --bpsec-config-file은 선택 사항이며, BPSec 활성화 및 **bpsink 노드가 보안 수신자(security acceptor)**인 경우에만 필요하다. 이 옵션은 BPSec 정책 규칙과 보안 오류 이벤트 처리를 정의한다.

> 권장: bpsink 종료 전 **3초 지연(sleep 3)**을 두어 초기화 후 다음 단계가 시작되도록 한다.

표 12-1: BpSink 실행 파라미터
| 파라미터 (Parameter) | 설명 (Description) |	기본값 (Default Value) |
|---------------------|--------------------|-----------------------|
|simulate-processing-lag-ms	| 번들 처리에 추가 지연(ms) (테스트용)	| 0 |
| inducts-config-file	| Inducts 설정 파일	| "" |
| my-uri-eid	| BpSink End Point ID | ipn:2.1 |
| custody-transfer-outducts-config-file	| Custody 전송용 Outduct 설정 파일 (Custody가 있는 경우 사용 사용) | "" |
| acs-aware-bundle-agent | Custody 전송이 유효한 CTEB가 있을 경우 Aggregate Custody Signals 지원	| NA |
| bpsec-config-file	| BPSec 설정 파일	| "" |
| max-rx-bundle-size-bytes	| 수신 가능한 최대 번들 크기 (기본값=10MB)	| 10000000 |

---

## 12.3 BpReceiveFile

BpReceiveFile의 일반적인 실행 예시는 다음과 같다:
```bash
./build/common/bpcodec/apps/bpreceivefile --save-directory=received --my-uri-eid=ipn:2.1 --inducts-config-file=$sink_config --bpsec-config-file=$bpsec_config &
```
이 명령은 3가지 주요 부분으로 구성된다:
- 수신 데이터가 저장될 디렉터리 (--save-directory)
- 수신 노드의 엔드포인트 ID (EID)
- inducts 설정 파일의 위치

> $gen_config는 경로 변수에 정의된 값을 참조한다. Generator 설정 파일에 대한 자세한 내용은 섹션 13.3을 참조한다.

bpsec-config-file은 선택적 매개변수로, BPSec이 활성화되고 bpreceivefile 노드가 보안 수락자(security acceptor)인 경우에만 필요하다. 이 옵션은 정책 규칙과 보안 작업 실패 이벤트 처리가 포함된 BPSec 설정 파일의 위치를 지정한다.

bpreceivefile을 종료하려면, 다음 섹션의 설정 파일이 시작되기 전에 초기화 시간을 위해 8초 대기 명령어(sleep 8)를 삽입하는 것이 권장된다.

표 12-2: BpReceiveFile 실행 파라미터
| 매개변수(Parameter)                   | 설명(Description)                                                                 | 기본값(Default Value)                                                                 |
|---------------------------------------|----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| inducts-config-file                   | Inducts 설정 파일.                                                               | ""                                                                                    |
| my-uri-eid                            | BpReceiveFile 엔드포인트 ID.                                                     | ipn:2.1                                                                               |
| custody-transfer-outducts-config-file | Custody 전송을 위한 Outducts 설정 파일 (custody가 있는 경우 사용).                | ""                                                                                    |
| acs-aware-bundle-agent                | Custody 전송이 유효한 CTEB가 있을 경우 Aggregate Custody Signals를 지원해야 함.    | NA                                                                                    |
| max-rx-bundle-size-bytes              | Convergence Layer에 따라 수신 가능한 번들의 최대 크기(기본값=10MB).               | 10000000                                                                              |
| outgoing-rtp-port                     | 생성된 RTP 스트림의 대상 포트.                                                   | 50560                                                                                 |
| outgoing-rtp-hostname                 | RTP 패킷을 전달할 원격 IP.                                                       | 127.0.0.1                                                                             |
| num-circular-buffer-vectors           | 수신된 번들을 위한 순환 버퍼 벡터 요소의 개수.                                   | 50                                                                                    |
| max-outgoing-rtp-packet-size-bytes    | 송신 RTP 패킷의 최대 크기(바이트 단위).                                          | 1400                                                                                  |
| shm-socket-path                       | GStreamer와 공유 메모리 Sink를 위한 소켓의 위치.                                 | GST_HDTN_OUTDUCT_SOCKET_PATH                                                          |
| outduct-type                          | RTP 스트림을 오프보드하기 위한 Outduct 유형.                                     | udp                                                                                   |
| gst-caps                              | 공유 메모리 인터페이스 이전에 GStreamer 요소에 적용할 Caps.                       | application/x-rtp,<br> media=(string)video,<br> clock-rate=(int)90000,<br> encoding-name=(string)H264,<br> payload=(int)96 |
| bpsec-config-file                     | BPSec 설정 파일.                                                                 | ""                                                                                    |
---

## 12.4 Egress
Egress의 일반적인 실행 예시는 다음과 같다.
```bash
./build/module/egress/hdtn-egress-async --hdtn-config-file=$hdtn_config &
```

이 명령은 두 가지 주요 부분으로 구성된다.
- HDTN 내 Egress 코드 위치
- Egress 실행에 필요한 HDTN 설정 파일
> 자세한 내용은 섹션 13.1을 참고.

>권장: Egress 종료 전 3초 지연(sleep 3)을 두어 초기화 후 다음 단계가 시작되도록 한다.

표 12-3: Egress 실행 파라미터

| 파라미터 (Parameter)	| 설명 (Description) |	기본값 (Default Value) |
| -----------------------| -------------------------| --------------------------|
| hdtn-config-file|	HDTN 설정 파일	| hdtn.json |
| hdtn-distributed-config-file	| HDTN 분산 모드 설정 파일	| hdtn_distributed.json |

---

## 12.5   Router 
Router의 일반적인 실행 예시는 다음과 같다.
```bash
./build/module/router/hdtn-router --contact-plan-file=contactPlan.json --hdtn-config-file=$hdtn_config &
```
이 명령어는 세 가지 주요 부분으로 구성된다.
- HDTN 내 Router 코드 위치
- Contact Plan 파일
- Egress와 동일한 HDTN 설정 파일
> 자세한 내용은 섹션 13.1 참고.

> 권장: Router 종료 전 1초 지연(sleep 1)을 두어 초기화 후 다음 단계가 시작되도록 한다.

예시: 4개 HDTN 노드 라우팅 시나리오 테스트 :
```bash
$HDTN_SOURCE_ROOT/test/test_scripts_linux/Routing_Test
```

### 표 12-4: Router 실행 파라미터
### 표 12-4: Router 실행 파라미터

| 파라미터(Parameter)          | 설명(Description)                               | 기본값(Default Value)      |
|------------------------------|------------------------------------------------|----------------------------|
| use-unix-timestamp           | Contact Plan에서 Unix 타임스탬프 사용           | NA                         |
| use-mgr                      | Multigraph Routing Algorithm 사용               | NA                         |
| hdtn-config-file             | HDTN 설정 파일                                  | hdtn.json                  |
| hdtn-distributed-config-file | HDTN 분산 모드 설정 파일                        | hdtn_distributed.json      |
| contact-plan-file            | 링크 가용성과 라우팅을 위한 Contact Plan 파일   | DEFAULT_FILE               |

---

## 12.6 Ingress
Ingress의 일반적인 실행 예시는 다음과 같다:
```bash
./build/module/ingress/hdtn-ingress --hdtn-config-file=$hdtn_config --bpsec-config-file=$bpsec_config &
```
이 명령어는 두가지 주요 부분으로 구성된다 : 
- HDTN 내 Ingress 코드 위치
- Ingress 실행에 필요한 HDTN 설정 파일
> HDTN 설정 파일에 대한 자세한 내용은 섹션 13.1을 참고

> bpsec-config-file은 선택 사항이며, BPSec이 활성화되고 이 HDTN 노드가 보안 소스(security source), 검증자(verifier), 또는 수락자(acceptor)인 경우에만 필요하다.

> 권장: Ingress 종료 전 3초 지연(sleep 3)을 두어 초기화 후 다음 단계가 시작되도록 한다.

### 표 12-5: Ingress 실행 파라미터

| 파라미터(Parameter)          | 설명(Description)             | 기본값(Default Value) |
|------------------------------|-------------------------------|-----------------------|
| hdtn-config-file             | HDTN 설정 파일                | hdtn.json             |
| bpsec-config-file            | BPSec 설정 파일               | ""                    |
| hdtn-distributed-config-file | HDTN 분산 모드 설정 파일      | hdtn_distributed.json |
| masker                       | 사용할 Masker 구현            | ""                    |

---

## 12.7   Storage 
Storage의 일반적인 실행 예시는 다음과 같다:
```bash
./build/module/storage/hdtn-storage --hdtn-config-file=$hdtn_config &
```
이 명령어는 두 가지 주요 부분으로 구성된다:
- HDTN내 Storage 코드의 위치
- Storage 코드가 필요로 하는 HDTN 설정 파일
> HDTN 설정 파일에 대한 자세한 내용은 섹션 13.1을 참고

> Storage를 종료하려면, 다음 섹션의 설정 파일이 시작되기 전에 초기화 시간을 위해 **3초 대기 명령어(sleep 3)**를 삽입하는 것이 권장

### 표 12-6 Storage 실행 파라미터
| 매개변수(Parameter)          | 설명(Description)        | 기본값(Default Value) |
|------------------------------|--------------------------|-----------------------|
| hdtn-config-file             | HDTN 설정 파일           | hdtn.json             |
| hdtn-distributed-config-file | HDTN 분산 모드 설정 파일 | hdtn_distributed.json |

---

## 12.8   HDTN One Process 
hdtn-one-process의 일반적인 실행 예시는 다음과 같다:
```bash
./build/module/hdtn one process/hdtn-one-process --hdtn-config-
file=$hdtn_config --contact-plan-file=contactPlan.json --bpsec-config- file=$bpsec_config &
```
이 명령어는 세가지 주요 부분으로 구성된다:
- HDTN 내 hdtn-one-process 코드의 위치
- hdtn-one-process 코드가 필요로 하는 HDTN 설정 파일
- Contact Plan 파일
> HDTN 설정 파일에 대한 자세한 내용은 섹션 13.1을 참고

> bpsec-config-file은 선택적 매개변수로, BPSec이 활성화되고 이 HDTN 노드가 보안 소스(security source), 검증자(verifier), 또는 수락자(acceptor)인 경우에만 필요하다.
이 옵션은 정책 규칙과 보안 작업 실패 이벤트 처리가 포함된 BPSec 설정 파일의 위치를 지정한다.
### 표 12-7 HDTN One Process 실행 파라미터
| 매개변수(Parameter)          | 설명(Description)                                       | 기본값(Default Value)    |
|------------------------------|--------------------------------------------------------|--------------------------|
| hdtn-config-file             | HDTN 설정 파일                                         | hdtn.json                |
| bpsec-config-file            | BPSec 설정 파일                                        | ""                       |
| contact-plan-file            | 라우터가 링크 가용성을 위해 사용하는 Contact Plan 파일 | DEFAULT_CONTACT_FILE     |
| use-unix-timestamp           | Contact Plan에서 Unix 타임스탬프 사용 여부             | NA                       |
| use-mgr                      | Multigraph Routing Algorithm 사용 여부                 | NA                       |
| masker                       | 사용할 Masker 구현체                                   | ""                       |

---

## 12.9   BpGen 
12.9 BpGen
BpGen의 일반적인 실행 예시는 다음과 같다:
```bash
./build/common/bpcodec/apps/bpgen-async --bundle-rate=100 --my-uri-eid=ipn:1.1 --dest-uri-eid=ipn:2.1 --duration=40 --outducts-config-file=$gen_config &
```
이 명령어는 여섯가지 주요 부분으로 구성된다
- HDTN 내 BpGen 애플리케이션 코드의 위치
- 번들 전송 속도 (초당 번들 수)
- 소스 엔드포인트 ID
- 목적지 엔드포인트 ID
- 실행 지속 시간 (초 단위)
- BpGen 코드가 필요로 하는 설정 파일 (gen configuration file)

예제에서는 번들 전송속도가 초당 100번들로 설정되었으며, BPGen의 엔드포인트 ID는 첫번째 노드(ipn1.1)이고, 두 번째 노드(ipn:2.1)로 번들을 전송한다. 이목적지 엔드 포인트 ID는 BpSink의 엔드포인트 ID와 일치한다. 
Duration 값은 초단위로 설정되며, 이경우 40초 동안 실행된다.
> 1번 노드(ipn:1.1) → 2번 노드(ipn:2.1), 속도 100 bundles/sec, 지속 40초

> BPSec이 활성화되고 BpGen 노드가 보안 소스(security source)인 경우, --bpsec-config-file=$bpsec_config 옵션을 추가하여 정책 규칙과 보안 작업 실패 이벤트 처리가 포함된 BPSec 설정 파일의 위치를 지정해야 한다

### 표 12-8: BpGen 실행 파라미터

| 파라미터(Parameter)                  | 설명(Description)                                      | 기본값(Default Value) |
|--------------------------------------|-------------------------------------------------------|-----------------------|
| bundle-size                          | 번들 크기(bytes)                                      | 100                   |
| bundle-rate                          | 번들 생성 속도 (0 → 최대한 빠르게)                    | 1500                  |
| duration                             | 번들 송신 시간 (0 → 무한)                             | 0                     |
| my-uri-eid                           | 소스 노드 EID                                         | ipn:1.1               |
| dest-uri-eid                         | 목적지 노드 EID                                       | ipn:2.1               |
| my-custodian-service-id              | Custodian 서비스 ID (항상 0)                          | 0                     |
| outducts-config-file                 | Outducts 설정 파일                                    | ""                    |
| custody-transfer-inducts-config-file | Custody Transfer용 Inducts 설정 파일                  | ""                    |
| bpsec-config-file                    | BPSec 설정 파일                                       | ""                    |
| custody-transfer-use-acs             | Aggregate Custody Signals 사용                        | NA                    |
| force-disable-custody                | Custody Transfer 강제 비활성화                        | NA                    |
| use-bp-version-7                     | BPv7 사용                                             | NA                    |
| bundle-send-timeout-seconds          | 번들 송신/ACK 대기 최대 시간                          | 3                     |
| bundle-lifetime-milliseconds         | 번들 수명 (ms)                                        | 1000000               |
| bundle-priority                      | 번들 우선순위 (0=Bulk, 1=Normal, 2=Expedited)         | 2                     |
| cla-rate                             | CLA 전송 속도 (0=무제한)                              | 0                     |

---

## 12.10 BpSendFile
BpSendFile의 일반적인 실행 예시는 다음과 같다(송신 노드):
```bash
./build/common/bpcodec/apps/bpsend-file --max-bundle-size-bytes=500000 --file-or-folder-path=./flightdata --my-uri-eid=ipn:1.1 --dest-uri-eid=ipn:2.1 --outducts-config-file=$gen_config --bpsec-config-file=$bpsec_config --upload-new-files &
```
이 명령어는 다섯 가지 주요 부분으로 구성된다: 
- 최대 번들 크기 (바이트 단위)
- 전송할 파일 또는 폴더의 경로
- 현재 노드의 엔드포인트 ID
- 목적지 노드의 엔드포인트 ID
- Outducts 설정 파일의 위치
> $gen_config는 경로 변수와 연결된다.
BpSendFile 설정 파일에 대한 자세한 내용은 섹션 13.3을 참조.

bpsec-config-file은 선택적 매개변수로, BPSec이 활성화되고 BpSendFile 노드가 보안 소스(security source)인 경우에만 필요하다. 이 옵션은 정책 규칙과 보안 작업 실패 이벤트 처리가 포함된 BPSec 설정 파일의 위치를 지정한다.

upload-new-files는 선택적 매개변수로, 전송 중인 폴더에 새 파일이 추가될 경우 이를 계속 전송하는 데 사용된다.

> BpSendFile을 종료하려면, 다음 섹션의 설정 파일이 시작되기 전에 초기화 시간을 위해 **8초 대기 명령어(sleep 8)**를 삽입하는 것이 권장된다.

### 표 12-9: BpSendFile 실행 파라미터

| 매개변수(Parameter)                  | 설명(Description)                                                         | 기본값(Default Value) |
|--------------------------------------|---------------------------------------------------------------------------|-----------------------|
| max-bundle-size-bytes                | 파일 조각의 최대 크기 (기본값 4MB).                                       | 4000000               |
| file-or-folder-path                  | 파일 또는 폴더 경로. 폴더는 재귀적으로 처리됨.                            | ""                    |
| my-uri-eid                           | BpSendFile 소스 노드 ID.                                                  | ipn:1.1               |
| dest-uri-eid                         | BpSendFile이 번들을 전송할 최종 목적지 ID.                                | ipn:2.1               |
| my-custodian-service-id              | Custodian 서비스 ID. 항상 0으로 설정.                                     | 0                     |
| outducts-config-file                 | Outducts 설정 파일.                                                       | ""                    |
| custody-transfer-inducts-config-file | Custody 전송을 위한 Inducts 설정 파일 (custody가 있는 경우 사용).          | ""                    |
| bpsec-config-file                    | BPSec 설정 파일.                                                          | ""                    |
| skip-upload-existing-files           | 디렉터리에 이미 존재하는 파일을 업로드하지 않음.                          | NA                    |
| upload-new-files                     | 디렉터리에 새로 추가된 파일을 업로드.                                     | NA                    |
| recurse-directories-depth            | 디렉터리 내 하위 디렉터리의 최대 깊이까지 파일 업로드 (0은 재귀 없음).     | 3                     |
| custody-transfer-use-acs             | Custody 전송 시 RFC5050 대신 Aggregate Custody Signals 사용.               | NA                    |
| force-disable-custody                | 링크 양방향성에 관계없이 Custody 전송 비활성화.                           | NA                    |
| use-bp-version-7                     | 번들 프로토콜 버전 7을 사용하여 번들 전송.                                | NA                    |
| bundle-send-timeout-seconds          | 번들을 전송하고 확인 응답을 받을 최대 시간 (초 단위).                      | 3                     |
| bundle-lifetime-milliseconds         | 번들의 수명 (밀리초 단위).                                                | 1000000               |
| bundle-priority                      | 번들 우선순위. 0 = Bulk, 1 = Normal, 2 = Expedited.                       | 2                     |
| cla-rate                             | Convergence Layer Adapter 전송 속도. 0은 제한 없음.                       | 0                     |


---

## 12.11 Bping 
Bping의 일반적인 실행 예시는 다음과 같다:
```bash
A typical instantiation of Bping is: 
./build/common/bpcodec/apps/bping --my-uri-eid=ipn:1.1 --dest-uri- eid=ipn:2.2047 --outducts-config-file=$ping_config &
```
이 명령어는 네 가지 주요 부분으로 구성된다.
- HDTN 내 Bping 애플리케이션 코드의 위치
- 소스 엔드포인트 ID
- 목적지 엔드포인트 ID
- Bping 코드가 필요로 하는 설정 파일 (BpGen 설정 파일과 동일)
예제에서는 노드 1(ipn:1.1)이 노드 2(ipn:2.2047)로 핑 번들을 전송한다. 여기서 2047은 에코 서비스 번호를 나타낸다.
> 노드 1(ipn:1.1) → 노드 2 (에코 서비스 번호 2047)

### 표 12-10: Bping 실행 파라미터
| 매개변수(Parameter)                  | 설명(Description)                                         | 기본값(Default Value) |
|--------------------------------------|----------------------------------------------------------|-----------------------|
| bundle-rate                          | 초당 번들 전송 속도 (0은 가능한 한 빠르게 전송).          | 1                     |
| duration                             | 번들을 전송할 지속 시간 (0은 무한대).                     | 5                     |
| my-uri-eid                           | Bping 소스 노드 ID.                                      | ipn:1.1               |
| dest-uri-eid                         | Bping이 번들을 전송할 최종 목적지 ID.                     | ipn:2.1               |
| my-custodian-service-id              | Custodian 서비스 ID. 항상 0으로 설정.                    | 0                     |
| outducts-config-file                 | Outducts 설정 파일.                                      | ""                    |
| custody-transfer-inducts-config-file | Custody 전송을 위한 Inducts 설정 파일 (custody가 있는 경우 사용). | ""            |
| custody-transfer-use-acs             | Custody 전송 시 RFC5050 대신 Aggregate Custody Signals 사용. | NA                 |
| use-bp-version-7                     | 번들 프로토콜 버전 7을 사용하여 번들 전송.                | NA                    |
| bundle-send-timeout-seconds          | 번들을 전송하고 확인 응답을 받을 최대 시간 (초 단위).      | 3                     |
| bundle-lifetime-milliseconds         | 번들의 수명 (밀리초 단위).                                | 1000000               |
| bundle-priority                      | 번들 우선순위 (0=Bulk, 1=Normal, 2=Expedited).            | 2                     |

---

## 12.12 BpSendPacket 
BpSendPacket의 일반적인 살행 예시는 다음과 같다:
```bash
A typical instantiation of BpSendPacket is: 
./build/common/bpcodec/apps/bpsendpacket --use-bp-version-7 --my-uri- eid=ipn:1.1 --dest-uri-eid=ipn:2.1 --outducts-config-file=$gen config --packet-inducts-config-file=$send_config &
```
이 명령어는 다른 애플리케이션과 동일한 매개변수를 사용하지만, packet-inducts-config-file 이라는 새로운 매개변수가 추가되었다. 이 매개 변수는 로컬 UDP 또는 STCP 클라이언트로부터 데이터 페이로드를 수신하는데 사용되는 Induct 설정 파일을 참조한다.
### 표 12-11: BpSendPacket 실행 파라미터

| 매개변수(Parameter)                  | 설명(Description)                                               | 기본값(Default Value) |
|--------------------------------------|----------------------------------------------------------------|-----------------------|
| max-bundle-size-bytes                | 페이로드 블록 내 데이터의 최대 크기 (기본값 4MB).               | 4000000               |
| my-uri-eid                           | BpSendPacket 소스 노드 ID.                                     | ipn:1.1               |
| dest-uri-eid                         | BpSendPacket이 번들을 전송할 최종 목적지 ID.                   | ipn:2.1               |
| my-custodian-service-id              | Custodian 서비스 ID. 항상 0으로 설정.                          | 0                     |
| outducts-config-file                 | Outducts 설정 파일.                                            | ""                    |
| bpsec-config-file                    | BPSec 설정 파일.                                               | ""                    |
| custody-transfer-inducts-config-file | Custody 전송을 위한 Inducts 설정 파일 (custody가 있는 경우 사용). | ""                    |
| packet-inducts-config-file           | 패킷 수신을 위한 Inducts 설정 파일.                            | ""                    |
| custody-transfer-use-acs             | Custody 전송 시 RFC5050 대신 Aggregate Custody Signals 사용.    | NA                    |
| force-disable-custody                | Custody 전송 강제 비활성화.                                    | NA                    |
| use-bp-version-7                     | 번들 프로토콜 버전 7을 사용하여 번들 전송.                     | NA                    |
| bundle-send-timeout-seconds          | 번들을 전송하고 확인 응답을 받을 최대 시간 (초 단위).           | 3                     |
| bundle-lifetime-milliseconds         | 번들의 수명 (밀리초 단위).                                     | 1000000               |
| bundle-priority                      | 번들 우선순위 (0=Bulk, 1=Normal, 2=Expedited).                 | 2                     |

---

## 12.13 BpReceivePacket 

BpReceivePacket의 일반적인 실행 예시는 다음과 같다: 

```bash
./build/common/bpcodec/apps/bpreceivepacket --my-uri-eid=ipn:2.1 --inducts-config-file=$sink_config --packet-outducts-config-file=$receive_config &
```

packet-outducts-config-file 매개변수는 새로운 항목으로, 로컬 애플리케이션이 UDP 또는 STCP 소켓에서 데이터를 수신할 수 있도록 Outduct 설정 파일을 참조합니다.

### 표 12-12 BpReceivePacket 실행 파라미터

| 매개변수(Parameter)                  | 설명(Description)                                              | 기본값(Default Value) |
|--------------------------------------|---------------------------------------------------------------|-----------------------|
| inducts-config-file                   | Inducts 설정 파일.                                            | ""                    |
| my-uri-eid                            | BpReceivePacket 엔드포인트 ID.                                | ipn:2.1               |
| custody-transfer-outducts-config-file | Custody 전송을 위한 Outducts 설정 파일 (custody가 있는 경우 사용). | ""                |
| packet-outducts-config-file           | 패킷 전달을 위한 Outducts 설정 파일.                          | ""                    |
| acs-aware-bundle-agent                | Custody 전송 시 Aggregate Custody Signals 지원.                | NA                    |
| bpsec-config-file                     | BPSec 설정 파일.                                              | ""                    |
| max-rx-bundle-size-bytes              | Convergence Layer에 따라 수신 가능한 번들의 최대 크기 (기본값=10MB). | 10000000          |

---

## 12.14 CleanUp 

HDTN이 일정시간 동안만 실행 된 후 종료되어야 하는 경우, 모든 섹션(경로변수 제외)의 인스턴스화 명령뒤에 다음의 형식으로 줄을 추가한다.

```bash
<SectionName>_PID=$!
```

예를 들어, BpGen의 인스턴스화 뒤에 다음과 같은 정리(cleanup) 명령을 추가한다.

```bash
bpgen_PID=$!
```

정리(Clean Up) 섹션에서는 HDTN이 실행되도록 대기한후, 설정 파일 섹션의 맨 아래에서 위로 각 프로세스를 종료한다. 각 종료 명령 사이에는 최소 2초의 대기 시간이 포함된다.

스크립트 예시 : 

```bash
sleep 30 
echo "\nkilling bpgen..." && kill -2 $bpgen_PID 
sleep 2 
echo "\nkilling HDTN storage..." && kill -2 $storage_PID 
sleep 2 
echo "\nkilling HDTN ingress..." && kill -2 $ingress_PID 
sleep 2 
echo "\nkilling router..." && kill -9 $router_PID 
sleep 2 
echo "\nkilling egress..." && kill -2 $egress_PID 
sleep 2 
echo "\nkilling bpsink..." && kill -2 $bpsink_PID 
```

> 이 예시는 HDTN을 30초 동안 실행한 후 종료 한다.

**추가 옵션** :

1. `--bpsec-config-file=$bpsec_config`: BPSec이 활성화되고 이 HDTN 노드가 보안 소스, 수락자, 또는 검증자인 경우, 이 옵션을 추가하여 BPSec 설정 파일의 위치를 지정한다.
2. `use-unix-timestamp`: Unix 타임스탬프를 사용하는 Contact Plan을 지정한다.
3. `use-mgr`: 기본 CGR Dijkstra 라우팅 알고리즘 대신 Multigraph Routing Algorithm을 사용한다.

> **참고**: hdtn-one-process를 종료하려면, 다음 섹션이 시작되기 전에 초기화 시간을 위해 **10초 대기 명령어(sleep 10)**를 삽입하는 것이 권장된다.

> **참고**: hdtn-one-process를 사용할 경우, 실행 스크립트에서 Egress, Storage, Ingress, Router를 별도로 실행할 필요가 없다.

---

# 13. CONFIG FILES (설정 파일)
## 13.1 hdtn_config

HDTN 기본 설정 파일 예시는 아래와 같으며, 모든 값은 기본값이며 수정 가능하다. 각 항목 설명은 다음과 같다.

**설정 파일에 대한 사용자 정의 설명**
```json
"hdtnConfigName": "my hdtn config"
```

**컴파일 시 인터페이스가 표시 될지 여부 결정**
```json
"userInterfaceOn": true
```

**DTN 스킴 이름(현재 미사용)**
```json
"mySchemeName": "unused scheme name"
```

**실행중인 노드의 ID(정수)**
```json
"myNodeId": 10
```

**사용자가 HDTN에 핑을 보내고자 할 때 사용할 서비스 번호(정수)**
```json
"myBpEchoServiceId": 2047
```

**Custodial 스킴의 특정 부분(현재 미사용, 정의 필요)**
```json
"myCustodialSsp": "unused custodial ssp"
```

**Custody 보고가 HDTN으로 전송되는 서비스 ID(정수)**
```json
"myCustodialServiceId": 0
```

**Aggregate Custody Signals(ACS)를 사용할지 여부를 지정함(Boolean)**
```json
"isAcsAware": true
```

**하나의 ACS 패킷에 포함될 custody 신호의 수(정수)**
```json
"acsMaxFillsPerAcsPacket": 100
```

**ACS 집계 주기(ms 단위, 정수)**
```json
"acsSendPeriodMilliseconds": 1000
```

**custody 신호 미수신 시 번들 재전송까지의 시간(ms, 정수)**
```json
"retransmitBundleAfterNoCustodySignalMilliseconds": 10000
```

**HDTN이 송수신할 수 있는 번들의 최대 크기(Byte 단위)**
> 참고: 번들이 이 크기를 초과하면 드롭됨.
```json
"maxBundleSizeBytes": 10000000
```

**링크 포화 상태에서 저장소로 버퍼링할지 여부(Boolean)**
```json
"bufferRxToStorageOnLinkUpSaturation": false
```

**Cut-through 모드에서 egress가 지정된 시간(ms) 내 번들을 처리하지 못하면 저장소로 전달함**
```json
"maxIngressBundleWaitOnEgressMilliseconds": 2000
```

**HDTN이 수신할 수 있는 최대 패킷 크기(Byte 단위)**
네트워크에서 프로토콜이 처리할 최대 데이터그램 크기로 설정.
65536은 일반적으로 로컬 UDP 패킷이 지원할 수 있는 최대 크기임.
> 참고: LTP를 사용하지 않을 경우 무관한 값임.
```json
"maxLtpReceiveUdpPacketSizeBytes": 65536
```

**고갈된 이웃 노드 우회 설정**
0이 아닌 경우, 저장소가 고갈된 이웃 노드를 우회하도록 재라우팅함. 지정된 시간(초) 후 고갈된 이웃 노드로 번들을 전송 시도.
> 참고: 0으로 설정하면 비활성화됨.
```json
"neighborDepletedStorageDelaySeconds": 0
```

**번들 우선순위 강제 적용**
true로 설정 시, 전달되는 번들에 대해 엄격한 우선순위 정렬을 활성화함. 낮은 우선순위 번들은 저장소에 있는 높은 우선순위 번들보다 먼저 전송될 수 없음.
> 참고: ingress-to-egress cutthrough 큐를 비활성화하여 성능이 저하될 수 있음.
```json
"enforceBundlePriority": false
```

**번들 조각화 설정**
0이 아닌 경우, HDTN은 지정된 크기보다 큰 페이로드를 가진 BPv6 번들을 조각화하려고 시도함.
> 참고: BPv7 번들이나 NOFRAGMENT 플래그가 설정된 번들은 조각화되지 않음.
```json
"fragmentBundlesLargerThanBytes": 0
```

**라우터 ZMQ pub-sub 소켓의 ZMQ 바운드 포트(정수)**
```json
"zmqBoundRouterPubSubPortPath": 10200
```

**API 소켓의 ZMQ 바운드 포트(정수)**
```json
"zmqBoundTelemApiPortPath": 10305
```

### induct 설정

- inductConfigName 및 name은 사용자 주석용.
- convergence layer에서 선택 가능: stcp, tcpcl v3, tcpcl v4, udp, ltp over udp.
- boundPort 및 numRxCircularBufferElements는 반드시 정수여야 함.

> **참고**: numRxCircularBufferElements는 각 convergence layer에 따라 의미가 다름:
> - STCP: 버퍼링할 번들의 수.
> - TCPCL: 번들 또는 데이터 조각의 수.
> - LTP: UDP 패킷의 수.
> - UDP: 패킷/번들의 수.

```json
"inductsConfig": {
  "inductConfigName": "myconfig",
  "inductVector": [
    {
      "name": "stcp ingress",
      "convergenceLayer": "stcp",
      "boundPort": 4556,
      "numRxCircularBufferElements": 200
    }
  ] 
}
```

### outduct 설정

- outductConfigName 및 name은 사용자 주석용.
- convergence layer에서 선택 가능: stcp, tcpcl v3, tcpcl v4, udp, ltp over udp.
- remoteHostname: HDTN이 번들을 전송할 대상의 IP 또는 호스트명.
- remotePort: HDTN이 번들을 전송할 포트.
- 최종 목적지 ID는 하나 또는 여러 개의 IPN URI일 수 있음. IPN 서비스 번호는 *로 와일드카드 지정 가능.
- nextHopNodeID, remotePort, maxNumberOfBundlesInPipeline, maxSumOfBundleBytesInPipeline은 반드시 정수여야 함.

```json
"outductsConfig": {
  "outductConfigName": "myconfig",
  "outductVector": [
    {
      "name": "stcp egress",
      "convergenceLayer": "stcp",
      "nextHopNodeId": 2,
      "remoteHostname": "localhost",
      "remotePort": 4558,
      "maxNumberOfBundlesInPipeline": 50,
      "maxSumOfBundleBytesInPipeline": 50000000
    }
  ]
}
```

### storage 설정

- storageImplementation: asio single threaded(기본값) 또는 stdio multi threaded 중 선택 가능.
- tryToRestoreFromDisk: 재시작 시 저장된 번들을 복구할지 여부.
- autoDeleteFilesOnExit: 정상 종료 시 번들을 삭제할지 여부.
- totalStorageCapacityBytes: 저장소 총 용량(Byte 단위).
- storageDeletionPolicy: 번들 삭제 정책
  - never: 삭제하지 않음.
  - on expiration: 만료 시 삭제.
  - on storage full: 저장소가 90% 초과 시 삭제.
- storageDiskConfigVector: 디스크 분할 저장 
  - name: 디스크 이름.
  - storeFilePath: 디스크 파일 경로.

> RAID 0과 유사한 스트라이핑 방식의 저장소 구성입니다. 단, RAID 자체를 사용하지는 않습니다.

> 저장소 벡터 크기(즉, storeFilePath의 개수)는 무제한으로 설정할 수 있습니다.

```json
"storageConfig": {
  "storageImplementation": "asio single threaded",
  "tryToRestoreFromDisk": false,
  "autoDeleteFilesOnExit": true,
  "totalStorageCapacityBytes": 8192000000,
  "storageDeletionPolicy": "never",
  "storageDiskConfigVector": [
    { "name": "d1", "storeFilePath": "./store1.bin" },
    { "name": "d2", "storeFilePath": "./store2.bin" }
  ]
}
```

> **참고**: convergence layer에 따라 inductVector / outductVector 항목이 추가될 수 있음 (15.0 참조).
> Ingress, Egress, Storage는 필요 시 선택적으로 포함 가능.

  - storageImplementation: asio single threaded(기본값) 또는 stdio multi threaded 중 선택 가능.
  - tryToRestoreFromDisk: 재시작 시 저장된 번들을 복구할지 여부.
  - autoDeleteFilesOnExit: 정상 종료 시 번들을 삭제할지 여부.
  - totalStorageCapacityBytes: 저장소 총 용량(Byte 단위).
  - storageDeletionPolicy: 번들 삭제 정책
    - never: 삭제하지 않음.
    - on expiration: 만료 시 삭제.
    - on storage full: 저장소가 90% 초과 시 삭제.
  - storageDiskConfigVector: 디스크 분할 저장 
    - name: 디스크 이름.
    - storeFilePath: 디스크 파일 경로.
    > RAID 0과 유사한 스트라이핑 방식의 저장소 구성입니다. 단, RAID 자체를 사용하지는 않는다.습니다.

    > 저장소 벡터 크기(즉, storeFilePath의 개수)는 무제한으로 설정할 수 있다.

  ```json
  "storageConfig": {
    "storageImplementation": "asio single threaded",
    "tryToRestoreFromDisk": false,
    "autoDeleteFilesOnExit": true,
    "totalStorageCapacityBytes": 8192000000,
    "storageDeletionPolicy": "never",
    "storageDiskConfigVector": [
      { "name": "d1", "storeFilePath": "./store1.bin" },
      { "name": "d2", "storeFilePath": "./store2.bin" }
    ]
  }
  ```
  > 참고: convergence layer에 따라 inductVector / outductVector 항목이 추가될 수 있음 (15.0 참조).
  > Ingress, Egress, Storage는 필요 시 선택적으로 포함 가능.

---

## 13.2 sink_config

일반적인 sink 설정 파일은 inductConfigName과 inductVector를 포함한다.  
inductVector에는 이름(name), 컨버전스 레이어(convergence layer), 바인딩된 포트 번호(bound port number), 수신 원형 버퍼 요소 개수(numRxCircularBufferElements)가 필요하다.

이 섹션의 세부사항은 13.1절을 참조해야 한다.  
왜냐하면 bp sink 설정 파일은 hdtn 설정 파일의 inductConfigName / inductVector 부분을 그대로 복사한 것이기 때문이다.  
이는 bp sink 설정 파일이 11.0절에서 설명된 BPSink 애플리케이션에 사용되기 때문이다.

> 컨버전스 레이어에 따라 inductVector에 추가적인 항목이 포함될 수 있다.  
> 이에 대해서는 15.0절을 참고하라.

### 구조 예시:

```json
{
  "inductsConfig": {
    "inductConfigName": "myconfig",
    "inductVector": [
      {
        "name": "stcp ingress",
        "convergenceLayer": "stcp",
        "boundPort": 4556,
        "numRxCircularBufferElements": 200
      }
    ]
  }
}
```

## 13.3 gen_config

일반적인 gen 설정 파일은 outductConfigName과 outductVector를 포함한다.
outductVector에는 이름(name), 컨버전스 레이어(convergence layer), 다음 홉(next hop), 원격 호스트명(remote hostname), 원격 포트(remote port), 번들 파이프라인 제한(bundle pipeline limit), 최종 목적지 엔드포인트 ID(final destination endpoint ID)가 필요하다.

이 섹션의 세부사항도 13.1절을 참조해야 한다. 왜냐하면 gen 설정 파일은 hdtn 설정 파일의 outductConfigName / outductVector 부분을 그대로 복사한 것이기 때문이다. 이는 gen 설정 파일이 11.0절에서 설명된 BPGen 애플리케이션에 사용되기 때문이다.

> 컨버전스 레이어에 따라 outductVector에 추가적인 항목이 포함될 수 있다. 이에 대해서는 15.0절을 참고하라.

### 구조 예시:

```json
{
  "outductConfigName": "myconfig",
  "outductVector": [
    {
      "name": "bpgen",
      "convergenceLayer": "tcpcl v3",
      "nextHopNodeId": 10,
      "remoteHostname": "localhost",
      "remotePort": 4556,
      "maxNumberOfBundlesInPipeline": 5,
      "maxSumOfBundleBytesInPipeline": 50000000
    }
  ]
}
```

## 13.4 bpsec_config

일반적인 BPSec 설정 파일은 bpSecConfigName(사용자 정의 설정 파일 설명), policyRules(보안 정책 규칙들을 담은 벡터), securityFailureEventSets(보안 작업 실패 이벤트를 처리하는 벡터)를 포함한다.

### 전체 구조 예시:

```json
{
  "bpsecConfigName": "my BPSec Config",
  "policyRules": [
    {
      "description": "Bpsource confidentiality",
      "securityPolicyRuleId": 1,
      "securityRole": "source",
      "securitySource": "ipn:1.1",
      "bundleSource": ["ipn:1.1"],
      "bundleFinalDestination": ["ipn:2.1"],
      "securityTargetBlockTypes": [1],
      "securityService": "confidentiality",
      "securityContext": "aesGcm",
      "securityFailureEventSetReference": "default_confidentiality",
      "securityContextParams": [
        { "paramName": "aesVariant", "value": 256 },
        { "paramName": "ivSizeBytes", "value": 12 },
        { "paramName": "keyFile", "value": "config_files/bpsec/ipn1.1_confidentiality.key" },
        { "paramName": "securityBlockCrc", "value": 0 },
        { "paramName": "scopeFlags", "value": 7 }
      ]
    }
  ],
  "securityFailureEventSets": [
    {
      "name": "default_confidentiality",
      "description": "default bcb confidentiality security operations event set",
      "securityOperationEvents": [
        {
          "eventId": "sopCorruptedAtAcceptor",
          "actions": ["removeSecurityOperationTargetBlock"]
        }
      ]
    }
  ]
}
```

### policyRules 벡터 상세 설명

```json
"description": "Bpsource confidentiality"
```
 - bpsec 정책 규칙 파일에 대한 사용자 설명
 
```json
"securityPolicyRuleId": 1
```
  - 정책 규칙 ID

```json
"securityRole": "source"
```
  - 보안 역할 (선택 가능: "source", "verifier", "acceptor")

```json
"securitySource": "ipn:1.1"
```
  - 보안 소스 Endpoint ID

```json
"bundleSource": ["ipn:1.1"]
```
  - 번들 소스 Endpoint ID 벡터 (와일드카드 사용 가능)


```json
"bundleFinalDestination": ["ipn:2.1"]
```
  - 번들 최종 목적지 Endpoint ID 벡터 (와일드카드 사용 가능)


```json
"securityTargetBlockTypes": [1]
```
  - 보안 대상 블록 유형 벡터 (예: 1 = Payload block)


```json
"securityService": "confidentiality"
```
  - 보안 서비스 (선택 가능: "confidentiality", "integrity")


```json
"securityContext": "aesGcm"
```
  - 보안 컨텍스트 (기밀성 = "aesGcm", 무결성 = "hmacSha")

```json
"securityContextParams": [ 
  { 
    "paramName": "aesVariant", 
    "value": 256 
  }
]
```
  - 보안 컨텍스트 파라미터와 해당 값들의 벡터:
    - aesVariant: 128, 256 가능
    - shaVariant: 256, 384, 512 가능
    - ivSizeBytes: 12 또는 16
    - keyFile: 키 파일 경로
    - keyEncryptionKeyFile: 키 암호화 키 파일 경로
    - securityBlockCrc: CRC 값(블록 손상 감지용)
    - scopeFlags: 블록 유형별 데이터 외 추가 데이터 포함 여부

```json
"securityFailureEventSetReference": "default_confidentiality",
```
  - 사용될 보안 실패 이벤트 세트의 이름 참조
  - 보안 실패 이벤트 세트 벡터의 각항목에 대한 추가 정보
    ```json
    "name": "default_confidentiality",
    ```
    - 보안  실패 이벤트 세트의 이름 또는 참조 
    
    ```json
    "name": "Description",
    ```
    - 이벤트 세트에 대한 사용자 설명
    ```json
    "securityOperationEvents": [
      {
        "eventId": "sopCorruptedAtAcceptor", 
        "actions": [ "removeSecurityOperationTargetBlock" ] 
      }
    ] 
    ``` 
    - 이벤트 세트(ID, 액션)
  
```json
"name": "securityOperationEvents",
```
- 이벤트 ID와 대응되는 액션 목록
- 자원되는 이벤트 ID:
  - `"sopMisconfiguredAtVerifier"`, `"sopMissingAtVerifier"`: 
    - HDTN이 처리하도록 설정된 Endpoint Identifier(EID)를 포함하지만, 예상과 다른 보안 작업이 적용된 번들이 수신될 때 발생.
    - 예시: 번들이 확장 블록에 기밀성(confidentiality)을 적용한 상태로 도착했지만, HDTN은 페이로드 블록에 기밀성을 적용하도록 설정되어 있음.
  - `"sopCorruptedAtVerifier"`, `"sopMissingAtAcceptor"`: 
    - 예상된 보안 작업이 없는 번들이 수신될 때 발생.
    - 예시: HDTN이 기밀성을 처리하도록 설정되어 있지만, Block Confidentiality Block(BCB)이 없는 번들이 수신됨.
  - `"sopCorruptedAtAcceptor"`: 
    - 보안 작업의 내용이나 대상 블록의 보호된 내용이 수정되었거나, 잘못된 키를 사용하여 작업을 처리하려고 할 때 발생.
    - 예시: 번들이 페이로드 블록에 무결성(integrity)을 적용한 상태로 도착했지만, 전송 오류로 인해 해당 블록의 데이터 필드 내용이 변경되어 검증할 수 없는 상태가 됨.

- 지원되는 작업(Actions)
  - `"removeSecurityOperation"`: 
    - 영향을 받은 BIB(Block Integrity Block) 또는 BCB(Block Confidentiality Block)를 삭제하지만, 대상 블록(target block)은 삭제하지 않음.
  - `"removeSecurityOperationTargetBlock"`: 
    - 영향을 받은 대상 블록(target block)을 삭제.
    > 참고: 대상 블록이 페이로드 블록인 경우, 번들은 페이로드 블록을 반드시 포함해야 하므로 번들 전체가 삭제됨.
  - `"removeAllSecurityTargetOperations"` :
    - 현재 효과 없음
  - `"failBundleForwarding"`:
    - 번들 전체 폐기
  - `"requestBundleStorage"`: 
    - 번들을 저장소로 이동
  - `"reportReason-Code"`
    - 현재 효과 없음
  - `"overrideSecurityTargetBlockBpcf"`
    - 현재 효과 없음
  - `"overrideSecurityBlockBpcf"`
    - 현재 효과 없음

## 13.5 distributed_config

HDTN을 분산(distributed) 모드로 실행하려면, `--hdtn-distributed-config-file`이라는 명령줄 인자를 추가해야 한다.
이 사용 예시는 `runscript_distributed.sh`와 `hdtn_distributed_defaults.json`에서 확인할 수 있다.

```json
"zmqIngressAddress": "localhost"
```
- HDTN의 Ingress 모듈이 실행되는 머신의 IP 또는 호스트명

```json
"zmqEgressAddress": "localhost"
```
- HDTN의 Egress 모듈이 실행되는 머신의 IP 또는 호스트명

```json
"zmqStorageAddress": "localhost"
```
- HDTN의 Storage 모듈이 실행되는 머신의 IP 또는 호스트명

```json
"zmqRouterAddress": "localhost"
```
- HDTN의 Router 모듈이 실행되는 머신의 IP 또는 호스트명

```json
"zmqBoundIngressToConnectingEgressPortPath": 10100
```
- Ingress 모듈이 Egress로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향(unidirectional)
- 정수여야 함

```json
"zmqConnectingEgressToBoundIngressPortPath": 10160
```
- Egress 모듈이 Ingress로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqBoundEgressToConnectingRouterPortPath": 10162
```
- Egress 모듈이 Router로 링크 다운 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqConnectingEgressBundlesOnlyToBoundIngressPortPath": 10161
```
- Egress 모듈이 Ingress로 TCPCL 기회성(opportunistic) 번들을 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqBoundIngressToConnectingStoragePortPath": 10110
```
- Ingress 모듈이 Storage로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqConnectingStorageToBoundIngressPortPath": 10150
```
- Storage 모듈이 Ingress로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqConnectingStorageToBoundEgressPortPath": 10120
```
- Storage 모듈이 Egress로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqBoundEgressToConnectingStoragePortPath": 10130
```
- Egress 모듈이 Storage로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqConnectingRouterToBoundEgressPortPath": 10210
```
- Router 모듈이 Egress로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요
- ZMQ TCP는 단방향
- 정수여야 함

```json
"zmqConnectingTelemToFromBoundIngressPortPath": 10301
```
- Ingress 모듈이 Telemetry 모듈(GUI)로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트

```json
"zmqConnectingTelemToFromBoundEgressPortPath": 10302
```
- Egress 모듈이 Telemetry 모듈로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트


```json
"zmqConnectingTelemToFromBoundStoragePortPath": 10303
```
- Storage 모듈이 Telemetry 모듈로 내부 메시지를 보낼 때 사용하는 ZMQ TCP 포트
- 위 세 Telemetry 포트는 hdtn-one-process 모드에서는 사용되지 않지만 반드시 정의 필요, 
- ZMQ TCP는 단방향, 정수여야 함

---

# 14. CONTACT PLANS (연락 계획)

HDTN 노드에서 **하나의 연락(contact) 항목**은 해당 노드의 아웃덕트(outduct)가 다음 홉(목적지)과 연결 가능한 **가용 시간 슬롯**을 정의한다.  
이 항목에는 다음 홉에 접근할 수 있는 **시작/종료 시간**, 그리고 두 지점 사이의 **링크 속도와 단방향 전파 시간(OWLT: One-Way Light Time)** 정보가 포함된다.  

연락 계획(contact plan) 파일은 해당 노드의 모든 연락 정보를 나열한다.  
- 만약 동일한 다음 홉이 서로 다른 시간대 또는 조건으로 접근 가능하다면, 각 경우마다 별도의 연락 항목이 필요하다.  
- 하나의 연락 계획 파일에는 네트워크 내 여러 노드에 대한 항목이 포함될 수 있다.  
- 단, 현재 이 파일을 처리 중인 노드 번호를 포함하지 않는 항목은 무시된다.  
- 아웃덕트의 다음 홉이 연락 계획에 존재하지 않는 경우, 해당 아웃덕트는 절대로 번들을 라우팅하지 않는다. (동일 호스트의 STCP 아웃덕트 포함)  

연락 계획은 **JSON 파일** 형식이며, HDTN 번들 에이전트(예: `hdtn-one-process`) 내에서만 사용된다.  
예제 파일은 다음 위치에 있다:  
`HDTN/module/router/contact_plans/src`

---

## 14.1 JSON 필드 설명

- `"contact": 1`  
  - 연락(접속) 식별 번호  
  - 정수형(Integer)

- `"source": 10`  
  - 해당 연락에서 번들을 전송하는 **소스 노드 번호**  
  - 정수형(Integer)

- `"dest": 2`  
  - 해당 연락에서 번들을 수신하는 **목적지 노드 번호** (또는 다음 홉)  
  - 정수형(Integer)

- `"startTime": 25`  
  - 이 연락의 링크가 **UP(가용)** 상태가 되는 시작 시간 (초 단위)  
  - 기준은 라우터 프로세스 시작 시점(time 0)  
  - 단, `--use-unix-timestamp` 옵션을 사용하면 1970년 1월 1일 00:00:00 UTC (Unix epoch) 이후의 초 단위 값으로 계산  
  - 정수형(Integer)

- `"endTime": 38`  
  - 이 연락의 링크가 **DOWN(비가용)** 상태가 되는 종료 시간 (초 단위)  
  - 기준은 라우터 프로세스 시작 시점(time 0)  
  - 단, `--use-unix-timestamp` 옵션을 사용하면 Unix epoch 이후의 초 단위 값으로 계산  
  - 정수형(Integer)

- `"rateBitsPerSec": 1000`  
  - 데이터 전송 속도 제한 (bps, bits per second)  
  - HDTN이 해당 연락에서 적용할 속도 제한 (LTP 및 UDP convergence layer에만 적용됨)  
  - 이 옵션은 애플리케이션에서 사용하는 `--cla-rate` 인자와 동일하다.  
  - 라우터는 **더 높은 속도를 가진 연락**을 우선한다.  
  - 정수형(Integer)

- `"owlt": 1`  
  - 단방향 전파 지연(One Way Light Time) — 거리(범위)를 **빛의 초 단위(light-seconds)** 로 표현  
  - 라우터는 **더 짧은 OWLT를 가진 연락**을 우선한다.  
  - 정수형(Integer)

- `"__comment__": "from node1 to node2"`  
  - HDTN이 사용하지는 않지만, 연락 계획 파일의 가독성을 높이는 주석  
  - 문자열(String)

---

# 15. CONVERGENCE LAYERS AND ROUTING PROTOCOLS (수렴 계층 및 라우팅 프로토콜)

HDTN은 다음과 같은 수렴 계층(convergence layer)을 지원한다:  
- Transmission Control Protocol (TCP) Convergence Layer (TCPCL)  
- User Datagram Protocol (UDP)  
- Licklider Transmission Protocol (LTP)  
- Simple TCP (STCP)  

각 수렴 계층에는 추가적인 **“inductVector”** 및 **“outductVector”** 설정 필드가 있다.  
이 추가 필드는 `inductVector` 와 `outductVector` 를 사용하는 모든 설정 파일(`hdtn_config`, `sink_config`, `gen_config`)에 적용된다.  
아래 섹션에 각 계층별 추가 필드를 정리한다.  

---

### 15.1 TCPCLv3

**공통 Induct/Outduct 필드**
- `"keepAliveIntervalSeconds": 15`  
  - 세션 Keepalive 최소 협상 간격(초 단위)  
  - 참조: RFC7242 Section 5.6  
  - 정수형(Integer)

- `"tcpclV3MyMaxTxSegmentSizeBytes": 200000`  
  - 데이터 세그먼트 전송 시 사용할 최대 세그먼트 크기(바이트 단위)  
  - 정수형(Integer)

**Induct 전용 필드**
- `"numRxCircularBufferBytesPerElement": 100`  
  - 수신 버퍼의 각 원소에 할당할 최대 크기(바이트 단위)  
  - 정수형(Integer)

**Outduct 전용 필드**
- `"tcpclAllowOpportunisticReceiveBundle": false`  
  - 기회적(opportunistic) 번들 수신 허용 여부  
  - 불리언(Boolean)

---

### 15.2 TCPCLv4

**공통 Induct/Outduct 필드**
- `"keepAliveIntervalSeconds": 15`  
  - 세션 Keepalive 최소 협상 간격(초 단위)  
  - 정수형(Integer)

- `"tcpcl4MyMaxTxSegmentSizeBytes": 200000`  
  - 데이터 세그먼트 전송 시 사용할 최대 세그먼트 크기(바이트 단위)  
  - 정수형(Integer)

- `"tlsIsRequired": false`  
  - TLS(전송 계층 보안) 필수 여부  
  - 불리언(Boolean)

**Induct 전용 필드**
- `"numRxCircularBufferBytesPerElement": 100`  
  - 수신 버퍼의 각 원소에 할당할 최대 크기(바이트 단위)  
  - 정수형(Integer)

- `"tcpclV4MyMaxRxSegmentSizeBytes": 20000`  
  - 수신 시 허용할 최대 세그먼트 크기(바이트 단위)  
  - 정수형(Integer)

- `"certificatePemFile": "C:hdtn_ssl_certificatescert.pem"`  
  - SSL 인증서 파일 경로  

- `"privateKeyPemFile": "C:hdtn_ssl_certificatesprivatekey.pem"`  
  - SSL 개인 키 파일 경로  

- `"diffieHellmanParametersPemFile": "C:hdtn_ssl_certificatesdh4096.pem"`  
  - TLS용 Diffie-Hellman 매개변수 파일 경로  

**Outduct 전용 필드**
- `"tryUseTls": false`  
  - TLS 사용 여부  
  - 불리언(Boolean)

- `"useTlsVersion1_3": false`  
  - TLS v1.3 사용 여부 (기본은 v1.2)  
  - 불리언(Boolean)

- `"doX509CertificateVerification": false`  
  - X.509 인증서 검증 여부  
  - 불리언(Boolean)

- `"verifySubjectAltNameInX509Certificate": false`  
  - X.509 인증서의 Subject Alternative Name 검증 여부  
  - 불리언(Boolean)

- `"certificationAuthorityPemFileForVerification": "C:hdtn_ssl_certificatescert.pem"`  
  - 인증서 검증용 인증 기관(CA) 인증서 파일 경로  

---

### 15.3 UDPCL

**Induct 전용 필드**
- `"numRxCircularBufferBytesPerElement": 100`  
  - 수신 버퍼의 각 원소에 할당할 최대 크기(바이트 단위)  
  - 정수형(Integer)

---

### 15.4 LTP

**공통 Induct/Outduct 필드**
- `"clientServiceId": 1`  
  - 클라이언트 서비스 ID  
  - 정수형(Integer)

- `"ltpDataSegmentMtu": 1360`  
  - LTP 송신 Red 데이터 세그먼트의 최대 크기(바이트 단위, 헤더 제외)  
  - Ethernet MTU 초과를 피하도록 설정 필요 → IP 단편화 방지  
  - 정수형(Integer)

- `"ltpMaxRetriesPerSerialNumber": 500`  
  - 동일한 시리얼 번호를 가진 LTP 패킷 재전송 최대 횟수  
  - 정수형(Integer)

- `"ltpMaxUdpPacketsToSendPerSystemCall": 1`  
  - 시스템 호출당 전송할 UDP 패킷의 최대 개수  
  - 정수형(Integer)

- `"ltpRandomNumberSizeBits": 64`  
  - 세션 식별용 난수 크기(32 또는 64 비트)  
  - 정수형(Integer)

- `"oneWayLightTimeMs": 1000`  
  - 단방향 전파 시간(밀리초 단위)  
  - 왕복 시간(RTT) 계산: `2 * (oneWayLightTime + oneWayMarginTime)`  
  - 정수형(Integer)

- `"oneWayMarginTimeMs": 200`  
  - 단방향 마진 시간(패킷 처리 지연 포함, 밀리초 단위)  
  - RTT 계산 시 포함됨  
  - 정수형(Integer)

- `"remoteLtpEngineId": 20`  
  - 원격 LTP 엔진 ID  
  - 정수형(Integer)

- `"thisLtpEngineId": 10`  
  - 현재 LTP 엔진 ID  
  - 정수형(Integer)

- `"delaySendingOfReportSegmentsTimeMsOrZeroToDisable": 20`  
  - 리포트 세그먼트 처리 효율성을 위해 재전송 지연(ms 단위)  
  - 정수형(Integer)

- `"keepActiveSessionDataOnDisk": false`  
  - LTP 세션 데이터를 메모리 대신 SSD 디스크에 저장해 실행할지 여부  
  - 활성화 시 `activeSessionDataOnDiskNewFileDurationMs`, `activeSessionDataOnDiskDirectory` 설정 필요  
  - 현재 실험적 기능: 링크 장애 시 번들이 저장소로 이전되지 못하고 손실될 수 있음  
  - 불리언(Boolean)

**Induct 전용 필드**
- `"ltpMaxExpectedSimultaneousSessions": 500`  
  - 예상 동시 세션 수  
  - 정수형(Integer)

- `"ltpRemoteUdpHostname": "localhost"`  
  - 원격 UDP 호스트명 또는 IP  

- `"ltpRemoteUdpPort": 4556`  
  - 원격 UDP 포트  
  - 정수형(Integer)

- `"ltpRxDataSegmentSessionNumberRecreationPreventerHistorySize": 1000`  
  - 최근 세션 번호 이력 저장 개수  
  - 오래된 세션이 재개되는 문제(IP 단편화 등) 방지를 위해 사용  
  - 정수형(Integer)

- `"preallocatedRedDataBytes": 200000`  
  - Red 데이터 수신 버퍼 사전 할당 크기(바이트 단위)  
  - 너무 작으면 수신 중 재할당/복사 발생 → 성능 저하  
  - 너무 크면 메모리 과다 할당 위험  
  - 정수형(Integer)

**Outduct 전용 필드**
- `"ltpCheckpointEveryNthDataSegment": 0`  
  - 송신 시 N번째 UDP 패킷마다 체크포인트 설정 (가속 재전송)  
  - 정수형(Integer)

- `"ltpSenderBoundPort": 1113`  
  - LTP 송신자 바인드 포트  
  - 정수형(Integer)

- `"ltpSenderPingSecondsOrZeroToDisable": 15`  
  - LTP 세션 송신자 ping 주기(초 단위)  
  - 데이터 전송이 없을 때 Cancel segment를 보내 ACK로 링크 활성 여부 확인  
  - 정수형(Integer)

---

### 15.5 STCP

**공통 Induct/Outduct 필드**
- `"keepAliveIntervalSeconds": 17`  
  - 세션 Keepalive 최소 협상 간격(초 단위)  
  - 정수형(Integer)

---

# 16. TEST CONFIGURATIONS AND INSTRUCTIONS (테스트 구성 및 지침)

HDTN은 기능 확인을 위해 여러 가지 테스트 환경과 실행 스크립트를 제공한다.  
이 장에서는 기본 루프백(loopback) 테스트부터, 다중 노드 라우팅, 파일 전송, 통합 테스트까지의 절차를 설명한다.  

---

## 16.1 TCP Loopback Test (TCP 루프백 테스트)

그림 16-1 에 표시된 간단한 루프백 테스트를 실행하려면, HDTN 소스 디렉터리에서 다음 명령어를 실행한다.
```bash
./runscript.sh
```
이 테스트는 약 30초 동안 세가지 모듈을 실행한다.
- BPGen – 번들을 생성하고 Ingress 모듈로 전달한다.
- HDTN One Process – HDTN의 모듈을 단일 프로세스로 실행한다.
  - 이 테스트는 컷스루(cutthrough) 모드이므로 Storage는 사용하지 않는다.
  - Ingress, Egress, Router가 실행된다.
- BPSink – Egress 모듈로부터 번들 데이터를 수신한다.

![그림 16-1](image-6.png)

## 16.2 Two Node LTP Test (2노드 LTP 테스트)

그림 16-2에 표시된 이 테스트는 HDTN을 실행하는 두개의 노드(송신기와 수신기)를 필요로 한다.
- 송신기는 BPGen과 HDTN One Process를 실행한다.
- 수신기는 BPSink와 HDTN One Process를 실행한다.

예제 스크립트는 'HDTN/tests/test_scripts_linux/LTP_2Nodes_Test' 에서 확인 할 수 있다.

별도의 머신에 HDTN을 설치한 경우, 각각 송신기 또는 수신기로 실행할 수 있다. 이 경우 각 설정 파일의 remoteHostname 필드를 적절한 IP 주소로 업데이트 해야 한다.

스크립트에서 사용되는 설정 파일은 다음의 경로에서 확인 할 수 있다. 
- 송신기 설정: HDTN/config_files/hdtn/hdtn_Node1_ltp.json
- 수신기 설정: HDTN/config_files/hdtn/hdtn_Node2_ltp.json

> 주의: 이 테스트를 실행할 때, 반드시 수신기 스크립트를 먼저 시작한 후 송신기 스크립트를 실행해야 합니다.

![그림16-2](image-7.png)

## 16.3 Four Nodes STCP Test (4노드 STCP 라우팅 테스트)

그림 16-3에 표시된 이 테스트는 HDTN을 실행하는 네 개의 노드와 라우터 모듈을 필요라 한다.
- Node 1: BPGen과 HDTN One Process를 실행한다.
- Node 2, 3: HDTN One Process만 실행한다.
- 최종 목적지 Node 4: HDTN One Process와 BPSink를 실행한다.

![그림 16-3](image-8.png)

Node 1의 HDTN 설정 파일에서 다음 홉이 Node3으로 설정되어 있다. 그러나 라우터가 최종 목적지로 가는 최적 경로를 계산한 후, Outduct는 Node2를 다음 홉으로 선택하도록 갱신된다. 

초기화 과정에서, HDTN Json 설정파일에는 가능한 모든 다음 홉이 포함되어 있다. 동일한 최종 목적지로 가는 여러 홉이 있는 경우, 하나의 Outduct만 최종 목적지 벡터로 초기화되어야 하며, 다른 다음 홉 Outduct는 비활성 상태(dormant)로 두어야 한다. 즉, 해당 Outduct의 최종 목적지 Json 필드에는 값이 초기화 되지 않아야 한다. 

라우터는 최적 경로를 계산 한 후, Egress에 이벤트를 전송하여 Outduct가 최적 경로의 다음 홉을 사용하도록 업데이트 한다.

각 노드의 실행 스크립트는 다음 경로에서 확인할 수 있다:
 - HDTN/tests/test_scripts_linux/Routing_Test

Node 1의 설정 파일은 다음 경로에서 확인할 수 있다:
  - HDTN/config_files/hdtn/hdtn_node1_cfg.json

다른 노드의 설정 파일은 이 파일 바로 뒤에 제공됩니다.

> 별도의 머신에서 실행하는 경우, 각 설정 파일의 remoteHostname 필드를 해당 머신의 올바른 IP 주소로 업데이트해야 합니다.

> 사용자는 각 노드의 실행 스크립트를 수신기에서 송신기 순서로 실행해야 합니다. 즉, Node 4를 먼저 시작한 후, Node 3, Node 2, 마지막으로 Node 1을 실행해야 합니다.

## 16.4 File Transfer Test (파일 전송 테스트)
이 테스트는 HDTN을 실행하는 두 대의 머신(송신기와 수신기)를 필요로 한다.
- 송신기(Sender): BPSendFile 실행, HDTN One Process 실행
- 수신기(Receiver): BPReceiveFile 실행, HDTN One Process 실행

LTP와 TCPCL 수렴 계층(convergence layers)을 사용하여 파일을 전송하는 예제 스크립트가 추가되었고, HDTN/tests/test_scripts_linux/LCRD_File_Transfer_Test 디렉터리에서 확인할 수 있다.

이 예제는 다음을 지원합니다:
- BPv6 (custody transfer 있음/없음)
- BPv7 (BPSec 있음/없음)

그림 16-4는 BPSec이 활성화된 상태에서 파일 전송 테스트를 보여줍니다.
- 소스(Source): 페이로드와 관련된 평문(plaintext)을 암호화합니다.
- 목적지(Destination): 페이로드의 보안 검증 및 복호화를 수행합니다.

별도의 머신에 HDTN을 설치한 경우, 각각 송신기 또는 수신기로 실행할 수 있습니다.
이 경우, 각 HDTN 설정 파일의 remoteHostname 필드를 적절한 IP 주소로 업데이트해야 합니다.

> 이 테스트를 실행할 때, 반드시 수신기 스크립트를 먼저 시작한 후 송신기 스크립트를 실행해야 합니다.

![그림 16-4](image-9.png)

## 16.5 Integrated Tests (통합 테스트)

Boost Test Framework 기반의 통합 테스트가 포함되어 있으며, 이는 CI/CD 파이프라인의 일부로 자동 실행된다.

포함된 주요 테스트
- HDTN 루프백 테스트 (컷스루 모드, 스토리지 모드)
- 링크 장애 포함 여부에 따른 Contact Plan 기반 테스트

---

# 17.0 HDTN을 통한 스트리밍 시작하기

HDTN에서 스트리밍을 수행하려면 **BPSendStream**과 **BPRecvStream** 두 가지 애플리케이션을 사용해야 한다.  
이 두 애플리케이션의 개요는 11.0 절에서 이미 언급되었다.

## 17.1 BPSendStream의 설정 파라미터

**num-circular-buffer-vectors**
UDP 싱크(UDP sink)가 RTP 패킷을 처리하기 전에 보관하는 원형 버퍼(circular buffer)의 요소 개수.
- 기본값: `50`

**max-incoming-udp-packet-size-bytes**
RTP 스트림으로부터 수신되는 UDP 패킷의 최대 크기(바이트 단위).
- 기본값: `2`

**incoming-rtp-stream-port**
RTP 스트림을 수신(listen)할 포트.
- 기본값: `50000`

**rtp-packets-per-bundle**
하나의 번들에 포함되는 RTP 패킷의 개수.
- 기본값: `1`

**induct-type**
사용할 인덕트(induct)의 종류.
- 옵션: `appsink`, `udp`, `tcp`, `fd`
- 기본값: `udp`

**file-to-stream**
스트리밍할 비디오 파일의 경로.
> ⚠️ **주의**: AVC/H.264 인코딩된 비디오만 지원. (HEVC/H.265는 현재 지원하지 않음)

### 17.1.1 BPSendStream 예제 JSON 설정 파일

```json
{
  "outductConfigName": "bpsendstream to node Z",
  "outductVector": [
    {
      "name": "to localhost hdtn one process",
      "convergenceLayer": "stcp",
      "nextHopNodeId": 1,
      "remoteHostname": "localhost",
      "remotePort": 5000,
      "maxNumberOfBundlesInPipeline": 10000,
      "maxSumOfBundleBytesInPipeline": 50000000,
      "keepAliveIntervalSeconds": 17
    }
  ]
}
```

## 17.2 BPRecvStream의 설정 파라미터

**max-rx-bundle-size-bytes**
수신할 번들의 최대 크기(바이트 단위).
- 기본값: `10000000` (10 MB)

**outgoing-rtp-port**
RTP 스트림을 송출(transmit)할 포트.
- 기본값: `50560`

**outgoing-rtp-hostname**
RTP 스트림을 송출할 호스트 이름(IP).
- 기본값: `127.0.0.1`

**num-circular-buffer-vectors**
원형 버퍼(circular buffer)의 요소 개수.
- 기본값: `50`

**outduct-type**
사용할 아웃덕트(outduct)의 종류.
- 옵션: `udp`
- 기본값: `udp`

### 17.2.1 BPRecvStream 예제 JSON 설정 파일

```json
{
  "inductConfigName": "from local hdtn one process to node Z",
  "inductVector": [
    {
      "name": "stcp_bpsink",
      "convergenceLayer": "stcp",
      "boundPort": 7000,
      "numRxCircularBufferElements": 10000,
      "keepAliveIntervalSeconds": 15
    }
  ]
}
```

## 17.3 스트리밍 시나리오를 위한 실행 스크립트

이 스트리밍 시나리오에서는 GStreamer를 활용하여 멀티미디어 데이터를 처리한다.
GStreamer는 오픈소스 멀티미디어 프레임워크로, 파이프라인 기반으로 데이터 처리를 지원한다.

### 17.3.1 파일 스트리밍 (File Streaming)

GStreamer를 사용하여 비디오 파일을 RTP 스트림으로 변환한 뒤, 해당 스트림을 BPSendStream에 전달한다.

**예제 실행 스크립트**:
```
tests/test_script_linux/Streaming/file_streaming/
```

### 17.3.2 실시간 스트리밍 (Live Streaming)

GStreamer를 사용하여 카메라로부터 실시간 RTSP 스트림을 수신하고 이를 BPSendStream에 전달할 수 있다.

세부 구현은 GStreamer 문서를 참고해야 한다.

## 17.4 추가 자료

HDTN 팀이 수행한 스트리밍 활용 사례는 다음 문서를 참고하라:

**4K 고해상도 비디오 및 오디오 스트리밍 – 고속 지연 내성 우주 네트워크 환경에서의 적용**

# 18.0 컨테이너화 (Containerization)

HDTN은 현재 **Docker**와 **Kubernetes**를 이용하여 컨테이너로 배포하는 방식을 지원한다.  
이 방식은 HDTN을 실행하기 위한 모든 의존성(Dependencies)이 포함된 상태로 빌드하여 제공한다.

## 18.1 Docker 사용법

### 1. Docker 설치 확인

```bash
apt-get install docker
```

### 2. 서비스 실행 확인

```bash
systemctl start docker
```

### 3. Dockerfile 빌드

HDTN은 두 가지 Dockerfile을 제공한다:
- 하나는 Oracle Linux 기반 컨테이너
- 다른 하나는 Ubuntu 기반 컨테이너

Ubuntu 컨테이너를 빌드하려면 다음 명령을 실행한다:

```bash
docker build -t hdtn_ubuntu containers/docker/ubuntu/ .
```

> `-t` 옵션은 이미지 이름을 지정한다. 여기서는 `hdtn_ubuntu`라는 이름으로 지정.

### 4. 이미지 확인

```bash
docker images
```

### 5. 컨테이너 실행

```bash
docker run -d -t hdtn_ubuntu
```

### 6. 실행 중인 컨테이너 확인

```bash
docker ps
```

실행 중인 컨테이너의 `CONTAINER_ID`가 표시된다.

### 7. 컨테이너 접속

```bash
docker exec -it <container_id> bash
```

### 8. 컨테이너 중지

```bash
docker stop <container_id>
```

동일한 컨테이너는 다시 시작하거나 제거할 수 있다.

### 9. 모든 컨테이너 확인

```bash
docker ps -a
```

### 10. 컨테이너 제거

사용하지 않을 컨테이너는 다음 명령으로 삭제한다:

```bash
docker rm <container_id>
```

## 18.2 Docker Compose 사용법

Docker Compose는 여러 노드를 동시에 실행하고 구성하는 데 사용된다.

### 관련 파일 위치

```bash
cd containers/docker/docker_compose
```

이 `docker-compose.yml` 파일은 Oracle Linux 기반의 두 컨테이너를 실행한다:
- `hdtn_sender`
- `hdtn_receiver`

### 컨테이너 실행

```bash
docker compose up
```

### 각 노드 접속 (새 터미널에서 실행)

```bash
docker exec -it hdtn_sender bash
docker exec -it hdtn_receiver bash
```

### 활용 예시

이 설정은 두 개의 HDTN 노드 간 테스트를 실행하는 데 적합하다.

예제 스크립트는 아래 위치에서 확인할 수 있다:
```
HDTN/tests/test_scripts_linux/LTP_2Nodes_Test/
```

> ⚠️ **주의**: 반드시 수신 노드(receiver) 스크립트를 먼저 실행해야 한다.
> 그렇지 않으면 송신 노드(sender)가 시작 시 보낼 대상이 없어 실패한다.

## 18.3 Kubernetes 사용법

### 1. 의존성 설치

```bash
sudo apt-install docker microk8s
```

### 2. Docker 이미지 빌드

Kubernetes가 사용할 이미지를 로컬에서 생성한다.

```bash
docker build docker/ubuntu/. -t myhdtn:local
```

### 3. 빌드된 이미지 확인

```bash
docker images
```

### 4. 이미지 캐시에 주입

로컬에서 빌드한 이미지를 microk8s 이미지 캐시에 넣는다.

```bash
docker save myhdtn > myhdtn.tar
microk8s ctr image import myhdtn.tar
```

### 5. 이미지 확인

```bash
microk8s ctr images ls
```

### 6. 클러스터 배포

YAML 파일에서 지정한 이름을 사용해 배포한다.

```bash
microk8s kubectl apply -f containers/kubernetes/hdtn_10_node_cluster.yaml
```

배포가 완료되면 10개의 HDTN Kubernetes Pod가 실행된다.

### 7. Pod 상태 확인

```bash
microk8s kubectl get pods
```

### 8. Pod 내부 컨테이너 접속

```bash
microk8s kubectl exec -it <container_name> -- bash
```

### 9. 배포 종료

작업을 마친 후에는 배포를 삭제한다.

```bash
microk8s kubectl delete deployment hdtn-deployment
```

### 10. 삭제 확인

```bash
microk8s kubectl get pods
```
