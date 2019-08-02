---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171843"
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

