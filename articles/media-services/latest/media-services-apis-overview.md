---
title: Desenvolvendo com APIs v3 – Azure | Microsoft Docs
description: Este artigo discute as regras que se aplicam a entidades e APIs ao desenvolver com os serviços de mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26fea4322df625b2e38028a3b7121fb41f2acf81
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311853"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvendo com APIs dos serviços de mídia v3

Como desenvolvedor, você pode usar a [API REST](https://aka.ms/ams-v3-rest-ref) dos Serviços de Mídia ou bibliotecas de clientes que permitem interagir com a API REST para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. A API dos [Serviços de Mídia v3](https://aka.ms/ams-v3-rest-sdk) se baseia na especificação do OpenAPI (anteriormente conhecida como um Swagger).

Este artigo discute as regras que se aplicam a entidades e APIs ao desenvolver com os serviços de mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessando a API dos serviços de mídia do Azure

Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. Os serviços de mídia oferecem suporte à autenticação [baseada no Azure Active Directory (AD do Azure)](../../active-directory/fundamentals/active-directory-whatis.md) . Duas opções comuns de autenticação são:
 
* **Autenticação de entidade de serviço** – usada para autenticar um serviço (por exemplo: aplicativos Web, aplicativos de funções, aplicativos lógicos, API e microservices). Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Por exemplo, para aplicativos Web, sempre deve haver uma camada intermediária que se conecte aos serviços de mídia com uma entidade de serviço.
* **Autenticação de usuário** – usada para autenticar uma pessoa que está usando o aplicativo para interagir com os recursos dos serviços de mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo.

A API dos serviços de mídia requer que o usuário ou aplicativo que faz as solicitações da API REST tenha acesso ao recurso de conta dos serviços de mídia e use uma função de **colaborador** ou **proprietário** . A API pode ser acessada com a função **leitor** , mas somente as operações **Get** ou **list**   estarão disponíveis. Para obter mais informações, consulte [controle de acesso baseado em função para contas de serviços de mídia](rbac-overview.md).

Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para acessar a API dos serviços de mídia por meio de Azure Resource Manager. Para saber mais sobre identidades gerenciadas para recursos do Azure, confira [o que são identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidade de serviço do Azure AD 

Se você estiver criando um aplicativo do Azure AD e uma entidade de serviço, o aplicativo terá que estar em seu próprio locatário. Depois de criar o aplicativo, conceda ao **colaborador** do aplicativo ou à função de **proprietário** acesso à conta dos serviços de mídia. 

Se você não tiver certeza se tem permissões para criar um aplicativo do Azure AD, consulte [permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo das solicitações em ordem cronológica:

![Aplicativos de camada intermediária](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo de camada intermediária solicita um token de acesso do Azure AD que tem os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.
   * URI de recurso dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
   * Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente.
   
   Para obter todos os valores necessários, consulte [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

### <a name="samples"></a>Exemplos

Consulte os seguintes exemplos que mostram como se conectar à entidade de serviço do Azure AD:

* [Conectar com REST](media-rest-apis-with-postman.md)  
* [Conectar-se com Java](configure-connect-java-howto.md)
* [Conectar-se com .NET](configure-connect-dotnet-howto.md)
* [Conectar-se com Node.js](configure-connect-nodejs-howto.md)
* [Conectar-se com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a atribuição de nome do Azure Resource Manager, confira: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operações de execução longa

As operações marcadas `x-ms-long-running-operation` com nos [arquivos do Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) dos serviços de mídia do Azure são operações de longa execução. 

Para obter detalhes sobre como rastrear operações assíncronas do Azure, consulte [operações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)assíncronas.

Os serviços de mídia têm as seguintes operações de execução longa:

* [Criar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Iniciar evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Parar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Use o `removeOutputsOnStop` parâmetro para excluir todas as saídas dinâmicas associadas ao parar o evento.  
* [Redefinir LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Criar LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Excluir LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Atualizar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Excluir StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Parar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Dimensionar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Após o envio bem-sucedido de uma operação longa, você receberá um ' 202 aceito ' e deverá sondar a conclusão da operação usando a ID da operação retornada.

Somente uma operação de execução longa tem suporte para um determinado evento ao vivo ou qualquer uma de suas saídas dinâmicas associadas. Depois de iniciada, uma operação de execução longa deve ser concluída antes de iniciar uma operação de execução longa subsequente no mesmo LiveEvent ou em qualquer saída ao vivo associada. Para eventos ao vivo com várias saídas ao vivo, você deve aguardar a conclusão de uma operação de execução longa em uma saída dinâmica antes de disparar uma operação de execução longa em outra saída ao vivo. 

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os SDKs dos Serviços de Mídia do Azure v3 não têm garantia de serem thread-safe. Ao desenvolver um aplicativo com multi-thread, você deverá adicionar sua própria lógica de sincronização de thread para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve tomar cuidado com problemas de multi-threading introduzidos por objetos opcionais fornecidos pelo código ao cliente (como uma instância do HttpClient no .NET).

|.|Referência|
|---|---|
|[SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência do .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referência de Java](https://aka.ms/ams-v3-java-ref)|
|[SDK do Python](https://aka.ms/ams-v3-python-sdk)|[Referência do Python](https://aka.ms/ams-v3-python-ref)|
|[SDK do Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência do Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[SDK do Go](https://aka.ms/ams-v3-go-sdk) |[Referência do Go](https://aka.ms/ams-v3-go-ref)|
|[SDK do Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Consulte também

- [SDK do .NET EventGrid que inclui eventos de serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Gerenciador dos Serviços de Mídia do Azure

O [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure) é uma ferramenta disponível para clientes do Windows que desejam saber mais sobre os Serviços de Mídia. O AMSE é um aplicativo do WinForms/C# que carrega, baixa, codifica e transmite VoD e conteúdo ao vivo com os Serviços de Mídia. A ferramenta AMSE destina-se aos clientes que desejam testar os Serviços de Mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para clientes que desejam desenvolver com os Serviços de Mídia.

O AMSE é um projeto de software livre, cujo suporte é fornecido pela comunidade (os problemas podem ser relatados a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate opencode@microsoft.com para enviar outras perguntas ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Consulte [filtragem, ordenação, paginação de entidades dos serviços de mídia do Azure](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se aos serviços de mídia com Java](configure-connect-java-howto.md)
* [Conectar-se aos serviços de mídia com o .NET](configure-connect-dotnet-howto.md)
* [Conectar-se aos serviços de mídia com o Node. js](configure-connect-nodejs-howto.md)
* [Conectar-se aos serviços de mídia com Python](configure-connect-python-howto.md)
