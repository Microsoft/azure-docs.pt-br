---
title: Segurança em nível de coluna para pool de SQL dedicado
description: A segurança em nível de coluna permite que os clientes controlem o acesso às colunas da tabela do banco de dados com base no contexto de execução do usuário ou na associação de grupo, simplificando o design e a codificação da segurança em seu aplicativo e permitindo que você implemente restrições de acesso a colunas.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: fb34051f7d4b24190806dde939c8cc6d9c2a4896
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679940"
---
# <a name="column-level-security"></a>Segurança ao nível da coluna

Column-Level segurança permite que os clientes controlem o acesso a colunas de tabela com base no contexto de execução do usuário ou na associação de grupo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Como esse vídeo foi lançado, a [segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ficou disponível para o pool SQL dedicado no Azure Synapse.

A segurança em nível de coluna simplifica o design e a codificação da segurança em seu aplicativo, permitindo que você restrinja o acesso às colunas para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas certas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso é localizado na camada de banco de dados, em vez de longe dos dados em outra camada de aplicativo. O banco de dados aplica as restrições de acesso sempre que houver uma tentativa de acessar dados em qualquer camada. Essa restrição torna a segurança mais robusta e confiável, reduzindo a área de superfície do sistema de segurança como um todo. Além disso, a segurança em nível de coluna também elimina a necessidade de introduzir modos de exibição para filtrar colunas para impor restrições de acesso em usuários.

Você pode implementar a segurança em nível de coluna com a instrução [Grant](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL. Com esse mecanismo, há suporte para a autenticação do SQL e do Azure Active Directory (Azure AD).

![O diagrama mostra uma tabela esquemático com a primeira coluna entre um cadeado fechado e suas células cor laranja, enquanto as outras colunas são células brancas.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintaxe

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Exemplo

O exemplo a seguir mostra como restringir `TestUser` de acessar a coluna `SSN` da tabela `Membership`:

Crie a tabela `Membership` com a coluna SSN usada para armazenar números de seguro social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permita que `TestUser` acesse todas as colunas, exceto a coluna SSN, que tem os dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

As consultas executadas como `TestUser` falharão se elas incluírem a coluna SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de uso

Alguns exemplos de como a segurança em nível de coluna está sendo usada atualmente:

- Uma empresa de serviços financeiros permite que apenas gerentes de contas tenham acesso aos números de CPF (cadastro de pessoas sociais), números de telefone e outros dados pessoais do cliente.
- Um provedor de saúde permite que apenas médicos e enfermeiros tenham acesso a registros médicos confidenciais, evitando que membros do departamento de cobrança vejam estes dados.
