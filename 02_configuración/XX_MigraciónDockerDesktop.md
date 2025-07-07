![image](https://github.com/user-attachments/assets/fdf88422-aee9-4880-9966-74f24039cd92)


以下は、「WSL 上の Ubuntu から Docker を完全にアンインストールする方法」の日本語訳です：

---

# WSL 上の Ubuntu から Docker を完全にアンインストールする方法

Windows Subsystem for Linux（WSL）で動作している Ubuntu から Docker を完全に削除するには、以下の手順に従ってください。

---

## 1. Docker サービスを停止する

Docker が実行中の場合は、すべての Docker サービスを停止します：

```bash
sudo systemctl stop docker
sudo systemctl stop docker.socket
```

WSL では Docker を手動で起動している場合が多いため、その場合は以下で停止します：

```bash
sudo service docker stop
```

---

## 2. Docker パッケージをアンインストールする

Docker Engine、CLI、containerd など関連パッケージを削除します：

```bash
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli containerd runc
```

---

## 3. Docker の依存関係を削除する

使われていない依存パッケージをクリーンアップします：

```bash
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce
sudo apt-get autoclean
```

---

## 4. Docker のデータと設定を削除する

すべての Docker イメージ、コンテナ、ボリューム、設定ファイルを削除します：

```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /etc/docker
sudo rm -rf /var/run/docker.sock
```

もし `data-root` をカスタマイズしていた場合は、そのディレクトリも削除してください。

---

## 5. Docker グループを削除する（任意）

`docker` グループを作成していた場合は、以下で削除します：

```bash
sudo groupdel docker
```

---

## 6. 削除の確認

Docker が削除されたことを確認します：

```bash
docker --version
```

Docker が完全に削除されていれば、「command not found」などのエラーが表示されるはずです。

---

**補足：**
Docker を便利スクリプトや Snap 経由でインストールしていた場合は、以下のコマンドも必要になる場合があります：

```bash
sudo snap remove docker
```

また、`.bashrc` や `.zshrc` などのシェル設定ファイルに Docker 関連の設定を手動で追加していた場合は、それらも削除してください。

---

この手順に従うことで、WSL 上の Ubuntu 環境から Docker を完全にアンインストールできます。

参照：

* [https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine](https://docs.docker.com/engine/install/ubuntu/#uninstall-docker-engine)
* [https://stackoverflow.com/questions/44798842/how-to-completely-uninstall-docker](https://stackoverflow.com/questions/44798842/how-to-completely-uninstall-docker)
