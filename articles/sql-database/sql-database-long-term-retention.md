---
title: "Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos | Microsoft Docs"
description: "Saiba como o Banco de Dados SQL do Azure dá suporte ao armazenamento de backups por um período de até 10 anos."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 25e651203f804fbf32d632b5f83145a3f3f72a7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos
Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Microsoft Azure. Ao utilizar o recurso de retenção de backup de longo prazo, você poderá armazenar seus backups do Banco de Dados SQL em um cofre dos Serviços de Recuperação do Azure por um período de até 10 anos. É possível armazenar até 1.000 bancos de dados por cofre. Você poderá selecionar qualquer backup no cofre para restaurá-lo como um novo banco de dados.

> [!IMPORTANT]
> A retenção de backup de longo prazo está atualmente em versão prévia e está disponível nas seguintes regiões: Leste da Austrália, Sudeste da Austrália, Sul do Brasil, EUA Central, Ásia Oriental, Leste dos EUA, Leste dos EUA 2, Índia Central, Sul da Índia, Leste do Japão, Oeste do Japão, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Europa Ocidental e Oeste dos EUA.
>

> [!NOTE]
> Habilite até 200 bancos de dados por cofre durante um período de 24 horas. É recomendável o uso de um cofre separado para cada servidor, a fim de minimizar o impacto desse limite. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Como funciona a retenção de backup de longo prazo do banco de dados SQL

Com a retenção de backup de longo prazo é possível associar um servidor de banco de dados SQL com um cofre dos Serviços de Recuperação do Azure. 

* O cofre deve ser criado na mesma assinatura do Azure que criou o SQL server e na mesma região geográfica e grupo de recursos. 
* Em seguida, você configura uma política de retenção para qualquer banco de dados. A política faz com que os backups semanais de banco de dados completo sejam copiados para o cofre dos Serviços de Recuperação e retidos durante o período de retenção especificado (até 10 anos). 
* Depois, será possível restaurar o banco de dados de qualquer um desses backups para um novo banco de dados em qualquer servidor na assinatura. O armazenamento do Azure cria uma cópia de backups existentes e a cópia não afeta o desempenho do banco de dados existente.

