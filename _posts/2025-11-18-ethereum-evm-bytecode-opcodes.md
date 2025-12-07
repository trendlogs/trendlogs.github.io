---
id: 208
title: '이더리움 EVM 바이트코드 구조와 오퍼코드 동작 원리'
date: '2025-11-18T06:42:00+00:00'
author: '김 윤호'
layout: post
guid: 'https://trendlogs.net/?p=208'
permalink: /ethereum-evm-bytecode-opcodes/
categories:
    - 금융
    - 암호화폐
tags:
    - EVM
    - 개발자
    - 바이트코드
    - 블록체인
    - 스마트컨트랙트
    - 오퍼코드
    - 이더리움
---

블록체인 세계에서 가장 널리 사용되는 스마트 계약 플랫폼인 Ethereum Virtual Machine(EVM)은 고급 언어(예: Solidity)로 작성된 코드를 실제로 블록체인 상에서 실행 가능한 형태로 변환하고 실행하는 핵심 엔진이다. 이 과정에서 ‘바이트코드(bytecode)’와 ‘오퍼코드(opcode)’라는 개념이 등장하며, 이를 정확히 이해하는 것은 스마트 계약의 동작 원리, 가스 비용, 보안 취약점 분석 등에 있어 매우 중요하다.

먼저 “바이트코드 구조”란 무엇인가부터 살펴보자. 스마트 계약 작성자가 Solidity 등으로 코드를 작성하면, 컴파일러는 이를 EVM이 처리할 수 있는 이진 형태인 바이트코드로 변환한다. 그리고 이 바이트코드 안에는 1바이트 단위로 정의된 명령(오퍼코드)와, 그 뒤 이어지는 즉시값(immediate value) 또는 데이터(data)가 함께 병열되어 있다. [learnevm.com+1](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

그다음으로 “오퍼코드 동작 원리”이다. EVM이 바이트코드를 실행할 때는 프로그램 카운터(PC)를 기준으로 바이트코드를 순차적으로 읽어나가며, 스택(Stack), 메모리(Memory), 저장소(Storage), 가스(Gas) 등의 실행 환경을 조작한다. [Quicknode+1](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

이 글에서는 이더리움 EVM의 바이트코드 구조를 단계별로 분해하고, 대표적인 오퍼코드들이 실제로 어떤 동작을 하는지 살펴본다. 나아가 바이트코드 실행이 어떻게 스마트 계약 상태 변화를 이끌어내는지 그 흐름을 정리할 것이다.

---

## 1. 바이트코드 구조

### 1.1 전체 구성

바이트코드는 배포되거나 호출된 계약(Account) 내부에 저장된 실행 코드로, 16진수 문자열 형태로 표현된다. 이 바이트코드 내부에는 다음 요소들이 포함된다:

- 오퍼코드(opcode) 1바이트
- 필요시 즉시값(immediate) 또는 데이터를 읽는 바이트열
- 컨트랙트 생성(constructor)용 코드 + 실행 코드(execution code)
- 메타데이터 (컴파일러 정보 등)

예컨대, 처음 바이트코드가 “0x60 …” 형태로 시작하면 이는 `PUSH1` (0x60) 명령어가 곧 이어진다는 의미이다. [learnevm.com+1](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

### 1.2 프로그램 카운터(PC)와 흐름

EVM은 프로그램 카운터(PC)를 0부터 시작하며, 바이트코드의 각 바이트(혹은 오퍼코드+즉시값)를 순차적으로 읽는다. 오퍼코드가 즉시값을 요구할 경우, PC는 그 즉시값 바이트만큼 추가로 이동한다. 그러나 `JUMP`, `JUMPI` 같은 제어 흐름 명령은 PC를 특정 위치로 설정한다. [learnevm.com+1](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

### 1.3 코드 구성과 배포 시점

계약이 생성될 때, 트랜잭션의 `data` 필드에 포함된 바이트코드는 “생성 코드(constructor code)”로 실행된 뒤, 실행 코드(execution code)를 반환하고, 이 반환된 코드가 실제로 블록체인 상에 저장된다. 배포 이후 호출될 때는 이 실행 코드가 호출된다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

### 1.4 바이트코드 예

내부적으로는 다음과 같은 흐름이 가능하다:

```
0x60 80 60 40 52 60 XX …

```

여기서 `0x60`은 `PUSH1`, 다음 바이트 ‘80’은 즉시값 0x80, 이어서 `0x60`(다시 PUSH1), ‘40’(즉시값 0x40), `0x52`(MSTORE) 등으로 읽힌다. [Quicknode+1](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

---

## 2. 오퍼코드 이해

### 2.1 오퍼코드란

오퍼코드는 EVM이 직접 해석·실행할 수 있는 기본 명령어 단위이다. 일반적으로 1바이트(8비트)를 사용하며, 각 명령에는 고정된 기능이 있고, 몇몇 명령은 즉시값이 뒤따른다. [learnevm.com+1](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

### 2.2 주요 실행 환경 구성요소

오퍼코드는 다음 실행 환경을 제어한다:

- **스택(Stack)**: 256비트(32바이트) 단위 워드(word)를 저장하는 LIFO 구조. 최대 깊이 1024개. [Quicknode+1](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)
- **메모리(Memory)**: 바이트 바이너리 배열로서 실행 중에만 존재하며, 트랜잭션 종료 시 사라진다. 동적 확장이 가능하지만 가스 비용이 증가한다. [evm.codes+1](https://www.evm.codes/about?utm_source=chatgpt.com)
- **스토리지(Storage)**: 각 계약에 할당된 영구 키-값 매핑(32바이트 키 → 32바이트 값). 계약 상태가 저장된다. [evm.codes](https://www.evm.codes/about?utm_source=chatgpt.com)
- **프로그램 카운터(PC)**: 다음 실행할 오퍼코드의 바이트 위치를 가리키는 인덱스. [learnevm.com](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)
- **가스(Gas)**: 각 오퍼코드 실행 및 메모리 확장 등에 대한 비용을 측정하는 단위. 실행 중 가스가 부족하면 상태가 되돌려진다. [Quicknode+1](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

### 2.3 오퍼코드 종류 및 범주

대표적인 오퍼코드 그룹을 살펴보면 다음과 같다. [Ethereum Stack Exchange+1](https://ethereum.stackexchange.com/questions/119/what-opcodes-are-available-for-the-ethereum-evm?utm_source=chatgpt.com)

- 산술 연산: `ADD (0x01)`, `MUL (0x02)`, `SUB (0x03)` 등
- 비교 및 논리: `LT (0x10)`, `GT (0x11)`, `EQ (0x14)`, `ISZERO (0x15)`
- 스택, 메모리, 저장소 및 흐름 제어: `POP (0x50)`, `MLOAD (0x51)`, `MSTORE (0x52)`, `SLOAD (0x54)`, `SSTORE (0x55)`, `JUMP (0x56)`, `JUMPI (0x57)`, `JUMPDEST (0x5b)`
- 푸시(PUSH) 명령: `PUSH1 (0x60)` ~ `PUSH32 (0x7f)`
- 복제(DUP) 및 교환(SWAP): `DUP1 (0x80)` ~ `DUP16`, `SWAP1 (0x90)` ~ `SWAP16`
- 로깅: `LOG0 (0xa0)` ~ `LOG4 (0xa4)`
- 시스템 호출: `CREATE (0xf0)`, `CALL (0xf1)`, `DELEGATECALL (0xf4)`, `SELFDESTRUCT (0xff)`

### 2.4 동작 예제

간단한 예로, 바이트코드 `0x60 02 60 03 01`가 있다면 다음과 같이 해석할 수 있다:

- `0x60 02` → `PUSH1 0x02` : 스택에 값 0x02 푸시
- `0x60 03` → `PUSH1 0x03` : 스택에 값 0x03 푸시
- `0x01` → `ADD` : 스택에서 두 값을 꺼내 더하고, 결과(0x05)를 스택에 푸시

이처럼 오퍼코드는 주로 스택 기반 구조로 동작한다. [learnevm.com+1](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

### 2.5 제어 흐름 및 조건 분기

- `JUMP (0x56)`는 스택 최상단에 위치한 값을 PC로 설정한다.
- `JUMPI (0x57)`는 스택에서 두 값을 꺼내 첫 번째 값이 ‘조건’으로 사용되며, 조건이 참이면 두 번째 값을 PC로 설정한다.
- `JUMPDEST (0x5b)`는 점프 대상이 될 수 있는 위치를 표시한다. EVM은 안전을 위해 점프 가능한 위치를 미리 표시해야 한다. [Ethereum Stack Exchange+1](https://ethereum.stackexchange.com/questions/119/what-opcodes-are-available-for-the-ethereum-evm?utm_source=chatgpt.com)

### 2.6 가스와 리소스 비용

각 오퍼코드는 고정 가스 비용이 있으며, 일부는 동적 비용(예: 메모리 확장 비용)을 가진다. 예컨대 메모리를 확장하면 `memory_size_word ** 2 / 512 + (3 * memory_size_word)` 형태의 비용 산정 방식이 존재한다. [evm.codes](https://www.evm.codes/about?utm_source=chatgpt.com)  
가스가 부족하면 `REVERT`, `INVALID` 등의 명령어로 실행이 중단되고 상태 변화가 취소된다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

---

## 3. 바이트코드 실행 흐름

### 3.1 트랜잭션 → EVM 실행 컨텍스트

1. 사용자가 외부 소유 계정(EOA)에서 계약 주소에 트랜잭션을 보낸다.
2. EVM은 해당 트랜잭션을 검증하고 실행 컨텍스트(스택·메모리·저장소·가스)를 초기화한다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)
3. 바이트코드가 저장되어 있는 계약 계정의 코드 영역이 실행된다.
4. PC는 0에서 시작하며 오퍼코드를 순차적으로 해석하고 실행한다.
5. 실행 중 스택, 메모리, 저장소가 변경된다. 제어 흐름 명령에 의해 PC가 조작될 수 있다.
6. 상태가 변경되면 저장소에 영구 기록되고, 실행이 끝나면 로그가 남을 수 있다.
7. 가스를 모두 소모하거나 `STOP`, `RETURN`, `REVERT`, `SELFDESTRUCT` 등이 호출되면 실행 종료된다. [learnevm.com](https://learnevm.com/chapters/vm/overview?utm_source=chatgpt.com)

### 3.2 스마트 계약 함수 호출과 바이트코드

계약 호출 시 트랜잭션의 `data` 필드 첫 4바이트는 함수 선택자(function selector)이다. 이후 인수(argument)가 인코딩되어 따라온다. 계약 코드 내부에서는 `CALLDATALOAD`, `CALLDATASIZE`, `CALLDATACOPY` 등의 오퍼코드로 호출 데이터를 읽어 처리한다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

### 3.3 배포 시 코드 실행

계약이 생성될 때에는 생성 코드가 실행되어 최종 계약 코드를 반환하고 배포된다. 그 후에는 반환된 코드만이 실행된다. 배포용 바이트코드는 일시적이다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

### 3.4 상태 변화 사례

예컨대 `SSTORE` 명령은 스토리지 키와 값을 스택에서 꺼내서 저장소에 기록한다. 이때 저장소가 변경되며, 가스 비용이 크게 발생한다. `SLOAD`는 저장소 읽기를 수행한다. 저장소는 영구 상태이므로 다음 호출에서도 유지된다. [evm.codes+1](https://www.evm.codes/about?utm_source=chatgpt.com)

### 3.5 오류 처리

오류가 발생하면 `REVERT`나 `INVALID` 등의 명령으로 실행이 중단된다. 이때는 상태 변경이 모두 취소되고, 내부적으로 반환된 데이터(혹은 없는 데이터)가 호출자에게 전달될 수 있다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

---

## 4. 왜 바이트코드와 오퍼코드 구조를 이해해야 하는가

### 4.1 가스 최적화

고급 언어에서 작성한 코드라도 컴파일된 바이트코드를 보면 불필요한 오퍼코드가 삽입된 경우가 있다. 오퍼코드 비용이 직접 가스 소비로 이어지므로, 바이트코드 구조를 이해하는 것은 가스 비용 절감 및 효율적 계약 설계에 기여한다.

### 4.2 보안 분석

바이트코드 수준에서 제어 흐름을 분석하면 함수 호출 경로, 조건문 우회 가능성, 리엔트런시(Reentrancy) 가능성 등을 판단할 수 있다. 예컨대 `JUMPDEST`나 `CALL`의 사용 위치 등을 바이트코드로 파악하면 취약점 분석이 가능하다.

### 4.3 디버깅 및 확인

블록체인 탐색기에서 실제로 배포된 계약의 바이트코드를 보고 문제를 진단하거나, ABI와 일치하는지 확인할 수 있다. 바이트코드와 오퍼코드를 이해하면 “이 코드가 실제로 어떤 일을 하나?”라는 의문에 답할 수 있다.

---

## 마무리

요약하자면, EVM 바이트코드는 고급 언어 → 컴파일 → 바이트코드 형태로 변환된 후, EVM 내부의 스택·메모리·저장소 환경 위에서 오퍼코드 단위로 실행된다. 이 구조와 동작 원리를 이해하는 것은 스마트 계약의 효율성, 보안, 디버깅 모든 측면에서 필수적이다.

### 3줄 요약

- 스마트 계약은 컴파일되어 EVM이 해석 가능한 바이트코드로 저장된다.
- 바이트코드는 1바이트 오퍼코드 + 즉시값 형식으로 구성되며, 스택·메모리·저장소 환경에서 실행된다.
- 오퍼코드 구조와 실행 흐름을 이해하면 가스 최적화와 보안 분석에 유리하다.