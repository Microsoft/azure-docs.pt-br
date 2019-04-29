---
title: Máscara de dados dinâmicos do Banco de Dados SQL do Azure | Microsoft Docs
description: A Máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2019
ms.openlocfilehash: 2be1f7bb6417834196da03b7cba22387744f8c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075525"
---
# <a name="sql-database-dynamic-data-masking"></a>Máscara de dados dinâmicos do Banco de Dados SQL

A máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. 

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um representante de serviço em um centro de chamada pode identificar os chamadores por vários dígitos do seu número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o representante de serviço. Uma regra de mascaramento pode ser definida para mascarar tudo menos os quatro últimos dígitos de qualquer número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger os dados de informações de identificação pessoal (PII), de forma que um desenvolvedor possa consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## <a name="sql-database-dynamic-data-masking-basics"></a>Noções básicas sobre a máscara de dados dinâmicos do Banco de Dados SQL

É possível configurar uma política de máscara de dados dinâmicos no Portal do Azure selecionando a operação máscara de dados dinâmicos na folha de configuração do Banco de Dados SQL ou na folha de configurações.

### <a name="dynamic-data-masking-permissions"></a>Permissões de mascaramento de dados dinâmico

A Máscara de Dados Dinâmicos pode ser configurada por meio do administrador do Banco de Dados SQL do Azure, do administrador do servidor ou de funções do [Gerenciador de Segurança do SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Política de mascaramento de dados dinâmico

* **Usuários SQL excluídos do mascaramento** – um conjunto de usuários do SQL ou de identidades do AAD que obtém dados não mascarados nos resultados da consulta SQL. Usuários com privilégios de administrador sempre são excluídos do mascaramento e veem os dados originais sem qualquer máscara.
* **Regras de mascaramento** – um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome da coluna.
* **Funções de mascaramento** : um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Padrão** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos Xs se o tamanho do campo tiver menos de quatro caracteres para os tipos de dados da cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Use um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numérico, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para os tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para SQL variant, o valor padrão do tipo atual é usado.<br/>• Para XML o documento <masked/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**Método de mascaramento que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo da cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada. <br/>prefixo[preenchimento]sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O mecanismo de recomendações DDM sinaliza determinados campos do banco de dados como potencialmente confidenciais, que podem ser bons candidatos para mascaramento. Na folha Mascaramento de Dados Dinâmicos no portal, você verá as colunas recomendadas para seu banco de dados. Basta clicar em **Adicionar Máscara** para uma ou mais colunas e em **Salvar** para aplicar uma máscara a esses campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar dados dinâmicos de mascaramento do banco de dados usando cmdlets do PowerShell

Confira [Cmdlets do Banco de Dados SQL do Azure](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmica para o banco de dados usando a API REST

Confira [Operações para o Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/).
