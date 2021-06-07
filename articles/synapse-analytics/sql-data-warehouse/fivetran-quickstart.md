---
title: 'Início rápido: Fivetran e pool dedicado de SQL (antigo SQL DW)'
description: Introdução ao Fivetran e ao pool dedicado do SQL (antigo SQL DW) na análise de Synapse do Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96456365"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Início rápido: Fivetran com pool dedicado de SQL (antigo SQL DW) na análise de Synapse do Azure 

Este guia de início rápido descreve como configurar um novo usuário do Fivetran para trabalhar com um pool SQL dedicado (anteriormente conhecido como SQL DW). O artigo pressupõe que você tenha um pool SQL dedicado existente (anteriormente conhecido como SQL DW).

## <a name="set-up-a-connection"></a>Configurar uma conexão

1. Localize o nome do servidor totalmente qualificado e o nome do banco de dados que você usa para se conectar ao seu pool SQL dedicado (anteriormente conhecido como SQL DW).
    
    Se precisar de ajuda para encontrar essas informações, consulte [conectar-se ao seu pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-connection-strings.md).

2. No assistente de configuração, escolha se deseja conectar seu banco de dados diretamente ou usando um túnel SSH.

   Se você optar por se conectar diretamente ao seu banco de dados, deverá criar uma regra de firewall para permitir o acesso. Esse método é o método mais simples e seguro.

   Se você optar por se conectar usando um túnel SSH, o Fivetran se conectará a um servidor separado em sua rede. O servidor fornece um túnel SSH para o seu banco de dados. Você deve usar este método se seu banco de dados estiver em uma sub-rede inacessível em uma rede virtual.

3. Adicione o endereço IP **52.0.2.4** ao seu firewall de nível de servidor para permitir conexões de entrada para a instância do pool de SQL dedicada (anteriormente conhecido como SQL DW) do Fivetran.

   Para obter mais informações, consulte [Criar uma regra de firewall no nível do servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar credenciais de usuário

1. Conecte-se ao seu pool SQL dedicado (anteriormente conhecido como SQL DW) usando o SQL Server Management Studio (SSMS) ou a ferramenta que você preferir. Entre como um usuário administrador do servidor. Em seguida, execute os seguintes comandos SQL para criar um usuário para o Fivetran:

    - No banco de dados mestre: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - No banco de dados dedicado do pool SQL (anteriormente SQL DW):

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao usuário Fivetran as seguintes permissões para seu pool SQL dedicado (anteriormente conhecido como SQL DW):

    ```sql
    GRANT CONTROL to fivetran;
    ```

    A permissão CONTROL é necessária para criar credenciais no escopo do banco de dados que são usadas quando um usuário carrega arquivos do armazenamento de Blob do Azure usando o PolyBase.

3. Adicione uma classe de recurso adequada ao usuário do Fivetran. A classe de recurso que você usa depende da memória necessária para criar um índice columnstore. Por exemplo, as integrações com produtos como o Marketo e o Salesforce exigem uma classe de recursos maior devido ao grande número de colunas e ao maior volume de dados que os produtos usam. Uma classe de recurso mais alta requer mais memória para criar índices columnstore.

    Recomendamos que você use classes de recursos estáticos. Você pode começar com a classe de recurso `staticrc20`. A classe de recurso `staticrc20` aloca 200 MB para cada usuário, independentemente do nível de desempenho usado. Se a indexação de columnstore falhar no nível da classe de recursos inicial, aumente a classe de recurso.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para obter mais informações, leia sobre [memória e limites de simultaneidade](memory-concurrency-limits.md) e [classes de recurso](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Conectar-se do Fivetran

Para se conectar ao seu pool SQL dedicado (anteriormente conhecido como SQL DW) de sua conta do Fivetran, insira as credenciais que você usa para acessar seu pool SQL dedicado (anteriormente conhecido como SQL DW): 

* Host (o nome do servidor).
* Porta.
* Banco de dados.
* Usuário (o nome de usuário deve ser **fivetran\@_server_name_** em que *server_name* é parte da sua URI do host do Azure: **_server\_name_.database.windows.net**).
* Senha.
