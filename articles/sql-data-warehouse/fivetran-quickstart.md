---
title: O início rápido do Fivetran para o SQL Data Warehouse do Azure | Microsoft Docs
description: Comece rapidamente com Fivetran e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 1d8b6d2df21fe0aabe85754e061db4deb129b32e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082504"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Comece rapidamente com Fivetran e Azure SQL Data Warehouse

Este início rápido descreve como configurar um novo usuário do Fivetran para trabalhar com o SQL Data Warehouse do Azure. O artigo pressupõe que você tenha uma instância existente do SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Configurar uma conexão

1. Encontre o nome completo do servidor e o nome do banco de dados que você usa para se conectar ao SQL Data Warehouse.
    
    Se você precisar de ajuda para encontrar essas informações, consulte [Conectar-se ao SQL Data Warehouse do Azure](sql-data-warehouse-connect-overview.md).

2. No assistente de configuração, escolha se deseja conectar seu banco de dados diretamente ou usando um túnel SSH.

   Se você optar por se conectar diretamente ao seu banco de dados, deverá criar uma regra de firewall para permitir o acesso. Esse método é o método mais simples e seguro.

   Se você optar por se conectar usando um túnel SSH, o Fivetran se conectará a um servidor separado em sua rede. O servidor fornece um túnel SSH para o seu banco de dados. Você deve usar este método se seu banco de dados estiver em uma sub-rede inacessível em uma rede virtual.

3. Adicione o endereço IP **52.0.2.4** ao seu firewall no nível do servidor para permitir conexões de entrada para sua instância do SQL Data Warehouse do Fivetran.

   Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar credenciais de usuário

1. Conecte-se ao seu SQL Data Warehouse do Azure usando o SQL Server Management Studio ou a ferramenta de sua preferência. Entre como um usuário administrador do servidor. Em seguida, execute os seguintes comandos SQL para criar um usuário para o Fivetran:
    - No banco de dados mestre: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - No banco de dados do SQL Data Warehouse:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao usuário Fivetran as seguintes permissões para o seu depósito:

    ```
    GRANT CONTROL to fivetran;
    ```

    A permissão CONTROL é necessária para criar credenciais no escopo do banco de dados que são usadas quando um usuário carrega arquivos do armazenamento de Blob do Azure usando o PolyBase.

3. Adicione uma classe de recurso adequada ao usuário do Fivetran. A classe de recurso que você usa depende da memória necessária para criar um índice columnstore. Por exemplo, as integrações com produtos como o Marketo e o Salesforce exigem uma classe de recursos maior devido ao grande número de colunas e ao maior volume de dados que os produtos usam. Uma classe de recurso mais alta requer mais memória para criar índices columnstore.

    Recomendamos que você use classes de recursos estáticos. Você pode começar com a classe de recurso `staticrc20`. A classe de recurso `staticrc20` aloca 200 MB para cada usuário, independentemente do nível de desempenho usado. Se a indexação de columnstore falhar no nível da classe de recursos inicial, aumente a classe de recurso.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para obter mais informações, leia sobre [memória e limites de simultaneidade](memory-and-concurrency-limits.md) e [classes de recurso](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Entrar para Fivetran

Para entrar no Fivetran, insira as credenciais que você usa para acessar o SQL Data Warehouse: 

* Host (o nome do servidor).
* Porta.
* Banco de dados.
* Usuário (o nome de usuário deve ser **fivetran\@_nome_do_servidor_**  onde *nome_do_servidor* faz parte do URI do host do Azure: ***server_name *. Database.Windows.NET**).
* Senha.
