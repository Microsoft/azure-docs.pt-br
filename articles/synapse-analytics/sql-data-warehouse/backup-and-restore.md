---
title: Backup e restauração-instantâneos, com redundância geográfica
description: Saiba como o backup e a restauração funcionam no pool SQL dedicado do Azure Synapse Analytics. Use backups para restaurar seu data warehouse para um ponto de restauração na região primária. Use backups com redundância geográfica para restaurar para uma região geográfica diferente.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 0fa6777dc5b587150f630ed8ccc110d16448cc21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602240"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Backup e restauração no pool SQL dedicado do Azure Synapse

Saiba como usar o backup e a restauração no pool SQL dedicado do Azure Synapse. Use pontos de restauração do pool do SQL dedicados para recuperar ou copiar seu data warehouse para um estado anterior na região primária. Use backups de data warehouse com redundância geográfica para restaurar para uma região geográfica diferente.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo data warehouse

Um *instantâneo de data warehouse* cria um ponto de restauração que pode ser aproveitado para recuperar ou copiar seu data warehouse para um estado anterior.  Como o pool do SQL dedicado é um sistema distribuído, um instantâneo de data warehouse consiste em muitos arquivos que estão localizados no armazenamento do Azure. Os instantâneos capturam as alterações incrementais dos dados armazenados no data warehouse.

A *restauração de um data warehouse* é um novo data warehouse criado por meio de um ponto de restauração de um data warehouse existente ou excluído. Restaurar o seu banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e de continuidade dos negócios, porque ela recria seus dados após uma exclusão ou corrupção acidental. O data warehouse também é um mecanismo eficiente para criar cópias do seu data warehouse para fins de teste ou desenvolvimento. As taxas de restauração do pool do SQL dedicado podem variar dependendo do tamanho do banco de dados e do local da data warehouse de origem e de destino.

## <a name="automatic-restore-points"></a>Pontos de restauração automática

Instantâneos são um recurso interno que cria pontos de restauração. Não é necessário habilitar essa funcionalidade. No entanto, o pool SQL dedicado deve estar em um estado ativo para a criação do ponto de restauração. Se ele for pausado com frequência, os pontos de restauração automáticas não poderão ser criados, portanto, certifique-se de criar um ponto de restauração definido pelo usuário antes de pausar o pool SQL dedicado. Atualmente, os pontos de restauração automáticos não podem ser excluídos pelos usuários, pois o serviço usa esses pontos de restauração para manter os SLAs para recuperação.

Os instantâneos de seus data warehouse são obtidos durante o dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL dedicado dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo foi iniciado, execute esta consulta em seu pool SQL dedicado online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Esse recurso permite que você acione manualmente os instantâneos para criar pontos de restauração do seu data warehouse antes e depois de grandes modificações. Esse recurso garante que os pontos de restauração sejam logicamente consistentes, o que fornece proteção de dados adicional no caso de quaisquer interrupções de carga de trabalho ou erros de usuário para o tempo de recuperação rápida. Os pontos de restauração definidos pelo usuário ficam disponíveis por sete dias e são excluídos automaticamente em seu nome. Não é possível alterar o período de retenção de pontos de restauração definidos pelo usuário. Garantimos apenas **42 pontos de restauração definidos pelo usuário** a qualquer momento; é necessário [excluir](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) algum ponto antes da criação de outro ponto de restauração. É possível disparar instantâneos para criar pontos de restauração definidos pelo usuário por meio do [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) ou do Portal do Azure.

> [!NOTE]
> Caso precise de pontos de restauração de mais de 7 dias, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse. Depois de ter restaurado, você tem o pool do SQL dedicado online e pode pausá-lo indefinidamente para economizar custos de computação. O banco de dados em pausa provoca encargos de armazenamento na taxa de armazenamento Synapse do Azure. Se você precisar de uma cópia ativa do data warehouse restaurado, poderá retomar, o que deve levar apenas alguns minutos.

### <a name="restore-point-retention"></a>Retenção do ponto de restauração

A lista a seguir detalha os períodos de retenção do ponto de restauração:

1. O pool SQL dedicado exclui um ponto de restauração quando atinge o período de retenção de 7 dias **e** quando há pelo menos 42 pontos de restauração totais (incluindo os definidos pelo usuário e automáticos).
2. Os instantâneos não são feitos quando um pool SQL dedicado é pausado.
3. A idade de um ponto de restauração é medida pelos dias de calendário absolutos a partir do momento em que o ponto de restauração é realizado, incluindo quando o pool do SQL é pausado.
4. A qualquer momento, é garantido que um pool SQL dedicado seja capaz de armazenar até 42 pontos de restauração definidos pelo usuário e 42 pontos de restauração automáticos, contanto que esses pontos de restauração não tenham atingido o período de retenção de 7 dias
5. Se um instantâneo for tirado, o pool do SQL dedicado será então pausado por mais de 7 dias e, em seguida, retomado, o ponto de restauração persistirá até que haja 42 de pontos de restauração totais (incluindo os definidos pelo usuário e automaticamente)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Retenção de instantâneo quando um pool de SQL é Descartado

