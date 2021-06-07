---
title: Azure Monitor visualizações de grade da pasta de trabalho
description: Saiba mais sobre todas as visualizações Azure Monitor grade da pasta de trabalho.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100605364"
---
# <a name="grid-visualizations"></a>Visualizações de grade

Grades ou tabelas são uma maneira comum de apresentar dados aos usuários. As pastas de trabalho permitem que os usuários estilizam individualmente as colunas da grade para fornecer uma interface do usuário rica para seus relatórios.

O exemplo a seguir mostra uma grade que combina ícones, calor e barras de Spark para apresentar informações complexas. A pasta de trabalho também fornece classificação, uma caixa de pesquisa e um botão ir para a análise.

[![Captura de tela da grade baseada em log](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Adicionando uma grade baseada em log

1. Alterne a pasta de trabalho para o modo de edição clicando no item **Editar** barra de ferramentas.
2. Use o link **Adicionar consulta** para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para sua análise (por exemplo, VMs com memória abaixo de um limite)
5. Definir a visualização como **grade**
6. Defina outros parâmetros, se necessário, como o intervalo de tempo, o tamanho, a paleta de cores e a legenda.

[![Captura de tela da consulta de grade com base em log](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parâmetros do gráfico de log

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a ser usado. | Log, grafo de recursos do Azure, etc. |
| `Resource Type` | O tipo de recurso para o destino. | Application Insights, Log Analytics ou Azure-First |
| `Resources` | Um conjunto de recursos do qual obter o valor de métricas. | MyApp1 |
| `Time Range` | A janela de tempo para exibir o gráfico de log. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a ser usada. | Grid |
| `Size` | O tamanho vertical do controle. | Pequeno, médio, grande ou completo |
| `Query` | Qualquer consulta KQL que retorna dados no formato esperado pela visualização de gráfico. | _solicitações \| resumem solicitações = Count () por nome_ |

## <a name="simple-grid"></a>Grade simples

As pastas de trabalho podem renderizar os resultados de KQL como uma tabela simples. A grade abaixo mostra a contagem de solicitações e usuários exclusivos por tipo de solicitação em um aplicativo.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Captura de tela de uma grade baseada em log no modo de edição](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Estilo da grade

Embora uma tabela simples mostre dados, é difícil ler e as informações não serão sempre aparentes. Estilizar a grade pode ajudar a facilitar a leitura e a interpretação dos dados.

Abaixo está a mesma grade da seção anterior com estilo de calor.

[![Captura de tela de uma grade baseada em log com colunas estilizadas como calor](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Aqui está a mesma grade com estilo de barras: [ ![ captura de tela de uma grade baseada em log com colunas estilizadas como barras](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Estilizando uma coluna de grade

1. Selecione o botão **configuração de coluna** na barra de ferramentas controle de consulta.
2. Em *Editar configurações de coluna*, selecione a coluna para estilo.
3. Escolha um renderizador de coluna (por exemplo, calor, barra, barra abaixo, etc.) e configurações relacionadas para estilizar a coluna.

Abaixo está um exemplo que define a coluna de *solicitação* como uma barra:

[![Captura de tela de uma grade baseada em log com a coluna de solicitação com o estilo de uma barra.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Renderizadores de coluna

| Renderizador de coluna | Explicação | Opções adicionais |
|:------------- |:-------------|:-------------|
| `Automatic` | O padrão-usa o renderizador mais apropriado com base no tipo de coluna.  |  |
| `Text` | Renderiza os valores de coluna como texto. | |
| `Right Aligned` | Semelhante ao texto, exceto que está alinhado à direita. | |
| `Date/Time` | Renderiza uma cadeia de caracteres de data e hora legível. | |
| `Heatmap` | Colore as células da grade com base no valor da célula. | Paleta de cores e o valor mín/máx. usado para dimensionamento. |
| `Bar` | Renderiza uma barra ao lado da célula com base no valor da célula. | Paleta de cores e o valor mín/máx. usado para dimensionamento. |
| `Bar underneath` | Renderiza uma barra próxima à parte inferior da célula com base no valor da célula. | Paleta de cores e o valor mín/máx. usado para dimensionamento. |
| `Composite bar` | Renderiza uma barra de composição usando as colunas especificadas nessa linha. Consulte a [barra de composição](workbooks-composite-bar.md) para obter detalhes. | Colunas com cores correspondentes para renderizar a barra e um rótulo a serem exibidos na parte superior da barra. |
| `Spark bars` | Renderiza uma barra do Spark na célula com base nos valores de uma matriz dinâmica na célula. Por exemplo, a coluna tendência da captura de tela na parte superior. | Paleta de cores e o valor mín/máx. usado para dimensionamento. |
| `Spark lines` | Renderiza uma linha Spark na célula com base nos valores de uma matriz dinâmica na célula. | Paleta de cores e o valor mín/máx. usado para dimensionamento. |
| `Icon` | Renderiza ícones com base nos valores de texto na célula. Os valores com suporte incluem:,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` ,,,,,,,,,,,,,, `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` , `4` , `3` ,, `2` `1` , `Sev0` , `Sev1` ,,,, `Sev2` ,,,, `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` , `Unknown` e `Blank` .|  |
| `Link` | Renderiza um link que, quando clicado ou executa uma ação configurável. Use isso *se você quiser que o* item seja um link.  Qualquer um dos outros tipos *também* pode ser um link usando a `Make this item a link` configuração. Para obter mais informações, consulte [ações de link](#link-actions) abaixo. |  |
| `Location` | Renderiza um nome de região amigável do Azure com base em IDs de região. |  |
| `Resource type` | Renderiza uma cadeia de caracteres de tipo de recurso amigável com base em uma ID de tipo de recurso  |  |
| `Resource` | Renderiza um nome de recurso amigável e um link com base em uma ID de recurso  | Opção para mostrar o ícone de tipo de recurso  |
| `Resource group` | Renderiza um nome de grupo de recursos amigável e um link com base em uma ID de grupo de recursos. Se o valor da célula não for um grupo de recursos, ele será convertido em um.  | Opção para mostrar o ícone do grupo de recursos  |
| `Subscription` | Renderiza um nome de assinatura amigável e um link com base em uma ID de assinatura.  Se o valor da célula não for uma assinatura, ele será convertido em um.  | Opção para mostrar o ícone de assinatura.  |
| `Hidden` | Oculta a coluna na grade. Útil quando a consulta padrão retorna mais colunas do que o necessário, mas um projeto de ausência não é desejado |  |

### <a name="link-actions"></a>Vincular ações

Se o `Link` renderizador for selecionado ou a caixa de seleção *tornar este item um link* estiver marcada, o autor poderá configurar uma ação de link que ocorrerá na seleção da célula. Isso geralmente faz com que o usuário faça alguma outra exibição com contexto proveniente da célula ou que possa abrir uma URL.

### <a name="custom-formatting"></a>Formatação personalizada

As pastas de trabalho também permitem que os usuários definam a formatação de números de seus valores de célula. Eles podem fazer isso clicando na caixa de seleção *formatação personalizada* quando disponível.

| Opção de formatação | Explicação |
|:------------- |:-------------|
| `Units` | As unidades da coluna-várias opções de porcentagem, contagens, tempo, byte, contagem/tempo, bytes/tempo, etc. Por exemplo, a unidade de um valor de 1234 pode ser definida como milissegundos e é renderizada como 1,234 s. |
| `Style` | O formato para renderizá-lo como-Decimal, moeda, porcentagem. |
| `Show group separator` | Caixa de seleção para mostrar separadores de grupo. Renderiza 1234 como 1.234 nos EUA. |
| `Minimum integer digits` | Número mínimo de dígitos inteiros a serem usados (padrão 1). |
| `Minimum fractional digits` | Número mínimo de dígitos fracionários a serem usados (padrão 0).  |
| `Maximum fractional digits` | Número máximo de dígitos fracionários a serem usados. |
| `Minimum significant digits` | Número mínimo de dígitos significativos a serem usados (padrão 1). |
| `Maximum significant digits` | Número máximo de dígitos significativos a serem usados. |
| `Custom text for missing values` | Quando um ponto de dados não tiver um valor, mostre esse texto personalizado em vez de um espaço em branco. |

### <a name="custom-date-formatting"></a>Formatação de data personalizada

Quando o autor tiver especificado que uma coluna é definida como o renderizador de data/hora, o autor poderá especificar opções de formatação de data personalizadas usando a caixa de seleção *formatação de data personalizada* .

| Opção de formatação | Explicação |
|:------------- |:-------------|
| `Style` | O formato para processar uma data como formato curto, longo, completo ou um tempo como formatos de hora curta ou longa. |
| `Show time as` | Permite que o autor decida entre mostrar a hora na hora local (padrão) ou como UTC. Dependendo do estilo de formato de data selecionado, as informações de fuso horário UTC/hora podem não ser exibidas. |

## <a name="custom-column-width-setting"></a>Configuração de largura da coluna personalizada

O autor pode personalizar a largura de qualquer coluna na grade usando o campo de *largura da coluna personalizada* em *configurações de coluna*.

![Captura de tela das configurações de coluna com o campo de largura de coluna personalizada indicado em uma caixa vermelha](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Se o campo for deixado em preto, a largura será determinada automaticamente com base no número de caracteres na coluna e no número de colunas visíveis. A unidade padrão é "ch" (caracteres).

Selecionar o botão azul **(largura atual)** no rótulo preencherá o campo de texto com a largura atual da coluna selecionada. Se um valor estiver presente no campo de largura personalizada sem unidade de medida, o padrão será usado.

As unidades de medida disponíveis são:

| Unidade de medida | Definição           |
|:--------------------|:---------------------|
| ch                  | caracteres (padrão) |
| px                  | pixels               |
| fr                  | unidades fracionárias     |
| %                   | percentage           |

Validação de entrada – se a validação falhar, uma mensagem de orientação vermelha ficará abaixo do campo, mas o usuário ainda poderá aplicar a largura. Se um valor estiver presente na entrada, ele será analisado. Se nenhuma unidade de medida válida for encontrada, o padrão será usado.

Não há largura mínima/máxima, pois isso é deixado para o critério do autor. O campo de largura da coluna personalizada está desabilitado para colunas ocultas.

## <a name="examples"></a>Exemplos

### <a name="spark-lines-and-bar-underneath"></a>Linhas e barras do Spark abaixo

O exemplo a seguir mostra as contagens de solicitações e sua tendência por nome de solicitação.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Captura de tela de uma grade baseada em log com uma barra abaixo e uma linha Spark](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Calor com escalas compartilhadas e formatação personalizada

Este exemplo mostra várias métricas de duração de solicitação e suas contagens. O renderizador calor usa os valores mínimos definidos em configurações ou calcula um valor mínimo e máximo para a coluna e atribui uma cor de plano de fundo da paleta selecionada para a célula com base no valor da célula em relação ao valor mínimo e máximo da coluna.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Captura de tela de uma grade baseada em log com um calor que tem uma escala compartilhada entre colunas](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

No exemplo acima, uma paleta compartilhada (verde ou vermelha) e escala é usada para colorir as colunas (Mean, Median, P80, P95 e P99). Uma paleta separada (azul) usada para a coluna de solicitação.

#### <a name="shared-scale"></a>Escala compartilhada

Para obter uma escala compartilhada:

1. Use expressões regulares para selecionar mais de uma coluna à qual aplicar uma configuração. Por exemplo, defina o nome da coluna como `Mean|Median|p80|p95|p99` para selecionar todos eles.
2. Exclua as configurações padrão para as colunas individuais.

Isso fará com que a nova configuração de várias colunas aplique suas configurações para incluir uma escala compartilhada.

[![Captura de tela de uma configuração de grade com base em log para obter uma escala compartilhada entre colunas](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Ícones para representar o status

O exemplo a seguir mostra o status personalizado de solicitações com base na duração do P95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Captura de tela de uma grade baseada em log com um calor que tem uma escala compartilhada entre colunas usando a consulta acima.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Os nomes de ícone com suporte incluem: `cancelled` ,, `critical` `disabled` , `error` , `failed` ,,,,,,,,,,,,,,, `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` , `4` , `3` , `2` , `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,, e.

### <a name="using-thresholds-with-links"></a>Usando limites com links

As instruções abaixo mostrarão como usar os limites com links para atribuir ícones e abrir pastas de trabalho diferentes. Cada link na grade abrirá um modelo de pasta de trabalho diferente para esse Application Insights recurso.

1. Alterne a pasta de trabalho para o modo de edição selecionando *Editar* item da barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar consulta*.
3. Altere a *fonte de dados* para "JSON" e *Visualização* para "grade".
4. Insira a consulta a seguir.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Executar consulta.
6. Selecione **configurações de coluna** para abrir as configurações.
7. Selecione "nome" em *colunas*.
8. Em *renderizador de coluna*, escolha "limites".
9.  Insira e escolha as seguintes *configurações de limite*.
   
    | Operador | Valor   | Ícones   |
    |----------|---------|---------|
    | ==       | warning | Aviso |
    | ==       | erro   | Com falha  |

    ![Captura de tela da guia Editar configurações de coluna com as configurações acima.](./media/workbooks-grid-visualizations/column-settings.png)

    Mantenha a linha padrão como está. Você pode inserir qualquer texto que desejar. A coluna de texto usa um formato de cadeia de caracteres como uma entrada e a preenche com o valor e a unidade da coluna, se especificado. Por exemplo, se aviso for o valor da coluna, o texto poderá ser " {0} {1} link!", ele será exibido como "link de aviso!".
10. Selecione a caixa *tornar este item um link* .
    1. Em *Exibir para abrir*, escolha "pasta de trabalho (modelo)".
    2. Em o *valor do link vem de*, escolha "vincular".
    3. Selecione a caixa *Abrir link na folha de contexto* .
    4. Escolha as configurações a seguir nas *configurações de link da pasta de trabalho*
        1. Em *ID do modelo vem de*, escolha "coluna".
        2. Em *coluna* , escolha "link".

    ![Captura de tela das configurações de link com as configurações acima.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Selecione "vincular" em *colunas*. Em configurações ao lado de *processador de coluna*, selecione **(Ocultar coluna)**.
1. Para alterar o nome de exibição da coluna "nome", selecione a guia **Rótulos** . Na linha com "nome" como sua *ID de coluna*, em * rótulo de coluna, digite o nome que você deseja exibir.
2. Selecione **aplicar**

![Captura de tela de limites em grade com as configurações acima](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Porcentagens de unidades fracionais

A unidade fracionária (FR) é uma unidade dinâmica de medição comumente usada em vários tipos de grades. À medida que o tamanho da janela/resolução muda, a largura fr também é alterada.

A captura de tela abaixo mostra uma tabela com oito colunas que são 1FR largura cada e todas as larguras iguais. À medida que o tamanho da janela é alterado, a largura de cada coluna é alterada proporcionalmente.

[![Captura de tela das colunas na grade com o valor de largura da coluna de 1FR cada](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

A imagem abaixo mostra a mesma tabela, exceto que a primeira coluna é definida como 50% Width. Isso definirá a coluna para metade da largura total da grade dinamicamente. O redimensionamento da janela continuará a reter a largura de 50%, a menos que o tamanho da janela fique muito pequeno. Essas colunas dinâmicas têm uma largura mínima com base em seu conteúdo. O restante de 50% da grade é dividido por oito unidades fracionárias totais. A coluna "Kind" abaixo é definida como 2fr, portanto, ela ocupa um quarto do espaço restante. Como as outras colunas são 1FR cada uma, cada uma delas ocupa um oitavo da metade direita da grade.

[![Captura de tela das colunas na grade com 1 valor de largura de coluna de 50% e o restante como 1FR cada](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

A combinação de larguras fr,%, PX e CH é possível e funciona de forma semelhante aos exemplos anteriores. As larguras definidas pelas unidades estáticas (CH e px) são constantes rígidas que não serão alteradas mesmo se a janela/resolução for alterada. As colunas definidas por% ocuparão sua porcentagem com base na largura total da grade (talvez não seja exato devido a larguras mínimas anteriores). As colunas definidas com fr apenas dividirão o espaço restante da grade com base no número de unidades fracionárias que são alocadas.

[![Captura de tela das colunas na grade com a variedade de unidades de largura diferentes usadas](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* Saiba como criar uma [árvore em pastas de trabalho](workbooks-tree-visualizations.md).
* Saiba como criar [parâmetros de texto da pasta de trabalho](workbooks-text.md).