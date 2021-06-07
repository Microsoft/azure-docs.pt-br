---
title: Visão geral de diagnóstico do Azure Functions
description: Saiba como solucionar problemas com seu aplicativo de funções com o diagnóstico do Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122351"
---
# <a name="azure-functions-diagnostics-overview"></a>Visão geral de diagnóstico do Azure Functions

Quando você estiver executando um aplicativo de funções, convém estar preparado para quaisquer problemas que possam surgir, desde erros 4xx até falhas de disparo. O diagnóstico do Azure Functions é uma experiência interativa e inteligente para ajudar você a solucionar problemas de seu aplicativo de funções sem configuração nem custo adicional. Quando você encontrar problemas com seu aplicativo de funções, o diagnóstico do Azure Functions indicará o que está errado. Ele conduz você às informações certas para solucionar o problema com mais facilidade e rapidez. Este artigo mostra as noções básicas de como usar o diagnóstico do Azure Functions para diagnosticar e resolver mais rapidamente problemas comuns de aplicativos de funções.

## <a name="start-azure-functions-diagnostics"></a>Iniciar o diagnóstico do Azure Functions

Para iniciar o diagnóstico do Azure Functions:

1. Navegue até o seu aplicativo de funções no [portal do Azure](https://portal.azure.com).
1. Selecione **Diagnosticar e resolver problemas** para abrir o diagnóstico do Azure Functions.
1. Escolha uma categoria que melhor descreve o problema do seu aplicativo de funções usando as palavras-chave no bloco home page. Você também pode digitar uma palavra-chave que melhor descreve seu problema na barra de pesquisa. Por exemplo, você pode digitar `execution` para ver uma lista de relatórios de diagnóstico relacionados à sua execução de aplicativo de funções e abri-los diretamente da home page.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Procurar diagnóstico do Azure Functions." border="true":::

## <a name="use-the-interactive-interface"></a>Usar a interface interativa

Depois de selecionar uma categoria de home page que melhor se alinha com o problema do seu aplicativo de funções, a interface interativa do diagnóstico do Azure Functions, chamada de Gênio, pode orientá-lo no diagnóstico e na resolução do problema de seu aplicativo. Você pode usar os atalhos de bloco fornecidos pelo Gênio para exibir o relatório de diagnóstico completo da categoria de problema em que você está interessado. Os atalhos de bloco fornecem uma forma direta de acessar suas métricas de diagnóstico.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="O Gênio é a interface do diagnóstico do Azure Functions." border="false":::

Depois de selecionar um bloco, você poderá ver uma lista de tópicos relacionados ao problema descrito no bloco. Esses tópicos fornecem trechos de informações importantes do relatório completo. Selecione qualquer um desses tópicos para investigar os problemas em mais detalhes. Além disso, você pode selecionar **Exibir relatório completo** para explorar todos os tópicos em apenas uma página.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Visualização do relatório de diagnóstico" border="false":::

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de escolher um tópico, você pode exibir um relatório de diagnóstico específico para seu aplicativo de funções. Os relatórios de diagnóstico usam ícones de status para indicar se há problemas específicos com seu aplicativo. Você verá uma descrição detalhada do problema, ações recomendadas, métricas relacionadas e documentos úteis. Relatórios de diagnóstico personalizados são gerados de uma série de verificações executadas em seu aplicativo de funções. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas em seu aplicativo de funções e orientá-lo para resolver o problema.

## <a name="find-the-problem-code"></a>Encontrar o código com problema

Para funções baseadas em script, você pode usar a **Execução de Função e Erros** em **Aplicativo de Funções Inoperante ou Relatando Erros** para restringir a linha de código que está causando exceções ou erros. Você pode usar essa ferramenta para obter a causa raiz e corrigir problemas de uma linha de código específica. Essa opção não está disponível para funções pré-compiladas de C# e Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Relatório de diagnóstico sobre erros de execução de função" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Exibição dos detalhes da exceção." border="false":::

## <a name="next-steps"></a>Próximas etapas

Você pode fazer perguntas ou fornecer comentários sobre o diagnóstico de Azure Functions em [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Inclua `[Diag]` no título dos seus comentários.

> [!div class="nextstepaction"]
> [Monitorar seus aplicativos de funções](functions-monitoring.md)
