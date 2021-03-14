---
title: Compreender dispositivos gêmeos no Hub IoT do Azure| Microsoft Docs
description: Guia de desenvolvedor ‑ use dispositivos gêmeos para sincronizar os dados de estado e de configuração entre os dispositivos e o Hub IoT
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: fff4b9157c30203f47c65a74b211e3dbf6426d92
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093027"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Entender e usar dispositivos gêmeos no Hub IoT

*Dispositivos gêmeos* são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT do Azure mantém um dispositivo gêmeo para cada dispositivo que você conecta ao Hub IoT. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este artigo descreve:

* A estrutura do dispositivo gêmeo: *marcas*, *propriedades desejadas* e *relatadas*.
* As operações que os aplicativos e back-ends de dispositivo podem executar em dispositivos gêmeos.

Use os dispositivos gêmeos para:

* Armazene os metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de vendas.

* Relate informações de estado atual, como recursos disponíveis e condições do aplicativo do dispositivo. Por exemplo, um dispositivo é conectado ao hub IoT via celular ou Wi-Fi.

* Sincronize o estado dos fluxos de trabalho de longa execução entre o aplicativo do dispositivo e o aplicativo do back-end. Por exemplo, quando a solução de back-end especifica a nova versão do firmware para instalação, e o aplicativo do dispositivo relata os diversos estágios do processo de atualização.

* Consultar os metadados, a configuração ou o estado do seu dispositivo.

Veja as [Diretrizes de comunicação do dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) para obter orientação sobre o uso de propriedades reportadas, mensagens do dispositivo para nuvem ou upload do arquivo.

Veja as [Diretrizes de comunicação da nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md) para obter orientação sobre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para dispositivo.

Para saber como o dispositivo gêmeos está relacionado ao modelo de dispositivo usado por um dispositivo de Plug and Play do Azure IoT, consulte [entender o IoT plug and Play Digital gêmeos](../iot-pnp/concepts-digital-twin.md).

## <a name="device-twins"></a>Dispositivos gêmeos

Dispositivos gêmeos armazenam informações relacionadas ao dispositivo que:

* O dispositivo e os back-ends podem usar para sincronizar a configuração e as condições do dispositivo.

* A solução que o back-end pode usar para consultar e direcionar operações de execução longa.

O ciclo de vida de um dispositivo gêmeo está vinculado à [identidade do dispositivo](iot-hub-devguide-identity-registry.md) correspondente. Os dispositivos gêmeos são criados e excluídos implicitamente quando uma identidade de dispositivo é criada ou excluída no Hub IoT.

Um dispositivo gêmeo é um documento JSON que inclui:

* **Marcas**. Uma seção do documento JSON que o back-end de solução pode ler e na qual pode gravar. As marcas não ficam visíveis para os aplicativos do dispositivo.

* **Propriedades desejadas**. Usado junto com as propriedades relatadas para sincronizar a configuração de dispositivo ou condições. O back-end da solução pode definir as propriedades desejadas e o aplicativo de dispositivo pode lê-las. O aplicativo do dispositivo também pode receber notificações de alterações nas propriedades desejadas.

* **Propriedades reportadas**. Usado junto com as propriedades desejadas para sincronizar a configuração de dispositivo ou condições. O aplicativo de dispositivo pode definir as propriedades relatadas e o back-end da solução pode lê-las e consultá-las.

* **Propriedades de identidade do dispositivo**. A raiz do documento JSON do dispositivo gêmeo contém as propriedades somente leitura da identidade do dispositivo correspondente armazenada no [registro identidade](iot-hub-devguide-identity-registry.md). Propriedades `connectionStateUpdatedTime` e `generationId` não serão incluídas.

![Captura de tela das propriedades do dispositivo gêmeo](./media/iot-hub-devguide-device-twins/twin.png)

O seguinte exemplo mostra um documento JSON de dispositivo gêmeo:

