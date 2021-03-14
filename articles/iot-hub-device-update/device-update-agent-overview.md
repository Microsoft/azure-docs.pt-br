---
title: Entender a atualização do dispositivo para o agente de Hub IoT do Azure | Microsoft Docs
description: Entenda a atualização do dispositivo para o agente do Hub IoT do Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e932238849baf267983fb3ca1ebb082db169d9fd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678989"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Visão geral da atualização do dispositivo para agente do Hub IoT

O agente de atualização de dispositivo consiste em duas camadas conceituais:

* A camada de interface baseia-se na [plug and Play de IOT do Azure (PnP)](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) , permitindo que o sistema de mensagens flua entre o agente de atualização de dispositivo e os serviços de atualização de dispositivo.
* A camada de plataforma é responsável pelas ações de atualização de alto nível de download, instalação e aplicação que podem ser plataforma ou dispositivo específico.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implementações de agente." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>A camada de interface

A camada de interface é composta pela [interface do Adu Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e pela [interface de informações do dispositivo](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Essas interfaces dependem de um arquivo de configuração para valores padrão. Os valores padrão incluem aduc_manufacturer e aduc_model para a interface AzureDeviceUpdateCore e o modelo e o fabricante da interface DeviceInformation. [Saiba mais](device-update-configuration-file.md) o arquivo de configuração.

### <a name="adu-core-interface"></a>Interface principal do ADU

A interface "ADU Core" é o canal de comunicação principal entre o agente de atualização de dispositivo e os serviços. [Saiba mais](device-update-plug-and-play.md#adu-core-interface) sobre esta interface.

### <a name="device-information-interface"></a>Interface de informações do dispositivo

A interface de informações do dispositivo é usada para implementar a `Azure IoT PnP DeviceInformation` interface. [Saiba mais](device-update-plug-and-play.md#device-information-interface) sobre esta interface.

## <a name="the-platform-layer"></a>A camada da plataforma

Há duas implementações da camada da plataforma. A camada da plataforma do simulador tem uma implementação trivial das ações de atualização e é usada para testar e avaliar rapidamente a atualização do dispositivo para serviços do Hub IoT e a instalação. Quando o agente de atualização de dispositivo é criado com a camada de plataforma do simulador, nos referimos a ele como o agente do simulador de atualização de dispositivo ou apenas o simulador. [Saiba mais](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sobre como usar o agente de simulador. A camada da plataforma Linux integra-se à [otimização de entrega](https://github.com/microsoft/do-client) para downloads e é usada em nossa imagem de referência do Raspberry Pi e todos os clientes que são executados em sistemas Linux.

### <a name="simulator-platform-layer"></a>Camada da plataforma do simulador

A implementação da camada da plataforma do simulador pode ser encontrada no `src/platform_layers/simulator_platform_layer` e pode ser usada para testar e avaliar a atualização do dispositivo para o Hub IOT.  Muitas das ações na implementação do "simulador" são simuladas para reduzir as alterações físicas para experimentar a atualização do dispositivo para o Hub IoT.  Uma atualização "simulada" de ponta a ponta pode ser executada usando essa camada de plataforma. [Saiba mais](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sobre como executar o agente simulador.

### <a name="linux-platform-layer"></a>Camada da plataforma Linux

A implementação da camada da plataforma Linux pode ser encontrada no `src/platform_layers/linux_platform_layer` e se integra com o [cliente de otimização de entrega](https://github.com/microsoft/do-client/releases) para downloads e é usada em nossa imagem de referência do Raspberry Pi e todos os clientes que são executados em sistemas Linux.

Essa camada pode ser integrada a diferentes manipuladores de atualização para implementar o instalador. Por exemplo, o `SWUpdate` manipulador de atualização invoca um script de Shell para chamar o `SWUpdate` executável para executar uma atualização.

## <a name="update-handlers"></a>Atualizar manipuladores

Manipuladores de atualização são componentes que lidam com conteúdo ou partes específicas do instalador da atualização. As implementações do manipulador de atualização estão em `src/content_handlers` .

### <a name="simulator-update-handler"></a>Manipulador de atualização do simulador

O manipulador de atualização do simulador é usado pela camada da plataforma do simulador e pode ser usado com a camada da plataforma Linux para simular interações com um manipulador de conteúdo. O manipulador de atualização do simulador implementa as APIs do manipulador de atualização com praticamente nenhuma Ops. A implementação do manipulador de atualização do simulador pode ser encontrada abaixo:
* [Simulador de atualização de imagem](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulador de atualização de pacote de apt](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Observação: o campo InstalledCriteria na interface PnP AzureDeviceUpdateCore deve ser o hash SHA256 do conteúdo. Esse é o mesmo hash presente no [objeto de manifesto de importação](import-update.md#create-device-update-import-manifest). [Saiba mais](device-update-plug-and-play.md) sobre `installedCriteria` o e a `AzureDeviceUpdateCore` interface.

### <a name="swupdate-update-handler"></a>`SWUpdate` Manipulador de atualização

O `SWUpdate` manipulador de atualização "se integra ao `SWUpdate` executável da linha de comando e a outros comandos do Shell para implementar atualizações a/B especificamente para a imagem de referência do Raspberry Pi. Localize a imagem de referência mais recente do Raspberry Pi [aqui](https://github.com/Azure/iot-hub-device-update/releases). O `SWUpdate` manipulador de atualização é implementado em [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Manipulador de atualização APT

O manipulador de atualização APT processa um manifesto de atualização específico da APT e invoca a APT para instalar ou atualizar os pacotes Debian especificados.

## <a name="self-update-device-update-agent"></a>Agente de atualização de dispositivo de atualização automática

O agente de atualização de dispositivo e suas dependências podem ser atualizados por meio da atualização do dispositivo para o pipeline do Hub IoT. Se você estiver usando uma atualização baseada em imagem, inclua o agente de atualização de dispositivo mais recente em sua nova imagem. Se você estiver usando uma atualização baseada em pacote, inclua o agente de atualização de dispositivo e sua versão desejada no manifesto apt como qualquer outro pacote. [Saiba mais](device-update-apt-manifest.md) sobre o manifesto apt. Você pode verificar a versão instalada do agente de atualização de dispositivo e o agente de otimização de entrega na seção Propriedades do dispositivo do seu [dispositivo IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins). [Saiba mais sobre as propriedades do dispositivo na interface do Adu Core](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Próximas etapas
[Entender o arquivo de configuração do agente de atualização de dispositivo](device-update-configuration-file.md)

