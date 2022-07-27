# Toss (https://docs.tosspayments.com/reference)
## 일반 결제
### 결제 api(client) - tosspay sdk 사용
```
tossPayments.requestPayment('카드', { // 결제 수단 파라미터 토스페이
  // 결제 정보 파라미터
  amount: 15000,
  orderId: 'Q6yE8tPKfEemiNwnTg6xs',
  orderName: '토스 티셔츠 외 2건',
  customerName: '박토스',
  successUrl: 'http://localhost:8080/success',
  failUrl: 'http://localhost:8080/fail',
})
```

##### [필수]
- amount(number) : 결제 금액
- orderId(string) : 상품 고유 아이디
- orderName(string) : 상품 이름
- successUrl(string) : 결제성공하면 callback url, 결제 승인 처리에 필요한 값이 query param으로 전달
    -> https://{ORIGIN}/success?paymentKey={PAYMENT_KEY}&orderId={ORDER_ID}&amount={AMOUNT}
- failUrl(string) : 결제 실패하면 calback url

##### [옵션]
- windowTarget(string) : 브라우저 결제창이 열리는 프레임 지정
    -> self/iframe
- cardCompany(string) : 카드사 코드. 값이 있으면 카드사가 고정된 상태로 결제창 열림
- cardInstallmentPlan(number) : 할부 개월수를 고정해 결제창을 열 때 사용. 5만원 이상만 가능
    -> 2~12만 가능
- maxCardInstallmentPlan(number) : 최대 할부개월수 제한
- freeInstallmentPlans(array) : 무이자 할부를 적용할 카드사/할부 개월 정보(무이자 할부비용 상점이 부담) 
    -> 해당 필드 사용시 company(string), months(array) 필수
- useCardPoint(bool) : 카드사 포인트 사용여부
    -> null : 사용자가 결정 / true : 카드사 포인트 적용상태 / false : 포인트 사용 불가
- useAppCardOnly(bool) : 카드사 앱으로 결제 여부
    -> true : 카드사의 앱카드 결제창만 사용 국민 / 농협 / 롯데 / 삼성 / 신한 / 현대 만 가능
- useInternationalCardOnly(bool) : 해외카드 결제 여부
- flowMode(string) : 특정 카드사 결제로 바로 연결
    -> DIRECT값 입력, cardCompany 값 필수
- easyPay(string) : 간편결제
    -> flowMode : DIRECT, 토스페이 / 삼성페이 / 엘페이/ 카카오페이 / 페이코 / LG페이 / SSG페이
- discountCode(string) : 카드사 할인 코드
- appScheme(string) : 모바일 ISP앱에서 상점 앱으로 돌아올 때 사용
- customerName(string) : 주문자 이름
- customerEmail(string) : 주문자 이메일 주소
- taxFreeAmount(number) : 전체 결제 금액중 면세 금액
    -> 0인 경우 전체 금액이 과세 대상
- cultureExpense(bool) : 문화비 비출 여부
    -> 결제 수단이 계좌일때만 가능

##### 결제수단 enum(한글 입력 가능)
1. 카드/CARD
2. 토스페이/TOSSPAY
3. 가상계좌/VIRTUAL_ACCOUNT
4. 계좌이체/TRANSFER
5. 휴대폰/MOBILE_PHONE
6. 문화상품권/CULTURE_GIFT_CERTIFICATE
7. 도서문화상품권/BOOK_GIFT_CERTIFICATE
8. 게임문화상품권/GAME_GIFT_CERTIFICATE

***

### 결제 승인 api(server) : https://api.tosspayments.com/v1/payments/confirm
#### req
```
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.tosspayments.com/v1/payments/confirm"))
    .header("Authorization", "Basic dGVzdF9za196WExrS0V5cE5BcldtbzUwblgzbG1lYXhZRzVSOg==")
    .header("Content-Type", "application/json")
    .method("POST", HttpRequest.BodyPublishers.ofString("{\"paymentKey\":\"s28ewVhias7V0VfylCapZ\",\"amount\":15000,\"orderId\":\"Q6yE8tPKfEemiNwnTg6xs\"}"))
    .build();
HttpResponse<String> response = HttpClient.newHttpClient().send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.body());
```

##### [필수]
- [header] Authroization : api 키
- paymentKey(string) : 결제 건에 대한 고유키
- orderId(string) : 상품 고유 ID
- amount(number) : 결제 금액

#### res
```
{
  "mId": "tosspayments",
  "version": "2022-06-08",
  "paymentKey": "Y0EwDUHpUhOauRQvzxM56",
  "status": "DONE",
  "transactionKey": "zrLENdi5v8-qeG7-kM5bR",
  "lastTransactionKey": "WVkakDJ5CAUMu8fh4MjS7",
  "orderId": "0uTcgd87KMH93kedWl5UI",
  "orderName": "토스 티셔츠 외 2건",
  "requestedAt": "2022-06-08T15:40:09+09:00",
  "approvedAt": "2022-06-08T15:40:49+09:00",
  "useEscrow": false,
  "cultureExpense": false,
  "card": {
    "company": "농협",
    "number": "123456******7890",
    "installmentPlanMonths": 0,
    "isInterestFree": false,
    "interestPayer": null,
    "approveNo": "00000000",
    "useCardPoint": false,
    "cardType": "신용",
    "ownerType": "개인",
    "acquireStatus": "READY",
    "receiptUrl": "https://dashboard.tosspayments.com/sales-slip?transactionId=KAgfjGxIqVVXDxOiSW1wUnRWBS1dszn3DKcuhpm7mQlKP0iOdgPCKmwEdYglIHX&ref=PX",
    "amount": 15000
  },
  "virtualAccount": null,
  "transfer": null,
  "mobilePhone": null,
  "giftCertificate": null,
  "cashReceipt": null,
  "discount": null,
  "cancels": null,
  "secret": null,
  "type": "NORMAL",
  "easyPay": {
    "provider": "토스페이",
    "amount": 0,
    "discountAmount": 0
  },
  "country": "KR",
  "failure": null,
  "isPartialCancelable": true,
  "receipt": {
    "url": "https://dashboard.tosspayments.com/sales-slip?transactionId=KAgfjGxIqVVXDxOiSW1wUnRWBS1dszn3DKcuhpm7mQlKP0iOdgPCKmwEdYglIHX&ref=PX"
  },
  "currency": "KRW",
  "totalAmount": 15000,
  "balanceAmount": 15000,
  "suppliedAmount": 13636,
  "vat": 1364,
  "taxFreeAmount": 0,
  "method": "간편결제"
}

```

***

## 빌링(카드 자동 결제)
### customerKey로 빌링키 발급 api : https://api.tosspayments.com/v1/billing/authorizations/card

#### req
```
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.tosspayments.com/v1/billing/authorizations/card"))
    .header("Authorization", "Basic dGVzdF9za196WExrS0V5cE5BcldtbzUwblgzbG1lYXhZRzVSOg==")
    .header("Content-Type", "application/json")
    .method("POST", HttpRequest.BodyPublishers.ofString("{\"cardNumber\":\"4330123412341234\",\"cardExpirationYear\":\"24\",\"cardExpirationMonth\":\"07\",\"cardPassword\":\"12\",\"customerIdentityNumber\":\"881212\",\"customerKey\":\"X0X1BtpZi1STrl4tnFUct\"}"))
    .build();
HttpResponse<String> response = HttpClient.newHttpClient().send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.body());
```

##### [필수]
- [header] Authroization : api key
- customerKey(string) : 주문자 구분을 위한 고유 ID
- cardNumber(string) : 카드 번호('-' 없이)
- cardExpirationYear(string) : 유효 연도(yy)
- cardExpirationMonth(string) : 유효 월(MM)
- customerIdentityNumber(string) : 카드 소유자 정보 생년월일 6자리(yyMMdd) / 사업자등록번호 10자리

##### [옵션]
- cardPassword(string) : 비밀번호 앞 두자리
- customerName(string) : 주문자 이름
- customerEmail(string) : 주문자 이메일
- vbv(object) : 해외 카드 결제시 3DS인증을 위해 사용
    - cavv(string) : 3D Secure
    - xid(string) : 트랜잭션 id
    - eci(string) : 3DS 인증 결과 코드

#### res
```
{
  "mId": "tosspayments",
  "customerKey": "o-VAJEckUlMgJ4C9AIRhX",
  "authenticatedAt": "2021-01-01T10:01:30+09:00",
  "method": "카드",
  "billingKey": "zp7jETHFq7T7Wqt_qYck_",
  "card": {
    "company": "현대",
    "number": "433012******1234",
    "cardType": "신용",
    "ownerType": "개인"
  }
}
```

