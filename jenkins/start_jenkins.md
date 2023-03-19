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