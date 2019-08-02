---
title: O que é o Azure Standard Load Balancer?
titlesuffix: Azure Load Balancer
description: Visão geral dos recursos do Azure Load Balancer Standard
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: allensu
ms.openlocfilehash: 8eb8134452685add53b9dc339437ac262ecc8a9f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274391"
---
# <a name="azure-standard-load-balancer-overview"></a>Visão geral do Azure Standard Load Balancer

O Azure Load Balancer permite dimensionar seus aplicativos e criar alta disponibilidade para seus serviços. O Azure Load Balancer pode ser usado tanto como cenários de entrada como de saída e fornece latência baixa, taxa de transferência alta e escala verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP. 

Este artigo discute o Load Balancer Standard.  Para obter uma visão mais geral do Azure Load Balancer, veja também a [Visão Geral do Azure Load Balancer](load-balancer-overview.md).

## <a name="what-is-standard-load-balancer"></a>O que é Load Balancer Standard?

O Load Balancer Standard é um novo produto Balanceador de Carga para aplicativos de todos os TCP e UDP com um recurso expandido e conjunto de recursos com relação ao Load Balancer Base.  Embora haja muitas semelhanças, é importante estar familiarizado com as diferenças, conforme descrito neste artigo.

É possível usar o Load Balancer Standard como um Load Balancer interno ou público. E uma máquina virtual pode ser conectada a um recurso de Balanceador de Carga público e um interno.

As funções de recurso do Balanceador de Carga são expressas como um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end.  Um recurso pode conter várias regras. Você pode colocar as máquinas virtuais no pool de back-end, especificando o pool de back-end do recurso NIC da máquina virtual.  Este parâmetro é passado por meio do perfil de rede e expandido ao usar Conjuntos de Dimensionamento de Máquinas Virtuais.

Um aspecto importante é o escopo da rede virtual para o recurso.  Embora exista o Load Balancer Basic dentro do escopo de um conjunto de disponibilidade, um Standard Load Balancer está totalmente integrado com o escopo de uma rede virtual e todos os conceitos de rede virtual se aplicam.

Os recursos do Load Balancer são objetos nos quais é possível expressar como o Azure deve programar sua infraestrutura multilocatária para alcançar o cenário que você deseja criar.  Não há relação direta entre os recursos do Load Balancer e a infraestrutura real. A criação de um balanceador de carga não cria uma instância, a capacidade está sempre disponível e não há inicialização ou atrasos de escalonamento para considerar. 

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de cargo para os seus cenários.  Se você estiver procurando por encerramento de TLS ("descarregamento de SSL") ou por processamento de camada de aplicativo de solicitação HTTP/HTTPS, revise [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md).  Se você estiver procurando por balanceamento de carga DNS global, veja [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).  Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções, conforme necessário.

## <a name="why-use-standard-load-balancer"></a>Por que usar o Load Balancer Standard?

O Load Balancer Standard permite que você dimensione os aplicativos e crie alta disponibilidade para implantações em escala pequena para arquiteturas de várias zonas complexas e grandes.

Revise a tabela abaixo para obter uma visão geral das diferenças entre o Load Balancer Standard e Load Balancer Load:

