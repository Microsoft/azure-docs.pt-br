---
title: O Stream Analytics do Azure no IoT Edge
description: Crie trabalhos do Edge no Azure Stream Analytics e implante-os nos dispositivos em execução no Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012607"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>O Stream Analytics do Azure no IoT Edge
 
Azure Stream Analytics no IoT Edge capacita os desenvolvedores a implantar a inteligência analítica quase em tempo real mais próxima de dispositivos IoT para que eles possam desbloquear o valor total dos dados gerados pelo dispositivo. O Azure Stream Analytics foi projetado para baixa latência, resiliência, uso eficiente da largura de banda e conformidade. As empresas podem implantar a lógica de controle perto das operações industriais e complementar a análise de Big data feita na nuvem.

Azure Stream Analytics no IoT Edge executado dentro da estrutura do [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Depois que o trabalho é criado no Stream Analytics, você pode implantá-lo e gerenciá-lo usando o Hub IoT.

## <a name="common-scenarios"></a>Cenários comuns

Esta seção descreve os cenários comuns de Stream Analytics no IoT Edge. O diagrama a seguir mostra o fluxo de dados entre os dispositivos IoT e a nuvem do Azure.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagrama de alto nível de IoT Edge":::

### <a name="low-latency-command-and-control"></a>Comando e controle de baixa latência

Os sistemas de segurança de manufatura devem responder a dados operacionais com latência extremamente baixa. Com Stream Analytics em IoT Edge, você pode analisar os dados do sensor quase em tempo real e emitir comandos ao detectar anomalias para interromper um computador ou disparar alertas.

### <a name="limited-connectivity-to-the-cloud"></a>Conectividade limitada com a nuvem

sistemas críticos, como equipamentos de mineração remotos, embarcações conectadas ou perfuração offshore, precisam analisar e reagir aos dados, mesmo quando a conectividade de nuvem é intermitente. Com o Stream Analytics, a lógica de streaming é executada independentemente da conectividade de rede e você pode escolher o que você envia para a nuvem para processamento adicional ou armazenamento.

### <a name="limited-bandwidth"></a>Largura de banda limitada

o volume de dados produzidos por motores a jato ou carros conectados pode ser tão grande que os dados devem ser filtrados ou pré-processados antes de serem enviados para a nuvem. Usando Stream Analytics, você pode filtrar ou agregar os dados que precisam ser enviados para a nuvem.

### <a name="compliance"></a>Conformidade

a conformidade normativa pode exigir que alguns dados sejam mantidos anônimos localmente ou agregados antes de serem enviados para a nuvem.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Trabalhos de borda no Azure Stream Analytics

Stream Analytics trabalhos de borda são executados em contêineres implantados em [dispositivos Azure IOT Edge](../iot-edge/about-iot-edge.md). Os trabalhos de borda são compostos por duas partes:

* Uma parte de nuvem que é responsável pela definição de trabalho: os usuários definem entradas, saída, consulta e outras configurações, como eventos fora de ordem, na nuvem.

* Um módulo em execução em seus dispositivos IoT. O módulo contém o mecanismo de Stream Analytics e recebe a definição de trabalho da nuvem. 

Stream Analytics usa o Hub IoT para implantar trabalhos de borda em dispositivos. Para obter mais informações, consulte [IOT Edge implantação](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Trabalho do Azure Stream Analytics no Edge":::

## <a name="edge-job-limitations"></a>Limitações de trabalho do Edge

O objetivo é ter a paridade entre os trabalhos do IoT Edge e os trabalhos de nuvem. A maioria dos recursos de linguagem de consulta SQL tem suporte para o Edge e a nuvem. No entanto, os recursos a seguir não têm suporte para trabalhos do Edge:
* Funções definidas pelo usuário (UDF) no JavaScript. Os UDF estão disponíveis no [C# para trabalhos do IoT Edge](./stream-analytics-edge-csharp-udf.md) (versão prévia).
* Agregações definidas pelo usuário (UDA).
* Funções do Azure ML.
* Formato AVRO para entrada/saída. No momento, há suporte apenas para CSV e JSON.
* Os seguintes operadores SQL:
    * PARTITION BY
    * GetMetadataPropertyValue
* Política de entrada tardia

### <a name="runtime-and-hardware-requirements"></a>Requisitos de hardware e de runtime
Para executar Stream Analytics no IoT Edge, você precisa de dispositivos que podem executar o [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Stream Analytics e Azure IoT Edge usar contêineres do **Docker** para fornecer uma solução portátil que é executada em vários sistemas operacionais de host (Windows, Linux).

Stream Analytics em IoT Edge é disponibilizada como imagens do Windows e do Linux, em execução em arquiteturas x86-64 ou ARM (Advanced RISC Machines). 


## <a name="input-and-output"></a>Entrada e saída

Stream Analytics trabalhos do Edge podem obter entradas e saídas de outros módulos em execução em dispositivos IoT Edge. Para se conectar de e para módulos específicos, você pode definir a configuração de roteamento no momento da implantação. Mais informações são descritas na [documentação do módulo de composição do IoT Edge](../iot-edge/module-composition.md).

Para entradas e saídas, há suporte para os formatos CSV e JSON.

Para cada fluxo de entrada e saída que você cria em seu trabalho de Stream Analytics, um ponto de extremidade correspondente é criado em seu módulo implantado. Esses pontos de extremidade podem ser usados nas rotas da implantação.

Os tipos de entrada de fluxo com suporte são:
* Hub do Edge
* Hub de evento
* Hub IoT

Os tipos de saída de fluxo com suporte são:
* Hub do Edge
* Banco de Dados SQL
* Hub de evento
* Armazenamento de BLOBs/ADLS Gen2

A entrada de referência dá suporte ao tipo de arquivo de referência. Outras saídas podem ser contatadas por meio de um downstream do trabalho de nuvem. Por exemplo, um trabalho do Stream Analytics hospedado no Edge envia a saída para o Hub do Edge, que pode então enviar a saída para o Hub IoT. Você pode usar um segundo trabalho de Azure Stream Analytics hospedado na nuvem com entrada do Hub IoT e saída para Power BI ou outro tipo de saída.

## <a name="license-and-third-party-notices"></a>Licenças e notificações de terceiros
* [Licença do Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para o Azure Stream Analytics no IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informações de imagem do módulo Azure Stream Analytics 

Esta informação de versão foi atualizada pela última vez em 2020-09-21:

- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - plataforma:
      - arquitetura: amd64
      - so: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - plataforma:
      - arquitetura: arm
      - so: linux
 
- Imagem: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - imagem base: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - plataforma:
      - arquitetura: arm64
      - so: linux
      
      
## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente a [página de Perguntas e respostas da Microsoft do Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximas etapas

* [Mais informações sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Stream Analytics no tutorial IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Desenvolver trabalhos de Borda do Stream Analytics utilizando ferramentas do Visual Studio](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Implementar CI/CD para Stream Analytics usando as APIs](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
