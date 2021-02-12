---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/20/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 241a2be834d7828cdb56f59313c0c1a9c478ff77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016500"
---
- Atualmente, esse recurso tem suporte apenas para SSDs Premium.
- Você deve desalocar sua VM ou desanexar o disco de uma VM em execução antes de poder alterar a camada do disco.
- Os níveis de desempenho P60, P70 e P80 só podem ser usados por discos maiores que 4.096 GiB.
- O nível de desempenho de um disco pode ser rebaixado apenas uma vez a cada 12 horas.