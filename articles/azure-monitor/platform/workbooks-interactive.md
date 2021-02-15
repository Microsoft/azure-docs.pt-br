---
title: Azure Monitor pastas de trabalho com parâmetros personalizados
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: daedef3410e32b97f8cf753bcbad6c2bc11bbc41
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143646"
---
# <a name="interactive-workbooks"></a>Pastas de trabalho interativas

As pastas de trabalho permitem que os autores criem relatórios e experiências interativos para seus consumidores. Há suporte para interatividade de várias maneiras.

## <a name="parameter-changes"></a>Alterações de parâmetro

Quando um usuário da pasta de trabalho atualiza um parâmetro, qualquer controle que usa o parâmetro é atualizado automaticamente e redesenha para refletir o novo estado. É assim que a maioria dos relatórios de portal do Azure dá suporte à interatividade. As pastas de trabalho fornecem isso de maneira direta com o mínimo de esforço do usuário.

Saiba mais sobre [parâmetros em pastas de trabalho](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Grade, bloco, seleções de gráfico

As pastas de trabalho permitem que os autores construam cenários em que clicar em uma linha em uma grade atualiza os gráficos subsequentes com base no conteúdo da linha.

Por exemplo, um usuário pode ter uma grade que mostra uma lista de solicitações e algumas estatísticas como contagens de falha. Eles poderiam configurá-lo de tal forma que clicar em uma linha correspondente a uma solicitação, resultará em gráficos detalhados abaixo da atualização para filtrar até a mesma solicitação.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Configurando a interatividade na linha de grade clique

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link _Adicionar consulta_ para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como _log_, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` para ver os resultados
6. Selecione o ícone _Configurações avançadas_ no rodapé da consulta (o ícone é semelhante a uma engrenagem). Isso abre o painel Configurações avançadas.
7. Verifique a configuração: `When an item is selected, export a parameter` .
8. Na configuração marcada, selecione *Adicionar parâmetro* e preencha com as informações abaixo.
    1. Campo a ser exportado: `Request`
    2. Nome do parâmetro: `SelectedRequest`
    3. Valor padrão: `All requests`
9. Selecionar Salvar

    ![Captura de tela mostrando o editor avançado com configurações para exportar campos como parâmetros.](./media/workbooks-interactive/export-parameters-add.png)

10. Selecione `Done Editing`.
11. Adicione outro controle de consulta usando as etapas 2 e 3.
12. Use o editor de consultas para inserir o KQL para sua análise.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` para ver os resultados.
14. Altere a _Visualização_ para `Area chart` .
15. Escolha uma linha para selecionar na primeira grade. Observe como o gráfico de área abaixo é filtrado para a solicitação selecionada.

O relatório resultante tem esta aparência no modo de edição:

![Captura de tela da primeira duas consultas no modo de edição.](./media/workbooks-interactive//interactivity-grid-create.png)

A imagem abaixo mostra um relatório interativo mais elaborado no modo de leitura com base nos mesmos princípios. O relatório usa cliques de grade para exportar parâmetros – que, por sua vez, são usados em dois gráficos e um bloco de texto.

![Captura de tela de um relatório usando cliques de grade no modo de leitura.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportando o conteúdo de uma linha inteira

Às vezes, é desejável exportar todo o conteúdo da linha selecionada em vez de apenas uma coluna específica. Nesses casos, deixe a `Field to export` Propriedade desdefinida na etapa 7,1 acima. As pastas de trabalho vão exportar todo o conteúdo da linha como um JSON para o parâmetro.

No controle KQL de referência, use a `todynamic` função para analisar o JSON e acessar as colunas individuais.

## <a name="grid-cell-clicks"></a>Cliques de célula de grade

As pastas de trabalho permitem que os autores adicionem interatividade por meio de um tipo especial de renderizador de coluna de grade chamado um `link renderer` . Um renderizador de link converte uma célula de grade em um hiperlink com base no conteúdo da célula. As pastas de trabalho dão suporte a vários tipos de renderizadores de link-incluindo aqueles que permitem abrir folhas de visão geral de recursos, visualizadores de conjunto de propriedades, pesquisa, uso, rastreamento de transações, etc.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Configurando a interatividade usando cliques de célula de grade

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Use o link _Adicionar consulta_ para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como _log_, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para a sua análise

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` para ver os resultados
6. Selecione _configurações de coluna_ para abrir o painel configurações.
7. Na seção _colunas_ , defina:
    1. _Exemplo_ -renderizador de coluna: `Link` , exibição para abrir: `Cell Details` , rótulo de link: `Sample`
    2. Processador de _contagem_ de colunas: `Bar` , paleta de cores: `Blue` , valor mínimo:`0`
    3. Renderizador de coluna de _solicitação_ :`Automatic`
    4. Selecione _salvar e fechar_ para aplicar as alterações

    ![Captura de tela da guia de configuração de coluna.](./media/workbooks-interactive/column-settings.png)

8. Clique em um dos `Sample` links na grade. Isso abre um painel com os detalhes de uma solicitação de amostra.

    ![Captura de tela do painel de detalhes da solicitação de amostra.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Ações do renderizador de link

| Ação do link | Ação ao clicar |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores de linha em uma guia de contexto de grade de propriedades |
| `Cell Details` | Mostra o valor da célula em uma guia de contexto da grade de propriedades. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, JSON com propriedades de solicitação, como local, instância de função, etc.). |
| `Cell Details` | Mostra o valor da célula em uma guia de contexto da grade de propriedades. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, JSON com propriedades de solicitação, como local, instância de função, etc.). |
| `Custom Event Details` | Abre os detalhes da pesquisa Application Insights com a ID do evento personalizado ( `itemId` ) na célula |
| `* Details` | Semelhante aos detalhes do evento personalizado, exceto para dependências, exceções, exibições de página, solicitações e rastreamentos. |
| `Custom Event User Flows` | Abre o Application Insights Fluxos dos Usuários experiência dinamizada no nome do evento personalizado na célula |
| `* User Flows` | Semelhante ao Fluxos dos Usuários de eventos personalizado, exceto para exceções, exibições de página e solicitações |
| `User Timeline` | Abre a linha do tempo do usuário com a ID de usuário (user_Id) na célula |
| `Session Timeline` | Abre a experiência de pesquisa Application Insights para o valor na célula (por exemplo, pesquise o texto ' abc ' em que ABC é o valor na célula) |
| `Resource overview` | Abra a visão geral do recurso no portal com base no valor da ID do recurso na célula |

## <a name="conditional-visibility"></a>Visibilidade condicional

A pasta de trabalho permite que os usuários façam com que determinados controles apareçam ou desapareçam com base nos valores dos parâmetros. Isso permite que os autores tenham a aparência de relatórios diferentes com base no estado de entrada ou telemetria do usuário. Um exemplo é mostrar aos consumidores apenas um resumo quando as coisas são boas, mas mostram detalhes completos quando algo está errado.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Configurando a interatividade usando a visibilidade condicional

1. Siga as etapas na seção [Configurando a interatividade na linha de grade e clique](#setting-up-interactivity-on-grid-row-click) para configurar dois controles interativos.
2. Adicione um novo parâmetro na parte superior:
    1. Nome: `ShowDetails`
    2. Tipo de parâmetro: `Drop down`
    3. Necessário: `checked`
    4. Obter dados de: `JSON`
    5. Entrada JSON: `["Yes", "No"]`
    6. Salve para confirmar as alterações.

    ![Depois de selecionar o botão Adicionar parâmetro, o painel Editar parâmetro é exibido.](./media/workbooks-interactive/edit-parameter.png)

3. Definir valor do parâmetro como `Yes`

    ![Acima do botão de edição concluído há uma lista suspensa que permitirá que você defina o valor do parâmetro](./media/workbooks-interactive/set-parameter.png)

4. No controle de consulta com o gráfico de área, selecione o ícone de _Configurações avançadas_ (ícone de engrenagem)
5. Verifique a configuração `Make this item conditionally visible`
    1. Este item é visível se o `ShowDetails` valor `equals` do parâmetro `Yes`
6. Selecione _edição concluída_ para confirmar as alterações.
7. Selecione _edição concluída_ na barra de ferramentas da pasta de trabalho para entrar no modo de leitura.
8. Alterne o valor do parâmetro `ShowDetails` para `No` . Observe que o gráfico abaixo desaparece.

A imagem abaixo mostra o caso visível em que `ShowDetails` é `Yes`

![Captura de tela mostrando a visibilidade condicional em que o gráfico está visível](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

A imagem abaixo mostra o caso oculto em que `ShowDetails` é `No`

![Captura de tela mostrando a visibilidade condicional em que o gráfico está oculto](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interatividade com várias seleções em grades e gráficos

As etapas de consulta e métricas também podem exportar um ou mais parâmetros quando uma linha (ou várias linhas) é selecionada.

![Captura de tela mostrando as configurações de parâmetros de exportação com vários parâmetros. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. Na etapa de consulta exibindo a grade, vá para as configurações avançadas.
2. Marque a `When items are selected, export parameters` caixa de seleção. Controles adicionais serão exibidos.
3. Marque a `allow selection of multiple values` caixa de seleção.
    1. A visualização exibida permitirá que os valores de parâmetros de seleção múltipla e exportados sejam matrizes de valores, como ao usar parâmetros de lista suspensa de seleção múltipla.
    2. Se desmarcada, a visualização de exibição respeitará apenas o último item selecionado. Exportando apenas um único valor por vez.
4. Para cada parâmetro que você deseja exportar, use o botão *Adicionar parâmetro* . Uma janela pop-up será exibida, contendo as configurações para o parâmetro a ser exportado.

Quando a seleção única está habilitada, o autor pode especificar o campo dos dados originais a serem exportados. Os campos incluem nome do parâmetro, tipo de parâmetro e valor padrão a ser usado se nada estiver selecionado (opcional).

Quando a seleção múltipla está habilitada, o autor especifica qual campo dos dados originais deve ser exportado. Os campos incluem nome do parâmetro, tipo de parâmetro, aspas e delimitador. As aspas com e valores delimitadores são usados ao transformar os valores de seta em texto quando eles são substituídos em uma consulta. Em seleção múltipla se nenhum valor for selecionado, o valor padrão será uma matriz vazia.

> [!NOTE]
> Para seleção múltipla, somente valores exclusivos serão exportados. você não verá valores de matriz de saída como "1, 1, 2, 1" você obterá "1, 2" como os valores de saída.

Você pode deixar a configuração "campo a ser exportado" vazia nas configurações de exportação. Se você fizer isso, todos os campos disponíveis nos dados serão exportados como um objeto JSON em cadeias de pares chave: valor. Para grades e títulos, todos os campos na grade serão todos. Para gráficos, os campos disponíveis serão x, y, Series e Label (dependendo do tipo de gráfico).

Embora o comportamento padrão seja exportar um parâmetro como texto, se você souber que o campo é uma assinatura ou ID de recurso, use-o como o tipo de parâmetro de exportação. Isso permitirá que o parâmetro seja usado downstream em locais que exijam esses tipos de parâmetros.

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](./workbooks-overview.md#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.