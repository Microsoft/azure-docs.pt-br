---
title: Consultas SQL para a API do DocumentDB do Azure Cosmos DB | Microsoft Docs
description: Saiba mais sobre a sintaxe SQL, os conceitos sobre banco de dados e as consultas SQL do Azure Cosmos DB. O SQL pode ser usado como uma linguagem de consulta JSON no Azure Cosmos DB.
keywords: "sintaxe sql, consulta sql, consultas sql, linguagem de consulta json, conceitos de banco de dados e consultas sql, funções agregadas"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: arramac
ms.openlocfilehash: 862594bcbd6df8a2c62a12340ceb8096fb6bd691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-queries-for-azure-cosmos-db-documentdb-api"></a>Consultas SQL para a API do DocumentDB do Azure Cosmos DB
O Microsoft Azure Cosmos DB dá suporte à consulta de documentos usando a linguagem SQL como uma linguagem de consulta JSON. O Cosmos DB é verdadeiramente sem esquemas. Em virtude de seu comprometimento com o modelo de dados JSON diretamente dentro do mecanismo do banco de dados, ele fornece a indexação automática de documentos JSON sem a necessidade de esquemas explícitos ou da criação de índices secundários. 

Ao criar a linguagem de consulta para o Cosmos DB, temos dois objetivos em mente:

* Em vez de inventar uma nova linguagem de consulta JSON, queremos oferecer suporte ao SQL. A SQL é uma das linguagens de consulta mais conhecidas e populares. O SQL do Cosmos DB fornece um modelo de programação formal para consultas avançadas em documentos JSON.
* Como um banco de dados de documentos JSON capaz de executar o JavaScript diretamente no mecanismo do banco de dados, queríamos usar o modelo de programação do JavaScript como os alicerces da nossa linguagem de consulta. O SQL da API do DocumentDB é baseado no sistema de tipos, na avaliação de expressão e na invocação de função do JavaScript. Isso, por sua vez, oferece um modelo de programação natural para projeções relacionais, navegação hierárquica em documentos JSON, autojunções, consultas espaciais e invocação de UDFs (funções definidas pelo usuário) gravadas inteiramente em JavaScript, entre outros recursos. 

Nós acreditamos que esses recursos sejam fundamentais para reduzir o atrito entre o aplicativo e o banco de dados e cruciais para a produtividade do desenvolvedor.

