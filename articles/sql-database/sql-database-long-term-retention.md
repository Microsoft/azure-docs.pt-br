---
title: Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos | Microsoft Docs
description: Saiba como o Banco de Dados SQL do Azure dá suporte para o armazenamento de backups completos do banco de dados por até 10 anos.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/18/2019
ms.openlocfilehash: 6549892bfd04065bf83ab50fa5f5b439c35c4238
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190538"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Azure. Usando o recurso LTR (retenção de longo prazo), é possível armazenar backups completos especificados do Banco de Dados SQL no armazenamento de blobs [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados.

> [!NOTE]
> O LTR pode ser habilitado para bancos de dados individuais e em pool. Ainda não está disponível para bancos de dados de instâncias em hospedados em Instâncias Gerenciadas. Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](sql-database-automated-backups.md) para habilitar a restauração pontual (PITR). Se uma política LTR estiver configurada, esses backups são copiados para blobs diferentes para o armazenamento de longo prazo. A operação de cópia é um trabalho em segundo plano que não tem nenhum impacto de desempenho da carga de trabalho de banco de dados. Os backups LTR são mantidos por um período de tempo definido pela política LTR. A política LTR para cada banco de dados SQL também pode especificar a frequência com que os backups LTR são criados. Para permitir essa flexibilidade, você pode definir a política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, um backup durante a primeira semana de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Cada backup será mantido no armazenamento de longo prazo pelo período especificado por esses parâmetros. Qualquer alteração da política LTR se aplica a backups futuros. Por exemplo, se o WeekOfYear especificado está no passado quando a política estiver configurada, o primeiro backup LTR será criado próximo ano. 

Exemplos da política LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O terceiro backup completo de cada ano será mantido por cinco anos.
   
- W=0, M=3, Y=0

   O primeiro backup completo de cada mês será mantido por três meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por seis semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)



Se você modificar a política acima e definisse w=0 (sem backups semanais), a cadência das cópias de backup serão alterados conforme mostrado na tabela acima pelas datas destacadas. A as quantidade de armazenamento necessária para manter esses backups reduziria adequadamente. 

> [!IMPORTANT]
> O tempo dos backups LTR individuais é controlado por banco de dados SQL. Manualmente, você não pode criar um backup do LTR ou controlar o tempo da criação do backup. Depois de configurar uma política LTR, pode levar até 7 dias antes que o primeiro backup LTR aparecerá na lista de backups disponíveis.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se você estiver usando replicação geográfica ativa ou grupos de failover como sua solução de continuidade de negócios, você deve se preparar para eventual failovers e configurar a mesma política LTR do banco de dados secundário geográfico. O custo de armazenamento LTR não aumentará conforme os backups não são gerados de secundários. Somente quando o secundário tornar-se primário, os backups serão criados. Ele garante que não interrompido geração dos backups LTR quando o failover é acionado e o primário move para a região secundária. 

> [!NOTE]
> Quando o banco de dados primário original é recuperado de uma interrupção que causou o failover, ela se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 

## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção de longo prazo usando o portal do Azure ou o PowerShell, consulte [retenção de backup de longo prazo de gerenciar o Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurar o banco de dados de backup do LTR

Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. Para saber como restaurar seu banco de dados de um backup LTR, usando o portal do Azure ou o PowerShell, consulte [retenção de backup de longo prazo de gerenciar o Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
