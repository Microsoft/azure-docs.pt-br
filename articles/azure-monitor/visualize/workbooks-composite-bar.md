---
title: Renderizador de barra de Azure Monitor da pasta de trabalho
description: Saiba mais sobre todas as Azure Monitor visualização do renderizador da barra de trabalho de composição.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 9a02299853174192c6963cbb382ceb1aa06ac088
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728596"
---
# <a name="composite-bar-renderer"></a>Renderizador de barra de composição

A pasta de trabalho permite renderizar dados usando a barra de composição, uma barra composta por várias barras.

A imagem abaixo mostra a barra de composição para o status do banco de dados que representa quantos servidores estão online, offline e em estado de recuperação.

![Captura de tela da barra de composição para o status do banco de dados.](./media/workbooks-composite-bar/database-status.png)

O renderizador de barra de composição tem suporte para visualizações de grades, blocos e grafos.

## <a name="adding-composite-bar-renderer"></a>Adicionando renderizador de barra de composição

1. Alterne a pasta de trabalho para o modo de edição selecionando *Editar* item da barra de ferramentas.
2. Selecione *Adicionar* e *Adicionar consulta*.
3. Defina a *fonte de dados* como "JSON" e *Visualização* como "grade".
4. Adicione os dados JSON a seguir.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Executar consulta.
6. Selecione **configurações de coluna** para abrir as configurações.
7. Selecione "total" nas *colunas* e escolha "barra de composição" para o *renderizador de coluna*.
8. Defina as configurações a seguir em *configurações da barra de composição*.

| Nome da coluna | Cor        |
|-------------|--------------|
| online      | Verde        |
| recuperando  | Amarelo       |
| offline     | Vermelho (brilhante) |

9. Adicionar rótulo:`["online"] of ["total"] are healthy`
10. Nas configurações de coluna para online, offline e recuperando, você pode definir o renderizador de coluna como "Hidden" (opcional).
11. Selecione *Rótulos* na parte superior e atualize o rótulo para a coluna total como "status do banco de dados" (opcional).
12. Selecionar ao **aplicar**

As configurações da barra de composição se parecerão com a captura de tela abaixo:

![Captura de tela das configurações de coluna da barra de composição com as configurações descritas acima.](./media/workbooks-composite-bar/composite-bar-settings.png)

A barra de composição com as configurações acima:

