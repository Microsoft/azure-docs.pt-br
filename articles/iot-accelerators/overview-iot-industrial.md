---
title: Visão geral de IoT industrial do Azure | Microsoft Docs
description: Visão geral de IoT industrial
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3c3a54d469d3dcbe04c11aa049906b551d68022f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606198"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é o IIoT (IoT industrial)

IIoT é a Internet das Coisas Industrial. IIoT aprimora a eficiência industrial por meio da aplicação de IoT no setor de fabricação. 

## <a name="improve-industrial-efficiencies"></a>Melhorar as eficiências industriais

Aprimore sua produtividade e lucratividade operacionais com um acelerador de solução de fábrica conectada. Conecte e monitore seus dispositivos e equipamentos industriais na nuvem – incluindo as máquinas que já operam no chão de fábrica. Analise seus dados de IoT para obter insights que o ajudam a aumentar o desempenho de todo o chão de fábrica.

Reduza o processo demorado de acessar computadores do chão de fábrica com gerenciamento OPC Twin e dedique seu tempo à criação de soluções de IIoT. Simplifique o gerenciamento de certificados e a integração de ativos industriais com o OPC Vault e tenha certeza de que a conectividade do ativo está protegida. Esses microsserviços fornecem uma API REST semelhante além dos [componentes do Azure IoT Industrial](https://github.com/Azure/azure-iiot-opc-ua). A API do serviço dá a você controle sobre a funcionalidade do módulo de borda. 

![Visão geral de IoT industrial](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Para obter mais informações sobre os serviços de IoT Industrial do Azure, veja o [repositório ](https://github.com/Azure/azure-iiot-services)do GitHub.
Se você não estiver familiarizado com o funcionamento dos módulos do Azure IoT Edge, comece com os seguintes artigos:
- [Sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Módulos do Azure IoT Edge](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Fábrica conectada

A [Fábrica Conectada](../iot-accelerators/iot-accelerators-connected-factory-features.md) é uma implementação da arquitetura de referência de IoT industrial do Microsoft Azure que pode ser personalizada para atender a requisitos empresariais específicos. O código completo da solução é de software livre e está disponível no repositório GitHub do acelerador de solução da Fábrica Conectada. Você pode usá-lo como um ponto de partida para um produto comercializado e implantar uma solução pré-criada em sua assinatura do Azure em apenas alguns minutos. 

## <a name="factory-floor-connectivity"></a>Conectividade de chão de fábrica

O OPC Twin é um componente de IIoT que automatiza o registro e a descoberta de dispositivos e oferece controle remoto de dispositivos industriais por meio de APIs REST. O OPC Twin usa o Hub IoT e o Azure IoT Edge para conectar-se à nuvem e à rede de fábrica. O OPC Twin permite que os desenvolvedores de IIoT se concentrem na criação de aplicativos de IIoT sem se preocuparem com a maneira de acessar as máquinas locais de modo seguro.

## <a name="security"></a>Segurança

O OPC Vault é uma implementação do GDS (Servidor de Descoberta Global) da UA de OPC que pode configurar, registrar e gerenciar o ciclo de vida de certificado para o servidor da UA de OPC e aplicativos cliente na nuvem. O OPC Vault simplifica a implementação e a manutenção de conectividade segura de ativo no espaço de industrial. Ao automatizar o gerenciamento de certificados, o OPC Vault libera os operadores de fábrica dos processos manuais e complexos associados a ao gerenciamento de certificados e conectividade.

## <a name="next-steps"></a>Próximas etapas

Agora que você viu uma introdução à IoT industrial e seus componentes, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [O que é um OPC Twin](overview-opc-twin.md)
