---
title: Tutorial – Executar o Azure Functions nos trabalhos do Azure Stream Analytics
description: Neste tutorial, você aprenderá a configurar o Azure Functions como um coletor de saída para trabalhos do Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 74e09e61a6132858d716686bdb6687bb670f0d33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879504"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Tutorial: Executar o Azure Functions a partir dos trabalhos do Azure Stream Analytics 

É possível executar o Azure Functions a partir do Azure Stream Analytics configurando o Azure Functions como um dos coletores de saída para o trabalho do Stream Analytics. O Azure Functions é uma experiência de computação sob demanda controlada por evento que permite implementar o código que é disparado por eventos que ocorrem no Azure ou em serviços de terceiros. Essa capacidade do Azure Functions de responder a gatilhos faz dele uma saída natural para trabalhos do Stream Analytics.

O Stream Analytics invoca o Azure Functions por meio de gatilhos HTTP. O adaptador de saída do Azure Functions permite que os usuários o conectem ao Stream Analytics, de modo que os eventos possam ser disparados com base em consultas do Stream Analytics. 

> [!NOTE]
> Não há suporte para a conexão com o Azure Functions em uma VNet (rede virtual) por meio de um trabalho do Stream Analytics que esteja sendo executado em um cluster multilocatário.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e executar um trabalho do Stream Analytics
> * Criar uma instância de Cache do Azure para Redis
> * Criar uma Função do Azure
> * Verificar o Cache do Azure para Redis quanto aos resultados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurar um trabalho do Stream Analytics para executar uma função 

Esta seção demonstra como configurar um trabalho do Stream Analytics para executar uma função que grava dados no Cache do Azure para Redis. O trabalho do Stream Analytics lê eventos dos Hubs de Eventos do Azure e executa uma consulta que invoca a função. Esta função lê dados do trabalho do Stream Analytics e os grava no Cache do Azure para Redis.

![Diagrama mostrando as relações entre os serviços do Azure](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Criar um trabalho do Stream Analytics com os Hubs de Eventos como entrada

Siga o tutorial [Detecção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) para criar um hub de eventos, iniciar o aplicativo gerador de evento e criar um trabalho do Stream Analytics. Ignore as etapas para criar a consulta e a saída. Em vez disso, consulte as seções a seguir para configurar uma saída do Azure Functions.

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma instância de Cache do Azure para Redis

1. Crie um cache no Cache do Azure para Redis usando as etapas descritas em [Criar um cache](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Depois de criar o cache, em **Configurações**, selecione **Chaves de Acesso**. Anote a **Chave da cadeia de conexão primária**.

   ![Captura de tela da cadeia de conexão do Cache do Azure para Redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Criar uma função no Azure Functions que pode gravar dados no Cache do Azure para Redis

1. Consulte a seção [Criar um aplicativo de funções](../azure-functions/functions-get-started.md) da documentação do Azure Functions. Esta seção detalha como criar um aplicativo de funções e uma [Função disparada por HTTP no Azure Functions](../azure-functions/functions-get-started.md) usando a linguagem CSharp.  

2. Navegue até a função **run.csx**. Atualize-a com o código a seguir. Substitua **"\<your Azure Cache for Redis connection string goes here\>"** pela cadeia de conexão primária do Cache do Azure para Redis recuperada na seção anterior. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Quando o Stream Analytics recebe a exceção "Entidade de Solicitação HTTP muito grande" da função, ele reduz o tamanho dos lotes que envia para o Azure Functions. O código a seguir garante que o Stream Analytics não envia lotes muito grandes. Certifique-se de que os valores de contagem e tamanho máximo do lote usados na função sejam consistentes com os valores inseridos no portal do Stream Analytics.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Em um editor de texto de sua escolha, crie um arquivo JSON chamado **project.json**. Cole o seguinte código e salve-o no computador local. Esse arquivo contém as dependências do pacote NuGet exigidas pela função C#.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Retorne ao portal do Azure. Na guia **Recursos da plataforma**, navegue até a função. Em **Ferramentas de Desenvolvimento**, selecione **Editor do Serviço de Aplicativo**. 
 
   ![A captura de tela mostra a guia Recursos da plataforma com o Editor do Serviço de Aplicativo selecionado.](./media/stream-analytics-with-azure-functions/image3.png)

5. No Editor do Serviço de Aplicativo, clique com o botão direito do mouse na pasta raiz e carregue o arquivo **project.json**. Após o upload bem-sucedido, atualize a página. Agora, você verá um arquivo gerado automaticamente chamado **project.lock.json**. O arquivo gerado automaticamente contém referências aos arquivos .dll que são especificados no arquivo project.json.  

   ![A captura de tela mostra a opção Carregar Arquivos selecionada no menu.](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Atualizar o trabalho do Stream Analytics com a função como saída

1. Abra seu trabalho do Stream Analytics no portal do Azure.  

2. Navegue até a função e selecione **Visão Geral** > **Saídas** > **Adicionar**. Para adicionar uma nova saída, selecione **Azure Functions** como a opção de coletor. O adaptador de saída do Functions tem as seguintes propriedades:  

   |**Nome da propriedade**|**Descrição**|
   |---|---|
   |Alias de saída| Um nome amigável que você usa na consulta do trabalho para fazer referência à saída. |
   |Importar opção| Você poderá usar a função da assinatura atual ou fornecer as configurações manualmente se a função estiver localizada em outra assinatura. |
   |Aplicativo de Funções| Nome de seu aplicativo de funções. |
   |Função| Nome da função em seu aplicativo de funções (nome da função run.csx).|
   |Tamanho Máximo do Lote|Define o tamanho máximo de cada lote de saída, que é enviado para a função em bytes. Por padrão, esse valor é definido como 262.144 bytes (256 KB).|
   |Contagem Máxima do Lote|Especifica o número máximo de eventos em cada lote enviado para a função. O valor padrão é 100. Essa propriedade é opcional.|
   |Chave|Permite que você use uma função de outra assinatura. Forneça o valor da chave para acessar sua função. Essa propriedade é opcional.|

3. Forneça um nome para o alias de saída. Neste tutorial, ele é nomeado **saop1**, mas você pode usar qualquer nome de sua escolha. Preencha outros detalhes.

4. Abra seu trabalho do Stream Analytics e atualize a consulta para o seguinte. Se você não nomear o coletor de saída como **saop1**, lembre-se alterá-lo na consulta.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Inicie o aplicativo telcodatagen.exe executando o seguinte comando na linha de comando. O comando usa o formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`.  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Inicie o trabalho do Stream Analytics.

## <a name="check-azure-cache-for-redis-for-results"></a>Verificar o Cache do Azure para Redis quanto aos resultados

1. Navegue até o portal do Azure e localize seu Cache do Azure para Redis. Selecione **Console**.  

2. Use [Comandos do Cache do Azure para Redis](https://redis.io/commands) para verificar se seus dados estão no Cache do Azure para Redis. (O comando usa o formato Get {chave}.) Por exemplo:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Este comando deve imprimir o valor para a chave especificada:

   ![Captura de tela da saída do Cache do Azure para Redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Tratamento de erros e novas tentativas

Se ocorrer uma falha durante o envio de eventos para o Azure Functions, o Stream Analytics repetirá a maioria das operações. Todas as exceções http serão repetidas até serem bem-sucedidas, com exceção do erro http 413 (entidade grande demais). Um erro de entidade grande demais é tratado como um erro de dados que está sujeito à [política de repetição ou remoção](stream-analytics-output-error-policy.md).

> [!NOTE]
> O tempo limite para solicitações HTTP do Stream Analytics para Azure Functions é definido como 100 segundos. Se o aplicativo do Azure Functions levar mais de 100 segundos para processar um lote, o Stream Analytics apresentará erro e tentará executar o lote novamente.

A repetição de tentativas após atingir o tempo limite pode levar à gravação de eventos duplicados no coletor de saída. Quando o Stream Analytics tenta executar novamente um lote com falha, ele tenta todos os eventos no lote. Por exemplo, considere um lote de 20 eventos que são enviados para o Azure Functions do Stream Analytics. Suponha que o Azure Functions leve 100 segundos para processar os 10 primeiros eventos nesse lote. Após os 100 segundos, o Stream Analytics suspende a solicitação, uma vez que não recebeu uma resposta positiva do Azure Functions, e outra solicitação é enviada para o mesmo lote. Os 10 primeiros eventos no lote são processados novamente pelo Azure Functions, o que causa uma duplicata. 

## <a name="known-issues"></a>Problemas conhecidos

No portal do Azure, quando você tenta redefinir o valor de Tamanho máximo do lote/Contagem máxima de lotes como vazio (padrão), o valor é alterado para o valor inserido anteriormente ao salvar. Nesse caso, insira manualmente os valores padrão para esses campos.

Atualmente, o uso de [roteiros HTTP](/sandbox/functions-recipes/routes?tabs=csharp) em seu Azure Functions não é compatível com o Stream Analytics.

O suporte para se conectar ao Azure Functions hospedado em uma rede virtual não está habilitado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, pode interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste início rápido usando as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado.  
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um trabalho simples do Stream Analytics, que executa uma função do Azure. Para saber mais sobre trabalhos do Stream Analytics, prossiga para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Executar funções definidas pelo usuário do JavaScript em trabalhos do Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
