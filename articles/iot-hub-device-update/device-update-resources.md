---
title: Entender a atualização do dispositivo para recursos do Hub IoT do Azure | Microsoft Docs
description: Entender a atualização do dispositivo para recursos do Hub IoT do Azure
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661929"
---
# <a name="device-update-resources"></a>Recursos de atualização de dispositivo

Para usar a atualização de dispositivo para o Hub IoT, você precisa criar uma conta de atualização de dispositivo e um recurso de instância. 

## <a name="device-update-account"></a>Conta de atualização do dispositivo

Uma conta de atualização de dispositivo é um recurso que é criado dentro de sua assinatura do Azure. No nível da conta de atualização do dispositivo, você pode selecionar a região em que a conta de atualização do dispositivo será criada. Você também pode definir permissões para autorizar usuários que terão acesso à atualização do dispositivo.


## <a name="device-update-instance"></a>Instância de atualização de dispositivo
Depois que uma conta tiver sido criada, você precisará criar uma instância de atualização de dispositivo. Uma instância é um contêiner lógico que contém atualizações e implantações associadas a um hub IoT específico. A atualização de dispositivo usa o Hub IoT como um diretório de dispositivo e um canal de comunicação com dispositivos. 

Durante a visualização pública, duas contas de atualização de dispositivo podem ser criadas por assinatura. Além disso, duas instâncias de atualização de dispositivo podem ser criadas por conta.

## <a name="configuring-device-update-linked-iot-hub"></a>Configurando o Hub IoT vinculado de atualização de dispositivo 

Para que a atualização do dispositivo receba notificações de alteração do Hub IoT, a atualização do dispositivo integra-se ao Hub de eventos "interno". Clicar no botão "configurar o Hub IoT" dentro de sua instância configura as rotas de mensagens necessárias e a política de acesso necessária para se comunicar com os dispositivos IoT. 

As rotas de mensagens a seguir são configuradas para a atualização do dispositivo:

|   Nome da rota    | Consulta de roteamento  | Descrição  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Escuta eventos de alterações de entrelaçamento digital  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | Escuta dispositivos que foram excluídos |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-ID = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Escuta novos tipos de dispositivos |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' ou opType = ' replaceTwin') E IS_DEFINED ($body. Tags. ADUGroup) | Escuta novos grupos de atualização de dispositivo |

## <a name="next-steps"></a>Próximas etapas

[Criar recursos de atualização de dispositivo](./create-device-update-account.md)
