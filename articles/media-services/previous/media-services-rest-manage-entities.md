---
title: Gerenciar entidades dos Serviços de Mídia com REST | Microsoft Docs
description: Saiba como gerenciar entidades de serviços de mídia com API REST.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761702"
---
# <a name="managing-media-services-entities-with-rest"></a>Gerenciar entidades dos Serviços de Mídia com REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

O Serviço de mídia do Microsoft Azure é um serviço baseado em REST integrado ao OData v3. Você pode adicionar, consultar, atualizar e excluir entidades exatamente como faria com qualquer outro serviço OData. As exceções serão indicadas, quando necessário. Para obter mais informações sobre OData, consulte [Documentação do protocolo Open Data](https://www.odata.org/documentation/).

Este tópico mostra como gerenciar as entidades dos Serviços de Mídia do Azure com REST.

>[!NOTE]
> A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias será excluído automaticamente, junto com seus registros de tarefas associados, mesmo que o número total de registros esteja abaixo da cota máxima. Por exemplo, no dia 1º de abril de 2017, qualquer registro de Trabalho em sua conta que seja mais antigo do que 31 de dezembro de 2016 será excluído automaticamente. Se você precisar arquivar as informações de trabalho/tarefa, poderá usar o código descrito neste tópico.

## <a name="considerations"></a>Considerações  

Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Adicionando entidades
Cada entidade no Media Services é adicionada a um conjunto de entidades, como Ativos, por meio de uma solicitação do HTTP POST.

O exemplo a seguir mostra como criar um AccessPolicy:

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Consultando entidades
Consultar e listar entidades é simples e envolve apenas uma solicitação HTTP GET e operações OData opcionais.
O exemplo a seguir recupera uma lista de todas as entidades MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Você também pode recuperar uma entidade específica ou todos os conjuntos de entidades associados a uma entidade específica, como nos exemplos a seguir:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo a seguir retorna apenas a propriedade State de todos os trabalhos.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

O exemplo a seguir retorna todos os JobTemplates com o nome "SampleTemplate".

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> A operação $expand não tem suporte no Media Services, bem como os métodos LINQ descritos em considerações sobre o LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerar através de grandes coleções de entidades
Ao consultar entidades, um limite de 1.000 entidades podem ser retornadas ao mesmo tempo porque a REST v2 pública limita os resultados da consulta a 1.000 resultados. Use as opções **Ignorar** e **Superior** para enumerar a grande coleção de entidades. 

O exemplo a seguir mostra como usar as opções **Ignorar** e **Superior** para ignorar os primeiros 2000 trabalhos e os 1000 trabalhos seguintes.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Atualizando entidades
Dependendo do tipo de entidade e do estado em que está, você pode atualizar as propriedades nesta entidade por meio de um PATCH, PUT ou  solicitações HTTP MERGE. Para obter mais informações sobre essas operações, consulte [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

O exemplo de código a seguir mostra como atualizar a propriedade Name de uma entidade de ativo.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Excluindo entidades
As  entidades podem ser excluídas do Media Services usando uma solicitação HTTP DELETE. Dependendo da entidade, a ordem de exclusão das entidades pode ser importante. Por exemplo, a entidade de ativos requer que você revogue (ou exclua) todos os localizadores que fazem referência a este ativo em particular antes de excluir o ativo.

O exemplo a seguir mostra como excluir um localizador que foi usado para carregar um arquivo no armazenamento de blob.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

