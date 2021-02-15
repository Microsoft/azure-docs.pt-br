---
title: Melhores práticas – QnA Maker
description: Use essas melhores práticas para melhorar a base de dados de conhecimento e fornecer melhores resultados aos usuários finais do aplicativo/chat.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 77d4a1cad4843578419be54465a06e87834dd186
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104511"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Melhores práticas de uma base de dados de conhecimento do QnA Maker

O [ciclo de vida de desenvolvimento da base de dados de conhecimento](../Concepts/development-lifecycle-knowledge-base.md) informa como gerenciar a base de dados de conhecimento de ponta a ponta. Use essas práticas recomendadas para melhorar sua base de dados de conhecimento e fornecer resultados melhores para o aplicativo cliente ou para os usuários finais do bate-papo.

## <a name="extraction"></a>Extração

O serviço QnA Maker está melhorando continuamente os algoritmos que extraem perguntas e respostas do conteúdo e está expandindo a lista de formatos de arquivo e de HTML com suporte. Siga as [diretrizes](../index.yml) de extração de dados com base em seu tipo de documento.

Em geral, as páginas de perguntas frequentes devem ser independentes e não combinadas com outras informações. Manuais de produtos devem ter títulos claros e, de preferência, uma página de índice.

### <a name="configuring-multi-turn"></a>Configurando o multi-Transform

[Crie sua base de dados de conhecimento](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) com a extração múltipla habilitada. Se sua base de dados de conhecimento faz ou deve oferecer suporte à hierarquia de perguntas, essa hierarquia pode ser extraída do documento ou criada depois que o documento é extraído.

## <a name="creating-good-questions-and-answers"></a>Criando boas perguntas e respostas

### <a name="good-questions"></a>Boas perguntas

As melhores perguntas são simples. Considere a palavra-chave ou frase para cada pergunta, em seguida, crie uma pergunta simples para essa palavra-chave ou frase.

Adicione quantas perguntas alternativas você precisa, mas mantenha as alterações simples. Adicionar mais palavras ou frases que não fazem parte da meta principal da pergunta não ajuda o QnA Maker a encontrar uma correspondência.


### <a name="add-relevant-alternative-questions"></a>Adicionar perguntas alternativas relevantes

O usuário pode inserir perguntas com um estilo de conversa de texto `How do I add a toner cartridge to my printer?` ou uma pesquisa de palavra-chave, como `toner cartridge` . A base de dados de conhecimento deve ter os dois estilos de perguntas para retornar corretamente a melhor resposta. Se você não tiver certeza de quais palavras-chave um cliente está inserindo, use Application Insights dados para analisar consultas.

### <a name="good-answers"></a>Boas respostas

