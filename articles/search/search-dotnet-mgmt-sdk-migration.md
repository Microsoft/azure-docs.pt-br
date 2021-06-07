---
title: Atualizar para o SDK de gerenciamento do .NET Azure Search
titleSuffix: Azure Cognitive Search
description: Atualize para o SDK de gerenciamento do .NET Azure Search de versões anteriores. Saiba mais sobre os novos recursos e as alterações de código necessárias para a migração.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936700"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Atualizando versões do SDK de gerenciamento do .NET Azure Search

Este artigo explica como migrar para versões sucessivas do SDK de gerenciamento do Azure Search .NET, usado para provisionar ou desprovisionar serviços de pesquisa, ajustar a capacidade e gerenciar chaves de API.

Os SDKs de gerenciamento visam uma versão específica da API REST de gerenciamento. Para obter mais informações sobre conceitos e operações, consulte [Search Management (REST)](/rest/api/searchmanagement/).

## <a name="versions"></a>Versões

| Versão do SDK | Versão da API REST correspondente | Adição de recurso ou alteração de comportamento |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020-30-20 | Adiciona segurança de ponto de extremidade (firewalls de IP e integração com o [link privado do Azure](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Aperfeiçoamentos no uso. Alteração significativa em [chaves de consulta de lista](/rest/api/searchmanagement/querykeys/listbysearchservice) (Get é descontinuado). |
| [1,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | Primeira versão |

## <a name="how-to-upgrade"></a>Como atualizar

1. Atualize a referência do NuGet para `Microsoft.Azure.Management.Search` usar o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

1. Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como seu código é estruturado, ele pode ser recriado com êxito, caso em que você está pronto.

1. Se a compilação falhar, talvez você já tenha implementado algumas interfaces do SDK (por exemplo, para fins de teste de unidade), que foram alteradas. Para resolver isso, você precisará implementar métodos mais novos, como `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. Depois de corrigir os erros de compilação, você pode fazer alterações em seu aplicativo para aproveitar a nova funcionalidade. 

## <a name="upgrade-to-30"></a>Atualizar para o 3,0

A versão 3,0 adiciona proteção de ponto de extremidade privada restringindo o acesso a intervalos de IP e, opcionalmente, integrando com o link privado do Azure para serviços de pesquisa que não devem ser visíveis na Internet pública.

### <a name="new-apis"></a>Novas APIs

| API | Categoria| Detalhes |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | Firewall de IP | Restrinja o acesso a um ponto de extremidade de serviço a uma lista de endereços IP permitidos. Consulte [Configurar o firewall IP](service-configure-firewall.md) para obter conceitos e instruções do Portal. |
| [Recurso de link privado compartilhado](/rest/api/searchmanagement/sharedprivatelinkresources) | Link Privado | Crie um recurso de link privado compartilhado a ser usado por um serviço de pesquisa.  |
| [Conexões de ponto de extremidade privado](/rest/api/searchmanagement/privateendpointconnections) | Link Privado | Estabeleça e gerencie conexões a um serviço de pesquisa por meio do ponto de extremidade privado. Consulte [criar um ponto de extremidade privado](service-create-private-endpoint.md) para obter conceitos e instruções do Portal.|
| [Recursos de link privado](/rest/api/searchmanagement/privatelinkresources/) | Link Privado | Para um serviço de pesquisa que tenha uma conexão de ponto de extremidade privada, obtenha uma lista de todos os serviços usados na mesma rede virtual. Se sua solução de pesquisa incluir indexadores que efetuam pull de fontes de dados do Azure (armazenamento do Azure, Cosmos DB, SQL do Azure) ou usar serviços cognitivas ou Key Vault, todos esses recursos deverão ter pontos de extremidade na rede virtual e essa API deverá retornar uma lista. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Link Privado | Essa é uma propriedade em criar ou atualizar solicitações de serviço. Quando desabilitado, o link privado é a única modalidade de acesso. |

### <a name="breaking-changes"></a>Alterações de quebra

Você não pode mais usar GET em uma solicitação de [chaves de consulta de lista](/rest/api/searchmanagement/querykeys/listbysearchservice) . Nas versões anteriores, você poderia usar GET ou POST, nesta versão e em todas as versões avançando, apenas POST tem suporte. 

## <a name="upgrade-to-20"></a>Atualizar para o 2,0

A versão 2 do SDK de gerenciamento do .NET Azure Search é uma pequena atualização, portanto, alterar seu código deve exigir apenas um mínimo de esforço. As alterações no SDK são estritamente alterações no lado do cliente para melhorar a usabilidade do próprio SDK. Essas alterações incluem o seguinte:

* Agora, o `Services.CreateOrUpdate` e suas versões assíncronas sondam automaticamente o provisionamento `SearchService` e não retornam até que o serviço de provisionamento esteja concluído. Isso evita a necessidade de escrever esse código de sondagem por conta própria.

* Se você ainda quiser sondar manualmente o provisionamento do serviço, use o novo método `Services.BeginCreateOrUpdate` ou uma de suas versões assíncronas.

* Novos métodos `Services.Update` e suas versões assíncronas foram adicionados ao SDK. Esses métodos usam HTTP PATCH para oferecer suporte à atualização incremental de um serviço. Por exemplo, agora você pode dimensionar um serviço passando uma instância `SearchService` para esses métodos contendo apenas as propriedades `partitionCount` e `replicaCount` desejadas. A forma antiga de chamar `Services.Get`, modificando o `SearchService` retornado, e passando-o para `Services.CreateOrUpdate` ainda tem suporte, mas não é mais necessária. 

## <a name="next-steps"></a>Próximas etapas

Se você encontrar problemas, o melhor fórum para as perguntas de postagem é [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de rotular o título do problema com "[Pesquisar]".