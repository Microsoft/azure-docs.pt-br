---
title: "Visão geral das Zonas de Disponibilidade | Microsoft Docs"
description: "Este artigo fornece uma visão geral das Zonas de Disponibilidade no Azure."
services: 
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c6225d368044ee2b2ab24136c854f81ae473c9e1
ms.contentlocale: pt-br
ms.lasthandoff: 09/22/2017

---

# <a name="overview-of-availability-zones-in-azure-preview"></a>Visão geral das Zonas de Disponibilidade no Azure (versão prévia)

As Zonas de Disponibilidade ajudam a te proteger contra falhas no nível do datacenter. Elas estão localizadas dentro de uma região do Azure, e cada uma tem sua própria fonte de energia, rede e resfriamento independente. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física e lógica das Zonas de Disponibilidade dentro de uma região protege dados e aplicativos contra falhas no nível da zona. 

![exibição conceitual de uma zona reduzindo-se a uma região](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Regiões que oferecem suporte às Zonas de Disponibilidade

- Leste dos EUA 2
- Europa Ocidental

## <a name="services-that-support-availability-zones"></a>Serviços que oferecem suporte às Zonas de Disponibilidade

Os serviços do Azure que oferecem suporte às Zonas de Disponibilidade são:

- Máquinas Virtuais do Linux
- Máquinas Virtuais do Windows
- Conjuntos de dimensionamento de máquinas virtuais em zona
- Managed Disks
- Balanceador de carga

## <a name="supported-virtual-machine-size-families"></a>Famílias de tamanho de máquina virtual com suporte

- Av2
- Dv2
- DSv2


## <a name="get-started-with-the-availability-zones-preview"></a>Introdução à versão prévia das Zonas de Disponibilidade

A versão prévia das Zonas de Disponibilidade está disponível nas regiões Leste dos EUA 2 e Europa Ocidental para serviços específicos do Azure. 

1. [Inscreva-se na versão prévia das Zonas de Disponibilidade](http://aka.ms/azenroll). 
2. Entre em sua assinatura do Azure.
3. Escolha uma região que oferece suporte às Zonas de Disponibilidade.
4. Use um dos links a seguir para começar a usar as Zonas de Disponibilidade com seu serviço. 
    - [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
    - [Criar um conjunto de dimensionamento de máquinas virtuais em zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)
    - [Adicionar um Disco Gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [Balanceador de carga](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>Próximas etapas
- [Modelos de início rápido](http://aka.ms/azqs)

