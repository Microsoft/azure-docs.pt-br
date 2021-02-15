---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para JavaScript'
description: Use a biblioteca de clientes para JavaScript do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares de chave/valor e dados de tabela de seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e5a131753829edddbb4f385766a2d8697ebd0106
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584580"
---
> [!IMPORTANT]
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Confira a documentação de referência abaixo. 

[Documentação de referência](../../index.yml) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Pacote (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do [Node.js](https://nodejs.org/)
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (baixe e extraia o *sample_data.zip*).
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
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

Instale o pacote NPM do `ai-form-recognizer`:

```console
npm install @azure/ai-form-recognizer
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

Crie um arquivo chamado `index.js`, abra-o e importe as seguintes bibliotecas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), que contém os exemplos de código neste início rápido.

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso do Reconhecimento de Formulários que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

## <a name="object-model"></a>Modelo de objeto 

Com o Reconhecimento de Formulários, você pode criar dois tipos diferentes de cliente. O primeiro, `FormRecognizerClient`, é usado para consultar o serviço em campos e conteúdo do formulário reconhecidos. O segundo, `FormTrainingClient`, é usado para criar e gerenciar modelos personalizados que você pode usar para aprimorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` fornece operações para:

 * Reconhecer campos e conteúdo do formulário usando modelos personalizados treinados para analisar seus formulários personalizados. Esses valores são retornados em uma coleção de objetos `RecognizedForm`.
 * Reconhecer o conteúdo do formulário, incluindo tabelas, linhas e palavras, sem a necessidade de treinar um modelo. O conteúdo do formulário é retornado em uma coleção de objetos `FormPage`.
 * Reconhecer campos comuns de recibos, usando um modelo de recibo pré-treinado no serviço do Reconhecimento de Formulários. Esses campos e metadados são retornados em uma coleção de `RecognizedReceipt`.

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` fornece operações para:

* Treinar modelos personalizados para analisar todos os campos e valores encontrados nos seus formulários personalizados. Um `CustomFormModel` é retornado indicando os tipos de formulário que o modelo analisará e os campos que serão extraídos para cada tipo de formulário. Confira a [documento de serviço sobre o treinamento de módulo sem rótulo](#train-a-model-without-labels) para obter uma explicação mais detalhada da criação de um conjunto de dados de treinamento.
* Treinar modelos personalizados para analisar campos e valores específicos que você determina rotulando seus formulários personalizados. Um `CustomFormModel` é retornado indicando os campos que o modelo extrairá, bem como a precisão estimada de cada campo. Confira a [documento de serviço sobre o treinamento de módulo com rótulo](#train-a-model-with-labels) para obter uma explicação mais detalhada da aplicação de rótulos a um conjunto de dados de treinamento.
* Gerenciar modelos criados em sua conta.
* Copiar um modelo personalizado de um recurso de Reconhecimento de Formulários para outro.

> [!NOTE]
> Os modelos também podem ser treinados usando uma interface gráfica do usuário, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários](../../quickstarts/label-tool.md).


## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como executar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar layout](#analyze-layout)
* [Analisar recibos](#analyze-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticar o cliente



Autentique um objeto de cliente usando as variáveis de assinatura definidas. Você usará um objeto `AzureKeyCredential`, para, se necessário, atualizar a chave de API sem criar objetos de cliente. Você também criará um objeto de cliente de treinamento.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Obter ativos para teste

Você também precisará adicionar referências às URLs para os dados de treinamento e teste.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
   
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação da URL SAS":::
* Use as imagens de exemplo e de recibo incluídas nos exemplos abaixo (também disponíveis no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)) ou use as etapas acima para obter a URL SAS de um documento individual no armazenamento de blobs. 


## <a name="analyze-layout"></a>Analisar layout

Use o Reconhecimento de Formulários para analisar tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo. Para obter mais informações sobre a extração de layout, confira o [Guia conceitual de layout](../../concept-layout.md). Para analisar o conteúdo de um arquivo em um determinado URI, use o método `beginRecognizeContentFromUrl`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> Você também pode obter o conteúdo em um arquivo local. Confira os métodos de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), como **beginRecognizeContent**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para obter cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```



## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para analisar todos os campos e valores encontrados nos formulários personalizados sem rotular manualmente os documentos de treinamento.

A função a seguir treina um modelo em um conjunto especificado de documentos e imprime o status do modelo no console. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>Saída

Esta é a saída de um modelo treinado com os dados de treinamento disponíveis do [SDK do JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer). Esta saída de exemplo foi truncada para facilitar a leitura.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais (`\<filename\>.pdf.labels.json`) no contêiner do Armazenamento de Blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame o método `beginTraining` com o parâmetro `uselabels` definido como `true`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>Saída 

Esta é a saída de um modelo treinado com os dados de treinamento disponíveis do [SDK do JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples). Esta saída de exemplo foi truncada para facilitar a leitura.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método `beginRecognizeCustomFormsFromUrl`. O valor retornado é uma coleção de objetos `RecognizedForm`: um para cada página no documento enviado.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Você também pode analisar arquivos locais. Confira os métodos de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), como **beginRecognizeCustomForms**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para obter cenários que envolvam imagens locais.


### <a name="output"></a>Saída

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="analyze-receipts"></a>Analisar recibos

Esta seção demonstra como analisar e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado. Para obter mais informações sobre a análise de recibos, confira o [Guia conceitual de recibos](../../concept-receipts.md).

Para analisar os recibos de um URI, use o método `beginRecognizeReceiptsFromUrl`. O código a seguir processa um recibo no URI fornecido e imprime os valores e os campos principais no console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Você também pode analisar imagens de recibos locais. Confira os métodos de [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest), como **beginRecognizeReceipts**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para obter cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="manage-your-custom-models"></a>Gerenciar seus modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. O código a seguir executa todas as tarefas de gerenciamento de modelos em uma só função, como um exemplo. 

### <a name="get-number-of-models"></a>Obter o número de modelos

O bloco de código a seguir obtém o número de modelos atualmente em sua conta.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>Obter lista de modelos na conta

O bloco de código a seguir fornece uma lista completa dos modelos disponíveis em sua conta, incluindo informações sobre quando o modelo foi criado e o status atual dele.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>Saída

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Obter lista de IDs de modelo por página

Esse bloco de código fornece uma lista paginada de modelos e IDs de modelo.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>Saída

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Obter o modelo por ID

A função a seguir usa uma ID de modelo e obtém o objeto de modelo correspondente. Essa função não é chamada por padrão.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID. Essa função exclui o modelo com a ID fornecida. Essa função não é chamada por padrão.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>Saída

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="enable-logs"></a>Habilitar logs

Você pode definir a variável de ambiente a seguir para ver os logs de depuração ao usar essa biblioteca.

```
export DEBUG=azure*
```

Para obter instruções mais detalhadas sobre como habilitar logs, confira a [documentação do pacote @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes JavaScript do Reconhecimento de Formulários para treinar modelos personalizados e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).
