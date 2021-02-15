---
title: Implantar módulos e rotas com manifestos de implantação – Azure IoT Edge
description: Saiba como um manifesto de implantação declara quais módulos para implantar, como implantá-las e como criar rotas de mensagens entre eles.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406420fcd517ceda8ea6eedfc955f54b15541f74
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366595"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Aprenda a implantar módulos e estabelecer rotas no IoT Edge

Cada dispositivo IoT Edge executa ao menos dois módulos: $edgeAgent e $edgeHub, que fazem parte do runtime do IoT Edge. O dispositivo do IoT Edge pode executar vários módulos adicionais para qualquer quantidade de processos. Use um manifesto de implantação para informar ao seu dispositivo quais módulos instalar e como configurá-los para que eles funcionem em conjunto.

O *manifesto de implantação* é um documento JSON que descreve:

* O módulo gêmeo do **agente do IoT Edge**, que inclui três componentes:
  * A imagem de contêiner para cada módulo que é executado no dispositivo.
  * As credenciais para acessar os registros de contêiner privado que contêm as imagens de módulo.
  * As instruções sobre como cada módulo deve ser criado e gerenciado.
* O módulo gêmeo do **hub do IoT Edge**, que inclui como as mensagens fluem entre os módulos e, eventualmente, o Hub IoT.
* As propriedades desejadas de qualquer módulo gêmeo adicional (opcional).

Todos os dispositivos IoT Edge precisam ser configurados com um manifesto de implantação. Um runtime do IoT Edge recém-instalado relatará um código de erro até ser configurado com um manifesto válido.

Nos tutoriais do Azure IoT Edge, você pode criar um manifesto de implantação por meio de um assistente no portal do Azure IoT Edge. Você também pode aplicar um manifesto de implantação por meio de programação usando REST ou o SDK do serviço Hub IoT. Para saber mais, veja [Noções básicas sobre implantações do IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Criar um manifesto de implantação

Em um alto nível, um manifesto de implantação é uma lista de módulos gêmeos que são configurados com suas propriedades desejadas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos), quais módulos instalar e como configurá-los. Manifestos de implantação incluem as *propriedades desejadas* para cada módulo gêmeo. Dispositivos do IoT Edge relatam de volta *as propriedades relatadas* para cada módulo.

