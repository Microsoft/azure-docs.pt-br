---
title: "Visão geral da API REST das Operações dos Serviços de Mídia | Microsoft Docs"
description: "Visão geral da API de REST de serviços de mídia"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: eada8f2bcd2488d5ed36b0c24aa3d1b917815517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="media-services-operations-rest-api-overview"></a>Visão geral da API REST das operações dos Serviços de Mídia
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

A API **REST das Operações dos Serviços de Mídia** é usada para criar trabalhos, ativos, políticas de acesso e outras operações em objetos em uma conta de Serviço de Mídia. Para saber mais, consulte [Referência da API REST das Operações dos Serviços de Mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Os serviços de mídia do Microsoft Azure é um serviço que aceita solicitações HTTP com base em OData e pode responder em verbose JSON ou atom+pub. Como os serviços de mídia estão em conformidade com as diretrizes de design do Azure, há um conjunto de cabeçalhos HTTP necessários que cada cliente deve usar ao se conectar aos serviços de mídia, bem como um conjunto de cabeçalhos opcionais que podem ser usados. As seções a seguir descrevem os cabeçalhos e verbos HTTP que podem ser usados ao criar solicitações e receber respostas dos serviços de mídia.

Este tópico apresenta uma visão geral de como usar REST v2 com Serviços de Mídia.

## <a name="considerations"></a>Considerações

As seguintes considerações se aplicam ao usar REST.

* Ao consultar entidades, um limite de 1.000 entidades podem ser retornadas ao mesmo tempo porque a REST v2 pública limita os resultados da consulta a 1.000 resultados. Você precisa usar **Skip** e **Take** (.NET)/ **top** (REST), conforme descrito [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ao usar o JSON e especificar para usar a palavra-chave **__metadata** na solicitação (por exemplo, para fazer referência a um objeto vinculado), é NECESSÁRIO definir o cabeçalho **Accept** para o [formato JSON Detalhado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (veja o exemplo a seguir). O Odata não entende a propriedade **__metadata** na solicitação, a menos que você a defina como detalhado.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Os cabeçalhos de solicitação HTTP padrão suportados pelos serviços de mídia
Para todas as chamadas feitas nos serviços de mídia, há um conjunto de cabeçalhos necessários que você deve incluir na solicitação e também um conjunto de cabeçalhos opcionais você talvez queira incluir. A tabela a seguir lista os cabeçalhos necessários:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Autorização |Portador |Portador é o único mecanismo de autorização aceito. O valor também deve incluir o token de acesso fornecido pelo ACS. |
| x-ms-version |Decimal |2.11 |
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Como os serviços de mídia usam o OData para expor seu repositório de metadados de ativo subjacente por meio de APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em qualquer solicitação. No entanto, se não forem, então os serviços de mídia assumem que o valor DataServiceVersion em uso é 3.0.
> 
> 

Este é um conjunto de cabeçalhos opcional:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Data |Data do RFC 1123 |Carimbo de hora da solicitação |
| Aceitar |Tipo de conteúdo |O conteúdo solicitado para a resposta, como o seguinte:<p> -application/json;odata=verbose<p> - application/atom+xml<p> As respostas podem ter tipos de conteúdo diferentes como uma busca de blob, em que uma resposta bem-sucedida conterá o fluxo de blob como carga. |
| Codificação aceita |Gzip, deflate |Codificar GZIP e DEFLATE, quando aplicável. Observação: para grandes recursos, os Serviços de Mídia podem ignorar esse cabeçalho e retornar dados não compactados. |
| Idioma aceito |"en", "es", e assim por diante. |Especifica o idioma preferencial para a resposta. |
| Conjunto de caracteres aceito |Tipo de conjunto de caracteres como "UTF-8" |Padrão é UTF-8. |
| Método X-HTTP |Método HTTP |Permite que os clientes ou firewalls que não suportam métodos HTTP como PUT ou DELETE usem esses métodos, desviados via uma chamada GET. |
| Tipo de conteúdo |Tipo de conteúdo |Tipo de conteúdo do corpo da solicitação em solicitações PUT ou POST. |
| ID da solicitação de cliente |Cadeia de caracteres |Um valor definido pelo chamador que identifica a solicitação em questão. Se especificado, esse valor será incluído na mensagem de resposta como uma maneira de mapear a solicitação. <p><p>**Importante**<p>Os valores devem ser limitados a 2096b (2K). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão suportados pelos serviços de mídia
Este é um conjunto de cabeçalhos que podem ser retornados para você, dependendo do recurso que você solicitou e da ação que você pretende executar.

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| ID da solicitação |Cadeia de caracteres |Um identificador exclusivo para a operação atual, serviço gerado. |
| ID da solicitação de cliente |Cadeia de caracteres |Um identificador especificado pelo chamador na solicitação original, se presente. |
| Data |Data do RFC 1123 |A data em que a solicitação foi processada. |
| Tipo de conteúdo |Varia |O tipo de conteúdo do corpo da resposta. |
| Codificação de conteúdo |Varia |Gzip ou deflate, conforme apropriado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão suportados pelos serviços de mídia.
A seguir está uma lista completa de verbos HTTP que podem ser usados quando fazem solicitações HTTP:

| Verbo | Descrição |
| --- | --- |
| GET |Retorna o valor atual de um objeto. |
| POST |Cria um objeto com base nos dados fornecidos ou envia um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| EXCLUIR |Exclui um objeto. |
| MESCLAR |Atualiza um objeto existente com alterações de propriedade nomeada. |
| HEAD |Retorna metadados de um objeto para uma resposta GET. |

## <a name="discover-media-services-model"></a>Descobrir o modelo Serviços de Mídia
Para tornar as entidades de serviços de mídia mais detectáveis, a operação de $metadata pode ser usada. Ele permite que você recupere todos os tipos de entidade válidos, propriedades da entidade, associações, funções, ações e assim por diante. O exemplo a seguir mostra como construir o URI: https://media.windows.net/API/$metadata.

Você deve acrescentar "?api-version=2.x" ao final do URI se desejar exibir os metadados em um navegador, ou não incluir o cabeçalho x-ms-version na solicitação.

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Depois de se conectar com êxito em https://media.windows.net, você receberá um redirecionamento 301 especificando outro URI dos serviços de mídia. Você deve fazer chamadas subsequentes para o novo URI.

## <a name="next-steps"></a>Próximas etapas

Para acessar APIs AMS com REST, consulte [Usar autenticação do Azure AD para acessar a API de Serviços de Mídia do Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

