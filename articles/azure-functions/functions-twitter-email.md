---
title: "Criar uma função que se integra aos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Crie uma função que se integra com os Aplicativos Lógicos do Azure e os Serviços Cognitivos do Azure para categorizar o sentimento de tweet e enviar notificações quando o sentimento for fraco."
services: functions, logic-apps, cognitive-services
keywords: "fluxo de trabalho, aplicativos de nuvem, serviços de nuvem, processos de negócios, integração de sistemas, integração de aplicativos empresariais, EAI"
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 910077645b521d4cd303d39f543cf155161a31c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Criar uma função que se integra aos Aplicativos Lógicos do Azure

O Azure Functions integra-se aos Aplicativos Lógicos do Azure no Designer de Aplicativos Lógicos. Essa integração permite usar o poder de computação do Functions em orquestrações com outros serviços de terceiros e do Azure. 

Este tutorial mostra como usar o Functions com os Aplicativos Lógicos e os Serviços Cognitivos da Microsoft no Azure para analisar o sentimento de postagens do Twitter. Uma função HTTP disparada categoriza tweets com cores verde, amarelo ou vermelho com base na pontuação de sentimento. Um email é enviado quando um sentimento inadequado é detectado. 

![imagem dos dois primeiros passos do aplicativo no Designer de Aplicativos Lógicos](media/functions-twitter-email/designer1.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta dos Serviços Cognitivos.
> * Crie uma função que categorize o sentimento do tweet.
> * Crie um aplicativo lógico que se conecte ao Twitter.
> * Adicione a detecção de sentimento ao aplicativo lógico. 
> * Conecte o aplicativo lógico à função.
> * Envie um email com base na resposta da função.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do [Twitter](https://twitter.com/) ativa. 
+ Uma conta do [Outlook.com](https://outlook.com/) (para enviar notificações).
+ Este tópico usa como ponto de partida os recursos criados em [Criar sua primeira função no portal do Azure](functions-create-first-azure-function.md).  
Se você ainda não fez isso, conclua estas etapas agora para criar seu aplicativo de função.

## <a name="create-a-cognitive-services-account"></a>Criar uma conta dos Serviços Cognitivos

Uma conta dos Serviços Cognitivos é necessária para detectar o sentimento dos tweets sendo monitorados.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

3. Clique em **Dados + Análise** > **Serviços Cognitivos**. Em seguida, use as configurações conforme especificadas na tabela, aceite os termos e marque **Fixar no painel**.

    ![Página Criar conta cognitiva](media/functions-twitter-email/cog_svcs_account.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | --- | --- | --- |
    | **Nome** | MyCognitiveServicesAccnt | Escolha um nome de conta exclusivo. |
    | **Tipo de API** | API de Análise de Texto | API usada para analisar texto.  |
    | **Localidade** | Oeste dos EUA | Atualmente, apenas o **Oeste dos EUA** está disponível para análise de texto. |
    | **Tipo de preços** | F0 | Comece com o nível mais baixo. Caso suas chamadas se esgotem, dimensione para uma camada mais elevada.|
    | **Grupo de recursos** | myResourceGroup | Use o mesmo grupo de recursos para todos os serviços neste tutorial.|

4. Clique em **Criar** para criar a conta. Depois que a conta for criada, clique na nova conta dos Serviços Cognitivos fixada no painel. 

5. Na conta, clique em **Chaves**, copie o valor da **Chave 1** e salve-o. Você pode usar essa chave para conectar o aplicativo lógico à conta dos Serviços Cognitivos. 
 
    ![simétricas](media/functions-twitter-email/keys.png)

## <a name="create-the-function"></a>Criar a função

O Functions fornece uma ótima maneira de descarregar tarefas de processamento em um fluxo de trabalho de aplicativos lógicos. Este tutorial usa uma função HTTP disparada para processar pontuações de sentimento de tweet dos Serviços Cognitivos e retornar um valor de categoria.  

1. Expanda seu aplicativo de funções, clique no botão **+** ao lado de **Functions** e clique no modelo **HTTPTrigger**. Digite `CategorizeSentiment` para a função **Nome** e clique em **Criar**.

    ![Folha Aplicativos de Funções, Funções +](media/functions-twitter-email/add_fun.png)

2. Substitua o conteúdo do arquivo run.csx pelo código abaixo e clique em **Salvar**:

    ```c#
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    Esse código de função retorna uma categoria de cor com base na pontuação de sentimento recebida na solicitação. 

3. Para testar a função, clique em **Testar** na extremidade direita para expandir a guia Teste. Digite um valor de `0.2` para o **corpo da solicitação**e clique em **Executar**. Um valor **VERMELHO** é retornado no corpo da resposta. 

    ![Testar a função no portal do Azure](./media/functions-twitter-email/test.png)

Agora você tem uma função que categoriza as pontuações de sentimento. Em seguida, você pode criar um aplicativo lógico que integra sua função às contas dos Serviços Cognitivos e do Twitter. 

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico   

1. Clique no botão **Novo** no canto superior esquerdo do portal do Azure.

2. Clique em **Integração de Empresas** > **Aplicativo Lógico**. Em seguida, use as configurações conforme especificadas na tabela, marque **Fixar no painel** e clique em **Criar**.
 
4. Em seguida, digite um **Nome** como `TweetSentiment`, use as configurações conforme especificadas na tabela, aceite os termos e marque **Fixar no painel**.

    ![Criar o aplicativo lógico no portal do Azure](./media/functions-twitter-email/new_logicApp.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Nome** | TweetSentiment | Escolha um nome apropriado para seu aplicativo. |
    | **Grupo de recursos** | myResourceGroup | Escolha o mesmo grupo de recursos existente. |
    | **Localidade** | Leste dos EUA | Escolha um local perto de você. |    

4. Marque **Fixar no painel** e clique em **Criar** para criar seu aplicativo lógico. 

5. Depois que o aplicativo for criado, clique em seu novo aplicativo lógico fixado no painel. Em seguida, no Designer de Aplicativos Lógicos, role para baixo e clique no modelo **Aplicativo Lógico em Branco**. 

    ![Modelo Aplicativo Lógico em Branco](media/functions-twitter-email/blank.png)

Agora você pode usar o Designer de Aplicativos Lógicos para adicionar serviços e gatilhos ao aplicativo.

## <a name="connect-to-twitter"></a>Conectar-se ao Twitter

Primeiro, crie uma conexão para sua conta do Twitter. O aplicativo lógico sonda tweets, o que dispara a execução do aplicativo.

1. No designer, clique no serviço **Twitter** e clique no gatilho **Quando um novo tweet é publicado**. Entre na sua conta do Twitter e autorize os Aplicativos Lógicos a usar sua conta.

2. Use as configurações de gatilho do Twitter conforme especificado na tabela. 

    ![Configurações do conector do Twitter](media/functions-twitter-email/azure_tweet.png)

    | Configuração      |  Valor sugerido   | Descrição                                        |
    | ----------------- | ------------ | ------------- |
    | **Texto da pesquisa** | #Azure | Use uma hashtag que seja popular o suficiente para gerar novos tweets no intervalo escolhido. Quando você usa a camada gratuita e a hashtag é muito popular, é possível consumir as transações rapidamente na conta dos Serviços Cognitivos. |
    | **Frequência** | Minuto | A unidade de frequência usada para sondar o Twitter.  |
    | **Intervalo** | 15 | O tempo decorrido entre as solicitações do Twitter, em unidades de frequência. |

3.  Clique em **Salvar** para se conectar à sua conta do Twitter. 

Agora, seu aplicativo está conectado ao Twitter. Em seguida, conecte-se à análise de texto para detectar o sentimento dos tweets coletados.

## <a name="add-sentiment-detection"></a>Adicionar detecção de sentimento

1. Clique em **Nova Etapa** e em **Adicionar uma ação**.

    ![Nova Etapa e, então, Adicionar uma ação](media/functions-twitter-email/new_step.png)

2. Em **Escolher uma ação**, clique em **Análise de Texto**e clique na ação **Detectar sentimento**.

    ![Detectar Sentimento](media/functions-twitter-email/detect_sent.png)

3. Digite um nome de conexão como `MyCognitiveServicesConnection`, cole a chave da conta dos Serviços Cognitivos que você salvou e clique em **Criar**.  

4. Clique em **Texto para Analisar** > **Texto do tweet**e clique em **Salvar**.  

    ![Detectar Sentimento](media/functions-twitter-email/ds_tta.png)

Agora que a detecção de sentimento está configurada, você pode adicionar uma conexão à função que consome a saída da pontuação de sentimento.

## <a name="connect-sentiment-output-to-your-function"></a>Conecte a saída de sentimento à função

1. No Designer de Aplicativos Lógicos, clique em **Nova etapa** > **Adicionar uma ação**e clique em **Azure Functions**. 

2. Clique em **Escolher uma função do Azure** e selecione a função **CategorizeSentiment** que você criou anteriormente.  

    ![Caixa de função do Azure mostrando "Escolher uma Função do Azure"](media/functions-twitter-email/choose_fun.png)

3. Em **Corpo da Solicitação**, clique em **Pontuação** e em **Salvar**.

    ![Pontuação](media/functions-twitter-email/trigger_score.png)

Agora, sua função é disparada quando uma pontuação de sentimento é enviada do aplicativo lógico. Uma categoria com codificação de cores é retornada para o aplicativo lógico pela função. Em seguida, adicione uma notificação por email que é enviada quando um valor de sentimento **vermelho** é retornado pela função. 

## <a name="add-email-notifications"></a>Adicionar notificações por email

A última parte do fluxo de trabalho é disparar um email quando o sentimento foi classificado como _VERMELHO_. Este tópico usa um conector do Outlook.com. Você pode executar etapas semelhantes para usar um conector Gmail ou Outlook do Office 365.   

1. No Designer de Aplicativos Lógicos, clique em **Nova etapa** > **Adicionar uma condição**. 

2. Clique em **Escolha um valor** e clique em **Corpo**. Selecione **é igual a**, clique em **Escolha um valor**, digite `RED`e clique em **Salvar**. 

    ![Adicionar uma condição ao aplicativo lógico.](media/functions-twitter-email/condition.png)

3. Em **SE SIM, NÃO FAÇA NADA**, clique em **Adicionar uma ação**, procure `outlook.com`, clique em **Enviar um email**e entre sua conta do Outlook.com.
    
    ![Escolha uma ação para a condição.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Se você não tiver uma conta do Outlook.com, poderá escolher outro conector, como Gmail ou Outlook do Office 365

4. Na ação **Enviar um email**, use as configurações de email conforme especificadas na tabela. 

    ![Configure o email para enviar uma ação de email.](media/functions-twitter-email/sendEmail.png)

    | Configuração      |  Valor sugerido   | Descrição  |
    | ----------------- | ------------ | ------------- |
    | **Para** | Digitar endereço de email | O endereço de email que recebe a notificação. |
    | **Assunto** | Sentimento de tweet negativo detectado  | A linha de assunto do email de notificação.  |
    | **Corpo** | Texto do Tweet, local | Clique nos parâmetros **Texto do tweet** e **Local**. |

5.  Clique em **Salvar**.

Agora que o fluxo de trabalho foi concluído, você poderá habilitar o aplicativo lógico e consultar a função em operação.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

1. No Designer de Aplicativos Lógicos, clique em **Executar** para iniciar o aplicativo.

2. Na coluna à esquerda, clique em **Visão Geral** para ver o status do aplicativo lógico. 
 
    ![Status de execução do aplicativo lógico](media/functions-twitter-email/over1.png)

3. (Opcional) Clique em uma das execuções para ver os detalhes da execução.

4. Vá para sua função, exiba os logs e verifique se os valores de sentimento foram recebidos e processados.
 
    ![Exibir os logs da função](media/functions-twitter-email/sent.png)

5. Quando um sentimento potencialmente negativo é detectado, você recebe um email. Se você ainda não recebeu um email, poderá alterar o código de função para retornar VERMELHO sempre que:

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    Depois de verificar as notificações por email, altere para o código original:

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > Depois de concluir este tutorial, você deverá desabilitar o aplicativo lógico. Ao desabilitar o aplicativo, você evita ser cobrado pelas execuções e não consome as transações em sua conta dos Serviços Cognitivos.

Agora você viu como é fácil integrar o Functions a um fluxo de trabalho dos Aplicativos Lógicos.

## <a name="disable-the-logic-app"></a>Desabilitar o aplicativo lógico

Para desabilitar o aplicativo lógico, clique em **Visão Geral** e clique em **Desabilitar** na parte superior da tela. Isso impede que o aplicativo lógico seja executado e incorra em encargos sem excluir o aplicativo. 

![Logs da função](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma conta dos Serviços Cognitivos.
> * Crie uma função que categorize o sentimento do tweet.
> * Crie um aplicativo lógico que se conecte ao Twitter.
> * Adicione a detecção de sentimento ao aplicativo lógico. 
> * Conecte o aplicativo lógico à função.
> * Envie um email com base na resposta da função.

Vá para o próximo tutorial para aprender a criar uma API sem servidor para sua função.

> [!div class="nextstepaction"] 
> [Criar uma API sem servidor usando o Azure Functions](functions-create-serverless-api.md)

Para saber mais sobre os Aplicativos Lógicos, consulte [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md).

