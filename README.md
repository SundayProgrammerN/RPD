# RPD
Dashboard App on Raspberry Pi

## セットアップ

### ハードウェア環境
+ Raspberry Pi 4 Model B Rev 1.5(メモリ8GB)
+ Raspberry Pi Touch Display(７インチ、800x480)
+ 32GBのマイクロSDカード
+ ディスプレイスタンド(7インチ用)

### SSHキーの作成

Macの場合は以下のコマンドを入力して、SSHの公開鍵と秘密鍵を作成する

```
ssh-keygen -t ed25519 -f ~/.ssh/raspi4-kiosk -C "for-raspberry-pi4-kiosk"
```

### Raspberry Pi Imagerで行うこと

Raspberry Pi OSのRelease date:15 Sep 2026

### インストール手順
Raspberry Pi Imager をダウンロードして起動します。

1. Select your Raspberry Pi device画面では、Raspberry Pi 4を選択
![alt text](image.png)

2. Raspberry Pi OS(64-bit)を選択し、次へ
![alt text](image-1.png)

3. インストール先となるメディアを選択
![alt text](image-2.png)

4. ホスト名を入力
![alt text](image-3.png)

5. ローカライゼーションでは日本語を選択
![alt text](image-4.png)

6. ログイン時のユーザ名、パスワードを設定
![alt text](image-5.png)

7. Raspberry piをWifiと接続する場合は、接続先のSSIDとそのパスワードを設定する
![alt text](image-6.png)

8. SSHは有効にし、BROWSEボタンをクリックして、作成した公開鍵を選択する
![alt text](image-7.png)

9. Raspberry Pi Connectは有効にしなくて良い
![alt text](image-8.png)

### Raspberry piで行うこと

#### 1.インターネットと接続する

wifiに繋げる(LANでもよし)

#### 2.タッチディスプレイの設定
タッチディスプレイはディスプレイスタンドを取り付けたことで上下反転して表示される。
この状態を解決するために、コントロールセンターでタッチディスプレイの設定を行う。

(※ディスプレイが上下反転していない場合は、この作業は不要)

1. Preference
2. control Centre
3. Screens
4. Screens
5. DSI-1
6. Orientation
7. Inverted
8. Apply

#### 3.ターミナルで行うこと
ターミナルを起動して以下のコマンドを順に実行

(SSHから実行するとよい)

```
sudo apt update && sudo apt upgrade -y
sudo apt install -y unclutter
sudo raspi-config nonint do_change_locale ja_JP.UTF-8
sudo raspi-config nonint do_boot_behaviour B4
```

一旦再起動して、再度ターミナルを起動。その後以下のコマンドを実行
```
sudo apt autoremove
sudo apt autoclean

mkdir -p $HOME/.config/labwc
nano $HOME/.config/labwc/autostart
```


autostartには以下の記述を追記する。

起動時に開きたいページのURLをあらかじめ調べておく。

```
sleep 60

chromium "https://example.com" --noerrdialogs --disable-infobars --no-first-run --enable-features=OverlayScrollbar --start-maximized --ozone-platform=wayland --disable-features=Translate --incognito --kiosk --password-store=basic &

```

その後、再起動

#### 4. Overlayfsを有効にする
Overlayfsを有効にすることで、インストールメディアを読み込み専用にすることができる。

書き込みによって寿命を縮めるSDカードがメディアの場合、この設定によって寿命を延ばすことができる。

設定方法は、
1. raspi-config
2. Performance options
3. Overlay file system
4. Enableに設定する

その後、再起動で設定が適用される
