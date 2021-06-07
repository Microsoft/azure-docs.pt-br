---
title: Mascaramento de dados dinâmicos
description: A máscara de dados dinâmicos limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios do banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/25/2021
tags: azure-synpase
ms.openlocfilehash: b10b00e724324779eb753bfefccce77a5eb2a39d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918070"
---
# <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics dão suporte a mascaramento de data dinâmicos. O mascaramento de dados dinâmicos limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. 

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designados, sendo que os dados no banco de dados não são alterados.

Por exemplo, um representante de serviço em um Call Center pode identificar um chamador confirmando vários caracteres de seu endereço de email, mas o endereço de email completo não deve ser revelado para o representante de serviço. Uma regra de mascaramento pode ser definida para mascarar todo o endereço de email no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger dados pessoais, para que um desenvolvedor possa consultar ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## <a name="dynamic-data-masking-basics"></a>Aspectos básicos do mascaramento de dados dinâmicos

Você configura uma política de mascaramento de dados dinâmicos na portal do Azure selecionando a folha **máscara de dados dinâmicos** em **segurança** no painel configuração do SQL Database. Este recurso não pode ser definido usando o portal para SQL Instância Gerenciada (use o PowerShell ou a API REST). Para obter mais informações, consulte [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-policy"></a>Política de mascaramento de dados dinâmico

* **Usuários do SQL excluídos do mascaramento** – um conjunto de usuários do SQL ou identidades do Azure AD que obtém dados não mascarados nos resultados da consulta SQL. Usuários com privilégios de administrador sempre são excluídos do mascaramento e veem os dados originais sem qualquer máscara.
* **Regras de mascaramento** – um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome da coluna.
* **Funções de mascaramento** : um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Default** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos Xs se o tamanho do campo tiver menos de quatro caracteres para os tipos de dados da cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Use um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numérico, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para os tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para SQL variant, o valor padrão do tipo atual é usado.<br/>• Para XML o documento \<masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**Método de mascaramento que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo da cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Captura de tela que mostra o método de mascaramento para gerar um número aleatório.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada. <br/>prefixo[preenchimento]sufixo<br/><br/>![Painel de navegação](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O mecanismo de recomendações DDM sinaliza determinados campos do banco de dados como potencialmente confidenciais, que podem ser bons candidatos para mascaramento. Na folha Mascaramento de Dados Dinâmicos no portal, você verá as colunas recomendadas para seu banco de dados. Basta clicar em **Adicionar Máscara** para uma ou mais colunas e em **Salvar** para aplicar uma máscara a esses campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar a máscara de dados dinâmica para o banco de dados usando cmdlets do PowerShell

### <a name="data-masking-policies"></a>Políticas de mascaramento de dados

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regras de mascaramento de dados

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Configurar o mascaramento de dados dinâmicos para seu banco usando a API REST

Você pode usar a API REST para gerenciar programaticamente a política e as regras de mascaramento de dados. A API REST publicada dá suporte às seguintes operações:

### <a name="data-masking-policies"></a>Políticas de mascaramento de dados

- [Criar ou atualizar](/rest/api/sql/datamaskingpolicies/createorupdate): cria ou atualiza uma política de mascaramento de dados de banco de dado.
- [Obter](/rest/api/sql/datamaskingpolicies/get): Obtém uma política de mascaramento de dados de banco de dados. 

### <a name="data-masking-rules"></a>Regras de mascaramento de dados

- [Criar ou Atualizar](/rest/api/sql/datamaskingrules/createorupdate): Cria ou atualiza uma regra de mascaramento de dados de banco de dados.
- [Listar por banco de dados](/rest/api/sql/datamaskingrules/listbydatabase): Obtém uma lista de regras de mascaramento de dados do banco de dados.

## <a name="permissions"></a>Permissões

A máscara de dados dinâmicos pode ser configurada pela função de administrador, administrador do servidor ou controle de acesso baseado em função (RBAC) do [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) .

## <a name="next-steps"></a>Próximas etapas

[Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking)
