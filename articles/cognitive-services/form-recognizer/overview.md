---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: O serviço Reconhecimento de Formulários do Azure permite que você identifique e extraia pares chave/valor e dados de tabela de seus documentos de formulário, bem como informações importantes de recibos de vendas e cartões de visita.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: processamento automatizado de dados, processamento de documentos, entrada automatizada de dados, processamento de formulários
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518212"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Reconhecimento de Formulários do Azure é um serviço cognitivo que permite criar software de processamento de dados automatizado usando a tecnologia de machine learning. Identifique e extraia texto, pares chave/valor, marcas de seleção, tabelas e estrutura dos seus documentos&mdash;o serviço produz dados estruturados que incluem as relações no arquivo original, caixas delimitadoras, confiança, entre outros. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados. Use o Reconhecimento de Formulários para automatizar a entrada de dados nos seus aplicativos e enriquecer suas funcionalidades de pesquisa de documentos.

O Reconhecimento de Formulários é composto por modelos de processamento de documentos personalizados, modelos predefinidos para faturas, recibos, IDs e cartões de visita e pelo modelo de layout. Chame os modelos do Reconhecimento de Formulários usando uma API REST ou SDKs da biblioteca de clientes para reduzir a complexidade e integrá-lo a seu fluxo de trabalho ou aplicativo.

Esta documentação contém os seguintes tipos de artigos:  

