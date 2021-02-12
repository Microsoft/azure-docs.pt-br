---
title: Versões de API
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 489c6be7bf977d01207fee4d1bd1fda13dc12025
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820510"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se alguns aspectos da API tiverem sido alterados de uma forma que interrompa a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

A mesma regra se aplica às atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK será liberada somente no caso de alterações que interrompam a compatibilidade com versões anteriores. Para atualizações contínuas de recursos, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!Important]
> Os SDKs do Azure para .NET, Java, Python e JavaScript estão distribuindo novas bibliotecas de cliente para o Azure Pesquisa Cognitiva. No momento, nenhuma das bibliotecas do SDK do Azure dá suporte total às APIs REST de pesquisa mais recentes (2020-06-30) ou às APIs REST de gerenciamento (2020-03-13), mas isso mudará ao longo do tempo. Você pode verificar periodicamente esta página ou o [que há de novo](whats-new.md) para anúncios em aprimoramentos funcionais.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versões sem suporte

Atualize as soluções de pesquisa existentes para a versão mais recente da API REST até 15 de outubro de 2020. Nesse momento, as seguintes versões da API REST do Azure Pesquisa Cognitiva serão desativadas e não terão mais suporte:

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

Além disso, as versões do SDK do .NET do Azure Pesquisa Cognitiva [**com mais de 3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) também serão desativadas, pois destinam-se a uma dessas versões da API REST. 

Após essa data, os aplicativos que usam qualquer uma das versões preteridas da API REST ou do SDK deixarão de funcionar e deverão ser atualizados. Assim como acontece com qualquer alteração desse tipo, estamos dando um aviso de 12 meses, portanto, você tem tempo adequado para se ajustar.

Para continuar usando o Azure Pesquisa Cognitiva, migre o código existente destinado à [API](search-api-migration.md) REST para a [api REST versão 2020-06-30](/rest/api/searchservice/) ou para um SDK mais recente até 15 de outubro de 2020.  Se você tiver alguma dúvida sobre a atualização para a versão mais recente, envie um email para azuresearch_contact@microsoft.com 15 de maio de 2020 para garantir que você tenha tempo suficiente para atualizar seu código.

## <a name="rest-apis"></a>APIs REST

Uma instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável [migrar seu código](search-api-migration.md) para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

A tabela a seguir fornece o histórico de versão das versões atuais e lançadas anteriormente da API REST do Serviço de Pesquisa. A documentação é publicada somente para as versões mais recentes do estável e de visualização.

### <a name="search-service-apis"></a>APIs de Serviço de Pesquisa

Criar e gerenciar conteúdo em um serviço de pesquisa.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Descrição |
|-------------------------|--------|------------------------------|
| [Pesquisar 2020-06-30](/rest/api/searchservice/index)| Estável | Versão estável mais recente das APIs REST de pesquisa, com avanços na pontuação de relevância e geralmente a disponibilidade para a loja de conhecimento.|
| [Pesquisar 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Versão Prévia | Versão de visualização associada à versão estável. Inclui vários [recursos de visualização](search-api-preview.md). |
| Pesquisar 2019-05-06 | Estável  | Adiciona [tipos complexos](search-howto-complex-data-types.md). |
| Pesquisa 2019-05-06-Versão Prévia | Versão Prévia | Versão de visualização associada à versão estável. |
| Pesquisar 2017-11-11 | Estável | Adiciona habilidades e [enriquecimento de ia](cognitive-search-concept-intro.md). |
| Pesquisa 2017-11-11-visualização | Versão Prévia | Versão de visualização associada à versão estável. |
| Pesquisar 2016-09-01 |Estável | Adiciona [indexadores](search-indexer-overview.md). |
| Pesquisa 2016-09-01-visualização | Versão Prévia | Versão de visualização associada à versão estável.|
| Pesquisar 2015-02-28 | Sem suporte após 10-10-2020   | Primeira versão disponível em geral.  |
| Pesquisa 2015-02-28-visualização | Sem suporte após 10-10-2020  | Versão de visualização associada à versão estável. |
| Pesquisa 2014-10-20-visualização | Sem suporte após 10-10-2020 | Segunda visualização pública. |
| Pesquisa 2014-07-31-visualização | Sem suporte após 10-10-2020  | Primeira visualização pública. |

### <a name="management-rest-apis"></a>APIs REST de gerenciamento

Criar e configurar um serviço de pesquisa e gerenciar chaves de API.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Descrição |
|-------------------------|--------|------------------------------|
| [Gerenciamento 2020-08-01](/rest/api/searchmanagement/) | Estável | Versão estável mais recente das APIs REST de gerenciamento. Adiciona suporte ao recurso de link privado compartilhado disponível de modo geral para todos os recursos acessados de saída, exceto aqueles indicados na versão de visualização |
| [Gerenciamento 2020-08-01-visualização](/rest/api/searchmanagement/index-preview) | Versão Prévia  | Atualmente em visualização: suporte a recurso de link privado compartilhado para Azure Functions e para o banco de dados do Azure para MySQL. |
| Gerenciamento 2020-03-13  | Estável | Adiciona um [ponto de extremidade privado](service-create-private-endpoint.md) por meio de link privado e [regras de IP de rede](service-configure-firewall.md) para novos serviços. Para obter mais informações, consulte esta [especificação do Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Gerenciamento 2019-10-01-Preview | Versão Prévia  | Não havia nenhum recurso de visualização introduzido nesta lista. Essa visualização é funcionalmente equivalente a 2020-03-13. Para obter mais informações, consulte esta [especificação do Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Gerenciamento 2015-08-19  | Estável | A primeira versão disponível geral das APIs REST de gerenciamento. Fornece provisionamento de serviço, escala vertical e gerenciamento de chaves de API. Para obter mais informações, consulte esta [especificação do Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Gerenciamento 2015-08-19-visualização  | Versão Prévia | A primeira versão de visualização das APIs REST de gerenciamento. Para obter mais informações, consulte esta [especificação do Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>SDK do Azure para .NET

A tabela a seguir fornece links para versões mais recentes do SDK. 

| Versão do SDK | Status | Descrição |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como alvo a API REST de pesquisa-Version = 2020-06-30, e ainda não fornece suporte nativo para filtros geográficos. Recomendamos [o pacote Microsoft. Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) para operações geográficas. Os exemplos estão disponíveis para [System.Text.Jsno](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) e [Newtonsoft.Jsem](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md). |
| [Microsoft. Azure. Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Estável | Lançada em maio de 2019. Tem como destino a API REST de pesquisa-Version = 2019-05-06.|
| [Microsoft. Azure. Management. Search 4.0.0](/dotnet/api/overview/azure/search/management) | Estável | Destina-se à API REST de gerenciamento-versão = 2020-08-01.  |
| Microsoft. Azure. Management. Search 3.0.0 | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19.  |

## <a name="azure-sdk-for-java"></a>SDK do Azure para Java

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [Java Azure-Search-Documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2019-05-06. |
| [1.35.0 do cliente de gerenciamento Java](/java/api/overview/azure/search/management) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>SDK do Azure para JavaScript

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [JavaScript Azure-Search 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2016-09-01. |
| [JavaScript Azure-ARM-Search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>SDK do Azure para Python

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [Python Azure-Search-Documents 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2019-05-06. |
| [Python Azure-MGMT-Search 1,0](/python/api/overview/azure/search) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |