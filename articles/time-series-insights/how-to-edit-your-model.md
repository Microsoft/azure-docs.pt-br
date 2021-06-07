---
title: Modelagem de dados em ambientes Gen2 – Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre a modelagem de dados no Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016846"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Modelagem de dados no Azure Time Series Insights Gen2

Este artigo descreve como trabalhar com o modelo de série temporal no Azure Time Series Insights Gen2. Detalha vários cenários comuns de dados.

> [!TIP]
>
> * Leia mais sobre o [modelo de série temporal](concepts-model-overview.md).
> * Saiba mais sobre como navegar no [Azure Time Series insights Explorer Gen2](./concepts-ux-panels.md).

## <a name="instances"></a>Instâncias

O Azure Time Series Insights Explorer dá suporte a operações de **criação**, **leitura**, **atualização** e **exclusão** no navegador.

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do Azure Time Series insights Explorer.

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel seletor de modelo de série temporal e selecione **instâncias** no menu. Todas as instâncias associadas ao ambiente de Azure Time Series Insights selecionado serão exibidas.

    [![Crie uma única instância selecionando primeiro as instâncias.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Adicione uma instância selecionando o botão + Adicionar.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Insira os detalhes da instância, selecione a associação de tipo e hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

> [!TIP]
> Você pode salvar suas instâncias em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo das instâncias.

    [![Instâncias de carregamento em massa por meio de JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Escolha **Carregar**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione o ícone **Editar** ou **lápis**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Editar uma única instância.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Excluir uma instância

1. Selecione a instância e selecione o ícone **excluir** ou **perder lixeira**.

   [![Exclua uma instância selecionando excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

> [!NOTE]
> Uma instância do deve passar uma verificação de validação de campo com êxito para ser excluída.

## <a name="hierarchies"></a>Hierarquias

O Azure Time Series Insights Explorer dá suporte a operações de **criação**, **leitura**, **atualização** e **exclusão** de hierarquia no navegador.

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do Azure Time Series insights Explorer.

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel seletor de modelo de série temporal e selecione **hierarquias** no menu. Todas as hierarquias associadas ao ambiente de Azure Time Series Insights selecionado serão exibidas.

    [![Crie uma hierarquia por meio do painel.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Hierarquia + adicionar botão.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecione **+ Adicionar nível** no painel direito.

    [![Adicione um nível à hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Insira os detalhes da hierarquia e selecione **salvar**.

    [![Especifique os detalhes da hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

> [!TIP]
> Você pode salvar suas hierarquias em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de hierarquia.
1. Escolha **Carregar**.

    [![Seleções para o carregamento em massa de hierarquias.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione o ícone **Editar** ou **lápis**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Seleções para editar uma única hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione o ícone **excluir** ou **perder lixeira**.

    [![Exclua uma hierarquia selecionando o botão excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

## <a name="types"></a>Tipos

O Azure Time Series Insights Explorer dá suporte ao tipo **criar**, **ler**, **Atualizar** e **excluir** operações no navegador.

Para começar, selecione o modo de exibição de **modelo** na exibição de **análise** do Azure Time Series insights Explorer.

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel seletor de modelo de série temporal e selecione **tipos** no menu. Todos os tipos associados ao ambiente de Azure Time Series Insights selecionado serão exibidos.

    [![Painel de tipos de modelo de série temporal.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecione **+ Adicionar** para exibir a janela pop-up **Adicionar um novo tipo** .
1. Insira Propriedades e variáveis para seu tipo. Depois de inserida, selecione **salvar**.

    [![Definições de configuração para adicionar um tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

> [!TIP]
> Você pode salvar seus tipos em sua área de trabalho em JSON. O arquivo JSON baixado pode ser carregado por meio das etapas a seguir.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de tipo.
1. Escolha **Carregar**.

    [![Opções de carregamento de tipos em massa.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione o ícone **Editar** ou **lápis**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edite um tipo no painel.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione o ícone **excluir** ou **perder lixeira**.

   [![Exclua um tipo selecionando excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirme a exclusão selecionando **excluir**.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o modelo de série temporal, leia [modelagem de dados](./concepts-model-overview.md).

* Para saber mais sobre o Gen2, leia [Visualizar dados no Azure Time Series insights Gen2 Explorer](./concepts-ux-panels.md).

* Para saber mais sobre as formas JSON com suporte, leia [formas de JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).