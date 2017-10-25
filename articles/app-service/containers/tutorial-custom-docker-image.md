---
title: "Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure | Microsoft Docs"
description: "Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, docker, contêiner"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure

O [Aplicativo Web para Contêineres](app-service-linux-intro.md) fornece imagens internas do Docker no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. O Aplicativo Web para Contêineres utiliza a tecnologia de contêiner do Docker para hospedar imagens internas e imagens personalizadas como uma plataforma como serviço. Neste tutorial, você aprenderá a criar uma imagem personalizada do Docker para uso no Aplicativo Web para Contêineres, que é um padrão comum se não há uma imagem interna para a linguagem ou se o aplicativo exige uma configuração específica que não é fornecida nas imagens internas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Git](https://git-scm.com/downloads)
* Uma [assinatura ativa do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Docker](https://docs.docker.com/get-started/#setup)
* Uma [conta do Hub do Docker](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o repositório de aplicativo de exemplo no computador local e, em seguida, mudar para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

O arquivo do Docker a seguir descreve o ambiente do Python que é necessário para executar nosso aplicativo. Além disso, a imagem configura um servidor [SSH](https://www.ssh.com/ssh/protocol/) para a comunicação segura entre o contêiner e o host.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Para criar a imagem do Docker, execute o comando `docker build` e forneça um nome, uma `mydockerimage` e uma marcação, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz um resultado semelhante ao seguinte:

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Teste se o build funciona executando o contêiner do Docker. Emita o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passe o nome e a marcação da imagem para ele. Também especifique a porta usando o argumento `-p`.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Verifique se o aplicativo web e o contêiner estão funcionando corretamente navegando no aplicativo Web localmente.

![Testar o aplicativo Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Enviar uma imagem do Docker por push para o Hub do Docker

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um Registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Enviando por push para um Registro particular do Docker? Consulte as instruções opcionais de como [Enviar uma imagem do Docker por push para um Registro particular](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares. Para enviar uma imagem personalizada do Docker por push para o Hub público do Docker, use o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) e forneça um nome de imagem completo e uma marcação. Um nome de imagem completo e uma marcação são parecidos com a seguinte amostra:

```bash
<docker-id>/image-name:tag
```

Se você ainda não estiver conectado no Hub do Docker, faça isso usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) antes de tentar enviar uma imagem por push.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Uma mensagem “Logon com êxito” confirma que você está conectado. Depois de conectado, você pode enviar a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Verifique se o push foi bem-sucedido examinando o resultado do comando.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Criar um Aplicativo Web para Contêineres

Hospede aplicativos Linux nativos na nuvem usando os Aplicativos Web do Azure. Para criar um Aplicativo Web para Contêineres, você deve executar os comandos da CLI do Azure que cria um grupo, um plano de serviço e, finalmente, o aplicativo Web em si. Primeiro, execute o comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) e passe um local e um nome exclusivo.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

Você verá um resultado semelhante à amostra apresentada aqui:

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Use o nome do grupo para ajudar a criar um plano de serviço de aplicativo usando o comando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create). Você também precisa nomeá-lo com um nome exclusivo e definir o sinalizador `--is-linux`.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

A criação de um plano de serviço produz resultados semelhantes à seguinte amostra:

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

Agora que o grupo de recursos e o plano de serviço foram criados, execute o comando [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) para criar o aplicativo Web. Observe que a pilha em tempo de execução é o Python 3.4 e que o aplicativo Web usa a configuração de grupo de recursos e de plano de serviço das etapas anteriores.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

O comando para criar um aplicativo Web produz o resultado mostrado aqui:

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

A maioria dos aplicativos Web tem configurações de aplicativo que precisam ser definidas. Se estiver usando uma imagem do Docker existente criada por outra pessoa, a imagem poderá exigir uma porta diferente da porta 80 do aplicativo. Para definir a `WEBSITES_PORT`, execute o comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings), conforme mostrado no seguinte exemplo de código:

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> As configurações de aplicativo diferenciam maiúsculas de minúsculas.
>

Verifique se o aplicativo Web funciona navegando nele. Não se esqueça do número da porta.

![Testar a configuração de porta do aplicativo Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Configurar o Aplicativo Web para usar o contêiner do Docker do Hub do Docker

O comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) permite usar uma imagem personalizada do Docker.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Implantando de um Registro Particular do Docker? Consulte as instruções opcionais sobre como [Configurar o Aplicativo Web para usar o contêiner do Docker de um Registro Particular](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

Para configurar o aplicativo Web para usar um Registro público do Docker, passe o nome do aplicativo, o grupo de recursos e o nome da imagem e a URL para o comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

Uma alteração de configuração bem-sucedida retorna informações gerais sobre o contêiner.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Testar o aplicativo no Azure

Antes de testar, você deve reiniciar o aplicativo Web usando o comando [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) para que as alterações entrem em vigor.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

O comando de reinicialização reinicia o aplicativo Web silenciosamente e, portanto, você não vê nenhum comentário no terminal. Quando o aplicativo Web estiver em execução, teste-o navegando em sua URL em `http://<username>.azurewebsites.net`. Verifique se o aplicativo exibe a nova mensagem de boas-vindas.

![Testar o aplicativo Web no Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Alterar o aplicativo Web e reimplantá-lo

Existe um arquivo do Python chamado `app.py` no diretório `using-custom-docker-image`. Esse arquivo contém uma linha de código que exibe uma mensagem `Hello World!`. Altere a linha para que ela exiba a mensagem `Hello World of Web Apps running in Docker Containers!`.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Depois de modificar o arquivo do Python e salvá-lo, recrie e envie por push a nova imagem do Docker. Em seguida, reinicie o aplicativo Web para que as alterações entrem em vigor. Use os mesmos comandos usados anteriormente neste tutorial. Consulte as seções [Criar a imagem com base no arquivo do Docker](#build-the-image-from-the-docker-file) e [Enviar a imagem do Docker por push](#push-docker-image). Teste o aplicativo Web seguindo as instruções em [Testar o aplicativo no Azure](#tTest-the-application-in-azure)

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Conectar-se ao Aplicativo Web para Contêineres usando SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que uma imagem personalizada do Docker dê suporte ao SSH, você deve criá-la em um arquivo do Docker. Habilite o SSH no próprio arquivo do Docker. As instruções sobre o SSH já foram adicionadas ao arquivo do Docker de exemplo, para que você possa seguir estas instruções com sua própria imagem personalizada:

* Uma instrução [RUN](https://docs.docker.com/engine/reference/builder/#run) que chama `apt-get` e, em seguida, define a senha da conta raiz como `"Docker!"`.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas por meio do Site do Kudu/SCM. O site do Kudu/SCM é autenticado com as credenciais de publicação.

* Uma instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) que instrui o mecanismo do Docker para copiar o arquivo [sshd_config](http://man.openbsd.org/sshd_config) para o diretório */etc/ssh/*. Seu arquivo de configuração deve ser baseado em nosso [arquivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) no repositório Azure-App-Service do GitHub.

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens: 
    > * `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* Uma instrução [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) que expõe a porta 2222 no contêiner. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada da Internet. Ela é uma porta interna que pode ser acessada somente por contêineres dentro da rede ponte de uma rede virtual privada. Depois disso, os comandos copiam os detalhes da configuração do SSH e iniciam o serviço `ssh`.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

O arquivo `init_container.sh` no código de exemplo contém instruções sobre como inicializar o contêiner quando ele é executado. O arquivo do Docker usa as instruções [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) e [CMD](https://docs.docker.com/engine/reference/builder/#cmd) para iniciar o script `init_container.sh` corretamente.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Talvez você deseje verificar se determinados aplicativos estão em execução no contêiner. Para inspecionar o contêiner e verificar os processos em execução, comece abrindo um navegador e navegue para `https://<app name>.scm.azurewebsites.net/webssh/host`. Em seguida, você será redirecionado para uma página que exibe um console interativo. Emita o comando `top` no prompt.

```bash
top
```

O comando `top` expõe todos os processos em execução em um contêiner.

```bash
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Parabéns! Você configurou uma imagem personalizada do Docker para um Aplicativo Web para Contêineres do Azure.

## <a name="push-a-docker-image-to-private-registry-optional"></a>Enviar uma imagem do Docker por push para um Registro Particular (opcional)

O Registro de Contêiner do Azure é um serviço gerenciado do Docker de hospedagem de imagens particulares. As implantações podem ser de qualquer tipo, incluindo [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) e contêineres de serviço de aplicativo do Azure. O uso do Registro de Contêiner do Azure é como o uso de qualquer Registro particular. Portanto, se precisar usar seu próprio Registro particular, as etapas para concluir esta tarefa serão semelhantes.

Use o comando [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) para criar um Registro de Contêiner do Azure. Passe o nome, o grupo de recursos e `Basic` do SKU. Os SKUs disponíveis são `Classic`, `Basic`, `Standard` e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

A criação de um contêiner produz o seguinte resultado:

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Para enviar uma imagem por push para o Registro, você precisa fornecer credenciais para que o Registro aceite o push. Recupere essas credenciais usando o comando [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show). 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
```

O comando revela duas senhas que podem ser usadas com o nome de usuário.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

Agora que você tem as credenciais necessárias, faça logon no Registro de Contêiner do Azure usando o comando `docker login`. O nome do servidor é necessário para fazer logon. Use o formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

Confirme se o logon foi bem-sucedido. Envie a imagem por push usando o comando `docker push` e marcando a imagem com o nome do registro, seguido pelo nome da imagem e pela marcação.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Verifique se o push adicionou com êxito um contêiner ao Registro, listando os repositórios do ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A listagem das imagens no Registro confirma que `mydockerimage` está no Registro.

```json
[
  "mydockerimage"
]
```

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>Configurar o Aplicativo Web para usar o contêiner do Docker em um Registro Particular (opcional)

Configure aplicativos Web no Linux para que eles executem um contêiner armazenado no Registro de Contêiner do Azure. O uso do Registro de Contêiner do Azure é como o uso de qualquer Registro particular. Portanto, se precisar usar seu próprio Registro particular, as etapas para concluir esta tarefa serão semelhantes.

O comando [az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) exibe as senhas do registro de contêiner. Copie o nome de usuário e uma das senhas para que você possa usá-la para configurar o aplicativo Web na próxima etapa.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<azure-container-registry-name>"
}
```

Execute o comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set). Esse comando atribui a imagem personalizada do Docker ao aplicativo Web. Observe que você precisa de uma URL que esteja no formato `https://{your-registry-username}.azurecr.io`. Além disso, o aplicativo Web precisa do nome de usuário e da senha obtidos na etapa anterior para acessar o registro de contêiner.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> O `https` na URL do registro é necessário.
>

O comando revela um resultado semelhante à seguinte cadeia de caracteres JSON, mostrando que a alteração na configuração foi bem-sucedida:

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

[Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)
