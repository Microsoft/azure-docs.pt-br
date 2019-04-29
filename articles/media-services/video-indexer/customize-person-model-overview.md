---
title: Personalizar um modelo de Pessoa no Video Indexer – Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de Pessoa no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553718"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo de Pessoa no Video Indexer

Video Indexer dá suporte ao reconhecimento de celebridades seus vídeos. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Faces que não são reconhecidos pelo indexador de vídeo ainda são detectados, mas são deixados sem nome. Os clientes podem criar modelos de pessoa personalizados e habilitar o indexador de vídeo reconhecer rostos que não são reconhecidos por padrão. Os clientes podem criar esses modelos de pessoa, associe o nome de uma pessoa com arquivos de imagem de face da pessoa.  

Se sua conta lida com casos de uso diferentes, você pode aproveitar seja capaz de criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo em sua conta deve ser classificados em canais diferentes, você talvez queira criar um modelo de pessoa separado para cada canal. 

> [!NOTE]
> Cada pessoa modelo oferece suporte a até 1 milhão de pessoas e cada conta tem um limite de 50 modelos de pessoa. 

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Um novo rosto para obter um vídeo de treinamento, atualiza o modelo personalizado específico que o vídeo foi associado. 

Se você não precisar de suporte ao modelo de várias pessoas, não atribua uma ID do modelo de Pessoa ao vídeo ao carregar/indexar ou reindexar. Nesse caso, o indexador de vídeo usará o modelo padrão de pessoa em sua conta. 

Você pode usar o site do indexador de vídeo para editar as faces detectadas em um vídeo e gerenciar vários modelos de pessoa personalizados em sua conta, conforme descrito na [personalizar um modelo de pessoa usando um site](customize-person-model-with-website.md) tópico. Você também pode usar a API, conforme descrito em [personalizar um modelo de Person usando as APIs](customize-person-model-with-api.md).