---
title: Segmentos de rede do HCX da VMware
description: Há quatro redes necessárias para o HCX da VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622096"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Há várias maneiras de configurar segmentos de rede do VMware HCX no local. O exemplo a seguir descreve uma configuração simples que dá suporte a um caso de uso de piloto ou de pequena produção.  Se o projeto for destinado a centenas ou milhares de cargas de trabalho, essa configuração poderá precisar ser alterada, dependendo das necessidades da migração.  

Em preparação para a implantação do VMware HCX a fim de dar suporte ao caso de uso de piloto ou de pequena produção, identifique o seguinte:

- **Rede de gerenciamento:** ao implantar o VMware HCX no local, você precisará definir uma rede de gerenciamento.  Normalmente, é a mesma rede de gerenciamento usada pelo cluster do VMware local.  No mínimo, identifique **dois** IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da escala da implantação, além do caso de uso de piloto ou pequeno.

> [!NOTE]
   > O método recomendado é criar uma rede /26, porque você pode usar até dez malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Você pode alongar **oito** redes por extensor de rede usando as nuvens privadas da Solução VMware no Azure.
   >
   
- **Rede do vMotion:** ao implantar o VMware HCX no local, você precisará definir uma rede do vMotion.  Normalmente, é a mesma rede usada para o vMotion pelo cluster do VMware local.  No mínimo, identifique **dois** IPs nesse segmento de rede para o VMware HCX. Talvez você precise de números maiores, dependendo da escala da implantação, além do caso de uso de piloto ou pequeno.

   A rede do vMotion deve ser exposta em um comutador virtual distribuído ou no vSwitch0. Se ela não estiver, modifique o ambiente para acomodá-la.

   > [!NOTE]
   > Muitos ambientes do VMware usam segmentos de rede não roteados para o vMotion, o que não apresenta nenhum problema.

- **Rede de uplink:** ao implantar o VMware HCX no local, você precisará definir uma rede de uplink. Use a rede de gerenciamento definida como a rede de uplink.
   
- **Rede de replicação:** ao implantar o VMware HCX no local, você precisará definir uma rede de replicação. Use a rede de gerenciamento definida como a rede de replicação.  Se os hosts de cluster locais usarem uma rede do kernel de VM de replicação dedicada, reserve **dois** endereços IP nesse segmento de rede e use a rede do kernel de VM de replicação para a rede de replicação.
