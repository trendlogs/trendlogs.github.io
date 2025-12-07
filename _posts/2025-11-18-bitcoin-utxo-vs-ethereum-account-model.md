---
id: 212
title: '비트코인 UTXO 구조와 이더리움 account-based 모델의 차이'
date: '2025-11-18T21:22:00+00:00'
author: '김 윤호'
layout: post
guid: 'https://trendlogs.net/?p=212'
permalink: /bitcoin-utxo-vs-ethereum-account-model/
categories:
    - 금융
    - 암호화폐
tags:
    - UTXO
    - 계정모델
    - 블록체인
    - 블록체인기술
    - 비트코인
    - 스마트컨트랙트
    - 암호화폐
    - 이더리움
---

“왜 어떤 블록체인은 ‘잔액’으로 보여주고, 또 어떤 블록체인은 ‘사용 가능한 출력(UTXO)’ 방식으로 보여주는가?”라는 질문은 블록체인 설계의 핵심에 닿아 있다. 우리가 ‘암호화폐 지갑에 2 BTC가 있다’고 말할 때, 실제로는 각기 다른 모델 속에서 그 2 BTC가 어떤 형태로 존재하고 관리되는지에 대한 설계적 선택이 숨어 있다.  
예컨대 Bitcoin은 UTXO(Unspent Transaction Output)라는 단위를 중심으로 잔액을 계산하고, Ethereum은 계정(account)이라는 단위로 잔액과 상태(state)를 관리한다. 이 두 모델은 단순히 ‘잔액을 기록하는 방식’ 이상으로, 확장성, 병렬처리 가능성, 스마트컨트랙트 연계성, 개인정보 보호 등 다양한 설계 트레이드오프(trade-off)를 수반한다.  
예컨대 비트코인의 UTXO 모델은 각각의 출력이 ‘한 번만 사용 가능(spent once)’하고, 이후에는 새 출력으로 전환되므로 중복 사용(double-spending)을 막는 데 강점을 보인다. [Investopedia+2Kraken+2](https://www.investopedia.com/terms/u/utxo.asp?utm_source=chatgpt.com) 반면, 이더리움의 계정모델(account-based model)은 각 계정의 잔액과 상태가 지속적으로 업데이트되는 구조로, 스마트컨트랙트가 여러 계정 상태를 참조하고 수정하기에 보다 적합하다. [Alchemy](https://www.alchemy.com/docs/ethereum-accounts?utm_source=chatgpt.com)  
그렇다면 두 모델은 구체적으로 어떤 메커니즘을 띠며, 각각 어떤 장단점이 있는가? 본문에서는 먼저 UTXO 모델과 계정모델의 작동 원리를 살펴보고, 이어서 양자 간의 비교·분석을 통해 설계 상의 함의를 정리한다.

---

## 1. UTXO 모델의 작동 원리

#### 1-1. 개념 정의

UTXO(“Unspent Transaction Output”)란, 이전 거래에서 생성되어 아직 소비되지 않은 출력(output)을 의미한다. [Investopedia+1](https://www.investopedia.com/terms/u/utxo.asp?utm_source=chatgpt.com) 즉 사용자가 잔액 X BTC를 보유했다고 할 때, 내부적으로는 여러 개의 UTXO들이 존재하며 각 UTXO가 특정 수량을 담고 있다.  
예컨대 A가 B에게 3 BTC를 보내려면, A의 지갑에 있는 UTXO 중 하나 또는 여러 개를 입력(input)으로 사용해서 해당 금액을 소비(spend)하고, B에게 3 BTC를 출력으로 생성하며, 남은 잔액에 대해 ‘거스름(change)’ 출력이 다시 A에게 되돌아가는 방식이다. [kaleido.io+1](https://www.kaleido.io/blockchain-blog/utxo-vs-account-model?utm_source=chatgpt.com)

#### 1-2. 거래 흐름

1. 이전 UTXO들이 입력으로 참조된다.
2. 거래가 검증되면 해당 UTXO들은 ‘소비(spent)’ 상태로 전환된다.
3. 새로운 출력들이 생성되어 사용 가능한 UTXO가 된다.
4. 잔액은 이 사용 가능한 UTXO들의 합산으로 계산된다. [cryptoapis.io+1](https://cryptoapis.io/blog/359-track-utxo-balances-across-bitcoin-litecoin-and-dogecoin-with-crypto-apis?utm_source=chatgpt.com)

#### 1-3. 특징 및 설계 함의

- **병렬 처리 가능성**: 각각의 UTXO는 독립적이기 때문에, 서로 상관 없는 입력들을 병렬로 처리 가능하다. [kaleido.io](https://www.kaleido.io/blockchain-blog/utxo-vs-account-model?utm_source=chatgpt.com)
- **투명성 및 추적성**: 각 출력이 고유하며 자금 흐름을 추적하기 쉽다.
- **거스름 처리 필요**: 입력이 출력보다 클 경우 반드시 잔액 반환 출력이 생성되어야 하므로, 거래 설계가 다소 복잡하다. [AMINA Bank](https://aminagroup.com/research/a-beginner-s-guide-to-blockchain-accounting-standards/?utm_source=chatgpt.com)
- **지갑 관리 복잡성**: 사용자는 여러 UTXO를 관리해야 하며, 매우 작거나 분리된 UTXO가 많아질 경우 수수료 및 관리 비용 측면에서 비효율이 된다. [River](https://river.com/learn/bitcoins-utxo-model/?utm_source=chatgpt.com)

### 2. 계정 기반(account-based) 모델의 작동 원리

#### 2-1. 개념 정의

계정 기반 모델은 각 주소(account)가 잔액(balance)과 상태(state)를 직접 보유하고, 거래가 발생하면 해당 계정의 상태가 직관적으로 수정되는 방식이다. [Alchemy+1](https://www.alchemy.com/docs/utxo-vs-account-models?utm_source=chatgpt.com)  
예컨대 A가 B에게 5 ETH를 보낼 경우, A의 계정 잔액이 5만큼 감소하고 B의 잔액이 5만큼 증가한다. 별도의 출력 / 입력 구조를 신경 쓸 필요가 없다. [dev.cube.exchange](https://dev.cube.exchange/what-is/account-model?utm_source=chatgpt.com)

#### 2-2. 거래 흐름

1. 송신 계정이 충분한 잔액을 갖추었는가 검증한다.
2. 송신 계정의 nonce(거래 순서를 위한 카운터)가 올바른지 확인한다. [blog.defichain.com](https://blog.defichain.com/2023/12/what-is-ethereums-accounts-based-model.html?utm_source=chatgpt.com)
3. 송신 계정 잔액이 감소하고, 수신 계정 잔액이 증가한다.
4. 상태(state)는 글로벌 루트(state root) 형태로 커밋된다. [Cube Exchange](https://www.cube.exchange/what-is/account-model?utm_source=chatgpt.com)

#### 2-3. 특징 및 설계 함의

- **단순성**: 잔액이 한눈에 파악 가능하므로 사용자 경험(UX)과 지갑 설계가 간편하다. [Cheesecake Labs](https://cheesecakelabs.com/blog/blockchain-transactions-utxo-vs-account-based-models/?utm_source=chatgpt.com)
- **스마트컨트랙트 및 상태 관리에 유리**: 계약 코드(account) 자체가 상태를 저장하고 호출이 가능하므로 복잡한 dApp 구성에 적합하다. [Xangle](https://xangle.io/en/research/detail/720?utm_source=chatgpt.com)
- **병렬성 제약**: 동일 계정에 대한 동시 거래가 있을 경우, 상태 충돌(race)이나 nonce 순서 문제 등이 발생할 수 있다. [ITNext](https://itnext.io/utxo-vs-account-based-blockchains-a-clear-comparison-963872e5a6ea?utm_source=chatgpt.com)
- **프라이버시 한계**: 계정 하나에 잔액이 집약되기 때문에 잔액 흐름을 분석하기가 비교적 쉽다. [arXiv](https://arxiv.org/abs/2005.14051?utm_source=chatgpt.com)

### 3. 두 모델의 비교

<figure class="wp-block-table">| 구분 | UTXO 모델 | 계정 기반 모델 |
|---|---|---|
| 잔액 표현 방식 | 개별 출력의 집합으로 잔액을 산출 [Investopedia+1](https://www.investopedia.com/terms/u/utxo.asp?utm_source=chatgpt.com) | 계정별로 직접 잔액을 기록 [blog.defichain.com](https://blog.defichain.com/2023/12/what-is-ethereums-accounts-based-model.html?utm_source=chatgpt.com) |
| 거래 구조 | 입력 → 출력, 잔액 반환(Change) 필요 | 송신계정 잔액 감소 → 수신계정 잔액 증가 |
| 병렬 처리 | 독립 UTXO 기반 처리 가능 → 확장성 유리 [kaleido.io](https://www.kaleido.io/blockchain-blog/utxo-vs-account-model?utm_source=chatgpt.com) | 계정 상태 공유 → 동시성 제약 존재 |
| 스마트컨트랙트 적합성 | 상태 중심 설계에 불리 | 상태 저장/호출 구조에 최적 |
| 프라이버시 및 분석 | 입력/출력 추적 가능하나 주소 재사용 회피 시 유리 | 계정 잔액집약으로 분석이 상대적으로 쉬움 [arXiv](https://arxiv.org/abs/2005.14051?utm_source=chatgpt.com) |
| 잔액 관리 복잡도 | 다수 UTXO 관리 필요 | 단일 잔액으로 관리 간편 |
| 설계 목적 | 가치 이전에 초점 (“전자화폐”에 가까움) | 상태 변화, 계약 실행에 초점 |

</figure>### 4. 설계적 선택이 갖는 의미

- **확장성과 처리량**: UTXO 모델은 병렬성 측면에서 유리하나, 출력 세트(UTXO set)가 커지면 검증·동기화 비용이 증가한다. [AMINA Bank](https://aminagroup.com/research/a-beginner-s-guide-to-blockchain-accounting-standards/?utm_source=chatgpt.com)
- **스마트컨트랙트 및 복합 상태 구조**: 계정 모델은 여러 계정 간 상호작용이 잦거나 계약 상태가 중요한 환경에 적합하다. [Xangle](https://xangle.io/en/research/detail/720?utm_source=chatgpt.com)
- **프라이버시 및 규제 대응**: UTXO는 주소별로 출력이 분산되어 프라이버시 설계가 가능하다. 계정 모델은 잔액과 활동이 계정 단위로 노출되기 쉽다.
- **지갑 설계 및 사용자 경험**: 일반 사용자는 계정 모델이 직관적이다. 반면 UTXO 기반 지갑은 “어떤 출력들을 사용할지” 관리하는 내부 로직이 필요하다.
- **믿음 설계의 차이**: 초창기 Bitcoin은 “인터넷 상의 디지털 현금” 개념으로 설계되어 출력 기반 모델을 도입한 반면, Ethereum은 “세계 컴퓨터” 개념으로 시작되어 계정 상태 중심 모델을 채택했다.

---

이상으로 UTXO 모델과 계정 기반 모델의 작동원리와 차이점을 구조적으로 살펴보았다. 각각의 모델은 단순히 잔액을 기록하는 방식이 아니라, 블록체인 프로토콜이 어떤 목표를 갖고 설계되었는지를 반영한다.  
아래는 핵심 요약이다.

- UTXO 모델: 개별 출력 단위로 잔액을 계산하고 병렬 처리·프라이버시 측면에서 유리.
- 계정 기반 모델: 계정 단위 잔액으로 상태를 직접 수정하며 스마트컨트랙트 및 사용자 경험 측면에서 직관적.
- 각 모델은 설계 트레이드오프(확장성 vs. 복잡성 / 프라이버시 vs. 상태관리)를 갖고 있다.

이 글을 모두 읽은 당신을 위해 **3줄 요약** 제공한다.

1. UTXO는 “사용 가능한 출력”을 연결해 잔액을 구성하는 방식이다.
2. 계정 모델은 “계정 잔액 + 상태”를 직접 기록하고 수정하는 방식이다.
3. 선택된 모델은 블록체인 설계 목표(가치이전, 스마트컨트랙트 등)에 따라 상이한 트레이드오프를 갖는다.