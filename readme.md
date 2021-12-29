# Treafik Sample

## 事前準備

1. 公開的網域名稱 (Domain Name)
2. 固定IP 或 DDNS
3. 準備一台伺服器，並確定 80、443 port可用
   + CPU : 4 Core
   + RAM : 8 Gb
   + Storage: 512 Gb
   + System : Ubuntu 18.04 (↑)

4. vs code extension list
   1. traefikode
   2. Remote Development
5. 安裝 git
6. 安裝 cockpit

## 注意事項

1. 本範例 路由規則均採用 子域名(sub-domain)的方式，DNS 必須設定成 萬用字元模式 (ex: *.Your_Domain)，</br>如果DNS供應商不支援此設定方式，請改用 子路徑(sub-path)方式設定路由。

    ```yaml
    labels:
        # 使用 sub-domain的模式 => <service-name>.your-domain
       - "traefik.http.routers.<router-name>.rule=Host(`<service-name>.your-domain`)"
        # 使用 sub-path的模式 => your-domain/service-name
       - "traefik.http.routers.<router-name>.rule=Host(`your-domain`) && Path(`<service-name>`)"       
    ```

2. 本範例相關設定均為假設在單一伺服器上佈署所做的設定
3. 本範例不適用於無對外網路的IP位置 (let's encrypts 必須要在網際網路環境上才能申請)

## 範例使用說明

1. 將專案儲存至家目錄

   ```shell
   cd ~
   git clone https://github.com/cymondez/traefik-sample.git
   cd traefik-sample
   ```

2. 將專案內 .env.temp 複製成 .env

   ```shell
   cp .env.temp .env
   ```

3. 設定 .env 內容
   必要變數設定

   ```shell
   # 你的網域名稱 (本範例不適合設定成IP位置)
   HOST_DOMAIN=<Your-Domain>
   
   # 申請 Let's Encrypts必填
   TRAEFIK_CERTRESOLVER_ACME_EMAIL=<Your-eMail>
   
   # 可使用 htpasswd 產生 ，指令範例 htpasswd -nb <user-name> <password>
   TRAEFIK_BASIC_AUTH_MIDDLEWARE_USERS=<user:encoded-password>
   ```

4. 將 .env 內環境變數導入

   將下面代碼加入  ~/.bashrc

   ```shell
   set -o allexport
   source ~/traefik-sample/.env
   set +o allexport
   ```

   完成編輯後，重新載入 .bashrc

   ```shell
   source ~/.bashrc
   ```

5. 啟動 Traefik

   建立 proxy 用的 docker network

   ```shell
   docker network create $PROXY_DOCKER_NETWORK
   ```

   啟動 traefik-proxy

   ```shell
   cd ~/traefik-sample/traefik/traefik-proxy
   docker-compose up -d
   ```

   將 error-page-service加入

   ```shell
   cd ~/traefik-sample/traefik/traefik-proxy
   docker-compose up -d
   ```

6. demo 範例
   各資料夾內容說明
   + 01.web-demo: 佈署http服務
   + 02.loadbalance-demo: 佈署並觀察Traefik的load balance功能
   + 03.web-secure-demo: 佈署https服務並觀察Traefik向Let's Encrypts自動申請 SSL憑證的過程
   + 04.http-to-https-demo: 示範如何佈署 http轉https的設定方式

## TODO

demo

+ [ ] 05.error-page-middleware-demo: 示範route叫用建立好的error-page-middleware

---

homelab 相關範例

+ [x] 01.portainer: container的web管理服務
+ [x] 02.redmine: issue管理服務
+ [x] 03.gitea: 輕量的git server
+ [ ] 04.keyloack: 提供了單點登錄（SSO）功能的服務，支援 OpenID Connect 、 SAML 2.0、docker-v2
+ [ ] 05.docker-registry: 建立簡易的docker儲存庫，使用 keyloack提供 docker v2 驗證模式
+ [x] 06.mocky: 建立簡易的Web Api Mock服務
+ [x] 07.sish: 建立類似ngrok的內往穿透服務

## 相關資料

Traefik 官網 [官網連結](https://doc.traefik.io/traefik/)

acme.json 轉出憑證檔案 [參考連結](https://stackoverflow.com/questions/47218529/store-traefik-lets-encrypt-certificates-not-as-json/ )
