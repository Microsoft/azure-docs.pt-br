---
title: O que é a Tradução de Documento?
description: Uma visão geral do serviço e do processo de tradução de documentos em lote baseada em nuvem.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968162"
---
# <a name="what-is-document-translation-preview"></a>O que é a Tradução de Documento (versão prévia)?

A Tradução de Documento é um recurso baseado em nuvem do serviço [Tradutor do Azure](../translator-info-overview.md) e faz parte da família dos Serviços Cognitivos do Azure de APIs REST. A API de Tradução de Documentos traduz documentos de e para 90 idiomas e dialetos com a preservação da estrutura do documento e do formato dos dados.

Esta documentação contém os seguintes tipos de artigos:  

* Os [**guias de início rápido**](get-started-with-document-translation.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.
* Os [**guias de instruções**](create-sas-tokens.md) contêm instruções de uso do recurso de maneiras mais específicas ou personalizadas.  

## <a name="document-translation-key-features"></a>Principais recursos da Tradução de Documento

| Recurso | Descrição |
| ---------| -------------|
| **Traduzir grandes arquivos**| Traduza documentos inteiros de forma assíncrona.|
|**Traduzir vários arquivos**|Traduza vários arquivos de e para 90 idiomas e dialetos.|
|**Preservar a apresentação do arquivo de origem**| Traduza os arquivos, mas preservando o layout e o formato originais.|
|**Aplicar tradução personalizada**| Traduza documentos com modelos de [tradução personalizada](../customization.md#custom-translator) e geral.|
|**Aplicar glossários personalizados**|Traduza documentos usando glossários personalizados.|

## <a name="how-to-get-started"></a>Como começar?

Com nosso guia de instruções, você começará a usar rapidamente o Tradutor de Documento. Para começar, você precisará de uma [Conta do Azure](https://azure.microsoft.com/free/cognitive-services/).  Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Introdução](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Formatos de documento com suporte

A Tradução de Documento dá suporte aos seguintes tipos de arquivo de documento:

| Tipo de arquivo| Extensão de arquivo|Descrição|
|---|---|--|
|Adobe PDF|.pdf|Formato de documento portátil do Adobe Acrobat|
|HTML|.html|Linguagem HTML.|
|Formato de arquivo de intercâmbio de localização|.xlf. , xliff| Um formato de documento paralelo, exportação de sistemas de Memória de Tradução. Os idiomas utilizados são definidos dentro do arquivo.|
|Microsoft Excel|.xlsx|Um arquivo de planilha para análise de dados e documentação.|
|Microsoft Outlook|.msg|Uma mensagem de email criada ou salva no Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| Um arquivo de apresentação usado para exibir conteúdo em um formato de apresentação de slides.|
|Microsoft Word|.docx| Um arquivo de documento de texto.|
|Valores separados por tabulação/TAB|.tsv/.tab| Um arquivo de dados brutos delimitado por tabulação usado por programas de planilha.|
|Texto|.txt| Um documento de texto não formatado.|

## <a name="supported-glossary-formats"></a>Formatos de glossário com suporte

A Tradução de Documento dá suporte aos seguintes tipos de arquivo de glossário:

| Tipo de arquivo| Extensão de arquivo|Descrição|
|---|---|--|
|Formato de arquivo de intercâmbio de localização|.xlf. , xliff| Um formato de documento paralelo, exportação de sistemas de Memória de Tradução. Os idiomas utilizados são definidos dentro do arquivo.|
|Valores separados por tabulação/TAB|.tsv/.tab| Um arquivo de dados brutos delimitado por tabulação usado por programas de planilha.|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução à Tradução de Documento](get-started-with-document-translation.md)
