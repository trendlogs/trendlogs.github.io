---
id: 198
title: '이더리움의 기본 구조와 동작 원리, 한 번에 끝내는 핵심 정리'
date: '2025-11-16T10:14:00+00:00'
author: '김 윤호'
layout: post
guid: 'https://trendlogs.net/?p=198'
permalink: /ethereum-structure-how-it-works/
categories:
    - 금융
    - 암호화폐
---

---

<figure class="wp-block-image size-large">![](https://cdn.pixabay.com/photo/2021/12/30/13/08/ethereum-6903889_1280.png)</figure>## 이더리움을 구조적으로 이해해야 하는 이유

이더리움은 시가총액 기준 상위에 있는 대표적인 블록체인 플랫폼이지만, 많은 사람이 “코인 가격”만 주로 바라본다. 그러나 이더리움의 진짜 가치는 가격이 아니라 **구조와 동작 원리**에 있다. 이 구조를 이해해야만 스마트컨트랙트, 디앱(DApp), 디파이(DeFi) 같은 개념이 논리적으로 연결되고, 뉴스에서 나오는 “지분증명 전환”, “가스비”, “EVM” 같은 용어를 맥락 속에서 해석할 수 있다.

이더리움은 단순한 분산 장부가 아니라, **전 세계에 분산된 하나의 거대한 상태(state) 머신**으로 설계되어 있다. 각 노드는 동일한 규칙에 따라 상태를 저장하고, 새로운 트랜잭션이 들어올 때마다 같은 연산을 수행해 동일한 결과 상태에 도달한다. 이때 상태 변경을 실제로 계산하는 엔진이 바로 \*\*EVM(Ethereum Virtual Machine, 이더리움 가상머신)\*\*이다. EVM은 모든 노드에서 동일하게 동작하는 가상 컴퓨터이며, 스마트컨트랙트 코드를 실행하고 그 결과를 체인 상태에 반영한다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)

또한 이더리움은 2022년 9월 15일 ‘더 머지(The Merge)’를 통해 작업증명(PoW)에서 **지분증명(PoS) 합의 알고리즘**으로 전환되었다. 이 전환으로 에너지 소비가 약 99.95% 감소했고, 블록을 만드는 방식이 ‘채굴자’ 중심에서 ‘검증자(Validator)’ 중심 구조로 바뀌었다.[ethereum.org+2EY+2](https://ethereum.org/roadmap/merge/?utm_source=chatgpt.com) 이러한 변화는 이더리움 구조를 이해할 때 반드시 포함해야 할 핵심 요소이다.

이 글의 목표는 초보 투자자와 개발 입문자도 이더리움의 전체 구조를 **큰 그림에서 한 번에 정리**할 수 있도록 돕는 것이다. 계정 구조, 트랜잭션 처리 과정, EVM, 스마트컨트랙트, 가스, 합의 구조를 하나의 흐름으로 묶어 설명한다. 이 글을 읽으면 “이더리움에서 어떤 일이 어떻게 흘러가는가?”라는 질문에 스스로 답을 구성할 수 있게 된다. 기술 백서를 통째로 읽지는 않아도 될 정도의 수준을 목표로 한다.

---

## 이더리움의 전체 구조: 분산 상태 머신과 계정 모델

### 1. 분산 상태 머신 개념

이더리움은 흔히 “분산 상태 머신(distributed state machine)”으로 설명된다. 이는 네트워크에 참여하는 모든 노드가 동일한 \*\*세계 상태(world state)\*\*를 저장하고, 새로운 입력(트랜잭션)이 들어올 때마다 같은 규칙으로 상태를 갱신한다는 의미이다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)

세계 상태에는 다음과 같은 정보가 포함된다.

- 각 계정의 ETH 잔고
- 각 스마트컨트랙트의 코드
- 컨트랙트 내부 저장소(storage)에 저장된 데이터
- 기타 넌스(nonce) 등 계정 관련 메타데이터

