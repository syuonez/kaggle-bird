# kaggle-bird

# 目的　金メダル

# solution　随時更新

Aug

リバーブ、ピッチ、テンポ、オーバードライブ
ピンクノイズ
ガウスノイズ
ガウスSNR
ゲイン（音量調整）



テストセットに手でラベルを付けることは不正行為であり、永久的なアカウント禁止につながる可能性があります。

アンサンブルはbird1位を参考にする

<img width="723" alt="スクリーンショット 2021-01-08 11 43 09" src="https://user-images.githubusercontent.com/59402913/103968163-b0595e80-51a6-11eb-89cc-53a9291bc75c.png">

私はそれを正しく理解しましたか？私が実際に持っていた最大の疑問符は、クリップ内の種の真陽性と偽陰性の記録が本当に「自動的にフラグが立てられない」ことを意味するかどうかについてでした=確かにわかりません（しかし、どれだけ良いかによってはそれほど可能性はありません使用されたアルゴリズムは）？

これは、真陽性と偽陰性を「主張」する損失関数を使用する必要があることを示唆していますが、確信が持てないクリップについてはもう少し「寛大」です（ただし、陽性よりも陰性である可能性が高いと推測される場合があります） ）？

非常に大きな課題の1つは、相関関係のある「基本的な検出アルゴリズム」ではフラグが立てられないものに対応するオーディオデータを用意することですが、これは間違いなくネガティブです。そうですか？

：気付かれない欠落しているラベルのトレーニング

:学習プロセスに損傷を与えるいくつかの重要なFNを削除します。これにより、パフォーマンスが突然向上します。

:これらの結果は、データセットが小さいほど、ラベルの欠落によるダメージが大きく、その結果、ラベルを破棄することで得られる性能の向上が大きいことを示しているように思われます

：テストセットの各テストオーディオには、23種のうち少なくとも1つが含まれている必要はないがある可能性大

:0.3秒のような非常に短い持続時間のtpセグメントがいくつかあるのを見ました、1）人がこれらをそのような精度/精度でどのようにラベル付けしたのか2）

:https://www.kaggle.com/c/rfcx-species-audio-detection/discussion/203394



:データは実際には48000 Hzでサンプリングされました=>これが、サンプルレートが48000のオーディオファイルの読み取りが他のどのサンプルレートよりも速い理由

flacファイルを再生してオーディオサンプルの長さが1分であることがわかりました。flacファイルを読み取ると、長さ1920000の配列が取得され、tfrecordファイルからwavファイルが読み取られます。長さ2880000の配列が取得されます。したがって、flacファイルのサンプルレートは32000で、wavファイルのサンプルレートは16000ではなく48000です。

つまりwavに変換して扱った方がいい？

:評価は、種の存在（songtype_id歌の種類ではない）の予測のみに基づいていますが、一部の種で示されているように、トレーニングデータには2つの異なる歌があります。これらの場合、両方の歌型は種からの一般的な信号です。ソングタイプ1は、私たちのチームの生物学者によって決定された最も一般的な曲を示し、ソングタイプ4は、別の一般的な曲のバリエーションを示しています。

つまり、song1とsong４で分けた方がいい精度が出そう

:https://github.com/CVxTz/COLA_pytorch

:https://www.kaggle.com/c/rfcx-species-audio-detection/discussion/198371



