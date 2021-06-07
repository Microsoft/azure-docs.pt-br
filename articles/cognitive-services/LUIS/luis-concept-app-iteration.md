---
title: Design de aplicativo iterativo-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 753d214b520affb59722bc29dbabc50c6e5968f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91538714"
---
# <a name="iterative-app-design-for-luis"></a>Design de aplicativo iterativo para LUIS

Um aplicativo Reconhecimento vocal (LUIS) aprende e executa com mais eficiência com iteração. Aqui está um ciclo de iteração típico:

* Criar nova versão
* Edite o esquema do aplicativo LUIS. Isso inclui:
    * Tentativas com o exemplo declarações
    * Entidades
    * Recursos
* Treinar, testar e publicar
    * Testar no ponto de extremidade de previsão para o aprendizado ativo
* Coletar dados de consultas de ponto de extremidade

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Criando um esquema LUIS

O esquema de um aplicativo define o que o usuário está solicitando (a _intenção_ ou a _intenção_ ) e quais partes da intenção fornecem detalhes (chamados de _entidades_) que são usadas para ajudar a determinar a resposta.

O esquema do aplicativo deve ser específico para os domínios de aplicativo para determinar palavras e frases relevantes, bem como para determinar a ordenação típica de palavras.

Exemplos declarações representam entradas de usuário, como fala ou texto reconhecido, que o aplicativo espera no tempo de execução.

O esquema requer tentativas e _deve ter_ entidades.

### <a name="example-schema-of-intents"></a>Esquema de exemplo de tentativas

O esquema mais comum é um esquema de intenção organizado com tentativas. Esse tipo de esquema usa LUIS para determinar a intenção de um usuário.

O tipo de esquema de intenção pode ter entidades se ajudar LUIS a determinar a intenção do usuário. Por exemplo, uma entidade de remessa (como um _recurso de aprendizado de máquina_ para uma intenção) ajuda a Luis a determinar uma intenção de envio.

### <a name="example-schema-of-entities"></a>Esquema de exemplo de entidades

Um esquema de entidade se concentra em entidades, que são os dados extraídos do usuário declarações. Por exemplo, se um usuário deveria dizer: "Eu gostaria de pedir três pizzas". Há duas entidades que seriam extraídas: _três_ e _pizzas_. Eles são usados para ajudar a atender à intenção, que era fazer um pedido.

Para um esquema de entidade, a intenção do expressão é menos importante para o aplicativo cliente.

Um método comum de organizar um esquema de entidade é adicionar todos os declarações de exemplo à intenção de **nenhum** .

### <a name="example-of-a-mixed-schema"></a>Exemplo de um esquema misto

O esquema mais potente e maduro é um esquema de intenção com uma gama completa de entidades e recursos. Esse esquema pode começar como um esquema de intenção ou de entidade e aumentar para incluir os conceitos de ambos, uma vez que o aplicativo cliente precisa dessas informações.

## <a name="add-example-utterances-to-intents"></a>Adicionar declarações de exemplo a intenções

LUIS precisa de alguns exemplos de declarações em cada **tentativa**. O exemplo declarações precisa de uma variação suficiente de opção de palavra e de palavra para poder determinar a qual intenção o expressão se destina.

> [!CAUTION]
> Não adicione o exemplo declarações em massa. Comece com 15 a 30 exemplos específicos e variados.

Cada exemplo de expressão precisa ter todos os **dados necessários para extrair** e rotulados com **entidades**.

|Elemento Key|Finalidade|
|--|--|
|Intencional|**Classifique** o declarações do usuário em uma única intenção ou ação. Os exemplos incluem `BookFlight` e `GetWeather`.|
|Entidade|**Extraia** os dados do expressão necessários para concluir a intenção. Os exemplos incluem data e hora de viagem e local.|

Um aplicativo LUIS pode ser projetado para ignorar declarações que não são relevantes para o domínio de um aplicativo atribuindo o expressão à tentativa de **nenhum** .

## <a name="test-and-train-your-app"></a>Testar e treinar seu aplicativo

Depois de ter 15 a 30 exemplos diferentes de declarações em cada tentativa, com as entidades necessárias rotuladas, você precisa testar e [treinar](luis-how-to-train.md) seu aplicativo Luis.

## <a name="publish-to-a-prediction-endpoint"></a>Publicar em um ponto de extremidade de previsão

O aplicativo LUIS deve ser publicado para que esteja disponível para você nas [regiões de ponto de extremidade de previsão](luis-reference-regions.md)de lista.

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

