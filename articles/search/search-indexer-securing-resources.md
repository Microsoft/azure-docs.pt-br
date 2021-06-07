---
title: Acesso do indexador a recursos protegidos
titleSuffix: Azure Cognitive Search
description: Visão geral conceitual das opções de segurança de nível de rede para o acesso a dados do Azure por indexadores no Azure Pesquisa Cognitiva.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92503480"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Acesso do indexador ao conteúdo protegido pelos recursos de segurança de rede do Azure (Azure Pesquisa Cognitiva)

Os indexadores do Azure Pesquisa Cognitiva podem fazer chamadas de saída para vários recursos do Azure durante a execução. Este artigo explica os conceitos por trás do acesso do indexador ao conteúdo protegido por firewalls IP, pontos de extremidade privados ou outros mecanismos de segurança no nível de rede do Azure. Um indexador faz chamadas de saída em duas situações: conectar-se a fontes de dados durante a indexação e conectar-se a código encapsulado por meio de um decodificador. Uma lista de todos os tipos de recursos possíveis que um indexador pode acessar em uma execução típica está listada na tabela a seguir.

| Recurso | Finalidade dentro da execução do indexador |
| --- | --- |
| Armazenamento do Azure (BLOBs, tabelas, ADLS Gen 2) | Fonte de dados |
| Armazenamento do Azure (BLOBs, tabelas) | Habilidades (caching de documentos aprimorados e armazenamento de projeções de armazenamento de conhecimento) |
| Azure Cosmos DB (várias APIs) | Fonte de dados |
| Banco de Dados SQL do Azure | Fonte de dados |
| SQL Server em máquinas virtuais do Azure | Fonte de dados |
| Instância Gerenciada de SQL | Fonte de dados |
| Azure Functions | Host para habilidades de API Web personalizadas |
| Serviços Cognitivos | Anexado ao skillset que será usado para faturar o enriquecimento além do limite de 20 documentos gratuitos |

> [!NOTE]
> O recurso de serviço cognitiva anexado a um qualificable é usado para cobrança, com base nos aprimoramentos executados e gravados no índice de pesquisa. Ele não é usado para acessar o API de Serviços Cognitivos. O acesso do pipeline de enriquecimento de um indexador para API de Serviços Cognitivos ocorre por meio de um canal de comunicação seguro interno, em que os dados são criptografados fortemente em trânsito e nunca são armazenados em repouso.

Os clientes podem proteger esses recursos por meio de vários mecanismos de isolamento de rede oferecidos pelo Azure. Com exceção de um recurso de serviço cognitiva, os indexadores têm a capacidade limitada de acessar todos os outros recursos, mesmo se forem isolados da rede, descritos na tabela a seguir.

| Recurso | Restrição de IP | Ponto de extremidade privado |
| --- | --- | ---- |
| Armazenamento do Azure (BLOBs, tabelas, ADLS Gen 2) | Com suporte apenas se a conta de armazenamento e o serviço de pesquisa estiverem em regiões diferentes | Com suporte |
| API do Azure Cosmos DB-SQL | Com suporte | Com suporte |
| API Azure Cosmos DB-Cassandra, Mongo e Gremlin | Com suporte | Sem suporte |
| Banco de Dados SQL do Azure | Com suporte | Com suporte |
| SQL Server em máquinas virtuais do Azure | Com suporte | N/D |
| Instância Gerenciada de SQL | Com suporte | N/D |
| Azure Functions | Com suporte | Com suporte, somente para determinadas camadas do Azure Functions |

> [!NOTE]
> Além das opções listadas acima, para contas de armazenamento do Azure protegidas por rede, os clientes podem aproveitar o fato de que o Azure Pesquisa Cognitiva é um [serviço confiável da Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services). Isso significa que um serviço de pesquisa específico pode ignorar restrições de rede virtual ou IP na conta de armazenamento e pode acessar dados na conta de armazenamento, se o controle de acesso baseado em função apropriado estiver habilitado na conta de armazenamento. Para obter mais informações, consulte [conexões do indexador usando a exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md). Essa opção pode ser utilizada em vez de uma rota de restrição de IP, caso a conta de armazenamento ou o serviço de pesquisa não possa ser movido para uma região diferente.

