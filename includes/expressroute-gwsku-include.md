---
title: incluir arquivo
description: arquivo de inclusão
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504661"
---
Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Quando você seleciona um SKU de gateway maior, mais CPUs e largura de banda da rede são alocados para o gateway e como resultado, o gateway pode dar suporte a uma taxa de transferência de rede mais alta para a rede virtual. 

Os gateways de rede virtual de ExpressRoute podem usar os seguintes SKUs:

|     | Coexistência de Gateway de VPN e o ExpressRoute | FastPath | Número máximo de conexões de circuito |
| --- | --- | --- | --- |
| **Standard** | Sim | Não | 4 |
| **HighPerformance** | Sim | Não | 4 |
| **UltraPerformance** | Sim | Yes | 16 |