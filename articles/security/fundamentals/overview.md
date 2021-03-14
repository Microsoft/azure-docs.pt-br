---
title: Introdução à segurança do Azure | Microsoft Docs
description: Apresente-se à segurança do Azure, seus diversos serviços e como ele funciona lendo esta visão geral.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: a62326d99eee8407b65c0c640b4db8a6f051c758
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101181"
---
# <a name="introduction-to-azure-security"></a>Introdução à segurança do Azure

## <a name="overview"></a>Visão geral

Sabemos que a segurança é o primeiro trabalho na nuvem e o quanto é importante que você encontre informações precisas e atualizadas sobre a segurança do Azure. Uma das melhores razões para usar o Azure para seus aplicativos e serviços é aproveitar sua ampla variedade de ferramentas e recursos de segurança. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure. O Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo que também permite uma responsabilidade transparente.

Este artigo fornece uma visão abrangente da segurança disponível com o Azure.

### <a name="azure-platform"></a>Plataforma do Azure

O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração com o Docker, criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js e criar back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e nas quais confiam. Ao se basear ou migrar ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente e fornece uma base confiável com a qual as empresas podem atender aos seus requisitos de segurança.

Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, de forma que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização. Este documento ajuda você a entender como as funcionalidades de segurança do Azure podem ajudá-lo a atender a esses requisitos.

> [!Note]
> O foco principal deste documento são os controles voltados para o cliente que você pode usar para personalizar e aumentar a segurança de seus aplicativos e serviços.
>
> Para obter informações sobre como a Microsoft protege a plataforma do Azure em si, consulte [segurança de infraestrutura do Azure](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Resumo dos recursos de segurança do Azure

Dependendo do modelo de serviço de nuvem, a responsabilidade de quem gerencia a segurança do aplicativo ou serviço varia. Há recursos disponíveis na Plataforma Azure para ajudar você a cumprir essas responsabilidades por meio de recursos internos, e soluções de parceiros que podem ser implantadas em uma assinatura do Azure.

Os recursos internos são organizados em seis áreas funcionais: operações, aplicativos, armazenamento, rede, computação e identidade. Detalhes adicionais sobre os recursos e funcionalidades disponíveis na plataforma do Azure nessas seis áreas são fornecidos por meio de informações de resumo.

## <a name="operations"></a>Operations

Esta seção fornece outras informações sobre os principais recursos em operações de segurança, e informações de resumo sobre esses recursos.

### <a name="azure-security-center"></a>Central de Segurança do Azure

A [central de segurança](../../security-center/security-center-introduction.md) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

Além disso, a central de segurança ajuda com as operações de segurança, fornecendo um único painel que superfícies alertas e recomendações que podem ser acionadas imediatamente. Geralmente, você pode corrigir problemas com um único clique dentro do console da central de segurança.

### <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) permite que você trabalhe com os recursos em sua solução como um grupo. Você pode implantar, atualizar ou excluir todos os recursos da sua solução em uma única operação coordenada. Use um [modelo do Azure Resource Manager](../../azure-resource-manager/templates/overview.md) para a implantação, e esse modelo pode ser útil para diferentes ambientes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

As implantações baseadas em modelos do Azure Resource Manager ajudam a melhorar a segurança das soluções implantadas no Azure devido às configurações de controle de segurança padrão, e podem ser integradas às implantações baseadas em modelo padronizadas. Isso reduz o risco de erros de configuração de segurança que podem ocorrer durante as implantações manuais.

### <a name="application-insights"></a>Application Insights

O [Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web. Com o Application Insights, você pode monitorar seus aplicativos Web ativos e detectar automaticamente as anomalias no desempenho. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele monitora seus aplicativos em todo o tempo de execução, tanto durante o teste quanto depois de publicado ou implantado.

O Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia você tem mais usuários, o nível de capacidade de resposta do aplicativo e quão bem ele é atendido por quaisquer serviços externos dos quais depende.

Se houver travamentos, falhas ou problemas de desempenho, você pode pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço enviará a você emails se houver alterações na disponibilidade e no desempenho de seu aplicativo. Assim, o Application Insight torna-se uma ferramenta de segurança importante, pois ajuda com a disponibilidade na tríade de segurança de disponibilidade, integridade e confidencialidade.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/monitoring-and-diagnostics/) oferece visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da assinatura do Azure ([log de atividades](../../azure-monitor/essentials/platform-logs-overview.md)) e de cada recurso individual do Azure ([logs de recursos](../../azure-monitor/essentials/platform-logs-overview.md)). Use o Azure Monitor para receber alertas sobre eventos relacionados à segurança que são gerados nos logs do Azure.

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Logs de Azure monitor](../../azure-monitor/logs/log-query-overview.md) – fornece uma solução de gerenciamento de ti para infraestrutura local e de terceiros baseada em nuvem (como AWS), além dos recursos do Azure. Os dados de Azure Monitor podem ser roteados diretamente para Azure Monitor logs para que você possa ver as métricas e os logs de todo o seu ambiente em um único lugar.

