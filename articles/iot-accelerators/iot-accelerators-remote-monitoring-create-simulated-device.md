---
title: Simulação de dispositivo com monitoramento remoto do IoT - Azure | Microsoft Docs
description: Este guia de instruções mostra como usar o simulador de dispositivo com o acelerador de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5044f8b85e59911633a4ffab509efc000948144a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65832591"
---
# <a name="create-and-test-a-new-simulated-device"></a>Criar e testar um novo dispositivo simulado

O acelerador de solução de Monitoramento Remoto permite que você defina seus próprios dispositivos simulados. Este artigo mostra como definir um novo dispositivo de lâmpada simulado e, em seguida, testá-lo localmente. O acelerador de solução inclui dispositivos simulados, como resfriadores e caminhões. No entanto, você pode definir seus próprios dispositivos simulados para testar as soluções de IoT antes de implantar dispositivos reais.

> [!NOTE]
> Este artigo descreve como usar dispositivos simulados hospedados no serviço de simulação do dispositivo. Se você quer criar um dispositivo real, confira [Conectar seu dispositivo ao acelerador de solução de Monitoramento Remoto](iot-accelerators-connecting-devices.md).

Este guia de instruções mostra como personalizar o microsserviço de simulação do dispositivo. Esse microsserviço faz parte do acelerador de solução de Monitoramento Remoto. Para mostrar os recursos de simulação do dispositivo, este guia de instruções usa dois cenários no aplicativo de IoT da Contoso:

No primeiro cenário, você adiciona um novo tipo de telemetria no tipo de dispositivo **Resfriador** existente da Contoso.

No segundo cenário, a Contoso deseja testar um novo dispositivo de lâmpada inteligente. Para executar os testes, você pode criar um novo dispositivo simulado com as seguintes características:

*Propriedades*

| NOME                     | Valores                      |
| ------------------------ | --------------------------- |
| Cor                    | White, Red, Blue            |
| Brilho               | 0 a 100                    |
| Vida útil restante estimada | Contagem regressiva de 10.000 horas |

*Telemetria*

A tabela a seguir mostra os dados que a lâmpada relata para a nuvem como um fluxo de dados:

| NOME   | Valores      |
| ------ | ----------- |
| Status | "on", "off" |
| Temperatura | Graus F |
| online | verdadeiro, falso |

> [!NOTE]
> O valor de telemetria **online** é obrigatório para simulada todos os tipos.

*Métodos*

A tabela a seguir mostra as ações com suporte no novo dispositivo:

| NOME        |
| ----------- |
| Ligar   |
| Desligar  |

*Estado inicial*

A tabela a seguir mostra o status inicial do dispositivo:

| NOME                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brilho inicial       | 75     |
| Vida útil restante inicial   | 10.000 |
| Status de telemetria inicial | "on"   |
| Temperatura da telemetria inicial | 200   |

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

