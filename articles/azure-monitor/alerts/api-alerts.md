---
title: Usando a API REST de alerta do Log Analytics
description: A API REST do alerta de Log Analytics permite criar e gerenciar alertas no Log Analytics, que faz parte do Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 33f5ce86a21a9aad110db162849ff3e3c7bbb2b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037482"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Criar e gerenciar regras de alerta no Log Analytics com a API REST 

> [!IMPORTANT]
> Conforme [anunciado](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), os espaços de trabalho do log Analytics criados após *1º de junho de 2019* gerenciam regras de alerta usando a [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules/)atual. Os clientes são incentivados a [mudar para a API atual](./alerts-log-api-switch.md) em espaços de trabalho mais antigos para aproveitar Azure monitor [benefícios](./alerts-log-api-switch.md#benefits)do scheduledQueryRules. Este artigo descreve o gerenciamento de regras de alerta usando a API herdada.

A API REST de alerta do Log Analytics permite criar e gerenciar alertas no Log Analytics.  Este artigo fornece detalhes da API e vários exemplos para executar operações diferentes.

A API REST de Pesquisa do Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. Neste documento, você encontrará exemplos em que a API é acessada por meio de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API de Pesquisa do Log Analytics. Com essas ferramentas, você pode utilizar a API do RESTful Azure Resource Manager para fazer chamadas para espaços de trabalho do Log Analytics e executar comandos de pesquisa dentro deles. A API produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de diferentes maneiras por meio de programação.

## <a name="prerequisites"></a>Pré-requisitos
Atualmente, os alertas somente podem ser criados com uma pesquisa salva no Log Analytics.  Você pode consultar a [API REST da Pesquisa de Log](../logs/log-query-overview.md) para obter mais informações.

## <a name="schedules"></a>Agendas
Uma pesquisa salva pode ter um ou mais agendamentos. O agendamento define a frequência com que a pesquisa é executada e o intervalo de tempo em que os critérios são identificados.
Os agendamentos têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Intervalo |A frequência com que a pesquisa é executada. Medida em minutos. |
| QueryTimeSpan |O intervalo durante o qual os critérios são avaliados. Deve ser igual ou maior que Interval. Medida em minutos. |
| Versão |A versão da API que está sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

Por exemplo, considere uma consulta de evento com um Interval (Intervalo) de 15 minutos e Timespan (Período) de 30 minutos. Nesse caso, a consulta deverá ser executada a cada 15 minutos e um alerta deverá ser disparado se os critérios continuarem a ser resolvidos como verdadeiro por um período de 30 minutos.

### <a name="retrieving-schedules"></a>Recuperando agendamentos
Use o método Get para recuperar todos os agendamentos de uma pesquisa salva.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Use o método Get com uma ID de agendamento para recuperar um agendamento específico para uma pesquisa salva.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

A seguir está um exemplo de resposta para um agendamento.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Criando uma agenda
Use o método Put com uma ID de agendamento única para criar um novo agendamento.  Dois agendamentos não podem ter a mesma ID, mesmo se estiverem associados a pesquisas salvas diferentes.  Quando você cria um agendamento no console do Log Analytics, um GUID é criado para o ID do agendamento.

> [!NOTE]
> O nome para todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em letras minúsculas.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Editando um agendamento
Use o método Put com uma ID de agendamento existente para a mesma pesquisa salva para modificar esse agendamento; no exemplo a seguir, o agendamento está desabilitado. O corpo da solicitação deve incluir a *ETag* da agenda.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Excluindo agendamentos
Use o método Delete com uma ID de agendamento para excluir um agendamento.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Ações
Um agendamento pode ter várias ações. Uma ação pode definir um ou mais processos a serem executados, como enviar um email ou iniciar um runbook, ou então ela pode definir um limite que determina quando os resultados de uma pesquisa correspondem a certos critérios.  Algumas ações definirão ambos para que os processos sejam executados quando o limite for atingido.

Todas as ações têm as propriedades indicadas na tabela a seguir.  Diferentes tipos de alertas têm diferentes propriedades adicionais que são descritas abaixo.

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |Tipo da ação.  Atualmente, os valores possíveis são Alerta e Webhook. |
| `Name` |Nome de exibição para o alerta. |
| `Version` |A versão da API que está sendo usada.  Atualmente, isso sempre deve ser definido como 1. |

### <a name="retrieving-actions"></a>Recuperando ações

Use o método Get para recuperar todas as ações de um agendamento.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Use o método Get com a ID de ação para recuperar uma ação específica de um agendamento.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Criar ou editar ações
Use o método Put com uma ID de ação que seja exclusiva para o agendamento para criar uma nova ação.  Quando você cria uma ação no console do Log Analytics, um GUID é para o ID da ação.

> [!NOTE]
> O nome para todas as pesquisas, agendas e ações salvas criadas com a API do Log Analytics deve estar em letras minúsculas.

Use o método Put com uma ID de ação existente para a mesma pesquisa salva para modificar esse agendamento.  O corpo da solicitação deve incluir a Etag do agendamento.

O formato da solicitação para criar uma nova ação varia conforme o tipo de ação, veja os exemplos fornecidos nas seções a seguir.

### <a name="deleting-actions"></a>Excluindo ações

Use o método Delete com a ID de ação para excluir uma ação.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Ações de Alerta
Um Agendamento deve ter somente uma ação de Alerta.  Ações de alerta têm uma ou mais das seções na tabela a seguir.  Elas são descritas em mais detalhes abaixo.

| Seção | Descrição | Uso |
|:--- |:--- |:--- |
| Limite |Critérios para quando a ação for executada.| Necessário para cada alerta, antes ou depois de ser estendido para o Azure. |
| Severidade |Rótulo usado para classificar o alerta quando disparado.| Necessário para cada alerta, antes ou depois de ser estendido para o Azure. |
| Suprimir |Opção para interromper notificações do alerta. | Opcional para cada alerta, antes ou depois de ser estendido para o Azure. |
| Grupos de Ação |IDs do ActionGroup do Azure em que as ações necessárias são especificadas, como - Emails SMSs, Chamadas de voz, Webhooks, Runbooks de automação, Conectores ITSM, etc.| Necessário quando os alertas são estendidos para o Azure|
| Personalizar Ações|Modificar a saída padrão para determinadas ações do ActionGroup| Opcional para cada alerta, pode ser usado depois de alertas serem estendidos para o Azure. |

### <a name="thresholds"></a>Limites
Uma ação de Alerta deve ter somente um limite.  Quando os resultados de pesquisas salvas coincidem com o limite de uma ação associada à pesquisa, outros processos nesta ação são executados.  Uma ação também pode conter apenas um limite para ser usado com ações de outros tipos que não contêm os limites.

Os limites têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| `Operator` |Operador de comparação de limite. <br> gt = Maior Que <br>  lt = Menor Que |
| `Value` |Valor para o limite. |

Por exemplo, considere uma consulta de evento com um Interval (Intervalo) de 15 minutos, Timespan (Período) de 30 minutos e Threshold (Limite) maior que 10. Nesse caso, a consulta deverá ser executada a cada 15 minutos e um alerta deverá ser disparado se ela retornar 10 eventos criados dentro de um período de 30 minutos.

Veja a seguir um exemplo de resposta para uma ação com apenas um limite.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

Use o método Put com uma ID de ação única para criar uma nova ação de limite para um agendamento.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Use o método Put com uma ID de ação existente para criar uma nova ação de limite para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Severidade
O Log Analytics permite classificar alertas em categorias, para facilitar a triagem e o gerenciamento. A gravidade do Alerta definida é: informativo, aviso e crítico. São mapeadas para a escala de gravidade normalizada dos Alertas do Azure como:

|Nível de gravidade do Log Analytics  |Nível de gravidade dos Alertas do Azure  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Veja a seguir um exemplo de resposta para uma ação com apenas um limite e gravidade. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Use o método Put com uma ID de ação única para criar uma nova ação para um agendamento com gravidade.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Use o método Put com uma ID de ação existente para modificar uma ação de gravidade para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Suprimir
Os alertas de consulta baseados no Log Analytics serão disparados sempre que o limite for atingido ou excedido. Com base na lógica implícita na consulta, isso pode resultar no disparo de um alerta durante uma série de intervalos e, portanto, no envio de notificações constantemente. Para evitar esse cenário, um usuário pode definir a opção Suprimir instruindo o Log Analytics a aguardar um período estipulado antes que a notificação seja disparada pela segunda vez para o regra de alerta. Portanto, se a supressão for definida para 30 minutos, o alerta será disparado pela primeira vez e enviará as notificações configuradas. Mas, depois, aguarde 30 minutos antes que a notificação da regra de alerta seja usada novamente. No período temporário, o regra de alerta continuará em execução – apenas a notificação é suprimida pelo Log Analytics pelo tempo especificado, independentemente de quantas vezes a regra de alerta foi disparada nesse período.

A propriedade Supressão do regra de alerta do Log Analytics é especificada usando o valor *Limitação* e o período de supressão usando o valor *DurationInMinutes*.

Veja a seguir uma resposta de exemplo para uma ação com apenas uma propriedade de limite, gravidade e supressão

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

Use o método Put com uma ID de ação única para criar uma nova ação para um agendamento com gravidade.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Use o método Put com uma ID de ação existente para modificar uma ação de gravidade para um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Grupos de Ação
Todos os alertas no Azure, use o Grupo de Ações como mecanismo padrão para lidar com ações. Com o Grupo de Ações, você pode especificar suas ações uma vez e, em seguida, associar o grupo de ações para vários alertas - em todo o Azure. Sem a necessidade de declarar repetidamente as mesmas ações. Os Grupos de Ações dão suportam a várias ações - incluindo Email, SMS, Chamada de voz, Conexão ITSM, Runbook de automação, URI de Webhook e muito mais. 

Para os usuários que tiverem estendido seus alertas ao Azure, agora deve haver uma agenda com detalhes do Grupo de Ações passado junto com o limite para poder criar um alerta. Detalhes de email, URLs de Webhook, detalhes de automação de Runbook e outras ações precisam ser definidos no Grupo de Ações antes de criar um alerta; é possível criar o [Grupo de Ações pelo Azure Monitor](./action-groups.md) no Portal ou usar a [API de Grupo de Ações](/rest/api/monitor/actiongroups).

Para adicionar a associação de grupo de ações a um alerta, especifique a ID exclusiva do Azure Resource Manager do grupo de ações na definição do alerta. Uma ilustração de exemplo é apresentada abaixo:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Personalizar Ações
Por padrão, as ações seguem o modelo e o formato padrão para notificações. Mas o usuário pode personalizar algumas ações, mesmo se elas são controladas por Grupos de Ações. Atualmente, a personalização é possível para o Assunto do Email e o Conteúdo de Webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Personalizar o Assunto do Email para o Grupo de Ações
Por padrão, o assunto do email para alertas é: Notificação de Alerta `<AlertName>` para `<WorkspaceName>`. Mas isso pode ser personalizado, para que você possa especificar palavras ou marcações - para permitir que você facilmente empregue regras de filtro na Caixa de entrada. Os detalhes de cabeçalho de email personalizado precisam ser enviados juntamente com os detalhes de ActionGroup, como no exemplo a seguir.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente com personalização a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Personalizar o Conteúdo de Webhook para o Grupo de Ações
Por padrão, o webhook enviado pelo Grupo de Ações para análise de logs tem uma estrutura fixa. Mas você pode personalizar o conteúdo de JSON, usando variáveis específicas compatíveis, para atender aos requisitos do ponto de extremidade do webhook. Para obter mais informações, consulte [Ação de webhook para alertas de log](./alerts-log-webhook.md). 

Os detalhes de webhook personalizados precisam ser enviados com os detalhes do ActionGroup e serão aplicados a todos os URI do Webhook especificado dentro do grupo de ações, como no exemplo a seguir.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

Use o método Put com uma ID de ação exclusiva para associar o Grupo de Ações já existente com personalização a um agendamento.  Abaixo é apresentada uma ilustração de exemplo do uso.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Use o método Put com uma ID de ação existente para modificar um Grupo de Ações associado a um agendamento.  O corpo da solicitação deve incluir a Etag da ação.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Próximas etapas

* Use a [API REST para executar pesquisas de log](../logs/log-query-overview.md) no Log Analytics.
* Saiba mais sobre os [alertas de log no Azure monitor](./alerts-unified-log.md)
* Como [criar, editar ou gerenciar regras de alerta de log no Azure monitor](./alerts-log.md)