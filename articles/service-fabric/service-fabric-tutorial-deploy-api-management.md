---
title: Integrar o Gerenciamento de API ao Service Fabric no Azure | Microsoft Docs
description: Saiba como começar a usar rapidamente o Gerenciamento de API do Azure e rotear o tráfego para um serviço de back-end no Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: c290b13ccb65c68c32d63638b15e8a3f59ef8010
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228097"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrar o Gerenciamento de API ao Service Fabric no Azure

A implantação do Gerenciamento de API do Azure com o Service Fabric é um cenário avançado.  O Gerenciamento de API é útil quando você precisa publicar APIs com um conjunto avançado de regras de roteamento para os serviços do Service Fabric de back-end. Os aplicativos em nuvem geralmente precisam de um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. No Service Fabric, um gateway pode ser qualquer serviço sem estado projetado para entrada de tráfego, como um aplicativo ASP.NET Core, Hubs de Eventos, Hub IoT ou Gerenciamento de API do Azure.

Este artigo mostra como configurar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para rotear o tráfego para um serviço de back-end no Service Fabric.  Quando terminar, você terá implantado o Gerenciamento de API para uma VNET e configurado uma operação de API para enviar tráfego para serviços sem monitoração de estado de back-end. Para saber mais sobre os cenários do Gerenciamento de API do Azure com Service Fabric, consulte o artigo [visão geral](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Este recurso está disponível nas camadas **Premium** e **Desenvolvedor** do Gerenciamento de API, devido ao suporte de rede virtual necessário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou a [CLI do Azure](/cli/azure/install-azure-cli).
* Crie um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro em um grupo de segurança de rede.
* Se você implantar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instale o [Visual Studio 2019](https://www.visualstudio.com) e as cargas de trabalho **Desenvolvimento do Azure**, **Desenvolvimento para a Web e ASP.NET** e **Desenvolvimento multiplataforma do .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).

## <a name="network-topology"></a>Topologia de rede

Agora que você tem um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure, implante o Gerenciamento de API na VNET (rede virtual) na sub-rede e no NSG designados para o Gerenciamento de API. Para este artigo, o modelo do Resource Manager de Gerenciamento de API é pré-configurado para usar os nomes da VNET, sub-rede e NSG que você configurou no [tutorial de cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md). Este artigo implanta a seguinte topologia para o Azure na qual o Gerenciamento de API e o Service Fabric estão em sub-redes da mesma Rede Virtual:

 ![Legenda de imagem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura

Entre na sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implantar um serviço de back-end do Service Fabric

Antes de configurar o Gerenciamento de API para rotear o tráfego para um serviço de back-end do Service Fabric, primeiro é necessário um serviço em execução para aceitar solicitações.  

Crie um Serviço Confiável do ASP.NET Core básico sem monitoração de estado utilizando o modelo de projeto de API Web padrão. Isso cria um ponto de extremidade HTTP para o seu serviço, que será exposto por meio do Gerenciamento de API do Azure.

Inicie o Visual Studio como administrador e crie um serviço ASP.NET Core:

 1. No Visual Studio, selecione Arquivo -> Novo projeto.
 2. Selecione o modelo de Aplicativo do Service Fabric em Nuvem e nomeie-o **"ApiApplication"** .
 3. Selecione o modelo do serviço do ASP.NET Core e nomeie o projeto **"WebApiService"** .
 4. Selecione a API Web ASP.NET Core modelo de projeto 2,1.
 5. Uma vez que o projeto é criado, abra `PackageRoot\ServiceManifest.xml` e remova o atributo `Port` da configuração do recurso do ponto de extremidade:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    A remoção da porta permite que Service Fabric especifique uma porta dinamicamente do intervalo de portas do aplicativo, aberta por meio do grupo de segurança de rede no modelo do Resource Manager de cluster, permitindo que o tráfego flua para ele do gerenciamento de API.

 6. Pressione F5 no Visual Studio para verificar se a API Web está disponível localmente.

    Abra o Service Fabric Explorer e faça busca detalhada de uma instância específica do serviço ASP.NET Core para visualizar o endereço básico no qual o serviço está ouvindo. Adicione `/api/values` ao endereço de base e abra-o em um navegador, o que invoca o método Get em ValuesController no modelo de API Web. Ele retorna a resposta padrão fornecida pelo modelo, uma matriz JSON que contém duas cadeias de caracteres:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto de extremidade que você expõe pelo Gerenciamento de API no Azure.

 7. Finalmente, implante o aplicativo em seu cluster no Azure. No Visual Studio, clique com o botão direito do mouse no projeto de Aplicativo e selecione **Publicar**. Forneça o seu ponto de extremidade de cluster (por exemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implantar o aplicativo em seu cluster do Service Fabric no Azure.

Um serviço sem estado ASP.NET Core nomeado `fabric:/ApiApplication/WebApiService` agora deve estar em execução no seu cluster do Service Fabric no Azure.

## <a name="download-and-understand-the-resource-manager-templates"></a>Baixar e compreender o modelo do Gerenciador de Recursos

Baixe e salve os seguintes modelos do Resource Manager e o arquivo de parâmetros:

* [Network-APIM. JSON][network-arm]
* [Network-APIM. Parameters. JSON][network-parameters-arm]
* [APIM. JSON][apim-arm]
* [APIM. Parameters. JSON][apim-parameters-arm]

O modelo *network-apim.json* implanta um novo grupo de segurança de rede e sub-rede na rede virtual em que o cluster do Service Fabric é implantado.

As seções a seguir descrevem os recursos que estão sendo definidos pelo modelo *apim.json*. Para saber mais, siga os links da documentação de referência de modelo em cada seção. Os parâmetros configuráveis definidos no arquivo de parâmetros *apim.parameters.json* são definidos mais adiante neste artigo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

O [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) descreve a instância do serviço de gerenciamento de API: nome, SKU ou camada, local do grupo de recursos, informações do editor e rede virtual.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura a segurança do gerenciamento de API. O Gerenciamento de API deve autenticar com seu cluster do Service Fabric para descoberta de serviço utilizando um certificado do cliente que tenha acesso ao seu cluster. Este artigo usa o mesmo certificado especificado anteriormente ao criar o [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), que, por padrão, pode ser usado para acessar seu cluster.

Este artigo usa o mesmo certificado para autenticação de cliente e segurança de nó a nó de cluster. É possível utilizar um certificado do cliente separado se você tiver um configurado para acessar seu cluster do Service Fabric. Forneça o **nome**, a **senha** e os **dados** (cadeia de caracteres codificada em base 64) do arquivo de chave privada (.pfx) do certificado de cluster que você especificou ao criar o cluster do Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) descreve o serviço de back-end para o qual o tráfego é encaminhado.

Para back-ends do Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço do Service Fabric específico. Isso permite que uma única política encaminhe para mais de um serviço no cluster. O campo **url** neste exemplo é um nome de serviço totalmente qualificado de um serviço em seu cluster ao qual todas as solicitações serão roteadas por padrão se nenhum nome de serviço for especificado em uma política de back-end. É possível utilizar um nome de serviço falso, como "fabric:/serviço/falso" se você não pretende ter um serviço de fallback. **resourceId** especifica o ponto de extremidade de gerenciamento de cluster.  **clientCertificateThumbprint** e **serverCertificateThumbprints** identificam certificados usados para autenticação com o cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) cria um produto. No Gerenciamento de API, um produto contém uma ou mais APIs, bem como uma quota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs dele.

Insira um **displayName** descritivo e uma **descrição** para o produto. Para este artigo, é necessária uma assinatura, mas não a aprovação por um administrador de assinatura.  Este **estado** de produto é "publicado" e é visível para os assinantes.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) cria uma API. Uma API no Gerenciamento de API representa um conjunto de operações que pode ser invocado pelas aplicações clientes. Após as operações serem adicionadas, a API é adicionada a um produto e pode ser publicada. Após uma API ser publicada, ela pode ser usada e assinada por desenvolvedores.

* **displayName** pode ser qualquer nome para a API. Para este artigo, use "Aplicativo do Service Fabric".
* **name** fornece um nome exclusivo e descritivo para a API, como "service-fabric-app". Esse nome será exibido nos portais do desenvolvedor e do editor.
* **serviceUrl** faz referência ao serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço. Para back-ends do Service Fabric, esse valor de URL não é usado. Aqui, você pode colocar qualquer valor. Para este artigo, por exemplo, "http\/:/servicefabric".
* **path** é acrescentado à URL base para o serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas por uma instância de um serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.
* **protocols** determina quais protocolos podem ser usados para acessar a API. Para este artigo, liste **http** e **https**.
* **path** é um sufixo para a API. Para este artigo, use "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) para que uma API de gerenciamento de API possa ser usada, as operações devem ser adicionadas à API.  Clientes externos usam uma operação para se comunicar com o serviço sem monitoração de estado do ASP.NET Core em execução no cluster do Service Fabric.

Para adicionar uma operação de API de front-end, preencha os valores:

* **displayName** e **description** descrevem a operação. Para este artigo, use "Values".
* **method** especifica o verbo HTTP.  Para este artigo, especifique **GET**.
* **urlTemplate** é acrescentado à URL base da API e identifica uma única operação HTTP.  Para este artigo, use `/api/values` se você tiver adicionado o serviço de back-end .NET ou `getMessage` se você tiver adicionado o serviço de back-end Java.  Por padrão, o caminho URL especificado aqui é o caminho URL enviado ao serviço do Service Fabric de back-end. Se você usar o mesmo caminho URL aqui que seu serviço usa, como "/api/values", a operação funcionará sem modificações adicionais. Também é possível especificar um caminho de URL diferente do caminho de URL utilizado pelo serviço do Service Fabric de back-end, caso em que também será necessário especificar uma reescrita do caminho na sua política de operação mais tarde.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) cria uma política de back-end, que reúne tudo. Dessa forma, o serviço do Service Fabric de back-end é configurado de acordo com as solicitações roteadas. É possível aplicar essa política a qualquer operação de API.  Para saber mais, confira [Visão geral das políticas](/azure/api-management/api-management-howto-policies).

