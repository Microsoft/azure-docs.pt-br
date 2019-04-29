---
title: Dimensionar os recursos de pool elástico – banco de dados SQL do Azure | Microsoft Docs
description: Esta página descreve os recursos de escala para pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848071"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensionar os recursos de pool elástico no banco de dados SQL do Azure

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para pools elásticos e bancos de dados em pool no Banco de Dados SQL do Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Alterar recursos de computação (vCores ou DTUs)

Depois de escolher inicialmente o número de vCores ou eDTUs, você pode dimensionar um pool Elástico para cima ou para baixo dinamicamente com base na experiência real usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), o [da CLI do Azure ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), ou o [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração de tamanho de computação da camada ou redimensionamento do serviço

Alterando o serviço de camada ou calcular o tamanho de um pool Elástico segue um padrão semelhante para bancos de dados individuais e envolve principalmente o serviço executando as seguintes etapas:

1. Criar nova instância de computação para o pool Elástico  

    Uma nova instância de computação para o pool Elástico é criada com a camada de serviço solicitado e o tamanho de computação. Para algumas combinações de camada de serviço e as alterações de tamanho de computação, uma réplica de cada banco de dados deve ser criada na nova instância de computação que envolve a cópia de dados e pode influenciar muito a latência geral. Independentemente disso, os bancos de dados permaneçam online durante esta etapa e continuam de conexões ser direcionado para os bancos de dados na instância de computação do original.

2. Alterne o roteamento de conexões para a nova instância de computação

    As conexões existentes com os bancos de dados na instância original de computação são descartadas. Novas conexões são estabelecidas com os bancos de dados na nova instância de computação. Para algumas combinações de camada de serviço e as alterações de tamanho de computação, os arquivos de banco de dados são desanexados e anexados novamente durante a troca.  Independentemente disso, a opção pode resultar em uma breve interrupção de serviço, quando os bancos de dados não estão disponíveis em geral para menos de 30 segundos e com frequência para apenas alguns segundos. Se houver de longa execução transações em execução quando as conexões são removidas, a duração dessa etapa pode levar mais tempo para recuperar transações anuladas. [Acelerada de recuperação de banco de dados](sql-database-accelerated-database-recovery.md) pode reduzir o impacto de anulação de transações de longa execução.

> [!IMPORTANT]
> Nenhum dado será perdido durante qualquer etapa no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alterar o tamanho de computação da camada ou redimensionamento do serviço

A latência para alterar a camada de serviço ou redimensionar o tamanho de computação de um único banco de dados ou pool Elástico é parametrizada da seguinte maneira:

|Camada de serviço|Banco de dados único básico,</br>Standard (S0-S1)|Pool Elástico básico,</br>Standard (S2-S12), </br>E em hiperescala </br>Geral finalidade único banco de dados ou pool Elástico|Premium ou comercialmente crítica de banco de dados único ou pool Elástico|
|:---|:---|:---|:---|
|**Banco de dados único básico,</br> Standard (S0-S1)**|&bull; &nbsp;Latência de tempo constante, independente do espaço usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Pool Elástico básico, </br>Standard (S2-S12) </br>e em hiperescala </br>único banco de dados de uso geral ou pool Elástico**|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência de tempo constante, independente do espaço usado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Premium ou comercialmente crítica de banco de dados único ou pool Elástico**|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull; &nbsp;Latência proporcional ao espaço de banco de dados usado devido à cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|

> [!NOTE]
>
> - No caso de alteração da camada de serviço ou redimensionamento de computação para um pool Elástico, o resumo de espaço usado em todos os bancos de dados no pool deve ser usado para calcular a estimativa.
> - No caso de mover um banco de dados de/para um pool Elástico, apenas o espaço usado pelo banco de dados afeta a latência, não o espaço usado por pool Elástico.
>
> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar tamanho da camada ou redimensionamento de computação de serviço

- Ao fazer o downgrade de vCores ou eDTUs para um pool Elástico, o espaço usado do pool deve ser menor do que o tamanho máximo permitido dos eDTUs de camada e o pool de serviço do destino.
- Ao redimensionar eDTUs de um pool Elástico ou de vCores, um custo de armazenamento extra será aplicado se (1) o tamanho máximo de armazenamento do pool é compatível com o pool de destino, e (2) o tamanho máximo do armazenamento excede a quantidade de armazenamento incluída do pool de destino. Por exemplo, se um pool Standard de 100 eDTU com um tamanho máximo de 100 GB for reduzido para um pool Standard de 50 eDTU, um custo de armazenamento extra será aplicado, pois o pool de destino dá suporte a um tamanho máximo de 100 GB, e a quantidade de armazenamento incluído é somente de 50 GB. Assim, a quantidade de armazenamento extra será 100 GB – 50 GB = 50 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.

### <a name="billing-during-rescaling"></a>Durante o redimensionamento de cobrança

Você será cobrado pelas horas em que um banco de dados existir usando a camada de serviço mais alta mais o tamanho da computação aplicado durante essas horas, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

## <a name="change-elastic-pool-storage-size"></a>Alterar o tamanho do armazenamento do pool elástico

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até o limite de tamanho máximo:

  - Para armazenamento nas camadas de serviço standard ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB
  - Para armazenamento nas camadas de serviço críticas premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB
- O armazenamento de um pool elástico pode ser provisionado aumentando ou diminuindo seu tamanho máximo.
- O preço do armazenamento para um pool elástico é a quantidade de armazenamento multiplicada pelo preço unitário do armazenamento da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de eDTU para um pool elástico inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites de tamanho máximos, consulte [Pool elástico: tamanhos de armazenamento e tamanhos da computação](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- É possível provisionar o armazenamento extra para um pool elástico aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) ou a [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um pool elástico é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="next-steps"></a>Próximas etapas

Para saber os limites de recurso global, confira [Limites de recurso baseado em vCore do banco de dados SQL – pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md) e [Limites de DTU de banco de dados do SQL com base em recurso – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).
