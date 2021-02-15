---
title: Versões de software VMware
description: Versões de software VMware com suporte para a solução Azure VMware.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825083"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


As versões de software atuais do software VMware usado em clusters de nuvem privada da solução Azure VMware são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>A única versão com suporte do NSX é a NSX-T.

Para qualquer novo cluster em uma nuvem privada, a versão do software corresponde ao que está em execução. Para qualquer nova nuvem privada em uma assinatura, a versão mais recente da pilha de software é instalada. Para obter mais informações, consulte os [requisitos de versão do software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software do VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN). Você pode encontrar as políticas e os processos de atualização gerais para o software da plataforma de solução do Azure VMware descrito em [atualizações de nuvem privada e upgrades](../concepts-upgrades.md).

