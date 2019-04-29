---
title: Aprenda os conceitos da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a ferramenta de revisão do moderador conteúdo, um site que coordena um combinado de AI e o esforço de moderação de análise humana.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269963"
---
# <a name="content-moderator-review-tool"></a>Ferramenta de revisão do moderador de conteúdo

O Azure Content Moderator fornece serviços para combinar a moderação de conteúdo de aprendizado de máquina com análises humanas e o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) site é um front-end amigável que oferece acesso detalhado para esses serviços.

![O painel da ferramenta de revisão em um navegador](./images/0-dashboard.png)

## <a name="what-it-does"></a>O que faz

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), quando usados em conjunto com a moderação auxiliada por computador APIs, permite que você realize as seguintes tarefas no processo de moderação de conteúdo:

- Use um conjunto de ferramentas para moderar o conteúdo em vários formatos (texto, imagem e vídeo).
- Automatizar a criação de humanos [revisa](../review-api.md#reviews) quando a API de moderação resulta são fornecidos no.
- Atribuir ou escalar as revisões conteúdas de várias equipes de revisão, organizadas por nível de categoria ou a experiência de conteúdo.
- Use o padrão ou filtros de lógica personalizada ([fluxos de trabalho](../review-api.md#workflows)) para classificar e acompanhar o conteúdo, sem escrever nenhum código.
- Use [conectores](./configure.md#connectors) para processar o conteúdo com o Microsoft PhotoDNA, análise de texto e APIs de detecção facial, além das APIs de moderador de conteúdo.
- Criar seu próprio conector para criar fluxos de trabalho para qualquer API ou o processo de negócios.
- Obtenha métricas de desempenho chave em seus processos de moderação de conteúdo.

## <a name="review-tool-dashboard"></a>Painel da ferramenta de revisão

Sobre o **Dashboard** guia, você pode ver as principais métricas para análises de conteúdo feitas dentro da ferramenta. Ver o número do total, concluído e pendentes as análises de imagem, texto e conteúdo de vídeo. Você também pode ver a divisão dos usuários e equipes que foram concluídos, revisões, bem como as marcas de moderação que foram aplicadas.

![Exibir painel](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Credenciais da ferramenta de revisão

Quando você se inscreve com o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), você será solicitado a selecionar uma região do Azure para você da conta. Isso ocorre porque o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita dos serviços do Azure Content Moderator; você precisará dessa chave para acessar qualquer um dos serviços de uma chamada REST ou o SDK do cliente. Você pode exibir sua URL de ponto de extremidade de API e a chave selecionando **as configurações** > **credenciais**.

![Credenciais do Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Próximas etapas

Ver [configurar a ferramenta de revisão](./configure.md) para aprender a acessar recursos da ferramenta de revisão e alterar as configurações.