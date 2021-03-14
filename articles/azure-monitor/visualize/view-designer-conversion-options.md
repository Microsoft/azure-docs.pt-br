---
title: Opções de conversão do designer de exibição para pastas de trabalho do Azure Monitor
description: Opções de conversão para transição de exibições para pastas de trabalho no Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b8b6b8b41c729c3cbb6cf4589d679e93149e5314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043381"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Opções de conversão do designer de exibição para pastas de trabalho do Azure Monitor
O [Designer de exibição](view-designer.md) é um recurso do Azure monitor que permite que você crie modos de exibição personalizados para ajudá-lo a Visualizar dados em seu espaço de trabalho do log Analytics, com gráficos, listas e linhas do tempo. Eles estão sendo desativados e substituídos por pastas de trabalho que fornecem funcionalidade adicional. Este artigo compara os conceitos fundamentais entre as duas opções para converter exibições em pastas de trabalho.

## <a name="basic-workbook-designs"></a>Designs de pasta de trabalho básica

O designer de exibição tem um estilo estático fixo de representação, enquanto as pastas de trabalho habilitam a liberdade para incluir e modificar o modo como os dados são representados. As imagens a seguir descrevem dois exemplos de como você pode organizar as pastas de trabalho ao converter exibições.

[Pasta de trabalho vertical](view-designer-conversion-examples.md#vertical) 
 ![ Vertical](media/view-designer-conversion-options/view-designer-vertical.png)

[Pasta de trabalho](view-designer-conversion-examples.md#tabbed) 
 ![ com guias Tipos de dados guia distribuição tipo de ](media/view-designer-conversion-options/distribution-tab.png)
 ![ dados na guia tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversão de bloco
O designer de exibição usa o recurso de bloco visão geral para representar e resumir o estado geral. Elas são representadas em sete blocos, variando de números a gráficos. Em pastas de trabalho, os usuários podem criar visualizações semelhantes e fixá-las para se parecer com o estilo original dos blocos de visão geral. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Exibir conversão de painel
Os blocos de designer de exibição geralmente consistem em duas seções, uma visualização e uma lista que corresponde aos dados da visualização, por exemplo, o bloco de **& de rosca**

![Donut](media/view-designer-conversion-options/donut-example.png)

Com as pastas de trabalho, permitimos que o usuário opte por consultar uma ou ambas as seções da exibição. As consultas do formular em pastas de trabalho são um processo simples de duas etapas. Primeiro, os dados são gerados a partir da consulta e, segundo, os dados são renderizados como uma visualização.  Um exemplo de como essa exibição seria recriada em pastas de trabalho é a seguinte:

![Converter](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Próximas etapas
- [Acessando pastas de trabalho & permissões](view-designer-conversion-access.md)