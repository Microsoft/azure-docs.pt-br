---
title: Recursos da solução de Fábrica Conectada - Azure | Microsoft Docs
description: Este artigo descreve uma visão geral dos recursos da solução pré-configurada de fábrica conectada, como painel de nuvem, regras e alertas.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: e492ebf70f69c985691852a1c1f2351d1e09578e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645963"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>O que é um acelerador da solução de IoT Fábrica Conectada?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Fábrica Conectada é uma implementação da arquitetura de referência de IoT Industrial do Microsoft Azure, empacotada como uma solução de software livre. É possível utilizá-la como ponto de partida para um produto comercial. Você pode implantar uma versão de pré-build da solução de Fábrica Conectada na sua assinatura do Azure por meio dos [aceleradores de solução do Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Painel da solução de Fábrica Conectada](./media/iot-accelerators-connected-factory-features/dashboard.png)

O código do acelerador de solução de Fábrica Conectada [está disponível no GitHub](https://github.com/Azure/azure-iot-connected-factory).

A Fábrica Conectada inclui os recursos a seguir:

## <a name="industrial-device-interoperability"></a>Interoperabilidade de dispositivo industrial

- Conecte ativos industriais com uma interface OPC UA.
- Use as linhas de produção simuladas (executando servidores OPC UA em contêineres do Docker) para ver a telemetria em tempo real a partir delas.
- Navegue pelo modelo de informações do OPC UA dos servidores OPC UA a partir de um painel de nuvem.

## <a name="remote-management"></a>Gerenciamento remoto

- Configure os ativos do OPC UA no painel de nuvem (métodos de chamada, leitura e gravação de dados).
- Publique e cancele publicação de dados telemétricos dos ativos do OPC UA a partir de um painel de nuvem.

## <a name="cloud-dashboard"></a>Painel de nuvem

- Visualize versões prévias de telemetria diretamente em um painel de nuvem.
- Visualize tendências em dados telemétricos e crie correlações usando o painel do Explorador do Time Series Insights.
- Consulte a OEE (Eficiência Geral do Equipamento) calculada e as KPIs (Indicadores Chave de Desempenho) de um painel de nuvem.
- Visualize hierarquias de ativos industriais em uma topologia de árvore, bem como em um mapa interativo.
- Visualizar, reconhecer e fechar alertas de um painel de nuvem.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- O [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) foi criado para armazenar, visualizar e consultar grandes quantidades de dados de séries temporais. A Fábrica Conectada aproveita esse serviço.
- A Fábrica Conectada integra-se a esse serviço, permitindo que você realize uma análise profunda e em tempo real dos dados do dispositivo.

## <a name="rules-and-alerts"></a>Regras e alertas

[Configure regras baseadas em limites para alertas](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Segurança de ponta a ponta

- Configure permissões de segurança para usuários usando o RBAC (controle de acesso baseado em função).
- A criptografia de ponta a ponta é implementada usando a autenticação OPC UA (usando certificados X.509), bem como tokens de segurança.

## <a name="customizability"></a>Personalização

- Personalize a solução para atender a requisitos de negócios específicos.
- Código-fonte da solução completa disponível no GitHub. Confira o repositório da [Solução pré-configurada de Fábrica Conectada](https://github.com/Azure/azure-iot-connected-factory).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Solution Accelerator da fábrica conectada, consulte o início rápido [Experimente uma solução baseada em nuvem para gerenciar meus dispositivos IOT industriais](quickstart-connected-factory-deploy.md).
