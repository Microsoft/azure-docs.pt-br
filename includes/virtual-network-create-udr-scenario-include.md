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
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743337"
---
## <a name="scenario"></a>Cenário
Para ilustrar melhor como se cria UDRs, este documento usará o cenário a seguir:

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-create-udr-scenario-include/figure1.png)

Neste cenário, você criará um UDR para a *sub-rede de Front-end* e outro UDR para a *Sub-rede de back-end*, como descrito abaixo: 

* **UDR-FrontEnd**. O UDR de front-end será aplicado à sub-rede *FrontEnd* e contém uma rota:    
  * **RouteToBackend**. Essa rota envia todo o tráfego à sub-rede de back-end para a máquina virtual **FW1**.
* **UDR-BackEnd**. O UDR de back-end será aplicado à sub-rede *BackEnd* e contém uma rota:    
  * **RouteToFrontend**. Essa rota envia todo o tráfego à sub-rede de front-end para a máquina virtual **FW1**.

A combinação dessas rotas garante que todo o tráfego destinado de uma sub-rede a outra seja roteado para a máquina virtual **FW1**, que está sendo usada como um dispositivo virtual. Você também precisa ativar o encaminhamento IP para essa VM **FW1** para garantir que ela possa receber o tráfego destinado a outras VMs.
