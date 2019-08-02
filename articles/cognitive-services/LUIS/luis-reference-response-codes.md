---
title: Códigos de resposta HTTP da API-LUIS
titleSuffix: Azure Cognitive Services
description: Entender quais códigos de resposta HTTP são retornados pelas APIs de Criação e de Ponto de extremidade
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: c92bcf55b571c37efa308d9121ee4aee714e684a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560076"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Códigos de resposta comuns da API e seus significados

As APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) e de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) retornam códigos de resposta HTTP. Enquanto as mensagens de resposta incluem informações específicas a uma solicitação, o código de status de resposta HTTP é geral. 

## <a name="common-status-codes"></a>Códigos de status comuns
A tabela a seguir lista alguns dos códigos de status de resposta HTTP mais comuns para as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) e de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356):

|Código|API|Explicação|
|:--|--|--|
|400|Criação, ponto de extremidade|os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|Criação, ponto de extremidade|o corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Criação|chave de assinatura do ponto de extremidade usada, em vez da chave de criação|
|401|Criação, ponto de extremidade|chave inválida, errada ou vazia|
|401|Criação, ponto de extremidade| a chave não corresponde à região|
|401|Criação|você não é o proprietário ou colaborador|
|401|Criação|ordem inválida das chamadas à API|
|403|Criação, ponto de extremidade|limite de cota mensal total de chave excedido|
|409|Ponto de extremidade|o aplicativo ainda está sendo carregado|
|410|Ponto de extremidade|o aplicativo precisa ser treinado e publicado novamente|
|414|Ponto de extremidade|a consulta excede o limite máximo de caracteres|
|429|Criação, ponto de extremidade|O limite de taxa foi excedido (solicitações/segundo)|

## <a name="next-steps"></a>Próximas etapas

* Documentação de [ponto de extremidade](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) e [criação](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) de API REST
