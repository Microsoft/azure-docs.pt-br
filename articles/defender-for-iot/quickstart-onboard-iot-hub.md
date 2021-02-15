---
title: 'Início Rápido: Habilitar o serviço'
description: Saiba como integrar e habilitar o serviço de segurança Defender para IoT no seu Hub IoT do Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e3768ef233c60f1687bc804778c3dabf32666e1d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835152"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Início Rápido: Integrar o serviço Azure Defender para IoT no Hub IoT

Este artigo oferece uma explicação de como habilitar o serviço Defender para IoT no seu Hub IoT existente. Se você não tiver, no momento, um Hub IoT, confira [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md) para começar a usar.

> [!NOTE]
> O Defender para IoT atualmente dá suporte apenas ao nível standard dos Hubs IoT.

## <a name="prerequisites-for-enabling-the-service"></a>Pré-requisitos para habilitar o serviço

- Espaço de trabalho do Log Analytics
  - Dois tipos de informação são armazenados por padrão no workspace do Log Analytics pelo Defender para IoT: **alertas de segurança** e **recomendações**.
  - Você pode optar por adicionar armazenamento de um tipo de informações adicionais **eventos brutos**. Observe que armazenar **eventos brutos** no Log Analytics transporta os custos de armazenamento adicionais.
- Hub IoT (camada standard)
- Atenda a todos os [pré-requisitos do sistema](quickstart-system-prerequisites.md).

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Habilitar o Defender para IoT no Hub IoT

Para habilitar a segurança no Hub IoT:

1. Abra o **Hub IoT** no portal do Azure.
1. No menu **Segurança**, clique em **Proteger sua solução de IoT**.

Parabéns! Você concluiu a habilitação do Defender para IoT no seu Hub IoT.

### <a name="geolocation-and-ip-address-handling"></a>Geolocalização e manipulação de endereço IP

Para proteger a solução de IoT, os endereços IP de conexões de entrada e saída nos dispositivos IoT, no IoT Edge e nos Hubs IoT são coletados e armazenados por padrão. Essas informações são essenciais para detectar a conectividade anormal de fontes de IP suspeitas. Por exemplo, quando são feitas tentativas de estabelecer conexões de uma fonte IP em um botnet conhecido ou de uma fonte IP fora de sua geolocalização. O serviço Defender para IoT oferece a flexibilidade para habilitar e desabilitar a qualquer momento a coleta de dados do endereço IP.

Para habilitar ou desabilitar a coleta de dados de endereços IP:

1. Abra o Hub IoT e selecione **Configurações** no menu **Segurança**.
1. Escolha a tela **Coleta de Dados** e modifique as configurações de geolocalização e/ou manipulação de IP como desejar.

### <a name="log-analytics-creation"></a>Criação do Log Analytics

Quando o Defender para IoT é ativado, um workspace do Azure Log Analytics padrão é criado para armazenar eventos de segurança brutos, alertas e recomendações para os dispositivos IoT, o IoT Edge e o Hub IoT. Todos os meses, os cinco (5) primeiros GB de dados ingeridos por cliente no serviço Azure Log Analytics são gratuitos. Cada GB de dados ingerido em seu workspace do Azure Log Analytics é retido gratuitamente nos primeiros 31 dias. Saiba mais sobre os preços do [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Para alterar a configuração do workspace do Log Analytics:

1. Abra o Hub IoT e selecione **Configurações** no menu **Segurança**.
1. Escolha a tela **Coleta de Dados** e modifique a configuração do workspace nas configurações do Log Analytics como desejar.

### <a name="customize-your-iot-security-solution"></a>Personalizar sua solução de segurança da IoT

Por padrão, a ativação da solução Defender para IoT protege automaticamente todos os Hubs IoT na sua assinatura do Azure.

Para ativar ou desativar o serviço Defender para IoT em um Hub IoT específico:

1. Abra o Hub IoT e selecione **Configurações** no menu **Segurança**.
1. Escolha a tela **Coleta de Dados** e modifique as configurações de segurança de qualquer hub IoT em sua assinatura do Azure como desejar.

## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para configurar sua solução...

> [!div class="nextstepaction"]
> [Configurar sua solução](quickstart-configure-your-solution.md)