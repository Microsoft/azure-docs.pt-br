---
title: O que há de novo no Azure Sentinel
description: Este artigo descreve os novos recursos do Azure Sentinel nos últimos meses.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 9136947767bffb7bea800cdd2a735794baf8f329
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007356"
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

## <a name="january-2021"></a>Janeiro de 2021

- [Assistente de regra de análise: experiência de edição de consulta aprimorada (visualização pública)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Módulo do PowerShell AZ. SecurityInsights (visualização pública)](#azsecurityinsights-powershell-module-public-preview)
- [Conector do banco de dados SQL](#sql-database-connector)
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

Para obter mais informações, consulte [tutorial: detectar ameaças prontas para uso](tutorial-detect-threats-built-in.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Módulo do PowerShell AZ. SecurityInsights (visualização pública)

O Azure Sentinel agora dá suporte ao novo módulo do PowerShell [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

O módulo **AZ. SecurityInsights** dá suporte a casos de uso comuns do Azure Sentinel, como interagir com incidentes para alterar Statues, severidade, proprietário e assim por diante, adicionando comentários e rótulos a incidentes e criando indicadores.

Embora seja recomendável usar modelos de [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) para o pipeline de CI/CD, o módulo **AZ. SecurityInsights** é útil para tarefas pós-implantação e é direcionado para a automação do SOC.  Por exemplo, a automação do SOC pode incluir etapas para configurar conectores de dados, criar regras de análise ou adicionar ações de automação a regras de análise.

Para obter mais informações, incluindo uma lista completa e uma descrição dos cmdlets disponíveis, descrições de parâmetros e exemplos, consulte a [documentação do PowerShell AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Conector do banco de dados SQL

O Azure Sentinel agora fornece um conector de banco de dados SQL do Azure, que você transmite os logs de auditoria e diagnóstico de seus bancos para o Azure Sentinel e monitora continuamente a atividade em todas as suas instâncias.

O SQL do Azure é um mecanismo de banco de dados PaaS (plataforma como serviço) totalmente gerenciado que lida com a maioria das funções de gerenciamento de banco de dados, como atualização, aplicação de patches, backups e monitoramento, sem envolvimento do usuário.

Para obter mais informações, consulte [conectar logs de auditoria e diagnóstico do banco de dados SQL do Azure](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Comentários de incidente aprimorados

Os analistas usam comentários de incidentes para colaborar em incidentes, documentar processos e etapas manualmente ou como parte de um guia estratégico. 

Nossa experiência aprimorada de comentários sobre incidentes permite que você formate seus comentários e edite ou exclua comentários existentes.

Para obter mais informações, consulte [criar incidentes automaticamente de alertas de segurança da Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Clusters de Log Analytics dedicados

O Azure Sentinel agora dá suporte a clusters de Log Analytics dedicados como uma opção de implantação. É recomendável considerar um cluster dedicado se você:

- **Ingerir mais de 1 TB por dia** em seu espaço de trabalho do Azure Sentinel
- **Ter vários espaços de trabalho do Azure Sentinel** em seu registro do Azure

Os clusters dedicados permitem usar recursos como chaves gerenciadas pelo cliente, Lockbox, criptografia dupla e consultas de espaço de trabalho mais rápidas quando você tem vários espaços de trabalho no mesmo cluster.

Para obter mais informações, consulte [Azure monitor logs de clusters dedicados](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Identidades gerenciadas de aplicativos lógicos

O Azure Sentinel agora dá suporte a identidades gerenciadas para o conector de aplicativos lógicos da Sentinela do Azure, permitindo que você conceda permissões diretamente a um guia estratégico específico para operar no Azure Sentinel em vez de criar identidades extras.

- **Sem uma identidade gerenciada**, o conector de aplicativos lógicos requer uma identidade separada com uma função RBAC do Azure Sentinel para ser executado no Azure Sentinel. A identidade separada pode ser um usuário do Azure AD ou uma entidade de serviço, como um aplicativo registrado do Azure AD.

- **Ativar o suporte de identidade gerenciada em seu aplicativo lógico** registra o aplicativo lógico com o Azure AD e fornece uma ID de objeto. Use a ID de objeto no Azure Sentinel para atribuir o aplicativo lógico com uma função de RBAC do Azure em seu espaço de trabalho do Azure Sentinel. 

Para obter mais informações, consulte:

- [Autenticando com identidade gerenciada em aplicativos lógicos do Azure](/azure/logic-apps/create-managed-service-identity)
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

Para obter mais informações, consulte a [documentação do log Analytics](/azure/azure-monitor/platform/log-analytics-agent) e as notas de versão do agente de [log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembro de 2020

- [Monitorar os guias estratégicos de aplicativos lógicos no Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Conector do Microsoft 365 defender (visualização pública)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Monitorar os guias estratégicos de aplicativos lógicos no Azure Sentinel

O Azure Sentinel agora se integra aos [aplicativos de log do Azure](/azure/logic-apps/), um serviço de nuvem que ajuda a agendar, automatizar e orquestrar tarefas, processos de negócios e fluxos de trabalho.

Use um aplicativo lógico do Azure no Azure Sentinel como um guia estratégico, que pode ser invocado automaticamente quando um incidente é criado ou durante a triagem e o trabalho com incidentes. 

Para fornecer informações sobre a integridade, o desempenho e o uso de seus guias estratégicos, incluindo qualquer um que você adicionar com os aplicativos lógicos do Azure, adicionamos uma [pasta de trabalho do Azure](/azure/azure-monitor/platform/workbooks-overview) denominada **monitoramento de integridade de guias estratégicos**. 

Use a pasta de trabalho de **monitoramento de integridade dos guias estratégicos** para monitorar a integridade de seus guias estratégicos ou Procure anomalias na quantidade de execuções com êxito ou com falha. 

A pasta de trabalho de **monitoramento de integridade dos guias estratégicos** agora está disponível na Galeria de modelos do Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exemplo de pasta de trabalho de monitoramento de integridade de guias":::

Para obter mais informações, consulte:

- [Documentação dos aplicativos lógicos](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Documentação do Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

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
