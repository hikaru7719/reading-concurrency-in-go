go routineでは変更を加えたい値のコピーを行い、
それに対して操作を行えば、並行処理における不整合は起こらない。

for selectループ

```
for {
    select{
    case <- done :
        return
    default:
    }
    /*処理*/
}
```
doneチャンネルが閉じられるまで無限に処理を行うやつ。
ゴルーチンリークはしっかりとdoneチャンネルを使って終了させる。
defer文等を使ってgoroutineを生成したプロセスでdoneチャネルを閉じる。

orチャネル
orチャネルは再帰的な処理を行う関数を用いてチャネルの処理を行う。

エラーハンドリング
エラーをラップした構造体をチャネルに渡して、チャネルの受け取り手がエラーを処理するという方法。

goroutineはストリームのような処理に向いている。
ジェネレータ
```go
repeat := func(done <-chan interface{}, values ...interface{}) <-chan interface{} {
	valueStream := make(chan interface{})
	go func() {
		defer close(valueStream)
		for {
			for _, n := range values{
				select {
				case <- done:
					return 
				case valueStream <- v:
				}
			}
		}
	}()
	return valueStream
}
```
take関数
```go
take := func(done <- chan interface{}, valueStream <- chan interface{},num int) <- chan interface{}{
	takeSream := make(chan interface{})
	go func() {
		defer close(takeStream)
		for i := 0; i < num; i++ {
			select{
			case <- done:
				return
			case takeStream <- <- valueStream:
			}
		}
	}()
	return takeStream
}
```

バッファを持たせることで処理が溜まっていても処理がなされないということを回避することができる。

Context型はdoneチャネルとしての役割を持ちつつ、キャンセル処理やタイムアウト処理を行う。
また、Context型に値を保持して次のgoroutineに渡すこともできる。