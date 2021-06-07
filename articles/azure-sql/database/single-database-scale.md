---
title: Recursos para dimensionar um banco de dados
description: Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 02/22/2021
ms.openlocfilehash: 5852899175f9cc9f2725b875c6e1ce9fd682768d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625255"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Escalar recursos de banco de dados individual no Banco de Dados SQL do Azure

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para um banco de dados SQL do Azure na camada de computação provisionada. Como alternativa, a [camada de computação sem servidor](serverless-tier-overview.md) fornece dimensionamento automático de computação e listas por segundo para computação usada.

Depois de escolher inicialmente o número de vCores ou DTUs, você pode dimensionar um banco de dados individual verticalmente com base na experiência real usando o [portal do Azure](single-database-manage.md#the-azure-portal), o [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), o [PowerShell](/powershell/module/az.sql/set-azsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](/rest/api/sql/databases/update).

O vídeo a seguir mostra como alterar dinamicamente o nível de serviço e o tamanho da computação para aumentar os DTUs disponíveis para um banco de dados individual.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="impact"></a>Impacto

Alterar a camada de serviço ou o tamanho da computação envolve principalmente o serviço executando as seguintes etapas:

1. Crie uma nova instância de computação para o banco de dados. 

    Uma nova instância de computação é criada com a camada de serviço e o tamanho de computação solicitados. Para algumas combinações de camada de serviço e alterações de tamanho de computação, uma réplica do banco de dados deve ser criada na nova instância de computação, que envolve a cópia de dados e pode influenciar de forma forte a latência geral. Independentemente do, o banco de dados permanece online durante essa etapa, e as conexões continuam a ser direcionadas para o banco de dados na instância de computação original.

2. Alterne o roteamento de conexões para uma nova instância de computação.

    As conexões existentes com o banco de dados na instância de computação original são descartadas. Todas as novas conexões são estabelecidas com o banco de dados na nova instância de computação. Para algumas combinações de camada de serviço e alterações de tamanho de computação, os arquivos de banco de dados são desanexados e reanexados durante o comutador.  Independentemente disso, a opção pode resultar em uma breve interrupção do serviço quando o banco de dados não está disponível geralmente por menos de 30 segundos e geralmente por apenas alguns segundos. Se houver transações de execução longa em execução quando as conexões forem descartadas, a duração dessa etapa poderá levar mais tempo para recuperar as transações anuladas. A [recuperação de banco de dados acelerada](../accelerated-database-recovery.md) pode reduzir o impacto da anulação de transações de longa execução.

> [!IMPORTANT]
> Nenhum dado é perdido durante qualquer etapa no fluxo de trabalho. Verifique se você implementou alguma [lógica de repetição](troubleshoot-common-connectivity-issues.md) nos aplicativos e componentes que estão usando o banco de dados SQL do Azure enquanto a camada de serviço é alterada.

## <a name="latency"></a>Latency

A latência estimada para alterar a camada de serviço, dimensionar o tamanho de computação de um único banco de dados ou pool elástico, mover um banco de dados para dentro/para fora de um pool elástico ou mover um banco de dados entre pools elásticos é parametrizada da seguinte maneira:

|Camada de serviço|Banco de dados individual básico,</br>Padrão (S0-S1)|Pool elástico básico,</br>Standard (S2-S12), </br>Uso Geral banco de dados individual ou pool elástico|Banco de dados único ou pool elástico Premium ou Comercialmente Crítico|Hiperescala
|:---|:---|:---|:---|:---|
|**Banco de dados individual básico, </br> Standard (S0-S1)**|&bull;&nbsp;Latência de tempo constante independente do espaço usado</br>&bull;&nbsp;Normalmente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Pool elástico básico, </br> Standard (S2-S12), </br> uso geral banco de dados individual ou pool elástico**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Para bancos de dados individuais, latência de tempo constante independente do espaço usado</br>&bull;&nbsp;Normalmente, menos de 5 minutos para bancos de dados individuais</br>&bull;&nbsp;Para pools elásticos, proporcionalmente ao número de bancos de dados|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Banco de dados único ou pool elástico Premium ou Comercialmente Crítico**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia do dado</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Hiperescala**|N/D|N/D|N/D|&bull;&nbsp;Latência de tempo constante independente do espaço usado</br>&bull;&nbsp;Normalmente, menos de 2 minutos|

> [!NOTE]
> Além disso, para os bancos de dados Standard (S2-S12) e Uso Geral, a latência para mover um banco de dados para dentro/para fora de um pool elástico ou entre pools elásticos será proporcional ao tamanho do banco de dados se o banco de dados estiver usando o armazenamento de compartilhamento de arquivos Premium ([PFS](../../storage/files/storage-files-introduction.md)).
>
> Para determinar se um banco de dados está usando o armazenamento PFS, execute a consulta a seguir no contexto do banco de dados. Se o valor na coluna AccountType for `PremiumFileStorage` ou `PremiumFileStorage-ZRS` , o banco de dados estará usando o armazenamento PFS.

[!NOTE]
 A propriedade com redundância de zona permanecerá a mesma por padrão ao Dimensionar da Comercialmente Crítico para a camada de Uso Geral. A latência para esse downgrade quando a redundância de zona é habilitada, bem como a latência para alternar para a redundância de zona para a camada de Uso Geral será proporcional ao tamanho do banco de dados.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Para monitorar as operações em andamento, consulte: [gerenciar operações usando a API REST do SQL](/rest/api/sql/operations/list), [gerenciar operações usando a CLI](/cli/azure/sql/db/op), [monitorar operações usando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Cancelando alterações

Uma operação de alteração de camada de serviço ou redimensionamento de computação pode ser cancelada.

### <a name="the-azure-portal"></a>O portal do Azure

Na folha visão geral do banco de dados, navegue até **notificações** e clique no bloco indicando que há uma operação em andamento:

![Operação em andamento](./media/single-database-scale/ongoing-operations.png)

Em seguida, clique no botão rotulado **cancelar esta operação**.

![Cancelar operação em andamento](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Em um prompt de comando do PowerShell, defina `$resourceGroupName` , `$serverName` e e `$databaseName` , em seguida, execute o seguinte comando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Considerações adicionais

- Se você estiver atualizando para uma camada de serviço ou tamanho de computação superior, o tamanho máximo do banco de dados não aumentará a menos que você especifique explicitamente um tamanho maior (MaxSize).
- Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e o tamanho da computação de destino.
- Ao fazer o downgrade da camada **Premium** para a camada **Standard**, um custo de armazenamento extra será aplicado se (1) o tamanho máximo do banco de dados tiver suporte no tamanho da computação de destino e (2) o tamanho máximo ultrapassar a quantidade de armazenamento incluída de tamanho da computação de destino. Por exemplo, se um banco de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, um custo de armazenamento extra será aplicado, pois o S3 dá suporte a um tamanho máximo de 1 TB e seu valor de armazenamento incluído é de apenas 250 GB. Assim, a quantidade de armazenamento extra será 500 GB – 250 GB = 250 GB. Para obter o preço do armazenamento extra, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.
- Ao atualizar um banco de dados com [replicação geográfica](active-geo-replication-configure-portal.md) habilitada, atualize seus bancos de dados secundários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao atualizar para uma edição diferente, é um requisito de que o banco de dados secundário seja atualizado primeiro.
- Ao fazer downgrade de um banco de dados com [replicação geográfica](active-geo-replication-configure-portal.md) habilitada, faça downgrade dos seus bancos de dados primários para a camada de serviço e o tamanho da computação desejados antes de atualizar o banco de dados secundário (orientação geral para o melhor desempenho). Ao fazer downgrade para uma edição diferente, é um requisito de que o banco de dados primário seja rebaixado primeiro.
- As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade para a camada **básica** , haverá um período de retenção de backup menor. Consulte [Backups de Banco de Dados SQL do Azure](automated-backups-overview.md).
- As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.
- Quando a cópia de dados é necessária para dimensionar um banco de dado (consulte [latência](#latency)) ao alterar a camada de serviço, a alta utilização de recursos simultâneas para a operação de dimensionamento pode causar tempos de dimensionamento mais longos. Com a [ADR (recuperação de banco de dados acelerada)](/sql/relational-databases/accelerated-database-recovery-concepts), a reversão de transações de longa execução não é uma fonte de atraso significativa, mas o alto uso simultâneo de recursos pode deixar menos recursos de computação, armazenamento e largura de banda de rede para dimensionamento, especialmente para tamanhos de computação menores.

## <a name="billing"></a>Cobrança

Você será cobrado por cada hora de existência de um banco de dados usando a camada de serviço mais alta + o tamanho de computação aplicado durante essa hora, independentemente do uso ou se o banco de dados esteve ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

## <a name="change-storage-size"></a>Alterar o tamanho de armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado para o limite de tamanho máximo de armazenamento de dados usando incrementos de 1 GB. O armazenamento de dados mínimo configurável é de 1 GB. Consulte páginas de documentação de limite de recursos para bancos de dados [individuais](resource-limits-vcore-single-databases.md) e [pools elásticos](resource-limits-vcore-elastic-pools.md) para obter os limites de tamanho máximo de armazenamento em cada objetivo de serviço.
- O armazenamento de dados para um único banco de dado pode ser provisionado aumentando ou diminuindo seu tamanho máximo usando o [portal do Azure](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](/rest/api/sql/databases/update). Se o valor de tamanho máximo for especificado em bytes, ele deverá ser um múltiplo de 1 GB (1073741824 bytes).
- A quantidade de dados que pode ser armazenada nos arquivos de dados de um banco de dado é limitada pelo tamanho máximo do armazenamento de dados configurado. Além desse armazenamento, o banco de dados SQL do Azure aloca automaticamente 30% de armazenamento a ser usado para o log de transações.
- O banco de dados SQL do Azure aloca automaticamente 32 GB por vCore para o `tempdb` banco de dados. `tempdb` está localizado no armazenamento SSD local em todas as camadas de serviço.
- O preço do armazenamento de um único banco de dados ou de um pool elástico é a soma das quantidades de armazenamento e armazenamento dos logs de transações multiplicadas pelo preço unitário de armazenamento da camada de serviço. O custo do `tempdb` é incluído no preço. Para obter detalhes sobre o preço de armazenamento, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de DTU para um único banco de dados inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer as quantidades de armazenamento incluídas e os limites máximos de tamanho, confira [Banco de dados individual: tamanhos de armazenamento e tamanhos de computação](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- O armazenamento extra para um único banco de dados pode ser provisionado aumentando seu tamanho máximo usando o portal do Azure, o [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), o [PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update)ou a [API REST](/rest/api/sql/databases/update).
- O preço do armazenamento extra para um único banco de dados é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

### <a name="geo-replicated-database"></a>Banco de dados replicado geograficamente

Para alterar o tamanho do banco de dados de um banco de dados secundário replicado, altere o tamanho do banco de dados primário. Essa alteração será então replicada e implementada também no banco de dados secundário.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições de P11 e P15 quando o tamanho máximo for maior que 1 TB

Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central e Alemanha nordeste. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se o tamanho máximo de um banco de dados P11 ou P15 fosse definido como um valor maior que 1 TB, ele só poderá ser restaurado ou copiado para um banco de dados P11 ou P15.  Subsequentemente, o banco de dados pode ser redimensionado para um tamanho de computação diferente, desde que a quantidade de espaço alocado no momento da operação de redimensionamento não exceda os limites de tamanho máximo do novo tamanho de computação.
- Para cenários com replicação geográfica ativa:
  - Configurando uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também deverão ser P11 ou P15. O tamanho de computação inferior é rejeitado como secundários, pois eles não são capazes de dar suporte a mais de 1 TB.
  - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Há limitações de região para a opção superior a 1 TB. Se o secundário estiver em uma região que não dá suporte a mais de 1 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de importação/exportação para carregar bancos de dados P11/P15 com mais de 1 TB. Use SqlPackage.exe para [importar](database-import.md) e [exportar](database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

Para obter os limites de recursos gerais, consulte [limites de recursos baseados em VCORE do banco de dados SQL do Azure-bancos](resource-limits-vcore-single-databases.md) de dados individuais e [limites de recursos baseados em DTU do Azure SQL](resource-limits-dtu-single-databases.md).
