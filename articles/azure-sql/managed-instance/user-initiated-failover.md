---
title: Iniciar manualmente um failover no SQL Instância Gerenciada
description: Saiba como fazer failover manualmente de réplicas primárias e secundárias no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/27/2021
ms.openlocfilehash: 3c969c1898e67361e37a825d7976b1c52d08dd24
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691137"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Failover manual iniciado pelo usuário na Instância Gerenciada de SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como fazer failover manual de um nó primário nas camadas de serviço GP (SQL Instância Gerenciada Uso Geral) e Comercialmente Crítico (BC) e como fazer failover manualmente de um nó de réplica somente leitura secundário na camada de serviço BC.

## <a name="when-to-use-manual-failover"></a>Quando usar o failover manual

A [alta disponibilidade](../database/high-availability-sla.md) é uma parte fundamental da plataforma SQL instância gerenciada que funciona de forma transparente para seus aplicativos de banco de dados. Failovers de nós primários para secundários em caso de degradação de nó ou detecção de falha ou durante atualizações de software mensais regulares são uma ocorrência esperada para todos os aplicativos que usam o SQL Instância Gerenciada no Azure.

Você pode considerar a execução de um [failover manual](../database/high-availability-sla.md#testing-application-fault-resiliency) no SQL instância gerenciada por alguns dos seguintes motivos:
- Aplicativo de teste para resiliência de failover antes de implantar na produção
- Testar sistemas de ponta a ponta para resiliência de falha em failovers automáticos
- Testar como o failover afeta as sessões de banco de dados existentes
- Verificar se um failover muda de desempenho de ponta a ponta devido a alterações na latência de rede
- Em alguns casos de degradações de desempenho de consulta, o failover manual pode ajudar a mitigar o problema de desempenho.

> [!NOTE]
> Garantir que seus aplicativos sejam resistentes a failover antes da implantação na produção ajudará a reduzir o risco de falhas de aplicativo na produção e contribuirá para a disponibilidade do aplicativo para seus clientes. Saiba mais sobre como testar seus aplicativos para prontidão de nuvem com o [teste da preparação da nuvem de aplicativo para resiliência de failover com a recodificação de vídeo do SQL instância gerenciada](https://youtu.be/FACWYLgYDL8) .

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Iniciar o failover manual no SQL Instância Gerenciada

### <a name="azure-rbac-permissions-required"></a>Permissões do RBAC do Azure necessárias

O usuário que estiver iniciando um failover precisará ter uma das seguintes funções do Azure:

- Função de proprietário da assinatura ou
- [Instância gerenciada função colaborador](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>Usando o PowerShell

A versão mínima do AZ. SQL precisa ser [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Considere o uso de [Azure cloud Shell](../../cloud-shell/overview.md) do portal do Azure que sempre tenha a versão mais recente do PowerShell disponível. 

Como pré-requisito, use o seguinte script do PowerShell para instalar os módulos do Azure necessários. Além disso, selecione a assinatura na qual Instância Gerenciada você deseja fazer o failover está localizado.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Use o comando [Invoke do PowerShell-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) com o exemplo a seguir para iniciar o failover do nó primário, aplicável à camada de serviço BC e GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Use o comando do PS a seguir para fazer failover do nó secundário de leitura, aplicável somente à camada de serviço BC.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Usando a CLI

Certifique-se de ter os scripts da CLI mais recentes instalados.

Use o comando AZ SQL mi failover CLI com o exemplo a seguir para iniciar o failover do nó primário, aplicável à camada de serviço BC e GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Use o comando da CLI a seguir para fazer failover do nó secundário de leitura, aplicável somente à camada de serviço BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Uso da API REST

Para usuários avançados que talvez precisem automatizar failovers de suas instâncias gerenciadas do SQL para fins de implementação do pipeline de teste contínuo, ou mitigadores de desempenho automatizados, essa função pode ser realizada por meio de um failover de inicialização por meio de uma chamada à API. consulte [instâncias gerenciadas – API REST de failover](/rest/api/sql/managed%20instances%20-%20failover/failover) para obter detalhes.

Para iniciar o failover usando a chamada à API REST, primeiro gere o token de autenticação usando o cliente de API de sua escolha. O token de autenticação gerado é usado como propriedade de autorização no cabeçalho da solicitação de API e é obrigatório.

O código a seguir é um exemplo do URI da API a ser chamado:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

As propriedades a seguir precisam ser passadas na chamada à API:

| **Propriedade de API** | **Parâmetro** |
| --- | --- |
| subscriptionId | ID da assinatura para a qual a instância gerenciada é implantada |
| resourceGroupName | Grupo de recursos que contém a instância gerenciada |
| managedInstanceName | Nome da instância gerenciada |
| réplicatype | Adicional (Primário ou ReadableSecondary). Esses parâmetros representam o tipo de réplica a ser reprovado: primário ou secundário legível. Se não for especificado, o failover será iniciado na réplica primária por padrão. |
| api-version | O valor estático e, no momento, precisa ser "2019-06-01-Preview" |

A resposta da API será uma das duas seguintes:

- 202 Aceito
- Um dos erros de solicitação 400.

O status da operação pode ser acompanhado por meio da revisão de respostas de API em cabeçalhos de resposta. Para obter mais informações, consulte [status das operações assíncronas do Azure](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Monitorar o failover

Para monitorar o progresso do failover iniciado pelo usuário para sua instância de BC, execute a seguinte consulta T-SQL em seu cliente favorito (como o SSMS) no SQL Instância Gerenciada. Ele lerá o sys.dm_hadr_fabric_replica_states de exibição do sistema e as réplicas de relatório disponíveis na instância. Atualize a mesma consulta depois de iniciar o failover manual.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Antes de iniciar o failover, sua saída indicará a réplica primária atual na camada de serviço BC que contém um primário e três secundários no grupo de disponibilidade AlwaysOn. Após a execução de um failover, executar essa consulta novamente precisará indicar uma alteração do nó primário.

Você não poderá ver a mesma saída com a camada de serviço GP como aquela acima mostrada para BC. Isso ocorre porque a camada de serviço de GP é baseada apenas em um único nó. Você pode usar a consulta T-SQL alternativa mostrando a hora em que o processo SQL foi iniciado no nó da instância da camada de serviço do GP:

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

A pequena perda de conectividade do cliente durante o failover, normalmente duradoura em um minuto, será a indicação da execução do failover, independentemente da camada de serviço.

> [!NOTE]
> A conclusão do processo de failover (não a indisponibilidade curta real) pode levar vários minutos por vez no caso de cargas de trabalho de **alta intensidade** . Isso ocorre porque o mecanismo de instância está tomando cuidado com todas as transações atuais no primário e acompanhe o nó secundário, antes de ser capaz de realizar o failover.

> [!IMPORTANT]
> As limitações funcionais do failover manual iniciado pelo usuário são:
> - Pode haver um (1) failover iniciado no mesmo Instância Gerenciada a cada **15 minutos**.
> - Para instâncias de BC, deve existir quorum de réplicas para que a solicitação de failover seja aceita.
> - Para instâncias de BC, não é possível especificar em qual réplica secundária legível iniciar o failover.
> - O failover não será permitido até que o primeiro backup completo de um novo banco de dados seja concluído por sistemas de backup automatizados.
> - O failover não será permitido se houver uma restauração de banco de dados em andamento.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como testar seus aplicativos para prontidão de nuvem com o [teste da preparação da nuvem de aplicativo para resiliência de failover com a recodificação de vídeo do SQL instância gerenciada](https://youtu.be/FACWYLgYDL8) .
- Saiba mais sobre a alta disponibilidade da instância gerenciada [alta disponibilidade para o Azure SQL instância gerenciada](../database/high-availability-sla.md).
- Para obter uma visão geral, consulte [o que é o Azure SQL instância gerenciada?](sql-managed-instance-paas-overview.md).
