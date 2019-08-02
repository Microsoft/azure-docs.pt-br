---
title: O que é a API do Detector de Anomalias?
titleSuffix: Azure Cognitive Services
description: Use algoritmos avançados da API do Detector de Anomalias para identificar anomalias em seus dados de série temporal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 80e0984deff83726fd96a462a1ae8a4375db9d2e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721614"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detector de Anomalias?

A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal com aprendizado de máquina. A API do Detector de Anomalias adapta-se automaticamente identificando e aplicando os melhores modelos para seus dados, não importa o setor, o cenário nem o volume de dados. Usando seus dados de série temporal, a API determina os limites para detecção de anomalias, os valores esperados e quais pontos de dados são anomalias.

![Detectar alterações de padrão nas solicitações de serviço](./media/anomaly_detection2.png)

Usar o Detector de Anomalias não exige nenhuma experiência anterior em aprendizado de máquina e a API RESTful permite que você integre facilmente o serviço a seus aplicativos e processos.

## <a name="features"></a>Recursos

Com o Detector de Anomalias, você pode detectar anomalias automaticamente em todos os seus dados de série temporal ou conforme elas ocorrem em tempo real. 

|Recurso  |DESCRIÇÃO  |
|---------|---------|
|Detecte anomalias conforme elas ocorrem em tempo real. | Detecte anomalias em seus dados de streaming usando pontos de vistos anteriormente para determinar se pelo menos um é uma anomalia. Essa operação gera um modelo usando os pontos de dados que você envia e determina se o ponto de destino é uma anomalia. Ao chamar a API com cada novo ponto de dados que você gera, você pode monitorar seus dados conforme eles são criados. |
|Detecte anomalias em todo o conjunto de dados como um lote. | Use sua série temporal para detectar todas as anomalias que podem existir em seus dados. Essa operação gera um modelo usando todos os seus dados de série temporal, analisando cada ponto com o mesmo modelo.         |
| Obtenha informações adicionais sobre seus dados. | Obtenha detalhes úteis sobre seus dados e todas as anomalias observadas, incluindo valores esperados, limites de anomalias e posições. |
| Ajuste os limites de detecção de anomalias. | A API do Detector de Anomalias criará automaticamente limites para a detecção de anomalias. Ajuste esses limites para aumentar ou diminuir a sensibilidade da API a anomalias de dados e ajustar melhor seus dados. |

## <a name="demo"></a>Demonstração

Para começar rapidamente a usar a API do Detector de Anomalias, experimente uma [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) que pode ser executada em seu navegador. Essa demonstração é executada em um Jupyter Notebook hospedado na Web e mostra como enviar uma solicitação à API e visualizar o resultado.

Para executar a demonstração, conclua as seguintes etapas:

1. Obtenha uma chave de assinatura válida da API do Detector de Anomalias e um ponto de extremidade de API. A seção a seguir tem instruções para inscrever-se. 
2. Entre e clique em Clonar no canto superior direito.
3. Clique em **Executar em computação gratuita**
4. Selecione um dos notebooks para este exemplo.
5. Adicione sua chave de assinatura de API do Detector de Anomalias válida à variável `subscription_key`. Altere a variável `endpoint` para seu ponto de extremidade. Por exemplo: `https://westus2.api.cognitive.microsoft.com`
1. Na barra de menus superior, clique em **Célula** e, em seguida, **Executar Tudo**.

## <a name="workflow"></a>Fluxo de trabalho

A API do Detector de Anomalias é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Após a inscrição:

1. Pegue seus dados de série temporal e converta-os em um formato JSON válido. Use as [melhores práticas](concepts/anomaly-detection-best-practices.md) ao preparar seus dados para obter os melhores resultados.
1. Envie uma solicitação à API do Detector de Anomalias com seus dados.
1. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Detectar anomalias nos dados de série temporal usando a API REST do Detector de Anomalias](quickstarts/detect-data-anomalies-csharp.md)
* A [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) da API do Detector de Anomalias
* A [referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) do Detector de Anomalias