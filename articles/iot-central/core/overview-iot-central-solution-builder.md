---
title: Criação de solução para o Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral da criação de soluções integradas com o IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417002"
---
# <a name="iot-central-solution-builder-guide"></a>Guia do Solution Builder IoT Central

*Este artigo se aplica a construtores de solução.*

Um aplicativo do IoT Central permite que você monitore e gerencie milhões de dispositivos ao longo de seu ciclo de vida. Este guia destina-se a construtores de solução que usam IoT Central para criar soluções integradas. Um aplicativo IoT Central permite que você gerencie dispositivos, analise a telemetria do dispositivo e integre-se a outros serviços de back-end.

Um Solution Builder:

- Configura painéis e exibições na interface do usuário do IoT Central Web.
- O usa as regras internas e as ferramentas de análise para gerar informações de negócios a partir dos dispositivos conectados.
- O usa a exportação de dados e os recursos de regras para integrar IoT Central com outros serviços de back-end.

## <a name="configure-dashboards-and-views"></a>Configurar painéis e exibições

Um aplicativo IoT Central pode ter um ou mais dashboards que os operadores usam para exibir e interagir com o aplicativo. Como um construtor de soluções, você pode personalizar o painel padrão e criar painéis especializados:

- Para exibir alguns exemplos de painéis personalizados, consulte [modelos focados no setor](concepts-app-templates.md#industry-focused-templates).
- Para saber mais sobre painéis, confira [criar e gerenciar vários dashboards](howto-create-personal-dashboards.md) e [Configurar o painel do aplicativo](howto-add-tiles-to-your-dashboard.md).

Quando um dispositivo se conecta a um IoT Central, o dispositivo é associado a um modelo de dispositivo para o tipo de dispositivo. Um modelo de dispositivo tem exibições personalizáveis que um operador usa para gerenciar dispositivos individuais. Como desenvolvedor de soluções, você pode criar e personalizar as exibições disponíveis para um tipo de dispositivo. Para saber mais, confira [Adicionar exibições](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Usar regras e análises internas

Um desenvolvedor de solução pode adicionar regras a um aplicativo IoT Central que executa ações personalizáveis. As regras avaliam condições, com base nos dados provenientes de um dispositivo, para determinar quando executar uma ação. Para saber mais sobre regras, consulte:

- [Tutorial: Criar uma regra e configurar notificações no aplicativo Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Criar ações de webhook em regras no Microsoft Azure IoT Central](howto-create-webhooks.md)
- [Agrupar várias ações para executar a partir de uma ou mais regras](howto-use-action-groups.md)

IoT Central tem recursos de análise internos que um operador pode usar para analisar os dados que fluem dos dispositivos conectados. Para saber mais, consulte [como usar a análise para analisar dados do dispositivo](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integrar com outros serviços

Como um construtor de soluções, você pode usar os recursos de regras e exportação de dados no IoT Central para integrar com outro serviço. Para saber mais, confira:

- [Exportar dados de IoT para destinos de nuvem usando a exportação de dados](howto-export-data.md)
- [Usar fluxos de trabalho para integrar o aplicativo Azure IoT Central a outros serviços em nuvem](howto-configure-rules-advanced.md)
- [Estender o Azure IoT Central com regras personalizadas usando Stream Analytics, Azure Functions e SendGrid](howto-create-custom-rules.md)
- [Estenda o IoT Central do Azure com análises personalizadas usando Azure Databricks](howto-create-custom-analytics.md)
- [Visualize e analise seus dados do Azure IoT Central em um painel do Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Próximas etapas

Se quiser saber mais sobre como usar o IoT Central, as próximas etapas sugeridas são experimentar os guias de início rápido, começando em [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).
