---
title: Declarar módulos e rotas com manifestos de implantação – Azure IoT Edge | Microsoft Docs
description: Saiba como um manifesto de implantação declara quais módulos para implantar, como implantá-las e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 03/28/2019
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4a562cab445398986c1b8f379f6cb90ca843342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363138"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Aprenda a implantar módulos e estabelecer rotas no IoT Edge

Cada dispositivo IoT Edge executa ao menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do tempo de execução do IoT Edge. Além disso, qualquer dispositivo IoT Edge pode executar vários módulos para executar qualquer número de processos. Você implanta todos esses módulos em um dispositivo ao mesmo tempo, portanto, o IoT Edge fornece uma maneira de declarar quais módulos instalar e como configurá-los para funcionar em conjunto. 

O *manifesto de implantação* é um documento JSON que descreve:

* A configuração do agente **IoT Edge**, que inclui a imagem de contêiner para cada módulo, as credenciais para registros de contêiner privado de acesso e instruções sobre como cada módulo deve ser criado e gerenciado.
* O módulo gêmeo do **hub do IoT Edge**, que inclui como as mensagens fluem entre os módulos e, eventualmente, o Hub IoT.
* Especifique, as propriedades desejadas do módulo gêmeo.

Todos os dispositivos IoT Edge precisam ser configurados com um manifesto de implantação. Um tempo de execução do IoT Edge recém-instalado relatará um código de erro até ser configurado com um manifesto válido. 

