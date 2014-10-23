s3cp
====

[![Build Status](https://drone.io/github.com/masahide/s3cp/status.png)](https://drone.io/github.com/masahide/s3cp/latest)

* S3へのrsyncのようにディレクトリ丸ごとアップロードします。
* アップロードの際、並列で複数のファイルを同時にアップロードすることが可能です。
* 既にアップロード済みのファイルがある場合は、ファイルサイズもしくはmd5sum(オプションで指定可)で検証し、異なる場合は上書きでアップロードします。

Download
--------

https://drone.io/github.com/masahide/s3cp/files


注意
----

* 現状ACLは固定ですべてprivateになります。
* S3からのダウンロード機能は未実装です。
* シンボリックリンクは追跡します。(循環参照無限ループを回避するため、symlinkは20階層でストップします)
* Windowsはまだ未対応




環境変数
--------

実行前にAWSのAccess keyとSecret access keyを環境変数にセットする必要があります。

```bash:
export AWS_ACCESS_KEY_ID="hoge"
export AWS_SECRET_ACCESS_KEY="fuga"
````

使い方
------

単一ファイルのアップロードの場合:

```bash:
$ s3cp [options] <ローカルのファイルパス> <バケット名> <アップロード先S3のディレクトリパス>
$ s3cp [options] <ローカルのファイルパス> <バケット名> <アップロードS3のファイル名(フルパス)>
```

ディレクトリの丸ごとアップロードの場合

```
$ s3cp -r [options] <ローカルのディレクトリパス> <バケット名> <S3のディレクトリパス>
```

### 例:

```
$ s3cp hoge.html test-bucket html/
```
`test-bucket`バケットに `html/hoge.html`として保存されます

```
$ s3cp hoge.html test-bucket html/fuga.html
```
`test-bucket`バケットに `html/fuga.html`として保存されます


```
$ s3cp -r /var/tmp/piyo test-bucket html/fuge
```
`/var/tmp/piyo`ディレクトリを `test-bucket`バケットの `html/fuge/` ディレクトリとしてコピーします



### options:

 *  -r
   *  ディレクトリコピーモード
 * -checkmd5=false:
   * 同名のファイルが既に存在する場合にMD5sumを検証し、異なる場合のみ上書
 * -checksize=true:
   * 同名のファイルが既に存在する場合にファイルサイズを検証し、異なる場合のみ上書
 * -n=1:
   * 並列アップロードする数(デフォルト:1)
 * -region=ap-northeast-1:
   * 対象リージョンの指定
 *  -jsonLog
   * 出力形式をjsonに
 *  -version
   * versionの表示
 *  -d=0: log level
   * ログ出力レベルの指定。0から5までで5が最大限に情報を出力します
 * 以下はリトライのルールを設定します
   *  -RetryInitialInterval=500: Retry Initial Interval (Millisecond)
   *  -RetryMaxElapsedTime=15: Retry Max Elapsed Time (Minute)
   *  -RetryMaxInterval=60: Retry Max Interval (Second)
   *  -RetryMultiplier=1.5: Retry Multiplier
   *  -RetryRandomizationFactor=0.5: Retry Randomization Factor

