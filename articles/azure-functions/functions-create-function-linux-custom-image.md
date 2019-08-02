---
title: Crie Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 66effb095edf7927a38fca53b2ff317e9bad468e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619618"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Crie uma função no Linux usando uma imagem personalizada

O Azure Functions permite hospedar suas funções no Linux em seu próprio contêiner personalizado. Você também pode [hospedar em um contêiner padrão do Serviço de Aplicativo do Azure](functions-create-first-azure-function-azure-cli-linux.md). Essa funcionalidade requer [o tempo de execução do Functions 2.x](functions-versions.md).

Neste tutorial, você aprenderá a implantar suas funções no Azure como uma imagem personalizada do Docker. Esse padrão será útil quando você precisar personalizar a imagem de contêiner interna. Convém usar uma imagem personalizada quando suas funções precisarem de uma versão de idioma específico, ou exigirem uma configuração ou dependência específica que não é fornecida na imagem interna. Imagens base com suporte para Azure Functions encontram-se no [repositório de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base). O [suporte para Python](functions-reference-python.md) está em versão prévia.

Este tutorial explica como usar o Azure Functions Core Tools para criar uma função em uma imagem personalizada do Linux. Publique essa imagem em um aplicativo de funções no Azure, que foi criado usando a CLI do Azure. Posteriormente, você atualizará a função para se conectar ao armazenamento de Filas do Azure. Você também a habilitará.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo de funções e o Dockerfile usando o Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar uma conta do Armazenamento do Azure.
> * Criar um plano de hospedagem Premium.
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Adicionar o monitoramento do Application Insights.

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

* Instale a [versão do Azure Core Tools 2.x](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem.  
Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicativo de funções local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual.

```bash
func init MyFunctionProj --docker
```

Quando você inclui a opção `--docker`, um dockerfile é gerada para o projeto. Esse arquivo é usado para criar um contêiner personalizado no qual executar o projeto. A imagem base usada depende da linguagem do tempo de execução do trabalho escolhida.  

Quando solicitado, escolha um tempo de execução do trabalho com as seguintes linguagens:

* `dotnet`: cria um projeto de biblioteca de classes do .NET Core (.csproj).
* `node`: cria um projeto de JavaScript.
* `python`: cria um projeto do Python.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Use o comando a seguir para navegar até a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

Examine o _Dockerfile_ na pasta raiz do projeto. Este arquivo descreve o ambiente necessário para executar o aplicativo de funções no Linux. O exemplo a seguir é um Dockerfile que cria um contêiner que executa um aplicativo de funções no tempo de execução do trabalho em JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> A lista completa de imagens base com suporte para Azure Functions encontram-se na [página de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Execute o comando `build`
Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `mydockerimage`, e uma marca, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker. Esse comando compila a imagem do Docker para o contêiner.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando o comando for executado, você verá algo parecido com a seguinte saída, que nesse caso é para um tempo de execução do trabalho em JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Teste a imagem localmente
Verifique se a imagem criada funciona executando a imagem do Docker em um contêiner local. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e envie o nome e a marcação da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução em um contêiner local do Docker, verifique se o aplicativo de funções e o contêiner estão funcionando corretamente navegando até <http://localhost:8080>.

![Teste o aplicativo de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Opcionalmente, você pode testar a função novamente, desta vez no contêiner local, usando a seguinte URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Depois de verificar o aplicativo de funções no contêiner, interrompa a execução. Agora, você pode enviar por push a imagem personalizada à sua conta do Hub do Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Envie por push a imagem personalizada ao Hub do Docker

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um registro. O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares.

Antes de enviar uma imagem por push, você deve entrar no Hub do Docker usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome de sua conta e digite sua senha no console do prompt. Para obter outras opções de senha de Hub do Docker, confira a [documentação de comandos de logon do docker](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Uma mensagem “Logon com êxito” confirma que você está conectado. Depois de conectado, envie a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verifique se o push foi bem-sucedido examinando o resultado do comando.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Agora, você pode usar essa imagem como a origem de implantação para um novo aplicativo de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Criar um plano Premium

Hospedagem do Linux para contêineres personalizados do Functions compatíveis em [planos Dedicados (Serviço de Aplicativo)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Este tutorial usa um plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md).

O exemplo a seguir cria um plano Premium chamado `myPremiumPlan` no **tipo de preço Elástico Premium 1** (`--sku EP1`), na região Oeste dos EUA (`-location WestUS`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>Criar e implantar a imagem personalizada

O aplicativo de funções gerencia a execução das funções em seu plano de hospedagem. Crie um aplicativo de funções a partir de uma imagem do Hub do Docker usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Como antes, `<docker-id>` é o nome de conta do Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

O parâmetro _deployment-container-image-name_ indica a imagem hospedada no Hub do Docker a ser usada para criar o aplicativo de funções. Use o comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para exibir informações sobre a imagem usada para a implantação. Use o comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar de uma imagem diferente.

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa da cadeia de conexão para se conectar à conta de armazenamento padrão. Quando você estiver publicando sua imagem personalizada em uma conta de contêiner privado, defina essas configurações de aplicativo como variáveis de ambiente no Dockerfile usando a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env), ou algo semelhante.

Nesse caso, `<storage_name>` é o nome da conta de armazenamento criada. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account). Adicione essas configurações de aplicativo ao aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se o contêiner for privado, você precisaria definir as seguintes configurações de aplicativo também  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Você terá que parar e iniciar o aplicativo de função para que esses valores sejam coletados

Agora você pode testar suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>Habilitar a implantação contínua

Um dos benefícios do uso de contêineres é ser capaz de implantar as atualizações automaticamente quando os contêineres são atualizados no registro. Habilitar a implantação contínua com o comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Esse comando retorna a URL de webhook de implantação após a implantação contínua ser habilitada. Você também pode usar o comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) para retornar essa URL. 

Copie a URL de implantação e navegue até seu repositório DockerHub, escolha a guia **Webhooks**, digite um **nome do Webhook** para o webhook, cole a URL em **URL do Webhook**e, em seguida, escolha o sinal de adição ( **+** ).

![Adicione o webhook em seu repositório DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Com o conjunto de webhook, todas as atualizações para a imagem vinculada no DockerHub fazem com que o aplicativo de funções baixe e instale a imagem mais recente.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

A maneira recomendada de monitorar a execução de suas funções é a integração do aplicativo de funções com o Azure Application Insights. Ao criar um aplicativo de funções no portal do Azure, essa integração é realizada por padrão. No entanto, quando você cria seu aplicativo de funções usando a CLI do Azure, não é feita a integração em seu aplicativo de funções no Azure.

Para habilitar o Application Insights no aplicativo de funções:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Crie um aplicativo de funções e o Dockerfile usando o Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar uma conta do Armazenamento do Azure.
> * Criar um plano Premium do Linux.
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Adicionar o monitoramento do Application Insights.

> [!div class="nextstepaction"] 
> [Saiba mais sobre as opções de implantação de funções no Azure](functions-deployment-technologies.md)