>[!NOTE]
> Novos designs devem adotar o Standard Load Balancer. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Revisar [limites de serviço para o Balanceador de Carga](https://aka.ms/lblimits), bem como [preços](https://aka.ms/lbpricing), e [SLA](https://aka.ms/lbsla).


### <a name="backend"></a>Pool de back-end

Os pools de back-end do Standard Load Balancer expandem para qualquer recurso de máquina virtual em uma rede virtual.  Pode conter até 1000 instâncias de back-end.  Uma instância de back-end é uma configuração de IP, que é uma propriedade de um recurso NIC.

O pool de back-end pode conter as máquinas virtuais autônomas, conjuntos de disponibilidade ou conjuntos de escala de máquina virtual.  Você também pode combinar recursos no pool de back-end. Você pode combinar até 150 recursos no pool de back-end por recurso do Load Balancer.

Ao considerar como projetar seu pool de back-end, você pode projetar o menor número de recursos do pool de back-end individuais para otimizar ainda mais a duração de operações de gerenciamento.  Não há nenhuma diferença no desempenho do plano de dados ou escala.

### <a name="probes"></a>Investigações de integridade
  
O Standard Load Balancer adiciona suporte para as [investigações de integridade HTTPS](load-balancer-custom-probe-overview.md#httpprobe) (investigação HTTP com wrapper de TLS) para monitorar precisamente seus aplicativos HTTPS.  

Além disso, quando todo o pool de back-end [investiga](load-balancer-custom-probe-overview.md#probedown), o Standard Load Balancer permite que todas as conexões TCP estabelecidas continuem. (o Azure Load Balancer Básico encerrará todas as conexões TCP para todas as instâncias).

Revise as [investigações de integridade do Azure Load Balancer](load-balancer-custom-probe-overview.md) para obter detalhes.

### <a name="az"></a> Zonas de Disponibilidades

>[!IMPORTANT]
>Examine [zonas de disponibilidade](../availability-zones/az-overview.md) para obter tópicos relacionados, incluindo qualquer informação específica da região.

O Load Balancer Standard oferece suporte a recursos adicionais em regiões em que as Zonas de Disponibilidade estão disponíveis.  Esses recursos são incrementais aos fornecimentos do Load Balancer Standard.  As configurações de Zonas de Disponibilidade estão disponíveis para o Load Balancer Standard interno.

Front-ends não zonais tornam-se redundantes de zona por padrão quando implantados em uma região com Zonas de Disponibilidade.   Um front-end com redundância de zona sobrevive a falha da zona e é fornecido por uma infraestrutura dedicada em todas as zonas simultaneamente. 

Além disso, você pode garantir um front-end para uma zona específica. Um front-end zonal compartilha o destino com a zona respectiva e é fornecido apenas por uma infraestrutura dedicada em uma única zona.

Balanceamento de carga entre zona está disponível para o pool de back-end, e qualquer recurso de máquina virtual em uma vnet pode ser parte de um pool de back-end.

Revise a [discussão detalhada das habilidades relacionadas às Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnóstico

O Load Balancer Standard fornece métricas multidimensionais por meio do Azure Monitor.  Essas métricas podem ser filtradas, agrupadas e divididas para uma determinada dimensão.  Elas fornecem informações atuais e históricas de desempenho e integridade do seu serviço.  Também há suporte para a Integridade de Recursos.  A seguir está uma visão geral de diagnóstico com suporte:

| Métrica | DESCRIÇÃO |
| --- | --- |
| Disponibilidade do VIP | O Standard Load Balancer usa continuamente o caminho de dados de dentro de uma região para o front-end do Load Balancer e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados usado por seus clientes também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.|
| Disponibilidade do DIP | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância individual no pool do Load Balancer.  Você pode verificar como o Load Balancer vê a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade.
| Pacotes SYN | O Load Balancer Standard não encerra conexões TCP nem interage com os fluxos de pacotes TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.|
| Conexões SNAT | O Standard Load Balancer relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa.  Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.|
| Contadores de bytes | O Standard Load Balancer informa os dados processados por front-end.|
| Contadores de pacotes | O Standard Load Balancer relata os pacotes processados por front-end.|

Revisar [a discussão detalhada do Diagnóstico do Standard Load Balancer](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Portas de alta disponibilidade

O Load Balancer Standard oferece suporte a um novo tipo de regra.  

É possível configurar as regras de balanceamento de carga para dimensionar seu aplicativo e ser altamente confiável. Ao usar uma regra de balanceamento de carga de portas de alta disponibilidade, o Standard Load Balancer fornecerá de acordo com o balanceamento de carga de fluxo em cada porta efêmera de um endereço IP de front-end do Standard Load Balancer.  O recurso é útil para outros cenários em que é impraticável ou indesejável especificar portas individuais.

Uma regra de balanceamento de carga de portas de alta disponibilidade permite que você cria cenários ativo-passivo ou ativo-ativo n+1 para Dispositivos Virtuais de Rede e qualquer aplicativo que requer grandes intervalos de portas de entrada.  Uma investigação íntegra pode ser usada para determinar quais back-ends devem receber novos fluxos.  Você pode usar um Grupo de Segurança de Rede para emular um cenário de intervalo de porta.

>[!IMPORTANT]
> Se você estiver planejando usar um Dispositivo de Rede Virtual, entre em contato com seu fornecedor para obter orientação se o produto foi testado com portas de alta disponibilidade e siga suas diretrizes específicas para implementação. 

Revisão [da discussão detalhada de Portas de alta disponibilidade](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Segurança por padrão

O Load Balancer Standard é totalmente integrado à rede virtual.  A rede virtual é uma rede privada, fechada.  Como os Load Balancers Standard e os endereços de IP público são projetados para permitir que essa rede virtual seja acessada de fora da rede virtual, esses recursos agora são padrão para fechado, a menos que você os abra. Isso significa que os NSGs (Grupos de Segurança de Rede) são usados para permitir explicitamente e tráfego permitido de lista de permissões.  Você pode criar seu data center virtual inteiro e decidir por meio do NSG o que e quando deve estar disponível.  Se você não tiver um NSG em uma sub-rede ou NIC de seu recurso de máquina virtual, o tráfego não terá permissão para acessar o recurso.

Para saber mais sobre NSGs e como aplicá-los para seu cenário, consulte [Grupos de segurança de rede no Azure](../virtual-network/security-overview.md).

### <a name="outbound"></a>Conexões de saída

O Load Balancer oferece suporte a cenários de entrada e saídas.  O Standard Load Balancer é significativamente diferente do Basic Load Balancer em relação a conexões de saída.

A Tradução de Endereço de Rede da Fonte (SNAT) é usada para mapear endereços de IP internos de mapa, particular em sua rede virtual para endereços de IP nos front-ends no Load Balancer.

O Load Balancer Standard apresenta um novo algoritmo para um [algoritmo SNAT mais robusto, dimensionável e previsível](load-balancer-outbound-connections.md#snat) e permite novas habilidades, remove ambiguidade e força configurações explícita, em vez de efeitos colaterais. Essas alterações são necessárias para permitir que novos recursos surjam. 

Estes são os principais princípios a serem lembrados ao trabalhar com o Load Balancer Standard:

- a conclusão de uma regra de unidades do recurso de Balanceador de Carga.  toda programação do Azure deriva de sua configuração.
- quando vários front-ends estiverem disponíveis, todos os front-ends são usados e cada front-end multiplica o número de portas SNAT disponíveis
- você pode escolher e controlar se não desejar que um front-end específico seja usado para conexões de saída.
- os cenários de saída são explícitos e a conectividade de saída não existe até que seja especificado.
- as regras de balanceamento de carga inferem em como a SNAT é programada. As regras de balanceamento de carga são um protocolo específico. SNAT é específica do protocolo e a configuração deve refletir isso em vez de criar um efeito colateral.

#### <a name="multiple-frontends"></a>Vários front-ends
Se você quiser mais portas SNAT porque você está esperando ou já está tendo uma alta demanda para conexões de saída, também pode adicionar o inventário de porta SNAT incremental ao configurar front-ends adicionais, regras e pools de back-end para os mesmos recursos de máquina virtual.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Controle quail front-end é usado para saída
Se desejar restringir conexões de saída para serem originadas somente de um endereço IP de front-end específico, opcionalmente, será possível desabilitar a saída SNAT na regra que expressa o mapeamento de saída.

#### <a name="control-outbound-connectivity"></a>Controle de conectividade de saída
O Standard Load Balancer existe dentro do contexto da rede virtual.  Uma rede virtual é uma rede isolada, privada.  A menos que exista uma associação a um endereço de IP público, a conectividade pública não é permitida.  Você pode acessar os [Pontos de extremidade de Serviço de VNet](../virtual-network/virtual-network-service-endpoints-overview.md) porque estão dentro e o local para sua rede virtual.  Se você deseja estabelecer conectividade de saída para um destino fora da sua rede virtual, você tem duas opções:
- atribuir um endereço de IP público do SKU Standard como um endereço de IP Público de Nível de Instância para o recurso de máquina virtual ou
- colocar o recurso de máquina virtual no pool de back-end de um Load Balancer Standard.

Ambos permitirão a conectividade de saída da rede virtual para fora da rede virtual. 

Se você _somente_ tiver um Load Balancer Standard interno associado ao pool de back-end em que o recurso de máquina virtual está localizado, sua máquina virtual só pode acessar recursos de rede virtual e [Pontos de Extremidade de Serviço VNet](../virtual-network/virtual-network-service-endpoints-overview.md).  Você pode seguir as etapas descritas no parágrafo anterior para criar a conectividade de saída.

Conectividade de saída de um recurso de máquina virtual não associada aos SKUs Standard permanecem como antes.

Revisão [de discussão detalhada de Conexões de Saída](load-balancer-outbound-connections.md).

### <a name="multife"></a>Vários front-ends
Balanceador de Carga dá suporte a várias regras com vários front-ends.  Load Balancer Standard expande para cenários de saída.  Os cenários de saída são essencialmente o inverso de uma regra de balanceamento de carga de entrada.  A regra de balanceamento de carga de entrada também cria um associado para conexões de saída. O Standard Load Balancer usa todos os front-ends associados a um recurso de máquina virtual por meio de uma regra de balanceamento de carga.  Além disso, um parâmetro sobre a regra de balanceamento de carga permite que você suprima uma regra desse tipo para fins de conectividade de saída, o que permite a seleção de front-ends específicos, incluindo nenhum.

Para comparação, o Load Balancer Basic seleciona um único front-end aleatório e não é possível controlar qual foi selecionado.

Revisão [de discussão detalhada de Conexões de Saída](load-balancer-outbound-connections.md).

### <a name="operations"></a>Operações de gerenciamento

Os recursos do Load Balancer Standard existem em uma plataforma de infraestrutura totalmente nova.  Isso permite que as operações de gerenciamento mais rápidas para SKUs Standards e tempos de conclusão sejam geralmente menos de 30 segundos por recursos do SKU Standard.  Conforme os pools de back-end aumentam em tamanho, a duração necessária para mudanças de pool de back-end aumenta.

Você pode modificar os recursos de Load Balancer Standard e mover um endereço de IP público Standard de uma máquina virtual para outra muito mais rápido.

## <a name="migration-between-skus"></a>Migração entre SKUs

Os SKUs não são mutáveis. Siga as etapas nesta seção para mover de um SKU de recurso para outro.

>[!IMPORTANT]
>Revise este documento na íntegra para reconhecer as diferenças entre as SKUs e analise cuidadosamente o seu cenário.  Talvez seja necessário fazer alterações adicionais para alinhar ao seu cenário.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrar do SKU Básico para Standard

1. Crie um novo recurso Standard (Load Balancer e IPs Públicos, conforme necessário). Recrie suas regras e definições de investigação.  Se você estava usando uma investigação de TCP 443/tcp anteriormente, considere alterar este protocolo de investigação para uma investigação HTTPS e adicione um caminho.

2. Crie um novo ou atualize o NSG existente no NIC ou na sub-rede para lista de permissões de tráfego com balanceamento de carga, investigação, assim como qualquer outro tráfego que você deseja permitir.

3. Remova os recursos de SKU Básico (Load Balancer e IPs Públicos, conforme aplicável) de todas as instâncias VM. Certifique-se também de remover todas as instâncias VM de um conjunto de disponibilidade.

4. Anexe todas as instâncias de VM aos novos recursos de SKU Standard.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrar do SKU Standard para o Básico

1. Crie um novo recurso Básico (Load Balancer e IPs Públicos, conforme necessário). Recrie suas regras e definições de investigação.  Altere uma investigação HTTPS para uma investigação de TCP 443/tcp. 

2. Remova os recursos de SKU Standard (Load Balancer e IPs Públicos, conforme aplicável) de todas as instâncias VM. Certifique-se também de remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias de VM aos novos recursos de SKU Básico.

>[!IMPORTANT]
>
>Há limitações com relação ao uso dos SKUs Básico e Standard.
>
>As Portas HA e o Diagnóstico do SKU Standard só estão disponíveis no SKU Standard. Não é possível migrar do SKU Standard para o SKU Básico e também manter esses recursos.
>
>Tanto o SKU Basic como o SKU têm um número de diferenças conforme definido neste artigo.  Certifique-se de entender e prepará-los.
>
>SKUs correspondentes devem ser usados para os recursos do Load Balancer e do IP Público. Não é possível ter uma combinação de recursos de SKU Básico e de SKU Standard. Você não pode anexar as máquinas virtuais autônomas, máquinas virtuais em um recurso de conjunto de disponibilidade, ou recursos de conjunto de dimensionamento de máquina virtual para os SKUs simultaneamente.

## <a name="region-availability"></a>Disponibilidade de região

O Standard Load Balancer está disponível atualmente em todas as regiões de nuvem pública.

## <a name="sla"></a>Contrato de Nível de Serviço

Os Load Balancers Standard estão disponíveis com SLA de 99,99%.  Revise o [SLA do Load Balancer Standard](https://aka.ms/lbsla) para obter detalhes.

## <a name="pricing"></a>Preços

O uso do Standard Load Balancer é cobrado.

- Número de regras de saída e de balanceamento de carga configuradas (as regras NAT de entrada não são consideradas no número total de regras)
- Quantidade de dados processados na entrada e na saída, independentemente da regra. 

Para obter informações sobre os preços do Load Balancer Standard, visite a página [Preços do Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitações

- Os SKUs não são mutáveis. Você não pode alterar a SKU de um recurso existente.
- Um recurso de máquina virtual padrão, recurso de conjunto de disponibilidade ou recurso de conjunto de dimensionamento da máquina virtal podem referenciar um SKU, nunca ambos.
- Uma regra de Balanceador de Carga não pode abranger duas redes virtuais.  Front-ends e suas instâncias de back-end relacionadas devem estar localizados na mesma rede virtual.  
- [Mover as operações de assinatura](../azure-resource-manager/resource-group-move-resources.md) não têm suporte para recursos de PIP e LB SKU Standard.
- Funções de trabalho sem uma rede virtual e outros serviços da plataforma Microsoft podem ser acessados quando apenas um Standard Load Balancer interno é usado devido a um efeito colateral de como os serviços pré-VNet e outros serviços da plataforma funcionam. Não confie nisso como o respectivo serviço em si ou a plataforma subjacente a pode ser alterada sem aviso prévio. Você sempre deve supor que precisa criar [conectividade de saída](load-balancer-outbound-connections.md) explicitamente se desejado ao usar um Standard Load Balancer interno apenas.
- O Load Balancer é um produto de TCP ou UDP para balanceamento de carga e encaminhamento de porta para esses protocolos IP específicos.  As regras de balanceamento de carga e as regras de NAT de entrada são compatíveis com TCP e UDP, mas não com outros protocolos IP, incluindo ICMP. O Load Balancer não encerra, responde ou, de outra forma, interage com a carga de um fluxo UDP ou TCP. Ele não é um proxy. A validação bem-sucedida da conectividade com um front-end deve ocorrer na banda, com o mesmo protocolo usado em uma regra de NAT de entrada ou de balanceamento de carga (TCP ou UDP) _e_ pelo menos uma de suas máquinas virtuais deve gerar uma resposta para um cliente para ver uma resposta de um front-end.  Não receber uma resposta na banda do front-end do balanceador de carga indica que nenhuma máquina virtual foi capaz de responder.  Não é possível interagir com um front-end do Load Balancer sem que uma máquina virtual possa responder.  Isso também se aplica a conexões de saída em que o [SNAT de representação de porta](load-balancer-outbound-connections.md#snat) é compatível apenas com TCP e UDP; outros protocolos IP, incluindo ICMP, também falharão.  Atribua um endereço IP público em nível da instância para mitigar esse problema.
- Diferente dos balanceadores de carga públicos que fornecem [conexões de saída](load-balancer-outbound-connections.md) durante a transição de endereços IP privados dentro da rede virtual para endereços IP públicos, os balanceadores de carga internos não convertem conexões originárias da saída para o front-end de um balanceador de carga interno, uma vez que ambos estão em um espaço de endereços IP privado.  Isso evita o potencial de esgotamento de SNAT dentro de espaços de endereço IP internos exclusivos, em que a conversão não é necessária.  O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar estabelecer um fluxo para o front-end do balanceador de carga interno no pool em que reside _e_ for mapeado de volta para si mesmo, os dois lados do fluxo não serão correspondentes e o fluxo falhará.  Se o fluxo não tiver sido mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end, ele será bem-sucedido.   Quando o fluxo é mapeado de volta para si mesmo, o fluxo de saída parece ser originado da VM para o front-end e o fluxo de entrada correspondente parece ser originado da VM para si mesmo. Do ponto de vista do SO convidado, as partes de entrada e de saída do mesmo fluxo não são correspondentes dentro da máquina virtual. A pilha TCP não reconhece essas metades do mesmo fluxo como parte do mesmo fluxo, pois a origem e o destino não são correspondentes.  Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo com êxito.  O sintoma desse cenário são tempos limite de conexão intermitentes. Há várias soluções alternativas comuns para alcançar esse cenário de forma confiável (originar fluxos de um pool de back-end para seu respectivo front-end interno do Load Balancer), que incluem a inserção de um proxy de terceiros por trás do Load Balancer interno ou o [uso de regras de estilo de DSR](load-balancer-multivip-overview.md).  Embora você possa usar um balanceador de carga público para mitigar esse problema, o cenário resultante será propenso ao [esgotamento de SNAT](load-balancer-outbound-connections.md#snat) e deverá ser evitado, a menos que seja gerenciado atentamente.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre usar o [Standard Load Balancer e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre [Investigações de Integridade](load-balancer-custom-probe-overview.md).
- Saiba mais sobre as [Zonas de disponibilidade](../availability-zones/az-overview.md).
- Saiba mais sobre o [Diagnóstico do Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [as métricas multidimensionais suportadas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) para diagnósticos no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Saiba mais sobre como usar o [Azure Load Balancer para conexões de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre as [Regras de saída](load-balancer-outbound-rules-overview.md).
- Saiba mais sobre a [Redefinição de TCP quando ocioso](load-balancer-tcp-reset.md).
- Saiba mais sobre [o Standard Load Balancer com as regras de balanceamento das Portas de Alta Disponibilidade](load-balancer-ha-ports-overview.md).
- Saiba mais sobre como usar o [Azure Load Balancer com vários Front-ends](load-balancer-multivip-overview.md).
- Saiba mais sobre as [Redes Virtuais](../virtual-network/virtual-networks-overview.md).
- Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
- Saiba mais sobre [Pontos de Extremidade de Serviço de VNET](../virtual-network/virtual-network-service-endpoints-overview.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
