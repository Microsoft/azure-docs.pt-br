---
title: Detecção de fraude em tempo real usando o Azure Stream Analytics
description: Aprenda a criar uma solução para detecção de fraudes em tempo real com a Stream Analytics. Use um hub de eventos para o processamento de eventos em tempo real.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a13d3b24cd7845de144183d9f2ea825e0e24219f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818409"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introdução ao uso do Stream Analytics do Azure: Detecção de fraude em tempo real

Este tutorial fornece uma ilustração completa de como usar o Azure Stream Analytics. Você aprenderá como: 

* Colocar o fluxo de eventos em uma instância de Hubs de eventos do Azure. Neste tutorial, você usará um aplicativo que simula um fluxo de registros de metadados de telefone celular.

* Escreva consultas de Stream Analytics do tipo SQL para transformar dados, agregar informações ou procurar padrões. Você verá como usar uma consulta para analisar o fluxo de entrada e procurar chamadas que podem ser fraudulentas.

* Envie os resultados para um coletor de saída (armazenamento) no qual você pode analisar informações adicionais. Nesse caso, você enviará os dados de chamada suspeitos para o armazenamento de Blobs do Azure.

Este tutorial usa o exemplo de detecção de fraudes em tempo real com base nos dados de chamada telefônica. A técnica ilustrada também é adequada para outros tipos de detecção de fraudes, como fraude de cartão de crédito ou roubo de identidade. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Detecção de fraudes de telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados para as chamadas de entrada. A empresa deseja detectar chamadas fraudulentas em tempo real para que eles possam notificar clientes ou desligar o serviço para um número específico. Um tipo de fraude SIM envolve várias chamadas da mesma identidade ao mesmo tempo, mas em locais geograficamente diferentes. Para detectar esse tipo de fraude, a empresa precisa analisar os registros de chamada de entrada e procurar padrões específicos – nesse caso, para chamadas feitas ao mesmo tempo em diferentes países. Os registros de telefone que entram nesta categoria são gravados no armazenamento para análise posterior.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você vai simular dados de chamada telefônica usando um aplicativo cliente que gera os metadados de exemplo de chamada telefônica. Alguns dos registros que o aplicativo produz se parecem com chamadas fraudulentas. 

Antes de começar, verifique se você possui:

* Uma conta do Azure.
* O aplicativo gerador de evento de chamada [Telcogenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), que pode ser baixado do Centro de Download da Microsoft. Descompacte este pacote em uma pasta no seu computador. Se você quiser ver o código-fonte e executar o aplicativo em um depurador, você pode obter o código-fonte do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >O Windows pode bloquear o arquivo zip baixado. Se você não conseguir descompactá-lo, clique com o botão direito do mouse no arquivo e selecione **Propriedades**. Se você vir a mensagem "Este arquivo veio de outro computador e pode ser bloqueado para ajudar a proteger este computador", marque a opção **Desbloquear** e clique em **Aplicar**.

