---
title: Autorizar o acesso ao servidor e ao banco de dados usando logons e contas de usuário
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba mais sobre como o banco de dados SQL do Azure, o SQL Instância Gerenciada e o Azure Synapse autenticam usuários para acesso usando logons e contas de usuário. Além disso, saiba como conceder funções de banco de dados e permissões explícitas para autorizar logons e usuários a executar ações e consultar o dado.
keywords: segurança do banco de dados SQL, gerenciamento de segurança de banco de dados, segurança de logon, segurança de banco de dados, acesso ao banco de dados
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: a0e9cc9967509eba05bd26f3bad86eefeeb6ca3e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639476"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autorizar acesso ao Banco de Dados SQL, à Instância Gerenciada de SQL e ao Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste artigo, você aprenderá sobre:

- Opções para configurar o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics para permitir que os usuários executem tarefas administrativas e acessem os dados armazenados nesses bancos.
- A configuração de acesso e autorização depois de criar inicialmente um novo servidor.
- Como adicionar logons e contas de usuário no banco de dados mestre e contas de usuário e conceder a essas contas permissões administrativas.
- Como adicionar contas de usuário em bancos de dados de usuário, associadas a logons ou a contas de usuário contidas.
- Configure contas de usuário com permissões em bancos de dados de usuário usando funções de banco de dados e permissões explícitas.

> [!IMPORTANT]
> No banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse são referenciados coletivamente no restante deste artigo como bancos de dados, e o servidor está se referindo ao [servidor](logical-servers.md) que gerencia bancos de dados do Azure SQL Database e do Azure Synapse.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