Dois módulos são necessários em cada manifesto de implantação: `$edgeAgent`, e `$edgeHub`. Esses módulos são parte do runtime do IoT Edge que gerencia o dispositivo IoT Edge e os módulos em execução nele. Para obter mais informações sobre esses módulos, consulte [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Além dos dois módulos de tempo de execução, você pode adicionar até 50 módulos próprios para serem executados em um dispositivo IoT Edge.

Um manifesto de implantação que contém apenas o runtime do IoT Edge (edgeAgent e edgeHub) é válido.

Manifestos de implantação seguem essa estrutura:

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
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
    }
  }
}
```

## <a name="configure-modules"></a>Configurar módulos

Definir como o runtime do IoT Edge instala os módulos na sua implantação. O agente do IoT Edge é o componente de runtime que gerencia a instalação, atualizações e relatório de status para um dispositivo IoT Edge. Portanto, o módulo gêmeo $edgeAgent contém a configuração e as informações de gerenciamento de todos os módulos. Essas informações incluem os parâmetros de configuração do próprio agente do IoT Edge.

As propriedades de $edgeAgent seguem esta estrutura:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
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
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

A versão 1,1 do esquema do agente do IoT Edge foi lançada junto com IoT Edge versão 1.0.10 e habilita a ordem de inicialização do módulo. A versão de esquema 1,1 é recomendada para qualquer implantação de IoT Edge que executa a versão 1.0.10 ou posterior.

### <a name="module-configuration-and-management"></a>Configuração e gerenciamento de módulo

A lista de propriedades desejadas do agente de IoT Edge é onde você define quais módulos são implantados em um dispositivo IoT Edge e como eles devem ser configurados e gerenciados.

Para obter uma lista completa das propriedades desejadas que podem ou devem ser incluídas, consulte [Propriedades do agente de IOT Edge e do hub de IOT Edge](module-edgeagent-edgehub.md).

Por exemplo:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

Cada módulo tem uma propriedade de **configurações** que contém a **imagem** do módulo, um endereço para a imagem de contêiner em um registro de contêiner e qualquer **criaroptions** para configurar a imagem na inicialização. Para obter mais informações, consulte [como configurar opções de criação de contêiner para módulos de IOT Edge](how-to-use-create-options.md).

O módulo edgeHub e os módulos personalizados também têm três propriedades que dizem ao agente de IoT Edge como gerenciá-los:

* **Status**: se o módulo deve estar em execução ou parado quando for implantado pela primeira vez. Obrigatório.
* **RestartPolicy**: quando e se o agente de IOT Edge deverá reiniciar o módulo se ele parar. Obrigatório.
* **StartupOrder**: *introduzido na versão do IOT Edge 1.0.10.* Qual ordem o agente de IoT Edge deve iniciar os módulos quando implantados pela primeira vez. A ordem é declarada com inteiros, em que um módulo, dado um valor de inicialização 0, é iniciado primeiro e, em seguida, os números mais altos são seguidos. O módulo edgeAgent não tem um valor de inicialização porque ele sempre é iniciado primeiro. Opcional.

  O agente de IoT Edge inicia os módulos na ordem do valor de inicialização, mas não aguarda a conclusão de cada módulo antes de ir para o próximo.

  A ordem de inicialização será útil se alguns módulos dependerem de outros. Por exemplo, talvez você queira que o módulo edgeHub inicie primeiro para que ele esteja pronto para rotear mensagens quando os outros módulos forem iniciados. Ou talvez você queira iniciar um módulo de armazenamento antes dos módulos que enviam dados para ele. No entanto, você sempre deve criar seus módulos para lidar com falhas de outros módulos. É a natureza dos contêineres que eles podem parar e reiniciar a qualquer momento e várias vezes.

## <a name="declare-routes"></a>Declarar rotas

O hub do IoT Edge gerencia a comunicação entre módulos, o Hub IoT e todos os dispositivos de folha. Portanto, o módulo gêmeo $edgeHub contém propriedade desejada chamada *rotas* que declara como as mensagens são passadas em uma implantação. Você pode ter várias rotas dentro da mesma implantação.

As rotas são declaradas nas propriedades desejadas do **$edgeHub** com a seguinte sintaxe:

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

O esquema de Hub de IoT Edge versão 1,1 foi lançado junto com IoT Edge versão 1.0.10 e habilita a priorização de rota e vida útil. A versão de esquema 1,1 é recomendada para qualquer implantação de IoT Edge que executa a versão 1.0.10 ou posterior.

Cada rota precisa de uma *fonte* na qual as mensagens são provenientes e de um *coletor* no qual as mensagens vão. A *condição* é uma parte opcional que você pode usar para filtrar mensagens.

Você pode atribuir *prioridade* às rotas que deseja garantir que o processe suas mensagens primeiro. Esse recurso é útil em cenários em que a conexão upstream é fraca ou limitada e você tem dados críticos que devem ser priorizados em relação a mensagens de telemetria padrão.

### <a name="source"></a>Fonte

A origem especifica de onde as mensagens são provenientes. O IoT Edge pode rotear as mensagens de módulos ou dispositivos folha.

Usando os SDKs de IoT, os módulos podem declarar filas de saída específicas para as mensagens deles usando a classe ModuleClient. As filas de saída não são necessárias, mas são úteis para gerenciar várias rotas. Os dispositivos folha podem usar a classe DeviceClient dos SDKs de IoT para enviar mensagens para dispositivos de gateway do IoT Edge da mesma forma que eles enviariam mensagens ao Hub IoT. Para obter mais informações, consulte [Noções básicas e uso de SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).

A propriedade da fonte pode ser qualquer um dos valores a seguir:

| Fonte | Descrição |
| ------ | ----------- |
| `/*` | Todas as mensagens do dispositivo para a nuvem ou gêmeo alteração as notificações de qualquer módulo ou dispositivo de folha |
| `/twinChangeNotifications` | Qualquer alteração de gêmeo (propriedades relatadas) proveniente de qualquer dispositivo de módulo ou folha |
| `/messages/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de alguma ou nenhuma saída ou por um dispositivo folha |
| `/messages/modules/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de algumas ou nenhuma saída |
| `/messages/modules/<moduleId>/outputs/*` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de alguma saída |
| `/messages/modules/<moduleId>/outputs/<output>` | Qualquer mensagem de dispositivo para nuvem enviada por um módulo específico por meio de uma saída específica |

### <a name="condition"></a>Condição

A condição é opcional em uma declaração de rota. Se você deseja transmitir todas as mensagens da fonte para o coletor, omita completamente a cláusula **WHERE**. Ou você pode usar a [linguagem de consulta de Hub IoT](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar determinadas mensagens ou tipos de mensagem que satisfazem a condição. As rotas do IoT Edge não dão suporte a mensagens de filtragem com base em propriedades ou marcas do gêmeo.

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

| Coletor | Descrição |
| ---- | ----------- |
| `$upstream` | Enviar a mensagem para o Hub IoT |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Enviar a mensagem para uma entrada específica de um módulo específico |

IoT Edge fornece garantias at-least-once. O hub do IoT Edge armazena mensagens localmente caso uma rota não consiga entregar a mensagem para o coletor. Por exemplo, se o hub do IoT Edge não puder se conectar ao Hub IoT ou se o módulo de destino não estiver conectado.

O hub do IoT Edge armazena as mensagens até a hora especificada na propriedade `storeAndForwardConfiguration.timeToLiveSecs` das [propriedades desejadas do hub do IoT Edge](module-edgeagent-edgehub.md).

### <a name="priority-and-time-to-live"></a>Prioridade e vida útil

As rotas podem ser declaradas com apenas uma cadeia de caracteres que define a rota ou como um objeto que usa uma cadeia de caracteres de rota, um inteiro de prioridade e um inteiro de vida útil.

Opção 1:

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

Opção 2, introduzida no IoT Edge versão 1.0.10 com IoT Edge esquema de Hub versão 1,1:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

Os valores de **prioridade** podem ser 0-9, inclusivo, onde 0 é a prioridade mais alta. As mensagens são enfileiradas com base em seus pontos de extremidade. Todas as mensagens de prioridade 0 direcionadas a um ponto de extremidade específico serão processadas antes que qualquer mensagem de prioridade 1 direcionada ao mesmo ponto de extremidade seja processada e na linha. Se várias rotas para o mesmo ponto de extremidade tiverem a mesma prioridade, suas mensagens serão processadas de acordo com a primeira vez atendidas. Se nenhuma prioridade for especificada, a rota será atribuída à prioridade mais baixa.

A propriedade **timeToLiveSecs** herda seu valor de **storeAndForwardConfiguration** do Hub IOT Edge, a menos que seja definida explicitamente. O valor pode ser qualquer inteiro positivo.

Para obter informações detalhadas sobre como as filas de prioridade são gerenciadas, consulte a página de referência para [prioridade de rota e vida](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)útil.

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
        "schemaVersion": "1.1",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
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
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
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
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa das propriedades que podem ou precisam ser incluídas em $edgeAgent e $edgeHub, consulte [Propriedades do agente do IoT Edge e do hub do IoT Edge](module-edgeagent-edgehub.md).

* Agora que sabe como os módulos do IoT Edge são usados, [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge](module-development.md).
