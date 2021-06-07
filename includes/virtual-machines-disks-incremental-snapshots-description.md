---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39750a86ccf781a10109e299e27a55a03173acb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900738"
---
Os instantâneos incrementais são backups pontuais para os discos gerenciados que, quando tirados, consistem apenas nas alterações desde o último instantâneo. Quando você restaura um disco de um instantâneo incremental, o sistema reconstrói o disco completo que representa o backup pontual do disco quando o instantâneo incremental foi tirado. Essa nova funcionalidade para instantâneos de disco gerenciado pode permitir que eles sejam mais econômicos, já que, a menos que você escolha, você não precisa armazenar todo o disco com cada instantâneo individual. Assim como instantâneos completos, instantâneos incrementais podem ser usados para criar um disco gerenciado completo ou um instantâneo completo.

Há algumas diferenças entre um instantâneo incremental e um instantâneo completo. Os instantâneos incrementais sempre usarão o armazenamento de HDDs padrão, independentemente do tipo de armazenamento do disco, enquanto os instantâneos completos podem usar o SSDs Premium. Se você estiver usando instantâneos completos no armazenamento Premium para escalar verticalmente as implantações de VM, recomendamos o uso de imagens personalizadas no armazenamento Standard na [Galeria de imagens compartilhadas](../articles/virtual-machines/shared-image-galleries.md). Ele o ajudará a alcançar uma escala mais maciça com menor custo. Além disso, os instantâneos incrementais podem oferecer melhor confiabilidade com ZRS ( [armazenamento com redundância de zona](../articles/storage/common/storage-redundancy.md) ). Se ZRS estiver disponível na região selecionada, um instantâneo incremental usará o ZRS automaticamente. Se ZRS não estiver disponível na região, o instantâneo usará como padrão o [armazenamento com redundância local](../articles/storage/common/storage-redundancy.md) (LRS). Você pode substituir esse comportamento e selecionar um manualmente, mas não é recomendável.

Os instantâneos incrementais também oferecem um recurso diferencial, disponível apenas para discos gerenciados. Eles permitem que você obtenha as alterações entre dois instantâneos incrementais dos mesmos discos gerenciados, até o nível de bloco. Você pode usar essa capacidade para reduzir o volume de dados ao copiar instantâneos entre regiões.  Por exemplo, você pode baixar o primeiro instantâneo incremental como um blob de base em outra região. Para os instantâneos incrementais subsequentes, você pode copiar somente as alterações desde o último instantâneo para o blob de base. Depois de copiar as alterações, você pode tirar instantâneos no blob de base que representam o backup pontual do disco em outra região. Você pode restaurar seu disco a partir do blob de base ou de um instantâneo no blob de base em outra região.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagrama que ilustra os instantâneos incrementais copiados entre regiões. Os instantâneos fazem várias chamadas à API até eventualmente formar blobs de página por cada instantâneo.":::

Os instantâneos incrementais são cobrados apenas pelo tamanho usado. Você pode encontrar o tamanho usado de seus instantâneos examinando o [relatório de uso do Azure](../articles/cost-management-billing/understand/review-individual-bill.md). Por exemplo, se o tamanho dos dados de um snapshot usado for 10 GiB, o relatório de uso **diário** mostrará 10 GiB/(31 dias) = 0,3226 como a quantidade consumida.