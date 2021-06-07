---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para Python'
description: Use a biblioteca de clientes para Python do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares de chave/valor e dados de tabela de seus documentos personalizados.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: f28fba451a5fbad9efe76e0d6bd39488beebb103
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761193"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade. Confira a documentação de referência abaixo. 

[Documentação de referência](/python/api/azure-ai-formrecognizer) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Pacote (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * A instalação do Python deve incluir o [pip](https://pip.pypa.io/en/stable/). Você pode executar `pip --version` na linha de comando para verificar se o pip está instalado. Instale a versão mais recente do Python para obter o pip.
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (baixe e extraia o *sample_data.zip*).
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você poderá instalar a versão mais recente da biblioteca de clientes do Reconhecimento de Formulários com:

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> O SDK do Reconhecimento de Formulários 3.1.0 reflete a _versão prévia da API versão 2.1.2_. Use a [**API REST**](../../quickstarts/client-library.md) para a _versão prévia da API versão 2.1. 3_.

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> O SDK do Reconhecimento de Formulários 3.0.0 reflete a API v2.0

---

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo aplicativo Python em seu IDE ou editor preferido. Depois, importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), que contém os exemplos de código neste início rápido.

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Modelo de objeto

Com o Reconhecimento de Formulários, você pode criar dois tipos diferentes de cliente. O primeiro, `form_recognizer_client`, é usado para consultar o serviço em campos e conteúdo do formulário reconhecidos. O segundo, `form_training_client`, é usado para criar e gerenciar modelos personalizados que você pode usar para aprimorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` fornece operações para:

* Reconhecer campos e conteúdo do formulário usando modelos personalizados treinados para analisar seus formulários personalizados.
* Reconhecer o conteúdo do formulário, incluindo tabelas, linhas e palavras, sem a necessidade de treinar um modelo.
* Reconhecer campos comuns de recibos, usando um modelo de recibo pré-treinado no serviço do Reconhecimento de Formulários.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` fornece operações para:

* Treinar modelos personalizados para analisar todos os campos e valores encontrados nos seus formulários personalizados. Confira a [documento de serviço sobre o treinamento de módulo sem rótulo](#train-a-model-without-labels) para obter uma explicação mais detalhada da criação de um conjunto de dados de treinamento.
* Treinar modelos personalizados para analisar campos e valores específicos que você determina rotulando seus formulários personalizados. Confira a [documento de serviço sobre o treinamento de módulo com rótulo](#train-a-model-with-labels) para obter uma explicação mais detalhada da aplicação de rótulos a um conjunto de dados de treinamento.
* Gerenciar modelos criados em sua conta.
* Copiar um modelo personalizado de um recurso de Reconhecimento de Formulários para outro.

> [!NOTE]
> Os modelos também podem ser treinados usando uma interface gráfica do usuário, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como executar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para Python:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar layout](#analyze-layout)
* [Analisar recibos](#analyze-receipts)
* [Analisar cartões de visita](#analyze-business-cards)
* [Analisar faturas](#analyze-invoices)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar layout](#analyze-layout)
* [Analisar recibos](#analyze-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Autenticar o cliente

Aqui, você autenticará os objetos de cliente usando as variáveis de assinatura definidas acima. Você usará um objeto **AzureKeyCredential**, para, se necessário, atualizar a chave de API sem criar objetos de cliente.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Obter ativos para teste

Você precisará adicionar referências às URLs para os dados de treinamento e teste.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação da URL SAS":::
* Use as imagens de exemplo e de recibo incluídas nos exemplos abaixo (também disponíveis no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ou use as etapas acima para obter a URL SAS de um documento individual no Armazenamento de Blobs. 

> [!NOTE]
> Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](/python/api/azure-ai-formrecognizer) e nas [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Analisar layout

Use o Reconhecimento de Formulários para analisar tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo. Para obter mais informações sobre a extração de layout, confira o [Guia conceitual de layout](../../concept-layout.md).

Para analisar o conteúdo de um arquivo em uma determinada URL, use o método `begin_recognize_content_from_url`. O valor retornado é uma coleção de objetos `FormPage`: um para cada página no documento enviado. O código a seguir itera nesses objetos e imprime os pares de chave/valor extraídos e os dados da tabela.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Você também pode obter o conteúdo das imagens locais. Confira os métodos de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), como `begin_recognize_content`. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para obter cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-invoices"></a>Analisar faturas

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

Esta seção demonstra como analisar e extrair campos comuns de faturas de vendas usando um modelo pré-treinado. Para obter mais informações sobre a análise de faturas, confira o [Guia conceitual de faturas](../../concept-invoices.md). Para analisar as faturas por meio de uma URL, use o método `begin_recognize_invoices_from_url`. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Você também pode analisar imagens de faturas locais. Confira os métodos de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), como `begin_recognize_invoices`. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para obter cenários que envolvam imagens locais.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Este recurso não está disponível na versão selecionada da API.

---

## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para analisar todos os campos e valores encontrados nos formulários personalizados sem rotular manualmente os documentos de treinamento.

O código a seguir usa o cliente de treinamento com a função `begin_training` para treinar um modelo em um conjunto especificado de documentos. O objeto retornado `CustomFormModel` contém informações sobre os tipos de formulários que o modelo pode analisar e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Saída

Esta é a saída de um modelo treinado com os dados de treinamento disponíveis do [SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. O `CustomFormModel` retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

> [!IMPORTANT]
> Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais (`\<filename\>.pdf.labels.json`) no contêiner do Armazenamento de Blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame a função `begin_training` com o parâmetro *use_training_labels* definido como `true`.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Saída

Esta é a saída de um modelo treinado com os dados de treinamento disponíveis do [SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método `begin_recognize_custom_forms_from_url`. O valor retornado é uma coleção de objetos `RecognizedForm`: um para cada página no documento enviado. O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Você também pode analisar imagens locais. Confira os métodos de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), como `begin_recognize_custom_forms`. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para obter cenários que envolvam imagens locais.


### <a name="output"></a>Saída

Usando o modelo do exemplo anterior, a saída a seguir é fornecida.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="analyze-receipts"></a>Analisar recibos

Esta seção demonstra como analisar e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado. Para obter mais informações sobre a análise de recibos, confira o [Guia conceitual de recibos](../../concept-receipts.md). Para analisar os recibos de uma URL, use o método `begin_recognize_receipts_from_url`. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Você também pode analisar imagens de recibos locais. Confira os métodos de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), como `begin_recognize_receipts`. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para obter cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>Analisar cartões de visita

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

Esta seção demonstra como analisar e extrair campos comuns de cartões de visita em inglês usando um modelo pré-treinado. Para obter mais informações sobre a análise de cartões de visita, confira o [Guia conceitual de cartões de visita](../../concept-business-cards.md). Para analisar cartões de visita por meio de uma URL, use o método `begin_recognize_business_cards_from_url`. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Analise também imagens de cartões de visita locais. Confira os métodos de [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient), como `begin_recognize_business_cards`. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para obter cenários que envolvam imagens locais.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Este recurso não está disponível na versão selecionada da API.

---

## <a name="manage-your-custom-models"></a>Gerenciar seus modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Saída

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console. Ele também salva uma referência ao primeiro modelo.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Saída

Esta é uma saída de exemplo par aa conta de teste.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obter um modelo específico usando a ID do modelo

O bloco de código a seguir usa a ID do modelo salva na seção anterior e a usa para recuperar detalhes sobre o modelo.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Saída

Esta é a saída de exemplo para o modelo personalizado criado no exemplo anterior.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID. Esse código exclui o modelo usado na seção anterior.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` no seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="general"></a>Geral

A biblioteca de clientes do Reconhecimento de Formulários vai gerar as exceções definidas no [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Registro em log

Essa biblioteca usa a [biblioteca de log padrão](https://docs.python.org/3/library/logging.html) para o log. As informações básicas sobre as sessões HTTP (URLs, cabeçalhos etc.) são registradas em log no nível de INFO.

O log detalhado no nível de DEBUG, incluindo corpos de solicitação/resposta e cabeçalhos não redigidos, pode ser habilitado em um cliente com o argumento de palavra-chave `logging_enable`:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Da mesma forma, `logging_enable` pode habilitar o log detalhado para uma operação individual, mesmo quando ela não está habilitada para o cliente:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes Python do Reconhecimento de Formulários para treinar modelos personalizado e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
