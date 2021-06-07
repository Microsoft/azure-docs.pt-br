---
title: Tarefas comuns de conversão do designer de exibição para pastas de trabalho Azure Monitor
description: Tarefas comuns ao fazer a transição de exibições para pastas de trabalho no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: d479e64620519491ba4847349ea33820747fcea7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043466"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Tarefas comuns de conversão do designer de exibição para pastas de trabalho
O [Designer de exibição](view-designer.md) é um recurso do Azure monitor que permite que você crie modos de exibição personalizados para ajudá-lo a Visualizar dados em seu espaço de trabalho do log Analytics, com gráficos, listas e linhas do tempo. Eles estão sendo desativados e substituídos por pastas de trabalho que fornecem funcionalidade adicional. Este artigo detalha as tarefas que são comuns na conversão de exibições em pastas de trabalho.


## <a name="quickstart-with-preset-view-designer-templates"></a>Início rápido com modelos de designer de exibição predefinido

As pastas de trabalho no Log Analytics espaços de trabalho já têm modelos feitos para corresponder a algumas das exibições no designer de exibição. Na categoria **Exibir guias de designer** , selecione o **Guia de transição do designer de exibição** para saber mais sobre suas opções ou selecione um dos modelos predefinidos.

![Modelos de exemplo](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Habilitando o filtro de intervalo de tempo
O designer de exibição tem um filtro de intervalo de tempo padrão interno, no entanto, em pastas de trabalho essa configuração não está habilitada por padrão. As pastas de trabalho permitem que os usuários criem seus próprios filtros de intervalo de tempo que podem ser mais aplicáveis aos seus logs de dados. As etapas para gerar o filtro estão listadas abaixo:

Selecione a opção **adicionar parâmetros** . O **estilo** padrão é definido como *pills*.

![Adicionar parâmetro](media/view-designer-conversion-tasks/add-param.png)

 Selecione o botão **Adicionar parâmetro** .

![Adicionar parâmetro](media/view-designer-conversion-tasks/add-parameter.png)

No menu da barra lateral, na caixa de texto **nome do parâmetro** , digite *timerange*. Defina o **tipo de parâmetro** como *seletor de intervalo de tempo*. Selecione a caixa de seleção **necessário?** .

![Menu de parâmetros](media/view-designer-conversion-tasks/parameter-menu.png)

Salve o parâmetro no canto superior esquerdo do menu da barra lateral. Você pode deixar o menu suspenso *como não* definido por padrão ou selecionar um valor de **intervalo** de tempo padrão, como *24 horas*. Selecione **edição concluída**.

Os parâmetros podem ser usados em consultas adicionando chaves {} ao seu nome de parâmetro. Mais detalhes sobre os parâmetros podem ser encontrados na [documentação de pastas de trabalho em parâmetros](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Atualizando consultas com o parâmetro timerange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opção 1: selecione timerange na lista suspensa intervalo de tempo

![Parâmetro de tempo](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opção 2: atualizar suas consultas de log

Em sua consulta, adicione a linha: `| where TimeGenerated {TimeRange}` como no exemplo a seguir:

Consulta original
```KQL
search * 
| summarize count() by Type
```

Consulta atualizada
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Incluindo uma lista
A maioria das exibições do designer de exibição inclui uma lista e você pode reproduzir essa lista padrão em uma pasta de trabalho.

![Lista de blocos](media/view-designer-conversion-tasks/tile-list.png)

Adicione uma visualização clicando em **Adicionar consulta** nas opções de célula.

![Adicionar parâmetro](media/view-designer-conversion-tasks/add-param.png)

O designer de exibição emprega uma consulta padrão que corresponde à sintaxe do exemplo original. Isso pode ser atualizado alterando a consulta para o formulário atualizado, como no exemplo a seguir:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Type
```

Isso irá gerar uma lista parecida com a seguinte:

![Exemplo de lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Habilitando minigráficos
Um recurso comum para grades é adicionar minigráficos para resumir vários padrões de dados ao longo do tempo. O designer de exibição oferece o recurso **habilitar minigráficos** para todas as listas, assim como as pastas de trabalho. Para incluir minigráficos em seus dados que correspondam ao designer de exibição, ingresse os dados com sua consulta original, como no exemplo a seguir:

Consulta original
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selecione **configurações de coluna**.
![Configurações de coluna](media/view-designer-conversion-tasks/column-settings.png)

Atualize a lista suspensa de **renderizador de coluna** para ser uma *área do Spark*.
![Minigráficos](media/view-designer-conversion-tasks/sparkline.png)

Salve as configurações e execute a consulta novamente para atualizar sua tabela para incluir um minigráfico.

A grade resultante será semelhante ao seguinte: ![ exemplo de Minigráfico](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Configurações avançadas de célula
Para o designer de exibição de espelho, você pode executar tarefas como alterar o tamanho das células da pasta de trabalho ou adicionar Pins e links externos aos logs.

Para acessar **as configurações avançadas** , selecione o ícone de engrenagem na parte inferior de cada célula.

![Configurações avançadas](media/view-designer-conversion-tasks/advanced-settings.png)

Isso exibirá um menu com várias opções:

![Configurações avançadas](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Para adicionar um PIN e um link a uma consulta externa, selecione as caixas de seleção correspondentes. Para adicionar um título à célula, digite o título desejado na seção **título do gráfico** .

Por padrão, qualquer célula de pastas de trabalho é definida para ocupar toda a largura da página, mas você pode ajustá-la ao dimensionar a célula para baixo na guia **estilo** do menu **Configurações avançadas**

![Estilo de configurações avançadas](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parâmetros adicionais
Selecione **Adicionar parâmetro** para criar um novo parâmetro na pasta de trabalho. 

Para selecionar uma assinatura, digite *assinatura* no campo **nome do parâmetro** no menu lateral e selecione *seletor de assinatura* na lista suspensa **tipo de parâmetro**

![Menu assinatura](media/view-designer-conversion-tasks/subscription-filter.png)

Para selecionar um recurso, digite *recurso* no campo **nome do parâmetro** no menu lateral e selecione *seletor de recursos* na lista suspensa **tipo de parâmetro** .

![Menu de recursos](media/view-designer-conversion-tasks/resource-filter.png)

Isso irá inserir listas suspensas para permitir que você acesse suas várias assinaturas e recursos.

![Lista suspensa de recursos de assinatura](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Próximas etapas
- [Conversões de blocos](view-designer-conversion-tiles.md)
