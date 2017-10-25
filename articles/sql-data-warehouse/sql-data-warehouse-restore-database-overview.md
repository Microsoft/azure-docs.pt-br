---
title: "Restaurar um Azure data warehouse - local e com redundância geográfica | Microsoft Docs"
description: "Visão geral das opções de restauração para recuperar um banco de dados no SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: ea42b7135d0695b66d569095e70bb3d9f8b9594b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse-restore"></a>Restauração do SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

O SQL Data Warehouse oferece restaurações locais e geográficas como parte de suas funcionalidades de recuperação de desastre de warehouse. Use backups de data warehouse para restaurar o data warehouse para um ponto de restauração na região primária ou usar backups com redundância geográfica para restaurá-lo para uma região geográfica diferente. Este artigo explica as especificidades de restaurar um data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>O que é uma restauração do data warehouse?
A restauração de um data warehouse é um novo data warehouse criado por meio de um backup de um data warehouse existente ou excluído. O data warehouse restaurado recria o data warehouse com backup realizado em um momento específico. Considerando que o SQL Data Warehouse é um sistema distribuído, uma restauração do data warehouse é criada por meio de muitos arquivos de backup armazenados em blobs do Azure. 

A restauração do banco de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e de continuidade dos negócios, porque ela recria seus dados após uma exclusão ou corrupção acidental.

Para obter mais informações, consulte:

* [Backups do SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Visão geral da continuidade dos negócios](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Pontos de restauração do data warehouse
Como um benefício do uso do Armazenamento Premium do Azure, o SQL Data Warehouse usa instantâneos do Azure Storage Blob para fazer backup do data warehouse primário. Cada instantâneo tem um ponto de restauração que representa a hora em que o instantâneo é iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

O SQL Data Warehouse sempre restaura o backup para um novo data warehouse. É possível manter o data warehouse restaurado e o atual ou excluir um deles. Se você desejar substituir o data warehouse atual pelo data warehouse restaurado, poderá renomeá-lo.

Se você precisar restaurar um data warehouse excluído ou em pausa, será possível [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica
Você pode restaurar seu data warehouse para qualquer região com suporte do SQL Data Warehouse do Azure com o nível de desempenho escolhido. Observe que DWU 9000 e 18000 não têm suporte em todas as regiões durante a versão prévia.

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.
> 
> 

## <a name="restore-timeline"></a>Restaurar linha do tempo
É possível restaurar um banco de dados para qualquer ponto de restauração disponível nos últimos sete dias. Os instantâneos iniciam a cada 4-8 horas e permanecem disponíveis por sete dias. Quando um instantâneo possui mais de sete dias, ele expira e seu ponto de restauração fica indisponível.

## <a name="restore-costs"></a>Restaurar custos
O custo de armazenamento para o data warehouse restaurado é cobrado na taxa de Armazenamento Premium do Azure. 

Caso você pause um data warehouse restaurado, você será cobrado pelo armazenamento com a taxa de Armazenamento Premium do Azure. A vantagem de pausar é que você não é cobrado pelos recursos de computação DWU.

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Usos para restauração
O uso primário da restauração do data warehouse é recuperar os dados após a perda de dados ou corrupção acidental.

Também é possível usar a restauração de data warehouse para manter um backup por mais de sete dias. Quando o backup for restaurado, o seu data warehouse estará online e será possível pausá-lo indefinidamente para economizar custos de computação. O banco de dados em pausa tem encargos de armazenamento com a taxa de armazenamento Premium do Azure. 

## <a name="related-topics"></a>Tópicos relacionados
### <a name="scenarios"></a>Cenários
* Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Para executar uma restauração de data warehouse, restaure usando:

* O portal do Azure, consulte [Restore a data warehouse using the Azure portal (Restaurar um data warehouse usando o portal do Azure)](sql-data-warehouse-restore-database-portal.md)
* Cmdlets do PowerShell, consulte [Restore a data warehouse using PowerShell cmdlets (Restaurar um data warehouse usando cmdlets do PowerShell)](sql-data-warehouse-restore-database-powershell.md)
* APIs REST, consulte [Restore a data warehouse using the REST APIs (Restaurar um data warehouse usando APIs REST)](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
