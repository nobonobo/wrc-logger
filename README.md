# wrc-logger

「EA Sports WRC」向けのペースノートロガー兼ペースノート読み上げツール

## WRCテレメトリについて

このツールはWRCが出力できるテレメトリUDPパケットを受けることで動作します。あらかじめ、UDPパケット出力を有効にしておいてください。

このツールはデフォルトのリッスンポートは「127.0.0.1:20777」です。コマンドラインオプションで変更できます。

「wrc-logger.exe -listen=127.0.0.1:20778」

また、同じパケット内容を転送する機能もあります。既存で同じパケットを利用している場合は以下のように転送オプションを使ってください。

「wrc-logger.exe -forward="192.168.1.1:20777"」

## ロギングの仕掛けについて

「-logging」オプションをつけるとOSDのペースノートを読み取ってログを記録する機能が有効になります。

- OBSでゲーム画面をフルHD（1920x1080）で取り込み、「仮想カメラ」にて出力
- このツールのロギングを有効にしたら「仮想カメラ」出力のコドライバーOSD表示のマークを画像解析してログを記録します
- ツールのlogフォルダ配下には「ステージ長.log」というファイルが記録されます
- もし既存のファイルがあれば「ステージ長.log.1」「ステージ長.log.2」と番号が付与されて残ります

「wrc-logger.exe -logging -camera 3」というオプションで仮想カメラの選択を変更できます。

## コドライバー読み上げについて

- VoiceVoxというランタイムを利用して音声合成を行います
- 音声生成の基本辞書が「log/base.json」にあります

### 生成のオプション

起動オプションにて以下のパラメータを変更できます。

- 「-actor アクターID」: VoiceVoxの話者変更
- 「-speed 0.5～2.0」: しゃべる速度
- 「-pitch 0.5～2.0」: 声の高さを変更
- 「-volume 0.5～2.0」: 音量変更
- 「-offset -50..50」: 読み上げのタイミング変更（メートル単位）

## ログのフォーマット
ファイル名：「log/ステージ長.log」

```
0,straight,unknown,50
47.01781152725222,3-right,unknown,140
199.68337484359745,slight-left,twisty,60
...
32032.167246975354,finish,unknown,0
```

「距離,単語１,単語２,単語３」の羅列です。
距離順にソートされている必要があります。

- 単語１には基本辞書のmarksキーワードかコンマを含まない日本語が直接書けます
- 単語２には基本辞書のiconsキーワードかコンマを含まない日本語が直接書けます、「unknown」は発音無し
- 単語３には基本辞書のdists距離数かコンマを含まない日本語が直接書けます「0」は発音無し

## アクターIDについて

|キャラクター名|スタイル|ID|
|-------|----|-----|
|四国めたん|ノーマル|2|
||あまあま|0|
||ツンツン|6|
||セクシー|4|
||ささやき|36|
||ヒソヒソ|37|
|ずんだもん|ノーマル|3|
||あまあま|1|
||ツンツン|7|
||セクシー|5|
||ささやき|22|
||ヒソヒソ|38|
|春日部つむぎ|ノーマル|8|
|雨晴はう|ノーマル|10|
|波音リツ|ノーマル|9|
|玄野武宏|ノーマル|11|
||喜び|39|
||ツンギレ|40|
||悲しみ|41|
|白上虎太郎|ふつう|12|
||わーい|32|
||びくびく|33|
||おこ|34|
||びえーん|35|
|青山龍星|ノーマル|13|
|冥鳴ひまり|ノーマル|14|
|九州そら|ノーマル|16|
||あまあま|15|
||ツンツン|18|
||セクシー|17|
||ささやき|19|
|もち子さん|ノーマル|20|
|剣崎雌雄|ノーマル|21|
|WhiteCUL|ノーマル|23|
||たのしい|24|
||かなしい|25|
||びえーん|26|
|後鬼|人間ver.|27|
||ぬいぐるみver.|28|
|No.7|ノーマル|29|
||アナウンス|30|
||読み聞かせ|31|
|ちび式じい|ノーマル|42|
|櫻歌ミコ|ノーマル|43|
||第二形態|44|
||ロリ|45|
|小夜/SAYO|ノーマル|46|
|ナースロボ＿タイプＴ|ノーマル|47|
||楽々|48|
||恐怖|49|
||内緒話|50|

