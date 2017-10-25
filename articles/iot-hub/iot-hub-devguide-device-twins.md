---
title: "Compreender dispositivos gêmeos no Hub IoT do Azure| Microsoft Docs"
description: "Guia de desenvolvedor ‑ use dispositivos gêmeos para sincronizar os dados de estado e de configuração entre os dispositivos e o Hub IoT"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 454eb7b1f4f48e8a2a78bd3fcb6eb03b6097d44d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Entender e usar dispositivos gêmeos no Hub IoT
## <a name="overview"></a>Visão geral
*Dispositivos gêmeos* são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta ao Hub IoT. Este artigo descreve:

* A estrutura do dispositivo gêmeo: *marcas*, *propriedades desejadas* e *relatadas* e
* As operações que os aplicativos e back-ends de dispositivo podem executar em dispositivos gêmeos.


### <a name="when-to-use"></a>Quando usar
Use os dispositivos gêmeos para:

* Armazene os metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de vendas.
* Relate informações de estado atual, como recursos disponíveis e condições do aplicativo do dispositivo. Por exemplo, um dispositivo é conectado ao hub IoT via celular ou Wi-Fi.
* Sincronize o estado dos fluxos de trabalho de longa execução entre o aplicativo do dispositivo e o aplicativo do back-end. Por exemplo, quando a solução de back-end especifica a nova versão do firmware para instalação, e o aplicativo do dispositivo relata os diversos estágios do processo de atualização.
* Consultar os metadados, a configuração ou o estado do seu dispositivo.

Veja as[Diretrizes de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] para obter orientação sobre o uso de propriedades reportadas, mensagens do dispositivo para a nuvem ou carregamento do arquivo.
Veja [Diretrizes de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] para obter orientação sobre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="device-twins"></a>Dispositivos gêmeos
Dispositivos gêmeos armazenam informações relacionadas ao dispositivo que:

* O dispositivo e os back-ends podem usar para sincronizar a configuração e as condições do dispositivo.
* A solução que o back-end pode usar para consultar e direcionar operações de execução longa.

O ciclo de vida de um dispositivo gêmeo está vinculado à [identidade do dispositivo][lnk-identity] correspondente. Os dispositivos gêmeos são criados e excluídos implicitamente quando uma nova identidade de dispositivo é criada ou excluída no Hub IoT.

Um dispositivo gêmeo é um documento JSON que inclui:

* **Marcas**. Uma seção do documento JSON que o back-end de solução pode ler e na qual pode gravar. As marcas não ficam visíveis para os aplicativos do dispositivo.
* **Propriedades desejadas**. Usado junto com as propriedades relatadas para sincronizar a configuração de dispositivo ou condições. As propriedades desejadas só podem ser definidas pelo back-end da solução e podem ser lidas pelo aplicativo do dispositivo. O aplicativo do dispositivo também pode ser notificado em tempo real sobre as alterações nas propriedades desejadas.
* **Propriedades reportadas**. Usado junto com as propriedades desejadas para sincronizar a configuração de dispositivo ou condições. As propriedades relatadas só podem ser definidas pelo aplicativo do dispositivo e podem ser lidas e consultadas pelo back-end da solução.

Além disso, a raiz do documento JSON do dispositivo gêmeo contém as propriedades somente leitura da identidade de dispositivo correspondente armazenada no [registro identidade][lnk-identity].

![][img-twin]

O seguinte exemplo mostra um documento JSON de dispositivo gêmeo:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

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
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

No objeto raiz estão as propriedades do sistema e os objetos de contêiner para `tags`, além das propriedades `reported` e `desired`. O contêiner `properties` tem alguns elementos somente leitura (`$metadata`, `$etag` e `$version`), descritos nas seções [Metadados de dispositivo gêmeo][lnk-twin-metadata] e [Simultaneidade otimista][lnk-concurrency].

