---
title: Introdução à Segurança do Azure | Microsoft Docs
description: Saiba mais sobre a Segurança do Azure, seus serviços e como ela funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: ed57d72d32ba82a37036c9af77590bd4e93db8d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610502"
---
# <a name="introduction-to-azure-security"></a>Introdução à Segurança do Azure
## <a name="overview"></a>Visão geral
Sabemos que a segurança é o primeiro trabalho na nuvem e o quanto é importante que você encontre informações precisas e atualizadas sobre a segurança do Azure. Uma das melhores razões para usar o Azure para seus aplicativos e serviços é aproveitar sua ampla variedade de ferramentas e recursos de segurança. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure. O Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo que também permite uma responsabilidade transparente.

Para ajudar você a entender melhor a coleção de controles de segurança implementada no Microsoft Azure das perspectivas do cliente e das operações da Microsoft, este white paper, “Introdução à Segurança do Azure”, foi elaborado para fornecer uma visão abrangente da segurança disponível com o Microsoft Azure.

### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração com o Docker, criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js e criar back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e nas quais confiam. Ao se basear ou migrar ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente e fornece uma base confiável com a qual as empresas podem atender aos seus requisitos de segurança.

Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, de forma que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização. Este documento ajuda você a entender como as funcionalidades de segurança do Azure podem ajudá-lo a atender a esses requisitos.

