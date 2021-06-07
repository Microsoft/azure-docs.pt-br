---
title: Criando soluções resilientes com a proteção contra DDoS do Azure
description: Saiba mais sobre como você pode usar os dados de log para obter informações detalhadas sobre seu aplicativo.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498924"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Proteção contra DDoS do Azure-criando soluções resilientes

Este artigo é destinado a tomadores de decisões de TI e à equipe de segurança. Espera que você esteja familiarizado com o Azure, rede e segurança.
O DDoS é um tipo de ataque que tenta esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Os ataques estão se tornando cada vez mais sofisticados e maiores tanto em tamanho quanto em impacto. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet. A criação de resiliência para DDoS (ataque de negação de serviço distribuído) requer planejamento e design para uma variedade de modos de falha. O Azure fornece proteção contínua contra ataques de DDoS. Essa proteção é integrada à plataforma do Azure por padrão e sem custos extras.

Além da proteção contra DDoS principal na plataforma, o [Padrão de Proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/) fornece recursos avançados de mitigação de DDoS contra ataques de rede. Se ajusta automaticamente para proteger os recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de novas redes virtuais. Isso também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou recurso.

![A função da Proteção contra DDoS do Azure na proteção de clientes e uma rede virtual de um invasor](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Práticas recomendadas fundamentais

A seção a seguir fornece diretrizes descritivas para criar serviços resilientes contra DDoS no Azure.

### <a name="design-for-security"></a>Design para segurança

Garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitem que um volume relativamente baixo de solicitações use uma quantidade excessiva de recursos, resultando em uma interrupção de serviço. 

Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](/azure/architecture/guide/pillars).
Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

### <a name="design-for-scalability"></a>Design para escalabilidade

A escalabilidade é como um sistema pode tratar aumentos de carga. projetar seus aplicativos para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.

Para [Azure app serviço](../../app-service/overview.md), selecione um [plano do serviço de aplicativo](../../app-service/overview-hosting-plans.md) que ofereça várias instâncias. Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md). Para [Máquinas Virtuais do Azure](../../virtual-machines/index.yml), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../../virtual-machines/windows/tutorial-availability-sets.md). É recomendável usar [conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/overview.md) para recursos de dimensionamento automático.

### <a name="defense-in-depth"></a>Proteção completa

A ideia por trás da defesa completa é gerenciar riscos ao usar estratégias de defesa diversificadas. Dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de um ataque ser bem-sucedido. É recomendável que você implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.

