---
title: "CarrierWaveのtips"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: true
---

# carrierwave
## versionの有無でdupの挙動に差がある
- versionありの場合、単にdupするだけだと画像がアップロードされないだけでなく、意図しないurlが返ってくる
### versionあり
```ruby
copy_instance = instace.dup
copy_instance.<column_name>.url
```
期待するurl
`/uploads/<model_name>/<column_name>/<partition_num>/<id>/<filename>`

返ってくるurl
`/uploads/<model_name>/<column_name>/<filename>`
-> しかも存在しない

### 対応方法
fileを渡す

```ruby
def dup
  double = super
  double.<column_name> = <column_name>.file
  double
end
```

## 動的に呼び出す時、sendしないとuploaderが返ってこない
sendはuploaderを返すが、[]は文字列を返す。

```
instance["<column_name>"]
#=> "filename.extention"

instance.send("<column_name>")
=> #<Uploader:0x000000000a7dbdb0 @model=#<Model id: 2040790,
```

## fogの場合s3のアップロードでコケた時リトライしない
- s3側でコケた時xmlが返ってくるがリトライしてくれない

### 対応方法
- fogからcarrierwave-awsに変更する
  内部でs3 sdkを使っているため、sdkのデフォルトのリトライ機能が働く
