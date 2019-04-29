---
title: Recursos de visualização do Azure Stream Analytics
description: Este artigo lista os recursos do Azure Stream Analytics que estão atualmente na visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 08430f3eee858cdb6c9a7fbdfe11bd4c00ef148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485668"
---
# <a name="azure-stream-analytics-preview-features"></a>Recursos de visualização do Azure Stream Analytics

Este artigo resume todos os recursos atualmente em visualização do Azure Stream Analytics. O uso de recursos de visualização em um ambiente de produção não é recomendado.

## <a name="public-previews"></a>Visualizações públicas

Os seguintes recursos estão em pré-visualização pública. Você pode aproveitar esses recursos hoje, mas não os use em seu ambiente de produção.

### <a name="anomaly-detection"></a>Detecção de anomalias

O Azure Stream Analytics apresenta novos modelos de aprendizado de máquina com suporte para a detecção de *spike* e *dips*, além da detecção de tendências negativas bidirecionais, lentas positivas e lentas. Para obter mais informações, visite [detecção de anomalias no Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Dados de referência de Banco de Dados SQL

O Azure Stream Analytics oferece suporte ao Banco de Dados SQL do Azure como uma fonte de entrada para dados de referência. É possível usar o Banco de Dados SQL como dados de referência para o trabalho do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Para obter mais informações, visite [Usar dados de referência de um Banco de Dados SQL para um trabalho do Azure Stream Analytics](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Você pode dimensionar os trabalhos do Stream Analytics com as funções de Aprendizado de Máquina (ML). Para saber mais sobre como usar as funções do ML em seu trabalho do Stream Analytics, visite [Dimensionar seu trabalho do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [Realizando análise de sentimentos usando o Azure Stream Analytics e o Aprendizado de Máquina do Azure](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agregado definido pelo usuário de JavaScript

O Azure Stream Analytics oferece suporte a agregados definidos pelo usuário (UDA) escritos em JavaScript, que permitem implementar lógica de negócios com informações de estado complexas. Aprenda a usar UDAs da documentação [agregados definidos pelo usuário do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Em tempo real de dados de teste no Visual Studio

As ferramentas do Visual Studio para o Azure Stream Analytics aprimoram o recurso de teste local que permite testar suas consultas em fluxos de eventos ao vivo de fontes de nuvem, como Hub de Eventos ou Hub de IoT. Saiba como [Testar dados ao vivo localmente usando as ferramentas do Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo usuário .NET no IoT Edge

Com as funções definidas pelo usuário padrão do .NET, você pode executar o código do .NET Standard como parte de seu fluxo de pipeline. Você pode criar classes C# simples ou importar projetos e bibliotecas completos. Experiência completa de autoria e depuração é suportada no Visual Studio. Para obter mais informações, visite [Desenvolver funções definidas pelo usuário do .NET Standard para tarefas do Edge Analytics Broadcast do Azure](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Versões prévias privadas

Os seguintes recursos estão em versão prévia privada.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Deserializador personalizado do C# para o Azure Stream Analytics no IoT Edge

Os desenvolvedores agora podem implementar deserializadores personalizados em C# para desserializar os eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser criados no Visual Studio Code. Para acessar recursos de visualização privada de ferramentas, entre em contato com a *ASAToolsfeedback\@microsoft.com*.

## <a name="next-steps"></a>Próximas etapas

* [Oito novos recursos no Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Quatro novos recursos agora disponíveis no Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
