---
id: 215
title: '카르다노 EUTXO: 초보자도 알 수 있는 설명'
date: '2025-11-19T08:24:00+00:00'
author: '김 윤호'
layout: post
guid: 'https://trendlogs.net/?p=215'
permalink: /cardano-eutxo-%ec%b4%88%eb%b3%b4%ec%9e%90-%ec%95%8c%ec%88%98%ec%9e%88%eb%8a%94-%ec%84%a4%eb%aa%85/
categories:
    - 금융
    - 암호화폐
tags:
    - EUTXO
    - UTXO
    - 계정모델
    - 블록체인
    - 스마트컨트랙트
    - 암호화폐
    - 카르다노
---

카르다노의 ‘확장된 미사용 거래 출력 모델(Extended Unspent Transaction Output, EUTXO)’은 블록체인 원리를 처음 접하는 독자에게도 반드시 알아야 할 핵심 설계다. 이 모델은 단순히 하나의 기술 개념이 아니라, 블록체인이 거래를 처리하고 상태를 관리하는 방식에 근본적 변화를 가져온 구조다. 독자는 이 글을 클릭한 순간부터 “왜 기존 모델이 충분하지 않았는가?”, “EUTXO가 해결하고자 한 문제는 무엇인가?”, 그리고 “이 개념이 실제로 어떻게 동작하는가?”라는 세 가지 질문을 품고 있을 것이다. 그러므로 이 도입부는 독자의 궁금증을 정리하고, 끝까지 글을 읽게 만드는 논리적 흐름을 갖춰야 한다.

