---
title: Perguntas frequentes – Content Moderator
titlesuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre como o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268891"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

### <a name="what-does-my-content-moderator-subscription-include"></a>O que minha assinatura do Content Moderator inclui?

Sua assinatura do Content Moderator inclui acesso a APIs e a ferramenta de revisão. Você pode decidir se deseja usar uma ou outra ou ambas, dependendo de suas necessidades de negócios.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Quais são os limites/restrições de conteúdo que podem ser moderados ao usar a API?

Ao usar a API, as imagens precisam ter, no mínimo, 128 pixels e o tamanho do arquivo máximo de 4 MB. Os textos podem ter no máximo 1024 de caracteres. Não há nenhum limite na duração da vídeo.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>O que acontecerá se o conteúdo passado para a API de texto ou para a API de imagem exceder os limites de tamanho?

A API de texto retorna um código de erro que informa que o texto é mais longo que o permitido. A API de imagem também retorna um código de erro que informa que a imagem não atende aos requisitos de tamanho.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Vocês mantêm as imagens, texto ou vídeos que são enviados para moderação?

O conteúdo é seu e talvez não sejam retido pela Microsoft sem o seu consentimento. A Microsoft usa medidas de segurança do setor para ajudar a proteger seu conteúdo.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Posso usar o Content Moderator para examinar imagens de exploração infantil ilegal?

Não. No entanto, as organizações qualificadas podem usar o [Serviço de Nuvem PhotoDNA](https://www.microsoft.com/photodna "Serviço de Nuvem do Microsoft PhotoDNA") para examinar este tipo de conteúdo.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Quantos revisar as equipes podem uma junção de usuário? O usuário pode alternar entre equipes?

Um usuário só pode ingressar em uma equipe por vez.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Que tipo de funções de membro de equipe são compatíveis com a ferramenta de análise? Como eles são diferentes?

As ferramentas de revisão atualmente permite atribuir funções de administrador e o revisor. Os administradores podem convidar outros usuários e têm acesso para as definições de configuração enquanto revisores só podem revisar marcas e os resultados de moderação ou desmarcar o conteúdo.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>O que é uma marca? A ferramenta de análise oferece suporte a marcas personalizadas?

Uma marca é um código curto de letra de um ou dois que denota um sinalizador de moderação, como 'a' para 'r' somente para adultos, para erótico e assim por diante. Os administradores podem definir novas marcas para seus negócios, conforme necessário.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Quantos membros da equipe posso ter na minha equipe de análise?

Você pode ter um máximo de cinco membros da equipe, incluindo o administrador, em uma equipe.
