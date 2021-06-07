---
title: Problemas de compatibilidade com aplicativos de terceiros e com o Azure Synapse Analytics
description: Descreve os problemas conhecidos que os aplicativos de terceiros podem encontrar com o Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98121372"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemas de compatibilidade com aplicativos de terceiros e com o Azure Synapse Analytics

Os aplicativos criados para SQL Server funcionarão diretamente com os pools SQL dedicados do Azure Synapse. Em alguns casos, no entanto, os recursos e os elementos de linguagem que normalmente são usados no SQL Server podem não estar disponíveis no Azure Synapse, ou podem se comportar de forma diferente.

Este artigo lista os problemas conhecidos que você pode ter ao usar aplicativos de terceiros com o Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Erro de tableau: "falha na tentativa de concluir uma transação. Nenhuma transação correspondente encontrada "

A partir do pool SQL dedicado do Azure Synapse versão 10.0.11038.0, algumas consultas do tableau que fazem chamadas de procedimento armazenado podem falhar com a seguinte mensagem de erro: "**[Microsoft] [ODBC Driver 17 for SQL Server] [SQL Server] 111214; Falha ao tentar concluir uma transação. Nenhuma transação correspondente encontrada.**"

### <a name="cause"></a>Causa

Isso é um problema no pool de SQL dedicado do Azure Synapse causado pela introdução de novos procedimentos armazenados do sistema que são chamados automaticamente pelos drivers ODBC e JDBC. Um desses procedimentos armazenados do sistema pode fazer com que transações abertas sejam anuladas se eles falharem na execução. Esse problema pode ocorrer dependendo da lógica do aplicativo cliente.

### <a name="solution"></a>Solução
Os clientes que veem esse problema específico ao usar o tableau conectado aos pools SQL dedicados do Azure Synapse devem definir FMTONLY como Sim na conexão SQL. Para o tableau desktop e o tableau Server, você deve usar um arquivo TDC (personalização de fonte de dados) do tableau para garantir que tableau passe esse parâmetro para o driver.  

> [!NOTE] 
> A Microsoft não oferece suporte a ferramentas de terceiros. Enquanto testamos que essa solução funciona com o tableau desktop 2020.3.2, você deve usar essa solução alternativa em sua própria capacidade.
>

* [Para saber como fazer personalizações globais com um arquivo TDC no tableau desktop, consulte a documentação da área de trabalho do tableau.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Para saber como fazer personalizações globais com um arquivo TDC no tableau Server, consulte usando um. Arquivo TDC com o servidor tableau.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

O exemplo a seguir mostra um arquivo TDC tableau que passa o parâmetro FMTONLY = YES para a cadeia de conexão SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Para obter mais detalhes sobre como usar arquivos TDC, entre em contato com o suporte do tableau. 

## <a name="see-also"></a>Veja também

* [Elementos de linguagem T-SQL para o pool SQL dedicado no Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Instruções T-SQL com suporte para o pool SQL dedicado no Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)