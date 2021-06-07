---
title: Novidades do Azure Sentinel
description: Confira neste artigo os novos recursos do Azure Sentinel nos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 74cd5ce5912e5a656342a43b7c2b165fdf3c32d4
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490277"
---
# <a name="whats-new-in-azure-sentinel"></a>Novidades do Azure Sentinel

Este artigo mostra os recursos adicionados ao Azure Sentinel recentemente, além de novos recursos em serviços relacionados que aprimoram a experiência do usuário nessa solução.

Para saber mais sobre os recursos oferecidos anteriormente, confira nossos [blogs da Tech Community](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Os recursos indicados estão em VERSÃO PRÉVIA no momento. Os [termos suplementares de versão prévia do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.


> [!TIP]
> Nossas equipes de busca de ameaças em toda a Microsoft contribuem com consultas, guias estratégicos, pastas de trabalho e notebooks para a [Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel), incluindo [consultas de busca](https://github.com/Azure/Azure-Sentinel) específicas que suas equipes podem adaptar e usar. 
>
> Você também pode contribuir! Participe da [comunidade do GitHub de caçadores de ameaças do Azure Sentinel](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>Março de 2021

- [Definir a atualização automática de pastas de trabalho no modo de exibição](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Novas detecções para o Firewall do Azure](#new-detections-for-azure-firewall)
- [Regras de automação e guias estratégicos disparados por incidentes](#automation-rules-and-incident-triggered-playbooks) (incluindo todas as novas documentações do guia estratégico)
- [Novos enriquecimentos de alertas: mapeamento de entidade aprimorado e detalhes personalizados](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Imprimir pastas de trabalho do Azure Sentinel ou salvar como PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtros de incidentes e preferências de classificação agora são salvos em sua sessão (versão prévia pública)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integração de incidentes do Microsoft 365 Defender (versão prévia pública)](#microsoft-365-defender-incident-integration-public-preview)
- [Novos conectores de serviço da Microsoft usando o Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Definir a atualização automática de pastas de trabalho no modo de exibição

Agora os usuários do Azure Sentinel podem usar a nova [capacidade do Azure Monitor](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) de atualizar automaticamente os dados da pasta de trabalho durante uma sessão de exibição.

Em cada pasta de trabalho ou modelo de pasta de trabalho, clique em :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Atualização automática** para ver as opções de intervalo. Escolha a opção desejada para a sessão de exibição atual e clique em **Aplicar**.

- Os intervalos de atualização disponíveis variam entre **5 minutos** e **1 dia**.
- A atualização automática fica desabilitada por padrão. Além disso, para otimizar o desempenho, também é desabilitada toda vez que você fecha uma pasta de trabalho e não fica em execução em segundo plano. Quando precisar, basta habilitar o recurso novamente na próxima vez que abrir a pasta de trabalho.
- A atualização automática fica em pausa enquanto você edita uma pasta de trabalho. Os intervalos são reiniciados toda vez que você sai do modo de edição e volta para o modo de exibição.

    Os intervalos também serão reiniciados se você atualizar manualmente a pasta de trabalho clicando no botão :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Atualizar**.

Para saber mais, confira [Tutorial: visualização e monitoramento de dados](tutorial-monitor-your-data.md) e a [documentação do Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Novas detecções para o Firewall do Azure

Várias detecções prontas para uso do Firewall do Azure foram adicionadas à área [Análise](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) do Azure Sentinel. Com essas novas detecções, as equipes de segurança podem receber alertas caso os computadores na rede interna tentem consultar ou se conectar a nomes de domínio de Internet ou endereços IP associados com ICs conhecidos, conforme definido na consulta de regra de detecção.

As novas detecções incluem:

- [Beacon de rede Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Domínios e hashes GALLIUM conhecidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [IP IRIDIUM conhecido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Domínios/IP do grupo Phosphorus conhecidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domínios THALLIUM incluídos no desarmamento do DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Hash maldoc relacionado ao ZINC conhecido](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Domínios de grupo STRONTIUM conhecidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM – Domínio e IP ICs – março de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


As detecções para o Firewall do Azure estão sendo continuamente adicionadas à galeria de modelos interna. Para obter as detecções mais recentes do Firewall do Azure, em **Modelos de Regra**, filtre **Fontes de Dados** pelo **Firewall do Azure**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Novas detecções na pasta de trabalho de eficiência da Análise":::

Para saber mais, confira [Novas detecções para o Firewall do Azure no Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Regras de automação e guias estratégicos disparados por incidentes

As regras de automação são um novo conceito no Azure Sentinel. Esse recurso permite gerenciar de modo centralizado a automação do tratamento de incidentes. Além de permitir que você atribua guias estratégicos a incidentes (e não somente alertas, como antes), as regras de automação também permitem automatizar respostas para várias regras de análise de uma só vez, marcar automaticamente ou fechar incidentes sem precisar de guias estratégicos e controlar a ordem das ações executadas. As regras de automação facilitam o uso da automação no Azure Sentinel e permitem simplificar fluxos de trabalho complexos para processos de orquestração de incidentes.

Saiba mais com esta [explicação completa das regras de automação](automate-incident-handling-with-automation-rules.md).

Conforme mencionado acima, os guias estratégicos agora podem ser ativados com o gatilho de incidentes, além do gatilho de alertas. O gatilho de incidentes oferece aos guias estratégicos um conjunto maior de entradas com as quais trabalhar (já que o incidente também inclui todos os dados de alerta e entidade), proporcionando ainda mais eficiência e flexibilidade nos fluxos de trabalho de resposta. Os guias estratégicos disparados por incidentes são ativados por meio de uma chamada de regras de automação.

Saiba mais sobre as [funcionalidades aprimoradas dos guias estratégicos](automate-responses-with-playbooks.md) e como [criar um fluxo de trabalho de resposta](tutorial-respond-threats-playbook.md) usando os guias estratégicos em conjunto com as regras de automação.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Novos enriquecimentos de alertas: mapeamento de entidade aprimorado e detalhes personalizados

Existem duas novas maneiras de enriquecer seus alertas e torná-los mais utilizáveis e informativos.

Comece aprimorando o mapeamento de entidades. Agora é possível mapear quase 20 tipos de entidades, de usuários, hosts e endereços IP até arquivos, processos, caixas de correio, recursos do Azure e dispositivos IoT. Também é possível usar diversos identificadores para cada entidade para fortalecer a identificação exclusiva. Dessa maneira, você tem um conjunto de dados muito mais rico nos seus incidentes, com uma correlação mais ampla e uma investigação mais potente. [Conheça a nova maneira de mapear entidades](map-data-fields-to-entities.md) nos alertas.

[Leia mais sobre as entidades](entities-in-azure-sentinel.md) e confira a [lista completa de entidades disponíveis e seus identificadores](entities-reference.md).

Personalize seus alertas para aumentar ainda mais a potência das funcionalidades de investigação e resposta e mostrar detalhes de eventos brutos. Dê visibilidade ao conteúdo do evento nos incidentes e tenha mais poder e flexibilidade ao responder e investigar as ameaças de segurança. [Saiba como mostrar detalhes personalizados](surface-custom-details-in-alerts.md) nos alertas.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Imprimir pastas de trabalho do Azure Sentinel ou salvar como PDF

Agora é possível imprimir as pastas de trabalho do Azure Sentinel, o que também permite exportar como PDF e salvar localmente ou compartilhar.

Na pasta de trabalho, selecione as opções Menu > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimir conteúdo**. Em seguida, selecione a impressora ou clique em **Salvar como PDF**, conforme o necessário.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Imprima a pasta de trabalho ou salve como PDF.":::

Para saber mais, confira [Tutorial: visualização e monitoramento de dados](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtros de incidentes e preferências de classificação agora são salvos em sua sessão (versão prévia pública)

Agora os filtros de incidentes e a classificação são salvos ao longo da sessão no Azure Sentinel, mesmo quando você navega para outras áreas do produto.
Desde que ainda esteja na mesma sessão, navegar de volta para a área [Incidentes](tutorial-investigate-cases.md) no Azure Sentinel mostra os filtros e a classificação do mesmo jeito que você deixou.

> [!NOTE]
> Os filtros de incidentes e a classificação não são salvos depois que você sai do Azure Sentinel ou atualiza o navegador.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integração de incidentes do Microsoft 365 Defender (versão prévia pública)

A integração de incidentes do [M365D (Microsoft 365 Defender)](/microsoft-365/security/mtp/microsoft-threat-protection) no Azure Sentinel permite transmitir todos os incidentes do M365D para o Azure Sentinel e mantê-los sincronizados entre os dois portais. Os incidentes do M365D (conhecido anteriormente como MTP [ou Proteção contra ameaças da Microsoft]) incluem todos os alertas, entidades e informações relevantes associados, dando contexto suficiente para realizar a triagem e a investigação preliminar no Azure Sentinel. No Sentinel, os incidentes permanecerão sincronizados de modo bidirecional com o M365D. Assim, você aproveita os benefícios de ambos os portais na investigação de incidentes.

Use o Azure Sentinel e o Microsoft 365 Defender juntos para ter o melhor dos dois mundos. Você tem a amplitude de insights de um SIEM em todo o escopo de recursos de informação da organização, além da profundidade da capacidade de investigação personalizada e adaptada que o XDR oferece para proteger seus recursos do Microsoft 365, ambos coordenados e sincronizados para uma operação SOC direta.

Para saber mais, confira [Integração do Microsoft 365 Defender com o Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Novos conectores de serviço da Microsoft usando o Azure Policy

O [Azure Policy](../governance/policy/overview.md) é um serviço do Azure que permite usar políticas para impor e controlar as propriedades de um recurso. O uso de políticas garante que os recursos permaneçam em conformidade com seus padrões de governança de TI.

Entre as propriedades de recursos que podem ser controladas por políticas estão a criação e a manipulação de diagnóstico e logs de auditoria. O Azure Sentinel agora usa o Azure Policy para permitir que você aplique um conjunto comum de configurações de logs de diagnóstico a todos os recursos (atuais e futuros) de um tipo específico cujos logs você deseja ingerir no Azure Sentinel. Por causa do Azure Policy, você não precisa mais definir as configurações de logs de diagnóstico por recurso.

Os conectores baseados no Azure Policy já estão disponíveis para os seguintes serviços do Azure:
- [Azure Key Vault](connect-azure-key-vault.md) (versão prévia pública)
- [Serviço de Kubernetes do Azure](connect-azure-kubernetes-service.md) (versão prévia pública)
- Bancos de dados/servidores SQL do Azure (GA)

Os clientes ainda podem enviar os logs manualmente para instâncias específicas e não precisam usar o mecanismo de política.

## <a name="february-2021"></a>Fevereiro de 2021

- [Pasta de trabalho de CMMC (Certificação do Modelo de Maturidade da Segurança Cibernética)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Conectores de dados de terceiros](#third-party-data-connectors)
- [Insights de UEBA na página de entidade (versão prévia pública)](#ueba-insights-in-the-entity-page-public-preview)
- [Pesquisa de incidentes aprimorada (versão prévia pública)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Pasta de trabalho de CMMC (Certificação do Modelo de Maturidade da Segurança Cibernética)

A Pasta de Trabalho de CMMC do Azure Sentinel oferece um mecanismo para exibir as consultas de log alinhadas com os controles de CMMC no portfólio da Microsoft, incluindo as ofertas de segurança da Microsoft, o Office 365, o Teams, o Intune, a Área de Trabalho Virtual do Windows e muito mais.

A pasta de trabalho de CMMC permite que arquitetos de segurança, engenheiros, analistas de operações de segurança, gerentes e profissionais de TI tenham visibilidade de reconhecimento de situação da postura de segurança das cargas de trabalho de nuvem. Também há recomendações para selecionar, projetar, implantar e configurar as ofertas da Microsoft para alinhamento com os respectivos requisitos e práticas de CMMC.

Mesmo que você não precise estar em conformidade com o CMMC, a pasta de trabalho de CMMC é útil na criação de centros de operações de segurança, no desenvolvimento de alertas, na visualização de ameaças e na conscientização sobre a situação de cargas de trabalho.

Acesse a pasta de trabalho de CMMC na área **Pastas de Trabalho** do Azure Sentinel. Selecione **Modelo** e pesquise **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Ativar e desativar o guia da pasta de trabalho de CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Para obter mais informações, consulte:

- [Pasta de trabalho de CMMC (Certificação do Modelo de Maturidade da Segurança Cibernética) do Azure Sentinel](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Tutorial: Visualização e monitoramento dos dados](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Conectores de dados de terceiros

Nossa coleção de integrações de terceiros continua crescendo, com 30 conectores adicionados nos últimos dois meses. Veja uma lista:

- [Agari Phishing Defense e Brand Protection](connect-agari-phishing-defense.md)
- [Akamai Security Events](connect-akamai-security-events.md)
- [Alsid para Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (antigo G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS Logs](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Segurança de Email do POD (Proofpoint On Demand)](connect-proofpoint-pod.md)
- [Base de dados de conhecimento do Qualys Vulnerability Management](connect-data-sources.md)
- [Nuvem de serviço Salesforce](connect-salesforce-service-cloud.md)
- [Firewall do SonicWall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Proxy da Squid](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Servidor secreto Thycotic](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Insights de UEBA na página de entidade (versão prévia pública)

As páginas de detalhes da entidade do Azure Sentinel contam com um [painel Insights](identify-threats-with-entity-behavior-analytics.md#entity-insights) que mostra insights comportamentais sobre a entidade e ajuda a identificar rapidamente anomalias e ameaças de segurança.

Se o [UEBA estiver habilitado](ueba-enrichments.md) e você tiver selecionado um período de pelo menos quatro dias, o painel Insights também incluirá as seguintes novas seções para insights do UEBA:

|Seção  |Descrição  |
|---------|---------|
|**Insights do UEBA**     | Resume as atividades anormais do usuário: <br>– Em locais geográficos, dispositivos e ambientes<br>– Em horizontes de tempo e frequência, em comparação com o histórico do usuário <br>– Em comparação com o comportamento dos pares <br>– Em comparação com o comportamento da organização     |
|**Pares do usuário com base na associação de grupo de segurança**     |   Lista os pares do usuário com base na associação de grupos de segurança do Azure AD, oferecendo às equipes de operações de segurança uma lista de outros usuários que compartilham permissões semelhantes.  |
|**Permissões de acesso de usuário para a assinatura do Azure**     |     Mostra as permissões de acesso do usuário para as assinaturas do Azure acessíveis diretamente ou por meio de grupos/entidades de serviço do Azure AD.   |
|**Indicadores de ameaça relacionados ao usuário**     |  Lista uma coleção de ameaças conhecidas relacionadas aos endereços IP representados nas atividades do usuário. As ameaças são listadas por tipo e família e são enriquecidas pelo serviço de inteligência contra ameaças da Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Pesquisa de incidentes aprimorada (versão prévia pública)

A experiência de pesquisa de incidentes no Azure Sentinel foi aprimorada para que você possa navegar mais rápido pelos incidentes enquanto investiga uma ameaça específica.

Ao pesquisar incidentes no Azure Sentinel, agora você pode ver os seguintes detalhes:

- ID
- Título
- Produto
- Proprietário
- Marca

## <a name="january-2021"></a>Janeiro de 2021

- [Assistente da regra de análise: experiência de edição de consulta aprimorada (versão prévia pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo do PowerShell Az.SecurityInsights (versão prévia pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector do banco de dados SQL](#sql-database-connector)
- [Conector do Dynamics 365 (versão prévia pública)](#dynamics-365-connector-public-preview)
- [Comentários sobre incidentes aprimorados](#improved-incident-comments)
- [Clusters dedicados do Log Analytics](#dedicated-log-analytics-clusters)
- [Identidades gerenciadas de aplicativos lógicos](#logic-apps-managed-identities)
- [Aprimoramento do ajuste de regras com os grafos de visualização de regra de análise](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Assistente da regra de análise: experiência de edição de consulta aprimorada (versão prévia pública)

O assistente de regra de Análise Agendada do Azure Sentinel agora conta com os seguintes aprimoramentos para escrever e editar consultas:

-   Uma janela de edição expansível, com mais espaço na tela para ver a consulta.
-   Realce da palavra-chave no código de consulta.
-   Suporte para o preenchimento automático expandido.
-   Validações de consulta em tempo real. Os erros na consulta agora são mostrados como um bloco vermelho na barra de rolagem e como um ponto vermelho no nome da guia **Definir lógica da regra**. Além disso, uma consulta com erros não pode ser salva.

Para saber mais, confira [Tutorial: criar regras de análise personalizadas para detectar ameaças](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo do PowerShell Az.SecurityInsights (versão prévia pública)

O Azure Sentinel agora é compatível com o novo módulo do PowerShell [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

O módulo **Az.SecurityInsights** é compatível com casos de uso comuns do Azure Sentinel, como a interação com incidentes para alterar status, severidade, proprietário etc., a inclusão de comentários e rótulos a incidentes e a criação de indicadores.

Embora seja recomendável usar os modelos do [ARM (Azure Resource Manager)](../azure-resource-manager/templates/index.yml) no pipeline de CI/CD, o módulo **Az.SecurityInsights** é útil para tarefas pós-implantação e é direcionado para a automação do SOC.  Por exemplo, a automação do SOC pode incluir etapas para configurar conectores de dados, criar regras de análise ou adicionar ações de automação a regras de análise.

Para saber mais, incluindo uma lista completa e a descrição dos cmdlets disponíveis, as descrições de parâmetros e exemplos, confira a [documentação do PowerShell Az.SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector do banco de dados SQL

O Azure Sentinel agora conta com um conector do Banco de Dados SQL do Azure para transmitir os logs de diagnóstico e auditoria do seu banco de dados para o Azure Sentinel e monitorar continuamente a atividade em todas as suas instâncias.

O SQL do Azure é um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que realiza a maior parte das funções de gerenciamento de banco de dados, como atualização, aplicação de patch, backups e monitoramento, sem o envolvimento do usuário.

Para saber mais, confira [Conectar logs de auditoria e de diagnóstico de banco de dados SQL do Azure](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Conector do Dynamics 365 (versão prévia pública)

O Azure Sentinel agora conta com um conector para o Microsoft Dynamics 365, que permite coletar os logs de atividade de usuário, administrador e suporte dos aplicativos do Dynamics 365 para o Azure Sentinel. É possível usar esses dados para ajudar a auditar todas as ações de processamento de dados em execução e analisar se há possíveis violações de segurança nelas.

Para saber mais, confira [Conectar os logs de atividades do Dynamics 365 ao Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Comentários sobre incidentes aprimorados

Os analistas usam os comentários para colaborar em incidentes, documentar processos e etapas manualmente ou como parte de um guia estratégico. 

Nossa experiência aprimorada de comentários sobre incidentes permite formatar comentários e editar ou excluir comentários existentes.

Para saber mais, confira [Criar incidentes automaticamente nos alertas de segurança da Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clusters dedicados do Log Analytics

O Azure Sentinel agora é compatível com os clusters dedicados do Log Analytics como uma opção de implantação. É recomendável considerar um cluster dedicado se você:

- **Ingere mais de 1 TB por dia** no seu workspace do Azure Sentinel
- **Tem vários workspaces do Azure Sentinel** em seu registro do Azure

Os clusters dedicados permitem usar recursos como chaves gerenciadas pelo cliente, sistema de proteção de dados, criptografia dupla e consultas de workspace mais rápidas quando você tem vários workspaces no mesmo cluster.

Para saber mais, confira [Clusters dedicados dos logs do Azure Monitor](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identidades gerenciadas de aplicativos lógicos

O Azure Sentinel agora é compatível com identidades gerenciadas para o conector de Aplicativos Lógicos do Azure Sentinel, o que permite conceder permissões diretamente a um guia estratégico específico para operar no Azure Sentinel em vez de criar identidades extras.

- **Sem uma identidade gerenciada**, o conector dos Aplicativos Lógicos requer uma identidade diferente com uma função RBAC do Azure Sentinel para ser executado no Azure Sentinel. A identidade diferente pode ser um usuário do Azure AD ou uma entidade de serviço, como um aplicativo registrado do Azure AD.

- **Ativar o suporte para identidade gerenciada no Aplicativo Lógico** registra o Aplicativo Lógico no Azure AD e fornece uma ID de objeto. Use a ID de objeto no Azure Sentinel para atribuir o Aplicativo Lógico com uma função de RBAC do Azure ao seu workspace do Azure Sentinel. 

Para obter mais informações, consulte:

- [Autenticar com identidade gerenciada nos Aplicativos Lógicos do Azure](../logic-apps/create-managed-service-identity.md)
- [Documentação do conector dos Aplicativos Lógicos do Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Aprimoramento do ajuste de regras com os grafos de visualização de regra de análise (versão prévia pública)

O Azure Sentinel agora ajuda você a ajustar melhor as regras de análise para aumentar a precisão e diminuir o ruído.

Após editar uma regra de análise na guia **Definir lógica de regra**, localize a área **Simulação de resultados**. 

Selecione **Testar com os dados atuais** para que o Azure Sentinel faça uma simulação das últimas 50 execuções da regra de análise. Um grafo será criado para mostrar o número médio de alertas que a regra teria gerado com base nos dados de eventos brutos avaliados. 

Para saber mais, confira [Definir a lógica de consulta de regra e as configurações](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dezembro de 2020

- [80 novas consultas de busca interna](#80-new-built-in-hunting-queries)
- [Aprimoramentos do agente do Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 novas consultas de busca interna
 
As consultas de busca interna do Azure Sentinel permitem que os analistas do SOC eliminem as lacunas na cobertura de detecção atual e iniciem novos leads de busca.

Esta atualização do Azure Sentinel inclui novas consultas de busca que cobrem toda a matriz da estrutura MITRE ATT&CK:

- **Coleção**
- **Comando e controle**
- **Acesso com credencial**
- **Discovery**
- **Execução**
- **Exfiltração**
- **Impacto**
- **Acesso inicial**
- **Persistência**
- **Escalonamento de Privilégios**

As consultas de busca adicionadas foram criadas para ajudar você a encontrar atividades suspeitas em seu ambiente. Embora possam retornar atividades legítimas e potencialmente mal-intencionadas, elas podem ser úteis na orientação de sua busca. 

Se depois de executar essas consultas, você estiver confiante com os resultados, talvez queira convertê-los em regras de análise ou adicionar resultados da busca a incidentes novos ou existentes.

Todas as consultas adicionadas estão disponíveis na página "Busca" do Azure Sentinel. Para saber mais, confira [Buscar ameaças com o Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Aprimoramentos do agente do Log Analytics

Os usuários do Azure Sentinel se beneficiam dos seguintes aprimoramentos do agente do Log Analytics:

- **Suporte para mais sistemas operacionais**, incluindo CentOS 8, RedHat 8 e SuSE Linux 15.
- **Suporte para Python 3**, além do Python 2

O Azure Sentinel usa o agente do Log Analytics para enviar eventos para o seu workspace, incluindo eventos de segurança do Windows, eventos de Syslogv, logs de CEF e muito mais.

> [!NOTE]
> O agente do Log Analytics às vezes é chamado de agente do OMS ou MMA (Microsoft Monitoring Agent). 
> 

Para saber mais, confira a [documentação do Log Analytics](../azure-monitor/agents/log-analytics-agent.md) e as [notas sobre a versão do agente do Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembro de 2020

- [Monitorar a integridade dos guias estratégicos no Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Conector do Microsoft 365 Defender (versão prévia pública)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitorar a integridade dos guias estratégicos no Azure Sentinel

Os guias estratégicos do Azure Sentinel se baseiam em fluxos de trabalho criados nos [Aplicativos Lógicos do Azure](../logic-apps/index.yml), um serviço de nuvem que ajuda a agendar, automatizar e orquestrar tarefas, processos de negócios e fluxos de trabalho. Os guias estratégicos podem ser invocados automaticamente quando um incidente é criado ou durante a triagem e o trabalho com incidentes. 

Para ver insights sobre a integridade, o desempenho e o uso dos guias estratégicos, incluímos uma [pasta de trabalho](../azure-monitor/visualize/workbooks-overview.md) chamada **Monitoramento de integridade de guias estratégicos**. 

Use a pasta de trabalho **Monitoramento de integridade de guias estratégicos** para monitorar a integridade dos guias estratégicos ou procurar anomalias no número de execuções com êxito ou com falha. 

A pasta de trabalho **Monitoramento de integridade de guias estratégicos** já está disponível na galeria de modelos do Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemplo de pasta de trabalho de monitoramento de integridade dos guias estratégicos":::

Para obter mais informações, consulte:

- [Documentação dos Aplicativos Lógicos](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentação do Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Conector do Microsoft 365 Defender (versão prévia pública)
 
O conector do Microsoft 365 Defender para o Azure Sentinel permite que transmitir logs de busca avançados (um tipo de dados de eventos brutos) do Microsoft 365 Defender para o Azure Sentinel. 

Com a integração do [MDATP (Microsoft Defender para Ponto de Extremidade)](/windows/security/threat-protection/) com a proteção de segurança do [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection), é possível coletar os eventos de busca avançada do Microsoft Defender para Ponto de Extremidade usando o conector do Microsoft 365 Defender e transmiti-los diretamente para as novas tabelas criadas no workspace do Azure Sentinel. 

Essas tabelas do Azure Sentinel são criadas no mesmo esquema usado no portal do Microsoft 365 Defender, dando acesso total ao conjunto completo de logs de busca avançada. 

Para saber mais, confira [Conectar dados do Microsoft 365 Defender ao Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> O Microsoft 365 Defender era conhecido anteriormente como MTP (ou Proteção contra ameaças da Microsoft). O Microsoft Defender para Ponto de Extremidade era conhecido anteriormente como MDATP (ou Proteção Avançada contra Ameaças do Microsoft Defender).
> 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Integração do Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtenha visibilidade sobre os alertas](quickstart-get-visibility.md)
