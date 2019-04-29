---
title: Referência de API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre as diversas APIs de Análise e moderação de conteúdo para o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 320638c958799bbf7fea73880fd3e27b6d598d23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269491"
---
# <a name="content-moderator-api-reference"></a>Referência da API do Content Moderator

Comece a usar o Azure Content Moderator das seguintes maneiras: (Confira também [Gerenciamento de credenciais](review-tool-user-guide/credentials.md).)

- No portal do Azure, [inscreva-se para as APIs do Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Inscreva-se para [a ferramenta de análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/). Confira [Experimentar o Content Moderator na Web](quick-start.md).

## <a name="moderation-apis"></a>APIs de Moderação

Você pode usar as seguintes APIs do Content Moderator para configurar seus fluxos de trabalho de pós-moderação.

| DESCRIÇÃO | Referência |
| -------------------- |-------------|
| **API de moderação de imagem**<br /><br />Examine imagens e detecte possíveis conteúdos adultos e obscenos usando marcas, pontuações de confiança e outras informações extraídas. <br /><br />Use essas informações para publicar, rejeitar ou revisar o conteúdo do fluxo de trabalho pós-moderação. <br /><br />| [Referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Referência da API de moderação de imagem")   |
| **API de moderação de texto**<br /><br />Examine o conteúdo do texto. Termos de conteúdo ofensivo e dados pessoais são retornados. <br /><br />Use essas informações para publicar, rejeitar ou revisar o conteúdo do fluxo de trabalho pós-moderação.<br /><br /> | [Referência da API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Referência da API de moderação de texto")   |
| **API de moderação de vídeo**<br /><br />Examine vídeos e detectar potencial conteúdo adulto e obsceno. <br /><br />Use essas informações para publicar, rejeitar ou revisar o conteúdo do fluxo de trabalho pós-moderação.<br /><br /> | [Visão geral da API de moderação vídeo](video-moderation-api.md "Visão geral da API de moderação de vídeo")   |
| **API de Gerenciamento de Lista**<br /><br />Crie e gerencie listas personalizadas de exclusão ou inclusão de imagens e texto. Se habilitada, as operações **Imagem - Corresponder** e **Texto - Tela** fazem correspondência difusa do conteúdo enviado em suas listas personalizadas. <br /><br />Para eficiência, você pode ignorar a etapa de moderação com base em aprendizado de máquina.<br /><br /> | [Referência de API de gerenciamento de lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Referência de API de gerenciamento de lista")   |

## <a name="review-api"></a>API de Análise

A API de Análise tem os seguintes componentes:

| DESCRIÇÃO | Referência |
| -------------------- |-------------|
| **Trabalhos**<br /><br /> Inicie fluxos de trabalho de moderação de revisão e verificação de conteúdo de imagem e texto. Um trabalho de moderação verifica seu conteúdo usando a API de Moderação de Imagem e a API de Moderação de Texto. Trabalhos de moderação usam fluxos de trabalho definidos e padrão para gerar as análises. <br /><br />Depois que um moderador humano revisou as marcas atribuídas automaticamente e os dados de previsão e enviou uma decisão de moderação de conteúdo, a API de Análise envia todas as informações para o ponto de extremidade de API.<br /><br /> | [Referência de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Referência de trabalho")   |
| **Análises**<br /><br />Use a ferramenta de análise para criar análises de texto ou imagem diretamente para moderadores humanos.<br /><br /> Depois que um moderador humano revisou as marcas atribuídas automaticamente e os dados de previsão e enviou uma decisão de moderação de conteúdo, a API de Análise envia todas as informações para o ponto de extremidade de API.<br /><br /> | [Referência de análise](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Referência de análise")   |
| **Fluxos de trabalho**<br /><br />Crie, atualize e obtenha detalhes sobre os fluxos de trabalho personalizados que sua equipe cria. Você define fluxos de trabalho usando a ferramenta de análise. <br /> <br />Normalmente, os fluxos de trabalho usam o Content Moderator, mas também podem usar outras APIs determinadas que estão disponíveis como conectores na ferramenta de análise.<br /><br /> | [Referência de fluxo de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Referência de fluxo de trabalho")   |