A [**autenticação**](security-overview.md#authentication) é o processo de provar que o usuário é quem diz ser. Um usuário se conecta a um banco de dados com uma conta de usuário.
Uma conta de usuário e informações de autenticação são fornecidas quando um usuário tenta se conectar a um banco de dados. O usuário é autenticado usando um dos dois métodos de autenticação a seguir:

- [Autenticação SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  O usuário envia um nome de conta de usuário e uma senha associada para estabelecer uma conexão ao usar esse método de autenticação. Essa senha é armazenada no banco de dados mestre das contas de usuário vinculadas a um logon ou é armazenada no banco de dados que contém as contas de usuário *não* vinculadas a um logon.
- [Autenticação do Azure Active Directory](authentication-aad-overview.md)

  Com esse método de autenticação, o usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure Active Directory (Azure AD).

**Logons e usuários**: uma conta de usuário em um banco de dados pode ser associada a um logon que é armazenado no banco de dados mestre ou pode ser um nome de usuário que é armazenado em um banco de dados individual.

- Um **logon** é uma conta individual no banco de dados mestre para a qual uma conta de usuário pode ser vinculada em um ou mais bancos de dados. Com um logon, as informações de credencial para a conta de usuário são armazenadas com o logon.
- Uma **conta de usuário** é uma conta individual em qualquer banco de dados que pode ser, mas não precisa estar vinculada a um logon. Com uma conta de usuário que não está vinculada a um logon, as informações da credencial são armazenadas com a conta de usuário.

A [**autorização**](security-overview.md#authorization) para acessar dados e executar várias ações são gerenciadas usando funções de banco de dados e permissões explícitas. A autorização refere-se às permissões atribuídas a um usuário e determina o que o usuário tem permissão para fazer. A autorização é controlada pelas [associações de função](/sql/relational-databases/security/authentication-access/database-level-roles) e [permissões no nível do objeto](/sql/relational-databases/security/permissions-database-engine) do banco de dados da conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logons existentes e contas de usuário após criar um novo banco de dados

Ao implantar o SQL do Azure pela primeira vez, você especifica um logon de administrador e uma senha associada para esse logon. Essa conta administrativa é chamada de **Administrador do servidor**. A seguinte configuração de logons e de usuários nos bancos de dados mestre e de usuário ocorre durante a implantação:

- Um logon do SQL com privilégios de administrador é criado usando o nome de logon especificado. Um [logon](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta de usuário individual para fazer logon no banco de dados SQL, SQL instância gerenciada e Azure Synapse.
- Esse logon recebe permissões administrativas completas em todos os bancos de dados como uma [entidade de segurança no nível do servidor](/sql/relational-databases/security/authentication-access/principals-database-engine). O logon tem todas as permissões disponíveis e não pode ser limitado. Em um Instância Gerenciada do SQL, esse logon é adicionado à [função de servidor fixa sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) (essa função não existe no banco de dados SQL do Azure).
- Uma [conta de usuário](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) chamada `dbo` é criada para esse logon em cada banco de dados de usuário. O usuário [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de banco de dados no banco de dados e é mapeado para a função de banco de dados fixa `db_owner`. Funções de banco de dados fixa adicionais são discutidas posteriormente neste artigo.

Para identificar as contas de administrador de um banco de dados, abra o portal do Azure e vá até a guia **Propriedades** do seu servidor ou instância gerenciada.

![Administradores do SQL Server](./media/logins-create-manage/sql-admins.png)

![Captura de tela que realça a opção de menu Propriedades.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> O nome de logon do administrador não pode ser alterado após ter sido criado. Para redefinir a senha para o administrador do servidor, vá para o [portal do Azure](https://portal.azure.com), clique em **Servidores SQL**, selecione o servidor na lista e clique em **Redefinir Senha**. Para redefinir a senha para o Instância Gerenciada do SQL, vá para o portal do Azure, clique na instância e clique em **Redefinir senha**. Você também pode usar o PowerShell ou a CLI do Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Crie logons e usuários adicionais com permissões administrativas

Neste ponto, o servidor ou a instância gerenciada só é configurada para acesso usando uma única conta de usuário e logon do SQL. Você tem as seguintes opções (dependendo do seu modo de implantação) para criar logons adicionais com permissões administrativas totais ou parciais:

- **Criar uma conta de administrador de Azure Active Directory com permissões administrativas completas**

  Habilite a autenticação do Azure Active Directory e crie um logon de administrador do Azure AD. Uma conta de Azure Active Directory pode ser configurada como um administrador da implantação do SQL Azure com permissões administrativas completas. Essa conta pode ser uma conta de grupo de segurança ou individual. Um administrador do Azure AD **deve** ser configurado se você quiser usar contas do Azure ad para se conectar ao banco de dados SQL, SQL instância gerenciada ou Azure Synapse. Para obter informações detalhadas sobre como habilitar a autenticação do Azure AD para todos os tipos de implantação do Azure SQL, consulte os seguintes artigos:

  - [Usar a autenticação Azure Active Directory para autenticação com o SQL](authentication-aad-overview.md)
  - [Configurar e gerenciar autenticação do Azure Active Directory com SQL](authentication-aad-configure.md)

- **No SQL Instância Gerenciada, crie logons do SQL com permissões administrativas completas**

  - Crie um logon do SQL adicional no banco de dados mestre.
  - Adicione o logon à [função de servidor fixa sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) usando a instrução [ALTERAR FUNÇÃO DE SERVIDOR](/sql/t-sql/statements/alter-server-role-transact-sql). Este logon terá permissões administrativas totais.
  - Crie um [logon do Azure AD](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) usando a sintaxe [CRIAR LOGON](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) como alternativa.

- **No banco de dados SQL, crie logons do SQL com permissões administrativas limitadas**

  - Crie um logon do SQL adicional no banco de dados mestre.
  - Crie uma conta de usuário no banco de dados mestre associada a esse novo logon.
  - Adicione a conta de usuário ao `dbmanager` , à `loginmanager` função ou ambas no banco de `master` dados usando a instrução [ALTER role](/sql/t-sql/statements/alter-role-transact-sql) (para Synapse do Azure, use a instrução [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager` e as `loginmanager` funções **não** pertencem a implantações do SQL instância gerenciada.

  Os membros dessas [funções de banco de dados mestre especiais](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) para o banco de dados SQL do Azure têm autoridade para criar e gerenciar bancos ou para criar e gerenciar logons. Em bancos de dados criados por um usuário que seja membro da função `dbmanager`, o membro é mapeado na função de banco de dados fixa `db_owner` e pode fazer logon e gerenciar esse banco de dados usando a conta de usuário do `dbo`. Essas funções não têm permissões explícitas fora do banco de dados mestre.

  > [!IMPORTANT]
  > Você não pode criar um logon do SQL adicional com permissões administrativas completas no banco de dados SQL.

## <a name="create-accounts-for-non-administrator-users"></a>Crie contas para usuários não administradores

Você pode criar contas para usuários não administradores usando um dos dois métodos:

- **Criar um logon**

  Crie um logon do SQL no banco de dados mestre. Depois crie uma conta de usuário em cada banco de dados ao qual o usuário precisa acessar e associe a conta de usuário a esse logon. Essa abordagem é preferida quando o usuário deve acessar vários bancos de dados e se você quiser manter as senhas sincronizadas. No entanto, essa abordagem tem complexidades quando usadas com replicação geográfica, já que o logon deve ser criado no servidor primário e no(s) servidor(es) secundário(s). Para obter mais informações, consulte [Configurar e gerenciar a segurança do Banco de Dados SQL do Azure para restauração geográfica ou failover ](active-geo-replication-security-configure.md).
- **Criar uma conta de usuário**

  Crie uma conta de usuário no banco de dados para a qual um usuário precisa de acesso (também chamado de [usuário independente](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Com o banco de dados SQL, você sempre pode criar esse tipo de conta de usuário.
  - Com o SQL Instância Gerenciada com suporte a [entidades de segurança de servidor do Azure ad](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities), você pode criar contas de usuário para autenticar para o SQL instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente.

  Com essa abordagem, as informações de autenticação de usuário são armazenadas em cada banco de dados e replicadas automaticamente nos bancos de dados replicados geograficamente. No entanto, se a mesma conta existir em vários bancos de dados e você estiver usando a autenticação do SQL Azure, você deverá manter as senhas sincronizadas manualmente. Além disso, se um usuário tiver uma conta em bancos de dados diferentes com senhas diferentes, lembrar dessas senhas pode se tornar um problema.

> [!IMPORTANT]
> Para criar usuários independentes mapeados para identidades do Azure AD, você deve estar conectado usando uma conta do Azure AD que seja um administrador no banco de dados no banco de dados SQL do Azure. No SQL Instância Gerenciada, um logon do SQL com `sysadmin` permissões também pode criar um logon ou usuário do Azure AD.

Para obter exemplos de criar logons e usuários, consulte:

- [Criar logon para o banco de dados SQL do Azure](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)
- [Criar logon para o Azure SQL Instância Gerenciada](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true#examples-2)
- [Criar logon para o Azure Synapse](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-3)
- [Criar usuário](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Criando usuários independentes do Azure AD](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Para obter um tutorial de segurança que inclui a criação de usuários no banco de dados SQL do Azure, consulte [tutorial: proteger banco de dados SQL do Azure](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Usando funções de banco de dados fixas e personalizadas

Após criar uma conta de usuário em um banco de dados, seja com base em um logon ou como um usuário independente, você pode autorizar esse usuário a executar várias ações e acessar dados em um banco de dados em particular. Você pode usar os seguintes métodos para autorizar o acesso:

- **Funções de banco de dados fixa**

  Adicione a conta do usuário a uma [função de banco de dados fixa](/sql/relational-databases/security/authentication-access/database-level-roles). Há 9 funções de banco de dados fixa, cada uma com um conjunto definido de permissões. As funções de banco de dados fixa mais comuns são: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é usada normalmente para conceder permissão total a apenas alguns usuários. As outras funções fixas de banco de dados são úteis para mover rapidamente um banco de dados simples para desenvolvimento, mas não são recomendadas para a maioria dos bancos de dados de produção. Por exemplo, a função de banco de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas no banco de dados, sendo mais do que é estritamente necessário.

  - Para adicionar um usuário a uma função de banco de dados fixa:

    - No Banco de Dados SQL do Azure, use a instrução [ALTERAR FUNÇÃO](/sql/t-sql/statements/alter-role-transact-sql). Para obter exemplos, consulte [Exemplos de ALTERAR FUNÇÃO](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse, use a instrução [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Para obter exemplos, consulte [Exemplos de sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples).

- **Função de banco de dados personalizada**

  Crie uma função de banco de dados personalizada usando a instrução [CRIAR FUNÇÃO](/sql/t-sql/statements/create-role-transact-sql). Uma função personalizada permite criar suas próprias funções de banco de dados definidas pelo usuário e conceder cuidadosamente a cada função o mínimo de permissões necessárias para a necessidade de negócios. Você pode adicionar usuários à função personalizada. Quando um usuário for membro de várias funções, ele agregará as permissões de todas elas.
- **Conceder permissões diretamente**

  Conceda [permissões](/sql/relational-databases/security/permissions-database-engine) à conta de usuário diretamente. Há mais de 100 permissões que podem ser concedidas ou negadas individualmente no Banco de Dados SQL. Muitas dessas permissões são aninhadas. Por exemplo, a permissão `UPDATE` em um esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Assim como ocorre na maioria dos sistemas de permissão, a negação de uma permissão substitui uma concessão. Devido à natureza aninhada e ao número de permissões, talvez seja necessário realizar um estudo cuidadoso para criar um sistema de permissões apropriado a fim de proteger corretamente o banco de dados. Comece com a lista de permissões em [Permissões (Mecanismo do Banco de Dados)](/sql/relational-databases/security/permissions-database-engine) e examine o [gráfico com tamanho de pôster](/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Usando grupos

O gerenciamento de acesso eficiente usa permissões atribuídas a grupos de segurança do Active Directory e funções fixas ou personalizadas em vez de usuários individuais.

- Ao usar a autenticação do Azure Active Directory, coloque os usuários do Azure Active Directory em um grupo de segurança do Azure Active Directory. Crie um usuário de banco de dados independente para o grupo. Adicione um ou mais usuários de banco de dados como um membro a funções de banco de dados personalizadas ou internas com as permissões específicas apropriadas para esse grupo de usuários.

- Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Coloque um ou mais usuários do banco de dados em uma função de banco de dados personalizada com permissões específicas adequadas para esse grupo de usuários.

  > [!NOTE]
  > Você também pode usar grupos para usuários de banco de dados dependentes.

Você deve estar familiarizado com os seguintes recursos que podem ser usados para limitar ou elevar as permissões:

- [Representação](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e [assinatura de módulo](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser usadas para elevar permissões temporariamente com segurança.
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security) pode ser usado para limitar quais linhas de um usuário pode acessar.
- [Mascaramento de dados](dynamic-data-masking-overview.md) pode ser usado para limitar a exposição de dados confidenciais.
- [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser usados para limitar as ações que podem ser executadas no banco de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral de todos os recursos do banco de dados SQL do Azure e do SQL Instância Gerenciada, consulte [visão geral de segurança](security-overview.md).
