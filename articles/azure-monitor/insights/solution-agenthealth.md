---
title: Solução de integridade do agente no Azure Monitor | Microsoft Docs
description: Este artigo destina-se a ajudá-lo a entender como usar essa solução para monitorar a integridade de seus agentes que se reportam diretamente ao Log Analytics ou ao System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: f431613d9fa1020f523e03c90cbe31f4d42ccf42
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596041"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Solução de integridade do agente no Azure Monitor
A solução de integridade do agente no Azure ajuda você a entender, para todos os agentes que se reportam diretamente ao espaço de trabalho do Log Analytics no Azure Monitor ou um grupo de gerenciamento do System Center Operations Manager conectados ao Azure Monitor, que não estão respondendo e enviando dados operacionais.  Você pode também manter controle de quantos agentes estão implantados, onde eles estão distribuídos geograficamente e executam outras consultas para saber a distribuição dos agentes implantados no Azure, em outros ambientes de nuvem ou no local.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implantar essa solução, confirme se você tem suporte no momento [agentes do Windows](../../log-analytics/log-analytics-windows-agent.md) relatórios no espaço de trabalho do Log Analytics ou em relatórios para um [grupo de gerenciamento do Operations Manager](../../azure-monitor/platform/om-agents.md) integrado com seu espaço de trabalho.

## <a name="solution-components"></a>Componentes da solução
Essa solução consiste nos seguintes recursos que são adicionados ao seu workspace e aos agentes conectados diretamente ou ao grupo de gerenciamento conectado do Operations Manager.

### <a name="management-packs"></a>Pacotes de gerenciamento
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager.  Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente após a adição dessa solução. Não há nada para configurar ou gerenciar com esses pacotes de gerenciamento.

* Pacote de inteligência do Microsoft System Center Advisor HealthAssessment Direct (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Pacote de inteligência do Microsoft System Center Advisor HealthAssessment Server Channel (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução de integridade do agente ao seu espaço de trabalho do Log Analytics usando o processo descrito em [adicionar soluções](solutions.md). Não é necessária nenhuma configuração.


## <a name="data-collection"></a>Coleta de dados
### <a name="supported-agents"></a>Agentes com suporte
A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de pulsação são coletados dos agentes diretos do Windows.|
| Grupo de gerenciamento do System Center Operations Manager | Sim | Eventos de pulsação são coletados dos agentes que se reportam ao grupo de gerenciamento a cada 60 segundos e, em seguida, encaminhados para o Azure Monitor. Uma conexão direta de agentes do Operations Manager para o Azure Monitor não é necessária. Dados de evento de pulsação são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics.|

## <a name="using-the-solution"></a>Usando a solução
Quando você adiciona a solução ao espaço de trabalho do Log Analytics, o **integridade do agente** bloco será adicionado ao seu painel. Esse bloco mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> ![Bloco da solução Integridade do Agente no painel](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no bloco **Integridade do Agente** para abrir o painel **Integridade do Agente**.  O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais eventos por contagem que correspondem aos critérios da coluna para o intervalo de tempo especificado. É possível executar uma pesquisa de logs que fornece a lista inteira selecionando **Ver todos** no canto inferior direito de cada coluna ou clicando no cabeçalho da coluna.

| Coluna | DESCRIÇÃO |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência de sua contagem de agentes durante um período de sete dias para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista de agentes que ainda não enviou uma pulsação nas últimas 24 horas.|
| Distribuição por tipo de sistema operacional | Uma partição de quantos agentes de Windows e Linux você tem em seu ambiente.|
| Distribuição por versão do agente | Uma partição de diferentes versões do agente instaladas em seu ambiente e uma contagem de cada uma delas.|
| Distribuição por categoria de agente | Uma partição das diferentes categorias de agentes que estão enviando eventos de pulsação: agentes diretos, agentes do OpsMgr ou o servidor de gerenciamento do OpsMgr.|
| Distribuição por grupo de gerenciamento | Uma partição de diferentes grupos de gerenciamento do Operations Manager em seu ambiente.|
| Localização geográfica de agentes | Uma partição de diferentes países em que você tem agentes e uma contagem total do número de agentes que foram instalados em cada país.|
| Contagem de gateways instalados | O número de servidores que possuem o Log Analytics Gateway instalado e uma lista desses servidores.|

![Exemplo de painel da solução Integridade do Agente](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor
A solução cria um tipo de registro no espaço de trabalho do Log Analytics.  

### <a name="heartbeat-records"></a>Registros de pulsação
Um registro com o tipo **pulsação** é criado.  Esses registros têm as propriedades descritas na tabela a seguir.  

| Propriedade | DESCRIÇÃO |
| --- | --- |
| `Type` | *Pulsação*|
| `Category` | O valor é *Agente Direto*, *Agente SCOM* ou *o Servidor de Gerenciamento do SCOM*.|
| `Computer` | Nome do computador.|
| `OSType` | Sistema operacional Windows ou Linux.|
| `OSMajorVersion` | Versão principal do sistema operacional.|
| `OSMinorVersion` | Versão secundária do sistema operacional.|
| `Version` | Versão do agente do log Analytics ou o agente do Operations Manager.|
| `SCAgentChannel` | O valor é *Direct* e/ou *SCManagementServer*.|
| `IsGatewayInstalled` | Se o Log de análise de Gateway estiver instalado, o valor é *verdadeira*, caso contrário, o valor é *falso*.|
| `ComputerIP` | Endereço IP do computador.|
| `RemoteIPCountry` | Localização geográfica onde o computador está implantado.|
| `ManagementGroupName` | Nome do grupo de gerenciamento do Operations Manager.|
| `SourceComputerId` | ID exclusiva do computador.|
| `RemoteIPLongitude` | Longitude do local geográfico do computador.|
| `RemoteIPLatitude` | Latitude da localização geográfica do computador.|

Cada agente subordinado a um servidor de gerenciamento do Operations Manager enviará duas pulsações e o valor da propriedade SCAgentChannel inclui tanto **direto** e **SCManagementServer** dependendo do que fontes de dados e soluções de monitoramento que você habilitou na sua assinatura. Se você se lembra, dados de soluções são ou enviados diretamente de um servidor de gerenciamento do Operations Manager para o Azure Monitor, ou por causa do volume de dados coletados no agente, são enviados diretamente do agente para o Azure Monitor. Para eventos de pulsação que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gerenciamento, desde que os dados realmente sejam carregados por ele.  Para pulsações em que SCAgentChannel está definido como **Direct**, é o endereço IP público do agente.  

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros coletados por essa solução.

| Consultar | DESCRIÇÃO |
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
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de Gateways de análise de Log instalado |




## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [alertas no Azure Monitor](../platform/alerts-overview.md) para obter detalhes sobre como gerar alertas de consultas de log. 
