---
title: Adicionar o Azure Search ao Armazenamento de Blobs | Microsoft Docs
description: "Crie um índice no código usando a API REST HTTP da Pesquisa do Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.openlocfilehash: 15469e8a2d28bdf00d6e8d8c9f823c51975ee90e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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

Se essas imagens são pré-processadas usando a [API da Pesquisa Visual Computacional](https://www.microsoft.com/cognitive-services/computer-vision-api) dos Serviços Cognitivos da Microsoft, é possível indexar o conteúdo visual encontrado em cada imagem, incluindo OCR e reconhecimento de manuscrito. Estamos trabalhando na adição de OCR e outras funcionalidades de processamento de imagem diretamente para o Azure Search; se você estiver interessado nessas funcionalidades, envie uma solicitação em nosso [UserVoice](https://aka.ms/azsuv) ou [envie-nos um email](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indexar e pesquisar em blobs do JSON
O Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado nos blobs que contêm JSON. O Azure Search pode ler blobs do JSON e analisar o conteúdo estruturado nos campos apropriados de um documento do Azure Search. O Azure Search também pode levar os blobs que contêm uma matriz de objetos JSON e mapeiam cada elemento para um documento do Azure Search separado.

Atualmente a análise de JSON não é configurável por meio do Portal. [Saiba mais sobre análise de JSON no Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Início rápido
O Azure Search pode ser adicionado para blobs diretamente da página do portal de Armazenamento de Blobs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **Adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço do Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será direcionado para fora da experiência de portal da conta de Armazenamento. Você pode navegar de volta até a página do portal de Armazenamento e selecionar novamente a opção **Adicionar Azure Search**, onde você pode selecionar o serviço existente.

### <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Indexador de Blobs do Azure Search na [documentação](https://aka.ms/azsblobindexer) completa.