Se você quiser analisar os resultados do trabalho do Streaming Analytics, você também precisará de uma ferramenta para exibir o conteúdo de um contêiner de armazenamento de Blobs do Azure. Se você usar o Visual Studio, você pode usar [ferramentas do Azure para Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) ou [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Como alternativa, você pode instalar ferramentas autônomas como [Azure Storage Explorer](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Criar Hubs de Eventos do Azure para eventos de ingestão

Para analisar um fluxo de dados, faça a *ingestão* no Azure. Uma forma comum de ingestão de dados é usar [Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md), que permite ingerir milhões de eventos por segundo e, em seguida, processar e armazenar as informações do evento. Para este tutorial, você criará um hub de eventos e, em seguida, fará com que o aplicativo gerador de evento de chamada envie dados de chamada para esse hub de eventos. Para saber mais sobre Hubs de eventos, consulte [documentação do Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Para obter uma versão mais detalhada deste procedimento, consulte [Criar um namespace de Hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Criar um hub de evento e de namespace
Neste procedimento, você primeiro cria um namespace de hub de eventos e, em seguida, adiciona um hub de eventos para esse namespace. Namespaces do hub de evento são usados para agrupar logicamente instâncias de barramento de evento relacionadas. 

1. Entre no Portal do Azure e clique em **Criar um recurso** > **Internet das Coisas** > **Hub de Eventos**. 

2. No painel **Criar um namespace**, insira um nome de namespace como `<yourname>-eh-ns-demo`. Você pode usar qualquer nome para o namespace, mas o nome deve ser válido para uma URL e deve ser exclusivo no Azure. 
    
3. Selecione uma assinatura e crie ou escolha um grupo de recursos e clique em **Criar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Quando o namespace acabar a implementação, localize o namespace de hub de eventos na lista de recursos do Azure. 

5. Clique em novo namespace e, no painel do namespace, clique em **Hub de eventos**.

   ![Botão Adicionar Hub de Eventos para criar um novo hub de eventos](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nomeie o novo hub de evento `asa-eh-frauddetection-demo`. Você pode usar um nome diferente. Se você fizer isso, anote-o, pois você precisará desse nome mais tarde. Você não precisa definir outras opções para o hub de eventos no momento.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Clique em **Criar**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Conceder acesso para o hub de eventos e obter uma cadeia de caracteres de conexão

Antes que um processo possa enviar dados para um hub de eventos, o hub de eventos deve ter uma política que permita o acesso apropriado. A política de acesso produz uma cadeia de conexão que inclui informações de autorização.

1.  No painel do namespace de evento, clique em **Hubs de Eventos** e, em seguida, clique no nome do seu novo Hub de Eventos.

2.  No painel do Hub de Eventos, clique em **Políticas de acesso compartilhado** e depois em **+&nbsp;Adicionar**.

    >[!NOTE]
    >Verifique se você está trabalhando com o hub de eventos, não com o namespace de hub de eventos.

3.  Adicione uma política chamada `sa-policy-manage-demo` e para **Declaração**, selecione **Gerenciar**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Clique em **Criar**.

5.  Depois que a política for implementada, clique na lista de políticas de acesso compartilhado.

6.  Localize a caixa rotulada **CHAVE PRIMÁRIA DA CADEIA DE CONEXÃO** e clique no botão de cópia próximo à cadeia de conexão. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Cole a cadeia de conexão em um editor de texto. Você precisa dessa cadeia de conexão para a próxima seção, depois que você fizer algumas pequenas modificações.

    A cadeia de conexão tem esta aparência:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Observe que a cadeia de conexão contém vários pares de chave-valor, separados por ponto e vírgula: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, e `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar o aplicativo gerador de evento

Antes de iniciar o aplicativo TelcoGenerator, você deve configurá-lo para que ele envie registros de chamada para o hub de eventos que você criou.

### <a name="configure-the-telcogenerator-app"></a>Configurar o aplicativo TelcoGenerator

1. No editor de onde você copiou a cadeia de caracteres de conexão, anote o `EntityPath` valor e, em seguida, remova o `EntityPath` par (não se esqueça de remover o ponto e vírgula anterior). 

2. Na pasta onde você descompactou o arquivo TelcoGenerator.zip, abra o arquivo de telcodatagen.exe.config em um editor. (Há mais de um arquivo .config, portanto certifique-se de abrir o correto.)

3. Faça o seguinte no elemento `<appSettings>`:

   * Defina o valor da chave `EventHubName` para o nome do hub de evento (ou seja, o valor do caminho da entidade).
   * Defina o valor da chave `Microsoft.ServiceBus.ConnectionString` para a cadeia de conexão. 

   A seção `<appSettings>` será parecida com o seguinte exemplo. (Para maior clareza, as linhas estão quebradas e alguns caracteres foram removidos do token de autorização).

   ![Arquivo de configuração de TelcoGenerator mostra o nome do hub de evento e da cadeia de conexão](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Salve o arquivo. 

### <a name="start-the-app"></a>Iniciar o aplicativo
1.  Abra uma janela de comando e altere para a pasta onde o aplicativo TelcoGenerator é descompactado.
2.  Digite o seguinte comando:

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    Os parâmetros são: 

    * Número de CDRs por hora. 
    * Probabilidade de fraude de cartão SIM: Frequência, como uma porcentagem de todas as chamadas, que o aplicativo deve simular uma chamada fraudulenta. O valor 0,2 significa que cerca de 20% dos registros de chamada parecerão ser fraudulentos.
    * Duração em horas. O número de horas que o aplicativo deve ser executado. Você também pode interromper o aplicativo a qualquer momento pressionando Ctrl + C na linha de comando.

    Depois de alguns segundos, o aplicativo é iniciado exibindo registros de chamada telefônica na tela, enquanto envia para o hub de eventos.

Alguns dos campos-chave que você vai usar neste aplicativo de detecção de fraudes em tempo real são os seguintes:

|**Registro**|**Definição**|
|----------|--------------|
|`CallrecTime`|Carimbo de data/hora para a hora de início da chamada. |
|`SwitchNum`|Chave do telefone usada para se conectar à chamada. Neste exemplo, as opções são cadeias de caracteres que representam o país de origem (Estados Unidos, China, Reino Unido, Alemanha ou Austrália). |
|`CallingNum`|Número de telefone do autor da chamada. |
|`CallingIMSI`|A Identidade do Assinante Móvel Internacional (IMSI). Este é o identificador exclusivo do autor da chamada. |
|`CalledNum`|O número de telefone do destinatário da chamada. |
|`CalledIMSI`|Identidade do Assinante Móvel Internacional (IMSI). Este é o identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Criar um trabalho de Stream Analytics para gerenciar o fluxo de dados

Agora que você tem um fluxo de eventos de chamada, você pode configurar um trabalho do Stream Analytics. O trabalho lerá os dados do hub de eventos que você configurou. 

### <a name="create-the-job"></a>Criar o trabalho 

1. No portal do Azure, clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

2. Selecione o trabalho `asa_frauddetection_job_demo`, especifique uma assinatura, um grupo de recursos e um local.

    É aconselhável colocar o trabalho e o hub de eventos na mesma região para melhor desempenho e para que não seja necessário pagar para transferir dados entre regiões.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Clique em **Criar**.

    O trabalho é criado e o portal exibe detalhes do trabalho. Nada está em execução ainda, no entanto, você precisa configurar o trabalho antes que ele possa ser iniciado.

### <a name="configure-job-input"></a>Configurar entrada de trabalho

1. No painel ou no painel **Todos os recursos**, encontre e selecione o `asa_frauddetection_job_demo` trabalho do Stream Analytics. 
2. Na seção **Visão geral** do painel do trabalho do Stream Analytics, clique na caixa **Entrada**.

   ![Caixa de entrada em Topologia no painel do trabalho do Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Clique em **Adicionar entrada de fluxo** e selecione **Hub de Eventos**. Em seguida, preencha a nova página de entrada com as seguintes informações:

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  CallStream   |  Insira um nome para identificar a entrada do trabalho.   |
   |Assinatura   |  \<Sua assinatura\> |  Selecione a assinatura do Azure com o Hub de Eventos que você criou.   |
   |Namespace do Hub de Eventos  |  asa-eh-ns-demo |  Insira o nome do namespace de Hub de Eventos.   |
   |Nome do Hub de Eventos  | asa-eh-frauddetection-demo | Selecione o nome do Hub de Eventos.   |
   |Nome da política do Hub de Eventos  | asa-policy-manage-demo | Selecione a política de acesso que você criou anteriormente.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Clique em **Criar**.

## <a name="create-queries-to-transform-real-time-data"></a>Criar consultas para transformar dados em tempo real

Neste ponto, você tem um trabalho do Stream Analytics configurado para ler um fluxo de dados de entrada. A próxima etapa é criar uma consulta que analisa os dados em tempo real. O Stream Analytics dá suporte a um modelo de consulta simples e declarativo para descrever as transformações para processamento em tempo real. As consultas usam uma linguagem semelhante a SQL que tem algumas extensões específicas para o Stream Analytics. 

Uma consulta simples pode apenas ler todos os dados de entrada. No entanto, geralmente você cria consultas que procuram dados específicos ou relações nos dados. Nesta seção do tutorial, você criar e testa várias consultas para saber algumas maneiras em que você pode transformar um fluxo de entrada para análise. 

As consultas que você criar aqui exibirão apenas os dados transformados na tela. Em uma seção posterior, você vai configurar um coletor de saída e uma consulta que grava os dados transformados para esse coletor.

Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Obter dados de exemplo para teste de consultas

O aplicativo TelcoGenerator está enviando registros de chamada para o hub de eventos e o trabalho do Stream Analytics é configurado para ler do hub de eventos. Você pode usar uma consulta para testar o trabalho para certificar-se de que está lendo corretamente. Para testar uma consulta no console do Azure, você precisa de dados de exemplo. Para este passo a passo, você vai extrair dados de exemplo do fluxo que estará disponível para o hub de eventos.

1. Certifique-se de que o aplicativo TelcoGenerator está em execução e gera os registros de chamada.
2. No portal, retorne para o painel de trabalho do Streaming Analytics. (Se você fechou o painel, pesquise `asa_frauddetection_job_demo` no painel **Todos os recursos**.)
3. Clique na caixa **Consulta**. Azure lista as entradas e saídas que são configuradas para o trabalho e permite que você crie uma consulta que permite transformar o fluxo de entrada como é enviado para a saída.
4. No painel **Consulta**, clique nos pontos ao lado da entrada `CallStream` e, em seguida, selecione **Dados de exemplo da entrada**.

   ![Opções de menu para usar dados de exemplo para a entrada de trabalho de Streaming Analytics, com "Dados de exemplo de entrada" selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Defina **Minutos** para 3 e, em seguida, clique em **Ok**. 
    
   ![Opções de amostragem de fluxo de entrada com 3 minutos selecionados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    O Azure prova dados do fluxo de entrada de 3 minutos e notifica quando os dados de exemplo estão prontos. (Isso pode levar um tempo.) 

Os dados de exemplo são armazenados temporariamente e estão disponíveis enquanto a janela de consulta estiver aberta. Se você fechar a janela de consulta, os dados de exemplo serão descartados e você terá que criar um novo conjunto de dados de exemplo. 

Como alternativa, você pode obter um arquivo .json que contém dados de exemplo de [do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)e, em seguida, carregar esse arquivo .json para usar como dados de exemplo para a entrada `CallStream`. 

### <a name="test-using-a-pass-through-query"></a>Teste usando uma consulta de passagem

Se deseja arquivar todos os eventos, você pode usar uma consulta de passagem para ler todos os campos na carga do evento.

1. Na janela de consulta, insira esta consulta:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Assim como acontece com SQL, as palavras-chave não diferenciam maiúsculas de minúsculas e espaço em branco não é significativo.

    Nesta consulta, `CallStream` é o alias que você especificou quando criou o servidor. Se você tiver usado um alias diferente, use esse nome.

2. Clique em **Testar**.

    O trabalho do Stream Analytics executa a consulta em relação aos dados de exemplo e exibe a saída na parte inferior da janela. Os resultados indicam que o Hub de Eventos e o trabalho de Streaming Analytics estão configurados corretamente. (Conforme observado, mais tarde você criará um coletor de saída para que a consulta possa gravar dados.)

   ![Saída de trabalho do Stream Analytics, mostrando 73 registros gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    O número exato de registros que você vê dependerá de quantos registros foram capturados em sua amostra de 3 minutos.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduza o número de campos usando uma projeção de coluna

Em muitos casos, a análise não precisa de todas as colunas do fluxo de entrada. Você pode usar uma consulta para um conjunto menor de campos retornados do que na consulta de passagem do projeto.

1. Altere a consulta no editor de código para a seguinte:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Clique em **Testar** novamente. 

   ![Saída de trabalho do Stream Analytics para projeção mostra 25 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de chamadas de entrada por região: Janela em cascata com agregação

Suponha que você deseja contar o número de chamadas de entrada por região. No fluxo de dados, quando você deseja executar funções de agregação como contagem, você precisa segmentar o fluxo em unidades temporais (desde que o fluxo de dados seja efetivamente uma infinidade). Você faz isso usando a [função de janela](stream-analytics-window-functions.md) do Streaming Analytics. Em seguida, você pode trabalhar com os dados dentro dessa janela como uma unidade.

Para essa transformação, você deseja uma sequência de janelas temporais que não se sobrepõem — cada janela terá um conjunto discreto de dados que você pode agrupar e agregar. Esse tipo de janela é conhecido como uma *janela em cascata*. Dentro da janela em cascata, você pode obter uma contagem das chamadas de entrada agrupadas por `SwitchNum`, que representa o país em que a chamada foi originada. 

1. Altere a consulta no editor de código para a seguinte:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Essa consulta usa a palavra-chave `Timestamp By` na cláusula `FROM` para especificar qual campo de carimbo de data/hora no fluxo de entrada a ser usado para definir a janela em cascata. Nesse caso, a janela divide os dados em segmentos pelo campo `CallRecTime` em cada registro. (Se nenhum campo for especificado, a operação em janela usará a hora em que cada evento chegou ao hub de eventos. Consulte "Hora de chegada versus hora do aplicativo" na [Referência de linguagem de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    A projeção inclui `System.Timestamp`, que retorna um carimbo de data/hora para o final de cada janela. 

    Para especificar que você deseja usar uma janela em cascata, você deve usar o [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) funcionar a `GROUP BY` cláusula. Na função, você especifica uma unidade de tempo (em qualquer lugar de um microssegundos a um dia) e um tamanho de janela (quantas unidades). Neste exemplo, a janela em cascata consiste em intervalos de 5 segundos, portanto você obterá uma contagem por país para o valor de cada 5 segundos de chamadas.

2. Clique em **Testar** novamente. Nos resultados, observe que os carimbos de data/hora em **WindowEnd** estão em incrementos de 5 segundos.

   ![Saída de trabalho do Stream Analytics para agregação mostrando 13 registros](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Detectar fraudes SIM usando uma autojunção

Neste exemplo, considere o uso fraudulento como sendo chamadas que se originam do mesmo usuário, mas em diferentes locais dentro de 5 segundos uma da outra. Por exemplo, o mesmo usuário não pode legitimamente fazer uma chamada da Austrália e dos Estados Unidos ao mesmo tempo. 

Para verificar nesses casos, você pode usar uma autojunção do fluxo de dados para associar o fluxo à mesma com base no valor `CallRecTime`. Em seguida, você pode procurar registros de chamada em que o valor `CallingIMSI` (o número de origem) é o mesmo, mas o valor `SwitchNum` (país de origem) não é o mesmo.

Quando você usa uma associação com o fluxo de dados, a junção deve fornecer alguns limites sobre quão distante as linhas correspondentes podem ser separadas no tempo. (Como observado anteriormente, o fluxo de dados é efetivamente uma infinidade.) Os limites de tempo para a relação são especificados dentro de cláusula `ON` da junção, usando a função `DATEDIFF`. Nesse caso, a junção é baseada em um intervalo de 5 segundos de dados de chamada.

1. Altere a consulta no editor de código para a seguinte: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Essa consulta é como qualquer junção SQL, exceto para a função `DATEDIFF` na junção. Esta versão de `DATEDIFF` é específica para Streaming Analytics, e ela deve aparecer na cláusula `ON...BETWEEN`. Os parâmetros são uma unidade de tempo (segundos neste exemplo) e os aliases de duas fontes para a junção. Isso é diferente da função `DATEDIFF` do padrão SQL.

    A cláusula `WHERE` inclui a condição que sinaliza a chamada fraudulenta: as opções de origem não são iguais. 

2. Clique em **Testar** novamente. 

   ![Saída de trabalho do Stream Analytics para autojunção, mostrando seis registros gerados](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Clique em **salvar** para salvar a consulta de autojunção como parte do trabalho do Stream Analytics. (Ele não salva os dados de exemplo.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Criar um coletor de saída para armazenar os dados transformados

Você definiu um fluxo de eventos, uma entrada de hub de eventos para a ingestão de eventos e uma consulta para executar uma transformação no fluxo. A última etapa é definir um coletor de saída para o trabalho — ou seja, um local para gravar o fluxo transformado. 

Você pode usar muitos recursos como coletores de saída — um banco de dados do SQL Server, o armazenamento de tabela, armazenamento do Data Lake, Power BI e até mesmo outro hub de eventos. Para este tutorial, você escreverá o fluxo no armazenamento de Blobs do Azure, que é uma opção típica para coletar informações de evento para análise posterior, já que inclui dados não estruturados.

Se você tiver uma conta de armazenamento de Blobs existente, poderá usá-la. Para este tutorial, você aprenderá a criar uma nova conta de armazenamento.

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de Armazenamento de Blobs do Azure

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**. Preencha a página do trabalho da conta de armazenamento com o **Nome** definido como "asaehstorage", o **Local** definido como "Leste dos EUA 2" e o **Grupo de recursos** definido como "asa-eh-ns-rg" (hospede a conta de armazenamento no mesmo grupo de recursos que o trabalho do Streaming para melhorar o desempenho). As configurações restantes podem ser deixadas em seus valores padrão.  

   ![Criar uma conta de armazenamento no portal do Azure](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. No Portal do Azure, retorne para o painel do trabalho do Streaming Analytics. (Se você fechou o painel, pesquise `asa_frauddetection_job_demo` no painel **Todos os recursos**.)

3. Na seção **Topologia do Trabalho**, clique na caixa **Saída**.

4. No painel **Saídas**, clique em **Adicionar** e selecione **Armazenamento de Blobs**. Em seguida, preencha a nova página de saída com as seguintes informações:

   |**Configuração**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída  |  CallStream-FraudulentCalls   |  Insira um nome para identificar a saída do trabalho.   |
   |Assinatura   |  \<Sua assinatura\> |  Selecione a assinatura do Azure que tem a conta de armazenamento criada. A conta de armazenamento pode estar na mesma assinatura ou em uma diferente. Este exemplo pressupõe que você criou a conta de armazenamento na mesma assinatura. |
   |Conta de armazenamento  |  asaehstorage |  Insira o nome da conta de armazenamento criada. |
   |Contêiner  | asa-fraudulentcalls-demo | Escolha Criar novo e insira um nome de contêiner. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Clique em **Salvar**. 


## <a name="start-the-streaming-analytics-job"></a>Iniciar o trabalho do Stream Analytics

Agora o trabalho está configurado. Você especificou uma entrada (o hub de eventos), uma transformação (a consulta para procurar por chamadas fraudulentas) e uma saída (armazenamento de blobs). Agora você pode iniciar o trabalho. 

1. Verifique se o aplicativo TelcoGenerator está em execução.

2. No painel do trabalho, clique em **Iniciar**. No painel **Iniciar trabalho**, para Hora de início da saída do trabalho, selecione **Agora**. 

   ![Iniciar o trabalho do Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Analise os dados transformados

Agora você tem um trabalho de Streaming Analytics completo. O trabalho é analisar um fluxo de metadados de chamada telefônica, procurando chamadas telefônicas fraudulentas em tempo real e gravar as informações sobre essas chamadas fraudulentas para armazenamento. 

Para concluir este tutorial, talvez você queira analisar os dados sendo capturados pelo trabalho de Stream Analytics. Os dados estão sendo gravados no armazenamento de Blobs do Azure em partes (arquivos). Você pode usar qualquer ferramenta que leia o armazenamento de Blobs do Azure. Conforme observado na seção pré-requisitos, você pode usar extensões do Azure no Visual Studio, ou você pode usar uma ferramenta como [Azure Storage Explorer](https://storageexplorer.com/) ou [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Quando você analisar o conteúdo de um arquivo no armazenamento de blobs, você verá algo semelhante ao seguinte:

   ![Armazenamento de Blobs do Azure com saída de Streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

Há artigos adicionais que dão continuidade o cenário de detecção de fraudes e usam os recursos que você criou neste tutorial. Se você quiser continuar, consulte as sugestões nas **Próximas etapas**.

No entanto, se estiver pronto e não precisar dos recursos que você criou, você pode excluí-los para acarretar cobranças desnecessárias do Azure. Nesse caso, é recomendável que você faça o seguinte:

1. Interrompa o trabalho do Stream Analytics. No painel **Trabalhos**, clique em **Parar** na parte superior.
2. Interrompa o aplicativo Telco Generator. Na janela de comando, onde você iniciou o aplicativo, pressione Ctrl + C.
3. Se você criou uma nova conta de armazenamento de blobs apenas para este tutorial, exclua essa conta. 
4. Exclua o trabalho do Stream Analytics.
5. Exclua o hub de evento.
6. Exclua o namespace do hub de eventos.

## <a name="get-support"></a>Obter suporte

Para obter mais assistência, experimente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Próximas etapas

Você pode continuar este tutorial com o seguinte artigo:

* [Stream Analytics e Power BI: Um painel de análise em tempo real para dados de streaming](stream-analytics-power-bi-dashboard.md). Este artigo explica como enviar a saída de Telco do trabalho de Stream Analytics para Power BI para análise e visualização em tempo real.

Para obter mais informações sobre Stream Analytics em geral, leia estes artigos:

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
