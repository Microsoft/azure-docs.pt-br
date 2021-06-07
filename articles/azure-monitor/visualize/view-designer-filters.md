---
title: Filtros em exibições de Azure Monitor | Microsoft Docs
description: Um filtro em uma exibição de Azure Monitor permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 988bb1467e7bf2cca31151d85c57677b235cab30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043296"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros em exibições de Azure Monitor
Um **filtro** em uma [exibição de Azure monitor](view-designer.md) permite que os usuários filtrem os dados na exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Por exemplo, você pode permitir que os usuários da sua exibição filtrem somente os dados de um computador ou conjunto de computadores específico.  É possível criar vários filtros em uma única exibição para permitir que os usuários filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.

## <a name="using-a-filter"></a>Como usar um filtro
Clique no intervalo de data e hora na parte superior de uma exibição para abrir o menu suspenso onde você pode alterar o intervalo de data e hora da exibição.

![Captura de tela do menu suspenso intervalo de tempo para uma exibição em Azure Monitor, mostrando o botão de opção nos últimos sete dias selecionados.](media/view-designer-filters/filters-example-time.png)

Clique em **+** para adicionar um filtro usando filtros personalizados que são definidos para a exibição. Selecione um valor para o filtro no menu suspenso ou digite um valor. Continue a adicionar filtros clicando no **+** . 


![Captura de tela da caixa de diálogo para adicionar um filtro personalizado no Azure Monitor. A propriedade Computers está sendo selecionada no menu suspenso selecionar propriedade.](media/view-designer-filters/filters-example-custom.png)

Se você remover todos os valores de um filtro, ele não será aplicado.


## <a name="creating-a-filter"></a>Como criar um filtro

Crie um filtro por meio da guia **Filtros** ao [editar uma exibição](view-designer.md).  O filtro é global para a exibição e se aplica a todas as partes da exibição.  

![Configurações de filtro](media/view-designer-filters/filters-settings.png)

A tabela a seguir descreve as configurações de um filtro.

| Configuração | Descrição |
|:---|:---|
| Nome do campo | Nome do campo usado para filtragem.  Este campo deve corresponder ao campo resumir na **consulta de valores**. |
| Consulta de valores | Consulta que deve ser executada para popular a lista suspensa do filtro para o usuário.  Essa consulta deve usar [resumir](/azure/kusto/query/summarizeoperator) ou [DISTINCT](/azure/kusto/query/distinctoperator) para fornecer valores exclusivos para um campo específico e deve corresponder ao nome do **campo**.  Você pode usar [sort](/azure/kusto/query/sortoperator) para classificar os valores que são exibidos para o usuário. |
| Marca | Nome do campo que é usado em consultas de apoio do filtro e que também é exibido para o usuário. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do campo | Consulta de valores | Marca |
|:--|:--|:--|
| Computador   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computadores |
| EventLevelName | Event &#124; distinct EventLevelName | Severidade |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severidade |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificar as consultas de exibição

Para que um filtro tenha efeito, é necessário modificar todas as consultas na exibição para filtrar os valores selecionados.  Se você não modificar nenhuma consulta na exibição, os valores que o usuário selecionar não terão nenhum efeito.

A sintaxe para usar um valor de filtro em uma consulta é: 

`where ${filter name}`  

Por exemplo, se o modo de exibição tiver uma consulta que retorne eventos e usar um filtro chamado _computadores_, você poderá usar a consulta a seguir.

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Se você adicionar outro filtro chamado Gravidade, você poderá usar a consulta a seguir para usar ambos os filtros.

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [Partes da Visualização](view-designer-parts.md) que você pode adicionar ao modo de exibição personalizado.
