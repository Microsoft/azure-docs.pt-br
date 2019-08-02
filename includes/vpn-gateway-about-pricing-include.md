---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666368"
---
Você paga por duas coisas: os custos de computação por hora para o gateway de rede virtual, e transferência de dados de saída do gateway de rede virtual. Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) . Para obter os preços do SKU de gateway herdado, confira a [página de preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) e role a página até a seção **Gateways de rede virtual**.

**Custos de computação do gateway de rede virtual**<br> Cada gateway de rede virtual tem um custo de computação por hora. O preço tem base no SKU do gateway que você especifica ao criar um gateway de rede virtual. O custo é para o próprio gateway e complementa a transferência de dados que fluem através do gateway. O custo de uma configuração ativa-ativa é a mesma que ativa-passiva.

**Custos de transferência de dados**<br>Os custos de transferência de dados são calculados com base no tráfego de saída do gateway de rede virtual de origem.

* Se você estiver enviando o tráfego para o seu dispositivo VPN local, ele será cobrado com a taxa de transferência de dados de saída da Internet.
* Se você estiver enviando tráfego entre redes virtuais em regiões diferentes, os preços terão base na região.
* Se você estiver enviando o tráfego somente entre redes virtuais que estão na mesma região, não haverá custo de dados. O tráfego entre VNets na mesma região é gratuito.
