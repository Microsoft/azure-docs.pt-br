---
title: Provisionamento de atualização de dispositivo para o agente de Hub IoT do Azure | Microsoft Docs
description: Provisionamento de atualização de dispositivo para o agente de Hub IoT do Azure
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097747"
---
# <a name="device-update-agent"></a>Agente de Atualização de Dispositivo

A Atualização de dispositivo do Hub IoT é compatível com duas formas de atualizações: baseadas em imagem e em pacote. 

* As atualizações de imagem fornecem um nível mais alto de confiança no estado final do dispositivo. Normalmente, é mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, já que não são apresentados os mesmos desafios que os pacotes e suas dependências. Devido à natureza atômica, também é possível adotar facilmente um modelo de failover A/B. 
* As atualizações baseadas em pacote são atualizações de destino que alteram apenas um componente ou aplicativo específico no dispositivo. Portanto, levando ao menor consumo de largura de banda e ajuda a reduzir o tempo para baixar e instalar a atualização. As atualizações de pacote geralmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitam a sobrecarga de criação de imagens. 

Siga os links abaixo sobre como compilar, executar e modificar o agente de atualização de dispositivo.

## <a name="build-the-device-update-agent"></a>Compilar o agente de atualização de dispositivo

Siga as instruções para [criar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) o agente de atualização de dispositivo da origem.

## <a name="run-the-device-update-agent"></a>Executar o agente de atualização de dispositivo

Depois que o agente é criado com êxito, é hora de [executar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) o agente.

## <a name="modifying-the-device-update-agent"></a>Modificando o agente de atualização de dispositivo

Agora, faça as alterações necessárias para incorporar o agente em sua imagem.  Veja como [Modificar](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) o agente de atualização de dispositivo para obter diretrizes.

### <a name="troubleshooting-guide"></a>Guia de solução de problemas

Se você tiver problemas, examine o [Guia de solução de problemas](troubleshoot-device-update.md) de atualização do dispositivo para o Hub IOT para ajudar a desbloquear possíveis problemas e coletar informações necessárias para fornecer à Microsoft.

## <a name="next-steps"></a>Próximas etapas

Use as imagens e os binários pré-criados para uma demonstração fácil da atualização do dispositivo para o Hub IoT.  

[Atualização de imagem: Introdução com Raspberry Pi 3 B + referência de imagem Yocto](device-update-raspberry-pi.md)

[Atualização de imagem: Introdução usando o agente de referência de simulador Ubuntu (18, 4 x64)](device-update-simulator.md)

[Atualização do pacote: Introdução usando o agente de pacote do Ubuntu Server 18, 4 x64](device-update-ubuntu-agent.md)

