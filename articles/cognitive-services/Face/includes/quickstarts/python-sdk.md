---
title: Início rápido da biblioteca de clientes Python de Detecção Facial
description: Use a biblioteca de clientes de Detecção Facial para o Python para detectar rostos, localizar semelhantes (pesquisa de rosto por imagem) e identificar rostos (pesquisa de reconhecimento facial).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: 0939216e1d96e898d7c45b9e7a3ec72453b263d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957974"
---
Comece a usar o reconhecimento facial usando a biblioteca de clientes de Detecção Facial para Python. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes de Detecção Facial para Python para:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um PersonGroup](#create-and-train-a-persongroup)
* [Identificar um rosto](#identify-a-face)
* [Verificar rostos](#verify-faces)

[Documentação de referência](/python/api/azure-cognitiveservices-vision-face/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Exemplos](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A instalação do Python deve incluir o [pip](https://pip.pypa.io/en/stable/). Você pode executar `pip --version` na linha de comando para verificar se o pip está instalado. Instale a versão mais recente do Python para obter o pip.
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial</a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando
 
### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um script do Python&mdash;*quickstart-file.py*, por exemplo. Em seguida, abra-o em seu editor ou IDE preferencial e importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py), que contém os exemplos de código neste início rápido.

Em seguida, crie variáveis para a chave e o ponto de extremidade do Azure do recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso da Detecção Facial que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes do Python de Detecção Facial.

|Nome|Descrição|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|Essa classe gerencia a funcionalidade de instantâneo; você pode usá-la para salvar temporariamente todos os dados de rosto baseados em nuvem e migrar esses dados para uma nova assinatura do Azure. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de Detecção Facial para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar e treinar um PersonGroup](#create-and-train-a-persongroup)
* [Identificar um rosto](#identify-a-face)
* [Verificar rostos](#verify-faces)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o ponto de extremidade para criar um objeto [FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

O código a seguir detecta um rosto em uma imagem remota. Ele imprime a ID do rosto detectada no console e também a armazena na memória do programa. Em seguida, ele detecta os rostos em uma imagem com várias pessoas e imprime as IDs deles no console também. Ao alterar os parâmetros no método [detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), você pode retornar informações diferentes com cada objeto [DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> Também é possível detectar rostos em uma imagem local. Confira os métodos [FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations), como **detect_with_stream**.

### <a name="display-and-frame-faces"></a>Exibir e enquadrar rostos

O código a seguir gera a imagem fornecida para a exibição e desenha retângulos em volta dos rostos usando a propriedade DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Uma moça com um retângulo vermelho desenhado ao redor da face](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências (pesquisa de rosto por imagem). Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="find-matches"></a>Encontrar correspondências

Primeiro, execute o código na seção acima ([Detectar faces em uma imagem](#detect-faces-in-an-image)) para salvar uma referência a um único rosto. Em seguida, execute o código a seguir para obter referências a vários rostos em uma imagem de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Em seguida, adicione o bloco de código a seguir para localizar instâncias do primeiro rosto no grupo. Confira o método [find_similar](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para saber como modificar esse comportamento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Imprimir correspondências

Use o código a seguir para imprimir os detalhes da correspondência no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-persongroup"></a>Criar e treinar um PersonGroup

O código a seguir cria um **PersonGroup** com três objetos **Person** diferentes. Ele associa cada **Person** a um conjunto de imagens de exemplo e, em seguida, treina para ser capaz de reconhecer cada pessoa. 

### <a name="create-persongroup"></a>Criar PersonGroup

Para percorrer esse cenário, você precisa salvar as seguintes imagens no diretório raiz do seu projeto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Esse grupo de imagens contém três conjuntos de imagens de rosto correspondentes a três pessoas diferentes. O código definirá três objetos **Person** e os associará a arquivos de imagem que começam com `woman`, `man` e `child`.

Depois de configurar suas imagens, defina um rótulo na parte superior do script para o objeto **PersonGroup** que você criará.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Adicione o código a seguir à parte inferior do script. Esse código cria um objeto **PersonGroup** e três objetos **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Atribuir rostos a pessoas

O código a seguir classifica as imagens pelo respectivo prefixo, detecta os rostos e atribui esses rostos a cada objeto **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> Você também pode criar um **PersonGroup** usando imagens remotas referenciadas pela URL. Confira os métodos de [PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations), como **add_face_from_url**.

### <a name="train-persongroup"></a>Treinar PersonGroup

Depois de atribuir os rostos, você precisa treinar o **PersonGroup** para que ele possa identificar as características visuais associadas a cada um dos objetos **Person** nele contidos. O código a seguir chama o método **train** assíncrono e sonda o resultado, imprimindo o status no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> A API de Detecção Facial é executada em um conjunto de modelos predefinidos que estão estáticos por natureza (o desempenho do modelo não regredirá nem será aprimorado conforme o serviço for executado). Os resultados que o modelo produz poderão ser alterados se a Microsoft atualizar o back-end do modelo sem fazer a migração para uma versão totalmente nova do modelo. Para aproveitar uma versão mais recente de um modelo, você pode treinar novamente seu **PersonGroup**, especificando o modelo mais recente como um parâmetro com as mesmas imagens de registro.

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar usa uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Ele compara cada rosto detectado com um **PersonGroup**, um banco de dados de diferentes objetos **Person** cujas características faciais são conhecidas.

> [!IMPORTANT]
> Para executar este exemplo, primeiro você precisa executar o código em [Criar e treinar um PersonGroup](#create-and-train-a-persongroup).

### <a name="get-a-test-image"></a>Obter uma imagem de teste

O código a seguir procura na raiz do projeto uma imagem _test-image-person-group.jpg_ e detecta os rostos existentes nela. Você pode encontrar essa imagem junto com aquelas usadas para o gerenciamento do **PersonGroup**: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificar faces em imagem

O método **Identify** usa uma matriz de rostos detectados e os compara a um **PersonGroup**. Se consegue fazer a correspondência entre um rosto detectado e um **Person**, ele salva o resultado. Esse código imprime os resultados de correspondência detalhados no console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verificar rostos

A operação de verificação toma uma ID de rosto e outra ID de rosto ou um objeto **Person** e determina se elas pertencem à mesma pessoa.

O código a seguir detecta rostos em duas imagens de origem e os verifica em relação a um rosto detectado em uma imagem de destino.

### <a name="get-test-images"></a>Obter imagens de teste

Os blocos de código a seguir declaram variáveis que apontarão para as imagens de origem e de destino para a operação de verificação.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Detectar rostos para verificação

O código a seguir detecta rostos nas imagens de origem e de destino e os salva em variáveis.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obter resultados da verificação

O código a seguir compara cada uma das imagens de origem com a imagem de destino e gera uma mensagem indicando se elas pertencem à mesma pessoa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo de reconhecimento facial no diretório do aplicativo com o comando `python`.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se você criou um **PersonGroup** neste início rápido e deseja excluí-lo, execute o seguinte código no script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca de clientes de Detecção Facial para Python para realizar tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial (Python)](/python/api/azure-cognitiveservices-vision-face/)

* [O que é o serviço de Detecção Facial?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
