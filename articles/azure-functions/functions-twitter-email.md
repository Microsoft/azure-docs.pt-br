---
title: Criar uma função que se integra aos Aplicativos Lógicos do Azure
description: Crie uma função que se integra com os Aplicativos Lógicos do Azure e os Serviços Cognitivos do Azure para categorizar o sentimento de tweet e enviar notificações quando o sentimento for fraco.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 5750597d7d4d372be975aa64ce8db11859791da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674311"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Criar uma função que se integra aos Aplicativos Lógicos do Azure

O Azure Functions integra-se aos Aplicativos Lógicos do Azure no Designer de Aplicativos Lógicos. Essa integração permite usar o poder de computação do Functions em orquestrações com outros serviços de terceiros e do Azure. 

Este tutorial mostra como usar o Azure Functions com os Aplicativos Lógicos e os Serviços Cognitivos no Azure para executar a análise de sentimento de postagens do Twitter. Uma função de gatilho HTTP categoriza tweets com cores verde, amarelo ou vermelho com base na pontuação de sentimento. Um email é enviado quando um sentimento inadequado é detectado. 

![imagem dos dois primeiros passos do aplicativo no Designer de Aplicativos Lógicos](media/functions-twitter-email/00-logic-app-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um Recurso de API de Serviços Cognitivos.
> * Crie uma função que categorize o sentimento do tweet.
> * Crie um aplicativo lógico que se conecte ao Twitter.
> * Adicione a detecção de sentimento ao aplicativo lógico. 
> * Conecte o aplicativo lógico à função.
> * Envie um email com base na resposta da função.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do [Twitter](https://twitter.com/) ativa. 
+ Uma conta do [Outlook.com](https://outlook.com/) (para enviar notificações).

> [!NOTE]
> Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrições nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar o conector do Gmail somente com serviços e aplicativos específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação no conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

+ Este artigo usa como ponto de partida os recursos criados em [Criar sua primeira função no portal do Azure](./functions-get-started.md).
Se você ainda não fez isso, conclua estas etapas agora para criar seu aplicativo de função.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

As APIs de Serviços Cognitivos estão disponíveis no Azure como recursos individuais. Use a API de Análise de Texto para detectar o sentimento dos tweets que estão sendo monitorados.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

3. Clique em **IA + Machine Learning** > **Análise de Texto**. Em seguida, use as configurações especificadas na tabela para criar o recurso.

    ![Criar página Recurso cognitivo](media/functions-twitter-email/01-create-text-analytics.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | --- | --- | --- |
    | **Nome** | MyCognitiveServicesAccnt | Escolha um nome de conta exclusivo. |
    | **Localidade** | Oeste dos EUA | Use o local mais próximo de você. |
    | **Tipo de preços** | F0 | Comece com o nível mais baixo. Caso suas chamadas se esgotem, dimensione para uma camada mais elevada.|
    | **Grupo de recursos** | myResourceGroup | Use o mesmo grupo de recursos para todos os serviços neste tutorial.|

4. Clique em **Criar** para criar seu recurso. 

5. Clique em **Visão geral** e copie o valor do **Ponto de Extremidade** para um editor de texto. Esse valor é usado para criar uma conexão com a API dos Serviços Cognitivos.

    ![Configurações dos Serviços Cognitivos](media/functions-twitter-email/02-cognitive-services.png)

6. Na coluna de navegação à esquerda, clique em **Chaves**, copie o valor da **Chave 1** e salve-o em um editor de texto. Você pode usar a chave para conectar o aplicativo lógico à API dos Serviços Cognitivos. 
 
    ![Chaves dos Serviços Cognitivos](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Crie o aplicativo de funções

O Azure Functions fornece uma ótima maneira de descarregar tarefas de processamento em um fluxo de trabalho de aplicativos lógicos. Este tutorial usa uma função de gatilho HTTP para processar pontuações de sentimento de tweet dos Serviços Cognitivos e retornar um valor de categoria.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-trigger-function"></a>Criar uma função de gatilho HTTP  

1. No menu esquerdo da janela **Funções**, selecione **Funções** e depois selecione **Adicionar** no menu superior.

2. Na janela **Nova Função**, selecione **Gatilho HTTP**.

    ![Escolher uma função de gatilho HTTP](./media/functions-twitter-email/06-function-http-trigger.png)

3. Na página **Nova Função**, selecione **Criar função**.

4. Em sua nova função de gatilho HTTP, selecione **Código + Teste** no menu esquerdo, substitua o conteúdo do arquivo `run.csx` pelo seguinte código e, em seguida, selecione **Salvar**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```

    Esse código de função retorna uma categoria de cor com base na pontuação de sentimento recebida na solicitação. 

5. Para testar a função, selecione **Testar** no menu superior. Na guia **Entrada**, insira um valor de `0.2` no **Corpo** e, em seguida, selecione **Executar**. Um valor de **VERMELHO** é retornado no **Conteúdo de resposta HTTP** na guia **Saída**. 

    :::image type="content" source="./media/functions-twitter-email/07-function-test.png" alt-text="Definir as configurações de proxy":::

Agora você tem uma função que categoriza as pontuações de sentimento. Em seguida, você pode criar um aplicativo lógico que integra sua função ao Twitter e à API dos Serviços Cognitivos. 

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico   

1. No portal do Azure, clique no botão **Criar um recurso** localizado no canto superior esquerdo do portal do Azure.

2. Clique em **Web** > **Aplicativo Lógico**.
 
3. Em seguida, digite um valor para **Nome**, como `TweetSentiment`, e use as configurações conforme especificado na tabela.

    ![Criar o aplicativo lógico no portal do Azure](./media/functions-twitter-email/08-logic-app-create.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Nome** | TweetSentiment | Escolha um nome apropriado para seu aplicativo. |
    | **Grupo de recursos** | myResourceGroup | Escolha o mesmo grupo de recursos existente. |
    | **Localidade** | Leste dos EUA | Escolha um local perto de você. |    

4. Depois de inserir os valores de configurações corretos, clique em **Criar** para criar o aplicativo lógico. 

5. Depois que o aplicativo for criado, clique em seu novo aplicativo lógico fixado no painel. Em seguida, no Designer de Aplicativos Lógicos, role para baixo e clique no modelo **Aplicativo Lógico em Branco**. 

    ![Modelo Aplicativo Lógico em Branco](media/functions-twitter-email/09-logic-app-create-blank.png)

Agora você pode usar o Designer de Aplicativos Lógicos para adicionar serviços e gatilhos ao aplicativo.

## <a name="connect-to-twitter"></a>Conectar-se ao Twitter

Primeiro, crie uma conexão para sua conta do Twitter. O aplicativo lógico sonda tweets, o que dispara a execução do aplicativo.

1. No designer, clique no serviço **Twitter** e clique no gatilho **Quando um novo tweet é publicado**. Entre na sua conta do Twitter e autorize os Aplicativos Lógicos a usar sua conta.

2. Use as configurações de gatilho do Twitter conforme especificado na tabela. 

    ![Configurações do conector do Twitter](media/functions-twitter-email/10-tweet-settings.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Texto da pesquisa** | #Azure | Use uma hashtag que seja popular o suficiente para gerar novos tweets no intervalo escolhido. Quando você usa a camada gratuita e a hashtag é muito popular, é possível consumir rapidamente a cota de transação na API dos Serviços Cognitivos. |
    | **Intervalo** | 15 | O tempo decorrido entre as solicitações do Twitter, em unidades de frequência. |
    | **Frequência** | Minuto | A unidade de frequência usada para sondar o Twitter.  |

3.  Clique em **Salvar** para se conectar à sua conta do Twitter. 

Agora, seu aplicativo está conectado ao Twitter. Em seguida, conecte-se à análise de texto para detectar o sentimento dos tweets coletados.

## <a name="add-sentiment-detection"></a>Adicionar detecção de sentimento

1. Clique em **Nova Etapa** e em **Adicionar uma ação**.

2. Em **Escolher uma ação**, digite **Análise de Texto** e clique na ação **Detectar sentimento**.
    
    ![A captura de tela mostra a seção "Escolher uma ação" com "Análise de Texto" na caixa de pesquisa e a ação "Detectar sentimentos" selecionada. ](media/functions-twitter-email/11-detect-sentiment.png)

3. Digite um nome de conexão, como `MyCognitiveServicesConnection`, cole a chave da API dos Serviços Cognitivos e o ponto de extremidade de Serviços Cognitivos que você anotou em um editor de texto e clique em **Criar**.

    ![Nova Etapa e, então, Adicionar uma ação](media/functions-twitter-email/12-connection-settings.png)

4. Em seguida, insira o **Texto do tweet** na caixa de texto e clique em **Nova Etapa**.

    ![Defina texto a ser analisado](media/functions-twitter-email/13-analyze-tweet-text.png)

Agora que a detecção de sentimento está configurada, você pode adicionar uma conexão à função que consome a saída da pontuação de sentimento.

## <a name="connect-sentiment-output-to-your-function"></a>Conecte a saída de sentimento à função

1. No Designer de Aplicativos Lógicos, clique em **Nova Etapa** > **Adicionar uma ação**, filtre pelo **Azure Functions** e clique em **Escolher uma função do Azure**.

    ![Detectar Sentimento](media/functions-twitter-email/14-azure-functions.png)
  
4. Selecione o aplicativo de funções que você criou anteriormente.

    ![Captura de tela que mostra a seção "Escolher uma ação" com um aplicativo de funções selecionado.](media/functions-twitter-email/15-select-function.png)

5. Selecione a função que você criou para este tutorial.

    ![Selecionar função](media/functions-twitter-email/16-select-function.png)

4. Em **Corpo da Solicitação**, clique em **Pontuação** e em **Salvar**.

    ![Pontuação](media/functions-twitter-email/17-function-input-score.png)

Agora, sua função é disparada quando uma pontuação de sentimento é enviada do aplicativo lógico. Uma categoria com codificação de cores é retornada para o aplicativo lógico pela função. Em seguida, adicione uma notificação por email que é enviada quando um valor de sentimento **RED** é retornado pela função. 

## <a name="add-email-notifications"></a>Adicionar notificações por email

A última parte do fluxo de trabalho é disparar um email quando o sentimento foi classificado como _RED_. Este artigo usa um conector do Outlook.com. Você pode executar etapas semelhantes para usar um conector Gmail ou Outlook do Office 365.   

1. No Designer de Aplicativos Lógicos, clique em **Nova etapa** > **Adicionar uma condição**. 

    ![Adicionar uma condição ao aplicativo lógico.](media/functions-twitter-email/18-add-condition.png)

2. Clique em **Escolha um valor** e clique em **Corpo**. Selecione **é igual a**, clique em **Escolha um valor**, digite `RED`e clique em **Salvar**. 

    ![Escolha uma ação para a condição.](media/functions-twitter-email/19-condition-settings.png)    

3. Em **SE VERDADEIRO**, clique em **Adicionar uma ação**, procure `outlook.com`, clique em **Enviar um email** e entre sua conta do Outlook.com.

    ![Captura de tela que mostra a seção "IF TRUE" com "outlook.com" inserido na caixa de pesquisa e a ação "Enviar um email" selecionada.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Se você não tiver uma conta do Outlook.com, poderá escolher outro conector, como Gmail ou Outlook do Office 365

4. Na ação **Enviar um email**, use as configurações de email conforme especificadas na tabela. 

    ![Configure o email para enviar uma ação de email.](media/functions-twitter-email/21-configure-email.png)
    
| Configuração      |  Valor sugerido   | Descrição  |
| ----------------- | ------------ | ------------- |
| **Para** | Digitar endereço de email | O endereço de email que recebe a notificação. |
| **Assunto** | Sentimento de tweet negativo detectado  | A linha de assunto do email de notificação.  |
| **Corpo** | Texto do Tweet, local | Clique nos parâmetros **Texto do tweet** e **Local**. |

1. Clique em **Save** (Salvar).

Agora que o fluxo de trabalho foi concluído, você poderá habilitar o aplicativo lógico e consultar a função em operação.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

1. No Designer de Aplicativos Lógicos, clique em **Executar** para iniciar o aplicativo.

2. Na coluna à esquerda, clique em **Visão Geral** para ver o status do aplicativo lógico. 
 
    ![Status de execução do aplicativo lógico](media/functions-twitter-email/22-execution-history.png)

3. (Opcional) Clique em uma das execuções para ver os detalhes da execução.

4. Vá para sua função, exiba os logs e verifique se os valores de sentimento foram recebidos e processados.
 
    ![Exibir os logs da função](media/functions-twitter-email/sent.png)

5. Quando um sentimento potencialmente negativo é detectado, você recebe um email. Se você ainda não recebeu um email, poderá alterar o código de função para retornar RED sempre que:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Depois de verificar as notificações por email, altere para o código original:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Depois de concluir este tutorial, você deverá desabilitar o aplicativo lógico. Ao desabilitar o aplicativo, você evita ser cobrado pelas execuções e não consome as transações em sua API dos Serviços Cognitivos.

Agora você viu como é fácil integrar o Functions a um fluxo de trabalho dos Aplicativos Lógicos.

## <a name="disable-the-logic-app"></a>Desabilitar o aplicativo lógico

Para desabilitar o aplicativo lógico, clique em **Visão Geral** e clique em **Desabilitar** na parte superior da tela. A desabilitação do aplicativo impede que ele seja executado e incorra em encargos sem precisar ser excluído.

![Logs da função](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Crie um Recurso de API de Serviços Cognitivos.
> * Crie uma função que categorize o sentimento do tweet.
> * Crie um aplicativo lógico que se conecte ao Twitter.
> * Adicione a detecção de sentimento ao aplicativo lógico. 
> * Conecte o aplicativo lógico à função.
> * Envie um email com base na resposta da função.

Vá para o próximo tutorial para aprender a criar uma API sem servidor para sua função.

> [!div class="nextstepaction"] 
> [Criar uma API sem servidor usando o Azure Functions](functions-create-serverless-api.md)

Para saber mais sobre os Aplicativos Lógicos, consulte [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).