Você pode testar seu aplicativo LUIS publicado do ponto de extremidade de previsão HTTPS. O teste do ponto de extremidade de previsão permite que o LUIS escolha qualquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Criar uma nova versão para cada ciclo

Cada versão é um instantâneo no momento do aplicativo LUIS. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para uma versão mais antiga do que tentar remover tentativas e declarações para um estado anterior.

A ID da versão é composta de caracteres, dígitos ou '.' e não pode ser maior do que 10 caracteres.

A versão inicial (0.1) é a versão ativa padrão.

### <a name="begin-by-cloning-an-existing-version"></a>Comece clonando uma versão existente

Clone uma versão existente para usar como um ponto de partida para cada nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa** .

### <a name="publishing-slots"></a>Slots de publicação

Você pode publicar no estágio e/ou nos slots de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isso é útil para verificar as alterações antes de publicar para produção, que está disponível para bots ou outros aplicativos de chamada LUIS.

Versões treinadas não estão disponíveis automaticamente no ponto de [extremidade](luis-glossary.md#endpoint)do seu aplicativo Luis. Você deve [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que ela esteja disponível no ponto de extremidade do aplicativo Luis. Você pode publicar para **preparo** e **produção**, fornecendo duas versões do aplicativo disponíveis no ponto de extremidade. Se mais versões do aplicativo precisarem estar disponíveis em um ponto de extremidade, você deverá exportar a versão e importá-la novamente para um novo aplicativo. O novo aplicativo tem uma ID do aplicativo diferente.

### <a name="import-a-version"></a>Importar uma versão

Uma versão pode ser **importada** como uma nova:
* Aplicativo, com uma nova ID do aplicativo
* Versão de um aplicativo existente

Essa versão se torna a versão ativa e usa a ID da versão na `versionId` Propriedade do arquivo do aplicativo.

### <a name="export-a-version"></a>Exportar uma versão

Uma versão pode ser **exportada** do portal do Luis no nível do aplicativo ou nível de versão:

* Nível do aplicativo – selecione aplicativo na página **meus aplicativos** e, em seguida, selecione **Exportar**
* Nível de versão – selecione o link do aplicativo na página **meus aplicativos** , selecione **gerenciar**, selecione **versões**

A única diferença é que o nível de aplicativo, a versão exportada, é a versão ativa no momento no nível de versão, você pode escolher qualquer versão para exportar na página de **[configurações](luis-how-to-manage-versions.md)** .

O arquivo exportado **não** contém:

* informações de aprendizado de máquina, porque o aplicativo é retreinado depois de ser importado
* Informações do colaborador

Para fazer backup do esquema do aplicativo LUIS, exporte uma versão do [portal do Luis](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gerenciar alterações de colaborador com versões e colaboradores

O LUIS usa o conceito de colaboradores para um aplicativo, fornecendo permissões no nível de recurso do Azure. Combine esse conceito com o controle de versão para fornecer colaboração direcionada.

Use as técnicas a seguir para gerenciar alterações de colaborador em seu aplicativo.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerenciar várias versões dentro do mesmo aplicativo

Comece pela [clonagem](luis-how-to-manage-versions.md#clone-a-version) de uma versão base para cada autor.

Cada autor faz alterações em sua própria versão do aplicativo. Quando o autor for satisfeito com o modelo, exporte as novas versões para arquivos JSON.

Aplicativos exportados, `.json` ou `.lu` arquivos, podem ser comparados para alterações. Combine os arquivos para criar um único arquivo da nova versão. Altere a `versionId` propriedade para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

Esse método permite que você tenha uma versão ativa, uma versão do estágio e uma versão publicada. Você pode comparar os resultados da versão ativa com uma versão publicada (fase ou produção) no painel de [teste interativo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerenciar várias versões como aplicativos

[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa o aplicativo é o proprietário da versão. Quando ela terminar de modificar o aplicativo, exporte a versão.

Aplicativos exportados são arquivos formatados para JSON, que podem ser comparados com a exportação base quanto a alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **versionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

Saiba mais sobre a criação de contribuições de [colaboradores](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Examinar o ponto de extremidade declarações para iniciar o novo ciclo iterativo

Quando terminar com um ciclo de iteração, você poderá repetir o processo. Comece com [revisar ponto de extremidade de previsão declarações](luis-how-to-review-endpoint-utterances.md) Luis marcado com baixa confiança. Verifique essas declarações para a entidade prevista correta e correta e completa extraída. Depois de revisar e aceitar as alterações, a lista de revisão deve estar vazia.

## <a name="next-steps"></a>Próximas etapas

Conheça conceitos sobre [colaboração](luis-how-to-azure-subscription.md).
