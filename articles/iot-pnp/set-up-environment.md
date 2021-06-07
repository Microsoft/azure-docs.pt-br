---
title: Guia de Início Rápido – Configurar os recursos de IoT necessários para o IoT Plug and Play | Microsoft Docs
description: Guia de Início Rápido – Criar uma instância do Serviço de Provisionamento de Dispositivos e Hub IoT a ser usado com os guias de início rápido e tutoriais do IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5446f8481439dabffe67f9b00b5f65b1da495746
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742646"
---
# <a name="quickstart-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Início Rápido: Configurar o ambiente para os inícios rápidos e tutoriais do IoT Plug and Play

Para concluir qualquer um dos tutoriais e guias de início rápido do IoT Plug and Play, você precisa configurar um hub IoT e o DPS (Serviço de Provisionamento de Dispositivos) na assinatura do Azure. Você também precisará de cópias locais dos arquivos de modelo usados pelos aplicativos de exemplo e pela ferramenta Azure IoT Explorer.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Evitar a necessidade de instalar a CLI do Azure localmente. É possível usar o Azure Cloud Shell para configurar serviços de nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Criar os recursos

Criar um grupo de recursos do Azure para os recursos:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Crie um Hub IoT. O comando a seguir usa o nome `my-pnp-hub` como um exemplo para o nome do hub IoT a ser criado. Escolha um nome exclusivo para o hub IoT a ser usado no lugar de `my-pnp-hub`:

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Crie uma instância do DPS. O comando a seguir usa o nome `my-pnp-dps` como um exemplo para o nome da instância do DPS a ser criada. Escolha um nome exclusivo para a instância do DPS a ser usada no lugar de `my-pnp-dps`:

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Para vincular a instância do DPS ao hub IoT, use os comandos a seguir. Substitua `my-pnp-dps` e `my-pnp-hub` pelos nomes exclusivos que você escolheu anteriormente:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Recuperar as configurações

Alguns guias de início rápido e tutoriais usam a cadeia de conexão para o hub IoT. Você também precisa da cadeia de conexão ao configurar a ferramenta Azure IoT Explorer. Recupere a cadeia de conexão e anote-a agora. Substitua `my-pnp-hub` pelo nome exclusivo escolhido para o hub IoT:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

A maioria dos guias de início rápido e tutoriais usam o *escopo de ID* da sua configuração de DPS. Recupere o escopo da ID e anote-o agora. Substitua `my-pnp-dps` pelo nome exclusivo escolhido para a instância do DPS:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Todos os guias de início rápido e tutoriais usam um registro de dispositivo de DPS. Use o comando a seguir para criar um *registro de dispositivo individual* do `my-pnp-device` na instância do DPS. Substitua `my-pnp-dps` pelo nome exclusivo escolhido para a instância do DPS. Anote os valores de ID do registro e de chave primária a serem usados nos guias de início rápido e tutoriais:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

Crie quatro variáveis de ambiente para configurar os exemplos nos guias de início rápido e tutoriais para usar o DPS (Serviço de Provisionamento de Dispositivos) a fim de se conectar ao hub IoT:

* **IOTHUB_DEVICE_SECURITY_TYPE**: o valor `DPS`.
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: o escopo da ID do DPS anotada anteriormente.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: o valor `my-pnp-device`.
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: a chave primária de registro anotada anteriormente.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: o valor `global.azure-devices-provisioning.net`

Os exemplos de serviço precisam das seguintes variáveis de ambiente para identificar o hub e o dispositivo aos quais se conectar:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Por exemplo, em um shell Bash do Linux:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Por exemplo, em uma linha de comando do Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Baixar os arquivos de modelo

Os guias de início rápido e os tutoriais usam arquivos de modelo de exemplo para o controlador de temperatura e dispositivos de termostato. Para baixar os arquivos de modelo de exemplo:

1. Crie uma pasta chamada *models* no computador local.

1. Clique com o botão direito do mouse em [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) e salve o arquivo JSON na pasta *models*.

1. Clique com o botão direito do mouse em [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) e salve o arquivo JSON na pasta *models*.

## <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Os guias de início rápido e os tutoriais usam a ferramenta **Azure IoT Explorer**. Acesse as [versões do Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) e expanda a lista de ativos para a versão mais recente. Baixe e instale a versão mais recente do aplicativo para o seu sistema operacional.

Na primeira vez que você executar a ferramenta, você será solicitado a fornecer a cadeia de conexão do hub IoT. Use a cadeia de conexão anotada anteriormente.

Configure a ferramenta para usar os arquivos de modelo que você baixou anteriormente. Na home page da ferramenta, selecione **Configurações do IoT Plug and Play** e **+ Adicionar > Pasta local**. Selecione a pasta *modelos* que você criou anteriormente. Em seguida, selecione **Salvar** para salvar as configurações.

Para saber mais, confira [Instalar e usar o Azure IoT Explorer](howto-use-iot-explorer.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o hub IoT e a instância do DPS para todos os guias de início rápido e tutoriais do IoT Plug and Play, então você só precisa concluir as etapas neste artigo uma vez. Quando tiver terminado, você poderá removê-las da sua assinatura com o seguinte comando:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que configurou seu ambiente, você pode experimentar um dos guias de início rápido ou tutoriais, por exemplo:

> [!div class="nextstepaction"]
> [Conectar um aplicativo de exemplo de dispositivo do IoT Plug and Play ao Hub IoT](quickstart-connect-device.md)
