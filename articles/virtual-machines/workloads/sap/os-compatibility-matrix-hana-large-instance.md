---
title: Matriz de Compatibilidade do Sistema Operacional para SAP HANA (Grandes Instâncias) | Microsoft Docs
description: A matriz de compatibilidade representa a compatibilidade de diferentes versões do sistema operacional com tipos de hardware diferentes (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccfdffc4e488de7f3cecb150305596743b3a9e44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675411"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemas Operacionais Compatíveis para Instâncias Grandes do HANA

## <a name="hana-large-instance-type-i"></a>Instância Grande do HANA Tipo I     
  | Sistema operacional | Disponibilidade        | SKUs                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Não é mais oferecido | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7.6         | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>SKUs de Memória Persistente
  | Sistema operacional | Disponibilidade | SKUs                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponível    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Instância Grande do HANA Tipo II     
  |  Sistema operacional       | Disponibilidade        | SKUs                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | Não é mais oferecido | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP3             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP4             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP5             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | RHEL 7.6                | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |

## <a name="related-documents"></a>Documentos relacionados

- Para saber mais sobre [SKUs disponíveis](hana-available-skus.md)
- Para saber mais sobre [Atualizar o sistema operacional](os-upgrade-hana-large-instance.md)
  

  
  
