# 인앱 구매 인증
 
## 설치

```sh
npm i jaemyeongson-node-iap
```

## 사용법

```javascript
var iap = require('iap');

var platform = 'apple';
var payment = {
	receipt: 'receipt data', // always required
	productId: 'abc',
	packageName: 'my.app',
	secret: 'password',
	subscription: true,	// optional, if google play subscription
	keyObject: {}, // required, if google
	userId: 'user id', // required, if amazon
	devToken: 'developer id' // required, if roku
};
```

### 구매 인증 ( 모든 플랫폼 )

구매 영수증을 확인하는 방법이 노출됩니다.

```javascript
iap.verifyPayment(platform, payment, function (error, response) {
	/* your code */
});
```

promise-based 방식을 선호하는 경우 : 

```javascript
iap.verifyPayment(platform, payment)
.then(
    response => {	
        /* your code */ 
    },
    error => {
        /* your code */ 
    }
)
```

전달하는 영수증은 확인하려는 백엔드의 요구 사항을 준수해야 합니다.
형식에 대한 자세한 내용은 아래를 참조해주세요.

### 구독 취소(Google Play만 해당)

Google은 반복 구독의 [서버 측 취소를 위한 API](https://developers.google.com/android-publisher/api-ref/purchases/subscriptions/cancel)를 노출합니다. 이것은
사용자가 앱 내에서 구독을 쉽게 취소할 수 있도록 하는 데 사용됩니다(Play 스토어로 ) 또는 사용자 요청 시 지원 담당자가 구독을 취소하도록 허용

```javascript
iap.cancelSubscription("google", payment, function (error, response) {
	/* your code */
});
```

promise-based 방식을 선호하는 경우: 

```javascript
iap.cancelSubscription(platform, payment)
.then(
    response => {    
        /* your code */ 
    },
    error => {
        /* your code */ 
    }
)
```

## 지원 

### Apple

**The payment object**

전달된 영수증 문자열은 Apple이 실제로 원하는 base64 문자열이거나 디코딩된 문자열일 수 있습니다.
iOS SDK에서 반환한 영수증(이 경우 자동으로 base64 직렬화됨).

productId 및 packageName(번들 ID)은 모두 선택 사항이지만 제공된 경우 테스트됩니다.
영수증이 제공된 값과 일치하지 않으면 오류가 반환됩니다.

자동 갱신 구독을 확인하려면 '비밀' 필드를 제공해야 합니다.
인앱 구매 공유 비밀을 포함합니다.

Apple은 [exclude-old-transactions](https://developer.apple.com/library/content/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) 옵션을 통해 자동 갱신 구독에 대한 가장 최근 트랜잭션만 반환하도록 지원합니다. . 이것은 둘 이상의 트랜잭션이 있는 사용자의 대역폭을 크게 절약할 수 있습니다. 이를 활성화하려면 지불 객체에 `excludeOldTransactions`를 전달하십시오.:

```javascript
let payment = {
  ...
  excludeOldTransactions: true
}
```

**The response**

콜백으로 다시 전달되는 응답도 Apple에 따라 다릅니다. 파싱된 전체 영수증
결과 개체에 있습니다. 월간 및 연간을 지원하는 애플리케이션
구독 액세스는 `in_app` 또는 `latestReceiptInfo` 속성.
```json
{
	"receipt": {
		"original_purchase_date_pst": "2016-10-29 15:46:57 America/Los_Angeles",
		"purchase_date_ms": "1477802802000",
		"unique_identifier": "78abf2209323434771637ee22f0ee8b8341f14b4",
		"original_transaction_id": "120000257973875",
		"bvrs": "0.0.1",
		"transaction_id": "120000265421254",
		"quantity": "1",
		"unique_vendor_identifier": "206FED24-2EAB-4FC6-B946-4AF61086DF21",
		"item_id": "820817285",
		"product_id": "abc",
		"purchase_date": "2016-10-29 22:46:57 Etc/GMT",
		"original_purchase_date": "2016-10-29 22:46:57 Etc/GMT",
		"purchase_date_pst": "2016-10-29 15:46:57 America/Los_Angeles",
		"bid": "test.myapp",
		"original_purchase_date_ms": "1477781217000",
		"in_app": [
			{
				"quantity": "1",
				"product_id": "abc",
				"transaction_id": "120000265421254",
				"original_transaction_id": "120000257973875",
				"purchase_date": "2016-10-30 04:46:42 Etc/GMT",
				"purchase_date_ms": "1477802802000",
				"purchase_date_pst": "2016-10-29 21:46:42 America/Los_Angeles",
				"original_purchase_date": "2016-10-29 22:46:57 Etc/GMT",
				"original_purchase_date_ms": "1477781217000",
				"original_purchase_date_pst": "2016-10-29 15:46:57 America/Los_Angeles",
				"expires_date": "2016-11-30 05:46:42 Etc/GMT",
				"expires_date_ms": "1480484802000",
				"expires_date_pst": "2016-11-29 21:46:42 America/Los_Angeles",
				"web_order_line_item_id": "820817285",
				"is_trial_period": "false"
			},
		]
	},
	"latestReceiptInfo": [
		{
			"quantity": "1",
			"product_id": "abc",
			"transaction_id": "120000233230473",
			"original_transaction_id": "120000233230473",
			"purchase_date": "2016-06-12 22:36:58 Etc/GMT",
			"purchase_date_ms": "1465771018000",
			"purchase_date_pst": "2016-06-12 15:36:58 America/Los_Angeles",
			"original_purchase_date": "2016-06-12 22:36:58 Etc/GMT",
			"original_purchase_date_ms": "1465771018000",
			"original_purchase_date_pst": "2016-06-12 15:36:58 America/Los_Angeles",
			"expires_date": "2016-07-12 22:36:58 Etc/GMT",
			"expires_date_ms": "1468363018000",
			"expires_date_pst": "2016-07-12 15:36:58 America/Los_Angeles",
			"web_order_line_item_id": "120000034778618",
			"is_trial_period": "true"
		},
	],
	"pendingRenewalInfo": [
		{
		  "expiration_intent": "1",
 			"auto_renew_product_id": "abc",
			"original_transaction_id": "120000233230473",
			"is_in_billing_retry_period": "0",
			"product_id": "abc",
			"auto_renew_status": "0"
		}
	],
	"transactionId": "120000233230473",
	"productId": "abc",
	"platform": "apple",
	"environment": "production"
}
```

### Google Play

**The payment object**

영수증 문자열은 구매 시 Google Play가 모바일 애플리케이션에 반환하는 구매 토큰입니다.

packageName과 productId는 모두 필수입니다.

마지막으로 Google Play에 연결된 Google API 서비스 계정 JSON 키 파일인 `keyObject`를 제공해야 합니다.
인증을 위한 계정. 이 속성은 문자열, 파일 버퍼 또는 개체일 수 있습니다. 문자열이나 파일이 제공된 경우
버퍼, 호출은 자동으로 사용할 개체로 구문 분석합니다.

**응답**

콜백으로 다시 전달되는 응답도 Google Play에 따라 달라집니다. 구문 분석된 전체 응답은
영수증 하위 객체.

```json
{
        "receipt": {
                "kind": "androidpublisher#productPurchase",
                "purchaseTimeMillis": "1410835105408",
                "purchaseState": 0,
                "consumptionState": 1,
                "developerPayload": ""
        },
        "transactionId": "ghbbkjheodjokkipdmlkjajn.AO-J1OwfrtpJd2fkzzZqv7i107yPmaUD9Vauf9g5evoqbIVzdOGYyJTSEMhSTGFkCOzGtWccxe17dtbS1c16M2OryJZPJ3z-eYhEJYiSLHxEZLnUJ8yfBmI",
        "productId": "abc",
        "platform": "google"
}
```

## License

MIT