![image](https://user-images.githubusercontent.com/59402913/104118426-468ab180-536c-11eb-898a-ed80ae46d82d.png)




# 中期SMART（目標）

~10/31

S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）

==========================================================

# kaggle日記　SMART（目標） 毎日更新する

# 1/7
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）

メル周波数スぺクトログラムとは

●wavファイル（生の音）にSTFT（短時間フーリエ変換）を施して
●メルフィルタバンクを適用した特徴量



:SEDタスクのモデル

開始時間とオフセット時間の情報を使用して予測を提供するにはどうすればよいですか？これを行うために、SEDタスクのモデルは、クリップの集約された予測を出力する代わりに、セグメントごとの予測を出力します。これは通常、オーディオタグ付けモデルに使用されます。

CNN特徴抽出器の出力には、周波数と時間に関する情報が含まれています（4次元である必要があります:(バッチサイズ、チャネル、周波数、時間））。したがって、周波数軸のみで集計すると、その特徴の時間情報を保持できます。地図。その機能マップには、どのタイムセグメントにどのサウンドイベントがあるかに関する情報が含まれています。

PANNモデルの優れている点は、生のオーディオクリップを入力として受け入れることです。上記のモデルのCNN特徴抽出器にチャンクを入れましょう。

PANNsCNN14Attでは、入力された生の波形は、torchlibrosaのユーティリティを使用して対数メルスペクトログラムに変換されます

<img width="857" alt="スクリーンショット 2021-01-12 17 51 11" src="https://user-images.githubusercontent.com/59402913/104291341-c47ec200-54fe-11eb-8a58-68ee2cadb411.png">

この図は、サウンドイベントの検出に関して、弱い注釈とは何か、強い注釈とは何かを直感的に説明しています。このコンテストでは、弱い注釈（クリップレベルの注釈）しかありません。したがって、弱教師ありの方法でSEDモデルをトレーニングする必要があります。

今回の競争は強いラベルがある

弱く監視された設定では、クリップレベルの注釈しかないため、時間軸でそれを集約する必要もあります。まず、特徴抽出器の直後に各時間ステップのクラス存在確率を出力する分類器を配置し、次に分類器の結果の出力を時間軸に集約します。このようにして、クリップレベルの予測とセグメントレベルの予測の両方を取得できます（時間分解能が高い場合は、イベントレベルの予測として扱うことができます）。次に、クリップレベルの予測とクリップレベルの注釈を使用したBCE損失を使用して、通常どおりにトレーニングします。

上記のPANNモデルでこれがどのように実装されているかを確認しましょう。セグメント単位の予測とクリップ単位の予測は、実際にはモデルのAttBlockで計算されます。

forwardメソッドでは、最初に最初の行で自己注意マップを計算しますnorm_att = torch.softmax（torch.clamp（self.att（x）、-10、10）、dim = -1）。これは、セグメントの分類結果を集計するために使用されます。 2行目では、cla = self.nonlinear_transform（self.cla（x））がセグメントごとの分類結果を計算します。次に、3行目で、注意の集約が実行され、クリップごとの予測が取得されます。

SEDアプローチは、長い録音に含まれる音を見つけるのに特に効果的/適切です。単純な心は、長い音全体の確率の平均を取り、他の場所のノイズに短いチャープを隠します。
https://www.kaggle.com/hidehisaarai1213/introduction-to-sound-event-detection/comments

secondary_labelsマルチラベル問題にするために使用する場合、実際にはf1スコアを使用しません。使える


==========================================================

# 10/28
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）

<img width="856" alt="スクリーンショット 2021-01-13 12 52 05" src="https://user-images.githubusercontent.com/59402913/104404504-27736600-559e-11eb-8759-2e9fb9d68779.png">

https://www.kaggle.com/hidehisaarai1213/introduction-to-sound-event-detection/comments



：サンプリング周波数は音を取得する頻度を表していて、これが高いほどより高い音(周波数が高い音)にも対応できます。48000Hzである必要ある？32000Hzにダウンサンプルしてもよくない？

<img width="829" alt="スクリーンショット 2021-01-13 17 05 13" src="https://user-images.githubusercontent.com/59402913/104423494-84344800-55c1-11eb-81f8-e689ad34b0f7.png">




<img width="435" alt="スクリーンショット 2021-01-13 17 16 44" src="https://user-images.githubusercontent.com/59402913/104424751-20ab1a00-55c3-11eb-889b-ea74bf1bd0d7.png">

mel_binsとは数？サンプリングレートが48000Hzだからmel_binsも多くした方がいいのでは？ここが違うとPANNのpre_trainが使えない？

==========================================================

# 10/28
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）

最適なウィンドウの長さは、アプリケーションによって異なります。アプリケーションがより正確であるために時間領域情報を必要とするようなものである場合、ウィンドウのサイズを小さくします。アプリケーションがより具体的な周波数ドメイン情報を要求する場合、ウィンドウのサイズを大きくします。ヒルマーが述べたように、これはUncertainty Principle本当に他に選択肢がありません。両方のドメインで一度に完全な解像度を取得することはできません


ウェーブスタイルでMixUpを使用しました。10秒のクリップ（A（48000 10）とB（48000 10））をランダムに選択します。
そして、AとBを混ぜます。たとえば、0.9A + 0.1Bです。最後に、ラベルが混合されます。

10秒のクリップウィークラベルトレーニングを使用しました。そして、10秒ごとに60秒のテストクリップを分割しました。
そして、10秒ごとにミニ予測（max（framewise_output、axis = time））を取得します。
最終予測は、ミニ予測の最大値です（= np.max（（6,24）、axis = 0））。
==========================================================

# 1/20
S(具体的）

audioのdatasetを新しくし始める

M（測定可能）

A（達成可能）

R（適切）

T（期限）

sum=>maxにしたらscoreが0.2上がった！！！！

fc 512<1024? 10fold

maxがいいのは確かそう

==========================================================

# 10/28
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）
:シングルラベルだが、実際はマルチラベルでは？５sに５種類ぐらいいる感覚

:泣いているか疑わしい

:同じclassでも鳴き方が異なる

:マルチラベルなものもあり

:違う種類じゃない？も含む

:遠くと近くで違いが大きい

:使っていない周波数帯を消す？

:怪しいリスト

:DB強いと弱いのバランスも大事？カエル＋カモメがほとんど　　カエルのみのclipどうなる？　DB強いのばかりだとDB弱いcilpきたらどうなる

18カエル　かもめと間違えるんじゃ？

:1-18


ブレンドのスコアがスコアのブレンドであることに気づきましたが、フォールド予測の平均化が優れていないとは結論付けませんでした。次回は必ず投票してみます！
==========================================================

# 2/7
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）

bs16=>bs32はLB下がるし、平均CVも下がる

評価指標には mean columns-wise ROC AUC です。つまりクラスごとに算出したAUCの平均値で評価します。

==========================================================

# 2/7
S(具体的）

M（測定可能）

A（達成可能）

R（適切）

T（期限）