Ao escolher qual mecanismo de acesso seguro deve ser usado por um indexador, considere as seguintes restrições:

- Um indexador não pode se conectar a um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md). Pontos de extremidade públicos com credenciais, pontos de extremidade privados, serviço confiável e endereçamento IP são as únicas metodologias com suporte para conexões do indexador.
- Um serviço de pesquisa não pode ser provisionado em uma rede virtual específica, executando nativamente em uma máquina virtual. Essa funcionalidade não será oferecida pelo Azure Pesquisa Cognitiva.
- Quando os indexadores utilizam pontos de extremidade privados (de saída) para acessar recursos, podem ser aplicados [encargos de link privado](https://azure.microsoft.com/pricing/details/search/) adicionais.

## <a name="indexer-execution-environment"></a>Ambiente de execução do indexador

Os indexadores do Azure Pesquisa Cognitiva são capazes de extrair conteúdo com eficiência de fontes de dados, adicionando aprimoramentos ao conteúdo extraído, opcionalmente gerando projeções antes de gravar os resultados no índice de pesquisa. Dependendo do número de responsabilidades atribuídas a um indexador, ele pode ser executado em um dos dois ambientes:

- Um ambiente privado para um serviço de pesquisa específico. Os indexadores em execução nesses ambientes compartilham recursos com outras cargas de trabalho (como outras cargas de trabalho de indexação ou consulta iniciadas pelo cliente). Normalmente, somente os indexadores que executam a indexação baseada em texto (por exemplo, não usam uma conseqüência de habilidades) são executados nesse ambiente.
- Um ambiente multilocatário que hospeda indexadores que são intensivas de recursos, como aqueles com habilidades. Esse ambiente é usado para descarregar o processamento computacionalmente intensivo, deixando os recursos específicos do serviço disponíveis para operações de rotina. Esse ambiente multilocatário é gerenciado e protegido pela Microsoft, sem custo adicional para o cliente.

Para qualquer execução de indexador específico, o Azure Pesquisa Cognitiva determina o melhor ambiente no qual executar o indexador. Se você estiver usando um firewall IP para controlar o acesso aos recursos do Azure, saber mais sobre os ambientes de execução ajudará você a configurar um intervalo de IP que seja inclusivo de ambos.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concedendo acesso aos intervalos de IP do indexador

Se o recurso que o indexador está tentando acessar estiver restrito a apenas um determinado conjunto de intervalos de IP, você precisará expandir o conjunto para incluir os possíveis intervalos de IP dos quais uma solicitação de indexador pode se originar. Conforme mencionado acima, há dois ambientes possíveis em que os indexadores são executados e do qual as solicitações de acesso podem se originar. Você precisará adicionar os endereços IP de **ambos os** ambientes para que o acesso do indexador funcione.

- Para obter o endereço IP do ambiente particular específico do serviço de pesquisa, `nslookup` (ou `ping` ) o FQDN (nome de domínio totalmente qualificado) do seu serviço de pesquisa. Por exemplo, o FQDN de um serviço de pesquisa na nuvem pública seria `<service-name>.search.windows.net` . Essas informações estão disponíveis no portal do Azure.
- Os endereços IP dos ambientes multilocatário estão disponíveis por meio da `AzureCognitiveSearch` marca de serviço. As [marcas de serviço do Azure](../virtual-network/service-tags-overview.md) têm um intervalo publicado de endereços IP para cada serviço-isso está disponível por meio de uma [API de descoberta (versão prévia)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou um [arquivo JSON baixável](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Em ambos os casos, os intervalos de IP são divididos por região. você pode escolher apenas os intervalos de IP atribuídos para a região em que o serviço de pesquisa é provisionado.

Para determinadas fontes de dados, a própria marca de serviço pode ser usada diretamente em vez de enumerar a lista de intervalos de IP (o endereço IP do serviço de pesquisa ainda precisa ser usado explicitamente). Essas fontes de dados restringem o acesso por meio da configuração de uma [regra de grupo de segurança de rede](../virtual-network/network-security-groups-overview.md), que oferece suporte nativo à adição de uma marca de serviço, ao contrário das regras de IP, como as oferecidas pelo armazenamento do azure, Cosmos DB, SQL do Azure e assim por diante. As fontes de dados que dão suporte à capacidade de utilizar a `AzureCognitiveSearch` marca de serviço diretamente, além do endereço IP do serviço de pesquisa, são:

- [SQL Server em máquinas virtuais do Azure](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Instâncias gerenciadas do SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Para obter mais informações sobre essa opção de conectividade, consulte [conexões de indexador por meio de um firewall de IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concedendo acesso por meio de pontos de extremidade privados

Os indexadores podem utilizar [pontos de extremidade privados](../private-link/private-endpoint-overview.md) para acessar recursos, o acesso ao qual estão bloqueados para selecionar redes virtuais ou não têm nenhum acesso público habilitado.
Essa funcionalidade só está disponível nos serviços de pesquisa faturáveis, com limites no número de pontos de extremidade privados que são criados. Para obter mais informações, consulte [limites de serviço](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Etapa 1: criar um ponto de extremidade privado para o recurso seguro

Os clientes devem chamar a operação de gerenciamento de pesquisa, a [API CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) em um **recurso de link privado compartilhado**, para criar uma conexão de ponto de extremidade privada para seu recurso seguro (por exemplo, uma conta de armazenamento). O tráfego que passa por essa conexão de ponto de extremidade privada (de saída) se originará somente da rede virtual que está no ambiente de execução do indexador "particular" específico do serviço de pesquisa.

O Azure Pesquisa Cognitiva validará que os chamadores dessa API têm permissões RBAC do Azure para aprovar solicitações de conexão de ponto de extremidade privado para o recurso seguro. Por exemplo, se você solicitar uma conexão de ponto de extremidade privada para uma conta de armazenamento com permissões somente leitura, essa chamada será rejeitada.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Etapa 2: aprovar a conexão de ponto de extremidade particular

Quando a operação (assíncrona) que cria um recurso de link privado compartilhado for concluída, uma conexão de ponto de extremidade privado será criada em um estado "pendente". Nenhum tráfego flui pela conexão ainda.
O cliente espera, então, localizar essa solicitação em seu recurso seguro e "aprová-la". Normalmente, isso pode ser feito por meio da portal do Azure ou por meio da [API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Etapa 3: forçar os indexadores a serem executados no ambiente "privado"

Um ponto de extremidade privado aprovado permite chamadas de saída do serviço de pesquisa para um recurso que tem alguma forma de restrições de acesso no nível da rede (por exemplo, uma fonte de dados da conta de armazenamento configurada para ser acessada somente de determinadas redes virtuais) para ter sucesso.
Isso significa que qualquer indexador capaz de acessar uma fonte de dados desse tipo no ponto de extremidade privado terá sucesso.
Se o ponto de extremidade privado não for aprovado, ou se o indexador não utilizar a conexão de ponto de extremidade particular, a execução do indexador terminará em `transientFailure` .

Para permitir que os indexadores acessem recursos por meio de conexões de ponto de extremidade privadas, é obrigatório definir o `executionEnvironment` do indexador como `"Private"` para garantir que todas as execuções do indexador possam utilizar o ponto de extremidade privado. Isso ocorre porque os pontos de extremidade privados são provisionados no ambiente específico do serviço de pesquisa particular.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Essas etapas são descritas mais detalhadamente em [conexões do indexador por meio de um ponto de extremidade privado](search-indexer-howto-access-private.md).
Depois de ter um ponto de extremidade privado aprovado para um recurso, os indexadores que estão definidos para serem uma tentativa *privada* de obter acesso por meio da conexão de ponto de extremidade privado.

### <a name="limits"></a>Limites

Para garantir o desempenho ideal e a estabilidade do serviço de pesquisa, as restrições são impostas (por camada de serviço de pesquisa) nas seguintes dimensões:

- Os tipos de indexadores que podem ser definidos como *particulares*.
- O número de recursos de vínculo privado compartilhado que podem ser criados.
- O número de tipos de recursos distintos para os quais os recursos de link privado compartilhado podem ser criados.

Esses limites são documentados em [limites de serviço](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Próximas etapas

- [Conexões do indexador por meio de firewalls de IP](search-indexer-howto-access-ip-restricted.md)
- [Conexões do indexador usando a exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md)
- [Conexões do indexador com um ponto de extremidade privado](search-indexer-howto-access-private.md)