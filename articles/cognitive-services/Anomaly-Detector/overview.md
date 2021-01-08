---
title: O que é a API do Detector de Anomalias?
titleSuffix: Azure Cognitive Services
description: Use os algoritmos da API do Detector de Anomalias para aplicar a detecção de anomalias aos dados de série temporal.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 11/23/2020
ms.author: mbullwin
keywords: detecção de anomalias, machine learning, algoritmos
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d8698a17db8be6a3d5b26d764ef86702320ebfa4
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705074"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detector de Anomalias?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A API do Detector de Anomalias permite monitorar e detectar anormalidades em seus dados de série temporal sem precisar entender sobre machine learning. Os algoritmos da API do Detector de Anomalias adaptam-se automaticamente identificando e aplicando os melhores modelos para seus dados, não importa o setor, o cenário nem o volume de dados. Usando seus dados de série temporal, a API determina os limites para detecção de anomalias, os valores esperados e quais pontos de dados são anomalias.

![Detectar alterações de padrão nas solicitações de serviço](./media/anomaly_detection2.png)

Usar o Detector de Anomalias não exige nenhuma experiência anterior em aprendizado de máquina e a API RESTful permite que você integre facilmente o serviço a seus aplicativos e processos.

## <a name="features"></a>Recursos

Com o Detector de Anomalias, você pode detectar anomalias automaticamente em todos os seus dados de série temporal ou conforme elas ocorrem em tempo real.

|Recurso  |Descrição  |
|---------|---------|
|Detecção de anomalias em tempo real. | Detecte anomalias em seus dados de streaming usando pontos de vistos anteriormente para determinar se pelo menos um é uma anomalia. Essa operação gera um modelo usando os pontos de dados que você envia e determina se o ponto de destino é uma anomalia. Ao chamar a API com cada novo ponto de dados que você gera, você pode monitorar seus dados conforme eles são criados. |
|Detecte anomalias em todo o conjunto de dados como um lote. | Use sua série temporal para detectar todas as anomalias que podem existir em seus dados. Essa operação gera um modelo usando todos os seus dados de série temporal, analisando cada ponto com o mesmo modelo.         |
|Detecte os pontos de alteração em todo o conjunto de dados como um lote. | Use a série temporal para detectar qualquer ponto de alteração de tendência existente nos dados. Essa operação gera um modelo usando todos os seus dados de série temporal, analisando cada ponto com o mesmo modelo.    |
| Obtenha informações adicionais sobre seus dados. | Obtenha detalhes úteis sobre seus dados e todas as anomalias observadas, incluindo valores esperados, limites de anomalias e posições. |
| Ajuste os limites de detecção de anomalias. | A API do Detector de Anomalias criará automaticamente limites para a detecção de anomalias. Ajuste esses limites para aumentar ou diminuir a sensibilidade da API a anomalias de dados e ajustar melhor seus dados. |

## <a name="demo"></a>Demonstração

Confira esta [demonstração interativa](https://aka.ms/adDemo) para entender como funciona o Detector de Anomalias.
Para executar a demonstração, você precisa criar um recurso de Detector de Anomalias e obter a chave de API e o ponto de extremidade.

## <a name="notebook"></a>Notebook

Para saber como chamar a API do Detector de Anomalias, experimente este [Notebook](https://aka.ms/adNotebook). Este Jupyter Notebook mostra como enviar uma solicitação de API e visualizar o resultado.

Para executar o Notebook, conclua as seguintes etapas:

1. Obtenha uma chave de assinatura válida da API do Detector de Anomalias e um ponto de extremidade de API. A seção a seguir tem instruções para inscrever-se.
1. Entre e clique em Clonar no canto superior direito.
1. Desmarque a opção "público" na caixa de diálogo antes de concluir a operação de clonagem, caso contrário, o notebook, incluindo as chaves de assinatura, será público.
1. Clique em **Executar em computação gratuita**
1. Selecione um dos notebooks.
1. Adicione sua chave de assinatura de API do Detector de Anomalias válida à variável `subscription_key`.
1. Altere a variável `endpoint` para seu ponto de extremidade. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na barra de menus superior, clique em **Célula** e, em seguida, **Executar Tudo**.

## <a name="workflow"></a>Fluxo de trabalho

A API do Detector de Anomalias é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Após a inscrição:

1. Pegue seus dados de série temporal e converta-os em um formato JSON válido. Use as [melhores práticas](concepts/anomaly-detection-best-practices.md) ao preparar seus dados para obter os melhores resultados.
1. Envie uma solicitação à API do Detector de Anomalias com seus dados.
1. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="algorithms"></a>Algoritmos

* Confira os seguintes blogs técnicos para obter informações sobre os algoritmos usados:
    * [Apresentando a API do Detector de Anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Visão geral do algoritmo SR-CNN no Detector de Anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Você pode ler o artigo [Serviço de detecção de anomalias da série temporal na Microsoft](https://arxiv.org/abs/1906.03821) (aceito por KDD 2019) para saber mais sobre os algoritmos SR-CNN desenvolvidos pela Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use contêineres do Detector de Anomalias](anomaly-detector-container-howto.md) para implantar recursos de API no local. Os contêineres do Docker permitem que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais.

## <a name="join-the-anomaly-detector-community"></a>Ingressar na comunidade do Detector de Anomalias

* Ingressar no [grupo de Assistentes do Detector de Anomalias no Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Veja o [conteúdo gerado pelo usuário](user-generated-content.md) selecionado

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Detectar anomalias nos dados de série temporal usando o Detector de Anomalias](quickstarts/client-libraries.md)
* A [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) da API do Detector de Anomalias
* A [referência da API REST](https://aka.ms/anomaly-detector-rest-api-ref) do Detector de Anomalias
