# Instalación de WSL2 y Docker

## 0. ¿Qué es WSL2?

設定>ストレージより空き容量確認
WSL2,Docker インストールのため、 20GB 程空があるのが望ましい

MicrosoftStore を開きWindows Terminalを検索＆取得

MS アカウントでログインして共有するか聞かれたら基本 No でよい
(別端末で設定共有したいなどあれば Yes)

Windows Terminalインストール済の場合、最新版を取得

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


wsl --install -d Ubuntu

![image](https://github.com/user-attachments/assets/cd7ee436-6cdb-4c70-a9e0-81eaafa78c12)

実行完了後以下が出るのでキー押下

![image](https://github.com/user-attachments/assets/81c878db-6e27-46c4-8a65-9e9c822b07bc)


実行完了したら PC 再起動



Ubuntu が起動し、ユーザ名/PW の設定が求められるので、任意の文字列を入力
★ ユーザ名/PW は忘れないようにメモしておく
