---
title: Compreender módulos gêmeos no Hub IoT do Azure| Microsoft Docs
description: Guia de desenvolvedor ‑ use módulos gêmeos para sincronizar os dados de estado e de configuração entre os dispositivos e o Hub IoT
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: cd0a9a66f3014a39a73cf04badfc67cd2ff4c3de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363161"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Entender e usar módulos gêmeos no Hub IoT

Este artigo pressupõe que você leu [compreender e usar dispositivos gêmeos no IoT Hub](iot-hub-devguide-device-twins.md) primeiro. No Hub IoT, em cada identidade de dispositivo, você pode criar até 20 identidades de módulo. Cada identidade de módulo implicitamente gera módulo gêmeo. Semelhante aos gêmeos do dispositivo, os gêmeos do módulo são documentos JSON que armazenam informações de estado do módulo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um módulo gêmeo para cada módulo que você conecta ao Hub IoT. 

No lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar módulos em que cada um abre uma conexão independente para o IoT Hub. Essa funcionalidade permite usar namespaces separados para diferentes componentes em seu dispositivo. Por exemplo, você tem uma máquina de vendas com três sensores diferentes. Cada sensor é controlado por diferentes departamentos na sua empresa. Você pode criar um módulo para cada sensor. Dessa forma, cada departamento só é capaz de enviar trabalhos ou métodos diretos para o sensor controlado por ele, evitando conflitos e erros de usuário.

 A identidade do módulo e o módulo gêmeo fornecem os mesmos recursos que a identidade do dispositivo e o dispositivo gêmeo, mas com granularidade mais precisa. Essa granularidade mais fina permite que dispositivos capazes, como dispositivos baseados em sistema operacional ou dispositivos de firmware que gerenciam vários componentes, isolem a configuração e as condições de cada um desses componentes. A identidade do módulo e os módulos gêmeos fornecem uma separação de problemas do gerenciamento ao trabalhar com dispositivos IoT que têm componentes de software modular. Nosso objetivo é dar suporte a toda a funcionalidade de dispositivos gêmeos no nível dos módulos gêmeos por meio da disponibilidade de módulos gêmeos para o público geral. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do módulo gêmeo: *marcas*, *propriedades desejadas* e *relatadas*.
* As operações que os módulos e back-ends de dispositivo podem executar em módulos gêmeos.

