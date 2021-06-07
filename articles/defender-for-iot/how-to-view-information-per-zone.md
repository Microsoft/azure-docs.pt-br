---
title: Saiba mais sobre dispositivos em zonas específicas
description: Use o console de gerenciamento local para obter informações abrangentes de exibição por zona específica
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776142"
---
# <a name="view-information-per-zone"></a>Exibir informações por zona


## <a name="view-a-device-map-for-a-zone"></a>Exibir um mapa de dispositivo para uma zona

Exibir um mapa de dispositivo para uma zona selecionada em um sensor. Essa exibição mostra todos os elementos de rede relacionados à zona selecionada, incluindo os sensores, os dispositivos conectados a eles e outras informações.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Captura de tela do mapa de zona.":::


- Na janela **Gerenciamento de site** , selecione **exibir mapa de zona** na barra que contém o nome da zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Região padrão para unidade de negócios padrão.":::

A janela **mapa do dispositivo** é exibida. As ferramentas a seguir estão disponíveis para exibir dispositivos e informações de dispositivo do mapa. Para obter detalhes sobre cada um desses recursos, consulte o *Guia do usuário da plataforma defender para IOT*.

- **Mapear exibições de zoom**: exibição simplificada, exibição de conexões e exibição detalhada. A exibição de mapa exibida varia dependendo do nível de zoom do mapa. Alterne entre exibições de mapa ajustando os níveis de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Ferramentas de layout e pesquisa de mapa**: ferramentas usadas para exibir segmentos variados de rede, dispositivos, grupos de dispositivos ou camadas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Captura de tela da exibição de ferramentas de pesquisa e layout.":::

- **Rótulos e indicadores em dispositivos:** Por exemplo, o número de dispositivos agrupados em uma sub-rede em uma rede de ti. Neste exemplo, é 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Captura de tela de rótulos e indicadores.":::

- **Exibir Propriedades do dispositivo**: por exemplo, o sensor que está monitorando o dispositivo e as propriedades básicas do dispositivo. Clique com o botão direito do mouse no dispositivo para exibir as propriedades do dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Captura de tela da exibição Propriedades do dispositivo.":::

- **Alerta associado a um dispositivo:** Clique com o botão direito do mouse no dispositivo para exibir alertas relacionados.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Captura de tela da exibição mostrar alertas.":::

## <a name="view-alerts-associated-with-a-zone"></a>Exibir alertas associados a uma zona

Para exibir alertas associados a uma zona específica:

- Selecione o ícone de alerta formulário a janela **zona** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="O modo de exibição de unidade de negócios padrão com exemplos.":::

Para obter mais informações, consulte [visão geral: trabalhando com alertas](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Exibir o inventário de dispositivos de uma zona

Para exibir o inventário de dispositivos associado a uma zona específica:

- Selecione **Exibir inventário de dispositivo** na janela **zona** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="A tela de inventário do dispositivo será exibida.":::

Para obter mais informações, consulte [investigar todas as detecções do sensor empresarial em um inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Exibir informações de zona adicionais

As informações de zona adicionais a seguir estão disponíveis:

- **Detalhes da zona**: exiba o número de dispositivos, alertas e sensores associados à zona.

- **Detalhes do sensor**: exiba o nome, o endereço IP e a versão de cada sensor atribuído à zona.

- **Status de conectividade**: se um sensor for desconectado, conecte-se do sensor. Consulte [conectar sensores ao console de gerenciamento local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Progresso da atualização**: se o sensor conectado estiver sendo atualizado, o status da atualização será exibido. Durante a atualização, o console de gerenciamento local não recebe informações do dispositivo do sensor.

## <a name="next-steps"></a>Próximas etapas

[Aprofunde-se nas ameaças globais, regionais e locais](how-to-gain-insight-into-global-regional-and-local-threats.md)
