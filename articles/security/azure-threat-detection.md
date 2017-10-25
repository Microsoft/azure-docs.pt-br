---
title: "Detecção Avançada de Ameaças do Azure | Microsoft Docs"
description: "Saiba mais sobre a Proteção de Identidade e seus recursos."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: a7a1179f815395028c4d0324e2bfb6fbed77229c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-advanced-threat-detection"></a>Detecção Avançada de Ameaças do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Visão geral

A Microsoft desenvolveu uma série de White Papers, Visões Gerais de Segurança, Práticas Recomendadas e Listas de Verificação para ajudar os clientes do Azure a entender os diversos recursos de segurança disponíveis na Plataforma Azure e em suas proximidades. Os tópicos variam em termos de abrangência e profundidade e são atualizados periodicamente. Este documento integra uma série, conforme resumido na próxima seção de resumo.

### <a name="azure-platform"></a>Plataforma Azure

O Azure é uma plataforma de serviço de nuvem pública que dá suporte à mais ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos.
Ele oferece suporte às seguintes linguagens de programação:
-   Execute contêineres do Linux com a integração com o Docker.
-   Criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js
-   Crie back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e nas quais confiam.

Quando você estiver migrando para uma nuvem pública com uma organização, que a organização é responsável para proteger seus dados e fornecer o controle ao redor do sistema e segurança.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar simultaneamente milhões de clientes e fornecer uma base confiável com a qual as empresas podem atender às suas necessidades de segurança. O Azure fornece uma ampla gama de opções para configurar e personalizar a segurança para atender às necessidades de implantações de seu aplicativo. Este documento ajuda você a atender a esses requisitos.

### <a name="abstract"></a>Resumo

O Microsoft Azure oferece a funcionalidade de detecção avançada de ameaças interna por meio de serviços como o Azure Active Directory, Azure Operations Management Suite (OMS) e a Central de Segurança do Azure. Esta coleção de recursos e serviços de segurança fornece uma maneira simples e rápida de compreender o que está acontecendo em suas implantações do Azure.

Este white paper orientará você na "abordagens do Microsoft Azure" para diagnóstico de vulnerabilidade de ameaça e análise dos riscos associados às atividades mal-intencionadas direcionadas a servidores e outros recursos do Azure. Isso ajuda você a identificar os métodos de identificação e de gerenciamento de vulnerabilidade com soluções otimizadas pela plataforma Azure e serviços e tecnologias de segurança e tecnologias voltadas para o cliente.

Este white paper se concentra na tecnologia da Plataforma Azure e nos controles voltados para o cliente e não tenta solucionar questões de SLAs, modelos de preço e considerações de prática de DevOps.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um recurso da edição [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) que oferece uma visão geral dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. A Microsoft tem protegido identidades baseadas em nuvem há mais de uma década e, com o Azure AD Identity Protection, ela está disponibilizando esses mesmos sistemas de proteção para os clientes empresariais. O Identity Protection usa os recursos de detecção de anomalias existentes do Azure AD (disponíveis por meio dos [Relatórios de Atividade Anômala do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)) e apresenta novos tipos de evento de risco que pode detectar anomalias em tempo real.

O Identity Protection usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar anomalias e eventos de risco que podem indicar que uma identidade foi comprometida. Usando esses dados, o Identity Protection gera relatórios e alertas que permitem investigar tais eventos de risco e tomar as devidas ações de mitigação ou correção.

Contudo, o Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Baseando-se em eventos de risco, o Identity Protection calcula um nível de risco para cada usuário, permitindo a você definir políticas baseadas em risco para proteger automaticamente as identidades da sua organização.

Essas políticas baseadas em risco, entre outros [controles de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) fornecidos pelo Azure Active Directory e pelo [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), podem bloquear automaticamente ou oferecer ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.

### <a name="identity-protections-capabilities"></a>Recursos do Identity Protection

O Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Para proteger as identidades da sua organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado foi alcançado. Essas políticas, entre outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo EMS, podem bloquear ou iniciar automaticamente ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.

Os exemplos de algumas das maneiras como o Azure Identity Protection pode ajudar a proteger suas contas e identidades incluem:

