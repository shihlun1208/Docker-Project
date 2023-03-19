# Docker 安裝 jenkins
安裝 Jenkins 可以透過 Docker 來實現，以下是在 Windows 上安裝 Jenkins 的步驟：

下載 Docker Desktop for Windows：前往 Docker 官網下載 Docker Desktop for Windows，並按照指示進行安裝。

啟動 Docker：安裝完成後，啟動 Docker Desktop for Windows。

```powershell
# 搜尋 Jenkins 映像檔：打開命令提示字元（Command Prompt）或 PowerShell，
# 輸入以下指令，搜尋 Jenkins 映像檔。
docker search jenkins

# 下載 Jenkins 映像檔：輸入以下指令，下載 Jenkins 映像檔。
docker pull jenkins/jenkins

# 建立容器：輸入以下指令，建立 Jenkins 容器。
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins
 
# 這個指令會建立一個 Jenkins 容器，
# 並將主機的 8080 和 50000 port 對應到 Jenkins 容器的 8080 和 50000 port，
# 以及將容器中的 /var/jenkins_home 掛載到主機的 jenkins_home 資料夾。

# 訪問 Jenkins：開啟瀏覽器，輸入 http://localhost:8080 訪問 Jenkins，並根據指示完成 Jenkins 的安裝程序。

# 啟動 Jenkins 服務：在 Jenkins 安裝完成後，輸入以下指令，啟動 Jenkins 服務。
docker start <container_id>

# container_id 是指剛才建立的 Jenkins 容器 ID，可以使用以下指令查詢容器 ID。
docker ps -a

# 停止 Jenkins 服務：輸入以下指令，停止 Jenkins 服務。
docker stop <container_id>

```

## 使用 yml
```powershell
# 當您使用 Docker 時，使用 Docker Compose 可以讓您更容易地管理應用程式。透過 Docker Compose，您可以使用 YAML 檔來定義應用程式的環境。

# 以下是使用 Docker Compose 的範例 YAML 檔，用於建立 Jenkins 容器：
# 開啟一個資料夾建立 docker-compose.yml 檔案，填入

version: '3'
services:
  jenkins:
    image: jenkins/jenkins
    container_name: jenkins
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
volumes:
  jenkins_home:

# 若您已經安裝了 Docker Compose，您可以使用以下指令來啟動 Jenkins 容器：
docker-compose up -d
# 該指令將會使用您所定義的 YAML 檔，並建立 Jenkins 容器。若您想要停止容器，您可以使用以下指令：
docker-compose down

```

## 解鎖密碼

為了確保 Jenkins 是由管理員安全地安裝，已將密碼寫入記錄檔（不確定在哪裡可以找到？）\
此檔案位於伺服器上：/var/jenkins_home/secrets/initialAdminPassword

```powershell
docker exec -it <Jenkins 容器 ID> bash
cd /var/jenkins_home/secrets/
cat initialAdminPassword
# 這樣就可以打印出 Jenkins 的 initialAdminPassword 密碼了。
# 請複製這個密碼，然後在瀏覽器中貼上此密碼，即可解鎖 Jenkins。
```
---
## 開始一個作業
### Jenkins 端
1. 新增作業

2. 輸入項目名稱 > 建置Free-Style專案 > 確定 > 接著會轉到 Configure 頁面
3. 原始碼管理的地方 > 選擇 GIT \
設定 Repository URL: https://github.com/shihlun1208/Docker-Project.git \
Credentials: github帳號密碼 \
Branches to build: */main
4. 建置觸發程序 > 選擇 GitHub hook trigger for GITScm polling
5. Build Steps > 執行 Shell > 打上你想要的測試代碼 (以下範例)
```
echo "This is a test"
cat README.md
```
6. 儲存
7. 左側有個馬上建置的選項 測試看看能不能建置，不行的話回去檢查組態是不是有設定錯誤
---
### Github 端
* 若有需要github有被push時自動觸發jenkins，則需要在github中設定 webhooks
1. 在Github repository頁面，選擇webhooks > Add webhook

如果你的 Jenkins 安裝在本地端的電腦上並且使用 localhost 作為網址，那麼 GitHub 無法直接將 Webhooks 事件推送到你的 Jenkins。因為 GitHub 無法將事件推送到本地端。你需要讓 Jenkins 可以從外部存取才能使用 Webhooks。有兩個常用的方法可以解決這個問題：

* 將 Jenkins 安裝到一個可以從外部存取的伺服器上，例如使用雲端服務提供商（例如 AWS、Azure 或 Google Cloud）或自己的伺服器。

* 使用 ngrok 之類的工具來在本地端建立一個可以從外部存取的 URL，讓 GitHub 可以將事件推送到這個 URL。這樣做需要安裝並設定 ngrok，並將 Jenkins Webhooks URL 設定為 ngrok 提供的 URL。


2. 以ngrok為例，在「Payload URL」欄位中，貼上你的 ngrok 網址，加上 /github-webhook/，例如：http://xxxxxxxxxx.ngrok.io/github-webhook/。

3. 在「Content type」欄位中，選擇「application/json」。
4. 在「Secret」欄位中，可以留空。
5. 在「Which events would you like to trigger this webhook?」中，選擇「Just the push event」。
6. 最後，點選下方的「Add webhook」按鈕即可。
7. Webhooks / Manage webhook 中查看是不是有綠色勾勾即完成連結，若沒有請重新redelivery並檢查其他設定。
---
### 開始測試
* push 到 github 檢視 jenkins 有無建置動作