> [!Note]
> O foco principal deste documento são os controles voltados para o cliente que você pode usar para personalizar e aumentar a segurança de seus aplicativos e serviços.
>
> Fornecemos algumas informações gerais, mas para obter informações detalhadas sobre como a Microsoft protege a própria plataforma do Azure, confira as informações fornecidas na [Central de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Resumo
Inicialmente, as migrações de nuvem pública eram orientadas pelas economias de custo e agilidade de inovação. Durante algum tempo, a segurança era considerada uma grande preocupação, e até mesmo uma estraga-prazeres para a migração na nuvem pública. No entanto, a segurança da nuvem pública passou de uma grande preocupação para um dos impulsionadores da migração na nuvem. A lógica por trás disso é a capacidade superior dos grandes provedores de serviços de nuvem pública de proteger aplicativos e os dados de ativos com base em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar simultaneamente milhões de clientes e fornecer uma base confiável com a qual as empresas podem atender às suas necessidades de segurança. Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, para que você possa personalizar a segurança de forma a atender aos requisitos específicos de suas implantações a fim de atender às suas políticas de controle de TI e aderir aos regulamentos externos.

Este documento descreve a abordagem da Microsoft com relação à segurança dentro da plataforma de nuvem do Microsoft Azure:
* Recursos de segurança implementados pela Microsoft para proteger a infraestrutura do Azure, dados de clientes e aplicativos.
* Os serviços e recursos de segurança do Azure disponíveis para gerenciar a Segurança dos Serviços e seus dados dentro de suas assinaturas do Azure.

## <a name="summary-azure-security-capabilities"></a>Resumo dos recursos de segurança do Azure
A tabela a seguir fornece uma breve descrição dos recursos de segurança implementados pela Microsoft para proteger a infraestrutura do Azure, dados de clientes e aplicativos seguros.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Recursos de segurança implementados para proteger a plataforma do Azure:
Examine os recursos listados a seguir para garantir que a Plataforma do Azure seja gerenciada de forma segura. Os links foram fornecidos para detalhar ainda mais a forma como a Microsoft aborda perguntas de confiança dos clientes em quatro áreas: Plataforma Segura, Privacidade e Controles, Conformidade e Transparência.


| [Plataforma Segura](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Privacidade e Controles](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Conformidade](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparência](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo de Desenvolvimento de Segurança](https://www.microsoft.com/en-us/sdl/), auditorias internas | [Gerenciar sempre seus dados](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Central de Confiabilidade](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Treinamento de segurança obrigatório, verificações em segundo plano](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Controlar o local dos dados](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Hub de Controles Comuns](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Como a Microsoft gerencia o local de dados nos serviços do Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Testes de penetração](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [Detecção de intrusão, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [Auditorias e registro em log](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Fornecer acesso a dados em seus próprios termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Lista de verificação de inspeção dos Serviços de Nuvem ](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Quem na Microsoft pode acessar seus dados e em quais termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Centro de dados de última geração](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), a segurança física, [rede segura](https://docs.microsoft.com/azure/security/security-network-overview) | [Respondendo às autoridades competentes](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformidade por serviço, local e setor](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Resposta ao incidente de segurança](https://aka.ms/SecurityResponsepaper), [Responsabilidade compartilhada](https://aka.ms/sharedresponsibility) |[Padrões de privacidade rigorosos](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Revisar a certificação para serviços do Azure, Hub de transparência](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Recursos de segurança oferecidos pelo Azure para proteger dados e aplicativos
Dependendo do modelo de serviço de nuvem, a responsabilidade de quem gerencia a segurança do aplicativo ou serviço varia. Há recursos disponíveis na Plataforma Azure para ajudar você a cumprir essas responsabilidades por meio de recursos internos, e soluções de parceiros que podem ser implantadas em uma assinatura do Azure.

Os recursos internos são organizados em seis (6) áreas funcionais: Operações, aplicativos, armazenamento, rede, computação e identidade. Os detalhes adicionais sobre os recursos e funcionalidades disponíveis na Plataforma do Azure nessas seis (6) áreas são fornecidos por meio de informações de resumo.

## <a name="operations"></a>Operações
Esta seção fornece outras informações sobre os principais recursos em operações de segurança, e informações de resumo sobre esses recursos.

### <a name="security-and-audit-dashboard"></a>Painel Segurança e Auditoria
A [solução de Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma exibição abrangente da postura de segurança de TI de sua organização com [consultas de pesquisa internas](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) para problemas importantes que exigem atenção. O [segurança e auditoria](https://technet.microsoft.com/library/mt484091.aspx) painel é a tela inicial para tudo relacionado à segurança nos logs do Azure Monitor. Ela fornece uma análise de alto nível do estado de segurança de seus computadores. Também inclui a capacidade de exibir todos os eventos das últimas 24 horas, dos últimos sete dias ou de qualquer outro intervalo de tempo personalizado.

Além disso, você pode configurar a Segurança e Conformidade para [realizar automaticamente ações específicas](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando um evento específico for detectado.

### <a name="azure-resource-manager"></a>Azure Resource Manager
O [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) permite trabalhar com os recursos da sua solução como um grupo. Você pode implantar, atualizar ou excluir todos os recursos da sua solução em uma única operação coordenada. Use um [modelo do Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) para a implantação, e esse modelo pode ser útil para diferentes ambientes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

As implantações baseadas em modelos do Azure Resource Manager ajudam a melhorar a segurança das soluções implantadas no Azure devido às configurações de controle de segurança padrão, e podem ser integradas às implantações baseadas em modelo padronizadas. Isso reduz o risco de erros de configuração de segurança que podem ocorrer durante as implantações manuais.

### <a name="application-insights"></a>Application Insights
O [Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de Gerenciamento de Desempenho de Aplicativos (APM) extensível para desenvolvedores da Web. Com o Application Insights, você pode monitorar seus aplicativos Web ativos e detectar automaticamente as anomalias no desempenho. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele monitora seus aplicativos em todo o tempo de execução, tanto durante o teste quanto depois de publicado ou implantado.

O Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia você tem mais usuários, o nível de capacidade de resposta do aplicativo e quão bem ele é atendido por quaisquer serviços externos dos quais depende.

Se houver travamentos, falhas ou problemas de desempenho, você pode pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço enviará a você emails se houver alterações na disponibilidade e no desempenho de seu aplicativo. Assim, o Application Insight torna-se uma ferramenta de segurança importante, pois ajuda com a disponibilidade na tríade de segurança de disponibilidade, integridade e confidencialidade.

### <a name="azure-monitor"></a>Azure Monitor
O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferece a visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure ([Logs de Atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) e de cada recurso individual do Azure( [Logs de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Use o Azure Monitor para receber alertas sobre eventos relacionados à segurança que são gerados nos logs do Azure.

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor
[Os logs do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics/) – fornece uma solução de gerenciamento de TI para locais e de terceiros baseados em nuvem infraestrutura de (como o AWS), além dos recursos do Azure. Dados do Azure Monitor podem ser roteados diretamente para os logs do Azure Monitor, você pode ver as métricas e logs de todo o ambiente em um só lugar.

Os logs do Azure Monitor podem ser uma ferramenta útil na análise forense e a segurança, como a ferramenta permite que você pesquise rapidamente grandes quantidades de entradas relacionadas à segurança com uma abordagem de consulta flexível. Além disso, no local [logs de firewall e proxy podem ser exportados para o Azure e disponibilizados para análise usando os logs do Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Assistente do Azure
O [Assistente do Azure](https://docs.microsoft.com/azure/advisor/) é um consultor de nuvem personalizado que ajuda você a otimizar suas implantações do Azure. Ele analisa a configuração dos recursos e a telemetria do uso. Depois, recomenda soluções para ajudar a melhorar o [desempenho](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [segurança](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) e [alta disponibilidade](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) de seus recursos enquanto procura oportunidades para [reduzir a despesa geral do Azure](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). O Assistente do Azure fornece recomendações de segurança, o que pode melhorar consideravelmente sua postura de segurança geral para soluções implantadas no Azure. Essas recomendações são obtidas da análise de segurança executada pela [Central de Segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Central de Segurança do Azure
A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

Além disso, a Central de Segurança do Azure ajuda com operações de segurança, fornecendo um único painel que mostra alertas e recomendações para ação imediata. Geralmente, você pode corrigir problemas com um único clique dentro do console da Central de Segurança do Azure.
## <a name="applications"></a>Aplicativos
A seção fornece outras informações sobre os principais recursos em segurança do aplicativo e informações de resumo sobre esses recursos.

### <a name="web-application-vulnerability-scanning"></a>Verificação de vulnerabilidades de aplicativos Web
Uma das maneiras mais fáceis de começar a usar testes de vulnerabilidades no [aplicativo Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview) é usar a [integração ao Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para executar o exame de vulnerabilidades com um clique no aplicativo. Você pode exibir os resultados do teste em um relatório de fácil compreensão e aprender como corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Teste de penetração
Se preferir realizar seus próprios testes de penetração ou se desejar usar outro pacote de scanners ou provedor, você deverá seguir o [processo de aprovação do teste de penetração do Azure](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) e obter aprovação prévia para realizar os testes de penetração desejados.

### <a name="web-application-firewall"></a>Firewall do aplicativo Web
O WAF (firewall de aplicativo Web) no [Gateway de Aplicativo do Azure](https://azure.microsoft.com/services/application-gateway/) protege os aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de scripts entre sites e sequestros de sessão. Ele vem pré-configurado com proteção contra as ameaças identificadas pelo [OWASP (Projeto Aberto de Segurança em Aplicativo Web) como as 10 vulnerabilidades mais comuns](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicativo do Azure
A [Autenticação/Autorização do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) é um recurso que oferece uma maneira para seu aplicativo conectar usuários de forma que você não precise alterar o código no back-end do aplicativo. Ele fornece uma maneira fácil de proteger o aplicativo e trabalhar com dados por usuário.

### <a name="layered-security-architecture"></a>Arquitetura de segurança em camadas
Como os [Ambientes do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) fornecem um ambiente de tempo de execução isolado implantado em uma [Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), os desenvolvedores podem criar uma arquitetura de segurança em camadas fornecendo níveis diferentes de acesso à rede para cada camada de aplicativo. Um desejo comum é ocultar os back-ends de API do acesso à Internet geral e só permitir que as APIs sejam chamadas por aplicativos Web upstream. Os [NSGs (grupos de segurança de rede)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) podem ser usados em sub-redes da Rede Virtual do Azure contendo Ambientes do Serviço de Aplicativo para restringir o acesso público aos aplicativos da API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnóstico de servidor Web e diagnóstico de aplicativos
Os aplicativos Web do Serviço de Aplicativo oferecem funcionalidade de diagnóstico para registro em log tanto de informações do servidor Web quanto do aplicativo Web. Estes estão logicamente separados em [diagnóstico de servidor Web](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) e [diagnóstico de aplicativos](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). O servidor Web inclui dois grandes avanços em diagnóstico e solução de problemas de sites e aplicativos.

O primeiro são informações de estado em tempo real sobre pools de aplicativos, processos de trabalho, sites, domínios de aplicativo e solicitações em execução. O segundo são os eventos de rastreamento detalhados que rastreiam uma solicitação por todo o processo de solicitação e resposta.

Para habilitar a coleta desses eventos de rastreamento, o IIS 7 pode ser configurado para capturar automaticamente os logs de rastreamento completos, em formato XML, para qualquer solicitação específica com base no tempo decorrido ou em códigos de resposta do erro.

#### <a name="web-server-diagnostics"></a>Diagnóstico de servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

-   Registro em Log Detalhado de Erros - informações detalhadas de erros para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.

-   Falha no Rastreamento de Solicitação - informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. Isto pode ser útil se você está tentando melhorar o desempenho do site ou isolar o que está causando o retorno de um erro específico de HTTP.

-   Registro em Log de Servidor Web - informações sobre transações HTTP usando o formato de arquivo de log estendido W3C. Este é útil para determinar as métricas gerais do site, como o número de solicitações manipuladas e quantas solicitações existem vindas de um endereço IP específico.

#### <a name="application-diagnostics"></a>Diagnóstico de aplicativo
O [Diagnóstico de aplicativo](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe [Rastreamento.de.Diagnóstico.de.Sistema](https://msdn.microsoft.com/library/system.diagnostics.trace) para registrar informações no log de diagnóstico do aplicativo. No Application Diagnostics, há dois tipos principais de eventos: aqueles relacionados ao desempenho do aplicativo e aqueles relacionados a erros e falhas do aplicativo. As falhas e erros podem ser divididos em problemas de conectividade, segurança e de falha. Os problemas de falha normalmente estão relacionados a um problema com o código do aplicativo.

No Application Diagnostics, você pode ver os eventos agrupados destas maneiras:

-   Todos (exibe todos os eventos)
-   Erros do Aplicativo (exibe eventos de exceção)
-   Desempenho (exibe eventos de desempenho)

## <a name="storage"></a>Armazenamento
A seção fornece outras informações sobre os principais recursos em segurança de armazenamento do Azure e informações de resumo sobre esses recursos.

### <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Você pode proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função). Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função de RBAC apropriada a grupos e aplicativos em determinado escopo. Você pode usar [funções RBAC internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), como Colaborador da Conta de Armazenamento, para atribuir privilégios aos usuários. O acesso às chaves de armazenamento para uma conta de armazenamento usando o [modelo Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) pode ser controlado por meio do RBAC (Controle de Acesso Baseado em Função).

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado
Uma [SAS (Assinatura de Acesso Compartilhado)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) fornece acesso delegado aos recursos da sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado período e com um conjunto específico de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:
-   [Criptografia de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), como HTTPS ao transferir dados dentro ou fora do Armazenamento do Azure.

-   [Criptografia na transmissão](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), como a [criptografia SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) para [compartilhamentos de Arquivo do Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Criptografia do cliente, que permite criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografá-los após serem transferidos para fora do armazenamento.

### <a name="encryption-at-rest"></a>Criptografia em repouso
Para muitas organizações, a criptografia de dados em repouso é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. Há três recursos de segurança de armazenamento do Azure que fornecem criptografia de dados que estão “em repouso”:

-   [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure.

-   [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) também fornece o recurso de criptografia em repouso.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento
A [Análise de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) executa registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento. A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. Os seguintes tipos de solicitações autenticadas são registrados:
-   Solicitações bem sucedidas.

-   Solicitações com falha, incluindo o tempo limite, limitação, rede, autorização e outros erros.

-   Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha.

-   Solicitações para dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>Habilitar clientes com base no navegador usando CORS
[CORS (Compartilhamento de recursos entre origens)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que permite que os domínios troquem permissões para acessar recursos uns dos outros. O Agente do Usuário envia cabeçalhos adicionais para garantir que o código JavaScript carregado de um determinado domínio tenha permissão para acessar os recursos localizados em outro domínio. Depois, o último domínio responde com cabeçalhos adicionais, permitindo ou negando o acesso do domínio original aos seus recursos.

Agora, os serviços de armazenamento do Azure oferecem suporte a CORS, para que depois de definir as regras de CORS para o serviço, uma solicitação autenticada corretamente feita no serviço de um domínio diferente será avaliada para determinar se é permitida de acordo com as regras que você especificou.
## <a name="networking"></a>Rede
A seção fornece outras informações sobre os principais recursos em segurança de rede do Azure e informações de resumo sobre esses recursos.

### <a name="network-layer-controls"></a>Controles de camada de rede
O controle de acesso à rede é o ato de limitar a conectividade de entrada ou saída de sub-redes ou dispositivos específicos e representa o aspecto fundamental da segurança de rede. O objetivo do controle de acesso à rede é certificar-se de que suas máquinas virtuais e seus serviços são acessíveis apenas aos usuários e dispositivos para os quais você deseja que tenham esse acesso.

#### <a name="network-security-groups"></a>Grupos de segurança de rede
Um [NSG (Grupo de Segurança de Rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) é um firewall básico de filtragem de pacotes com estado e permite o controle do acesso baseado em uma sequência de [5 tuplas](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção da camada de aplicativo nem controles de acesso autenticado. Eles podem ser usados para controlar o tráfego entre sub-redes dentro de uma Rede Virtual do Azure e o tráfego entre uma Rede Virtual do Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado
A capacidade de controlar o comportamento de roteamento em suas Redes Virtuais do Azure é uma funcionalidade crítica de controle de acesso e segurança de rede. Por exemplo, se você quiser ter certeza de que todo o tráfego de entrada e saída da Rede Virtual do Azure passa por esse dispositivo de segurança virtual, será necessário conseguir controlar e personalizar o comportamento do roteamento. É possível fazer isso configurando as Rotas Definidas pelo Usuário no Azure.

As [Rotas Definidas pelo Usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitem que você personalize os caminhos de entrada e saída de máquinas virtuais individuais ou sub-redes a fim de garantir a rota mais segura possível. [túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode ser usado para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet.

Isso é diferente de poder aceitar conexões de entrada e responder a elas. Os servidores Web front-end precisam responder à solicitação dos hosts da Internet e, portanto, o tráfego originado da Internet tem permissão de entrada nesses servidores Web, que, por sua vez, podem responder.

O túnel forçado é normalmente usado para forçar o tráfego de saída para a Internet a fim de passar por firewalls e proxies de segurança locais.

#### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual
Embora os Grupos de Segurança de Rede, as Rotas Definidas pelo Usuário e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), em algumas ocasiões você pode querer habilitar a segurança em níveis superiores da pilha. É possível acessar esses recursos avançados de segurança de rede por meio de uma solução de dispositivo de segurança de rede de parceiro do Azure. Você pode encontrar as soluções mais atuais de segurança de rede de parceiros do Azure visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e pesquisando por “segurança” e “segurança de rede”.

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da malha de rede do Azure dedicada à sua assinatura. Você pode controlar os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas na rede. Você pode segmentar a VNet em sub-redes e colocar as VMs (máquinas virtuais) de IaaS do Azure e/ou os [Serviços de nuvem (instâncias de função de PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) em Redes Virtuais do Azure.

Além disso, você pode conectar a rede virtual à sua rede local usando uma das [opções de conectividade](https://docs.microsoft.com/azure/vpn-gateway/) disponíveis no Azure. Em linhas gerais, você pode expandir sua rede no Azure, com controle total sobre os blocos de endereços IP, com benefícios de escala empresarial proporcionados pelo Azure.

A rede do Azure dá suporte a vários cenários de acesso remoto seguro. Entre eles estão:

-   [Conectar estações de trabalho individuais a uma Rede Virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Conectar a rede local a uma Rede Virtual do Azure com uma VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Conectar a rede local a uma Rede Virtual do Azure com uma conexão WAN dedicada](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Conectar Redes Virtuais do Azure entre si](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>Gateway de VPN
Para enviar o tráfego de rede entre sua Rede Virtual do Azure e seu site local, será necessário criar um gateway de VPN para sua Rede Virtual do Azure. Um [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você também pode usar gateways de VPN para enviar o tráfego entre as Redes Virtuais do Azure pela malha de rede do Azure.

### <a name="express-route"></a>ExpressRoute
O Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) é uma conexão WAN dedicada que permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização.

As conexões de ExpressRoute não passam pela Internet pública e, portanto, podem ser consideradas mais seguras do que soluções de VPN. Isso permite que as conexões de ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.


### <a name="application-gateway"></a>Gateway de Aplicativo
O [Gateway de Aplicativo do Microsoft  Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) fornece um [ADC (Controlador de Entrega de Aplicativos)](https://en.wikipedia.org/wiki/Application_delivery_controller) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o aplicativo.

![Gateway de Aplicativo](./media/azure-security/azure-security-fig2.png)

Ele permite que você otimize a produtividade do Web farm descarregando a terminação SSL com uso intensivo de CPU para o Gateway de Aplicativo (também conhecido como “descarregamento de SSL” ou “ponte SSL”). Ele também fornece outros recursos de roteamento de Camada 7, incluindo distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e a capacidade de hospedar vários sites por trás de um único Gateway de Aplicativo baseado em cookie. O Gateway de Aplicativo do Azure é um balanceador de carga de camada 7.

Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local.

O aplicativo fornece muitos recursos do Controlador de Entrega de Aplicativos (ADC), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell), as sondas de integridade personalizadas, suporte para vários sites e muitos outros.

### <a name="web-application-firewall"></a>Firewall do Aplicativo Web
O Firewall do aplicativo Web é um recurso do [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção para aplicativos Web que utilizam o gateway de aplicativo para as funções ADC (controle de entrega de aplicativos) padrão. O firewall do aplicativo Web faz isso protegendo-os contra a maioria das 10 vulnerabilidades mais comuns da Web segundo o OWASP.

![Firewall do Aplicativo Web](./media/azure-security/azure-security-fig1.png)

-   Proteção contra injeção de SQL

-   Proteção Contra Ataques Comuns da Web, como a injeção de comandos, as solicitações HTTP indesejadas, a divisão de resposta HTTP e o ataque de inclusão de arquivo remoto

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação

-   Prevenção contra bots, rastreadores e scanners

-   Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS etc.)


Um firewall do aplicativo Web para proteger contra ataques da Web simplifica muito o gerenciamento de segurança e oferece mais garantia para o aplicativo contra ameaças de invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com o firewall do aplicativo Web.
### <a name="traffic-manager"></a>Gerenciador de Tráfego
O [Gerenciador de Tráfego do Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) permite controlar a distribuição do tráfego do usuário para pontos de extremidade do serviço em diferentes datacenters. Os pontos de extremidade de serviço com suporte no Gerenciador de Tráfego incluem VMs do Azure, Aplicativos Web e Serviços de Nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure. O Gerenciador de Tráfego usa o DNS (Sistema de Nome de Domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e a integridade dos pontos de extremidade.

O Gerenciador de Tráfego oferece uma variedade de métodos de roteamento de tráfego para atender às necessidades de diferentes aplicativo, [monitoramento](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) de integridade do ponto de extremidade e failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.
### <a name="azure-load-balancer"></a>Azure Load Balancer
O [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) oferece alta disponibilidade e desempenho de rede para seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre as instâncias de serviço íntegras definidas em um conjunto de balanceadores de carga. O Azure Load Balancer pode ser configurado para:

-   Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga voltada para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Balanceie o tráfego de carga entre as máquinas virtuais em uma rede virtual, entre as máquinas virtuais nos serviços de nuvem ou entre os computadores locais e as máquinas virtuais em uma rede virtual entre as instalações. Essa configuração é conhecida como [balanceamento de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Encaminhe o tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS interno
Você pode gerenciar a lista de servidores DNS usados em uma VNet no Portal de Gerenciamento ou no arquivo de configuração de rede. O cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se os servidores DNS do cliente estão listados na ordem correta para o ambiente de seu cliente. As listas de servidores DNS não funcionam em round robin. Elas são usadas na ordem em que foram especificadas. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS independentemente de ele estar funcionando corretamente. Para alterar a ordem de servidor DNS para a rede virtual de seu cliente, remova os servidores DNS da lista e adicione-os na ordem desejada pelo cliente. O DNS oferece suporte ao aspecto de disponibilidade da tríade de segurança "CIA".

### <a name="azure-dns"></a>DNS do Azure
O [sistema de nomes de domínio](https://technet.microsoft.com/library/bb629410.aspx), ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. O DNS oferece suporte ao aspecto de disponibilidade da tríade de segurança "CIA".
### <a name="azure-monitor-logs-nsgs"></a>O Azure Monitor registra NSGs
Você pode habilitar as seguintes categorias de log de diagnóstico para NSGs:
-   Evento: Contém entradas para as regras NSG que são aplicadas às VMs e funções de instância com base no endereço MAC. O status para essas regras é coletado a cada 60 segundos.

-   Contador de regras: Contém entradas de quantas vezes cada regra de NSG é aplicada para negar ou permitir tráfego.

### <a name="azure-security-center"></a>Central de Segurança do Azure
A Central de Segurança ajuda a prevenir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança. As recomendações da rede giram em torno de firewalls, Grupos de Segurança da Rede, configuração das regras do tráfego de entrada e muito mais.

As recomendações de rede disponíveis são as seguintes:

-   [Adicionar um firewall de próxima geração](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) Recomenda que você adicione um Firewall de Última Geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança

-   [Encaminhar o tráfego somente por NGFW](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) Recomenda que você configure regras para o grupo de segurança de rede (NSG) que forcem o tráfego de entrada em sua VM a passar pelo NGFW.

-   [Habilitar Grupos de Segurança de Rede em máquinas virtuais ou sub-redes](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) Recomenda que você habilite NSGs em sub-redes ou VMs.

-   [Restringir o acesso por meio do ponto de extremidade voltado para a Internet](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) Recomenda que você configure regras de tráfego de entrada para NSGs.


## <a name="compute"></a>Computação

A seção fornece outras informações sobre os principais recursos nessa área e informações de resumo sobre esses recursos.

### <a name="antimalware--antivirus"></a>Antimalware e antivírus
Com o Azure IaaS, você pode usar o software antimalware dos fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky, para proteger suas máquinas virtuais contra arquivos maliciosos, adware e outras ameaças. O [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Serviços de Nuvem e Máquinas Virtuais do Azure é uma funcionalidade de proteção que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure. O Microsoft Antimalware também podem ser implantado usando a Central de Segurança do Azure

### <a name="hardware-security-module"></a>Módulos de segurança de hardware
A autenticação e a criptografia não melhoram a segurança, a menos que as próprias chaves estejam bem protegidas. Você pode simplificar o gerenciamento e a segurança dos seus principais segredos e chaves armazenando-os no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). O Key Vault oferece a opção de armazenar as chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Suas chaves de criptografia do SQL Server para backup ou [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas no Cofre de Chaves com quaisquer chaves ou segredos dos seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados pelo [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Backup de máquinas virtuais
O [Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução que protege os dados do seu aplicativo com zero investimento de capital e custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos, o que pode causar problemas de segurança. Com o Backup do Azure, suas máquinas virtuais executando Windows e Linux estão protegidas.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia de [BCDR (continuidade dos negócios/recuperação de desastre)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) de sua organização é descobrir como manter as cargas de trabalho corporativas e aplicativos em execução durante interrupções planejadas e não planejadas. O [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, failover e recuperação dos aplicativos e cargas de trabalho para que eles estejam disponíveis a partir de um local secundário, caso o local principal fique inativo.

### <a name="sql-vm-tde"></a>TDE de VM do SQL
[TDE (Transparent Data Encryption)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) e CLE (criptografia de nível de coluna) são recursos de criptografia do SQL Server. Essa forma de criptografia exige que os clientes gerenciem e armazenem as chaves criptográficas usadas para a criptografia.

O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O SQL Server Connector permite que o SQL Server use essas chaves do Azure Key Vault.

Se você estiver executando o SQL Server em máquinas locais, existirão etapas a serem seguidas para acessar o Azure Key Vault do computador local com SQL Server. Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso Integração do Azure Key Vault. Com alguns cmdlets do Azure PowerShell para habilitar esse recurso, você poderá automatizar a configuração necessária para que uma VM do SQL acesse seu cofre da chave.

### <a name="vm-disk-encryption"></a>Criptografia de disco da VM
O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) é um novo recurso que ajuda a criptografar os discos de suas máquinas virtuais IaaS Windows e Linux. Ele aplica o recurso BitLocker do Windows padrão do setor e o recurso DM-Crypt do Linux para fornecer uma criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura de Key Vault. A solução também garante que todos os dados em discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

### <a name="virtual-networking"></a>Rede Virtual
As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais sejam conectadas a uma Rede Virtual do Azure. Uma Rede Virtual do Azure é um constructo lógico criado na malha de rede física do Azure. Cada [Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) lógica é isolada das todas as outras Redes Virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego da rede nas implantações não seja acessível para os outros clientes do Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de patch
As atualizações de patch fornecem a base para encontrar e corrigir problemas em potencial e simplificam o processo de gerenciamento de atualizações de software, tanto reduzindo o número de atualizações de software que você deve implantar em sua empresa quanto aumentando a capacidade de monitorar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gerenciamento de política de segurança e emissão de relatórios
A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda você a impedir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

### <a name="azure-security-center"></a>Central de Segurança do Azure
A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

A proteção de sistemas, aplicativos e dados começa com controles de acesso baseados em identidade. Os recursos de gerenciamento de identidades e acesso integrados aos produtos e serviços comerciais da Microsoft ajudam a proteger as informações pessoais e corporativas contra o acesso não autorizado, mas as disponibilizam aos usuários legítimos, quando e onde eles precisarem.

### <a name="secure-identity"></a>Proteção da identidade
A Microsoft usa várias tecnologias e práticas de segurança em seus produtos e serviços para gerenciar a identidades e o acesso.
-   A [Autenticação Multifator](https://azure.microsoft.com/services/multi-factor-authentication/) exige que os usuários usem vários métodos para obter acesso, localmente e na nuvem. Ela fornece uma autenticação forte com uma gama de opções de verificação simples, e proporciona ao usuários um processo de logon simples.

-   O [Microsoft Authenticator](https://aka.ms/authenticator) fornece uma experiência simples de Autenticação Multifator que funciona com o Microsoft Azure Active Directory e com contas da Microsoft, além de incluir suporte para itens vestíveis e aprovações com base em impressão digital.

-   A [Aplicação de políticas de senha](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) aumenta a segurança de senhas tradicionais impondo requisitos de comprimento e complexidade, rotação periódica forçada e bloqueio de conta depois de falhas nas tentativas de autenticação.

-   A [Autenticação baseada em token](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) permite autenticação via Azure Active Directory.

-   O [RBAC (Controle de acesso baseado em função)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) permite que você conceda acesso baseado na função atribuída do usuário, facilitando a concessão apenas do acesso necessário para os usuários realizarem seus trabalhos. Você pode personalizar o RBAC de acordo com o modelo de negócios e da tolerância a riscos de sua organização.

-   O [Gerenciamento de identidade integrado (identidade híbrida)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) permite que você mantenha o controle do acesso dos usuários em data centers internos e plataformas de nuvem, criando uma identidade de usuário único para autenticação e autorização para todos os recursos.

### <a name="secure-apps-and-data"></a>Aplicativos e dados seguros
O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é uma solução abrangente de gerenciamento de identidade e acesso na nuvem, ajuda a proteger o acesso aos seus dados em aplicativos locais e na nuvem e simplifica o gerenciamento de usuários e grupos. Ele combina os principais serviços de diretório, controle de identidade avançado, segurança e gerenciamento de acesso ao aplicativo, facilitando para os desenvolvedores a compilação do gerenciamento de identidade baseado em políticas em seus aplicativos. Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as edições do Azure Active Directory Basic, Premium P1 e Premium P2.

| Recursos gratuitos/comuns     | Recursos básicos    |Recursos do Premium P1 |Recursos do Premium P2 | Ingresso do Active Directory do Azure - apenas para recursos relacionados ao Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Gerenciamento de usuários/grupos (adicionar/atualizar/excluir)/Provisionamento baseado em usuário, Registro de dispositivos](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SSO (Logon Único)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Autoatendimento para alteração de senha para usuários de nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect (mecanismo de sincronização que estende os diretórios locais para o Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Relatórios de segurança/uso](https://docs.microsoft.com/azure/active-directory/active-directory-editions)       |     [Gerenciamento/provisionamento de acesso baseado em grupo](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Redefinição de senha por autoatendimento para usuários de nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Identidade visual da empresa (personalização de páginas de logon/painel de acesso)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Proxy de Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SLA de 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions) |  [Autoatendimento para gerenciamento de grupo e aplicativo/Autoatendimento para adições de aplicativos/Grupos dinâmicos](https://docs.microsoft.com/azure/active-directory/active-directory-editions),  [Autoatendimento para redefinição/alteração/desbloqueio de senha com write-back local](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Autenticação Multifator do Microsoft Azure (local e na nuvem [Servidor de MFA])](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MIM CAL + Servidor MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Substituição automática de senha para contas de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|  [Proteção de identidade](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Unir um dispositivo ao Azure Active Directory, SSO na Área de Trabalho, Microsoft Passport para Azure Active Directory, Recuperação do BitLocker pelo administrador](https://docs.microsoft.com/azure/active-directory/active-directory-editions),   [Registro automático de MDM, Autoatendimento para recuperação do BitLocker, Administradores locais adicionais para dispositivos com Windows 10 por meio do ingresso no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|


- O [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) é um recurso premium do Azure Active Directory que permite identificar os aplicativos em nuvem usados pelos funcionários em sua organização.

- O [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) é um serviço de segurança que usa recursos de detecção de anomalias do Azure Active Directory para fornecer uma visão consolidada sobre eventos de risco e possíveis vulnerabilidades que poderiam afetar as identidades de sua organização.

- O [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite ingressar VMs do Azure em um domínio sem precisar implantar controladores de domínio. Os usuários entram nessas VMs usando suas credenciais corporativas do Active Directory e podem acessar tranquilamente os recursos.

- O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade global, altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades e ser integrado em várias plataformas móveis e da Web. Seus clientes podem entrar em todos os seus aplicativos por meio de experiências personalizáveis que usam contas de mídia social existentes, ou você pode criar novas credenciais autônomas.

- A [Colaboração B2B do Azure Active Directory](https://aka.ms/aad-b2b-collaboration) é uma solução de integração de parceiro seguro que dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos e dados corporativos usando suas identidades autogerenciadas.

- O [Ingresso no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) permite que você estenda os recursos de nuvem para dispositivos com Windows 10 a fim de proporcionar um gerenciamento centralizado. Ele permite que os usuários se conectem à nuvem corporativa ou organizacional por meio do Azure Active Directory e simplifica o acesso a aplicativos e recursos.

- [Proxy de Aplicativo do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornece SSO e acesso remoto seguro para aplicativos da Web hospedados no local.

## <a name="next-steps"></a>Próximas etapas
- [Introdução à Segurança do Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Os serviços do Azure e os recursos que você pode usar para ajudar a proteger seus serviços e seus dados no Azure

- [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/)

Previna, detecte e responda a ameaças com maior visibilidade e controle sobre a segurança de seus recursos do Azure

- [Monitoramento da integridade de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Os recursos de monitoramento na Central de Segurança do Azure para monitorar a conformidade com as políticas.
