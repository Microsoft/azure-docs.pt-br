---
title: Criar uma imagem personalizada para o Aplicativo Web para Contêineres – Serviço de Aplicativo do Azure | Microsoft Docs
description: Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres.
keywords: serviço de aplicativo do azure, aplicativo web, linux, docker, contêiner
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b48ec72a1f0a4178dad66ed31c544399e90c5293
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484509"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Tutorial: Criar uma imagem personalizada e executá-la no Serviço de Aplicativo por meio de um registro particular

O [Serviço de Aplicativo](app-service-linux-intro.md) fornece imagens internas do Docker no Linux com suporte para versões específicas, como PHP 7.3 e Node.js 10.14. O Serviço de Aplicativo usa a tecnologia de contêiner do Docker para hospedar imagens internas e personalizadas como uma plataforma como serviço. Neste tutorial, você aprenderá a criar uma imagem personalizada e executá-la no Serviço de Aplicativo. Esse padrão é útil quando as imagens internas não incluem a linguagem de sua escolha ou quando seu aplicativo requer uma configuração específica que não é fornecida nas imagens internas.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar uma imagem personalizada em um registro de contêiner particular
> * Executar a imagem personalizada no Serviço de Aplicativo
> * Configurar variáveis de ambiente
> * Atualizar e reimplantar a imagem
> * Acessar logs de diagnóstico
> * Conectar-se ao contêiner usando SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local e, em seguida, alterar para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

No repositório Git, confira o _Dockerfile_. Este arquivo descreve o ambiente do Python necessário para executar seu aplicativo. Além disso, a imagem configura um servidor [SSH](https://www.ssh.com/ssh/protocol/) para a comunicação segura entre o contêiner e o host.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Crie a imagem do Docker com o comando `docker build`.

```bash
docker build --tag mydockerimage .
```

Teste se o build funciona executando o contêiner do Docker. Execute o comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) e passe o nome e a marca da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Verifique se o aplicativo Web e o contêiner estão funcionando corretamente, navegando para `http://localhost:8000`.

![Testar o aplicativo Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Para criar um aplicativo que usa a imagem recém-criada, execute comandos da CLI do Azure que criam um grupo de recursos, enviam a imagem por push e, em seguida, criam o aplicativo Web do Plano do Serviço de Aplicativo para executá-lo.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

No Cloud Shell, use o comando [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) para criar um Registro de Contêiner do Azure.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Entrar no Registro de Contêiner do Azure

Para enviar uma imagem por push para o registro, é necessário se autenticar no registro particular. No Cloud Shell, use o comando [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) para recuperar as credenciais do registro criado.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

O resultado revela duas senhas, juntamente com o nome de usuário.

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
  "username": "<registry-username>"
}
```

Na janela do terminal local, entre no Registro de Contêiner do Azure usando o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<azure-container-registry-name>* e *\<registry-username>* pelos valores do registro. Quando solicitado, digite uma das senhas da etapa anterior.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Confirme se o logon foi bem-sucedido.

### <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Marque a imagem local para o Registro de Contêiner do Azure. Por exemplo:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Envie a imagem por push usando o comando `docker push`. Marque a imagem com o nome do registro, seguido pelo seu nome da imagem e a marca.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Novamente no Cloud Shell, verifique se o push foi bem-sucedido.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Você deverá obter o resultado a seguir.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Criar plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um [aplicativo Web](app-service-linux-intro.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Substitua _\<app-name>_ por um nome de aplicativo exclusivo e _\<azure-container-registry-name>_ pelo nome do registro.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Configurar as credenciais de registro no aplicativo Web

Para que o Serviço de Aplicativo efetue pull da imagem particular, ele precisará obter informações sobre o registro e a imagem. No Cloud Shell, forneça-as com o comando [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Substitua *\<app-name>* pelo nome do aplicativo, *\<azure-container-registry-name>* pelo nome do Registro de Contêiner do Azure, _\<registry-username>_ pelo nome de usuário do registro e _\<password>_ pela senha.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Ao usar outro registro que não seja o Hub do Docker, `--docker-registry-server-url` precisa ser formatado como `https://` seguido do nome de domínio totalmente qualificado do registro.
>

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

A maioria das imagens do Docker usa variáveis de ambiente personalizadas, como uma porta diferente de 80. Informe o Serviço de Aplicativo da porta usada pela imagem usando a configuração de aplicativo `WEBSITES_PORT`. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_.

Para definir as configurações do aplicativo, use o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testar o aplicativo Web

Verifique se o aplicativo Web funciona procurando por ele (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Na primeira vez que você acessar o aplicativo, pode levar algum tempo, porque o Serviço de Aplicativo precisa efetuar pull da imagem inteira. Se o navegador atingir o tempo limite, bastará atualizar a página.

![Testar a configuração de porta do aplicativo Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Alterar o aplicativo Web e reimplantá-lo

No seu repositório Git local, abra app/templates/app/index.html. Localize o primeiro elemento HTML e altere-o para:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Depois de modificar o arquivo do Python e salvá-lo, recrie e envie por push a nova imagem do Docker. Em seguida, reinicie o aplicativo Web para que as alterações entrem em vigor. Use os mesmos comandos usados anteriormente neste tutorial. Veja [Criar a imagem com base no arquivo do Docker](#build-the-image-from-the-docker-file) e [Enviar uma imagem por push para o Registro de Contêiner do Azure](#push-image-to-azure-container-registry). Teste o aplicativo Web, seguindo as instruções em [Testar o aplicativo Web](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para habilitar a conexão SSH com o contêiner, a imagem personalizada precisa ser configurada para ele. Vamos dar uma olhada no repositório de exemplo que já tem a configuração necessária.

* No [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), o código a seguir instala o servidor SSH e também define as credenciais de entrada.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas por meio do Site do Kudu/SCM. O site do Kudu/SCM é autenticado com sua conta do Azure.

* O [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) cópias o arquivo [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) no repositório para o diretório */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* O [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) expõe a porta 2222 no contêiner. Ela é uma porta interna que pode ser acessada somente por contêineres dentro da rede ponte de uma rede virtual privada. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* O [script de entrada](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) inicia o servidor SSH.

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

```bash
top
```

O comando `top` expõe todos os processos em execução em um contêiner.

```
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

Parabéns! Você configurou um contêiner personalizado do Linux no Serviço de Aplicativo.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Implantar uma imagem personalizada em um registro de contêiner particular
> * Executar a imagem personalizada no Serviço de Aplicativo
> * Configurar variáveis de ambiente
> * Atualizar e reimplantar a imagem
> * Acessar logs de diagnóstico
> * Conectar-se ao contêiner usando SSH

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: Mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo WordPress cm vários contêineres](tutorial-multi-container-app.md)
