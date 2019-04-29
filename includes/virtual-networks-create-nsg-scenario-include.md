---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309900"
---
## <a name="scenario"></a>Cenário
Para ilustrar melhor como se cria NSGs, este documento usará o cenário a seguir:

![Cenário de Rede Virtual](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Nesse cenário, você criará um NSG para cada sub-rede na rede virtual **TestVNet**, conforme abaixo: 

* **NSG-FrontEnd**. O front-end de NSG será aplicado à sub-rede *FrontEnd* e contém duas regras:    
  * **rdp-rule**. Permite tráfego RDP para a sub-rede *FrontEnd*.
  * **web-rule**. Permite tráfego HTTP para a sub-rede *FrontEnd*.
* **NSG-BackEnd**. O back-end de NSG será aplicado à sub-rede *BackEnd* e contém duas regras:    
  * **sql-rule**. Permite o tráfego SQL somente da sub-rede *FrontEnd*.
  * **web-rule**. Nega todo o tráfego limitado da Internet da sub-rede *BackEnd*.

A combinação dessas regras cria um cenário de DMZ, em que a sub-rede de back-end só pode receber tráfego de entrada para SQL da sub-rede front-end e não tem acesso à Internet, enquanto a sub-rede de front-end pode se comunicar à Internet e receber somente solicitações HTTP de entrada.
