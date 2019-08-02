---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172592"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especifica a definição de estrutura para conjuntos de dados retangulares
A seção de estrutura em conjuntos de dados JSON é uma seção **opvional** para tabelas retangulares (com linhas e colunas) e contém uma coleção de colunas para a tabela. Você usará a seção de estrutura tanto para fornecer informações de tipo para conversões de tipo quanto para fazer mapeamentos de coluna. As seções a seguir descrevem esses recursos detalhadamente. 

Cada coluna contém as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| type |Tipo de dados da coluna. Consulte a seção de conversões de tipo abaixo para obter mais detalhes sobre quando deve você especificar informações de tipo |Não |
| culture |Cultura baseada em .NET a ser usada quando o tipo é especificado e é o tipo .NET Datetime ou Datetimeoffset. O padrão é "en-us". |Não |
| format |O formato de cadeia de caracteres a ser usado quando o tipo é especificado e é o tipo .NET Datetime ou Datetimeoffset. |Não |

O exemplo a seguir mostra a seção da estrutura JSON para uma tabela que tem três colunas userid, name e lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Use as diretrizes a seguir referentes a quando incluir informações de "estrutura" e o que incluir na seção **estrutura** .

* **Para fontes de dados estruturados** que armazenam o esquema de dados e informações de tipo junto com os dados em si (fontes como tabela do Azure do SQL Server, Oracle, etc.), você deve especificar a seção "estrutura" apenas se quiser fazer o mapeamento de coluna de colunas de origem específicas para colunas específicas no coletor e seus nomes não forem iguais (consulte detalhes na seção mapeamento de coluna abaixo). 
  
    Conforme mencionado acima, as informações de tipo são opcionais na seção "estrutura". Para fontes estruturadas, as informações de tipo já estão disponíveis como parte da definição de conjunto de dados no repositório de dados, portanto, você não deve incluir informações de tipo quando você incluir a seção "estrutura".
* **Para esquema de fontes de dados de leitura (especificamente o blob do Azure)** , você pode optar por armazenar os dados sem armazenar nenhuma informação de tipo ou esquema juntamente com esses dados. Para esses tipos de fontes de dados, você deve incluir "estrutura" nos 2 casos a seguir:
  * Você deseja fazer o mapeamento de coluna.
  * Quando o conjunto de dados é uma fonte em uma Atividade de cópia, você pode fornecer informações de tipo em "estrutura" e o data factory usará essas informações de tipo para conversão em tipos nativos para o coletor. Consulte o artigo [Mover dados de e para o Blob do Azure](../articles/data-factory/v1/data-factory-azure-blob-connector.md) para obter mais informações.

### <a name="supported-net-based-types"></a>Tipos baseados em .NET para os quais há suporte
O data factory dá suporte aos valores de tipo baseados em .NET compatíveis com CLS a seguir, para fornecer informações de tipo em "estrutura" para esquema de fontes de dados de leitura, como blob do Azure.

* Int16
* Int32 
* Int64
* Single
* Duplo
* Decimal
* Byte[]
* Bool
* Cadeia de caracteres 
* Guid
* Datetime
* Datetimeoffset
* Timespan 

Para Datetime e Datetimeoffset, você também pode especificar as cadeias de caracteres para "cultura" e "formato" para facilitar a análise de sua cadeia de caracteres Datetime personalizada. Consulte o exemplo de conversão de tipo abaixo.