Recomendamos que você comece assistindo ao vídeo a seguir, em que Aravind Ramachandran mostra as funcionalidades de consulta do Cosmos DB e visitando nosso [Espaço de Consulta](http://www.documentdb.com/sql/demo), em que você pode experimentar o Cosmos DB e executar consultas SQL em nosso conjunto de dados.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/DataExposedQueryingDocumentDB/player]
> 
> 

Em seguida, retorne a este artigo, em que começamos com um tutorial de consulta SQL que apresenta alguns documentos JSON e comandos SQL simples.

## <a id="GettingStarted"></a>Introdução aos comandos SQL no Cosmos DB
Para ver o SQL do Cosmos DB em ação, vamos começar com alguns documentos JSON simples e analisar algumas consultas simples neles. Considere esses dois documentos JSON sobre duas famílias. Com o Cosmos DB, não precisamos criar nenhum esquema nem índice secundário explicitamente. Basta inserir os documentos JSON em uma coleção do Cosmos DB e, em seguida, realizar a consulta. Aqui, temos um documento JSON simples relacionado à família Andersen, os pais, os filhos (e seus animais de estimação), endereço e informações de registro. O documento tem cadeias de caracteres, números, boolianos, matrizes e propriedades aninhadas. 

**Documento**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Aqui está um segundo documento, com uma pequena diferença: `givenName` e `familyName` são usados em vez de `firstName` e `lastName`.

**Documento**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Agora, vamos tentar realizar algumas consultas nesses dados para entender alguns dos principais aspectos do SQL da API do DocumentDB. Por exemplo, a consulta a seguir retorna documentos cujo campo de ID corresponde a `AndersenFamily`. Por se tratar de um `SELECT *`, a saída da consulta será todo o documento JSON:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Agora, considere um caso em que precisamos reformatar a saída JSON para um formato diferente. Esta consulta projeta um novo documento JSON com dois campos selecionados, Nome e Cidade, com a cidade e o estado tendo o mesmo nome. Neste caso, "NY, NY" é correspondente.

**Consulta**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


A próxima consulta retorna todos os nomes dos filhos na família cuja identificação corresponde ao `WakefieldFamily` solicitado pela cidade de residência.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Gostaríamos de chamar atenção para alguns aspectos de destaque da linguagem de consulta do Cosmos DB nos exemplos que vimos até agora:  

* Como o SQL da API do DocumentDB trabalha com valores JSON, ele lida com entidades com formato de árvore em vez de linhas e colunas. A linguagem, portanto, possibilita a referência a nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, de forma semelhante à SQL relacional relativa à referência bipartida de `<table>.<column>`.   
* A linguagem de consulta estruturada trabalha com dados com menos esquema. Portanto, o sistema de tipos precisa estar vinculado dinamicamente. A mesma expressão pode obter diferentes tipos em diferentes documentos. O resultado de uma consulta é um valor JSON válido, mas não há garantia de que seja de um esquema fixo.  
* O Cosmos DB dá suporte apenas a documentos JSON estritos. Isto significa que as expressões e sistema de tipos são restritos para lidar somente com tipos JSON. Consulte a [especificação JSON](http://www.json.org/) para obter mais detalhes.  
* Uma coleção do Cosmos DB é um contêiner de documentos JSON sem esquemas. As relações nas entidades de dados dentro e entre documentos em uma coleção são capturadas implicitamente pela contenção e não pelas relações chave primária e chave estrangeira. Este é um importante aspecto que vale a pena destacar em virtude das junções intradocumentos abordadas mais adiante neste artigo.

## <a id="Indexing"></a> Indexação do Cosmos DB
Antes de passarmos à sintaxe do SQL da API do DocumentDB, vale a pena explorar o design de indexação no Cosmos DB. 

O objetivo de índices de bancos de dados é atender a consultas em suas diversas formas com um consumo mínimo de recursos (como CPU, entrada/saída), oferecendo alta produtividade e baixa latência. Frequentemente, a escolha do índice correto para consultar um banco de dados requer muito planejamento e experimentação. Esta abordagem representa um desafio para bancos de dados sem esquemas, nos quais os dados não seguem um esquema rígido e evoluem rapidamente. 

Portanto, quando criamos o subsistema de indexação do Cosmos DB, definimos os seguintes objetivos:

* Indexar documentos sem precisar de um esquema: o subsistema de indexação não requer nenhuma informação de esquema e não faz suposições sobre o esquema dos documentos. 
* Suporte para pesquisas hierárquicas e relacionais avançadas e eficientes: o índice dá suporte à linguagem de pesquisa do Cosmos DB de maneira eficiente, incluindo suporte para projeções hierárquicas e relacionais.
* Suporte para consultas consistentes diante do grande volume de gravações: para possibilitar cargas de trabalho com alta produtividade de gravação com consultas consistentes, o índice é atualizado gradativamente, eficientemente e online, em face de um volume contínuo de gravações. A atualização consistente do índice é crucial para atender às consultas no nível de consistência em que o usuário configurou o sistema.
* Suporte a multilocatário: dado o modelo baseado em reserva para a governança de recurso entre os locatários, as atualizações do índice são realizadas dentro do orçamento dos recursos do sistema (CPU, memória e operações de entrada/saída por segundo) alocados por réplica. 
* Eficiência no armazenamento: para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é vinculada e previsível. Isto é fundamental porque o Cosmos DB permite que o desenvolvedor faça compensações baseadas em custo entre a sobrecarga do índice em relação ao desempenho da consulta.  

Consulte as [amostras do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) no MSDN para obter amostras que exibem como configurar a política de indexação de uma coleção. Agora, vejamos os detalhes da sintaxe SQL do Azure Cosmos DB.

## <a id="Basics"></a>Noções básicas de uma consulta SQL do Azure Cosmos DB
Toda consulta consiste em uma cláusula SELECT e cláusulas FROM e WHERE opcionais de acordo com os padrões ANSI-SQL. Normalmente, para cada consulta, a fonte da cláusula FROM é enumerada. Então, o filtro da cláusula WHERE é aplicado para recuperar um subconjunto de documentos JSON. Por fim, a cláusula SELECT é usada para projetar os valores JSON solicitados na lista selecionada.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>Cláusula FROM
A cláusula `FROM <from_specification>` é opcional, a menos que a fonte seja filtrada ou projetada mais adiante na consulta. O objetivo desta cláusula é especificar a fonte de dados na qual a consulta deve operar. Normalmente, a coleção inteira é a fonte, mas é possível também especificar um subconjunto da coleção. 

Uma consulta como `SELECT * FROM Families` indica que a coleção Families inteira é a fonte a ser enumerada. Um identificador especial ROOT pode ser usado para representar a coleção em vez de usar o nome da coleção. A lista a seguir contém as regras que são impostas por uma consulta:

* A coleção pode ter um alias como `SELECT f.id FROM Families AS f`, ou simplesmente `SELECT f.id FROM Families f`. Aqui, `f` é o equivalente de `Families`. `AS` é uma palavra-chave opcional que serve como alias para o identificador.
* Após receber um alias, a fonte original não pode ser associada. Por exemplo: `SELECT Families.id FROM Families f` é sintaticamente inválido, pois o identificador "Families" não pode mais ser resolvido.
* Todas as propriedades que precisam ser referidas devem ser completamente qualificadas. Na falta de aderência a um esquema rígido, esta regra é aplicada para evitar associações ambíguas. Portanto, `SELECT id FROM Families f` é sintaticamente inválido, pois a propriedade `id` não está vinculada.

### <a name="subdocuments"></a>Subdocumentos
A fonte também pode ser reduzida a um subconjunto menor. Por exemplo, para enumerar somente uma subárvore de cada documento, a sub-raiz pode então se tornar a fonte, como no exemplo a seguir:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Embora o exemplo acima tenha usado uma matriz como origem, um objeto também pode ser usado como origem, o que é mostrado no exemplo a seguir: qualquer valor JSON válido (não indefinido) que pode ser encontrado na origem é considerado para inclusão no resultado da consulta. Se algumas famílias não tiverem um valor de `address.state`, elas serão excluídas do resultado da consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Cláusula WHERE
A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Ela especifica as condições que os documentos JSON fornecidos pela fonte devem satisfazer para serem incluídas como parte dos resultados. Qualquer documento JSON deve avaliar as condições especificadas como “verdadeiras” para serem consideradas para os resultados. A cláusula WHERE é usada pela camada do índice para determinar o subconjunto absolutamente menor de documentos fonte que pode fazer parte do resultado. 

A consulta a seguir solicita documentos que contêm uma propriedade de nome cujo valor é `AndersenFamily`. Qualquer outro documento que não tiver uma propriedade de nome ou cujo valor não corresponder a `AndersenFamily` será excluído. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


O exemplo anterior mostrou uma consulta de igualdade simples. O SQL da API do DocumentDB também dá suporte a diversas expressões escalares. As expressões mais usadas são as binárias e unárias. Referências de propriedade do objeto JSON fonte também são expressões válidas. 

Os operadores binários a seguir têm suporte atualmente em consultas como as exemplificadas:  

<table>
<tr>
<td>Aritmético</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit a bit</td>    
<td>|, &, ^, <<, >>, >>> (deslocamento à direita com preenchimento com zero)</td>
</tr>
<tr>
<td>Lógico</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>Comparação</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadeia de caracteres</td>    
<td>|| (concatenado)</td>
</tr>
</table>  


Vejamos algumas consultas que usam valores binários.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Os operadores unários +,-, ~ e NOT também têm suporte e podem ser usados dentro de consultas, como mostrado no exemplo a seguir:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Além de operadores binários e unários, as referências de propriedade também são permitidas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o documento JSON que contém a propriedade `isRegistered`, em que o valor da propriedade é igual ao valor JSON `true`. Todos os outros valores (false, null, Indefinido, `<number>`, `<string>`, `<object>`, `<array>` etc.) levam ao documento de origem que está sendo excluído do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de igualdade e de comparação
A tabela a seguir mostra o resultado de comparações de igualdade no SQL da API do DocumentDB entre dois tipos JSON quaisquer.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Indefinido</strong>
         </td>
         <td valign="top">
            <strong>Nulo</strong>
         </td>
         <td valign="top">
            <strong>Booliano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadeia de caracteres</strong>
         </td>
         <td valign="top">
            <strong>Objeto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Indefinido<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nulo<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booliano<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadeia de caracteres<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objeto<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Para outros operadores de comparação, como >, >=, !=, < e <=, aplicam-se as seguintes regras:   

* Comparação entre resultados de tipos em Indefinido.
* Comparação entre resultados de dois objetos ou duas matrizes em Indefinido.   

Se o resultado da expressão escalar do filtro for Indefinido, o documento correspondente não seria incluído no resultado, uma ver que Indefinido não corresponde logicamente a “verdadeiro”.

### <a name="between-keyword"></a>Palavra-chave BETWEEN
Você também pode usar a palavra-chave BETWEEN para expressar consultas a intervalos de valores, como na ANSI SQL. BETWEEN pode ser usado em cadeias de caracteres ou números.

Por exemplo, esta consulta retorna todos os documentos de família nos quais a série do primeiro filho vai de 1 a 5 (incluindo ambos). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Diferente da ANSI-SQL, você também pode usar a cláusula BETWEEN na cláusula FROM, como no exemplo a seguir.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para que os tempos de execução das consultas sejam menores, lembre-se de criar uma política de indexação que use um índice do tipo intervalo para quaisquer caminhos/propriedades numéricas que sejam filtradas na cláusula BETWEEN. 

A principal diferença entre usar BETWEEN na API do DocumentDB e no ANSI SQL é que você pode expressar consultas de intervalo em propriedades de tipos mistos. Por exemplo, você pode designar “série” como um número (5) em alguns documentos e como cadeias de caracteres em outros (“grade4”). Nesses casos, como no JavaScript, uma comparação entre dois tipos diferentes traz um resultado "indefinido" e o documento é ignorado.

### <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)
Operadores lógicos funcionam em valores boolianos. As tabelas de verdade lógica desses operadores são mostradas nas tabelas a seguir.

| OU | Verdadeiro | Falso | Indefinido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Verdadeiro |Verdadeiro |
| Falso |Verdadeiro |Falso |Indefinido |
| Indefinido |Verdadeiro |Indefinido |Indefinido |

| E | Verdadeiro | Falso | Indefinido |
| --- | --- | --- | --- |
| Verdadeiro |Verdadeiro |Falso |Indefinido |
| Falso |Falso |Falso |Falso |
| Indefinido |Indefinido |Falso |Indefinido |

| NÃO |  |
| --- | --- |
| Verdadeiro |Falso |
| Falso |Verdadeiro |
| Indefinido |Indefinido |

### <a name="in-keyword"></a>Palavra-chave IN
A palavra-chave IN pode ser usada para verificar se um valor especificado corresponde a qualquer dos valores em uma lista. Por exemplo, esta consulta retorna todos os documentos de família cuja ID é "WakefieldFamily" ou então "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este exemplo retorna todos os documentos cujo estado é qualquer um dos valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operadores Ternário (?) e de União (??)
Os operadores Ternário e de União podem ser usados para compilar expressões condicionais, de modo semelhante a linguagens de programação populares como C# e JavaScript. 

O operador Ternário (?) pode ser muito útil para construir novas propriedades JSON com muita rapidez. Por exemplo, agora você pode criar consultas para classificar os níveis de classe em um formato legível, como Iniciante/Intermediário/Avançado, como é mostrado abaixo.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Você também pode aninhar as chamadas no operador, como na consulta a seguir.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Assim como outros operadores de consulta, se as propriedades mencionadas na expressão condicional estiverem faltando em algum documento ou se os tipos comparados forem diferentes, esses documentos serão excluídos dos resultados da consulta.

O operador de União (??) pode ser usado para verificar de modo eficaz a presença de uma propriedade (ou seja, é definido) em um documento. Isso é útil ao consultar dados semiestruturados ou dados de tipos mistos. Por exemplo, a consulta retorna o "lastName" se estiver presente ou o "surname" se não estiver.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Acessador de propriedade entre aspas
Você também pode acessar propriedades usando o operador de propriedade entre aspas `[]`. Por exemplo: `SELECT c.grade` and `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil quando você precisa substituir uma propriedade que contém espaços, caracteres especiais ou compartilha o mesmo nome que uma palavra-chave ou palavra reservada SQL.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Cláusula SELECT
A cláusula SELECT (**`SELECT <select_list>`**) é obrigatória e especifica quais valores são recuperados da consulta, exatamente como ocorre em ANSI-SQL. O subconjunto que foi filtrado sobre os documentos fonte é passado à fase de projeção, em que os valores JSON especificados são recuperados e um novo objeto JSON é construído, para cada entrada passada a ele. 

O exemplo a seguir mostra uma consulta SELECT típica. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriedades aninhadas
No exemplo a seguir, estamos projetando duas propriedades aninhadas, `f.address.state` and `f.address.city`.

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


A projeção tem suporte também para expressões JSON, conforme mostrado no exemplo a seguir:

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Vejamos a função de `$1` aqui. A cláusula `SELECT` precisa criar um objeto JSON e, como nenhuma chave é fornecida, usamos nomes de variáveis de argumento implícito iniciadas por `$1`. Por exemplo, esta consulta retorna duas variáveis de argumento implícito, rotuladas como `$1` and `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Atribuição de alias
Agora, vamos estender o exemplo acima com a atribuição explícita de alias aos valores. AS é a palavra-chave usada para a atribuição de alias. É opcional, conforme mostrado ao projetar o segundo valor como `NameInfo`. 

Caso uma consulta tenha duas propriedades com o mesmo nome, a atribuição de alias deve ser usada para renomear uma ou as duas propriedades para que elas não sejam ambíguas no resultado projetado.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressões escalares
Além de referências de propriedade, a cláusula SELECT dá suporte também a expressões escalares como constantes, expressões aritméticas, expressões lógicas etc. Por exemplo, vejamos uma consulta simples do tipo "Olá mundo".

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


Este é um exemplo mais complexo que usa uma expressão escalar.

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3    

**Resultados**

    [{
      "$1": 1.33333
    }]


No exemplo a seguir, o resultado da expressão escalar é um booliano.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Criação de objeto e de matriz
Outro recurso fundamental do SQL da API do DocumentDB é a criação de matriz/objeto. Observe que, no exemplo anterior, criamos um novo objeto JSON. De modo semelhante, é possível construir matrizes, como mostram os exemplos a seguir:

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>Palavra-chave VALUE
A palavra-chave **VALUE** é uma forma de retornar valores JSON. Por exemplo: a consulta mostrada abaixo retorna o `"Hello World"` escalar, em vez de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


A consulta a seguir retorna o valor JSON sem o rótulo `"address"` nos resultados.

**Consulta**

    SELECT VALUE f.address
    FROM Families f    

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

O exemplo a seguir expande esse procedimento para mostrar como retornar valores JSON primitivos (no nível da folha da árvore JSON). 

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f    

**Resultados**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* Operador
O operador especial (*) é suportado para projetar o documento da forma que ele é. Quando usado, ele deve ser o único campo projetado. Embora uma consulta como `SELECT * FROM Families f` seja válida, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` não são.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>Operador TOP
A palavra-chave TOP pode ser usada para limitar o número de valores de uma consulta. Quando TOP é usado em conjunto com a cláusula ORDER BY, o conjunto de resultados é limitado ao primeiro número N de valores ordenados; caso contrário, ele retorna o primeiro número N de resultados em uma ordem indefinida. Como melhor prática, em uma instrução SELECT, sempre use uma cláusula ORDER BY com a cláusula TOP. Essa é a única maneira de indicar de modo previsível quais linhas são afetadas pelo TOP. 

**Consulta**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

O TOP pode ser usado com um valor constante (conforme mostrado acima) ou com um valor de variável usando consultas parametrizadas. Para obter mais detalhes, veja as consultas parametrizadas abaixo.

### <a id="Aggregates"></a>Funções de agregação
Você também pode executar agregações na cláusula `SELECT`. Funções agregadas executam um cálculo em um conjunto de valores e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de documentos de família dentro da coleção.

**Consulta**

    SELECT COUNT(1) 
    FROM Families f 

**Resultados**

    [{
        "$1": 2
    }]

Você também pode retornar o valor escalar da agregação usando a palavra-chave `VALUE`. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

**Consulta**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Resultados**

    [ 2 ]

Você também pode executar agregações em combinação com filtros. Por exemplo, a consulta a seguir retorna a contagem de documentos com endereço no estado de Washington.

**Consulta**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Resultados**

    [ 1 ]

A tabela a seguir mostra a lista de funções de agregação com suporte na API do DocumentDB. `SUM` e `AVG` são executados por meio de valores numéricos, enquanto `COUNT`, `MIN` e `MAX` podem ser executados em relação a números, cadeias de caracteres, Boolianos e nulos. 

| Uso | Descrição |
|-------|-------------|
| COUNT | Retorna o número de itens na expressão. |
| SUM   | Retorna a soma de todos os valores na expressão. |
| MÍN.   | Retorna o valor mínimo na expressão. |
| MÁX.   | Retorna o valor máximo na expressão. |
| AVG   | Retorna a média dos valores na expressão. |

Agregações também podem ser executadas em relação aos resultados de uma iteração de matriz. Para obter mais informações, consulte [Iteração de matriz em consultas](#Iteration).

> [!NOTE]
> Ao usar o Gerenciador de Consultas do portal do Azure, observe que as consultas de agregação podem retornar resultados parcialmente agregados em uma página de consulta. Os SDKs produzem um único valor cumulativo em todas as páginas. 
> 
> Para executar consultas de agregação usando o código, você precisa do .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou posterior.    
>

## <a id="OrderByClause"></a>Cláusula ORDER BY
Como no ANSI-SQL, agora você pode incluir uma cláusula Order By opcional ao realizar consultas. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem na qual os resultados devem ser recuperados.

Por exemplo, aqui está uma consulta que recupera famílias pela ordem do nome da cidade do residente.

**Consulta**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

E aqui está uma consulta que recupera famílias em ordem de data de criação, que é armazenada como um número que representa a época, ou seja, o tempo decorrido desde 1 de janeiro de 1970, em segundos.

**Consulta**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Conceitos avançados de banco de dados e consultas SQL

### <a id="Iteration"></a>Iteração
Um novo constructo foi adicionado por meio da palavra-chave **IN** ao SQL da API do DocumentDB para fornecer suporte à iteração em matrizes JSON. A fonte FROM dá suporte à iteração. Comecemos com o exemplo a seguir:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Agora, vejamos outra consulta que realiza a iteração em filhos na coleção. Observe a diferença na matriz de saída. Este exemplo divide `children` e planifica os resultados em uma única matriz.  

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Isso pode ser usado ainda para filtrar cada entrada individual da matriz, como mostra o exemplo a seguir:

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

Você também pode executar a agregação sobre o resultado da iteração de matriz. Por exemplo, a consulta a seguir conta o número de filhos entre todas as famílias.

**Consulta**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Resultados**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Junções
Em um banco de dados relacional, a necessidade de realizar junções entre tabelas é importante. É o padrão lógico para criar esquemas normalizados. De forma contrária, a API do DocumentDB lida com o modelo de dados desnormalizado dos documentos sem esquemas. Trata-se do equivalente lógico de uma “autojunção”.

A sintaxe à qual a linguagem oferece suporte é <from_source1> JOIN <from_source2> JOIN ... JUNÇÃO < from_sourceN >. De modo geral, isto retorna um conjunto de tuplas **N** (tupla com valores **N**). Cada tupla terá os valores produzidos pela iteração de todos os alias da coleção em seus respectivos conjuntos. Em outras palavras, trata-se do produto do cruzamento completo dos conjuntos que participam da junção.

Os exemplos a seguir mostram como a cláusula junção funciona. No exemplo a seguir, o resultado é vazio porque o produto cruzado de cada documento da fonte e de um conjunto vazio é vazio.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


No exemplo a seguir, a junção ocorre entre a raiz do documento e a sub-raiz de `children`. Trata-se de um produto cruzado entre dois objetos JSON. O fato de os filhos serem uma matriz não tem efeito sobre a junção, pois estamos lidando com uma única raiz que é a matriz de filhos. Sendo assim, os resultados contêm apenas dois resultados, uma vez que o produto cruzado de cada documento com a matriz resulta em exatamente um documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


O exemplo a seguir mostra uma junção mais convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



A primeira coisa a observar é que o `from_source` da cláusula **JOIN** é um iterador. Sendo assim, neste caso o fluxo é o seguinte:  

* Expanda cada elemento filho **c** na matriz.
* Aplique um produto cruzado com a raiz do documento **f** com cada elemento filho **c** que foi tornado bidimensional na primeira etapa.
* Por fim, projete a propriedade do nome do objeto raiz **f** sozinha. 

O primeiro documento (`AndersenFamily`) contém somente um elemento filho, de modo que o conjunto de resultados contém apenas um único objeto correspondente a esse documento. O segundo documento (`WakefieldFamily`) contém dois filhos. Sendo assim, o produto cruzado produz um objeto separado para cada filho, resultando em dois objetos, um para cada filho correspondente a este documento. Os campos raiz em ambos os documentos são os mesmos, exatamente como você esperaria em um produto cruzado.

A utilidade real da junção é formar tuplas do produto cruzado em um formato que, de outra forma, seria difícil projetar. Além disso, como vemos no exemplo abaixo, é possível filtrar a combinação de uma tupla que permite ao usuário escolher uma condição que é atendida pelas tuplas de modo geral.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este exemplo é uma extensão natural do exemplo anterior, e realiza uma junção dupla. Assim, o produto cruzado pode ser visto como o pseudocódigo a seguir:

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` tem um filho que, por sua vez, tem um animal de estimação. Assim, o produto cruzado traz uma linha (1\*1\*1) desta família. WakefieldFamily, no entanto, tem dois filhos, mas apenas a filha "Jesse" tem animais de estimação. Porém, Jesse tem dois animais de estimação. Assim, o produto cruzado traz 1\*1\*2 = 2 linhas desta família.

No próximo exemplo, há um filtro adicional em `pet`. Isto exclui todas as tuplas em que o nome do animal não é "Shadow". Observe que podemos criar tuplas por meio de matrizes, filtrar qualquer um dos elementos da tupla e projetas qualquer combinação dos elementos. 

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Integração do JavaScript
O Azure Cosmos DB oferece um modelo de programação para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções, com relação a procedimentos armazenados e gatilhos. Isso possibilita:

* Capacidade de realizar operações CRUD transacionais de alto desempenho e consultas documentos em uma coleção devido à profunda integração do tempo de execução do JavaScript diretamente ao mecanismo de banco de dados. 
* Um modelamento natural de fluxo de controle, escopo de variáveis, atribuição e integração de primitivos que lidam com exceções com transações de bancos de dados. Para obter detalhes sobre o suporte do Azure Cosmos DB à integração com JavaScript, consulte a documentação sobre programação JavaScript do lado do servidor.

### <a id="UserDefinedFunctions"></a>UDFs (Funções Definidas pelo Usuário)
Além dos tipos já definidos neste artigo, o SQL da API do DocumentDB fornece suporte a UDFs (Funções Definidas pelo Usuário). Em particular, há suporte a UDFs escalares nas quais os desenvolvedores podem passar zero ou muitos argumentos e retornar um resultado com um único argumento. Cada um desses argumentos é verificado para definir se são valores JSON legais.  

A sintaxe SQL da API do DocumentDB é estendida para dar suporte à lógica de aplicativos personalizados usando essas Funções Definidas pelo Usuário. As UDFs podem ser registradas na API do DocumentDB e então referenciadas como parte de uma consulta SQL. De fato, as UDFs são projetadas de maneira especial para serem invocadas por consultas. Como consequência dessa escolha, as UDFs não têm acesso ao objeto de contexto que outros tipos de JavaScript (procedimentos armazenados e gatilhos) têm. Como as consultas são executadas como somente leitura, elas podem ser executadas em réplicas primárias ou secundárias. Portanto, as UDFs foram criadas para serem executadas em réplicas secundárias, diferente de outros tipos de JavaScript.

Veja abaixo um exemplo de como uma UDF pode ser registrada no banco de dados do Cosmos DB, especificamente em uma coleção de documentos.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

O exemplo anterior cria um UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores de cadeia de caracteres JSON `input` and `pattern` , além de verificar, pelo uso da função string.match() do JavaScript, se o primeiro desses valores corresponde ao padrão especificado no segundo.

Agora, podemos usar esta UDF em uma consulta em uma projeção. UDFs devem ser qualificadas com o prefixo que diferencia maiúsculas de minúsculas "udf." quando chamadas por meio de consultas. 

> [!NOTE]
> Antes de 17/03/2015, o Cosmos DB dava suporte a chamadas a UDF sem o prefixo “udf”. como SELECT REGEX_MATCH(). Esse padrão de chamada foi preterido.  
> 
> 

**Consulta**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

A UDF também pode ser usada dentro de um filtro, conforme mostrado no exemplo abaixo, também qualificado com o prefixo "udf." prefixo:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Basicamente, as UDFs são expressões escalares válidas e podem ser usadas em projeções e filtros. 

Para expandir o poder das UDFs, vejamos outro exemplo com lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Abaixo há um exemplo que aplica a UDF.

**Consulta**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Como os exemplos anteriores demonstram, as UDFs integram o poder da linguagem JavaScript ao SQL da API do DocumentDB para fornecer uma avançada interface programável, a fim de realizar uma lógica complexa, condicional e de procedimentos com a ajuda das funcionalidades em tempo de execução internas do JavaScript.

O SQL da API do DocumentDB fornece os argumentos às UDFs para cada documento na fonte no estágio atual (cláusula WHERE ou cláusula SELECT) de processamento da UDF. O resultado é incorporado perfeitamente ao pipeline de execução geral. Se as propriedades referidas aos parâmetros das UDFs não estiverem disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação das UDFs é totalmente ignorada. De maneira semelhante, se o resultado das UDFs for indefinido, ele não será incluído nos resultados. 

Em resumo, as UDFs são ótimas ferramentas para realizar lógicas de negócios complexas como parte da consulta.

### <a name="operator-evaluation"></a>Avaliação de operador
O Cosmos DB, em virtude se ser um banco de dados JSON, estabelece um paralelo com operadores JavaScript e sua semântica de avaliação. Embora o Cosmos DB tente preservar a semântica do JavaScript em termos de suporte ao JSON, a avaliação da operação se desvia em alguns casos.

No SQL da API do DocumentDB, ao contrário do que ocorre no SQL tradicional, é frequente que os tipos de valores não sejam conhecidos até que os valores sejam recuperados do banco de dados. Para executar consultas com eficiência, a maioria dos operadores tem requisitos restritos de tipo. 

O SQL da API do DocumentDB não realiza conversões implícitas, diferente do JavaScript. Por exemplo, uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponde a documentos que contêm a propriedade Age com o valor 21. Qualquer outro documento cuja propriedade Age corresponder a “21” — ou a outras variações potencialmente infinitas como “021”, “21,0”, “0021”, “00021” etc. — não será correspondido. Isso ocorre em oposição ao JavaScript, que os valores das cadeias de caracteres são convertidos implicitamente em números (baseado em operador como, por exemplo: ==). Esta escolha é fundamental para uma correspondência eficiente de índices no SQL da API do DocumentDB. 

## <a name="parameterized-sql-queries"></a>Consultas SQL parametrizadas
O Cosmos DB dá suporte a consultas com parâmetros expressos com a conhecida notação @. A SQL parametrizada oferece recursos robustos de manuseio e saída das entradas de usuário, evitando a exposição acidental de dados por meio de uma injeção SQL. 

Por exemplo, você pode escrever uma consulta que define o sobrenome e o estado do endereço como parâmetros e executá-la para vários valores de sobrenome e estado de endereço, com base na entrada do usuário.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Essa solicitação pode então ser enviada ao Cosmos DB como uma consulta JSON parametrizada, conforme mostrado abaixo.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

O argumento para TOP pode ser definido usando consultas parametrizadas, como mostrado abaixo.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Os valores dos parâmetros podem ser qualquer JSON válido (cadeias de caracteres, números, boolianos, nulos, ou mesmo matrizes e JSON aninhado). Além disso, como o Cosmos DB não tem esquemas, os parâmetros não são validados em relação a nenhum tipo.

## <a id="BuiltinFunctions"></a>Funções internas
O Cosmos DB também dá suporte a várias funções internas para operações comuns, que podem ser usadas em consultas como UDFs (funções definidas pelo usuário).

| Grupo de funções          | Operações                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Funções matemáticas  | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN e TAN |
| Funções de verificação de tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE                                                           |
| Funções de cadeia de caracteres        | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING e UPPER       |
| Funções de matriz         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE                                                                                         |
| Funções espaciais       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID e ST_ISVALIDDETAILED                                                                           | 

Se, no momento, você estiver usando uma UDF (função definida pelo usuário) para a qual uma função interna agora está disponível, deverá usar a função interna correspondente, pois sua execução será mais rápida e mais eficiente. 

### <a name="mathematical-functions"></a>Funções matemáticas
As funções matemáticas executam um cálculo, com base em valores de entrada fornecidos como argumentos e retornam um valor numérico. Aqui está uma tabela de funções matemáticas internas com suporte.


| Uso | Descrição |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Retorna o valor absoluto (positivo) da expressão numérica especificada. |
| [CEILING (num_expr)](#bk_ceiling) | Retorna o menor valor de número inteiro maior ou igual à expressão numérica especificada. |
| [FLOOR (num_expr)](#bk_floor) | Retorna o maior inteiro menor ou igual à expressão numérica especificada. |
| [EXP (num_expr)](#bk_exp) | Retorna o expoente da expressão numérica especificada. |
| [LOG (num_expr [,base])](#bk_log) | Retorna o logaritmo natural da expressão numérica especificada ou o logaritmo usando a base especificada |
| [LOG10 (num_expr)](#bk_log10) | Retorna o valor logarítmico de base 10 da expressão numérica especificada. |
| [ROUND (num_expr)](#bk_round) | Retorna um valor numérico, arredondado para o valor inteiro mais próximo. |
| [TRUNC (num_expr)](#bk_trunc) | Retorna um valor numérico, truncado para o valor inteiro mais próximo. |
| [SQRT (num_expr)](#bk_sqrt) | Retorna a raiz quadrada de expressão numérica especificada. |
| [SQUARE (num_expr)](#bk_square) | Retorna o quadrado de expressão numérica especificada. |
| [POWER (num_expr, num_expr)](#bk_power) | Retorna a potência da expressão numérica especificada para o valor especificado. |
| [SIGN (num_expr)](#bk_sign) | Retorna o valor de entrada (-1, 0, 1) da expressão numérica especificada. |
| [ACOS (num_expr)](#bk_acos) | Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno). |
| [ASIN (num_expr)](#bk_asin) | Retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado de arco seno. |
| [ATAN (num_expr)](#bk_atan) | Retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado de arco tangente. |
| [ATN2 (num_expr)](#bk_atn2) | Retorna o ângulo, em radianos, entre o eixo x positivo e o raio da origem até o ponto (x, y), em que x e y são os valores de duas expressões flutuantes especificadas. |
| [COS (num_expr)](#bk_cos) | Retorna o cosseno trigonométrico do ângulo especificado, em radianos, na expressão especificada. |
| [COT (num_expr)](#bk_cot) | Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada. |
| [DEGREES (num_expr)](#bk_degrees) | Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos. |
| [PI ()](#bk_pi) | Retorna o valor constante de PI. |
| [RADIANS (num_expr)](#bk_radians) | Retorna radianos quando uma expressão numérica é inserida em graus. |
| [SIN (num_expr)](#bk_sin) | Retorna o seno trigonométrico do ângulo especificado, em radianos, na expressão especificada. |
| [TAN (num_expr)](#bk_tan) | Retorna a tangente da expressão de entrada, na expressão especificada. |

Por exemplo, agora você pode executar consultas como as seguintes:

**Consulta**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

A principal diferença entre as funções do Cosmos DB em comparação com o ANSI SQL é que elas são criadas para funcionar bem com os dados de esquemas mistos e sem esquemas. Por exemplo, se você tem um documento em que a propriedade Tamanho está ausente ou tem um valor não numérico, como "desconhecido", o documento é ignorado, em vez de retornar um erro.

### <a name="type-checking-functions"></a>Funções de verificação de tipo
As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL. As funções de verificação de tipo podem ser usadas para determinar o tipo de propriedades em documentos imediatamente quando ele é desconhecido ou variável. Aqui está uma tabela de funções de verificação de tipo internas com suporte.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é uma matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é um booliano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é um número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é um objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Retorna um booliano indicando se o tipo do valor é uma cadeia de caracteres.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Retorna um valor booliano que indica se um valor foi atribuído à propriedade.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Retorna um valor booliano indicando se o tipo do valor é uma cadeia de caracteres, número, booliano ou nulo.</td>
</tr>

</table>

Usando essas funções, agora você pode executar consultas como as seguintes:

**Consulta**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funções de cadeia de caracteres
As funções escalares a seguir executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano. Aqui temos uma tabela de funções de cadeia de caracteres internas:

| Uso | Descrição |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Retorna o número de caracteres da expressão de cadeia de caracteres especificada |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Retorna parte de uma expressão de cadeia de caracteres. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Retorna a posição inicial da primeira ocorrência da segunda expressão de cadeia de caracteres dentro da primeira expressão de cadeia de caracteres especificada, ou -1 se a cadeia de caracteres não for encontrada. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Retorna a parte esquerda de uma cadeia de caracteres com o número especificado de caracteres. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Retorna a parte direita de uma cadeia de caracteres com o número especificado de caracteres. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Retorna uma expressão de cadeia de caracteres após remover os espaços em branco iniciais. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Retorna uma expressão de cadeia de caracteres após truncar todos os espaços em branco finais. |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos. |
| [UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos. |
| [REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Substitui todas as ocorrências de um valor de cadeia de caracteres especificado por outro valor de cadeia de caracteres. |
| [REPLICATE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-reference#bk_replicate) |Repete um valor de cadeia de caracteres por um número de vezes especificado. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Retorna a ordem inversa de um valor de cadeia de caracteres. |

Usando essas funções, agora você pode executar consultas como as descritas a seguir. Por exemplo, você pode retornar o sobrenome em caracteres maiúsculos, da seguinte maneira:

**Consulta**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou concatenar cadeias de caracteres, como neste exemplo:

**Consulta**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funções de cadeia de caracteres também podem ser usadas na cláusula WHERE para filtrar os resultados, como no exemplo a seguir:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funções de matriz
As funções escalares a seguir executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz. Aqui temos uma tabela de funções de matriz internas:

| Uso | Descrição |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Retorna o número de elementos da expressão de matriz especificada. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Retorna um valor booliano que indica se a matriz contém o valor especificado. Pode especificar se a correspondência é completa ou parcial. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Retorna parte de uma expressão de matriz. |

Funções de matriz podem ser usadas para manipular matrizes contidas no JSON. Por exemplo, aqui está uma consulta que retorna todos os documentos nos quais um dos pais é "Robin Wakefield". 

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Você pode especificar um fragmento parcial para elementos correspondentes dentro da matriz. A consulta a seguir localiza todos os pais com o `givenName` de `Robin`.

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]


Aqui está outro exemplo que usa ARRAY_LENGTH para obter o número de filhos por família.

**Consulta**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funções espaciais
O Cosmos DB dá suporte às funções internas do OGC (Open Geospatial Consortium) a seguir em consultas geoespaciais. 

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retorna uma expressão booliana que indica se o primeiro objeto GeoJSON (Ponto, Polígono ou LineString) está em um segundo objeto GeoJSON (Ponto, Polígono ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Retorna uma expressão booliana que indica se os dois objetos GeoJSON especificados (Ponto, Polígono ou LineString) se cruzam.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Retorna um valor Booliano que indica se a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.</td>
</tr>
</table>

As funções espaciais podem ser usadas para executar consultas de proximidade em consultas espaciais. Por exemplo, veja uma consulta que retorna todos os documentos de família que estejam em um raio de 30 km do local especificado usando a função interna ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Para obter detalhes sobre o suporte geoespacial no Cosmos DB, consulte [Trabalhando com os dados geoespaciais no Azure Cosmos DB](geospatial.md). Com isso, encerramos as funções espaciais e a sintaxe SQL do Cosmos DB. Agora vamos dar uma olhada em como o sistema de consultas LINQ funciona e como ele interage com a sintaxe que vimos até agora.

## <a id="Linq"></a>LINQ para SQL da API do DocumentDB
O LINQ é um modelo de programação .NET que expressa a computação como consultas em fluxos de objetos. O Cosmos DB oferece uma biblioteca do lado do cliente para fazer interface com o LINQ ao facilitar a conversão entre objetos JSON e .NET e um mapeamento por meio de um subconjunto de consultas LINQ para consultas do Cosmos DB. 

A imagem abaixo mostra a arquitetura do suporte a consultas LINQ usando o Cosmos DB.  Usando o cliente do Cosmos DB, os desenvolvedores podem criar um objeto **IQueryable** que consulta diretamente o provedor de consultas do Cosmos DB que, por sua vez, converte a consulta LINQ em uma consulta do Cosmos DB. A consulta é, então, passada ao servidor do Cosmos DB para recuperar um conjunto de resultados no formato JSON. Os resultados retornados são desserializados em um fluxo de objetos .NET no lado do cliente.

![Arquitetura de suporte a consultas LINQ usando a API do DocumentDB – sintaxe SQL, linguagem de consulta JSON, conceitos de banco de dados e consultas SQL][1]

### <a name="net-and-json-mapping"></a>Mapeamento de .NET e JSON
O mapeamento entre objetos .NET e documentos JSON é natural - cada campo de membro de dados é mapeado para um objeto JSON, em que o nome do campo é mapeado para a parte “chave” do objeto e a parte do “valor” é mapeada recursivamente para a parte de valor do objeto. Considere o seguinte exemplo: o objeto Família criado é mapeado para o documento JSON conforme mostrado abaixo. E vice-versa, o documento JSON é mapeado para um objeto .NET.

**Classe C#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>Tradução de LINQ em SQL
O provedor de consultas do Cosmos DB realiza um mapeamento de melhor esforço de uma consulta LINQ para uma consulta SQL do Cosmos DB. Na descrição a seguir, presumimos uma familiaridade básica do leitor com o LINQ.

Primeiro, para o sistema de tipos, oferecemos suporte para todos os tipos de JSON primitivos - tipos numéricos, boolianos, cadeia de caracteres e nulo. Somente esses tipos de JSON têm suporte. As expressões escalares são suportadas.

* Valores constantes – incluem valores constantes dos tipos de dados primitivos no momento em que a consulta é avaliada.
* Expressões de índice de propriedade/matriz – essas expressões se referem à propriedade de um objeto ou um elemento de matriz.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n é uma variável int
* Expressões aritméticas - Incluem expressões aritméticas comuns em valores numéricos e boolianos. Para obter uma lista completa, consulte a especificação da SQL.
  
     2 * family.children[0].grade;    x + y;
* Expressão de comparação de cadeias de caracteres - incluem a comparação de um valor de cadeia de caracteres a um valor constante de cadeia de caracteres.  
  
     mother.familyName == "Smith";    child.givenName == s; //s é uma variável de cadeia de caracteres
* Expressão de criação de objeto/matriz - estas expressões retornam um objeto do tipo de valor composto ou tipo anônimo ou uma matriz desses objetos. Esses valores podem ser aninhados.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //um tipo anônimo com dois campos              
     novo int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Lista de operadores LINQ com suporte
Aqui está uma lista de operadores LINQ com suporte no provedor LINQ incluídos no SDK do .NET do DocumentDB.

* **Select**: as projeções são convertidas para SQL SELECT, incluindo a construção de objetos
* **Em que**: os filtros são convertidos para SQL WHERE e dão suporte à conversão entre && , || e ! para os operadores SQL
* **SelectMany**: permite o desenrolamento de matrizes à cláusula SQL JOIN. Pode ser usado para encadear/aninhar expressões para filtrar elementos de matriz
* **OrderBy e OrderByDescending**: são convertidas em ORDER BY de forma crescente/decrescente
* Operadores **Count**, **Sum**, **Min**, **Max** e **Average** para agregação e os seus equivalentes assíncronos, **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
* **CompareTo**: é convertido em comparações de intervalo. Normalmente usados para cadeias de caracteres, já que não são comparáveis no .NET
* **Take**: é convertido em SQL TOP para limitar os resultados de uma consulta
* **Funções matemáticas**: dão suporte à conversão de Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan e Truncate do .NET nas funções internas do SQL equivalentes.
* **Funções de cadeia de caracteres**: dão suporte à conversão de Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString e ToUpper do .NET nas funções internas do SQL equivalentes.
* **Funções de matriz**: dão suporte à conversão de Concat, Contains e Count do .NET nas funções internas do SQL equivalentes.
* **Funções de extensão geoespacial**: dão suporte à conversão dos métodos stub Distance, Within, IsValid e IsValidDetailed nas funções internas do SQL equivalentes.
* **Função de Extensão da Função Definida pelo Usuário**: dá suporte à translação do método stub UserDefinedFunctionProvider.Invoke na função definida pelo usuário correspondente.
* **Diversos**: dá suporte à conversão dos operadores de união e condicional. Pode converter Contains para a Cadeia de caracteres CONTAINS, ARRAY_CONTAINS ou para o SQL IN, dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
Aqui, temos alguns exemplos que ilustram como alguns dos operadores de consulta LINQ padrão são convertidos em consultas do Cosmos DB.

#### <a name="select-operator"></a>Operador Select
A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar.

**Expressão lambda do LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expressão lambda do LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expressão lambda do LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operador SelectMany
A sintaxe é `input.SelectMany(x => f(x))`, em que `f` é uma expressão escalar que retorna um tipo de coleção.

**Expressão lambda do LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Operador Where
A sintaxe é `input.Where(x => f(x))`, em que `f` é uma expressão escalar que retorna um valor booliano.

**Expressão lambda do LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expressão lambda do LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Consultas SQL composta
Os operadores acima podem ser compostos para formar consultas mais poderosas. Como o Cosmos DB dá suporte a coleções aninhadas, a composição pode ser concatenada ou aninhada.

#### <a name="concatenation"></a>Concatenação
A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode ser iniciada por uma consulta `SelectMany` opcional, seguida por múltiplos operadores `Select` ou `Where`.

**Expressão lambda do LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expressão lambda do LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expressão lambda do LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expressão lambda do LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Aninhamento
A sintaxe é `input.SelectMany(x=>x.Q())`, em que Q é um operador `Select`, `SelectMany` ou `Where`.

Em uma consulta aninhada, a consulta interior é aplicada a cada elemento da coleção externa. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos na coleção exterior, como autojunções.

**Expressão lambda do LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expressão lambda do LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expressão lambda do LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Execução de consultas SQL
O Cosmos DB expõe recursos por meio de uma API REST que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Cosmos DB oferece bibliotecas de programação para várias linguagens populares, como .NET, Node.js, JavaScript e Python. A API REST e as diversas bibliotecas suportam a consulta por meio de SQL. O SDK .NET oferece suporte para consultas no LINQ além da SQL.

Os exemplos a seguir mostram como criar uma consulta e enviá-la a uma conta de banco de dados do Cosmos DB.

### <a id="RestAPI"></a>API REST
O Cosmos DB oferece um modelo de programação RESTful por HTTP. As contas do banco de dados podem ser provisionadas usando uma assinatura do Azure. O modelo de recurso do Cosmos DB consiste em um conjunto de recursos em uma conta de banco de dados, cada um endereçável usando um URI lógico e estável. Um conjunto de recursos é referido como um feed neste documento. Uma conta do banco de dados é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas, por sua vez, contendo documentos, UDFs e outros tipos de recursos.

O modelo de interação básico com esses recursos é por meio dos verbos HTTP GET, PUT, POST e DELETE, com sua interpretação padrão. O verbo POST é usado para criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta do Cosmos DB. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a seguir mostram um POST para uma consulta da API do DocumentDB feita em uma coleção que contém os dois documentos de exemplo que vimos até agora. A consulta tem um filtro simples na propriedade do nome JSON. Observe o uso dos cabeçalhos `x-ms-documentdb-isquery` e Content-Type: `application/query+json` para indicar que a operação é uma consulta.

**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


O segundo exemplo mostra uma consulta mais complexa que retorna múltiplos resultados da junção.

**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Se os resultados de uma consulta não couberem em uma página de resultados, a API REST retornará um token de continuação por meio do cabeçalho de resposta `x-ms-continuation-token` . Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado por meio do cabeçalho de número `x-ms-max-item-count` . Se a consulta especificada tiver uma função de agregação como `COUNT`, a página de consulta poderá retornar um valor parcialmente agregado na página de resultados. Os clientes devem executar uma segunda agregação nesses resultados para produzir os resultados finais. Por exemplo, a soma das contagens retornadas nas páginas individuais para retornar a contagem total.

Para gerenciar a política de consistência de dados para consultas, use o cabeçalho `x-ms-consistency-level` como todas as solicitações da API REST. Para que haja consistência da sessão, é necessário também ecoar o cabeçalho de cookie `x-ms-session-token` mais recente na solicitação de consulta. A política de indexação da coleção consultada também pode influenciar a consistência dos resultados da consulta. Com as configurações da política de indexação padrão, para as coleções, o índice sempre está atualizado com o conteúdo dos documentos e os resultados das consultas correspondem à consistência escolhida para os dados. Se a política de indexação for relaxada para Lenta, as consultas poderão retornar resultados obsoletos. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB][consistency-levels].

Se a política de indexação configurada na coleção não puder dar suporte à consulta especificada, o servidor do Azure Cosmos DB retornará um erro 400, “Solicitação Inválida”. Este código é retornado para consultas de intervalo em caminhos configurados para pesquisas hash (igualdade), e para caminhos excluídos explicitamente da indexação. O cabeçalho `x-ms-documentdb-query-enable-scan` pode ser especificado para permitir que a consulta faça uma verificação quando um índice estiver indisponível.

Você pode obter métricas detalhadas na execução da consulta configurando o cabeçalho `x-ms-documentdb-populatequerymetrics` como `True`. Para obter mais informações, consulte [Métricas de consulta SQL para a API do DocumentDB do Azure Cosmos DB](documentdb-sql-query-metrics.md).

### <a id="DotNetSdk"></a>SDK do C# (.NET)
O SDK .NET suporta consultas LINQ e SQL. O exemplo a seguir mostra como realizar a consulta de filtro simples mencionada no início deste documento.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Esta amostra compara duas propriedades quanto à igualdade dentro de cada documento e usa projeções anônimas. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


A amostra seguinte mostra junções, expressas por meio do LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



O cliente .NET itera automaticamente em todas as páginas dos resultados de pesquisa nos blocos foreach, conforme mostrado acima. As opções de consulta introduzidas na seção da API REST também estão disponíveis no SDK .NET usando as classes `FeedOptions` and `FeedResponse` no método CreateDocumentQuery. O número de páginas pode ser controlado usando a configuração `MaxItemCount` . 

Você também pode controlar explicitamente paginação criando `IDocumentQueryable` usando o objeto `IQueryable`, lendo os valores` ResponseContinuationToken` e passando-os de volta como `RequestContinuationToken` em `FeedOptions`. `EnableScanInQuery` pode ser configurado para permitir verificações quando não for possível que a política de indexação configurada dê suporte à consulta. Para coleções particionadas, use `PartitionKey` para executar a consulta em uma única partição (embora o Cosmos DB possa extraí-la do texto da consulta) e `EnableCrossPartitionQuery` para executar consultas que talvez precisem ser executadas em várias partições. 

Consulte [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) para obter mais amostras que contêm consultas. 

### <a id="JavaScriptServerSideApi"></a>API do lado servidor do JavaScript
O Cosmos DB oferece um modelo de programação para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções usando procedimentos armazenados e gatilhos. A lógica de JavaScript registrada no nível da coleção pode então emitir operações do banco de dados nas operações dos documentos da coleção determinada. Essas operações são encapsuladas em transações ACID ambiente.

O exemplo a seguir mostra como usar o queryDocuments na API do servidor do JavaScript para realizar consultas de dentro de procedimentos e gatilhos armazenados.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Referências
1. [Introdução ao Azure Cosmos DB][introduction]
2. [Especificação do SQL no Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net)
4. [Níveis de consistência do Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Especificação Javascript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Técnicas de avaliação de consulta para bancos de dados de grande porte [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. Estrutura em cascata para otimização da consulta. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md