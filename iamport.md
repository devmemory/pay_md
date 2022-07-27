# IamPort (https://api.iamport.kr/#)
## 일반 결제
### 결제 api (client) - sdk 사용
```
IMP.request_pay({ // param
    pg: "html5_inicis",
    pay_method: "card",
    merchant_uid: "ORD20180131-0000011",
    name: "노르웨이 회전 의자",
    amount: 64900,
    buyer_email: "gildong@gmail.com",
    buyer_name: "홍길동",
    buyer_tel: "010-4242-4242",
    buyer_addr: "서울특별시 강남구 신사동",
    buyer_postcode: "01181"
}, function (rsp) { // callback
    if (rsp.success) {
        ...,
        // 결제 성공 시 로직,
        ...
    } else {
        ...,
        // 결제 실패 시 로직,
        ...
    }
});
```

##### [필수]
- pg(string) : pg사 코드값
- pay_method(string) : 결제 수단
- amount(number) : 결제 금액
- buyer_tel(string) : 주문자 연락처

##### [옵션]
- escrow(bool) : 에스크로(제삼자 중개)가 적용되는 결제창 호출 여부
- name : 주문명
- custom_data(object) : 주문건 부가 정보 저장
- tax_free(number) : 면세 공급가액
- currency(string) : 통화 (페이팔은 KRW적용 안됨)
- language(string) : 결제창 화면 언어
- buyer_name(string) : 주문자 명
- buyer_email(string) : 주문자 이메일(페이먼트월 사용시 필수)
- buyer_addr(string) : 주문자 주소
- buyer_postcode(string) : 주문자 우편번호
- confirm_url(string) : 가맹점 end point url(별도 요청 필요)
- notice_url(string / array(string)) : 알림 url
- display(object) : 결제화면 설정
    -> card_quota(array(number)) : 할부
    -> 기타 속성(입금기한, 실물/컨텐츠인지 구분, 기타 앱 관련)

##### 결제 요청 후 처리
- https://docs.iamport.kr/sdk/javascript-sdk?lang=ko#request_pay-display 참고

##### pg사 코드
- html5_inicis(이니시스웹표준)
- inicis(이니시스ActiveX결제창)
- kcp(NHN KCP)
- kcp_billing(NHN KCP 정기결제)
- uplus(토스페이먼츠(구 LG U+))
- nice(나이스페이)
- jtnet(JTNet)
- kicc(한국정보통신)
- bluewalnut(블루월넛)
- kakaopay(카카오페이)
- danal(다날휴대폰소액결제)
- danal_tpay(다날일반결제)
- mobilians(모빌리언스 휴대폰소액결제)
- chai(차이 간편결제)
- syrup(시럽페이)
- payco(페이코)
- paypal(페이팔)
- eximbay(엑심베이)
- naverpay(네이버페이-결제형)
- naverco(네이버페이-주문형)
- smilepay(스마일페이)
- alipay(알리페이)
- paymentwall(페이먼트월)
- payple(페이플)
- eximbay(엑심베이)
- tosspay(토스간편결제)
- smartro(스마트로)
- settle(세틀뱅크)

##### 결제 수단
- card(신용카드)
- trans(실시간계좌이체)
- vbank(가상계좌)
- phone(휴대폰소액결제)
- samsung(삼성페이 / 이니시스, KCP 전용)
- kpay(KPay앱 직접호출 / 이니시스 전용)
- kakaopay(카카오페이 직접호출 / 이니시스, KCP, 나이스페이먼츠 전용)
- payco(페이코 직접호출 / 이니시스, KCP 전용)
- lpay(LPAY 직접호출 / 이니시스 전용)
- ssgpay(SSG페이 직접호출 / 이니시스 전용)
- tosspay(토스간편결제 직접호출 / 이니시스 전용)
- cultureland(문화상품권 / 이니시스, 토스페이먼츠(구 LG U+), KCP 전용)
- smartculture(스마트문상 / 이니시스, 토스페이먼츠(구 LG U+), KCP 전용)
- happymoney(해피머니 / 이니시스, KCP 전용)
- booknlife(도서문화상품권 / 토스페이먼츠(구 LG U+), KCP 전용)
- point(베네피아 포인트 등 포인트 결제 / KCP 전용)
- wechat(위쳇페이 / 엑심베이 전용)
- alipay(알리페이 / 엑심베이 전용)
- unionpay(유니온페이 / 엑심베이 전용)
- tenpay(텐페이 / 엑심베이 전용)

***

## 빌링(카드 자동 결제)
### 인증 토큰 발급 api : https://api.iamport.kr/users/getToken
- 토큰은 발행시간 기준 30분 동안 사용 가능

#### req
```
const getToken = await axios({
  url: "https://api.iamport.kr/users/getToken",
  method: "post", // POST method
  headers: { "Content-Type": "application/json" }, // "Content-Type": "application/json"
  data: {
    imp_key: "imp_apikey", // REST API 키
    imp_secret: "ekKoeW8RyKuT0zgaZsUtXXTLQ4AhPFW3ZGseDA6bkA5lamv9OqDMnxyeB9wqOsuO9W3Mx9YSJ4dTqJ3f" 
    // REST API Secret
  }
});
```

##### [필수]
- imp_key(string) : REST API 키
- imp_secret(string) : REST API secret 키

#### res
```
{
  "code": 0,
  "message": null,
  "response":{
    "access_token": "a9ace025c90c0da2161075da6ddd3492a2fca776", // access token
    "now": 1512446940, // 아임포트 REST API 서버의 현재 시간
    "expired_at": 1512448740, // token의 만료 시간 (UNIX timestamp, KST 기준)
  },
}
```

***

### 빌링키 발급 요청 api : https://api.iamport.kr/subscribe/customers/${customer_uid}
#### req
```
const issueBilling = await axios({
  url: `https://api.iamport.kr/subscribe/customers/${customer_uid}`,
  method: "post",
  headers: { "Authorization": access_token }, // 인증 토큰 Authorization header에 추가
  data: {
    card_number, // 카드 번호
    expiry, // 카드 유효기간
    birth, // 생년월일
    pwd_2digit, // 카드 비밀번호 앞 두자리
  }
});
```

##### 서버에서 빌링키 직접 접근 불가로 1:1 대응되는 키를 생성해야됨
- ex) 길동_0001_xxxx(카드 뒷번호 4자리)

##### [필수]
- [path_param] customer_uid(string) : 카드와 1:1 대응되는 키(빌링키)
- [header] Authorization : access_token
- card_number(string) : 카드번호(dddd-dddd-dddd-dddd)
- expiry(string) : 카드 유효기간(yyyyMM)

##### [옵션]
- pg(string) : pg사 혹은 {pg사}.{pg상점아이디}
- customer_id(string) : 구매자 식별 고유 번호
- birth(string) : 생년월일
- pwd_2digit(string) : 카드 비밀번호 앞 두자리
- cvc(string) : 인증번호
- customer_name(string) : 주문자 이름
- customer_tel(string) : 주문자 폰 번호
- customer_email(string) : 주문자 이메일
- customer_addr(string) : 주문자 주소
- customer_postcode(string) : 주문자 우편번호

#### res
```
{
  "code": 0,
  "message": "string",
  "response": {
    "customer_uid": "string",
    "pg_provider": "string",
    "pg_id": "string",
    "customer_id": "string",
    "card_name": "string",
    "card_code": "string",
    "card_number": "string",
    "card_type": "null",
    "customer_name": "string",
    "customer_tel": "string",
    "customer_email": "string",
    "customer_addr": "string",
    "customer_postcode": "string",
    "inserted": 0,
    "updated": 0
  }
}
```

***

### 결제(재결제) 요청 api : https://api.iamport.kr/subscribe/payments/again
#### req
```
const paymentResult = await axios({
  url: `https://api.iamport.kr/subscribe/payments/again`,
  method: "post",
  headers: { "Authorization": access_token }, // 인증 토큰을 Authorization header에 추가
  data: {
    customer_uid,
    merchant_uid: "order_monthly_0001", // 새로 생성한 결제(재결제)용 주문 번호
    amount: 8900,
    name: "월간 이용권 정기결제"
  }
});
```

##### [필수]
- [header] : access_token
- customer_uid(string) : 카드와 1:1 대응되는 키(빌링키)
- merchant_uid(string) : 결제 주문번호
- amount(double) : 결제 금액
- name(string) : 주문명

##### [옵션]
- currency(string) : 통화
- tax_free(double) : 면세 공급가액 기본값 0 (모두 과세 대상)
- buyer_name(string) : 주문자 이름
- buyer_email(string) : 주문자 이메일
- buyer_tel(string) : 주문자 폰 번호('-' 없이)
- buyer_addr(string) : 주문자 주소
- buyer_post(string) : 주문자 우편번호
- carf_quota(int) : 할부 개월수 2개월 이상/5만원 이상
- interest_free_by_merchant(bool) : 할부이자 (가맹점이 지급시 true)
- use_card_point(bool) : 카드 포인트 결제 승인 여부
- custom_data(string) : 거래정보에 관한 추가 정보
- notice_url(string) : 결제성공시 통지될 notification url
- browser_ip(string) : 구매자 브라우저 ip (페이먼트월 필수)

#### res
```
{
  "code": 0,
  "message": "string",
  "response": {
    "imp_uid": "string",
    "merchant_uid": "string",
    "pay_method": "string",
    "channel": "pc",
    "pg_provider": "string",
    "emb_pg_provider": "string",
    "pg_tid": "string",
    "pg_id": "string",
    "escrow": true,
    "apply_num": "string",
    "bank_code": "string",
    "bank_name": "string",
    "card_code": "string",
    "card_name": "string",
    "card_quota": 0,
    "card_number": "string",
    "card_type": "null",
    "vbank_code": "string",
    "vbank_name": "string",
    "vbank_num": "string",
    "vbank_holder": "string",
    "vbank_date": 0,
    "vbank_issued_at": 0,
    "name": "string",
    "amount": 0,
    "cancel_amount": 0,
    "currency": "string",
    "buyer_name": "string",
    "buyer_email": "string",
    "buyer_tel": "string",
    "buyer_addr": "string",
    "buyer_postcode": "string",
    "custom_data": "string",
    "user_agent": "string",
    "status": "ready",
    "started_at": 0,
    "paid_at": 0,
    "failed_at": 0,
    "cancelled_at": 0,
    "fail_reason": "string",
    "cancel_reason": "string",
    "receipt_url": "string",
    "cancel_history": [
      {
        "pg_tid": "string",
        "amount": 0,
        "cancelled_at": 0,
        "reason": "string",
        "receipt_url": "string"
      }
    ],
    "cancel_receipt_urls": [
      "string"
    ],
    "cash_receipt_issued": true,
    "customer_uid": "string",
    "customer_uid_usage": "issue"
  }
}
```

***

### 결제 예약 api : https://api.iamport.kr/subscribe/payments/schedule
- 복수 예약 등록 가능
#### req
```
axios({
  url: `https://api.iamport.kr/subscribe/payments/schedule`,
  method: "post",
  headers: { "Authorization": access_token }, // 인증 토큰 Authorization header에 추가
  data: {
    customer_uid: "gildong_0001_1234", // 카드(빌링키)와 1:1로 대응하는 값
    schedules: [
      {
        merchant_uid: "order_monthly_0001", // 주문 번호
        schedule_at: 1519862400, // 결제 시도 시각 in Unix Time Stamp. 예: 다음 달 1일
        amount: 8900,
        name: "월간 이용권 정기결제",
        buyer_name: "홍길동",
        buyer_tel: "01012345678",
        buyer_email: "gildong@gmail.com"
      }
    ]
  }
});
```

##### [필수]
- [header] : access_token
- customer_uid(string) : 카드와 1:1 대응되는 키(빌링키)
- schedules(object)
    - merchant_uid(string) : 결제 주문번호
    - schedule_at(number) : 결제 시각 (Unix Time Stamp 값으로 전달)
    - currency(string) : 통화
    - amount(number) : 결제 금액
    --- 이하 [옵션]
    - tax_free(number) : 면세공급가액(기본값 : 0)
    - name(string) : 주문명(누락되면 아임포트 자체 기본값 사용)
    - buyer_name(string) : 주문자 이름
    - buyer_email(string) : 주문자 이메일
    - buyer_tel(string) : 주문자 전화번호
    - buyer_addr(string) : 주문자 주소
    - buyer_postcode(string) : 주문자 우편번호
    - custom_data(string) : 결제수행 시 사용될 custom_data 값
    - notice_url(string) : 예약결제수행 후 통지될 Notification URL
    - extra.naverUseCfm(string) : 이용완료일 yyyyMMdd (네이버페이 반복결제 필수)

##### [옵션]
- customer_id(string) : 주문자 고유 식별번호
- checking_amount(int) : 카드정상결제 여부 체크용 금액(0원일 경우 테스트 안함)
- card_number(string) : 카드 번호(dddd-dddd-dddd-dddd)
- expiry(string) : 카드 유효기간(yyyy-MM)
- birth(string) : 생년월일 6자리 / 법인카드 사업자 등록번호 10자리
- pwd_2digit(string) : 카드비밀번호 앞 2자리
- cvc(string) : 카드 인증번호
- pg(string) : pg사 / {pg사}.{pg상점아이디}

#### res
```
{
  "code": 0,
  "message": "string",
  "response": [
    {
      "customer_uid": "string",
      "merchant_uid": "string",
      "imp_uid": "string",
      "customer_id": "string",
      "schedule_at": "0",
      "executed_at": "0",
      "revoked_at": "0",
      "amount": 0,
      "name": "string",
      "buyer_name": "string",
      "buyer_email": "string",
      "buyer_tel": "string",
      "buyer_addr": "string",
      "buyer_postcode": "string",
      "custom_data": "string",
      "schedule_status": "scheduled",
      "payment_status": "paid",
      "fail_reason": "string"
    }
  ]
}
```

***

### 결제 결과 저장 api : https://api.iamport.kr/payments/${imp_uid}
- 예약 결제 완료 후 imp_uid(결제번호),merchant_uid(주문번호) 전달

#### req
```
const getPaymentData = await axios({
  url: `https://api.iamport.kr/payments/${imp_uid}`, // imp_uid 전달
  method: "get", // GET method
  headers: { "Authorization": access_token } // 인증 토큰 Authorization header에 추가
});
```

##### [필수]
- imp_uid(string) : 결제 번호

#### res
```
{
  "code": 0,
  "message": "string",
  "response": {
    "imp_uid": "string",
    "merchant_uid": "string",
    "pay_method": "string",
    "channel": "pc",
    "pg_provider": "string",
    "emb_pg_provider": "string",
    "pg_tid": "string",
    "pg_id": "string",
    "escrow": true,
    "apply_num": "string",
    "bank_code": "string",
    "bank_name": "string",
    "card_code": "string",
    "card_name": "string",
    "card_quota": 0,
    "card_number": "string",
    "card_type": "null",
    "vbank_code": "string",
    "vbank_name": "string",
    "vbank_num": "string",
    "vbank_holder": "string",
    "vbank_date": 0,
    "vbank_issued_at": 0,
    "name": "string",
    "amount": 0,
    "cancel_amount": 0,
    "currency": "string",
    "buyer_name": "string",
    "buyer_email": "string",
    "buyer_tel": "string",
    "buyer_addr": "string",
    "buyer_postcode": "string",
    "custom_data": "string",
    "user_agent": "string",
    "status": "ready",
    "started_at": 0,
    "paid_at": 0,
    "failed_at": 0,
    "cancelled_at": 0,
    "fail_reason": "string",
    "cancel_reason": "string",
    "receipt_url": "string",
    "cancel_history": [
      {
        "pg_tid": "string",
        "amount": 0,
        "cancelled_at": 0,
        "reason": "string",
        "receipt_url": "string"
      }
    ],
    "cancel_receipt_urls": [
      "string"
    ],
    "cash_receipt_issued": true,
    "customer_uid": "string",
    "customer_uid_usage": "issue"
  }
}
```

![schedule](https://user-images.githubusercontent.com/71013471/181182783-470627f2-2b4a-4982-888c-d8aa45bdd9e8.svg)
