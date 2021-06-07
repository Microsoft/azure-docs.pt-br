---
title: Habilitar Transparent Data Encryption para Stretch Database (T-SQL)
description: Habilitar TDE (Transparent Data Encryption) para SQL Server Stretch Database no Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 772341d046186e46b79ad7b11170e1bad23a3a6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024187"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Habilitar TDE (Transparent Data Encryption) para Stretch Database no Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

O TDE (Transparent Data Encryption) ajuda a proteger contra ameaças de atividades mal-intencionadas por meio da execução de criptografia e descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem exigir mudanças no aplicativo.

A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. A chave de criptografia do banco de dados está protegida por um certificado do servidor interno. O certificado do servidor interno é exclusivo para cada servidor do Azure. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [TDE (Transparent Data Encryption)].

## <a name="enabling-encryption"></a>Habilitando a criptografia
Para habilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Conecte ao banco de dados *mestre* no servidor que está hospedando o banco de dados do Azure, usando um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Para desabilitar a TDE para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Conecte-se ao banco de dados *mestre* que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificando a criptografia
Para verificar o status da criptografia para um banco de dados do Azure que armazena os dados migrados de um banco de dados do SQL Server habilitado para ampliação, faça o seguinte:

1. Conecte-se ao banco de dados *mestre* ou de instância que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica um banco de dados criptografado, ```0``` indica um banco de dados não criptografado.

<!--Anchors-->
[TDE (Transparent Data Encryption)]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->

<!--Link references-->