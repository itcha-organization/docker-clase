![image](https://github.com/user-attachments/assets/fdf88422-aee9-4880-9966-74f24039cd92)


> ![image](https://github.com/user-attachments/assets/08750ec8-e105-4c81-b754-dfbad9181464)
> ![image](https://github.com/user-attachments/assets/09f9854e-32c3-4038-8b28-d3afe88aff6e)



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

# INSTALACION DE DOCKER DESKTOP

![image](https://github.com/user-attachments/assets/62de90ac-2d74-41e0-ba07-a53ff0b0cafb)

![image](https://github.com/user-attachments/assets/40c0fc80-660c-42c9-a2f4-0f51a604055d)

![image](https://github.com/user-attachments/assets/efbad90d-b64c-427d-b25d-0a8657c101ab)

despues de reiniciar

![image](https://github.com/user-attachments/assets/43bd1809-61cd-4864-9698-c5a85d7d7d55)

![image](https://github.com/user-attachments/assets/3721aec4-65f5-422f-90f8-d9fe0af03542)

![image](https://github.com/user-attachments/assets/9222869a-23fe-487e-972d-01b8c1be091c)

![image](https://github.com/user-attachments/assets/2e4b054b-c961-46b2-8f8f-f95da8a5fb8d)

Si hay error, desactivar hyper-v

![image](https://github.com/user-attachments/assets/471f9298-6f4e-414d-8c1c-53bd69aa27af)
