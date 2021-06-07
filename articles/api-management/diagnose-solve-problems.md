---
title: Gerenciamento de API do Azure diagnosticar e resolver problemas
description: Saiba como solucionar problemas com sua API no gerenciamento de API do Azure com a ferramenta de diagnóstico e resolução no portal do Azure.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652395"
---
# <a name="azure-api-management-diagnostics-overview"></a>Visão geral do diagnóstico de gerenciamento de API do Azure

Quando você cria e gerencia uma API no gerenciamento de API do Azure, você deseja estar preparado para quaisquer problemas que possam surgir, de 404 erros não encontrados a 502 erro de gateway inadequado. O diagnóstico de gerenciamento de API é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas de sua API publicada no APIM sem nenhuma configuração necessária. Quando você tiver problemas com suas APIs publicadas, o diagnóstico de gerenciamento de API indicará o que está errado e o orientará nas informações corretas para solucionar problemas e resolver o problema rapidamente.

Embora essa experiência seja mais útil quando você tiver problemas com sua API nas últimas 24 horas, todos os gráficos de diagnóstico estarão sempre disponíveis para você analisar.

## <a name="open-api-management-diagnostics"></a>Abrir o diagnóstico de gerenciamento de API

Para acessar o diagnóstico de gerenciamento de API, navegue até a instância do serviço de gerenciamento de API no [portal do Azure](https://portal.azure.com). No painel de navegação esquerdo, selecione **diagnosticar e resolver problemas**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Captura de tela mostra como navegar até diagnósticos.":::



## <a name="intelligent-search"></a>Pesquisa inteligente

Você pode pesquisar seus problemas ou problemas na barra de pesquisa na parte superior da página. A pesquisa também ajuda a encontrar as ferramentas que podem ajudar a solucionar problemas. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="captura de tela da pesquisa inteligente.":::


## <a name="troubleshooting-categories"></a>Categorias de solução de problemas

Você pode solucionar problemas em categorias. Os problemas comuns relacionados ao desempenho da API, ao gateway, às políticas de API e à camada de serviço podem ser analisados em cada categoria. Cada categoria também fornece verificações de diagnóstico mais específicas. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="captura de tela da categoria visão geral.":::


### <a name="availability-and-performance"></a>Disponibilidade e desempenho

Verifique a disponibilidade da API e os problemas de desempenho nessa categoria. Depois de selecionar esse bloco de categoria, você verá algumas verificações comuns recomendadas em uma interface interativa. Clique em cada verificação para aprofundar-se nas especificações de cada problema. A verificação também fornecerá a você um gráfico mostrando o desempenho da API e um resumo dos problemas de desempenho. Por exemplo, o serviço de API pode ter tido um erro 5xx e um tempo limite na última hora no back-end. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Captura de tela 1 da verificação de interface interativa.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Captura de tela 2 da verificação de interface interativa.":::

### <a name="api-policies"></a>Políticas de API

Essa categoria detecta erros e notifica sobre seus problemas de política. 

Uma interface interativa semelhante orienta você às métricas de dados para ajudá-lo a solucionar problemas de configuração de políticas de API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="captura de tela do bloco categoria de políticas de API.":::

### <a name="gateway-performance"></a>Desempenho do gateway 

Para solicitações ou respostas de gateway ou quaisquer erros 4xx ou 5xx em seu gateway, use essa categoria para monitorar e solucionar problemas. Da mesma forma, aproveite a interface interativa para aprofundar-se na área específica que você deseja verificar quanto ao desempenho do gateway de API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="captura de tela do bloco de categoria de desempenho do gateway.":::

### <a name="service-upgrade"></a>Atualização de serviço

Essa categoria verifica qual camada de serviço (SKU) que você está usando no momento e o lembra de atualizar para evitar problemas que possam estar relacionados a essa camada. A mesma interface interativa ajuda a aprofundar-se com mais elementos gráficos e um resultado de verificação de resumo. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="captura de tela do bloco categoria de atualização de serviço.":::

## <a name="search-documentation"></a>Pesquisar documentação

Em outras ferramentas de diagnóstico e solução de problemas, você pode pesquisar a documentação de solução de problemas relacionada ao seu problema. Depois de executar o diagnóstico em seu serviço, selecione a **documentação de pesquisa** na interface interativa. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="captura de tela 1 de como usar a função de documentação de pesquisa.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="captura de tela 2 de como usar a documentação de pesquisa.":::


## <a name="next-steps"></a>Próximas etapas

* Use também a [análise de API](howto-use-analytics.md) para analisar o uso e o desempenho das APIs. 
* Deseja solucionar problemas de aplicativos Web com o diagnóstico? Leia [aqui](../app-service/overview-diagnostics.md)
* Aproveite o diagnóstico para verificar os problemas dos serviços Kubernetess do Azure. Consulte [diagnósticos em AKs](../aks/concepts-diagnostics.md)
* Poste suas perguntas ou comentários no [UserVoice](https://feedback.azure.com/forums/248703-api-management) adicionando "[diag]" no título.
