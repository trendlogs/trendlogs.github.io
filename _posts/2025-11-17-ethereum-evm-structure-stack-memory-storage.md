---
id: 206
title: '이더리움 EVM 구조 초보자도 쉽게 이해하기: 스택·메모리·스토리지 동작 방식'
date: '2025-11-17T13:56:28+00:00'
author: '김 윤호'
layout: post
guid: 'https://trendlogs.net/?p=206'
permalink: /ethereum-evm-structure-stack-memory-storage/
categories:
    - 금융
    - 암호화폐
tags:
    - EVM
    - EVM내부구조
    - 가스비최적화
    - 메모리
    - 블록체인구조
    - 솔리디티
    - 스마트컨트랙트
    - 스택
    - 스토리지
    - 이더리움
---

블록체인 세계에 막 발을 내딛은 당신에게, 이 용어들은 때로는 낯설고 복잡하게 느껴질 수 있다. 스마트컨트랙트를 작성할 때, “이 값은 어디 저장되는 걸까?”, “왜 어떤 자료형은 변경된 뒤에도 사라지지 않을까?”라는 질문을 한 번쯤 해봤을 것이다. 이 글에서는 그런 막연한 궁금증을 단순하고 명확한 개념으로 풀어준다.

먼저, 당신이 ‘왜 알아야 할까?’라는 의문을 가졌다면 답변이 있다. 왜냐하면 Ethereum Virtual Machine(EVM)의 핵심 구조를 이해하면 스마트컨트랙트의 동작 원리를 꿰뚫을 수 있고, 가스비가 왜 그렇게 나가는지, 오류가 왜 발생하는지, 저장된 값이 왜 남아 있는지 등이 훨씬 명확해진다.

예컨대, 당신이 단순히 “변수 X에 10을 저장했다”라는 말을 쓰더라도, 실제로는 어떤 영역(Stack, Memory, Storage)으로 들어갔는지, 얼마나 오래 남는지, 가스 비용은 얼마인지 등 여러 보이지 않는 기제들이 작동하고 있다. 이러한 내부 동작을 이해하면, 스마트컨트랙트를 설계하거나 디버깅할 때 막혔던 지점이 술술 풀릴 수 있다.

이제 막막했던 용어 ‘스택’, ‘메모리’, ‘스토리지’가 이 글을 통해 명확한 개념으로 전환될 것이다. 먼저 각각이 어떤 역할을 하는지 살펴본 뒤, 서로 어떻게 연관되며 왜 구분되어 있는지까지 단계별로 정리한다.

그럼 지금부터 EVM 내부의 세 가지 핵심 영역을 차근차근 탐구해보자.

---

## 1. EVM 구조 개요

Ethereum Virtual Machine(이하 EVM)은 Ethereum 블록체인에서 스마트컨트랙트를 실행하는 가상 컴퓨터 환경이다. [Quicknode+2EVM.codes+2](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com) 이 환경은 스마트컨트랙트 바이트코드를 해석하고, 상태 전환을 수행하며, 전체 시스템 상태(World State)를 업데이트한다. 그 내부에는 크게 **스택(Stack)**, **메모리(Memory)**, \*\*스토리지(Storage)\*\*라는 세 가지 저장 영역이 존재하며, 각 영역은 역할과 지속성, 비용 구조에서 명확히 구분된다. 이를 이해하면 스마트컨트랙트가 “값을 저장했다”라는 단순한 문장을 넘어 실제 어떤 영역에서 어떤 기제에 의해 처리되는지 파악할 수 있다.

---

## 2. 스택(Stack)의 동작 방식

