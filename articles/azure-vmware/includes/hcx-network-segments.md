---
title: Segmentos de rede do HCX da VMware
description: Há quatro redes necessárias para o HCX da VMware.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999614"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Quatro redes são necessárias para o VMware HCX:

- **Rede de gerenciamento:** Normalmente é a mesma rede de gerenciamento usada no cluster vSphere. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da implantação.

   > [!NOTE]
   > O método que recomendamos é criar uma rede /26. Em uma rede /26, você pode usar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Você pode alongar oito redes por extensor de rede usando as nuvens privadas da Solução VMware no Azure.
   >
   
- **Rede do vMotion:** Normalmente é a mesma rede de usada para o vMotion no cluster vSphere.  No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da implantação.  

   A rede do vMotion deve ser exposta em um comutador virtual distribuído ou no vSwitch0. Se não estiver, modifique o ambiente.

   > [!NOTE]
   > Essa rede pode ser privada (não roteada).

- **Rede de uplink:** É interessante criar uma rede para o uplink do HCX da VMware e estendê-la para o cluster vSphere por meio de um grupo de portas. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da implantação.  

   > [!NOTE]
   > O método que recomendamos é criar uma rede /26. Em uma rede /26, você pode usar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Você pode alongar oito redes por extensor de rede usando as nuvens privadas da Solução VMware no Azure.
   >
   
- **Rede de replicação:** Isso é opcional. É interessante criar uma rede para a Replicação do HCX da VMware e estender essa rede para o cluster vSphere por meio de um grupo de portas. No mínimo, identifique dois IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da implantação.

   > [!NOTE]
   > Essa configuração só é possível quando os hosts de cluster local usam uma rede do kernel de VM de Replicação dedicada.  Se o cluster local não tiver uma rede do kernel de VM de Replicação dedicada definida, não será necessário criar essa rede.
