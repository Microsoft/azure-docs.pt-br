---
title: Personalizar um modelo de Pessoa no Video Indexer – Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de Pessoa no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 613ad2d0ab90bef016e80a33be12b71cba5e2f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047050"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo de Pessoa no Video Indexer

Video Indexer dá suporte ao reconhecimento de celebridade em seus vídeos. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Faces que não são reconhecidas pelo Video Indexer ainda são detectadas, mas não têm nome. Os clientes podem criar modelos de pessoa personalizados e permitir que Video Indexer reconheçam faces que não são reconhecidas por padrão. Os clientes podem criar esses modelos de pessoa emparelhando o nome de uma pessoa com arquivos de imagem da face da pessoa.  

Se sua conta atender a casos de uso diferentes, você poderá se beneficiar com a possibilidade de criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo em sua conta for destinado a ser classificado em canais diferentes, talvez você queira criar um modelo de pessoa separado para cada canal. 

> [!NOTE]
> Cada modelo de pessoa dá suporte a até 1 milhão pessoas e cada conta tem um limite de 50 modelos de pessoa. 

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Treinar uma nova face para um vídeo, atualiza o modelo personalizado específico ao qual o vídeo foi associado. 

Se você não precisar de suporte ao modelo de várias pessoas, não atribua uma ID do modelo de Pessoa ao vídeo ao carregar/indexar ou reindexar. Nesse caso, Video Indexer usará o modelo Person padrão em sua conta. 

Você pode usar o site Video Indexer para editar faces que foram detectadas em um vídeo e para gerenciar vários modelos de pessoa personalizada em sua conta, conforme descrito no tópico [Personalizar um modelo Person usando um site](customize-person-model-with-website.md) . Você também pode usar a API, conforme descrito em [Personalizar um modelo Person usando APIs](customize-person-model-with-api.md).
