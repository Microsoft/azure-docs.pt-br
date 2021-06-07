---
title: Início rápido da biblioteca de clientes .NET de Detecção Facial
description: Use a biblioteca de clientes de Detecção Facial para .NET para detectar rostos, localizar semelhantes (pesquisa facial por imagem), identificar rostos (pesquisa de reconhecimento facial) e migrar os dados de rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: ab99d574588989c84783e532fcf801dcaffdd54d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958258"
---
Comece a usar o reconhecimento facial usando a biblioteca de clientes de Detecção Facial para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a biblioteca de clientes de Detecção Facial para .NET para:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar um PersonGroup](#create-a-persongroup)
* [Identificar um rosto](#identify-a-face)

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/faceapi) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [Exemplos](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Pré-requisitos


* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Usando o Visual Studio, crie um aplicativo .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Depois de criar um projeto, instale a biblioteca de clientes clicando com o botão direito do mouse na solução do projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Microsoft.Azure.CognitiveServices.Vision.Face`. Selecione a versão `2.6.0-preview.1` e, em seguida, **Instalar**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `face-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*. 

```console
dotnet new console -n face-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Dentro do diretório do aplicativo, instale a biblioteca de clientes de Detecção Facial para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), que contém os exemplos de código neste início rápido.


No diretório do projeto, abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Na classe **Program** do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.


> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso da Detecção Facial que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

No método **Main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os implementará mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com alguns dos principais recursos da biblioteca de clientes do .NET de Detecção Facial:

|Nome|Descrição|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Essa classe representa sua autorização para usar o serviço de Detecção Facial e é necessária para todas as funcionalidades desse serviço. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Essa classe manipula as tarefas básicas de detecção e reconhecimento que você pode realizar com rostos humanos. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Essa classe representa todos os dados detectados de um único rosto em uma imagem. Você pode usá-la para recuperar informações detalhadas sobre o rosto.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Essa classe gerencia os constructos **FaceList** armazenados em nuvem, que armazenam um conjunto classificado de rostos. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Essa classe gerencia os constructos **Person** armazenados em nuvem, que armazenam um conjunto de rostos que pertencem a uma única pessoa.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Essa classe gerencia os constructos **PersonGroup** armazenados em nuvem, que armazenam um conjunto classificado de objetos **Person**. |

## <a name="code-examples"></a>Exemplos de código

Os snippets de código abaixo mostram como realizar as seguintes tarefas com a biblioteca de clientes de Detecção Facial para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)
* [Criar um PersonGroup](#create-a-persongroup)
* [Identificar um rosto](#identify-a-face)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, instancie um cliente com o ponto de extremidade e a chave. Crie um objeto **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** com sua chave e use-o com seu ponto de extremidade para criar um objeto **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Declarar campos auxiliares

Os campos a seguir são necessários para várias das operações de Face que você adicionará posteriormente. Na raiz de sua classe **Program**, defina a cadeia de caracteres de URL a seguir. Essa URL aponta para uma pasta de imagens de exemplo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

No método **Main**, defina cadeias de caracteres para apontar para os diferentes tipos de modelo de reconhecimento. Posteriormente, você poderá especificar qual modelo de reconhecimento você deseja usar para detecção facial. Confira [Especificar um modelo de reconhecimento](../../Face-API-How-to-Topics/specify-recognition-model.md) para obter informações sobre essas opções.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

### <a name="get-detected-face-objects"></a>Detectar objetos de rosto

Crie um método para detectar rostos. O método `DetectFaceExtract` processa três das imagens na URL fornecida e cria uma lista de objetos **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** na memória do programa. A lista de valores **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** especifica quais recursos extrair. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Também é possível detectar rostos em uma imagem local. Confira os métodos de [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations), como **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Exibir dados do rosto detectado

O restante do método `DetectFaceExtract` analisa e imprime os dados de atributos para cada rosto detectado. Cada atributo deve ser especificado separadamente na chamada à API de detecção facial (na lista **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** ). O código a seguir processa todos os atributos, mas provavelmente você só precisará usar um ou alguns deles.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

O código a seguir usa um rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências (pesquisa de rosto por imagem). Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar rostos para comparação

Primeiro, defina um segundo método de detecção facial. Você precisa detectar rostos em imagens antes de poder compará-las. Esse método de detecção é otimizado para operações de comparação. Ele não extrai atributos de rosto detalhados, como na seção acima, e usa um modelo de reconhecimento diferente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Encontrar correspondências

O método a seguir detecta rostos em um conjunto de imagens de destino e em uma única imagem de origem. Em seguida, compara os e encontra todas as imagens de destino semelhantes à imagem de origem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Imprimir correspondências

O código a seguir imprime os detalhes de correspondência no console:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identificar um rosto

A operação Identificar usa uma imagem de uma pessoa (ou de várias pessoas) e procura encontrar a identidade de cada rosto na imagem (pesquisa de reconhecimento facial). Ele compara cada rosto detectado com um **PersonGroup**, um banco de dados de diferentes objetos **Person** cujas características faciais são conhecidas. Para fazer a operação de identificação, primeiro você precisa criar e treinar um **PersonGroup**

### <a name="create-a-persongroup"></a>Criar um PersonGroup

O código a seguir cria um **PersonGroup** com seis objetos **Person** diferentes. Ele associa cada **Person** a um conjunto de imagens de exemplo e, em seguida, o treina para reconhecer cada pessoa por suas características faciais. Os objetos **Person** e **PersonGroup** são usados nas operações Verificar, Identificar e Agrupar.

Declare uma variável de cadeia de caracteres na raiz de sua classe para representar a ID do **PersonGroup** a ser criado.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Em um novo método, adicione o seguinte código. Esse método executará a operação de Identificação. O primeiro bloco de código associa os nomes de pessoas a suas imagens de exemplo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Observe que esse código define uma variável `sourceImageFileName`. A variável corresponde à imagem de origem&mdash;à imagem que contém pessoas a serem identificadas.

Em seguida, adicione o seguinte código para criar um objeto **Person** para cada pessoa no Dicionário e adicione os dados de rosto das imagens apropriadas. Cada objeto **Person** está associado ao mesmo **PersonGroup** por meio de sua cadeia de caracteres de ID exclusiva. Lembre-se de passar as variáveis `client`, `url` e `RECOGNITION_MODEL1` para esse método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Você também pode criar um **PersonGroup** usando imagens locais. Confira os métodos de [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson), como **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Treinar o PersonGroup

Após ter extraído dados de rosto de suas imagens e os ter classificado em diferentes objetos **Person**, você deverá treinar o **PersonGroup** para identificar os recursos associados a cada um de seus objetos **Person**. O código a seguir chama o método **train** assíncrono e sonda os resultados, imprimindo o status no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> A API de Detecção Facial é executada em um conjunto de modelos predefinidos que estão estáticos por natureza (o desempenho do modelo não regredirá nem será aprimorado conforme o serviço for executado). Os resultados que o modelo produz poderão ser alterados se a Microsoft atualizar o back-end do modelo sem fazer a migração para uma versão totalmente nova do modelo. Para aproveitar uma versão mais recente de um modelo, você pode treinar novamente seu **PersonGroup**, especificando o modelo mais recente como um parâmetro com as mesmas imagens de registro.

Esse grupo **Person** e seus objetos **Person** associados agora estão prontos para serem usados nas operações Verificar, Identificar ou Agrupar.

### <a name="identify-faces"></a>Identificar faces em imagem

O código a seguir usa a imagem de origem e cria uma lista de todos os rostos detectados nela. Esses são os rostos que serão identificados no **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

O snippet de código a seguir chama a operação **IdentifyAsync** e imprime os resultados no console. Aqui o serviço tenta corresponder cada rosto da imagem de origem a uma **Pessoa** no **PersonGroup** fornecido. Isso fecha o método Identify.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Execute o aplicativo

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Execute o aplicativo clicando no botão **Depurar** na parte superior da janela do IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Se você tiver criado um **PersonGroup** neste início rápido e desejar excluí-lo, execute o seguinte código em seu programa:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Defina o método de exclusão com o seguinte código:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca de clientes de Detecção Facial para .NET a fim de realizar tarefas básicas de reconhecimento do rosto. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [O que é o serviço de Detecção Facial?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
