---
title: Gerenciar workspaces do Log Analytics no Azure Monitor | Microsoft Docs
description: Você pode gerenciar o acesso aos dados armazenados em um espaço de trabalho do Log Analytics no Azure Monitor usando permissões de recurso, espaço de trabalho ou nível de tabela. Este artigo fornece detalhes sobre como concluir.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 79bcca688146b2c2382876b6a61bc0eee34382a1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040950"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gerenciar acesso a dados de log e workspaces no Azure Monitor

Azure Monitor armazena dados de [log](../logs/data-platform-logs.md) em um espaço de trabalho log Analytics. Um workspace é um contêiner que inclui dados e informações de configuração. Para gerenciar o acesso aos dados de log, você executa várias tarefas administrativas relacionadas ao seu espaço de trabalho.

Este artigo explica como gerenciar o acesso aos logs e administrar os espaços de trabalho que os contêm, incluindo como conceder acesso a: 

* O espaço de trabalho usando permissões de espaço de trabalho.
* Usuários que precisam de acesso a dados de log de recursos específicos usando o controle de acesso baseado em função do Azure (RBAC do Azure), também conhecido como [contexto de recurso](../logs/design-logs-deployment.md#access-mode)
* Usuários que precisam de acesso a dados de log em uma tabela específica no espaço de trabalho usando o RBAC do Azure.

Para entender os conceitos de logs em relação às estratégias de acesso e RBAC do Azure, leia [criando sua implantação de logs de Azure monitor](../logs/design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>Configurar o modo de controle de acesso

Você pode exibir o [modo de controle de acesso](../logs/design-logs-deployment.md) configurado em um espaço de trabalho do portal do Azure ou com Azure PowerShell.  Você pode alterar essa configuração usando um dos seguintes métodos com suporte:

* Portal do Azure

* Azure PowerShell

* Modelo do Azure Resource Manager

### <a name="from-the-azure-portal"></a>No portal do Azure

Você pode exibir o modo de controle de acesso do espaço de trabalho atual na página **visão geral** do espaço de trabalho no menu **log Analytics espaço de trabalho** .

![Exibir modo de controle de acesso do espaço de trabalho](media/manage-access/view-access-control-mode.png)

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal do Azure, selecione Log Analytics espaços de trabalho > seu espaço de trabalho.

Você pode alterar essa configuração na página **Propriedades** do espaço de trabalho. A alteração da configuração será desabilitada se você não tiver permissões para configurar o espaço de trabalho.

![Alterar modo de acesso do espaço de trabalho](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Usando o PowerShell

Use o seguinte comando para examinar o modo de controle de acesso para todos os espaços de trabalho na assinatura:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

O resultado deve ser assim:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Um valor de `False` significa que o espaço de trabalho está configurado com o modo de acesso de contexto de espaço de trabalho.  Um valor de `True` significa que o espaço de trabalho está configurado com o modo de acesso de contexto de recurso.

> [!NOTE]
> Se um espaço de trabalho for retornado sem um valor booliano e estiver em branco, isso também corresponderá aos resultados de um `False` valor.
>

Use o script a seguir para definir o modo de controle de acesso para um espaço de trabalho específico para a permissão de contexto de recurso:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Use o script a seguir para definir o modo de controle de acesso para todos os espaços de trabalho na assinatura para a permissão de contexto de recurso:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Usar um modelo do Resource Manager

Para configurar o modo de acesso em um modelo de Azure Resource Manager, defina o sinalizador de recurso **enableLogAccessUsingOnlyResourcePermissions** no espaço de trabalho para um dos valores a seguir.

* **false**: defina o espaço de trabalho para permissões de contexto de espaço de trabalho. Essa será a configuração padrão se o sinalizador não estiver definido.
* **true**: defina o espaço de trabalho para permissões de contexto de recurso.

## <a name="manage-access-using-workspace-permissions"></a>Gerenciar o acesso usando permissões de espaço de trabalho

Cada workspace pode ter várias contas associadas e cada conta pode ter acesso a vários workspaces. O acesso é gerenciado usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md).

As atividades a seguir também exigem permissões do Azure:

|Ação |Permissões do Azure necessárias |Observações |
|-------|-------------------------|------|
| Adicionando e removendo soluções de monitoramento | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um workspace no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Exibir propriedades básicas do espaço de trabalho e inserir a folha do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Consultar logs usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Acessar todos os tipos de log usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Acessar uma tabela de log específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Ler as chaves do espaço de trabalho para permitir o envio de logs para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gerenciar o acesso usando as permissões do Azure

Para conceder acesso ao espaço de trabalho do Log Analytics usando as permissões do Azure, siga as etapas em [atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md). Por exemplo, funções personalizadas, consulte [funções personalizadas de exemplo](#custom-role-examples)

O Azure tem duas funções de usuário predefinidas para workspaces do Log Analytics:

* Leitor do Log Analytics
* Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:

* Exibir e pesquisar todos os dados de monitoramento
* Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos do Azure e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler as configurações do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Preterido, sem necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Preterido, sem necessidade de atribuí-los aos usuários. |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Nenhuma Ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do workspace necessária para usar a API da coleta de dados e instalar os agentes. Isso impede que o usuário adicione os novos recursos para o workspace |

Os membros da função *Colaborador do Log Analytics* podem:

* Inclui todos os privilégios da *função leitor de log Analytics*, permitindo que o usuário Leia todos os dados de monitoramento
* Criar e configurar contas de automação
* Adicionar e remover soluções de gerenciamento

    > [!NOTE]
    > Para executar com êxito essas duas ações, essa permissão deve ser concedida no nível do grupo de recursos ou da assinatura.

* Ler chaves de conta de armazenamento
* Configurar a coleta de logs do armazenamento do Azure
* Editar configurações de monitoramento dos recursos do Azure, incluindo
  * Adicionar a extensão VM às VMs
  * Configurar o diagnóstico do Azure em todos os recursos do Azure

> [!NOTE]
> Você pode usar a capacidade de adicionar uma extensão da máquina virtual a uma máquina virtual para ter controle total sobre uma máquina virtual.

A função de Colaborador do Log Analytics inclui as seguintes ações do Azure:

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade de exibir todos os recursos e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler a configuração do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
| `Microsoft.Automation/automationAccounts/*` | Capacidade de criar e configurar Contas de automação do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover as extensões da máquina virtual, incluindo a extensão do Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Exiba a chave da conta de armazenamento. Necessário para configurar o Log Analytics para ler os logs das contas de armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover as regras de alerta |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as configurações de diagnóstico nos recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração de Log Analytics espaços de trabalho. Para editar as configurações avançadas do espaço de trabalho, as necessidades do usuário `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Use essas funções para conceder acesso aos usuários em escopos diferentes:

* Assinatura – acesso a todos os workspaces na assinatura
* Grupo de Recursos - acesso a todo workspace no grupo de recursos
* Recurso - acesso somente ao workspace especificado

É recomendável executar atribuições no nível de recurso (espaço de trabalho) para garantir o controle de acesso preciso. Use as [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recurso

Quando os usuários consultam logs de um espaço de trabalho usando acesso de contexto de recurso, eles terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de exibir todos os dados de log para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidade de definir a configuração de diagnóstico para permitir a configuração de logs para este recurso. |

`/read`Normalmente, a permissão é concedida de uma função que inclui _\* /Read ou_ _\*_ permissões, como as funções de [leitor](../../role-based-access-control/built-in-roles.md#reader) e [colaborador](../../role-based-access-control/built-in-roles.md#contributor) internas. Funções personalizadas que incluem ações específicas ou funções internas dedicadas podem não incluir essa permissão.

Consulte [definindo o controle de acesso por tabela](#table-level-azure-rbac) abaixo se você quiser criar um controle de acesso diferente para tabelas diferentes.

## <a name="custom-role-examples"></a>Exemplos de função personalizada

1. Para conceder a um usuário acesso aos dados de log de seus recursos, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda usuários `*/read` ou `Microsoft.Insights/logs/*/read` permissões a seus recursos. Se eles já tiverem sido atribuídos à função [leitor de log Analytics](../../role-based-access-control/built-in-roles.md#reader) no espaço de trabalho, será suficiente.

2. Para conceder a um usuário acesso aos dados de log de seus recursos e configurar seus recursos para enviar logs para o espaço de trabalho, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões no espaço de trabalho: `Microsoft.OperationalInsights/workspaces/read` e `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Com essas permissões, os usuários não podem executar nenhuma consulta no nível do espaço de trabalho. Eles só podem enumerar o espaço de trabalho e usá-lo como um destino para configurações de diagnóstico ou configuração do agente.

    * Conceda aos usuários as seguintes permissões para seus recursos: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write` . Se eles já tiverem sido atribuídos à função de [colaborador de log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , atribuída a função de leitor ou permissões concedidas nesse `*/read` recurso, isso será suficiente.

3. Para conceder a um usuário acesso aos dados de log de seus recursos sem a capacidade de ler eventos de segurança e enviar dados, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões para seus recursos: `Microsoft.Insights/logs/*/read` .

    * Adicione a seguinte não ação para impedir que os usuários leiam o tipo de SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read` . A não ação deve estar na mesma função personalizada que a ação que fornece a permissão de leitura ( `Microsoft.Insights/logs/*/read` ). Se o usuário inerente à ação de leitura de outra função atribuída a esse recurso ou à assinatura ou ao grupo de recursos, ele poderá ler todos os tipos de log. Isso também será verdadeiro se eles herdarem `*/read` , que existem por exemplo, com a função de leitor ou colaborador.

4. Para conceder a um usuário acesso a dados de log de seus recursos e ler todos os dados de logon do Azure AD e ler Gerenciamento de Atualizações de log da solução no espaço de trabalho, execute o seguinte:

    * Configurar o modo de controle de acesso do espaço de trabalho para **usar permissões de espaço de trabalho ou recurso**

    * Conceda aos usuários as seguintes permissões no espaço de trabalho: 

        * `Microsoft.OperationalInsights/workspaces/read` – necessário para que o usuário possa enumerar o espaço de trabalho e abrir a folha do espaço de trabalho no portal do Azure
        * `Microsoft.OperationalInsights/workspaces/query/read` – necessário para cada usuário que pode executar consultas
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – para poder ler os logs de entrada do Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – para poder ler Gerenciamento de Atualizações logs da solução
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – para poder ler Gerenciamento de Atualizações logs da solução
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – para poder ler os logs de gerenciamento de atualizações
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – necessário para poder usar a solução Gerenciamento de Atualizações
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – necessário para poder usar a solução Gerenciamento de Atualizações

    * Conceda aos usuários as seguintes permissões para seus recursos: `*/read` , atribuído à função leitor ou `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>RBAC do Azure no nível de tabela

O **Azure RBAC de nível de tabela** permite que você defina um controle mais granular para dados em um espaço de trabalho log Analytics além das outras permissões. Esse controle permite que você defina tipos de dados específicos que são acessíveis somente para um conjunto específico de usuários.

Você implementa o controle de acesso de tabela com as [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) para conceder acesso a [tabelas](../logs/data-platform-logs.md) específicas no espaço de trabalho. Essas funções são aplicadas a espaços de trabalho com o contexto do espaço de trabalho ou os [modos de controle de acesso](../logs/design-logs-deployment.md#access-control-mode) de contexto de recurso, independentemente do modo de [acesso](../logs/design-logs-deployment.md#access-mode)do usuário.

Crie uma [função personalizada](../../role-based-access-control/custom-roles.md) com as ações a seguir para definir o acesso ao controle de acesso à tabela.

* Para conceder acesso a uma tabela, inclua-a na seção **ações** da definição de função. Para subtrair o acesso das **ações** permitidas, inclua-o na seção não- **ações** .
* Use Microsoft. OperationalInsights/Workspaces/Query/* para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso às tabelas _Heartbeat_ e _AzureActivity_ , crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso apenas à tabela _SecurityBaseline_ , crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
Os exemplos acima definem uma lista de tabelas que são permitidas. Este exemplo mostra a definição da lista bloqueada quando um usuário pode acessar todas as tabelas, exceto a tabela _SecurityAlert_ :

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Logs personalizados

 Os logs personalizados são criados a partir de fontes de dados como logs personalizados e API do coletor de dados HTTP. A maneira mais fácil de identificar o tipo de log é verificando as tabelas listadas em [logs personalizados no esquema de log](./log-analytics-tutorial.md#table-schema).

 Você não pode conceder acesso a logs personalizados individuais, mas pode conceder acesso a todos os logs personalizados. Para criar uma função com acesso a todos os logs personalizados, crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Uma abordagem alternativa para gerenciar o acesso a logs personalizados é atribuí-los a um recurso do Azure e gerenciar o acesso usando o paradigma de contexto de recurso. Para usar esse método, você deve incluir a ID do recurso especificando-a no cabeçalho [x-MS-AzureResourceId](../logs/data-collector-api.md#request-headers) quando os dados são ingeridos para log Analytics por meio da [API do coletor de dados http](../logs/data-collector-api.md). A ID do recurso deve ser válida e ter regras de acesso aplicadas a ela. Depois que os logs são ingeridos, eles podem ser acessados por eles com acesso de leitura ao recurso, conforme explicado aqui.

Às vezes, os logs personalizados são provenientes de fontes que não estão diretamente associadas a um recurso específico. Nesse caso, crie um grupo de recursos apenas para gerenciar o acesso a esses logs. O grupo de recursos não provoca nenhum custo, mas fornece uma ID de recurso válida para controlar o acesso aos logs personalizados. Por exemplo, se um firewall específico estiver enviando logs personalizados, crie um grupo de recursos chamado "MyFireWallLogs" e verifique se as solicitações de API contêm a ID de recurso "MyFireWallLogs". Os registros de log do firewall são então acessíveis somente para usuários que receberam acesso a MyFireWallLogs ou àqueles com acesso total ao espaço de trabalho.          

### <a name="considerations"></a>Considerações

* Se um usuário receber permissão de leitura global com as funções leitor padrão ou colaborador que incluem a ação _\* /Read_ , ele substituirá o controle de acesso por tabela e dará acesso a todos os dados de log.
* Se um usuário receber acesso por tabela, mas nenhuma outra permissão, ele poderá acessar dados de log da API, mas não do portal do Azure. Para fornecer acesso do portal do Azure, use o leitor de Log Analytics como sua função base.
* Os administradores e os proprietários da assinatura terão acesso a todos os tipos de dados, independentemente de quaisquer outras configurações de permissão.
* Os proprietários do espaço de trabalho são tratados como qualquer outro usuário para o controle de acesso por tabela.
* É recomendável atribuir funções a grupos de segurança em vez de usuários individuais para reduzir o número de atribuições. Isso também ajudará você a usar as ferramentas de gerenciamento de grupo existentes para configurar e verificar o acesso.

## <a name="next-steps"></a>Próximas etapas

* Consulte a [visão geral do agente do Log Analytics](../agents/log-analytics-agent.md) para reunir dados de computadores no datacenter ou outro ambiente de nuvem.

* Consulte [coletar dados sobre máquinas virtuais do Azure](../vm/quick-collect-azurevm.md) para configurar a coleta de dados de VMs do Azure.