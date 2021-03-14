---
title: O que há de novo no Azure Sentinel
description: Este artigo descreve os novos recursos do Azure Sentinel nos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 88ac8bb1bc804604b96d5c90025b8325a6ce4962
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503106"
---
# <a name="whats-new-in-azure-sentinel"></a>O que há de novo no Azure Sentinel

Este artigo lista os recursos recentes adicionados para o Azure Sentinel e novos recursos em serviços relacionados que fornecem uma experiência de usuário aprimorada no Azure Sentinel.

Para obter informações sobre os recursos anteriores fornecidos, consulte nossos [Blogs da comunidade técnica](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Os recursos indicados estão atualmente em versão prévia. Os [termos suplementares de versão prévia do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.


> [!TIP]
> Nossas equipes de busca de ameaças em consultas, guias estratégicos, pastas de trabalho e notebooks da Microsoft contribuem com a [comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel), incluindo consultas específicas de [busca](https://github.com/Azure/Azure-Sentinel) que suas equipes podem adaptar e usar. 
>
> Você também pode contribuir! Junte-se a nós na [comunidade GitHub do Azure Sentinel Threat caçadores](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>Março de 2021

- [Integração do Microsoft 365 defender Incident](#microsoft-365-defender-incident-integration) (visualização pública)
- [Novos conectores de serviço da Microsoft usando Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="microsoft-365-defender-incident-integration"></a>Integração do Microsoft 365 defender Incident

A integração de incidentes do Azure Sentinel [Microsoft 365 defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) permite transmitir todos os incidentes do M365D para o Azure Sentinel e mantê-los sincronizados entre os dois portais. Os incidentes do M365D (anteriormente conhecido como Microsoft Threat Protection ou MTP) incluem todos os alertas associados, entidades e informações relevantes, fornecendo contexto suficiente para realizar a triagem e a investigação preliminar no Azure Sentinel. Uma vez no sentinela, os incidentes permanecerão sincronizados bidirecionalmente com o M365D, permitindo que você aproveite os benefícios de ambos os portais em sua investigação de incidentes.

O uso do Azure Sentinel e do Microsoft 365 defender juntos oferece o melhor dos dois mundos. Você Obtém a amplitude de insights que um SIEM oferece a todo o escopo de recursos de informação de sua organização e também a profundidade da capacidade de investigação personalizada e adaptada que um XDR oferece para proteger seus recursos de Microsoft 365, ambos coordenados e sincronizados para uma operação de SOC direta.

Para obter mais informações, consulte [integração do Microsoft 365 defender com o Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Novos conectores de serviço da Microsoft usando Azure Policy

[Azure Policy](../governance/policy/overview.md) é um serviço do Azure que permite que você use políticas para impor e controlar as propriedades de um recurso. O uso de políticas garante que os recursos permaneçam em conformidade com seus padrões de governança de ti.

Entre as propriedades de recursos que podem ser controladas por políticas estão a criação e manipulação de logs de diagnóstico e auditoria. O Azure Sentinel agora usa Azure Policy para permitir que você aplique um conjunto comum de configurações de logs de diagnóstico a todos os recursos (atuais e futuros) de um tipo específico cujos logs você deseja ingerir no Azure Sentinel. Graças ao Azure Policy, você não precisará mais definir o recurso de configurações dos logs de diagnóstico por recurso.

Os conectores baseados em Azure Policy agora estão disponíveis para os seguintes serviços do Azure:
- [Azure Key Vault](connect-azure-key-vault.md) (visualização pública)
- [Serviço kubernetes do Azure](connect-azure-kubernetes-service.md) (visualização pública)
- Bancos de dados/servidores SQL do Azure (GA)

Os clientes ainda poderão enviar os logs manualmente para instâncias específicas e não precisarão usar o mecanismo de política.

## <a name="february-2021"></a>Fevereiro de 2021

- [Pasta de trabalho de certificação do modelo de maturidade segurança cibernética (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Conectores de dados de terceiros](#third-party-data-connectors)
- [UEBA insights na página de entidade](#ueba-insights-in-the-entity-page)
- [Pesquisa de incidente aprimorada](#improved-incident-search)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Pasta de trabalho de certificação do modelo de maturidade segurança cibernética (CMMC)

A pasta de trabalho do Azure Sentinel CMMC fornece um mecanismo para exibir as consultas de log alinhadas aos controles CMMC no portfólio da Microsoft, incluindo as ofertas de segurança da Microsoft, o Office 365, as equipes, o Intune, a área de trabalho virtual do Windows e muito mais.

A pasta de trabalho CMMC permite que arquitetos de segurança, engenheiros, analistas de operações de segurança, gerentes e profissionais de ti tenham visibilidade de reconhecimento de situação para a postura de segurança das cargas de trabalho de nuvem. Também há recomendações para selecionar, projetar, implantar e configurar ofertas da Microsoft para alinhamento com os respectivos requisitos e práticas de CMMC.

Mesmo que você não precise estar em conformidade com o CMMC, a pasta de trabalho do CMMC é útil na criação de centros de operações de segurança, no desenvolvimento de alertas, na visualização de ameaças e no fornecimento de conscientização da situação de cargas de trabalho.

Acesse a pasta de trabalho CMMC na área de **pastas de trabalho** do Azure Sentinel. Selecione **modelo** e, em seguida, procure **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Ativar e desativar o guia de pasta de trabalho do CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Para obter mais informações, consulte:

- [Pasta de trabalho CMMC (certificação do modelo de maturidade segurança cibernética) do Azure Sentinel](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Tutorial: Visualização e monitoramento dos dados](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Conectores de dados de terceiros

Nossa coleção de integrações de terceiros continua crescendo, com trinta conectores sendo adicionados nos últimos dois meses. Aqui está uma lista:

- [Agari Phishing Defense e Brand Protection](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Alsid para Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [CylancePROTECT BlackBerry](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco potência eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [UCS (sistema de computação unificada da Cisco)](connect-cisco-ucs.md)
- [Inspetor do ESET Enterprise](connect-data-sources.md)
- [Centro de gerenciamento de segurança do ESET](connect-data-sources.md)
- [Google Workspace (antigo G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [Logs DNS do NXLog](connect-nxlog-dns.md)
- [Auditoria do NXLog Linux](connect-nxlog-linuxaudit.md)
- [Plataforma Onapsis](connect-data-sources.md)
- [POD (segurança de email) do proofpoint on Demand](connect-proofpoint-pod.md)
- [Base de dados de conhecimento do gerenciamento de vulnerabilidades Qualys](connect-data-sources.md)
- [Nuvem de serviço Salesforce](connect-salesforce-service-cloud.md)
- [Firewall do SonicWall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Proxy da Squid](connect-squid-proxy.md)
- [Endpoint Protection da Symantec](connect-data-sources.md)
- [Servidor secreto Thycotic](connect-thycotic-secret-server.md)
- [XDR da Trend Micro](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page"></a>UEBA insights na página de entidade

As páginas de detalhes da entidade Sentinela do Azure fornecem um [painel de informações](identify-threats-with-entity-behavior-analytics.md#entity-insights), que exibe informações comportamentais sobre a entidade e ajuda a identificar rapidamente anomalias e ameaças à segurança.

Se você tiver o [Ueba habilitado](ueba-enrichments.md)e tiver selecionado um período de pelo menos quatro dias, este painel de informações também incluirá as seguintes novas seções para o Ueba insights:

|Seção  |Descrição  |
|---------|---------|
|**Informações do UEBA**     | Resume as atividades anormais do usuário: <br>-Em locais geográficos, dispositivos e ambientes<br>-Em horizontes de tempo e frequência, em comparação com o próprio histórico do usuário <br>-Comparação com o comportamento de pares <br>– Em comparação com o comportamento da organização     |
|**Pares do usuário com base na associação de grupo de segurança**     |   Lista os pares do usuário com base na associação de grupos de segurança do Azure AD, fornecendo às equipes de operações de segurança uma lista de outros usuários que compartilham permissões semelhantes.  |
|**Permissões de acesso de usuário para a assinatura do Azure**     |     Mostra as permissões de acesso do usuário para as assinaturas do Azure acessíveis diretamente ou por meio de grupos/entidades de serviço do Azure AD.   |
|**Indicadores de ameaça relacionados ao usuário**     |  Lista uma coleção de ameaças conhecidas relacionadas aos endereços IP representados nas atividades do usuário. As ameaças são listadas por tipo de ameaça e família e são aprimoradas pelo serviço de inteligência contra ameaças da Microsoft.       |
|     |         |

### <a name="improved-incident-search"></a>Pesquisa de incidente aprimorada

Melhoramos a experiência de pesquisa de incidentes do Azure Sentinel, permitindo que você navegue mais rápido por meio de incidentes à medida que investiga uma ameaça específica.

Ao pesquisar incidentes no Azure Sentinel, agora você poderá pesquisar os seguintes detalhes do incidente:

- ID
- Título
- Produto
- Proprietário
- Marca

## <a name="january-2021"></a>Janeiro de 2021

- [Assistente de regra de análise: experiência de edição de consulta aprimorada (visualização pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo do PowerShell AZ. SecurityInsights (visualização pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector do banco de dados SQL](#sql-database-connector)
- [Conector do Dynamics 365](#dynamics-365-connector)
- [Comentários de incidente aprimorados](#improved-incident-comments)
- [Clusters de Log Analytics dedicados](#dedicated-log-analytics-clusters)
- [Identidades gerenciadas de aplicativos lógicos](#logic-apps-managed-identities)
- [Aprimoramento do ajuste de regras com os gráficos de visualização de regra de análise](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Assistente de regra de análise: experiência de edição de consulta aprimorada (visualização pública)

O assistente de regra de análise agendada do Azure Sentinel agora fornece os seguintes aprimoramentos para escrever e editar consultas:

-   Uma janela de edição expansível, fornecendo mais espaço na tela para exibir a consulta.
-   Realce da palavra-chave no código de consulta.
-   Suporte de preenchimento automático expandido.
-   Validações de consulta em tempo real. Os erros em sua consulta agora são mostrados como um bloco vermelho na barra de rolagem e como um ponto vermelho no nome da guia **definir lógica da regra** . Além disso, uma consulta com erros não pode ser salva.

Para obter mais informações, consulte [tutorial: criar regras de análise personalizadas para detectar ameaças](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo do PowerShell AZ. SecurityInsights (visualização pública)

O Azure Sentinel agora dá suporte ao novo módulo do PowerShell [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

O módulo **AZ. SecurityInsights** dá suporte a casos de uso comuns do Azure Sentinel, como interagir com incidentes para alterar Statues, severidade, proprietário e assim por diante, adicionando comentários e rótulos a incidentes e criando indicadores.

Embora seja recomendável usar modelos de [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) para o pipeline de CI/CD, o módulo **AZ. SecurityInsights** é útil para tarefas pós-implantação e é direcionado para a automação do SOC.  Por exemplo, a automação do SOC pode incluir etapas para configurar conectores de dados, criar regras de análise ou adicionar ações de automação a regras de análise.

Para obter mais informações, incluindo uma lista completa e uma descrição dos cmdlets disponíveis, descrições de parâmetros e exemplos, consulte a [documentação do PowerShell AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector do banco de dados SQL

O Azure Sentinel agora fornece um conector de banco de dados SQL do Azure, que você transmite os logs de auditoria e diagnóstico de seus bancos para o Azure Sentinel e monitora continuamente a atividade em todas as suas instâncias.

O SQL do Azure é um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que lida com a maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches, backups e monitoramento, sem envolvimento do usuário.

Para obter mais informações, consulte [conectar logs de auditoria e diagnóstico do banco de dados SQL do Azure](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector"></a>Conector do Dynamics 365

O Azure Sentinel agora fornece um conector para o Microsoft Dynamics 365, que permite que você colete os logs de atividade de usuário, administrador e suporte dos aplicativos do Dynamics 365 para o Azure Sentinel. Você pode usar esses dados para ajudá-lo a auditar todo o funcionamento de ações de processamento de dados e analisá-lo para possíveis violações de segurança.

Para obter mais informações, consulte [conectar logs de atividades do Dynamics 365 ao Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Comentários de incidente aprimorados

Os analistas usam comentários de incidentes para colaborar em incidentes, documentar processos e etapas manualmente ou como parte de um guia estratégico. 

Nossa experiência aprimorada de comentários sobre incidentes permite que você formate seus comentários e edite ou exclua comentários existentes.

Para obter mais informações, consulte [criar incidentes automaticamente de alertas de segurança da Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clusters de Log Analytics dedicados

O Azure Sentinel agora dá suporte a clusters de Log Analytics dedicados como uma opção de implantação. É recomendável considerar um cluster dedicado se você:

- **Ingerir mais de 1 TB por dia** em seu espaço de trabalho do Azure Sentinel
- **Ter vários espaços de trabalho do Azure Sentinel** em seu registro do Azure

Os clusters dedicados permitem usar recursos como chaves gerenciadas pelo cliente, Lockbox, criptografia dupla e consultas de espaço de trabalho mais rápidas quando você tem vários espaços de trabalho no mesmo cluster.

Para obter mais informações, consulte [Azure monitor logs de clusters dedicados](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identidades gerenciadas de aplicativos lógicos

O Azure Sentinel agora dá suporte a identidades gerenciadas para o conector de aplicativos lógicos da Sentinela do Azure, permitindo que você conceda permissões diretamente a um guia estratégico específico para operar no Azure Sentinel em vez de criar identidades extras.

- **Sem uma identidade gerenciada**, o conector de aplicativos lógicos requer uma identidade separada com uma função RBAC do Azure Sentinel para ser executado no Azure Sentinel. A identidade separada pode ser um usuário do Azure AD ou uma entidade de serviço, como um aplicativo registrado do Azure AD.

- **Ativar o suporte de identidade gerenciada em seu aplicativo lógico** registra o aplicativo lógico com o Azure AD e fornece uma ID de objeto. Use a ID de objeto no Azure Sentinel para atribuir o aplicativo lógico com uma função de RBAC do Azure em seu espaço de trabalho do Azure Sentinel. 

Para obter mais informações, consulte:

- [Autenticando com identidade gerenciada em aplicativos lógicos do Azure](../logic-apps/create-managed-service-identity.md)
- [Documentação do conector de aplicativos lógicos do Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Aprimoramento do ajuste de regras com os gráficos de visualização de regra de análise (visualização pública)

O Azure Sentinel agora ajuda você a ajustar melhor suas regras de análise, ajudando-o a aumentar sua precisão e diminuir o ruído.

Depois de editar uma regra de análise na guia **definir lógica de regra** , localize a área de **simulação de resultados** à direita. 

Selecione **testar com os dados atuais** para que o Azure Sentinel execute uma simulação das últimas 50 execuções da regra de análise. Um grafo é gerado para mostrar o número médio de alertas que a regra teria gerado, com base nos dados de eventos brutos avaliados. 

Para obter mais informações, consulte [definir a lógica de consulta da regra e definir as configurações](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dezembro de 2020

- [80 novas consultas de busca interna](#80-new-built-in-hunting-queries)
- [Aprimoramentos do agente de Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 novas consultas de busca interna
 
As consultas de busca interna do Sentinela do Azure capacitam os analistas do SOC para reduzir as lacunas na cobertura de detecção atual e Ignite novos leads de busca.

Esta atualização para o Azure Sentinel inclui novas consultas de busca que fornecem cobertura na matriz do MITRE ATT&CK Framework:

- **Coleção**
- **Comando e controle**
- **Acesso à credencial**
- **Discovery**
- **Execução**
- **Exfiltração**
- **Impacto**
- **Acesso inicial**
- **Persistência**
- **Elevação de privilégio**

As consultas de busca adicionadas foram projetadas para ajudá-lo a encontrar atividades suspeitas em seu ambiente. Embora possam retornar atividades legítimas e atividades potencialmente mal-intencionadas, elas podem ser úteis na orientação de sua busca. 

Se, depois de executar essas consultas, você estiver confiante com os resultados, talvez queira convertê-los em regras de análise ou adicionar resultados de busca a incidentes novos ou existentes.

Todas as consultas adicionadas estão disponíveis por meio da página de busca do Sentinela do Azure. Para obter mais informações, consulte procurar [ameaças com o Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Aprimoramentos do agente de Log Analytics

Os usuários do Azure Sentinel se beneficiam dos seguintes aprimoramentos do agente Log Analytics:

- **Suporte para sistemas operacionais mais**, incluindo CentOS 8, RedHat 8 e SuSE Linux 15.
- **Suporte para Python 3** além do Python 2

O Azure Sentinel usa o agente de Log Analytics para enviar eventos para seu espaço de trabalho, incluindo eventos de segurança do Windows, eventos de syslog, logs de CEF e muito mais.

> [!NOTE]
> O agente de Log Analytics, às vezes, é chamado de agente do OMS ou o Microsoft Monitoring Agent (MMA). 
> 

Para obter mais informações, consulte a [documentação do log Analytics](../azure-monitor/agents/log-analytics-agent.md) e as notas de versão do agente de [log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembro de 2020

- [Monitore a integridade dos guias estratégicos no Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Conector do Microsoft 365 defender (visualização pública)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitore a integridade dos guias estratégicos no Azure Sentinel

Os guias estratégicos do Azure Sentinel se baseiam em fluxos de trabalho criados nos [aplicativos de log do Azure](../logic-apps/index.yml), um serviço de nuvem que ajuda a agendar, automatizar e orquestrar tarefas, processos de negócios e fluxos de trabalho. Os guias estratégicos podem ser invocados automaticamente quando um incidente é criado ou durante a triagem e o trabalho com incidentes. 

Para fornecer informações sobre a integridade, o desempenho e o uso de seus guias estratégicos, adicionamos uma [pasta de trabalho](../azure-monitor/visualize/workbooks-overview.md) denominada monitoramento de **integridade de guias estratégicos**. 

Use a pasta de trabalho de **monitoramento de integridade dos guias estratégicos** para monitorar a integridade de seus guias estratégicos ou Procure anomalias na quantidade de execuções com êxito ou com falha. 

A pasta de trabalho de **monitoramento de integridade dos guias estratégicos** agora está disponível na Galeria de modelos do Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemplo de pasta de trabalho de monitoramento de integridade de guias":::

Para obter mais informações, consulte:

- [Documentação dos aplicativos lógicos](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentação do Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Conector do Microsoft 365 defender (visualização pública)
 
O conector do Microsoft 365 defender para o Azure Sentinel permite que você transmita logs de busca avançados (um tipo de dados brutos de evento) do Microsoft 365 defender para o Azure Sentinel. 

Com a integração do [Microsoft defender for Endpoint (MDATP)](/windows/security/threat-protection/) com a proteção de segurança do [Microsoft 365 defender](/microsoft-365/security/mtp/microsoft-threat-protection) , agora você pode coletar seus eventos de busca avançada do Microsoft defender para pontos de extremidade usando o conector do Microsoft 365 defender e transmiti-los diretamente para novas tabelas criadas no seu espaço de trabalho do Azure Sentinel. 

As tabelas Sentinela do Azure são criadas no mesmo esquema usado no portal do Microsoft 365 defender e fornecem acesso completo ao conjunto completo de logs de busca avançados. 

Para obter mais informações, consulte [conectar dados do Microsoft 365 defender ao Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> O Microsoft 365 defender era conhecido anteriormente como Microsoft Threat Protection ou MTP. O Microsoft defender for Endpoint era conhecido anteriormente como proteção avançada contra ameaças do Microsoft defender ou MDATP.
> 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Azure Sentinel integrado](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenha visibilidade sobre os alertas](quickstart-get-visibility.md)
