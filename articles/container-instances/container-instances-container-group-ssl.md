---
title: Habilitar SSL em instâncias de contêiner do Azure
description: Criar um ponto de extremidade SSL ou TLS para um grupo de contêineres em execução em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: f11fb209f8d2ace51081fd81f453faf9505af27c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326075"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Habilitar um ponto de extremidade SSL em um grupo de contêineres

Este artigo mostra como criar um [grupo](container-instances-container-groups.md) de contêineres com um contêiner de aplicativo e um contêiner sidecar executando um provedor SSL. Ao configurar um grupo de contêineres com um ponto de extremidade SSL separado, você habilita conexões SSL para seu aplicativo sem alterar o código do aplicativo.

Você configura um grupo de contêineres que consiste em dois contêineres:
* Um contêiner de aplicativo que executa um aplicativo Web simples usando a imagem pública do Microsoft [ACI-HelloWorld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) . 
* Um contêiner sidecar executando a imagem [Nginx](https://hub.docker.com/_/nginx) pública, configurado para usar SSL. 

Neste exemplo, o grupo de contêineres expõe apenas a porta 443 para nginx com seu endereço IP público. O Nginx roteia solicitações HTTPS para o aplicativo Web complementar, que escuta internamente na porta 80. Você pode adaptar o exemplo para aplicativos de contêiner que escutam em outras portas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse arquivo. Se você quer usá-lo localmente, recomendamos usar a versão 2.0.55 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Para configurar o Nginx como um provedor SSL, você precisa de um certificado SSL. Este artigo mostra como criar e configurar um certificado SSL autoassinado. Para cenários de produção, você deve obter um certificado de uma autoridade de certificação.

Para criar um certificado SSL autoassinado, use a ferramenta [OpenSSL](https://www.openssl.org/) disponível em Azure cloud Shell e muitas distribuições do Linux ou use uma ferramenta de cliente comparável em seu sistema operacional.

Primeiro, crie uma solicitação de certificado (arquivo. CSR) em um diretório de trabalho local:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Siga os prompts para adicionar as informações de identificação. Para nome comum, insira o nome do host associado ao certificado. Quando uma senha for solicitada, pressione Enter sem digitar para ignorar a adição de uma senha.

Execute o comando a seguir para criar o certificado autoassinado (arquivo. CRT) da solicitação de certificado. Por exemplo:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Agora você deve ver três arquivos no diretório: a solicitação de certificado (`ssl.csr`), a chave privada (`ssl.key`) e o certificado autoassinado (`ssl.crt`). Você usa `ssl.key` o `ssl.crt` e o em etapas posteriores.

## <a name="configure-nginx-to-use-ssl"></a>Configurar o Nginx para usar SSL

### <a name="create-nginx-configuration-file"></a>Criar arquivo de configuração Nginx

Nesta seção, você cria um arquivo de configuração para o Nginx usar SSL. Comece copiando o texto a seguir em um novo arquivo`nginx.conf`chamado. No Azure Cloud Shell, você pode usar Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```console
code nginx.conf
```

No `location`, certifique-se de `proxy_pass` definir com a porta correta para o aplicativo. Neste exemplo, definimos a porta 80 para o `aci-helloworld` contêiner.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Arquivos de configuração e segredos de codificação Base64

Base64-codificar o arquivo de configuração Nginx, o certificado SSL e a chave SSL. Na próxima seção, insira o conteúdo codificado em um arquivo YAML usado para implantar o grupo de contêineres.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Implantar grupo de contêineres

Agora, implante o grupo de contêineres especificando as configurações de contêiner em um [arquivo YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Criar arquivo YAML

Copie o YAML a seguir em um novo arquivo `deploy-aci.yaml`chamado. No Azure Cloud Shell, você pode usar Visual Studio Code para criar o arquivo em seu diretório de trabalho:

```console
code deploy-aci.yaml
```

Insira o conteúdo dos arquivos codificados em base64, onde indicado `secret`em. Por exemplo, `cat` cada um dos arquivos codificados em base64 para ver seu conteúdo. Durante a implantação, esses arquivos são adicionados a um [volume secreto](container-instances-volume-secret.md) no grupo de contêineres. Neste exemplo, o volume secreto é montado no contêiner nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Implantar o grupo de contêineres

Crie um grupo de recursos com o comando [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o grupo de contêineres com o comando [AZ container Create](/cli/azure/container#az-container-create) , passando o arquivo YAML como um argumento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Exibir estado da implantação

Para exibir o estado da implantação, use o seguinte comando [AZ container show](/cli/azure/container#az-container-show) :

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Para uma implantação bem-sucedida, a saída é semelhante à seguinte:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Verificar conexão SSL

Para exibir o aplicativo em execução, navegue até seu endereço IP no navegador. Por exemplo, o endereço IP mostrado neste exemplo é `52.157.22.76`. Você deve usar `https://<IP-ADDRESS>` para ver o aplicativo em execução, devido à configuração do servidor Nginx. Tentativas de conexão com `http://<IP-ADDRESS>` falha.

![Captura de tela de navegador mostrando aplicativo em execução em uma instância de contêiner do Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Como este exemplo usa um certificado autoassinado e não um de uma autoridade de certificação, o navegador exibe um aviso de segurança ao se conectar ao site por HTTPS. O comportamento é esperado.
>

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como configurar um contêiner Nginx para habilitar conexões SSL para um aplicativo Web em execução no grupo de contêineres. Você pode adaptar este exemplo para aplicativos que escutam em portas diferentes da porta 80. Você também pode atualizar o arquivo de configuração Nginx para redirecionar automaticamente as conexões do servidor na porta 80 (HTTP) para usar HTTPS.

Embora este artigo use Nginx no sidecar, você pode usar outro provedor SSL, como [Caddy](https://caddyserver.com/).

Outra abordagem para habilitar o SSL em um grupo de contêineres é implantar o grupo em uma [rede virtual do Azure](container-instances-vnet.md) com um [Gateway de aplicativo do Azure](../application-gateway/overview.md). O gateway pode ser configurado como um ponto de extremidade SSL. Consulte um exemplo de [modelo de implantação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) que você pode adaptar para habilitar a terminação SSL no gateway.
