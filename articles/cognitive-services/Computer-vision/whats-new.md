---
title: Novidades na Pesquisa Visual Computacional.
titleSuffix: Azure Cognitive Services
description: Este artigo contém notícias sobre a Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/13/2021
ms.author: pafarley
ms.openlocfilehash: f102bbe45fd5eea853ba63def4f1c1a92888ca46
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285936"
---
# <a name="whats-new-in-computer-vision"></a>Novidades na Pesquisa Visual Computacional

Conheça o que há de novo no serviço. Esses itens podem ser notas sobre a versão, vídeos, postagens no blog e outros tipos de informações. Marque esta página para se manter atualizado quanto ao serviço.

## <a name="march-2021"></a>Março de 2021

### <a name="computer-vision-32-public-preview-update"></a>Atualização da Versão Prévia Pública da Pesquisa Visual Computacional 3.2

A Versão Prévia Pública da API da Pesquisa Visual Computacional v3.2 foi atualizada. A versão prévia tem todos os recursos da Pesquisa Visual Computacional, juntamente com APIs de Leitura e Análise atualizadas.

> [!div class="nextstepaction"]
> [Confira a Pesquisa Visual Computacional v3.2, versão prévia pública 3](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Versão Prévia Pública da API de Leitura v3.2 com suporte de OCR para 73 idiomas
A versão prévia pública da API de Leitura v3.2 da Pesquisa Visual Computacional, disponível como serviço de nuvem e contêiner do Docker, inclui estas atualizações:
* [OCR para 73 idiomas](./language-support.md#optical-character-recognition-ocr) incluindo idiomas latinos, chinês simplificado e tradicional, japonês e coreano.
* Ordem natural de leitura da saída da linha de texto (somente para idiomas latinos)
* Classificação de estilo manuscrito para linhas de texto juntamente com uma pontuação de confiança (somente em idiomas latinos).
* Extraia o texto somente das páginas selecionadas de um documento de várias páginas.
* Disponível como um [contêiner distroless](./computer-vision-how-to-install-containers.md?tabs=version-3-2) para implantação local.

Confira o [guia de instruções da API de Leitura](Vision-API-How-to-Topics/call-read-api.md) para saber mais.

> [!div class="nextstepaction"]
> [Use a Versão Prévia Pública da API de Leitura v3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Janeiro de 2021

### <a name="spatial-analysis-container-update"></a>Atualização do contêiner de Análise Espacial

Uma nova versão do [contêiner de Análise Espacial](spatial-analysis-container.md) foi lançada, com um novo conjunto de recursos. O contêiner do Docker permite que você analise vídeos de streaming em tempo real para entender as relações espaciais existentes entre as pessoas e a movimentação delas em ambientes físicos. 

* Agora, as [operações de Análise Espacial](spatial-analysis-operations.md) podem ser configuradas para detectar se uma pessoa está usando uma proteção para o rosto, como uma máscara. 
    * Foi habilitado um classificador de máscara para as operações `personcount`, `personcrossingline` e `personcrossingpolygon` configurando o parâmetro `ENABLE_FACE_MASK_CLASSIFIER`.
    * Os atributos `face_mask` e `face_noMask` serão retornados como metadados com a pontuação de confiança para cada pessoa detectada no fluxo de vídeo
* A operação *personcrossingpolygon* foi estendida para permitir o cálculo do tempo de duração da pesquisa que uma pessoa gasta em uma zona. Você pode definir o parâmetro `type` na configuração de zona da operação para `zonedwelltime` e um novo evento do tipo *personZoneDwellTimeEvent* incluirá o campo `durationMs` populado com o número de milissegundos que a pessoa gastou na zona.
* **Alteração interruptiva**: O evento *personZoneEvent* foi renomeado para *personZoneEnterExitEvent*. Esse evento é gerado pela operação *personcrossingpolygon* quando uma pessoa entra ou sai da zona e fornece informações direcionais com o lado numerado da zona que foi cruzada.
* A URL de vídeo pode ser fornecida como "Parâmetro Privado/ofuscado" em todas as operações. A ofuscação é opcional agora e só funcionará se `KEY` e `IV` forem fornecidas como variáveis de ambiente.
* A calibragem é habilitada por padrão para todas as operações. Defina o `do_calibration: false` para desabilitá-lo.
* Adição de suporte para recalibragem automática (desabilitada por padrão) por meio do parâmetro `enable_recalibration`. Veja [Operações de Análise Espacial](./spatial-analysis-operations.md) para obter detalhes
* Parâmetros de calibragem de câmera para o `DETECTOR_NODE_CONFIG`. Veja [Operações de Análise Espacial](./spatial-analysis-operations.md) para obter detalhes.


## <a name="october-2020"></a>Outubro de 2020

### <a name="computer-vision-api-v31-ga"></a>GA da API da Pesquisa Visual Computacional v3.1

A API da Pesquisa Visual Computacional em disponibilidade geral foi atualizada para a v3.1.

## <a name="september-2020"></a>Setembro de 2020

### <a name="spatial-analysis-container-preview"></a>Versão prévia do contêiner de Análise Espacial

O [contêiner de Análise Espacial](spatial-analysis-container.md) já está em versão prévia. O recurso de Análise Espacial da Pesquisa Visual Computacional permite que você analise vídeos de streaming em tempo real para entender as relações espaciais existentes entre as pessoas e o movimento delas nos ambientes físicos. A Análise Espacial é um contêiner do Docker que pode ser usado no local. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>A API de Leitura v3.1 Visualização Pública adiciona o OCR para japonês
A visualização pública da API de Leitura v3.1 da Pesquisa Visual Computacional adiciona as seguintes funcionalidades:
* OCR para o idioma japonês
* Para cada linha de texto, indique se a aparência está em estilo manuscrito ou impresso, juntamente com uma pontuação de confiança (somente para idiomas latinos).
* Para um documento de várias páginas, extraia o texto somente da página ou do intervalo de páginas selecionado.

* Esta versão prévia da API de Leitura é compatível com os idiomas inglês, holandês, francês, alemão, italiano, japonês, português, chinês simplificado e espanhol.

Confira o [guia de instruções da API de Leitura](Vision-API-How-to-Topics/call-read-api.md) para saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API de Leitura v3.1 Visualização Pública 2](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Julho de 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Visualização Pública da API de Leitura v3.1 com OCR para chinês simplificado
A versão prévia pública da API de Leitura v3.1 da Pesquisa Visual Computacional adiciona suporte ao chinês (simplificado).

* Esta versão prévia da API de Leitura dá suporte aos idiomas inglês, holandês, francês, alemão, italiano, português, chinês simplificado e espanhol.

Confira o [guia de instruções da API de Leitura](Vision-API-How-to-Topics/call-read-api.md) para saber mais.

> [!div class="nextstepaction"]
> [Saiba mais sobre a API de Leitura v3.1 Visualização Pública 1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Maio de 2020
A API de Pesquisa Visual Computacional v3.0 entrou em disponibilidade geral, com atualizações na API de Leitura:

* Suporte para inglês, holandês, francês, alemão, italiano, português e espanhol
* Precisão aprimorada
* Pontuação de confiança para cada palavra extraída
* Novo formato de saída

Confira a [Visão geral do OCR](overview-ocr.md) para saber mais.

## <a name="march-2020"></a>Março de 2020

* Agora o TLS 1.2 é obrigatório para todas as solicitações HTTP a este serviço. Para saber mais, confira [Segurança nos Serviços Cognitivos do Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Janeiro de 2020

### <a name="read-api-30-public-preview"></a>Versão prévia pública da API de Leitura 3.0

Agora você pode usar a versão 3.0 da API de Leitura para extrair texto impresso ou manuscrito de imagens. Em comparação com as versões anteriores, a 3.0 oferece:
* Precisão aprimorada
* Novo formato de saída
* Pontuação de confiança para cada palavra extraída
* Suporte para os idiomas espanhol e inglês com o parâmetro de idioma adicional

Siga um [início rápido de extração de texto](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3) para começar a usar a API 3.0.

## <a name="cognitive-service-updates"></a>Atualizações dos Serviços Cognitivos

[Comunicados de atualização do Azure para os Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)