# tech-battle-techfes-2023-spring

# レスポンス

## 正常

- json が返却されて、キー「data」に csv データが格納されています。
- 改行は「\n」形式です。

```
{
  "data": "姓,名,せい,めい,都道府県,市区町村,町名番地建物,緯度,経度,緯度経度ハッシュ値,年齢,性別,前職業界コード,生年月日,電話番号,希望通勤手段,希望通勤時間,予想事業所受取日給,予想支払日給,希望勤務時間,流入地区コード\n綾祐,浅井,あさい,りょうすけ,東京都,世田谷区,三軒茶屋1-1-1,35.641285,139.667989,4bd7df94a5cfa1d0f8a4bdf6e798fe40319bfecd73bc973ff38babf83d766fc4,4歳,男,A,2019-4-30,080-1111-2222,電車,60分,16100,12000,7,4\n海斗,中村,なかむら,かいと,広島県,広島市,東区矢賀町1-1-1,34.401538,132.498343,8bc4bae6730f33edcc30516b09fc476b950962ee7acde08b3b128c94d841082e,49歳,男,B,1974-4-8,080-3333-4444,自転車,20分,20000,14400,8,7\n智哉,星野,ほしの,ともや,埼玉県,川口市,坂下町1-1-1,35.829225,139.741025,0084508107dfcc5dd63bbd1d8ec3af7f5c03ca9622ebbd976e7765ed3b56d3e5,3歳,男,C,2020-6-29,080-5555-6666,バス,90分,13500,16000,5,4\n雄一,呉,くれ,ゆういち,静岡県,静岡市,駿河区桃園町1-1-1,34.930786,138.37736,d5d02408b7c9786aabff61ceb454f1e36ff16409ffc7c15fae6e5790666b71f0,99歳,男,D,1924-3-29,080-7777-8888,自転車,190分,13200,13600,6,5"
}

```

## 異常

- json が返却される。
  - errorCode：エラーコード、提出対象
  - field：エラーが発生したカラム、提出対象
  - message：エラー内容のメッセージ

```
{
  "errorCode": "TechBattleErrorCodeXXXXX",
  "field": "カラム名",
  "message": "エラー内容のメッセージ"
}
```

# crul

## 変換プログラム 1 を実行する curl コマンド

```
curl -X POST -H "Content-Type: text/plain" --data-binary @sample_data/input_1.csv http://localhost:8000/converta -o output_1.json
```

## 変換プログラム 2 を実行する curl コマンド

```
curl -X POST -H "Content-Type: text/plain" --data-binary @output_1.csv http://localhost:8000/convertb -o output_2.json
```

# API リファレンス

## API A（converta）

### 入力

以下のカラムを持つ csv

- 氏名
  - ex:浅井 綾祐
  - 名前と苗字を半角スペースで結合している
- ふりがな
  - ex:あさい りょうすけ
  - 名前と苗字を半角スペースで結合している
- 住所
  - ex:東京都世田谷区三軒茶屋 1-1-1
  - 日本の住所
- 緯度
  - ex:35.641285
  - -90〜90 の数値（小数点あり）
  - 注意：住所と完全一致していませんがテックバトルの内容とは関係ないので無視してください
- 経度
  - ex:139.667989
  - -180〜180 の数値（小数点あり）
  - 注意：住所と完全一致していませんがテックバトルの内容とは関係ないので無視してください
- 年齢
  - ex:4 歳
  - 数値+歳
- 性別
  - ex:男
  - 男、女、その他・不明　のいずれか
- 前職業界コード
  - ex:A
  - 大文字アルファベット A〜T
- 生年月日
  - ex:2019-4-30
  - 半角ハイフン区切り
- 電話番号
  - ex:080-1111-2222
  - 国内電話番号の半角ハイフン区切り
- 通勤手段
  - ex:電車
  - 電車、バス、徒歩、自転車　のいずれか
- 希望通勤時間
  - ex:60 分
  - 数値+分
  - ※60 分を超えても分表記（ex:100 分）
- 予想事業所受取時給
  - ex:2300
  - 0 以上の整数
- 予想支払時給
  - ex:1500
  - 0 以上の整数
- 希望勤務時間
  - ex:7
  - 1〜10 の整数

### 出力

- json の data 内の csv データは以下の項目を持つ

- 姓
  - ex:浅井
  - 氏名を半角スペースで分割した前半
- 名
  - ex:綾祐
  - 氏名を半角スペースで分割した後半
- せい
  - ex:あさい
  - ふりがなを半角スペースで分割した前半
- めい
  - ex:りょうすけ
  - ふりがなを半角スペースで分割した後半
