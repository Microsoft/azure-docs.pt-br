---
title: Pesquisar em Azure Data Lake Storage Gen2 (versão prévia)
titleSuffix: Azure Cognitive Search
description: Saiba como indexar conteúdo e metadados em Azure Data Lake Storage Gen2. Este recurso está atualmente em visualização pública
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545557"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexação de documentos no Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> O suporte a Azure Data Lake Storage Gen2 está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Você pode solicitar acesso às visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [API REST versão 2020-06-30-Preview](search-api-preview.md) e o portal fornecem esse recurso. Atualmente, o SDK do .NET não é compatível.


Ao configurar uma conta de armazenamento do Azure, você tem a opção de habilitar o [namespace hierárquico](../storage/blobs/data-lake-storage-namespace.md). Isso permite que a coleta de conteúdo em uma conta seja organizada em uma hierarquia de diretórios e subdiretórios aninhados. Ao habilitar o namespace hierárquico, você habilita [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Este artigo descreve como começar a indexar documentos que estão em Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurar o indexador Azure Data Lake Storage Gen2

Há algumas etapas que você precisará concluir para indexar o conteúdo de Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Etapa 1: inscrever-se para a versão prévia

Inscreva-se para a versão prévia do indexador Data Lake Storage Gen2 preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação depois de ser aceito para a versão prévia.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Etapa 2: siga as etapas de configuração de indexação do armazenamento de BLOBs do Azure

Depois de receber a confirmação de que sua inscrição de visualização foi bem-sucedida, você estará pronto para criar o pipeline de indexação.

Você pode indexar conteúdo e metadados de Data Lake Storage Gen2 usando a [API REST versão 2020-06-30-Preview](search-api-preview.md) ou o Portal. Não há suporte para SDK do .NET no momento.

A indexação de conteúdo no Data Lake Storage Gen2 é idêntica à indexação de conteúdo no armazenamento de BLOBs do Azure. Portanto, para entender como configurar a fonte de dados Data Lake Storage Gen2, o índice e o indexador, consulte [como indexar documentos no armazenamento de BLOBs do Azure com o Azure pesquisa cognitiva](search-howto-indexing-azure-blob-storage.md). O artigo armazenamento de BLOBs também fornece informações sobre quais formatos de documento têm suporte, quais propriedades de metadados de blob são extraídas, indexação incremental e muito mais. Essas informações serão as mesmas para Data Lake Storage Gen2.

## <a name="access-control"></a>Controle de acesso

Azure Data Lake Storage Gen2 implementa um [modelo de controle de acesso](../storage/blobs/data-lake-storage-access-control.md) que dá suporte ao controle de acesso baseado em função do Azure (RBAC do Azure) e às ACLs (listas de controle de acesso) semelhantes a POSIX. Ao indexar conteúdo do Data Lake Storage Gen2, o Pesquisa Cognitiva do Azure não extrairá as informações de ACL e RBAC do Azure do conteúdo. Como resultado, essas informações não serão incluídas no índice de Pesquisa Cognitiva do Azure.

Se a manutenção do controle de acesso em cada documento no índice for importante, cabe ao desenvolvedor do aplicativo implementar a [remoção de segurança](./search-security-trimming-for-azure-search.md).

## <a name="change-detection"></a>Detecção de alteração

O indexador Data Lake Storage Gen2 dá suporte à detecção de alteração. Isso significa que, quando o indexador executa, ele apenas reindexa os BLOBs alterados conforme determinado pelo carimbo de `LastModified` data/hora do blob.

> [!NOTE] 
> Data Lake Storage Gen2 permite que os diretórios sejam renomeados. Quando um diretório é renomeado, os carimbos de data/hora dos BLOBs nesse diretório não são atualizados. Como resultado, o indexador não reindexará esses BLOBs. Se você precisar que os BLOBs em um diretório sejam reindexados após uma renomeação de diretório porque agora têm novas URLs, será necessário atualizar o `LastModified` carimbo de data/hora para todos os BLOBs no diretório para que o indexador saiba reindexá-los durante uma execução futura.