A [configuração de back-end para o Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controles de roteamento de solicitação:

* Seleção de instância de serviço, especificando um nome de instância de serviço do Service Fabric, codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerado a partir da solicitação HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Resolução de partição gerando uma chave de partição utilizando qualquer esquema de particionamento do Service Fabric.
* Seleção de réplica para serviços com estado.
* Condições de repetição de resolução que permitem especificar as condições para resolver novamente um local de serviço e reenviar uma solicitação.

**policyContent** é o conteúdo XML Json com escape da política.  Para este artigo, crie uma política de back-end para rotear solicitações diretamente para o serviço sem estado .NET ou Java implantado anteriormente. Adicione um política `set-backend-service` em políticas de entrada.  Substitua o valor *sf-service-instance-name* por `fabric:/ApiApplication/WebApiService` se você tiver implantado o serviço de back-end .NET, ou por `fabric:/EchoServerApplication/EchoServerService` se tiver implantado o serviço Java.  *backend-id* faz referência a um recurso de back-end, nesse caso o recurso `Microsoft.ApiManagement/service/backends` definido no modelo *apim.json*. *backend-id* também pode fazer referência a outro recurso de back-end criado usando as APIs de Gerenciamento de API. Para este artigo, defina *backend-id* como o valor do parâmetro *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
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

## <a name="set-parameters-and-deploy-api-management"></a>Definir parâmetros e implantar o gerenciamento de API

Preencha os seguintes parâmetros vazios em *apim.parameters.json* para a implantação.

|Parâmetro|Valor|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Desenvolvedores|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 encoded string&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Cadeia de caracteres XML&gt;|

*certificatePassword* e *serviceFabricCertificateThumbprint* devem coincidir com o certificado de cluster usado para configurar o cluster.

*serviceFabricCertificate* é o certificado como uma cadeia de caracteres codificada em base 64, que pode ser gerada usando o seguinte script:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Em *inbound_policy*, substitua o valor *sf-service-instance-name* por `fabric:/ApiApplication/WebApiService` se você tiver implantado o serviço de back-end .NET, ou por `fabric:/EchoServerApplication/EchoServerService` se você tiver implantado o serviço Java. *backend-id* faz referência a um recurso de back-end, nesse caso o recurso `Microsoft.ApiManagement/service/backends` definido no modelo *apim.json*. *backend-id* também pode fazer referência a outro recurso de back-end criado usando as APIs de Gerenciamento de API. Para este artigo, defina *backend-id* como o valor do parâmetro *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Use o seguinte script para implantar o modelo do Resource Manager e os arquivos de parâmetro para o Gerenciamento de API:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testá-lo

Agora é possível tentar enviar uma solicitação para seu serviço de back-end no Service Fabric através do Gerenciamento de API diretamente do [portal do Azure](https://portal.azure.com).

 1. No serviço de Gerenciamento de API, selecione **API**.
 2. Na API do **aplicativo do Service Fabric** criada nas etapas anteriores, selecione a guia **Testar** e, em seguida, a operação **Valores**.
 3. Clique no botão **Enviar** para enviar uma solicitação de teste para o serviço de back-end.  Você deve ver uma resposta HTTP semelhante a:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Entre no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](https://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzResourceGroup](/en-us/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o uso do [Gerenciamento de API](/azure/api-management/import-and-publish).

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
vice-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.jsonn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
