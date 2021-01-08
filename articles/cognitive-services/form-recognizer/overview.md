---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: O serviço Reconhecimento de Formulários do Azure permite que você identifique e extraia pares chave/valor e dados de tabela de seus documentos de formulário, bem como informações importantes de recibos de vendas e cartões de visita.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: processamento automatizado de dados, processamento de documentos, entrada automatizada de dados, processamento de formulários
ms.openlocfilehash: ed940622f72271ef3e606c5068babcb6366c31b6
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845518"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Reconhecimento de Formulários do Azure é um serviço cognitivo que permite criar software de processamento de dados automatizado usando a tecnologia de machine learning. Identifique e extraia texto, pares chave/valor, marcas de seleção, tabelas e estrutura dos seus documentos&mdash;o serviço produz dados estruturados que incluem as relações no arquivo original, caixas delimitadoras, confiança, entre outros. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados. Use o Reconhecimento de Formulários para automatizar a entrada de dados nos seus aplicativos e enriquecer suas funcionalidades de pesquisa de documentos.

O Reconhecimento de Formulários é composto por modelos de processamento de documentos personalizados, por modelos predefinidos para faturas, recibos e cartões de visita e pelo modelo de layout. Chame os modelos do Reconhecimento de Formulários usando uma API REST ou SDKs da biblioteca de clientes para reduzir a complexidade e integrá-lo a seu fluxo de trabalho ou aplicativo.

