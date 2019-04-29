---
title: Integrar os dados do SIM na Solução de Monitoramento Remoto - Azure | Microsoft Docs
description: Este artigo descreve como integrar os dados do SIM da Telefónica à solução de Monitoramento Remoto.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442200"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrar dados do SIM na solução de monitoramento remoto

Os dispositivos IoT geralmente se conectam à nuvem usando um cartão SIM que permite enviar fluxos de dados de qualquer lugar. A solução de Monitoramento Remoto de IoT do Azure permite a integração de dados de Conectividade Gerenciada de IoT, de modo que os operadores também possam acompanhar a integridade do dispositivo por meio dos dados fornecidos pelo SIM de IoT.

O Monitoramento Remoto fornece integração pronta para uso com a Conectividade IoT da Telefónica, permitindo que os clientes que usam sua Plataforma de Conectividade IoT sincronizem os dados de conectividade dos SIMs de seus dispositivos com as soluções. Essa solução pode ser estendida para dar suporte a outros provedores de Conectividade IoT por meio do [repositório](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) GitHub.

Neste tutorial, você aprenderá como:

* Integrar os dados do SIM de IoT da Telefónica à solução de Monitoramento Remoto
* Exibir telemetria em tempo real
* Exibir dados do SIM

## <a name="telefnica-iot-integration-setup"></a>Configuração da integração de IoT da Telefónica

### <a name="prerequisites"></a>Pré-requisitos

Atualmente, esse recurso adicional de Monitoramento Remoto está em versão prévia. Para sincronizar seus dados de conectividade na solução de monitoramento remoto do Azure, siga estas etapas:

1. Preencha uma solicitação no [site da Telefónica](https://iot.telefonica.com/contact), selecione a opção **Monitoramento Remoto do Azure**, incluindo seus dados de contato.
2. A Telefónica ativará sua conta.
3. Caso ainda não seja um cliente da Telefónica e deseja aproveitar esse ou outros serviços Prontos para a Nuvem de Conectividade IoT, visite o [site da Telefónica](https://iot.telefonica.com/) e selecione a opção **Conectividade**.

### <a name="telefnica-sim-setup"></a>Configuração do SIM da Telefónica
A associação do SIM da Telefónica e da identificação do dispositivo gêmeo do Azure baseia-se na propriedade "alias" do SIM de IoT da Telefónica. 

Navegue para o [Portal da Plataforma de Conectividade IoT da Telefónica](https://m2m-movistar-es.telefonica.com/) > Inventário de SIM > selecione o SIM e atualize cada "alias" de SIM com a ID de Dispositivo gêmeo desejada. Essa tarefa também pode ser realizada em massa (consulte os manuais do usuário da Plataforma de Conectividade IoT da Telefónica).

Essa tarefa também pode ser realizada em massa (consulte os manuais do usuário da plataforma de conectividade de IoT da Telefonica)

![Atualização da Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Para conectar seu dispositivo ao monitoramento remoto, você pode seguir esses tutoriais usando [C](iot-accelerators-connecting-devices-linux.md) ou [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Exibir a telemetria do dispositivo e as propriedades do SIM

Após configurar sua conta da Telefónica corretamente e conectar o dispositivo, você poderá exibir os detalhes do dispositivo e os dados do SIM.

Os parâmetros de conectividade a seguir são publicados:

* ICCID
* IP
* Presença de rede
* Status do SIM
* Localização baseada em rede
* Tráfego de dados consumidos

![painel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você já tem uma visão geral de como integrar os dados do SIM no monitoramento remoto do Azure IoT, aqui estão as próximas etapas sugeridas para os aceleradores de solução:

* [Operar a solução de Monitoramento Remoto do Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Executar o monitoramento avançado](iot-accelerators-remote-monitoring-monitor.md)
* [Gerenciar seus dispositivos](iot-accelerators-remote-monitoring-manage.md)
* [Solucionar problemas de dispositivos](iot-accelerators-remote-monitoring-maintain.md)

