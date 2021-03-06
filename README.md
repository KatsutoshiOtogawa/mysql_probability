# mysql_probability

## DB側にビジネスロジックを寄せる理由

1. api server, web server, batch serverとビジネスロジックをバラバラに実装しなくてすむ。

    特にWeb serverからすべてロジックをapiサーバーに移そうというフレーズのとき、既存のコードと新規のapi serverと両方にビジネスロジックを管理するのが辛い。のとスマホとかのアプリは他社に請負で投げていて、

2. DBはソフトウェアとしてかなり堅牢なので、落ちづらい

    api serverがよくわからんけど落ちるとかWebサーバーが落ちるってよくあると思うんですが、 \
    DBサーバーはそれらに比べて落ちづらい。
    オンプレで最初の設計ミスってない限り。
    DBサーバーってメモリの量が結構重要で、
    SQLを実行するときにオプティマイザっていう構文パーサーがプログラミング言語に変更して、それを実行することにより検索がかかるのですが、その吐き出したCacheを保存しておくのがPGA領域なので、2回目以降は早くなります。遅いとかいわれて何回かSQL実行したら早くなるっていうのはこういう作りのためです。よく使われる検索部分が思いなら単純にメモリの量をあげるのが一番簡単に速度をあげれます。OracleのSilverの本に結構
    PGAとか結構詳しく書かれていて、ほかのRDBMSでもOracleと作りは違うけど、
    似たような構成になっているものが多いから、一度OracleのSilverとったほうがいいですよ。

3. オンプレ以外ならDBサーバーはマネージドなサービスが多いので、スペックが足りなかったらアップアップデートが容易。

    Cloud 以外でもメジャーなVPSはマネージドなDBが多い。

4. 実行計画により時間を見積やすい。

    CPU的なコストとか。プログラミング言語側に実装すると時間にしろ
    CPU的なコストを見積もるのって結構面倒臭いけど、
    ただしmysql は実行計画わかりづらい。

5. 月々の固定コスト増やす必要がない。

6. プログラミング言語側よりメモリの使い方が賢いので早い。
   pythonの計算用のライブラリのnumpyとかpandasとかその他機械学習や統計のライブラリが早いのはpythonにC言語
   のやつをくっつけているから早いだけで、python自体はスクリプト言語の中では早いけどというレベル。
   M1Macがまだ使っていない人がいるのはここら辺のCPUの問題が解決できてないというのが問題だったり。
   Vmwareとか仮想環境でWindowsも使えないし。とか。

## デメリットとしては

    管理する側の話になるんですが。

1. C言語なみにスキル差が出る。

    集合的な考え方が必要になる。
    そもそもSQLかけてない人が多すぎる１０人に一人ぐらいしかかけてない。
    

2. テスト、デバッグが難しい。
    ログとるのが難しかったり。DBの中にあるからとり方が結構雑になる。
    Log Table作ってテキスト型で入れてというのもあり(割とOracleのplsql環境だとこれやっている。)。
3. エディタのインテリセンスが効かない。
   MYSQL WorkBenchでもjson型のインテリセンス効かなかったり

## じゃあどういうロジックを入れるか。

ここら辺のデメリットを消すために、以下の方針を徹底するとよい。

1. 計算、一時的なデータの加工のみ。　CRUD処理はしない。
   
    手続き的な処理はしない。
    あんま考えなくても冪等性を確保するのが容易。

2. 落ちてもいい処理のみ

    上とダブるが

3. 計算用テーブルにデータ挿入とか。一時テーブルにデータを入れて、データを見せるとか

    割とWhere句のINに1000個とか具体的に指定して絞りこんでとか。
    外部の決済代理にせよ、なんかの代行のやつは結果をcsvでファイルを送りつけてくる所が多いと思います。

    これそもそも自動化すべきやつで、一度一時テーブルに入れるなり、計算用テーブルに入れて、値をチェックして
    それで大丈夫ならそのまま突き合わせてCREATEなりUPDATEなりするという設計にすべき。

    Oracleだったら、そもそもそれは設計的におかしいし、そんな長いやつをサポートしたくないから、
    1000行までだったか、1000個まで指定


## 確率統計

確率統計のための関数がMysqlにないので、
実装する。

基本的な関数。

## 順列

```sql
CREATE

```

## 階乗

```sql
-- 階乗の一部のパターンという形で実装できる。
```

## 組み合わせ

```sql
-- 階乗と順列で実装できる。

```

## ジニ係数

平均さ

## 二項分布に従う確率変数



## ポアソン分布に従う確率変数



## 二項分布


## ポアソン分布

例えば、ABテストをDBで計算したい。
Tableにいれることにより、データ入れた後に指定の日時に計算して、
csvをだすとか。

二項分布を計算したい。
Oracleには統計用の関数がかなり豊富に実装されているが、
SqlServerにもないですね。
PostgresqlはExtensionに統計関係のがかなりあるので、
それを使ったらいい。
Mysqlにはそういう便利なのがないので、自分で実装しましょう。
今年に本出せたらいいなと思っているんですけど、そこに
C言語での実装方もかけたらいいなと思っています。

そういうのはC言語でユーザー定義の集計関数を使えっていうことなのかもしれません。


## 

1. 