# 既知の問題点

- ロギングモードが環境依存が大きいかも（自分の環境がワイドモニタからフルHD切り出しをしている）
- 画像からペースノートを作ったので実際の読み上げよりも情報が少ない（ショートやロングなどはアイコンには無い）
- 画像処理も確度が９０％ちょいしかなく、結構読み取り間違いや重複が入っちゃってる（特に空が見えにくいステージに弱い）
- コールが忙しい区間で早めに読み始めるという処理が無いので、読み遅れが発生したりする
- ただし以上の問題はログの手修正で直せるので地道に頑張っていこうと思います

# ログの収集状況

- newは最新の分類処理によるログ。
- oldは精度の悪い分類処理によるログ。
- fixは手修正したログ
- noneやコメントの無いものはまだログが無い

```
		// 0:Monte-Carlo ----------------------------------------------------------
		18799.8984375:    {0, 0},  // old: La Bollène-Vésubie - Peïra Cava
		18606.03125:      {0, 1},  // new: Peïra Cava - La Bollène-Vésubie
		12349.2734375:    {0, 2},  // new: La Bollène-Vésubie - Col de Turini
		12167.2060546875: {0, 3},  // new: Pra d'Alart
		6745.568359375:   {0, 4},  // new: La Maïris
		6680.1611328125:  {0, 5},  // new: Baisse de Patronel
		17064.154296875:  {0, 6},  // new: Saint-Léger-les-Mélèzes - La Bâtie-Neuve
		16908.458984375:  {0, 7},  // new: La Bâtie-Neuve - Saint-Léger-les-Mélèzes
		8478.833984375:   {0, 8},  // new: Moissière
		8306.2373046875:  {0, 9},  // old: Ancelle
		8924.6201171875:  {0, 10}, // new: Ravin de Coste Belle
		8922.3984375:     {0, 11}, // new: Les Borels

		// 1:Sweden ---------------------------------------------------------------
		21768.318359375: {1, 0}, // none: Hof-Finnskog
		21780.54296875:  {1, 1}, // none: Åsnes
		11371.87109375:  {1, 2}, // new: Spikbrenna
		11270.384765625: {1, 3}, // new: Lauksjøen
		//4
		//5
		//6
		//7
		3630.523193359375: {1, 8}, // old: Älgsjön
		3678.771240234375: {1, 9}, // new: Ekshärad
		//10
		//11
		// 2:México ---------------------------------------------------------------
		27065.39453125: {2, 0}, // none: El Chocolate
		//1
		//2
		//3
		//4
		//5
		//6
		//7
		8367.2353515625: {2, 8}, // new: Alfaro
		//9
		6154.95751953125: {2, 10}, // old: San Diego
		7242.689453125:   {2, 11}, // old: El Mosquito
		// 3:Croatia --------------------------------------------------------------
		25884.58203125:  {3, 0}, // none: Bliznec
		25880.095703125: {3, 1}, // new: Trakošćan
		//2
		//3
		//4
		//5
		//6
		//7
		8101.09228515625: {3, 8}, // new: Hartje
		//9
		9099.501953125: {3, 10}, // old: Krašić
		//11
		// 4:Portugal -------------------------------------------------------------
		30647.3671875:   {4, 0}, // old: Baião
		31512.115234375: {4, 1}, // old: Caminha
		17035.876953125: {4, 2}, // new: Fridão
		//3
		//4
		//5
		//6
		7591.076171875: {4, 7}, // new: Ervideiro
		// : Monte Olia
		//9
		//10
		//11
		// 5:Sardegna -------------------------------------------------------------
		31854.994140625: {5, 0}, // none: Rena Majore
		31971.994140625: {5, 1}, // new: Monte Olia
		//2
		//3
		//4
		//5
		//6
		//7
		//8
		//9
		7790.3369140625: {5, 10}, // old: Monte Muvri
		7818.212890625:  {5, 11}, // none: Monte Acuto
		// 6:Kenya ----------------------------------------------------------------
		//0
		//1
		5753.6005859375: {6, 2}, // old: Moi North
		//3
		4848.55517578125: {6, 4}, // fixed: Wileli
		//5
		//6
		//7
		//8
		//9
		//10
		//11
		// 7:Estonia --------------------------------------------------------------
		17430.73828125:  {7, 0}, // none: Otepää
		17420.412109375: {7, 1}, // new: Rebaste
		//2
		//3
		//4
		//5
		//6
		//7
		//8
		//9
		//10
		//11
		// 8:Finland --------------------------------------------------------------
		11414.5859375: {8, 0}, // none: Leustu
		//1
		//2
		//3
		//4
		//5
		//6
		//7
		//8
		10670.9384765625: {8, 9}, // new: Venkajarvi
		//10
		//11
		// 9:Greece ---------------------------------------------------------------
		24990.927734375: {9, 0}, // none: Gravia
		24989.751953125: {9, 1}, // new: Prosilio
		//2
		//3
		//4
		//5
		//6
		//7
		//8
		5884.07763671875: {9, 9}, // old: Bauxites
		//10
		//11
		// 10:Chile ---------------------------------------------------------------
		//0
		//1
		18300.140625: {10, 2}, // new: Río Lía
		//3
		//4
		//5
		//6
		//7
		8075.86572265625: {10, 8}, // old: Yumbel
		//9
		8425.1728515625: {10, 10}, // new: Hualqui
		//11
		// 11:Central Europe ------------------------------------------------------
		//0
		32679.244140625: {11, 1}, // old: Lukoveček
		//2
		15770.38671875: {11, 3}, // new: Žabárna
		//4
		//5
		9173.345703125:  {11, 6}, // old: Vítová
		9098.77734375:   {11, 7}, // old: Brusné
		15078.583984375: {11, 8}, // old: Libosváry
		//9
		9267.7421875:    {11, 10}, // old: Osíčko
		8979.5126953125: {11, 11}, // old: Příkazy
		// 12:Japan ---------------------------------------------------------------
		20209.443359375: {12, 0}, // new: Lake Mikawa
		//1
		//2
		//3
		10608.0771484375: {12, 4}, // none: Habu Dam
		//5
		6734.7861328125: {12, 6}, // new: Nenoue Plateau
		//7
		//8
		//9
		//10
		7184.89013671875: {12, 11}, // old: Nakatsugawa
		// 13:Mediterraneo --------------------------------------------------------
		//0
		//1
		//2
		//3
		//4
		7982.541015625: {13, 5}, // new: Serra Di Cuzzioli
		//6
		//7
		//8
		9752.8134765625: {13, 9}, // old: Ravin de Finelio
		//10
		//11
		// 14:Pacifico ------------------------------------------------------------
		//0
		//1
		//2
		//3
		//4
		//5
		//6
		5712.67041015625: {14, 7}, // old: Abai
		6709.298828125:   {14, 8}, // fixed: Moearaikoer
		//9
		//10
		9444.4287109375: {14, 11}, // new: Gunung Tujuh
		// 15:Oceania -------------------------------------------------------------
		11336.53125: {15, 0}, // new: Oakleigh
		//1
		7023.32177734375: {15, 2}, // none: Mangapai
		//3
		//4
		//5
		//6
		//7
		//8
		9625.2822265625: {15, 9}, // old: Orewa
		//10
		//11
		// 16:Scandia -------------------------------------------------------------
		31230.755859375: {16, 0}, // old: Holtjønn
		32164.1796875:   {16, 1}, // new: Hengeltjønn
		//2
		//3
		//4
		//5
		//6
		//7
		//8
		//9
		//10
		//11
		// 17:Iberia --------------------------------------------------------------
		19315.458984375: {17, 0}, // new: Santes Creus
		//1
		//2
		10075.623046875: {17, 3}, // old: Pontils
		//4
		//5
		//6
		//7
		//8
		//9
		//10
		//11
```