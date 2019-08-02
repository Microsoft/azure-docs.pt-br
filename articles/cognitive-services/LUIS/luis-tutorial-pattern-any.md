---
title: Entidade pattern.any
titleSuffix: Azure Cognitive Services
description: Use a entidade pattern.any para extrair dados de enunciados quando o enunciado está bem formatado e quando o fim dos dados pode ser facilmente confundido com as palavras restantes do enunciado.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 6007f88af4d1049a87851b3808c66693173a648a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069242"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Tutorial: Extrair dados de forma livre com a entidade Pattern.any

Neste tutorial, use a entidade pattern.any para extrair dados de enunciados quando o enunciado está bem formatado e quando o fim dos dados pode ser facilmente confundido com as palavras restantes do enunciado. 

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Adicionar enunciados de exemplo à entidade existente
> * Criar uma entidade pattern.any
> * Criar padrão
> * Treinar
> * Testar novo padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Usando a entidade Pattern.any

A entidade pattern.any permite localizar dados de formulário livre em que as palavras da entidade dificultam determinar o fim da entidade no resto do enunciado. 

Este aplicativo de Recursos Humanos ajuda os funcionários a encontrar formulários da empresa. 

|Enunciado|
|--|
|Onde está **HRF-123456**?|
|Quem criou **HRF 123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, usado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`. 

Enunciados com o nome amigável do formulário se parecem com:

|Enunciado|
|--|
|Onde está **Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018**?|
|Quem criou **“Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018”** ?|
|**Solicitar a realocação do funcionário novo para a versão 5 da empresa 2018** está publicado em francês?|

O comprimento variável inclui palavras que podem confundir o LUIS sobre onde a entidade termina. Usando uma entidade Pattern.any em um padrão permite especificar o início e o fim do nome do formulário para que o LUIS extraia corretamente o nome do formulário.

|Exemplo de enunciado de modelo|
|--|
|Onde está {FormName}[?]|
|Quem criou {FormName}[?]|
|{FormName} foi publicado em francês[?]|

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

1. Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

1. No [portal LUIS](https://www.luis.ai), na página **Meus aplicativos**, importe o JSON em um novo aplicativo.

1. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `patt-any`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="add-example-utterances"></a>Adicionar enunciados de exemplo 

1. Selecione **Construir** no painel de navegação superior e, em seguida, selecione **Intenções** na barra de navegação esquerda.

1. Selecione **FindForm** na lista de intenções.

1. Adicione alguns enunciados de exemplo:

    |Exemplo de enunciado|
    |--|
    |Onde está o formulário **O que fazer quando ocorrer um incêndio no laboratório** e quem precisa para assiná-lo depois que eu lê-lo?|
    |Onde está **Solicitar a realocação do funcionário novo para a empresa** no servidor?|
    |Quem criou "**Solicitações de saúde e bem-estar no campus principal**" e qual é a versão mais atual?|
    |Estou procurando o formulário nomeado "**Solicitação de mudança do escritório incluindo ativos físicos**". |

    Sem uma entidade Pattern.any, seria difícil para o LUIS reconhecer onde o título do formulário termina por causa das muitas variações dos nomes de formulário.

## <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Isso só funciona em um padrão, porque o ele marca o início e o fim da entidade.  

1. Selecione **Entidades** no painel de navegação esquerdo.

1. Selecione **Criar nova entidade**, insira o nome `FormName` e selecione **Pattern.any** como o tipo. Selecione **Concluído**. 

    Não é possível identificar a entidade no enunciado de exmeplo da intenção porque uma Pattern.any somente é válida em um padrão. 

    Se você quiser que os dados extraídos incluam outras entidades, como número ou datetimeV2, será necessário criar uma entidade de composição que inclua a Pattern.any, bem como o número e o datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que usa o Pattern.any

1. Selecione **Padrões** no painel de navegação esquerdo.

1. Selecione a intenção **FindForm**.

1. Insira os seguintes enunciados de modelo que usam a nova entidade:

    |Enunciados de modelo|
    |--|
    |Qual é o formulário ["]{FormName}["] e quem precisa para assiná-lo depois que eu lê-lo[?]|
    |Onde está ["]{FormName}["] no servidor[?]|
    |Quem criou ["]{FormName}["] e qual é a versão mais atual[?]|
    |Estou procurando o formulário nomeado ["]{FormName}["][.]|

    Se você quiser contabilizar variações do formulário, como aspas simples em vez de aspas duplas ou um ponto final em vez de um ponto de interrogação, crie um novo padrão para cada variação.

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão quanto à extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste. 

1. Insira o enunciado a seguir: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selecione **Inspecionar** embaixo do resultado para ver os resultados de teste para a entidade e para a intenção.

    A entidade `FormName` é encontrada primeiro e o padrão é encontrado depois, determinando a intenção. Se tiver um resultado do teste em que as entidades não forem detectadas e, portanto, o padrão não for encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão).

1. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

## <a name="using-an-explicit-list"></a>Uso de uma lista explícita

Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir esse problema.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial adicionou enunciados de exemplo a uma intenção existente e, depois, criou um novo pattern.any para o nome do formulário. Em seguida, criou um padrão para a intenção existente com os novos enunciados de exemplo e a entidade. Um teste interativo mostrou que o padrão e sua intenção foram previstos porque a entidade foi encontrada. 

> [!div class="nextstepaction"]
> [Saiba como usar funções com um padrão](luis-tutorial-pattern-roles.md)
