---
title: Solução de problemas de reinicializações inesperadas de VMs com VHDs anexados em VMs do Azure | Microsoft Docs
description: Como solucionar problemas de reinicializações inesperadas de VMs.
keywords: conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75358519"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se uma Máquina Virtual (VM) do Azure tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, você pode exceder os destinos de escalabilidade para uma conta de armazenamento individuais, fazendo com que a VM reinicie inesperadamente. Verifique as métricas de minuto para a conta de armazenamento (**TotalRequests** / **TotalIngress** / **TotalEgress**) para picos que excedam as metas de escalabilidade para uma conta de armazenamento. Consulte [Métricas mostram um aumento no PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para assistência para determinar se a limitação ocorreu em sua conta de armazenamento.

Em geral, cada operação de entrada ou saída individual em um VHD a partir de uma máquina virtual se traduz em operações **Get Page** ou **Put Page** no blob de página subjacente. Portanto, você pode usar os IOPS estimados para o seu ambiente para ajustar quantos VHDs você pode ter em uma única conta de armazenamento com base no comportamento específico do seu aplicativo. A Microsoft recomenda ter 40 ou menos discos em uma única conta de armazenamento. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md). Para obter mais informações sobre metas de escalabilidade para contas de armazenamento de blob de página Premium, consulte [metas de escalabilidade para contas de armazenamento de blob de páginas Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Se você estiver excedendo as metas de escalabilidade para sua conta de armazenamento, coloque seus VHDs em várias contas de armazenamento para reduzir a atividade de cada conta individual.
