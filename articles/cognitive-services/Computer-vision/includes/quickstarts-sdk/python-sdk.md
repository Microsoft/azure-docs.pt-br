---
title: 'Guia de início rápido: Biblioteca de clientes de reconhecimento óptico de caracteres para Python'
description: Comece a usar a biblioteca de clientes de reconhecimento óptico de caracteres para Python com este guia de início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: d0b2a854391097cc7d95c4286ba581f3660d397e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284732"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes de reconhecimento óptico de caracteres para ler textos impressos e manuscritos com a API de Leitura.

[Documentação de referência](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A instalação do Python deve incluir o [pip](https://pip.pypa.io/en/stable/). Você pode executar `pip --version` na linha de comando para verificar se o pip está instalado. Instale a versão mais recente do Python para obter o pip.
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Criar um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional</a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.

    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

É possível instalar a biblioteca de clientes com:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Instale também a biblioteca Pillow.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), que contém os exemplos de código neste início rápido.

Crie um arquivo do Python&mdash;*quickstart-file.py*, por exemplo. Depois, abra-o no seu editor ou IDE preferido.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontrar a chave de assinatura e o ponto de extremidade

Acesse o portal do Azure. Se o recurso da Pesquisa Visual Computacional criado na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave de assinatura e o ponto de extremidade na página **Chave e ponto de extremidade** do recurso em **Gerenciamento de recursos**. 

Crie variáveis para a chave de assinatura e o ponto de extremidade da Pesquisa Visual Computacional. Cole a chave de assinatura e o ponto de extremidade no código a seguir, quando indicado. O ponto de extremidade da Pesquisa Visual Computacional tem o formato `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de assinatura do código quando terminar e nunca a poste publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de OCR do Python.

|Nome|Descrição|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Essa classe manipula diretamente todas as operações de imagem, como análise de imagem, detecção de texto e geração de miniaturas.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. Ela implementa **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Essa enumeração define os diferentes tipos de análise de imagem que podem ser feitos em uma operação de análise padrão. Especifique um conjunto de valores **VisualFeatureTypes** dependendo de suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de OCR para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com sua chave e use-o com seu ponto de extremidade para criar um objeto [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço de OCR pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. Faça isso em duas partes.

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Primeiro, use o código a seguir para chamar o método **read** para a imagem especificada. Isso retorna uma ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> Você também pode ler um texto de uma imagem local. Confira os métodos de [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin), como **read_in_stream**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) para obter cenários que envolvam imagens locais.

### <a name="get-read-results"></a>Obter os resultados da leitura

Em seguida, obtenha a ID da operação retornada da chamada **read** e use-a para consultar o serviço para obter os resultados da operação. O código a seguir verifica a operação em intervalos de um segundo até que os resultados sejam retornados. Em seguida, ele imprime os dados de texto extraídos no console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Li o texto](?success=read-printed-handwritten-text#run-the-application) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Executei o aplicativo](?success=run-the-application#clean-up-resources) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca de OCR para Python a fim de realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência da API de OCR (Python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Visão geral de OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