***

### authKey로 빌링키 발급 api : https://api.tosspayments.com/v1/billing/authorizations/card
#### req
```
curl --request POST \
  --url https://api.tosspayments.com/v1/billing/authorizations/issue \
  --header 'Authorization: Basic dGVzdF9za196WExrS0V5cE5BcldtbzUwblgzbG1lYXhZRzVSOg==' \
  --header 'Content-Type: application/json' \
  --data '{"authKey":"e_826EDB0730790E96F116FFF3799A65DE","customerKey":"aENcQAtPdYbTjGhtQnNVj"}'
```

##### [필수]
- [header] Authroization : api key
- authKey(string) : 자동 결제 등록창 호출 성공시 리다이렉트 URL에 query param로 포함되어 돌아오는 인증 키
- customerKey(string) : 주문자 구분을 위한 고유 ID

#### res
```
{
  "mId": "tosspayments",
  "customerKey": "aENcQAtPdYbTjGhtQnNVj",
  "authenticatedAt": "2020-09-25T14:38:41+09:00",
  "method": "카드",
  "billingKey": "Z_t5vOvQxrj4499PeiJcjen28-V2RyqgYTwN44Rdzk0=",
  "card": {
    "company": "현대",
    "number": "433012******1234",
    "cardType": "신용",
    "ownerType": "개인"
  }
}
```

***

### 빌링키 결제 승인 api : https://api.tosspayments.com/v1/billing/{billingKey}
#### req
```
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.tosspayments.com/v1/billing/{billingKey}"))
    .header("Authorization", "Basic dGVzdF9za196WExrS0V5cE5BcldtbzUwblgzbG1lYXhZRzVSOg==")
    .header("Content-Type", "application/json")
    .method("POST", HttpRequest.BodyPublishers.ofString("{\"customerKey\":\"X0X1BtpZi1STrl4tnFUct\",\"amount\":15000,\"orderId\":\"h9ZUp3t0-OqhOz9NgvnV0\",\"orderName\":\"토스 티셔츠 외 2건\"}"))
    .build();
HttpResponse<String> response = HttpClient.newHttpClient().send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.body());
```

##### [필수]
- [path_param] billingKey(string) : 발급된 빌링키
- [header] Authroization : api key
- amount(number) : 결제 금액
- customerKey(string) : 주문자 고유 ID
- orderId(string) : 상품 고유 ID
- orderName(string) : 상품 이름

##### [옵션]
- customerName(string) : 주문자 이름
- customerEmail(string) : 주문자 이메일 주소
- taxFreeAmount(number) : 전체 결제 금액중 면세 금액
    -> 0인 경우 전체 금액이 과세 대상
- cultureExpense(bool) : 문화비 비출 여부
    -> 결제 수단이 계좌일때만 가능

#### res
```
{
  "mId": "tosspayments",
  "version": "1.4",
  "paymentKey": "kVfgxzjBvQfTwMMwveNw2",
  "orderId": "oJIcMamaeF4f6CsdH9hUr",
  "orderName": "토스 프라임 구독",
  "currency": "KRW",
  "method": "카드",
  "status": "DONE",
  "requestedAt": "2021-01-01T10:01:30+09:00",
  "approvedAt": "2021-01-01T10:05:40+09:00",
  "useEscrow": false,
  "cultureExpense": false,
  "card": {
    "company": "현대",
    "number": "433012******1234",
    "installmentPlanMonths": 0,
    "isInterestFree": false,
    "interestPayer": null,
    "approveNo": "00000000",
    "useCardPoint": false,
    "cardType": "신용",
    "ownerType": "개인",
    "acquireStatus": "READY",
    "receiptUrl": "https://merchants.tosspayments.com/web/serve/merchant/test_ck_D5GePWvyJnrK0W0k6q8gLzN97Eoq/receipt/kVfgxzjBvQfTwMMwveNw2"
  },
  "virtualAccount": null,
  "transfer": null,
  "mobilePhone": null,
  "giftCertificate": null,
  "foreignEasyPay": null,
  "cashReceipt": null,
  "discount": null,
  "cancels": null,
  "secret": null,
  "type": "BILLING",
  "easyPay": null,
  "country": "KR",
  "failure": null,
  "totalAmount": 4900,
  "balanceAmount": 4900,
  "suppliedAmount": 4455,
  "vat": 455,
  "taxFreeAmount": 0
}

```