Por exemplo, o risco de ataque aumenta conforme o tamanho (*área da superfície*) do aplicativo. Você pode reduzir a área da superfície usando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de escuta que não são necessárias nos balanceadores de carga ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [aplicativo Azure gateway](../../application-gateway/application-gateway-create-probe-portal.md)). [NSGs (grupos de segurança de rede)](../../virtual-network/network-security-groups-overview.md) são outra maneira de reduzir a superfície de ataque.
Você pode usar [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) e [grupos de segurança de aplicativo](../../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão. Usar [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) alterará o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, recomendamos que você minimize a exposição dos recursos locais à Internet pública. Use as funcionalidades de escala e de proteção contra DDoS avançada do Azure implantando suas entidades públicas conhecidas no Azure. Como essas entidades publicamente acessíveis muitas vezes são alvo de ataques de DDoS, colocá-las no Azure reduz o impacto sobre os recursos locais.

## <a name="azure-offerings-for-ddos-protection"></a>Ofertas do Azure para proteção contra DDoS

O Azure tem duas ofertas de serviço de DDoS que fornecem proteção contra ataques de rede (camadas 3 e 4): Proteção contra DDoS Básica e Standard. 

### <a name="ddos-protection-basic"></a>Proteção contra DDoS Básica

A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A Proteção contra DDoS Básica não exige nenhuma alteração de aplicativo ou de configuração do usuário. A Proteção contra DDoS Básica ajuda a proteger todos os serviços do Azure, incluindo serviços PaaS, como o DNS do Azure.

![Representação de mapa da rede do Azure, com o texto "Presença de mitigação DDoS global" e "Capacidade de mitigação de DDoS principal"](./media/ddos-best-practices/image3.png)

A Proteção contra DDoS do Azure Básica consiste em componentes de hardware e de software. Um plano de controle de software decide quando, onde e qual tipo de tráfego deve ser direcionado para dispositivos de hardware que analisam e removem o tráfego de ataque. O plano de controle toma essa decisão com base em uma *política* de Proteção contra DDoS de ampla infraestrutura. Essa política é definida estaticamente e universalmente aplicada a todos os clientes do Azure.

Por exemplo, a política de Proteção contra DDoS especifica em qual volume de tráfego a proteção deve ser *disparada.* (Ou seja, o tráfego do locatário deve ser roteado por meio de dispositivos de depuração.) Em seguida, a política especifica como os dispositivos de depuração devem *mitigar* o ataque.

O serviço de Proteção contra DDoS do Azure Básica é voltado para a proteção da infraestrutura e da plataforma do Azure. Ela mitiga o tráfego quando ele excede uma taxa tão significativa que pode afetar vários clientes em um ambiente multilocatário. Ela não fornece alertas nem políticas personalizadas por cliente.

### <a name="ddos-protection-standard"></a>Proteção contra DDoS Standard

A proteção Standard fornece recursos de mitigação de DDoS avançados. Se ajusta automaticamente para proteger os recursos específicos do Azure em uma rede virtual. É muito simples habilitar a proteção em qualquer rede virtual nova ou existente, e ela não exige nenhum aplicativo ou alterações de recursos. Ela tem várias vantagens em comparação com o serviço básico, incluindo registro em log, alertas e telemetria. As seções a seguir descrevem os principais recursos do serviço de Proteção contra DDoS do Azure Standard.

#### <a name="adaptive-real-time-tuning"></a>Ajuste de tempo real adaptável

O serviço da Proteção contra DDoS do Azure Básica ajuda a proteger os clientes e evitar impactos a outros clientes. Por exemplo, se um serviço está provisionado para um volume típico de tráfego de entrada legítimo menor do que a *taxa de gatilho* da política da Proteção contra DDoS de toda a infraestrutura, um ataques de DDoS contra os recursos do cliente em questão pode passar despercebido. Em geral, a complexidade dos ataques recentes (por exemplo, DDoS de múltiplos vetores) e os comportamentos específicos do aplicativo de locatários necessitam de políticas de proteção personalizadas por cliente. O serviço realiza essa personalização usando dois insights:

- Aprendizado automático de padrões de tráfego por cliente (por IP) das camadas 3 e 4.

- Minimizando falsos positivos, considerando que a escala do Azure permite que absorva uma quantidade significativa de tráfego.

![Diagrama de como a Proteção contra DDoS Standard funciona, com a "Política de geração" circulada](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitoramento e alertas da Proteção contra DDoS

A Proteção contra DDoS Standard expõe uma telemetria sofisticada por meio do [Azure Monitor](../../azure-monitor/overview.md) por toda a duração de um ataque de DDoS. Você pode configurar alertas para qualquer uma das métricas do Azure Monitor que a Proteção contra DDoS utiliza. Você pode integrar o registro em log com Splunk (hubs de eventos do Azure), logs de Azure Monitor e armazenamento do Azure para análise avançada por meio da interface de diagnóstico de Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Políticas de mitigação de DDoS

Na portal do Azure, selecione **monitorar**  >  **métricas**. No painel **Métricas**, selecione o grupo de recursos, o tipo de recurso do **Endereço IP Público** e o endereço IP público do Azure. As métricas de DDoS estão visíveis no painel de **Métricas disponíveis**.

A Proteção contra DDoS Standard aplica três políticas de mitigação ajustadas automaticamente (TCP SYN, TCP e UDP) em cada endereço IP público do recurso protegido, na rede virtual que tem o DDoS habilitado. Você pode exibir os limites da política selecionando os pacotes de entrada da métrica **para disparar a mitigação de DDoS**.

![Métricas disponíveis e gráfico de métricas](./media/ddos-best-practices/image7.png)

Os limites da política são configurados automaticamente por meio da criação de perfil de tráfego de rede baseada em aprendizado de máquina. A mitigação de DDoS ocorre para um endereço IP sob ataque somente quando o limite da política for excedido.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Métrica para um endereço IP sob ataque de DDoS

Se o endereço IP público estiver sob ataque, o valor da métrica **Sob ataque de DDoS ou não** mudará para 1 conforme a Proteção contra DDoS executa a mitigação do tráfego do ataque.

![Gráfico e métrica "Sob ataque DDoS ou não"](./media/ddos-best-practices/image8.png)

É recomendável configurar um alerta nessa métrica. Você será notificado quando houver uma mitigação de DDoS ativa executada no seu endereço IP público.

Para ver mais informações, consulte [Gerenciar a Proteção contra DDoS do Azure padrão usando o Portal do Azure](../../ddos-protection/manage-ddos-protection.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall do aplicativo Web para ataques de recurso

Especificamente para ataques de recursos na camada de aplicativo, você deve configurar o WAF (Firewall do Aplicativo Web) para ajudar a proteger os aplicativos Web. O WAF inspeciona o tráfego de entrada da Web para bloquear injeções de SQL, scripts intersites, DDoS e outros ataques da camada 7. O Azure fornece o [WAF como um recurso do Gateway de Aplicativo](../../web-application-firewall/ag/ag-overview.md) para proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. Há outras ofertas de WAF disponíveis de parceiros do Azure que podem ser mais adequadas às suas necessidades no [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Mesmo os firewalls do aplicativo Web estão suscetíveis a ataques de esgotamento volumétrico e de estado. Nós recomendamos habilitar a Proteção contra DDoS Standard em uma rede virtual WAF para ajudar a protegê-la contra ataques volumétricos e de protocolo. Para obter mais informações, consulte a seção [Arquiteturas de referência da Proteção contra DDoS](#ddos-protection-reference-architectures).

### <a name="protection-planning"></a>Planejamento de proteção

Planejamento e preparação são cruciais para entender como será o desempenho de um sistema durante um ataque de DDoS. Criar um plano de resposta de gerenciamento de incidentes faz parte desse esforço.

Se tiver a Proteção contra DDoS Standard, certifique-se de que esteja habilitada na rede virtual dos pontos de extremidade voltados para a Internet. Configurar alertas de DDoS ajuda a manter um olhar constante sobre qualquer possível ataque à sua infraestrutura. 

Monitore seus aplicativos de forma independente. Entenda o comportamento normal do aplicativo. Prepare-se para agir caso o aplicativo não esteja se comportando conforme o esperado durante um ataque de DDoS.

#### <a name="testing-through-simulations"></a>Testes por meio de simulações

É uma prática recomendada testar suas suposições sobre como os serviços responderão a um ataque ao conduzir simulações periódicas. Durante o teste, valide se os serviços ou aplicativos continuam funcionando conforme esperado e se não há interrupções na experiência do usuário. Identifique lacunas do ponto de vista de tecnologia e de processo, e incorpore-as à estratégia de resposta de DDoS. Recomendamos que realize esses testes em ambientes de preparo ou fora do horário de pico para minimizar o impacto no ambiente de produção.

Firmamos parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface na qual os clientes do Azure podem gerar tráfego contra os pontos de extremidade públicos com Proteção contra DDoS habilitada para fins de simulação. Você pode usar a simulação [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para:

- Validar como a Proteção contra DDoS do Azure ajuda a proteger seus recursos do Azure contra ataques de DDoS.

- Otimize o processo de resposta a incidentes durante ataques de DDoS.

- Documente a conformidade de DDoS.

- Treine suas equipes de segurança de rede.

A segurança cibernética requer constante inovação na defesa. A Proteção contra DDoS Standard do Azure é uma oferta de ponta com uma solução efetiva para mitigar ataques de DDoS cada vez mais complexos.

## <a name="components-of-a-ddos-response-strategy"></a>Componentes de uma estratégia de resposta a DDoS

Um ataque de DDoS que tem como alvo recursos do Azure normalmente requer intervenção mínima do ponto de vista do usuário. Ainda assim, incorporar a mitigação de DDoS como parte da estratégia de resposta a incidentes ajudará a minimizar o impacto na continuidade de negócios.

### <a name="microsoft-threat-intelligence"></a>Inteligência contra ameaças da Microsoft

A Microsoft tem uma ampla rede de inteligência sobre ameaças. Essa rede usa o conhecimento coletivo de uma comunidade de segurança estendida que dá suporte aos serviços online da Microsoft, parceiros da Microsoft e relações da comunidade de segurança da Internet. 

Como um provedor de infraestrutura crítico, a Microsoft recebe avisos antecipados sobre ameaças. A Microsoft reúne inteligência sobre ameaças de seus serviços online e de sua base de clientes global. A Microsoft incorpora toda essa inteligência contra ameaças em seus produtos de Proteção contra DDoS do Azure.

Além disso, a DCU (Unidade de Crimes Digitais da Microsoft) executa estratégias ofensivas contra botnets. Botnets são uma fonte comum de comando e controle de ataques de DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Avaliação de risco dos seus recursos do Azure

É fundamental compreender o escopo do seu risco a ataques de DDoS continuamente. Periodicamente, pergunte-se:

- Quais novos recursos do Azure disponíveis publicamente precisam de proteção?

- Há um ponto único de falha no serviço? 

- Como os serviços podem ser isolados para limitar o impacto de um ataque e ainda disponibilizá-los para os clientes válidos?

- Há redes virtuais em que a Proteção contra DDoS Standard deve ser habilitada, mas ainda não está? 

- Meu serviços são ativo/ativo com failover em várias regiões?

### <a name="customer-ddos-response-team"></a>Equipe de resposta a DDoS do cliente

Criar uma equipe de resposta a DDoS é uma etapa importante para responder a um ataque de forma rápida e eficiente. Identifique vários contatos na organização que supervisionarão o planejamento e a execução. Essa equipe de resposta de DDoS deve compreender totalmente o serviço de Proteção contra DDoS do Azure Standard. Certifique-se de que a equipe possa identificar e eliminar um ataque através da coordenação com clientes internos e externos, incluindo a equipe de Suporte da Microsoft.

Para a sua equipe de resposta de DDoS, recomendamos que use exercícios de simulação como uma parte normal do seu planejamento de continuidade e disponibilidade do serviço. Esses exercícios devem incluir o teste de escala.

### <a name="alerts-during-an-attack"></a>Alertas durante um ataque

A Proteção contra DDoS do Azure Standard identificará e mitigará os ataques de DDoS sem qualquer intervenção do usuário. Para ser notificado quando houver uma mitigação ativa para um IP público protegido, você pode [configurar um alerta](../../ddos-protection/manage-ddos-protection.md) na métrica **sob ataque de DDoS ou não**. Você pode optar por criar alertas para as outras métricas de DDoS para entender a escala do ataque, o tráfego que está sendo descartado e outros detalhes.

#### <a name="when-to-contact-microsoft-support"></a>Quanto entrar em contato com o Suporte da Microsoft

- Durante um ataque de DDoS, você descobre que o desempenho do recurso protegido foi drasticamente prejudicado ou que o recurso não está disponível.

- Você acha que o serviço da Proteção contra DDoS não está se comportando conforme o esperado. 

  O serviço de Proteção contra DDoS iniciará a mitigação somente se o valor da métrica **Política para disparar a mitigação de DDoS (TCP/TCP SYN/UDP)** for menor do que o tráfego recebido no recurso de IP público protegido.

- Você está planejando um evento viral que aumentará significativamente o tráfego de rede.

- Um ator ameaçou iniciar um ataque de DDoS contra seus recursos.

- Se você precisar permitir que o liste um IP ou intervalo IP do padrão de proteção contra DDoS do Azure. Um cenário comum é permitir o IP da lista se o tráfego for roteado de um WAF de nuvem externo para o Azure. 

Para ataques que têm um impacto comercial crítico, crie um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) com gravidade A.

### <a name="post-attack-steps"></a>Etapas de pós-ataques

Sempre é uma boa estratégia realizar uma análise posterior ao ataque e reajustar a estratégia de resposta a DDoS conforme necessário. Itens a serem considerados:

- Ocorreu alguma interrupção no serviço ou na experiência do usuário devido à falta de arquitetura escalonável?

- Quais aplicativos ou serviços mais sofreram?

- O quão eficaz foi a estratégia de resposta a DDoS e como ela pode ser aprimorada?

Se você suspeitar que está sofrendo um ataque de DDoS, escale a questão por meio dos canais de suporte normais do Azure.

## <a name="ddos-protection-reference-architectures"></a>Arquiteturas de referência da Proteção contra DDoS

A proteção contra DDoS Standard é projetada [para serviços que são implantados em uma rede virtual](../../virtual-network/virtual-network-for-azure-services.md). Para outros serviços, a Proteção contra DDoS Básica padrão se aplica. As seguintes arquiteturas de referência são organizadas por cenários, com padrões de arquitetura agrupados.

### <a name="virtual-machine-windowslinux-workloads"></a>Cargas de trabalho de máquina virtual (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplicativo em execução em VMs com balanceamento de carga

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para executar várias VMs do Windows em um conjunto de dimensionamento atrás de um balanceador de carga para melhorar a disponibilidade e a escalabilidade. Essa arquitetura pode ser usada para qualquer carga de trabalho sem estado, como um servidor Web.

![Diagrama da arquitetura de referência para um aplicativo em execução em VMs com balanceamento de carga](./media/ddos-best-practices/image9.png)

Nesta arquitetura, uma carga de trabalho é distribuída em várias instâncias de VM. Há um único endereço IP público e o tráfego da Internet é distribuído para as VMs por meio de um balanceador de carga. A Proteção contra DDoS Standard está habilitada na rede virtual do balanceador de carga do Azure (Internet) que tem o IP público associado a ela.

O balanceador de carga distribui as solicitações de entrada da Internet para as instâncias de VM. Os conjuntos de dimensionamento de máquinas virtuais permitem que o número de VMs seja reduzido ou aumentado horizontalmente manualmente, ou automaticamente com base em regras predefinidas. Isso é importante se o recurso está sob ataque de DDoS. Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplicativo em execução em N camadas do Windows

Há muitas maneiras de implementar uma arquitetura de N camadas. O diagrama a seguir mostra um aplicativo Web típico de três camadas. Essa arquitetura se baseia no artigo [Executar VMs com balanceamento de carga para escalabilidade e disponibilidade](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). As camadas comerciais e da Web usam VMs com balanceamento de carga.

![Diagrama da arquitetura de referência para um aplicativo em execução em N camadas do Windows](./media/ddos-best-practices/image10.png)

Nesta arquitetura, a Proteção contra DDoS Standard está habilitada na rede virtual. Todos os IPs públicos na rede virtual terão proteção contra DDoS nas camadas 3 e 4. Para proteção da camada 7, implante o Gateway de Aplicativo no SKU do WAF. Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Aplicativo Web PaaS

Essa arquitetura de referência mostra a execução de um aplicativo do Serviço de Aplicativo do Azure em uma única região. Essa arquitetura mostra um conjunto de práticas comprovadas para um aplicativo Web que usa o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/documentation/services/app-service/) e o [Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Uma região em espera é configurada para cenários de failover.

![Diagrama da arquitetura de referência para um aplicativo da Web de PaaS](./media/ddos-best-practices/image11.png)

O Gerenciador de Tráfego do Microsoft Azure roteia as solicitações de entrada para o Gateway de Aplicativo em uma das regiões. Durante as operações normais, ele roteia as solicitações para o Gateway de Aplicativo na região ativa. Se essa região ficar não disponível, o Gerenciador de Tráfego fará failover para o Gateway de Aplicativo na região em espera.

Todo o tráfego da Internet destinado ao aplicativo Web é roteado para o [Endereço IP público do Gateway de Aplicativo](../../application-gateway/application-gateway-web-app-overview.md) por meio do Gerenciador de Tráfego. Nesse cenário, o Serviço de Aplicativo (aplicativo Web) em si não é diretamente externo e está protegido pelo Gateway de Aplicativo. 

É recomendável configurar o SKU do WAF do Gateway de Aplicativo (modo de prevenção) para ajudar a proteger contra ataques da camada 7 (HTTP/HTTPS/WebSocket). Além disso, os aplicativos Web são configurados para [aceitar somente o tráfego do endereço IP do Gateway de Aplicativo](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Para obter mais informações sobre arquitetura de referência, consulte [este artigo](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Mitigação para serviços PaaS não Web

#### <a name="hdinsight-on-azure"></a>HDInsight no Azure

Essa arquitetura de referência mostra a configuração da Proteção contra DDoS Standard para [cluster do Azure HDInsight](../../hdinsight/index.yml). Verifique se o cluster do HDInsight está vinculado a uma rede virtual e se a Proteção contra DDoS está habilitada nessa rede virtual.

![Os painéis "HDInsight" e "Configurações avançadas", com as configurações de rede virtual](./media/ddos-best-practices/image12.png)

![Seleção para habilitar a proteção contra DDoS](./media/ddos-best-practices/image13.png)

Nesta arquitetura, o tráfego destinado ao cluster de HDInsight vindo da Internet é roteado para o IP público associado ao balanceador de carga de gateway do HDInsight. O balanceador de carga de gateway, em seguida, envia o tráfego para os nós principais ou nós de trabalho diretamente. Como a Proteção contra DDoS Standard está habilitada na rede virtual do HDInsight, todos os IPs públicos na rede virtual receberão proteção contra DDoS nas camadas 3 e 4. Essa arquitetura de referência pode ser combinada com arquiteturas de referência de N camadas e com várias regiões.

Para obter mais informações sobre a arquitetura de referência, consulte a documentação [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> Azure app Service - Serviço de Aplicativo do Azure para gerenciamento de API ou PowerApps em uma rede virtual com um IP público são ambos não tem suporte nativo.

## <a name="next-steps"></a>Próximas etapas

* [Responsabilidade compartilhada na nuvem](shared-responsibility.md)
* [Página do produto proteção contra DDoS do Azure](https://azure.microsoft.com/services/ddos-protection/)
* [Documentação da proteção contra DDoS do Azure](../../ddos-protection/ddos-protection-overview.md)