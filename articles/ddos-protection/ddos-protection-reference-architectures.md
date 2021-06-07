---
title: Arquiteturas de referência da proteção contra DDoS do Azure
description: Conheça as arquiteturas de referência da proteção contra DDoS do Azure.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992430"
---
# <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência da Proteção contra DDoS

A proteção contra DDoS Standard é projetada [para serviços que são implantados em uma rede virtual](../virtual-network/virtual-network-for-azure-services.md). Para outros serviços, a Proteção contra DDoS Básica padrão se aplica. As seguintes arquiteturas de referência são organizadas por cenários, com padrões de arquitetura agrupados.

## <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Aplicativo em execução em VMs com balanceamento de carga

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para executar várias VMs do Windows em um conjunto de dimensionamento atrás de um balanceador de carga para melhorar a disponibilidade e a escalabilidade. Essa arquitetura pode ser usada para qualquer carga de trabalho sem estado, como um servidor Web.

![Diagrama da arquitetura de referência para um aplicativo em execução em VMs com balanceamento de carga](./media/ddos-best-practices/image-9.png)

Nesta arquitetura, uma carga de trabalho é distribuída em várias instâncias de VM. Há um único endereço IP público e o tráfego da Internet é distribuído para as VMs por meio de um balanceador de carga. A Proteção contra DDoS Standard está habilitada na rede virtual do balanceador de carga do Azure (Internet) que tem o IP público associado a ela.

O balanceador de carga distribui as solicitações de entrada da Internet para as instâncias de VM. Os conjuntos de dimensionamento de máquinas virtuais permitem que o número de VMs seja reduzido ou aumentado horizontalmente manualmente, ou automaticamente com base em regras predefinidas. Isso é importante se o recurso está sob ataque de DDoS. Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Aplicativo em execução em N camadas do Windows

Há muitas maneiras de implementar uma arquitetura de N camadas. O diagrama a seguir mostra um aplicativo Web típico de três camadas. Essa arquitetura se baseia no artigo [Executar VMs com balanceamento de carga para escalabilidade e disponibilidade](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). As camadas comerciais e da Web usam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para um aplicativo em execução em N camadas do Windows](./media/ddos-best-practices/image-10.png)

Nesta arquitetura, a Proteção contra DDoS Standard está habilitada na rede virtual. Todos os IPs públicos na rede virtual terão proteção contra DDoS nas camadas 3 e 4. Para proteção da camada 7, implante o Gateway de Aplicativo no SKU do WAF. Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Não há suporte para cenários nos quais uma única VM está sendo executada por trás de um IP público.

### <a name="paas-web-application"></a>Aplicativo Web PaaS

Essa arquitetura de referência mostra a execução de um aplicativo do Serviço de Aplicativo do Azure em uma única região. Essa arquitetura mostra um conjunto de práticas comprovadas para um aplicativo Web que usa o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/services/app-service/) e o [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Uma região em espera é configurada para cenários de failover.

![Diagrama da arquitetura de referência para um aplicativo da Web de PaaS](./media/ddos-best-practices/image-11.png)

O Gerenciador de Tráfego do Microsoft Azure roteia as solicitações de entrada para o Gateway de Aplicativo em uma das regiões. Durante as operações normais, ele roteia as solicitações para o Gateway de Aplicativo na região ativa. Se essa região ficar não disponível, o Gerenciador de Tráfego fará failover para o Gateway de Aplicativo na região em espera.

Todo o tráfego da Internet destinado ao aplicativo Web é roteado para o [Endereço IP público do Gateway de Aplicativo](../application-gateway/application-gateway-web-app-overview.md) por meio do Gerenciador de Tráfego. Nesse cenário, o Serviço de Aplicativo (aplicativo Web) em si não é diretamente externo e está protegido pelo Gateway de Aplicativo. 

É recomendável configurar o SKU do WAF do Gateway de Aplicativo (modo de prevenção) para ajudar a proteger contra ataques da camada 7 (HTTP/HTTPS/WebSocket). Além disso, os aplicativos Web são configurados para [aceitar somente o tráfego do endereço IP do Gateway de Aplicativo](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços PaaS não Web

### <a name="hdinsight-on-azure"></a>HDInsight no Azure

Essa arquitetura de referência mostra a configuração da Proteção contra DDoS Standard para [cluster do Azure HDInsight](../hdinsight/index.yml). Verifique se o cluster do HDInsight está vinculado a uma rede virtual e se a Proteção contra DDoS está habilitada nessa rede virtual.

![Os painéis "HDInsight" e "Configurações avançadas", com as configurações de rede virtual](./media/ddos-best-practices/image-12.png)

![Seleção para habilitar a proteção contra DDoS](./media/ddos-best-practices/image-13.png)

Nesta arquitetura, o tráfego destinado ao cluster de HDInsight vindo da Internet é roteado para o IP público associado ao balanceador de carga de gateway do HDInsight. O balanceador de carga de gateway, em seguida, envia o tráfego para os nós principais ou nós de trabalho diretamente. Como a Proteção contra DDoS Standard está habilitada na rede virtual do HDInsight, todos os IPs públicos na rede virtual receberão proteção contra DDoS nas camadas 3 e 4. Essa arquitetura de referência pode ser combinada com arquiteturas de referência de N camadas e com várias regiões.

Para obter mais informações sobre a arquitetura de referência, consulte a documentação [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> Azure app Service - Serviço de Aplicativo do Azure para gerenciamento de API ou PowerApps em uma rede virtual com um IP público são ambos não tem suporte nativo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um plano de proteção contra DDoS](manage-ddos-protection.md).