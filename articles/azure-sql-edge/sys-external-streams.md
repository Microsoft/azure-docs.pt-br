---
title: sys.external_streams (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre como usar sys.external_streams no Azure SQL Edge
keywords: sys.external_streams, SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900308"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

Retorna uma linha para cada objeto de fluxo externo criado dentro do escopo do banco de dados.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|Nome do fluxo. É exclusiva no banco de dados.|
|**object_id**|**int**|número de identificação do objeto para o objeto de fluxo. É exclusiva no banco de dados.|
|**principal_id**|**int**|ID da entidade de segurança que possui o assembly|
|**schema_id**|**int**| ID do esquema que contém o objeto.|
|**parent_object_id**|**id**| número de identificação do objeto para o objeto-pai desse fluxo. Na implementação atual, esse valor é sempre nulo|
|**tipo**|**char(2)**|Tipo de objeto. Para os objetos de fluxo, o tipo é sempre “ES”|
|**type_desc**|**nvarchar(60)**| Descrição do tipo de objeto. Para os objetos de fluxo, o tipo é sempre “EXTERNAL_STREAM”|
|**create_date**|**datetime**| A data em que o objeto foi criado.|
|**modify_date**|**datetime**| A data em que o objeto foi modificado pela última vez com uma instrução ALTER.|
|**is_ms_shipped**|**bit**| O objeto é criado por um componente interno.|  
|**is_published**|**bit**|O objeto é publicado.|  
|**is_schema_published**|**bit**|Apenas o esquema do objeto é publicado.|
|**max_column_id_used**|**bit**| Essa coluna é usada para fins internos e será removida no futuro|  
|**uses_ansi_nulls**|**bit**| O objeto de fluxo foi criado com a opção do banco de dados SET ANSI_NULLS definida para ON|
|**data_source_id**|**int**| A ID do objeto para a fonte de dados externa representada pelo objeto de fluxo |  
|**file_format_id**|**int**| A ID do objeto para o formato de arquivo externo usado pelo objeto de fluxo. O formato de arquivo externo é requerido para especificar o layout real dos dados referenciados por um fluxo externo| 
|**local**|**varchar(max)**| O destino para o objeto de fluxo externo. Para obter mais informações, confira [Criar Fluxo Externo](overview.md) |
|**input_option**|**varchar(max)**| As opções de entrada usadas durante a criação do fluxo externo. Para obter mais informações, confira [Criar Fluxo Externo](overview.md) |
|**output_option**|**varchar(max)**| As opções de saída usadas durante a criação do fluxo externo. Para obter mais informações, confira [Criar Fluxo Externo](overview.md) | 

## <a name="permissions"></a>Permissões

A visibilidade dos metadados em exibições do catálogo está limitada aos protegíveis que pertencem a um usuário ou para os quais o usuário recebeu permissão. Para obter mais informações, consulte [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Confira também

- [Exibições do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Exibições do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)
