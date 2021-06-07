---
title: Rede de contêineres com a Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre o plug-in de CNI (adaptador de rede de contêiner) da Rede Virtual do Azure e saiba como habilitar contêineres para usar uma Rede Virtual do Azure.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 727e79cede9319cb7aae701082d5842ad895a61d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223509"
---
# <a name="enable-containers-to-use-azure-virtual-network-capabilities"></a>Habilitar contêineres para usar as funcionalidades de Rede Virtual do Azure

Leve o conjunto avançado de funcionalidades de rede do Azure para os contêineres utilizando a mesma pilha de rede definida pelo software que alimenta as máquinas virtuais. O plug-in da CNI (adaptador de rede de contêineres) da Rede Virtual do Azure contêiner é instalado em uma máquina virtual do Azure. O plug-in atribui endereços IP de uma rede virtual aos contêineres acionados na máquina virtual, anexando-os à rede virtual e conectando-os diretamente a outros contêineres e aos recursos da rede virtual. O plug-in não depende de redes de sobreposição nem de rotas para a conectividade e fornece o mesmo desempenho que as máquinas virtuais. Em geral, o plug-in fornece as seguintes funcionalidades:

- Um endereço IP de rede virtual é atribuído a cada pod, que pode consistir em um ou mais contêineres.
- Os pods podem se conectar a redes virtuais emparelhadas e à VPN local pelo ExpressRoute ou à VPN site a site. Os pods também podem ser acessados por redes emparelhada e locais.
- Os pods podem acessar serviços como o Armazenamento do Azure e o Banco de Dados SQL do Azure, que são protegidos por pontos de extremidade de serviço de rede virtual.
- Grupos de segurança de rede e rotas podem ser aplicados diretamente aos pods.
- Os pods podem ser colocados diretamente atrás do Azure Load Balancer interno ou público, assim como as máquinas virtuais
- Os pods podem ser atribuídos a um endereço IP público, o que os torna diretamente acessíveis pela Internet. Os pods também podem acessar a Internet por conta própria.
- Funciona perfeitamente com os recursos de Kubernetes como os serviços, os controladores de entrada e o DNS Kube. Um serviço de Kubernetes também pode ser exposto interna ou externamente por meio do Azure Load Balancer.

A imagem a seguir mostra como o plug-in fornece as funcionalidades de Rede Virtual do Azure aos pods:

![Visão geral da rede de contêineres](./media/container-networking/container-networking-overview.png)

O plug-in dá suporte às plataformas Linux e Windows.

## <a name="connecting-pods-to-a-virtual-network"></a>Conectando pods a uma rede virtual

Os pods são ativados em uma máquina virtual que faz parte de uma rede virtual. Um pool de endereços IP dos Pods é configurado como endereços secundários no adaptador de rede da máquina virtual. A CNI do Azure define a conectividade de rede básica dos pods e gerencia a utilização dos endereços IP no pool. Quando um pod é ativado na máquina virtual, a CNI do Azure atribui um endereço IP disponível do pool e conecta o pod a uma ponte de software na máquina virtual. Quando o pod termina, o endereço IP é adicionado novamente ao pool. A imagem a seguir mostra como os pods se conectam a uma rede virtual:

![Detalhe da rede de contêineres](./media/container-networking/container-networking-detail.png)

## <a name="internet-access"></a>Acesso à Internet

Para permitir que os pods acessem a Internet, o plug-in configura as regras *iptables* para fazer a NAT (conversão de endereços de rede) do tráfico limitado da Internet dos pods. O endereço IP de origem do pacote é convertido no endereço IP primário no adaptador de rede da máquina virtual. As Máquinas Virtuais do Windows originam automaticamente o tráfego NAT (SNAT) destinado a endereços IP fora da sub-rede em que a máquina virtual está. Normalmente, todo o tráfego destinado a um endereço IP fora do intervalo de IP da rede virtual é convertido.

## <a name="limits"></a>limites

O plug-in dá suporte a até 250 pods por máquina virtual e até 16 mil pods em uma rede virtual. Esses limites são diferentes para o [Serviço de Kubernetes do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-kubernetes-service-limits).

## <a name="using-the-plug-in"></a>Usando o plug-in

O plug-in pode ser usado das seguintes maneiras para fornecer conexão de rede virtual básica para pods ou contêineres do Docker:

- **Serviço de Kubernetes do Azure**: O plug-in é integrado ao AKS (Serviço de Kubernetes do Azure) e pode ser usado escolhendo a opção *Rede Avançada*. A Rede Avançada permite implantar um cluster Kubernetes em uma rede virtual existente ou nova. Para saber mais sobre a Rede Avançada e as etapas para configurá-la, confira [configuração de rede no AKS](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **AKS-Engine**: O AKS-Engine é uma ferramenta que gera um modelo do Azure Resource Manager para a implantação de um cluster Kubernetes no Azure. Para obter instruções detalhadas, confira [Implantar o plug-in em clusters Kubernetes do AKS-Engine](deploy-container-networking.md#deploy-the-azure-virtual-network-container-network-interface-plug-in).
- **Criando seu próprio cluster Kubernetes no Azure**: O plug-in pode ser usado para fornecer uma rede básica para pods em clusters Kubernetes implantados por você, sem depender do AKS nem de ferramentas como o AKS-Engine. Nesse caso, o plug-in é instalado e habilitado em cada máquina virtual em um cluster. Para ver instruções detalhadas, confira [Implantar o plug-in em um cluster Kubernetes que você implanta por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).
- **Anexo de rede virtual para contêineres do Docker no Azure**: O plug-in pode ser usado quando você não quer criar um cluster Kubernetes e deseja criar contêineres do Docker com anexo de rede virtual, em máquinas virtuais. Para obter instruções detalhadas, confira [Implantar o plug-in para Docker](deploy-container-networking.md#deploy-plug-in-for-docker-containers).

## <a name="next-steps"></a>Próximas etapas

[Implantar o plug-in](deploy-container-networking.md) para clusters Kubernetes ou contêineres do Docker