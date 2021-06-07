---
title: Introdução aos aceleradores de solução de IoT – Azure | Microsoft Docs
description: Saiba mais sobre os aceleradores de solução de IoT do Azure. Os aceleradores de solução de IoT são soluções de IoT completas, de ponta a ponta e prontas para implantação.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c966051ed5699d408fe83f1e9c862ca78b3282c4
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714534"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>O que são aceleradores de solução do Azure IoT?

Uma solução de IoT baseada em nuvem normalmente usa o código personalizado e serviços de nuvem para gerenciar a conectividade do dispositivo, o processamento de dados e análise e apresentação.

Os aceleradores de solução de IoT são completos e prontos para implantar soluções de IoT que implementam cenários comuns de IoT. Os cenários incluem simulações de dispositivos e fábricas conectadas. Quando você implanta um acelerador de solução, a implantação inclui todos os serviços baseados em nuvem necessários junto com qualquer código de aplicativo necessário.

Os aceleradores de solução são pontos de partida para suas próprias soluções de IoT. O código-fonte para todos os aceleradores de solução é um software livre e está disponível no GitHub. Recomenda-se baixar e personalizar os aceleradores de solução para atender às suas necessidades.

Você também pode usar os aceleradores de solução como ferramentas de aprendizado antes de compilar uma solução de IoT personalizada do zero. Os aceleradores de solução implementam práticas comprovadas para soluções de IoT baseadas em nuvem para você seguir.

O código do aplicativo em cada acelerador de solução inclui um aplicativo Web que permite que você gerencie o acelerador de solução.

> [!NOTE]
> As soluções de monitoramento remoto e manutenção preditiva foram removidas do site de [Aceleradores de solução IoT do Azure](https://www.azureiotsolutions.com/Accelerators). Para saber mais, confira [O que são os aceleradores de solução IoT do Azure? (versão anterior)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Cenários de IoT com suporte

Atualmente, há dois aceleradores de soluções disponíveis para implantação:

### <a name="connected-factory"></a>Fábrica conectada

Use o [acelerador de solução Alocador Conectado](iot-accelerators-connected-factory-features.md) para coletar a telemetria de ativos industriais com uma interface de [Arquitetura Unificada OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) e para controlá-los. Os ativos industriais podem incluir montagem e teste de estações em uma linha de produção de fábrica.

É possível usar o painel da fábrica conectada para monitorar e gerenciar seus dispositivos industriais:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Captura de tela que mostra o painel da solução de fábrica conectada." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Simulação de dispositivo

Use este [acelerador de solução de Simulação de Dispositivo](iot-accelerators-device-simulation-overview.md) para executar dispositivos simulados que geram telemetria realista. Você pode usar este acelerador de solução para testar o comportamento de outros aceleradores de solução ou testar suas próprias soluções personalizadas de IoT.

Você pode usar o aplicativo Web de simulação de dispositivo para configurar e executar simulações:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Captura de tela que mostra o painel da solução de simulação de dispositivo." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Princípios de design

Todos os aceleradores de solução seguem os mesmos princípios de design e metas. Eles são projetados para serem:

* **Escalonáveis**, permitindo que você conecte e gerencie milhões de dispositivos conectados.
* **Extensíveis**, permitindo que você personalize-os para atender às suas necessidades.
* **Compreensíveis**, permitindo que você entenda como eles funcionam e como eles são implementados.
* **Modulares**, permitindo que você troque os serviços por alternativas.
* **Seguros**, combinando a segurança do Azure com recursos internos de segurança do dispositivo e de conectividade.

## <a name="architectures-and-languages"></a>Arquiteturas e linguagens

Os aceleradores de solução originais foram escritos usando o .NET usando uma arquitetura de model-view-controller (MVC). A Microsoft está atualizando os aceleradores de solução para uma nova arquitetura de microsserviços. A tabela a seguir mostra o status atual dos aceleradores de solução com links para repositórios GitHub:

| Acelerador de solução   | Arquitetura  | Languages     |
| ---------------------- | ------------- | ------------- |
| Fábrica conectada      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulação de dispositivo      | Microsserviços | [.NET](https://github.com/Azure/azure-iot-pcs-device-simulation)          |

Para saber mais sobre a arquitetura de microsserviços, confira [Introdução à arquitetura de referência do Azure IoT](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Opções de implantação

Você pode implantar os aceleradores de solução do site [Aceleradores de solução IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#) ou usando a linha de comando.

O custo para executar um acelerador de solução é o [custo combinado da execução dos serviços subjacentes do Azure](https://azure.microsoft.com/pricing). Você verá os detalhes dos serviços do Azure usados quando você escolher as opções de implantação.

## <a name="next-steps"></a>Próximas etapas

Para experimentar um dos aceleradores de solução de IoT, confira os início rápido [Experimentar uma solução de fábrica conectada](quickstart-connected-factory-deploy.md).
