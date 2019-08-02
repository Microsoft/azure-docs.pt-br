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
ms.openlocfilehash: d9dd3ff2f24660404a1ab0440668c4d4da1e7319
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080524"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>O que são aceleradores de solução do Azure IoT?

Uma solução de IoT baseada em nuvem normalmente usa o código personalizado e serviços de nuvem para gerenciar a conectividade do dispositivo, o processamento de dados e análise e apresentação.

Os aceleradores de solução de IoT são completos e prontos para implantar soluções de IoT que implementam cenários comuns de IoT. Os cenários incluem monitoramento remoto, fábrica conectada, manutenção preditiva e simulação de dispositivo. Quando você implanta um acelerador de solução, a implantação inclui todos os serviços baseados em nuvem necessários junto com qualquer código de aplicativo necessário.

Os aceleradores de solução são pontos de partida para suas próprias soluções de IoT. O código-fonte para todos os aceleradores de solução é um software livre e está disponível no GitHub. Recomenda-se baixar e personalizar os aceleradores de solução para atender às suas necessidades.

Você também pode usar os aceleradores de solução como ferramentas de aprendizado antes de compilar uma solução de IoT personalizada do zero. Os aceleradores de solução implementam práticas comprovadas para soluções de IoT baseadas em nuvem para você seguir.

O código do aplicativo em cada acelerador de solução inclui um aplicativo Web que permite que você gerencie o acelerador de solução.

## <a name="supported-iot-scenarios"></a>Cenários de IoT com suporte

Atualmente, há quatro aceleradores de soluções disponíveis para implantação:

### <a name="remote-monitoring"></a>Monitoramento remoto

Use o [acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md) para coletar telemetria de dispositivos remotos e controlá-los. Os exemplos de dispositivo incluem sistemas de arrefecimento instalados no local dos seus clientes ou válvulas instaladas em estações de bombeamento remotas.

É possível usar o painel de monitoramento remoto para exibir a telemetria dos seus dispositivos conectados, provisionar novos dispositivos ou atualizar o firmware em seus dispositivos conectados:

[![Painel da solução de monitoramento remoto](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Fábrica conectada

Use o [acelerador de solução Alocador Conectado](iot-accelerators-connected-factory-features.md) para coletar a telemetria de ativos industriais com uma interface de [Arquitetura Unificada OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) e para controlá-los. Os ativos industriais podem incluir montagem e teste de estações em uma linha de produção de fábrica.

É possível usar o painel da fábrica conectada para monitorar e gerenciar seus dispositivos industriais:

[![Painel da solução de fábrica conectada](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Manutenção preditiva

Use este [acelerador de solução de Manutenção Preditiva](iot-accelerators-predictive-walkthrough.md) para prever quando um dispositivo remoto deve falhar, para que você possa realizar manutenção antes que o dispositivo falhe. Este acelerador de solução usa algoritmos de aprendizado de máquina para prever falhas de telemetria do dispositivo. Os exemplos de dispositivo incluem motores de avião ou elevadores.

É possível usar o painel de manutenção preditiva para exibir a análise de manutenção preditiva:

[![Painel da solução de fábrica conectada](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Simulação de dispositivo

Use este [acelerador de solução de Simulação de Dispositivo](iot-accelerators-device-simulation-overview.md) para executar dispositivos simulados que geram telemetria realista. Você pode usar este acelerador de solução para testar o comportamento de outros aceleradores de solução ou testar suas próprias soluções personalizadas de IoT.

Você pode usar o aplicativo Web de simulação de dispositivo para configurar e executar simulações:

[![Painel da solução de fábrica conectada](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Princípios de design

Todos os aceleradores de solução seguem os mesmos princípios de design e metas. Eles são projetados para serem:

* **Escalonáveis**, permitindo que você conecte e gerencie milhões de dispositivos conectados.
* **Extensíveis**, permitindo que você personalize-os para atender às suas necessidades.
* **Compreensíveis**, permitindo que você entenda como eles funcionam e como eles são implementados.
* **Modulares**, permitindo que você troque os serviços por alternativas.
* **Seguros**, combinando a segurança do Azure com recursos internos de segurança do dispositivo e de conectividade.

## <a name="architectures-and-languages"></a>Arquiteturas e linguagens

Os aceleradores de solução originais foram escritos usando o .NET usando uma arquitetura de model-view-controller (MVC). A Microsoft está atualizando os aceleradores de solução para uma nova arquitetura de microsserviços. A tabela a seguir mostra o status atual dos aceleradores de solução com links para repositórios GitHub:

| Acelerador de solução   | Arquitetura  | Linguagens     |
| ---------------------- | ------------- | ------------- |
| Monitoramento remoto      | Microsserviços | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) e [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Manutenção preditiva | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Fábrica conectada      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulação de dispositivo      | Microsserviços | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Para saber mais sobre a arquitetura de microsserviços, confira [Introdução à arquitetura de referência do Azure IoT](iot-accelerators-architecture-overview.md).

## <a name="deployment-options"></a>Opções de implantação

Você pode implantar os aceleradores de solução do site [Aceleradores de solução IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#) ou usando a linha de comando.

Você pode implantar o acelerador de solução de monitoramento remoto nas seguintes configurações:

* **Standard:** implantação de infraestrutura expandida para o desenvolvimento de uma implantação de produção. O Serviço de Contêiner do Azure implanta os microsserviços em máquinas virtuais do Azure. O Kubernetes orquestra os contêineres do Docker que hospedam os microsserviços individuais.
* **Básica:** versão de custo reduzido para obter uma demonstração ou testar uma implantação. Todos os microsserviços implantam em uma única máquina virtual do Azure.
* **Local:** implantação do computador local para teste e desenvolvimento. Essa abordagem implanta os microsserviços em um contêiner do Docker local e conecta-se ao Hub IoT, Azure Cosmos DB e serviços de armazenamento do Azure na nuvem.

O custo para executar um acelerador de solução é o [custo combinado da execução dos serviços subjacentes do Azure](https://azure.microsoft.com/pricing). Você verá os detalhes dos serviços do Azure usados quando você escolher as opções de implantação.

## <a name="next-steps"></a>Próximas etapas

Para experimentar um dos aceleradores de solução de IoT, confira os guias de início rápido:

* [Experimente uma solução de monitoramento remoto](quickstart-remote-monitoring-deploy.md)
* [Experimente uma solução de fábrica conectada](quickstart-connected-factory-deploy.md)
* [Experimente uma solução de manutenção preditiva](quickstart-predictive-maintenance-deploy.md)
* [Experimente uma solução de simulação de dispositivo](quickstart-device-simulation-deploy.md)