> [!TIP]
> Para um guia de instruções, consulte [Configurar e restaurar de uma retenção de backup de longo prazo do Banco de Dados SQL do Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Habilitar retenção de backup de longo prazo

Para configurar a retenção de backup de longo prazo em um banco de dados:

1. Crie um cofre dos Serviços de Recuperação do Azure na mesma região, assinatura e grupo de recursos que o servidor de banco de dados SQL. 
2. Registre o servidor no cofre.
3. Crie uma política de proteção dos Serviços de Recuperação do Azure.
4. Aplique a política de proteção aos bancos de dados que exigem a retenção de backup de longo prazo.

Para configurar, gerenciar e restaurar um banco de dados da retenção de backup de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure, execute um desses procedimentos:

* Utilizando o Portal do Azure: Clique em **Retenção de backup de longo prazo**, selecione um banco de dados e, em seguida, clique em **Configurar**. 

   ![Selecionar um banco de dados para retenção de backup a longo prazo](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Utilizando o PowerShell: Vá para [Configurar e restaurar de uma retenção de backup de longo prazo do Banco de Dados SQL do Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Restaurar um banco de dados armazenado com o recurso de retenção de backup de longo prazo

Para fazer a recuperação com base em um backup com retenção de longo prazo:

1. Liste o cofre em que o backup está armazenado.
2. Liste o contêiner que está mapeado para o servidor lógico.
3. Liste a fonte de dados no cofre que está mapeada para o banco de dados.
4. Liste os pontos de recuperação disponíveis para restauração.
5. Restaure o banco de dados do ponto de recuperação para o servidor de destino em sua assinatura.

Para configurar, gerenciar e restaurar um banco de dados da retenção de backup de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure, execute um desses procedimentos:

* Utilizando o Portal do Azure: Vá para [Gerenciar a retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md). 

* Utilizando o PowerShell: Vá para [Gerenciar a retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Obter preços para a retenção de backup de longo prazo

A retenção de backup de longo prazo de um Banco de Dados SQL é cobrada de acordo com as [taxas de preços dos serviços de backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

Depois que o servidor de banco de dados SQL é registrado no cofre, você será cobrado pelo armazenamento total usado pelos backups semanais armazenados no cofre.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Exibir backups disponíveis que são armazenados na retenção de backup de longo prazo

Para configurar, gerenciar e restaurar um banco de dados da retenção de backup de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure utilizando o portal do Azure, execute um desses procedimentos:

* Utilizando o Portal do Azure: Vá para [Gerenciar a retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md). 

* Utilizando o PowerShell: Vá para [Gerenciar a retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Desabilitar a retenção de longo prazo

O serviço de recuperação manipula automaticamente a limpeza de backups com base na política de retenção fornecida. 

Para parar de enviar os backups de um banco de dados específico para o cofre, remova a política de retenção do banco de dados.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> Os backups que já estão no cofre não são afetados. Eles são excluídos automaticamente pelo serviço de recuperação quando seu período de retenção expirar.

## <a name="long-term-backup-retention-faq"></a>FAQ sobre retenção de backup de longo prazo

**Posso excluir manualmente os backups específicos no cofre?**

Não atualmente. O cofre limpa os backups automaticamente quando o período de retenção expirar.

**Posso registrar meu servidor para armazenar backups em mais de um cofre?**

Não, atualmente só é possível armazenar backups em um cofre por vez.

**Posso ter um cofre e o servidor em assinaturas diferentes?**

Não, atualmente o cofre e o servidor devem estar na mesma assinatura e no mesmo grupo de recursos.

**Posso usar um cofre que criei em uma região diferente da região do servidor?**

Não, o cofre e o servidor devem estar na mesma região para minimizar o tempo de cópia e evitar os encargos de tráfego.

**Quantos bancos de dados posso armazenar em um cofre?**

Atualmente, o suporte é para até 1.000 bancos de dados por cofre. 

**Quantos cofres posso criar por assinatura?**

Você pode criar até 25 cofres por assinatura.

**Quantos bancos de dados posso configurar por dia e por cofre?**

Só é possível configurar até 200 bancos de dados por dia e por cofre.

**A retenção de backup de longo prazo funciona com pools elásticos?**

Sim. Qualquer banco de dados no pool pode ser configurado com a política de retenção.

**Posso escolher a hora em que o backup é criado?**

Não, o Banco de Dados SQL controla o agendamento de backups para minimizar o impacto no desempenho de seus bancos de dados.

**Tenho a transparent data encryption habilitada para meu banco de dados. Posso usá-lo com o cofre?** 

Sim, há suporte para a criptografia de dados transparente. Você poderá restaurar o banco de dados do cofre mesmo se o banco de dados original não existir mais.

**O que acontecerá com os backups no cofre se minha assinatura for suspensa?** 

Se sua assinatura for suspensa, manteremos os bancos de dados e backups existentes. Novos backups não são copiados para o cofre. Depois que você reativar a assinatura, o serviço continua a copiar os backups no cofre. Seu cofre torna-se acessível para as operações de restauração usando os backups que foram copiados nele antes da suspensão da assinatura. 

**Posso obter acesso aos arquivos de backup do banco de dados SQL para baixá-los ou restaurá-los no SQL Server?**

Não no momento.

**É possível ter vários agendamentos (diário, semanal, mensal, anual) em uma política de retenção do SQL?**

Não, atualmente vários agendamentos estão disponíveis apenas para backups de máquinas virtuais.

**E se configurarmos a retenção de backup de longo prazo em um banco de dados que é um banco de dados secundário de replicação geográfica ativa?**

Como não fazemos backups em réplicas, atualmente não há nenhuma opção para retenção de backup de longo prazo em bancos de dados secundários. No entanto, é importante que os usuários configurem a retenção de backup de longo prazo em um banco de dados de replicação geográfica ativa por esses motivos:
* Quando ocorrer um failover e o banco de dados se torna um banco de dados primário, fazemos um backup completo, o qual é carregado no cofre.
* Não há custo adicional ao cliente para configurar a retenção de backup de longo prazo em um banco de dados secundário.

## <a name="next-steps"></a>Próximas etapas
Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
