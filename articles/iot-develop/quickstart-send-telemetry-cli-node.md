---
title: Guia de início rápido Enviar telemetria do dispositivo para o Hub IoT do Azure (Node.js)
description: Neste guia de início rápido, use o SDK do Dispositivo do Hub IoT do Azure para Node.js para enviar telemetria de um dispositivo para um hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 3d42ac814678136c2f6342cd1064e3c3ff394507
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777231"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Guia de início rápido: Enviar telemetria de um dispositivo para um hub IoT (Node.js)

**Aplica-se a**: [desenvolvimento de aplicativos do dispositivo](about-iot-develop.md#device-application-development)

Neste guia de início rápido, aprenda um fluxo de trabalho básico de desenvolvimento de aplicativos do dispositivo IoT. Use a CLI do Azure para criar um hub IoT do Azure e um dispositivo simulado e use o SDK do Node.js de IoT do Azure para acessar o dispositivo e enviar telemetria para o hub.

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Você pode executar todos os comandos neste guia de início rápido usando o Azure Cloud Shell, um shell da CLI interativa que é executado em seu navegador. Se você usar o Cloud Shell, não precisará instalar nada. Se você preferir usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.76 ou posterior. Execute az --version para localizar a versão. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se você estiver usando o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente do Node.js.

    Verifique a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Usar o SDK do Node.js para enviar mensagens
Nesta seção, você usará o SDK do Node.js para enviar mensagens do seu dispositivo simulado para o hub IoT. 

1. Abra uma nova janela de terminal. Você usará esse terminal para instalar o SDK do Node.js e trabalhar com o código de exemplo do Node.js. Agora você deve ter dois terminais abertos: aquele que você acabou de abrir para trabalhar com o Node.js e o shell da CLI que você usou nas seções anteriores para inserir os comandos da CLI do Azure.

1. Copie os [exemplos de dispositivo do SDK do Node.js de IoT do Azure](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) para o computador local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Acesse o diretório *azure-iot-sdk-node/device/samples/pnp*:

    ```console
    cd azure-iot-sdk-node/device/samples/pnp
    ```

1. Instale o SDK do Node.js de IoT do Azure e as dependências necessárias:

    ```console
    npm install
    ```

    Esse comando instala as dependências apropriadas conforme especificado no arquivo *package.json* no diretório de exemplos do dispositivo.

1. Defina ambas as variáveis de ambiente a seguir para habilitar o dispositivo simulado a se conectar à IoT do Azure.
    * Defina uma variável de ambiente chamada `IOTHUB_DEVICE_CONNECTION_STRING`. Para o valor da variável, use a cadeia de conexão do dispositivo salva na seção anterior.
    * Defina uma variável de ambiente chamada `IOTHUB_DEVICE_SECURITY_TYPE`. Para a variável, use o valor da cadeia de caracteres literal `connectionString`.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Para o Windows CMD, não há aspas em torno dos valores de cadeia de caracteres para cada variável.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux ou Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. No shell da CLI aberta, execute o comando [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) para iniciar o monitoramento de eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegarem.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No terminal do Node.js, execute o código do arquivo de exemplo instalado *simple_thermostat.js*. Esse código acessa o dispositivo IoT simulado e envia uma mensagem ao hub IoT.

    Para executar o exemplo do Node.js no terminal:
    ```console
    node ./simple_thermostat.js
    ```
    > [!NOTE]
    > Este exemplo de código usa o Azure IoT Plug and Play, que permite integrar dispositivos inteligentes às suas soluções sem nenhuma configuração manual.  Por padrão, a maioria dos exemplos desta documentação usa o IoT Plug and Play. Para saber mais sobre as vantagens do IoT PnP e os casos para usá-lo ou não usá-lo, confira [O que é o IoT Plug and Play?](../iot-pnp/overview-iot-plug-and-play.md)

Como o código Node.js envia uma mensagem de telemetria simulada do seu dispositivo para o hub IoT, a mensagem é exibida no shell da CLI que está monitorando os eventos:

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device ID>
  payload:
    temperature: 36.87027777131555
```

Agora, o seu dispositivo está conectado com segurança e está enviando telemetria ao Hub IoT do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos recursos do Azure criados neste guia de início rápido, poderá usar a CLI do Azure para excluí-los.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

Para excluir um grupo de recursos por nome:
1. Execute o comando [az group delete](/cli/azure/group#az_group_delete). Esse comando removerá o grupo de recursos, o Hub IoT e o registro de dispositivo que você criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Execute o comando [az group list](/cli/azure/group#az_group_list) para confirmar se o grupo de recursos foi excluído.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu um fluxo de trabalho básico do aplicativo de IoT do Azure para conectar com segurança um dispositivo à nuvem e enviar telemetria do dispositivo para nuvem. Você usou a CLI do Azure para criar um hub IoT e um dispositivo simulado e usou o SDK do Node.js de IoT do Azure para acessar o dispositivo e enviar telemetria para o hub. 

Como próxima etapa, explore o SDK do Node.js de IoT do Azure por meio de exemplos de aplicativo.

- [Mais Exemplos de Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): esse diretório contém mais exemplos do repositório do SDK do Node.js para demonstrar cenários do Hub IoT.