[Detecção de eventos de risco e contas arriscadas:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Detecção de seis tipos de evento de risco usando regras de aprendizado de máquina e heurística
-   Calcular os níveis de risco do usuário
-   Fornecer recomendações personalizadas para melhorar a postura de segurança geral ao realçar as vulnerabilidades

[Investigação dos eventos de risco:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Enviar notificações para eventos de risco
-   Investigar os eventos de risco usando informações relevantes e contextuais
-   Fornecer fluxos de trabalho básicos para acompanhar as investigações
-   Fornecer acesso fácil às ações de correção, tais como redefinição de senha

[Políticas de acesso condicional baseadas em risco:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Política para reduzir entradas arriscadas ao bloquear entradas ou exigir desafios de autenticação multifator.
-   Política para bloquear ou proteger contas de usuário arriscadas
-   Política para exigir o registro para autenticação multifator

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Com o [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Gerenciamento de identidades com privilégios do AD do Azure](./media/azure-threat-detection/azure-threat-detection-fig2.png)

Você pode gerenciar, controlar e monitorar o acesso em sua organização. Isso inclui o acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

O Azure AD Privileged Identity Management ajuda você a:

-   Obter um alerta e gerar relatórios sobre o acesso administrativo “just in time” para Microsoft Online Services como o Office 365 e o Intune

-   Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador

-   Receber alertas sobre o acesso a uma função com privilégios

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

O [Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura local e de nuvem. Como o OMS é implementado como um serviço baseado em nuvem, é possível colocá-lo em funcionamento com investimentos mínimos em serviços de infraestrutura. Os novos recursos são entregues automaticamente, evitando os custos contínuos com manutenção e atualização.

Além de fornecer serviços importantes por conta própria, o OMS pode integrar a componentes do System Center, como o [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), para estender seus investimentos de gerenciamento de segurança existentes para a nuvem. O System Center e o OMS podem trabalhar juntos para proporcionar uma experiência completa de gerenciamento híbrido.

### <a name="holistic-security-and-compliance-posture"></a>Postura de conformidade e segurança holística

O painel [Segurança e Auditoria do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma exibição abrangente da postura de segurança de TI de sua organização com consultas de pesquisa internas para problemas importantes que exigem atenção. O painel Segurança e Auditoria é a tela inicial para tudo relacionado à segurança no OMS. Ela fornece uma análise de alto nível do estado de segurança de seus computadores. Também inclui a capacidade de exibir todos os eventos das últimas 24 horas, dos últimos sete dias ou de qualquer outro intervalo de tempo personalizado.

Os painéis OMS ajudam você a compreender com rapidez e facilidade a postura geral de segurança de qualquer ambiente, tudo dentro do contexto de Operações de TI, incluindo: avaliação de atualização de software, avaliação de antimalware e linhas de base de configuração. Além disso, os dados de log de segurança estão prontamente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

O painel Auditoria e Segurança do OMS é organizado em quatro categorias principais:

![Painel Segurança e Auditoria do OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Domínios de segurança**: nessa área, você poderá explorar ainda mais os registros de segurança ao longo do tempo, acessar a avaliação de malware, atualizar as informações de avaliação, segurança de rede, identidade e acesso, computadores com eventos de segurança e ter acesso rapidamente ao painel da Central de Segurança do Azure.

-   **Problemas importantes**: essa opção permite que você identifique rapidamente o número de problemas ativos e sua severidade.

-   **Detecções (visualização)**: permite que você identifique padrões de ataque visualizando alertas de segurança à medida que eles ocorrem em relação a seus recursos.

-   **Inteligência contra ameaças**: permite que você identifique padrões de ataque visualizando o número total de servidores com tráfego IP de saída mal-intencionado, o tipo de ameaça mal-intencionada e um mapa que mostra a origem desses IPs.

-   **Consultas comuns de segurança**: essa opção fornece uma lista das consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você clica em uma dessas consultas, ela abre a folha Pesquisa com os resultados da consulta.

### <a name="insight-and-analytics"></a>Insight and Analytics
No centro no [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) está o repositório do OMS, que está hospedado na nuvem do Azure.

![Insight and Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Os dados são coletados para o repositório de fontes conectadas configurando fontes de dados e adicionando soluções à sua assinatura.

![subscription](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Fontes de dados e soluções criarão diferentes tipos de registro que têm seus próprios conjuntos de propriedades, mas ainda podem ser analisados juntos em consultas no repositório. Isso permite usar as mesmas ferramentas e métodos para trabalhar com diferentes tipos de dados coletados por fontes diferentes.


A maioria da sua interação com o Log Analytics se dá por meio do portal do OMS, que é executado em qualquer navegador e fornece acesso às definições de configuração e várias ferramentas para analisar e agir sobre os dados coletados. No portal, você pode usar as [pesquisas de log](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) nas quais é possível construir consultas para analisar os dados coletados, [painéis](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards) que podem ser usados para personalizar a exibição gráfica das suas pesquisas mais valiosas e [soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) que fornecem ferramentas de análise e funcionalidade adicionais.

![ferramentas de análise](./media/azure-threat-detection/azure-threat-detection-fig6.png)

As soluções acrescentam funcionalidades ao Log Analytics. A maioria delas é executada na nuvem e fornecem análise de dados coletados no repositório do OMS. Elas também podem definir novos tipos de registro a serem coletados que podem ser analisados com Pesquisas de Log ou pela interface do usuário adicional fornecida pela solução no painel do OMS.
A segurança e a auditoria é um exemplo desses tipos de soluções.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automação e Controle: alerta de dessincronização de configuração de segurança

A Automação do Azure automatiza processos administrativos com runbooks que se baseiam no PowerShell e que são executados na nuvem do Azure. Eles também podem ser executados em um servidor em seu data center local para gerenciar os recursos locais. A Automação do Azure fornece o gerenciamento de configuração com o DSC (Desired State Configuration) do PowerShell.

![Automação do Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Você pode criar e gerenciar recursos de DSC hospedados no Azure e aplique-los para a nuvem e sistemas no local para definir e automaticamente aplicar sua configuração ou obtenha relatórios sobre descompasso para ajudar a assegurar que as configurações de segurança permaneçam em política.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure ajuda a proteger os recursos do Azure. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Dentro do serviço, você pode definir políticas não apenas em relação a suas assinaturas do Azure, mas também aos [Grupos de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal); portanto, é possível ser mais granular.

![Central de Segurança do Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Os pesquisadores de segurança da Microsoft estão constantemente à procura de ameaças. Eles têm acesso a um conjunto expansivo de telemetria obtida com a presença global da Microsoft na nuvem e localmente. Esta coleção diversa e abrangente de conjuntos de dados permite que a Microsoft descubra novos padrões de ataque e tendências em seus produtos de consumidor e empresariais no local, bem como em serviços on-line.

Dessa forma, a Central de Segurança pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Isso ajuda a acompanhar o ritmo de um ambiente de ameaças que muda rapidamente.

![Central de Segurança](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A detecção de ameaças da Central de Segurança funciona coletando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente.  Ele analisa essas informações, correlacionando informações de várias fontes para identificar ameaças.
Os alertas de segurança são priorizados na Central de Segurança, juntamente com recomendações sobre como corrigir a ameaça.

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. As inovações em tecnologias de big data e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para avaliar eventos em toda a malha de nuvem, detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução de ataques. Essas análises de segurança incluem o seguinte.

### <a name="threat-intelligence"></a>Inteligência contra ameaças

A Microsoft tem uma grande quantidade de inteligência contra ameaças globais.
A telemetria flui de várias fontes, como o Azure, o Office 365, o Microsoft CRM online, o Microsoft Dynamics AX, o outlook.com, o MSN.com, a DCU (Unidade de Crimes Digitais da Microsoft) e o Microsoft Security Response Center (MSRC).

![Inteligência contra ameaças](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Os pesquisadores também recebem informações de inteligência de ameaça que são compartilhadas entre os principais provedores de serviços de nuvem e assinam feeds de inteligência contra ameaças de terceiros. A Central de Segurança do Azure pode usar essas informações para alertá-lo de ameaças vindas de maus atores conhecidos. Alguns exemplos incluem:

-   **Utilizando a energia do Machine Learning -** Central de segurança do Azure tem acesso a uma grande quantidade de dados sobre a atividade de rede de nuvem, que pode ser usada para detectar ameaças que visem as implantações do Azure. Por exemplo:

-   **Detecções de força bruta -** aprendizado de máquina é usado para criar um padrão de histórico de tentativas de acesso remoto, que permite detectar ataques de força bruta contra portas SQL, RDP e SSH.

-   **Saída de DDoS e detecção de Botnet** -um objetivo comum de ataques direcionados a recursos de nuvem é usar o poder da computação desses recursos para executar outros ataques.

-   **Novos servidores de análise comportamental e VMs -** depois que uma máquina virtual ou o servidor estiver comprometida, os invasores empregam uma grande variedade de técnicas para executar código mal-intencionado no sistema ao evitar a detecção, garantir a persistência e evitando o uso de controles de segurança.

-   **Detecção de ameaças do banco de dados SQL Azure -** detecção de ameaças para o banco de dados SQL, que identifica as atividades do banco de dados anormais indicando incomuns e potencialmente prejudiciais tenta acessar ou explorar bancos de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise de comportamento é uma técnica que analisa e compara dados em uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos de aprendizado de máquina complexos que são aplicados a grandes conjuntos de dados.

![Análise comportamental](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Eles também são determinados pela análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A Central de Segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos baseado na análise dos logs de máquina virtual, dos logs de dispositivo de rede virtual, dos logs da malha, dos despejos de memória e de outras fontes.

Além disso, há uma correlação com outros sinais para verificar se há suporte a evidências de uma campanha generalizada. Essa correlação ajuda a identificar os eventos que são consistentes com os indicadores de comprometimento estabelecidos.

Alguns exemplos incluem:
-   **Execução de processo suspeito**: os invasores empregam várias técnicas para executar um software mal-intencionado sem detecção. Por exemplo, um invasor pode dar os mesmos nomes de arquivos do sistema legítimos ao malware, mas colocar esses arquivos em locais alternativos, usar um nome que é muito semelhante a um arquivo benigno ou mascarar a extensão verdadeira do arquivo. Os modelos da Central de Segurança processam comportamentos e monitoram execuções de processo para detectar exceções como essas.

-   **Malware oculto e tentativas de exploração**: o malware sofisticado é capaz de escapar dos produtos antimalware tradicionais não gravando no disco ou criptografando os componentes do software armazenados no disco. No entanto, esse tipo de malware pode ser detectado usando a análise de memória, já que o malware deve deixar rastreamentos na memória para poder funcionar. Quando o software falha, um despejo de memória captura uma parte da memória no momento da falha. Analisando a memória no despejo, a Central de Segurança do Azure pode detectar técnicas usadas para explorar vulnerabilidades no software, acessar dados confidenciais e se manter em uma máquina comprometida de maneira furtiva sem afetar o desempenho do seu computador.

-   **Movimentação lateral e reconhecimento interno**: para persistir em uma rede comprometida e localizar/coletar dados valiosos, os invasores geralmente tentam mover-se lateralmente da máquina comprometida para outras dentro da mesma rede. A Central de Segurança monitora as atividades de processo e de logon para detectar tentativas de expandir a presença do invasor dentro da rede, como sondagem de rede de execução de comando remoto e a enumeração de contas.

-   **Scripts do PowerShell mal-intencionados:** o PowerShell pode ser usado pelos invasores para executar código mal-intencionado em máquinas virtuais de destino com diversas finalidades. A Central de Segurança inspeciona a atividade do PowerShell para obter evidência de atividades suspeitas.

-   **Ataques de saída**: os invasores costumam atacar os recursos de nuvem com o objetivo de usá-los para montar ataques adicionais. As máquinas virtuais comprometidas, por exemplo, podem ser usadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar SPAM ou verificar portas abertas e outros dispositivos na Internet. Aplicando o aprendizado de máquina ao tráfego de rede, a Central de Segurança pode detectar quando as comunicações de rede de saída excedem a norma. FQuando for SPAM, a Central de Segurança também correlaciona o tráfego de email incomum com inteligência do Office 365 para determinar se o email é possivelmente perigoso ou o resultado de uma campanha de email legítima.

### <a name="anomaly-detection"></a>Detecção de anomalias

A Central de Segurança do Azure também usa detecção de anomalias para identificar ameaças. Ao contrário da análise de comportamento (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a detecção de anomalias é mais "personalizada" e se concentra nas linhas de base que são específicas das suas implantações. O aprendizado de máquina é aplicado para determinar a atividade normal das implantações e, em seguida, as regras são geradas para definir condições de exceção que possam representar um evento de segurança. Aqui está um exemplo:

-   **Ataques de força bruta vindos de RDP/SSH**: suas implantações podem ter máquinas virtuais ocupadas com uma grande quantidade diária de logons e outras máquinas virtuais que têm poucos ou nenhum logon. A Central de Segurança do Azure pode determinar a linha de base da atividade de logon para essas máquinas virtuais e usar o aprendizado de máquina para definir o que há em torno das atividades de logon normal. Se houver qualquer discrepância com a linha de base definida para as características relacionadas ao logon, um alerta poderá ser gerado. Novamente, o aprendizado de máquina determina o que é relevante.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitoramento Contínuo de Inteligência contra Ameaças

A Central de Segurança do Azure opera com equipes de pesquisas de segurança e de ciência de dados no mundo inteiro que monitoram continuamente em busca de alterações no panorama de ameaças. Isso inclui as seguintes iniciativas:

-   **Monitoramento de inteligência contra ameaças**: a inteligência contra ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças iminentes ou existentes. Essas informações são compartilhadas na comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.

-   **Compartilhamento de sinal**: ideias de equipes de segurança de todo o amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade cliente são compartilhadas e analisadas.

-   **Especialistas de segurança da Microsoft**: comprometimento contínuo com as equipes da Microsoft que trabalham em campos de segurança especializada, como computação forense e detecção de ataque à Web.

-   **Ajuste de detecção**: algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança trabalham com clientes para validar os resultados. Verdadeiros e falsos positivos são usados para refinar os algoritmos de aprendizado de máquina.

Esses esforços combinados culminam em detecções novas e aprimoradas de que você pode se beneficiar instantaneamente. Não há nenhuma ação a ser tomada.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Recursos de Detecção Avançada de Ameaças – Outros Serviços do Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Máquina Virtual: Antimalware da Microsoft

O [Antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Azure é uma solução de agente único para aplicativos e ambientes de locatário, projetado para ser executado em segundo plano sem intervenção humana. Você pode implantar a proteção baseada nas necessidades de suas cargas de trabalho do aplicativo, com configuração básica padronizada ou personalizada avançada, incluindo monitoramento de antimalware. Antimalware do Azure é uma opção de segurança para máquinas virtuais do Azure e é instalado automaticamente em todas as máquinas virtuais de PaaS do Azure.

**Recursos do Azure para implantar e habilitar o Microsoft Antimalware para seus aplicativos**

#### <a name="microsoft-antimalware-core-features"></a>Recursos básicos de Antimalware da Microsoft

-   A **Proteção em tempo real -** monitora a atividade em Serviços de Nuvem e em máquinas virtuais para detectar e bloquear a execução de malware.

-   **Varredura programada -** executa periodicamente uma varredura direcionada para detectar malware, incluindo programas ativamente em execução.

-   **Remediação de Malware -** atua automaticamente sobre o malware detectado, por exemplo, excluindo ou colocando arquivos mal-intencionados em quarentena e limpando entradas de Registro mal-intencionadas.

-   **Atualizações de assinatura -** instala automaticamente as últimas assinaturas de proteção (definições de vírus) para garantir que a proteção seja atualizada em uma frequência predeterminada.

-   **Atualizações de mecanismo Antimalware -** atualiza automaticamente o mecanismo Microsoft Antimalware.

-   **Atualizações da plataforma Antimalware -** atualiza automaticamente a plataforma Microsoft Antimalware.

-   **Proteção ativa -**  reporta metadados de telemetria sobre ameaças detectadas e recursos suspeitos ao Microsoft Azure para garantir uma resposta rápida ao panorama de ameaças em constante evolução, além de habilitar a entrega de assinatura síncrona em tempo real por meio do MAPS (Microsoft Active Protection System).

-   **Relatórios de exemplos -** fornece e relata exemplos para o serviço Microsoft Antimalware para ajudar a aprimorar o serviço e a habilitar a solução de problemas.

-   **Exclusões -** permite que os administradores de serviço e aplicativo configurem determinados arquivos, processos e unidades para excluí-los da proteção e da verificação por motivos de desempenho e/ou outros.

-   **Coleta de eventos de Antimalware -** registra a integridade do serviço de antimalware, atividades suspeitas e ações de remediação realizadas no log de eventos do sistema operacional e os coleta na conta de armazenamento do Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Detecção de Ameaças do Banco de Dados SQL do Azure

A [Detecção de Ameaças do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é um novo recurso de inteligência de segurança criado para o serviço Banco de Dados SQL do Azure. Trabalhando ininterruptamente para aprender, perfil e detectar atividades anormais de banco de dados, a detecção de ameaças de banco de dados SQL do Azure identifica ameaças potenciais no banco de dados.

Os agentes de segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas de banco de dados conforme eles ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e minimizar a ameaça.

Atualmente, a detecção de ameaças de banco de dados SQL do Azure detecta possíveis vulnerabilidades e ataques de injeção de SQL e padrões de acesso do banco de dados anormais.

Ao receber a notificação de email de detecção de ameaça, os usuários são capazes de navegar e exibir os registros de auditoria relevantes usando o link profundo no email que abre um visualizador de auditoria e/ou auditoria pré-configurado modelo do Excel que mostra os registros de auditoria relevantes no momento do evento suspeito acordo com o seguinte:
-   Armazenamento de auditoria para o servidor/banco de dados com as atividades anormais de banco de dados

-   Tabela de armazenamento relevantes de auditoria que era usada no momento do evento para gravar o log de auditoria

-   Audite registros da hora seguinte desde a ocorrência do evento.

-   Registros de auditoria com a ID de evento semelhantes no momento do evento (opcional para alguns detectores)

Detectores de ameaça do banco de dados SQL use uma das seguintes metodologias de detecção:

-   **Detecção Determinística –** detecta padrões suspeitos (com base em regras) em consultas de cliente SQL correspondentes a ataques conhecidos. Essa metodologia tem detecção alta e baixa de falsos positivos, porém limitadas cobertura porque ele está na categoria de "atômicas detecções".

-   **Detecção de behavioural –** defeitos de atividade anormal, que é um comportamento anormal do banco de dados que não foi detectada durante os últimos 30 dias.  Um exemplo de atividade anormal de cliente SQL pode ser um pico de logons com falha/consultas, grande volume de dados sendo extraídos, consultas canônicas incomuns e familiarizados endereços IP usados para acessar o banco de dados

### <a name="application-gateway-web-application-firewall"></a>Firewall do Aplicativo Web do Gateway de Aplicativo

O [Firewall do Aplicativo Web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é um recurso do [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) que fornece proteção para aplicativos Web que utilizam o gateway de aplicativo para as funções de [Controle de Entrega de Aplicativos](https://kemptechnologies.com/in/application-delivery-controllers) padrão. O firewall do aplicativo Web faz isso protegendo-os contra a maioria das [Dez vulnerabilidades mais comuns da Web segundo o OWASP](https://www.owasp.org/index.php/Top_10_2010-Main)

![Firewall do Aplicativo Web do Gateway de Aplicativo](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Proteção contra injeção de SQL

-   Proteção contra scripts entre sites

-   Proteção Contra Ataques Comuns da Web, como a injeção de comandos, as solicitações HTTP indesejadas, a divisão de resposta HTTP e o ataque de inclusão de arquivo remoto

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação

-   Prevenção contra bots, rastreadores e scanners

-   Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS etc.)

Configurar o WAF no Gateway de Aplicativo oferece os seguintes benefícios:

-   Protege seu aplicativo Web de vulnerabilidades e ataques da Web sem modificar o código de back-end.

-   Protege vários aplicativos Web ao mesmo tempo por trás de um gateway de aplicativo. O Gateway de Aplicativo oferece suporte à hospedagem de até 20 sites por trás de um único gateway e que podem ser protegidos contra ataques da Web.

-   Monitore seu aplicativo Web contra ataques usando o relatório em tempo real gerado pelos logs do WAF do gateway de aplicativo.

-   Determinados controles de conformidade exigem que todos os pontos de extremidade voltados para a Internet estejam protegidos por uma solução WAF. Usando o gateway de aplicativo com WAF habilitado, você pode atender a esses requisitos de conformidade.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Detecção de Anomalias – uma API criada com o Azure Machine Learning

Detecção de anomalias é uma API criada com o Azure Machine Learning que é útil para detectar os diferentes tipos de padrões anômalos em seus dados de série temporal. A API atribui uma pontuação de anomalias em cada ponto de dados na série de tempo, o que pode ser usado para gerar alertas, monitoramento por meio de painéis ou conectar-se com seus sistemas de emissão de tíquetes.

O [API de detecção de anomalias](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) pode detectar os seguintes tipos de anomalias em dados de série temporal:

-   **Picos e quedas**: por exemplo, ao monitorar o número de falhas de logon para um serviço ou o número de check-outs em um site de comércio eletrônico, os picos ou as quedas incomuns podem indicar ataques de segurança ou interrupções de serviço.

-   **Tendências positivas e negativas:** ao monitorar o uso de memória em computação, por exemplo, reduzindo o tamanho de memória livre é uma indicação de um vazamento de memória potencial; quando o comprimento da fila do serviço de monitoramento, uma tendência de aumento persistente pode indicar um problema de software subjacente.

-   **Nível de alterações e intervalo dinâmico dos valores:** por exemplo, nível altera latências de um serviço depois que um serviço de atualização ou diminuir níveis de exceções após a atualização pode ser interessante para monitorar.

A API baseada em machine learning permite:

-   **Detecção de flexível e robusta:** os modelos de detecção de anomalias permitem aos usuários definir configurações de sensibilidade e detectar anomalias entre conjuntos de dados não sazonal e sazonais. Os usuários podem ajustar o modelo de detecção de anomalias para tornar a API de detecção diferenciação menos ou mais de acordo com suas necessidades. Isso significaria detectar as anomalias visíveis menos ou mais nos dados com e sem padrões sazonais.

-   **Detecção escalonável e em tempo hábil:** a maneira tradicional de monitoramento com presentes limites definidos pelo conhecimento do domínio dos especialistas são caros e não escalável a milhões de alterar dinamicamente os conjuntos de dados. Os modelos de detecção de anomalias nesta API são aprendidos e ajustados automaticamente modelos de dados históricos e em tempo real.

-   **Detecção proativa e acionável:** a detecção de alteração de nível e de tendência lenta podem ser aplicadas para detecção de anomalias. Os sinais anormais antecipados detectados podem ser usados para direcionar pessoas para investigar e agir sobre as áreas problemáticas.  Além disso, os modelos de análise de causas básicas e as ferramentas de alertas podem ser desenvolvioas sobre esse serviço de API de detecção de anomalias.

A API de detecção de anomalias é uma solução efetiva e eficiente para uma ampla variedade de cenários, como a integridade do serviço e monitoramento, IoT, monitoramento de desempenho e monitoramento de tráfego de rede KPI. Aqui estão alguns cenários comuns onde essa API pode ser útil:
- Os departamentos de TI precisam de ferramentas para acompanhar eventos, código de erro, log de uso e desempenho (CPU, memória e assim por diante) no momento oportuno.

-   Sites de comércio online desejam controlar atividades do cliente, modos de exibição de página, cliques e assim por diante.

-   Utilitário de empresas deseja controlar o consumo de água e outros recursos, eletricidade e gás.

-   Serviços de gerenciamento de recurso/construção deseja monitorar a temperatura, umidade, tráfego e assim por diante.

-   IoT/fabricantes deseja usar os dados do sensor na série de tempo para o fluxo de trabalho de monitor, qualidade e assim por diante.

-   Provedores de serviços, como centros de chamada precisam monitorar tendências de demanda de serviço, volume de incidentes, aguarde o comprimento da fila e assim por diante.

-   Grupos de análise de negócios desejam monitorar KPIs da empresa' (por exemplo, o volume de vendas, opiniões de clientes, preços) anormal movimento em tempo real.

### <a name="cloud-app-security"></a>Segurança de Aplicativo de Nuvem

[Segurança de aplicativo em nuvem](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente crítico da pilha de segurança de nuvem da Microsoft. É uma solução abrangente que pode ajudar sua organização para aproveitar totalmente a promessa de aplicativos em nuvem, mas mantê-lo no controle, a maior visibilidade em atividade. Ele também ajuda a aumentar a proteção de dados críticos em aplicativos de nuvem.

Com as ferramentas que ajudam a descobrir sombra IT, avaliar os riscos, políticas, investigar atividades e evitar ameaças, sua organização com mais segurança pode mudar para a nuvem, mantendo o controle dos dados críticos.

<table style="width:100%">
 <tr>
   <td>Descobrir</td>
   <td>Descobrir a TI de sombra com o sombra IT com a Segurança de Aplicativo de Nuvem. Ganhe visibilidade, detectando aplicativos, atividades, usuários, dados e arquivos em seu ambiente de nuvem. Descubra os aplicativos de terceiros conectados à sua nuvem.</td>
 </tr>
 <tr>
   <td>Investigar</td>
   <td>Investigue os seus aplicativos de nuvem usando as ferramentas de análise forense de nuvem para aprofundar em aplicativos arriscados, usuários específicos e arquivos em sua rede. Localize padrões nos dados coletados da nuvem. Gere relatórios para monitorar sua nuvem.</td>

 </tr>
 <tr>
   <td>Controle</td>
   <td>Reduza o risco ao definir políticas e alertas para obter o máximo controle sobre o tráfego de rede na nuvem. Use a Segurança de Aplicativo de Nuvem para migrar seus usuários para alternativas de aplicativo de nuvem seguros e sancionados.</td>

 </tr>
 <tr>
   <td>Proteger</td>
   <td>Usar segurança de aplicativo de nuvem para aprovar ou impedir que os aplicativos, aplicar prevenção de perda de dados, as permissões de controle e de compartilhamento e gerar alertas e relatórios personalizados.</td>

 </tr>
 <tr>
   <td>Controle</td>
   <td>Reduza o risco ao definir políticas e alertas para obter o máximo controle sobre o tráfego de rede na nuvem. Use a Segurança de Aplicativo de Nuvem para migrar seus usuários para alternativas de aplicativo de nuvem seguros e sancionados.</td>

 </tr>
</table>


![Segurança de Aplicativo de Nuvem](./media/azure-threat-detection/azure-threat-detection-fig14.png)

A Segurança de Aplicativo de Nuvem integra a visibilidade com sua nuvem por

-   Usar a descoberta de nuvem para mapear e identificar o seu ambiente de nuvem e os aplicativos na nuvem sua organização está usando.


-   Sancionar e proibir aplicativos em sua nuvem.



-   Usando conectores de fácil de implantar aplicativos que aproveitam APIs, de provedor para visibilidade e controle dos aplicativos que você se conectar a.

-   Ajudando você a ter controle contínuo de configuração e ajustar continuamente, políticas.

Na coleta de dados dessas fontes, a segurança de aplicativo de nuvem executa análise sofisticada nos dados. Ele imediatamente alerta para atividades anormais e fornece visibilidade profunda em seu ambiente de nuvem. Você pode configurar uma política de segurança de aplicativo de nuvem e usá-lo para proteger tudo em seu ambiente de nuvem.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Recursos ATD de terceiros por meio do Azure Marketplace

### <a name="web-application-firewall"></a>Firewall do Aplicativo Web

Firewall de aplicativo Web inspeciona carregamentos de malware blocos injeções de SQL, script entre sites e o tráfego de entrada da web e aplicativo DDoS e outros ataques direcionados em seus aplicativos web. Ele também inspeciona as respostas dos servidores Web back-end para DLP (Prevenção contra Perda de Dados). O mecanismo de controle de acesso integrado permite aos administradores criar políticas de controle de acesso granular para autenticação, autorização e contabilidade (AAA), que fornece autenticação forte organizações e controle de usuário.

**Destaques:**
-   Detecta e bloqueia SQL injeções, scripts intersites, carregamentos de malware, DDoS de aplicativo ou quaisquer outros ataques contra seu aplicativo.

-   Autenticação e controle de acesso.

-   Examina o tráfego de saída para detectar dados confidenciais e pode mascarar ou bloquear as informações sendo vazados.

-   Acelera a entrega de conteúdo do aplicativo web, usando recursos como armazenamento em cache, compactação e outras otimizações de tráfego.

A seguir é exemplos de firewalls de aplicativo da Web disponíveis no mercado do Azure:

[Firewall do aplicativo Web Barracuda, Brocade Virtual Firewall do aplicativo Web (Brocade vWAF), o Firewall do IP ThreatSTOP & Imperva SecureSphere.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Próximas etapas

- [Recursos de detecção da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Os recursos de detecção avançada da Central de Segurança do Azure ajudam a identificar ameaças ativas aos recursos do Microsoft Azure e fornece as informações necessárias para uma resposta rápida.

- [Detecção de Ameaças do Banco de Dados SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Detecção de ameaças de banco de dados SQL Azure ajudou suas preocupações sobre ameaças em potencial para seu banco de dados.