Veja as [Diretrizes de comunicação do dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientação sobre o uso de propriedades reportadas, mensagens do dispositivo para nuvem ou upload do arquivo.

Veja as [Diretrizes de comunicação da nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientação sobre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para dispositivo.

## <a name="module-twins"></a>Módulos gêmeos

Módulos gêmeos armazenam informações relacionadas ao módulo que:

* Os módulos no dispositivo e o Hub IoT podem usar para sincronizar a configuração e as condições do módulo.

* A solução que o back-end pode usar para consultar e direcionar operações de execução longa.

O ciclo de vida de um módulo gêmeo está vinculado a correspondente [identidade de módulo](iot-hub-devguide-identity-registry.md). Os módulos gêmeos são criados e excluídos implicitamente quando uma identidade de módulo é criada ou excluída no Hub IoT.

Um módulo gêmeo é um documento JSON que inclui:

* **Marcas**. Uma seção do documento JSON que o back-end de solução pode ler e na qual pode gravar. As marcações não são visíveis para os módulos no dispositivo. As marcações são definidas para fins de consulta.

* **Propriedades desejadas**. Usado junto com as propriedades relatadas para sincronizar a configuração ou condições de módulo. O back-end da solução pode definir as propriedades desejadas e o aplicativo de módulo pode lê-las. O aplicativo do módulo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas**. Usado junto com as propriedades desejadas para sincronizar a configuração ou condições de módulo. O aplicativo de módulo pode definir as propriedades relatadas e o back-end da solução pode lê-las e consultá-las.

* **Propriedades de identidade do módulo**. A raiz do documento JSON do módulo gêmeo contém as propriedades somente leitura da identidade de módulo correspondente armazenado na [registro de identidade](iot-hub-devguide-identity-registry.md).

![Representação de arquitetura do dispositivo gêmeo](./media/iot-hub-devguide-device-twins/module-twin.jpg)

O seguinte exemplo mostra um documento JSON de módulo gêmeo:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

No objeto raiz estão as propriedades de identidade do módulo e os objetos de contêiner para `tags`, além das propriedades `reported` e `desired`. O contêiner `properties` contém alguns elementos somente leitura (`$metadata`, `$etag` e `$version`) descritos nas seções de [Módulo de metadados gêmeos](iot-hub-devguide-module-twins.md#module-twin-metadata) e [Otimista de simultaneidade](iot-hub-devguide-device-twins.md#optimistic-concurrency).

### <a name="reported-property-example"></a>Exemplo da propriedade reportada

No exemplo anterior, o módulo gêmeo contém uma propriedade `batteryLevel` que é reportada pelo aplicativo do módulo. Essa propriedade torna possível a consultar e operação em módulos com base no último nível da bateria reportado. Outros exemplos incluem os recursos de módulo de relatórios de aplicativo de módulo ou as opções de conectividade.

> [!NOTE]
> As propriedades relatadas simplificam cenários nos quais o back-end da solução está interessado no último valor conhecido de uma propriedade. Use [mensagens do dispositivo para nuvem](iot-hub-devguide-messages-d2c.md) se o back-end da solução precisa processar telemetria do módulo na forma de sequências de eventos de data/hora, como uma série temporal.

### <a name="desired-property-example"></a>Exemplo da propriedade desejada

No exemplo anterior, as propriedades relatadas e desejadas do módulo gêmeo `telemetryConfig` são usadas pelo aplicativo do módulo e pelo back-end da solução para sincronizar a configuração de telemetria para o módulo em questão. Por exemplo: 

1. O back-end da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades desejado:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. O aplicativo do módulo é notificado sobre a alteração imediatamente, se estiver conectado, ou na primeira reconexão. Em seguida, o aplicativo do módulo reporta a configuração atualizada (ou uma condição de erro usando a propriedade `status`). Esta é a parte das propriedades reportadas:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. O back-end da solução pode acompanhar os resultados da operação de configuração em vários módulos, por [consultando](iot-hub-devguide-query-language.md) módulos gêmeos.

> [!NOTE]
> Os snippets anteriores são exemplos, otimizados para facilitar a leitura, de uma forma de codificar uma configuração de módulo e seu status. O Hub IoT não impõe um esquema específico para as propriedades desejadas e reportadas do módulo gêmeo nos módulos gêmeos.
> 
> 

## <a name="back-end-operations"></a>Operações de back-end
O back-end da solução funciona no módulo gêmeo usando as seguintes operações atômicas, expostas por meio de HTTPS:

* **Recuperar módulo gêmeo por ID**. Essa operação retorna o documento do módulo gêmeo, incluindo marcas e propriedades do sistema desejadas e reportadas.

* **Atualizar parcialmente o módulo gêmeo**. Essa operação permite que o back-end da solução atualize parcialmente as marcações ou propriedades desejadas em um módulo gêmeo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas como `null` foram removidas. O exemplo a seguir cria uma nova propriedade desejada com o valor `{"newProperty": "newValue"}`, substitui o valor existente de `existingProperty` por `"otherNewValue"` e remove `otherOldProperty`. Nenhuma outra alteração é feitas nas propriedades desejadas ou marcas existentes:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Substituir propriedades desejadas**. Esta operação permite que o back-end da solução substitua completamente todas as suas propriedades desejadas existentes e substitui um novo documento JSON por `properties/desired`.

* **Substituir marcas**. Esta operação permite que o back-end da solução substitua completamente todas as marcas existentes e substitui um novo documento JSON por `tags`.

* **Receba notificações gêmeas**. Esta operação permite que o back-end de solução seja notificado quando o gêmeo é modificado. Para fazer isso, sua solução de IoT precisa para criar uma rota e definir a Fonte de Dados como *twinChangeEvents*. Por padrão, nenhuma notificação gêmea é enviada, ou seja, nenhuma dessas rotas existe previamente. Se a taxa de alteração for alta demais ou então por outros motivos como falhas internas, o Hub IoT poderá enviar apenas uma notificação contendo todas as alterações. Portanto, se o aplicativo precisar de auditoria e registro em log confiável de todos os estados intermediários, será necessário usar mensagens de dispositivo para nuvem. A mensagem de notificação gêmea inclui propriedades e corpo.

  - propriedades

    | NOME | Value |
    | --- | --- |
    $content-type | aplicativo/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID do dispositivo |
    moduleId | ID do módulo |
    hubName | Nome do Hub IoT |
    operationTimestamp | Carimbo de data/hora [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) da operação |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    As propriedades do sistema de mensagens são fixadas previamente com o símbolo `$`.

  - Corpo
        
    Esta seção inclui todas as alterações gêmeas em um formato JSON. Ele usa o mesmo formato que um patch, com as diferenças de que ele pode conter todas as seções gêmeas: marcas, properties.reported, properties.desired e também de que ele contém os elementos "$metadata". Por exemplo,

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Todas as operações anteriores suportam [simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e exigem a permissão **ServiceConnect**, conforme definido no artigo [Control Access to IoT Hub](iot-hub-devguide-security.md).

Além dessas operações, o backend da solução pode consultar os gêmeos do módulo usando a linguagem de consulta do [Hub IoT semelhante a SQL](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Operações de módulo

O aplicativo do módulo opera no módulo gêmeo usando as seguintes operações atômicas:

* **Recuperar módulo gêmeo**. Essa operação retorna o documento do módulo gêmeo (incluindo marcas e propriedades do sistema desejadas e reportadas) para o módulo conectado no momento.

* **Atualizar parcialmente as propriedades reportadas**. Essa operação permite a atualização parcial das propriedades reportadas do módulo conectado no momento. Esta operação usa o mesmo formato de atualização JSON que a solução de back-end usa para uma atualização parcial de propriedades desejadas.

* **Observar as propriedades desejadas**. O módulo conectado no momento pode optar por ser notificado sobre atualizações para as propriedades desejadas quando elas ocorrem. O módulo recebe a mesma forma de atualização (substituição total ou parcial) executada pelo back-end da solução.

Todas as operações anteriores exigem o **ModuleConnect** permissão, conforme definido na [controlar o acesso ao IoT Hub](iot-hub-devguide-security.md) artigo.

Os [SDKs do dispositivo IoT do Azure](iot-hub-devguide-sdks.md) facilitam o uso das operações anteriores em várias linguagens e plataformas.

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Marcas, propriedades desejadas e propriedades reportadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON são cadeias de caracteres UNICODE UTF-8 de 64 bytes que diferenciam maiúsculas de minúsculas. Caracteres permitidos excluir caracteres de controle UNICODE (segmentos C0 e C1) e `.`, SP, e `$`.

* Todos os valores em objetos JSON podem ser dos seguintes tipos de JSON: booliano, número, cadeia de caracteres, objeto. Não há permissão para matrizes. O valor máximo dos inteiros é 4503599627370495 e o valor mínimo dos inteiros é -4503599627370496.

* Todos os objetos JSON em marcações, propriedades desejadas e reportadas podem ter uma profundidade máxima de 5. Por exemplo, o seguinte objeto é válido:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Todos os valores de cadeia de caracteres podem ter no máximo 512 bytes de comprimento.

## <a name="module-twin-size"></a>Tamanho do módulo gêmeo

O Hub IoT impõe um limite de tamanho de 8 KB em cada um dos valores totais respectivos de `tags`, `properties/desired` e `properties/reported`, excluindo elementos somente leitura.

O tamanho é calculado pela contagem de todos os caracteres, exceto caracteres de controle UNICODE (segmentos C0 e C1) e espaços que estão fora das constantes da cadeia de caracteres.

O Hub IoT rejeita com erro todas as operações que podem aumentar o tamanho desses documentos acima do limite.

## <a name="module-twin-metadata"></a>Metadados do módulo gêmeo

O Hub IoT mantém o carimbo de data e hora da última atualização de cada objeto JSON nas propriedades desejadas e reportadas do módulo gêmeo. Os carimbos de data e hora estão em UTC e são codificados no formato [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo: 

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Essas informações são armazenadas em cada nível (não apenas nas folhas da estrutura JSON) a fim de preservar as atualizações que removem chaves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade otimista

Marcas, propriedades desejadas e reportadas oferecem suporte à simultaneidade otimista.
Marcas tem uma Etag, de acordo com [RFC7232](https://tools.ietf.org/html/rfc7232), que representa a representação JSON da marca. Você pode usar ETags em operações de atualização condicionais do back-end da solução para garantir a consistência.

As propriedades desejadas e reportadas do módulo gêmeo não têm as ETags, mas um valor `$version` com garantia de ser incremental. De forma semelhante a uma ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de módulo para uma propriedade relatada ou o back-end de solução para uma propriedade desejada.

Versões também são úteis quando um agente observador (por exemplo, o aplicativo do módulo que observa as propriedades desejadas) deve reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A seção [fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações. 

## <a name="next-steps"></a>Próximas etapas

Para experimentar alguns dos conceitos descritos neste artigo, consulte os tutoriais do Hub IoT a seguir:

* [Introdução ao IoT Hub identidade e o módulo gêmeo do módulo usando o back-end do .NET e o dispositivo do .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)
