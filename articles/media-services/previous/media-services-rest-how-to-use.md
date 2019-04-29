---
title: Visão geral da API REST das Operações dos Serviços de Mídia | Microsoft Docs
description: Visão geral da API de REST de serviços de mídia
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;johndeu
ms.openlocfilehash: 549554521570d1d2f27b2da2b36ca1dfde25562f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762502"
---
# <a name="media-services-operations-rest-api-overview"></a>Visão geral da API REST das operações dos Serviços de Mídia 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

A API **REST das Operações dos Serviços de Mídia** é usada para criar trabalhos, ativos, canais ao vivo e outros recursos em uma conta de Serviços de Mídia do Azure. Para saber mais, consulte [Referência da API REST das Operações dos Serviços de Mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Os Serviços de Mídia do Microsoft Azure fornecem uma API REST que aceita o formato JSON ou XML atom+pub. API REST do Serviços de Mídia do Microsoft Azure requer cabeçalhos HTTP específicos que cada cliente deve enviar ao se conectar aos Serviços de Mídia do Microsoft Azure, bem como um conjunto de cabeçalhos opcionais. As seções a seguir descrevem os cabeçalhos e verbos HTTP que podem ser usados ao criar solicitações e receber respostas dos serviços de mídia.

A autenticação para a API REST de Serviços de Mídia do Azure é feita por meio da autenticação do Microsoft Azure Active Directory que está descrita no artigo [Usar a autenticação do Azure AD para acessar a API de Serviços de Mídia do Azure com REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerações

As seguintes considerações se aplicam ao usar REST.

* Ao consultar entidades, um limite de 1.000 entidades podem ser retornadas ao mesmo tempo porque a REST v2 pública limita os resultados da consulta a 1.000 resultados. Você precisa usar **Skip** e **Take** (.NET)/ **top** (REST), conforme descrito [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ao usar o JSON e especificar para usar a palavra-chave **__metadata** na solicitação (por exemplo, para fazer referência a um objeto vinculado), é NECESSÁRIO definir o cabeçalho **Accept** para o [formato JSON Detalhado](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (veja o exemplo a seguir). O Odata não entende a propriedade **__metadata** na solicitação, a menos que você a defina como detalhado.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Os cabeçalhos de solicitação HTTP padrão suportados pelos serviços de mídia
Para todas as chamadas feitas nos serviços de mídia, há um conjunto de cabeçalhos necessários que você deve incluir na solicitação e também um conjunto de cabeçalhos opcionais você talvez queira incluir. A tabela abaixo lista os cabeçalhos necessários:

| Cabeçalho | Type | Value |
| --- | --- | --- |
| Autorização |Portador |Portador é o único mecanismo de autorização aceito. O valor também deve incluir o token de acesso fornecido pelo Microsoft Azure Active Directory. |
| x-ms-version |Decimal |2.17 (ou versão mais recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Como os Serviços de Mídia do Microsoft Azure usam o OData para expor seu APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em todas as solicitações. No entanto, se não forem, então os Serviços de Mídia do Microsoft Azure assumem que o valor DataServiceVersion em uso é 3.0.
> 
> 

Este é um conjunto de cabeçalhos opcional:

| Cabeçalho | Type | Value |
| --- | --- | --- |
| Data |Data do RFC 1123 |Carimbo de hora da solicitação |
| Aceitar |Tipo de conteúdo |O conteúdo solicitado para a resposta, como o seguinte:<p> -application/json;odata=verbose<p> - application/atom+xml<p> As respostas podem ter tipos de conteúdo diferentes como uma busca de blob, em que uma resposta bem-sucedida contém o fluxo de blob como carga. |
| Codificação aceita |Gzip, deflate |Codificar GZIP e DEFLATE, quando aplicável. Observação: Para recursos grandes, os Serviços de Mídia podem ignorar esse cabeçalho e retornar dados não compactados. |
| Idioma aceito |"en", "es", e assim por diante. |Especifica o idioma preferencial para a resposta. |
| Conjunto de caracteres aceito |Tipo de conjunto de caracteres como "UTF-8" |Padrão é UTF-8. |
| Método X-HTTP |Método HTTP |Permite que os clientes ou firewalls que não suportam métodos HTTP como PUT ou DELETE usem esses métodos, desviados via uma chamada GET. |
| Tipo de conteúdo |Tipo de conteúdo |Tipo de conteúdo do corpo da solicitação em solicitações PUT ou POST. |
| ID da solicitação de cliente |Cadeia de caracteres |Um valor definido pelo chamador que identifica a solicitação em questão. Se especificado, esse valor será incluído na mensagem de resposta como uma maneira de mapear a solicitação. <p><p>**Importante**<p>Os valores devem ser limitados a 2096b (2K). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão suportados pelos serviços de mídia
Este é um conjunto de cabeçalhos que podem ser retornados para você, dependendo do recurso que você solicitou e da ação que você pretende executar.

| Cabeçalho | Type | Value |
| --- | --- | --- |
| ID da solicitação |Cadeia de caracteres |Um identificador exclusivo para a operação atual, serviço gerado. |
| ID da solicitação de cliente |Cadeia de caracteres |Um identificador especificado pelo chamador na solicitação original, se presente. |
| Data |Data do RFC 1123 |A data/hora em que a solicitação foi processada. |
| Tipo de conteúdo |Varia |O tipo de conteúdo do corpo da resposta. |
| Codificação de conteúdo |Varia |Gzip ou deflate, conforme apropriado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão suportados pelos serviços de mídia.
A seguir está uma lista completa de verbos HTTP que podem ser usados quando fazem solicitações HTTP:

| Verbo | DESCRIÇÃO |
| --- | --- |
| GET |Retorna o valor atual de um objeto. |
| POST |Cria um objeto com base nos dados fornecidos ou envia um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| EXCLUIR |Exclui um objeto. |
| MESCLAR |Atualiza um objeto existente com alterações de propriedade nomeada. |
| HEAD |Retorna metadados de um objeto para uma resposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Descobrir e procurar o modelo de entidade dos Serviços de Mídia do Azure
Para tornar as entidades de serviços de mídia mais detectáveis, a operação de $metadata pode ser usada. Ele permite que você recupere todos os tipos de entidade válidos, propriedades da entidade, associações, funções, ações e assim por diante. Adicionando a operação $metadata ao final do seu ponto de extremidade de API REST de Serviços de Mídia do Azure, você pode acessar esse serviço de descoberta.

 /api/$metadata.

Você deve acrescentar "?api-version=2.x" ao final do URI se desejar exibir os metadados em um navegador, ou não incluir o cabeçalho x-ms-version na solicitação.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autenticar com o REST de Serviços de Mídia do Microsoft Azure usando o Azure Active Directory

A autenticação da API REST é feita por meio do Azure Active Directory (AAD).
Para obter detalhes sobre como obter os detalhes de autenticação necessários para sua conta de Serviços de Mídia do Microsoft Azure do Portal do Azure, consulte [Acessar a API de Serviços de Mídia do Microsoft Azure com a autenticação do Microsoft Azure Active Directory](media-services-use-aad-auth-to-access-ams-api.md).

Para obter detalhes sobre como gravar um código que se conecte à API REST usando a autenticação do Azure AD, consulte o artigo [Usar a autenticação do Microsoft Azure Active Directory para acessar a API de Serviços de Mídia do Microsoft Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Próximas etapas
Para saber como usar a autenticação do Azure AD com a API REST de Serviços de Mídia do Microsoft Azure, consulte [Usar a autenticação do Microsoft Azure Active Directory para acessar a API de Serviços de Mídia do Microsoft Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

