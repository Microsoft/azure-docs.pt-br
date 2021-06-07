---
title: sys.external_streaming_jobs (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre como usar sys.external_streaming_jobs no Azure SQL Edge
keywords: sys.external_streaming_jobs, SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90900332"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

Retorna uma linha para cada trabalho de fluxo externo criado no escopo do banco de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|Nome do fluxo. É exclusiva no banco de dados.|
|**object_id**|**int**|número de identificação do objeto para o objeto de fluxo. É exclusiva no banco de dados.|
|**principal_id**|**int**|ID da entidade de segurança que possui o assembly|
|**schema_id**|**int**| ID do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação do objeto para o objeto-pai desse fluxo. Na implementação atual, esse valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para os objetos de fluxo, o tipo é sempre “EI”|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para os objetos de fluxo, o tipo é sempre “EXTERNAL_STREAMING_JOB”|
|**create_date**|**datetime**| A data em que o objeto foi criado.|
|**modify_date**|**datetime**| Na implementação atual, esse valor é igual a create_date para o objeto de fluxo |
|**is_ms_shipped**|**bit**| O objeto é criado por um componente interno.|  
|**is_published**|**bit**| O objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção do banco de dados SET ANSI_NULLS definida para ON|
|**instrução**|**varchar(max)**| Texto de consulta do stream analytics para o serviço de fluxo. Para obter mais informações, consulte [sp_create_streaming_job](overview.md) |
|**status**|**int**| O estado atual do trabalho de fluxo. Os valores possíveis são <br /><br /> **Criado** = 0. O trabalho de fluxo foi criado, mas não iniciou ainda. <br /><br /> **Iniciando** = 1. O trabalho de fluxo está na fase inicial. <br /><br /> **Falhou** = 6. O trabalho de streaming falhou. Em geral indica um erro fatal durante o processamento. <br /><br /> **Parado** = 4. O trabalho de fluxo foi interrompido. <br /><br /> **Ocioso** = 7. O trabalho de fluxo está em execução, porém não há nenhuma entrada para processar. <br /><br /> **Processando** = 8. O trabalho de streaming está em execução e processando as entradas. Indica um estado de integridade para o trabalho de fluxo. <br /><br /> **Degradado** = 9. O trabalho de streaming está em execução, porém, houve erros de serialização/desserialização de entrada/saída não fatal durante o processamento da entrada. O trabalho de entrada continuará em execução, mas removerá as entradas que encontrar erros.|

## <a name="permissions"></a>Permissões

A visibilidade dos metadados em exibições do catálogo está limitada aos protegíveis que pertencem a um usuário ou para os quais o usuário recebeu permissão. Para obter mais informações, consulte [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Confira também

- [Exibições do Catálogo de Fluxo T-SQL](overview.md)
- [Exibições do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Exibições do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)

