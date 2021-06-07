---
title: Consumo de recursos do SQL do Synapse
description: Saiba mais sobre os modelos de consumo do Synapse SQL no Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 03144d478be0053ac77709132b08cdf17b062fb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666300"
---
# <a name="synapse-sql-resource-consumption"></a>Consumo de recursos do SQL do Synapse

Este artigo descreve os modelos de consumo de recursos do SQL do Synapse.

## <a name="serverless-sql-pool"></a>Pool de SQL sem servidor

O pool de SQL sem servidor é um serviço de pagamento por consulta que não exige a escolha do tamanho certo. O sistema se ajusta automaticamente com base nos seus requisitos, liberando você do gerenciamento da infraestrutura e escolhendo o tamanho certo para a sua solução.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Pool de SQL dedicado – DWUs (Unidades de Data Warehouse) e cDWUs (Unidades de Data Warehouse de computação)

Recomendações sobre como escolher o número ideal de unidades de DWUs (Unidades de Data Warehouse) para otimizar o preço e o desempenho e como alterar o número unidades.

### <a name="data-warehouse-units"></a>Unidades de Data Warehouse

Um pool de SQL do Synapse representa uma coleção de recursos analíticos que estão sendo provisionados. Recursos analíticos são definidos como uma combinação de CPU, memória e E/S. Esses três recursos são agrupados em unidades de escala de computação chamadas DWUs (Unidades de Data Warehouse). Uma DWU representa uma medida abstrata normalizada de recursos de computação e desempenho. Uma alteração em seu nível de serviço altera o número de DWUs disponíveis para o sistema. Essa alteração, por sua vez, ajusta o desempenho e o custo de seu sistema.

Para obter um melhor desempenho, você pode aumentar o número de Unidades de Data Warehouse. Para obter um desempenho menor, reduza as Unidades de Data Warehouse. Os custos de armazenamento e computação são cobrados separadamente, para que as mudanças de unidades de data warehouse não afetem os custos de armazenamento.

O desempenho de unidades de data warehouse baseia-se nestas métricas de carga de trabalho do data warehouse:

- A velocidade com a qual uma consulta de data warehouse padrão verifica um grande número de linhas e executa uma agregação complexa. Essa operação tem uso intensivo de E/S e CPU.
- A velocidade com a qual o data warehouse pode ingerir dados do Azure Storage Blobs ou do Azure Data Lake. Essa operação tem uso intensivo de rede e CPU.
- A velocidade com a qual o comando T-SQL [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) pode copiar uma tabela. Essa operação envolve a leitura de dados do armazenamento, distribuindo-os em todos os nós do dispositivo e gravando novamente no armazenamento. Essa operação é de uso intensivo de CPU, rede e E/S.

Aumento de DWUs:

- Altera de maneira linear o desempenho do sistema para verificações, agregações e instruções de CTAS
- Aumenta o número de leitores e gravadores para operações de carregamento do PolyBase
- Aumenta o número máximo de consultas simultâneas e slots de simultaneidade.

### <a name="service-level-objective"></a>Objetivo de nível de serviço

O Objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do data warehouse. Os níveis de serviço para Gen2 são medidos em unidades de computação do data warehouse (cDWU), por exemplo DW2000c. O nível de serviço Gen1 é medido em DWUs, por exemplo DW2000.

O Objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do data warehouse. Os níveis de serviço para o pool de SQL dedicado Gen2 são medidos em DWU (Unidades de Data Warehouse), por exemplo, DW2000c.

