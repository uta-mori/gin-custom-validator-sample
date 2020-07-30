# gin-custom-validator-sample

## リクエスト

```shell
$ curl "localhost:8085/bookable?check_in=2018-04-16&check_out=2018-04-17"
{"message":"Booking dates are valid!"}

$ curl "localhost:8085/bookable?check_in=2018-03-10&check_out=2018-03-09"
{"error":"Key: 'Booking.CheckOut' Error:Field validation for 'CheckOut' failed on the 'gtfield' tag"}
```

## RegisterValidation の定義

第一引数にタグ名、第二引数に`Func`を取ります。


```go
// RegisterValidationは、与えられたタグでの検証を追加します。
//
// 注：
// - キーが既に存在する場合は、以前の検証関数が置き換えられます。
// - このメソッドはスレッドセーフではありません。これらはすべて、検証の前に登録されていることが意図されています。
func (v *Validate) RegisterValidation(tag string, fn Func, callValidationEvenIfNull ...bool) error {
	return v.RegisterValidationCtx(tag, wrapFunc(fn), callValidationEvenIfNull...)
}
```

ちなみに`Func`の定義はこう  
フィールドレベルを受け取って、検証の可否を返す。

```go
// Func は、すべてのバリデーションのニーズを満たすためにFieldLevel インターフェイスを受け入れます。バリデーションが成功した場合、戻り値はtrueでなければなりません。
type Func func(fl FieldLevel) bool
```
