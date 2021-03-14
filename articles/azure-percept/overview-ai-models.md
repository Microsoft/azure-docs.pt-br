---
title: Modelos de IA do Azure Percept
description: Saiba mais sobre os modelos de ia disponíveis para protótipos e implantação
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: d0cdabb3b22d642a7903810181106b09c549e1a2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218155"
---
# <a name="azure-percept-ai-models"></a>Modelos de IA do Azure Percept

O Azure Percept permite que você desenvolva e implante modelos de ia diretamente em seu Percept do Azure DK no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). A implantação de modelo utiliza o [Hub IOT do Azure](https://azure.microsoft.com/services/iot-hub/) e [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Modelos de ia de exemplo

O Azure Percept Studio contém modelos de exemplo para os seguintes aplicativos:

- detecção de pessoas
- detecção de veículo
- detecção geral de objeto
- produtos – detecção na prateleira

Com modelos pré-treinados, nenhuma coleta de dados de codificação ou treinamento é necessária. Basta implantar o modelo desejado no Azure Percept DK do portal e abrir o fluxo de vídeo de seu devkit para ver o modelo inferência em ação. A telemetria do modelo inferência também pode ser acessada por meio da ferramenta do [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="reference-solutions"></a>Soluções de referência

Uma [solução de referência de contagem de pessoas](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) também está disponível. Essa solução de referência é um aplicativo de ia de software livre que fornece às pessoas baseadas em borda a contagem de eventos de entrada/saída de zona definida pelo usuário. A saída de vídeo e ia do dispositivo de borda local é proveniente de [Azure data Lake](https://azure.microsoft.com/solutions/data-lake/), com a interface do usuário em execução como um site do Azure. O ia inferência é fornecido por um modelo de ia de software livre para detecção de pessoas.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Gif da solução de análise espacial criada previamente.":::

## <a name="custom-no-code-solutions"></a>Soluções personalizadas sem código

Por meio do Azure Percept Studio, você pode desenvolver soluções personalizadas de [visão](./tutorial-nocode-vision.md) e fala, sem necessidade de codificação.

Para soluções de visão personalizada, os modelos de ia de detecção e classificação de objetos estão disponíveis. Basta carregar e marcar suas imagens de treinamento, que podem ser tomadas diretamente com o SoM de visão do Azure Percept do Azure Percept DK, se desejado. O treinamento e a avaliação do modelo são facilmente executados em [visão personalizada](https://www.customvision.ai/), que faz parte dos [Serviços cognitivas do Azure](https://azure.microsoft.com/services/cognitive-services/#overview).

Para soluções de fala personalizadas, os modelos de assistente de voz estão disponíveis atualmente para os seguintes aplicativos:

- Hotelaria: sala de Hotel equipado com dispositivos inteligentes controlados por voz.
- Saúde: recursos de atendimento equipados com dispositivos inteligentes controlados por voz.
- Inventário: Hub de inventário equipado com dispositivos inteligentes controlados por voz.
- Automotivo: Hub automotivado equipado com dispositivos inteligentes controlados por voz.

Os comandos e palavras-chave de assistente de voz pré-criados estão disponíveis diretamente por meio do Portal. Palavras-chave e comandos personalizados podem ser criados e treinados no [Speech Studio](https://speech.microsoft.com/), que também faz parte dos serviços cognitivas do Azure.

## <a name="advanced-development"></a>Desenvolvimento avançado

Consulte o [GitHub de desenvolvimento avançado do Azure PERCEPT DK](https://github.com/microsoft/azure-percept-advanced-development) para obter orientações atualizadas, tutoriais e exemplos para coisas como:

* Trazendo um modelo de ia personalizado para o dispositivo
* Atualizando um modelo que já oferecemos suporte com o aprendizado de transferência
* E mais
