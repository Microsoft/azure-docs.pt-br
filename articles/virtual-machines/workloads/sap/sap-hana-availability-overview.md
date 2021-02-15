---
title: Disponibilidade SAP HANA em VMs do Azure - Visão geral | Microsoft Docs
description: Descreve as operações do SAP HANA em VMs nativas do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac74bb4b37535d0c0b095ea300afc15aa0978ed8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950118"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Alta disponibilidade do SAP HANA para máquinas virtuais do Azure

Você pode usar inúmeros recursos do Azure para implantar bancos de dados críticos como o SAP HANA nas VMs do Azure. Este artigo fornece diretrizes sobre como alcançar disponibilidade para instâncias do SAP HANA hospedadas em VMs do Azure. O artigo descreve vários cenários que você pode implementar usando a infraestrutura do Azure para aumentar a disponibilidade do SAP HANA no Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você está familiarizado com infraestruturas como serviço (IaaS) no Azure, incluindo: 

- Como implantar máquinas ou redes virtuais usando o portal do Azure ou o PowerShell.
- Usar a interface de linha de comando (CLI do Azure) de plataforma cruzada do Azure, incluindo a opção de usar modelos JavaScript Object Notation (JSON).

Este artigo também pressupõe que você está familiarizado com instalação de instâncias do SAP HANA e com a administração e a operação de instâncias do SAP HANA. É especialmente importante estar familiarizado com a instalação e as operações de replicação do sistema HANA. Isso inclui tarefas como backup e restauração para bancos de dados do SAP HANA.

Estes artigos fornecem uma boa visão geral de como usar o SAP HANA no Azure:

- [Instalação manual de SAP HANA de instância única em VMs do Azure](./hana-get-started.md)
- [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [Fazer backup do SAP HANA em VMs do Azure](./sap-hana-backup-guide.md)

Também é uma boa ideia estar familiarizado com estes artigos sobre o SAP HANA:

- [Alta disponibilidade para SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Perguntas frequentes: alta disponibilidade para SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Realizar replicação de sistema para SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 O que há de novo: alta disponibilidade](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Recomendações de rede para replicação de sistema do SAP HANA](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replicação de sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Reinicialização automática de serviço do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Configurar a replicação do sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Além de estar familiarizado com a implantação de VMs no Azure, antes de definir sua arquitetura de disponibilidade no Azure, é recomendável a leitura do artigo [Gerenciar a disponibilidade de máquinas virtuais Windows no Azure](../../manage-availability.md).

## <a name="service-level-agreements-for-azure-components"></a>Contratos de nível de serviço para componentes do Azure

O Azure possui diferentes SLAs de disponibilidade para diferentes componentes como rede, armazenamento e VMs. Todos os SLAs são documentados. Para obter mais informações, consulte [Contratos de Nível de Serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/). 

O [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) descreve três diferentes SLAs, para três configurações diferentes:

- Uma única VM que usa [SSDs Premium do Azure](../../managed-disks-overview.md) para o disco do SO e todos os discos de dados. Essa opção fornece um tempo de atividade mensal de 99,9%.
- Múltiplas (pelo menos duas) VMs organizadas em um [Conjunto de disponibilidade do Azure](../../windows/tutorial-availability-sets.md). Essa opção fornece um tempo de atividade mensal de 99,95%.
- Várias VMs (pelo menos duas) que são organizadas em uma [zona de disponibilidade](../../../availability-zones/az-overview.md). Essa opção forneceu um tempo de atividade mensal de 99,99%.

Meça o seu requisito de disponibilidade em relação aos SLAs os componentes do Azure podem fornecer. Em seguida, escolha seus cenários para o SAP HANA alcançar o nível necessário de disponibilidade.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Disponibilidade do SAP HANA em uma região do Azure](./sap-hana-availability-one-region.md).
- Saiba mais sobre [Disponibilidade do SAP HANA entre regiões do Azure](./sap-hana-availability-across-regions.md). 















