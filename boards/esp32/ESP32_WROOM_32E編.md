<!--
  手順書テンプレート(全ボード共通)
  - このファイルをコピーして、ボードごとのフォルダに保存してから書き始める。
  - 章の番号と名前は変えない。該当しない章は消さずに「本ボードでは作成しない(理由:〜)」と書く。
  - ボード独自の内容は、第9章以降に追加章として書く。
  - このコメントはプレビューには表示されない。消さずに残してよい。
-->

# 【ESP32_WROOM_32E】開発手順書

| 項目 | 内容 |
|---|---|
| 対象ボード | ESP32_WROOM_32E |
| 開発言語 | C言語 |
| IDE・ツール | VS Code ver1.146.0 |
||ESP-IDF Extension ver2.3.0|
||ESP-IDF Installation Maneger ver0.21.1|
||CP210x Windows Drivers ver6.7.6|
| SDK・ライブラリ | ESP-IDF ver6.1 |
| 開発PCのOS | Windows 11 26H2 |
| 最終検証日 | 2026/11/20 |

## 改版履歴

| 版 | 日付 | 担当者 | 内容 |
|---|---|---|---|
| 0.1 | 2026/11/13 | E1 | 新規作成 |
| 0.2 | 2026/11/20 | E1 | 第0章を追加 |

---

## 0. 概要

### 0.1 この手順書でできること
<!-- 読者がこの手順書を最後まで進めると何ができるようになるかを書く -->
本手順書を最後まで進めることで、ESP32-WROOM-32Eの接続方法とプロジェクト作成およびプログラムの動作の確認が可能となる。  
それによりESP32-WROOM-32Eを用いた、FreeRTOS有りの組み込み開発ができるようになる。

### 0.2 必要な機材
必要な機材を次の表に示す。
#### 表1.必要機材一覧
| 品名 | 数量 | 備考 |
|---|---|---|
| ESP32-DevKitC V4(ESP32-WROOM-32E) | 1人1台 | 開発するボード本体 |
| USBケーブル(micro-B、データ通信対応) | 1人1本 | 給電・書込み・モニタ用 |
| ESP-PROG(JTAGデバッガ) | 1セット | デバッグ用 |
| ブレッドボード・ジャンパワイヤ | 1人1式 | LED・ボタン・JTAGの配線 |
| LED・抵抗(330Ω 程度) | 1人1組 | 題材1・2・4・5・6で使用 |
| タクトスイッチ | 1人1個 | 題材2・3・5・6で使用 |


