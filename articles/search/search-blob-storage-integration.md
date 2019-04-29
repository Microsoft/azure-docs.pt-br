---
title: Adicionar pesquisa de texto completo ao Armazenamento de Blob do Azure - Azure Search
description: Rastreie o conteúdo de texto no Armazenamento de Blob do Azure para indexar o Azure Search, no código, usando a API REST HTTP.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b7e7ecd2a82a8d64967288def9c6ede7a292f72a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127563"
---
# <a name="searching-blob-storage-with-azure-search"></a>Pesquisando no Armazenamento de Blobs com o Azure Search

Pesquisar toda a variedade de tipos de conteúdo armazenados no Armazenamento de Blobs do Azure pode ser um problema difícil de solucionar. No entanto, você pode indexar e pesquisar o conteúdo de seus Blobs em apenas alguns cliques usando o Azure Search. Pesquisar no Armazenamento de Blobs requer o provisionamento de um serviço Azure Search. Os diversos limites de serviço e tipos de preço do Azure Search podem ser encontrados na [página de preços](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>O que é o Azure Search?
O [Azure Search](https://aka.ms/whatisazsearch) é um serviço de pesquisa que torna mais fácil para os desenvolvedores adicionar experiências de pesquisa de texto completo robustas para aplicativos Web e móveis. Como um serviço, o Azure Search remove a necessidade de gerenciar qualquer infraestrutura de pesquisa, oferecendo simultaneamente um [SLA com tempo de atividade de 99,9%](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Índice e pesquisa de formatos de documentos corporativos
Com suporte para [extração de documento](https://aka.ms/azsblobindexer) no armazenamento de Blobs do Azure, você pode indexar o conteúdo a seguir:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Extraindo os metadados e o texto desses tipos de arquivo, você pode pesquisar em vários formatos de arquivo com uma única consulta. 

## <a name="search-through-your-blob-metadata"></a>Pesquisar os metadados de blob
Um cenário comum que facilita a classificação de blobs de qualquer tipo de conteúdo é a indexação dos metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações para todos os blobs são indexadas, independentemente do tipo de documento. Em seguida, você poderá classificar, filtrar e realizar a faceted navigation em todo o conteúdo do armazenamento de Blob.

[Saiba que mais sobre a indexação de metadados de blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Pesquisa de imagem
As funcionalidades de pesquisa de texto completo, navegação facetada e classificação do Azure Search agora podem ser aplicadas aos metadados de imagem armazenados em blobs.

Pesquisa cognitiva inclui as habilidades de processamento de imagem, como [reconhecimento óptico de caracteres (OCR)](cognitive-search-skill-ocr.md) e a identificação da [recursos visuais](cognitive-search-skill-image-analysis.md) que tornam possível indexar o conteúdo visual encontrado em cada imagem.

## <a name="index-and-search-through-json-blobs"></a>Indexar e pesquisar em blobs do JSON
O Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado nos blobs que contêm JSON. O Azure Search pode ler blobs do JSON e analisar o conteúdo estruturado nos campos apropriados de um documento do Azure Search. O Azure Search também pode levar os blobs que contêm uma matriz de objetos JSON e mapeiam cada elemento para um documento do Azure Search separado.

Atualmente a análise de JSON não é configurável por meio do Portal. [Saiba mais sobre análise de JSON no Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Início rápido
O Azure Search pode ser adicionado para blobs diretamente da página do portal de Armazenamento de Blobs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **Adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço do Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será direcionado para fora da experiência de portal da conta de Armazenamento. Você pode navegar de volta até a página do portal de Armazenamento e selecionar novamente a opção **Adicionar Azure Search**, onde você pode selecionar o serviço existente.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Indexador de Blobs do Azure Search na [documentação](https://aka.ms/azsblobindexer) completa.
