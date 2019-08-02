---
title: Como usar blocos no painel de controle de aplicativo do Azure IoT Central | Microsoft Docs
description: Como um construtor, saiba como usar blocos nos painéis do aplicativo, painéis de conjunto de dispositivo e painéis do dispositivo.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503520"
---
# <a name="how-to-use-tiles"></a>Como usar blocos
Você pode usar blocos para personalizar os painéis do aplicativo, painéis de dispositivo, e o dispositivo definido painéis. Você pode adicionar vários blocos em um painel e personalizar esses blocos para mostrar informações relevantes para seu aplicativo. Você também pode redimensionar blocos e personalizar o layout em qualquer dashboard. A captura de tela abaixo mostra o painel com blocos diferentes do aplicativo.

![Painel do aplicativo](media/howto-use-tiles/image1a.png)


A tabela a seguir resume o uso de blocos no Azure IoT Central:

 
| Bloco | painel | DESCRIÇÃO
| ----------- | ------- | ------- |
| Link | Painéis de conjunto de aplicativos e dispositivos |Os blocos de link são clicáveis blocos que exibem texto de título e descrição. Use um bloco de link para permitir que um usuário navegar para uma URL que está relacionada ao seu aplicativo. |
| Image | Painéis de conjunto de aplicativos e dispositivos |Blocos de imagem exibem uma imagem personalizada e podem ser clicáveis. Use um bloco de imagem para adicionar elementos gráficos em um painel e, opcionalmente, permitir que um usuário navegar para uma URL que é relevante para seu aplicativo.|
| Rotular | Painéis do aplicativo |Blocos de rótulo exibem texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um bloco de rótulo para adicionar informações relevantes para o painel essas descrições, detalhes de contato ou ajuda|
| Mapa | Painéis de conjunto de aplicativos e dispositivos |Peças de mapa exibem o local e o estado de um dispositivo em um mapa. Por exemplo, você pode exibir em que é um dispositivo e se o ventilador está ativado.|
| Gráfico de linhas | Painéis de aplicativo e dispositivo |Blocos do gráfico de linha exibem um gráfico de medida de agregação para um dispositivo para um período de tempo. Por exemplo, você pode exibir um gráfico de linhas que mostra a temperatura média e a pressão em um dispositivo para a última hora|
| Gráfico de Barras | Painéis de aplicativo e dispositivo |Blocos de gráfico de barras exibem um gráfico de medições de agregação para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de barras que mostra a temperatura média e a pressão em um dispositivo para a última hora |
| Histórico de eventos | Painéis de aplicativo e dispositivo |Bloco do histórico de eventos exibe os eventos para um dispositivo em um período de tempo. Por exemplo, você pode usá-lo para mostrar que todas as alterações de temperatura ocorreram para um dispositivo durante a última hora. |
| Histórico de Estado | Painéis de aplicativo e dispositivo |Bloco de histórico de estado exibe os valores de medida para um período de tempo. Por exemplo, você pode usá-lo para mostrar os valores de temperatura para um dispositivo durante a última hora.|
| KPI | Painéis de aplicativo e dispositivo | Bloco KPI exibe uma medida de telemetria ou evento de agregação para um período de tempo. Por exemplo, você pode usá-lo para mostrar a temperatura máxima atingida para um dispositivo durante a última hora.|
| Último valor conhecido | Painéis de aplicativo e dispositivo |Bloco do último valor conhecido exibe o último valor para uma medida de estado ou de telemetria. Por exemplo, você pode usar esse bloco para exibir medidas mais recentes de temperatura, pressão e umidade para um dispositivo.|
| Grade de conjunto de dispositivo | Painéis de conjunto de aplicativos e dispositivos | Dispositivo definido grade exibe informações sobre o conjunto de dispositivo. Use um bloco de grade do dispositivo definido para mostrar informações como nome, local e o modelo de todos os dispositivos no conjunto de dispositivos.|


Para saber mais sobre como configurar o painel no aplicativo do Azure IoT Central, consulte [painel do aplicativo configurar](howto-configure-homepage.md).
