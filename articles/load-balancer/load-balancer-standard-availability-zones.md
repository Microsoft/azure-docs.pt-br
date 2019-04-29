---
title: Standard Load Balancer e Zonas de Disponibilidade do Azure
titlesuffix: Azure Load Balancer
description: Load Balancer Standard e Zonas de Disponibilidade
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: kumud
ms.openlocfilehash: 6f33be6e418366f57d243f578035b5c87079c99e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734449"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e Zonas de Disponibilidade

O SKU do Azure Load Balancer Standard fornece suporte para cenários de [Zonas de Disponibilidade](../availability-zones/az-overview.md). Vários novos conceitos estão disponíveis com o Standard Load Balancer, que permite otimizar a disponibilidade em seu cenário de ponta a ponta, alinhando os recursos com as zonas e distribuindo-as entre as zonas.  Analise as [Zonas de Disponibilidades](../availability-zones/az-overview.md) para obter diretrizes sobre o que são Zonas de Disponibilidade, quais regiões atualmente fornecem suporte para Zonas de Disponibilidade, e outros conceitos e produtos relacionados. As Zonas de Disponibilidade, em combinação com o Standard Load Balancer, são um conjunto de recursos expansíveis e flexíveis que podem criar muitos cenários diferentes.  Analise este documento para entender esses [conceitos](#concepts) e as [diretrizes de design](#design) de cenário fundamentais.

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

## <a name="concepts"></a> Conceitos de Zonas de Disponibilidade aplicados ao Load Balancer

Não há relação direta entre os recursos do balanceador de carga e a infraestrutura real. A criação de um balanceador de carga não cria uma instância. Os recursos do Load Balancer são objetos nos quais é possível expressar como o Azure deve programar sua infraestrutura multilocatária pré-compilada para alcançar o cenário que você deseja criar.  Isso é significativo no contexto das Zonas de Disponibilidade porque um único recurso do Load Balancer pode controlar a programação da infraestrutura em várias Zonas de Disponibilidade, enquanto um serviço com redundância de zona aparece como um recurso do ponto de vista do cliente.

As funções de um recurso do Load Balancer são expressas como um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end.

No contexto das Zonas de Disponibilidade, o comportamento e as propriedades de um recurso do Load Balancer são descritos como zonal ou com redundância de zona.  Zonal ou com redundância de zona descreve a zonalidade de uma propriedade.  No contexto do balanceador de carga, com redundância de zona sempre significa *várias zonas* e zonal significa isolando o serviço para um *única zona*.

O balanceador de carga público e o interno são compatíveis com redundância de zona e com os cenários zonais, e ambos podem direcionar o tráfego entre as zonas, conforme necessário (*balanceamento de carga entre zonas*).

Um recurso do Load Balancer em si é regional e nunca zonal.  E uma VNet e sub-rede são sempre regionais e nunca zonais.

### <a name="frontend"></a>Front-end

Um front-end do Load Balancer é uma configuração de IP de front-end que faz referência a um recurso de endereço IP público ou a um endereço IP privado na sub-rede de um recurso de rede virtual.  Ele forma o ponto de extremidade com balanceamento de carga onde o serviço está exposto.

Um recurso do Load Balancer pode conter front-ends zonais e com redundância de zona simultaneamente. 

Quando um recurso IP público for garantido para uma zona, a zonalidade (ou a falta dela) não será mutável.  Se você deseja alterar ou omitir a zonalidade de um front-end de IP público, é necessário recriar o IP público na zona apropriada.  

É possível alterar a zonalidade de um front-end de um Load Balancer interno, removendo e recriando o front-end, alterando ou omitindo a zonalidade.

Ao utilizar vários front-ends, analise [vários front-ends para Load Balancer](load-balancer-multivip-overview.md) para considerações importantes.

#### <a name="zone-redundant-by-default"></a>Com redundância de zona por padrão

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

Em uma região com Zonas de Disponibilidade, um front-end do Load Balancer Standard é, por padrão, com redundância de zona.  Um único endereço IP de front-end pode sobreviver à falha da zona e ser utilizado para alcançar todos os membros do pool de back-end, independentemente da zona. Isso não significa um caminho de dados sem ocorrência, mas indica que qualquer nova tentativa ou restabelecimento terá êxito. Esquemas de redundância de DNS não são necessários. O único endereço IP de front-end é atendido simultaneamente por várias implantações de infraestrutura independentes em diversas Zonas de Disponibilidade.  A redundância de zona indica que todos os fluxos de entrada ou saída são atendidos por várias Zonas de Disponibilidade em uma região, usando simultaneamente um único endereço IP.

Uma ou mais Zonas de Disponibilidade podem falhar e o caminho de dados sobreviver enquanto uma zona na região permanecer íntegra. A configuração com redundância de zona é o padrão e não requer ações adicionais.  

Utilize o script a seguir para criar um endereço IP público com redundância de zona para o Load Balancer Standard interno. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o script a seguir para criar um endereço IP público com redundância de zona para o Load Balancer Standard interno. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-isolation"></a>Isolamento de zona opcional

Você pode optar por ter um front-end garantido para uma única zona, conhecido como um *front-end zonal*.  Isso significa que qualquer fluxo de entrada ou saída é atendido por uma única zona em uma região.  O front-end compartilha o destino com a integridade da zona.  O caminho de dados não é afetado por falhas em outras zonas além de onde foi garantido. É possível utilizar front-ends zonais para expor um endereço IP por Zona de Disponibilidade.  Além disso, é possível consumir front-ends zonais diretamente, ou quando o front-end consistir em endereços IP públicos, integrá-los com um produto de balanceamento de carga DNS como o [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS, o qual um cliente resolverá para vários endereços IP zonais.  Você também pode usar isso para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona.  Se você deseja mesclar esses conceitos (zonal e com redundância de zona para o mesmo back-end), analise [vários front-ends para Azure Load Balancer](load-balancer-multivip-overview.md).

Para um front-end público do Load Balancer, você inclui um parâmetro *zonas* para o IP público referenciado pela configuração de IP de front-end.  

Para um front-end público do Load Balancer, você inclui um parâmetro *zonas* para o IP público referenciado pela configuração de IP de front-end. O front-end zonal faz com que o Load Balancer garanta um endereço IP em uma sub-rede para uma zona específica.

Utilize o script a seguir para criar um endereço IP Público Standard zonal na Zona de Disponibilidade 1. Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o script a seguir para criar um front-end do Standard Load Balancer na Zona de Disponibilidade 1.

Se você estiver usando modelos do Resource Manager existentes em sua configuração, adicione a seção **sku** a esses modelos. Além disso, defina a propriedade **zonas** na configuração de IP de front-end para o recurso filho.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zonas

O balanceamento de carga entre zonas é a capacidade do balanceador de carga de alcançar um ponto de extremidade de back-end em qualquer zona e é independente do front-end e da zonalidade.

Se você deseja alinhar e garantir a implantação dentro de uma única zona, alinhe os recursos de back-end zonal e front-end zonal com a mesma zona. Nenhuma ação adicional é necessária.

### <a name="backend"></a>Back-end

O Load Balancer funciona com Máquinas Virtuais.  Qualquer VM em uma única VNet pode fazer parte do pool de back-end, independentemente de estar ou não garantida para uma zona ou para qual zona estava garantida.

Se você quiser alinhar e garantir o front-end e back-end com uma única zona, coloque apenas as VMs na mesma zona no respectivo pool de back-end.

Se você quiser endereçar VMs em várias zonas, simplesmente coloque VMs de várias zonas no mesmo pool de back-end.  Ao utilizar conjuntos de dimensionamento de máquinas virtuais, é possível colocar um ou mais conjuntos de dimensionamento de máquinas virtuais no mesmo pool de back-end.  E cada um desses conjuntos de dimensionamento de máquinas virtuais pode estar em uma única ou em várias zonas.

### <a name="outbound-connections"></a>Conexões de saída

As [Conexões de saída](load-balancer-outbound-connections.md) são atendidas por todas as zonas e serão automaticamente com redundância de zona em uma região com Zonas de Disponibilidade quando uma máquina virtual for associada a um Load Balance público e a um front-end com redundância de zona.  Alocações de porta SNAT de conexão de saída sobrevivem a falhas de zona.  

Por sua vez, se a VM estiver associada a um Load Balancer Público e a um front-end zonal, as conexões de saída serão garantidas para serem atendidas por uma única zona.  Conexões de saída compartilham destino com a integridade da respectiva região.

O algoritmo e a pré-alocação de porta SNAT são os mesmos com ou sem zonas.

### <a name="health-probes"></a>Investigações de integridade

Suas definições de investigação de integridade existentes permanecem como estão sem Zonas de Disponibilidade.  Mas nós expandimos o modelo de integridade em um nível de infraestrutura. 

Ao usar front-ends com redundância de zona, o Load Balancer expande o modelo de integridade interno para independentemente investigar a acessibilidade de uma VM de cada Zona de Disponibilidade e desligar caminhos em zonas que podem ter falhado sem a intervenção do cliente.  Se um determinado caminho não estiver disponível na infraestrutura do Load Balancer de uma zona para uma VM em outra zona, o Load Balancer poderá detectar e evitar essa falha. Outras zonas que podem alcançar essa VM podem continuar a atender a VM dos respectivos front-ends.  Como resultado, é possível que durante eventos de falha cada zona possa ter distribuições de fluxo ligeiramente diferentes, ao mesmo tempo em que protege a integridade geral dou serviço de ponta a ponta.

## <a name="design"></a> Considerações sobre o Design

O Load Balancer é propositadamente flexível no contexto de Zonas de Disponibilidade. É possível escolher alinhar a zonas, ou escolher ser com redundância de zona.  O aumento da disponibilidade pode resultar no aumento da complexidade e você deve projetar a disponibilidade para um desempenho ideal.  Vejamos algumas considerações importantes sobre o design.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

O Load Balancer torna simples ter um único IP como um front-end com redundância de zona. Um endereço IP com redundância de zona pode seguramente atender um recurso zonal em qualquer zona e sobreviver a uma ou mais falhas de zona, desde que uma zona permaneça íntegra dentro da região. Por outro lado, um front-end zonal é uma redução do serviço para uma única zona e compartilha o destino com a respectiva zona.

Com redundância de zona não implica plano de controle ou caminho de dados sem ocorrência; é expressamente plano de dados. Fluxos com redundância de zona podem usar qualquer zona e os fluxos de um cliente usarão todas as zonas íntegras em uma região. No caso de falha de zona, os fluxos de tráfego usando zonas íntegras naquele momento não serão afetados.  Fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem recuperar e esses fluxos continuarem nas zonas íntegras remanescentes na região após a retransmissão ou o reestabelecimento, uma vez que o Azure tenha convergido em torno da falha de zona.

### <a name="xzonedesign"></a> Limites entre zonas

É importante entender que sempre que um serviço de ponta a ponta cruza as zonas, você compartilha o destino não com uma zona, mas potencialmente várias zonas.  Como resultado, o serviço de ponta a ponta pode não ter obtido nenhuma disponibilidade em implantações não zonais.

Evite apresentar dependências não intencionais entre zonas que anularão os ganhos de disponibilidade ao usar as Zonas de Disponibilidade.  Quando o aplicativo consiste em vários componentes e você deseja ser resiliente a falhas de zona, é necessário ter cuidado para garantir a sobrevivência de componentes críticos suficientes no caso de falha de uma zona.  Por exemplo, um único componente crítico para o aplicativo pode afetar todo o aplicativo se ele existir em uma zona diferente da(s) zona(s) sobrevivente(s).  Além disso, considere também a restauração de zona e como o aplicativo irá convergir. Vamos revisar alguns pontos principais e utilizá-los como inspiração para perguntas ao refletir sobre um cenário específico.

- Se o aplicativo tiver dois componentes, como um endereço IP e uma VM com disco gerenciado, e eles são garantidos na zona 1 e zona 2, quando a zona 1 falhar o serviço de ponta a ponta não sobreviverá quando a zona 1 falha.  Não cruze zonas, a menos que você entenda completamente que está criando um modo de falha potencialmente perigoso.

- Se o aplicativo tiver dois componentes, como um endereço IP e uma VM com disco gerenciado, e eles são garantidos para serem com redundância de zona e zona 1 respectivamente, o serviço de ponta a ponta sobreviverá à falha de zona da zona 2, zona 3, ou ambas, a menos que a zona 1 tenha falhado.  No entanto, alguma capacidade de avaliar sobre a integridade do serviço será perdida, se tudo o que estiver observando for a acessibilidade do front-end.  Considere desenvolver um modelo de capacidade e integridade mais abrangente.  Você pode usar os conceitos com redundância de zona e zonal juntos para expandir a análise e a capacidade de gerenciamento.

- Se o aplicativo tiver dois componentes, como um front-end do balanceador de carga com redundância de zona e um conjunto de dimensionamento de máquinas virtuais entre regiões em três zonas, os recursos nas zonas não afetadas por falha estarão disponíveis, mas a capacidade do serviço de ponta a ponta poderá ser degradada durante a falha de zona. Do ponto de vista de infraestrutura, a implantação pode sobreviver a uma ou mais falhas de zona e isso suscita as seguintes perguntas:
  - É possível entender como o aplicativo avalia essas falhas e a capacidade degradada?
  - Você precisará de garantias no serviço para forçar um failover em um par de regiões, se necessário?
  - Como você monitorará, detectará e mitigará esse cenário? É possível utilizar os diagnósticos do Load Balancer Standard para aumentar o monitoramento do desempenho do serviço de ponta a ponta. Considere o que está disponível e o que pode ser necessário ampliar para uma imagem completa.

- Zonas podem tornar falhas mais fáceis de serem compreendidas e contidas.  No entanto, a falha de zona não é diferente de outras falhas quando se trata de conceitos como tempos limites, repetições e algoritmos de retirada. Mesmo que o Azure Load Balancer forneça caminhos com redundância de zona e tente recuperá-los rapidamente, em um nível de pacote em tempo real, retransmissões ou reestabelecimentos poderão ocorrer durante o início de uma falha e é importante entender como o aplicativo lida com falhas. O esquema de balanceamento de carga será mantido, mas será necessário planejar o seguinte:
  - Quando uma zona falha, o serviço de ponta a ponta entende isso e, se o estado for perdido, como você irá recuperar?
  - Quando uma zona retorna, o aplicativo é capaz de convergir com segurança?

### <a name="zonalityguidance"></a> Com redundância de zona versus zonal

>[!IMPORTANT]
>Revisão [zonas de disponibilidade](../availability-zones/az-overview.md) para tópicos relacionados, incluindo quaisquer informações específicas da região.

Com redundância de zona pode fornecer uma zona independente e ao mesmo tempo opção resiliente com um único endereço IP para o serviço.  Por sua vez, pode reduzir a complexidade.  Com redundância de zona também tem mobilidade entre zonas e pode ser utilizada com segurança em recursos em qualquer zona.  Além disso, é resistente ao tempo em regiões sem Zonas de Disponibilidade, o que pode limitar alterações necessárias quando uma região ganha Zonas de Disponibilidade.  A sintaxe de configuração para um endereço IP ou front-end com redundância de zona tem êxito em qualquer região, incluindo aquelas sem Zonas de Disponibilidade.

Zonal pode fornecer uma garantia explícita para uma zona, compartilhando o destino com a integridade da zona. A associação de um endereço IP zonal ou front-end do Load Balancer zonal pode ser um atributo desejável ou razoável, especialmente se o recurso anexado for uma VM zonal na mesma zona.  Ou talvez o aplicativo exija conhecimento explícito sobre qual zona um recurso está localizado e você deseja avaliar sobre a disponibilidade em zonas separadas explicitamente.  É possível escolher expor vários front-ends zonais para um serviço de ponta a ponta distribuído entre zonas (ou seja, front-ends zonais por zona para vários conjuntos de dimensionamento de máquinas virtuais zonais).  E se os front-ends zonais forem endereços IP públicos, será possível utilizar esses vários front-ends zonais para expor o serviço com o [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).  Ou poderá utilizar vários front-ends zonais para obter análises de desempenho e integridade por zona por meio de soluções de monitoramento de terceiros e expor o serviço geral com um front-end com redundância de zona. Você deve atender apenas recursos zonais com front-ends zonais alinhados à mesma zona e evitar cenários entre zonas potencialmente prejudiciais para recursos zonais.  Somente existem recurso zonais em regiões onde há Zonas de Disponibilidade.

Não há nenhuma diretriz geral informando que uma escolha é melhor do que a outra, sem conhecer a arquitetura do serviço.

## <a name="limitations"></a>Limitações

- Enquanto o plano de dados tiver completamente com redundância de zonas (a menos que a garantia zonal tenha sido especificada), as operações do plano de controle não terão total redundância entre zonas.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md)
- Saiba como [balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](load-balancer-standard-public-zonal-cli.md)
- Saiba como [balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](load-balancer-standard-public-zone-redundant-cli.md)
