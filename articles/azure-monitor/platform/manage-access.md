---
title: Gerenciar workspaces do Log Analytics no Azure Monitor | Microsoft Docs
description: Você pode gerenciar workspaces do Log Analytics no Azure Monitor usando uma variedade de tarefas administrativas em usuários, contas, workspaces e contas do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 27db27d79a05f24461e63242c0395cfd81315432
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782732"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gerenciar dados de log e espaços de trabalho no Azure Monitor
O Azure Monitor armazena dados de log em um espaço de trabalho do Log Analytics, que é essencialmente um contêiner que inclui informações de configuração e dados. Para gerenciar o acesso aos dados de log, você executa várias tarefas administrativas relacionadas aos workspaces. Você ou outros membros de sua organização podem usar vários workspaces para gerenciar diferentes conjuntos de dados que são coletados de todos ou de partes da sua infraestrutura de TI.

Este artigo explica como gerenciar o acesso aos logs e para administrar os espaços de trabalho que os contêm. 

## <a name="create-a-workspace"></a>Criar um workspace
Para criar um espaço de trabalho do Log Analytics, você precisa:

1. Tenho uma assinatura do Azure.
2. Escolher um nome para o workspace.
3. Associe o workspace com uma das suas assinaturas e grupos de recursos.
4. Escolher uma localização geográfica.

Consulte os seguintes artigos para obter detalhes sobre como criar um espaço de trabalho:

