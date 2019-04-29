---
title: Como usar chamadas à API com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar chamadas à API com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: aba3c2eb925370704ea52364891502a7a09cc9ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635699"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Como adicionar chamadas à API a um modelo Aprendiz de Conversa

Este tutorial mostra como adicionar chamadas à API ao modelo. Chamadas de API são funções que você define e grava em seu bot, e que podem ser chamadas pelo Conversation Learner.

## <a name="video"></a>Vídeo

[![Visualização do Tutorial de chamadas de API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Requisitos
O tutorial exige que o bot "tutorialAPICalls.ts" esteja em execução.

    npm run tutorial-api-calls

## <a name="details"></a>Detalhes

- As chamadas de API podem ler e manipular entidades.
- As chamadas de API têm acesso ao objeto do gerenciador de memória.
- As chamadas de API podem ter argumentos.

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web clique em "Importar Tutoriais" e selecione o modelo nomeado "Tutorial-14-APICalls".

### <a name="entities"></a>Entidades

Nós definimos uma entidade no modelo chamado `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Chamadas à API
O código para as chamadas de API é definido neste arquivo: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- O `RandomGreeting` retorna uma saudação aleatória definida na `greeting` matriz.
- O `Multiply` retorno de chamada irá multiplicar dois números passados pela ação que faz a chamada e retorna um resultado que pode ser renderizado na interface do usuário.
    - Observe que o gerenciador de memória é o primeiro argumento. 
    - Observe que os retornos de chamada de API podem levar várias entradas, neste caso `num1string` e `num2string`.
- O `ClearEntities` retorno de chamada limpa a entidade numérica de modo que o usuário possa digitar outro número. 
    - Ilustra como as chamadas à API podem manipular entidades.

### <a name="actions"></a>Ações
Criamos quatro ações. Três deles são ações de API "Não Aguarde", com o quarto é uma ação de "Texto" que pede ao usuário uma pergunta semelhante ao que temos visto em outros tutoriais. Para ver como cada um foi criado, faça o seguinte:
1. No painel esquerdo, clique em "Ações", em seguida, clique em uma das quatro ações listadas na grade.
2. Observe os valores de cada campo no formulário pop-up.
3. Observe o `Refresh` botão ao lado do campo de API.
    - Se fossemos interromper o Bot e alterar para as APIs enquanto a página de interface do usuário estiver ativa, em seguida, você pode clicar no `Refresh` botão para acompanhar as alterações mais recentes.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, Multiplicar e RandomGreeting
Todos os três dessas ações são do tipo de API. Cada um deles se baseiam nas funções de retorno de chamada de API para executar algum trabalho e, possivelmente, retornar um valor a ser apresentada ao usuário.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Qual número você deseja multiplicar por 12?"
Essa é a ação de "Texto" e ela simplesmente faz uma pergunta do usuário. Enquanto essa ação não interage realmente com um dos retornos de chamada de API, ela solicita que o usuário responda com um número que será lançado na memória de uma entidade que pode ser usada pela ação "Multiplicar" que usa um dos retornos de chamada de API.


### <a name="train-dialog"></a>Diálogo de Treinamento

Vamos examinar um “Diálogo de Treinamento”.

1. No painel esquerdo, clique em `Train Dialogs`, em seguida, no `New Train Dialog` botão.
2. Digite 'olá'.
3. Clique no botão `Score Actions`.
4. Selecione `RandomGreeting`. 
    - Isso executará a chamada à API de Saudação Aleatória.
    - Isso NÃO aguardará uma resposta do usuário.
5. Selecione `What number to do you want to multiply by 12?`
6. Digite um número, qualquer número e apenas um número.
    - Observe que o número foi automaticamente etiquetado como a `number` entidade.
7. Clique no botão `Score Actions`.
8. Selecione a `Multiply` ação.
    - Observe o resultado da multiplicação por 12.
    - Observe que a memória ainda contém o valor inserido para `number`
9. Selecione a `ClearEntities` Ação.
    - Observe que o valor de entidade `number` foi limpo da memória.
10. Clique no botão `Save`.

Agora você viu como registrar retornos de chamada à API, os padrões comuns e como definir argumentos e associar valores e entidades neles.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Cartões parte 1](./15-cards.md)
