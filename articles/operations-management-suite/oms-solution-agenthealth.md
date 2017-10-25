---
title: "Solução Integridade do Agente do OMS | Microsoft Docs"
description: "Este artigo destina-se a ajudá-lo a entender como usar a solução para monitorar a integridade de seus agentes que se reportam diretamente ao OMS ou ao System Center Operations Manager."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="agent-health-solution-in-oms"></a>Solução Integridade do Agente do OMS
A solução Integridade do Agente do OMS ajuda você a entender, para todos os agentes que se reportam diretamente ao espaço de trabalho do OMS ou a um grupo de gerenciamento do System Center Operations Manager conectado ao OMS, quais estão respondendo e enviando dados operacionais.  Você pode também manter controle de quantos agentes estão implantados, onde eles estão distribuídos geograficamente e executam outras consultas para saber a distribuição dos agentes implantados no Azure, em outros ambientes de nuvem ou no local.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implantar essa solução, confirme se você atualmente tem [agentes do Windows](../log-analytics/log-analytics-windows-agents.md) com suporte se reportando ao espaço de trabalho do OMS ou a um [grupo de gerenciamento do Operations Manager](../log-analytics/log-analytics-om-agents.md) integrado ao seu espaço de trabalho do OMS.    

## <a name="solution-components"></a>Componentes da solução
Essa solução consiste nos seguintes recursos que são adicionados ao seu espaço de trabalho e aos agentes conectados diretamente ou ao grupo de gerenciamento conectado do Operations Manager.

### <a name="management-packs"></a>Pacotes de gerenciamento
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do OMS, os pacotes de gerenciamento a seguir serão instalados no Operations Manager.  Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente após a adição dessa solução. Não há nada para configurar ou gerenciar com esses pacotes de gerenciamento.

