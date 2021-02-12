---
title: Recursos de visualização do Azure Stream Analytics
description: Este artigo lista os recursos do Azure Stream Analytics que estão atualmente na visualização.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: d07a2364cbd2b63ef9e38387b6e915f7e63676ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012148"
---
# <a name="azure-stream-analytics-preview-features"></a>Recursos de visualização do Azure Stream Analytics

Este artigo resume todos os recursos atualmente em visualização do Azure Stream Analytics. O uso de recursos de visualização em um ambiente de produção não é recomendado.

## <a name="public-previews"></a>Visualizações públicas

Os seguintes recursos estão em pré-visualização pública. Você pode aproveitar esses recursos hoje, mas não os use em seu ambiente de produção.

### <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Autenticar na saída do banco de dados SQL com identidades gerenciadas (versão prévia)

O Azure Stream Analytics dá suporte à [Autenticação de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para os coletores de saída do Banco de Dados SQL do Azure. Identidades gerenciadas eliminam as limitações de métodos de autenticação baseados no usuário, como a necessidade de autenticar-se por alterações de senha. 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos de ML personalizados gerenciados pelo Azure Machine Learning

O Azure Stream Analytics dá suporte para pontuação em tempo real de alto desempenho, aproveitando modelos personalizados de aprendizado de máquina pré-treinados gerenciados pelo Azure Machine Learning e hospedados no AKS (Serviço de Kubernetes do Azure) ou na ACI (Instâncias de Contêiner do Azure), usando um fluxo de trabalho que não exige que você escreva código. [Inscreva-se](https://aka.ms/asapreview1) para obter a versão prévia

### <a name="c-custom-de-serializers"></a>Desserializadores personalizados de C#
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou qualquer formato personalizado. Você pode implementar [desserializadores personalizados](custom-deserializer-examples.md) em C# e usá-los para desserializar eventos recebidos pelo Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilidade com código personalizado de C#

Os desenvolvedores que criam módulos do Stream Analytics na nuvem ou no IoT Edge podem gravar ou reutilizar funções C# personalizadas e chamá-las diretamente na consulta por meio de [funções definidas pelo usuário](stream-analytics-edge-csharp-udf-methods.md).

### <a name="debug-query-steps-in-visual-studio"></a>Depurar etapas de consulta no Visual Studio

Você pode visualizar facilmente o conjunto de linhas intermediárias em um diagrama de dados ao fazer testes locais nas ferramentas do Azure Stream Analytics para Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Em tempo real de dados de teste no Visual Studio

As ferramentas do Visual Studio para o Azure Stream Analytics aprimoram o recurso de teste local que permite testar suas consultas em fluxos de eventos ao vivo de fontes de nuvem, como Hub de Eventos ou Hub de IoT. Saiba como [Testar dados ao vivo localmente usando as ferramentas do Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser criados no Visual Studio Code. Confira nosso [tutorial de introdução ao VS Code](./quick-create-visual-studio-code.md).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Teste local com dados dinâmicos no Visual Studio Code

Você pode testar suas consultas com dados dinâmicos em seu computador local antes de enviar o trabalho para o Azure. Cada iteração de teste leva menos de dois a três segundos em média, resultando em um processo de desenvolvimento muito eficiente.

## <a name="other-previews"></a>Outras versões prévias

Os recursos a seguir também estão disponíveis em versão prévia mediante solicitação.

### <a name="support-for-azure-stack"></a>Suporte para Azure Stack
Esse recurso habilitado no runtime do Azure IoT Edge, aproveita os recursos personalizados do Azure Stack, como suporte nativo para entradas e saídas locais em execução no Azure Stack (por exemplo, hubs de eventos, Hub IoT, Armazenamento de Blobs). Essa nova integração permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando os insights.
Esse recurso permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando os insights. [Inscreva-se](https://aka.ms/asapreview1) para obter esta versão prévia.