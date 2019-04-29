---
title: Detecção Avançada de Ameaças do Azure | Microsoft Docs
description: Saiba mais sobre o Azure AD Identity Protection e suas funcionalidades.
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
ms.openlocfilehash: 60a0d7c2da25f63d0728dbb86f9a6c2328def811
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611234"
---
# <a name="azure-advanced-threat-detection"></a>Detecção avançada de ameaças do Azure

Ofertas do Azure criado na funcionalidade de detecção avançada de ameaças por meio de serviços, como o Azure Active Directory (Azure AD), logs do Azure Monitor e a Central de segurança do Azure. Esta coleção de recursos e serviços de segurança fornece uma maneira simples e rápida de compreender o que está acontecendo em suas implantações do Azure.

O Azure fornece uma ampla gama de opções para configurar e personalizar a segurança para atender às necessidades de implantações de seu aplicativo. Este artigo discute como atender a esses requisitos.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

O [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um recurso da edição [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) que oferece uma visão geral dos eventos de risco e das possíveis vulnerabilidades que podem afetar as identidades da sua organização. O Identity Protection usa as funcionalidades existentes de detecção de anomalias do Azure AD que estão disponíveis por meio de [Relatórios de Atividade Anômala do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports) e apresenta novos tipos de eventos de risco que podem detectar anomalias em tempo real.

![Diagrama do Azure AD Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)

O Identity Protection usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar anomalias e eventos de risco que possam indicar que uma identidade foi comprometida. Usando esses dados, o Identity Protection gera relatórios e alertas para que você possa investigar esses eventos de risco e executar as ações de mitigação ou de correção apropriadas.

O Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Com base em eventos de risco, o Identity Protection calcula um nível de risco do usuário para cada usuário, permitindo que você configure políticas baseadas em risco para proteger automaticamente as identidades da sua organização.

Essas políticas baseadas em risco, entre outros [controles de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) fornecidos pelo Azure Active Directory e pelo [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), podem bloquear automaticamente ou oferecer ações de correção adaptáveis que incluem redefinições de senha e a imposição da autenticação multifator.

### <a name="identity-protection-capabilities"></a>Recursos do Identity Protection

O Azure Active Directory Identity Protection é mais do que apenas uma ferramenta de monitoramento e criação de relatórios. Para proteger as identidades da sua organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado foi alcançado. Essas políticas, entre outros controles de acesso condicional fornecidos pelo Azure Active Directory e pelo EMS, podem bloquear ou iniciar automaticamente ações de correção adaptáveis que incluem redefinições de senha e a imposição de autenticação multifator.

Os exemplos de algumas das maneiras como o Azure Identity Protection pode ajudar a proteger suas contas e identidades incluem:

[Detectando eventos de risco e contas arriscadas](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Detecte seis tipos de eventos de risco usando aprendizado de máquina e regras heurísticas.
-   Calcule os níveis de risco do usuário.
-   Forneça recomendações personalizadas para melhorar a situação geral de segurança destacando as vulnerabilidades.

[Investigando eventos de risco](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Envie notificações para eventos de risco.
-   Investigue eventos de risco usando informações relevantes e contextuais.
-   Forneça fluxos de trabalho básicos para acompanhar as investigações.
-   Forneça acesso fácil a ações de correção, como redefinição de senha.

[Políticas de acesso condicional baseadas em risco](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Atenue entradas arriscadas ao bloquear entradas ou exigir desafios de autenticação multifator.
-   Bloqueie ou proteja contas de usuário arriscadas.
-   Exija que os usuários se registrem na autenticação multifator.

### <a name="azure-ad-privileged-identity-management"></a>Gerenciamento de identidades com privilégios do AD do Azure

Com o [PIM (Azure Active Directory Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), você pode gerenciar, controlar e monitorar o acesso em sua organização. Esse recurso inclui o acesso aos recursos do Azure AD e a outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

O PIM ajuda você a:

-   Obter alertas e gerar relatórios sobre o acesso administrativo JIT (Just-In-Time) e de administradores do Azure AD aos serviços online da Microsoft, como o Office 365 e o Intune.

-   Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador.

-   Receber alertas sobre o acesso a uma função com privilégios.

## <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs do Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é uma Microsoft baseados em nuvem gerenciamento solução de TI que ajuda você a gerenciar e proteger suas instalações e infraestrutura de nuvem. Como logs do Azure Monitor é implementado como um serviço baseado em nuvem, você pode colocá-lo em funcionamento rapidamente com investimento mínimo nos serviços de infraestrutura. Novos recursos de segurança são entregues automaticamente, economizando em manutenção contínua e custos de atualização.

Além de fornecer serviços importantes por conta própria, o Azure Monitor logs podem integrar componentes do System Center, como [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), para estender sua segurança existente investimentos de gerenciamento para o nuvem. Logs do System Center e o Azure Monitor podem trabalhar juntos para fornecer a experiência de um gerenciamento híbrido completa.

### <a name="holistic-security-and-compliance-posture"></a>Situação de conformidade e de segurança abrangente

O [painel de Segurança e Auditoria do Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma exibição abrangente da situação de segurança de TI da organização, com consultas de pesquisa internas para problemas importantes que exigem atenção. O painel segurança e auditoria é a tela inicial para tudo relacionado à segurança nos logs do Azure Monitor. Ela fornece uma análise de alto nível do estado de segurança de seus computadores. Você também pode exibir todos os eventos das últimas 24 horas, 7 dias ou de qualquer outro período personalizado.

O Azure Monitor registra ajudam a forma rápida e fácil entender a postura geral de segurança de qualquer ambiente, todas dentro do contexto de operações de TI, incluindo avaliação de atualização de software, avaliação de antimalware e linhas de base de configuração. Os dados de log de segurança ficam prontamente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

![O painel de Segurança e Auditoria do Log Analytics](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

O painel de Segurança e Auditoria do Log Analytics é organizado em quatro categorias principais:

-   **Domínios de Segurança**: Permite explorar mais os registros de segurança ao longo do tempo; acessar avaliações de malware; atualizar avaliações; exibir informações de acesso, identidade e segurança de rede; exibir computadores com eventos de segurança e acessar rapidamente o painel da Central de Segurança do Azure.

-   **Problemas Importantes**: Permite identificar rapidamente o número de problemas ativos e sua gravidade.

-   **Detecções (Versão Prévia)**: Permite identificar padrões de ataque exibindo os alertas de segurança à medida que eles ocorrem nos recursos.

-   **Inteligência contra Ameaças**: Permite identificar padrões de ataque exibindo o número total de servidores com tráfego IP de saída mal-intencionado, o tipo de ameaça mal-intencionada e um mapa dos locais desses IPs.

-   **Consultas comuns de segurança**: Lista as consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você seleciona qualquer consulta, o painel Pesquisa é aberto e exibe os resultados da consulta.

### <a name="insight-and-analytics"></a>Insight e análise
No centro da [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é o repositório, que é hospedado pelo Azure.

![Diagrama de insight e análise](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Colete dados no repositório de fontes conectadas configurando fontes de dados e adicionando soluções à sua assinatura.

![O painel de logs do Azure Monitor](./media/azure-threat-detection/azure-threat-detection-fig5.png)

As fontes de dados e as soluções criam tipos de registro separados com seus próprios conjuntos de propriedades, mas também é possível analisá-los juntos fazendo consultas ao repositório. Você pode usar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados que são coletados por várias fontes.


A maioria da sua interação com logs do Azure Monitor é por meio do portal do Azure, que é executado em qualquer navegador e fornece acesso às definições de configuração e várias ferramentas para analisar e agir sobre dados coletados. No portal, você pode usar:
* [Pesquisas de logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) nas quais é possível construir consultas para analisar os dados coletados.
* [Painéis](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), que você pode personalizar com exibições gráficas das pesquisas mais valiosas.
* [Soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), que fornecem ferramentas de análise e funcionalidade adicionais.

![Ferramentas de análise](./media/azure-threat-detection/azure-threat-detection-fig6.png)

As soluções adicionam funcionalidade aos logs do Azure Monitor. Eles basicamente executam na nuvem e fornecem análise de dados que são coletados no repositório do log analytics. As soluções também podem definir novos tipos de registro a serem coletados que podem ser analisados com pesquisas de log ou por meio de uma interface de usuário adicionais que a solução fornece no painel de análise de log.

O painel Segurança e Auditoria é um exemplo desses tipos de soluções.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automação e controle: Alerta de dessincronização da configuração de segurança

A Automação do Azure automatiza processos administrativos com runbooks que se baseiam no PowerShell e são executados na nuvem. Eles também podem ser executados em um servidor em seu data center local para gerenciar os recursos locais. A Automação do Azure fornece o gerenciamento de configuração com a DSC (Desired State Configuration) do PowerShell.

![Diagrama da Automação do Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Você pode criar e gerenciar recursos de DSC hospedados no Azure e aplicá-los a sistemas de nuvem e locais. Ao fazer isso, você pode definir e aplicar automaticamente a configuração ou obter relatórios sobre dessincronização para ajudar a garantir que as configurações de segurança permaneçam na política.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure ajuda a proteger os recursos do Azure. Ela fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Dentro do serviço, você pode definir políticas em relação às assinaturas e aos [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure para aumentar a granularidade.

![Diagrama da Central de Segurança do Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Os pesquisadores de segurança da Microsoft estão constantemente à procura de ameaças. Eles têm acesso a um conjunto expansivo de telemetria obtida com a presença global da Microsoft na nuvem e localmente. Esta coleção diversa e abrangente de conjuntos de dados permite que a Microsoft descubra novos padrões de ataque e tendências em seus produtos de consumidor e empresariais no local, bem como em serviços on-line.

Dessa forma, a Central de Segurança pode atualizar rapidamente seus algoritmos de detecção conforme os invasores lançam explorações novas e cada vez mais sofisticadas. Isso ajuda a acompanhar o ritmo de um ambiente de ameaças que muda rapidamente.

![Detecção de ameaças da Central de Segurança](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A detecção de ameaças da Central de Segurança funciona coletando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações, correlacionando informações de várias fontes para identificar ameaças.

Os alertas de segurança são priorizados na Central de Segurança, juntamente com recomendações sobre como corrigir a ameaça.

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. As inovações nas tecnologias de big data e de [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são usadas para avaliar eventos em toda a malha de nuvem. As análises avançadas podem detectar ameaças que seriam impossíveis de serem identificadas com métodos manuais e prevendo a evolução dos ataques. Esses tipos de análise de segurança são abordados nas seções a seguir.

### <a name="threat-intelligence"></a>Inteligência contra ameaças

A Microsoft tem acesso a uma grande quantidade de inteligência global contra ameaças.

A telemetria flui de várias fontes, como o Azure, o Office 365, o Microsoft CRM online, o Microsoft Dynamics AX, o outlook.com, o MSN.com, a DCU (Unidade de Crimes Digitais da Microsoft) e o Microsoft Security Response Center (MSRC).

![Descobertas de inteligência contra ameaças](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Os pesquisadores também recebem informações de inteligência contra ameaças que são compartilhadas entre os principais provedores de serviços de nuvem e assinam feeds de inteligência contra ameaças de terceiros. A Central de Segurança do Azure pode usar essas informações para alertá-lo de ameaças vindas de maus atores conhecidos. Alguns exemplos incluem:

-   **Aproveitando o poder do aprendizado de máquina**: A Central de Segurança do Azure tem acesso a uma grande quantidade de dados sobre a atividades de rede na nuvem, que pode ser usada para detectar ameaças direcionadas às suas implantações do Azure.

-   **Detecção de força bruta**: O aprendizado de máquina é usado para criar um padrão histórico de tentativas de acesso remoto, o que permite detectar ataques de força bruta contra portas SSH (Secure Shell), RDP (protocolo RDP) e SQL.

-   **DDoS de saída e detecção de botnet**: Um objetivo comum dos ataques direcionados a recursos de nuvem é usar o poder de computação desses recursos para executar outros ataques.

-   **Novos servidores e VMs de análise comportamental**: Depois que uma máquina virtual ou um servidor é comprometido, os invasores empregam uma grande variedade de técnicas para executar um código mal-intencionado no sistema evitando a detecção, garantindo a persistência e eliminando controles de segurança.

-   **Detecção de Ameaças do Banco de Dados SQL do Azure**: Detecção de ameaças do Banco de Dados SQL do Azure, que identifica atividades anormais do banco de dados que indicam tentativas incomuns e possivelmente prejudiciais de acessar ou explorar bancos de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise de comportamento é uma técnica que analisa e compara dados em uma coleção de padrões conhecidos. No entanto, esses padrões não são assinaturas simples. Eles são determinados por meio de algoritmos de aprendizado de máquina complexos que são aplicados a grandes conjuntos de dados.

![Descobertas de análise comportamental](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Os padrões também são determinados pela análise cuidadosa de comportamentos mal-intencionados por analistas especialistas. A Central de Segurança do Azure pode usar a análise de comportamento para identificar recursos comprometidos baseado na análise dos logs de máquina virtual, dos logs de dispositivo de rede virtual, dos logs da malha, dos despejos de memória e de outras fontes.

Além disso, os padrões são correlacionados com outros sinais para verificar se há evidências que indicam uma campanha generalizada. Essa correlação ajuda a identificar os eventos que são consistentes com os indicadores de comprometimento estabelecidos.

Alguns exemplos incluem:
-   **Execução de processo suspeito**: Os invasores empregam várias técnicas para executar um software mal-intencionado sem detecção. Por exemplo, um invasor pode dar ao malware os mesmos nomes de arquivos legítimos do sistema, mas colocar esses arquivos em locais alternativos, usar um nome muito semelhante ao de um arquivo benigno ou mascarar a extensão verdadeira do arquivo. Os modelos da Central de Segurança processam comportamentos e monitoram execuções de processo para detectar exceções como essas.

-   **Malware oculto e tentativas de exploração**: Um malware sofisticado pode escapar dos produtos antimalware tradicionais nunca gravando em disco ou criptografando componentes de software armazenados em disco. No entanto, esse tipo de malware pode ser detectado usando a análise de memória, porque o malware precisa deixar rastros na memória para que funcione. Quando o software falha, um despejo de memória captura uma parte da memória no momento da falha. Analisando a memória no despejo, a Central de Segurança do Azure pode detectar técnicas usadas para explorar vulnerabilidades no software, acessar dados confidenciais e manter-se em um computador comprometido disfarçadamente sem afetar o desempenho do computador.

-   **Movimentação lateral e reconhecimento interno**: Para persistir em uma rede comprometida e localizar e coletar dados importantes, em geral, os invasores tentam se mover lateralmente do computador comprometido para outros da mesma rede. A Central de Segurança monitora as atividades de processo e de logon para detectar tentativas de expandir a presença do invasor dentro da rede, como sondagem de rede de execução de comando remoto e a enumeração de contas.

-   **Scripts do PowerShell mal-intencionados**: O PowerShell pode ser usado por invasores para executar um código mal-intencionado em máquinas virtuais de destino com diversas finalidades. A Central de Segurança inspeciona a atividade do PowerShell para obter evidência de atividades suspeitas.

-   **Ataques de saída**: Os invasores costumam atacar os recursos de nuvem com a meta de usá-los para montar ataques adicionais. As máquinas virtuais comprometidas, por exemplo, podem ser usadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar spam ou verificar portas abertas e outros dispositivos na Internet. Aplicando o aprendizado de máquina ao tráfego de rede, a Central de Segurança pode detectar quando as comunicações de rede de saída excedem a norma. Quando um spam é detectado, a Central de Segurança também correlaciona o tráfego de email incomum à inteligência do Office 365 para determinar se o email pode ser perigoso ou é o resultado de uma campanha de email legítima.

### <a name="anomaly-detection"></a>Detecção de anomalias

A Central de Segurança do Azure também usa detecção de anomalias para identificar ameaças. Ao contrário da análise de comportamento (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a detecção de anomalias é mais "personalizada" e se concentra nas linhas de base que são específicas das suas implantações. O aprendizado de máquina é aplicado para determinar a atividade normal das implantações e, em seguida, são geradas regras para definir condições de exceção que possam representar um evento de segurança. Aqui está um exemplo:

-   **Ataques de força bruta vindos de RDP/SSH**: Suas implantações podem ter máquinas virtuais ocupadas com uma grande quantidade diária de logons e outras máquinas virtuais com poucos logons ou nenhum. A Central de Segurança do Azure pode determinar a linha de base da atividade de logon para essas máquinas virtuais e usar o aprendizado de máquina para definir o que há em torno das atividades de logon normal. Quando há alguma discrepância com a linha de base definida para as características relacionadas ao logon, um alerta pode ser gerado. Novamente, o aprendizado de máquina determina o que é relevante.

### <a name="continuous-threat-intelligence-monitoring"></a>Monitoramento contínuo de inteligência contra ameaças

A Central de Segurança do Azure opera com equipes de pesquisas de segurança e de ciência de dados no mundo inteiro que monitoram continuamente em busca de alterações no panorama de ameaças. Isso inclui as seguintes iniciativas:

-   **Monitoramento de inteligência contra ameaças**: A inteligência contra ameaças inclui mecanismos, indicadores, implicações e recomendações acionáveis sobre ameaças existentes ou iminentes. Essas informações são compartilhadas na comunidade de segurança e a Microsoft monitora continuamente os feeds de inteligência contra ameaças de fontes internas e externas.

-   **Compartilhamento de sinal**: Insights das equipes de segurança de todo o amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade de cliente são compartilhados e analisados.

-   **Especialistas de segurança da Microsoft**: Participação contínua com as equipes da Microsoft que trabalham em campos de segurança especializados, como computação forense e detecção de ataques à Web.

-   **Ajuste de detecção**: Algoritmos são executados em conjuntos de dados do cliente reais e pesquisadores de segurança trabalham com os clientes para validar os resultados. Verdadeiros e falsos positivos são usados para refinar os algoritmos de aprendizado de máquina.

Esses esforços combinados culminam em detecções novas e melhores, das quais você pode se beneficiar instantaneamente. Não há nenhuma ação a ser executada.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Recursos de detecção avançada de ameaças: Outros serviços do Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Máquinas virtuais: Microsoft Antimalware

O [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Azure é uma solução de agente único para aplicativos e ambientes de locatário, projetado para ser executado em segundo plano sem intervenção humana. Você pode implantar a proteção baseada nas necessidades de suas cargas de trabalho do aplicativo, com configuração básica padronizada ou personalizada avançada, incluindo monitoramento de antimalware. O antimalware do Azure é uma opção de segurança para máquinas virtuais do Azure instalada automaticamente em todas as máquinas virtuais de PaaS do Azure.

#### <a name="microsoft-antimalware-core-features"></a>Principais recursos do Microsoft Antimalware

Aqui estão os recursos do Azure que implantam e habilitam o Microsoft Antimalware para seus aplicativos:

-   **Proteção em tempo real**: Monitora a atividade em serviços de nuvem e em máquinas virtuais para detectar e bloquear a execução de malware.

-   **Verificação programada**: Executa periodicamente uma verificação direcionada para detectar malware, incluindo programas ativamente em execução.

-   **Remediação de malware**: Atua automaticamente no malware detectado, por exemplo, excluindo ou colocando arquivos mal-intencionados em quarentena e limpando entradas mal-intencionadas do Registro.

-   **Atualizações de assinatura**: Instala automaticamente as últimas assinaturas de proteção (definições de vírus) para garantir que a proteção fique atualizada em uma frequência predeterminada.

-   **Atualizações do Antimalware Engine**: Atualiza automaticamente o Microsoft Antimalware Engine.

-   **Atualizações da plataforma antimalware**: Atualiza automaticamente a plataforma Microsoft Antimalware.

-   **Proteção ativa**: Relata metadados telemétricos sobre ameaças detectadas e recursos suspeitos para o Microsoft Azure, a fim de garantir uma resposta rápida ao panorama de ameaças em constante evolução, habilitando a entrega de assinatura síncrona em tempo real por meio do sistema de proteção ativa da Microsoft.

-   **Relatórios de exemplos**: Fornece e relata amostras ao serviço Microsoft Antimalware para ajudar a refinar o serviço e habilitar a solução de problemas.

-   **Exclusões**: Permite que os administradores de aplicativos e de serviços configurem determinados arquivos, processos e unidades para serem excluídos da proteção e da verificação por motivos de desempenho, entre outros.

-   **Coleta de eventos de antimalware**: Registra a integridade do serviço antimalware, as atividades suspeitas e as ações de remediação realizadas no log de eventos do sistema operacional e coleta essas informações na conta de armazenamento do Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Detecção de Ameaças do Banco de Dados SQL do Azure

A [Detecção de Ameaças do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é um novo recurso de inteligência de segurança criado para o serviço Banco de Dados SQL do Azure. Trabalhando ininterruptamente para conhecer, criar perfis e detectar atividades anormais do banco de dados, a Detecção de Ameaças do Banco de Dados SQL do Azure identifica possíveis ameaças no banco de dados.

Os agentes de segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas de banco de dados conforme eles ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como investigar e minimizar a ameaça.

Atualmente, a detecção de ameaças de banco de dados SQL do Azure detecta possíveis vulnerabilidades e ataques de injeção de SQL e padrões de acesso do banco de dados anormais.

Ao receber uma notificação de detecção de ameaças por email, os usuários podem navegar e exibir os registros de auditoria relevantes por meio de um link profundo no email. O link abre um visualizador de auditoria ou um modelo de auditoria do Excel pré-configurado que mostra os registros de auditoria relevantes no momento do evento suspeito, de acordo com o seguinte:

-   Armazenamento de auditoria para o servidor/banco de dados com as atividades anormais do banco de dados.

-   Tabela de armazenamento de auditoria relevante que estava sendo usada no momento do evento para gravar no log de auditoria.

-   Registros de auditoria da hora imediatamente após a ocorrência do evento.

-   Registros de auditoria com uma ID de evento semelhante no momento do evento (opcional para alguns detectores).

Os detectores de ameaças do Banco de Dados SQL usam uma das seguintes metodologias de detecção:

-   **Detecção determinística**: Detecta padrões suspeitos (baseados em regras) nas consultas de cliente SQL que correspondem a ataques conhecidos. Essa metodologia tem um índice alto de detecção e baixo de falsos positivos, mas tem uma cobertura limitada porque está na categoria de "detecções atômicas".

-   **Detecção de comportamento**: Detecta uma atividade anormal, ou seja, um comportamento anormal no banco de dados não observado nos últimos 30 dias. Exemplos de atividade anormal de cliente SQL podem ser um pico de logons ou consultas com falha, um alto volume de dados sendo extraídos, consultas canônicas incomuns ou endereços IP usados desconhecidos para acessar o banco de dados.

### <a name="application-gateway-web-application-firewall"></a>Firewall do Aplicativo Web do Gateway de Aplicativo

O [WAF (firewall do aplicativo Web)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é um recurso do [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) que fornece proteção para aplicativos Web que usam um gateway de aplicativo para as funções padrão de [controle de entrega de aplicativos](https://kemptechnologies.com/in/application-delivery-controllers). O firewall do aplicativo Web faz isso protegendo-os contra a maioria das [10 principais vulnerabilidades comuns da Web do OWASP (Projeto Aberto de Segurança em Aplicações Web)](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagrama do firewall do aplicativo Web do Gateway de Aplicativo](./media/azure-threat-detection/azure-threat-detection-fig13.png)

As proteções incluem:

-   Proteção contra injeção de SQL.

-   Proteção contra cross-site scripting.

-   Proteção Contra Ataques Comuns na Web, como injeção de comandos, solicitações HTTP indesejadas, divisão de resposta HTTP e ataque de inclusão de arquivo remoto.

-   Proteção contra violações de protocolo HTTP.

-   Proteção contra anomalias de protocolo HTTP, como ausência de cabeçalhos de agente de usuário do host e de aceitação.

-   Prevenção contra bots, rastreadores e scanners.

-   Detecção de erros de configuração de aplicativo comuns (ou seja, Apache, IIS e assim por diante).

A configuração do WAF no gateway de aplicativo oferece os seguintes benefícios:

-   Protege o aplicativo Web contra as vulnerabilidades da Web e os ataques sem modificações no código de back-end.

-   Protege vários aplicativos Web ao mesmo tempo por trás de um gateway de aplicativo. Um gateway de aplicativo dá suporte a até 20 sites de hospedagem.

-   Monitora aplicativos Web contra ataques por meio de relatórios em tempo real que são gerados por logs do WAF do gateway de aplicativo.

-   Ajuda a atender aos requisitos de conformidade. Determinados controles de conformidade exigem que todos os pontos de extremidade voltados para a Internet sejam protegidos por uma solução de WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API de Detecção de Anomalias: Criada com o Azure Machine Learning

A API de Detecção de Anomalias é útil para detectar uma variedade de padrões anômalos em seus dados de série temporal. A API atribui uma pontuação de anomalias a cada ponto de dados na série temporal, que pode ser usada para gerar alertas, monitorar usando painéis ou conectar-se aos sistemas de emissão de tíquetes.

O [API de detecção de anomalias](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) pode detectar os seguintes tipos de anomalias em dados de série temporal:

-   **Picos e quedas**: Quando você monitora o número de falhas de logon de um serviço ou o número de check-outs em um site de comércio eletrônico, picos ou quedas incomuns podem indicar ataques de segurança ou interrupções de serviço.

-   **Tendências positivas e negativas**: Quando você monitora o uso de memória na computação, a redução do tamanho da memória livre indica uma possível perda de memória. Para o monitoramento do tamanho da fila do serviço, uma tendência de aumento persistente pode indicar um problema de software subjacente.

-   **Alterações de nível e alterações no intervalo dinâmico dos valores**: Pode ser interessante monitorar as alterações de nível nas latências de um serviço após uma atualização do serviço ou níveis mais baixos de exceções após a atualização.

A API baseada em aprendizado de máquina permite:

-   **Detecção flexível e robusta**: Os modelos de detecção de anomalias permitem que os usuários definam configurações de confidencialidade e detectem anomalias entre conjuntos de dados sazonais e não sazonais. Os usuários podem ajustar o modelo de detecção de anomalias para tornar a API de detecção diferenciação menos ou mais de acordo com suas necessidades. Isso significaria detectar as anomalias visíveis menos ou mais nos dados com e sem padrões sazonais.

-   **Detecção escalonável e em tempo hábil**: A maneira tradicional de monitorar com os limites presentes definidos pelo conhecimento de especialistas na área é cara e não pode ser escalonável para milhões de conjuntos de dados com alteração dinâmica. Os modelos de detecção de anomalias nesta API são aprendidos e ajustados automaticamente por meio de dados históricos e em tempo real.

-   **Detecção proativa e acionável**: A detecção de alteração lenta de nível e de tendência pode ser aplicada para a detecção de anomalias logo no início. Os primeiros sinais anormais detectados podem ser usados para permitir que pessoas investiguem e executem ações nas áreas com problema. Além disso, os modelos de análise de causas raiz e as ferramentas de alertas podem ser desenvolvidos com base nesse serviço de API de detecção de anomalias.

A API de detecção de anomalias é uma solução efetiva e eficiente para uma ampla variedade de cenários, como monitoramento de integridade do serviço e de KPI, IoT, monitoramento de desempenho e monitoramento de tráfego de rede. Aqui estão alguns cenários comuns onde essa API pode ser útil:

- Os departamentos de TI precisam de ferramentas para acompanhar eventos, códigos de erro, logs de uso e o desempenho (de CPU, memória e outros) em tempo hábil.

-   Sites de comércio online desejam controlar atividades do cliente, modos de exibição de página, cliques e assim por diante.

-   Utilitário de empresas deseja controlar o consumo de água e outros recursos, eletricidade e gás.

-   Os serviços de gerenciamento de instalações ou prédios precisam monitorar a temperatura, a umidade, o tráfego e assim por diante.

-   IoT/fabricantes deseja usar os dados do sensor na série de tempo para o fluxo de trabalho de monitor, qualidade e assim por diante.

-   Os provedores de serviços, como call centers, precisam monitorar as tendências na demanda do serviço, o volume de incidentes, o tamanho da fila de espera e assim por diante.

-   Os grupos de análise de negócios precisam monitorar a movimentação anormal de KPIs da empresa (como volume de vendas, sentimentos de clientes ou preços) em tempo real.

### <a name="cloud-app-security"></a>Segurança de Aplicativo de Nuvem

[Segurança de aplicativo em nuvem](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente crítico da pilha de segurança de nuvem da Microsoft. É uma solução abrangente que pode ajudar sua organização a aproveitar ao máximo a promessa dos aplicativos de nuvem à medida que os adota. Ele mantém você no controle com melhor visibilidade das atividades. Ele também ajuda a aumentar a proteção de dados críticos em aplicativos de nuvem.

Com as ferramentas que ajudam a descobrir sombra IT, avaliar os riscos, políticas, investigar atividades e evitar ameaças, sua organização com mais segurança pode mudar para a nuvem, mantendo o controle dos dados críticos.

| | |
|---|---|
| Descobrir | Descobrir a TI de sombra com o sombra IT com a Segurança de Aplicativo de Nuvem. Ganhe visibilidade, detectando aplicativos, atividades, usuários, dados e arquivos em seu ambiente de nuvem. Descubra os aplicativos de terceiros conectados à sua nuvem.|
|Investigar | Investigue os seus aplicativos de nuvem usando as ferramentas de análise forense de nuvem para aprofundar em aplicativos arriscados, usuários específicos e arquivos em sua rede. Localize padrões nos dados coletados da nuvem. Gere relatórios para monitorar sua nuvem. |
| Controle | Reduza o risco ao definir políticas e alertas para obter o máximo controle sobre o tráfego de rede na nuvem. Use a Segurança de Aplicativo de Nuvem para migrar seus usuários para alternativas de aplicativo de nuvem seguros e sancionados. |
| Proteger | Usar segurança de aplicativo de nuvem para aprovar ou impedir que os aplicativos, aplicar prevenção de perda de dados, as permissões de controle e de compartilhamento e gerar alertas e relatórios personalizados. |
| Controle | Reduza o risco ao definir políticas e alertas para obter o máximo controle sobre o tráfego de rede na nuvem. Use a Segurança de Aplicativo de Nuvem para migrar seus usuários para alternativas de aplicativo de nuvem seguros e sancionados. |
| | |


![Diagrama do Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

O Cloud App Security integra visibilidade à sua nuvem por:

-   Usar a descoberta de nuvem para mapear e identificar o seu ambiente de nuvem e os aplicativos na nuvem sua organização está usando.

-   Sancionar e proibir aplicativos em sua nuvem.

-   Usando conectores de fácil de implantar aplicativos que aproveitam APIs, de provedor para visibilidade e controle dos aplicativos que você se conectar a.

-   Ajudando você a ter controle contínuo de configuração e ajustar continuamente, políticas.

Ao coletar dados dessas fontes, o Cloud App Security executa uma análise sofisticada nos dados. Ele imediatamente alerta para atividades anormais e fornece visibilidade profunda em seu ambiente de nuvem. Você pode configurar uma política de segurança de aplicativo de nuvem e usá-lo para proteger tudo em seu ambiente de nuvem.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Funcionalidades de detecção avançada de ameaças de terceiros por meio do Azure Marketplace

### <a name="web-application-firewall"></a>Firewall do Aplicativo Web

O firewall do aplicativo Web inspeciona o tráfego da Web de entrada e bloqueia injeções de SQL, cross-site scripting, uploads de malware, ataques de DDoS ao aplicativo e outros ataques direcionados a seus aplicativos Web. Ele também inspeciona as respostas dos servidores Web de back-end quanto à DLP (prevenção contra perda de dados). O mecanismo de controle de acesso integrado permite que os administradores criem políticas de controle de acesso granulares para AAA (autenticação, autorização e auditoria), que fortalecem a autenticação e o controle de usuário das organizações.

O firewall do aplicativo Web fornece os seguintes benefícios:

-   Detecta e bloqueia SQL injeções, scripts intersites, carregamentos de malware, DDoS de aplicativo ou quaisquer outros ataques contra seu aplicativo.

-   Autenticação e controle de acesso.

-   Examina o tráfego de saída para detectar dados confidenciais e pode mascarar ou bloquear as informações sendo vazados.

-   Acelera a entrega de conteúdo do aplicativo web, usando recursos como armazenamento em cache, compactação e outras otimizações de tráfego.

Para obter exemplos de firewalls de aplicativo Web que estão disponíveis no Azure Marketplace, confira [Barracuda WAF, Brocade virtual web application firewall (vWAF), Imperva SecureSphere, and the ThreatSTOP IP firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) (Barracuda WAF, vWAF (Brocade virtual web application firewall), Imperva SecureSphere e o firewall de IP ThreatSTOP).

## <a name="next-steps"></a>Próximas etapas

- [Funcionalidades de detecção da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Ajudam a identificar ameaças ativas direcionadas aos seus recursos do Azure e fornece os insights de que você precisa para responder rapidamente a elas.

- [Detecção de Ameaças do Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Ajuda a sanar suas preocupações com possíveis ameaças aos bancos de dados.