O Reconhecimento de Formulários é composto pelos seguintes serviços:
* **[API de Layout](#layout-api)** : extraia texto, marcas de seleção e estrutura de tabelas de documentos, juntamente com as respectivas coordenadas de caixa delimitadora.
* **[Modelos personalizados](#custom-models)** : extraia texto, pares chave/valor, marcas de seleção e dados de tabela de formulários. Esses modelos são treinados usando dados próprios e, portanto, são adaptados para seus formulários.
* **[Modelos predefinidos](#prebuilt-models)** – extraia dados de tipos de formulário exclusivos usando modelos predefinidos. No momento, os modelos predefinidos a seguir estão disponíveis
    * [Faturas](./concept-invoices.md)
    * [Recibos de vendas](./concept-receipts.md)
    * [Cartões de visita](./concept-business-cards.md)


## <a name="try-it-out"></a>Experimente

Para experimentar o serviço Reconhecimento de Formulários, acesse a ferramenta de interface do usuário de exemplo online:


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

---

Você precisará ter uma assinatura do Azure ([crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)), bem como um ponto de extremidade e uma chave de [recurso do Reconhecimento de Formulários](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) para experimentar o serviço Reconhecimento de Formulários.

## <a name="layout-api"></a>API de layout

O Reconhecimento de Formulários pode extrair de documentos texto, marcas de seleção e estrutura de tabela (os números de linha e coluna associados ao texto) usando o OCR (reconhecimento óptico de caracteres) de alta definição e um modelo aprimorado de aprendizado profundo. Confira o guia conceitual [Layout](./concept-layout.md) para obter mais informações.

:::image type="content" source="./media/tables-example.jpg" alt-text="exemplo de tabelas" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do Reconhecimento de Formulários treina para dados próprios e você só precisa de cinco formulários de entrada de exemplo para começar. Um modelo de processamento de documento treinado pode gerar dados estruturados que incluem as relações no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

Você tem as seguintes opções ao treinar modelos personalizados: treinamento usando dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Treinar sem rótulos

Por padrão, o Reconhecimento de Formulários usa o aprendizado não supervisionado para entender o layout e as relações entre campos e entradas em seus formulários. Quando você envia seus formulários de entrada, o algoritmo agrupa os formulários por tipos, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. Isso não exige a rotulagem manual de dados nem a codificação e a manutenção intensivas. Recomendamos que você experimente esse método primeiro.

Confira [Criar um conjunto de dados de treinamento](./build-training-data-set.md) para obter dicas sobre como coletar seus documentos de treinamento.

### <a name="train-with-labels"></a>Treinar com rótulos

Quando você treina os dados rotulados, o modelo supervisiona o aprendizado para extrair os valores de interesse usando os formulários rotulados que você fornece. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.

O Reconhecimento de Formulários usa a [API de layout](#layout-api) para aprender os tamanhos e as posições esperados de elementos de texto impressos e manuscritos. Em seguida, ele usa rótulos especificados pelo usuário para aprender as associações de chave/valor nos documentos. Recomendamos que você use cinco formulários rotulados manualmente do mesmo tipo (mesma estrutura) para começar ao treinar um novo modelo e adicionar mais dados rotulados, conforme necessário, a fim de aprimorar a precisão do modelo.

[Introdução ao treinamento com rótulos](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Modelos predefinidos

O Reconhecimento de Formulários também inclui modelos predefinidos para o processamento automático de dados de tipos de formulário exclusivos.

### <a name="prebuilt-invoice-model"></a>Modelo de fatura predefinido
O modelo de fatura predefinido extrai dados de faturas em uma variedade de formatos e retorna dados estruturados. Esse modelo extrai informações importantes, como ID da fatura, detalhes do cliente, detalhes do fornecedor, endereço para entrega, endereço para cobrança, total, imposto, subtotal, entre outros. Além disso, o modelo de fatura predefinido é treinado para reconhecer e retornar todo o texto e as tabelas da fatura. Confira o guia conceitual [Faturas](./concept-invoices.md) para obter mais informações.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="exemplo de fatura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Modelo de Recibo Predefinido

O modelo de Recibo Predefinido é usado para a leitura de recibos de vendas em inglês da Austrália, do Canadá, do Reino Unido, da Índia e dos Estados Unidos: o tipo usado por restaurantes, postos de gasolina, varejo etc. Esse modelo extrai informações essenciais, como data e hora da transação, informações do comerciante, valores de impostos, itens de linha, totais, entre outros. Além disso, o modelo de recibo predefinido é treinado para reconhecer e retornar todo o texto de um recibo. Confira o guia conceitual [Recibos](./concept-receipts.md) para obter mais informações.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exemplo de recibo" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Modelo de Cartões de Visita Predefinidos

O modelo de Cartões de Visita permite que você extraia informações, como nome da pessoa, cargo, endereço, email, empresa e números de telefone de cartões de visita em inglês. Confira o guia conceitual [Cartões de visita](./concept-business-cards.md) para obter mais informações.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exemplo de cartão de visita" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Introdução

Use a [ferramenta Reconhecimento de Formulários de Exemplo](https://fott.azurewebsites.net/) ou siga um guia de início rápido para começar a extrair dados dos seus formulários. Recomendamos usar o serviço gratuito quando estiver aprendendo a tecnologia. Lembre-se de que o número de páginas gratuitas é limitado a 500 por mês.

* [Início rápido da biblioteca de clientes/API REST](./quickstarts/client-library.md) (todos os idiomas, vários cenários)
* Inícios rápidos da interface do usuário da Web
  * [Treinar com rótulos – ferramenta de rotulação de amostra](quickstarts/label-tool.md)
* Exemplos de REST (GitHub)
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
| **Analisar Layout** | Analisar um documento passado como um fluxo para extrair texto, marcas de seleção, tabelas e estrutura do documento |
| **Treinar Modelo Personalizado**| Treine um novo modelo para analisar seus formulários usando cinco formulários do mesmo tipo. Defina o parâmetro _useLabelFile_ como `true` para treinar usando dados rotulados manualmente. |
| **Analisar Formulário** |Analise um formulário passado como um fluxo para extrair texto, pares chave/valor e tabelas do formulário com o modelo personalizado.  |
| **Analisar Fatura** | Analise uma fatura para extrair informações importantes, tabelas e outros tipos de texto da fatura.|
| **Analisar Recibo** | Analise um documento de recibo para extrair informações importantes e outros tipos de texto do recibo.|
| **Analisar o Cartão de Visita** | Analise um cartão de visita para extrair informações e texto importantes.|


# <a name="v20"></a>[v2.0](#tab/v2-0)
Explore a [documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

# <a name="v21"></a>[v2.1](#tab/v2-1)
Explore a [documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

---

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use os contêineres do Reconhecimento de Formulários (versão prévia)](form-recognizer-container-howto.md) para implantar recursos de API no local. Esse contêiner do Docker permite que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os serviços cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Conclua um [início rápido](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de formulários com o Reconhecimento de Formulários no idioma de sua escolha.