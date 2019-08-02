---
title: Registrar um novo dispositivo na linha de comando – Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para a CLI do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bbb8dcb410f17ba894210c9c090ec0975f83c0b3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796134"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registrar um novo dispositivo Azure IoT Edge com a CLI do Azure

Antes de poder usar os dispositivos do IoT com Azure IoT Edge, será necessário registrá-los no hub IoT. Quando você registra um dispositivo, você receberá uma cadeia de caracteres de conexão que pode ser usada para configurar seu dispositivo para cargas de trabalho de IoT Edge.

A [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A nova extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e funcionalidade completa do IoT Edge.

Este artigo descreve como registrar um novo dispositivo IoT Edge usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Criar um dispositivo

Use o [identidade de dispositivo de hub de iot de az criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) comando para criar uma nova identidade do dispositivo no hub IoT. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: forneça um nome descritivo exclusivo para o hub IoT.

* **hub-name**: forneça o nome do hub IoT.

* **edge-enabled**: este parâmetro declara que o dispositivo é para uso com o IoT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Exibir todos os dispositivos

Use o [lista de identidade do dispositivo do az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) comando para exibir todos os dispositivos no hub IoT. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

## <a name="retrieve-the-connection-string"></a>Recuperar a cadeia de conexão

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o [az iot hub identidade do dispositivo show-conexão-cadeia de caracteres](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) comando para retornar a cadeia de caracteres de conexão para um único dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar módulos em um dispositivo com a CLI do Azure](how-to-deploy-modules-cli.md).
