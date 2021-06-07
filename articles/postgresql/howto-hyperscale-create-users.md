---
title: Criar usuários-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/8/2019
ms.openlocfilehash: 3d23ee6119b625e11ce44bb9ad11ce4b3ee0280d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295729"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Criar usuários no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

> [!NOTE]
> O termo "usuários" refere-se a usuários em um grupo de servidores de hiperescala (Citus). Para saber mais sobre os usuários de assinatura do Azure e seus privilégios, visite o [artigo controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/built-in-roles.md) ou examine [como personalizar funções](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>A conta do administrador do servidor

O mecanismo PostgreSQL usa [funções](https://www.postgresql.org/docs/current/sql-createrole.html) para controlar o acesso a objetos de banco de dados, e um grupo de servidores Citus (hiperscale) recém-criado vem com várias funções predefinidas:

* As [funções PostgreSQL padrão](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Como o Citus (subscale) é um serviço de PaaS gerenciado, somente a Microsoft pode entrar com a `postgres` função de superusuário. Para acesso administrativo limitado, o Citus (hiperescala) fornece a `citus` função.

Permissões para a `citus` função:

* Ler todas as variáveis de configuração, mesmo variáveis normalmente visíveis apenas para superusuários.
* Leia todas as \_ exibições de instrução PG \_ \* e use várias extensões relacionadas a estatísticas – até mesmo exibições ou extensões normalmente visíveis apenas para superusuários.
* Execute funções de monitoramento que podem ter acesso a bloqueios de compartilhamento em tabelas, possivelmente por muito tempo.
* [Crie extensões PostgreSQL](concepts-hyperscale-extensions.md) (porque a função é membro de `azure_pg_admin` ).

Notavelmente, a `citus` função tem algumas restrições:

* Não é possível criar funções
* Não é possível criar bancos de dados

## <a name="how-to-create-additional-user-roles"></a>Como criar funções de usuário adicionais

Conforme mencionado, a `citus` conta de administrador não tem permissão para criar usuários adicionais. Para adicionar um usuário, use a interface portal do Azure.

1. Vá para a página **funções** do seu grupo de servidores Citus (hiperescala) e clique em **+ Adicionar**:

   :::image type="content" source="media/howto-hyperscale-create-users/1-role-page.png" alt-text="A página funções":::

2. Insira o nome da função e a senha. Clique em **Salvar**.

   :::image type="content" source="media/howto-hyperscale-create-users/2-add-user-fields.png" alt-text="Adicionar função":::

O usuário será criado no nó de coordenador do grupo de servidores e propagado para todos os nós de trabalho. As funções criadas por meio do portal do Azure têm o `LOGIN` atributo, o que significa que são usuários verdadeiros que podem entrar no banco de dados.

## <a name="how-to-modify-privileges-for-user-role"></a>Como modificar privilégios para a função de usuário

As novas funções de usuário geralmente são usadas para fornecer acesso de banco de dados com privilégios restritos. Para modificar privilégios de usuário, use comandos PostgreSQL padrão, usando uma ferramenta como PgAdmin ou psql. (Consulte [conectando-se com psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) no guia de início rápido de hiperescala (Citus).)

Por exemplo, para permitir `db_user` a leitura `mytable` , conceda a permissão:

```sql
GRANT SELECT ON mytable TO db_user;
```

O Citus (subscale) propaga instruções GRANT de tabela única por todo o cluster, aplicando-as em todos os nós de trabalho. Ele também propaga concessões que são de todo o sistema (por exemplo, para todas as tabelas em um esquema):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Como excluir uma função de usuário ou alterar sua senha

Para atualizar um usuário, visite a página **funções** para seu grupo de servidores Citus (hiperescala) e clique nas reticências **...** ao lado do usuário. As reticências abrirão um menu para excluir o usuário ou redefinir sua senha.

   :::image type="content" source="media/howto-hyperscale-create-users/edit-role.png" alt-text="Editar uma função":::

A `citus` função é privilegiada e não pode ser excluída.

## <a name="next-steps"></a>Próximas etapas

Abra o firewall para os endereços IP dos computadores dos novos usuários para permitir que eles se conectem: [criar e gerenciar regras de firewall de Citus (hiperescala) usando o portal do Azure](howto-hyperscale-manage-firewall-using-portal.md).

Para obter mais informações sobre o gerenciamento de conta de usuário de banco de dados, consulte a documentação do produto PostgreSQL:

* [Funções e privilégios de banco de dados](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Direitos](https://www.postgresql.org/docs/current/static/ddl-priv.html)
