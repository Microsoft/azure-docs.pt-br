---
title: Análise de Sentimento do Twitter em tempo real com o Azure Stream Analytics
description: Este artigo descreve como usar o Stream Analytics para análise de sentimento do Twitter em tempo real. Orientações passo a passo de geração de eventos aos dados em um painel em tempo real.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: f24ad348c681609392f83af894bf774dbee226bc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620849"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de sentimento do Twitter em tempo real no Stream Analytics do Azure

> [!IMPORTANT] 
> A criação do aplicativo Twitter não está mais disponível por meio de [apps.twitter.com](https://apps.twitter.com/). Este tutorial está em processo de atualização para inclusão da nova API do Twitter.

Aprenda a compilar uma solução de análise de sentimento para análise de mídia social colocando os eventos em tempo real do Twitter nos Hubs de Eventos do Azure. Você pode então gravar uma consulta Azure Stream Analytics para analisar os dados e ou armazenar os resultados para uso posterior ou usar um painel e [Power BI](https://powerbi.com/) para fornecer insights em tempo real.

As ferramentas de análise de mídias sociais ajudam as organizações a compreender os tópicos mais populares. Os tópicos mais populares são entidades e atitudes com um alto volume de postagens em mídia social. A análise de sentimento, também chamada de *mineração opinião*, usa as ferramentas de análise de mídia social para determinar as atitudes em direção a um produto, ideia e assim por diante. 

A análise de tendência do Twitter em tempo real é um ótimo exemplo de uma ferramenta analítica, porque o modelo de assinatura com hashtag permite que você escute palavras-chave específicas (hashtags) e desenvolva a análise de sentimento no feed.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento de mídia social em tempo real

Uma empresa com um site de mídia de notícias está interessada em obter uma vantagem sobre seus concorrentes apresentando conteúdo do site imediatamente relevante para seus leitores. A empresa usa a análise de mídia social sobre os tópicos relevantes para seus leitores, fazendo uma análise de sentimento em tempo real nos dados do Twitter.

Para identificar os tópicos em destaque em tempo real no Twitter, a empresa precisa de análise em tempo real sobre o volume de tweets e de sentimento para os tópicos principais. Em outras palavras, eles precisam de um mecanismo de análise para análise de sentimento baseado nesse feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos
Neste tutorial, você deve usar um aplicativo cliente que se conecta ao Twitter e procura tweets com determinados hashtags (que podem ser definidos). Para executar o aplicativo e analisar os tweets usando o Azure Streaming Analytics, você deve ter o seguinte:

* Uma assinatura do Azure
* Uma conta do Twitter 
* Um aplicativo do Twitter e o [token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) para o aplicativo. Nós fornecemos instruções de alto nível sobre como criar um aplicativo do Twitter mais tarde.
* O aplicativo TwitterWPFClient, que lê o feed do Twitter. Para obter esse aplicativo, baixe o arquivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) do GitHub e, em seguida, descompacte o pacote em uma pasta no seu computador. Se você quiser ver código-fonte e executar o aplicativo em um depurador, você pode obter o código-fonte do [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Criar um hub de eventos para entrada do Streaming Analytics

O aplicativo de exemplo gera eventos e empurra eles para um hub de eventos do Azure. Os Hubs de Eventos do Azure são o método preferencial de ingestão de eventos para Stream Analytics. Para obter mais informações, consulte a [documentação dos Hubs de Evento do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Criar um namespace de hub de eventos e um hub de eventos
Neste procedimento, você primeiro cria um namespace de hub de eventos e, em seguida, adiciona um hub de eventos para esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Entre no Portal do Azure e clique em **Criar um recurso** > **Internet das Coisas** > **Hub de Eventos**. 

2. Na folha **Criar um namespace**, insira um nome de namespace como `<yourname>-socialtwitter-eh-ns`. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione uma assinatura e crie ou escolha um grupo de recursos e clique em **Criar**. 

    ![Criar um namespace do hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Quando o namespace acabar a implementação, localize o namespace de hub de eventos na lista de recursos do Azure. 

5. Clique em novo namespace e, na folha do namespace, clique em **+&nbsp;Hub de eventos**. 

    ![Botão Adicionar Hub de Eventos para criar um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nomeie o novo hub de evento `socialtwitter-eh`. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos.

    ![Folha para a criação de um novo hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Clique em **Criar**.


### <a name="grant-access-to-the-event-hub"></a>Conceder acesso para o hub de eventos

Antes que um processo possa enviar dados para um hub de eventos, o hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  Na folha de namespace de evento, clique em **Hubs de Eventos** e, em seguida, clique no nome do seu novo Hub de Eventos.

2.  Na folha de Hub de Eventos, clique em **Políticas de acesso compartilhado** e depois em **+&nbsp;Adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o hub de eventos, não com o namespace de hub de eventos.

3.  Adicione uma política chamada `socialtwitter-access` e para **Declaração**, selecione **Gerenciar**.

    ![Folha para a criação de uma nova política de acesso de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Clique em **Criar**.

5.  Depois que a política for implementada, clique na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **CHAVE PRIMÁRIA DA CADEIA DE CONEXÃO** e clique no botão de cópia próximo à cadeia de conexão. 
    
    ![Copiando a chave de cadeia de cadeia de conexão primária da política de acesso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois que você fizer algumas pequenas modificações.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

    > [!NOTE]
    > Por segurança, partes da cadeia de conexão do exemplo foram removidas.

8.  No editor de texto, remova o `EntityPath` par da cadeia de conexão (não se esqueça de remover o ponto e vírgula anterior). Quando você terminar, a cadeia de conexão terá esta aparência:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter
O aplicativo cliente obtém eventos de tweet diretamente do Twitter. Para fazer isso, ele precisa de permissão para chamar as APIs de Streaming do Twitter. Para configurar essa permissão, você pode criar um aplicativo no Twitter que gere as credenciais exclusivas (por exemplo, um token OAuth). Você pode configurar o aplicativo de cliente para usar essas credenciais ao fazer chamadas à API. 

### <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter
Se você ainda não tiver um aplicativo do Twitter que você possa usar para este tutorial, você pode criar um. Você já deve ter uma conta do Twitter.

> [!NOTE]
> O processo exato no Twitter para criar um aplicativo e obter o token, as chaves e segredos pode mudar. Se essas instruções não corresponderem ao que você vê no site do Twitter, consulte a documentação do desenvolvedor do Twitter.

1. Vá para a [página de gerenciamento de aplicativo do Twitter](https://apps.twitter.com/). 

2. Crie um aplicativo novo. 

   * Para a URL do site, especifique uma URL válida. Ele não precisa ser um site ativo. (Você não pode especificar apenas `localhost`.)
   * Deixe o campo de retorno de chamada em branco. O aplicativo cliente que você usar para este tutorial não requer retornos de chamada.

     ![Criando um aplicativo no Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Opcionalmente, altere as permissões do aplicativo para somente leitura.

4. Quando o aplicativo for criado, clique na página **Chaves e Tokens de acesso**.

5. Clique no botão para gerar um token de acesso e um segredo de token de acesso.

Mantenha essas informações em mãos, pois você precisará delas no próximo procedimento.

>[!NOTE]
>As chaves e segredos do aplicativo Twitter fornecem acesso à sua conta do Twitter. Trate essas informações como confidenciais, o mesmo faça com sua senha do Twitter. Por exemplo, não insira essas informações em um aplicativo que você forneça a outras pessoas. 


### <a name="configure-the-client-application"></a>Configurar o aplicativo do cliente
Nós criamos um aplicativo de cliente que se conecta aos dados do Twitter por meio das [APIs de Streaming do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos de Tweets sobre um conjunto específico de tópicos. O aplicativo usa a ferramenta de código aberto [Sentiment140](http://help.sentiment140.com/), que atribui o seguinte valor de sentimento para cada tweet:

* 0 = negativo
* 2 = neutro
* 4 = positivo

Depois que um valor de sentimento foi atribuído aos eventos de tweet, eles são enviados para o hub de eventos que você criou anteriormente.

Antes do aplicativo ser executado, ele requer certas informações, como as chaves do Twitter e a cadeia de conexão de hub de eventos. Você pode fornecer as informações de configuração das seguintes maneiras:

* Execute o aplicativo e, em seguida, use a interface de usuário do aplicativo para inserir a cadeia de conexão, as chaves e os segredos. Se você fizer isso, as informações de configuração são usadas para a sessão atual, mas não serão salvas.
* Edite o arquivo de configuração do aplicativo e defina os valores de lá. Essa abordagem persiste as informações de configuração, mas isso também significa que essas informações potencialmente confidenciais são armazenadas em texto sem formatação em seu computador.

O procedimento a seguir documenta as duas abordagens. 

1. Verifique se você baixou e descompactou o aplicativo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), conforme listado nos pré-requisitos.

2. Para definir os valores em tempo de execução (e apenas para a sessão atual), execute o `TwitterWPFClient.exe` aplicativo. Quando o aplicativo solicitar, insira os seguintes valores:

    * A chave do consumidor do Twitter (chave de API).
    * A segredo do consumidor do Twitter (segredo de API).
    * O Token de acesso do Twitter.
    * O Segredo do Token de acesso do Twitter.
    * As informações de cadeia de conexão que você salvou anteriormente. Certifique-se de que você use a cadeia de conexão do qual você removeu o `EntityPath` par chave-valor.
    * As palavras-chave do Twitter que você deseja determinar o sentimento.

   ![Aplicativo TwitterWpfClient em execução, mostrando as configurações obscuras](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para definir os valores de maneira persistente, use um editor de texto para abrir o arquivo de configuração TwitterWpfClient.exe. Em seguida, no elemento `<appSettings>`, faça isso:

   * Defina `oauth_consumer_key` a chave do consumidor do Twitter (chave de API). 
   * Defina `oauth_consumer_secret` o segredo do consumidor do Twitter (segredo de API).
   * Defina `oauth_token` o Token de acesso do Twitter.
   * Defina `oauth_token_secret` o Segredo do Token de acesso do Twitter.

     Mais adiante no elemento `<appSettings>`, faça essas alterações:

   * Defina `EventHubName` para o nome do hub de evento (ou seja, o valor do caminho da entidade).
   * Defina `EventHubNameConnectionString` a cadeia de conexão. Certifique-se de que você use a cadeia de conexão do qual você removeu o `EntityPath` par chave-valor.

     A `<appSettings>` seção se parece com o seguinte exemplo. (Para maior clareza e segurança, nós encapsulamos algumas linhas e removemos alguns caracteres.)

     ![Arquivo de configuração de aplicativo TwitterWpfClient em um editor de texto, mostrando as chaves e segredos do Twitter e as informações de cadeia de conexão de hub de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Se você ainda não iniciou o aplicativo, execute o TwitterWpfClient.exe agora. 

5. Selecione o botão iniciar verde para coletar sentimento social. Você vê eventos de Tweet com os valores **CreatedAt**, **Topic** e **SentimentScore** sendo enviados ao hub de eventos.

    ![Aplicativo TwitterWpfClient em execução, mostrando as listagem de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Se você encontrar erros, e você não vir um fluxo de tweets exibido na parte inferior da janela, verifique novamente as chaves e segredos. Além disso, verifique a cadeia de conexão (certifique-se de que ela não inclui o `EntityPath` chave e valor.)


## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Agora que os eventos de Tweets estão sendo transmitidos em tempo real do Twitter, você pode configurar um trabalho de Stream Analytics para analisar esses eventos em tempo real.

1. No portal do Azure, clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Selecione o trabalho `socialtwitter-sa-job` e especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

    ![Criando um novo trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho.


## <a name="specify-the-job-input"></a>Especificar a entrada de trabalho

1. No trabalho do Stream Analytics, em **Topologia do Trabalho**, no meio da folha de trabalho, selecione **Entradas**. 

2. Na folha **Entradas**, clique em **+&nbsp;Adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de entrada**: Use o nome `TwitterStream`. Se você usar um nome diferente, anote-o porque você precisará dele depois.
   * **Tipo de fonte**: Selecione **Fluxo de dados**.
   * **Fonte**: Selecione **Hub de eventos**.
   * **Opção de importação**: Selecione **Usar hub de eventos da assinatura atual**. 
   * **Namespace do barramento de serviço**: Selecione o namespace do hub de eventos criado anteriormente (`<yourname>-socialtwitter-eh-ns`).
   * **Hub de eventos**: Selecione o hub de eventos criado anteriormente (`socialtwitter-eh`).
   * **Nome da política do hub de eventos**: Selecione a política de acesso que você criou anteriormente (`socialtwitter-access`).

     ![Criar nova entrada para o trabalho de Streaming Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Clique em **Criar**.


## <a name="specify-the-job-query"></a>Especificar a consulta de trabalho

O Stream Analytics dá suporte a um modelo de consulta simples e declarativo que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Este tutorial ajuda você a criar e testar várias consultas sobre dados do Twitter.

Para comparar o número de menções entre tópicos, você pode usar uma [Janela em Cascata](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) para obter a contagem de menções por tópico a cada cinco segundos.

1. Feche a folha de **entradas** se ainda não o fez.

2. Na folha **Visão geral**, clique em **Editar Consulta** perto da parte superior direita da caixa de Consulta. Azure lista as entradas e saídas que são configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada como é enviado para a saída.

3. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

3. Na folha **Consulta**, clique nos pontos ao lado da `TwitterStream` entrada e, em seguida, selecione **Dados de exemplo da entrada**.

    ![Opções de menu para usar dados de exemplo para a entrada de trabalho de Streaming Analytics, com "Dados de exemplo de entrada" selecionados](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Isso abrirá uma folha que permite que você especifique quantos dados de exemplo obter definido em termos de quanto tempo necessário para ler o fluxo de entrada.

4. Defina **Minutos** para 3 e, em seguida, clique em **Ok**. 
    
    ![Opções de amostragem de fluxo de entrada, com "3 minutos" selecionados.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    O Azure prova dados do fluxo de entrada de 3 minutos e notifica quando os dados de exemplo estão prontos. (Isso pode levar um tempo.) 

    Os dados de exemplo são armazenados temporariamente e estão disponíveis enquanto a janela de consulta estiver aberta. Se você fechar a janela de consulta, os dados de exemplo serão descartados e você terá que criar um novo conjunto de dados de exemplo. 

5. Altere a consulta no editor de código para:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Se não usar `TwitterStream` como o alias para a entrada, substitua o alias para `TwitterStream` na consulta.  

    Essa consulta usa a palavra-chave **TIMESTAMP BY** para especificar um campo de carimbo de data/hora na carga a ser usada na computação temporal. Se esse campo não for especificado, a operação em janela será realizada usando a hora em que cada evento chegou ao hub de eventos. Saiba mais na seção “Hora de chegada versus tempo de aplicação” na [Referência de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    A consulta também acessa um carimbo de data/hora para o final de cada janela usando a propriedade **System.Timestamp**.

5. Clique em **Testar**. A consulta é executada em relação aos dados que você amostrou.
    
6. Clique em **Salvar**. Isso salva a consulta como parte do trabalho de Streaming Analytics. (Ele não salva os dados de exemplo.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimente usar diferentes campos do fluxo 

A tabela a seguir lista os campos que fazem parte do fluxo de dados JSON. Fique à vontade para fazer experiências no editor de consultas.

|Propriedade JSON | Definição|
|--- | ---|
|CreatedAt | A hora em que foi criado o tweet|
|Tópico | O tópico que corresponde à palavra-chave especificada|
|SentimentScore | A pontuação de sentimento do Sentiment140|
|Autor | O identificador do Twitter que enviou o tweet|
|Texto | O corpo completo do tweet|


## <a name="create-an-output-sink"></a>Criar um coletor de saída

Agora você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho.  

Neste tutorial, você gravou os eventos de tweets agregados de nossa consulta de trabalho para o armazenamento de Blobs do Azure.  Você também pode enviar por push os resultados para um Banco de Dados SQL do Azure, um armazenamento de Tabelas do Azure, para Hubs de Eventos ou Power BI, dependendo das suas necessidades de aplicativo.

## <a name="specify-the-job-output"></a>Especificar a saída de trabalho

1. Na seção **Trabalho de Topologia**, clique na caixa **Saída**. 

2. Na folha **Saídas**, clique em **+&nbsp;Adicionar** e, em seguida, preencha a folha com estes valores:

   * **Alias de saída**: Use o nome `TwitterStream-Output`. 
   * **Coletor**: Selecione **Armazenamento de Blobs**.
   * **Opções de importação**: Selecione **Usar Armazenamento de Blobs da assinatura atual**.
   * **Conta de armazenamento**. Selecione **Criar uma nova conta de armazenamento.**
   * **Conta de armazenamento** (segunda caixa). Digite `YOURNAMEsa`, onde `YOURNAME` é o seu nome ou outra cadeia de caracteres exclusiva. O nome pode ter apenas letras minúsculas e números e deve ser exclusivo no Azure. 
   * **Contêiner**. Digite `socialtwitter`.
     O nome da conta de armazenamento e o nome do contêiner são usados juntos para fornecer um URI para o armazenamento de blobs, como este: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Folha "Nova saída" para trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Clique em **Criar**. 

    Azure cria a conta de armazenamento e gera uma chave automaticamente. 

5. Feche a folha **Saída**. 


## <a name="start-the-job"></a>Iniciar o trabalho

Uma entrada de trabalho, uma consulta e uma saída são especificadas. Você está pronto para iniciar o trabalho do Stream Analytics.

1. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

2. Na folha do trabalho, clique em **Iniciar**.

    ![Iniciar o trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Na folha **Iniciar trabalho**, para **Hora de início de trabalho**, selecione **Agora** e, em seguida, clique em **Iniciar**. 

    ![Folha "Começar trabalho" para trabalho do Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    O Azure notifica você quando o trabalho for iniciado e, na folha de trabalho, o status é exibido como **Executando**.

    ![Trabalho em execução](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Exibir saída para análise sentimento

Depois que o trabalho tiver sido iniciado e estiver processando o fluxo do Twitter em tempo real, escolha como você deseja exibir a saída para análise de sentimento.

Use uma ferramenta como o [Azure Storage Explorer](https://storageexplorer.com/) ou o [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída do trabalho em tempo real. Daqui, você pode usar o [Power BI](https://powerbi.com/) para estender seu aplicativo para incluir um painel personalizado como o demonstrado na seguinte captura de tela:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Criar outra consulta para identificar os tópicos mais populares

Outra consulta que você pode usar para entender o sentimento do Twitter baseia-se em uma [Janela Deslizante](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Para identificar os tópicos mais populares, procuraremos tópicos que ultrapassem um valor limite de menções em determinado período de tempo.

Para as finalidades deste tutorial, verificaremos os tópicos mencionados mais de 20 vezes nos últimos 5 segundos.

1. Na folha de trabalho, clique em **Parar** para interromper o trabalho. 

2. Na seção **Trabalho de Topologia**, clique na caixa **Saída**. 

3. Mude a consulta para o seguinte:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Clique em **Salvar**.

5. Certifique-se de que o aplicativo TwitterWpfClient está em execução. 

6. Clique em **Iniciar** para reiniciar o trabalho usando a nova consulta.


## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
