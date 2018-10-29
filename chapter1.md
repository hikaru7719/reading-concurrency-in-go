# 並行処理入門

競合状態
```
var data int

go func() {
  data ++
}
if data == 0 {
  fmt.Printf("the value is %v\n",data)
}
```

このコードはdataが0ではない時にも関わらず1と印字されてしまうことがある。

アトミック性
その処理がこれ以上分割できないという、処理の最小単位
アトミックな操作を組み合わせても必ずしもアトミックな操作ができるとは限らない

メモリアクセス同期
`sync.Mutex`を使ってLockとUnLockを明示的に行う。

## アンチパターン
- デッドロック
二つの並行処理がお互いに、Lockし合っている
- ライブロック
お互いが同じ動作をして、一歩も動けない状態
- リソース枯渇
一つの処理だけ、過剰にLockしてしまって平等に処理が行われない

並行処理を行う関数にはコメントをかく
- 誰が並行処理を担っているか
- 問題空間がどのようにプリミティブ空間に対応しているか
- 誰が同期処理を担っているか