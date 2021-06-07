---
title: Versões de software VMware
description: Versões de software VMware com suporte para a solução Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462500"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


As versões de software VMware usadas em novas implantações de clusters de nuvens privadas da solução do Azure VMware são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX-T <br />**Observação:** O NSX-T é a única versão com suporte do NSX.               |      2.5     |


Novos clusters adicionados a uma nuvem privada existente, a versão do software em execução no momento é aplicada. Para obter mais informações, consulte os [requisitos de versão do software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software do VMware. As versões de software de nuvem privada podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN). Você pode encontrar as políticas e os processos de atualização gerais para o software da plataforma de solução do Azure VMware descrito em [atualizações de nuvem privada e upgrades](../concepts-upgrades.md).

