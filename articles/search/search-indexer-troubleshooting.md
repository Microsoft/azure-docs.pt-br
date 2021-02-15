---
title: Solucionar problemas comuns do indexador de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrija erros e problemas comuns com indexadores no Azure Pesquisa Cognitiva, incluindo conexão de fonte de dados, firewall e documentos ausentes.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355118"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Solucionando problemas comuns do indexador no Azure Pesquisa Cognitiva

Os indexadores podem ter vários problemas ao indexar dados no Azure Pesquisa Cognitiva. As principais categorias de dados são:

* [Conectando-se a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documentos para um índice](#index-errors)

## <a name="connection-errors"></a>Erros de conexão

> [!NOTE]
> Os indexadores têm suporte limitado para acessar fontes de dados e outros recursos protegidos pelos mecanismos de segurança de rede do Azure. Atualmente, os indexadores só podem acessar fontes de dados por meio de mecanismos de restrição de intervalo de endereços IP correspondentes ou regras NSG quando aplicável. Os detalhes para acessar cada fonte de dados com suporte podem ser encontrados abaixo.
>
> Você pode descobrir o endereço IP do serviço de pesquisa executando ping no nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net` ).
>
> Você pode descobrir o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) usando [arquivos JSON baixáveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou por meio da [API de descoberta de marca de serviço](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). O intervalo de endereços IP é atualizado semanalmente.

### <a name="configure-firewall-rules"></a>Configurar regras de firewall

O armazenamento do Azure, o CosmosDB e o SQL do Azure fornecem um firewall configurável. Não há nenhuma mensagem de erro específica quando o firewall estiver habilitado. Normalmente, os erros de firewall são genéricos e são semelhantes a `The remote server returned an error: (403) Forbidden` ou `Credentials provided in the connection string are invalid or have expired` .

Há duas opções para permitir que os indexadores acessem esses recursos em uma instância desse tipo:

* Desabilite o firewall, permitindo o acesso de **todas as redes** (se possível).
* Como alternativa, você pode permitir o acesso para o endereço IP do serviço de pesquisa e o intervalo de endereços IP da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) nas regras de firewall do recurso (restrição de intervalo de endereços IP).

Os detalhes para configurar restrições de intervalo de endereços IP para cada tipo de fonte de dados podem ser encontrados nos seguintes links:

* [Armazenamento do Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL do Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitação**: conforme indicado na documentação acima para o armazenamento do Azure, as restrições de intervalo de endereços IP só funcionarão se o serviço de pesquisa e sua conta de armazenamento estiverem em regiões diferentes.

O Azure Functions (que pode ser usado como uma [habilidade de API Web personalizada](cognitive-search-custom-skill-web-api.md)) também dá suporte a [restrições de endereço IP](../azure-functions/ip-addresses.md#ip-address-restrictions). A lista de endereços IP a serem configurados seria o endereço IP do serviço de pesquisa e o intervalo de endereços IP da `AzureCognitiveSearch` marca de serviço.

Os detalhes para acessar dados no SQL Server em uma VM do Azure são descritos [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurar regras do NSG (grupo de segurança de rede)

Ao acessar dados em uma instância gerenciada do SQL ou quando uma VM do Azure é usada como o URI do serviço Web para uma [habilidade personalizada da API Web](cognitive-search-custom-skill-web-api.md), os clientes não precisam se preocupar com endereços IP específicos.

Nesses casos, a VM do Azure ou a instância gerenciada do SQL podem ser configuradas para residir em uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir para dentro e fora das sub-redes e interfaces de rede da rede virtual.

A `AzureCognitiveSearch` marca de serviço pode ser usada diretamente nas regras de [NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) de entrada sem a necessidade de Pesquisar seu intervalo de endereços IP.

Mais detalhes para acessar dados em uma instância gerenciada do SQL são descritos [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>A "indexação" do CosmosDB não está habilitada

O Azure Pesquisa Cognitiva tem uma dependência implícita na indexação de Cosmos DB. Se você desativar a indexação automática no Cosmos DB, o Pesquisa Cognitiva do Azure retornará um estado bem-sucedido, mas falhará ao indexar o conteúdo do contêiner. Para obter instruções sobre como verificar as configurações e ative a indexação, consulte [Gerenciar a indexação no Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos não processável ou sem suporte

Os documentos do indexador [de BLOB cujos formatos de documento são explicitamente compatíveis.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Às vezes, um contêiner de armazenamento de Blob contém documentos não compatíveis. Outras vezes, pode haver um documentos problemáticos. Você pode evitar interromper seu indexador nesses documentos ao [Alternar as opções de configuração](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo do documento ausente

O indexador de Blob [localiza e extrai o texto de blobs em um contêiner](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração de texto incluem:

* O documento contém apenas imagens digitalizadas. Blobs PDF que têm conteúdo não textual, como imagens digitalizadas (JPGs), não produzem resultados em um pipeline de indexação de Blob padrão. Se você tiver conteúdo de imagem com elementos de texto, poderá usar a [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para localizar e extrair o texto.
* O indexador de Blob está configurado para metadados do índice. Para extrair o conteúdo, o indexador de Blob deve ser configurado para [extrair o conteúdo e metadados](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erros de índice

### <a name="missing-documents"></a>Documentos ausentes

Os indexadores encontram documentos de uma [fonte de dados](/rest/api/searchservice/create-data-source). Às vezes, um documento da fonte de dados que deve ter sido indexado, aparece faltando em um índice. Há duas razões comuns para que esses erros ocorram:

* O documento ainda não foi indexado. Verifique o portal para uma execução bem-sucedida do indexador.
* Verifique seu valor de [controle de alterações](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Se o valor de marca d' água alta for uma data definida para um horário futuro, todos os documentos que tiverem uma data menor que isso serão ignorados pelo indexador. Você pode entender o estado de controle de alterações do indexador usando os campos ' initialTrackingState ' e ' finalTrackingState ' no [status do indexador](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* O documento foi atualizado após o execução do indexador. Se o indexador estiver em um [agendamento](/rest/api/searchservice/create-indexer#indexer-schedule), eventualmente será executado novamente e pegará o documento.
* A [consulta](/rest/api/searchservice/create-data-source) especificada nos dados de origem exclui o documento. Os indexadores não podem indexar documentos que não fazem parte da fonte de dados.
* Os [mapeamentos de campo](/rest/api/searchservice/create-indexer#fieldmappings) ou o [enriquecimento de ai](./cognitive-search-concept-intro.md) alteraram o documento e parecem diferentes do esperado.
* Use a [API Procurar documento](/rest/api/searchservice/lookup-document) para localizar seu documento.