Quando você remove um pool SQL dedicado, um instantâneo final é criado e salvo por sete dias. Você pode restaurar o pool de SQL dedicado para o ponto de restauração final criado na exclusão. Se o pool do SQL dedicado for descartado em um estado de pausa, nenhum instantâneo será obtido. Nesse cenário, certifique-se de criar um ponto de restauração definido pelo usuário antes de descartar o pool SQL dedicado.

> [!IMPORTANT]
> Se você excluir o servidor/espaço de trabalho que hospeda um pool SQL dedicado, todos os bancos de dados que pertencem ao servidor/espaço de trabalho também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

## <a name="geo-backups-and-disaster-recovery"></a>Backups geográficos e recuperação de desastre

Um backup geográfico é criado uma vez por dia para um [Data Center emparelhado](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). O RPO de uma restauração geográfica é de 24 horas. Você pode restaurar o backup geográfico para um servidor em qualquer outra região em que o pool SQL dedicado tenha suporte. Um backup geográfico garante que você possa restaurar um data warehouse caso não seja possível acessar os pontos de restauração em sua região primária.

Se você não precisar de backups geográficos para seu pool SQL dedicado, poderá desabilitá-los e economizar em custos de armazenamento de recuperação de desastre. Para fazer isso, consulte [como guiar: desabilitar backups geográficos para um pool de SQL dedicado (anteriormente conhecido como SQL DW)](disable-geo-backup.md). Observe que, se você desabilitar os backups geográficos, não será possível recuperar seu pool SQL dedicado para sua região do Azure emparelhada se sua data center primária do Azure não estiver disponível. 

> [!NOTE]
> Se precisar de um RPO mais curto para backups geográficos, escolha esta funcionalidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Você também pode criar um ponto de restauração definido pelo usuário e restaurar com base no ponto de restauração criado recentemente para um novo data warehouse em uma região diferente. Após a restauração, seu data warehouse estará online e será possível pausá-lo indefinidamente para em economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. Caso você precise de uma cópia ativa do data warehouse, poderá retomar, o que deve levar apenas alguns minutos.

## <a name="data-residency"></a>Residência de dadosResidência de dados 

Se o data center emparelhado estiver localizado fora do seu limite geográfico, você poderá garantir que seus dados permaneçam dentro de seu limite geográfico, recusando o armazenamento com redundância geográfica. Isso pode ser feito ao provisionar seu pool SQL dedicado (anteriormente SQL DW) por meio da opção de armazenamento com redundância geográfica ao criar ou restaurar um pool SQL dedicado (anteriormente conhecido como SQL DW). 

Para confirmar se o data center emparelhado está em um país diferente, consulte [regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="backup-and-restore-costs"></a>Custos de backup e restauração

Você observará que a fatura do Azure tem um item de linha para Armazenamento e um item de linha para Armazenamento para Recuperação de Desastre. O encargo de armazenamento é o custo total para armazenar seus dados na região primária junto com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são cobrados, veja [Understanding how Snapshots Accrue Charges](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Noções básicas sobre como os instantâneos acumulam cobranças). O encargo com redundância geográfica abrange o custo para armazenar os backups geográficos.  

O custo total para o data warehouse primário e de sete dias de alterações de instantâneos é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos capturam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure.

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do Azure Synapse, consulte [preços do Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Você não será cobrado pela saída de dados durante a restauração entre regiões.

## <a name="restoring-from-restore-points"></a>Restauração a partir dos pontos de restauração

Cada instantâneo cria um ponto de restauração que representa a hora em que o instantâneo é iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

É possível manter o data warehouse restaurado e o atual ou excluir um deles. Se desejar substituir o data warehouse atual pelo data warehouse restaurado, você poderá renomeá-lo usando [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) com a opção Modify Name.

Para restaurar um data warehouse, consulte [restaurar um pool SQL dedicado](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Para restaurar um data warehouse excluído ou em pausa, será possível [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Restauração de assinatura cruzada

Se você precisar restaurar diretamente na assinatura, vote nesse recurso [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaure para um servidor diferente e [' mova '](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o servidor em assinaturas para executar uma restauração de assinatura cruzada.

## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica

Você pode [restaurar seu pool SQL dedicado](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) para qualquer região com suporte para pool de SQL dedicado no nível de desempenho escolhido.

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre pontos de restauração, consulte [pontos de restauração definidos pelo usuário](sql-data-warehouse-restore-points.md)