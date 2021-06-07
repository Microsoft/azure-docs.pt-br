---
title: Análise de mídia social com Azure Stream Analytics
description: Este artigo descreve como usar Stream Analytics para análise de mídia social usando a API de cliente do Twitter. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e2369e8f4349a3b4a83c1729c6414d319dd8419e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589660"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Análise de mídia social com Azure Stream Analytics

Este artigo ensina como criar uma solução de análise de sentimento de mídias sociais colocando os eventos em tempo real do Twitter no Hubs de Eventos do Azure. Você grava uma consulta no Azure Stream Analytics para analisar os dados e ou armazenar os resultados para uso posterior ou cria um painel de [Power BI](https://powerbi.com/) para fornecer insights em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Os tópicos mais populares são entidades e atitudes com um alto volume de postagens em mídia social. A análise de sentimento, também chamada de *mineração de opinião*, usa as ferramentas de análise de mídia social para determinar as atitudes em relação a um produto ou ideia. 

A análise de tendência do Twitter em tempo real é um ótimo exemplo de uma ferramenta analítica, porque o modelo de assinatura com hashtag permite que você escute palavras-chave específicas (hashtags) e desenvolva a análise de sentimento no feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Para identificar os tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais.

## <a name="prerequisites"></a>Pré-requisitos

Neste guia de instruções, você deve usar um aplicativo cliente que se conecta ao Twitter e procura tweets com determinados hashtags (que podem ser definidos). Para executar o aplicativo e analisar os tweets usando o Azure Streaming Analytics, você deve ter o seguinte:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Uma conta do [Twitter](https://twitter.com).

* O aplicativo TwitterClientCore, que lê o feed do Twitter. Para obter esse aplicativo, baixe o [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale a versão 2.1.0 da [CLI do .NET Core](/dotnet/core/tools/?tabs=netcore2x).

Abaixo está a arquitetura da solução que você vai implementar.

   ![Um diagrama que mostra diferentes partes de serviços e aplicativos usados para criar a solução.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Diagrama de solução")

## <a name="create-an-event-hub-for-streaming-input"></a>Criação de um hub de eventos para entrada do streaming

O aplicativo de exemplo gera eventos e empurra eles para um hub de eventos do Azure. O Hubs de Eventos do Azure é o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, consulte a [documentação dos Hubs de Evento do Azure](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace de hub de eventos e um hub de eventos
Nesta seção, você cria um namespace de hub de eventos e adiciona um hub de eventos para esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Faça logon no portal do Azure e selecione **Criar um recurso**. Então. pesquise por **Hubs de Eventos** e selecione **Criar**.

2. Na página **Criar um namespace**, insira um nome de namespace. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione um tipo de preço e uma assinatura e crie ou escolha um grupo de recursos. Em seguida, escolha um local e selecione **Criar**. 
 
4. Quando o namespace acabar a implementação, navegue até o grupo de recursos e localize o namespace de hub de eventos na lista de recursos do Azure. 

5. No novo namespace, selecione **+&nbsp;Hub de Eventos**. 

6. Nomeie o novo hub de eventos como *socialtwitter-eh*. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos.
 
7. Selecione **Criar**.

### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o hub de eventos precisa de uma política que permita o acesso. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  Na barra de navegação à esquerda do namespace de seus hubs de eventos, selecione a opção **Hubs de Eventos**, localizada na seção **Entidades**. Em seguida, selecione o hub de eventos que você acabou de criar.

2.  Na barra de navegação à esquerda, selecione a opção **Políticas de acesso compartilhado** localizada em **Configurações**.

    >[!NOTE]
    >Há uma opção de Políticas de acesso compartilhado para o namespace do hub de eventos e para o hub de eventos. Verifique se você está trabalhando no contexto do seu hub de eventos, não no namespace geral do hub de eventos.

3.  Na página de política de acesso, selecione **+ Adicionar**. Em seguida, insira *socialtwitter-access* para o **Nome da política** e marque a caixa de seleção **Gerenciar**.
 
4.  Selecione **Criar**.

5.  Depois que a política for implementada, selecione a política na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **Chave primária da cadeia de conexão** e clique no botão de cópia próximo à cadeia de conexão.
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois que fizer algumas pequenas modificações.

   A cadeia de conexão tem esta aparência:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

   > [!NOTE]
   > Por segurança, partes da cadeia de conexão do exemplo foram removidas.

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

O aplicativo cliente obtém eventos de tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de Streaming do Twitter. Para configurar essa permissão, você pode criar um aplicativo no Twitter que gere as credenciais exclusivas (por exemplo, um token OAuth). Você pode configurar o aplicativo de cliente para usar essas credenciais ao fazer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Se ainda não tiver um aplicativo do Twitter que você possa usar para este tutorial, crie um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter o token, as chaves e segredos pode mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. Em um navegador da Web, acesse [Twitter para Desenvolvedores](https://developer.twitter.com/en/apps), crie uma conta de desenvolvedor e selecione **Criar um aplicativo**. Você poderá ver uma mensagem indicando que precisa solicitar uma conta de desenvolvedor do Twitter. Fique à vontade para fazer isso e, depois que seu aplicativo tiver sido aprovado, você verá um email de confirmação. Podem ser necessários vários dias para ser aprovado para uma conta de desenvolvedor.

   ![Captura de tela mostra o botão criar um aplicativo.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

2. Na página **Criar um aplicativo**, forneça os detalhes para o novo aplicativo e selecione **Criar seu aplicativo do Twitter**.

   ![Captura de tela mostra o painel detalhes do aplicativo, no qual você pode inserir valores para seu aplicativo.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **Chaves e Tokens** e copie os valores de **Chave de API do Consumidor** e **Chave Secreta de API do Consumidor**. Além disso, selecione **Criar** em **Token de Acesso e Segredo do Token de Acesso** para gerar os tokens de acesso. Copie os valores do **Token de Acesso** e do **Segredo do Token de Acesso**.

   Salve os valores recuperados do aplicativo do Twitter. Você precisará desses valores mais tarde.

> [!NOTE]
> As chaves e segredos do aplicativo Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, o mesmo faça com sua senha do Twitter. Por exemplo, não insira essas informações em um aplicativo que você forneça a outras pessoas. 

### <a name="configure-the-client-application"></a>Configurar o aplicativo do cliente

Nós criamos um aplicativo de cliente que se conecta aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto específico de tópicos.

Antes do aplicativo ser executado, ele requer certas informações, como as chaves do Twitter e a cadeia de conexão de hub de eventos.

1. Verifique se você baixou o aplicativo [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore), conforme listado nos pré-requisitos.

2. Use um editor de texto para abrir o arquivo *App.config*. Faça as alterações a seguir ao elemento `<appSettings>`:

   * Defina `oauth_consumer_key` como a Chave do Consumidor do Twitter (chave de API). 
   * Defina `oauth_consumer_secret` como o Segredo do Consumidor do Twitter (chave secreta de API).
   * Defina `oauth_token` como o Token de acesso do Twitter.
   * Defina `oauth_token_secret` como o segredo do Token de acesso do Twitter.
   * Defina `EventHubNameConnectionString` a cadeia de conexão.
   * Defina `EventHubName` como o nome do hub de eventos (ou seja, o valor do caminho da entidade).

3. Abra a linha de comando e navegue até o diretório onde seu aplicativo TwitterClientCore está localizado. Use o comando `dotnet build` para compilar o projeto. Em seguida, use o comando `dotnet run` para executar o aplicativo. O aplicativo envia Tweets para o Hub de Eventos.

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. No portal do Azure, navegue até o grupo de recursos e selecione **+ Adicionar**. Em seguida, procure **Trabalho do Stream Analytics** e selecione **Criar**.

2. Selecione o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

3. Selecione **Criar**. Em seguida, navegue até o seu trabalho quando a implantação for concluída.

## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No trabalho do Stream Analytics, selecione **Entradas** no menu **Topologia do Trabalho** à esquerda.

2. Selecione **+&nbsp;Adicionar entrada de fluxo** > **Hub de Eventos**. Preencha o **Novo formulário** com as seguintes informações:

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Especifique um alias para a entrada. |
   |Subscription  | \<Your subscription\> |  Selecione a assinatura do Azure que você deseja usar. |
   |Namespace do Hub de Eventos | *asa-Twitter-eventhub* |
   |Nome do Hub de Eventos | *socialtwitter-eh* | Escolha *Usar existente*. Em seguida, selecione o Hub de Eventos que você acabou de criar.|
   |Tipo de compactação do evento| GZip | O tipo de compactação de dados.|

   Deixe os valores padrão restantes e selecione **Salvar**.

## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference). Este guia de instruções ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, você pode usar uma [Janela em Cascata](/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. Na **Visão geral** do trabalho, selecione **Editar consulta** perto da parte superior direita da caixa de Consulta. O Azure lista as entradas e saídas que são configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada conforme ele é enviado para a saída.

2. Altere a consulta no editor de código para a seguinte:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Os dados de eventos das mensagens devem aparecer na janela **Visualização de entrada** abaixo da consulta. Verifique **Exibição** está definido como **JSON**. Caso não veja nenhum dado, verifique se o gerador de dados está enviando eventos para o hub de eventos e se você selecionou **GZip** como o tipo de compactação para a entrada.

4. Selecione **Testar consulta** e observe os resultados na janela **Resultados do teste** abaixo da consulta.

5. Altere a consulta no editor de códigos para o seguinte e selecione **Testar consulta**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Essa consulta retorna todos os tweets que incluem a palavra-chave *Azure*.

## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste guia de instruções, você gravou os eventos de tweets agregados de nossa consulta de trabalho para o armazenamento de Blobs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure, para Hubs de Eventos ou Power BI, dependendo das suas necessidades de aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na seção **Topologia do Trabalho** no menu de navegação à esquerda, selecione **Saídas**. 

2. Na página **Saídas**, clique em **+&nbsp;Adicionar** e **Armazenamento de Blobs/Data Lake Storage Gen2**:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Opções de importação**: Selecione a opção **Selecionar armazenamento nas suas assinaturas**.
   * **Conta de armazenamento**. Selecione sua conta de armazenamento.
   * **Contêiner**. Selecione **Criar novo** e insira `socialtwitter`.
   
4. Clique em **Salvar**.   

## <a name="start-the-job"></a>Iniciar o trabalho

Uma entrada de trabalho, uma consulta e uma saída são especificadas. Você está pronto para iniciar o trabalho do Stream Analytics.

1. Verifique se o aplicativo TwitterClientCore está em execução. 

2. Na visão geral do trabalho, selecione **Iniciar**.

3. Na página **Iniciar trabalho**, para **Hora de início de trabalho**, selecione **Agora** e, em seguida, selecione **Iniciar**.

## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, veja nossa [página de perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
