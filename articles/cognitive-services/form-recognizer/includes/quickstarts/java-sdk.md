---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para Java'
description: Use a biblioteca de clientes para Java do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares de chave/valor e dados de tabela de seus documentos personalizados.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/19/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: a709f82b04ed5c1fe70f6927b33605cfff20ed6b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761241"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> O código neste artigo usa métodos síncronos e armazenamento de credenciais não seguras por motivos de simplicidade.

[Documentação de referência](/java/api/overview/azure/ai-formrecognizer-readme) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [Exemplos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (baixe e extraia o *sample_data.zip*).

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando criará arquivos de build essenciais para o Gradle, incluindo *build.gradle.kts*, que é usado em runtime para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Este início rápido usa o gerenciador de dependência do Gradle. Você pode encontrar a biblioteca de clientes e informações para outros gerenciadores de dependência no [Repositório Central do Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

No arquivo *build.gradle.kts* do projeto, inclua a biblioteca de clientes como uma instrução `implementation`, juntamente com as configurações e os plug-ins necessários.

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> O SDK do Reconhecimento de Formulários 3.1.0 reflete a _versão prévia da API versão 2.1.2_. Use a [**API REST**](../../quickstarts/client-library.md) para a _versão prévia da API versão 2.1. 3_.

#### <a name="v20"></a>[v2.0](#tab/ga)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

> [!NOTE]
> O SDK do Reconhecimento de Formulários 3.0.0 reflete a API v2.0

---

### <a name="create-a-java-file"></a>Criar um arquivo Java


Do diretório de trabalho, execute o seguinte comando:

```console
mkdir -p src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *FormRecognizer.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), que contém os exemplos de código neste início rápido.


Na classe **FormRecognizer** do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se o recurso do Reconhecimento de Formulários que você criou na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Ir para o Recurso** em **Próximas etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

No método **main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os definirá mais tarde. Você também precisará adicionar referências às URLs para os dados de treinamento e teste.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação da URL SAS":::
* Para obter uma URL de um formulário para teste, use as etapas acima para obter a URL de SAS de um documento individual no armazenamento de blobs. Ou então, selecione a URL de um documento localizado em outro lugar.
* Use o método acima para obter a URL de uma imagem de recibo também.
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>Modelo de objeto

Com o Reconhecimento de Formulários, você pode criar dois tipos diferentes de cliente. O primeiro, `FormRecognizerClient`, é usado para consultar o serviço em campos e conteúdo do formulário reconhecidos. O segundo, `FormTrainingClient`, é usado para criar e gerenciar modelos personalizados que você pode usar para aprimorar o reconhecimento.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` fornece operações para:

* Reconhecer campos e conteúdo do formulário usando modelos personalizados treinados para analisar seus formulários personalizados.  Esses valores são retornados em uma coleção de objetos `RecognizedForm`. Confira o exemplo [Analisar formulários personalizados](#analyze-forms-with-a-custom-model).
* Reconhecer o conteúdo do formulário, incluindo tabelas, linhas e palavras, sem a necessidade de treinar um modelo.  O conteúdo do formulário é retornado em uma coleção de objetos `FormPage`. Confira o exemplo [Analisar layout](#analyze-layout).
* Reconhecer campos comuns de recibos dos EUA, usando um modelo de recibo pré-treinado no serviço do Reconhecimento de Formulários.  Esses campos e metadados são retornados em uma coleção de objetos `RecognizedForm`. Confira o exemplo [Analisar recibos](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` fornece operações para:

* Treinar modelos personalizados para analisar todos os campos e valores encontrados nos seus formulários personalizados.  Um `CustomFormModel` é retornado indicando os tipos de formulário que o modelo analisará e os campos que serão extraídos para cada tipo de formulário.
* Treinar modelos personalizados para analisar campos e valores específicos que você determina rotulando seus formulários personalizados.  Um `CustomFormModel` é retornado indicando os campos que o modelo extrairá, bem como a precisão estimada de cada campo.
* Gerenciar modelos criados em sua conta.
* Copiar um modelo personalizado de um recurso de Reconhecimento de Formulários para outro.

> [!NOTE]
> Os modelos também podem ser treinados usando uma interface gráfica do usuário, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como executar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para Java:
<!-- markdownlint-disable MD001 -->
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

Na parte superior do método **main**, adicione o código a seguir. Aqui, você autenticará os objetos de cliente usando as variáveis de assinatura definidas acima. Você usará um objeto **AzureKeyCredential**, para, se necessário, atualizar a chave de API sem criar objetos de cliente.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analisar layout

Use o Reconhecimento de Formulários para analisar tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo. Para obter mais informações sobre a extração de layout, confira o [Guia conceitual de layout](../../concept-layout.md).

Para analisar o conteúdo de um arquivo em determinada URL, use o método **beginRecognizeContentFromUrl**.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Você também pode obter o conteúdo em um arquivo local. Confira os métodos de [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), como **beginRecognizeContent**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) para obter cenários que envolvam imagens locais.

O valor retornado é uma coleção de objetos **FormPage**: um para cada página no documento enviado. O código a seguir itera nesses objetos e imprime os pares de chave/valor extraídos e os dados da tabela.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Saída

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-invoices"></a>Analisar faturas

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

Esta seção demonstra como analisar e extrair campos comuns de faturas de vendas usando um modelo pré-treinado. Para obter mais informações sobre a análise de faturas, confira o [Guia conceitual de faturas](../../concept-invoices.md).

Para analisar as faturas por meio de uma URL, use o método `beginRecognizeInvoicesFromUrl`. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Você também pode analisar faturas locais. Confira os métodos de [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), como **beginRecognizeInvoices**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) para obter cenários que envolvam imagens locais.

O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada fatura no documento. O código a seguir processa a fatura no URI fornecido e imprime os valores e os campos principais no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

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

O método a seguir treina um modelo em um especificado conjunto de documentos e imprime o status do modelo no console. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

O objeto **CustomFormModel** retornado contém informações sobre os tipos de formulários que o modelo pode analisar e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Por fim, esse método retorna a ID exclusiva do modelo.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Saída

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais ( *\<filename\>.pdf.labels.json*) no contêiner de armazenamento de blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame o método **beginTraining** com o parâmetro *useTrainingLabels* definido como `true`.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


O **CustomFormModel** retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Saída

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método **beginRecognizeCustomFormsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Você também pode analisar um arquivo local. Confira os métodos de [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), como **beginRecognizeCustomForms**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) para obter cenários que envolvam imagens locais.

O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada página no documento enviado. O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Saída

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="analyze-receipts"></a>Analisar recibos

Esta seção demonstra como analisar e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado. Para obter mais informações sobre a análise de recibos, confira o [Guia conceitual de recibos](../../concept-receipts.md).

Para analisar recibos de um URI, use o método **beginRecognizeReceiptsFromUrl**. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Você também pode analisar imagens de recibos locais. Confira os métodos de [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), como **beginRecognizeReceipts**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) para obter cenários que envolvam imagens locais.

O valor retornado é uma coleção de objetos **RecognizedReceipt**: um para cada página no documento enviado. O próximo bloco de código itera pelos recibos e imprime os detalhes no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

O próximo bloco de código itera em itens individuais detectados no recibo e imprime os detalhes no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Saída 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Analisar cartões de visita

#### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/preview)

Esta seção demonstra como analisar e extrair campos comuns de cartões de visita em inglês usando um modelo pré-treinado. Para obter mais informações sobre a análise de cartões de visita, confira o [Guia conceitual de cartões de visita](../../concept-business-cards.md).

Para analisar cartões de visita por meio de uma URL, use o método `beginRecognizeBusinessCardsFromUrl`. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Analise também imagens de cartões de visita locais. Confira os métodos de [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient), como **beginRecognizeBusinessCards**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) para obter cenários que envolvam imagens locais.

O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada cartão no documento. O código a seguir processa um cartão de visita no URI fornecido e imprime os valores e os campos principais no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Este recurso não está disponível na versão selecionada da API.

---

## <a name="manage-custom-models"></a>Gerenciar modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. O código a seguir executa todas as tarefas de gerenciamento de modelos em um só método, como um exemplo. Comece copiando a assinatura de método abaixo:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Saída 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Saída 

Essa reposta foi truncada para facilitar a leitura.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Navegue de volta para o diretório principal do projeto. Em seguida, crie o aplicativo com o seguinte comando:

```console
gradle build
```

Execute o aplicativo com a meta `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

Os clientes do Reconhecimento de Formulários geram exceções `ErrorResponseException`. Por exemplo, se você tentar fornecer uma URL de origem de arquivo inválida, uma `ErrorResponseException` será gerada com um erro indicando a causa da falha. No snippet de código a seguir, o erro é tratado normalmente com a captura da exceção e a exibição de informações adicionais sobre o erro.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Habilitar o log do cliente

Os SDKs do Azure para Java oferecem uma história de log consistente para ajudar na solução de erros de aplicativos e na aceleração da resolução. Os logs produzidos capturam o fluxo de um aplicativo antes que acessem o estado do terminal para ajudar a localizar o problema raiz. Veja o [wiki de log](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) para obter diretrizes sobre como habilitar o log.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes Java do Reconhecimento de Formulários para treinar modelos personalizado e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia (e outros) no [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
