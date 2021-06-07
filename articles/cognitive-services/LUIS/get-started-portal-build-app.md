---
title: 'Início Rápido: Criar um aplicativo no portal do LUIS'
description: Neste início rápido, você criará as partes básicas de um aplicativo, intenções e entidades, bem como um teste com enunciado de exemplo no portal do LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: c2f2bafe68dd3f3dad704ba12cdd4d01bf4f6588
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278755"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início Rápido: Criar um aplicativo no portal do LUIS

Neste início rápido, você criará um aplicativo no portal do LUIS. Primeiro, crie as partes básicas de um aplicativo, **intenções** e **entidades**. Em seguida, teste o aplicativo fornecendo um enunciado de usuário de exemplo no painel de teste interativo para obter a intenção prevista.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Criar um aplicativo

Para criar um aplicativo, selecione **+ Novo aplicativo**. 

Na janela exibida, insira as seguintes informações:

|Nome  |Descrição  |
|---------|---------|
|Nome     | Um nome para o aplicativo. Por exemplo, "automação residencial". Obrigatórios.        |
|Cultura     | O idioma que o seu aplicativo compreende e fala. Obrigatórios.   |
|Descrição | Uma descrição para o aplicativo. Opcional.
|Recurso de previsão | O recurso de previsão que receberá consultas. Opcional. |

Selecione **Concluído**.

>[!NOTE]
>A cultura não poderá ser alterada depois que o aplicativo for criado.


## <a name="create-intents"></a>Criar intenções

Depois de criar o aplicativo LUIS, você precisará criar intenções. As intenções são uma forma de classificar o texto dos usuários. Por exemplo, um aplicativo de recursos humanos pode ter duas funções. Ajudar as pessoas a:

 1. Encontrar um emprego e se candidatar a ele
 1. Encontrar formulários para se candidatar a um emprego

As duas diferentes _intenções_ do aplicativo se alinham com as seguintes intenções:

|Intencional|Exemplo de texto do usuário<br>conhecido como um _enunciado_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Para criar intenções, conclua as seguintes etapas:

1. Depois que o aplicativo for criado, verifique se você está na página **Intenções** da seção **Build**. Selecione **Criar**.

   [![Captura de tela da seleção “Criar” para criar nova intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Insira o nome da intenção, `FindForm`, e, em seguida, selecione **Concluído**.

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Você adicionará enunciados de exemplo depois de criar intenções. Enunciados de exemplo são um texto que um usuário insere em um chatbot ou em outro aplicativo cliente. Eles mapeiam a intenção de texto do usuário para uma intenção do LUIS. Para a intenção `FindForm` deste aplicativo de exemplo, os enunciados de exemplo incluirão o número de formulário. O aplicativo cliente precisa do número de formulário para atender à solicitação do usuário, portanto, é importante incluí-lo no enunciado.

Na página **Intenções** de `FindForm`, adicione os exemplos de enunciados a seguir em **Exemplo de Enunciado**. 

|#|Exemplo de enunciados|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Por design, esses enunciados de exemplo variam das seguintes maneiras:

* tamanho do enunciado
* [pontuação](luis-reference-application-settings.md#punctuation-normalization)
* escolha de palavras
* tempos verbais (é, foi, será)
* ordem das palavras


## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para retornar o número do formulário na resposta de previsão durante a execução, o número precisa ser extraído como uma entidade. Como o texto do número do formulário é altamente estruturado, você pode usar uma entidade de expressão regular. Crie a entidade de expressão regular com as seguintes etapas:

1. Selecione **Entidades** no menu à esquerda.

1. Selecione **Criar** na página **Entidades**.

1. Insira o nome `FormNumber` e selecione o tipo de entidade **Regex**.

1. Insira a expressão regular `hrf-[0-9]{6}` no campo **Regex**. Essa entrada corresponde aos caracteres literais, `hrf-`, e permite exatamente seis dígitos; depois selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da criação da entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Essa entidade extrai qualquer texto que corresponda à expressão regular em qualquer uma das intenções.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None

A intenção **Nenhum** é a intenção de fallback e não deve ser deixada vazia. Essa intenção deve conter um enunciado para cada dez enunciados de exemplo que você adicionou para as outras intenções do aplicativo.

Os exemplos de enunciado da intenção **Nenhum** devem ficar fora do seu domínio do aplicativo cliente.

1. Selecione **Intenções** no menu à esquerda e, em seguida, selecione **Nenhum** na lista de intenções.

1. Adicione os quinze exemplos de enunciado a seguir à intenção:

   |Exemplos de enunciado da intenção Nenhum|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Para este aplicativo, esses exemplos de enunciado estão fora do domínio. Se o domínio incluir animais, ou o oceano, você deverá usar enunciados de exemplo diferentes para a intenção **Nenhum**.

## <a name="train-the-app"></a>Treinar o aplicativo

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examinar a entidade de expressão regular nos exemplos de enunciado

1. Verifique se a entidade é encontrada na intenção **FindForm** selecionando **Intenções** no menu à esquerda. Em seguida, selecione a intenção **FindForm**.

   A entidade é marcada onde ela aparece nos exemplos de enunciado.

   > [!div class="mx-imgBorder"]
   > [![Captura de tela de todos os enunciados de exemplo marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar seu novo aplicativo com o painel de teste interativo

Use o painel **Teste** interativo no portal do LUIS para validar se a entidade é extraída de novos enunciados que o aplicativo ainda não viu.

1. Selecione **Testar** no menu superior direito.

1. Adicione um novo enunciado e, em seguida, pressione Enter:

   ```Is there a form named hrf-234098```

    Selecione **Inspecionar** para ver as previsões de entidade.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do teste do novo enunciado no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A primeira intenção prevista é corretamente **FindForm**, com uma confiança acima de 90% (0,977). A entidade **FormNumber** é extraída com um valor hrf-234098.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você concluir este início rápido e não estiver passando para o próximo, selecione **Meus aplicativos** no menu de navegação superior. Em seguida, marque a caixa de seleção à esquerda do aplicativo na lista e selecione **Excluir** na barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar um aplicativo](get-started-portal-deploy-app.md)
