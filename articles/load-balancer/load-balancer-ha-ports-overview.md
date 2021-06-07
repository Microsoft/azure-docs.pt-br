---
title: Visão geral de portas de alta disponibilidade no Azure
titleSuffix: Azure Load Balancer
description: Saiba mais sobre o balanceamento de carga de portas de alta disponibilidade em um balanceador de carga interno.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94694952"
---
# <a name="high-availability-ports-overview"></a>Visão geral de portas de alta disponibilidade

O Azure Standard Load Balancer ajuda a balancear a carga de **todos os** fluxos de protocolo em **todas** as portas simultaneamente quando você estiver usando um Load balancer interno por meio de portas de alta disponibilidade.

As portas HA (alta disponibilidade) são um tipo de regra de balanceamento de carga que fornece uma maneira fácil de balancear a carga de **todos os** fluxos que chegam em **todas** as portas de um Standard Load balancer interno. A decisão de balanceamento de carga é feita por fluxo. A ação é baseada na seguinte conexão de cinco tuplas: endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e protocolo

As regras de balanceamento de carga de portas de HA o ajudam com cenários críticos, como alta disponibilidade e escala para NVAs (soluções de virtualização de rede) dentro de redes virtuais. O recurso também pode ajudar quando um grande número de portas precisar de balanceamento de carga. 

As regras de balanceamento de carga das portas de alta disponibilidade são configuradas quando você define as portas de front-end e back-end como **0** e o protocolo como **todos**. O recurso de balanceador de carga interno agora balanceia todos os fluxos TCP e UDP, independentemente do número da porta

## <a name="why-use-ha-ports"></a>Por que usar portas de alta disponibilidade?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Dispositivos de rede virtual

É possível usar as NVA para ajudar a proteger as cargas de trabalho do Azure contra vários tipos de ameaças à segurança. Ao usar as NVA nesses cenários, elas devem ser confiáveis, altamente disponíveis e expandir sob demanda.

Para atingir essas metas, basta adicionar instâncias NVA ao pool de back-end do balanceador de carga interno e configurar uma regra do balanceador de carga para portas de alta disponibilidade.

Para cenários de alta disponibilidade de NVA, portas de alta disponibilidade oferecem as seguintes vantagens:
- Fornecer failover rápido para instâncias íntegras com investigações de integridade por instância
- Garantir alto desempenho com expansão para instâncias *n*-ativas
- Fornecer cenários *n*-ativos e ativos-passivos
- Eliminar a necessidade de soluções complexas, como nós de Apache ZooKeeper para monitorar dispositivos

O diagrama a seguir apresenta uma implantação de rede virtual de hub e spoke. Os spokes forçam o tráfego por túnel para a rede virtual do hub e por meio de NVA, antes de deixar o espaço confiável. As NVAs estão por atrás de um Load Balancer Standard interno com a configuração de portas de alta disponibilidade. Todo o tráfego pode ser devidamente processado e encaminhado. Quando configurado como mostrado no diagrama a seguir, uma regra de balanceamento de carga de portas de alta disponibilidade também fornecerá simetria de fluxo para tráfego de entrada e saída.

