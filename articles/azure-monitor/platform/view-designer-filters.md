---
title: Filtros em exibições do Azure Monitor | Microsoft Docs
description: Um filtro em uma exibição do Azure Monitor permite que os usuários filtrem os dados no modo de exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551681"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros em exibições do Azure Monitor
Um **filtro** em um [modo de exibição do Azure Monitor](view-designer.md) permite que os usuários filtrem os dados no modo de exibição pelo valor de uma propriedade específica sem modificar a exibição em si.  Por exemplo, você pode permitir que os usuários da sua exibição filtrem somente os dados de um computador ou conjunto de computadores específico.  É possível criar vários filtros em uma única exibição para permitir que os usuários filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.

## <a name="using-a-filter"></a>Como usar um filtro
Clique no intervalo de tempo de data na parte superior de uma exibição para abrir a lista suspensa onde você pode alterar o intervalo de tempo de data para o modo de exibição.

![Exemplo de filtro](media/view-designer-filters/filters-example-time.png)

Clique em **+** para adicionar um filtro usando filtros personalizados que são definidos para a visualização. Selecione um valor para o filtro no menu suspenso ou digite um valor. Continue a adicionar filtros clicando em **+**. 


![Exemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Se você remover todos os valores de um filtro, ele não será aplicado.


## <a name="creating-a-filter"></a>Como criar um filtro

Crie um filtro por meio da guia **Filtros** ao [editar uma exibição](view-designer.md).  O filtro é global para a exibição e se aplica a todas as partes da exibição.  

![Configurações de filtro](media/view-designer-filters/filters-settings.png)

A tabela a seguir descreve as configurações de um filtro.

| Configuração | DESCRIÇÃO |
|:---|:---|
| Nome do campo | Nome do campo usado para filtragem.  Este campo deve corresponder ao campo summarize na **consulta de valores**. |
| Consulta de valores | Consulta que deve ser executada para popular a lista suspensa do filtro para o usuário.  Essa consulta deve usar o [resumir](/azure/kusto/query/summarizeoperator) ou [distintos](/azure/kusto/query/distinctoperator) para fornecer valores exclusivos para um determinado campo e ele devem corresponder a **nome do campo**.  Você pode usar [sort](/azure/kusto/query/sortoperator) para classificar os valores que são exibidos para o usuário. |
| Marca | Nome do campo que é usado em consultas de apoio do filtro e que também é exibido para o usuário. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do campo | Consulta de valores | Marca |
|:--|:--|:--|
| Computador   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computadores |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificar as consultas de exibição

Para que um filtro tenha efeito, é necessário modificar todas as consultas na exibição para filtrar os valores selecionados.  Se você não modificar todas as consultas no modo de exibição, em seguida, quaisquer valores que o usuário seleciona não terá efeito.

A sintaxe para usar um valor de filtro em uma consulta é: 

    where ${filter name}  

Por exemplo, se o modo de exibição tem uma consulta que retorna os eventos e usa um filtro chamado _computadores_, você pode usar a consulta a seguir.

    Event | where ${Computers} | summarize count() by EventLevelName

Se você adicionar outro filtro chamado Gravidade, você poderá usar a consulta a seguir para usar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [Partes da Visualização](view-designer-parts.md) que você pode adicionar ao modo de exibição personalizado.
