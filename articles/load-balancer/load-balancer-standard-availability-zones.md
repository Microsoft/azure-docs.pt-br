---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titleSuffix: Azure Load Balancer
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e Zonas de Disponibilidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: fca6b40f4bd871041d14d119eb44e8366d8562ee
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700453"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e Zonas de Disponibilidade

O Azure Standard Load Balancer dá suporte a cenários de zonas de disponibilidade. Você pode usar o balanceador de carga padrão para aumentar a disponibilidade em todo o cenário alinhando os recursos com e a distribuição entre as zonas. As zonas de disponibilidade em combinação com o Load Balancer standard são um conjunto de recursos extenso e flexível que pode criar vários cenários diferentes.  Analise este documento para entender esses [conceitos](#concepts) e as [diretrizes de design](#design) de cenário fundamentais.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Conceitos de Zonas de Disponibilidade aplicados ao Load Balancer

Um balanceador de carga herda a configuração de zona de seus componentes: 

* Front-end
* Regras
* Definição do pool de back-end

No contexto de zonas de disponibilidade, o comportamento e as propriedades de uma regra de balanceador de carga são descritos como com redundância de zona ou zonas.  No contexto do balanceador de carga, o redundância de zona sempre significa **várias zonas** e zonal significa isolar o serviço em uma **única zona**. Um Azure Load Balancer tem dois tipos, público e interno. Os dois tipos de balanceador de carga dão suporte à redundância de zona e à implantação de zonas.  Ambos os tipos de balanceador de carga podem direcionar o tráfego entre as zonas, conforme necessário.

## <a name="frontend"></a>Front-end

Um front-end do balanceador de carga é uma configuração de IP de front-end que referencia um endereço IP público ou um endereço IP privado dentro da sub-rede de uma rede virtual. Ele forma o ponto de extremidade com balanceamento de carga onde o serviço está exposto.

Um recurso de balanceador de carga pode conter regras com front-ends zonais e com redundância de zonas simultaneamente. Quando um IP público ou privado é garantido para uma zona, o zonalidade (ou a falta dele) não é alterável. Para alterar ou omitir o zonalidade de um front-end de endereço IP público ou privado, recrie o IP na zona apropriada. 

As zonas de disponibilidade não alteram as restrições para vários front-ends. Examine [vários front-ends para obter Load Balancer](load-balancer-multivip-overview.md) para obter detalhes sobre essa capacidade.

### <a name="zone-redundant"></a>Redundância de zona 

Em uma região com zonas de disponibilidade, um front-end do balanceador de carga padrão pode ser com redundância de zona. Várias zonas podem servir de entrada ou de saída em uma região. Esse tráfego é servido por um único endereço IP. Esquemas de redundância de DNS não são necessários. 

Um único endereço IP de front-end sobreviver A falhas de zona. O IP de front-end pode ser usado para alcançar todos os membros do pool de back-end (não impactados), independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobreviver, desde que uma zona na região permaneça íntegra. 

O endereço IP do front-end é servido simultaneamente por várias implantações de infraestrutura independentes em várias zonas de disponibilidade. Qualquer nova tentativa ou restabelecimento terá êxito em outras zonas não afetadas pela falha de zona. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="NAT de Rede Virtual">
</p>

*Figura: balanceador de carga com redundância de zona*

### <a name="zonal"></a>Zonal

Você pode optar por ter um front-end garantido para uma única zona, conhecido como um *front-end zonal*.  Esse cenário significa que qualquer fluxo de entrada ou saída é servido por uma única zona em uma região.  O front-end compartilha o destino com a integridade da zona.  O caminho de dados não é afetado por falhas em outras zonas além de onde foi garantido. É possível utilizar front-ends zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, há suporte para o uso de front-ends zonais diretamente para pontos de extremidade com balanceamento de carga em cada zona. Você pode usar essa configuração para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona. Para pontos de extremidade públicos, você pode integrá-los a um produto de balanceamento de carga de DNS como o [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="NAT de Rede Virtual">
</p>

*Figura: balanceador de carga zonal*

Se você deseja mesclar esses conceitos (zonal e com redundância de zona para o mesmo back-end), analise [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um front-end público do Load Balancer, você adiciona um parâmetro de **zonas** ao IP público. Esse IP público é referenciado pela configuração de IP de front-end usada pela respectiva regra.

Para um front-end do balanceador de carga interno, adicione um parâmetro de **zonas** à configuração de IP de front-end do balanceador de carga interno. Um front-end zonal garante um endereço IP em uma sub-rede para uma zona específica.

## <a name="backend"></a>Back-end

Azure Load Balancer funciona com instâncias de máquina virtual. Essas instâncias podem ser autônomas, conjuntos de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais. Qualquer máquina virtual em uma única rede virtual pode fazer parte do pool de back-end, qualquer zona à qual a máquina virtual esteja garantida.

Para alinhar e garantir o frontend e o back-end com uma única zona, só coloque as máquinas virtuais na mesma zona no pool de back-end correspondente.

Para tratar máquinas virtuais em várias zonas, coloque máquinas virtuais de várias zonas no mesmo pool de back-end. 

Ao usar conjuntos de dimensionamento de máquinas virtuais, coloque um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end. Os conjuntos de dimensionamento podem estar em zonas únicas ou múltiplas.

## <a name="outbound-connections"></a>Conexões de saída

Com redundância de zona e zonas se aplicam a [conexões de saída](load-balancer-outbound-connections.md). Um endereço IP público com redundância de zona usado para conexões de saída é servido por todas as zonas. Um endereço IP público zonal é servido somente pela zona em que ele está garantido. 

As alocações de porta SNAT de conexão de saída sobrevivem a falhas de zona e seu cenário continuará a fornecer conectividade SNAT de saída se não for afetado por falha de zona. As falhas de zona podem exigir que as conexões sejam restabelecidas para cenários com redundância de zona se o tráfego foi servido por uma zona afetada. Fluxos em zonas diferentes das zonas afetadas não são afetados.

O algoritmo de prealocação de porta SNAT é o mesmo com ou sem zonas de disponibilidade.

## <a name="health-probes"></a>Investigações de integridade

Suas definições de investigação de integridade existentes permanecem como estão sem zonas de disponibilidade. No entanto, expandimos o modelo de integridade em um nível de infraestrutura. 

Ao usar front-ends com redundância de zona, o balanceador de carga expande sua verificação de integridade interna. O balanceador de carga investiga de forma independente a disponibilidade de uma máquina virtual de cada zona e desliga os caminhos entre as zonas que falharam sem intervenção.  

Outras zonas que podem alcançar essa VM podem continuar a atender a VM dos respectivos front-ends. Durante os eventos de falha, cada zona pode ter diferentes distribuições de novos fluxos e, ao mesmo tempo, proteger a integridade geral do seu serviço.

## <a name="design-considerations"></a><a name="design"></a> Considerações de design

O balanceador de carga é flexível no contexto de zonas de disponibilidade. Você pode optar por se alinhar às zonas ou ser com redundância de zona para cada regra. A maior disponibilidade pode chegar ao preço de maior complexidade. Design para disponibilidade para desempenho ideal.

### <a name="zone-redundancy"></a>Redundância de zona

O Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode atender a um recurso de zona em qualquer região.  O IP pode sobreviver a uma ou mais falhas de zona, contanto que uma zona permaneça íntegra na região.  Em vez disso, um front-end zonal é uma redução do serviço para uma única zona e compartilha destino com a respectiva zona.

A redundância de zona não implica em implica ou plano de controle de caminho; é plano de dados. Fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. Em uma falha de zona, os fluxos de tráfego usando zonas íntegras não são afetados. 

Os fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem ser recuperados. O tráfego continua nas zonas íntegras dentro da região após a retransmissão quando o Azure convergiu em volta da falha de zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Limites entre zonas

É importante entender que sempre que um serviço cruza zonas, você compartilha destino com uma zona, mas possivelmente com várias zonas. Como resultado, o serviço pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite introduzir dependências entre zonas não pretendidas, o que anulará os ganhos de disponibilidade ao usar zonas de disponibilidade. Se seu aplicativo contiver vários componentes críticos, certifique-se de que a sobrevivência se uma zona falhar.

Um único componente crítico para seu aplicativo pode afetar todo o seu aplicativo se ele só existir em uma zona diferente da (s) zona (ões) sobrevivente. Considere a restauração de zona e como seu aplicativo convergirá. Entenda como seu aplicativo responde a falhas de partes dele. Revise os principais pontos e use-os para perguntas como você imagina em seu cenário específico.

- Se seu aplicativo tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerenciado

Eles são configurados na zona 1 e na zona 2. Quando a zona 1 falha, o serviço não sobreviver. Não entre zonas com cenários de zona, a menos que você entenda totalmente que está criando um modo de falha potencialmente nocivo. Esse cenário tem permissão para fornecer flexibilidade.

- Se seu aplicativo tiver dois componentes:

    * Endereço IP
    * Máquina virtual com disco gerenciado

Eles estão configurados para serem com redundância de zona e zona 1. Seu serviço sobreviver a falhas de zona da zona 2, zona 3 ou ambas, a menos que a zona 1 tenha falhado. No entanto, você perde alguma capacidade de motivo da integridade do seu serviço se tudo o que você está observando é a acessibilidade do front-end.  Considere desenvolver um modelo de capacidade e integridade mais abrangente.  Você pode usar os conceitos com redundância de zona e zonal juntos para expandir a análise e a capacidade de gerenciamento.

- Se seu aplicativo tiver dois componentes:

    * Front-end do balanceador de carga com redundância de zona
    * Conjunto de dimensionamento de máquinas virtuais entre zonas em três zonas

Seus recursos em zonas não afetadas pela falha estarão disponíveis. A capacidade do serviço pode ser degradada durante a falha. Do ponto de vista da infra-estrutura, sua implantação pode sobreviver a uma ou mais falhas de zona. Este cenário gera as seguintes perguntas:

  - Você entende como seu aplicativo responde às falhas e à capacidade degradada?
  - Você precisará de garantias no serviço para forçar um failover em um par de regiões, se necessário?
  - Como você monitorará, detectará e mitigará esse cenário? Você pode usar o diagnóstico de balanceador de carga padrão para aumentar o monitoramento do desempenho do serviço. Considere o que está disponível e o que pode precisar de aumento.

- Zonas podem tornar falhas mais fáceis de serem compreendidas e contidas. A falha de zona não é diferente de outras falhas quando se trata de tempos limite, repetições e algoritmos de retirada. Azure Load Balancer fornece caminhos com redundância de zona. O balanceador de carga tenta recuperar-se rapidamente, em um nível de pacote em tempo real. Retransmissores ou reestabelecimentos podem ocorrer durante o início de uma falha. É importante entender como seu aplicativo lida com falhas. Seu esquema de balanceamento de carga sobreviverá, mas você precisará planejar as seguintes perguntas:

  - Quando uma zona falha, o serviço entende essa falha e, se o estado for perdido, como você recuperará?
  - Quando uma zona retorna, o aplicativo é capaz de convergir com segurança?

Examine os [padrões de design de nuvem do Azure](/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Standard Load Balancer](./load-balancer-overview.md)
- Saiba como [balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](./quickstart-load-balancer-standard-public-cli.md)
- Saiba como [balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](./quickstart-load-balancer-standard-public-cli.md)
- Saiba mais sobre os [padrões de design de nuvem do Azure](/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.