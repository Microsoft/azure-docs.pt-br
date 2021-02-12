---
title: BLOBs alterados e excluídos
titleSuffix: Azure Cognitive Search
description: Após um Build de índice de pesquisa inicial que importa do armazenamento de BLOBs do Azure, a indexação subsequente pode pegar apenas os blobs que são alterados ou excluídos. Este artigo explica os detalhes.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: b23dabb4388331de9e37ee9db1d4b9d727ccde68
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430553"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Como configurar a detecção de alteração e exclusão para BLOBs na indexação de Pesquisa Cognitiva do Azure

Depois que um índice de pesquisa inicial é criado, talvez você queira que os trabalhos subsequentes do indexador selecionem apenas os documentos novos e alterados. Para o conteúdo de pesquisa originado do armazenamento de BLOBs do Azure, a detecção de alteração ocorre automaticamente quando você usa uma agenda para disparar a indexação. Por padrão, o serviço reindexa apenas os BLOBs alterados, conforme determinado pelo `LastModified` carimbo de data/hora do blob. Em contraste com outras fontes de dados com suporte dos indexadores de pesquisa, os BLOBs sempre têm um carimbo de data/hora, o que elimina a necessidade de configurar manualmente uma política de detecção de alteração.

Embora a detecção de alterações seja determinada, a detecção de exclusão não é. Se você quiser detectar documentos excluídos, use uma abordagem de "exclusão reversível". Se você excluir os blobs imediatamente, documentos correspondentes não serão removidos do índice de pesquisa.

Há duas maneiras de implementar a abordagem de exclusão reversível:

+ Exclusão reversível de blob nativo (versão prévia), descrita a seguir
+ [Exclusão reversível usando metadados personalizados](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Exclusão reversível de blob nativo (versão prévia)

Para essa abordagem de detecção de exclusão, Pesquisa Cognitiva depende do recurso de [exclusão reversível de blob nativo](../storage/blobs/soft-delete-blob-overview.md) no armazenamento de BLOBs do Azure para determinar se os BLOBs foram transferidos para um estado de exclusão reversível. Quando os BLOBs são detectados nesse estado, um indexador de pesquisa usa essas informações para remover o documento correspondente do índice.

> [!IMPORTANT]
> O suporte para exclusão reversível de blob nativo está em versão prévia. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2020-06-30-Preview](./search-api-preview.md) fornece esse recurso. No momento, não há suporte para Portal ou SDK do .NET.

### <a name="prerequisites"></a>Pré-requisitos

+ [Habilite a exclusão reversível para BLOBs](../storage/blobs/soft-delete-blob-enable.md).
+ Os BLOBs devem estar em um contêiner de armazenamento de BLOBs do Azure. Não há suporte para a política de exclusão reversível de blob nativo do Pesquisa Cognitiva para BLOBs de Azure Data Lake Storage Gen2.
+ As chaves de documento para os documentos em seu índice devem ser mapeadas para serem uma propriedade de BLOB ou metadados de BLOB.
+ Você deve usar a API REST de visualização ( `api-version=2020-06-30-Preview` ) para configurar o suporte para exclusão reversível.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Como configurar a detecção de exclusão usando a exclusão reversível nativa

1. No armazenamento de BLOBs, ao habilitar a exclusão reversível, defina a política de retenção com um valor muito maior do que a agenda do intervalo do indexador. Dessa forma, se houver um problema ao executar o indexador ou se você tiver um grande número de documentos para indexar, haverá muito tempo para que o indexador eventualmente processe os BLOBs com exclusão reversível. Os indexadores do Azure Pesquisa Cognitiva excluirão apenas um documento do índice se ele processar o blob enquanto ele estiver em um estado de exclusão reversível.

1. Em Pesquisa Cognitiva, defina uma política de detecção de exclusão reversível de blob nativo na fonte de dados. Um exemplo é mostrado abaixo. Como esse recurso está em versão prévia, você deve usar a API REST de visualização.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Execute o indexador](/rest/api/searchservice/run-indexer) ou defina o indexador para ser executado [em um agendamento](search-howto-schedule-indexers.md). Quando o indexador executar e processar um blob com um estado de exclusão reversível, o documento de pesquisa correspondente será removido do índice.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Reindexando BLOBs não excluídos (usando políticas nativas de exclusão reversível)

Se você restaurar um blob com exclusão reversível no armazenamento de BLOB, o indexador nem sempre será reindexado. Isso ocorre porque o indexador usa o carimbo de `LastModified` data/hora do blob para determinar se a indexação é necessária. Quando um blob com exclusão reversível não é excluído, seu `LastModified` carimbo de data/hora não é atualizado, portanto, se o indexador já tiver processado BLOBs com `LastModified` carimbos de data/hora mais recentes, ele não reindexará o BLOB não excluído. 

Para garantir que um BLOB não excluído seja reindexado, será necessário atualizar o `LastModified` carimbo de data/hora do blob. Uma maneira de fazer isso é salvando novamente os metadados desse blob. Você não precisa alterar os metadados, mas resalvá-los atualizará o `LastModified` carimbo de data/hora do blob para que o indexador saiba que ele deve ser escolhido.

## <a name="soft-delete-using-custom-metadata"></a>Exclusão reversível usando metadados personalizados

Esse método usa metadados do blob para determinar se um documento de pesquisa deve ser removido do índice. Esse método requer duas ações separadas, excluindo o documento de pesquisa do índice, seguido pela exclusão de blob no armazenamento do Azure.

Há etapas a serem seguidas no armazenamento de BLOBs e Pesquisa Cognitiva, mas não há outras dependências de recurso. Esse recurso tem suporte em APIs geralmente disponíveis.

1. Adicione um par chave-valor de metadados personalizados ao blob para indicar ao Azure Pesquisa Cognitiva que ele é excluído logicamente.

1. Configure uma política de detecção de coluna de exclusão reversível na fonte de dados. Por exemplo, a política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Depois que o indexador tiver processado o blob e excluído o documento do índice, você poderá excluir o blob no armazenamento de BLOBs do Azure.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Reindexando BLOBs não excluídos (usando metadados personalizados)

Depois que um indexador processar um blob excluído e remover o documento de pesquisa correspondente do índice, ele não revisitará esse BLOB se você restaurá-lo posteriormente se o `LastModified` carimbo de data/hora do blob for mais antigo do que a última execução do indexador.

Se você quiser reindexar esse documento, altere o `"softDeleteMarkerValue" : "false"` para esse BLOB e execute novamente o indexador.

## <a name="next-steps"></a>Próximas etapas

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Como configurar um indexador de BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Visão geral da indexação de BLOB](search-blob-storage-integration.md)