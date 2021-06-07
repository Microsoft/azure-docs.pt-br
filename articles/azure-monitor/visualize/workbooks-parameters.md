---
title: Azure Monitor pastas de trabalho criando parâmetros
description: Saiba como os parâmetros permitem que os autores da pasta de trabalho coletem a entrada dos consumidores e faça referência a ele em outras partes da pasta de trabalho.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700551"
---
# <a name="workbook-parameters"></a>Parâmetros da pasta de trabalho

Os parâmetros permitem que os autores da pasta de trabalho coletem a entrada dos consumidores e façam referência a ele em outras partes da pasta de trabalho – geralmente para definir o escopo do conjunto de resultados ou definir o Visual correto. É um recurso importante que permite aos autores criar relatórios e experiências interativos. 

As pastas de trabalho permitem que você controle como os controles de parâmetro são apresentados aos consumidores – caixa de texto versus lista suspensa, Single-vs. multisseleção, valores de texto, JSON, KQL ou grafo de recursos do Azure, etc.  

Os tipos de parâmetro com suporte incluem:
* [Hora](workbooks-time.md) – permite que um usuário selecione intervalos de tempo preenchidos previamente ou selecione um intervalo personalizado
* [Lista suspensa](workbooks-dropdowns.md) – permite que um usuário selecione um valor ou um conjunto de valores
* [Texto](workbooks-text.md) – permite que um usuário insira texto arbitrário
* [Recurso](workbooks-resources.md) – permite que um usuário selecione um ou mais recursos do Azure
* [Assinatura](workbooks-resources.md) – permite que um usuário selecione um ou mais recursos de assinatura do Azure
* Tipo de recurso – permite que um usuário selecione um ou mais valores de tipo de recurso do Azure
* Local – permite que um usuário selecione um ou mais valores de local do Azure

Esses valores de parâmetro podem ser referenciados em outras partes de pastas de trabalho por meio de associações ou expansões de valor.

## <a name="creating-a-parameter"></a>Criando um parâmetro
1. Comece com uma pasta de trabalho vazia no modo de edição.
2. Escolha _adicionar parâmetros_ nos links na pasta de trabalho.
3. Clique no botão azul _Adicionar parâmetro_ .
4. No novo painel de parâmetros que aparece, digite:
    1. Nome do parâmetro: `TimeRange` *(Observe que os __nomes__ de parâmetros **não podem** incluir espaços ou caracteres especiais)*
    2. Nome de exibição: `Time Range` *(no entanto, os __nomes de exibição__ podem incluir espaços, caracteres especiais, Emoji, etc.)*  
    2. Tipo de parâmetro: `Time range picker`
    3. Necessário: `checked`
    4. Intervalos de tempo disponíveis: última hora, últimas 12 horas, últimas 24 horas, últimas 48 horas, últimos 3 dias, últimos 7 dias e permitir seleção de intervalo de tempo personalizado
5. Escolha ' salvar ' na barra de ferramentas para criar o parâmetro.

   ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-parameters/time-settings.png)

É assim que a pasta de trabalho se parecerá no modo de leitura, no estilo "pills".

   ![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Referenciando um parâmetro
### <a name="via-bindings"></a>Via associações
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. Abra a lista suspensa _intervalo de tempo_ e selecione a `Time Range` opção na seção parâmetros na parte inferior.
3. Isso associa o parâmetro de intervalo de tempo ao intervalo de tempo do gráfico. O escopo de tempo da consulta de exemplo agora é as últimas 24 horas.
4. Execute a consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado por meio de associações](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à pasta de trabalho e selecione um recurso de Application Insights.
2. No KQL, insira um filtro de escopo de tempo usando o parâmetro: `| where timestamp {TimeRange}`
3. Isso expande o tempo de avaliação de consulta para `| where timestamp > ago(1d)` , que é o valor de intervalo de tempo do parâmetro.
4. Execute a consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Em texto 
1. Adicione um controle de texto à pasta de trabalho.
2. Na redução, insira `The chosen time range is {TimeRange:label}`
3. Escolha a _edição concluída_
4. O controle de texto mostrará o texto: _o intervalo de tempo escolhido é das últimas 24 horas_

## <a name="parameter-options"></a>Opções de parâmetro
A seção _no texto_ usou o `label` do parâmetro em vez de seu valor. Os parâmetros expõem várias opções tais, dependendo de seu tipo-por exemplo, seletores de intervalo de tempo permitem valor, rótulo, consulta, início, término e granulação.

Use a `Previews` seção do painel _Editar parâmetro_ para ver as opções de expansão para seu parâmetro:

![Imagem mostrando as opções de parâmetro de intervalo de tempo](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Próximas etapas

* [Comece a aprender mais](./workbooks-overview.md#visualizations) sobre pastas de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e compartilhe o acesso aos recursos da pasta de trabalho.