---
title: Melhores práticas de Segurança Operacional do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de melhores práticas de Segurança Operacional do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587012"
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de Segurança Operacional do Azure
A segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Azure. A segurança operacional do Azure se baseia em uma estrutura que incorpora o conhecimento adquirido por recursos exclusivos da Microsoft, incluindo [o SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl), o programa [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e o conhecimento profundo do cenário de ameaças de segurança cibernética.

Neste artigo, veremos uma coleção de melhores práticas de segurança. Essas melhores práticas derivam da nossa experiência com segurança de banco de dados do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:
-   O que é a prática recomendada
-   Por que é ideal habilitar essa prática recomendada
-   O que poderá acontecer se você não habilitar a prática recomendada
- Como você pode aprender a habilitar a prática recomendada

Este artigo Melhores práticas de Segurança Operacional do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorar serviços de armazenamento quanto a mudanças inesperadas no comportamento
Questões de diagnóstico e de solução de problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas que em ambientes tradicionais. Aplicativos podem ser implantados em uma infraestrutura PaaS ou IaaS, local, em um dispositivo móvel ou em alguma combinação desses ambientes. Tráfego de rede do seu aplicativo pode passar por redes públicas e privadas, e seu aplicativo poderá usar várias tecnologias de armazenamento.

Você deve monitorar continuamente os serviços de armazenamento que o aplicativo usa para qualquer mudança inesperada em comportamento (como tempos de resposta mais lentos). Use o log para coletar dados mais detalhados e analisar o problema em profundidade. As informações de diagnósticos que você obtiver tanto do monitoramento quanto do registro em log o ajudarão a determinar a raiz do problema que o seu aplicativo encontrou. Você poderá solucionar o problema e determinar as etapas apropriadas para corrigi-lo.

A [Análise de Armazenamento do Azure](../storage/storage-analytics.md) executa o registro em log e fornece dados de métrica para uma conta de armazenamento do Azure. Recomendamos que você use esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detectar e reagir a ameaças
A [Central de Segurança do Azure](../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade e controle da segurança de seus recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um diversas soluções de segurança.

A camada gratuita da Central de Segurança oferece segurança limitada somente para seus recursos do Azure. A camada Standard estende esses recursos para locais e outras nuvens. A Central de Segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando a análise e inteligência e responder rapidamente quando sob ataque. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. É recomendável [integrar sua assinatura do Azure à Central de Segurança Standard](../security-center/security-center-get-started.md).

Use a Central de Segurança para obter uma exibição central do estado da segurança de todos os seus recursos do Azure. Verifique rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, além disso, identifique com rapidez os recursos que exigem atenção.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Acompanhar monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure, combinando recursos de rede como rede virtual, ExpressRoute, Gateway de Aplicativo e balanceadores de carga. O monitoramento está disponível em cada um dos recursos da rede.

O [Observador de Rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Use suas ferramentas de diagnóstico e visualização para monitorar e diagnosticar condições em um nível de cenário de rede no Azure, para o Azure e do Azure.

A seguir estão as melhores práticas para as ferramentas disponíveis e monitoramento de rede.

**Melhor prática**: automatizar o monitoramento remoto de rede com a captura de pacote.  
**Detalhe**: monitore e realize o diagnóstico de problemas de rede sem fazer logon em suas VMs usando o Observador de Rede. Disparar [captura de pacote](../network-watcher/network-watcher-alert-triggered-packet-capture.md) por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes para um diagnóstico melhor.

**Melhor prática**: obter insights sobre o tráfego de rede usando os logs de fluxo.  
**Detalhe**: desenvolva um entendimento aprofundado sobre padrões de tráfego de rede usando os [logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md). As informações em logs de fluxo ajudam a coletar dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

**Melhor prática**: diagnosticar problemas de conectividade de VPN.  
**Detalhe**: use o Observador de Rede para [diagnosticar os problemas mais comuns de Gateway de VPN e conexão](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Você pode não apenas identificar o problema, como também usar logs detalhados para investigar ainda mais.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implantação segura usando ferramentas do DevOps comprovadas
Use as seguintes práticas recomendadas de DevOps para garantir que suas equipes e sua empresa sejam produtivas e eficientes.

**Melhor prática**: automatizar a compilação e a implantação de serviços.  
**Detalhe**: [infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI a remover a sobrecarga de compilação e do gerenciamento diários da infraestrutura modular. Habilita os profissionais de TI a criar e realizar a manutenção do ambiente de servidor moderno de maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo.

Você pode usar o [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para provisionar seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo em cada estágio do ciclo de vida do aplicativo.

**Melhor prática**: compilar e implantar automaticamente serviços de nuvem ou aplicativos Web do Azure.  
**Detalhe**: você pode usar o Azure Pipelines para [construir automaticamente e implantar](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) nos serviços de nuvem ou aplicativos Web do Azure. O Azure Pipelines implanta os binários automaticamente depois de fazer uma compilação para o Azure após cada verificação do código. O processo de build do pacote é equivalente ao comando Package no Visual Studio, e as etapas de publicação equivalem ao comando Publish do Visual Studio.

**Melhor prática**: usar a implantação contínua.  
**Detalhe**: o [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) é uma solução para automatizar a implantação em vários estágios e gerenciar o processo de lançamento. Crie pipelines de implantação gerenciados e contínuos, a fim de lançar com rapidez, facilidade e frequência. Com o Azure Pipelines, você pode automatizar o processo de liberação e pode ter fluxos de trabalho de aprovação predefinidos. Implante localmente e na nuvem, estenda e personalize conforme a necessidade.

**Melhor prática**: Verificar o desempenho de seu aplicativo antes de iniciá-lo ou implantar atualizações na produção.  
**Detalhe**: execute [testes de carga](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) baseados em nuvem usando os Azure Test Plans:

- Localizar problemas de desempenho em seu aplicativo Web.
- Melhorar a qualidade da implantação.
- Garantir que seu aplicativo esteja sempre disponível.
- Garantir que seu aplicativo possa lidar com o tráfego da sua próxima campanha de marketing ou de seu próximo lançamento.

**Melhor prática**: Monitorar o desempenho do aplicativo.  
**Detalhe**: O [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use o Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

## <a name="mitigate-and-protect-against-ddos"></a>Atenuação de riscos e proteção contra DDoS
Ataque de DDoS (negação de serviço distribuído) é um tipo de ataque que tenta esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Esses ataques estão se tornando cada vez mais sofisticados e maiores tanto em termos de tamanho quanto de impacto. Eles podem ser direcionados a qualquer ponto de extremidade publicamente acessível pela Internet.

Projetar e criar para garantir a resiliência contra DDoS exige planejar e projetar para uma variedade de modos de falha.

A seguir, estão as melhores práticas para a criação de serviços resilientes a DDoS no Azure.

**Melhor prática**: Garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitam que um volume relativamente baixo de solicitações use muitos recursos, resultando em uma interrupção de serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars). Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/en-us/sdl). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

**Melhor prática**: projetar seus aplicativos para [escalar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.  
**Detalhe**: Para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [Plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../cloud-services/cloud-services-choose-me.md).

Para [Máquinas Virtuais do Azure](../virtual-machines/windows/overview.md), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). É recomendável usar conjuntos de dimensionamento de máquinas virtuais para obter recursos de dimensionamento automático.

**Melhor prática**: Dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de um ataque ser bem-sucedido. Implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.  
**Detalhe**: o risco de ataque aumenta conforme o tamanho (área da superfície) do aplicativo. Você pode reduzir a área da superfície usando a lista de permissões para fechar o espaço de endereços IP exposto e portas de escuta que não são necessários em balanceadores de carga ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-create-probe-portal.md)).

[Grupos de segurança de rede](../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Você pode usar [marcas de serviço](../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão.

Usar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) altera o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, minimize a exposição dos recursos locais à Internet pública.

O Azure tem duas [ofertas de serviço](../virtual-network/ddos-protection-overview.md) contra DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A básica não exige nenhuma alteração ao aplicativo ou à configuração do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS, como o DNS do Azure.
- A proteção Standard fornece funcionalidades avançadas de atenuação de DDoS contra ataques de rede. Se ajusta automaticamente para proteger os recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de redes virtuais. Isso também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou recurso.

## <a name="next-steps"></a>Próximas etapas
Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por email para secure@microsoft.com
