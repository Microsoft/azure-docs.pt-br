---
title: Como usar a Marcação de Versão com um Modelo do Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o controle de versão e a marcação com um modelo de aprendiz de conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5073d3ab967c4c4e1b90636c247839875a6aa0d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640032"
---
# <a name="how-to-use-version-tagging"></a>Como usar a Marcação de Versão

Este tutorial ilustra como marcar versões do seu Modelo do Conversation Learner e definir qual versão está "ativa".  

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do Bot Framework Emulator para criar Diálogos de Log, e não a interface do usuário da Web do Diálogo de Log.  

Este tutorial requer que o Bot de tutorial geral esteja em execução:

    npm run tutorial-general

## <a name="details"></a>Detalhes

Versões com marcação do Modelo são estáticas; não é possível editar ou alterar elas. Ao editar seu modelo, você sempre está editando a versão Mestre. Quando você adiciona uma nova Marca, o Conversation Learner captura um instantâneo do Modelo nesse ponto no tempo. 

Seu Bot usará a versão do Modelo que você selecionou como a versão "Ativa", mas quaisquer conversas que ela tenha serão visíveis somente quando a "Marca de Edição" estiver definida como "Mestre". Se a propriedade "Editar Marca" do modelo estiver definida como algo que não seja "Mestre", você poderá exibir o instantâneo do Modelo, mas não poderá alterá-lo de maneira nenhuma.

## <a name="steps"></a>Etapas

### <a name="install-the-bot-framework-emulator"></a>Instalar o Bot Framework Emulator

1. Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Baixe e instale o emulador.

### <a name="create-a-model"></a>Criar um modelo

1. Na Home Page de Lista do Modelo, clique no botão `New Model`.
2. No campo `Name`, digite "Tutorial-18-Versioning" e pressione enter.
4. No painel esquerdo, clique em "Configurações".
5. Copie o conteúdo do campo CONVERSATION_LEARNER_MODEL_ID na área de transferência.

### <a name="configure-the-emulator"></a>Configurar o Emulador

1. Na pasta raiz do Conversation Learner, abra o arquivo ".env".
2. Adicione uma linha para o arquivo ". env" como esta:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Reinicie o serviço Conversation Learner ao sair do prompt de comando e execute novamente:
    - `npm run tutorial-general`
4. No Bot Framework Emulator, crie uma nova configuração de bot, defina a URL de Ponto de Extremidade para `http://localhost:3978/api/messages`

### <a name="version-1"></a>Versão 1

Vamos criar uma única Ação para a Versão 1.

1. No painel esquerdo da interface do usuário da Web, clique em "Ações" e, em seguida, clique no botão `New Action`.
2. No campo "Resposta do Bot", digite "olá (versão 1)".
3. Clique no botão `Save`.

Agora, vamos marcar isso como "Versão 1" do Modelo.

1. No painel esquerdo, clique em "configurações" e, em seguida, clique no ícone ![](../media/tutorial18_version_tags.PNG) "Marcas de Versão" para revelar o botão `New Tag` no qual você deve clicar.
    - Nomeie como "Versão 1"
1. Na lista suspensa"Marca Ativa", selecione "Versão 1".  
    - Agora os canais que usam esse Bot usarão a "Versão 1" do nosso Modelo.
    - As Entidades, Ações e Diálogos de Treinamento desse Modelo Versão 1 não poderão mais ser alteradas.
    - Se você selecionar "Versão 1" como "Marca de Edição" SOMENTE poderá exibir o Modelo e não editá-lo.
    - Deixe "Marca de Edição" definido como "Mestre", pois é a única versão do modelo que pode ser editada.

Agora você verá "Versão 1" na grade "Marcas de Versão".

### <a name="version-2"></a>Versão 2

Agora, editaremos o nosso modelo para diferenciá-lo da Versão 1.

1. No painel esquerdo, clique em "Ações".
2. Na grade Ações, clique em "olá (versão 1)".
3. Altere o campo "Resposta do Bot" para "olá (versão 2)".
4. Clique no botão `Save`.
5. Clique no botão `New Action`.
6. No campo "Resposta do Bot", digite "até logo (versão 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Confirme se o Bot Framework Emulator está usando a Versão 1

1. No Bot Framework Emulator, digite a mensagem, "Olá".
2. Observe que o Bot responde com "olá (versão 1)".
    - Isso verifica se a Versão 1 está "ativa".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Exibir os Logs de Conversa na IU da Web do Conversation Learner

1. No painel esquerdo, clique em "Diálogos de Log"
    - Se você não visualizar nenhum diálogo, clique no botão Atualizar.
2. Observe a marca "Versão 1" na grade.
3. Na grade, clique em "olá (versão 1)"

> [!NOTE]
> É possível fazer correções escolhendo dentre todas as funcionalidades do Conversation Learner disponíveis no momento, no entanto, essas edições serão feitas no Mestre e não na Versão 1.

Agora você viu como funciona o controle de versão e como é possível interagir com o Bot usando o Bot Framework Emulator.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração – redefinição de senha](./demo-password-reset.md)