* Pacote de inteligência do Microsoft System Center Advisor HealthAssessment Direct (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Pacote de inteligência do Microsoft System Center Advisor HealthAssessment Server Channel (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Para saber mais sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução Integridade do Agente ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções](../log-analytics/log-analytics-add-solutions.md). Não é necessária nenhuma configuração.


## <a name="data-collection"></a>Coleta de dados
### <a name="supported-agents"></a>Agentes com suporte
A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de pulsação são coletados dos agentes diretos do Windows.|
| Grupo de gerenciamento do System Center Operations Manager | Sim | Os eventos de pulsação são coletados dos agentes que se reportam ao grupo de gerenciamento a cada 60 segundos e, em seguida, encaminhados para o Log Analytics. Uma conexão direta de agentes do Operations Manager ao Log Analytics não é necessária. Os dados de evento de pulsação são encaminhados do grupo de gerenciamento para o repositório do Log Analytics.|

## <a name="using-the-solution"></a>Usando a solução
Quando você adicionar a solução ao espaço de trabalho do OMS, o bloco **Integridade do Agente** será adicionado ao seu painel do OMS. Esse bloco mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> ![Bloco da solução Integridade do Agente no painel](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no bloco **Integridade do Agente** para abrir o painel **Integridade do Agente**.  O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais eventos por contagem que correspondem aos critérios da coluna para o intervalo de tempo especificado. É possível executar uma pesquisa de logs que fornece a lista inteira selecionando **Ver todos** no canto inferior direito de cada coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência de sua contagem de agentes durante um período de sete dias para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista de agentes que ainda não enviou uma pulsação nas últimas 24 horas.|
| Distribuição por tipo de sistema operacional | Uma partição de quantos agentes de Windows e Linux você tem em seu ambiente.|
| Distribuição por versão do agente | Uma partição de diferentes versões do agente instaladas em seu ambiente e uma contagem de cada uma delas.|
| Distribuição por categoria de agente | Uma partição das diferentes categorias de agentes que estão enviando eventos de pulsação: agentes diretos, agentes do OpsMgr ou o servidor de gerenciamento do OpsMgr.|
| Distribuição por grupo de gerenciamento | Uma partição dos diferentes grupos de gerenciamento do SCOM em seu ambiente.|
| Localização geográfica de agentes | Uma partição de diferentes países em que você tem agentes e uma contagem total do número de agentes que foram instalados em cada país.|
| Contagem de gateways instalados | O número de servidores que têm o Gateway de OMS instalado e uma lista desses servidores.|

![Exemplo de painel da solução Integridade do Agente](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Registros do Log Analytics
A solução cria um tipo de registro no repositório do OMS.  

### <a name="heartbeat-records"></a>Registros de pulsação
Um registro com o tipo **pulsação** é criado.  Esses registros têm as propriedades descritas na tabela a seguir.  

| Propriedade | Descrição |
| --- | --- |
| Tipo | *Pulsação*|
| Categoria | O valor é *Agente Direto*, *Agente SCOM* ou *o Servidor de Gerenciamento do SCOM*.|
| Computador | Nome do computador.|
| OSType | Sistema operacional Windows ou Linux.|
| OSMajorVersion | Versão principal do sistema operacional.|
| OSMinorVersion | Versão secundária do sistema operacional.|
| Versão | Versão do agente do OMS ou do agente do Operations Manager.|
| SCAgentChannel | O valor é *Direct* e/ou *SCManagementServer*.|
| IsGatewayInstalled | Se o Gateway do OMS está instalado, o valor é *true*; caso contrário, o valor é *false*.|
| ComputerIP | Endereço IP do computador.|
| RemoteIPCountry | Localização geográfica onde o computador está implantado.|
| ManagementGroupName | Nome do grupo de gerenciamento do Operations Manager.|
| SourceComputerId | ID exclusiva do computador.|
| RemoteIPLongitude | Longitude do local geográfico do computador.|
| RemoteIPLatitude | Latitude da localização geográfica do computador.|

Cada agente subordinado a um servidor de gerenciamento do Operations Manager enviará duas pulsações e o valor da propriedade SCAgentChannel inclui tanto **Direct** quanto **SCManagementServer**, dependendo de quais fontes de dados do Log Analytics e soluções você habilitou na sua assinatura do OMS. Lembre-se de que os dados das soluções são enviados diretamente de um servidor de gerenciamento do Operations Manager para o serviço Web do OMS ou por causa do volume de dados coletados no agente, são enviados diretamente do agente para o serviço Web do OMS. Para eventos de pulsação que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gerenciamento, desde que os dados realmente sejam carregados por ele.  Para pulsações em que SCAgentChannel está definido como **Direct**, é o endereço IP público do agente.  

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros coletados por essa solução.

| Consultar | Descrição |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |Número total de agentes |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |Contagem de agentes sem resposta nas últimas 24 horas |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |Contagem de agentes sem resposta nos últimos 15 minutos |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Computadores online (nas últimas 24 horas) |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Total agentes offline nos últimos 30 minutos (para as últimas 24 horas) |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |Obter uma tendência do número de agentes ao longo do tempo por OSType|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |Distribuição por tipo de sistema operacional |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |Distribuição por versão do agente |
| Type=Heartbeat&#124;measure count() by Category |Distribuição por categoria de agente |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | Distribuição por grupo de gerenciamento |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |Localização geográfica de agentes |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |Número de Gateways do OMS instalados |


>[!NOTE]
> Se o seu espaço de trabalho fosse atualizado para a [nova linguagem de consulta do Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), as consultas acima seriam alteradas para o demonstrado a seguir.
>
>| Consultar | Descrição |
|:---|:---|
| Heartbeat &#124; distinct Computer |Número total de agentes |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Contagem de agentes sem resposta nas últimas 24 horas |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Contagem de agentes sem resposta nos últimos 15 minutos |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computadores online (nas últimas 24 horas) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total agentes offline nos últimos 30 minutos (para as últimas 24 horas) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Obter uma tendência do número de agentes ao longo do tempo por OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuição por tipo de sistema operacional |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuição por versão do agente |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuição por categoria de agente |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuição por grupo de gerenciamento |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Localização geográfica de agentes |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de Gateways do OMS instalados |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md) para obter detalhes sobre como gerar alertas por meio do Log Analytics.
