---
title: Artigo sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados SQL do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: afafaa86988905329a0e4ff45f29bea9d1d57820
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501031"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure

Os problemas conhecidos e as limitações associados às migrações online do SQL Server para o Banco de Dados SQL do Azure são descritos abaixo.

> [!IMPORTANT]
> Com as migrações online do SQL Server para o banco de dados SQL do Azure, não há suporte para a migração de tipos de dado SQL_variant.

### <a name="migration-of-temporal-tables-not-supported"></a>A migração das tabelas temporais não é compatível

**Sintoma** Se o banco de dados de origem consistir em uma ou mais tabelas temporais, a migração do banco de dados falhará durante a operação de "carregamento de carga total" e você poderá ver a seguinte mensagem:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemplo de erros da tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução alternativa** Use as etapas a seguir.

1. Localize tabelas temporais no seu esquema de origem usando a consulta abaixo.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

3. Execute a atividade de migração novamente.

**Recursos** do Para obter mais informações, consulte o artigo [tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração das tabelas inclui uma ou mais colunas com o tipo de dados hierarchyid

**Sintoma** Você pode ver uma exceção SQL sugerindo "ntext é incompatível com hierarchyid" durante a operação de "carregamento de dados completo":

![Exemplo de erros de hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução alternativa** Use as etapas a seguir.

1. Encontre as tabelas de usuário que incluem colunas com o tipo de dados hierarchyid usando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exclua estas tabelas da folha **Definir configurações de migração**, em que você especifica tabelas para migração.

3. Execute a atividade de migração novamente.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com várias violações de integridade com gatilhos ativos no esquema durante o "Carregamento de dados completo" ou a "Sincronização de dados incremental"

**Solução alternativa** Use as etapas a seguir.

1. Localize os gatilhos que estão atualmente ativos no banco de dados de origem usando a consulta abaixo:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Desabilite os gatilhos em seu banco de dados de origem usando as etapas fornecidas no artigo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Execute a atividade de migração novamente.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma** Se o comprimento da coluna de objeto grande (LOB) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando a consulta abaixo:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solução alternativa** Se você tiver uma coluna LOB maior que 32 KB, entre em contato com a equipe de engenharia em [solicitar migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas de carimbo de data/hora

**Sintoma** O serviço de migração de banco de dados do Azure não migra o valor de timestamp de origem; em vez disso, o serviço de migração de banco de dados do Azure gera um novo valor timestamp na tabela de destino.

**Solução alternativa**

Se você precisar do serviço de migração de banco de dados do Azure para migrar o valor exato do carimbo de data/hora armazenado na tabela de origem, contate a equipe de engenharia em [solicitar migrações de banco de dados](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Os erros de migração de dados não fornecem detalhes adicionais sobre a folha status detalhado do banco de dados

**Sintoma** Quando você se depara com falhas de migração na exibição de status detalhes de bancos de **dados** , a seleção do link de erros de migração de dados na faixa de visão superior pode não fornecer detalhes adicionais específicos para as falhas de migração.

![exemplos de erros de migração de dados sem detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução alternativa** Para obter detalhes específicos da falha, use as etapas a seguir.

1. Feche a folha de status detalhado do banco de dados para exibir a tela Atividade de migração.

     ![tela de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Ver detalhes do erro** para exibir mensagens de erro específicas que ajudam você a solucionar problemas de erros de migração.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo de dados de Geografia sem suporte na migração online do SQLDB

**Sintoma** A migração falha com uma mensagem de erro que contém o seguinte texto:

 "* * encontrou um erro fatal", "errorEvents":<Table>.<Column> é do tipo ' geography ', que não tem suporte da ' carga completa ' no modo de suporte ' LOB completo '. "

**Solução alternativa** Embora o serviço de migração de banco de dados do Azure dê suporte ao tipo de dado geography para migrações offline para o Azure SQL Database, para migrações online, não há suporte para o DataType geography. Experimente os métodos alternativos para alterar o tipo de dados na origem para um Type com suporte antes de tentar usar o serviço de migração de banco de dados do Azure para uma migração online desse banco de dados.

### <a name="supported-editions"></a>Edições com suporte

**Sintoma** A migração falha com uma mensagem de erro que contém o seguinte texto:

 Erro de validação das configurações de migração: A edição do servidor [Business Intelligence Edition (64 bits)] não corresponde à (s) edição (ões) com suporte [Enterprise, Standard, Developer].

**Solução alternativa** O suporte para migrações online para o banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure se estende somente às edições Enterprise, Standard e Developer. Certifique-se de que você está usando uma edição com suporte antes de iniciar o processo de migração.
