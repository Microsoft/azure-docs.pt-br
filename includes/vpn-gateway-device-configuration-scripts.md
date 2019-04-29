---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761600"
---
| **Fornecedor** | **Família do dispositivo** | **Versão do Firmware** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (versão prévia)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2 não BGP) para ASA abaixo de 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - não BGP) para ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|

> [!NOTE]
> (*) Obrigatório: NarrowAzureTrafficSelectors (habilite a opção UsePolicyBasedTrafficSelectors) e CustomAzurePolicies (IKE/IPsec)
>
