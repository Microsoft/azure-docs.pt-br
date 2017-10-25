---
title: Integrar o Azure Service Fabric ao Gerenciamento de API | Microsoft Docs
description: Saiba como iniciar rapidamente com o Gerenciamento de API do Azure e o Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 705212675fc0a869a4374f621d5f2d7e035294dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Implantar o Gerenciamento de API com o Service Fabric
Este tutorial é a parte dois de uma série. Este tutorial mostra como configurar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para rotear o tráfego para um serviço de back-end no Service Fabric.  Quando terminar, você terá implantado o Gerenciamento de API para uma VNET, terá configurado uma operação de API para enviar tráfego para serviços sem monitoração de estado de back-end. Para saber mais sobre os cenários do Gerenciamento de API do Azure com Service Fabric, consulte o artigo [visão geral](service-fabric-api-management-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar o Gerenciamento de API
> * Configurar o Gerenciamento de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API a um produto

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure usando um modelo
> * Implantar o Gerenciamento de API com o Service Fabric

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [CLI 2.0 do Azure](/cli/azure/install-azure-cli).
- Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura
Este tutorial usa o [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, entre em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.
 
Entre na sua conta do Azure e selecione sua assinatura:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>Implantar o Gerenciamento de API
Os aplicativos em nuvem geralmente precisam de um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. No Service Fabric, um gateway pode ser qualquer serviço sem estado, como um aplicativo ASP.NET Core ou outro serviço projetado para entrada de tráfego, como Hubs de Evento, Hub IoT ou Gerenciamento de API do Azure. Este tutorial é uma introdução ao uso de Gerenciamento de API do Azure como um gateway para seus aplicativos do Service Fabric. O Gerenciamento de API integra-se diretamente com o Service Fabric, permitindo que APIs sejam publicadas com um conjunto de regras de roteamento avançado para serviços de back-end do Service Fabric . 

Agora que você tem um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou um [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure, implante o Gerenciamento de API na VNET (rede virtual) na sub-rede e no NSG designados para o Gerenciamento de API. Para este tutorial, o modelo do Resource Manager do Gerenciamento de API é pré-configurado para usar os nomes da VNET, da sub-rede e do NSG configurados no [tutorial do cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou no [tutorial do cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) anterior. 

Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Preencha os parâmetros vazios em `apim.parameters.json` para sua implantação.

Use o seguinte script para implantar o modelo do Resource Manager e os arquivos de parâmetro para o Gerenciamento de API:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Configurar o Gerenciamento de API

Depois que o cluster do Service Fabric e do Gerenciamento de API forem implantados, será possível definir configurações de segurança e um back-end do Service Fabric no Gerenciamento de API. Isso permite que você crie uma política de serviço de back-end que envia tráfego para o cluster do Service Fabric.

### <a name="configure-api-management-security"></a>Configurar a segurança do Gerenciamento de API

Para configurar o back-end do Service Fabric, primeiro você precisa definir as configurações de segurança do Gerenciamento de API. Para definir as configurações de segurança, acesse o serviço de Gerenciamento de API no portal do Azure.

#### <a name="enable-the-api-management-rest-api"></a>Habilite a API REST do Gerenciamento de API

Atualmente, a API REST do Gerenciamento de API é a única maneira de configurar um serviço de back-end. A primeira etapa é habilitar a API REST do Gerenciamento de API e protegê-la.

 1. No serviço de Gerenciamento de API, selecione **API de Gerenciamento** em **Segurança**.
 2. Marque a caixa de seleção **Habilitar REST API do Gerenciamento de API**.
 3. Observe a **URL da API de Gerenciamento**, que usaremos mais tarde para configurar o back-end do Service Fabric.
 4. Gere um **Token de acesso** selecionando uma data de validade e uma chave e, em seguida, clique no botão **Gerar** no final da página.
 5. Copie o **token de acesso** e salve-o.  Usaremos o token de acesso nas etapas a seguir. Observe que isso é diferente da chave primária e da chave secundária.

#### <a name="upload-a-service-fabric-client-certificate"></a>Upload de um certificado do cliente do Service Fabric

O Gerenciamento de API deve autenticar com seu cluster do Service Fabric para descoberta de serviço utilizando um certificado do cliente que tenha acesso ao seu cluster. Para simplificar, este tutorial usa o mesmo certificado especificado anteriormente ao criar o [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) ou o [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, por padrão, pode ser usado para acessar seu cluster.

 1. No serviço de Gerenciamento de API, selecione **Certificados do cliente** em **Segurança**.
 2. Clique no botão **+ Adicionar**.
 2. Selecione o arquivo de chave privada (.pfx) do certificado de cluster que você especificou ao criar seu cluster do Service Fabric, atribua um nome e forneça a senha de chave privada.

> [!NOTE]
> Este tutorial usa o mesmo certificado para autenticação de cliente e segurança de nó a nó de cluster. É possível utilizar um certificado do cliente separado se você tiver um configurado para acessar seu cluster do Service Fabric.

### <a name="configure-the-backend"></a>Configure o back-end

Agora que a segurança do Gerenciamento de API está configurada, é possível configurar o back-end do Service Fabric. Para back-ends do Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço do Service Fabric específico. Isso permite que uma única política encaminhe para mais de um serviço no cluster.

Essa etapa requer o token de acesso gerado anteriormente e a impressão digital do certificado de cluster carregado anteriormente para o Gerenciamento de API.

> [!NOTE]
> Se você utilizou um certificado do cliente separado na etapa anterior para o Gerenciamento de API, nessa etapa será necessária a impressão digital para o certificado do cliente, além da impressão digital do certificado de cluster.

Envie a seguinte solicitação HTTP PUT para a URL da API de Gerenciamento de API que você observou anteriormente ao habilitar a API REST do Gerenciamento de API para configurar o serviço back-end do Serviço Fabric. Uma resposta `HTTP 201 Created` deverá ser exibida quando o comando for bem-sucedido. Para obter mais informações sobre cada campo, consulte a [documentação de referência de API do back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) do Gerenciamento de API.

Comando HTTP e URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Cabeçalhos de solicitação:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Corpo da solicitação:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

O parâmetro **url** nesse exemplo é um nome de serviço totalmente qualificado de um serviço em seu cluster onde todos os pedidos são roteados por padrão se nenhum nome de serviço for especificado em uma política de back-end. É possível utilizar um nome de serviço falso, como "fabric:/serviço/falso" se você não pretende ter um serviço de fallback.

Consulte a [documentação de referência da API do back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) do Gerenciamento de API para obter mais detalhes em cada campo.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implantar um serviço de back-end do Service Fabric

Agora que você tem o Service Fabric configurado como um back-end para Gerenciamento de API, é possível criar políticas de back-end para suas APIs que enviam tráfego para serviços do Service Fabric. Mas, primeiro, é necessário um serviço executando no Service Fabric para aceitar solicitações.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Criar um serviço do Service Fabric com um ponto de extremidade HTTP

Para este tutorial, criaremos um serviço confiável do Núcleo de ASP.NET sem estado básico utilizando o modelo de projeto de API Web padrão. Isso cria um ponto de extremidade HTTP para o seu serviço que será exposto através do Gerenciamento de API do Azure:

```
/api/values
```

Inicie [configurando seu ambiente de desenvolvimento para o desenvolvimento de ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Após configurar seu ambiente de desenvolvimento, inicie o Visual Studio como Administrador e crie um serviço ASP.NET Core:

 1. No Visual Studio, selecione Arquivo -> Novo projeto.
 2. Selecione o modelo de Aplicativo do Service Fabric em Nuvem e nomeie-o **"ApiApplication"**.
 3. Selecione o modelo de serviço do ASP.NET Core e nomeie o projeto **"WebApiService"**.
 4. Selecione o modelo de projeto ASP.NET Core 1.1 da API Web.
 5. Uma vez que o projeto é criado, abra `PackageRoot\ServiceManifest.xml` e remova o atributo `Port` da configuração do recurso do ponto de extremidade:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Isso permite que o Service Fabric especifique uma porta dinamicamente do intervalo de portas do aplicativo, que abrimos através do Grupo de Segurança de Rede no modelo do Resource Manager do cluster, permitindo que o tráfego flua para o Gerenciamento de API.
 
 6. Pressione F5 no Visual Studio para verificar se a API Web está disponível localmente. 

    Abra o Service Fabric Explorer e faça busca detalhada de uma instância específica do serviço ASP.NET Core para visualizar o endereço básico no qual o serviço está ouvindo. Adicione `/api/values` ao endereço básico e abra-o em um navegador. Isso invoca o método Get no ValuesController no modelo da API Web. Ele retorna a resposta padrão fornecida pelo modelo, uma matriz JSON que contém duas cadeias de caracteres:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto de extremidade que você irá expor através do Gerenciamento de API no Azure.

 7. Finalmente, implante o aplicativo em seu cluster no Azure. [Utilizando o Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), clique com o botão direito do mouse no projeto de Aplicativo e selecione **Publicar**. Forneça o seu ponto de extremidade de cluster (por exemplo, `mycluster.westus.cloudapp.azure.com:19000`) para implantar o aplicativo em seu cluster do Service Fabric no Azure.

Um serviço sem estado ASP.NET Core nomeado `fabric:/ApiApplication/WebApiService` agora deve estar em execução no seu cluster do Service Fabric no Azure.

## <a name="create-an-api-operation"></a>Criar uma operação de API

Agora, estamos prontos para criar uma operação no Gerenciamento de API que os clientes externos utilizam para se comunicar com o serviço sem estado ASP.NET Core que está executando no cluster do Service Fabric.

1. Entren no portal do Azure e navegue até a implantação do serviço de Gerenciamento de API.
2. Na folha do serviço de Gerenciamento de API, selecione **APIs**
3. Adicione uma nova API clicando em **+API**, em seguida na caixa **API em branco** e preenchendo a caixa de diálogo:

    - **URL do serviço Web**: para back-ends do Service Fabric esse valor de URL não é utilizado. Aqui, você pode colocar qualquer valor. Para este tutorial, use: "http://servicefabric".
    - **Nome de exibição**: forneça qualquer nome para sua API. Para este tutorial, use "Aplicativo do Service Fabric".
    - **Nome**: insira "service-fabric-app".
    - **Esquema de URL**: selecione **HTTP**, **HTTPS** ou **ambos**. Para este tutorial, use **ambos**.
    - **Sufixo de URL da API**: forneça um sufixo para a API. Para este tutorial, use “myapp”.
 
4. Selecione **Aplicativo do Service Fabric** na lista de APIs e clique em **+ Adicionar operação** para adicionar uma operação de API de front-end. Preencha os valores:
    
    - **URL**: selecione **GET** e forneça um caminho URL para a API. Para este tutorial, use "/api/values".  Por padrão, o caminho URL especificado aqui é o caminho URL enviado ao serviço do Service Fabric de back-end. Se você usar o mesmo caminho URL aqui que seu serviço usa, nesse caso "/api/values", a operação funcionará sem modificações adicionais. Além disso, é possível especificar um caminho URL que seja diferente do caminho URL utilizado pelo serviço do Service Fabric de beck-end, caso em que também será necessário especificar posteriormente uma reescrita do caminho na sua política de operação.
    - **Nome de exibição**: forneça um nome para a API. Para este tutorial, use “Values”.

5. Clique em **Salvar**.

## <a name="configure-a-backend-policy"></a>Configure uma política de back-end

A política de back-end vincula tudo em conjunto. Dessa forma, o serviço do Service Fabric de back-end é configurado de acordo com as solicitações roteadas. É possível aplicar essa política a qualquer operação de API. A [configuração de back-end para o Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controles de roteamento de solicitação: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço do Service Fabric, codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerado a partir da solicitação HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição gerando uma chave de partição utilizando qualquer esquema de particionamento do Service Fabric.
 - Seleção de réplica para serviços com estado.
 - Condições de repetição de resolução que permitem especificar as condições para resolver novamente um local de serviço e reenviar uma solicitação.

Para este tutorial, crie uma política de back-end que roteie as solicitações diretamente para o serviço sem estado no ASP.NET Core implantado anteriormente:

 1. Selecione e edite as **políticas de entrada** para a operação Values clicando no ícone de edição e, em seguida, selecionando **Modo de exibição de código**.
 2. No editor de código de política, adicione uma política `set-backend-service` em políticas de entrada, conforme mostrado e clique no botão **Salvar** :
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

Para obter um conjunto completo de atributos de política de back-end do Service Fabric, consulte a [documentação de back-end do Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

### <a name="add-the-api-to-a-product"></a>Adicionar a API a um produto 

Antes de poder chamar a API, ela deve ser adicionada a um produto onde você pode conceder acesso a usuários. 

 1. No serviço de Gerenciamento de API, selecione **Produtos**.
 2. Por padrão, o Gerenciamento de API oferece dois produtos: Starter e Ilimitado. Selecione o produto Ilimitado.
 3. Selecione **+Adicionar APIs**.
 4. Selecione a `Service Fabric App` API criada nas etapas anteriores e clique no botão **Selecionar**.

### <a name="test-it"></a>Testá-lo

Agora é possível tentar enviar uma solicitação para seu serviço de back-end no Service Fabric através do Gerenciamento de API diretamente do portal do Azure.

 1. No serviço de Gerenciamento de API, selecione **API**.
 2. Na API do **aplicativo do Service Fabric** criada nas etapas anteriores, selecione a guia **Testar** e, em seguida, a operação **Valores**.
 3. Clique no botão **Enviar** para enviar uma solicitação de teste para o serviço de back-end.  Você deve ver uma resposta HTTP semelhante a:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o Gerenciamento de API
> * Configurar o Gerenciamento de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API a um produto


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json