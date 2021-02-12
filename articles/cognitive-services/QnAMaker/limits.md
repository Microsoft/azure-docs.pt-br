---
title: Limites – QnA Maker
description: O QnA Maker tem limites de meta para partes do serviço e da base de dados de conhecimento. É importante manter sua base de dados de conhecimento dentro desses limites para testar e publicar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1e57ae537c271e61f0b2d37f5320cb177b04802b
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98164865"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker

Os limites de QnA Maker fornecidos abaixo são uma combinação dos [limites do tipo de preço do Azure pesquisa cognitiva](../../search/search-limits-quotas-capacity.md) e os limites do tipo de preço do [QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Você precisa saber os dois conjuntos de limites para entender quantas bases de dados de conhecimento você pode criar por recurso e o tamanho de cada base de dados de conhecimento.

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

O número máximo de bases de dados de conhecimento baseia-se nos [limites da camada de pesquisa cognitiva do Azure](../../search/search-limits-quotas-capacity.md).

|**Camada de Pesquisa Cognitiva do Azure** | **Gratuito** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice, `testkb`, é usado para todas as bases de dados de conhecimento para criação e teste.

## <a name="extraction-limits"></a>Limites de extração

### <a name="file-naming-constraints"></a>Restrições de nomenclatura de arquivo

Os nomes de arquivo não podem incluir os seguintes caracteres:

|Não usar caractere|
|--|
|Aspa simples `'`|
|Aspas duplas `"`|

### <a name="maximum-file-size"></a>Tamanho máximo do arquivo

|Formatar|Tamanho máximo do arquivo (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de arquivos

O número máximo de arquivos que podem ser extraídos e o tamanho máximo do arquivo baseia-se nos limites do seu **[QnA Maker tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker gerenciado (versão prévia) é um serviço gratuito sem limites no número de fontes que podem ser adicionadas. A taxa de transferência está atualmente limitada a 10 transações por segundo para APIs de gerenciamento e APIs de previsão.

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de links profundos da URL

O número máximo de links profundos que podem ser rastreados para extração de QnAs de uma página de URL é **20**.

## <a name="metadata-limits"></a>Limites de metadados

Os metadados são apresentados como um par chave com base em texto: valor, como `product:windows 10` . Ele é armazenado e comparado em letras minúsculas.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Pelo Azure Pesquisa Cognitiva tipo de preço

O número máximo de campos de metadados por base de dados de conhecimento baseia-se nos **[limites da camada de pesquisa cognitiva do Azure](../../search/search-limits-quotas-capacity.md)**.

|**Camada de Pesquisa Cognitiva do Azure** | **Gratuito** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Máximo de campos de metadados por serviço QnA Maker (entre todas as bases de dados de conhecimento)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Por nome e valor

O comprimento e os caracteres aceitáveis para o nome e o valor dos metadados são listados na tabela a seguir.

|Item|Caracteres permitidos|Correspondência de padrão de Regex|Máximo de caracteres|
|--|--|--|--|
|Nome (chave)|Permitem<br>alfanumérico (letras e dígitos)<br>`_` sublinhado<br> Não deve conter espaços.|`^[a-zA-Z0-9_]+$`|100|
|Valor|Permite tudo, exceto<br>`:` pontos<br>`|` (canal vertical)<br>Apenas um valor é permitido.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Dados de Conhecimento
Limites gerais sobre o conteúdo na base de dados de conhecimento:
* Tamanho do texto de resposta: 25.000 caracteres
* Tamanho do texto da pergunta: 1.000 caracteres
* Comprimento do texto da chave de metadados: 100 caracteres
* Comprimento do texto do valor de metadados: 500 caracteres
* Caracteres com suporte para nome de metadados: alfabetos, dígitos e `_`
* Caracteres com suporte para valor de metadados: todos exceto `:` e `|`
* Tamanho do nome do arquivo: 200
* Formatos de arquivo com suporte: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de resposta de pergunta: depende da **[camada de pesquisa cognitiva do Azure](../../search/search-limits-quotas-capacity.md#document-limits)** escolhida. Um par de perguntas e respostas é mapeado para um documento no índice de Pesquisa Cognitiva do Azure.
* URL/página HTML: 1 milhão caracteres

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada da base de dados de conhecimento:
Eles representam os limites de cada ação de criação da base de dados de conhecimento; ou seja, clicar em *Criar KB* ou chamar a API CreateKnowledgeBase.
* Número máximo recomendado de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de arquivos: 10
* Número máximo de QnAs permitido por chamada: 1000

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada da base de dados de conhecimento
Eles representam os limites de cada ação de atualização; ou seja, clique em *Salvar e treinar* ou chame a API UpdateKnowledgeBase.
* Tamanho de cada nome de origem: 300
* Número máximo recomendado de perguntas alternativas adicionadas ou excluídas: 300
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizadas: 5
* Número máximo de QnAs permitido por chamada: 1000

## <a name="next-steps"></a>Próximas etapas

Saiba quando e como alterar os [tipos de preço de serviço](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
