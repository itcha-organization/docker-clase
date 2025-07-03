# Instalación de WSL2 y Docker

## 0. ¿Qué es WSL2?

## 1. Instalación de WindowsTerminal

設定>ストレージより空き容量確認
WSL2,Docker インストールのため、 20GB 程空があるのが望ましい

MicrosoftStore を開きWindows Terminalを検索＆取得

MS アカウントでログインして共有するか聞かれたら基本 No でよい
(別端末で設定共有したいなどあれば Yes)

Windows Terminalインストール済の場合、最新版を取得

## 2. Instalación de WSL2

![image](https://github.com/user-attachments/assets/f7fb914a-09dc-4231-916a-1f4a9a07afd6)

![image](https://github.com/user-attachments/assets/f57fcad4-9ab8-4fe5-8739-cee43dceeaa5)

![image](https://github.com/user-attachments/assets/69bf5f78-73f5-4c36-9464-f09e237b7a2c)

![image](https://github.com/user-attachments/assets/5e8367f6-c479-4ba6-ab58-f107002d7b3d)

![image](https://github.com/user-attachments/assets/3296ab42-19b2-4731-84b9-240f92213a6e)

https://learn.microsoft.com/es-mx/windows/wsl/install#prerequisites

![image](https://github.com/user-attachments/assets/49e70e12-1fee-43e7-b941-df347c19dbd5)

![image](https://github.com/user-attachments/assets/0cee307e-add0-4742-a537-d515925b11d3)

Windowsの機能の有効化または無効化>Linux用Windowsサブシステムのステータス確認

PowerShell を管理者権限で起動し、以下コマンドを実行

![image](https://github.com/user-attachments/assets/7c81782c-eba1-44c3-a909-0265d03e3f5f)


wsl --install
![image](https://github.com/user-attachments/assets/b2bb1d0b-728a-410b-98f1-844400536d45)
※途中で確認が出たら、はいを押す

実行完了したら PC 再起動

![image](https://github.com/user-attachments/assets/3a9566ff-432a-462e-983e-01e22444884c)

↓再起動後にダウンロードが開始する。

![image](https://github.com/user-attachments/assets/215e29fd-d50a-450f-9a7f-d892239c0672)


Ubuntu が起動し、ユーザ名/PW の設定が求められるので、任意の文字列を入力
★ ユーザ名/PW は忘れないようにメモしておく
![image](https://github.com/user-attachments/assets/0cfaece1-b1d9-4f6d-9d9b-b4fe10772733)

![image](https://github.com/user-attachments/assets/60dc2862-0751-411e-8330-fd796d8de4a4)

TODO:termnal 上でのUbuntu開き方

curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

実行例
```
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kyoonuma@LAPTOP-J1K1E58T:~$ curl -fsSL https://get.docker.com -o get-docker.sh
kyoonuma@LAPTOP-J1K1E58T:~$ sudo sh get-docker.sh
[sudo] password for kyoonuma:
# Executing docker install script, commit: 53a22f61c0628e58e1d6680b49e82993d304b449

WSL DETECTED: We recommend using Docker Desktop for Windows.
Please get Docker Desktop from https://www.docker.com/products/docker-desktop/


You may press Ctrl+C now to abort this script.
+ sleep 20
+ sh -c apt-get -qq update >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get -y -qq install ca-certificates curl >/dev/null
+ sh -c install -m 0755 -d /etc/apt/keyrings
+ sh -c curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" -o /etc/apt/keyrings/docker.asc
+ sh -c chmod a+r /etc/apt/keyrings/docker.asc
+ sh -c echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu noble stable" > /etc/apt/sources.list.d/docker.list
+ sh -c apt-get -qq update >/dev/null
+ sh -c DEBIAN_FRONTEND=noninteractive apt-get -y -qq install docker-ce docker-ce-cli containerd.io docker-compose-plugin docker-ce-rootless-extras docker-buildx-plugin >/dev/null
+ sh -c docker version
Client: Docker Engine - Community
 Version:           28.3.1
 API version:       1.51
 Go version:        go1.24.4
 Git commit:        38b7060
 Built:             Wed Jul  2 20:56:27 2025
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          28.3.1
  API version:      1.51 (minimum version 1.24)
  Go version:       go1.24.4
  Git commit:       5beb93d
  Built:            Wed Jul  2 20:56:27 2025
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.27
  GitCommit:        05044ec0a9a75232cad458027ca83437aae3f4da
 runc:
  Version:          1.2.5
  GitCommit:        v1.2.5-0-g59923ef
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

================================================================================

To run Docker as a non-privileged user, consider setting up the
Docker daemon in rootless mode for your user:

    dockerd-rootless-setuptool.sh install

Visit https://docs.docker.com/go/rootless/ to learn about rootless mode.


To run the Docker daemon as a fully privileged service, but granting non-root
users access, refer to https://docs.docker.com/go/daemon-access/

WARNING: Access to the remote API on a privileged Docker daemon is equivalent
         to root access on the host. Refer to the 'Docker daemon attack surface'
         documentation for details: https://docs.docker.com/go/attack-surface/

================================================================================

kyoonuma@LAPTOP-J1K1E58T:~$
```

![image](https://github.com/user-attachments/assets/7d46e457-8032-4791-aaa3-0cfd71d4995e)


docker -v

![image](https://github.com/user-attachments/assets/5e228c0e-5f94-412c-aa73-d977038b39fe)

3-2. Docker セットアップ(権限設定&動作確認)

docker run hello-world
![image](https://github.com/user-attachments/assets/4fab2c29-7aba-42df-baf4-93f9c5bfb220)

kyoonuma@LAPTOP-J1K1E58T:~$ docker run hello-world
docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied

Run 'docker run --help' for more information
kyoonuma@LAPTOP-J1K1E58T:~$ sudo service docker start
kyoonuma@LAPTOP-J1K1E58T:~$ sudo usermod -aG docker $USER
kyoonuma@LAPTOP-J1K1E58T:~$ groups $USER
kyoonuma : kyoonuma adm cdrom sudo dip plugdev users docker
kyoonuma@LAPTOP-J1K1E58T:~$ su - ${USER}
Password:
kyoonuma@LAPTOP-J1K1E58T:~$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
e6590344b1a5: Pull complete
Digest: sha256:940c619fbd418f9b2b1b63e25d8861f9cc1b46e3fc8b018ccfe8b78f19b8cc4f
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

![image](https://github.com/user-attachments/assets/2363941b-e259-4b8e-b13f-e57614cfc2a3)

wsl --shutdown
![image](https://github.com/user-attachments/assets/3bcb4669-41d1-46a1-ae16-018e190cb3b0)

![image](https://github.com/user-attachments/assets/abf25015-199f-4ea0-b075-cf8c3e6b782a)


4. Docker でブラウザゲーム実行環境を作成
コンテナを起動し、ローカルでブラウザゲームを動かす

以下コマンドを順次実行

wget https://raw.githubusercontent.com/pianomanfrazier/lemmings-clone/master/server/docker-compose.yml
cat ./docker-compose.yml
docker compose up
docker-compose up(-あり)は、エラーになるので注意
docker compose と docker-compose、ハイフン有無で何が違うのか？
http://localhost:3000 をブラウザで開く
【チートシート】docker コマンド一覧 を見て気になるコマンドを試してみる

参考にしたサイト

github ソースコード(Lemmings Clone)


5. Docker Compose で WordPress サーバを立てる

kyoonuma@LAPTOP-J1K1E58T:~$ mkdir wordpress
kyoonuma@LAPTOP-J1K1E58T:~$ cd wordpress/
kyoonuma@LAPTOP-J1K1E58T:~/wordpress$ vi docker-compose.yml
kyoonuma@LAPTOP-J1K1E58T:~/wordpress$ cat docker-compose.yml

Docker Compose で複数の Docker コンテナをまとめて定義できる

wordpress と mysql のコンテナまとめてを起動(ウェブサーバと DB サーバ)

dockerhubのExample docker-compose.yml for wordpress:の内容をコピー
以下コマンドを順次実行
- コピーした内容を貼り付けてwq:で保存終了
vi ~/docker-compose.yml
- docker-compose.ymlに定義された通りコンテナを起動
docker compose up
http://localhost:8080 をブラウザで開く
参考にしたサイト

【超初心者向け】Docker で WordPress をサクッと構築
【初心者向け・図解】Docker Compose とは？
