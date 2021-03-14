---
title: Exibir e configurar métricas do Conector IoT do Azure para FHIR (versão prévia)
description: Este artigo explica como exibir e configurar as métricas do conector IoT do Azure para FHIR (versão prévia).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017693"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Exibir e configurar métricas do Conector IoT do Azure para FHIR (versão prévia) 

Neste artigo, você aprenderá a exibir e configurar o conector IoT do Azure para recursos de interoperabilidade do Fast Healthcare (FHIR&#174;) * métricas.

> [!TIP]
> Para saber como configurar a exportação de dados de métricas, siga as orientações em [exportar o conector do Azure IOT para métricas de FHIR (versão prévia) por meio das configurações de diagnóstico](iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Exibir métricas para o conector do Azure IoT para FHIR (versão prévia)

1. Entre no portal do Azure e, em seguida, selecione sua API do Azure para o serviço FHIR. 

2. No painel esquerdo, selecione **métricas**. 

3. Selecione a guia **conector IOT** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Captura de tela do painel ' conector IoT ', com grafos de linha que exibem o número de mensagens de entrada e normalizadas." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecione um conector IoT para exibir suas métricas. Por exemplo, há quatro conectores IoT (*conector 1*, *conector 2* e assim por diante) associados a essa API do Azure para o serviço FHIR.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Captura de tela do painel ' conector IoT ', exibindo as guias 1, 2, 3 e 4 do conector IoT." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Selecione o período de tempo (por exemplo, **1 hora**, **24 horas**, **7 dias** ou **personalizado**) das métricas do conector IOT que você deseja exibir. Ao selecionar a guia **personalizado** , você pode criar combinações de data/hora específicas para exibir as métricas do conector IOT.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Captura de tela do painel ' conector IoT ', exibindo um gráfico de linha de período de tempo de ' 1 hora ' para ' conector 1 '." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Tipos de métrica para o conector do Azure IoT para FHIR (versão prévia) 

> [!TIP]
> Para saber mais sobre o fluxo de dados no conector IoT do Azure para FHIR, veja o [fluxo de dados do Azure IOT Connector para FHIR (versão prévia)](iot-data-flow.md) e o [conector de IOT do Azure para FHIR (visualização) guia de solução de problemas](iot-troubleshoot-guide.md) para saber mais sobre mensagens de erro e correções.

As métricas do conector IoT que você pode exibir estão listadas na tabela a seguir:

|Tipo de métrica|Finalidade da métrica| 
|-----------|--------------|
|Número de mensagens de entrada|Exibe o número de mensagens de entrada não processadas recebidas (por exemplo, os eventos de dispositivo).|
|Número de mensagens normalizadas|Exibe o número de mensagens normalizadas.|
|Número de grupos de mensagens|Exibe o número de grupos que têm mensagens agregadas na janela de tempo designada.|
|Latência média de estágio normalizada|Exibe a latência média do estágio normalizado. O estágio normalizado executa a normalização em mensagens de entrada brutas.|
|Latência média de estágio de grupo|Exibe a latência média do estágio do grupo. O estágio Group executa o armazenamento em buffer, agregação e agrupamento em mensagens normalizadas.| 
|Contagem Total de Erros|Exibe o número total de erros.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Concentrar-se e configurar o conector do Azure IoT para métricas de FHIR (versão prévia)

Neste exemplo, vamos nos concentrar no **número de métricas de mensagens de entrada** .

1. Selecione um ponto no tempo no qual você deseja se concentrar.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Captura de tela do painel de métrica ' número de mensagens recebidas ', destacando um único ponto no tempo no grafo de linha." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. No painel **número de mensagens de entrada** , você pode personalizar ainda mais a métrica selecionando **Adicionar métrica**, **Adicionar filtro** ou **aplicar divisão**. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Captura de tela do painel de métrica ' número de mensagens recebidas ', destacando os botões ' Adicionar métrica ', ' Adicionar filtro ' e ' aplicar divisão '." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusão 
Ter acesso às métricas do plano de dados é essencial para o monitoramento e a solução de problemas. O conector do Azure IoT para FHIR ajuda você com essas ações por meio de métricas. 

## <a name="next-steps"></a>Próximas etapas

Obtenha respostas para perguntas frequentes sobre o conector do Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[FAQ do conector do Azure IoT para FHIR](fhir-faq.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia). FHIR é uma marca registrada da HL7, usada com permissão da HL7. 
