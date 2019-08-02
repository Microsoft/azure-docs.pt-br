---
title: Códigos de resposta HTTP da API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Entender quais códigos de resposta HTTP são retornados pelas APIs de QnA Maker. Isso ajudará você a solucionar quaisquer erros.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446530"
---
# <a name="qna-maker-api-http-response-codes"></a>Códigos de resposta HTTP da API de QnA Maker
As APIs de [gerenciamento](https://go.microsoft.com/fwlink/?linkid=2092179) e previsão retornam códigos de resposta HTTP. Enquanto as mensagens de resposta incluem informações específicas a uma solicitação, o código de status de resposta HTTP é geral. 

### <a name="management"></a>Gerenciamento

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|Os parâmetros da solicitação estão incorretos, o que significa que os parâmetros necessários estão ausentes, errados ou são muitos grandes|
|400|O corpo da solicitação está incorreto, o que significa que o JSON está ausente, errado ou é muito grande|
|401|Chave inválida|
|403|Proibido – você não tem as permissões corretas|
|404|O KB não existe|
|410|Essa API foi preterida e não está mais disponível|
