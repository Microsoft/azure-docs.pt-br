---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Saiba como usar o Reconhecimento de Formulários para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: e064faf3017b95cb3a5f3d9b89f178fb7f846766
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592610"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

O Reconhecimento de Formulários do Azure é um serviço cognitivo que usa a tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e dados de tabela de documentos de formulário. Em seguida, ele gera dados estruturados que incluem as relações no arquivo original. Chame seu modelo personalizado do Reconhecimento de Formulários usando uma API REST simples para reduzir a complexidade e integrá-lo com facilidade ao seu fluxo de trabalho ou aplicativo. Para começar, você precisa apenas de cinco documentos de formulário preenchidos ou de dois formulários preenchidos e um vazio do mesmo tipo que o material de entrada. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados.

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do Reconhecimento de Formulários treina para seus próprios dados e você só precisa de cinco formulários de entrada de exemplo para começar. Quando você envia seus dados de entrada, o algoritmo agrupa os formulários por tipos, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. Em seguida, ele gera dados estruturados que incluem as relações no arquivo original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

O aprendizado sem supervisão permite que o modelo entenda o layout e as relações entre os campos e as entradas sem rotulagem manual de dados nem codificação e manutenção intensivas. Por outro lado, os modelos de aprendizado de máquina previamente treinado exigem dados padronizados. Eles são menos precisos, com material de entrada que se desvia dos formatos tradicionais, como formulários específicos do setor.

## <a name="pre-built-receipt-model"></a>Modelo de recibo predefinidos

O Reconhecimento de Formulários também inclui um modelo de leitura de recibos de vendas. Esse modelo extrai informações importantes, como a data e a hora da transação, informações de comerciantes, valores de impostos e totais e muito mais. Além disso, os modelos de recibos predefinidos são treinados para reconhecer e retornar todo o texto de um recibo.

## <a name="what-it-includes"></a>O que inclui

O Reconhecimento de Formulários está disponível como uma API REST. Você pode criar, treinar e classificar um modelo personalizado ou acessar o modelo predefinido chamando essas APIs. Se quiser, você pode treinar e executar modelos personalizados em um contêiner local do Docker.

## <a name="input-requirements-custom-model"></a>Requisitos de entrada (modelo personalizado)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Solicitar acesso

O Reconhecimento de Formulários está disponível em uma versão prévia de acesso limitado. Para obter acesso à versão prévia, preencha e envie o formulário de [Solicitação de acesso ao Reconhecimento de Formulários](https://aka.ms/FormRecognizerRequestAccess). O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual usará o Reconhecimento de Formulários. Caso sua solicitação seja aprovada pela equipe dos Serviços Cognitivos do Azure, você receberá um email com instruções para acessar o serviço.

## <a name="where-do-i-start"></a>Por onde começo?

**Etapa 1:** Criar um recurso do Reconhecimento de Formulários no portal do Azure.

**Etapa 2:** Siga um início rápido para usar a API REST:
* [Início Rápido: treinar em um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o cURL](quickstarts/curl-train-extract.md)
* [Início Rápido: treinar em um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o Python](quickstarts/python-train-extract.md)
* [Início Rápido: extrair dados de recibos usando cURL](quickstarts/curl-receipts.md)
* [Início Rápido: extrair dados de recibos usando Python](quickstarts/python-receipts.md)

Recomendamos usar o serviço gratuito quando estiver aprendendo a tecnologia. Lembre que o número de páginas gratuitas é limitado a 500 por mês.

**Etapa 3:** revisar as Especificações das APIs REST

Use as APIs a seguir para treinar e extrair dados estruturados de formulários.

|||
|---|---|
| Treinar Modelo| Treine um novo modelo para analisar seus formulários usando cinco formulários do mesmo tipo. Ou treine com um formulário vazio e dois formulários preenchidos.  |
| Analisar Formulário |Analise um único documento transmitido como um fluxo para extrair pares chave-valor e tabelas do formulário com o modelo personalizado.  |
| Analisar Recibo |Analise um único documento de recibo para extrair informações importantes e outros textos do recibo.|

Explore a [documentação de referência da API REST](https://aka.ms/form-recognizer/api) para saber mais. 

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

O serviço é oferecido como uma [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure de acordo com os [Termos de Serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Assim como ocorre com todos os serviços cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://aka.ms/form-recognizer/api).