> [!NOTE]
> Recentemente o Azure Synapse Analytics Gen2 adicionou funcionalidades de dimensionamento adicional para dar suporte a camadas de computação tão baixas como 100 cDWU. Os data warehouses existentes em Gen1 que exigem níveis de computação menores agora fazer upgrade para Gen2 nas regiões que estejam disponíveis no momento sem nenhum custo adicional.  Se ainda não houver suporte para a sua região, você poderá atualizar para uma região com suporte. Para saber mais, confira [Atualizar para Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

No T-SQL, a configuração SERVICE_OBJECTIVE determina o nível de serviço e o nível de desempenho do pool de SQL dedicado.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Níveis de desempenho e unidades de data warehouse

Cada nível de desempenho usa uma unidade de medida ligeiramente diferente para suas unidades de data warehouse. Essa diferença é refletida na fatura à medida que a unidade de escala é convertida diretamente para cobrança.

- Data warehouses do Gen1 são medidos em Unidades do Data Warehouse (DWUs).
- Data warehouses do Gen2 são medidos em cDWUs (Unidades do Data Warehouse de computação).

DWUs e cDWUs oferecem suporte ao dimensionamento vertical ou horizontal de computação e à pausa da computação quando você não precisar usar o data warehouse. Todas essas operações são sob demanda. O Gen2 usa um cache local baseado em disco em nós de computação para melhorar o desempenho. Quando você dimensiona ou pausa o sistema, o cache é invalidado e, portanto, é necessário um período de aquecimento de cache antes que o desempenho ideal seja obtido.  

Conforme aumenta as unidades de data warehouse, você está aumentando linearmente os recursos de computação. O Gen2 fornece o melhor desempenho de consulta e a escala mais alta. Os sistemas do Gen2 fazem o melhor uso do cache.

#### <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma cota de [Unidade de Transação de Banco de Dados (DTU)](../../azure-sql/database/service-tiers-dtu.md) que permite um número específico de unidades de depósito de dados. Para mais informações, consulte o [limites de capacidade de gerenciamento de carga de trabalho](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>Avaliar o número de unidades de data warehouse necessárias

O número ideal de unidades de data warehouse depende muito de sua carga de trabalho e da quantidade de dados carregados no sistema.

Etapas para encontrar a melhor DWU para sua carga de trabalho:

1. Comece selecionando um DWU menor.
2. Monitore o desempenho do seu aplicativo à medida que testa as cargas de dados no sistema, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Identifique quaisquer requisitos adicionais para períodos de atividade de pico temporários. As cargas de trabalho que mostram picos significativos e ciclos na atividade podem precisar ser escaladas com frequência.

O pool de SQL é um sistema de expansão que pode provisionar várias quantidades de dados dimensionáveis de computação e consulta. Para ver seus verdadeiros recursos para dimensionamento, especialmente em DWUs maiores, é recomendável dimensionar o conjunto de dados ao dimensionar para garantir que você tenha dados suficientes para alimentar as CPUs. Para testar o dimensionamento, é recomendável usar pelo menos 1 TB.

> [!NOTE]
>
> O desempenho de consulta só aumentará com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se você achar que o dimensionamento não está alterando o desempenho, será necessário ajustar o design da tabela e/ou de suas consultas. Para obter diretrizes de ajuste de consulta, consulte [Gerenciar consultas de usuário](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="permissions"></a>Permissões

Alterar as unidades de data warehouse exige as permissões descritas em [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Funções internas do Azure, como Colaborador do BD SQL e Colaborador do SQL Server, podem alterar as configurações da DWU.

#### <a name="view-current-dwu-settings"></a>Exibir configurações atuais de DWU

Para exibir a configuração atual de DWU:

1. Abra o Pesquisador de Objetos do SQL Server no Visual Studio.
2. Conecte-se ao banco de dados mestre associado ao servidor SQL lógico.
3. Selecione do modo de exibição de gerenciamento dinâmico sys.database_service_objectives. Veja um exemplo:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Alterar unidades do data warehouse

#### <a name="azure-portal"></a>Portal do Azure

Para alterar DWUs:

1. Abra o [Portal do Azure](https://portal.azure.com), abra seu banco de dados e selecione **Escala**.

2. Em **Escala**, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

3. Selecione **Salvar**. Será exibida uma mensagem de confirmação. Selecione **sim** para confirmar ou **não** para cancelar.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para alterar as DWUs, use o cmdlet do PowerShell [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Para obter mais informações, confira [cmdlets do PowerShell para o Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

Com o T-SQL, você pode ver as configurações atuais de DWU, alterar as configurações e verificar o progresso.

Para alterar as DWUs:

1. Conecte-se ao banco de dados mestre associado ao servidor.
2. Use a instrução TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true). O exemplo a seguir define o objetivo de nível de serviço como DW1000c para o banco de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>APIs REST

Para alterar as DWUs, use a API REST [Criar ou Atualizar Banco de Dados](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true). O exemplo a seguir define o objetivo de nível de serviço como DW1000c para o banco de dados MySQLDW, que está hospedado no servidor MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de API REST, confira [APIs REST para o Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Verificar o status das alterações de DWU

As alterações de DWU podem levar vários minutos para concluir. Se estiver dimensionando automaticamente, considere implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação.

Verificar o estado do banco de dados por meio de vários pontos de extremidade permitirá que você implemente corretamente a automação. O portal fornece uma notificação após a conclusão de uma operação e o estado atual do bancos de dados, mas não permite a verificação de estado programática.

Não é possível verificar o estado do banco de dados para operações de expansão com o portal do Azure.

Para verificar o status de alterações de DWU:

1. Conecte-se ao banco de dados mestre associado ao servidor.
2. Envie a seguinte consulta para verificar o estado do banco de dados.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Envie a seguinte consulta para verificar o estado da operação

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Esta DMV retorna informações sobre várias operações de gerenciamento no pool de SQL dedicado, como a operação e o estado da operação, que será IN_PROGRESS ou COMPLETED.

### <a name="the-scaling-workflow"></a>O fluxo de trabalho do dimensionamento

Quando você inicia uma operação de escala, o sistema primeiro encerra todas as sessões abertas, revertendo as transações abertas para garantir um estado consistente. Para operações de dimensionamento, o dimensionamento ocorrerá somente após a conclusão dessa reversão de transação.  

- Para uma operação de escala vertical, o sistema desanexa todos os nós de computação, provisiona os nós de computação adicional e anexa-os novamente à camada de armazenamento.
- Para uma operação de redução vertical, o sistema desanexa todos os nós de computação e anexa apenas os nós necessários à camada de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciamento do desempenho, consulte [Classes de recursos para gerenciamento de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e [Limites de simultaneidade e memória](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).