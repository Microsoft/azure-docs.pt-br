---
title: Trabalhando com dados JSON
description: O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada permitem que você analise, consulte e formate a data em notação JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92782790"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Introdução aos recursos JSON no banco de dados SQL do Azure e Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure permitem que você analise e consulte as consultas representadas no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exporte os dados relacionais como texto JSON. Os seguintes cenários JSON estão disponíveis:

- [Formatação de dados relacional no formato JSON](#formatting-relational-data-in-json-format) usando `FOR JSON` cláusula.
- [Trabalhando com dados JSON](#working-with-json-data)
- [Consultando dados JSON](#querying-json-data) usando funções escalares JSON.
- [Transformando JSON em formato tabular](#transforming-json-into-tabular-format) usando a função `OPENJSON`.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacional no formato JSON

Se você tiver um serviço Web que usa dados da camada de banco de dados e fornece uma resposta no formato JSON ou bibliotecas ou estruturas JavaScript do lado do cliente que aceitam dados formatados como JSON, você pode formatar o conteúdo do banco de dados como JSON diretamente em uma consulta SQL. Você não precisa mais escrever o código do aplicativo que formata os resultados do banco de dados SQL do Azure ou do Azure SQL Instância Gerenciada como JSON, ou incluir alguma biblioteca de serialização JSON para converter os resultados da consulta de tabela e, em seguida, serializar objetos no formato JSON. Em vez disso, você pode usar a cláusula FOR JSON para formatar os resultados da consulta SQL como JSON e usá-los diretamente em seu aplicativo.

No exemplo a seguir, as linhas da tabela Sales.Customer são formatadas como JSON usando a cláusula FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula FOR JSON PATH formata os resultados da consulta como texto JSON. Nomes de coluna são usados como chaves, enquanto os valores de célula são gerados como valores JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON em que cada linha é formatada como um objeto JSON separado.

PATH indica que você pode personalizar o formato de saída do seu resultado JSON usando a notação de pontos de aliases de coluna. A consulta a seguir altera o nome da chave "CustomerName" no formato JSON de saída e coloca os números de telefone e fax no subobjeto "Contact":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

A saída dessa consulta tem o seguinte aspecto:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo é retornado um único objeto JSON em vez de uma matriz, especificando a opção [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option). Você pode usar essa opção se souber que está retornando um único objeto como resultado da consulta.

O principal valor da cláusula FOR JSON é que ela permite retornar dados hierárquicos complexos de seu banco de dados formatado como matrizes ou objetos JSON aninhados. O exemplo a seguir mostra como incluir as linhas da tabela `Orders` que pertencem ao `Customer` como uma matriz aninhada de `Orders`:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Em vez de enviar consultas separadas para obter dados do Cliente e, em seguida, para obter uma lista de Pedidos relacionados, você pode obter todos os dados necessários com uma única consulta, conforme mostrado na seguinte saída de exemplo:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Trabalhando com dados JSON

Se você não tiver dados estritamente estruturados, se tiver subobjetos, matrizes ou dados hierárquicos complexos ou se suas estruturas de dados evoluem ao longo do tempo, o formato JSON pode ajudá-lo a representar qualquer estrutura de dados complexos.

JSON é um formato textual que pode ser usado como qualquer outro tipo de cadeia de caracteres no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada. Você pode enviar ou armazenar dados JSON como um NVARCHAR padrão:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Os dados JSON usados neste exemplo são representados usando o tipo NVARCHAR(MAX). JSON pode ser inserido nessa tabela ou fornecido como um argumento de procedimento armazenado usando sintaxe Transact-SQL padrão, conforme mostrado no exemplo a seguir:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer linguagem ou biblioteca do lado do cliente que funcione com dados de cadeia de caracteres no Azure SQL Database e no Azure SQL Instância Gerenciada também funcionará com dados JSON. JSON pode ser armazenado em qualquer tabela que suporta o tipo NVARCHAR, como uma tabela com otimização de memória ou uma tabela com versão do sistema. JSON não apresenta qualquer restrição no código do lado do cliente ou na camada de banco de dados.

## <a name="querying-json-data"></a>Consultando dados JSON

Se você tiver dados formatados como JSON armazenados em tabelas SQL do Azure, as funções JSON permitem que você use esses dados em qualquer consulta SQL.

As funções JSON que estão disponíveis no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada permitem tratar dados formatados como JSON como qualquer outro tipo de dado SQL. Você pode facilmente extrair valores de texto JSON e usar dados JSON em qualquer consulta:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor de texto JSON armazenado na coluna de Dados. Essa função usa um caminho de JavaScript para fazer referência a um valor em texto JSON para extração. O valor extraído pode ser usado em qualquer parte da consulta SQL.

A função JSON_QUERY é semelhante à JSON_VALUE. Ao contrário de JSON_VALUE, essa função extrai subobjetos complexos, como matrizes ou objetos que são colocados em texto JSON.

A função JSON_MODIFY permite especificar o caminho do valor em texto JSON que deve ser atualizado, bem como um novo valor que substituirá o antigo. Dessa forma você pode atualizar facilmente o texto JSON sem ter que reanalisar toda a estrutura.

Como JSON é armazenado em um texto padrão, não há nenhuma garantia de que os valores armazenados em colunas de texto estão formatados corretamente. Você pode verificar se o texto armazenado na coluna do JSON está formatado corretamente usando restrições de verificação padrão do Banco de Dados SQL do Azure e a função ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada estiver formatado corretamente como JSON, a função ISJSON retornará o valor 1. Em cada inserção ou atualização de coluna JSON, essa restrição irá verificar se o novo valor de texto não é um JSON malformado.

## <a name="transforming-json-into-tabular-format"></a>Transformando o JSON em formato tabular

O banco de dados SQL do Azure e o SQL Instância Gerenciada do Azure também permitem transformar coleções JSON em formato tabular e carregar ou consultar dado JSON.

OPENJSON é uma função com valor de tabela que analisa texto JSON, localiza uma matriz de objetos JSON, itera através dos elementos da matriz e retorna uma linha no resultado de saída para cada elemento da matriz.

![JSON tabular](./media/json-features/image_2.png)

No exemplo acima, podemos especificar onde localizar a matriz JSON que deve ser aberta (no caminho $.Orders), quais colunas devem ser retornadas como resultado e onde encontrar os valores JSON que serão retornados como células.

Podemos transformar uma matriz JSON na variável @orders em um conjunto de linhas, analisar o conjunto de resultados ou inserir linhas em uma tabela padrão:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

A coleção de pedidos formatada como uma matriz JSON e fornecida como um parâmetro para o procedimento armazenado pode ser analisada e inserida na tabela Pedidos.

## <a name="next-steps"></a>Próximas etapas

Para saber como integrar o JSON em seu aplicativo, consulte estes recursos:

Para saber mais sobre vários cenários para integrar o JSON em seu aplicativo, confira as demonstrações neste [vídeo do Canal 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontre um cenário que corresponda ao seu caso de uso nas [postagens no Blog do JSON](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases).