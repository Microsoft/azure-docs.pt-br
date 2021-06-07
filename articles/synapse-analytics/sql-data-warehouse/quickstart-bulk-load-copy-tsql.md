---
title: 'Início Rápido: Carregar dados em massa usando uma instrução T-SQL'
description: Carregar dados em massa usando a instrução COPY
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2b41342ab7d267c37b8e68fdbcaa9d570034ac17
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568430"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Início Rápido: Carregar dados em massa usando a instrução COPY

Neste guia de início rápido, você carregará dados em massa para seu pool de SQL dedicado usando a [instrução COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) simples e flexível para a ingestão de dados com alta taxa de transferência. A instrução COPY é o utilitário de carregamento recomendado, pois permite carregar dados de maneira direta e flexível oferecendo funcionalidades para:

- Permitir que usuários com menor privilégio carreguem sem a necessidade de permissões CONTROL estritas no data warehouse
- Usar apenas uma instrução T-SQL sem precisar criar nenhum objeto de banco de dados adicional
- Usar um modelo de permissão mais preciso sem expor as chaves de conta de armazenamento usando SAS (Assinaturas de Acesso Compartilhado)
- Especificar uma conta de armazenamento diferente para a localização ERRORFILE (REJECTED_ROW_LOCATION)
- Personalizar valores padrão para cada coluna de destino e especificar campos de dados de origem para carregar em colunas de destino específicas
- Especificar um terminador de linha personalizado para arquivos CSV
- Escapar delimitadores de cadeia de caracteres, campo e linha para arquivos CSV
- Usar formatos de data do SQL Server para arquivos CSV
- Especificar caracteres curinga e vários arquivos no caminho do local de armazenamento

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você já tem um pool de SQL dedicado. Se um pool de SQL dedicado não tiver sido criado, use o início rápido [Criar e conectar-se – portal](create-data-warehouse-portal.md).

## <a name="set-up-the-required-permissions"></a>Configurar as permissões necessárias

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Criar a tabela de destino

Neste exemplo, carregaremos dados do conjunto de dados de táxis de Nova York. Carregaremos uma tabela chamada Viagens que representa as corridas de táxi feitas em um ano. Execute o seguinte para criar a tabela:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Execute a instrução COPY

Execute a instrução COPY a seguir que carregará dados da conta de Armazenamento de Blobs do Azure para a tabela Viagens.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitore o carregamento

Verifique se seu carregamento está progredindo executando periodicamente a seguinte consulta:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Próximas etapas

- Para obter as melhores práticas sobre o carregamento de dados, confira [Melhores práticas para carregar dados](./guidance-for-loading-data.md).
- Para obter informações sobre como gerenciar os recursos para carregamentos de dados, confira [Isolamento da carga de trabalho](./quickstart-configure-workload-isolation-tsql.md).