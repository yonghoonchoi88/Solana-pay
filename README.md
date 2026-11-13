# Solana Pay — SPL Token Transaction Request

**한국어** · [English](#english)

Solana Pay의 **Transaction Request** 방식으로 SPL 토큰(USDC) 결제 트랜잭션을 서버에서 만들어 지갑에 돌려주는 흐름을 공부하며 작성한 코드입니다. (2022)

Solana Pay 공식 문서의 Transaction Request 예제를 따라가며, 각 단계에서 무엇을 검증해야 하는지 정리하는 데 초점을 두었습니다.

## 결제 흐름

1. 고객이 지갑으로 결제 QR을 스캔하면, 지갑이 가맹점 서버에 **POST** 요청으로 자신의 계정(`account`)을 보냅니다.
2. 서버가 **SPL 토큰 전송 명령**을 만들고, 서명하지 않은 트랜잭션을 base64로 직렬화해 돌려줍니다.
3. 지갑이 트랜잭션을 보여주고, 고객이 서명하면 네트워크로 전송됩니다.

## 파일

| 파일 | 내용 |
|---|---|
| `Request.js` | POST 핸들러 — 요청의 `account` 확인, 트랜잭션 생성 · 직렬화, 응답 메시지 반환 |
| `Respond.js` | `createSplTransferIx()` — SPL 전송 명령 생성과 검증 |

## 서버에서 확인하는 것

- 보내는 사람과 가맹점의 **토큰 계정(ATA)이 존재하고 초기화되어 있으며, 동결되지 않았는지**
- 토큰 **mint가 초기화되어 있는지**, 소수 자릿수(`decimals`) 맞추기
- 결제 금액은 클라이언트가 조작할 수 없도록 **서버에서 계산**
- 보내는 사람의 **잔액이 충분한지**
- `createTransferCheckedInstruction`으로 mint와 decimals가 일치하는지 체인에서 한 번 더 확인
- 결제 세션마다 고유한 **reference 키**를 붙여 나중에 트랜잭션을 찾을 수 있게 함

## 참고

- 학습용 코드 조각이라 이 저장소만으로는 바로 실행되지 않습니다. `connection`, `calculateCheckoutAmount()`, 환경 변수(`USDC_MINT`, `MERCHANT_WALLET`)는 실제 서버에서 따로 정의해야 합니다.
- 사용 라이브러리: `@solana/web3.js` · `@solana/spl-token` · `@solana/pay` · `bignumber.js`
- 참고 문서: [Solana Pay 공식 문서](https://docs.solanapay.com/)

---

<a id="english"></a>

## English

Study code (2022) for building an **SPL token (USDC) payment** with the Solana Pay **Transaction Request** flow: the merchant server builds the transaction and hands it back to the wallet to sign. It follows the transaction request example in the Solana Pay docs, with a focus on what the server must verify at each step.

**Flow** — the wallet POSTs the customer's `account` to the merchant server → the server builds an SPL transfer instruction and returns the unsigned, base64-serialized transaction → the customer reviews and signs it in the wallet.

**Files** — `Request.js`: POST handler (reads `account`, builds and serializes the transaction). `Respond.js`: `createSplTransferIx()`, which builds and validates the SPL transfer.

**Server-side checks** — sender and merchant token accounts exist, are initialized and not frozen · the mint is initialized · the amount is calculated on the server, not the client · the sender has enough balance · `createTransferCheckedInstruction` asserts mint and decimals · a unique reference key per checkout.

**Note** — this is a study snippet and does not run on its own: `connection`, `calculateCheckoutAmount()` and the `USDC_MINT` / `MERCHANT_WALLET` environment variables must be defined by the host server. Libraries: `@solana/web3.js`, `@solana/spl-token`, `@solana/pay`, `bignumber.js`. Reference: [Solana Pay docs](https://docs.solanapay.com/).