Os logs de Azure Monitor podem ser uma ferramenta útil em análise forense e outras análises de segurança, pois a ferramenta permite que você pesquise rapidamente por grandes quantidades de entradas relacionadas à segurança com uma abordagem de consulta flexível. Além disso, [os logs de firewall e proxy locais podem ser exportados para o Azure e disponibilizados para análise usando logs de Azure monitor.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Assistente do Azure

O [Assistente do Azure](../../advisor/advisor-overview.md) é um consultor de nuvem personalizado que ajuda você a otimizar suas implantações do Azure. Ele analisa a configuração dos recursos e a telemetria do uso. Em seguida, ele recomenda soluções para ajudar a melhorar o [desempenho](../../advisor/advisor-performance-recommendations.md), a [segurança](../../advisor/advisor-security-recommendations.md)e a [confiabilidade](../../advisor/advisor-high-availability-recommendations.md) de seus recursos, ao mesmo tempo em que procura oportunidades para [reduzir o gasto geral do Azure](../../advisor/advisor-cost-recommendations.md). O Assistente do Azure fornece recomendações de segurança, o que pode melhorar consideravelmente sua postura de segurança geral para soluções implantadas no Azure. Essas recomendações são obtidas da análise de segurança executada pela [Central de Segurança do Azure.](../../security-center/security-center-introduction.md)

## <a name="applications"></a>Aplicativos

A seção fornece outras informações sobre os principais recursos em segurança do aplicativo e informações de resumo sobre esses recursos.

### <a name="web-application-vulnerability-scanning"></a>Verificação de vulnerabilidades de aplicativos Web

Uma das maneiras mais fáceis de começar a usar testes de vulnerabilidades no [aplicativo Serviço de Aplicativo](../../app-service/overview.md) é usar a [integração ao Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para executar o exame de vulnerabilidades com um clique no aplicativo. Você pode exibir os resultados do teste em um relatório de fácil compreensão e aprender como corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Teste de penetração

Não realizamos [testes de penetração](./pen-testing.md) de seu aplicativo para você, mas entendemos que você deseja e precisa executar testes em seus próprios aplicativos. Isso é bom, porque ao aprimorar a segurança de seus aplicativos, você ajuda a tornar todo o ecossistema do Azure mais seguro. Embora a notificação da Microsoft de atividades de teste de caneta não seja mais necessária, os clientes ainda devem estar em conformidade com as [regras de teste de penetração do Microsoft Cloud](https://www.microsoft.com/msrc/pentest-rules-of-engagement).

### <a name="web-application-firewall"></a>Firewall do aplicativo Web

O WAF (firewall de aplicativo Web) no [Gateway de Aplicativo do Azure](../../application-gateway/features.md#web-application-firewall) protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão. Ele vem pré-configurado com proteção contra as ameaças identificadas pelo [OWASP (Projeto Aberto de Segurança em Aplicativo Web) como as 10 vulnerabilidades mais comuns](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicativo do Azure

A [autenticação/autorização do serviço de aplicativo](../../app-service/overview-authentication-authorization.md) é um recurso que fornece uma maneira para seu aplicativo conectar usuários para que você não precise alterar o código no back-end do aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário.

### <a name="layered-security-architecture"></a>Arquitetura de segurança em camadas

Como os [Ambientes do Serviço de Aplicativo](../../app-service/environment/app-service-app-service-environment-intro.md) fornecem um ambiente de runtime isolado implantado em uma [Rede Virtual do Azure](../../virtual-network/virtual-networks-overview.md), os desenvolvedores podem criar uma arquitetura de segurança em camadas fornecendo níveis diferentes de acesso à rede para cada camada de aplicativo. Um desejo comum é ocultar os back-ends de API do acesso à Internet geral e só permitir que as APIs sejam chamadas por aplicativos Web upstream. Os [NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) podem ser usados em sub-redes da Rede Virtual do Azure contendo Ambientes do Serviço de Aplicativo para restringir o acesso público aos aplicativos da API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnóstico de servidor Web e diagnóstico de aplicativos
Os [aplicativos Web do serviço de aplicativo](../../app-service/troubleshoot-diagnostic-logs.md) fornecem funcionalidade de diagnóstico para registrar informações do servidor Web e do aplicativo Web. Estes estão logicamente separados em diagnóstico de servidor Web e diagnóstico de aplicativos. O servidor Web inclui dois grandes avanços em diagnóstico e solução de problemas de sites e aplicativos.

O primeiro são informações de estado em tempo real sobre pools de aplicativos, processos de trabalho, sites, domínios de aplicativo e solicitações em execução. O segundo são os eventos de rastreamento detalhados que rastreiam uma solicitação por todo o processo de solicitação e resposta.

Para habilitar a coleta desses eventos de rastreamento, o IIS 7 pode ser configurado para capturar automaticamente os logs de rastreamento completos, em formato XML, para qualquer solicitação específica com base no tempo decorrido ou em códigos de resposta do erro.

## <a name="storage"></a>Armazenamento
A seção fornece outras informações sobre os principais recursos em segurança de armazenamento do Azure e informações de resumo sobre esses recursos.

### <a name="azure-role-based-access-control-azure-rbac"></a>RBAC do Azure (controle de acesso baseado em função do Azure)
Você pode proteger sua conta de armazenamento com o Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md). Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e aos princípios de segurança de [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é imperativo para organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função apropriada do Azure a grupos e aplicativos em um determinado escopo. Você pode usar [funções internas do Azure](../../role-based-access-control/built-in-roles.md), como colaborador da conta de armazenamento, para atribuir privilégios aos usuários. O acesso às chaves de armazenamento para uma conta de armazenamento usando o modelo de [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) pode ser controlado por meio do RBAC do Azure.

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado
Uma [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-sas-overview.md) fornece acesso delegado aos recursos em sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado período e com um conjunto específico de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:
- [Criptografia no nível de transporte](../../storage/blobs/security-recommendations.md), como https, quando você transfere dados para dentro ou para fora do armazenamento do Azure.

- [Criptografia de transmissão](../../storage/blobs/security-recommendations.md), como a [criptografia SMB 3,0](../../storage/blobs/security-recommendations.md) para [compartilhamentos de arquivos do Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

- Criptografia do cliente, que permite criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografá-los após serem transferidos para fora do armazenamento.

### <a name="encryption-at-rest"></a>Criptografia em repouso
Para muitas organizações, a criptografia de dados em repouso é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. Há três recursos de segurança de armazenamento do Azure que fornecem criptografia de dados que estão “em repouso”:

- [Criptografia do Serviço de Armazenamento](../../storage/common/storage-service-encryption.md) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure.

- A [criptografia do lado do cliente](../../storage/common/storage-client-side-encryption.md) também fornece o recurso de criptografia em repouso.

- [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento

[Análise de armazenamento do Azure](/rest/api/storageservices/fileservices/storage-analytics) executa o registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento. A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem sucedidas.
- Solicitações com falha, incluindo o tempo limite, limitação, rede, autorização e outros erros.
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha.
- Solicitações para dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>Habilitar clientes com base no navegador usando CORS

[CORS (Compartilhamento de recursos entre origens)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que permite que os domínios troquem permissões para acessar recursos uns dos outros. O Agente do Usuário envia cabeçalhos adicionais para garantir que o código JavaScript carregado de um determinado domínio tenha permissão para acessar os recursos localizados em outro domínio. Depois, o último domínio responde com cabeçalhos adicionais, permitindo ou negando o acesso do domínio original aos seus recursos.

Agora, os serviços de armazenamento do Azure oferecem suporte a CORS, para que depois de definir as regras de CORS para o serviço, uma solicitação autenticada corretamente feita no serviço de um domínio diferente será avaliada para determinar se é permitida de acordo com as regras que você especificou.

## <a name="networking"></a>Rede

A seção fornece outras informações sobre os principais recursos em segurança de rede do Azure e informações de resumo sobre esses recursos.

### <a name="network-layer-controls"></a>Controles de camada de rede

O controle de acesso à rede é o ato de limitar a conectividade de entrada ou saída de sub-redes ou dispositivos específicos e representa o aspecto fundamental da segurança de rede. O objetivo do controle de acesso à rede é certificar-se de que suas máquinas virtuais e seus serviços são acessíveis apenas aos usuários e dispositivos para os quais você deseja que tenham esse acesso.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) é um Firewall básico de filtragem de pacotes com estado e permite que você controle o acesso com base em uma tupla de 5. Os NSGs não fornecem inspeção da camada de aplicativo nem controles de acesso autenticado. Eles podem ser usados para controlar o tráfego entre sub-redes dentro de uma Rede Virtual do Azure e o tráfego entre uma Rede Virtual do Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado

A capacidade de controlar o comportamento de roteamento em suas Redes Virtuais do Azure é uma funcionalidade crítica de controle de acesso e segurança de rede. Por exemplo, se você quiser ter certeza de que todo o tráfego de entrada e saída da Rede Virtual do Azure passa por esse dispositivo de segurança virtual, será necessário conseguir controlar e personalizar o comportamento do roteamento. É possível fazer isso configurando as Rotas Definidas pelo Usuário no Azure.

As [Rotas Definidas pelo Usuário](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) permitem que você personalize os caminhos de entrada e saída de máquinas virtuais individuais ou sub-redes a fim de garantir a rota mais segura possível. O [túnel forçado](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) é um mecanismo que você pode usar para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet.

Isso é diferente de poder aceitar conexões de entrada e responder a elas. Os servidores Web front-end precisam responder à solicitação dos hosts da Internet e, portanto, o tráfego originado da Internet tem permissão de entrada nesses servidores Web, que, por sua vez, podem responder.

O túnel forçado é normalmente usado para forçar o tráfego de saída para a Internet a fim de passar por firewalls e proxies de segurança locais.

#### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual

Embora os Grupos de Segurança de Rede, as Rotas Definidas pelo Usuário e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), em algumas ocasiões você pode querer habilitar a segurança em níveis superiores da pilha. É possível acessar esses recursos avançados de segurança de rede por meio de uma solução de dispositivo de segurança de rede de parceiro do Azure. Você pode encontrar as soluções de segurança de rede do parceiro do Azure mais atuais visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurando por "segurança" e "segurança de rede".

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da malha de rede do Azure dedicada à sua assinatura. Você pode controlar os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas na rede. Você pode segmentar a VNet em sub-redes e colocar as VMs (máquinas virtuais) de IaaS do Azure e/ou os [Serviços de nuvem (instâncias de função de PaaS)](../../cloud-services/cloud-services-choose-me.md) em Redes Virtuais do Azure.

Além disso, você pode conectar a rede virtual à sua rede local usando uma das [opções de conectividade](../../vpn-gateway/index.yml) disponíveis no Azure. Em linhas gerais, você pode expandir sua rede no Azure, com controle total sobre os blocos de endereços IP, com benefícios de escala empresarial proporcionados pelo Azure.

A rede do Azure dá suporte a vários cenários de acesso remoto seguro. Entre eles estão:

- [Conectar estações de trabalho individuais a uma Rede Virtual do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [Conectar a rede local a uma Rede Virtual do Azure com uma VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Conectar a rede local a uma Rede Virtual do Azure com uma conexão WAN dedicada](../../expressroute/expressroute-introduction.md)

- [Conectar Redes Virtuais do Azure entre si](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>Gateway de VPN

Para enviar o tráfego de rede entre sua Rede Virtual do Azure e seu site local, será necessário criar um gateway de VPN para sua Rede Virtual do Azure. Um [gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você também pode usar gateways de VPN para enviar o tráfego entre as Redes Virtuais do Azure pela malha de rede do Azure.

### <a name="express-route"></a>ExpressRoute

O Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) é uma conexão WAN dedicada que permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade.

![ExpressRoute](./media/overview/azure-security-figure-1.png)

Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Microsoft Azure, Microsoft 365 e CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização.

As conexões de ExpressRoute não passam pela Internet pública e, portanto, podem ser consideradas mais seguras do que soluções de VPN. Isso permite que as conexões de ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

### <a name="application-gateway"></a>Gateway de Aplicativo

O [Gateway de Aplicativo](../../application-gateway/overview.md) do Microsoft Azure fornece um [ADC (Controlador de Entrega de Aplicativos)](https://en.wikipedia.org/wiki/Application_delivery_controller) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o aplicativo.

![Gateway de Aplicativo](./media/overview/azure-security-figure-2.png)

Ele permite que você otimize a produtividade web farm descarregando a terminação de TLS com uso intensivo de CPU para o gateway de aplicativo (também conhecido como "descarregamento de TLS" ou "ponte TLS"). Ele também fornece outros recursos de roteamento de Camada 7, incluindo distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e a capacidade de hospedar vários sites por trás de um único Gateway de Aplicativo baseado em cookie. O Gateway de Aplicativo do Azure é um balanceador de carga de camada 7.

Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local.

O aplicativo fornece muitos recursos do ADC (controlador de entrega de aplicativos), incluindo balanceamento de carga HTTP, afinidade de sessão baseada em cookie, [descarregamento de TLS](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), investigações de integridade personalizadas, suporte para vários sites e muitos outros.

### <a name="web-application-firewall"></a>Firewall do Aplicativo Web

O Firewall do aplicativo Web é um recurso do [Gateway de Aplicativo do Azure](../../application-gateway/overview.md) que fornece proteção para aplicativos Web que utilizam o gateway de aplicativo para as funções ADC (controle de entrega de aplicativos) padrão. O firewall do aplicativo Web faz isso protegendo-os contra a maioria das 10 vulnerabilidades mais comuns da Web segundo o OWASP.

![Firewall do Aplicativo Web](./media/overview/azure-security-figure-3.png)

- Proteção contra injeção de SQL

- Proteção Contra Ataques Comuns da Web, como a injeção de comandos, as solicitações HTTP indesejadas, a divisão de resposta HTTP e o ataque de inclusão de arquivo remoto

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação

- Prevenção contra bots, rastreadores e scanners

- Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS etc.)

Um firewall do aplicativo Web para proteger contra ataques da Web simplifica muito o gerenciamento de segurança e oferece mais garantia para o aplicativo contra ameaças de invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com o firewall do aplicativo Web.

### <a name="traffic-manager"></a>Gerenciador de Tráfego

O [Gerenciador de Tráfego do Microsoft Azure](../../traffic-manager/traffic-manager-overview.md) permite controlar a distribuição do tráfego do usuário para pontos de extremidade do serviço em diferentes datacenters. Os pontos de extremidade de serviço com suporte no Gerenciador de Tráfego incluem VMs do Azure, Aplicativos Web e Serviços de Nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure. O Gerenciador de Tráfego usa o DNS (Sistema de Nome de Domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](../../traffic-manager/traffic-manager-routing-methods.md) e a integridade dos pontos de extremidade.

O Gerenciador de Tráfego oferece uma variedade de métodos de roteamento de tráfego para atender às necessidades de diferentes aplicativo, [monitoramento](../../traffic-manager/traffic-manager-monitoring.md) de integridade do ponto de extremidade e failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

### <a name="azure-load-balancer"></a>Azure Load Balancer

O [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) oferece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre as instâncias de serviço íntegras definidas em um conjunto de balanceadores de carga. O Azure Load Balancer pode ser configurado para:

- Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga público](../../load-balancer/components.md#frontend-ip-configurations).

- Balanceie o tráfego de carga entre as máquinas virtuais em uma rede virtual, entre as máquinas virtuais nos serviços de nuvem ou entre os computadores locais e as máquinas virtuais em uma rede virtual entre as instalações. Essa configuração é conhecida como [balanceamento de carga interno](../../load-balancer/components.md#frontend-ip-configurations).

- Encaminhe o tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS interno

Você pode gerenciar a lista de servidores DNS usados em uma VNet no Portal de Gerenciamento ou no arquivo de configuração de rede. O cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se os servidores DNS do cliente estão listados na ordem correta para o ambiente de seu cliente. As listas de servidores DNS não funcionam em round robin. Elas são usadas na ordem em que foram especificadas. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS independentemente de ele estar funcionando corretamente. Para alterar a ordem de servidor DNS para a rede virtual de seu cliente, remova os servidores DNS da lista e adicione-os na ordem desejada pelo cliente. O DNS oferece suporte ao aspecto de disponibilidade da tríade de segurança "CIA".

### <a name="azure-dns"></a>DNS do Azure

O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. [DNS do Azure](../../dns/dns-overview.md) é um serviço de hospedagem para domínios DNS, que fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS oferece suporte ao aspecto de disponibilidade da tríade de segurança "CIA".

### <a name="azure-monitor-logs-nsgs"></a>Logs de Azure Monitor NSGs

Você pode habilitar as seguintes categorias de log de diagnóstico para NSGs:

- Evento: contém entradas para as regras NSG que são aplicadas às VMs e funções de instância com base no endereço MAC. O status para essas regras é coletado a cada 60 segundos.

- Contador de regras: contém entradas de quantas vezes cada regra NSG é aplicada para negar ou permitir tráfego.

### <a name="security-center"></a>Central de Segurança

A [central de segurança do Azure](../../security-center/security-center-introduction.md) analisa continuamente o estado de segurança de seus recursos do Azure para práticas recomendadas de segurança de rede. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria [recomendações](../../security-center/security-center-recommendations.md) que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

## <a name="compute"></a>Computação
A seção fornece outras informações sobre os principais recursos nessa área e informações de resumo sobre esses recursos.

### <a name="antimalware--antivirus"></a>Antimalware e antivírus
Com o Azure IaaS, você pode usar o software antimalware dos fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky, para proteger suas máquinas virtuais contra arquivos maliciosos, adware e outras ameaças. O [Microsoft Antimalware](antimalware.md) para Serviços de Nuvem e Máquinas Virtuais do Azure é uma funcionalidade de proteção que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure. O Microsoft Antimalware também podem ser implantado usando a Central de Segurança do Azure

### <a name="hardware-security-module"></a>Módulos de segurança de hardware
A autenticação e a criptografia não melhoram a segurança, a menos que as próprias chaves estejam bem protegidas. Você pode simplificar o gerenciamento e a segurança de seus segredos e chaves críticos armazenando-os em [Azure Key Vault](../../key-vault/general/overview.md). O Key Vault oferece a opção de armazenar as chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Suas chaves de criptografia do SQL Server para backup ou [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) podem ser armazenadas no Cofre de Chaves com quaisquer chaves ou segredos dos seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados pelo [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Backup de máquinas virtuais
O [Backup do Azure](../../backup/backup-overview.md) é uma solução que protege os dados do seu aplicativo com zero investimento de capital e custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos, o que pode causar problemas de segurança. Com o Backup do Azure, suas máquinas virtuais executando Windows e Linux estão protegidas.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia de [BCDR (continuidade dos negócios/recuperação de desastre)](../../best-practices-availability-paired-regions.md) de sua organização é descobrir como manter as cargas de trabalho corporativas e aplicativos em execução durante interrupções planejadas e não planejadas. O [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ajuda a orquestrar a replicação, failover e recuperação dos aplicativos e cargas de trabalho para que eles estejam disponíveis a partir de um local secundário, caso o local principal fique inativo.

### <a name="sql-vm-tde"></a>TDE de VM do SQL
TDE (Transparent Data Encryption) e CLE (criptografia de nível de coluna) são recursos de criptografia do SQL Server. Essa forma de criptografia exige que os clientes gerenciem e armazenem as chaves criptográficas usadas para a criptografia.

O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O SQL Server Connector permite que o SQL Server use essas chaves do Azure Key Vault.

Se você estiver executando SQL Server com computadores locais, há etapas que podem ser seguidas para acessar Azure Key Vault da instância SQL Server local. Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso Integração do Azure Key Vault. Com alguns cmdlets do Azure PowerShell para habilitar esse recurso, você poderá automatizar a configuração necessária para que uma VM do SQL acesse seu cofre da chave.

### <a name="vm-disk-encryption"></a>Criptografia de disco da VM
[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) é um novo recurso que ajuda a criptografar seus discos de máquina virtual IaaS Windows e Linux. Ele aplica o recurso BitLocker do Windows padrão do setor e o recurso DM-Crypt do Linux para fornecer uma criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura de Key Vault. A solução também garante que todos os dados em discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

### <a name="virtual-networking"></a>Rede Virtual
As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais sejam conectadas a uma Rede Virtual do Azure. Uma Rede Virtual do Azure é um constructo lógico criado na malha de rede física do Azure. Cada [Rede Virtual do Azure](../../virtual-network/virtual-networks-overview.md) lógica é isolada das todas as outras Redes Virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego da rede nas implantações não seja acessível para os outros clientes do Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de patch
As atualizações de patch fornecem a base para encontrar e corrigir problemas em potencial e simplificam o processo de gerenciamento de atualizações de software, tanto reduzindo o número de atualizações de software que você deve implantar em sua empresa quanto aumentando a capacidade de monitorar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gerenciamento de política de segurança e emissão de relatórios
A [central de segurança](../../security-center/security-center-introduction.md) ajuda você a prevenir, detectar e responder a ameaças, além de oferecer maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso
A proteção de sistemas, aplicativos e dados começa com controles de acesso baseados em identidade. Os recursos de gerenciamento de identidades e acesso integrados aos produtos e serviços comerciais da Microsoft ajudam a proteger as informações pessoais e corporativas contra o acesso não autorizado, mas as disponibilizam aos usuários legítimos, quando e onde eles precisarem.

### <a name="secure-identity"></a>Proteção da identidade
A Microsoft usa várias tecnologias e práticas de segurança em seus produtos e serviços para gerenciar a identidades e o acesso.

- A [Autenticação Multifator](https://azure.microsoft.com/services/multi-factor-authentication/) exige que os usuários usem vários métodos para obter acesso, localmente e na nuvem. Ela fornece uma autenticação forte com uma gama de opções de verificação simples, e proporciona ao usuários um processo de logon simples.

- O [Microsoft Authenticator](https://aka.ms/authenticator) fornece uma experiência simples de Autenticação Multifator que funciona com o Microsoft Azure Active Directory e com contas da Microsoft, além de incluir suporte para itens vestíveis e aprovações com base em impressão digital.

- A [Aplicação de políticas de senha](../../active-directory/authentication/concept-sspr-policy.md) aumenta a segurança de senhas tradicionais impondo requisitos de comprimento e complexidade, rotação periódica forçada e bloqueio de conta depois de falhas nas tentativas de autenticação.

- A [Autenticação baseada em token](../../active-directory/develop/authentication-vs-authorization.md) permite autenticação via Azure Active Directory.

- O Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/built-in-roles.md) permite que você conceda acesso com base na função atribuída do usuário, facilitando a concessão dos usuários apenas da quantidade de acesso de que eles precisam para realizar suas tarefas de trabalho. Você pode personalizar o Azure RBAC de acordo com o modelo de negócios e a tolerância a riscos de sua organização.

- O [Gerenciamento de identidade integrado (identidade híbrida)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) permite que você mantenha o controle do acesso dos usuários em data centers internos e plataformas de nuvem, criando uma identidade de usuário único para autenticação e autorização para todos os recursos.

### <a name="secure-apps-and-data"></a>Aplicativos e dados seguros
O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é uma solução abrangente de gerenciamento de identidade e acesso na nuvem, ajuda a proteger o acesso aos seus dados em aplicativos locais e na nuvem e simplifica o gerenciamento de usuários e grupos. Ele combina os principais serviços de diretório, controle de identidade avançado, segurança e gerenciamento de acesso ao aplicativo, facilitando para os desenvolvedores a compilação do gerenciamento de identidade baseado em políticas em seus aplicativos. Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as edições do Azure Active Directory Basic, Premium P1 e Premium P2.

| Recursos gratuitos/comuns     | Recursos básicos    |Recursos do Premium P1 |Recursos do Premium P2 | Ingresso do Active Directory do Azure - apenas para recursos relacionados ao Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório](../../active-directory/fundamentals/active-directory-whatis.md),    [Gerenciamento de usuário/grupo (adicionar/atualizar/excluir)/provisionamento baseado em usuário, registro de dispositivo](../../active-directory/fundamentals/active-directory-whatis.md),  [SSO (Sign-On único)](../../active-directory/fundamentals/active-directory-whatis.md),     [alteração de senha de autoatendimento para usuários de nuvem](../../active-directory/fundamentals/active-directory-whatis.md),     [conectar (mecanismo de sincronização que estende diretórios locais para Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md),     [relatórios de segurança/uso](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Gerenciamento/provisionamento de acesso baseado em grupo](../../active-directory/fundamentals/active-directory-whatis.md), [redefinição de senha de autoatendimento para usuários de nuvem](../../active-directory/fundamentals/active-directory-whatis.md),  [identidade visual da empresa (personalização de páginas de logon/painel de acesso)](../../active-directory/fundamentals/active-directory-whatis.md),    [proxy de aplicativo](../../active-directory/fundamentals/active-directory-whatis.md),    [SLA 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  [Gerenciamento de grupo e aplicativo de autoatendimento/adições de aplicativo de autoatendimento/grupos dinâmicos](../../active-directory/fundamentals/active-directory-whatis.md),   [redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local](../../active-directory/fundamentals/active-directory-whatis.md),    [autenticação multifator (nuvem e local (servidor MFA))](../../active-directory/fundamentals/active-directory-whatis.md), [mim Cal + servidor mim](../../active-directory/fundamentals/active-directory-whatis.md),     [Cloud app Discovery](../../active-directory/fundamentals/active-directory-whatis.md),  [conexão de integridade](../../active-directory/fundamentals/active-directory-whatis.md),   [substituição automática de senha para contas de grupo](../../active-directory/fundamentals/active-directory-whatis.md)|   [Proteção de identidade](../../active-directory/identity-protection/overview-identity-protection.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Ingressar um dispositivo no Azure AD, desktop SSO, Microsoft Passport para Azure AD, recuperação do administrador do BitLocker](../../active-directory/fundamentals/active-directory-whatis.md),    [registro automático do MDM, Self-Service recuperação do BitLocker, administradores locais adicionais para dispositivos Windows 10 por meio do ingresso no Azure ad](../../active-directory/fundamentals/active-directory-whatis.md)|

- O [Cloud App Discovery](/cloud-app-security/set-up-cloud-discovery) é um recurso premium do Azure Active Directory que permite identificar os aplicativos em nuvem usados pelos funcionários em sua organização.

- O [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é um serviço de segurança que usa Azure Active Directory recursos de detecção de anomalias para fornecer uma visão consolidada das detecções de risco e possíveis vulnerabilidades que podem afetar as identidades da sua organização.

- O [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite ingressar VMs do Azure em um domínio sem precisar implantar controladores de domínio. Os usuários entram nessas VMs usando suas credenciais corporativas do Active Directory e podem acessar tranquilamente os recursos.

- O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade global, altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades e ser integrado em várias plataformas móveis e da Web. Seus clientes podem entrar em todos os seus aplicativos por meio de experiências personalizáveis que usam contas de mídia social existentes, ou você pode criar novas credenciais autônomas.

- A [Colaboração B2B do Azure Active Directory](../../active-directory/external-identities/what-is-b2b.md) é uma solução de integração de parceiro seguro que dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos e dados corporativos usando suas identidades autogerenciadas.

- O [Azure Active Directory Unido](../../active-directory/devices/overview.md) permite que você estenda recursos de nuvem para dispositivos Windows 10 para gerenciamento centralizado. Ele permite que os usuários se conectem à nuvem corporativa ou organizacional por meio do Azure Active Directory e simplifica o acesso a aplicativos e recursos.

- [Proxy de Aplicativo do Azure Active Directory](../../active-directory/manage-apps/application-proxy.md) fornece SSO e acesso remoto seguro para aplicativos da Web hospedados no local.

## <a name="next-steps"></a>Próximas etapas

- Entenda sua [responsabilidade compartilhada na nuvem](shared-responsibility.md).

- Saiba como a [central de segurança do Azure](../../security-center/security-center-introduction.md) pode ajudá-lo a prevenir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança de seus recursos do Azure.