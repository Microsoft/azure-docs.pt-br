---
title: "Configurar a segurança do Banco de Dados SQL do Azure para recuperação de desastre | Microsoft Docs"
description: "Este tópico explica as considerações de segurança para configurar e gerenciar a segurança após uma restauração de banco de dados ou um failover para um servidor secundário no caso de uma interrupção do data center ou outros desastres"
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: c7c898c9-69d4-4e16-8b7e-720bbb3353dd
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/13/2016
ms.author: sashan
ms.openlocfilehash: 48b35f761273c68b03af1fc5e977bb99455a01e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configurar e gerenciar a segurança do Banco de Dados SQL para restauração geográfica ou failover 

> [!NOTE]
> A [Replicação geográfica ativa](sql-database-geo-replication-overview.md) agora está disponível para todos os bancos de dados em todas as camadas de serviço.
>  

## <a name="overview-of-authentication-requirements-for-disaster-recovery"></a>Visão geral dos requisitos de autenticação para a recuperação de desastre
Este tópico descreve os requisitos de autenticação para configurar e controlar a [replicação geográfica ativa](sql-database-geo-replication-overview.md) e as etapas necessárias para configurar o acesso de usuário ao banco de dados secundário. Ele também descreve como habilitar o acesso ao banco de dados recuperado depois de usar a [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore). Para obter mais informações sobre as opções de recuperação, confira [Visão geral de continuidade dos negócios](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Recuperação de desastre com usuários independentes
Ao contrário de usuários tradicionais, que devem ser mapeados para logons no banco de dados mestre, um usuário independente é totalmente gerenciado pelo próprio banco de dados. Isso oferece dois benefícios. No cenário de recuperação de desastre, os usuários podem continuar a conectar ao novo banco de dados primário recuperado usando restauração geográfica sem qualquer configuração adicional, pois o banco de dados gerencia os usuários. Também há possíveis benefícios de desempenho e escalabilidade com esta configuração de uma perspectiva de logon. Para obter mais informações, consulte [Usuários do banco de dados independente - Tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx). 

A principal desvantagem é que gerenciar o processo de recuperação de desastre em grande escala é mais desafiador. Quando você tiver vários bancos de dados que usam o mesmo logon, manter as credenciais usando usuários independentes em vários bancos de dados pode invalidar os benefícios de usuários independentes. Por exemplo, a política de rotação de senha requer que alterações ocorram consistentemente em vários bancos de dados em vez de alterar a senha do logon apenas uma vez no banco de dados mestre. Por esse motivo, se você tiver vários bancos de dados que usam o mesmo nome de usuário e senha, a utilização de usuários independentes não será recomendada. 

## <a name="how-to-configure-logins-and-users"></a>Como configurar logons e usuários
Se estiver usando logons e usuários (em vez de usuários independentes), será necessário realizar etapas extras para garantir que os mesmos logons existam no banco de dados mestre. As seções a seguir descrevem as etapas envolvidas e considerações adicionais.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso do usuário a um banco de dados secundário ou recuperado
Para o banco de dados secundário poder ser usado como banco de dados secundário somente leitura e para garantir o acesso apropriado ao novo banco de dados primário ou o banco de dados recuperado usando a restauração geográfica, o banco de dados mestre do servidor de destino deve ter a configuração de segurança apropriadas em vigor antes da recuperação.

As permissões específicas para cada etapa são descritas posteriormente neste tópico.

A preparação do acesso do usuário a uma replicação geográfica secundária deve ser realizada como parte da configuração da replicação geográfica. A preparação do acesso do usuário aos bancos de dados restaurados geograficamente pode ser realizada a qualquer momento em que o servidor original estiver online (ex.: como parte do teste de DR).

> [!NOTE]
> Se o failover ou a restauração geográfica for para um servidor que não tem acesso de logons configurado corretamente, o acesso a ele será limitado à conta do administrador do servidor.
> 
> 

Configurar logons no servidor de destino envolve três etapas descritas abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determine os logons com acesso ao banco de dados primário:
A primeira etapa do processo é determinar quais logons devem ser duplicados no servidor de destino. Isso é feito com um par de instruções SELECT, uma no banco de dados mestre lógico no servidor de origem e outra no próprio banco de dados primário.

Somente o administrador de servidores ou um membro da função de servidor **LoginManager** pode determinar os logons no servidor de origem com a instrução SELECT a seguir. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Somente um membro da função de banco de dados db_owner, o usuário dbo ou o administrador de servidores pode determinar todas as entidades de usuário do banco de dados no banco de dados primário.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Localize o SID dos logons identificados na etapa 1:
Ao comparar a saída das consultas da seção anterior e fazer a correspondência dos SIDs, é possível mapear o logon do servidor para o usuário do banco de dados. Logons que têm um usuário de banco de dados com um SID correspondente têm acesso de usuário a esse banco de dados como essa entidade de usuário de banco de dados. 

A consulta a seguir pode ser usada para ver todas as entidades de usuário e seus SIDs em um banco de dados. Somente um membro da função de banco de dados db_owner ou o administrador de servidores pode executar essa consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Os usuários **INFORMATION_SCHEMA** e **sys** têm SIDs *NULL* e o SID **convidado** é **0x00**. O SID **dbo** pode começar com *0x01060000000001648000000000048454* se o criador do banco de dados foi o administrador do servidor, em vez de um membro do **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Crie os logons no servidor de destino:
A última etapa é acessar o servidor de destino, ou servidores, e gerar os logons com os SIDs apropriados. A sintaxe básica é mostrada a seguir.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Se desejar conceder ao usuário o acesso ao secundário, mas não ao primário, você poderá fazer isso alterando o logon do usuário no servidor primário usando a sintaxe a seguir.
> 
> ALTER LOGIN <login name> DISABLE
> 
> DISABLE não altera a senha, portanto você sempre poderá habilitá-la se necessário.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar o acesso ao banco de dados e os logons, veja [Segurança do Banco de Dados SQL: gerenciar a segurança de acesso e de logon do banco de dados](sql-database-manage-logins.md).
* Para saber mais sobre usuários de bancos de dados independentes, confira [Usuários de bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).
* Para saber como usar e configurar a replicação geográfica ativa, confira [replicação geográfica ativa](sql-database-geo-replication-overview.md)
* Para obter informações sobre como usar a restauração geográfica, confira [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore)

