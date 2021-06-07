---
title: Recomendações e requisitos do contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006893"
---
> [!NOTE]
> Os requisitos e as recomendações são baseados em parâmetros de comparação com uma solicitação por segundo, usando uma imagem de 8 MB de uma carta empresarial digitalizada que contém 29 linhas e 803 caracteres no total.

A tabela a seguir descreverá a alocação mínima e recomendada de recursos para cada contêiner de Leitura.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
| Leia 2,0-visualização | 1 núcleo, 8 GB de memória |  8 núcleos, 16 GB de memória |
| 3\.2-preview da Leitura | 8 núcleos, 16 GB de memória | 8 núcleos, 24 GB de memória |

* Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.
