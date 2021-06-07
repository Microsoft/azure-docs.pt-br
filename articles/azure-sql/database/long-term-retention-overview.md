---
title: Retenção de backup de longo prazo
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba como o banco de dados SQL do Azure & o Azure SQL Instância Gerenciada dá suporte ao armazenamento de backups completos de bancos de dados por até 10 anos por meio da política de retenção de longo prazo.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: shkale
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: b1ba3f98f39511bcebf94502ed749d7cba1fc71b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184042"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Retenção de longo prazo-banco de dados SQL do Azure e Azure SQL Instância Gerenciada

Muitos aplicativos têm regulamentação, conformidade ou outras finalidades comerciais que exigem que você retenha backups de banco de dados além dos 7-35 dias fornecidos pelo banco de dados SQL do Azure e pelo Azure SQL Instância Gerenciada [backups automáticos](automated-backups-overview.md). Usando o recurso de retenção de longo prazo (EPD), você pode armazenar o banco de dados SQL especificado e os backups completos do SQL Instância Gerenciada no armazenamento de BLOBs do Azure com [redundância configurada](automated-backups-overview.md#backup-storage-redundancy) por até 10 anos. Os backups EPD podem ser restaurados como um novo banco de dados.

A retenção de longo prazo pode ser habilitada para o banco de dados SQL do Azure e está disponível em visualização pública para o Azure SQL Instância Gerenciada. Este artigo fornece uma visão geral conceitual da retenção de longo prazo. Para configurar a retenção de longo prazo, consulte [Configurar o banco de dados SQL do Azure EPD](long-term-backup-retention-configure.md) e [Configurar o Azure SQL instância gerenciada EPD](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.

> [!IMPORTANT]
> A retenção de longo prazo no Instância Gerenciada está disponível atualmente somente na visualização pública nas regiões públicas do Azure. 


## <a name="how-long-term-retention-works"></a>Como a retenção de longo prazo funciona
     
A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](automated-backups-overview.md) para habilitar a restauração pontual (PITR). Se uma política de LTR for configurada, esses backups serão copiados para blobs diferentes para armazenamento de longo prazo. A cópia é um trabalho em segundo plano que não afeta o desempenho na carga de trabalho do banco de dados. A política EPD para cada banco de dados no banco de dados SQL também pode especificar com que frequência os backups EPD são criados.

Para permitir a LTR, você pode definir uma política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, o primeiro backup de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Se a WeekOfYear especificada estiver no passado quando a política for configurada, o primeiro backup LTR será criado no ano seguinte. Cada backup será mantido no armazenamento de longo prazo de acordo com os parâmetros de política configurados quando o backup LTR é criado.

> [!NOTE]
> Qualquer alteração na política de LTR se aplica somente a backups futuros. Por exemplo, se a retenção de backup semanal (W), retenção de backup mensal (M) ou retenção de backup anual (Y) for modificada, a nova configuração de retenção será aplicada somente a novos backups. A retenção de backups existentes não será modificada. Se sua intenção for excluir backups LTR antigos antes do término do período de retenção, será necessário [excluir manualmente os backups](./long-term-backup-retention-configure.md#delete-ltr-backups).
> 

Exemplos da política de LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O 3º backup completo de cada ano será mantido por 5 anos.
   
- W=0, M=3, Y=0

   O 1º backup completo de cada mês será mantido por 3 meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por 6 semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/long-term-retention-overview/ltr-example.png)


Se você modificar a política acima e definir W=0 (sem backups semanais), a cadência das cópias de backup mudará, conforme mostrado na tabela acima pelas datas destacadas. A as quantidade de armazenamento necessária para manter esses backups reduziria adequadamente. 

> [!IMPORTANT]
> O tempo de backups de LTR individuais é controlado pelo Azure. Não é possível criar manualmente um backup EPD ou controlar o tempo de criação do backup. Depois de configurar uma política de LTR, pode levar até 7 dias para que o primeiro backup LTR apareça na lista de backups disponíveis.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se você estiver usando grupos de failover ou replicação geográfica ativa como sua solução de continuidade de negócios, deverá se preparar para failovers eventuales e configurar a mesma política EPD no banco de dados ou instância secundária. O custo de armazenamento EPD não aumentará conforme os backups não forem gerados dos secundários. Os backups são criados somente quando o secundário torna-se primário os backups serão criados. Isso garante a geração ininterrupta de backups de LTR quando o failover for acionado e o primário for movido para a região secundária. 

> [!NOTE]
> Quando o banco de dados primário original se recuperar da indisponibilidade que causou o failover, ele se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 


## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Você pode configurar a retenção de backup de longo prazo usando o portal do Azure e o PowerShell para o banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure. Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor ou instância gerenciada existente na mesma assinatura do banco de dados original.

Para saber como configurar a retenção de longo prazo ou restaurar um banco de dados do backup para o banco de dados SQL usando o portal do Azure ou o PowerShell, consulte [gerenciar retenção de backup de longo prazo do banco de dados SQL do Azure](long-term-backup-retention-configure.md).

Para saber como configurar a retenção de longo prazo ou restaurar um banco de dados do backup para o SQL Instância Gerenciada usando o PowerShell, consulte [gerenciar o SQL Azure instância gerenciada retenção de backup de longo prazo](../managed-instance/long-term-backup-retention-configure.md).

Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. Para saber como restaurar seu banco de dados a partir de um backup de LTR, usando o portal do Azure ou o PowerShell, consulte [Gerenciar a retenção de longo prazo de backup do Banco de Dados SQL do Azure](long-term-backup-retention-configure.md). 

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. 

- Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](../database/automated-backups-overview.md)
