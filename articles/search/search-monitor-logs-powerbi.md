---
title: Visualize os logs e as métricas do Azure Pesquisa Cognitiva com Power BI
description: Visualize os logs e as métricas do Azure Pesquisa Cognitiva com Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 4056e892855c06ce6c412ec4a592ebcd97fc11a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325376"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualize os logs e as métricas do Azure Pesquisa Cognitiva com Power BI
O [Azure pesquisa cognitiva](./search-what-is-azure-search.md) permite que você armazene logs de operação e métricas de serviço sobre o serviço de pesquisa em uma conta de armazenamento do Azure. Esta página fornece instruções sobre como você pode visualizar essas informações por meio de um aplicativo de modelo de Power BI. O aplicativo fornece informações detalhadas sobre o seu serviço de pesquisa, incluindo informação sobre pesquisa, indexação, operações e métricas de serviço.

Você pode encontrar o aplicativo de modelo de Power BI **pesquisa cognitiva do Azure: analisar logs e métricas** no [marketplace de aplicativos Power bi](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Como começar a usar o aplicativo

1. Habilite o log de recursos e métricas para o serviço de pesquisa:

    1. Criar ou identificar uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md) existente onde você pode arquivar os logs
    1. Navegue até o serviço de Pesquisa Cognitiva do Azure no portal do Azure
    1. Na seção monitoramento, na coluna à esquerda, selecione **configurações de diagnóstico**

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Captura de tela mostrando como selecionar as configurações de diagnóstico na seção monitoramento do serviço de Pesquisa Cognitiva do Azure." border="false":::

    1. Selecione **+ Adicionar configuração de diagnóstico**
    1. Verificar o **arquivamento em uma conta de armazenamento**, fornecer as informações da conta de armazenamento e verificar **OperationLogs** e **biométricas**

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Captura de tela mostrando como fazer seleções para métricas e log de recursos na página Configurações de diagnóstico.":::
    1. Selecione **Salvar**

1. Depois que o registro em log tiver sido habilitado, use o serviço de pesquisa para começar a gerar logs e métricas. Leva até uma hora antes que os contêineres apareçam no armazenamento de BLOBs com esses logs. Você verá um contêiner **insights-logs-operationlogs** para os logs de tráfego de pesquisa e um contêiner **insights-Metrics-PT1M** para métricas.

1. Localize o aplicativo de Power BI de Pesquisa Cognitiva do Azure no [Marketplace de aplicativos Power bi](https://appsource.microsoft.com/marketplace/apps) e instale-o em um novo espaço de trabalho ou em um espaço de trabalho existente. O aplicativo é chamado de **pesquisa cognitiva do Azure: analisar logs e métricas**.

1. Depois de instalar o aplicativo, selecione o aplicativo na lista de aplicativos no Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Captura de tela mostrando o aplicativo de Pesquisa Cognitiva do Azure para selecionar na lista de aplicativos.":::

1. Selecione **conectar** para conectar seus dados

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Captura de tela mostrando como se conectar aos seus dados no aplicativo Pesquisa Cognitiva do Azure.":::

1. Insira o nome da conta de armazenamento que contém os logs e as métricas. Por padrão, o aplicativo examinará os últimos 10 dias de dados, mas esse valor pode ser alterado com o parâmetro **Days** .

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Captura de tela mostrando como inserir o nome da conta de armazenamento e o número de dias a serem consultados na página conectar ao Azure Pesquisa Cognitiva.":::

1. Selecione a **chave** como o método de autenticação e forneça sua chave de conta de armazenamento. Selecione **privado** como o nível de privacidade. Clique em Entrar para iniciar o processo de carregamento.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Captura de tela mostrando como inserir o método de autenticação, a chave de conta e o nível de privacidade na página conectar ao Azure Pesquisa Cognitiva.":::

1. Aguarde até que os dados sejam atualizados. Isso pode levar algum tempo, dependendo da quantidade de dados que você tem. Você pode ver se os dados ainda estão sendo atualizados com base no indicador abaixo.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Captura de tela mostrando como ler as informações na página atualização de dados.":::

1. Depois que a atualização de dados for concluída, selecione **relatório de pesquisa cognitiva do Azure** para exibir o relatório.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Captura de tela mostrando como selecionar o relatório de Pesquisa Cognitiva do Azure na página atualização de dados.":::

1. Certifique-se de atualizar a página depois de abrir o relatório para que ele seja aberto com seus dados.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Captura de tela do relatório de Power BI do Pesquisa Cognitiva do Azure.":::

## <a name="how-to-change-the-app-parameters"></a>Como alterar os parâmetros do aplicativo
Se você quiser Visualizar dados de uma conta de armazenamento diferente ou alterar o número de dias de dados a serem consultados, siga as etapas abaixo para alterar os parâmetros de **dias** e **StorageAccount** .

1. Navegue até seu Power BI aplicativos, localize o aplicativo de Pesquisa Cognitiva do Azure e selecione o botão **Editar aplicativo** para exibir o espaço de trabalho.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Captura de tela mostrando como selecionar o botão Editar aplicativo para o aplicativo Pesquisa Cognitiva do Azure.":::

1. Selecione **configurações** nas opções de conjunto de

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Captura de tela mostrando como selecionar configurações nas opções do conjunto de Pesquisa Cognitiva do Azure.":::

1. Na guia conjuntos de valores, altere os valores de parâmetro e selecione **aplicar**. Se houver um problema com a conexão, atualize as credenciais da fonte de dados na mesma página.

1. Navegue de volta para o espaço de trabalho e selecione **Atualizar agora** nas opções de conjunto de

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Captura de tela mostrando como selecionar atualizar agora nas opções do conjunto de Pesquisa Cognitiva do Azure.":::

1. Abra o relatório para exibir os dados atualizados. Talvez você também precise atualizar o relatório para exibir os dados mais recentes.

## <a name="troubleshooting"></a>Solução de problemas
Se você achar que não consegue ver seus dados, siga estas etapas de solução de problemas:

1. Abra o relatório e atualize a página para certificar-se de que você está exibindo os dados mais recentes. Há uma opção no relatório para atualizar os dados. Selecione esta seleção para obter os dados mais recentes.

1. Verifique se o nome da conta de armazenamento e a chave de acesso fornecidos estão corretos. O nome da conta de armazenamento deve corresponder à conta configurada com os logs do serviço de pesquisa.

1. Confirme se sua conta de armazenamento contém os contêineres **insights-logs-operationlogs** e **insights – Metrics-PT1M** e cada contêiner tem dados. Os logs e as métricas estarão dentro de duas camadas de pastas.

1. Verifique se o conjunto de um DataSet ainda está sendo atualizado. O indicador de status de atualização é mostrado na etapa 8 acima. Se ainda estiver atualizando, aguarde até que a atualização seja concluída para abrir e atualizar o relatório.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Azure Pesquisa Cognitiva](./index.yml)

[O que é o Power BI?](/power-bi/fundamentals/power-bi-overview)

[Conceitos básicos para designers no serviço do Power BI](/power-bi/service-basic-concepts)