<a node="diagram"></a>
![Diagrama de rede virtual de Hub e spoke, com NVAs implantado no modo de alta disponibilidade](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se você usar NVAs, confirme com o provedor a melhor maneira de usar portas de alta disponibilidade e quais cenários são compatíveis.

### <a name="load-balancing-large-numbers-of-ports"></a>Balanceamento de carga de grandes quantidades de portas

Também é possível usar portas de alta disponibilidade para aplicativos que exigem o balanceamento de carga de grandes quantidades de portas. Você pode simplificar esses cenários usando um [Load Balancer Standard](./load-balancer-overview.md) interno com portas de alta disponibilidade. Uma única regra de balanceamento de carga substitui várias regras individuais de balanceamento de carga, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

O recurso de portas de alta disponibilidade está disponível em todas as regiões globais do Azure.

## <a name="supported-configurations"></a>Configurações com suporte

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma única configuração de portas de alta disponibilidade de IP não flutuante (retorno de servidor não direto) em um balanceador de carga Standard interno

Essa configuração é uma configuração básica de portas de alta disponibilidade. Você pode configurar uma regra de balanceamento de carga de portas de alta disponibilidade em um único endereço IP de front-end, da seguinte maneira:
1. Ao configurar o Load Balancer Standard, selecione a caixa **Portas de alta disponibilidade** na configuração de regra do Load Balancer.
2. Para **IP flutuante**, selecione **Desabilitado**.

Essa configuração não permite nenhuma outra configuração de regra de balanceamento de carga no recurso do balanceador de carga atual. Ele também permite que nenhuma outra configuração de recurso de balanceador de carga interno para o determinado conjunto de instâncias de back-end.

No entanto, você pode configurar um Load Balancer Standard para as instâncias de back-end além dessa regra de porta de alta disponibilidade.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Uma única configuração de portas de alta disponibilidade de IP flutuante (retorno de servidor direto) em um Load Balancer Standard interno

Você pode configurar o balanceador de carga de forma similar para usar uma regra de balanceamento de carga com **Porta de alta disponibilidade** com um único front-end e configurando o **IP flutuante** como **Habilitado**. 

Ao usar essa configuração, você pode adicionar mais regras de balanceamento de carga de IP flutuante e/ou um balanceador de carga público. No entanto, você não pode usar uma configuração de balanceamento de carga de portas de alta disponibilidade de IP não flutuante com essa configuração.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Várias configurações de portas de alta disponibilidade em um Load Balancer Standard interno

Se seu cenário exigir que você configure mais de um front-end de porta de alta disponibilidade para o mesmo pool de back-end, você pode realizar as seguintes ações: 
- Configurar mais de um endereço IP privado de front-end para um único recurso do Load Balancer Standard interno.
- Configurar várias regras de balanceamento de carga, em que cada regra tem um único endereço IP de front-end selecionado.
- Selecione a opção **Portas de alta disponibilidade** e defina **IP flutuante** para **Habilitado** em todas as regras de balanceamento de carga.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Um balanceador de carga interno com portas de alta disponibilidade e um balanceador de carga público na mesma instância de back-end

Você pode configurar *um* recurso de Standard Load Balancer público para os recursos de back-end, juntamente com uma única Standard Load Balancer interna com portas de alta disponibilidade.

## <a name="limitations"></a>Limitações

- As regras de balanceamento de carga das portas de alta disponibilidade estão disponíveis somente para Standard Load Balancer internas.
- A combinação de uma regra de balanceamento de carga de portas de alta disponibilidade e uma regra de balanceamento de carga de portas não HA apontando para a mesma ipconfiguration (s) de back-end **não** tem suporte em uma configuração de IP de frontend único, a menos que ambas tenham o IP flutuante habilitado.
- Os fragmentos IP existentes serão encaminhados pelas regras de balanceamento de carga das portas de alta disponibilidade para o mesmo destino que o primeiro pacote.  Não há suporte para a fragmentação de IP de um pacote UDP ou TCP.
- A simetria do Flow (principalmente para cenários NVA) tem suporte com a instância de back-end e uma única NIC (e configuração de IP único) somente quando usada, conforme mostrado no diagrama acima, e usando regras de balanceamento de carga de portas de alta disponibilidade. Ela não é fornecida em nenhum outro cenário. Isso significa que dois ou mais recursos do Load Balancer e as respectivas regras tomam decisões independentes e nunca são coordenadas. Veja a descrição e o diagrama para [Soluções de virtualização de rede](#nva). Quando você estiver usando várias NICs ou sanduíches do NVA entre um Load Balancer público e interno, a simetria do fluxo não estará disponível.  Talvez seja possível solucionar isso fazendo a conversão de endereços de rede de origem do fluxo de entrada para o IP do dispositivo para permitir que as respostas cheguem ao mesmo NVA.  No entanto, é altamente recomendável usar uma única NIC e usar a arquitetura de referência mostrada no diagrama acima.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Standard Load Balancer](load-balancer-overview.md)
