Goで並行処理を制御するにはsyncパッケージの関数を使うか、
チャネル&selectを使って制御する方法の２種類ある。

1つのgoroutineのメモリ消費量はデフォルトでおよそ2kb。OSのスレッドでは2MB消費するので
およそ1/1000違う。メッセージの受け渡しも非常に早い。

- sync.WaitGroup型
Add関数でgortutineの数をカウントし、Doneで一つのgorutineの数をデカウントする。
waitgroupの数が０になるまで、Wait関数はブロックする。

- MutexとRWMutex
RWMutexは読み込みのLock、書き込みのLockを別のものとする考え方。

- Once
一度だけしかWaitGroupを実行しないようにする。

- Pool
特定のメモリ量だけしか消費したくない時などに、事前に消費量を決定しておくことができる。
またstaticな関数などを事前に準備しておくことで、実行時の呼び出しを早くすることもできる。

チャネル
読み込みチャネルは空の状態の時にブロックし、書き込みチャネルは満杯の状態の時に処理をブロックする。
この仕組みによってgoroutineが呼び出されない状態を回避する。

Close関数を使ってチャネルを閉じることによって、読み込みの終了時点を決めることができる。
読み込み専用チャネルはfor文のrangeを使って制御することもできる。

複数のgoroutineで一つのチャネルを使っているときに、globalなところで使用しているチャネルをクローズすることで
全てのgoroutineが終了できる。

キャパシティが決まったバッファつきチャネルというものもある。
キューのようなもの。

チャネルの所有者が気をつけなくてはならないところ
- チャネルを初期化する
- 書き込みを行うか、他のgoroutineに所有権を渡す
- チャネルを閉じる
- 上の３つをカプセル化して読み込みチャネルを経由して、後悔する

チャネルの消費者が気をつけいなくてはならないところ
- チャネルがいつとじられたかを把握する
- いかなる理由でもブロックする動作は責任を持ってあつかう

select文
複数のチャネルの動作を制御する文法
```aidl
select{
case <-chan:
  処理
}
```
の形式でかく。defaultもある。caseが複数ある場合には一様分布によって同じ回数だけ選択されるようなアルゴリズムになっている。
ずっとブロックしている可能性がある場合はタイムアウトの設定をすることができる。