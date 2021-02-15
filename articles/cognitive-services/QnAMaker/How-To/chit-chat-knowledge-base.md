---
title: Adicionar um bate-papo a uma base de dados de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar um bate-papo pessoal ao seu bot o torna mais comunicativo e interessante ao criar uma KB. O QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1208d95a282ca6e236d9d6be8013f51dead90d13
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376632"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar bate-papo à base de dados de conhecimento

Adicionar bate-papo ao seu bot o torna mais comunicativo e interessante. O recurso de bate-papo do QnA Maker permite adicionar com facilidade um conjunto previamente populado dos principais bate-papos à sua KB (base de dados de conhecimento). Esse pode ser um ponto de partida para a personalidade do seu bot, o que economizará o tempo e o custo de escrevê-la do zero.

Esse conjunto de informações tem cerca de 100 cenários de Chit-Chat na voz de várias pessoas, como profissional, amigável e voluntária. Escolha a persona que mais se assemelha à voz do seu bot. Dada uma consulta de usuário, o QnA Maker tenta correspondê-la às perguntas e respostas do bate-papo conhecido mais próximo.

Veja abaixo alguns exemplos dos diferentes personalidades. Você pode ver todos os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de informações de personalidade junto com os detalhes do personalidades.

Para a consulta de usuário do `When is your birthday?` , cada personalidade tem uma resposta com estilo:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Professional|A idade não se aplica de fato a mim.|
|Amigável|Eu realmente não tenho uma idade.|
|Voluntariay|Sou sem idade.|
|Preocupar|Não tenho uma idade.|
|Entusiasmado|Sou um bot, então eu não tenho uma idade.|
||


## <a name="language-support"></a>Suporte ao idioma

Chit-os conjuntos de dados de chat têm suporte nos seguintes idiomas:

|Idioma|
|--|
|Chinês|
|Inglês|
|Francês|
|Alemanha|
|Italiano|
|Japonês|
|Coreano|
|Português|
|Espanhol|


## <a name="add-chit-chat-during-kb-creation"></a>Adicionar bate-papo durante a criação da KB
Durante a criação da base de dados de conhecimento, depois de adicionar suas URLs e arquivos de origem, há uma opção para adicionar um bate-papo. Escolha a personalidade que você deseja como sua base de bate-papo. Se você não quiser adicionar um bate-papo ou se já tiver um bate-papo compatível nas suas fontes de dados, escolha **Nenhum**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar bate-papo a uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Há um link para todos os conjuntos de dados de bate-papo no formato **.tsv** apropriado. Baixe a personalidade que você desejar e, em seguida, carregue-a como uma fonte de arquivo. Não edite o formato ou os metadados ao baixar e carregar o arquivo.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

![Adicionar bate-papo a uma KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

# <a name="qna-maker-managed-preview-release"></a>[Gerenciado QnA Maker (versão de visualização)](#tab/v2)

![Adicionar Chit-Chat à versão de visualização da KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset-v2.png)

---

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar as perguntas e respostas do seu bate-papo
Ao editar sua KB, você verá uma nova fonte para bate-papo, com base na personalidade que você selecionou. Agora é possível adicionar perguntas alteradas ou editar as respostas, assim como com qualquer outra fonte.

![Editar as perguntas e respostas do bate-papo](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para exibir os metadados, selecione **Exibir opções** na barra de ferramentas e selecione **Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicionar perguntas e respostas adicionais ao bate-papo
Você pode adicionar um novo par Chit-Chat QnA que não esteja no conjunto de dados predefinido. Verifique se você não está duplicando um par de pergunta e resposta já abordado no conjunto de bate-papo. Ao adicionar novas perguntas e respostas ao bate-papo, elas são adicionadas à fonte **Editorial**. Para garantir que o classificador entenda que se trata de Chit, adicione o par chave/valor de metadados "editorial: Chitchat", como mostrado na imagem a seguir:

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="Adicionar perguntas e respostas ao bate-papo" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>Excluir o bate-papo de uma KB existente
Selecione sua KB e, em seguida, navegue para a página **Configurações**. Sua fonte de bate-papo é listada como um arquivo, com o nome de personalidade selecionada. Você pode excluí-lo como um arquivo de origem.

![Excluir o bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Confira também

[Visão geral do QnA Maker](../Overview/overview.md)