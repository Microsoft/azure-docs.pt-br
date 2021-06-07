---
title: Gerenciamento de dispositivos IoT do Azure com a extensão de IoT para a CLI do Azure | Microsoft Docs
description: Use a ferramenta extensão de IoT para a CLI do Azure para o gerenciamento de dispositivos Hub IoT do Azure, que contam com métodos Diretos e as opções de gerenciamento de propriedades desejadas do Gêmeo.
author: chrissie926
manager: ''
keywords: gerenciamento de dispositivos iot do azure, gerenciamento de dispositivos hub iot do azure, iot de gerenciamento de dispositivos, gerenciamento de dispositivos hub iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 499c0c065cb38313c98ed9412ab1f08d70dbc654
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199519"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Use a extensão de IoT da CLI do Azure para gerenciamento de dispositivo Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) é uma extensão de IOT de software livre que adiciona aos recursos do [CLI do Azure](/cli/azure/overview). O CLI do Azure inclui comandos para interagir com os pontos de extremidade de Azure Resource Manager e de gerenciamento. Por exemplo, é possível usar a CLI do Azure para criar uma VM do Azure ou um Hub IoT. Uma extensão CLI permite que um serviço do Azure amplie a CLI do Azure, fornecendo acesso a recursos específicos de serviços adicionais. A extensão de IoT fornece aos desenvolvedores de IoT acesso de linha de comando a todos os recursos do Hub IoT, IoT Edge e do serviço de provisionamento de dispositivos no Hub IoT.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa  |
|----------------------------|-----------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Propriedades desejadas do gêmeo    | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Propriedades relatadas do gêmeo   | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Marcações do gêmeo                  | Armazene os metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de vendas.                         |
| Consultas de dispositivo gêmeo        | Consulte todos os dispositivos gêmeos para recuperar esses gêmeos com condições arbitrárias, como identificar os dispositivos que estão disponíveis para uso. |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que você aprenderá

Você aprende a usar a extensão de IoT para CLI do Azure com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute a CLI do Azure e a extensão do IoT para a CLI do Azure com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

* Conclua o tutorial do [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com Node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Esses itens abrangem os seguintes requisitos:

  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o hub IoT do Azure.

* Verifique se o dispositivo está sendo executado com o aplicativo cliente durante este tutorial.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

* A CLI do Azure. Se você precisar instalar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). No mínimo, a versão da CLI do Azure deve ser 2.0.70 ou superior. Use `az –version` para validar.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Instalar a extensão de IoT. A maneira mais simples é executar `az extension add --name azure-iot`. [O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) descreve várias maneiras de instalar a extensão.

## <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Faça logon em sua conta do Azure executando o comando a seguir:

```azurecli
az login
```

## <a name="direct-methods"></a>Métodos diretos

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propriedades desejadas do dispositivo gêmeo

Defina um intervalo de propriedade desejado = 3000 executando o seguinte comando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Essa propriedade pode ser lido do seu dispositivo.

## <a name="device-twin-reported-properties"></a>Propriedades relatadas do dispositivo gêmeo

Obtenha as propriedades relatadas do dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Uma das propriedades relatadas de entrelaçamento é $metadata. $lastUpdated, que mostra a última vez em que o aplicativo do dispositivo atualizou seu conjunto de propriedades relatadas.

## <a name="device-twin-tags"></a>Marcas do dispositivo gêmeo

Exiba as marcações e as propriedades do dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adicione uma função de campo = temperature&humidity ao dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Consultas de dispositivo gêmeo

Consulte dispositivos com uma marcação de função = 'temperature&humidity' executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos os dispositivos, exceto aqueles com uma marcação de função = 'temperature&humidity', executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]