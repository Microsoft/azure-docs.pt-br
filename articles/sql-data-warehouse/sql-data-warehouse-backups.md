---
title: "Backups do SQL Data Warehouse do Azure - instantâneos, redundância geográfica | Microsoft Docs"
description: "Saiba mais sobre os backups do banco de dados interno do SQL Data Warehouse que permitem que você restaure um SQL Data Warehouse do Azure para um ponto de restauração ou outra região geográfica."
services: sql-data-warehouse
documentationcenter: 
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 54c0149a769e654139bbdf709802d49127f041ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-backups"></a>Backups do SQL Data Warehouse
O SQL Data Warehouse oferece backups locais e geográficos como parte de suas funcionalidades de backup do data warehouse. Essas funcionalidades incluem instantâneos de Blob de Armazenamento do Azure e armazenamento com redundância geográfica. Use backups de data warehouse para restaurar o data warehouse para um ponto de restauração na região primária ou restaurá-lo para uma região geográfica diferente. Este artigo explica as especificidades de backups no SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>O que é um backup do data warehouse?
Um backup do data warehouse são os dados que você pode usar para restaurar um data warehouse para um momento específico.  Considerando que o SQL Data Warehouse é um sistema distribuído, um backup do data warehouse consiste em vários arquivos que são armazenados em blobs do Azure. 

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para obter mais informações, veja [Visão geral sobre a continuidade dos negócios](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redundância de dados
O SQL Data Warehouse também protege seus dados armazenando-os em Armazenamento Premium LRS (localmente redundante) do Azure. Esse recurso do Armazenamento do Azure armazena várias cópias síncronas dos dados no datacenter local para assegurar a proteção transparente de dados em caso de falhas localizadas. A redundância de dados faz com que os dados possam sobreviver a problemas de infraestrutura como falhas de disco. A redundância de dados garante a continuidade de negócios com uma infraestrutura altamente disponível e tolerante a falhas.

Para saber mais sobre:

* Armazenamento Premium do Azure, veja [Introdução ao Armazenamento Premium do Azure](../storage/common/storage-premium-storage.md).
* Armazenamento com redundância local, veja [Replicação do Armazenamento do Azure](../storage/common/storage-redundancy.md#locally-redundant-storage).

## <a name="azure-storage-blob-snapshots"></a>Instantâneos do Azure Storage Blob
Como um benefício do uso do Armazenamento Premium do Azure, o SQL Data Warehouse usa instantâneos do Blob de Armazenamento do Azure para fazer backup do data warehouse localmente. Você pode restaurar um data warehouse para um ponto de restauração de instantâneo. Instantâneos iniciam no mínimo a cada oito horas e permanecem disponíveis por sete dias.  

Para saber mais sobre:

* Instantâneos de blob do Azure, consulte [Criar um instantâneo de blob](../storage/blobs/storage-blob-snapshots.md).

## <a name="geo-redundant-backups"></a>Backups com redundância geográfica
A cada 24 horas, o SQL Data Warehouse armazena um data warehouse completo no Armazenamento padrão. Um data warehouse completo é criado para coincidir com a hora do último instantâneo. O armazenamento padrão pertence a uma conta de armazenamento com RA-GRS (redundância geográfica com acesso de leitura). O recurso RA-GRS do Armazenamento do Azure replica os arquivos de backup para um [data center emparelhado](../best-practices-availability-paired-regions.md). Essa replicação geográfica garante que você possa restaurar um data warehouse caso não seja possível acessar os instantâneos em sua região primária. 

Esse recurso está ligado por padrão. Se você não quiser usar backups com redundância geográfica, é possível [recusar] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn). 

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com o banco de dados primário. 
> 
> 

> [!NOTE]
> Não é possível recusar backups com redundância geográfica no DWU 9000 e DWU 18000. 
>
> 

Para saber mais sobre:

* Armazenamento com redundância geográfica, veja [Replicação do Armazenamento do Azure](../storage/common/storage-redundancy.md).
* Armazenamento RA-GRS, veja [Armazenamento com redundância geográfica com acesso de leitura](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Período de retenção e agendamento de backup de data warehouse
O SQL Data Warehouse cria instantâneos em seus data warehouses online a cada quatro a oito horas e mantém cada instantâneo por sete dias. Você pode restaurar seu banco de dados online para um dos pontos de restauração nos últimos sete dias. 

Para ver quando o último instantâneo iniciado, execute esta consulta no SQL Data Warehouse online. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se você precisar manter um instantâneo por mais de sete dias, poderá restaurar um ponto de restauração para um novo data warehouse. Depois que a restauração for concluída, o SQL Data Warehouse iniciará criando instantâneos no novo data warehouse. Se você não fizer alterações no novo data warehouse, os instantâneos ficarão vazios e, portanto, o custo do instantâneo será mínimo. Você também pode pausar o banco de dados para impedir que o SQL Data Warehouse crie instantâneos.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>O que acontece com a retenção do meu backup enquanto o data warehouse está em pausa?
O SQL Data Warehouse não cria nem expira instantâneos enquanto um data warehouse está em pausa. A idade de instantâneo não será alterada enquanto o data warehouse estiver em pausa. A retenção do instantâneo baseia-se no número de dias pelos quais o data warehouse estiver online e não pelos dias do calendário.

Por exemplo, se um instantâneo inicia em 1º de outubro às 16h e o data warehouse colocado em pausa em 3 de outubro às 16h, o instantâneo é dois dias. Independentemente de quando o data warehouse voltar a ficar online, o instantâneo terá dois dias. Se o data warehouse voltar a ficar online em 5 de outubro às 16h, o instantâneo terá dois dias e permanecerá por mais cinco dias.

Quando o data warehouse de volta a ficar online, o SQL Data Warehouse retoma novos instantâneos e expira instantâneos quando eles têm dados com mais de sete dias.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>De quanto tempo é o período de retenção para um data warehouse descartado?
Quando um data warehouse é descartado, o data warehouse e os instantâneos são salvos por sete dias e, em seguida, removidos. Você pode restaurar o data warehouse para qualquer um dos pontos de restauração salvos.

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencerem a essa instância também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.
> 
> 

## <a name="data-warehouse-backup-costs"></a>Custos de backup do data warehouse
O custo total para o data warehouse primário e de sete dias de instantâneos de Blob do Azure é arredondado para o TB mais próximo. Por exemplo, se seu data warehouse é de 1,5 TB e os instantâneos usam 100 GB, você será cobrado pelo equivalente a 2 TB de dados segundo as taxas do Armazenamento Premium do Azure. 

> [!NOTE]
> Cada instantâneo está inicialmente vazio e aumenta à medida que você faz alterações ao depósito de dados primário. Todos os instantâneos aumentam de tamanho conforme o data warehouse é alterado. Portanto, os custos de armazenamento de instantâneos crescem de acordo com a taxa de alteração.
> 
> 

Se você estiver usando armazenamento com redundância geográfica, você receberá uma cobrança de armazenamento separada. O armazenamento com redundância geográfica é cobrado segundo a taxa padrão de RA-GRS (Armazenamento com Redundância Geográfica com Acesso de Leitura).

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Usando backups de banco de dados
O uso principal para backups do SQL data warehouse é restaurar o data warehouse para um dos pontos de restauração dentro do período de retenção.  

* Para restaurar um SQL data warehouse, veja [Restaurar um SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

## <a name="related-topics"></a>Tópicos relacionados
### <a name="scenarios"></a>Cenários
* Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* Para restaurar um data warehouse, veja [Restaurar um SQL data warehouse](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