- 都道府県
  - ex:埼玉県
  - 住所の都道府県の部分
- 市区町村
  - ex:川口市
  - 住所の市区町村の部分（政令指定都市は区まで）
- 町名番地建物
  - ex:坂下町 1-1-1
  - 住所の市区町村以降の値（建物や部屋番号を含む）
- 緯度
  - ex:43.06466
  - 入力値のまま
- 経度
  - ex:141.34683
  - 入力値のまま
- 緯度経度ハッシュ値
  - ex:1668a87d64939f5fd7b22c7e843898b06a70cea42f1d9b24a037a10ecfb5a742
  - 緯度と経度をそれぞれを 32 桁のハッシュ値に変換して、緯度ハッシュ値+経度ハッシュ値で結合した値
- 年齢
  - ex:20 歳
  - 入力値のまま
- 性別
  - ex:男
  - 入力値のまま
- 前職業界コード
  - ex:A
  - 入力値のまま
- 生年月日
  - ex:1990-8-25
  - 入力値のまま
- 電話番号
  - ex:070-2981-7576
  - 入力値のまま
- 希望通勤手段
  - ex:電車
  - 入力値のまま
- 希望通勤時間
  - ex:60
  - 入力値のまま
- 予想事業所受取日給
  - ex:9750
  - 予想事業所受取時給\*希望勤務時間
- 予想支払日給
  - ex:14500
  - 予想支払日給\*希望勤務時間
- 希望勤務時間
  - ex:8
  - 入力値のまま
- 流入地区コード
  - ex:1
  - 1〜8
  - 地方毎にコードが割り当てられる
    - 1:北海道
    - 2:青森県,岩手県,宮城県,秋田県,山形県,福島県
    - 3:茨城県,栃木県,群馬県,埼玉県,千葉県,東京都,神奈川県
    - 4:新潟県,富山県,石川県,福井県,山梨県,長野県,岐阜県,静岡県,愛知県
    - 5:三重県,滋賀県,京都府,大阪府,兵庫県,奈良県,和歌山県
    - 6:鳥取県,島根県,岡山県,広島県,山口県
    - 7:徳島県,香川県,愛媛県,高知県
    - 8:福岡県,佐賀県,長崎県,熊本県,大分県,宮崎県,鹿児島県,沖縄県

## API B（convertb）

### 入力

- API A の出力が入力になる（csv 形式）

### 出力

- 姓
  - ex:井上
  - 入力値のまま
- 名
  - ex:崇士
  - 入力値のまま
- せい
  - ex:いのうえ
  - 入力値のまま
- めい
  - ex:たかし
  - 入力値のまま
- 都道府県コード
  - ex:28
  - 都道府県を都道府県コードへ変更している
  - https://nlftp.mlit.go.jp/ksj/gml/codelist/PrefCd.html
- 市区町村
  - ex:神戸市東灘区
  - 入力値のまま
- 町名番地建物
  - ex:深江北町 3-4-402
  - 入力値のまま
- 緯度
  - ex:43.06466
  - 入力値のまま
- 経度
  - ex:141.34683
  - 入力値のまま
- 緯度経度ハッシュ
  - ex:****************************\*\*\*****************************5a742
  - 下 5 桁以外を\*でマスク
- 年齢
  - ex:76
  - 入力値のまま
- 性別
  - ex:男
  - 入力値のまま
- 前職業界コード
  - ex:J
  - 入力値のまま
- 生年月日（和暦）
  - ex:昭和21年7月1日
  - 和暦YY年M月D日形式に変換している
  - 1 年は元年になる
- 電話番号
  - ex:07029817576
  - 半角ハイフンを削除して数値のみに変換している
- 希望通勤手段
  - ex:電車
  - 入力値のまま
- 希望通勤時間
  - ex:1時間20分
  - 分から時分形式に変換している
- 予想事業所受取日給
  - ex:9750
  - 入力値のまま
- 予想支払日給
  - ex:14500
  - 入力値のまま
- 希望勤務時間
  - ex:5
  - 入力値のまま
- 担当者
  - ex:吉田正芳
  - 流入地区コードに紐付く担当者を出力
    - 1:岡本和也
    - 2:石井陽一
    - 3:松田伸
    - 4:中島健
    - 5:吉田正芳
    - 6:伊藤泰文
    - 7:佐藤竜介
    - 8:長島智也
- 都道府県人口
  - ex:5403823
  - 都道府県コードに紐付く人口
- 予想利益率（％）
  - ex:49
  - （予想事業所受取日給-予想支払日給）/予想事業所受取日給
  - 最後に四捨五入して整数にしている