![Captura de tela da barra de composição.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Configurações da barra de composição

Selecione o nome da coluna e a cor correspondente para renderizar a coluna nessa cor como parte da barra de composição. Você pode inserir, excluir e mover linhas para cima e para baixo.

### <a name="label"></a>Label

O rótulo da barra de composição é exibido na parte superior da barra de composição. Você pode usar uma mistura de texto estático, colunas e parâmetro.  Se o rótulo estiver vazio, o valor das colunas atuais será exibido como o rótulo. No exemplo anterior, se deixarmos o campo de rótulo preto, o valor do total de colunas será exibido.

Consulte as colunas com `["columnName"]` .

Consulte os parâmetros com `{paramName}` .

O nome de coluna e o nome do parâmetro diferenciam maiúsculas de minúsculas. Você também pode fazer com que os rótulos sejam vinculados selecionando "criar este item como um link" e, em seguida, adicionar configurações de link.

### <a name="aggregation"></a>Agregação

As agregações são úteis para árvore/grupo por visualizações. Os dados de uma coluna para a linha de grupo são decididos pelo conjunto de agregação para essa coluna. Há três tipos de agregações aplicáveis para barras compostas: None, Sum e Inherit.

Para Adicionar grupo por configurações:

1. Em configurações de coluna, vá para a coluna à qual você deseja adicionar configurações.
2. Em *árvore/grupo por configurações* em *tipo de árvore*, selecione **Agrupar por**
3. Selecione o campo pelo qual você deseja agrupar.

![Captura de tela das configurações de agrupar por.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Nenhum

Nenhum agregação significa não exibir nenhum resultado para essa coluna para as linhas do grupo.

![Captura de tela da barra de composição sem agregação None.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Somar

Se a agregação for definida como Sum, a coluna na linha do grupo mostrará a barra de composição usando a soma das colunas usadas para renderizá-la. O rótulo também usará a soma das colunas referenciadas.

No exemplo abaixo, online, offline e recuperando todos têm a agregação máxima definida para eles e a agregação da coluna total é Sum.

![Captura de tela da barra de composição com agregação Sum.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Herdar

Se a agregação for definida como Inherit, a coluna na linha do grupo mostrará a barra de composição usando a agregação definida por usuários para as colunas usadas para renderizá-la. As colunas usadas no rótulo também usam a agregação definida pelo usuário. Se o renderizador de coluna atual for uma barra de composição e for Refereed no rótulo (como "total" no exemplo acima), Sum será usado como a agregação para essa coluna.

No exemplo abaixo, online, offline e recuperando todos têm a agregação máxima definida para eles e a agregação da coluna total é herdada.

![Captura de tela da barra de composição com agregação de herança.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Classificação

Para visualizações de grade, a classificação das linhas da coluna com o renderizador de barra de composição funciona com base no valor que é a soma das colunas usadas para renderizar o computador da barra de composição dinamicamente. Nos exemplos anteriores, o valor usado para classificação é a soma das colunas online, recuperando e offline dessa linha específica.

## <a name="tiles-visualization"></a>Visualização de blocos

1. Selecione **Adicionar** e *Adicionar consulta*.
2. Alterar a fonte de dados para JSON Insira os dados do [exemplo anterior](#adding-composite-bar-renderer).
3. Altere a visualização para *blocos*.
4. Executar consulta.
5. Selecione **configurações de bloco**.
6. Selecione *esquerda* nos campos de bloco.
7. Insira as configurações abaixo em *configurações de campo*.
    1. Usar coluna: "servidor".
    2. Renderizador de coluna: "text".
8. Selecione *inferior* nos campos de bloco.
9. Insira as configurações abaixo em *configurações de campo*.
    1. Usar coluna: "total".
    2. Renderizador de coluna: "barra de composição".
    3. Digite definir as seguintes configurações em "configurações da barra de composição".

    | Nome da coluna | Cor        |
    |-------------|--------------|
    | online      | Verde        |
    | recuperando  | Amarelo       |
    | offline     | Vermelho (brilhante) |

    4. Adicionar rótulo: `["online"] of ["total"] are healthy` .
10. Escolha **Aplicar**.

Configurações da barra de composição para blocos:

![Captura de tela das configurações de bloco da barra de composição com as configurações descritas acima.](./media/workbooks-composite-bar/tiles-settings.png)

A exibição da barra de composição para blocos com as configurações acima terá a seguinte aparência:

![Captura de tela dos blocos da barra de composição.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Visualização de gráficos

Para fazer um renderizador de barra de composição para visualização de gráficos (tipos de clusters de Hive), siga as instruções abaixo.

1. Selecione **Adicionar** e *Adicionar consulta*.
2. Alterar a fonte de dados para JSON Insira os dados do [exemplo anterior](#adding-composite-bar-renderer).
3. Altere a visualização para *grafos*.
4. Executar consulta.
5. Selecione **configurações de grafo**.
6. Selecione o *conteúdo do centro* nas configurações de formato do nó.
7. Insira as configurações abaixo em *configurações de campo*.
    1. Usar coluna: "total".
    2. Renderizador de coluna: "barra de composição".
    3. Insira as configurações a seguir em *configurações da barra de composição*.

    |Nome da coluna  |     Cor    |
    |-------------|--------------|
    | online      | Verde        |
    | recuperando  | Amarelo       |
    | offline     | Vermelho (brilhante) |

   4. Adicionar rótulo: `["online"] of ["total"] are healthy` .
9. Insira as configurações abaixo em *configurações de layout*.
    1. Tipo de grafo: **clusters de Hive**.
    2. Seleção de ID do nó: "servidor".
    3. Agrupar por campo: "nenhum".
    4. Tamanho do nó: 100.
    5. Margem entre hexágonos: 5.
    6. Tipo de Coloring Type: **None**.
1. Escolha **Aplicar**.
    
Configurações da barra de composição para grafos:

![Captura de tela das configurações de grafo de barra de composição com as configurações descritas acima.](./media/workbooks-composite-bar/graphs-settings.png)

A exibição da barra de composição para o grafo com as configurações acima terá a seguinte aparência:

![Captura de tela de grafos de barra de composição com clusters de Hive.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Próximas etapas

* [Implante](../visualize/workbooks-automate.md) pastas de trabalho com Azure Resource Manager.
* [Controle](./workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.