```json
{
    "deviceId": "devA",
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

No objeto raiz estão as propriedades de identidade do dispositivo e os objetos de contêiner para `tags`, além das propriedades `reported` e `desired`. O contêiner `properties` tem alguns elementos somente leitura (`$metadata`, `$etag` e `$version`) descritos nas seções [Metadados de dispositivo gêmeo](iot-hub-devguide-device-twins.md#device-twin-metadata) e [Simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency).

### <a name="reported-property-example"></a>Exemplo da propriedade reportada

No exemplo anterior, o dispositivo gêmeo contém uma propriedade `batteryLevel` que é reportada pelo aplicativo do dispositivo. Essa propriedade torna possível a consultar e operação em dispositivos com base no último nível da bateria reportado. Outros exemplos incluem os recursos de dispositivo de relatórios de aplicativo de dispositivo ou as opções de conectividade.

> [!NOTE]
> As propriedades relatadas simplificam cenários nos quais o back-end da solução está interessado no último valor conhecido de uma propriedade. Use as [mensagens do dispositivo para nuvem](iot-hub-devguide-messages-d2c.md) se o back-end da solução precisar processar telemetria do dispositivo na forma de sequências de eventos com carimbo de data/hora, como uma série temporal.

### <a name="desired-property-example"></a>Exemplo da propriedade desejada

No exemplo anterior, as propriedades relatadas e desejadas do dispositivo gêmeo `telemetryConfig` são usadas pelo aplicativo do dispositivo e pelo back-end da solução para sincronizar a configuração de telemetria para o dispositivo em questão. Por exemplo:

1. O back-end da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades desejado:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. O aplicativo do dispositivo é notificado sobre a alteração imediatamente, se estiver conectado, ou na primeira reconexão. Em seguida, o aplicativo do dispositivo reporta a configuração atualizada (ou uma condição de erro usando a propriedade `status`). Esta é a parte das propriedades reportadas:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. O back-end da solução pode acompanhar os resultados da operação de configuração em vários dispositivos [consultando](iot-hub-devguide-query-language.md) os dispositivos gêmeos.

> [!NOTE]
> Os snippets anteriores são exemplos, otimizados para facilitar a leitura, de uma forma de codificar uma configuração de dispositivo e seu status. O Hub IoT não impõe um esquema específico para as propriedades desejadas e reportadas do dispositivo gêmeo nos dispositivos gêmeos.
> 

Você pode usar gêmeos para sincronizar operações de longa execução, como atualizações de firmware. Para obter mais informações sobre como usar propriedades para sincronizar e acompanhar uma operação de longa duração entre dispositivos, confira [Usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operações de back-end

O back-end da solução funciona no dispositivo gêmeo usando as seguintes operações atômicas, expostas por meio de HTTPS:

* **Recuperar o dispositivo de entrelaçamento por ID**. Essa operação retorna o documento do dispositivo gêmeo, incluindo marcas e propriedades do sistema desejadas e reportadas.

* **Atualizar parcialmente o dispositivo gêmeo**. Essa operação permite que o back-end da solução atualize parcialmente as marcações ou propriedades desejadas em um dispositivo gêmeo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas como `null` foram removidas. O exemplo a seguir cria uma nova propriedade desejada com o valor `{"newProperty": "newValue"}`, substitui o valor existente de `existingProperty` por `"otherNewValue"` e remove `otherOldProperty`. Nenhuma outra alteração é feitas nas propriedades desejadas ou marcas existentes:

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

* **Receba notificações gêmeas**. Esta operação permite que o back-end de solução seja notificado quando o gêmeo é modificado. Para fazer isso, sua solução de IoT precisa para criar uma rota e definir a Fonte de Dados como *twinChangeEvents*. Por padrão, nenhuma dessas rotas existe previamente, então nenhuma notificação gêmea é enviada. Se a taxa de alteração for alta demais ou então por outros motivos como falhas internas, o Hub IoT poderá enviar apenas uma notificação contendo todas as alterações. Portanto, se o aplicativo precisar de auditoria e registro em log confiável de todos os estados intermediários, será necessário usar mensagens de dispositivo para nuvem. A mensagem de notificação gêmea inclui propriedades e corpo.

  - Propriedades

    | Nome | Valor |
    | --- | --- |
    $content-type | aplicativo/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID do dispositivo |
    hubName | Nome do Hub IoT |
    operationTimestamp | Carimbo de data/hora [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) da operação |
    iothub-message-schema | twinChangeNotification |
    opType | "replaceTwin" ou "updateTwin" |

    As propriedades do sistema de mensagens são fixadas previamente com o símbolo `$`.

  - Corpo
        
    Esta seção inclui todas as alterações gêmeas em um formato JSON. Ele usa o mesmo formato que um patch, com a diferença de que ele pode conter todas as seções de myup: Tags, Properties. reported, Properties. Desired e que ele contém os elementos "$metadata". Por exemplo,

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

Todas as operações anteriores dão suporte à [Simultaneidade otimista](iot-hub-devguide-device-twins.md#optimistic-concurrency) e exigem a permissão **ServiceConnect**, conforme definido no artigo [Controlar acesso ao Hub IoT](iot-hub-devguide-security.md).

Além dessas operações, o back-end da solução pode:

* Consulte os dispositivos gêmeos usando a [linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) semelhante a SQL.

* Execute operações em conjuntos grandes de dispositivos gêmeos usando [trabalhos](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operações de dispositivo

O aplicativo do dispositivo opera no dispositivo gêmeo usando as seguintes operações atômicas:

* **Recuperar dispositivo gêmeo**. Esta operação retorna o documento de dispositivo de documentos (incluindo as propriedades do sistema desejadas e reportadas) para o dispositivo conectado no momento. (As marcas não são visíveis para os aplicativos do dispositivo.)

* **Atualizar parcialmente as propriedades reportadas**. Essa operação permite a atualização parcial das propriedades reportadas do dispositivo conectado no momento. Esta operação usa o mesmo formato de atualização JSON que a solução de back-end usa para uma atualização parcial de propriedades desejadas.

* **Observar as propriedades desejadas**. O dispositivo conectado no momento pode optar por ser notificado sobre atualizações para as propriedades desejadas quando elas ocorrem. O dispositivo recebe a mesma forma de atualização (substituição total ou parcial) executada pelo back-end da solução.

Todas as operações anteriores exigem a permissão **DeviceConnect**, conforme definido no [Controlar acesso ao Hub IoT](iot-hub-devguide-security.md).

Os [SDKs do dispositivo IoT do Azure](iot-hub-devguide-sdks.md) facilitam o uso das operações anteriores em várias linguagens e plataformas. Para obter mais informações sobre os detalhes dos primitivos do Hub IoT para sincronização de propriedades desejadas, consulte [Fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades

Marcas, propriedades desejadas e propriedades reportadas são objetos JSON com as seguintes restrições:

* **Chaves**: todas as chaves em objetos JSON são codificadas em UTF-8, diferencia maiúsculas de minúsculas e até 1 KB de comprimento. Os caracteres permitidos excluem caracteres de controle UNICODE (segmentos C0 e C1) e `.`, `$` e SP.

* **Valores**: todos os valores em objetos JSON podem ser dos seguintes tipos JSON: booliano, número, Cadeia de caracteres, objeto. Também há suporte para matrizes.

    * Os inteiros podem ter um valor mínimo de-4503599627370496 e um valor máximo de 4503599627370495.

    * Os valores de cadeia de caracteres são codificados em UTF-8 e podem ter um comprimento máximo de 4 KB.

* **Profundidade**: a profundidade máxima de objetos JSON em marcas, propriedades desejadas e propriedades relatadas é 10. Por exemplo, o seguinte objeto é válido:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Tamanho do dispositivo gêmeo

O Hub IoT impõe um limite de tamanho de 8 KB no valor de e `tags` um limite de tamanho de 32 KB, cada um com o valor de `properties/desired` e `properties/reported` . Esses totais são exclusivos de elementos somente leitura como `$etag` , `$version` e `$metadata/$lastUpdated` .

O tamanho do papel é calculado da seguinte maneira:

* Para cada propriedade no documento JSON, o Hub IoT computa cumulativamente e adiciona o comprimento da chave e do valor da propriedade.

* As chaves de propriedade são consideradas cadeias de caracteres codificadas em UTF8.

* Valores de propriedade simples são considerados cadeias de caracteres codificadas em UTF8, valores numéricos (8 bytes) ou valores Boolianos (4 bytes).

* O tamanho das cadeias de caracteres codificadas em UTF8 é calculado por meio da contagem de todos os caracteres, excluindo caracteres de controle UNICODE (segmentos C0 e C1).

* Valores de propriedade complexos (objetos aninhados) são calculados com base no tamanho agregado das chaves de propriedade e dos valores de propriedade que eles contêm.

O Hub IoT rejeita com um erro todas as operações que aumentariam o tamanho dos `tags` `properties/desired` documentos, ou `properties/reported` acima do limite.

## <a name="device-twin-metadata"></a>Metadados do dispositivo gêmeo

O Hub IoT mantém o carimbo de data e hora da última atualização de cada objeto JSON nas propriedades desejadas e reportadas do dispositivo gêmeo. Os carimbos de data e hora estão em UTC e são codificados no formato [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)`YYYY-MM-DDTHH:MM:SS.mmmZ`.

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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
As marcas têm uma ETag, de acordo com a [RFC7232](https://tools.ietf.org/html/rfc7232), que representa a representação JSON da marca. Você pode usar ETags em operações de atualização condicionais do back-end da solução para garantir a consistência.

As propriedades desejadas e reportadas do dispositivo gêmeo não têm as ETags, mas um valor `$version` com garantia de ser incremental. De forma semelhante a uma ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de dispositivo para uma propriedade relatada ou o back-end de solução para uma propriedade desejada.

Versões também são úteis quando um agente observador (por exemplo, o aplicativo do dispositivo que observa as propriedades desejadas) deve reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A [seção Fluxo de reconexão do dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo

O Hub IoT não preserva notificações de atualização de propriedades desejadas para dispositivos desconectados. Acontece que um dispositivo que está se conectando deve recuperar o documento completo de propriedades desejadas, além de se inscrever para receber notificações de atualização. Considerando a possibilidade de corridas entre notificações de atualização e recuperação completa, o seguinte fluxo deve ser garantido:

1. O aplicativo de dispositivo conecta-se a um Hub IoT.
2. O aplicativo de dispositivo se inscreve para as notificações de atualização de propriedades desejadas .
3. O aplicativo de dispositivo recupera o documento completo para as propriedades desejadas.

O aplicativo de dispositivo pode ignorar todas as notificações com `$version` menor ou igual à versão do documento recuperado completo. Essa abordagem é possível porque o Hub IoT garante que as versões sempre aumentam.

> [!NOTE]
> Essa lógica já está implementada nos [SDKs do dispositivo IoT do Azure](iot-hub-devguide-sdks.md). Essa descrição será útil somente se o aplicativo de dispositivo não puder usar qualquer um dos SDKs do dispositivo IoT do Azure, e deve programar a interface MQTT diretamente.
> 

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* O artigo [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.

* O artigo [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.

* O artigo [SDKs de dispositivo e serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* O artigo [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta do Hub IoT que você pode utilizar para recuperar informações do Hub IoT sobre seus dispositivos gêmeos e trabalhos.

* O artigo [Suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre dispositivos gêmeos, pode ser interessante ler os seguintes tópicos do Guia do desenvolvedor do Hub IoT:

* [Entender e usar módulos gêmeos no Hub IoT](iot-hub-devguide-module-twins.md)
* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)
* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte os tutoriais do Hub IoT a seguir:

* [Como usar o dispositivo gêmeo](iot-hub-node-node-twin-getstarted.md)
* [Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)
* [Gerenciamento de dispositivos com as ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)
