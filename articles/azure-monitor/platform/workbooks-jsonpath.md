---
title: Azure Monitor pastas de trabalho – transformar dados JSON com JSONPath
description: Como usar JSONPath em pastas de trabalho Azure Monitor para transformar os resultados de dados JSON retornados por um ponto de extremidade consultado para o formato desejado.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: a2411d9257b1083cb2bcbfcad289813a6c062dff
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143575"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Como usar o JSONPath para transformar dados JSON em pastas de trabalho

As pastas de trabalho são capazes de consultar dados de várias fontes. Alguns pontos de extremidade, como [Azure Resource Manager](../../azure-resource-manager/management/overview.md) ou ponto de extremidades personalizado, podem retornar resultados em JSON. Se os dados JSON retornados pelo ponto de extremidade consultado não estiverem configurados em um formato desejado, JSONPath poderá ser usado para transformar os resultados.

JSONPath é uma linguagem de consulta para JSON semelhante ao XPath for XML. Como o XPath, o JSONPath permite a extração e a filtragem de dados da estrutura JSON.

Usando a transformação JSONPath, os autores da pasta de trabalho são capazes de converter JSON em uma estrutura de tabela. A tabela pode ser usada para plotar as [visualizações da pasta de trabalho](./workbooks-overview.md#visualizations).

## <a name="using-jsonpath"></a>Usando JSONPath

1. Alterne a pasta de trabalho para o modo de edição clicando no item *Editar* barra de ferramentas.
2. Use o link *Adicionar*  >  *consulta adicionar* para adicionar um controle de consulta à pasta de trabalho.
3. Selecione a fonte de dados como *JSON*.
4. Use o editor de JSON para inserir o trecho de JSON a seguir
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Vamos supor que recebamos o objeto JSON acima como uma representação do inventário de um repositório. Nossa tarefa é criar uma tabela dos livros disponíveis do repositório listando seus títulos, autores e preços.

1. Selecione a guia *configurações de resultado* e mude o formato de resultado para *caminho JSON*.
2. Aplique as seguintes configurações de caminho JSON:

    Tabela de caminho JSON: `$.store.books` . Este campo representa o caminho da raiz da tabela. Nesse caso, nos preocupamos com o inventário de livros da loja. O caminho da tabela filtra o JSON para as informações do livro.

   | IDs de coluna | Caminho JSON da coluna |
   |:-----------|:-----------------|
   | Título      | `$.title`        |
   | Autor     | `$.author`       |
   | Preço      | `$.price`        |

    As IDs de coluna serão os cabeçalhos de coluna. Campos de caminhos JSON de coluna representam o caminho da raiz da tabela para o valor da coluna.

1. Aplique as configurações acima clicando em *Executar consulta*

![ Editando o item de consulta com a fonte de dados JSON e o formato de resultado do caminho JSON](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Próximas etapas
- [Visão geral das pastas de trabalho](workbooks-overview.md)
- [Grupos em pastas de trabalho do Azure Monitor](workbooks-groups.md)