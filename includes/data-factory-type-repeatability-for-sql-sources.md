---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335346"
---
## <a name="repeatability-during-copy"></a>Capacidade de repetição durante a cópia
Ao copiar os dados de outros repositórios de dados para o Azure SQL/SQL Server, a pessoa precisa manter em mente a capacidade de repetição para evitar desfechos não intencionais. 

Ao copiar dados ao Azure SQL/Banco de dados do servidor do SQL, a atividade de cópia vai, por padrão, ACRESCENTAR o conjunto de dados à tabela de coletor por padrão. Por exemplo, ao copiar dados de uma fonte de arquivo CSV (dados com valores separados por vírgulas) contendo dois registros de banco de dados do SQL do Azure/SQL Server, a aparência da tabela é semelhante à seguinte:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Suponha que você encontrou erros no arquivo de origem e atualizou a quantidade de Down Tubes de 2 para 4 no arquivo de origem. Se você executar novamente a fatia de dados para esse período, você encontrará dois novos registros anexados ao banco de dados SQL do Azure/banco de dados do SQL Server. O exemplo abaixo pressupõe que nenhuma das colunas na tabela tenha a restrição de chave primária.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar isso, você precisará especificar a semântica UPSERT, aproveitando um dos 2 mecanismos descritos abaixo.

> [!NOTE]
> Uma fatia pode ser reexecutada automaticamente no Azure Data Factory, de acordo com a política de repetição especificada.
> 
> 

### <a name="mechanism-1"></a>Mecanismo 1
Você pode aproveitar a propriedade **sqlWriterCleanupScript** para executar primeiro a ação de limpeza quando uma fatia é executada. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

O script de limpeza deve ser executado primeiro durante a cópia para uma determinada fatia que excluiria os dados da tabela SQL correspondente a essa fatia. A atividade vai inserir, subsequentemente, os dados na tabela SQL. 

Se a fatia for executada novamente, você verá que a quantidade é atualizada conforme desejado.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Suponha que o registro Flat Washer é removido do csv original. Nesse caso, reexecutar novamente a fatia geraria o seguinte resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Não era necessário fazer nada de novo. A atividade de cópia executou o script de limpeza para excluir os dados correspondentes àquela fatia. Em seguida, lê a entrada do csv (que continha apenas 1 registro) e insere-a na tabela. 

### <a name="mechanism-2"></a>Mecanismo 2
> [!IMPORTANT]
> No momento, não há suporte para sliceIdentifierColumnName no SQL Data Warehouse do Azure. 

Outro mecanismo para atingir a capacidade de repetição é ter uma coluna dedicada (**sliceIdentifierColumnName**) na tabela de destino. Essa coluna pode ser usada pelo Azure Data Factory para garantir que a origem e destino permaneçam em sincronia. Essa abordagem funciona quando há flexibilidade para alteração ou definição do esquema de tabela SQL de destino. 

Essa coluna seria usada pelo Azure Data Factory para fins de capacidade de repetição e, no processo, o Azure Data Factory não fará alterações de esquema à tabela. Modo de usar essa abordagem:

1. Defina uma coluna do tipo binário (32) na tabela SQL de destino. Não deve haver nenhuma restrição nessa coluna. Vamos nomear esta coluna como 'ColumnForADFuseOnly' para este exemplo.
2. Use-a na atividade de cópia da seguinte maneira:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

A Azure Data Factory vai popular essa coluna para garantir que a origem e destino permaneçam em sincronia. Os valores desta coluna não devem ser usados fora deste contexto pelo usuário. 

Semelhante ao mecanismo 1, a atividade de cópia limpará primeiro automaticamente os dados para a fatia determinada da tabela SQL de destino e, em seguida, executará a atividade de cópia normalmente para inserir os dados da origem para o destino, nessa fatia. 

