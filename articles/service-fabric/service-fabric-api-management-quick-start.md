---
title: "Azure Service Fabric com início rápido de Gerenciamento de API | Microsoft Docs"
description: Este guia mostra como iniciar rapidamente com o Gerenciamento de API e o Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: 2969834713fc7c2f1a2e281a6c988158d803dc45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-quick-start"></a>Service Fabric com início rápido de Gerenciamento de API

Este guia mostra como configurar o Gerenciamento de API do Azure com o Service Fabric e configurar sua primeira operação de API para enviar tráfego para serviços de back-end no Service Fabric. Para saber mais sobre os cenários do Gerenciamento de API do Azure com Service Fabric, consulte o artigo [visão geral](service-fabric-api-management-overview.md). 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Implantar o Gerenciamento de API e o Service Fabric ao Azure

A primeira etapa é implantar o Gerenciamento de API e um cluster do Service Fabric no Azure em uma Rede Virtual compartilhada. Isso permite que o Gerenciamento de API se comunique diretamente com o Service Fabric para que ele possa executar a descoberta do serviço, a resolução da partição de serviço e encaminhar o tráfego diretamente para qualquer serviço de back-end no Service Fabric.

### <a name="topology"></a>Topologia

Este guia implanta a seguinte topologia para o Azure, na qual o Gerenciamento de API e o Service Fabric estão em sub-redes da mesma Rede Virtual:

 ![Legenda de imagem][sf-apim-topology-overview]

Para iniciar rapidamente, os modelos do Resource Manager são fornecidos para cada etapa de implantação:

 - Topologia de rede:
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Cluster do Service Fabric:
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - Gerenciamento de API:
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura

Este guia usa o [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, entre em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.
 
Entre na sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione sua assinatura:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um novo grupo de recursos para sua implantação. Dê um nome e um local.

```powershell
New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Implante a topologia da rede

O primeiro passo é configurar a topologia de rede para a qual o Gerenciamento de API e o cluster do Service Fabric serão implantados. O modelo [network.json][network-arm] do Resource Manager é configurado para criar uma VNET (Rede Virtual) com duas sub-redes e dois NSG (Grupos de Segurança de Rede). 

O arquivo de parâmetros [network.parameters.json][network-parameters-arm] contém os nomes das sub-redes e NSGs em que o Gerenciamento de API e o Service Fabric serão implantados. Para este guia, os valores de parâmetros não precisam ser alterados. Os modelos do Resource Manager do Service Fabric e Gerenciamento de API utilizam esses valores, portanto, se forem aqui modificados, então, será necessário modificá-los nos outros modelos do Resource Manager. 

 1. Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. Use o seguinte comando do PowerShell para implantar o modelo do Resource Manager e os arquivos de parâmetros para a configuração de rede:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Implantar o cluster do Service Fabric

Uma vez que os recursos de rede concluíram a implantação, a próxima etapa é implantar um cluster do Service Fabric na VNET na sub-rede e o NSG designado para o cluster do Service Fabric. Para este tutorial, o modelo do Resource Manager do Service Fabric é pré-configurado para usar os nomes da VNET, sub-rede e do NSG que você configurou na etapa anterior. 

O modelo do Resource Manager do cluster do Service Fabric é configurado para criar um cluster seguro com segurança de certificado. O certificado é utilizado para proteger a comunicação do nó para nó para o seu cluster e gerenciar o acesso do usuário ao seu cluster do Service Fabric. O Gerenciamento de API utiliza esse certificado para acessar o Serviço de Cadastramento do Service Fabric para descoberta de serviço.

Esta etapa exige ter um certificado no Key Vault para segurança de cluster. Para obter mais informações sobre como configurar um cluster seguro com o Key Vault, consulte [este guia em criar um cluster no Azure usando o Resource Manager](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> É possível adicionar a autenticação do Azure Active Directory, além do certificado utilizado para acesso ao cluster. O Azure Active Directory é a maneira recomendada para gerenciar o acesso do usuário ao cluster do Service Fabric, mas não é necessário concluir este tutorial. Um certificado é exigido de qualquer forma para a segurança de nó para nó de cluster e para autenticação de Gerenciamento de API do Azure, que atualmente não oferece suporte para autenticação com Azure Active Directory para back-end do Service Fabric.

 1. Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. Preencha os parâmetros vazios no arquivo `cluster.parameters.json` para sua implantação, incluindo as [informações do Key Vault](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) para o certificado de cluster.

 3. Utilize o seguinte comando do PowerShell para implantar o modelo do Resource Manager e arquivos de parâmetros para criar o cluster do Service Fabric:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Implantar o Gerenciamento de API

Finalmente, implante o Gerenciamento de API na VNET na sub-rede e no NSG designado para o Gerenciamento de API. Não é necessário aguardar a implantação do cluster do Service Fabric terminar, antes de implantar o gerenciamento de API. 

Para este tutorial, o modelo do Resource Manager do Gerenciamento de API é pré-configurado para usar os nomes da VNET, sub-rede e do NSG que você configurou na etapa anterior. 

 1. Baixe o seguinte modelo do Resource Manager e arquivo de parâmetros:
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. Preencha os parâmetros vazios em `apim.parameters.json` para sua implantação.

 3. Utilize o seguinte comando do PowerShell para implantar o modelo do Resource Manager e os arquivos de parâmetros para o Gerenciamento de API:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Configurar o Gerenciamento de API

Uma vez que o cluster do Service Fabric e o Gerenciamento de API tenha sido implantado será possível configurar um back-end do Service Fabric no Gerenciamento de API. Isso permite que você crie uma política de serviço de back-end que envia tráfego para o cluster do Service Fabric.

### <a name="api-management-security"></a>Segurança de Gerenciamento de API

Para configurar o back-end do Service Fabric, primeiro você precisa definir as configurações de segurança do Gerenciamento de API. Para definir as configurações de segurança, acesse o serviço de Gerenciamento de API no portal do Azure.

#### <a name="enable-the-api-management-rest-api"></a>Habilite a API REST do Gerenciamento de API

Atualmente, a API REST do Gerenciamento de API é a única maneira de configurar um serviço de back-end. A primeira etapa é habilitar a API REST do Gerenciamento de API e protegê-la.

 1. No serviço de Gerenciamento de API, selecione **API de Gerenciamento - VISUALIZAÇÃO** em **Segurança**.
 2. Marque a caixa de seleção **Habilitar REST API do Gerenciamento de API**.
 3. Observe a URL de API de Gerenciamento - essa é a URL que usaremos mais tarde para configurar o back-end do Service Fabric
 4. Gere um **Token de acesso** selecionando uma data de validade e uma chave e, em seguida, clique no botão **Gerar** no final da página.
 5. Copie o **token de acesso** e salve-o - utilizaremos isso nas seguintes etapas. Observe que isso é diferente da chave primária e da chave secundária.

#### <a name="upload-a-service-fabric-client-certificate"></a>Upload de um certificado do cliente do Service Fabric

O Gerenciamento de API deve autenticar com seu cluster do Service Fabric para descoberta de serviço utilizando um certificado do cliente que tenha acesso ao seu cluster. Por simplicidade, esse tutorial utiliza o mesmo certificado especificado ao criar o cluster do Service Fabric que, por padrão, pode ser utilizado para acessar seu cluster.

 1. No serviço de gerenciamento de API, selecione **Certificados do cliente - VISUALIZAÇÃO** em **Segurança**.
 2. Clique no botão **+ Adicionar**
 2. Selecione o arquivo de chave privada (.pfx) do certificado de cluster que você especificou ao criar seu cluster do Service Fabric, atribua um nome e forneça a senha de chave privada.

> [!NOTE]
> Este tutorial usa o mesmo certificado para autenticação de cliente e segurança de nó a nó de cluster. É possível utilizar um certificado do cliente separado se você tiver um configurado para acessar seu cluster do Service Fabric.

### <a name="configure-the-backend"></a>Configure o back-end

Agora que a segurança do Gerenciamento de API está configurada, é possível configurar o back-end do Service Fabric. Para back-ends do Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço do Service Fabric específico. Isso permite que uma única política encaminhe para mais de um serviço no cluster.

Essa etapa requer o token de acesso gerado anteriormente e a impressão digital do certificado de cluster que você enviou para o Gerenciamento de API na etapa anterior.

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

O parâmetro **url** nesse exemplo é um nome de serviço totalmente qualificado de um serviço em seu cluster onde todos os pedidos são roteados por padrão se nenhum nome de serviço for especificado em uma política de back-end. É possível utilizar um nome de serviço falso, como "fabric:/serviço/falso" se você não pretende ter um serviço de fallback. Lembre-se, o **url** deverá estar no formato "fabric:/aplicativo/serviço" mesmo se for um serviço de fallback falso.

Consulte a [documentação de referência da API do back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) do Gerenciamento de API para obter mais detalhes em cada campo.

#### <a name="example"></a>Exemplo

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
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
 2. Na folha do serviço de Gerenciamento de API, selecione **APIs - Visualização**
 3. Adicione uma nova API clicando na caixa **API em Branco** e preenchendo a caixa de diálogo:

     - **URL do serviço Web**: para back-ends do Service Fabric esse valor de URL não é utilizado. Aqui, você pode colocar qualquer valor. Neste tutorial, utilize: `http://servicefabric`.
     - **Nome**: forneça qualquer nome para sua API. Neste tutorial, utilize `Service Fabric App`.
     - **Esquema de URL**: selecione HTTP, HTTPS ou ambos. Neste tutorial, utilize `both`.
     - **Sufixo de URL da API**: forneça um sufixo para a API. Neste tutorial, utilize `myapp`.
 
 4. Uma vez que a API é criada, clique em **+ Adicionar operação** para adicionar uma operação de API de front-end. Preencha os valores:
    
     - **URL**: selecione `GET` forneça um caminho URL para a API. Neste tutorial, utilize `/api/values`.
     
       Por padrão, o caminho URL especificado aqui é o caminho URL enviado ao serviço do Service Fabric de back-end. Se você utiliza o mesmo caminho URL que aquele do serviço, nesse caso `/api/values`, a operação funciona sem modificações adicionais. Além disso, é possível especificar um caminho URL que seja diferente do caminho URL utilizado pelo serviço do Service Fabric de beck-end, caso em que também será necessário especificar posteriormente uma reescrita do caminho na sua política de operação.
     - **Nome de exibição**: forneça um nome para a API. Neste tutorial, utilize `Values`.

## <a name="configure-a-backend-policy"></a>Configure uma política de back-end

A política de back-end vincula tudo em conjunto. Dessa forma, o serviço do Service Fabric de back-end é configurado de acordo com as solicitações roteadas. É possível aplicar essa política a qualquer operação de API. A [configuração de back-end para o Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controles de roteamento de solicitação: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço do Service Fabric, codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerado a partir da solicitação HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição gerando uma chave de partição utilizando qualquer esquema de particionamento do Service Fabric.
 - Seleção de réplica para serviços com estado.
 - Condições de repetição de resolução que permitem especificar as condições para resolver novamente um local de serviço e reenviar uma solicitação.

Para este tutorial, crie uma política de back-end que roteie as solicitações diretamente para o serviço sem estado no ASP.NET Core implantado anteriormente:

 1. Selecione e edite as **políticas de entrada** para a `Values` operação clicando no ícone de edição e, em seguida, selecionando **Modo de Exibição de Código**.
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

### <a name="add-the-api-to-a-product"></a>Adicione a API a um produto. 

Antes de poder chamar a API, ela deve ser adicionada a um produto onde você pode conceder acesso a usuários. 

 1. No serviço de gerenciamento de API, selecione **Produtos - VISUALIZAÇÃO**.
 2. Por padrão, o Gerenciamento de API oferece dois produtos: Starter e Ilimitado. Selecione o produto Ilimitado.
 3. Selecione APIs.
 4. Clique no botão **+ Adicionar**.
 5. Selecione a `Service Fabric App` API criada nas etapas anteriores e clique no botão **Selecionar**.

### <a name="test-it"></a>Testá-lo

Agora é possível tentar enviar uma solicitação para seu serviço de back-end no Service Fabric através do Gerenciamento de API diretamente do portal do Azure.

 1. No serviço de Gerenciamento de API, selecione **API - VISUALIZAÇÃO**.
 2. Na `Service Fabric App` API criada nas etapas anteriores, selecione a guia **Teste**.
 3. Clique no botão **Enviar** para enviar uma solicitação de teste para o serviço de back-end.

## <a name="next-steps"></a>Próximas etapas

Neste ponto, é possível ter uma configuração básica com o Service Fabric e o Gerenciamento de API.

Este tutorial utiliza a autenticação de usuário básica baseada em certificado para o cluster do Service Fabric para ajudá-lo a configurar rapidamente. A autenticação de usuário mais avançada para o cluster do Service Fabric é preferível com a [autenticação Azure Active Directory](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Em seguida, [crie e defina as configurações avançadas do produto no Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) de modo a preparar seu aplicativo para o tráfego do mundo real.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png
