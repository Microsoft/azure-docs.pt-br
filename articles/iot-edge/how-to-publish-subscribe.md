---
title: Publicar e assinar com Azure IoT Edge | Microsoft Docs
description: Use IoT Edge agente MQTT para publicar e assinar mensagens
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 13bfd7c602389ff286a80f625829da5924a73bdf
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621888"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publicar e assinar com Azure IoT Edge

Você pode usar Azure IoT Edge agente MQTT para publicar e assinar mensagens. Este artigo mostra como se conectar a esse agente, publicar e assinar mensagens em tópicos definidos pelo usuário e usar primitivos de mensagens do Hub IoT. O agente do MQTT IoT Edge é interno do hub de IoT Edge. Para obter mais informações, consulte [os recursos de agente do hub de IOT Edge](iot-edge-runtime.md).

> [!NOTE]
> IoT Edge agente MQTT está atualmente em visualização pública.

## <a name="pre-requisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura válida
- [CLI do Azure](/cli/azure/) com a `azure-iot` extensão da CLI instalada. Para obter mais informações, consulte [as etapas de instalação da extensão do Azure IOT para o azure CLI do Azure](/cli/azure/azure-cli-reference-for-iot).
- Um **Hub IOT** do SKU, F1, S1, S2 ou S3.
- Ter um **dispositivo IOT Edge com a versão 1,2 ou superior**. Como IoT Edge agente MQTT está atualmente em visualização pública, defina as seguintes variáveis de ambiente como true no contêiner edgeHub para habilitar o agente MQTT:

   | Nome | Valor |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Clientes do mosquitto** instalados no dispositivo IOT Edge. Este artigo usa os clientes populares do mosquitto [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) e [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Outros clientes do MQTT podem ser usados em vez disso. Para instalar os clientes do mosquitto em um dispositivo Ubuntu, execute o seguinte comando:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Não instale o servidor mosquitto, pois ele pode causar o bloqueio das portas MQTT (8883 e 1883) e entrar em conflito com o Hub de IoT Edge.

## <a name="connect-to-iot-edge-hub"></a>Conectar-se ao Hub de IoT Edge

Conectar-se ao IoT Edge Hub segue as mesmas etapas, conforme descrito no artigo [conectando-se ao Hub IOT com um cliente MQTT genérico](../iot-hub/iot-hub-mqtt-support.md) ou na [Descrição conceitual do tópico IOT Edge Hub](iot-edge-runtime.md). Estas etapas são:

1. Opcionalmente, o cliente MQTT estabelece uma *conexão segura* com o hub de IOT Edge usando segurança de camada de transporte (TLS)
2. O cliente MQTT se *autentica* no Hub IOT Edge
3. O Hub de IoT Edge *autoriza* o cliente MQTT de acordo com sua política de autorização

### <a name="secure-connection-tls"></a>Conexão segura (TLS)

O protocolo TLS é usado para estabelecer uma comunicação criptografada entre o cliente e o Hub de IoT Edge.

Para desabilitar o TLS, use a porta 1883 (MQTT) e associe o contêiner edgeHub à porta 1883.

Para habilitar o TLS, se um cliente se conectar na porta 8883 (MQTTS) ao agente do MQTT, um canal TLS será iniciado. O agente envia sua cadeia de certificados que o cliente precisa para validar. Para validar a cadeia de certificados, o certificado raiz do agente MQTT deve ser instalado como um certificado confiável no cliente. Se o certificado raiz não for confiável, a biblioteca de cliente será rejeitada pelo agente do MQTT com um erro de verificação de certificado. As etapas a serem seguidas para instalar esse certificado raiz do agente no cliente são as mesmas do caso do [gateway transparente](how-to-create-transparent-gateway.md) e são descritas na documentação [preparar um dispositivo downstream](how-to-connect-downstream-device.md#prepare-a-downstream-device) .

### <a name="authentication"></a>Autenticação

Para que um cliente MQTT se autentique, ele primeiro precisa enviar um pacote CONNECT para o agente MQTT para iniciar uma conexão em seu nome. Esse pacote fornece três partes de informações de autenticação: a `client identifier` , a `username` e a `password` :

- O `client identifier` campo é o nome do dispositivo ou nome do módulo no Hub IOT. Ela usa a seguinte sintaxe:

  - Para um dispositivo: `<device_name>`

  - Para um módulo: `<device_name>/<module_name>`

   Para se conectar ao agente do MQTT, um dispositivo ou um módulo deve ser registrado no Hub IoT.

   O agente não permitirá conexões de vários clientes usando as mesmas credenciais. O agente desconectará o cliente já conectado se um segundo cliente se conectar usando as mesmas credenciais.

- O `username` campo é derivado do nome do dispositivo ou do módulo e o nome do IoTHub ao qual o dispositivo pertence usando a seguinte sintaxe:

  - Para um dispositivo: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Para um módulo: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- O `password` campo do pacote Connect depende do modo de autenticação:

  - Ao usar a [autenticação de chaves simétricas](how-to-authenticate-downstream-device.md#symmetric-key-authentication), o `password` campo é um token SAS.
  - Ao usar a [autenticação autoassinada X. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), o `password` campo não está presente. Nesse modo de autenticação, é necessário um canal TLS. O cliente precisa se conectar à porta 8883 para estabelecer uma conexão TLS. Durante o handshake de TLS, o agente MQTT solicita um certificado de cliente. Esse certificado é usado para verificar a identidade do cliente e, portanto, o `password` campo não será necessário mais tarde, quando o pacote de conexão for enviado. O envio de um certificado de cliente e o campo de senha resultará em um erro e a conexão será fechada. Bibliotecas do MQTT e bibliotecas de cliente TLS geralmente têm uma maneira de enviar um certificado de cliente ao iniciar uma conexão. Você pode ver um exemplo passo a passo na seção usando o [certificado X509 para autenticação de cliente](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

Os módulos implantados por IoT Edge usam a [autenticação de chaves simétricas](how-to-authenticate-downstream-device.md#symmetric-key-authentication) e podem chamar a [API de carga de trabalho IOT Edge](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) local para obter um token SAS, mesmo quando estiver offline.

### <a name="authorization"></a>Autorização

Depois que um cliente MQTT é autenticado para IoT Edge Hub, ele precisa estar autorizado a se conectar. Uma vez conectado, ele precisa estar autorizado a publicar ou assinar tópicos específicos. Essas autorizações são concedidas pelo hub de IoT Edge com base em sua política de autorização. A política de autorização é um conjunto de instruções expressas como uma estrutura JSON que é enviada para o Hub de IoT Edge por meio de seu entrelaçamento. Edite um hub de IoT Edge "e" para configurar sua política de autorização.

> [!NOTE]
> Para a visualização pública, a edição de políticas de autorização do agente do MQTT só está disponível por meio do Visual Studio, Visual Studio Code ou CLI do Azure. O portal do Azure atualmente não dá suporte à edição do hub de IoT Edge e sua política de autorização.

Cada instrução de política de autorização consiste na combinação de `identities` , `allow` ou `deny` efeitos, `operations` e `resources` :

- `identities` Descreva o assunto da política. Ele deve mapear para o `client identifier` enviado por clientes em seu pacote do Connect.
- `allow` ou os `deny` efeitos definem se as operações devem ser permitidas ou negadas.
- `operations` Defina as ações a serem autorizadas. `mqtt:connect``mqtt:publish`e `mqtt:subscribe` as três ações com suporte hoje.
- `resources` Defina o objeto da política. Pode ser um tópico ou um padrão de tópico definido com [curingas MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Abaixo está um exemplo de uma política de autorização que não permite explicitamente que o cliente "rogue_client" se conecte, permite que os clientes IoT do Azure se conectem e permite que "sensor_1" publique no tópico `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Algumas coisas para ter em mente ao escrever sua política de autorização:

- Requer o `$edgeHub` esquema de entrelaçamento versão 1,2
- Por padrão, todas as operações são negadas.
- As instruções de autorização são avaliadas na ordem em que aparecem na definição de JSON. Ele começa examinando `identities` e, em seguida, selecionando as primeiras instruções Allow ou Deny que correspondem à solicitação. Em caso de conflitos entre instruções allow e Deny, a instrução DENY vence.
- Várias variáveis (por exemplo, substituições) podem ser usadas na política de autorização:
    - `{{iot:identity}}` representa a identidade do cliente conectado no momento. Por exemplo, uma identidade de dispositivo como `myDevice` ou uma identidade de módulo como `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` representa a identidade do dispositivo conectado no momento. Por exemplo, uma identidade de dispositivo como `myDevice` ou a identidade do dispositivo onde um módulo está sendo executado `myEdgeDevice` .
    - `{{iot:module_id}}` representa a identidade do módulo conectado no momento. Essa variável está em branco para dispositivos conectados ou uma identidade de módulo como `SampleModule` .
    - `{{iot:this_device_id}}` representa a identidade do dispositivo IoT Edge que executa a política de autorização. Por exemplo, `myIoTEdgeDevice`.

Autorizações para tópicos do Hub IoT são tratadas de forma ligeiramente diferente do que os tópicos definidos pelo usuário. Aqui estão os pontos principais a serem lembrados:

- Os módulos ou dispositivos IoT do Azure precisam de uma regra de autorização explícita para se conectarem a IoT Edge o agente MQTT do Hub. Uma política de autorização de conexão padrão é fornecida abaixo.
- Os módulos ou dispositivos IoT do Azure podem acessar seus próprios tópicos do Hub IoT por padrão sem nenhuma regra de autorização explícita. No entanto, as autorizações são originadas de relações pai/filho nesse caso e essas relações devem ser definidas. Os módulos IoT Edge são automaticamente definidos como filhos de seu dispositivo IoT Edge, mas os dispositivos precisam ser definidos explicitamente como filhos de seu gateway IoT Edge.

Aqui está uma política de autorização padrão que pode ser usada para habilitar todos os dispositivos ou módulos de IoT do Azure para **se conectar** ao agente:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Agora que você entende como se conectar ao agente do IoT Edge MQTT, vamos ver como ele pode ser usado para publicar e assinar mensagens primeiro em tópicos definidos pelo usuário, em seguida, nos tópicos do Hub IoT e, finalmente, para outro agente do MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publicar e assinar tópicos definidos pelo usuário

Neste artigo, você usará um cliente chamado **sub_client** que assina um tópico e outro cliente chamado **pub_client** que é publicado em um tópico. Usaremos a autenticação de [chave simétrica](how-to-authenticate-downstream-device.md#symmetric-key-authentication) , mas o mesmo pode ser feito com a autenticação [autoassinada x. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) ou a [autenticação assinada por AC x. 509](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Criar clientes do Publicador e do assinante

Crie dois dispositivos IoT no Hub IoT e obtenha suas senhas. Usando o CLI do Azure do seu terminal para:

1. Crie dois dispositivos IoT no Hub IoT, os pais para seu dispositivo IoT Edge:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Obtenha suas senhas gerando um token SAS:

    - Para um dispositivo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       em que 3600 é a duração do token SAS em segundos (por exemplo, 3600 = 1 hora).
    
    - Para um módulo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       em que 3600 é a duração do token SAS em segundos (por exemplo, 3600 = 1 hora).

3. Copie o token SAS, que é o valor correspondente à chave "SAS" da saída. Aqui está um exemplo de saída do comando CLI do Azure acima:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorizar clientes do Publicador e do assinante

Para autorizar o Publicador e o Assinante, edite o IoT Edge Hub//para criar uma implantação IoT Edge por meio do CLI do Azure, do Visual Studio ou do Visual Studio Code para incluir a seguinte política de autorização:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Autenticação de chaves simétricas sem TLS

#### <a name="subscribe"></a>Assinar

Conecte o cliente do **sub_client** MQTT ao agente do MQTT e assine o `test_topic` executando o seguinte comando em seu dispositivo de IOT Edge:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

em que `<edge_device_address>`  =  `localhost` neste exemplo, como o cliente está em execução no mesmo dispositivo que IOT Edge.

Observe que a porta 1883 (MQTT), sem o TLS, é usada neste primeiro exemplo. Outro exemplo com a porta 8883 (MQTTS), com TLS habilitado, é mostrado na próxima seção.

O **sub_client** cliente MQTT agora está iniciado e está aguardando mensagens de entrada no `test_topic` .

#### <a name="publish"></a>Publicação

Conecte o cliente do **pub_client** MQTT ao agente do MQTT e publique uma mensagem na mesma `test_topic` forma acima, executando o seguinte comando em seu dispositivo de IOT Edge de outro terminal:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

em que `<edge_device_address>`  =  `localhost` neste exemplo, como o cliente está em execução no mesmo dispositivo que IOT Edge.

Executando o comando, o **sub_client** cliente MQTT recebe a mensagem "Olá".

### <a name="symmetric-keys-authentication-with-tls"></a>Autenticação de chaves simétricas com TLS

Para habilitar o TLS, a porta deve ser alterada de 1883 (MQTT) para 8883 (MQTTS) e os clientes devem ter o certificado raiz do agente MQTT para poder validar a cadeia de certificados enviada pelo agente de MQTT. Isso pode ser feito seguindo as etapas fornecidas na seção [Secure Connection (TLS)](#secure-connection-tls).

Como os clientes estão em execução no mesmo dispositivo que o MQTT Broker no exemplo acima, as mesmas etapas se aplicam para habilitar o TLS apenas alterando o número da porta de 1883 (MQTT) para 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Tópicos de publicar e assinar o Hub IoT

Os [SDKs do dispositivo IOT do Azure](https://github.com/Azure/azure-iot-sdks) já permitem que os clientes executem operações do Hub IOT, mas não permitem publicação/assinatura em tópicos definidos pelo usuário. As operações do Hub IoT podem ser executadas usando qualquer cliente MQTT usando a semântica de publicação e assinatura, desde que os protocolos primitivos do Hub IoT sejam respeitados. Vamos percorrer as especificidades para entender como esses protocolos funcionam.

### <a name="send-telemetry-data-to-iot-hub"></a>Enviar dados de telemetria ao Hub IoT

O envio de dados de telemetria para o Hub IoT é semelhante à publicação em um tópico definido pelo usuário, mas usando um tópico específico do Hub IoT:

- Para um dispositivo, a telemetria é enviada no tópico: `devices/<device_name>/messages/events`
- Para um módulo, a telemetria é enviada no tópico: `devices/<device_name>/<module_name>/messages/events`

Além disso, crie uma rota como `FROM /messages/* INTO $upstream` para enviar telemetria do IOT Edge agente do MQTT para o Hub IOT. Para saber mais sobre roteamento, consulte [declarar rotas](module-composition.md#declare-routes).

### <a name="get-twin"></a>Colocar o entrelaçado

Obter o dispositivo/módulo "r" não é um padrão MQTT típico. O cliente precisa emitir uma solicitação para o entrelaçamento que o Hub IoT vai atender.

Para receber gêmeos, o cliente precisa assinar um tópico específico do Hub IoT `$iothub/twin/res/#` . Este nome de tópico é herdado do Hub IoT e todos os clientes precisam assinar o mesmo tópico. Isso não significa que os dispositivos ou módulos recebam as duas. O Hub IoT e o Hub de IoT Edge sabe que o entrelaçamento deve ser entregue onde, mesmo que todos os dispositivos escutem o mesmo nome de tópico. 

Depois que a assinatura é feita, o cliente precisa solicitar o "" publicando uma mensagem em um tópico específico do Hub IOT, `$iothub/twin/GET/?rid=<request_id>/#` em que  `<request_id>` é um identificador arbitrário. O Hub IoT enviará sua resposta com os dados solicitados no tópico `$iothub/twin/res/200/?rid=<request_id>` , que o cliente assina. É assim que um cliente pode emparelhar suas solicitações com as respostas.

### <a name="receive-twin-patches"></a>Receber patches de entrelaçamento

Para receber patches de para o IoTHub, um cliente precisa assinar o tópico especial para o inscrições `$iothub/twin/PATCH/properties/desired/#` . Depois que a assinatura é feita, o cliente recebe os patches de entrelaçamento enviados pelo Hub IoT neste tópico. 

### <a name="receive-direct-methods"></a>Receber métodos diretos

O recebimento de um método direto é semelhante ao recebimento de gêmeos completo com a adição de que o cliente precisa confirmar que recebeu a chamada. Primeiro, o cliente assina o tópico especial do Hub IoT `$iothub/methods/POST/#` . Depois que um método direto for recebido neste tópico, o cliente precisará extrair o identificador `rid` de solicitação do subtópico no qual o método direto é recebido e, por fim, publicar uma mensagem de confirmação no tópico especial do Hub IOT `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Métodos diretos de envio

O envio de um método direto é uma chamada HTTP e, portanto, não passa pelo agente MQTT. Para enviar um método direto ao Hub IoT, consulte [entender e invocar métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md). Para enviar um método direto localmente para outro módulo, consulte este [exemplo de invocação de método direto do SDK do Azure IOT C#](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publicar e assinar entre os agentes MQTT

Para conectar dois agentes MQTT, o Hub de IoT Edge inclui uma ponte MQTT. Uma ponte MQTT é comumente usada para conectar um agente do MQTT em execução a outro agente do MQTT. Em geral, apenas um subconjunto do tráfego local é enviado por push para outro agente.

> [!NOTE]
> A ponte do Hub IoT Edge pode ser usada no momento somente entre dispositivos IoT Edge aninhados. Ele não pode ser usado para enviar dados ao Hub IoT, pois o Hub IoT não é um agente MQTT completo. Para saber mais sobre o suporte a recursos do agente MQTT do Hub IoT, consulte [comunicar-se com o Hub IOT usando o protocolo MQTT](../iot-hub/iot-hub-mqtt-support.md). Para saber mais sobre como aninhar dispositivos IoT Edge, consulte [conectar um dispositivo de IOT Edge downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

Em uma configuração aninhada, a ponte IoT Edge Hub MQTT atua como um cliente do agente MQTT pai, portanto, as regras de autorização devem ser definidas no EdgeHub pai para permitir que o EdgeHub filho publique e assine os tópicos específicos definidos pelo usuário para os quais a ponte está configurada.

O IoT Edge ponte MQTT é configurado por meio de uma estrutura JSON que é enviada para o Hub de IoT Edge por meio de seu entrelaçamento. Edite um hub de IoT Edge "e" para configurar sua ponte MQTT.

> [!NOTE]
> Para a visualização pública, a configuração da ponte MQTT só está disponível por meio do Visual Studio, Visual Studio Code ou CLI do Azure. Atualmente, o portal do Azure não dá suporte à edição do hub de IoT Edge e sua configuração de ponte MQTT.

A ponte MQTT pode ser configurada para conectar um agente de MQTT de Hub de IoT Edge a vários agentes externos. Para cada agente externo, as seguintes configurações são necessárias:

- `endpoint` é o endereço do agente de MQTT remoto ao qual se conectar. Somente os dispositivos IoT Edge pai têm suporte no momento e são definidos pela variável `$upstream` .
- `settings` define quais tópicos fazer a ponte para um ponto de extremidade. Pode haver várias configurações por ponto de extremidade e os seguintes valores são usados para configurá-lo:
    - `direction`: seja `in` para assinar os tópicos do agente remoto ou `out` publicar nos tópicos do agente remoto
    - `topic`: padrão de tópico principal a ser correspondido. Os [curingas MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) podem ser usados para definir esse padrão. Prefixos diferentes podem ser aplicados a este padrão de tópico no agente local e no agente remoto.
    - `outPrefix`: Prefixo aplicado ao `topic` padrão no agente remoto.
    - `inPrefix`: Prefixo que é aplicado ao `topic` padrão no agente local.

Abaixo está um exemplo de uma configuração de ponte IoT Edge MQTT que Republica todas as mensagens recebidas nos tópicos `alerts/#` de um dispositivo de IOT Edge pai para um dispositivo IOT Edge filho nos mesmos tópicos e Republica todas as mensagens enviadas nos tópicos `/local/telemetry/#` de um dispositivo IOT Edge filho para um dispositivo de IOT Edge pai nos tópicos `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Outras observações sobre a ponte MQTT do hub de IoT Edge:
- O protocolo MQTT será usado automaticamente como protocolo upstream quando o agente MQTT for usado e esse IoT Edge for usado em uma configuração aninhada, por exemplo, com um `parent_hostname` especificado. Para saber mais sobre protocolos upstream, consulte [comunicação na nuvem](iot-edge-runtime.md#cloud-communication). Para saber mais sobre configurações aninhadas, confira [conectar um dispositivo de IOT Edge downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Próximas etapas

[Entender o Hub de IoT Edge](iot-edge-runtime.md#iot-edge-hub)