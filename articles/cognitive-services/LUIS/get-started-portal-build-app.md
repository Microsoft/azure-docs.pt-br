---
title: 'Início Rápido: Criar um aplicativo no portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você cria um aplicativo no portal do LUIS. Crie as partes básicas de um aplicativo, intenções e entidades. Teste o aplicativo fornecendo um enunciado de usuário de exemplo no painel de teste interativo para obter a intenção prevista. Criar um aplicativo é gratuito e não requer uma assinatura do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f54643883028d93b56c7e122f43db95bb6d0b8c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560883"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início Rápido: Criar um aplicativo no portal do LUIS

Neste início rápido, você criará um aplicativo no [portal do LUIS](https://www.luis.ai). Primeiro, você criará as partes básicas de um aplicativo, **intenções** e **entidades**. Em seguida, você testará o aplicativo fornecendo um enunciado de usuário de exemplo no painel de teste interativo para obter a intenção prevista.

A criação de um aplicativo é gratuita e não exige uma assinatura do Azure. Quando você estiver pronto para implantar seu aplicativo, confira o [início rápido para implantar um aplicativo](get-started-portal-deploy-app.md). Ele mostra como criar um recurso do Serviço Cognitivo do Azure e atribuí-lo ao aplicativo.

## <a name="create-an-app"></a>Criar um aplicativo

1. Abra o [portal do LUIS](https://www.luis.ai) em um navegador e entre. Se esta for a primeira vez que você entra, você precisará criar uma conta de usuário gratuita do portal do LUIS.

1. Selecione **Criar aplicativo** da barra de ferramentas de contexto.

   [![Criar aplicativo no portal do LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configure o aplicativo com as configurações a seguir e, em seguida, selecione **Concluído**.

   |Nome da configuração| Valor | Finalidade|
   |--|--|--|
   |NOME|`myEnglishApp`|Nome exclusivo de aplicativo LUIS<br>obrigatório|
   |Cultura|**Inglês**|Linguagem de enunciados de usuários, **pt-br**<br>obrigatório|
   |DESCRIÇÃO|`App made with LUIS Portal`|Descrição do aplicativo<br>opcional|
   | | | |

   ![Inserir configurações do novo aplicativo](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois de criar o aplicativo LUIS, você precisará criar intenções. Intenções são uma forma de categorizar o texto dos usuários. Por exemplo, um aplicativo de recursos humanos pode ter duas funções. Ajudar as pessoas a:

 1. Encontrar um emprego e se candidatar a ele
 1. Encontrar formulários para se candidatar a um emprego

As duas diferentes _intenções_ do aplicativo se alinham com as seguintes intenções:

|Intenção|Exemplo de texto do usuário<br>conhecido como um _enunciado_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Para criar intenções, conclua as seguintes etapas:

1. Depois que o aplicativo é criado, você está na página **Intenções** da seção **Build**. Selecione **Criar nova intenção**.

   [![Selecione o botão Criar nova intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Insira o nome da intenção, `FindForm`, e, em seguida, selecione **Concluído**.

   ![Insira o nome da intenção do FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Você adicionará enunciados de exemplo depois de criar intenções. Enunciados de exemplo são um texto que um usuário insere em um chatbot ou em outro aplicativo cliente. Eles mapeiam a intenção de texto do usuário para uma intenção do LUIS.

Para a intenção `FindForm` deste aplicativo de exemplo, os enunciados de exemplo incluirão o número de formulário. O aplicativo cliente precisa do número de formulário para atender à solicitação do usuário, portanto, é importante incluí-lo no enunciado.

[![Inserir os exemplos de enunciado para a intenção do FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione os 15 enunciados de exemplo a seguir à intenção `FindForm`.

|#|Exemplo de enunciados|
|--|--|
|1|Procurando por hrf-123456|
|2|Onde está o formulário de recursos humanos hrf-234591?|
|3|hrf-345623, onde ele está|
|4|É possível enviar-me o hrf-345794|
|5|Eu preciso do hrf-234695 para candidatar-me a uma vaga de emprego interna?|
|6|Meu gerente precisa saber que estou me candidatando a um emprego com hrf-234091|
|7|Para onde envio o hrf-234918? Eu obtenho uma resposta por email de que ele foi recebido?|
|8|hrf-234555|
|9|Quando o hrf-234987 foi atualizado?|
|10|Posso usar o formulário hrf-876345 me candidatar a vagas de engenharia|
|11|Uma nova versão do hrf-765234 foi enviada para minha solicitação em aberto?|
|12|Devo usar o hrf-234234 para vagas de emprego internacionais?|
|13|Erro de ortografia no hrf-234598|
|14|o hrf-234567 será editado para novos requisitos|
|15|hrf-123456, hrf-123123, hrf-234567|

Por design, esses enunciados de exemplo variam das seguintes maneiras:

* tamanho do enunciado
* pontuação
* escolha de palavras
* tempos verbais (é, foi, será)
* ordem das palavras



## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para retornar o número de formulário na resposta de previsão do tempo de execução, o formulário precisa ser marcado como uma entidade. Como o texto do número de formulário é altamente estruturado, você pode marcá-lo usando uma entidade de expressão regular. Crie a entidade com as seguintes etapas:

1. Selecione **Entidades** no menu à esquerda.

1. Selecione **Criar entidade** na página **Entidades**.

1. Insira o nome `Human Resources Form Number`, selecione o tipo de entidade **Regex** e insira a expressão regular, `hrf-[0-9]{6}`. Essa entrada corresponde aos caracteres de literal, `hrf-`, e permite exatamente seis dígitos.

   ![Insira as informações de entidade da entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecione **Concluído**.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None

A intenção **Nenhum** é a intenção de fallback e não deve ser deixada vazia. Essa intenção deve conter um enunciado para cada dez enunciados de exemplo que você adicionou para as outras intenções do aplicativo.

Os exemplos de enunciado da intenção **Nenhum** devem ficar fora do seu domínio do aplicativo cliente.

1. Selecione **Intenções** no menu à esquerda e, em seguida, selecione **Nenhum** na lista de intenções.

1. Adicione os quinze exemplos de enunciado a seguir à intenção:

   |Exemplos de enunciado da intenção Nenhum|
   |--|
   |Cachorros que latem demais são irritantes|
   |Peça uma pizza para mim|
   |Pinguins no oceano|

   Para este aplicativo de recursos humanos, esses exemplos de enunciado estão fora do domínio. Se o domínio de recursos humanos incluir animais, alimentos ou o oceano, você deverá usar outros enunciados de exemplo para a intenção **Nenhum**.

## <a name="train-the-app"></a>Treinar o aplicativo

No menu do canto superior direito, selecione **Treinar** para aplicar as alterações do modelo de entidade e de intenção à versão atual do aplicativo.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examinar a entidade de expressão regular nos exemplos de enunciado

1. Verifique se a entidade é encontrada na intenção **FindForm** selecionando **Intenções** no menu à esquerda. Em seguida, selecione a intenção **FindForm**.

   A entidade é marcada onde ela aparece nos exemplos de enunciado. Caso deseje ver o texto original em vez do nome da entidade, ative/desative a **Exibição de Entidades** na barra de ferramentas.

   [![Todos os exemplos de enunciado marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar seu novo aplicativo com o painel de teste interativo

Use o painel **Teste** interativo no portal do LUIS para validar se a entidade é extraída de novos enunciados que o aplicativo ainda não viu.

1. Selecione **Testar** no menu superior direito.

1. Adicione um novo enunciado e, em seguida, pressione Enter:

   ```Is there a form named hrf-234098```

   ![Testar o novo enunciado no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A primeira intenção prevista é corretamente **FindForm**, com uma confiança acima de 90% (0,977). A entidade **Número de Formulário de Recursos Humanos** é extraída com um valor igual a hrf-234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você concluir este início rápido e não estiver passando para o próximo, selecione **Meus aplicativos** no menu de navegação superior. Em seguida, marque a caixa de seleção à esquerda do aplicativo na lista e selecione **Excluir** na barra de ferramentas de contexto acima da lista.

[![Excluir aplicativo da lista Meus aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [2. Implantar um aplicativo](get-started-portal-deploy-app.md)