새로운 블록이 추가될 때마다, 블록 안의 모든 트랜잭션이 순차적으로 실행되고 그 결과가 상태에 반영된다. 이 일련의 과정을 \*\*상태 전이(state transition)\*\*라고 부른다.

### 2. 계정 모델: EOA와 컨트랙트 계정

이더리움은 **계정(Account) 기반 모델**을 사용한다. 대표적으로 두 종류의 계정이 존재한다.[ScienceDirect](https://www.sciencedirect.com/topics/computer-science/ethereum?utm_source=chatgpt.com)

1. **EOA(Externally Owned Account)**
    - 개인이 지갑을 통해 사용하는 일반 계정이다.
    - 비밀키/공개키 쌍을 기반으로 서명하며, 사용자가 트랜잭션을 직접 생성한다.
    - ETH 잔고와 넌스를 가진다.
2. **컨트랙트 계정(Contract Account)**
    - 스마트컨트랙트 코드가 배포된 계정이다.
    - 사용자가 직접 트랜잭션을 ‘보내는’ 것이 아니라, 다른 트랜잭션에 의해 호출된다.
    - 코드와 저장소를 갖고 있으며, 코드 실행 결과에 따라 상태가 바뀐다.

비트코인이 UTXO 기반으로 “코인 조각”을 추적하는 것과 달리, 이더리움은 계정의 상태(잔고, 저장소)를 직접 갱신하는 방식이다. 이 때문에 스마트컨트랙트의 상태를 다루기가 더 유연하다.

---

## EVM(이더리움 가상머신)의 역할

EVM은 이더리움의 핵심 연산 엔진이다. 모든 노드가 동일한 EVM 사양을 구현하고, 같은 바이트코드를 동일한 입력 값으로 실행하면 반드시 같은 결과를 얻도록 설계되어 있다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)

EVM의 주요 특징은 다음과 같다.

- **스택 기반 가상머신**  
    연산은 스택 위에서 이뤄지며, 명령어(opcode)는 피연산자를 스택에서 꺼내 처리한다.
- **상태 머신**  
    각 트랜잭션은 EVM을 통해 실행되며, 실행 결과로 세계 상태가 새로운 상태로 전이된다.[Hedera+1](https://hedera.com/learning/smart-contracts/ethereum-virtual-machine?utm_source=chatgpt.com)
- **가스(Gas) 시스템**  
    모든 연산은 일정한 가스 비용을 갖고, 이 가스가 부족하면 실행이 중단된다. 이는 무한 루프나 과도한 연산 사용을 방지한다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)