* Os [**guias de início rápido**](quickstarts/client-library.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.  
* Os [**guias de instruções**](build-training-data-set.md) contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.  
* Os [**conceitos**](concept-layout.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.  
* Os [**tutoriais**](tutorial-bulk-processing.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.  

## <a name="form-recognizer-features"></a>Recursos do Reconhecimento de Formulários

Com o Reconhecimento de Formulários, é fácil extrair e analisar dados de formulários usando estes recursos:

* **[API de Layout](#layout-api)** : extraia texto, marcas de seleção e estrutura de tabelas de documentos, juntamente com as respectivas coordenadas de caixa delimitadora.
* **[Modelos personalizados](#custom-models)** : extraia texto, pares chave/valor, marcas de seleção e dados de tabela de formulários. Esses modelos são treinados usando dados próprios e, portanto, são adaptados para seus formulários.

* **[Modelos predefinidos](#prebuilt-models)** – extraia dados de tipos de documentos exclusivos usando modelos predefinidos. No momento, os modelos predefinidos a seguir estão disponíveis

  * [Faturas](./concept-invoices.md)
  * [Recibos de vendas](./concept-receipts.md)
  * [Cartões de visita](./concept-business-cards.md)
  * [Cartões de identificação (ID)](./concept-identification-cards.md)


## <a name="get-started"></a>Introdução

Use a Ferramenta de Reconhecimento de Formulários de Amostra para experimentar os modelos de Layout e Pré-criados e treinar um modelo personalizado para seus documentos. Você precisará ter uma assinatura do Azure ([**crie uma gratuitamente**](https://azure.microsoft.com/free/cognitive-services)), bem como um ponto de extremidade e uma chave de [**recurso do Reconhecimento de Formulários**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) para experimentar o serviço Reconhecimento de Formulários.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Experimentar o Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Experimentar o Reconhecimento de Formulários](https://fott.azurewebsites.net/)

---
Siga o [Início Rápido da biblioteca de clientes/API REST](./quickstarts/client-library.md) para começar a extrair dados de seus documentos. Recomendamos usar o serviço gratuito quando estiver aprendendo a tecnologia. Lembre-se de que o número de páginas gratuitas é limitado a 500 por mês.

Você também pode usar as amostras de REST (GitHub) para começar - 

* Extrair texto, marcas de seleção e estrutura de tabela de documentos
  * [Extrair dados de layout – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Treinar modelos personalizados e extrair dados de formulário
  * [Treinar sem rótulos – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Treinar com rótulos – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extrair dados de faturas
  * [Extrair dados de faturas – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extrair dados de recibos de vendas
  * [Extrair dados de recibo – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extrair dados de cartões de visita
  * [Extrair dados do cartão de visita – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>revisar as Especificações das APIs REST

Você usará as APIs a seguir para treinar modelos e extrair dados estruturados de formulários.

|Nome |Descrição |
|---|---|
| **Analisar Layout** | Analise um documento passado como um fluxo para extrair texto, marcas de seleção, tabelas e estrutura do documento |
| **Treinar Modelo Personalizado**| Treine um novo modelo para analisar seus formulários usando cinco formulários do mesmo tipo. Defina o parâmetro _useLabelFile_ como `true` para treinar usando dados rotulados manualmente. |
| **Analisar Formulário** |Analise um formulário passado como um fluxo para extrair texto, pares chave/valor e tabelas do formulário com o modelo personalizado.  |
| **Analisar Fatura** | Analise uma fatura para extrair informações importantes, tabelas e outros tipos de texto da fatura.|
| **Analisar Recibo** | Analise um documento de recibo para extrair informações importantes e outros tipos de texto do recibo.|
| **Analisar ID** | Analise um documento de cartão de ID para extrair informações importantes e outros tipos de texto do cartão de identificação.|
| **Analisar o Cartão de Visita** | Analise um cartão de visita para extrair informações e texto importantes.|

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

Explore a [documentação de referência da API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Explore a [documentação de referência da API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

---

## <a name="layout-api"></a>API de layout

O Reconhecimento de Formulários pode extrair de documentos texto, marcas de seleção e estrutura de tabela (os números de linha e coluna associados ao texto) usando o OCR (reconhecimento óptico de caracteres) de alta definição e um modelo aprimorado de aprendizado profundo. Confira o guia conceitual [Layout](./concept-layout.md) para obter mais informações.

:::image type="content" source="./media/tables-example.jpg" alt-text="exemplo de tabelas" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do Reconhecimento de Formulários treina para dados próprios e você só precisa de cinco formulários de entrada de exemplo para começar. Um modelo de processamento de documento treinado pode gerar dados estruturados que incluem as relações no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

Você tem as seguintes opções ao treinar modelos personalizados: treinamento usando dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Treinar sem rótulos

O Reconhecimento de Formulários usa aprendizado não supervisionado para entender o layout e as relações entre campos e entradas em seus formulários. Quando você envia seus formulários de entrada, o algoritmo agrupa os formulários por tipos, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. O treinamento sem rótulos não exige a rotulagem de dados manual nem a codificação e a manutenção intensivas. Recomendamos que você experimente esse método primeiro.

Confira [Criar um conjunto de dados de treinamento](./build-training-data-set.md) para obter dicas sobre como coletar seus documentos de treinamento.

### <a name="train-with-labels"></a>Treinar com rótulos

Quando você treina usando dados rotulados, o modelo usa aprendizado supervisionado para extrair os valores de interesse usando os formulários rotulados que você fornece. Os dados rotulados geram modelos de melhor desempenho e podem produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.

O Reconhecimento de Formulários usa a [API de Layout](#layout-api) para aprender os tamanhos e as posições esperados de elementos de texto impressos e manuscritos e extrair tabelas. Em seguida, ele usa rótulos especificados pelo usuário para aprender as associações de chave/valor e as tabelas nos documentos. Recomendamos que você use cinco formulários rotulados manualmente do mesmo tipo (mesma estrutura) para começar ao treinar um novo modelo e adicionar mais dados rotulados, conforme necessário, a fim de aprimorar a precisão do modelo. O Reconhecimento de Formulários permite treinar um modelo para extrair pares chave-valor e tabelas usando funcionalidades de aprendizado supervisionado. 

[Introdução ao treinamento com rótulos](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Modelos predefinidos

O Reconhecimento de Formulários também inclui modelos predefinidos para o processamento automático de dados de tipos de formulário exclusivos.

### <a name="prebuilt-invoice-model"></a>Modelo de fatura predefinido

O modelo Fatura Predefinida extrai dados de faturas em diversos formatos e retorna dados estruturados. Esse modelo extrai informações importantes, como a ID da fatura, detalhes do cliente, detalhes do fornecedor, endereço de entrega, endereço de cobrança, total, imposto, subtotal, itens de linha, entre outros. Além disso, o modelo de fatura predefinido é treinado para analisar e retornar todo o texto e as tabelas da fatura. Confira o guia conceitual [Faturas](./concept-invoices.md) para obter mais informações.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="exemplo de fatura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Modelo de Recibo Predefinido

O modelo de Recibo Predefinido é usado para a leitura de recibos de vendas em inglês da Austrália, do Canadá, do Reino Unido, da Índia e dos Estados Unidos: o tipo usado por restaurantes, postos de gasolina, varejo etc. Esse modelo extrai informações essenciais, como data e hora da transação, informações do comerciante, valores de impostos, itens de linha, totais, entre outros. Além disso, o modelo de recibo predefinido é treinado para analisar e retornar todo o texto de um recibo. Confira o guia conceitual [Recibos](./concept-receipts.md) para obter mais informações.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exemplo de recibo" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Modelo de cartões de ID (identificação) predefinidos

O modelo de cartões de ID (identificação) permite extrair informações importantes de passaportes do mundo inteiro, bem como de carteiras de motorista dos EUA. Ele extrai dados como a ID do documento, a data de validade, a data de nascimento, o nome, o país, a região, a zona legível por computador e muito mais. Confira o guia conceitual [Cartões de ID (identificação)](./concept-identification-cards.md) para obter mais informações.

:::image type="content" source="./media/overview-id.jpg" alt-text="amostra de cartão de identificação" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Modelo de Cartões de Visita Predefinidos

O modelo de Cartões de Visita permite que você extraia informações, como nome da pessoa, cargo, endereço, email, empresa e números de telefone de cartões de visita em inglês. Confira o guia conceitual [Cartões de visita](./concept-business-cards.md) para obter mais informações.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exemplo de cartão de visita" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use os contêineres do Reconhecimento de Formulários (versão prévia)](form-recognizer-container-howto.md) para implantar recursos de API no local. Esse contêiner do Docker permite que você aproxime o serviço de seus dados para fins de conformidade, segurança ou outras razões operacionais.

## <a name="service-availability-and-redundancy"></a>Disponibilidade e redundância do serviço

### <a name="is-form-recognizer-service-zone-resilient"></a>O serviço de Reconhecimento de Formulários é resiliente por zona?

Sim. O serviço de Reconhecimento de Formulários é resiliente por zona por padrão.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Como faço para configurar o serviço de Reconhecimento de Formulários para ser resiliente por zona?

Nenhuma configuração do cliente será necessária para habilitar a resiliência de zona. A resiliência por zona para recursos do Reconhecimento de Formulários está disponível por padrão e é gerenciada pelo próprio serviço.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os serviços cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Experimente nossa ferramenta online e o início rápido para saber mais sobre o serviço de Reconhecimento de Formulários.

* [**Ferramenta Reconhecimento de Formulários**](https://fott-preview.azurewebsites.net/)
* [**Início Rápido da biblioteca de clientes e da API REST**](quickstarts/client-library.md)