Nos tutoriais do Azure IoT Edge, você pode criar um manifesto de implantação por meio de um assistente no portal do Azure IoT Edge. Você também pode aplicar um manifesto de implantação por meio de programação usando REST ou o SDK do serviço Hub IoT. Para saber mais, veja [Noções básicas sobre implantações do IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Em um alto nível, um manifesto de implantação é uma lista de módulos gêmeos que são configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais módulos instalar e como configurá-los. Manifestos de implantação incluem as *propriedades desejadas* para cada módulo gêmeo. Dispositivos do IoT Edge relatam de volta *as propriedades relatadas* para cada módulo. 

Dois módulos são necessários em cada manifesto de implantação: `$edgeAgent`, e `$edgeHub`. Esses módulos são parte do tempo de execução do IoT Edge que gerencia o dispositivo IoT Edge e os módulos em execução nele. Para obter mais informações sobre esses módulos, consulte [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Além dos dois módulos de tempo de execução, você pode adicionar até 20 módulos de sua preferência para ser executado em um dispositivo IoT Edge. 

Um manifesto de implantação que contém apenas o tempo de execução do IoT Edge (edgeAgent e edgeHub) é válido.

Manifestos de implantação seguem essa estrutura:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurar módulos

Definir o tempo de execução IoT Edge como instalar os módulos na sua implantação. O agente do IoT Edge é o componente de tempo de execução que gerencia a instalação, atualizações e relatório de status para um dispositivo IoT Edge. Portanto, o módulo gêmeo $edgeAgent requer a configuração e informações de gerenciamento para todos os módulos. Essas informações incluem os parâmetros de configuração para o próprio agente do IoT Edge. 

Para obter uma lista completa das propriedades que podem ou devem ser incluídos, consulte [propriedades do agente do IoT Edge e hub do IoT Edge](module-edgeagent-edgehub.md).

As propriedades de $edgeAgent seguem esta estrutura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Declarar rotas

O hub do IoT Edge gerencia a comunicação entre módulos, o Hub IoT e todos os dispositivos de folha. Portanto, o módulo gêmeo $edgeHub contém propriedade desejada chamada *rotas* que declara como as mensagens são passadas em uma implantação. Você pode ter várias rotas dentro da mesma implantação.

As rotas são declaradas nas propriedades desejadas do **$edgeHub** com a seguinte sintaxe:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Cada rota precisa de uma origem e um coletor, mas a condição é uma parte opcional que você pode usar para filtrar mensagens. 


### <a name="source"></a>Fonte

A origem especifica de onde as mensagens são provenientes. O IoT Edge pode rotear as mensagens de dispositivos de folha ou módulos.

A propriedade da fonte pode ser qualquer um dos valores a seguir:

| Fonte | DESCRIÇÃO |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a nuvem ou gêmeo alteração as notificações de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração de gêmeo (propriedades relatadas) proveniente de qualquer dispositivo de módulo ou folha |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por módulo ou dispositivo de folha por meio de algumas ou nenhuma saída |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de uma saída específica |

### <a name="condition"></a>Condição
A condição é opcional em uma declaração de rota. Se você deseja passar todas as mensagens do coletor para a fonte, deixe-o inteiramente fora da cláusula **WHERE**. Ou você pode usar a [linguagem de consulta de Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que satisfazem a condição. As rotas do IoT Edge não dão suporte a mensagens de filtragem com base em propriedades ou marcas do gêmeo. 

As mensagens que passam entre módulos no IoT Edge são formatadas da mesma forma que as mensagens que passam entre seus dispositivos e o Hub IoT do Azure. Todas as mensagens são formatadas como JSON e têm os parâmetros **systemProperties**, **appProperties** e **body**. 

Você pode criar consultas baseadas nos três parâmetros com a seguinte sintaxe: 

* Propriedades do sistema: `$<propertyName>` ou `{$<propertyName>}`
* Propriedades do aplicativo: `<propertyName>`
* Propriedades do corpo: `$body.<propertyName>` 

Para obter exemplos sobre como criar consultas para propriedades de mensagem, consulte [Expressões de consulta de rotas de mensagem do dispositivo para a nuvem](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Um exemplo específico do IoT Edge é quando você deseja filtrar as mensagens que chegaram a um dispositivo de gateway vindo de um dispositivo de folha. Mensagens que chegam de módulos incluem uma propriedade do sistema chamada **connectionModuleId**. Então se você quiser rotear mensagens de dispositivos de folha diretamente ao Hub IoT, use a rota a seguir para excluir mensagens de módulo:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Coletor
O coletor define onde as mensagens são enviadas. Somente os módulos e o Hub IoT podem receber mensagens. As mensagens não podem ser roteadas para outros dispositivos. Não há nenhuma opção de curinga na propriedade do coletor. 

A propriedade da fonte pode ser qualquer um dos valores a seguir:

| Coletor | DESCRIÇÃO |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Enviar a mensagem para uma entrada específica de um módulo específico |

IoT Edge fornece garantias at-least-once. O hub do IoT Edge armazena as mensagens localmente no caso de uma rota não é possível entregar a mensagem para o coletor. Por exemplo, se o hub do IoT Edge não pode se conectar ao IoT Hub ou o módulo de destino não está conectado.

Hub do IoT Edge armazena as mensagens até o tempo especificado na `storeAndForwardConfiguration.timeToLiveSecs` propriedade do [propriedades desejadas do hub do IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definir ou atualizar as propriedades desejadas 

O manifesto de implantação especifica as propriedades desejadas para cada módulo implantado ao dispositivo do IoT Edge. Quando as propriedades no manifesto de implantação substituem qualquer propriedade desejada atualmente no gêmeo do módulo.

Se você não especificar as propriedades desejadas de um gêmeo de módulo no manifesto de implantação, o Hub IoT não modifica o gêmeo de módulo de qualquer forma e você poderá definir as propriedades desejadas programaticamente. Em vez disso, você pode definir as propriedades desejadas programaticamente.

Os mesmos mecanismos que permitem que você modifique dispositivos gêmeos são usados para modificar módulos gêmeos. Para obter mais informações, consulte o [guia do desenvolvedor do módulo gêmeo](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exemplo de manifesto de implantação

O exemplo a seguir mostra o que pode ser a aparência de um documento de manifesto de implantação válido.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de propriedades que podem ou devem ser incluídos na $edgeAgent e $edgeHub, consulte [propriedades do agente do IoT Edge e hub do IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos do IoT Edge são usados, [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
