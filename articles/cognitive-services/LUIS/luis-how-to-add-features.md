---
title: Usar recursos para melhorar a lista de palavras do LUIS
titleSuffix: Azure Cognitive Services
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para adicionar recursos de aplicativos que podem melhorar a detecção ou previsão de intenções e entidades que as categorias e padrões
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999919"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Usar recursos para impulsionar o sinal da lista de palavras

É possível adicionar recursos ao aplicativo de LUIS para melhorar a precisão. Os recursos ajudam o LUIS fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário do domínio do aplicativo.

Examine os [conceitos](luis-concept-feature.md) para entender quando e por que usar um recurso.

## <a name="add-phrase-list-as-a-feature"></a>Adicionar lista de frases como um recurso

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione **Compilar** e, em seguida, selecione **recursos** no painel esquerdo do seu aplicativo.

1. Na página **recursos** , selecione **+ criar**.

1. Na caixa de diálogo **criar novo recurso de lista de frases** , insira um nome como `Cities` . Na caixa **valor** , insira exemplos das cidades, como `Seattle` . É possível digitar um valor por vez ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de cidades de recurso (lista de frases)](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de inserir valores suficientes para LUIS, serão exibidas sugestões. Você pode **+ adicionar todos** os valores propostos ou selecionar termos individuais.

1. Mantenha **esses valores intercambiáveis** verificados se as frases podem ser usadas de forma intercambiável.

1. A lista de frases pode ser aplicada a todo o aplicativo com a configuração **global** ou a um modelo específico (intenção ou entidade). Se você criar a lista de frases, como um _recurso_ de uma intenção ou entidade, a alternância não será definida para global. Nesse caso, o significado da alternância é que o recurso é local somente para esse modelo, portanto, _não global_ para o aplicativo.

1. Selecione **Concluído**. O novo recurso é adicionado à página de **recursos do ml** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Você pode excluir ou desativar uma lista de frases da barra de ferramentas contextual na página de **recursos do ml** .

## <a name="global-phrase-list-applies-to-entire-app"></a>A lista de frases globais se aplica ao aplicativo inteiro

Uma lista de frases deve ser aplicada à intenção ou entidade para ajudar, mas pode haver ocasiões em que uma lista de frases deve ser aplicada a todo o aplicativo como um recurso **global** .

Na página recursos de ML, selecione a lista de frases e, em seguida, selecione **tornar global** na barra de ferramentas contextual superior.

## <a name="model-as-a-feature"></a>Modelo como um recurso

Uma entidade pode ser um [recurso para uma intenção ou entidade](luis-concept-feature.md).

Para adicionar uma entidade como um recurso a uma intenção, selecione a intenção na página tentativas e, em seguida, selecione **+ Adicionar recurso** acima da barra de ferramentas contextual. A lista incluirá todas as listas de frases e entidades que podem ser aplicadas como recursos.

Para adicionar uma entidade como um recurso a outra entidade, você pode adicionar o recurso na página de detalhes da intenção usando a [paleta de entidades](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) ou pode [Adicionar o recurso](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) na página de detalhes da entidade.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar, editar, excluir ou desativar um recurso, [treine e teste o aplicativo](luis-interactive-test.md) novamente para ver se o desempenho melhora.
