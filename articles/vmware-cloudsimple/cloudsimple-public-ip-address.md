---
title: Solução do Azure VMware por endereço IP público CloudSimple
description: Saiba mais sobre endereços IP públicos e seus benefícios na solução do Azure VMware por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "77024935"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Visão geral do endereço IP público do CloudSimple

Um endereço IP público permite que os recursos da Internet comuniquem a entrada para recursos de nuvem privada em um endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software em sua nuvem privada vCenter. O endereço IP público permite que você exponha serviços em execução na sua nuvem privada para a Internet.

O endereço IP público é dedicado ao endereço IP privado até que você o cancele. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público sempre usa o endereço IP público para acesso à Internet. Por padrão, somente o acesso à Internet de saída é permitido em um endereço IP público.  O tráfego de entrada no endereço IP público foi negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

O uso de um endereço IP público para comunicar a entrada fornece:

* Prevenção de ataque de DDoS (negação de serviço distribuído). Essa proteção é habilitada automaticamente para o endereço IP público.
* Monitoramento de tráfego sempre ativa e mitigação em tempo real de ataques comuns em nível de rede. Essas defesas são as mesmas usadas pelo Microsoft serviços online.
* Toda a escala da rede global do Azure. A rede pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões.  

## <a name="next-steps"></a>Próximas etapas

* Saiba como [alocar um endereço IP público](public-ips.md)