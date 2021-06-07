---
title: Início rápido da biblioteca de clientes Python do Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste início rápido, saiba como começar a usar a biblioteca de clientes do Content Moderator do Azure para Python. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 06ff04d8615b7ebdda07e993a3fc560d44fbf702
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104455"
---
Introdução à biblioteca de clientes do Content Moderator do Azure para Python. Siga essas etapas para instalar o pacote PiPy e testar o código de exemplo para tarefas básicas. 

O Content Moderator é um serviço de IA que permite que você lide com conteúdos potencialmente ofensivos, arriscados ou, de alguma outra forma, indesejáveis. Use o serviço de moderação de conteúdo baseado em IA para digitalizar texto, imagem e vídeos e aplicar sinalizadores de conteúdo automaticamente. Em seguida, integre seu aplicativo à ferramenta de Análise, um ambiente de moderador online para uma equipe de revisores humanos. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.

Use a biblioteca de clientes do Content Moderator para Python para:

* Moderar texto
* Usar uma lista de termos personalizados
* Moderar imagens
* Usar uma lista de imagem personalizada
* Criar uma revisão

[Documentação de referência](/python/api/overview/azure/cognitiveservices/contentmoderator) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A instalação do Python deve incluir o [pip](https://pip.pypa.io/en/stable/). Você pode executar `pip --version` na linha de comando para verificar se o pip está instalado. Instale a versão mais recente do Python para obter o pip.
* Depois de obter uma assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Criar um recurso do Content Moderator"  target="_blank">crie um recurso do Content Moderator</a> no portal do Azure para obter uma chave e um ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Será necessário obter uma chave e um ponto de extremidade do recurso criado para conectar o aplicativo ao Content Moderator. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.


## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Após instalar o Python, você pode instalar a biblioteca de clientes do Content Moderator com o seguinte comando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo script do Python e abra-o em seu IDE ou editor preferido. Depois, adicione as seguintes instruções `import` à parte superior do arquivo.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py), que contém os exemplos de código neste início rápido.

Em seguida, crie variáveis para a localização do ponto de extremidade do recurso e a chave.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Acesse o portal do Azure. Caso o recurso do Content Moderator criado na seção **Pré-requisitos** tenha sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos da biblioteca de clientes do Python do Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)|Essa classe é necessária para toda a funcionalidade do Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo para adulto, informações pessoais ou rostos humanos.|
|[TextModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
[ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations)|Essa classe fornece a funcionalidade das APIs de Revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Content Moderator para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Moderar texto](#moderate-text)
* [Usar uma lista de termos personalizados](#use-a-custom-terms-list)
* [Moderar imagens](#moderate-images)
* [Usar uma lista de imagem personalizada](#use-a-custom-image-list)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o ponto de extremidade para criar um objeto [ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderar texto

O código a seguir usa um cliente do Content Moderator para analisar um corpo de texto e imprimir os resultados no console. Primeiro, crie uma pasta **text_files/** na raiz do seu projeto e adicione um arquivo *content_moderator_text_moderation. txt*. Adicione seu próprio texto a este arquivo ou use o seguinte texto de exemplo:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Adicione uma referência à nova pasta.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Depois, adicione o seguinte código ao script do Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Usar uma lista de termos personalizados

O código a seguir mostra como gerenciar uma lista de termos personalizados para moderação de texto. Você pode usar a classe [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations) para criar uma lista de termos, gerenciar os termos individuais e a tela de outros corpos de texto em relação a ela.

### <a name="get-sample-text"></a>Obter texto de exemplo

Para usar este exemplo, crie uma pasta **text_files/** na raiz do seu projeto e adicione um arquivo *content_moderator_term_list.txt*. Esse arquivo deve conter um texto orgânico que será verificado em relação à lista de termos. Você pode usar o seguinte texto de exemplo:

```
This text contains the terms "term1" and "term2".
```

Adicione uma referência à pasta se você ainda não tiver definido uma.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Cria uma lista

Adicione o seguinte código ao seu script do Python para criar uma lista de termos personalizados e salvar seu valor de ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definir detalhes da lista

Você pode usar a ID da lista para editar seu nome e sua descrição.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Adicionar um termo à lista

O código a seguir adiciona os termos `"term1"` e `"term2"` à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Obter todos os termos da lista

Você pode usar a ID da lista para retornar todos os termos da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Sempre que adicionar ou remover termos da lista, você deverá atualizar o índice antes de poder usar a lista atualizada.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Texto da tela em relação à lista

A principal funcionalidade da lista de termos personalizados é comparar um corpo de texto com a lista e descobrir se há termos correspondentes. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Remover um termo de uma lista

O seguinte código remove o termo `"term1"` da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Excluir todos os termos de uma lista

Use o seguinte código para limpar uma lista de todos os seus termos.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Excluir uma lista

Use o código a seguir para excluir uma lista de termos personalizados.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Moderar imagens

O seguinte código usa um cliente do Content Moderator, juntamente com um objeto [ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations), para analisar imagens de conteúdo adulto e obsceno.

### <a name="get-sample-images"></a>Obter imagens de exemplo

Defina uma referência a algumas imagens para analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Depois, adicione o seguinte código para iterar pelas imagens. O restante do código nesta seção entrará nesse loop.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Verificar se há conteúdo adulto ou erótico

O seguinte código verifica a imagem na URL fornecida para conteúdo adulto ou obsceno e imprime os resultados no console. Consulte o guia de [Conceitos de moderação de imagem](../../image-moderation-api.md) para obter informações sobre o que esses termos significam.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Verificar texto visível

O código a seguir verifica a imagem em busca de conteúdo de texto visível e imprime os resultados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificar se há rostos

O código a seguir verifica a imagem em busca de rostos humanos e imprime os resultados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Usar uma lista de imagem personalizada

O código a seguir mostra como gerenciar uma lista personalizada de imagens para moderação de imagem. Esse recurso será útil se a plataforma receber com frequência instâncias do mesmo conjunto de imagens que você deseja eliminar. Ao manter uma lista dessas imagens específicas, você pode melhorar o desempenho. A classe [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations) permite que você crie uma lista de imagens, gerencie as imagens individuais na lista e compare outras imagens com ela.

Crie as variáveis de texto a seguir para armazenar as URLs de imagem que você usará nesse cenário.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Essa não é a própria lista em si, mas uma lista informal de imagens que será adicionada na seção `add images` do código.


### <a name="create-an-image-list"></a>Criar uma lista de imagens

Adicione o código a seguir para criar uma lista de imagens e salvar uma referência à sua ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Adicionar imagens a uma lista

O código a seguir adiciona todas as suas imagens à lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Defina a função auxiliar **add_images** em outro lugar no seu script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Obter imagens na lista

O código a seguir imprime os nomes de todas as imagens na sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Atualizar detalhes da lista

Você pode usar a ID da lista para atualizar o nome e a descrição da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Obter detalhes da lista

Use o código a seguir para imprimir os detalhes atuais da sua lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Atualizar o índice da lista

Depois de adicionar ou remover imagens, você deve atualizar o índice da lista antes de poder usá-lo para fazer a triagem de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Corresponder as imagens em relação à lista

A função principal das listas de imagens é comparar novas imagens e ver se há quaisquer correspondências.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Remover uma imagem da lista

O seguinte código remove um item da lista. Nesse caso, é uma imagem que não corresponde à categoria da lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Excluir todas as imagens de uma lista

Use o código a seguir para limpar uma lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Excluir a lista de imagens

Use o código a seguir para excluir uma determinada lista de imagens.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Criar uma revisão

Você pode usar a biblioteca de clientes do Python do Content Moderator para alimentar conteúdo na [Ferramenta de análise](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam analisá-lo. Para saber mais sobre a ferramenta Revisão, confira o [Guia conceitual da ferramenta Revisão](../../review-tool-user-guide/human-in-the-loop.md).

O código a seguir usa a classe [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations) para criar uma revisão, recuperar sua ID e verificar seus detalhes depois de receber a entrada humana por meio do portal da Web da Ferramenta de análise.

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Primeiro, entre na Ferramenta de análise e recupere o nome da equipe. Em seguida, atribua-o à variável apropriada no código. Como alternativa, você pode configurar um ponto de extremidade de retorno de chamada para receber atualizações sobre a atividade da revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Criar uma revisão de imagens

Adicione o código a seguir para criar e lançar uma revisão para uma determinada URL da imagem. O código salva uma referência na ID de revisão. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Obter detalhes de revisão

Use o código a seguir para verificar os detalhes de uma determinada revisão. Depois de criar a revisão, você pode ir para a ferramenta de análise por conta própria e interagir com o conteúdo. Para saber mais sobre como fazer isso, confira o [Guia de instruções de revisões](../../review-tool-user-guide/review-moderated-images.md). Quando terminar, você poderá executar esse código novamente e ele recuperará os resultados do processo de revisão.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Se você usou um ponto de extremidade de retorno de chamada neste cenário, ele deverá receber um evento neste formato:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca Python do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
>[Conceitos de moderação de imagem](../../image-moderation-api.md)
