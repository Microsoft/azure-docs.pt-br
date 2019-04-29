---
title: Atualizar o Hub IoT | Microsoft Docs
description: Altere o tipo e escala de preço do Hub IoT para obter mais recursos de gerenciamento de dispositivo e mensagens.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440194"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Como fazer upgrade do Hub IoT

Na medida em que a solução de IoT aumentar, o Hub IoT estará pronto para ajudá-lo a escalar verticalmente. O Hub IoT oferece duas camadas, básico (B) e standard (S), para acomodar clientes que desejam usar recursos diferentes. Dentro de cada camada há três tamanhos (1, 2 e 3) que determinam o número de mensagens que podem ser enviadas por dia.

Quando você tiver mais dispositivos e precisar de mais recursos, haverá três maneiras de ajustar o Hub IoT para atender às suas necessidades:

* Adicione unidades no Hub IoT. Por exemplo, cada unidade adicional em um Hub IoT B1 permite mais 400.000 mensagens por dia.

* Altere o tamanho do Hub IoT. Por exemplo, migre da camada B1 para a camada B2 para aumentar o número de mensagens que pode dar suporte a cada unidade por dia.

* Atualize para uma camada superior. Por exemplo, atualize da camada B1 para a camada S1 para acesso a recursos avançados com a mesma capacidade de mensagens.

Todas essas mudanças podem ocorrer sem interromper as operações existentes.

Se você quiser fazer o downgrade de seu hub IoT, você pode remover unidades e reduzir o tamanho do hub IoT, mas não é possível fazer o downgrade para uma camada inferior. Por exemplo, você pode mover da camada S2 para a camada de S1, mas não da camada S2 para o nível B1. Apenas um tipo de [edição do Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de uma camada pode ser escolhido por IoT Hub. Por exemplo, você pode criar um Hub IoT com várias unidades do S1, mas não com uma mistura de unidades de edições diferentes, como S1 e B3, ou S1 e S2.

Esses exemplos destinam-se a ajudá-lo a reconhecer como ajustar o Hub IoT na medida em que a solução altera. Para obter informações específicas sobre os recursos de cada camada, você deve sempre consultar [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Fazer upgrade do Hub IoT existente

1. Entre no [Portal do Azure](https://portal.azure.com/) e navegue até o Hub IoT.

2. Selecione **Preço e escala**.

   ![Preços e dimensionamento](./media/iot-hub-upgrade/pricing-scale.png)

3. Para alterar a camada do hub, selecione **Tipo e escala de preço**. Escolha a nova camada e clique em**selecionar**.

   ![Tipo e escala de preço](./media/iot-hub-upgrade/select-tier.png)

4. Para alterar o número de unidades no hub, insira um novo valor em **Unidades do Hub IoT**.

5. Selecione **Salvar** para salvar as alterações.

O Hub IoT agora está ajustado e as configurações permanecem inalteradas.

O limite de partição máxima para camada básica IoT Hub e IoT Hub de camada padrão é 32. A maioria dos Hubs IoT precisa apenas de 4 partições. O limite de partição é escolhido quando o Hub IoT é criado e se relaciona as mensagens de dispositivo para a nuvem com o número de leitores simultâneos dessas mensagens. Esse valor permanecerá inalterado quando você migrar da camada Básica para a camada Standard.

## <a name="next-steps"></a>Próximas etapas

Obtenha mais detalhes sobre [Como escolher a camada correta do Hub IoT](iot-hub-scaling.md).