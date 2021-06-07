---
title: Ações de link de Azure Monitor pastas de trabalho
description: Como usar ações de link em pastas de trabalho Azure Monitor
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 2d1abe6d77dd4293dc70a07f3b8fd5d9da78d01e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100605449"
---
# <a name="link-actions"></a>Vincular ações

As ações de link podem ser acessadas por meio de etapas de link da pasta de trabalho ou por meio de configurações de coluna de [grades](../visualize/workbooks-grid-visualizations.md), [títulos](../visualize/workbooks-tile-visualizations.md)ou [gráficos](../visualize/workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Ações de link geral

| Ação do link | Ação ao clicar |
|:------------- |:-------------|
| `Generic Details` | Mostra os valores de linha em uma exibição de contexto de grade de propriedades. |
| `Cell Details` | Mostra o valor da célula em uma exibição de contexto da grade de propriedades. Útil quando a célula contém um tipo dinâmico com informações (por exemplo, JSON com propriedades de solicitação, como local, instância de função, etc.). |
| `Url` | Espera-se que o valor da célula seja uma URL http válida, e a célula será um link que abre essa URL em uma nova guia.|

## <a name="application-insights"></a>Application Insights

| Ação do link | Ação ao clicar |
|:------------- |:-------------|
| `Custom Event Details` | Abre os detalhes da pesquisa Application Insights com a ID do evento personalizado ( `itemId` ) na célula. |
| `* Details` | Semelhante aos detalhes do evento personalizado, exceto para dependências, exceções, exibições de página, solicitações e rastreamentos. |
| `Custom Event User Flows` | Abre o Application Insights Fluxos dos Usuários experiência dinamizada no nome do evento personalizado na célula. |
| `* User Flows` | Semelhante ao Fluxos dos Usuários de eventos personalizado, exceto para exceções, exibições de página e solicitações. |
| `User Timeline` | Abre a linha do tempo do usuário com a ID de usuário ( `user_Id` ) na célula. |
| `Session Timeline` | Abre a experiência de pesquisa Application Insights para o valor na célula (por exemplo, pesquise o texto ' abc ' em que ABC é o valor na célula). |

`*` denota um curinga para a tabela acima

## <a name="azure-resource"></a>Recursos do Azure

| Ação do link | Ação ao clicar |
|:------------- |:-------------|
| `ARM Deployment` | Implantar um modelo de Azure Resource Manager.  Quando este item é selecionado, são exibidos campos adicionais para permitir que o autor configure o modelo de Azure Resource Manager a ser aberto, os parâmetros para o modelo, etc. [consulte Azure Resource Manager configurações de link de implantação](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Cria uma regra de alerta para um recurso.  |
| `Custom View` | Abre uma exibição personalizada. Quando este item é selecionado, são exibidos campos adicionais para permitir que o autor configure a extensão de exibição, o nome de exibição e todos os parâmetros usados para abrir a exibição. [Consulte modo de exibição personalizado](#custom-view-link-settings). |
| `Metrics` | Abre uma exibição de métricas.  |
| `Resource overview` | Abra o modo de exibição do recurso no portal com base no valor da ID do recurso na célula. O autor também pode definir, opcionalmente, um `submenu` valor que abrirá um item de menu específico no modo de exibição de recursos. |
| `Workbook (template)` | Abra um modelo de pasta de trabalho.  Quando este item é selecionado, são exibidos campos adicionais para permitir que o autor configure o modelo a ser aberto, etc.  |

## <a name="link-settings"></a>Configurações de link

Ao usar o processador de link, as seguintes configurações estão disponíveis:

![Captura de tela das configurações de link](./media/workbooks-link-actions/link-settings.png)

| Configuração | Explicação |
|:------------- |:-------------|
| `View to open` | Permite que o autor selecione uma das ações enumeradas acima. |
| `Menu item` | Se "visão geral do recurso" estiver selecionado, esse será o item de menu na visão geral do recurso para abrir. Isso pode ser usado para abrir alertas ou logs de atividade em vez de "visão geral" para o recurso. Os valores de item de menu são diferentes para cada Azure `Resourcetype` .|
| `Link label` | Se especificado, esse valor será exibido na coluna grade. Se esse valor não for especificado, o valor da célula será exibido. Se você quiser que outro valor seja exibido, como um calor ou um ícone, não use o `Link` renderizador, em vez disso, use o renderizador apropriado e selecione a `Make this item a link` opção. |
| `Open link in Context Blade` | Se especificado, o link será aberto como uma exibição pop-up de "contexto" no lado direito da janela, em vez de abrir como uma exibição completa. |

Ao usar a `Make this item a link` opção, as seguintes configurações estão disponíveis:

| Configuração | Explicação |
|:------------- |:-------------|
| `Link value comes from` | Ao exibir uma célula como um renderizador com um link, esse campo especifica onde o valor "link" a ser usado no link é proveniente, permitindo que o autor selecione em uma lista suspensa de outras colunas na grade. Por exemplo, a célula pode ser um valor calor, mas você deseja que o link Abra a visão geral do recurso para a ID do recurso na linha. Nesse caso, você definiria o valor do link para ser proveniente do `Resource Id` campo.
| `View to open` | O mesmo que o descrito acima. |
| `Menu item` | O mesmo que o descrito acima. |
| `Open link in Context Blade` | O mesmo que o descrito acima. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager configurações do link de implantação

Se o tipo de link selecionado for `ARM Deployment` o autor deve especificar configurações adicionais para abrir uma implantação de Azure Resource Manager. Há duas guias principais para configurações.

### <a name="template-settings"></a>Configurações de modelo

Esta seção define de onde o modelo deve vir e os parâmetros usados para executar a implantação de Azure Resource Manager.

| Fonte | Explicação |
|:------------- |:-------------|
|`Resource group id comes from` | A ID do recurso é usada para gerenciar recursos implantados. A assinatura é usada para gerenciar os custos e recursos implantados. Os grupos de recursos são usados como pastas para organizar e gerenciar todos os seus recursos. Se esse valor não for especificado, a implantação falhará. Selecione from `Cell` , `Column` , `Static Value` ou `Parameter` em [fontes de link](#link-sources).|
|`ARM template URI from` | O URI para o próprio modelo de Azure Resource Manager. O URI do modelo precisa ser acessível aos usuários que implantarão o modelo. Selecione from `Cell` , `Column` , `Parameter` ou `Static Value`  em [fontes de link](#link-sources). Para iniciantes, dê uma olhada em nossos [modelos de início rápido](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | Esta seção define os parâmetros de modelo usados para o URI de modelo definido acima. Esses parâmetros serão usados para implantar o modelo na página executar. A grade contém um botão de barra de ferramentas de expansão para ajudar a preencher os parâmetros usando os nomes definidos no URI do modelo e defini-lo como valores vazios estáticos. Essa opção só pode ser usada quando não há parâmetros na grade e o URI do modelo foi definido. A seção inferior é uma visualização da aparência da saída do parâmetro. Selecione atualizar para atualizar a visualização com as alterações atuais. Os parâmetros são normalmente valores, enquanto as referências são algo que pode apontar para segredos do keyvault aos quais o usuário tem acesso. <br/><br/> **Limitação de folha do Visualizador de modelos** – não processa parâmetros de referência corretamente e aparecerá como nulo/valor, portanto, os usuários não poderão implantar corretamente os parâmetros de referência da guia Visualizador de modelos.|

![Captura de tela das configurações do modelo de Azure Resource Manager](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>Configurações de UX

Esta seção configura o que os usuários verão antes de executarem a implantação do Azure Resource Manager.

| Fonte | Explicação |
|:------------- |:-------------|
|`Title from` | Título usado na exibição de execução. Selecione from `Cell` , `Column` , `Parameter` ou `Static Value` em [fontes de link](#link-sources).|
|`Description from` | Este é o texto de redução usado para fornecer uma descrição útil aos usuários quando eles desejam implantar o modelo. Selecione from `Cell` , `Column` , `Parameter` ou `Static Value`  em [fontes de link](#link-sources). <br/><br/> **Observação:** Se `Static Value` for selecionado, uma caixa de texto de várias linhas será exibida. Nessa caixa de texto, você pode resolver os parâmetros usando `{paramName}` . Além disso, você pode tratar colunas como parâmetros acrescentando `_column` após o nome da coluna como `{columnName_column}` . Na imagem de exemplo abaixo, podemos fazer referência à coluna `VMName` escrevendo `{VMName_column}` . O valor após os dois-pontos é o [formatador de parâmetro](../visualize/workbooks-parameters.md#parameter-options), neste caso, é `value` .|
|`Run button text from` | Rótulo usado no botão Executar (executar) para implantar o modelo de Azure Resource Manager. Isso é o que os usuários selecionarão para iniciar a implantação do modelo de Azure Resource Manager.|

![Captura de tela das configurações de Azure Resource Manager UX](./media/workbooks-link-actions/ux-settings.png)

Depois que essas configurações forem definidas, quando os usuários selecionarem o link, ele abrirá a exibição com a UX descrita nas configurações de UX. Se o usuário selecionar `Run button text from` , ele implantará um modelo de Azure Resource Manager usando os valores das [configurações de modelo](#template-settings). O modelo de exibição abrirá a guia Visualizador de modelos para que o usuário examine o modelo e os parâmetros antes da implantação.

![Captura de tela da exibição Azure Resource Manager de execução](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Configurações de link de exibição personalizada

Use isso para abrir modos de exibição personalizados no portal do Azure. Verifique todas as definições e configuração. Os valores incorretos causarão erros no portal ou falharão ao abrir as exibições corretamente. Há duas maneiras de definir as configurações por meio do `Form` ou do `URL` .

> [!NOTE]
> Modos de exibição com um menu não podem ser abertos em uma guia de contexto. Se uma exibição com um menu estiver configurada para ser aberta em uma guia de contexto, nenhuma guia de contexto será mostrada quando o link for selecionado.

### <a name="form"></a>Formulário

| Fonte | Explicação |
|:------------- |:-------------|
|`Extension name` | O nome da extensão que hospeda o nome da exibição.|
|`View name` | O nome da exibição a ser aberta.|

#### <a name="view-inputs"></a>Exibir entradas

Há dois tipos de entradas, grades e JSON. Use `Grid` para entradas de guia de chave e valor simples ou selecione `JSON` para especificar uma entrada JSON aninhada.

- Grid
    - `Parameter Name`: O nome do parâmetro de entrada do modo de exibição.
    - `Parameter Comes From`: Em que o valor do parâmetro de exibição deve vir. Selecione from `Cell` , `Column` , `Parameter` ou `Static Value` em [fontes de link](#link-sources).
    > [!NOTE]
    > Se `Static Value` for selecionado, os parâmetros poderão ser resolvidos usando o link colchetes `{paramName}` na caixa de texto. As colunas podem ser tratadas como colunas de parâmetros acrescentando `_column` após o nome da coluna como `{columnName_column}` .

    - `Parameter Value`: dependendo de `Parameter Comes From` , isso será uma lista suspensa de parâmetros, colunas ou um valor estático disponíveis.

    ![Captura de tela da configuração de coluna de edição Mostrar configurações de exibição personalizadas do formulário.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Especifique a entrada de guia em um formato JSON no editor. Como o `Grid` modo, os parâmetros e as colunas podem ser referenciados usando `{paramName}` for Parameters, e `{columnName_column}` para colunas. Selecionar `Show JSON Sample` , mostrará a saída esperada de todos os parâmetros resolvidos e usuário de colunas para a entrada de exibição.

    ![Captura de tela mostrando as configurações de exibição personalizada abertas com a entrada de exibição no JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Cole uma URL do portal que contém a extensão, o nome da exibição e todas as entradas necessárias para abrir a exibição. Depois de selecionar `Initialize Settings` , ele preencherá o `Form` para o autor para adicionar/modificar/remover qualquer uma das entradas de exibição.

![Captura de tela mostrando a configuração de coluna Mostrar configurações de exibição personalizadas da URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Configurações de link da pasta de trabalho (modelo)

Se o tipo de link selecionado for `Workbook (Template)` , o autor deverá especificar configurações adicionais para abrir o modelo de pasta de trabalho correto. As configurações abaixo têm opções de como a grade encontrará o valor apropriado para cada uma das configurações.

| Configuração | Explicação |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Essa é a ID de recurso do recurso do Azure que "possui" a pasta de trabalho. Normalmente, é um recurso Application Insights ou um espaço de trabalho Log Analytics. Dentro de Azure Monitor, isso também pode ser a cadeia de caracteres literal `"Azure Monitor"` . Quando a pasta de trabalho é salva, é isso que a pasta de trabalho será vinculada. |
| `Workbook resources` | Uma matriz de IDs de recursos do Azure que especificam o recurso padrão usado na pasta de trabalho. Por exemplo, se o modelo que está sendo aberto mostrar métricas de máquina virtual, os valores aqui seriam IDs de recurso de máquina virtual.  Muitas vezes, o proprietário e os recursos são definidos com as mesmas configurações. |
| `Template Id` | Especifique a ID do modelo a ser aberto. Se esse for um modelo de comunidade da galeria (o caso mais comum), Prefixe o caminho para o modelo com `Community-` , como `Community-Workbooks/Performance/Apdex` para o `Workbooks/Performance/Apdex` modelo. Se esse for um link para uma pasta de trabalho/modelo salvo, será a ID de recurso completa do Azure desse item. |
| `Workbook Type` | Especifique o tipo de modelo de pasta de trabalho a ser aberto. Os casos mais comuns usam a `Default` `Workbook` opção ou para usar o valor na pasta de trabalho atual. |
| `Gallery Type` | Isso especifica o tipo de galeria que será exibido na exibição de "Galeria" do modelo que é aberto. Os casos mais comuns usam a `Default` `Workbook` opção ou para usar o valor na pasta de trabalho atual. |
|`Location comes from` | O campo local deve ser especificado se você estiver abrindo um recurso de pasta de trabalho específico. Se o local não for especificado, encontrar o conteúdo da pasta de trabalho será muito mais lento. Se você souber o local, especifique-o. Se você não souber o local ou estiver abrindo um modelo que não tem local específico, deixe esse campo como "padrão".|
|`Pass specific parameters to template` | Selecione para passar parâmetros específicos para o modelo. Se selecionado, somente os parâmetros especificados são passados para o modelo. todos os parâmetros na pasta de trabalho atual são passados para o modelo e, nesse caso, os *nomes* de parâmetro devem ser os mesmos em ambas as pastas de trabalho para que esse valor de parâmetro funcione.|
|`Workbook Template Parameters` | Esta seção define os parâmetros que são passados para o modelo de destino. O nome deve corresponder ao nome do parâmetro no modelo de destino. Selecione valor de `Cell` , `Column` , `Parameter` e `Static Value` . O nome e o valor não devem estar vazios para passar esse parâmetro para o modelo de destino.|

Para cada uma das configurações acima, o autor deve escolher de onde virão o valor na pasta de trabalho vinculada. Confira [fontes de link](#link-sources)

Quando o link da pasta de trabalho for aberto, a nova exibição da pasta de trabalho passará todos os valores configurados nas configurações acima.

![Captura de tela das configurações de link da pasta de trabalho](./media/workbooks-link-actions/workbook-link-settings.png)

![Captura de tela de configurações de parâmetros de modelo](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Fontes de link

| Fonte | Explicação |
|:------------- |:-------------|
| `Cell` | Isso usará o valor nessa célula na grade como o valor do link |
| `Column` | Quando selecionado, outro campo será exibido para permitir que o autor selecione outra coluna na grade.  O valor dessa coluna para a linha será usado no valor do link. Normalmente, isso é usado para permitir que cada linha de uma grade abra um modelo diferente, definindo `Template Id` campo como `column` , ou para abrir o mesmo modelo de pasta de trabalho para diferentes recursos, se o `Workbook resources` campo estiver definido como uma coluna que contém uma ID de recurso do Azure |
| `Parameter` | Quando selecionado, outro campo será exibido para permitir que o autor selecione um parâmetro. O valor desse parâmetro será usado para o valor quando o link for clicado |
| `Static value` | Quando selecionado, outro campo será exibido para permitir que o autor digite um valor estático que será usado na pasta de trabalho vinculada. Isso é normalmente usado quando todas as linhas na grade usarão o mesmo valor para um campo. |
| `Step` | Use o valor definido na etapa atual da pasta de trabalho. Isso é comum nas etapas de consulta e métricas para definir os recursos da pasta de trabalho na pasta de trabalho vinculada àqueles usados *na etapa de consulta/métrica*, não na pasta de trabalho atual |
| `Workbook` | Use o valor definido na pasta de trabalho atual. |
| `Default` | Use o valor padrão que seria usado se nenhum valor tiver sido especificado. Isso é comum para o tipo de galeria, em que a Galeria padrão seria definida pelo tipo do recurso do proprietário |

## <a name="next-steps"></a>Próximas etapas

- [Controle](../visualize/workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.
- Saiba como usar [grupos em pastas de trabalho](../visualize/workbooks-groups.md).