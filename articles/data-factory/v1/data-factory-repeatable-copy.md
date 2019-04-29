---
title: Cópia repetida no Azure Data Factory | Microsoft Docs
description: Saiba como evitar duplicatas mesmo que uma fatia que copia dados seja executada mais de uma vez.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20c916275acd6bb79675c592711b17b277c9fc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605205"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Cópia repetida no Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada.  
 
> [!NOTE]
> Os exemplos a seguir são para o Azure SQL, mas são aplicáveis a qualquer repositório de dados com suporte a conjuntos de dados retangulares. Talvez seja necessário ajustar o **tipo** de origem e a propriedade **query** (por exemplo: query em vez de sqlReaderQuery) para o repositório de dados.   

Geralmente, ao ler repositórios relacionais, convém ler apenas os dados correspondentes a essa fatia. Uma maneira de fazer isso é usando as variáveis do sistema WindowStart e WindowEnd disponíveis no Azure Data Factory. Leia sobre as variáveis e funções no Azure Data Factory aqui, no artigo [Azure Data Factory – Funções e Variáveis do Sistema](data-factory-functions-variables.md). Exemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Essa consulta lê os dados do intervalo de duração da fatia (WindowStart -> WindowEnd) da tabela MyTable. A eventual reexecução dessa fatia também asseguraria sempre que os mesmos dados fossem lidos. 

Em outros casos, talvez seja conveniente ler a tabela inteira e definir sqlReaderQuery da seguinte maneira:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Gravação repetível para SqlSink
Ao copiar os dados de outros armazenamentos de dados para o **Azure SQL/SQL Server**, você precisa manter em mente a capacidade de repetição para evitar desfechos não intencionais. 

Ao copiar dados para o Banco de Dados do SQL Server/Azure SQL, a atividade de cópia acrescenta dados à tabela de coletor por padrão. Digamos que você estiver copiando dados de um arquivo CSV (valores separados por vírgulas) contendo dois registros na tabela a seguir em um Banco de Dados do SQL Server/Azure SQL. Quando uma fatia é executada, os dois registros são copiados para a tabela do SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que você encontrou erros no arquivo de origem e atualizou a quantidade de Down Tubes de dois para quatro. Se você executar novamente a fatia de dados para esse período manualmente, você encontrará dois novos registros anexados ao banco de dados SQL do Azure/banco de dados do SQL Server. Este exemplo pressupõe que nenhuma das colunas na tabela tem a restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar esse comportamento, você precisa especificar a semântica UPSERT, usando um dos dois mecanismos a seguir:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo 1: usando o sqlWriterCleanupScript
Você pode usar a propriedade **sqlWriterCleanupScript** para limpar os dados da tabela de coletor antes de inserir os dados quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Quando uma fatia é executada, o script de limpeza é executado pela primeira vez para excluir dados que correspondem à fatia da tabela SQL. A atividade de cópia, em seguida, insere dados na tabela SQL. Se a fatia é executada novamente, a quantidade é atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registro Flat Washer é removido do csv original. Nesse caso, executar novamente a fatia geraria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

A atividade de cópia executou o script de limpeza para excluir os dados correspondentes àquela fatia. Ela então leu a entrada do csv (que continha então apenas um registro) e a inseriu na tabela. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo 2: usando o sliceIdentifierColumnName
> [!IMPORTANT]
> No momento, não há suporte para sliceIdentifierColumnName no SQL Data Warehouse do Azure. 

O segundo mecanismo para atingir a capacidade de repetição é ter uma coluna dedicada (sliceIdentifierColumnName) na tabela de destino. Essa coluna pode ser usada pelo Azure Data Factory para garantir que a origem e destino permaneçam em sincronia. Essa abordagem funciona quando há flexibilidade para alteração ou definição do esquema de tabela SQL de destino. 

Essa coluna é usada pelo Azure Data Factory para fins de capacidade de repetição e, no processo, o Azure Data Factory não faz nenhuma alteração de esquema na tabela. Modo de usar essa abordagem:

1. Defina uma coluna do tipo **binário (32)** na tabela SQL de destino. Não deve haver nenhuma restrição nessa coluna. Vamos nomear esta coluna como AdfSliceIdentifier para este exemplo.


    Tabela de origem:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabela de destino: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Use-a na atividade de cópia da seguinte maneira:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

A Azure Data Factory popula essa coluna para garantir que a origem e destino permaneçam em sincronia. Os valores desta coluna não devem ser usados fora deste contexto. 

Semelhante ao mecanismo 1, os dados da fatia fornecida da tabela SQL de destino são limpos automaticamente pela Atividade de Cópia. A Atividade de Cópia então insere dados da origem na tabela de destino. 

## <a name="next-steps"></a>Próximas etapas
Examine os artigos sobre conector a seguir para obter exemplos de JSON completos: 

- [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