스택은 ‘마지막에 들어간 것이 먼저 나오는(LIFO: Last-In First-Out)’ 구조로 운영된다. EVM 내부에서는 각 항목이 **256비트(32바이트)** 크기의 워드(word)이다. [CoinsBench+1](https://coinsbench.com/the-evm-stack-0766e212560b?utm_source=chatgpt.com) 스택의 최대 깊이는 1,024개 항목으로 제한되어 있으며, 이 한계를 넘으면 스택 오버플로우 오류가 발생한다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)  
예컨대 `PUSH1`, `ADD`, `POP`, `SWAP1` 같은 오퍼코드는 스택에 값을 넣고 꺼내는 방식으로 실행된다. [EVM.codes+1](https://www.evm.codes/about?utm_source=chatgpt.com) 스택은 함수 인자나 로컬 변수 저장용이라기보다는 **연산 중간 값 처리 및 제어 흐름 관리**에 주로 쓰인다. 따라서 개발자가 직접 스택을 지정할 일은 거의 없으며, 컴파일러가 대부분 관리한다. [Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm?utm_source=chatgpt.com)

### 스택 사용 시 유의사항

- 스택 항목은 계약 호출이 끝나면 사라지는 임시 상태이다.
- 배열이나 매핑 등 복잡한 구조체는 스택에 올릴 수 없으며, 이 경우 메모리나 스토리지로 넘어간다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)
- 스택을 효율적으로 사용하려면 연산 흐름을 단순화하고 불필요한 중간 값을 만들지 않는 것이 좋다.

---

## 3. 메모리(Memory)의 특성

메모리는 함수 호출 또는 트랜잭션 실행 중 **임시적 데이터를 저장하는 선형 메모리 공간**이다. 호출이 종료되면 메모리 내용은 사라진다. [Medium+1](https://medium.com/%40andrey_obruchkov/introduction-to-the-ethereum-virtual-machine-evm-bb4162d6dc53?utm_source=chatgpt.com) 메모리는 바이트 단위로 확장 가능하며, 기본 단위는 32바이트(256비트) 워드이다. 명령어로는 `MLOAD`, `MSTORE`, `MSTORE8` 등이 있다. [powersandwich.com.tw+1](https://powersandwich.com.tw/en/posts/evm-structure-overview/?utm_source=chatgpt.com)  
메모리는 스택의 한계로 처리하기 힘든 **배열, 문자열, 동적 데이터** 등을 임시 저장할 때 쓰인다. 스택에 직접 올릴 수 없는 데이터 구조도 여기서 작업 된다. [CoinsBench](https://coinsbench.com/the-evm-stack-0766e212560b?utm_source=chatgpt.com)

### 메모리 비용 및 최적화

메모리는 스택보다 저렴하지만, 사용 용량이 커질수록 비용도 커진다. 특히 메모리 확장은 비용이 제곱(quadratic) 형태로 증가할 수 있다. [Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm?utm_source=chatgpt.com) 따라서 메모리를 많이 사용하는 함수에서는 가능한 작은 데이터 구조, 적절한 초기 크기 지정, 불필요한 확장을 피하는 설계가 권장된다.  
예를 들어, 임시 배열을 지나치게 크게 선언하거나, 반복문 내에서 메모리 동적 할당을 남발하면 가스 비용이 급격히 올라갈 수 있다.

---

## 4. 스토리지(Storage)의 영구성

스토리지는 계약 상태변수(state variables) 등 **영구적 데이터 저장소**로, 트랜잭션이 끝난 후에도 블록체인 상태 트리에 남는다. [Medium+1](https://medium.com/%40andrey_obruchkov/introduction-to-the-ethereum-virtual-machine-evm-bb4162d6dc53?utm_source=chatgpt.com) 스토리지는 “키(key) → 값(value)” 형태의 256비트 워드 맵 형태로 구성되어 있고, 읽기에는 `SLOAD`, 쓰기에는 `SSTORE` 명령어가 사용된다. [EVM.codes](https://www.evm.codes/about?utm_source=chatgpt.com)  
스토리지는 가장 비용이 높다. 예컨대 “값이 0에서 다른 값으로 바뀌는 경우” 가스 비용이 크며, “값을 0으로 바꾸는 경우”에는 일부 가스 환급이 제공되기도 한다. [Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm?utm_source=chatgpt.com)

### 스토리지 사용 전략

- 영구성 있는 데이터이므로, 잦은 쓰기가 필요한 데이터는 스토리지 대신 메모리 또는 외부 저장소 고려.
- 상태변수 개수, 구조체 크기, 매핑 깊이 등을 설계 초기부터 신중히 구성하면 가스 절감에 효과적이다.
- 불필요한 상태 업데이트를 최소화하고, 초기값 혹은 0값으로 돌아가는 디자인이면 가스 환급을 고려한다.

---

## 5. 세 영역의 비교 및 관계

<figure class="wp-block-table">| 영역 | 지속성 | 주사용도 | 비용 구조 |
|---|---|---|---|
| 스택 | 호출 종료 시 사라짐 | 연산 중간값 · 제어 흐름 | 매우 저렴 |
| 메모리 | 호출 종료 시 사라짐 | 함수 실행 중 임시 데이터 저장 | 중간 수준 (사용량 증가 시 비용 증가) |
| 스토리지 | 트랜잭션 이후 영구 저장 | 상태변수, 계약 상태 보존 | 가장 비용이 높음 |

</figure>이들 영역이 존재하는 이유는 **속도·비용·영속성의 균형**을 맞추기 위함이다. 예컨대 모든 값을 스토리지에 저장한다면 비용이 급증하고 성능이 저하된다. 반대로 모든 값을 스택에 저장할 수는 없기에 메모리가 개입하며, 메모리만으로 영구 저장할 수 없기에 스토리지가 존재한다.

---

## 6. 가스비와 저장 구조의 상관관계

EVM에서 각 오퍼코드는 사전 정의된 가스 비용을 갖는다. 저장 구조 선택이 가스 비용에 직접적인 영향을 준다.

- 스택 연산은 비용이 매우 낮다. [Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm?utm_source=chatgpt.com)
- 메모리는 크기가 커질수록 추가 가스가 발생한다.
- 스토리지는 읽기와 쓰기 비용이 크며, 특히 쓰기는 상태 트리를 업데이트하므로 비용 부담이 크다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)  
    따라서 스마트컨트랙트를 설계할 때는 “어느 저장 영역을 써야 할까?”라는 질문이 곧 “가스를 얼마나 줄일 수 있을까?”라는 질문이 된다. 예컨대, 반복문 내부에서 스토리지에 직접 쓰기보다는 메모리에 먼저 저장하고 마지막에 한 번만 스토리지로 옮기는 방식이 대표적이다.

---

## 7. 실제 컨트랙트 흐름과 저장 구조 매핑

솔리디티(Solidity)로 작성된 계약 코드가 어떻게 EVM 내 저장 구조로 분산되는지를 이해하면 설계 품질이 높아진다. 예를 들어:

```
contract SimpleStorage {
    uint256 public storedData;        // → 스토리지
    function set(uint256 x) public {
        uint256 temp = x * 2;         // → 메모리 또는 스택
        storedData = temp;            // → 스토리지 write
    }
    function get() public view returns (uint256) {
        return storedData;            // → 스토리지 read → 스택 return
    }
}

```

위 코드에서 `storedData`는 상태변수이므로 스토리지에 저장된다. 함수 내 `temp` 변수는 임시 데이터이므로 메모리 또는 스택에 위치한다. 실제 EVM는 그 값을 스택에 올리고, 계산 후 다시 스토리지로 쓰는 흐름을 거친다. [CoinsBench+1](https://coinsbench.com/the-evm-stack-0766e212560b?utm_source=chatgpt.com)  
이처럼 설계 시 저장 구조를 고려하면 디버깅이나 가스 최적화에서 우위를 가질 수 있다.

---

## 8. 최적화 전략

- **불필요한 상태 변수 제거**: 상태변수 → 스토리지 사용을 줄이면 가스 절감에 직결된다.
- **메모리 활용 극대화**: 복잡한 연산 중간값이나 반복 데이터는 메모리에 담고, 최종 결과만 스토리지로 옮긴다.
- **배열 &amp; 구조체 설계 시 슬롯 배치 고려**: 32바이트 슬롯으로 저장되기 때문에 구조체 멤버 배치에 따라 저장 소비 슬롯이 달라질 수 있다.
- **읽기만 발생하는 경우 view 함수 활용**: 상태변수를 읽기만 한다면 가스 소비가 적은 읽기용 함수로 설계한다.
- **초기값 0 설정–&gt;청산 전략**: 스토리지 값은 0이 기본이므로, 다시 0으로 설정하면 일부 가스 환급이 가능한 케이스가 있다. [Ethereum Stack Exchange](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm?utm_source=chatgpt.com)

---

## 9. 심화 주제: 호출(Context)과 저장 구조

컨트랙트 호출 시 새로운 실행 컨텍스트가 생성된다. 이 맥락 안에서 스택·메모리 공간이 초기화되고, 호출이 끝나면 이들은 사라진다. [EVM.codes](https://www.evm.codes/about?utm_source=chatgpt.com) 반면 스토리지는 이 컨텍스트 외부에 위치해 있으며 호출 간에도 유지된다. 이러한 구조 덕분에 EVM은 \*\*결과의 결정성(determinism)\*\*을 보장할 수 있다: 동일한 상태와 입력이면 동일한 출력이 나온다. [Quicknode](https://www.quicknode.com/guides/ethereum-development/smart-contracts/a-dive-into-evm-architecture-and-opcodes?utm_source=chatgpt.com)

---

스택(Stack), 메모리(Memory), 스토리지(Storage)는 Ethereum Virtual Machine(EVM)의 핵심 저장 구조로, 각각의 용도와 지속성이 다르다. 스택은 각 명령어가 실행되는 즉시 사용하는 임시 자료 공간으로서 연산 처리에서 가장 빠르고 비용도 낮다. 메모리는 트랜잭션 실행 중 사용할 임시 저장소로, 함수 호출이나 배열 / 문자열 등 복잡한 데이터가 처리되는 동안 생성되며, 호출이 종료되면 사라진다. 반면 스토리지는 스마트컨트랙트의 상태변수 등이 저장되는 영구적 공간으로 블록체인 상태에 반영되며, 읽기·쓰기 비용이 크게 들기 때문에 최적화가 중요하다. 이 글에서 이들 구조의 특징과 가스비와의 관계, 코드 설계 시 고려사항을 정리했다. 이를 통해 스마트컨트랙트를 설계하거나 디버깅할 때 내부 동작을 이해하고 비용을 합리적으로 제어할 수 있는 기반을 마련했다.

### 3줄 요약

1. 스택은 연산 중심의 임시 공간이며, 처리 속도가 빠르고 비용이 가장 적다.
2. 메모리는 트랜잭션 실행 중 유지되며, 호출 종료 시 삭제되는 임시 저장소다.
3. 스토리지는 계약의 상태를 영구 저장하며, 수정 시 많은 가스가 소비되므로 설계 최적화가 필수다.