개발자는 Solidity 같은 고급 언어로 스마트컨트랙트를 작성하고, 이를 컴파일해 EVM 바이트코드로 변환한다. 이 바이트코드가 컨트랙트 계정에 저장되고, 이후 호출될 때마다 EVM이 코드를 실행한다.[metaschool.so+1](https://metaschool.so/articles/what-is-ethereum/?utm_source=chatgpt.com)

---

## 스마트컨트랙트와 디앱(DApp)의 동작 과정

### 1. 스마트컨트랙트의 개념

스마트컨트랙트는 “조건이 충족되면 자동으로 실행되는 프로그램”이다. 이 프로그램은 이더리움 블록체인 위에 배포되고, 누구나 코드를 조회할 수 있으며, 트랜잭션을 통해 함수를 호출할 수 있다.[Quicknode+1](https://www.quicknode.com/guides/ethereum-development/smart-contracts/an-overview-of-how-smart-contracts-work-on-ethereum?utm_source=chatgpt.com)

스마트컨트랙트는 다음과 같은 특징을 가진다.

- 한 번 배포되면 코드가 블록체인에 영구 저장된다.
- 모든 노드가 같은 코드를 실행하므로, 실행 결과가 어디서나 동일하다.
- 상태(변수, 매핑, 구조체 등)를 온체인에 저장할 수 있다.

### 2. 디앱(DApp)의 구조

일반적인 디앱은 다음처럼 구성된다.

1. 프론트엔드(웹, 앱)
2. 사용자의 지갑(메타마스크 등)
3. 이더리움 네트워크의 스마트컨트랙트

사용자는 웹 화면에서 버튼을 클릭하고, 지갑이 트랜잭션 내용을 보여 준다. 사용자가 서명하면 이 트랜잭션이 네트워크로 전파되고, EVM이 해당 스마트컨트랙트 코드를 실행한다. 실행 결과에 따라 토큰 전송, 데이터 업데이트, 이벤트 발생 등이 일어난다.[Alchemy+1](https://www.alchemy.com/docs/how-ethereum-transactions-work?utm_source=chatgpt.com)

---

## 트랜잭션과 가스: 상태를 바꾸는 연료

### 1. 트랜잭션의 기본 구조

트랜잭션은 “누가 어떤 행동을 하겠다”는 서명이 포함된 요청이다. 대표적인 내용은 다음과 같다.

- 보낸 사람 주소(EOA)
- 받는 사람 주소(EOA 또는 컨트랙트)
- 전송하는 ETH 또는 토큰 양
- 호출할 함수 및 인자 데이터
- 가스 리미트, 가스 가격 관련 필드

트랜잭션은 먼저 \*\*메모리풀(mempool)\*\*에 들어가고, 검증자(또는 이전에는 채굴자)가 이를 선택해 블록에 포함한다.[Alchemy](https://www.alchemy.com/docs/how-ethereum-transactions-work?utm_source=chatgpt.com)

### 2. 가스와 가스비

EVM의 각 연산은 미리 정해진 가스 비용을 가진다. 예를 들어, 단순한 덧셈은 적은 가스를 사용하지만, 저장소를 변경하는 연산은 훨씬 많은 가스를 사용한다. 전체 스마트컨트랙트 실행에 필요한 가스량은 개별 연산 가스의 합으로 계산된다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)

사용자가 실제로 지불하는 수수료는 다음 공식으로 표현된다.

> 지불 수수료 = 사용된 가스량 × (기본 수수료(Base Fee) + 팁(Tip))

EIP-1559 이후, 블록마다 네트워크 혼잡도에 따라 \*\*기본 수수료(Base Fee)\*\*가 자동으로 조정되며, 이 Base Fee는 소각된다. 사용자가 추가로 설정하는 팁은 검증자의 수익이 되어 트랜잭션 우선순위에 영향을 준다.[ethereum.org+1](https://ethereum.org/roadmap/merge/?utm_source=chatgpt.com)

이 구조 덕분에 네트워크 자원이 효율적으로 배분되고, 무분별한 연산 사용을 억제할 수 있다.

---

## 합의 알고리즘: 작업증명에서 지분증명으로의 전환

### 1. PoW에서 PoS로

출시 초기 이더리움은 비트코인과 유사한 **작업증명(PoW)** 합의 방식을 사용했다. 그러나 높은 에너지 소비와 확장성 문제를 해결하기 위해 개발자들은 일찍부터 **지분증명(PoS)** 전환을 목표로 준비했다.

2022년 9월 15일, 이더리움은 “더 머지(The Merge)” 업그레이드를 통해 기존 PoW 기반 메인넷과 PoS 기반 비콘 체인(Beacon Chain)을 병합했고, 이 시점부터 합의는 지분증명으로 전환되었다.[ethereum.org+2Consensys - The Ethereum Company+2](https://ethereum.org/roadmap/merge/?utm_source=chatgpt.com)

PoS에서는 다음과 같은 구조가 사용된다.

- 일정량 이상의 ETH를 **스테이킹**한 검증자가 블록 제안 및 검증에 참여한다.[Flipster](https://flipster.io/en/blog/does-ethereums-move-to-proof-of-stake-affect-eth-staking?utm_source=chatgpt.com)
- 무작위 선택과 경제적 인센티브를 통해 정직한 검증 행위를 유도한다.
- 잘못된 블록을 제안하거나 검증할 경우, 스테이킹한 ETH 일부가 슬래싱(slashing)될 수 있다.

### 2. 에너지 효율과 보안 구조

여러 연구와 분석에 따르면, PoS 전환 이후 이더리움 네트워크의 에너지 소비는 약 99.95% 이상 감소한 것으로 추정된다.[EY+1](https://www.ey.com/en_ch/insights/technology/how-does-the-ethereum-merge-help-the-real-and-virtual-world-save-energy?utm_source=chatgpt.com) PoW에서 필요했던 대규모 GPU·ASIC 채굴 장비가 더 이상 필요하지 않기 때문이다.

보안 측면에서는 **경제적 이해관계**가 핵심이다. 검증자가 공격을 시도하려면 매우 큰 양의 ETH를 스테이킹해야 하고, 공격에 실패할 경우 그 자산을 잃을 위험이 있다. 이는 PoW에서 전기료와 장비 비용이 공격 억제 요인으로 작용하던 구조와 유사한 역할을 한다.

---

## 이더리움 구조가 만들어내는 특징과 한계

### 1. 강점: 프로그래머블 머니와 디지털 자산 생태계

이더리움의 구조는 단순 송금 기능을 넘어, **프로그래머블 머니**와 다양한 디지털 자산을 구현할 수 있게 한다.

- **ERC-20 토큰**: 대다수 토큰이 사용하는 대칭형 토큰 표준
- **ERC-721, ERC-1155**: NFT 및 복합 자산 표준
- **디파이, DAO, NFT 마켓플레이스** 등 다양한 디앱 생태계

스마트컨트랙트와 EVM, 계정 모델이 조합되어 이런 애플리케이션을 온체인에 직접 구현할 수 있다.[metaschool.so+1](https://metaschool.so/articles/what-is-ethereum/?utm_source=chatgpt.com)

### 2. 한계: 확장성, 수수료, 복잡성

이 구조는 동시에 몇 가지 한계를 가진다.

1. **확장성 문제**
    - 모든 노드가 모든 트랜잭션을 검증해야 하므로 처리량에 한계가 있다.
    - 이를 보완하기 위해 레이어2, 롤업, 샤딩 등 확장 기술이 논의·도입되고 있다.[ethereum.org+1](https://ethereum.org/developers/docs/evm/?utm_source=chatgpt.com)
2. **수수료 변동성**
    - 디앱·NFT 등 수요가 몰리는 시기에는 가스비가 크게 상승한다.
    - 사용자는 타이밍과 가스 설정 전략을 고려해야 한다.
3. **개발·보안 복잡성**
    - 스마트컨트랙트는 배포 후 수정이 제한적이고, 버그가 있을 경우 자금 손실로 이어질 수 있다.
    - 보안 감사, 정형 검증, 업그레이드 패턴 등 추가적인 설계가 필요하다.

이더리움의 구조와 동작 원리를 이해하면, 이러한 강점과 한계를 단순한 인상 수준이 아니라 **구조적 이유**와 함께 설명할 수 있다.

---

## 정리 및 3줄 요약

이더리움은 전 세계에 분산된 상태 머신 위에서 동작하는 프로그래머블 블록체인이다. 계정 기반 모델, EVM, 스마트컨트랙트, 가스, 지분증명 합의 구조가 서로 맞물려, 하나의 일관된 시스템을 구성한다. 사용자가 지갑에서 버튼을 한 번 클릭할 때, 내부에서는 계정 상태가 바뀌고, EVM이 코드를 실행하며, 검증자가 블록을 제안하고, 네트워크 전체가 새로운 상태에 합의하는 복잡한 과정이 순차적으로 진행된다.

**3줄 요약**

1. 이더리움은 계정 기반 상태 머신 구조 위에서 EVM이 스마트컨트랙트를 실행하며 상태를 전이시키는 플랫폼이다.
2. 트랜잭션은 가스를 연료로 사용하여 EVM을 구동하고, 그 결과가 블록 단위로 체인에 기록된다.
3. 더 머지 이후 이더리움은 지분증명(PoS) 합의를 사용하며, 에너지 효율성과 보안 구조를 동시에 추구한다.