---
title: Níveis de serviço no Azure NetApp Files | Microsoft Docs
description: Descreve o desempenho de taxa de transferência para os níveis de serviço do Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691046"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviço do Azure NetApp Files
O Azure NetApp Files dá suporte a dois níveis de serviço: Premium e Standard. 

## <a name="Premium"></a>Armazenamento Premium

O armazenamento *Premium* fornece até 64 MiB/s por TiB de taxa de transferência. O desempenho da taxa de transferência é indexado em relação à cota do volume. Por exemplo, um volume do armazenamento Premium com 2 TiB de cota provisionada (independentemente do consumo real) tem uma taxa de transferência de 128 MiB/s.

## <a name="Standard"></a>Armazenamento Standard

O armazenamento *Standard* fornece até 16 MiB/s por TiB de taxa de transferência. O desempenho da taxa de transferência é indexado em relação à cota do volume. Por exemplo, um volume do armazenamento Standard com 2 TiB de cota provisionada (independentemente do consumo real) tem uma taxa de transferência de 32 MiB/s.

## <a name="next-steps"></a>Próximas etapas

- Confira a [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) para ver o preço de diferentes níveis de serviço
- [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
