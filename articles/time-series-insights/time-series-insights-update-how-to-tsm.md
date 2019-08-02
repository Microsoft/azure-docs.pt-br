---
title: Modelagem de dados na Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entenda a modelagem de dados na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461086"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na Versão Prévia do Azure Time Series Insights

Este documento descreve como trabalhar com Modelos de Série Temporal seguindo a Versão Prévia do Azure Time Series Insights. Detalha vários cenários comuns de dados.

Para saber mais sobre como usar a atualização, leia [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Tipos** no menu. Recolha o painel para se concentrar nos tipos de Modelos de Série Temporal.

    [![Criar um único tipo](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecione **Adicionar**.
1. Insira todos os detalhes referentes a tipos e selecione **Criar**. Essa ação cria tipos no ambiente.

    [![Adicionar um tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de tipo.
1. Escolha **Carregar**.

    [![Carregar o JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Editar um tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione **Excluir**.
1. Se nenhuma instância estiver associada aos tipos, eles serão excluídos.

    [![Excluir um tipo](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Hierarquias** no menu. Recolha o painel para se concentrar nas hierarquias de Modelos de Série Temporal.

    [![Selecione hierarquias](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecione **Adicionar**.

    [![Adicionar uma hierarquia](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecione **Adicionar Nível** no painel direito.

    [![Adicionar um nível](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Insira os detalhes de hierarquia e selecione **Criar**.

    [![Criar um nível](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de hierarquia.
1. Escolha **Carregar**.

    [![Hierarquias de carregamento em massa](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione **Editar**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Editar uma única hierarquia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione **Excluir**. 
1. Se nenhuma instância estiver associada à hierarquia, ela será excluída.

    [![Excluir uma hierarquia](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Instâncias** no menu. Recolha o painel para se concentrar nas instâncias de Modelos de Série Temporal.

    [![Criar uma única instância](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecione **Adicionar**.

    [![Adicionar uma instância](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Insira os detalhes da instância, selecione a associação de tipo e hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo das instâncias.

    [![Uma ou mais instâncias de carregamento em massa](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Escolha **Carregar**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Editar uma única instância](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre Modelos de Série Temporal, leia [Modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Para saber mais sobre formas JSON com suporte, leia [Formas JSON com suporte](./time-series-insights-send-events.md#json).