- [Criar um espaço de trabalho do Log Analytics no portal do Azure](../learn/quick-create-workspace.md)
- [Criar um espaço de trabalho do Log Analytics com a CLI 2.0 do Azure](../learn/quick-create-workspace-cli.md)
- [Criar um espaço de trabalho do Log Analytics com o Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de workspaces que você precisa
Um espaço de trabalho do Log Analytics é um recurso do Azure e é um contêiner no qual os dados são coletados, agregados, analisados e apresentados no Azure Monitor. Você pode ter vários workspaces por assinatura do Azure e ter acesso a mais de um workspace, com a capacidade de realizar uma consulta facilmente. Esta seção descreve quando pode ser útil criar mais de um workspace.

Um espaço de trabalho do Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados para definir direitos de acesso de usuário diferente no modo centrado no espaço de trabalho. Não é relevante ao trabalhar no modo centrado no recurso.
* Escopo para a configuração das configurações, como [tipo de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [retenção](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e [limitar dados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Encargos relacionados a retenção e ingestão de dados são feitos no recurso do espaço de trabalho.

Do ponto de vista do consumo, recomendamos que você crie o menor número de workspaces possível. Isso torna a administração e a consulta mais fácil e rápida. Porém, com base nas características anteriores, pode ser útil criar vários workspaces se:

* Você é uma empresa global e precisa de dados de log armazenados em regiões específicas por motivos de soberania de dados ou conformidade.
* Você usa o Azure e deseja evitar encargos de transferência de dados de saída com um workspace na mesma região que os recursos do Azure que ele gerencia.
* Você é um provedor de serviço gerenciado e precisa para manter os dados do Log Analytics para cada cliente que você gerencia isolados dos de outros clientes.
* Você gerencia vários clientes e deseja que cada cliente / departamento / grupo de negócios para ver seus próprios dados, mas não os dados de outras pessoas, e não há nenhuma necessidade de negócios para um cliente cruzada consolidado / departamento / modo de exibição de grupo de negócios. ".

Ao usar agentes do Windows para coletar dados, você pode [configurar cada agente para relatar para um ou mais workspaces](../../azure-monitor/platform/agent-windows.md).

Se você estiver usando o System Center Operations Manager, cada grupo de gerenciamento do Operations Manager poderá ser conectado a apenas um workspace. Você pode instalar o Microsoft Monitoring Agent em computadores gerenciados pelo Operations Manager e fazer o agente relatar ao Operations Manager e a um espaço de trabalho do Log Analytics diferente.

Depois que a arquitetura do espaço de trabalho é definida, você deve aplicar essa política em recursos do Azure com [política do Azure](../../governance/policy/overview.md). Isso pode fornecer uma definição interna que automaticamente se aplica a todos os recursos do Azure. Por exemplo, você pode definir uma política para garantir que todos os seus recursos do Azure em uma região específica enviada a todos os seus logs de diagnóstico para um determinado espaço de trabalho.

## <a name="view-workspace-details"></a>Exibir detalhes do espaço de trabalho
Enquanto você analisar dados no espaço de trabalho do Log Analytics a **do Azure Monitor** menu no portal do Azure, criar e gerenciar espaços de trabalho na **espaços de trabalho do Log Analytics** menu.
 

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Pesquise por workspaces do **Log Analytics**.  

    ![Portal do Azure](media/manage-access/azure-portal-01.png)  

3. Selecione o workspace da lista.

4. A página de espaço de trabalho exibe detalhes sobre o espaço de trabalho, introdução, configuração e links para obter informações adicionais.  

    ![Detalhes do workspace](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Escopo e permissões de espaço de trabalho
Os dados que um usuário tem acesso são determinados por vários fatores, que são listados na tabela a seguir. Cada um é descrita nas seções a seguir.

| Fator | DESCRIÇÃO |
|:---|:---|
| [Modo de acesso](#access-modes) | Método que o usuário usa para acessa o espaço de trabalho.  Define o escopo dos dados disponíveis e o modo de controle de acesso que é aplicado. |
| [Modo de controle de acesso](#access-control-mode) | Configuração no espaço de trabalho que define se as permissões são aplicadas no nível do espaço de trabalho ou recurso. |
| [Permissões](#manage-accounts-and-users) | Permissões aplicadas ao individuais ou grupos de usuários para o espaço de trabalho ou um recurso. Define os dados que o usuário terá acesso ao. |
| [RBAC de nível de tabela](#table-level-rbac) | Permissões granulares opcionais que se aplica a todos os usuários, independentemente do seu modo de acesso ou o modo de controle de acesso. Define quais tipos de dados que um usuário pode acessar. |



## <a name="access-modes"></a>Modos de acesso
O _modo de acesso_ refere-se como um usuário acessa um espaço de trabalho do Log Analytics e define o escopo dos dados que eles podem acessar. 

**Espaço de trabalho centrado**: Nesse modo, um usuário pode exibir todos os logs no espaço de trabalho que eles têm permissões. Escopo das consultas nesse modo para todos os dados em todas as tabelas no espaço de trabalho. Este é o modo de acesso usado quando os logs são acessados com o espaço de trabalho como o escopo, como quando você seleciona **Logs** da **do Azure Monitor** menu no portal do Azure.

**Recursos centrados**: Quando você acessa o espaço de trabalho para um recurso específico, como quando você seleciona **Logs** de um menu de recursos no portal do Azure, você pode exibir os logs para apenas esse recurso, em todas as tabelas que você tem acesso. Nesse modo o escopo das consultas apenas os dados associados a esse recurso. Esse modo também permite que o controle de acesso granular com base em função (RBAC). 

> [!NOTE]
> Logs estão disponíveis para consultas centrados no recurso apenas se eles foram corretamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações: 
> - Computadores fora do Azure
> - Service Fabric
> - Application Insights
> - Contêineres
>
> Você pode testar se os logs associados corretamente seus recursos, executando uma consulta e inspecionar os registros você está interessado. Se a ID do recurso correto está no [_ResourceId](log-standard-properties.md#_resourceid) propriedade e, em seguida, dados estão disponíveis para consultas centrados no recurso.

### <a name="comparing-access-modes"></a>Comparando os modos de acesso

A tabela a seguir resume os modos de acesso:

| | Centralizado no espaço de trabalho | Centrada em recursos |
|:---|:---|:---|
| Quem cada modelo se destina? | Administração Central. Administradores que precisam configurar coleta de dados e os usuários que precisam de acesso a uma ampla variedade de recursos. Atualmente, também necessário para os usuários que têm acesso aos logs para recursos fora do Azure. | Equipes de aplicativo. Administradores de recursos do Azure que está sendo monitorados. |
| O que um usuário requer para exibir os logs? | Permissões para o espaço de trabalho. Ver **permissões de espaço de trabalho** na [gerenciar contas e usuários](#manage-accounts-and-users). | Acesso de leitura para o recurso. Ver **permissões de recurso** na [gerenciar contas e usuários](#manage-accounts-and-users). As permissões podem ser herdadas (como o grupo de recursos contendo) ou diretamente atribuído ao recurso. Permissão para os logs para o recurso será atribuído automaticamente. |
| O que é o escopo das permissões? | Espaço de trabalho. Os usuários com acesso ao espaço de trabalho podem consultar todos os logs no espaço de trabalho de tabelas que tenham as permissões para. Consulte [controle de acesso de tabela](#table-level-rbac) | Recursos do Azure. Usuário pode consultar os logs para recursos eles têm acesso a partir de qualquer espaço de trabalho, mas não é possível consultar os logs para outros recursos. |
| Como pode logs de acesso do usuário? | Inicie **Logs** de **do Azure Monitor** menu ou **espaços de trabalho do Log Analytics**. | Inicie **Logs** do menu para o recurso do Azure. |


## <a name="access-control-mode"></a>Modo de controle de acesso
O _modo de controle de acesso_ é uma configuração em cada espaços de trabalho que define como as permissões são determinadas para esse espaço de trabalho.

**Requer permissões de espaço de trabalho**:  Este modo de controle não permite RBAC granular. Para um usuário acessar o espaço de trabalho, eles devem receber permissões para o espaço de trabalho ou tabelas específicas. 

Se um usuário acessa o espaço de trabalho no modo centrado no espaço de trabalho, eles terão acesso a todos os dados de todas as tabelas que receberam acesso ao. Se um usuário acessa o espaço de trabalho no modo centrado em recursos, eles terão acesso a dados apenas para esse recurso em todas as tabelas que receberam acesso ao.

Isso é a configuração padrão para todos os espaços de trabalho criados antes de março de 2019.

**Usar permissões de recurso ou o espaço de trabalho**: Este modo de controle permite RBAC granular. Os usuários recebem acesso a apenas os dados associados aos recursos que podem ser visualizados por meio de permissões do Azure, recursos para os quais eles têm `read` permissão. 

Quando um usuário acessa o espaço de trabalho no modo centrado no espaço de trabalho, permissões de espaço de trabalho serão aplicadas. Quando um usuário acessa o espaço de trabalho no modo centrado em recursos, apenas as permissões de recurso serão verificadas e permissões do espaço de trabalho serão ignoradas. Habilite o RBAC para um usuário removê-los das permissões de espaço de trabalho e permitindo que suas permissões de recurso a ser reconhecido.

Isso é a configuração padrão para todos os espaços de trabalho criados depois de março de 2019.

> [!NOTE]
> Se um usuário tiver apenas permissões de recurso para o espaço de trabalho, eles só poderão acessar o espaço de trabalho usando [modo centrado no recurso](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definir o modo de controle de acesso no portal do Azure
Você pode exibir o modo de controle de acesso de espaço de trabalho atual na **visão geral** página do espaço de trabalho a **espaço de trabalho do Log Analytics** menu.

![Modo de controle de acesso de espaço de trabalho de modo de exibição](media/manage-access/view-access-control-mode.png)

Você pode alterar essa configuração na **propriedades** página para o espaço de trabalho. Alterando a configuração será desabilitada se você não tiver permissões para configurar o espaço de trabalho.

![Alterar modo de acesso do espaço de trabalho](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definir o modo de controle de acesso no PowerShell

Use o comando a seguir para examinar o modo de controle de acesso para todos os espaços de trabalho na assinatura:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Use o script a seguir para definir o modo de controle de acesso para um espaço de trabalho específico:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Use o seguinte script para definir o modo de controle de acesso para todos os espaços de trabalho na assinatura

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definir o modo de acesso no modelo do Resource Manager
Para configurar o modo de acesso em um modelo do Azure Resource Manager, defina as **enableLogAccessUsingOnlyResourcePermissions** sinalizador no espaço de trabalho para um dos seguintes valores de recursos.

- **False**: Defina o espaço de trabalho às permissões centrado no espaço de trabalho. Isso é a configuração padrão se o sinalizador não estiver definido.
- **True**: Defina o espaço de trabalho como centrados no recurso de permissões.


## <a name="manage-accounts-and-users"></a>Gerenciar contas e usuários
As permissões para o espaço de trabalho que são aplicadas a um determinado usuário são definidas por seu modo de acesso e o [modo de controle de acesso](#access-control-mode) do espaço de trabalho. **Permissões de espaço de trabalho** são aplicadas quando um usuário acessa qualquer espaço de trabalho usando **centrada no espaço de trabalho** na [modo centrado no espaço de trabalho](#access-modes). **Permissões de recurso** são aplicadas quando um usuário acessa um espaço de trabalho com **usar permissões de recurso ou o espaço de trabalho** [modo de controle de acesso](#access-control-mode) usando [modo centrado em recursos ](#access-modes).

### <a name="workspace-permissions"></a>Permissões de espaço de trabalho
Cada workspace pode ter várias contas associadas e cada conta pode ter acesso a vários workspaces. O acesso é gerenciado via [acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md). 


As atividades a seguir também exigem permissões do Azure:

| Ação                                                          | Permissões do Azure necessárias | Observações |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de monitoramento                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Essas permissões precisam ser concedidas no nível de assinatura ou no grupo de recursos. |
| Alterar o tipo de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Exibir dados nos blocos de solução *Backup* e *Site Recovery* | Administrador/coadministrador | Acessa recursos implantados usando o modelo de implantação clássico |
| Criar um workspace no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gerenciar o acesso ao espaço de trabalho de análise de Log usando permissões do Azure 
Para conceder acesso ao espaço de trabalho do Log Analytics usando permissões do Azure, execute as etapas em [Usar atribuições de função para gerenciar o acesso aos recursos de sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

O Azure tem duas funções de usuário predefinidas para workspaces do Log Analytics:
- Leitor do Log Analytics
- Colaborador do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Exibir e pesquisar todos os dados de monitoramento 
- Exiba configurações de monitoramento, incluindo exibir a configuração do diagnóstico do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Type    | Permissão | DESCRIÇÃO |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de exibir todos os recursos do Azure e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidade de execução de consultas de Pesquisa de Logs v2 |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidade de execução de consultas de Pesquisa de Logs v1 |
| Ação | `Microsoft.Support/*` | Capacidade de abrir casos de suporte |
|Nenhuma Ação | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do workspace necessária para usar a API da coleta de dados e instalar os agentes. Isso impede que o usuário adicione os novos recursos para o workspace |


Os membros da função *Colaborador do Log Analytics* podem:
- Ler todos os dados de monitoramento, como o leitor do Log Analytics pode  
- Criar e configurar Contas de automação  
- Adicionar e remover soluções de gerenciamento    
    > [!NOTE] 
    > Para executar com êxito essas duas ações, essa permissão deve ser concedida no nível do grupo de recursos ou da assinatura.  

- Ler as chaves da conta de armazenamento   
- Configurar a coleta de logs no Armazenamento do Azure  
- Editar configurações de monitoramento dos recursos do Azure, incluindo
  - Adicionar a extensão VM às VMs
  - Configurar o diagnóstico do Azure em todos os recursos do Azure

> [!NOTE] 
> Você pode usar a capacidade de adicionar uma extensão da máquina virtual a uma máquina virtual para ter controle total sobre uma máquina virtual.

A função de Colaborador do Log Analytics inclui as seguintes ações do Azure:

| Permissão | DESCRIÇÃO |
| ---------- | ----------- |
| `*/read`     | Capacidade de exibir todos os recursos e a configuração do recurso. Inclui exibir: <br> Status de extensão da máquina virtual <br> Configuração do diagnóstico do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos |
| `Microsoft.Automation/automationAccounts/*` | Capacidade de criar e configurar Contas de automação do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover as extensões da máquina virtual, incluindo a extensão do Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Exiba a chave da conta de armazenamento. Necessário para configurar o Log Analytics para ler os logs das contas de armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover as regras de alerta |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as configurações de diagnóstico nos recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração dos workspaces do Log Analytics |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gerenciamento |
| `Microsoft.Resources/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crie e exclua implantações. Necessário para adicionar e remover soluções, workspace e contas de automação |

Para adicionar e remover usuários de uma função de usuário, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Use essas funções para conceder acesso aos usuários em escopos diferentes:
- Assinatura – acesso a todos os workspaces na assinatura
- Grupo de Recursos - acesso a todo workspace no grupo de recursos
- Recurso - acesso somente ao workspace especificado

Você deve executar as atribuições no nível do recurso (espaço de trabalho) para garantir que o controle de acesso preciso.  Use as [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recurso 
Quando fizer consultas dos usuários de um espaço de trabalho usando o acesso centralizado em recursos, eles terá as seguintes permissões no recurso:

| Permissão | DESCRIÇÃO |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de exibir todos os dados de log para o recurso.  |


Geralmente, essa permissão é concedida de uma função que inclui  _\*/leitura ou_ _\*_ permissões como a conta interna [leitor](../../role-based-access-control/built-in-roles.md#reader) e [ Colaborador](../../role-based-access-control/built-in-roles.md#contributor) funções. Observe que as funções personalizadas que incluem ações específicas ou funções internas dedicadas podem não incluir essa permissão.

Ver [definindo o controle de acesso por tabela](#table-level-rbac) abaixo se você deseja criar o controle de acesso diferentes para diferentes tabelas.


## <a name="table-level-rbac"></a>RBAC de nível de tabela
**RBAC de nível de tabela** permite que você forneça um controle mais granular para dados em um espaço de trabalho do Log Analytics, além de outras permissões. Esse controle permite que você defina tipos de dados específicos que são acessíveis somente a um conjunto específico de usuários.

Implementar o controle de acesso de tabela com [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) para conceder ou negar acesso a específico [tabelas](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) no espaço de trabalho. Essas funções são aplicadas a espaços de trabalho centrado no espaço de trabalho ou recurso centrado [modos de controle de acesso](#access-control-mode) independentemente do usuário [modo de acesso](#access-modes).

Criar uma [função personalizada](../../role-based-access-control/custom-roles.md) com as seguintes ações para definir o acesso ao controle de acesso de tabela.

- Para conceder acesso a uma tabela, incluí-lo na **ações** seção da definição da função.
- Para negar acesso a uma tabela, incluí-lo na **NotActions** seção da definição da função.
- Use * para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso para o _pulsação_ e _AzureActivity_ tabelas, criar uma função personalizada usando as seguintes ações:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso a apenas _SecurityBaseline_ e nenhuma outra tabela, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Logs personalizados
 Logs personalizados são criados por fontes de dados, como logs personalizados e a API do coletor de dados de HTTP. A maneira mais fácil de identificar o tipo de log está verificando as tabelas listadas em [Logs personalizados no esquema de log](../log-query/get-started-portal.md#understand-the-schema).

 No momento, você não pode conceder ou negar acesso aos logs personalizados individuais, mas você pode conceder ou negar acesso a todos os logs personalizados. Para criar uma função com acesso a todos os logs personalizados, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considerações

- Se um usuário recebe global leia permissão com as funções de Colaborador ou leitor padrão que incluem o  _\*/leitura_ ação, ele substituirá o controle de acesso por tabela e fornecer a eles acessem a todos os dados de log.
- Se um usuário recebe acesso por tabela, mas nenhuma outra permissão, eles poderão acessar os dados de log da API, mas não do portal do Azure. Para fornecer acesso ao portal do Azure, use o leitor do Log Analytics como sua função de base.
- Os administradores para a assinatura terá acesso a todos os tipos de dados, independentemente de outras configurações de permissão.
- Os proprietários do espaço de trabalho são tratados como qualquer outro usuário para controle de acesso por tabela.
- Você deve atribuir funções aos grupos de segurança em vez de usuários individuais para reduzir o número de atribuições. Isso também ajudará a usar ferramentas de gerenciamento de grupo existentes para configurar e verificar o acesso.




## <a name="next-steps"></a>Próximas etapas
* Consulte a [visão geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para reunir dados de computadores no datacenter ou outro ambiente de nuvem.
* Consulte [Coletar dados sobre as máquinas virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a coleta de dados de máquinas virtuais do Azure.  

