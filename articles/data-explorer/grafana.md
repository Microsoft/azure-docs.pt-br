---
title: Visualizar dados do Data Explorer do Azure usando Grafana
description: Neste tutorial, você aprenderá como configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, visualizar dados de um cluster de exemplo.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: 0f148a97b25afb9135223ff92afb898d4734c586
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537795"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizar dados do Azure Data Explorer no Grafana

O Grafana é uma plataforma de análise que permite consultar e visualizar dados e, em seguida, criar e compartilhar painéis com base nas visualizações. O Grafana fornece um *plug-in* do Azure Data Explore que permite conectar e visualizar dados do Azure Data Explorer. Neste artigo, você aprenderá como configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, visualizar dados de um cluster de exemplo.

Usando o vídeo a seguir, você pode aprender a usar o plug-in de Gerenciador de dados do Azure do Grafana, configurar o Data Explorer do Azure como uma fonte de dados para o Grafana e, em seguida, visualizar dados. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Como alternativa, você pode [configurar a fonte de dados](#configure-the-data-source) e [visualizar dados](#visualize-data) conforme detalhado no artigo abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este tutorial:

* [Grafana versão 5.3.0 ou posterior](https://docs.grafana.org/installation/) para o sistema operacional

* [Plug-in do Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para Grafana

* Um cluster que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte o [Início rápido: Criar um banco de dados e cluster do Azure Data Explorer](create-cluster-database-portal.md) e [Ingerir dados de exemplo no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Configurar a fonte de dados

Você executa as seguintes etapas para configurar o Azure Data Explorer como uma fonte de dados para Grafana. Abordaremos essas etapas com mais detalhes nesta seção:

1. Crie uma entidade de serviço do Azure AD (Azure Active Directory). A entidade de serviço é usada pelo Grafana para acessar o serviço do Azure Data Explorer.

1. Adicione a entidade de serviço do Azure AD à função *visualizadores* no banco de dados do Azure Data Explorer.

1. Especifique as propriedades de conexão do Grafana com base nas informações da entidade de serviço do Azure AD e, em seguida, teste a conexão.

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

É possível criar a entidade de serviço no [portal do Azure](#azure-portal) ou usando a experiência de linha de comando da [CLI do Azure](#azure-cli). Independentemente do método usado, após a criação você obterá valores para quatro propriedades de conexão que serão usadas em etapas posteriores.

#### <a name="azure-portal"></a>Portal do Azure

1. Para criar a entidade de serviço, siga as instruções na [documentação do portal do Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Na seção [Atribuir o aplicativo a uma função](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role), atribua um tipo de função **Leitor** ao cluster do Azure Data Explorer.

    1. Na seção [Obter valores para conectar](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), copie os três valores de propriedade abordados nas etapas: **ID de diretório** (ID do Locatário), **ID de Aplicativo** e **Senha**.

1. No portal do Azure, selecione **Assinaturas** e copie a ID da assinatura na qual você criou a entidade de serviço.

    ![ID da assinatura - portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>CLI do Azure

1. Crie uma entidade de serviço. Defina um escopo apropriado e um tipo de função `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Para obter mais informações, consulte [Criar uma entidade de serviço do Azure com CLI do Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

1. O comando retorna um conjunto de resultados semelhante ao seguinte. Copie os três valores de propriedade: **appID**, **password** e **tenant**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenha uma lista das suas assinaturas.

    ```azurecli
    az account list --output table
    ```

    Copie a ID de assinatura apropriado.

    ![ID da assinatura - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adicionar a entidade de serviço à função visualizadores

Agora que você tem uma entidade de serviço, adicione-a à função *visualizadores* no banco de dados do Azure Data Explorer. É possível executar essa tarefa em **Permissões** no portal do Azure ou em **Consultar**, usando um comando de gerenciamento.

#### <a name="azure-portal---permissions"></a>Portal do Azure - Permissões

1. No portal do Azure, acesse o cluster do Azure Data Explorer.

1. Na seção **Visão geral**, selecione o banco de dados com os dados de exemplo do StormEvents.

    ![Selecionar um banco de dados](media/grafana/select-database.png)

1. Selecione **Permissões** e, em seguida, **Adicionar**.

    ![Permissões de banco de dados](media/grafana/database-permissions.png)

1. Em **Adicionar permissões de banco de dados**, selecione o **Visualizador** e, em seguida, **Selecionar entidades**.

    ![Adicionar permissões de banco de dados](media/grafana/add-permission.png)

1. Procure a entidade de serviço que você criou (o exemplo mostra a entidade **mb-grafana**). Selecione a entidade de serviço e, em seguida **Selecionar**.

    ![Gerenciar permissões no portal do Azure](media/grafana/new-principals.png)

1. Clique em **Salvar**.

    ![Gerenciar permissões no portal do Azure](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Comando de gerenciamento - Consultar

1. No portal do Azure, acesse o cluster do Azure Data Explorer e selecione **Consultar**.

    ![Consulta](media/grafana/query.png)

1. Execute o seguinte comando na janela de consulta. Use a ID de aplicativo e o ID de locatário do portal do Azure ou CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    O comando retorna um conjunto de resultados semelhante ao seguinte. Neste exemplo, a primeira linha é para um usuário existente no banco de dados e a segunda linha é para a entidade de serviço que acabou de ser adicionada.

    ![Conjunto de resultados](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Especificar propriedades e testar a conexão

Com a entidade de serviço atribuída à função *visualizadores*, agora você especifica as propriedades na instância do Grafana e testa a conexão com Azure Data Explorer.

1. No Grafana, no menu esquerdo, selecione o ícone de engrenagem e, em seguida, **Fonte de Dados**.

    ![Fontes de dados](media/grafana/data-sources.png)

1. Clique em **Adicionar fonte de dados**.

1. Na página **Fonte de Dados/Novo**, insira um nome para a fonte de dados e selecione o tipo **Fonte de Dados do Azure Data Explorer**.

    ![Nome e tipo de conexão](media/grafana/connection-name-type.png)

1. Insira o nome do cluster no formato https://{ClusterName}.{Region}.kusto.windows.net. Insira os outros valores no portal do Azure ou na CLI. Consulte na tabela abaixo a imagem a seguir para um mapeamento.

    ![Propriedades da conexão](media/grafana/connection-properties.png)

    | UI do Grafana | Portal do Azure | CLI do Azure |
    | --- | --- | --- |
    | ID da assinatura | ID DA ASSINATURA | SubscriptionId |
    | ID do locatário | ID do Diretório | tenant |
    | Id do Cliente | ID do aplicativo | appId |
    | Segredo do cliente | Senha | password |
    | | | |

1. Selecione **Salvar e Testar**.

    Se o teste for obtido com êxito, siga para a próxima seção. Se você encontrar algum problema, verifique os valores especificados no Grafana e revise as etapas anteriores.

## <a name="visualize-data"></a>Visualizar dados

Agora que você terminou de configurar o Azure Data Explorer como uma fonte de dados para o Grafana, é hora de visualizar os dados. Mostraremos um exemplo básico aqui, mas você pode fazer muito mais. É recomendável consultar [Gravar consultas para Azure Data Explorer](write-queries.md) para exemplos de outras consultas a serem executadas no conjunto de dados de exemplo.

1. No Grafana, no menu esquerdo, selecione o ícone de adição e, em seguida, **Painel**.

    ![Criar painel](media/grafana/create-dashboard.png)

1. Na guia **Adicionar**, selecione **Grafo**.

    ![Adicionar grafo](media/grafana/add-graph.png)

1. No painel de garfo, selecione **Título do Painel** e, em seguida, **Editar**.

    ![Editar painel](media/grafana/edit-panel.png)

1. Na parte inferior do painel, selecione **Fonte de Dados** e, em seguida, selecione a fonte de dados que você configurou.

    ![Selecione uma fonte de dados](media/grafana/select-data-source.png)

1. No painel de consulta, copie na consulta a seguir e selecione **Executar**. A consulta agrupa a contagem de eventos por dia para o conjunto de dados de exemplo.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Executar consulta](media/grafana/run-query.png)

1. O grafo não mostra nenhum resultado porque tem como escopo, por padrão, os dados das últimas seis horas. No menu superior, selecione **Últimas 6 horas**.

    ![Últimas seis horas](media/grafana/last-six-hours.png)

1. Especifique um intervalo personalizado que abrange 2007, o ano incluído em nosso conjunto de dados de exemplo do StormEvents. Escolha **Aplicar**.

    ![Intervalo de datas personalizado](media/grafana/custom-date-range.png)

    Agora, o grafo mostra os dados de 2007, classificados por dia.

    ![Grafo concluído](media/grafana/finished-graph.png)

1. No menu superior, selecione o ícone salvar: ![Ícone Salvar](media/grafana/save-icon.png).

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas para Azure Data Explorer](write-queries.md)

* [Tutorial: Visualizar dados do Azure Data Explorer no Power BI](visualize-power-bi.md)
