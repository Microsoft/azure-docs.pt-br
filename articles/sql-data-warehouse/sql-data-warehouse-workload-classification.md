---
title: Classificação do SQL Data Warehouse do Azure | Microsoft Docs
description: Diretrizes para usar a classificação para gerenciar a simultaneidade, importância e os recursos para consultas no Azure SQL Data Warehouse de computação.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 4988d284bed46a918f85eec8d7b4a5b89fc6549e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588500"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Classificação de carga de trabalho do Azure SQL Data Warehouse

Este artigo explica o processo de classificação de carga de trabalho do SQL Data Warehouse da atribuição de uma classe de recurso e a importância às solicitações de entrada.

## <a name="classification"></a>classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classificação de gerenciamento de carga de trabalho permite que as políticas de carga de trabalho a ser aplicado às solicitações por meio da atribuição [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância](sql-data-warehouse-workload-importance.md).

Embora haja várias maneiras de classificar as cargas de trabalho de armazenamento de dados, a classificação mais simples e mais comum é a carga e a consulta. Carregar dados com insert, update e delete instruções.  Você consulta os dados usando o seleciona. Uma solução de data warehouse geralmente terá uma política de carga de trabalho para a atividade de carga, como a atribuição de uma classe de recurso maior com mais recursos. Uma política de carga de trabalho diferentes pode aplicar a consultas, como a menor importância em comparação comparada para carregar atividades.

Você também pode subclassify suas cargas de trabalho de consulta e de carga. Subclassificação lhe dá mais controle das cargas de trabalho. Por exemplo, cargas de trabalho de consulta podem consistir em atualizações do cubo, consultas ao painel ou consultas ad hoc. Você pode classificar cada uma dessas cargas de trabalho de consulta com classes de recursos diferente ou as configurações de importância. Carga também pode beneficiar subclassificação. Transformações grandes podem ser atribuídas a classes de recursos maiores. Importância mais alta pode ser usada para garantir que os dados de vendas principais forem carregador antes dos dados de clima ou um feed de dados sociais.

Nem todas as instruções são classificadas como eles não exigem recursos ou precisam de importância para influenciar a execução.  Os comandos DBCC, instruções BEGIN, COMMIT e ROLLBACK TRANSACTION não são classificadas.

## <a name="classification-process"></a>Processo de classificação

A classificação no SQL Data Warehouse é feita hoje, atribuindo usuários a uma função que tenha uma classe de recurso correspondente atribuída a ele usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). A capacidade para caracterizar as solicitações que ultrapassarem um logon para uma classe de recurso é limitada com essa funcionalidade. Um método mais avançado para classificação agora está disponível com o [CLASSIFICADOR de carga de trabalho criar](/sql/t-sql/statements/create-workload-classifier-transact-sql) sintaxe.  Com essa sintaxe, os usuários do SQL Data Warehouse podem atribuir importância e uma classe de recurso para solicitações.  

> [!NOTE]
> Classificação é avaliada em uma base por solicitação. Várias solicitações em uma única sessão podem ser classificadas de forma diferente.

## <a name="classification-precedence"></a>Precedência de classificação

Como parte do processo de classificação, a precedência é em vigor para determinar qual classe de recurso é atribuído. Classificação com base em um usuário de banco de dados tem precedência sobre a associação de função. Se você criar um classificador que mapeia o usuário de banco de dados de UserA à classe de recurso mediumrc. Em seguida, mapear a função de banco de dados FunçãoUma (dos quais o UsuárioA é um membro) para a classe de recurso largerc. O classificador que mapeia o usuário de banco de dados para a classe de recurso mediumrc terá precedência sobre o classificador que mapeia a função de banco de dados FunçãoUma para a classe de recurso largerc.

Se um usuário for um membro de várias funções com classes de recursos diferente atribuído ou correspondido vários classificadores, o usuário recebe a atribuição de classe de recurso mais alta.  Esse comportamento é consistente com o comportamento de atribuição de classe de recurso existente.

## <a name="system-classifiers"></a>Classificadores de sistema

Classificação de carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores de sistema mapeiam associações de função de classe de recurso existente para as alocações de recursos de classe de recurso com prioridade normal. Classificadores de sistema não podem ser descartados. Para exibir os classificadores de sistema, você pode executar a consulta abaixo:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Misturar as atribuições de classe com classificadores de recursos

Classificadores de sistema criados em seu nome fornecem um caminho fácil para migrar a classificação de carga de trabalho. Usando mapeamentos de função de classe de recurso com a precedência de classificação, pode levar a classificação incorreta, quando você começar a criar novos classificadores com importância.

Considere este cenário:

- Um data warehouse existente tem um usuário de banco de dados que dbauser atribuído à função de classe de recurso largerc. A atribuição de classe de recurso foi feita com sp_addrolemember.
- O data warehouse foi atualizado com o gerenciamento de carga de trabalho.
- Para testar a nova sintaxe de classificação, a função de banco de dados DBARole (que DBAUser é um membro da), tem um classificador criado para eles, mapeando-os para mediumrc e de alta prioridade.
- Quando DBAUser faz logon e executa uma consulta, a consulta será atribuída para largerc. Porque um usuário tem precedência sobre uma associação de função.

Para simplificar a classificação incorreta de solução de problemas, recomendamos que você remover mapeamentos de função de classe de recurso que você crie classificadores de carga de trabalho.  O código a seguir retorna as associações de função de classe de recurso existente.  Execute [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) para cada nome de membro retornado de classe de recurso correspondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como criar um classificador, consulte o [criar o CLASSIFICADOR de carga de trabalho (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Consulte o guia de início rápido sobre como criar um classificador de carga de trabalho [criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md).
- Consulte os artigos de instruções [configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.