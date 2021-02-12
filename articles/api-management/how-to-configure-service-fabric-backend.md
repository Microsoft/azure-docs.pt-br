---
title: Configurar o back-end Service Fabric no gerenciamento de API do Azure | Microsoft Docs
description: Como criar um back-end de serviço Service Fabric no gerenciamento de API do Azure usando o portal do Azure
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500624"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Configurar um back-end Service Fabric no gerenciamento de API usando o portal do Azure

Este artigo mostra como configurar um serviço de [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) como um back-end de API personalizado usando o portal do Azure. Para fins de demonstração, ele mostra como configurar um serviço de ASP.NET Core confiável básico sem estado como o back-end Service Fabric.

Para obter informações, consulte [back-ends no gerenciamento de API](backends.md).

## <a name="prerequisites"></a>Pré-requisitos

Pré-requisitos para configurar um serviço de exemplo em um Cluster Service Fabric executando o Windows como um back-end personalizado:

* **Ambiente de desenvolvimento do Windows** -instale o [Visual Studio 2019](https://www.visualstudio.com) e o desenvolvimento **do Azure**, **ASP.net e desenvolvimento** para a Web e cargas de trabalho de **desenvolvimento de plataforma cruzada do .NET Core** . Em seguida, configure um [ambiente de desenvolvimento .NET](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster** -consulte [tutorial: implantar um Cluster Service Fabric executando o Windows em uma rede virtual do Azure](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Você pode criar um cluster com um certificado X. 509 existente ou para fins de teste, criar um certificado novo e autoassinado. O cluster é criado em uma rede virtual.

* **Aplicativo de Service Fabric de exemplo** – crie um aplicativo de API Web e implante no Cluster Service Fabric conforme descrito em [integrar o gerenciamento de api com o Service Fabric no Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Estas etapas criam um serviço de ASP.NET Core confiável básico sem estado usando o modelo de projeto de API Web padrão. Posteriormente, você expõe o ponto de extremidade HTTP para esse serviço por meio do gerenciamento de API do Azure.

    Anote o nome do aplicativo, por exemplo `fabric:/myApplication/myService` . 

* **Instância de gerenciamento de API** – uma instância de gerenciamento de API nova ou existente na camada **Premium** ou de  **desenvolvedor** e na mesma região que o cluster de Service Fabric. Se você precisar de um, [crie uma instância de gerenciamento de API](get-started-create-service-instance.md).

* **Rede virtual** -Adicione sua instância de gerenciamento de API à rede virtual que você criou para o Cluster Service Fabric. O gerenciamento de API requer uma sub-rede dedicada na rede virtual.

  Para obter as etapas para habilitar a conectividade de rede virtual para a instância de gerenciamento de API, consulte [como usar o gerenciamento de API do Azure com redes virtuais](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Criar back-end-Portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Adicionar Service Fabric certificado de cluster ao gerenciamento de API

O certificado de Cluster Service Fabric é armazenado e gerenciado em um cofre de chaves do Azure associado ao cluster. Adicione este certificado à sua instância de gerenciamento de API como um certificado de cliente.

Para obter as etapas para adicionar um certificado à sua instância de gerenciamento de API, consulte [como proteger serviços de back-end usando a autenticação de certificado do cliente no gerenciamento de API do Azure](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> É recomendável adicionar o certificado ao gerenciamento de API referenciando o certificado do cofre de chaves. 

### <a name="add-service-fabric-backend"></a>Adicionar Service Fabric back-end

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **APIs**, selecione **back-ends**  >  **+ Adicionar**.
1. Insira um nome de back-end e uma descrição opcional
1. Em **tipo**, selecione **Service Fabric**.
1. Em **URL de tempo de execução**, insira o nome do serviço de back-end Service Fabric ao qual o gerenciamento de API encaminhará as solicitações. Exemplo: `fabric:/myApplication/myService`. 
1. Em **número máximo de novas tentativas de resolução de partição**, insira um número entre 0 e 10.
1. Insira o ponto de extremidade de gerenciamento do Cluster Service Fabric. Esse ponto de extremidade é a URL do cluster na porta `19080` , por exemplo, `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. Em **certificado de cliente**, selecione o Service Fabric certificado de cluster que você adicionou à sua instância de gerenciamento de API na seção anterior.
1. No **método de autorização de ponto de extremidade de gerenciamento**, insira uma impressão digital ou um nome X509 de servidor de um certificado usado pelo serviço de gerenciamento de Cluster Service Fabric para comunicação TLS.
1. Habilite as configurações **validar cadeia de certificados** e **validar nome do certificado** .
1. Em **credenciais de autorização**, forneça credenciais, se necessário, para acessar o serviço de back-end configurado no Service Fabric. Para o aplicativo de exemplo usado neste cenário, as credenciais de autorização não são necessárias.
1. Selecione **Criar**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Criar um back-end do Service Fabric":::

## <a name="use-the-backend"></a>Usar o back-end

Para usar um back-end personalizado, faça referência a ele usando a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) política. Essa política transforma a URL base do serviço de back-end padrão de uma solicitação de API de entrada para um back-end especificado, nesse caso, o Service Fabric back-end. 

A `set-backend-service` política pode ser útil com uma API existente para transformar uma solicitação de entrada em um back-end diferente daquele especificado nas configurações da API. Para fins de demonstração neste artigo, crie uma API de teste e defina a política para direcionar solicitações de API para o back-end de Service Fabric. 

### <a name="create-api"></a>Criar API

Siga as etapas em [Adicionar uma API manualmente](add-api-manually.md) para criar uma API em branco.

* Nas configurações de API, deixe a **URL do serviço Web** em branco.
* Adicione um **sufixo de URL de API**, como *malha*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Criar API em branco":::

### <a name="add-get-operation-to-the-api"></a>Adicionar operação GET à API

Conforme mostrado em [implantar um serviço de back-end Service Fabric](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), o serviço de ASP.NET Core de exemplo implantado no Cluster Service Fabric dá suporte a uma única operação http Get no caminho da URL `/api/values` .

A resposta padrão nesse caminho é uma matriz JSON de duas cadeias de caracteres:

```json
["value1", "value2"]
```

Para testar a integração do gerenciamento de API com o cluster, adicione a operação GET correspondente à API no caminho `/api/values` :

1. Selecione a API que você criou na etapa anterior.
1. Selecione **+ Adicionar Operação**.
1. Na janela **frontend** , insira os valores a seguir e selecione **salvar**.

     | Configuração             | Valor                             | 
    |---------------------|-----------------------------------|
    | **Nome de exibição**    | *Testar back-end*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Adicionar operação GET à API":::

### <a name="configure-set-backend-policy"></a>Configurar `set-backend` política

Adicione a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) política à API de teste.

1. Na guia **design** , na seção **processamento de entrada** , selecione o ícone editor de código ( **</>** ). 
1. Posicionar o cursor dentro do elemento de **&lt; entrada &gt;**
1. Adicione a instrução de política a seguir. No `backend-id` , substitua o nome do seu back-end Service Fabric.

   A `sf-resolve-condition` é uma condição de repetição se a partição de cluster não for resolvida. O número de repetições foi definido ao configurar o back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selecione **Save**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Configurar a política Set-back-end-Service":::

### <a name="test-backend-api"></a>Testar API de back-end

1. Na guia **teste** , selecione a operação **obter** que você criou em uma seção anterior.
1. Selecione **Enviar**.

Quando configurado corretamente, a resposta HTTP mostra um código de êxito HTTP e exibe o JSON retornado do serviço de Service Fabric de back-end.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testar o back-end Service Fabric":::

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Configurar políticas](api-management-advanced-policies.md) para encaminhar solicitações para um back-end
* Os back-ends também podem ser configurados usando a [API REST](/rest/api/apimanagement/2020-06-01-preview/backend)de gerenciamento de api, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)ou [modelos de Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