### 0.3 前提知識・事前準備
<!-- 既存の Git/GitHub 運用・Redmine 運用を参考に、前提とする知識と準備を書く -->
#### 開発の前提条件
開発にあたって次に示す内容を確認しておくこと。
- 開発は[技術資料](http://hst-saba:3000/675802efc7569e701fb1c2ee)に記載された手順に沿って進める。  
- ツールやドライバのバージョンはメンバー間で統一する。 

#### 開発対象マイコンの概要
ESP32-WROOM-32Eとは
Espressif Systems社の、Wi-Fi・Bluetooth通信機能を内蔵したIoT向けマイコンモジュール。  
デュアルコアCPUを搭載しており、GPIOやUART、SPI、I2Cなどの豊富な周辺機能を利用できる。  
FreeRTOSを利用したマルチタスク処理に対応しており、センサー制御や無線通信など、さまざまな組み込みシステムの開発に使用される。  

#### 用語解説
- マイコン  
マイコン（Microcontroller Unit, MCU）とは、CPU・メモリ・入出力（I/O）などを1つのICに集積した小型コンピュータのこと。  
家電や自動車などの電子機器を制御するために使われる。  

- GPIO  
GPIO(General Purpose Input/Output)とは、汎用的に使用できる入出力ポートのこと。  
センサの値やスイッチのオンオフを入力信号として伝えたり、LEDを光らせる出力信号を伝えたりすることができる。  
プログラム上でピンの番号を指定して利用する。

- UART  
UARTとは、データを1ビットずつ順番に送受信するシリアル通信方式のこと。  
マイコンとPCや他の電子機器との間で、データの送受信に使用される。  
ESP32では、プログラムの書き込みやログの確認などに利用される。  

- JTAG  
JTAGとは、マイコンなどの内部状態を確認したり、プログラムのデバッグを行ったりするためのインターフェース規格のこと。  
ブレークポイントの設定やステップ実行、変数の確認などに使用される。  
ESP32では、専用のJTAGデバッガ(例：ESP-PROG)を接続することで、プログラムの実行状態を確認できる。  

---

## 1. 開発環境構築

### 1.1 ツールのインストール
#### VS Code インストール手順
1. VSCode公式の[ダウンロードページ](https://code.visualstudio.com/download)へアクセスし、Windows向けインストーラをダウンロードする。<br>
<img src="./images/E1_vscode_download.png" width="300" height="" alt="VSCodeインストーラ_ダウンロード手順"><br>

1. ダウンロードしたインストーラ`VSCodeUserSetup-x64-1.147`を実行する。
1. 「同意する」にチェックを入れて「次へ」を押下する。<br>
<img src="./images/E1_vscode_install.png" width="300" height="" alt="VSCode_インストール手順1"><br>

1. インストール先を指定して「次へ」を押下する。<br>
<img src="./images/E1_vscode_install2.png" width="300" height="" alt="VSCode_インストール手順2"><br>

1. 「次へ」を押下する。<br>
<img src="./images/E1_vscode_install3.png" width="300" height="" alt="VSCode_インストール手順3"><br>

1. 「次へ」を押下する。<br>
<img src="./images/E1_vscode_install4.png" width="300" height="" alt="VSCode_インストール手順4"><br>

1. 「次へ」を押下する。<br>
<img src="./images/E1_vscode_install5.png" width="300" height="" alt="VSCode_インストール手順5"><br>

1. 「インストール」を押下する。<br>
<img src="./images/E1_vscode_install6.png" width="300" height="" alt="VSCode_インストール手順6"><br>

1. 完了ボタンまで進んだらインストール完了。<br>
<img src="./images/E1_vscode_install7.png" width="300" height="" alt="VSCode_インストール手順7"><br>

#### ESP-IDF Extension インストール手順
1. 画像の①からVSCodeの拡張機能を開く。<br>
1. 画像の②にESP-IDFと入力し検索する。<br>
1. 画像の③をクリックしてESP-IDFのインストールページを開く。<br>
<img src="./images/E1_ESP-IDFex_install.png" width="300" height="" alt="ESP-IDF_Extension_インストール手順1"><br>

1. Installボタンをクリックしてインストールする。<br>
<img src="./images/E1_ESP-IDFex_install2.png" width="300" height="" alt="ESP-IDF_Extension_インストール手順2"><br>

#### ESP-IDF Installation Maneger ダウンロード手順
1. Espressifの[公式ダウンロードページ](https://dl.espressif.com/dl/eim/)にアクセスする。<br>
1. `eim-gui-windows-x64.exe`をダウンロードする。<br>
<img src="./images/E1_ESP-eim_download.png" width="300" height="" alt="EIM_download手順"><br>

#### ESP-IDF 本体インストール手順
1. `eim-gui-windows-x64.exe`を実行する

1. Start Installationを押下する。<br>
<img src="./images/E1_ESP-IDF_install1.png" width="300" height="" alt="Start_Installation"><br>

1. Start Easy Installationを押下する。<br>
<img src="./images/E1_ESP-IDF_install2.png" width="300" height="" alt="Easy_Installation"><br>

1. インストールするESP-IDFのバージョンを選択する。<br>
<img src="./images/E1_ESP-IDF_install3.png" width="300" height="" alt="Version_select"><br>

1. 1. Start Installationを押下する。<br>
<img src="./images/E1_ESP-IDF_install4.png" width="300" height="" alt="Start_Installation2"><br>

1. インストールを待つ。(15分～30分程度)<br>
<img src="./images/E1_ESP-IDF_install5.png" width="300" height="" alt="Wait_Installation"><br>

1. インストール完了画面が出たことを確認する。<br>
<img src="./images/E1_ESP-IDF_install6.png" width="300" height="" alt="Complete_Installation"><br>

### 1.2 ドライバのインストール
#### CP210x Windows Drivers インストール手順
1. [ドライバダウンロードページ](https://www.silabs.com/software-and-tools/usb-to-uart-bridge-vcp-drivers?tab=downloads)へアクセスする。<br>
1. CP210x Windows Driversを押下する。(zipファイルがダウンロードされる)<br>
<img src="./images/E1_CP210xDrivers_install1.png" width="300" height="" alt="Driver_Installation"><br>
1. ダウンロードしたzipファイルを任意の場所に解凍する。<br>
1. `CP210xVCPInstaller_x64`を実行する。<br>
1. 「次へ」を押下する。<br>
<img src="./images/E1_CP210xDrivers_install2.png" width="300" height="" alt="Driver_Installation2"><br>
1. 「同意します」にチェックを入れて「次へ」を押下する。<br>
<img src="./images/E1_CP210xDrivers_install3.png" width="300" height="" alt="Driver_Installation3"><br>
1. 完了画面を確認する。<br>
<img src="./images/E1_CP210xDrivers_install4.png" width="300" height="" alt="Driver_Installation4"><br>


### 1.3 インストールできたことの確認
下記2点の確認をもってインストール完了とする。<br>
- ESP-IDF WelcomeからNew projectを選択して画面上部にESP-IDF v6.1が出ること。<br>
<img src="./images/E1_check_install_IDF.png" width="500" height="" alt="check_Install"><br>
- マイコンをPCと接続したときデバイスマネージャーでCOMx(例：COM5)としてポートが表示されること。<br>
<img src="./images/E1_check_install_Driver.png" width="300" height="" alt="check_Install2"><br>

## 2. シリアルターミナルの使い方

### 2.1 使用するツールと接続方法
### 2.2 通信設定(ポート、ボーレートなど)
### 2.3 動作確認

## 3. プロジェクト作成

### 3.1 新規プロジェクトの作成
### 3.2 プロジェクトの構成(フォルダ・ファイルの役割)

## 4. コーディング・ビルド・書込み(RTOSなし)

<!-- 各題材の節は「作るもの → 配線 → プログラム → ビルドと書込み → 動作確認」の順で書く -->

### 4.1 題材1:LED点滅
### 4.2 題材2:ボタン入力
### 4.3 題材3:UART出力
### 4.4 題材4:タイマ割込み

## 5. コーディング・ビルド・書込み(FreeRTOS)

### 5.1 FreeRTOSの導入・設定
### 5.2 題材5:複数タスク
### 5.3 題材6:タスク間通信

## 6. デバッグ

### 6.1 デバッガの接続(配線図・写真)
### 6.2 デバッグの開始
### 6.3 ブレークポイント・ステップ実行
### 6.4 変数・メモリ・レジスタの確認

## 7. トラブルシューティング

| 症状 | 原因 | 対処 |
|---|---|---|
| | | |

## 8. 参考資料

- 

<!-- ボード独自の追加章がある場合は、ここから「## 9. 〜」として追加する -->

<!--
  記述ルール(書きぶりを全章でそろえる)
  1. 見出し:章は「## 1. 開発環境構築」、節は「### 1.1 ツールのインストール」の形にする。
  2. 手順:番号付きリストで書き、1つの番号に1つの操作。文末は「〜する。」にそろえる。
  3. 確認:手順のあとに「> 確認:〜れば成功」と、成功したかどうかの判断方法を書く。
  4. 注意:まちがえやすい所は「> 注意:〜」と書く。
  5. 画像:画面操作にはスクリーンショットを付ける。images/ に置き、名前は担当番号から始める(例:p1_ext.png)。
  6. 図:処理の流れやタスクの関係は Mermaid で書く。配線は図か写真で、ピン番号と信号名を書く。
  7. プログラム:手順書の中にコードブロックで書き、日本語のコメントを付ける。
  8. バージョン:ツールと SDK のバージョンを書く。
  9. 用語:同じものは同じことばで書く。英数字は半角で、日本語との間に半角スペースを入れる。
  10. つまずいた点:第7章に必ず記録する。
-->
