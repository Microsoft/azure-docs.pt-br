---
title: O que é o Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Saiba como usar Content Moderator para acompanhar, sinalizar, avaliar e filtrar material inadequado em conteúdo gerado pelo usuário.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/16/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, online moderator, content filtering software, content moderation service, content moderation
ms.openlocfilehash: b0ff5a241a76cd49d104e4145df37515b43c4e27
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726188"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Azure Content Moderator é um serviço de IA que permite que você lide com conteúdos potencialmente ofensivos, arriscados ou, de alguma outra forma, indesejáveis. Ele inclui o serviço de moderação de conteúdo da plataforma AI que examina textos, imagens e vídeos e aplica sinalizadores de conteúdo automaticamente, bem como a ferramenta de Análise, um ambiente de moderador online para uma equipe de revisores humanos.

Talvez você queira incorporar software de filtragem de conteúdo ao seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.

Esta documentação contém os seguintes tipos de artigos:  

* Os [**guias de início rápido**](client-libraries.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.  
* Os [**guias de instruções**](try-text-api.md) contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.  
* Os [**conceitos**](text-moderation-api.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.  
* Os [**tutoriais**](ecommerce-retail-catalog-moderation.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.  

## <a name="where-its-used"></a>Como ele é usado

Estes são alguns cenários nos quais uma equipe ou um desenvolvedor de software precisaria de um software de moderação de conteúdo:

- Marketplaces online que moderam catálogos de produtos e outros tipos de conteúdo gerados pelo usuário.
- Empresas de jogos que moderam artefatos de jogos gerados pelo usuário e salas de chat.
- Plataformas de mensagens sociais que moderam imagens, textos e vídeos adicionados pelos usuários.
- Empresas de mídia corporativa que implementam moderação de conteúdo centralizada para seu conteúdo.
- Provedores de soluções de ensino fundamental e médio que filtram conteúdo considerado inapropriado para alunos e professores.

> [!IMPORTANT]
> Não é possível usar o Content Moderator para examinar imagens ilícitas de exploração infantil. No entanto, as organizações qualificadas podem usar o [Serviço de Nuvem PhotoDNA](https://www.microsoft.com/photodna "Serviço de nuvem Microsoft PhotoDNA") para examinar este tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator consiste em várias APIs de serviço Web disponíveis por meio de chamadas REST e um SDK do .NET. Ele também inclui a ferramenta de análise, o que permite que revisores humanos ajudem o serviço e aprimorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço Content Moderator inclui APIs de Moderação que verificam se o conteúdo do material que é potencialmente indesejável ou inapropriado.

![diagrama de bloco para APIs de moderação do Content Moderator](images/content-moderator-mod-api.png)

A tabela a seguir descreve os diferentes tipos de APIs de moderação.

| Grupo de APIs | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Examina o texto quanto a conteúdo ofensivo, conteúdo sexualmente explícito ou sugestivo, conteúdo ofensivo e dados pessoais.|
|[**Listas de termos personalizadas**](try-terms-list-api.md)| Examina o texto em relação a uma lista de termos personalizados juntamente com os termos internos. Use listas personalizadas para bloquear ou permitir conteúdo de acordo com suas próprias políticas de conteúdo.|  
|[**Moderação de imagem**](image-moderation-api.md)| Verifica conteúdo adulto ou erótico em imagens, detecta textos em imagens com o recurso de OCR (reconhecimento óptico de caracteres) e detecta rostos.|
|[**Listas de imagens personalizadas**](try-image-list-api.md)| Verifica imagens em relação a uma lista personalizada de imagens. Use listas de imagem personalizadas para filtrar instâncias de conteúdo recorrentes que você não deseja classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Verifica conteúdo adulto ou erótico em vídeos e retorna os marcadores de tempo para esse conteúdo.|

## <a name="review-apis"></a>Analisar APIs

As APIs de Revisão permitem integrar seu pipeline de moderação com os revisores humanos. Use as operações [Trabalhos](review-api.md#jobs), [Revisões](review-api.md#reviews) e [Fluxo de Trabalho](review-api.md#workflows) para criar e automatizar fluxos de trabalho com envolvimento humano (human-in-the-loop) dentro da [Ferramenta de Revisão](#review-tool) (abaixo).

> [!NOTE]
> A API de Fluxo de Trabalho ainda não está disponível no SDK do .NET, mas pode ser usada com o ponto de extremidade REST.

![diagrama de bloco para APIs de revisão do Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Ferramenta de análise

O serviço do Content Moderator também inclui a [ferramenta Examinar](Review-Tool-User-Guide/human-in-the-loop.md) baseada na Web, que hospeda o conteúdo de revisões para moderadores humanos processarem. A entrada humana não treina o serviço, mas o trabalho combinado do serviço e das equipes de análise humana permite que os desenvolvedores alcancem o equilíbrio entre a precisão e a eficiência. A ferramenta de Análise também fornece um front-end amigável para uma variedade de recursos do Content Moderator.

![Page inicial da ferramenta de análise do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço Content Moderator devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Content Moderator no portal da Web, siga [Experimentar o Content Moderator na Web](quick-start.md). Ou conclua um [guia de início rápido da biblioteca de cliente ou API REST](client-libraries.md) para implementar os cenários básicos no código.