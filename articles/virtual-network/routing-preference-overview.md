---
title: Preferência de roteamento no Azure
description: Saiba mais sobre como você pode escolher as rotas de tráfego entre o Azure e a Internet com a preferência de roteamento.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 44edd9273730ff7ba81a3ad5af2408ff6b7b7a5c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060628"
---
# <a name="what-is-routing-preference"></a>O que é preferência de roteamento?

A preferência de roteamento do Azure permite que você escolha as rotas de tráfego entre o Azure e a Internet. Você pode optar por rotear o tráfego por meio da rede da Microsoft ou por meio da rede do ISP (Internet pública). Essas opções também são conhecidas como *roteamento cold potato* e *roteamento hot potato*, respectivamente. O preço da transferência de dados de saída varia de acordo com a seleção de roteamento. Você pode escolher a opção de roteamento ao criar um endereço IP público. O endereço IP público pode ser associado aos recursos como máquina virtual, conjuntos de dimensionamento de máquinas virtuais, balanceador de carga voltado para a Internet etc. Defina também a preferência de roteamento para os recursos de armazenamento do Azure, como blobs, arquivos, Web e Azure Data Lake. Por padrão, o tráfego é roteado por meio da rede global da Microsoft para todos os serviços do Azure.

## <a name="routing-via-microsoft-global-network"></a>Roteamento por meio da rede global da Microsoft

Quando você roteia o tráfego por meio da *rede global da Microsoft*, o tráfego é entregue para uma das maiores redes do mundo, abrangendo mais de 160 mil milhas de fibra com mais de 165 POP (Ponto de Presença) de borda. A rede é bem provisionada com vários caminhos de fibra redundantes que visam garantir a confiabilidade e a disponibilidade excepcionalmente alta. A engenharia de tráfego é gerenciada por um controlador WAN definido pelo software que garante a seleção de caminho de baixa latência para o tráfego e oferece um desempenho de rede Premium.

![Roteamento por meio da rede global da Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Tráfego de entrada:** o comunicado global do BGP da Anycast garante que o tráfego de entrada entre na rede da Microsoft mais próximo ao usuário. Por exemplo, se um usuário de Singapura acessar os recursos do Azure hospedados em Chicago, nos EUA, o tráfego entrará na rede global da Microsoft no POP de borda de Singapura e viajará na rede da Microsoft para o serviço hospedado em Chicago.

**Tráfego de saída:** o tráfego de saída segue o mesmo princípio. O tráfego viaja na maior parte da jornada na rede global da Microsoft e sai mais próximo do usuário. Por exemplo, se o tráfego do Azure em Chicago for destinado a um usuário de Singapura, o tráfego viajará na rede da Microsoft de Chicago para Singapura e sairá da rede da Microsoft no POP de borda de Singapura.

Tanto o tráfego de entrada quanto o de saída permanecem grande parte da viagem na rede global da Microsoft. Isso também é conhecido como *roteamento cold potato*.


## <a name="routing-over-public-internet-isp-network"></a>Roteamento pela Internet pública (rede do ISP)

A nova escolha de *roteamento da Internet* minimiza a viagem na rede global da Microsoft e usa a rede do ISP de trânsito para rotear o tráfego. Essa opção de roteamento com otimização de custo oferece um desempenho de rede comparável a outros provedores de nuvem.

![Roteamento pela Internet pública](media/routing-preference-overview/route-via-isp-network.png)

**Tráfego de entrada:** o caminho de entrada usa o *roteamento hot potato*, o que significa que o tráfego entra na rede da Microsoft mais próxima da região do serviço hospedado. Por exemplo, se um usuário de Singapura acessa os recursos do Azure hospedados em Chicago, o tráfego viaja pela Internet pública e entra na rede global da Microsoft em Chicago.

**Tráfego de saída:** o tráfego de saída segue o mesmo princípio. O tráfego sai da rede da Microsoft na mesma região em que o serviço está hospedado. Por exemplo, se o tráfego do serviço do Azure em Chicago for destinado a um usuário de Singapura, o tráfego sairá da rede da Microsoft em Chicago e viajará pela Internet pública para o usuário em Singapura.

## <a name="supported-services"></a>Serviços com suporte

O IP público com a escolha de preferência de roteamento “Rede Global da Microsoft” pode ser associado a qualquer serviço do Azure. No entanto, o IP público com a escolha de preferência de roteamento **Internet** pode ser associado aos seguintes recursos do Azure:

* Máquina virtual
* Conjunto de escala de máquina virtual
* AKS (Serviço de Kubernetes do Azure)
* Balanceador de carga voltado para a Internet
* Gateway de Aplicativo
* Firewall do Azure

Para o armazenamento, os pontos de extremidade primários sempre usam a **rede global da Microsoft**. Habilite pontos de extremidade secundários com a **Internet** como a escolha de roteamento de tráfego. Os serviços de armazenamento compatíveis são:

* Blobs
* Arquivos
* Web
* Azure Data Lake

## <a name="pricing"></a>Preços
A diferença de preço entre as duas opções é refletida nos preços de transferência de dados de saída da Internet. O preço de transferência de dados do roteamento por meio da **rede global da Microsoft** é igual ao atual preço de saída da Internet. Visite a [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/) para obter as informações de preços mais recentes.

## <a name="limitations"></a>Limitações


* A preferência de roteamento é compatível apenas com o SKU Standard com redundância de zona do endereço IP público. Não há suporte para o SKU Básico do endereço IP público.
* Atualmente, a preferência de roteamento dá suporte apenas a endereços IP públicos IPv4. Não há suporte para endereços IP públicos IPv6.


## <a name="next-steps"></a>Próximas etapas

* [Configurar a preferência de roteamento para uma VM usando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configurar a preferência de roteamento para uma VM usando a CLI do Azure](configure-routing-preference-virtual-machine-cli.md)