* Visual Studio Code. Você pode [fazer o download do Visual Studio Code para Mac, Linux e Windows](https://code.visualstudio.com/download).
* .NET Core. Você pode fazer o download do [NET Core para Mac, Linux e Windows](https://www.microsoft.com/net/download).
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Você pode fazer o download do [Postman para Mac, Windows ou Linux](https://www.getpostman.com/apps).
* Um [hub IoT implantado na assinatura do Azure](../../articles/iot-hub/iot-hub-create-through-portal.md). Você precisa da cadeia de conexão do hub IoT para concluir as etapas neste guia. Você pode obter a cadeia de conexão no portal do Azure.
* Um banco de dados do Cosmos DB que usa a API SQL e que está configurado para [consistência forte](../../articles/cosmos-db/how-to-manage-database-account.md). Você precisa da cadeia de conexão do banco de dados do Cosmos DB para concluir as etapas neste guia. Você pode obter a cadeia de conexão no portal do Azure.

## <a name="prepare-your-development-environment"></a>Preparar seu ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar seu ambiente de desenvolvimento:

* Baixe a fonte para o microsserviço de simulação de dispositivo.
* Baixe a fonte para o microsserviço de adaptador de armazenamento.
* Execute o microsserviço de adaptador de armazenamento localmente.

As instruções neste artigo pressupõem que você está usando o Windows. Se você estiver usando outro sistema operacional, talvez você precise ajustar alguns dos caminhos de arquivo e comandos para se adequar ao seu ambiente.

### <a name="download-the-microservices"></a>Baixar os microsserviços

Baixe e descompacte os [microsserviços de monitoramento remoto](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) do GitHub para um local adequado em seu computador local. O artigo considera que o nome dessa pasta é **remote-monitoring-services-dotnet-master**.

Baixe e descompacte o [microsserviço de adaptador de armazenamento](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) do GitHub para um local adequado em seu computador local. O artigo considera que o nome dessa pasta é **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Executar o microsserviço de adaptador de armazenamento

Abra a pasta **remote-monitoring-services-dotnet-master\storage-adapter** no Visual Studio Code. Clique em qualquer botão **Restaurar** para corrigir todas as dependências não resolvidas.

Abra o **storage-adapter/WebService/appsettings.ini** do arquivo e atribuir a cadeia de conexão do Cosmos DB para o **documentDBConnectionString** variável.

Para executar o microsserviço localmente, clique em **Depurar > Iniciar Depuração**.

A janela **Terminal** no Visual Studio Code mostra a saída do microsserviço em execução, incluindo uma URL para a verificação de integridade do serviço Web: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Quando você navegar até esse endereço, o status deve ser "Okey: Ativo e em funcionamento".

Deixe o microsserviço de adaptador de armazenamento em execução nesta instância do Visual Studio Code enquanto você conclui as próximas etapas.

## <a name="modify-the-chiller"></a>Modificar o resfriador

Nesta seção, você adiciona um novo tipo de telemetria de **Temperatura Interna** ao tipo de dispositivo de **Resfriador** existente:

1. Crie uma nova pasta **C:\temp\devicemodels** em seu computador local.

1. Copie os seguintes arquivos para a nova pasta a partir da cópia baixada do microsserviço de simulação de dispositivo:

    | source | Destino |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Abra o arquivo **C:\temp\devicemodels\chiller-01.json**.

1. Na seção **InitialState**, adicione as duas definições a seguir:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Na matriz **Telemetria**, adicione a seguinte definição:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Salve o arquivo **C:\temp\devicemodels\chiller-01.json**.

1. Abra o arquivo **C:\temp\devicemodels\scripts\chiller-01-state.js**.

1. Adicione os seguintes campos à variável **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Atualize a função **principal** da seguinte maneira:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Salve o arquivo **C:\temp\devicemodels\scripts\chiller-01-state.js**.

## <a name="create-the-lightbulb"></a>Criar a lâmpada

Nesta seção, você define um novo tipo de dispositivo **Lâmpada**:

1. Crie um arquivo **C:\temp\devicemodels\lightbulb-01.json** e adicione o seguinte conteúdo:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Salve as alterações em **C:\temp\devicemodels\lightbulb-01.json**.

1. Crie um arquivo **C:\temp\devicemodels\scripts\lightbulb-01-state.js** e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Salve as alterações em **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Crie um arquivo **C:\temp\devicemodels\scripts\SwitchOn-method.js** e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Salve as alterações em **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Crie um arquivo **C:\temp\devicemodels\scripts\SwitchOff-method.js** e adicione o seguinte conteúdo:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Salve as alterações em **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Agora, você criou uma versão personalizada do tipo de dispositivo **Resfriador** e criou um novo tipo de dispositivo **Lâmpada**.

## <a name="test-the-devices"></a>Testar os dispositivos

Nesta seção, você testa os tipos de dispositivo que criou nas seções anteriores localmente.

### <a name="run-the-device-simulation-microservice"></a>Executar o microsserviço de simulação de dispositivo

Abra a pasta **device-simulation-dotnet-master** que você baixou do GitHub em uma nova instância do Visual Studio Code. Clique em qualquer botão **Restaurar** para corrigir todas as dependências não resolvidas.

Abra o **WebService/appsettings.ini** do arquivo e atribuir a cadeia de conexão do Cosmos DB para o **documentdb_connstring** variável e também modificar as configurações como segue:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Para executar o microsserviço localmente, clique em **Depurar > Iniciar Depuração**.

A janela **Terminal** no Visual Studio Code mostra a saída do microsserviço em execução.

Deixe o microsserviço de simulação de dispositivo em execução nesta instância do Visual Studio Code enquanto você conclui as próximas etapas.

### <a name="set-up-a-monitor-for-device-events"></a>Configurar um monitor para eventos de dispositivo

Nesta seção, você pode usar a CLI do Azure para configurar um monitor de eventos para exibir a telemetria enviada dos dispositivos conectados ao hub IoT.

O script a seguir pressupõe que o nome do hub IoT é **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Deixe o monitor de eventos em execução enquanto você testa os dispositivos simulados.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Criar uma simulação com o tipo de dispositivo de resfriador atualizado

Nesta seção, você pode usar a ferramenta Postman para solicitar o microsserviço de simulação de dispositivo para executar uma simulação usando o tipo de dispositivo de resfriador atualizado. Postman é uma ferramenta que permite que você envie solicitações REST para um serviço Web. Os arquivos de configuração do Postman necessários estão em sua cópia local do repositório **device-simulation-dotnet**.

Para configurar o Postman:

1. Abra o Postman no computador local.

1. Clique em **Arquivo > Importar**. Em seguida, clique em **Escolher Arquivos**.

1. Navegue até a pasta **device-simulation-dotnet-master/docs/postman**. Selecione **accelerator.postman_collection da solução Simulação de Dispositivo IoT do Azure** e **accelerator.postman_environment da solução Simulação de Dispositivo IoT do Azure** e clique em **Abrir**.

1. Expanda o **acelerador da solução Simulação de Dispositivo IoT do Azure** para as solicitações que você pode enviar.

1. Clique em **Nenhum Ambiente** e selecione **acelerador da solução Simulação de Dispositivo IoT do Azure**.

Agora, você tem uma coleção e o ambiente carregado em seu workspace de Postman que você pode usar para interagir com o microsserviço de simulação de dispositivo.

Para configurar e executar a simulação:

1. Na coleção de Postman, selecione **Criar simulação de resfriador modificado** e clique em **Enviar**. Essa solicitação cria quatro instâncias do tipo de dispositivo de resfriador simulado.

1. A saída do monitor de eventos na janela CLI do Azure mostra a telemetria dos dispositivos simulados, incluindo os novos valores **internal_temperature**.

Para parar a simulação, selecione a solicitação **Parar a simulação** no Postman e clique em **Enviar**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Criar uma simulação com o tipo de dispositivo de lâmpada

Nesta seção, você pode usar a ferramenta Postman para solicitar o microsserviço de simulação de dispositivo para executar uma simulação usando o tipo de dispositivo de lâmpada. Postman é uma ferramenta que permite que você envie solicitações REST para um serviço Web.

Para configurar e executar a simulação:

1. Na coleção de Postman, selecione **Criar simulação de lâmpada** e clique em **Enviar**. Essa solicitação cria duas instâncias do tipo de dispositivo de lâmpada simulado.

1. A saída do monitor de eventos na janela CLI do Azure mostra a telemetria das lâmpadas simuladas.

Para parar a simulação, selecione a solicitação **Parar a simulação** no Postman e clique em **Enviar**.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode parar de dois microsserviços em execução localmente em suas instâncias do Visual Studio Code (**Depurar > Parar Depuração**).

Se você não precisar mais instâncias do Hub IoT e do Cosmos DB, exclua-as de sua assinatura do Azure para evitar encargos desnecessários.

## <a name="next-steps"></a>Próximas etapas

Este guia mostrou como criar um tipo de dispositivo simulado personalizado e testá-lo executando o microsserviço de simulação do dispositivo localmente.

A próxima etapa recomendada é saber como implantar os tipos de dispositivos simulados personalizados no [Acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
