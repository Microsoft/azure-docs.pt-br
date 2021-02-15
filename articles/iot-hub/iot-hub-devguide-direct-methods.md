---
title: Entender os métodos diretos do Hub IoT do Azure | Microsoft Docs
description: Guia de desenvolvedor – use métodos diretos para invocar código em seus dispositivos de um aplicativo de serviço.
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147792"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Entender e chamar métodos diretos do Hub IoT

O Hub IoT oferece a capacidade de invocar métodos diretos em dispositivos a partir da nuvem. Os métodos diretos representam uma interação entre solicitação e resposta com um dispositivo semelhante a uma chamada HTTP, na qual eles são bem-sucedidos ou falham imediatamente (depois que o tempo limite especificado pelo usuário é atingido). Essa abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método de dispositivo tem como alvo um único dispositivo. [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) mostra como oferecer uma maneira de invocar métodos diretos em vários dispositivos e agendar invocação de método para dispositivos desconectados.

Qualquer pessoa com permissões de **conectar serviço** no Hub IoT pode invocar um método em um dispositivo.

Os métodos diretos seguem um padrão de solicitação e resposta e se destinam a comunicações que exigem confirmação imediata de seus resultados. Por exemplo, controle interativo do dispositivo, como ativar um ventilador.

Veja as [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) se está em dúvida entre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Os métodos diretos são implementados no dispositivo e podem precisar ou não de entradas no conteúdo do método para instanciar corretamente. Você invoca um método direto por meio de um URI voltado para serviços (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos por meio de um tópico MQTT específico do dispositivo (`$iothub/methods/POST/{method name}/`) ou de links do AMQP (as propriedades de aplicativo `IoThub-methodname` e `IoThub-status`).

> [!NOTE]
> Quando você invoca um método direto em um dispositivo, os valores e nomes de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto pelo seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Os métodos diretos são síncronos e são bem-sucedidos ou falham após o período de tempo limite (padrão: 30 segundos, configurável entre 5 e 300 segundos). Os métodos diretos são úteis em cenários interativos em que você deseja que um dispositivo atue somente, e somente se, o dispositivo estiver online e recebendo comandos. Por exemplo, ativar a luz de um telefone. Nesses cenários, você deseja ver uma falha ou êxito imediatamente, para que o serviço de nuvem possa atuar quanto ao resultado o mais rápido possível. O dispositivo pode retornar algum corpo de mensagem como resultado do método, mas não é obrigatório que o método faça isso. Não há nenhuma garantia quanto à ordenação ou semântica de simultaneidade nas chamadas de método.

Os métodos diretos são somente HTTPS do lado da nuvem e MQTT, AMQP, MQTT sobre WebSockets ou AMQP por WebSockets do lado do dispositivo.

A carga das solicitações e respostas do método é um documento JSON de até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto de um aplicativo back-end

Agora, invoque um método direto de um aplicativo de back-end.

### <a name="method-invocation"></a>Invocação de método

As invocações de método direto em um dispositivo são chamadas HTTPS, compostas pelos itens a seguir:

* O *URI de Solicitação* específico para o dispositivo em conjunto com a [versão da API](/rest/api/iothub/service/devices/invokemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* A *método* POST

* *Cabeçalhos* que contêm a autorização, a ID da solicitação, o tipo de conteúdo e a codificação de conteúdo.

* Um *corpo* JSON transparente no seguinte formato:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

O valor fornecido como `responseTimeoutInSeconds` na solicitação é a quantidade de tempo que o serviço do Hub IOT deve aguardar para a conclusão de uma execução de método direto em um dispositivo. Defina esse tempo limite como pelo menos, desde que o tempo de execução esperado de um método direto por um dispositivo. Se timeout não for fornecido, será usado o valor padrão de 30 segundos. Os valores mínimo e máximo para `responseTimeoutInSeconds` são 5 e 300 segundos, respectivamente.

O valor fornecido como `connectTimeoutInSeconds` na solicitação é a quantidade de tempo na invocação de um método direto que o serviço do Hub IOT deve aguardar para que um dispositivo desconectado fique online. O valor padrão é 0, o que significa que os dispositivos já devem estar online na invocação de um método direto. O valor máximo de `connectTimeoutInSeconds` é de 300 segundos.

#### <a name="example"></a>Exemplo

Este exemplo permitirá que você inicie com segurança uma solicitação para invocar um método direto em um dispositivo IoT registrado para um hub IoT do Azure.

Para começar, use a [extensão de IoT Microsoft Azure para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) para criar um SharedAccessSignature.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Em seguida, substitua o cabeçalho Authorization pelo SharedAccessSignature gerado recentemente, modifique os `iothubName` parâmetros, `deviceId` `methodName` e `payload` para corresponder à sua implementação no comando de exemplo `curl` abaixo.  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

Execute o comando Modified para invocar o método direto especificado. As solicitações bem-sucedidas retornarão um código de status HTTP 200.

> [!NOTE]
> O exemplo acima demonstra como invocar um método direto em um dispositivo.  Se você quiser invocar um método direto em um módulo IoT Edge, precisaria modificar a solicitação de URL, conforme mostrado abaixo:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Resposta

O aplicativo de back-end recebe uma resposta que é composta pelos itens a seguir:

* *Código de status http*:
  * 200 indica a execução bem-sucedida do método direto;
  * 404 indica que a ID do dispositivo é inválida ou que o dispositivo não estava online após a invocação de um método direto e para `connectTimeoutInSeconds` depois (use a mensagem de erro acompanhada para entender a causa raiz);
  * 504 indica o tempo limite do gateway causado pelo dispositivo não responder a uma chamada de método direta no `responseTimeoutInSeconds` .

* *Cabeçalhos* que contêm a ETag, a ID da solicitação, o tipo de conteúdo e a codificação de conteúdo.

* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` e `body` são fornecidos pelo dispositivo e usados para responder com o código de status e/ou descrição do dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos do Azure IoT Edge

A invocação de métodos diretos usando uma ID do módulo tem suporte no [SDK do C# do cliente do serviço de IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Para essa finalidade, use o `ServiceClient.InvokeDeviceMethodAsync()` método e passe em `deviceId` e `moduleId` como parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Tratar um método direto em um dispositivo

Vamos dar uma olhada em como lidar com um método direto em um dispositivo IoT.

### <a name="mqtt"></a>MQTT

A seção a seguir é para o protocolo MQTT.

#### <a name="method-invocation"></a>Invocação de método

Os dispositivos recebem solicitações de método direto sobre o tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. O número de assinaturas por dispositivo é limitado a 5. Portanto, é recomendável não assinar individualmente cada método direto. Em vez disso, considere a possibilidade de se inscrever em `$iothub/methods/POST/#` e, em seguida, filtre as mensagens entregues com base nos nomes de método desejados.

O corpo que o dispositivo recebe está no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

As solicitações de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia as respostas para `$iothub/methods/res/{status}/?$rid={request id}`, em que:

* A propriedade `status` é o status de execução fornecido pelo dispositivo da execução do método.

* A propriedade `$rid` é a ID de solicitação de invocação do método recebida do Hub IoT.

O corpo é definido pelo dispositivo e pode ter qualquer status.

### <a name="amqp"></a>AMQP

A seção a seguir é para o protocolo AMQP.

#### <a name="method-invocation"></a>Invocação de método

O dispositivo recebe solicitações de método direto criando um link de recebimento no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A mensagem do AMQP chega ao link de recebimento que representa a solicitação do método. Ele contém as seções a seguir:

* A propriedade de ID de correlação, que contém uma ID de solicitação que deve ser passada de volta com a resposta do método correspondente.

* Uma propriedade de aplicativo chamada `IoThub-methodname`, que contém o nome do método que está sendo invocado.

* O corpo da mensagem do AMQP que contém a carga do método como JSON.

#### <a name="response"></a>Resposta

O dispositivo cria um link de envio para retornar a resposta do método no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A resposta do método é retornada no link de envio e é estruturada da seguinte maneira:

* A propriedade de ID de correlação, que contém a ID da solicitação passada na mensagem de solicitação do método.

* Uma propriedade de aplicativo chamada `IoThub-status`, que contém o status do método fornecido pelo usuário.

* O corpo da mensagem do AMQP que contém a resposta do método como JSON.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas que se aplicam e o comportamento de limitação esperado ao usar o Hub IoT.

* [SDKs do dispositivo e do serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta do Hub IoT que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.

* O [suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT ao protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar métodos diretos, pode ser interessante ler o seguinte artigo do Guia do Desenvolvedor do Hub IoT:

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

* [Usar métodos diretos](quickstart-control-device-node.md)
* [Gerenciamento de dispositivos com as ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)