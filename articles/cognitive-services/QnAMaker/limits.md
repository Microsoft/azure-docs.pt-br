---
title: Limites – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker tem limites de meta para partes do serviço e da base de dados de conhecimento. É importante manter sua base de dados de conhecimento dentro desses limites para testar e publicar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b90b4806e86ed0ba33500cf31a6ed892241ceabe
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423460"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker

Os limites de QnA Maker fornecidos abaixo são uma combinação dos [limites do tipo de preço Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) e os [limites do tipo de preço do QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Você precisa saber os dois conjuntos de limites para entender quantas bases de dados de conhecimento você pode criar por recurso e o tamanho de cada base de dados de conhecimento.

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

O número máximo de bases de dados de conhecimento baseia-se nos [limites da camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Camada do Azure Search** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice, `testkb`, é usado para todas as bases de dados de conhecimento para criação e teste. 

## <a name="extraction-limits"></a>Limites de extração

### <a name="maximum-number-of-files"></a>Número máximo de arquivos

O número máximo de arquivos que podem ser extraídos e o tamanho máximo do arquivo baseia-se nos limites do seu **[QnA Maker tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de links profundos da URL

O número máximo de links profundos que podem ser rastreados para extração de QnAs de uma página de URL é **20**.

## <a name="metadata-limits"></a>Limites de metadados

O número máximo de campos de metadados por base de dados de conhecimento baseia-se em seus **[limites de camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Camada do Azure Search** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Máximo de campos de metadados por serviço QnA Maker (entre todas as bases de dados de conhecimento)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Dados de Conhecimento
Limites gerais sobre o conteúdo na base de dados de conhecimento:
* Tamanho do texto de resposta: 25.000
* Tamanho do texto da pergunta: 1.000
* Tamanho do texto de chave/valor dos metadados: 100
* Caracteres com suporte para nome de metadados: caracteres alfabéticos, dígitos e _  
* Caracteres com suporte para valor de metadados: Todos, exceto : e | 
* Tamanho do nome do arquivo: 200
* Formatos de arquivo com suporte: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de pergunta-resposta: Depende da **[camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** escolhida. Um par de perguntas e respostas é mapeado para um documento no índice Azure Search. 
* URL/página HTML: 1 milhão caracteres

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada da base de dados de conhecimento:
Eles representam os limites de cada ação de criação da base de dados de conhecimento; ou seja, clicar em *Criar KB* ou chamar a API CreateKnowledgeBase.
* Número máximo de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de arquivos: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada da base de dados de conhecimento
Eles representam os limites de cada ação de atualização; ou seja, clique em *Salvar e treinar* ou chame a API UpdateKnowledgeBase.
* Tamanho de cada nome de origem: 300
* Número máximo de perguntas alternativas adicionadas ou excluídas: 300
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizadas: 5

## <a name="next-steps"></a>Próximas etapas

Saiba quando e como alterar as camadas de serviço:

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Quando você precisar ter mais arquivos de origem ou documentos maiores na sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service.
* [Serviço de aplicativo](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Quando sua base de dados de conhecimento precisar fornecer mais solicitações do aplicativo cliente, atualize o tipo de preço do serviço de aplicativo.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Ao planejar ter muitas bases de conhecimento, atualize o tipo de preço do serviço do Azure Search.