### <a name="reported-property-example"></a>Exemplo da propriedade reportada
No exemplo anterior, o dispositivo gêmeo contém uma propriedade `batteryLevel` que é reportada pelo aplicativo do dispositivo. Essa propriedade torna possível a consultar e operação em dispositivos com base no último nível da bateria reportado. Outros exemplos incluem os recursos de dispositivo de relatórios de aplicativo de dispositivo ou as opções de conectividade.

> [!NOTE]
> As propriedades relatadas simplificam cenários nos quais o back-end da solução está interessado no último valor conhecido de uma propriedade. Use as [mensagens do dispositivo para a nuvem][lnk-d2c] se o back-end da solução precisar processar telemetria do dispositivo na forma de sequências de eventos com carimbo de data e hora, como uma série temporal.

### <a name="desired-property-example"></a>Exemplo da propriedade desejada
No exemplo anterior, as propriedades relatadas e desejadas do dispositivo gêmeo `telemetryConfig` são usadas pelo aplicativo do dispositivo e pelo back-end da solução para sincronizar a configuração de telemetria para o dispositivo em questão. Por exemplo:

1. O back-end da solução define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com o conjunto de propriedades desejado:
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. O aplicativo do dispositivo é notificado sobre a alteração imediatamente, se estiver conectado, ou na primeira reconexão. Em seguida, o aplicativo do dispositivo reporta a configuração atualizada (ou uma condição de erro usando a propriedade `status`). Esta é a parte das propriedades reportadas:
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. O back-end da solução pode acompanhar os resultados da operação de configuração em vários dispositivos [consultando][lnk-query] os dispositivos gêmeos.

> [!NOTE]
> Os trechos anteriores são exemplos, otimizados para facilitar a leitura, de uma forma de codificar uma configuração de dispositivo e seu status. O Hub IoT não impõe um esquema específico para as propriedades desejadas e reportadas do dispositivo gêmeo nos dispositivos gêmeos.
> 
> 

Você pode usar gêmeos para sincronizar operações de longa execução, como atualizações de firmware. Para obter mais informações sobre como usar propriedades para sincronizar e acompanhar uma operação de longa duração entre dispositivos, confira [Usar propriedades desejadas para configurar dispositivos][lnk-twin-properties].

## <a name="back-end-operations"></a>Operações de back-end
O back-end da solução funciona no dispositivo gêmeo usando as seguintes operações atômicas, expostas por meio de HTTPS:

1. **Recuperar dispositivo gêmeo por id**. Essa operação retorna o documento do dispositivo gêmeo, incluindo marcações e propriedades desejadas, reportadas e do sistema.
2. **Atualizar parcialmente o dispositivo gêmeo**. Essa operação permite que o back-end da solução atualize parcialmente as marcações ou propriedades desejadas em um dispositivo gêmeo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade. As propriedades definidas como `null` foram removidas. O exemplo a seguir cria uma nova propriedade desejada com o valor `{"newProperty": "newValue"}`, substitui o valor existente de `existingProperty` por `"otherNewValue"` e remove `otherOldProperty`. Nenhuma outra alteração é feitas nas propriedades desejadas ou marcas existentes:
   
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
3. **Substituir propriedades desejadas**. Esta operação permite que o back-end da solução substitua completamente todas as suas propriedades desejadas existentes e substitui um novo documento JSON por `properties/desired`.
4. **Substituir marcas**. Esta operação permite que o back-end da solução substitua completamente todas as marcas existentes e substitui um novo documento JSON por `tags`.
5. **Receba notificações gêmeas**. Esta operação permite que o back-end de solução seja notificado quando o gêmeo é modificado. Para fazer isso, sua solução de IoT precisa para criar uma rota e definir a Fonte de Dados como *twinChangeEvents*. Por padrão, nenhuma notificação gêmea é enviada, ou seja, nenhuma dessas rotas existe previamente. Se a taxa de alteração for alta demais ou então por outros motivos como falhas internas, o Hub IoT poderá enviar apenas uma notificação contendo todas as alterações. Portanto, se seu aplicativo precisar de auditoria e registro em log confiáveis de todos os estados intermediários, ainda será recomendável que você use mensagens D2C. A mensagem de notificação gêmea inclui propriedades e corpo.

    - propriedades

    | Nome | Valor |
    | --- | --- |
    $content-type | aplicativo/json |
    $iothub-enqueuedtime |  Hora em que a notificação foi enviada |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID do dispositivo |
    hubName | Nome do Hub IoT |
    operationTimestamp | Carimbo de data/hora [ISO8601] da operação |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" ou "updateTwin" |

    As propriedades do sistema de mensagens são fixadas previamente com o símbolo `'$'`.

    - Corpo
        
    Esta seção inclui todas as alterações gêmeas em um formato JSON. Ele usa o mesmo formato que um patch, com as diferenças de que ele pode conter todas as seções gêmeas: marcas, properties.reported, properties.desired e também de que ele contém os elementos "$metadata". Por exemplo,
    ```
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

Todas as operações anteriores dão suporte à [Simultaneidade otimista][lnk-concurrency] e exigem a permissão **ServiceConnect**, conforme definido no artigo [Segurança][lnk-security].

Além dessas operações, o back-end da solução pode:

* Consulte os gêmeos de dispositivo usando a [linguagem de consulta do Hub IoT][lnk-query] semelhante a SQL.
* Executar operações em conjuntos grandes de gêmeos de dispositivo usando [trabalhos][lnk-jobs].

## <a name="device-operations"></a>Operações de dispositivo
O aplicativo do dispositivo opera no dispositivo gêmeo usando as seguintes operações atômicas:

1. **Recuperar dispositivo gêmeo**. Essa operação retorna o documento do dispositivo gêmeo (incluindo marcações e propriedades desejadas, reportadas e do sistema) para o dispositivo conectado no momento.
2. **Atualizar parcialmente as propriedades reportadas**. Essa operação permite a atualização parcial das propriedades reportadas do dispositivo conectado no momento. Esta operação usa o mesmo formato de atualização JSON que a solução de back-end usa para uma atualização parcial de propriedades desejadas.
3. **Observar as propriedades desejadas**. O dispositivo conectado no momento pode optar por ser notificado sobre atualizações para as propriedades desejadas quando elas ocorrem. O dispositivo recebe a mesma forma de atualização (substituição total ou parcial) executada pelo back-end da solução.

Todas as operações anteriores exigem a permissão **DeviceConnect**, conforme definido no artigo [Segurança][lnk-security].

Os [SDKs do dispositivo IoT do Azure][lnk-sdks] facilitam o uso das operações anteriores em várias linguagens e plataformas. Encontre mais informações sobre os detalhes dos primitivos do Hub IoT para sincronização de propriedades desejadas em [Fluxo de reconexão do dispositivo][lnk-reconnection].


## <a name="reference-topics"></a>Tópicos de referência:
Os tópicos de referência a seguir fornecem a você mais informações sobre como controlar o acesso ao seu Hub IoT.

## <a name="tags-and-properties-format"></a>Formato de marcas e propriedades
Marcações, propriedades desejadas e reportadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON são cadeias de caracteres UNICODE UTF-8 de 64 bytes que diferenciam maiúsculas de minúsculas. Os caracteres permitidos excluir caracteres de controle UNICODE (segmentos C0 e C1) e `'.'`, `' '` e `'$'`.
* Todos os valores em objetos JSON podem ser dos seguintes tipos de JSON: booliano, número, cadeia de caracteres, objeto. Não há permissão para matrizes.
* Todos os objetos JSON em marcações, propriedades desejadas e reportadas podem ter uma profundidade máxima de 5. Por exemplo, o seguinte objeto é válido:

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

* Todos os valores de cadeia de caracteres podem ter no máximo 512 bytes de comprimento.

## <a name="device-twin-size"></a>Tamanho do dispositivo gêmeo
O Hub IoT impõe um limite de tamanho de 8 KB nos valores de `tags`, `properties/desired` e `properties/reported`, excluindo elementos somente leitura.
O tamanho é calculado pela contagem de todos os caracteres, exceto caracteres de controle UNICODE (segmentos C0 e C1) e espaço `' '`, quando aparece fora de uma constante de cadeia de caracteres.
O Hub IoT rejeita com erro todas as operações que podem aumentar o tamanho desses documentos acima do limite.

## <a name="device-twin-metadata"></a>Metadados do dispositivo gêmeo
O Hub IoT mantém o carimbo de data e hora da última atualização de cada objeto JSON nas propriedades desejadas e reportadas do dispositivo gêmeo. Os carimbos de data e hora estão em UTC e são codificados no formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

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
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
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

Essas informações são armazenadas em cada nível (não apenas nas folhas da estrutura JSON) a fim de preservar as atualizações que removem chaves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidade otimista
Marcas, propriedades desejadas e reportadas oferecem suporte à simultaneidade otimista.
Marcas tem uma Etag, de acordo com [RFC7232], que representa a representação JSON da marca. Você pode usar ETags em operações de atualização condicionais do back-end da solução para garantir a consistência.

As propriedades desejadas e reportadas do dispositivo gêmeo não têm as ETags, mas um valor `$version` com garantia de ser incremental. De forma semelhante a uma ETag, a versão pode ser usada pela parte de atualização para impor a consistência das atualizações. Por exemplo, um aplicativo de dispositivo para uma propriedade relatada ou o back-end de solução para uma propriedade desejada.

Versões também são úteis quando um agente observador (por exemplo, o aplicativo do dispositivo que observa as propriedades desejadas) deve reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A seção [Fluxo de reconexão do dispositivo][lnk-reconnection] fornece mais informações.

## <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo
O Hub IoT não preserva notificações de atualização de propriedades desejadas para dispositivos desconectados. Acontece que um dispositivo que está se conectando deve recuperar o documento completo de propriedades desejadas, além de se inscrever para receber notificações de atualização. Considerando a possibilidade de corridas entre notificações de atualização e recuperação completa, o seguinte fluxo deve ser garantido:

1. O aplicativo de dispositivo conecta-se a um Hub IoT.
2. O aplicativo de dispositivo se inscreve para as notificações de atualização de propriedades desejadas .
3. O aplicativo de dispositivo recupera o documento completo para as propriedades desejadas.

O aplicativo de dispositivo pode ignorar todas as notificações com `$version` menor ou igual à versão do documento recuperado completo. Essa abordagem é possível porque o Hub IoT garante que as versões sempre aumentam.

> [!NOTE]
> Essa lógica já está implementada nos [SDKs do dispositivo IoT do Azure][lnk-sdks]. Essa descrição será útil somente se o aplicativo de dispositivo não puder usar qualquer um dos SDKs do dispositivo IoT do Azure, e deve programar a interface MQTT diretamente.
> 
> 

## <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* O artigo [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* O artigo [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* O artigo [SDKs de dispositivo e serviço IoT do Azure][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* O artigo [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-query] descreve a linguagem de consulta do Hub IoT que você pode utilizar para recuperar informações do Hub IoT sobre seus dispositivos gêmeos e trabalhos.
* O artigo [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu sobre dispositivos gêmeos, pode ser interessante ler os seguintes tópicos do Guia do desenvolvedor do Hub IoT:

* [Invocar um método direto em um dispositivo][lnk-methods]
* [Agendar trabalhos em vários dispositivos][lnk-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelos seguintes tutoriais de Hub IoT:

* [Como usar o dispositivo gêmeo][lnk-twin-tutorial]
* [Como usar as propriedades do dispositivo gêmeo][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
