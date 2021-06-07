---
title: Tutorial – Verificar conectividade de dispositivo com o Hub IoT do Azure
description: Tutorial – Use ferramentas do Hub IoT para solucionar problemas de conectividade do dispositivo durante o desenvolvimento no hub IoT.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: 70ad8f9a910fc66a5e22231d5130cfa8a22db0c6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066993"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Tutorial: Usar um dispositivo simulado para testar a conectividade com o hub IoT

Neste tutorial, você usa ferramentas do portal do Hub IoT e comandos da CLI do Azure para testar conectividade de dispositivo. Este tutorial também usa um simulador de dispositivo simples que você executa no computador desktop.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Verificar autenticação de dispositivo
> * Verificar conectividade de dispositivo para nuvem
> * Verificar conectividade de nuvem para dispositivo
> * Verificar sincronização de dispositivo gêmeo

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

O aplicativo do simulador de dispositivos executado neste tutorial é gravado usando o Node.js. Você precisa do Node.js v10.x.x ou posterior em seu computador de desenvolvimento.

Você pode fazer o download do Node.js para várias plataformas a partir do [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

Baixe o projeto Node.js do simulador de dispositivo de exemplo de https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip extraia o arquivo ZIP.

Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste tutorial usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se você criou um Hub IoT de camada Standard ou gratuita em um início rápido ou tutorial anterior, poderá ignorar esta etapa.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Verificar autenticação de dispositivo

Um dispositivo deve autenticar-se com o hub antes que ele possa trocar dados com o hub. Você pode usar a ferramenta de **Dispositivos IoT** na seção **Gerenciamento de Dispositivos** do portal para gerenciar os dispositivos e verificar as chaves de autenticação que estão utilizando. Nesta seção do tutorial, você adiciona um novo dispositivo de teste, recupera a chave e verifica se o dispositivo de teste pode conectar-se ao hub. Posteriormente, você redefinirá a chave de autenticação para observar o que acontece quando um dispositivo tenta usar uma chave desatualizada. Esta seção do tutorial usa o portal do Azure para criar, gerenciar e monitorar um dispositivo e o simulador de dispositivo Node.js de exemplo.

Entre no portal e navegue até o Hub IoT. Em seguida, navegue até a ferramenta de **Dispositivos IoT**:

:::image type="content" source="media/tutorial-connectivity/iot-devices-tool.png" alt-text="Ferramenta de Dispositivos IoT":::

Para registrar um novo dispositivo, clique em **+ Novo**, defina **ID do Dispositivo** como **MyTestDevice** e clique em **Salvar**.

:::image type="content" source="media/tutorial-connectivity/add-device.png" alt-text="Adicionar novo dispositivo":::

Para recuperar a cadeia de conexão para **MyTestDevice**, clique nela na lista de dispositivos e copie o valor da **Cadeia de conexão primária**. A cadeia de conexão inclui a *chave de acesso compartilhado* do dispositivo.

:::image type="content" source="media/tutorial-connectivity/copy-connection-string.png" alt-text="Recuperar cadeia de conexão do dispositivo}":::

Para simular **MyTestDevice** enviando telemetria para o Hub IoT, execute o aplicativo de dispositivo simulado Node.js que você baixou anteriormente.

Em uma janela de terminal no computador de desenvolvimento, navegue até a pasta raiz do projeto Node.js de exemplo que você baixou. Em seguida, navegue até a pasta **iot-hub\Tutorials\ConnectivityTests**.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado. Use a cadeia de conexão do dispositivo que você anotou quando adicionou o dispositivo no portal.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

A janela do terminal exibe informações enquanto tenta conectar o hub:

![Conexão de dispositivo simulado](media/tutorial-connectivity/sim-1-connected.png)

Agora, você autenticou com êxito a partir de um dispositivo usando uma chave de dispositivo gerada pelo Hub IoT.

### <a name="reset-keys"></a>Redefinir chaves

Nesta seção, você redefine a chave do dispositivo e observa o erro quando o dispositivo simulado tenta conectar-se.

Para redefinir a chave do dispositivo primário para **MyTestDevice**, execute os comandos a segur:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-iot

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Na janela do terminal no computador de desenvolvimento, execute o aplicativo de dispositivo simulado novamente:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Desta vez, você verá um erro de autenticação quando o aplicativo tentar conectar-se:

![Erro de conexão](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Gerar token SAS (assinatura de acesso compartilhado)

Se o dispositivo usar um dos SDKs do dispositivo do Hub IoT, o código da biblioteca do SDK gerará o token SAS utilizado para autenticar com o hub. Um token SAS é gerado a partir do nome do hub, do nome do dispositivo e da chave do dispositivo.

Em alguns cenários, como em um gateway de protocolo de nuvem ou como parte de um esquema de autenticação personalizado, você poderá precisar gerar o token SAS por conta própria. Para solucionar problemas com o código de geração do SAS, é útil gerar um token SAS válido para utilizar durante o teste.

> [!NOTE]
> O exemplo de SimulatedDevice-2.js inclui exemplos de geração de um token SAS com e sem o SDK.

Para gerar um token SAS válido usando a CLI, execute o comando a seguir:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Anote o texto completo do token SAS gerado. Um token SAS é semelhante ao seguinte: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

Em uma janela de terminal no computador de desenvolvimento, navegue até a pasta raiz do projeto Node.js de exemplo que você baixou. Em seguida, navegue até a pasta **iot-hub\Tutorials\ConnectivityTests**.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

A janela do terminal exibe informações enquanto tenta conectar o hub usando o token SAS:

![Dispositivo simulado conectando com token](media/tutorial-connectivity/sim-2-connected.png)

Agora, você autenticou com êxito de um dispositivo usando um token SAS de teste gerado por um comando da CLI. O arquivo **SimulatedDevice-2.js** inclui código de exemplo que mostra como gerar um token SAS no código.

### <a name="protocols"></a>Protocolos

Um dispositivo pode usar qualquer um dos protocolos a seguir para conectar o Hub IoT:

| Protocolo | Porta de saída |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTPS |443 |

Se a porta de saída estiver bloqueada por um firewall, o dispositivo não poderá conectar:

![Porta bloqueada](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Verificar conectividade de dispositivo para nuvem

Depois que um dispositivo conectar, normalmente ele tentará enviar a telemetria para o Hub IoT. Esta seção mostra como é possível verificar se a telemetria enviada pelo dispositivo alcança o hub.

Primeiro, recupere a cadeia de conexão atual para o dispositivo simulado usando o comando a seguir:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Para executar um dispositivo simulado que envia mensagens, navegue até a pasta **iot-hub\Tutorials\ConnectivityTests** no código que você baixou.

Na janela de terminal, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

A janela do terminal exibe informações conforme envia a telemetria para o hub:

![Dispositivo simulado enviando mensagens](media/tutorial-connectivity/sim-3-sending.png)

Você pode usar **Métricas** no portal para verificar se as mensagens de telemetria estão alcançando o hub IoT. Selecione o Hub IoT na lista suspensa **Recurso**, selecione **Mensagens de telemetria enviadas** omo a métrica e defina o intervalo de tempo para **Última hora**. O gráfico mostra a contagem agregada de mensagens enviadas pelo dispositivo simulado:

![Mostrar métricas do Hub IoT](media/tutorial-connectivity/metrics-portal.png)

Demora alguns minutos para que as métricas sejam disponibilizadas após o início do dispositivo simulado.

## <a name="check-cloud-to-device-connectivity"></a>Verificar conectividade de nuvem para dispositivo

Esta seção mostra como é possível fazer uma chamada de método direto de teste a um dispositivo para verificar a conectividade de nuvem para dispositivo. Você executa um dispositivo simulado no computador de desenvolvimento para ouvir chamadas de método direto do hub.

Em uma janela de terminal, use o comando a seguir para executar o aplicativo de dispositivo simulado:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Use um comando da CLI para chamar um método direto no dispositivo:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

O dispositivo simulado imprime uma mensagem para o console quando recebe uma chamada de método direto:

![Dispositivo simulado recebe chamada de método direto](media/tutorial-connectivity/receive-method-call.png)

Quando o dispositivo simulado recebe com êxito a chamada de método direto, ele envia uma confirmação de volta ao hub:

![Receber confirmação de método direto](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Verificar a sincronização gêmea

Os dispositivos usam gêmeos para sincronizar o estado entre o dispositivo e o hub. Nesta seção, você usa os comandos da CLI para enviar _propriedades desejadas_  a um dispositivo e ler as _propriedades relatadas_ enviadas pelo dispositivo.

O dispositivo simulado usado nesta seção envia as propriedades relatadas ao hub sempre que ele é inicializado e imprime as propriedades desejadas no console sempre que elas são recebidas.

Em uma janela de terminal, use o comando a seguir para executar o aplicativo de dispositivo simulado:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Para verificar se o hub recebeu as propriedades relatadas do dispositivo, use o comando da CLI a seguir:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Na saída do comando, é possível ver a propriedade **devicelaststarted** na seção de propriedades relatadas. Esta propriedade mostra a data e a hora em que você iniciou o dispositivo simulado pela última vez.

![Exibir propriedades relatadas](media/tutorial-connectivity/reported-properties.png)

Para verificar se o hub pode enviar valores de propriedade desejadas ao dispositivo, use o comando da CLI a seguir:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

O dispositivo simulado imprime uma mensagem quando recebe uma atualização de propriedade desejada do hub:

![Receber propriedades desejadas](media/tutorial-connectivity/desired-properties.png)

Além de receber alterações de propriedades desejadas conforme elas são feitas, o dispositivo simulado verifica automaticamente as propriedades desejadas quando é inicializado.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do hub IoT, exclua-o junto com o grupo de recursos no portal. Para fazer isso, selecione o grupo de recursos **tutorials-iot-hub-rg** que contém o Hub IoT e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como verificar as chaves do dispositivo, verificar conectividade de dispositivo para nuvem, verificar conectividade de nuvem para dispositivo e verificar a sincronização de dispositivo gêmeo. Para saber mais sobre como monitorar o Hub IoT, visite o artigo sobre como fazer monitoramento do Hub IoT.

> [!div class="nextstepaction"]
> [Monitorar o Hub IoT](monitor-iot-hub.md)
