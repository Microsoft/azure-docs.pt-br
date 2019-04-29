---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731373"
---
O armazenamento com redundância de zona (ZRS) replica seus dados de forma síncrona em três clusters de armazenamento em uma única região. Cada cluster de armazenamento é fisicamente separado dos outros e está localizado em sua própria zona de disponibilidade (AZ). Cada zona de disponibilidade &mdash;e o cluster ZRS dentro dela&mdash; é autônomo e inclui utilitários e recursos de rede separados.

Quando você armazena seus dados em uma conta de armazenamento usando a replicação do ZRS, você pode continuar a acessar e gerenciar seus dados se uma zona de disponibilidade ficar indisponível. O ZRS oferece excelente desempenho e baixa latência. O ZRS oferece os mesmos [alvos de escalabilidade](../articles/storage/common/storage-scalability-targets.md) que o [armazenamento localmente redundante (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Considere o ZRS para cenários que exigem consistência, durabilidade e alta disponibilidade. Mesmo se uma interrupção ou desastre natural tornar indisponível uma zona de disponibilidade, o ZRS oferece durabilidade para objetos de armazenamento de pelo menos 99.9999999999% (12,9s) em um determinado ano.

Para obter informações sobre zonas de disponibilidade, consulte [visão geral de Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).