As melhores respostas são respostas simples, mas não muito simples. Não use respostas como `yes` e `no` . Se sua resposta deve ser vinculada a outras fontes ou fornecer uma experiência rica com mídia e links, use a [marcação de metadados](../how-to/edit-knowledge-base.md#add-metadata) para distinguir entre as respostas e, em seguida, [envie a consulta com as](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) marcas de metadados na `strictFilters` propriedade para obter a versão de resposta correta.

|Resposta|Prompts de Follup|
|--|--|
|Desligue o laptop da superfície com o botão de energia no teclado.|* Combinações de teclas para suspensão, desligamento e reinicialização.<br>* Como inicializar um laptop de superfície<br>* Como alterar o BIOS de um laptop de superfície<br>* Diferenças entre suspensão, desligamento e reinicialização|
|O atendimento ao cliente está disponível por telefone, Skype e mensagem de texto 24 horas por dia.|* Informações de contato para vendas.<br> * Localizações e horas do escritório e da loja para uma visita pessoal.<br> * Acessórios para um laptop Surface.|

## <a name="chit-chat"></a>Bate-papo
Adicione bate-papo ao seu bot para torná-lo mais conversacional e envolvente com pouco esforço. Você pode adicionar facilmente conjuntos de dados do Chit-Chat de personalidades predefinidos ao criar seu KB e alterá-los a qualquer momento. Saiba como [adicionar bate-papo à base de dados de conhecimento](../How-To/chit-chat-knowledge-base.md).

Chit-há suporte para o chat em [muitos idiomas](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Escolhendo uma personalidade
Chit-o chat tem suporte para vários personalidades predefinidos:

|Personalidade |Arquivo do conjunto de QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Amigável |[qna_chitchat_friendly. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Voluntariay |[qna_chitchat_witty. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Preocupar |[qna_chitchat_caring. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasmado |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

As respostas vão de formal e informal a irreverente. Selecione a personalidade mais alinhada com o tom que você deseja que o bot tenha. Você pode exibir os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)de dados e escolher um que sirva como base para o bot e, em seguida, personalizar as respostas.

### <a name="edit-bot-specific-questions"></a>Editar perguntas específicas do bot
Há algumas perguntas específicas do bot que fazem parte do conjunto de dados de bate-papo e que foram preenchidas com respostas genéricas. Altere essas respostas para refletir melhor os detalhes de seu bot.

Recomendamos que você torne as seguintes perguntas e respostas do bate-papo mais específicas:

* quem é você?
* o que você pode fazer?
* Quantos anos você tem?
* Quem criou você?
* Olá

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adicionando Chit personalizado-chat com uma marca de metadados

Se você adicionar seus próprios pares de QnA Chit-Chat, certifique-se de adicionar metadados para que essas respostas sejam retornadas. O par nome/valor de metadados é `editorial:chitchat` .

## <a name="searching-for-answers"></a>Procurando respostas

A API do GenerateAnswer usa as duas perguntas e a resposta para pesquisar as melhores respostas para a consulta de um usuário.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Perguntas de pesquisa somente quando a resposta não é relevante

Use o [`RankerType=QuestionOnly`](#choosing-ranker-type) se você não quiser pesquisar respostas.

Um exemplo disso é quando a base de dados de conhecimento é um catálogo de acrônimos como perguntas com sua forma completa como resposta. O valor da resposta não ajudará a procurar a resposta apropriada.

## <a name="rankingscoring"></a>Classificação/pontuação
Certifique-se de que você está utilizando da melhor forma os recursos de classificação com suporte do QnA Maker. Isso aumentará a probabilidade de que uma determinada consulta de usuário seja respondida com uma resposta apropriada.

### <a name="choosing-a-threshold"></a>Escolhendo um limite

A [Pontuação de confiança](confidence-score.md) padrão usada como um limite é 0, no entanto, você pode [alterar o limite](confidence-score.md#set-threshold) para a KB com base em suas necessidades. Como cada base de dados de conhecimento é diferente, você precisa testar e escolher o limite mais adequado à sua.

### <a name="choosing-ranker-type"></a>Escolhendo o tipo de classificação
Por padrão, QnA Maker pesquisa perguntas e respostas. Se você quiser pesquisar apenas as perguntas, para gerar uma resposta, use o `RankerType=QuestionOnly` no corpo da postagem da solicitação GenerateAnswer.

### <a name="add-alternate-questions"></a>Adicionar perguntas alternativas
[Perguntas alternativas](../How-To/edit-knowledge-base.md) melhoram a probabilidade de uma correspondência com uma consulta de usuário. Perguntas alternativas são úteis quando há várias maneiras de como a mesma pergunta pode ser feita. Isso pode incluir alterações na estrutura da frase e no estilo das expressões.

|Consulta original|Consultas alternativas|Alterar|
|--|--|--|
|Há estacionamento disponível?|Vocês têm estacionamento?|estrutura da frase|
 |Oi|E aí<br>Olá!|estilo das expressões ou gírias|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Use marcas de metadados para filtrar perguntas e respostas

Os [metadados](../How-To/edit-knowledge-base.md) adicionam a capacidade de um aplicativo cliente saber que ele não deve receber todas as respostas, mas sim restringir os resultados de uma consulta de usuário com base nas marcas de metadados. A resposta da base de dados de conhecimento poderá variar com base na marca de metadados, mesmo se a consulta for a mesma. Por exemplo, *"onde o estacionamento está localizado"* pode ter uma resposta diferente se o local da ramificação do restaurante for diferente, ou seja, os metadados forem *Localização: Seattle* versus *local: Redmond*.

### <a name="use-synonyms"></a>Usar sinônimos
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)
Embora haja suporte para sinônimos no idioma inglês, use as alterações de palavras que não diferenciam maiúsculas de minúsculas por meio da [API de alterações](/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinônimos a palavras-chave que usam diferentes formas. Os sinônimos são adicionados no nível de serviço QnA Maker e **compartilhados por todas as bases de conhecimento no serviço**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)
Embora haja suporte para sinônimos no idioma inglês, use as alterações de palavras que não diferenciam maiúsculas de minúsculas por meio da [API de alterações](/rest/api/cognitiveservices/qnamaker/alterations/replace) para adicionar sinônimos a palavras-chave que usam diferentes formas. Os sinônimos em QnA Maker gerenciados (versão prévia) são **adicionados por base de dados de conhecimento**.

|Palavra original|Sinônimos|
|--|--|
|comprar|comprar<br>NET-Banking<br>Internet banking|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Use palavras distintas para diferenciar perguntas
O algoritmo de classificação do QnA Maker, que corresponde uma consulta de usuário a uma pergunta na base de conhecimento, funciona melhor se cada pergunta atende a uma necessidade diferente. A repetição do mesmo conjunto de palavras entre as perguntas reduz a probabilidade da resposta correta ser escolhida para uma determinada consulta de usuário com essas palavras.

Por exemplo, você pode ter duas perguntas e respostas separadas com as seguintes perguntas:

|Perguntas e respostas|
|--|
|qual é a *localização* do estacionamento|
|onde está a *localização* do caixa eletrônico|

Uma vez que essas perguntas e respostas são formuladas com palavras muito semelhantes, essa semelhança poderia levar a pontuações muito semelhantes para muitas consultas do usuário formuladas como *"qual é a localização de `<x>`"*. Em vez disso, tente diferenciar claramente usando consultas como *"onde fica o estacionamento"* e *"onde fica o caixa eletrônico"*, evitando palavras como "localização", que poderiam estar em muitas perguntas em sua base de dados.

## <a name="collaborate"></a>Colaboração
O QnA Maker permite que os usuários colaborem em uma base de dados de conhecimento. Os usuários precisam de acesso ao grupo de recursos do QnA Maker do Azure para acessar as bases de dados de conhecimento. Algumas organizações podem querer terceirizar a edição e manutenção da base de dados de conhecimento e ainda proteger o acesso aos recursos do Azure. Esse modelo de editor-aprovador é realizado configurando dois [serviços do QnA Maker](../How-to/set-up-qnamaker-service-azure.md) idênticos em assinaturas diferentes e designando um deles para o ciclo de teste e edição. Quando o teste estiver concluído, o conteúdo da base de dados de conhecimento será transferido com um processo de [importação-exportação](../Tutorials/migrate-knowledge-base.md) para o serviço do QnA Maker do aprovador que, por fim, publicará a base de dados de conhecimento e atualizará o ponto de extremidade.



## <a name="active-learning"></a>Aprendizado ativo

O [aprendizado ativo](../How-to/use-active-learning.md) faz o melhor trabalho de sugerir perguntas alternativas quando ele tem diversas consultas baseadas no usuário de qualidade e quantidade variadas. É importante permitir que as consultas do usuário dos aplicativos cliente participem do loop de comentários do aprendizado ativo sem censura. Quando as perguntas são sugeridas no portal de QnA Maker, você pode **[Filtrar por sugestões](../How-To/improve-knowledge-base.md)** e, em seguida, revisar e aceitar ou rejeitar essas sugestões.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-to/edit-knowledge-base.md)
