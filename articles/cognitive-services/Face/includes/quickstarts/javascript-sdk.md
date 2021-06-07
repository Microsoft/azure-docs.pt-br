---
title: Início rápido da biblioteca de clientes JavaScript da Detecção Facial
description: Use a biblioteca de clientes de Detecção Facial para JavaScript para detectar rostos, localizar semelhantes (pesquisa facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados de rosto.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 8f968572a357bb3c98d9c3133a7ec0a0a94dbf93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958214"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Início Rápido: Biblioteca de clientes de Detecção Facial para JavaScript

Comece a usar o reconhecimento facial usando a biblioteca de clientes de Detecção Facial para JavaScript. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes de Detecção Facial para JavaScript para:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar um PersonGroup](#create-a-persongroup)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](/javascript/api/@azure/cognitiveservices-face/) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [Pacote (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [Exemplos](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão mais recente do [Node.js](https://nodejs.org/en/)
* Depois de obter sua assinatura do Azure, [Crie um recurso da Detecção Facial](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Instale os pacotes NPM `ms-rest-azure` e `azure-cognitiveservices-face`:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

Crie um arquivo chamado `index.js` e importe as seguintes bibliotecas:

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](), que contém os exemplos de código neste início rápido.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. 

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso da Detecção Facial que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com alguns dos principais recursos da biblioteca de clientes do .NET de Detecção Facial:

|Nome|Descrição|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[Detecção Facial](/javascript/api/@azure/cognitiveservices-face/face)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram como realizar as seguintes tarefas com a biblioteca de clientes de Detecção Facial para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar um PersonGroup](#create-a-persongroup)
* [Identificar um rosto](#identify-a-face)

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), que contém os exemplos de código neste início rápido.

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instancie um cliente com o ponto de extremidade e a chave. Crie um objeto **[ApiKeyCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)** com sua chave e use-o com seu ponto de extremidade para criar um objeto **[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient)** .

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Declarar valores globais e uma função auxiliar

Os valores globais a seguir são necessários para várias das operações de Detecção Facial que você adicionará posteriormente.

A URL aponta para uma pasta de imagens de exemplo. O UUID servirá como o nome e a ID para o PersonGroup que você criará.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Você usará a função a seguir para aguardar a conclusão do treinamento do PersonGroup.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

### <a name="get-detected-face-objects"></a>Detectar objetos de rosto

Crie um método para detectar rostos. O método `DetectFaceExtract` processa três das imagens na URL fornecida e cria uma lista de objetos **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** na memória do programa. A lista de valores **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** especifica quais recursos extrair. 

Depois, o método `DetectFaceExtract` analisa e imprime os dados de atributos para cada rosto detectado. Cada atributo deve ser especificado separadamente na chamada à API de detecção facial (na lista **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ). O código a seguir processa todos os atributos, mas provavelmente você só precisará usar um ou alguns deles.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Também é possível detectar rostos em uma imagem local. Confira os métodos de [Detecção Facial](/javascript/api/@azure/cognitiveservices-face/face), como [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências (pesquisa de rosto por imagem). Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar rostos para comparação

Primeiro, defina um segundo método de detecção facial. Você precisa detectar rostos em imagens antes de poder compará-las. Esse método de detecção é otimizado para operações de comparação. Ele não extrai atributos de rosto detalhados, como na seção acima, e usa um modelo de reconhecimento diferente.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Encontrar correspondências

O método a seguir detecta rostos em um conjunto de imagens de destino e em uma única imagem de origem. Em seguida, compara os e encontra todas as imagens de destino semelhantes à imagem de origem. Por fim, ele imprime os detalhes de correspondência no console.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identificar um rosto

A operação [Identificar](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) usa uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Ele compara cada rosto detectado com um [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup), um banco de dados de diferentes objetos [Person](/javascript/api/@azure/cognitiveservices-face/person) cujas características faciais são conhecidas. Para fazer a operação de identificação, primeiro você precisa criar e treinar um [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

### <a name="add-faces-to-persongroup"></a>Adicionar rostos a um PersonGroup

Crie a função a seguir para adicionar rostos ao [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>Aguardar o treinamento de PersonGroup

Crie a função auxiliar a seguir para aguardar a conclusão do treinamento de **PersonGroup**.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>Criar um PersonGroup

O seguinte código:
- Cria um [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup)
- Adiciona rostos ao **PersonGroup** chamando `AddFacesToPersonGroup`, que você definiu anteriormente.
- Treina o **PersonGroup**.
- Identifica os rostos no **PersonGroup**.

Esse **PersonGroup** e seus objetos **Person** associados estão prontos para serem usados nas operações Verificar, Identificar ou Agrupar.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Você também pode criar um **PersonGroup** usando imagens locais. Confira os métodos de [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson), como [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Principal

Por fim, crie a função `main` e chame-a.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca de clientes de Detecção Facial para JavaScript para realizar as tarefas de reconhecimento facial de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência de API de Detecção Facial (JavaScript)](/javascript/api/@azure/cognitiveservices-face/)

* [O que é o serviço de Detecção Facial?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).