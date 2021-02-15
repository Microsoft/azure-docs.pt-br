---
title: Formato TSV de teste em lote-QnA Maker
titleSuffix: Azure Cognitive Services
description: Entender o formato TSV para testes em lotes
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132118"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV de teste do lote

O teste em lotes está disponível no [código-fonte](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) ou como um [executável que pode ser baixado](https://aka.ms/qna_btzip). O formato do comando para executar o teste em lotes é:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valor Esperado|
|--|--|
|1|nome do arquivo TSV formatado com os [campos de entrada TSV](#tsv-input-fields)|
|2|URI para ponto de extremidade, com o-HOST da página de publicação do portal de QnA Maker.|
|3|CHAVE de ponto de extremidade, encontrada na página publicar do portal de QnA Maker.|
|4|nome do arquivo TSV criado pelo teste do lote para resultados.|

Use as informações a seguir para entender e implementar o formato TSV para testes em lotes. 

## <a name="tsv-input-fields"></a>Campos de entrada TSV

|Campos do arquivo de entrada TSV|Observações|
|--|--|
|KBID|Sua ID da KB encontrada na página de publicação.|
|Pergunta|A pergunta que um usuário deve inserir.|
|Marcas de metadados|opcional|
|Parâmetro superior|opcional| 
|ID de resposta esperada|opcional|

![Formato de entrada para o arquivo TSV para teste em lotes.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campos de saída TSV 

|Parâmetros do arquivo de saída TSV|Observações|
|--|--|
|KBID|Sua ID da KB encontrada na página de publicação.|
|Pergunta|A pergunta como inserida no arquivo de entrada.|
|Resposta|Resposta principal da sua base de dados de conhecimento.|
|ID da resposta|ID da resposta|
|Pontuação|Pontuação de previsão para resposta. |
|Marcas de metadados|associado à resposta retornada|
|ID de resposta esperada|opcional (somente quando a ID de resposta esperada é fornecida)|
|Etiqueta de julgamento|opcional, os valores podem ser: correto ou incorreto (somente quando a resposta esperada é fornecida)|