기존의 UTXO(Unspent Transaction Output) 모델은 비트코인이 채택한 구조로서, 각 거래가 이전 출력(UTXO)을 입력으로 소비하고 새로운 출력들을 생성하는 방식이다. [Eternl Wiki+3docs.cardano.org+3aiken-lang.org+3](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 반면, 계정 기반 모델은 이더리움에서 사용되며, 계정의 잔액이 직접 갱신되는 방식으로 동작한다. 이 두 모델의 차이는 단순히 기술적 선택을 넘어서 스마트컨트랙트 표현력, 병렬 처리 가능성, 거래 예측 가능성 등 블록체인 설계 전반에 영향을 준다. [OneKey+1](https://onekey.so/blog/ecosystem/cardano-vs-ethereum-why-the-eutxo-model-is-still-a-game-changer/?utm_source=chatgpt.com)

EUTXO는 이러한 배경 위에서 등장했다. 이 모델은 “확장(Extended)”이라는 수식어에서 드러나듯, 단순히 값(value)과 락(lock)만을 가진 출력이 아니라 조건(조건문)을 포함한 스크립트(script)와 추가 데이터(datum)를 담을 수 있도록 설계되었다. 즉, 출력이 단순히 “이 주소에 이 값이 있다”는 정보만을 갖는 것이 아니라 “이 주소에 이 값이 있다 + 어떤 조건이 충족되어야 이 출력이 소비될 수 있다”는 맥락을 포함한다. [docs.cardano.org+1](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 이는 스마트컨트랙트를 보다 정교하게 구현할 수 있는 기반이다.

더 나아가 EUTXO 모델의 가장 중요한 특징 중 하나는 **거래 검증의 예측 가능성**이다. 출력이 어떤 조건과 데이터를 갖고 있는지 명확히 정의되어 있으면, 거래 제출 전에 그 유효성(예컨대 수수료, 조건 충족 여부)을 오프체인(off-chain)에서 미리 점검할 수 있다. [IOHK+1](https://iohk.io/blog/posts/2025/05/02/cardano-s-eutxo-model-bringing-deterministic-predictability-to-blockchain/?utm_source=chatgpt.com) 이런 속성은 계정 기반 모델이 가지기 어려운 특징이다. 따라서 독자가 이 글을 통해 얻어야 할 건 단순한 개념 암기가 아니라 “왜 EUTXO가 중요한가”, “어떻게 구조가 설계되어 있는가”, “실제 어떤 장점과 한계가 존재하는가”에 대한 명확한 이해다.

이제 본문에서는 EUTXO 모델의 구조적 구성요소, 기존 모델과의 비교, 실제 적용 사례 및 장단점을 단계적으로 탐구한다. 이를 통해 독자는 단지 기술 용어를 암기하는 차원이 아니라, 이 모델이 블록체인의 설계방식에 어떻게 영향을 주는지를 체계적으로 파악하게 될 것이다.

---

### 1. EUTXO 모델의 구조적 개념

Extended UTXO (EUTXO) 모델은 Cardano 내 스마트컨트랙트 실행을 위해 설계된 미사용 거래 출력(Unspent Transaction Output, UTXO) 모델의 확장판이다. [docs.cardano.org+2fc20.ifca.ai+2](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 이 모델에서는 거래 출력(Output) 하나가 단순한 금액 잔액을 담는 것이 아니라, 값(value)과 주소(address) 외에 스크립트(script)와 데이터(datum)를 포함할 수 있다. [docs.cardano.org+1](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 출력이 해당 스크립트와 데이터를 통해 소비가능(즉 입력(input)으로 쓰일 수 있는) 여부가 결정되며, 이는 거래 검증 단계에서 핵심적 역할을 한다. 기본적으로, 각 출력은 독립된 상태 객체처럼 기능하며, 이로 인해 전체 네트워크 상태를 하나의 중앙 계정(account)에 저장하고 갱신하는 것과 달리 분리된 단위들이 병렬로 존재할 수 있다.

### 2. 기본 UTXO 모델과의 비교

고전적 UTXO 모델은 Bitcoin이 채택한 방식으로, 각 입력(input)은 이전 거래의 출력(output)을 소비하고 새로운 출력들을 생성하는 구조이다. [위키백과+1](https://en.wikipedia.org/wiki/Unspent_transaction_output?utm_source=chatgpt.com) 이 구조는 출력을 소비한 후에는 해당 출력이 다시 사용할 수 없다는 점에서 더블스펜딩(double spending)을 방지하는 단순하고 안전한 설계이다. 하지만 이 방식은 출력이 추가 조건이나 상태(state)를 포함하기에는 한계가 있다. 이에 반해 EUTXO는 출력에 추가 데이터를 담거나 조건부 스크립트를 포함함으로써 스마트컨트랙트적 기능을 실행할 수 있게 설계되었다. [fc20.ifca.ai+1](https://fc20.ifca.ai/wtsc/WTSC2020/WTSC20_paper_25.pdf?utm_source=chatgpt.com)

### 3. 계정 기반 모델과의 비교

한편 Ethereum이 채택한 계정 기반 모델(account-based model)은 각 계정이 잔액을 보유하며 거래 시 계정 잔액을 갱신한다. 이 방식은 스마트컨트랙트를 보다 자유롭게 작성할 수 있게 하지만, 동시에 상태변화(state mutation)가 전역(global)으로 이루어지므로 병렬 처리(parallelism)에 제약이 있고 거래 결과의 예측 가능성이 떨어진다는 단점이 존재한다. [OneKey+1](https://onekey.so/blog/ecosystem/cardano-vs-ethereum-why-the-eutxo-model-is-still-a-game-changer/?utm_source=chatgpt.com) 반면 EUTXO 모델은 각 출력이 독립적으로 존재하기 때문에 동일 블록 내에서 서로 다른 출력만 소비하는 거래들은 병렬로 처리 가능하며, 결과를 오프체인(off-chain)에서 미리 예측할 수 있다. [IOHK](https://iohk.io/blog/posts/2025/05/02/cardano-s-eutxo-model-bringing-deterministic-predictability-to-blockchain/?utm_source=chatgpt.com)

### 4. 스크립트와 데이터의 역할

EUTXO 모델의 핵심 확장 기능은 ‘검증 스크립트(validator script)’와 ‘추가 데이터(datum)’이다. 출력이 생성될 때 해당 출력은 스크립트와 데이터 필드를 포함할 수 있으며, 거래 소비 시 입력 측은 이 출력이 가진 스크립트와 데이터, 그리고 거래 자체의 정보 등을 참조하여 유효성(validity)을 판단하게 된다. [docs.cardano.org](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 예컨대 특정 시간 이후에만 소비 가능하거나, 특정 서명자가 제출해야 하는 조건, 그 밖의 논리적 제약이 출력에 포함될 수 있다. 이처럼 출력이 “이 금액을 이 주소에 보낸다”는 단순 정보에서 “이 금액을 이 주소에 보냈다 + 다음 조건이 충족될 때만 소비할 수 있다”라는 구조로 진화한 것이다.

### 5. 예측 가능한 거래 검증

EUTXO의 또 다른 특징은 거래 제출 전에 유효성 검증을 오프체인에서 미리 수행할 수 있다는 점이다. 출력이 어떤 조건을 포함하고 있는지, 소비될 입력이 무엇인지, 데이터 및 스크립트 내용이 무엇인지 사전에 파악 가능하므로 수수료(fee)나 실행 실패의 리스크를 줄일 수 있다. [docs.cardano.org+1](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 이러한 예측 가능성은 계정 모델에서는 얻기 어렵다. 계정 모델에서는 내부 상태 변화가 외부 환경이나 이전 거래에 따라 달라질 수 있기 때문이다.

### 6. 병렬 처리 가능성

출력 단위로 상태가 독립적이기 때문에, 동일 블록 내에서 서로 다른 UTXO를 소비하는 거래들은 서로 충돌 없이 병렬로 처리될 수 있다. [docs.cardano.org](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 이는 블록체인 처리량(scannability) 향상 및 지연(latency) 감소 측면에서 의미가 있다. 반면 계정 모델에서는 여러 거래가 동일 계정을 수정하려 할 경우 충돌이나 상태 대기(lock) 현상이 발생할 수 있다. 따라서 EUTXO 구조는 고처리량(high-throughput) 응용에 유리한 설계다. [OneKey](https://onekey.so/blog/ecosystem/cardano-vs-ethereum-why-the-eutxo-model-is-still-a-game-changer/?utm_source=chatgpt.com)

### 7. 스마트컨트랙트 구성 방식

EUTXO 환경에서 스마트컨트랙트는 전통적인 의미의 상태 변화(state mutation)보다는 ‘조건부 출력의 생성 및 소비(creation and consumption of conditional outputs)’ 방식으로 구성된다. 출력에 담긴 스크립트 및 데이터가 소비되면서 새로운 출력이 생성되고, 이 과정이 상태머신(state machine) 형태로 설계될 수 있다. [fc20.ifca.ai](https://fc20.ifca.ai/wtsc/WTSC2020/WTSC20_paper_25.pdf?utm_source=chatgpt.com) 실제로 논문에서는 CEM(Constraint Emitting Machines)이라는 상태전환 머신이 EUTXO 모델에서 구현 가능하다고 제시되어 있다. [fc20.ifca.ai](https://fc20.ifca.ai/wtsc/WTSC2020/WTSC20_paper_25.pdf?utm_source=chatgpt.com)

### 8. 장점

EUTXO 모델이 가진 주요 장점은 다음과 같다.

- 거래 유효성 검증이 오프체인에서 가능하므로 실패 리스크 및 수수료 낭비 감소. [docs.cardano.org](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com)
- 병렬 처리 가능성이 높아 확장성(scalability) 측면에서 유리.
- 출력이 독립 상태를 유지하므로 상태 변경이 글로벌하게 이뤄지는 계정 모델 대비 구조가 단순하고 분석이 용이하다.
- 스마트컨트랙트 실행 비용 및 결과의 예측 가능성이 높다. [IOHK+1](https://iohk.io/blog/posts/2025/05/02/cardano-s-eutxo-model-bringing-deterministic-predictability-to-blockchain/?utm_source=chatgpt.com)

### 9. 한계 및 고려사항

모델이 갖는 한계도 존재한다.

- 출력 하나가 조건과 데이터를 포함함으로써 설계 복잡성이 증가할 수 있다.
- 다단계 상태머신을 구현하기 위해서는 여러 출력과 거래의 조합이 필요해 설계-개발 난이도가 계정 기반 모델보다 높다.
- 출력이 분리되어 독립 상태를 유지하는 구조는 통합된 글로벌 상태 참조가 필요한 응용(예컨트랙트 간 복잡한 상호작용 등)엔 설계가 다소 제한적일 수 있다.
- 또, 병렬 처리가 가능하더라도 실제 네트워크-노드 설계, 수수료 정책, 데이터 용량 등 시스템적 제약이 존재한다.

### 10. 실제 적용 사례

Cardano 생태계 내에서 EUTXO 모델은 여러 스마트컨트랙트-기반 DeFi(탈중앙금융), NFT(대체불가능토큰), 조건부 지불(payments) 시스템 등에 적용되고 있다. 공식 문서에서는 EUTXO 모델이 “거래 성공 여부가 거래 자체와 입력만으로 결정된다”는 점에서 설계 상의 안전성과 예측 가능성을 강조한다. [docs.cardano.org](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 또한 다양한 개발자 문서 및 튜토리얼에서는 “UTXO처럼 작동하되 더 큰 유연성을 가진” 모델이라는 설명이 제시되어 있다. [Eternl Wiki](https://wiki.eternl.io/en/1_getting-started/eutxo?utm_source=chatgpt.com)

---

Cardano의 EUTXO(Extended Unspent Transaction Output) 모델은 기존 UTXO 구조 위에 스크립트(script)와 데이터(datum)를 결합하여 스마트컨트랙트 실행이 가능한 상태 객체(state object)를 구현한 설계다. [docs.cardano.org+1](https://docs.cardano.org/about-cardano/learn/eutxo-explainer?utm_source=chatgpt.com) 출력 하나가 특정 값과 소유자 정보를 담는 데 그치지 않고, 그 출력이 어떤 조건 하에서 소비될 수 있는지를 검증 스크립트로 정의하므로 거래 처리 방식이 보다 명시적으로 변모한다. 또한 각 출력이 독립적 상태로 존재함에 따라 오프체인(off-chain)에서 거래 유효성을 사전에 점검할 수 있고, 결과적으로 수수료 예측 가능성이 높아진다. [IOHK+1](https://iohk.io/blog/posts/2025/05/02/cardano-s-eutxo-model-bringing-deterministic-predictability-to-blockchain/?utm_source=chatgpt.com)

병렬 처리 가능성 또한 EUTXO의 강점이다. 동일 블록 내에서 서로 다른 출력만을 사용하는 거래들은 서로 간섭 없이 처리될 수 있으며, 이는 계정 기반 모델보다 설계상 확장성과 안정성 측면에서 유리한 구조다. [OneKey+1](https://onekey.so/blog/ecosystem/cardano-vs-ethereum-why-the-eutxo-model-is-still-a-game-changer/?utm_source=chatgpt.com) 다만 설계적 난이도, 출력 조합 증가로 인한 복잡성, 다수 출력 간 상호작용 구성에서의 제한성 등은 구현 시 고려해야 할 과제다.

결론적으로, EUTXO 모델은 블록체인의 상태 변경을 보다 명확하고 예측 가능하게 설계함으로써 스마트컨트랙트 플랫폼으로서의 구조적 안정성과 확장성을 동시에 추구한다.

**3줄 요약**

1. EUTXO는 출력에 스크립트와 데이터를 포함시켜 스마트컨트랙트 기능을 지원한다.
2. 거래 검증의 예측성 및 병렬 처리 가능성 덕분에 설계상 확장성과 안정성이 증가한다.
3. 설계 및 개발 난이도, 출력 구조 복잡성 등이 구현 시 주요 고려사항이다.