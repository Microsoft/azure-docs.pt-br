---
title: Tutorial – Implantar um aplicativo na Malha do Azure Service Fabric
description: Neste tutorial, você aprende como implantar um aplicativo na Malha do Service Fabric usando um modelo.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 589e881eb48daf7da9cd2a934b14acfcc76dc5f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625409"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Tutorial: Implantar um aplicativo na Malha do Service Fabric usando um modelo

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a primeira parte de uma série. Você aprenderá como implantar um aplicativo de Malha do Azure Service Fabric usando um modelo.  O aplicativo é composto de um serviço de front-end de web do ASP.NET e um serviço de back-end de API Web ASP.NET Core, que são encontrados no Hub do Docker.  Efetue pull de duas imagens de contêiner do Hub do Docker e, em seguida, envie-as ao seu próprio registro privado. Em seguida, crie um modelo do Azure RM para o aplicativo e implante-o do seu registro de contêiner para a Malha do Service Fabric. Quando tiver terminado, você terá um aplicativo simples de lista de tarefas em execução na Malha do Serviço Fabric.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma instância privada do Registro de Contêiner do Azure
> * Enviar uma imagem de contêiner para o registro
> * Criar arquivos de modelo e parâmetros do RM
> * Implantar um aplicativo na Malha do Service Fabric

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Implantar um aplicativo na Malha do Service Fabric usando um modelo
> * [Dimensionar serviços dentro de um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Atualizar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Remover um aplicativo](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Instalar o Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Instale a CLI do Azure e a CLI da Malha do Service Fabric localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

As imagens de contêiner associadas aos serviços em seu aplicativo de Malha do Service Fabric devem ser armazenadas em um registro de contêiner.  Este tutorial utiliza a instância do Registro de Contêiner do Azure (ACR). 

Use as seguintes etapas para criar uma nova instância do ACR.  Se você já tiver uma instância do ACR configurada, você poderá ignorar.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure e defina a assinatura ativa.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Use o comando a seguir para criar um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Criar o registro de contêiner

Crie uma instância do ACR usando o comando `az acr create`. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir, o nome *myContainerRegistry* é usado. Se você receber um erro indicando que o nome do registro está em uso, escolha um nome diferente.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Quando o registro é criado, você vê uma saída semelhante à seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Enviar as imagens por push ao Registro de Contêiner do Azure

Este tutorial usa o aplicativo de exemplo de lista de tarefas como um exemplo.  As imagens de contêiner para os serviços [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) podem ser encontradas no Hub do Docker. Veja [Compilar um aplicativo Web da Malha do Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md) para obter informações sobre como compilar o aplicativo no Visual Studio. A Malha do Service Fabric pode executar contêineres do Docker no Windows ou Linux.  Se você estiver trabalhando com contêineres do Linux, selecione **Alternar para contêineres do Linux** no Docker.  Se você estiver trabalhando com contêineres do Windows, selecione **Alternar para contêineres do Windows** no Docker.

Para enviar por push uma imagem para uma instância do ACR, primeiro você deve ter uma imagem de contêiner. Se você ainda não tiver as imagens de contêiner local, use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para efetuar pull das imagens [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) do Hub do Docker.

>[!NOTE]
> Desde 2 de novembro de 2020, [limites de taxa de download se aplicam](https://docs.docker.com/docker-hub/download-rate-limit/) a solicitações anônimas e autenticadas para o Docker Hub de contas do plano gratuito do Docker e são impostas por endereço IP. 
> 
> Esses comandos fazem uso de imagens públicas do Docker Hub. Observe que você pode ter uma taxa limitada. Para obter mais detalhes, confira [Autenticar-se com o Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Efetuar pull das imagens do Windows:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Antes de poder enviar uma imagem por push no registro, você deve marcá-lo com o nome totalmente qualificado do seu servidor de logon ACR.

Execute o seguinte comando para obter o nome do servidor de logon completo da instância do ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Agora marque sua imagem do Docker usando o comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). O exemplo a seguir marca as imagens seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 e seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Se você estiver usando imagens diferentes, substitua os nomes de imagem no comando a seguir.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Entre no Registro de Contêiner do Azure.

```azurecli
az acr login -n myContainerRegistry
```

Enviar a imagem por push para a instância do ACR com o comando [push do docker](https://docs.docker.com/engine/reference/commandline/push/):

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Listar imagens de contêiner

Para ver os repositórios em sua instância do ACR, execute o seguinte:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

O exemplo a seguir lista as marcas no repositório **azure-mesh-todo-service**.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

A saída anterior confirma a presença de `azure-mesh-todo-service:1.0-nanoserver-1709` no registro de contêiner privado.  Também verifica a presença de `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Recuperar credenciais para o registro

> [!IMPORTANT]
> Não é recomendável habilitar o usuário administrador em uma instância do ACR para cenários de produção. Isso é feito aqui para sua conveniência. Para cenários de produção, use uma [entidade de serviço](../container-registry/container-registry-auth-service-principal.md) para autenticação de usuário e do sistema em cenários de produção.

Para implantar uma instância de contêiner do registro que foi criado usando um modelo, você deve fornecer as credenciais de registro durante a implantação. Primeiro, permita o usuário administrador no seu registro com o seguinte comando:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Em seguida, obtenha o nome do servidor de logon do registro, o nome de usuário e a senha usando os comandos a seguir:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Use os valores de nome do servidor de logon do ACR, nome de usuário e senha retornados ao criar os arquivos de modelo e parâmetros do RM na seção a seguir.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Baixar e explorar os arquivos de modelo e parâmetros

Um aplicativo de Malha do Serviço Fabric é um recurso do Azure que você pode implantar e gerenciar usando modelos do Azure Resource Manager (RM). Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Compreender a estrutura e sintaxe de modelos do RM](../azure-resource-manager/templates/template-syntax.md).

Este tutorial usa o exemplo de lista de tarefas como um exemplo.  Em vez de criar novos arquivos de modelo e parâmetros, baixe os arquivos [modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parâmetros
Quando há valores no modelo que você pretende alterar após a implantação do aplicativo ou se você deseja ter a opção de alterar a cada implantação (se você planeja reutilizar esse modelo para outras implantações), a prática recomendada é parametrizar os valores. A maneira correta de fazer isso é criar uma seção "parâmetros" na parte superior do seu modelo de implantação, em que você especifica nomes e propriedades de parâmetros, que serão referenciados posteriormente no modelo de implantação. Cada definição de parâmetro inclui *tipo*, *defaultValue* e uma seção opcional *metadados* com uma *descrição*.

A seção de parâmetros é definida na parte superior do seu modelo de implantação, logo antes da seção *recursos*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

No [modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json), os seguintes parâmetros são declarados: local, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  As descrições para cada parâmetro podem ser encontradas no arquivo de modelo de implantação.

Esses parâmetros são usados no arquivo [parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  Usar um arquivo de parâmetros separados lhe dá a capacidade de alterar valores de parâmetro de implantação a implantação sem atualizar o próprio modelo de implantação.

### <a name="overview-of-the-application-and-services"></a>Visão geral dos aplicativos e serviços

Os serviços são especificados no modelo como propriedades do recurso de aplicativo.  Os aplicativos são implantados em uma rede privada, que é declarada como um recurso no modelo.  Os serviços podem usar volumes para persistir dados, que são declarados como recursos no modelo.  Para cada serviço, o tipo de sistema operacional, pacotes de código, número de réplicas e a rede são especificados como propriedades do serviço.  Para cada pacote de códigos, especifique a imagem de contêiner, pontos de extremidade, memória e recursos de CPU e as credenciais de repositório de imagem. Em um alto nível, o modelo para um aplicativo da Malha do Service Fabric com vários serviços se parece com:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Consulte o arquivo [modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) para as especificações do aplicativo de lista de tarefas.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Implantar o aplicativo na Malha do Service Fabric
Crie o aplicativo e os recursos relacionados usando o comando a seguir e forneça as credenciais da etapa anterior [Recuperar credenciais para o registro](#retrieve-credentials-for-the-registry).

No arquivo de parâmetros, atualize os valores de parâmetros a seguir:

|Parâmetro|Valor|
|---|---|
|local|A região para implantar o aplicativo.  Por exemplo, "eastus".|
|registryPassword|A senha que você obteve anteriormente em [Recuperar credenciais para o registro](#retrieve-credentials-for-the-registry). Esse parâmetro no modelo é uma cadeia de caracteres segura e não será exibido no status de implantação ou comandos `az mesh service show`.|
|registryUserName|O nome de usuário que você obteve em [Recuperar credenciais para o registro](#retrieve-credentials-for-the-registry).|
|registryServer|O nome do servidor de registro que você obteve em [Recuperar credenciais para o registro](#retrieve-credentials-for-the-registry).|
|frontEndImage|A imagem de contêiner para o serviço de front-end.  Por exemplo, `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|A imagem de contêiner para o serviço de back-end.  Por exemplo, `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Para implantar o aplicativo, faça o seguinte:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Este comando produzirá um snippet JSON mostrado abaixo. Na seção ```outputs``` da saída JSON, copie a propriedade ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Essa informação vem da seção ```outputs``` no modelo ARM. Conforme mostrado abaixo, esta seção referencia o recurso de Gateway para buscar o endereço IP público. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Abrir o aplicativo

Depois que o aplicativo é implantado com êxito, obtenha o endereço IP público para o ponto de extremidade de serviço. O comando de implantação retorna o endereço IP público do ponto de extremidade de serviço. Opcionalmente, você também pode consultar o recurso de rede para localizar o endereço IP público do ponto de extremidade de serviço. O nome do recurso de rede para este aplicativo é `todolistappNetwork`, buscar informações sobre ele usando o comando a seguir. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Navegue até o endereço IP em um navegador da web.

## <a name="check-application-status"></a>Verificar o status do aplicativo

Você pode verificar o status do aplicativo usando o comando mostrar do aplicativo. O nome do aplicativo para o aplicativo implantado é "todolistapp", então, busque seus detalhes.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Examine os logs para o aplicativo implantado usando o comando `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um registro de contêiner privado
> * Enviar uma imagem de contêiner para o registro
> * Criar um arquivo de modelo e parâmetros
> * Implantar um aplicativo na Malha do Service Fabric

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Dimensionar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md)