---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 94451cfefefe30bbae1748844f9303b2cfdd7be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612016"
---
Este tutorial mostra como criar um aplicativo de exemplo de dispositivo do IoT Plug and Play com componentes, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para ver as informações que ele envia ao hub. O aplicativo de exemplo é escrito no Python e é incluído no SDK do dispositivo de IoT do Azure para Python. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

Neste tutorial, você:

> [!div class="checklist"]
> * Baixe o código de exemplo.
> * Executará o aplicativo de dispositivo de exemplo e validará se ele se conecta ao seu hub IoT.
> * Examinará o código-fonte.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este tutorial, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/).

## <a name="download-the-code"></a>Baixar o código

O pacote **azure-iot-device** é publicado como um PIP.

No ambiente Python local, instale o pacote da seguinte maneira:

```cmd/sh
pip install azure-iot-device
```

Se você concluiu o [Início Rápido: Conecte um aplicativo de exemplo de dispositivo do IoT Plug and Play em execução no Windows ao Hub IoT (Python)](../articles/iot-pnp/quickstart-connect-device.md). Você já clonou o repositório.

Clone o repositório de IoT do SDK do Python:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo controlador de temperatura do IoT Plug and Play. O modelo que este exemplo implementa usa [vários componentes](../articles/iot-pnp/concepts-modeling-guide.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

A pasta *azure-iot-sdk-python\azure-iot-device\samples\pnp* contém o código de exemplo do dispositivo IoT Plug and Play. Os arquivos para o exemplo do controlador de temperatura são:

- temp_controller_with_thermostats.py
- pnp_helper.py

O controlador de temperatura tem vários componentes e um componente padrão, com base no modelo de DTDL do controlador de temperatura.

Abra o arquivo *temp_controller_with_thermostats.py* em um editor de sua escolha. O código neste arquivo:

1. Importa `pnp_helper.py` para obter acesso aos métodos auxiliares.

1. Define dois DTMIs (identificadores de modelo de gêmeos digitais) que representam com exclusividade duas interfaces diferentes, definidas no modelo de DTDL. Os componentes em um controlador de temperatura real devem implementar essas duas interfaces. Essas duas interfaces já estão publicadas em um repositório central. Esses DTMIs devem ser conhecidos do usuário e variar dependendo do cenário de implementação do dispositivo. Para o exemplo atual, essas duas interfaces representam:

    - Um termostato
    - Informações do dispositivo desenvolvidas pelo Azure.

1. Define o `model_id` do DTMI do dispositivo que está sendo implementado. O DTMI é definido pelo usuário e deve corresponder ao DTMI no arquivo de modelo de DTDL.

1. Define os nomes fornecidos aos componentes no arquivo DTDL. Há dois termostatos conectados no DTDL e um componente de informações do dispositivo. Uma constante chamada `serial_number` também é definida no componente padrão. Uma `serial_number` não pode ser alterada em um dispositivo.

1. Define implementações do manipulador de comando. Elas definem o que o dispositivo faz quando recebe solicitações de comando.

1. Define funções para criar uma resposta de comando. Elas definem como o dispositivo responde com as solicitações de comando. Você cria funções de resposta de comando se um comando precisa enviar uma resposta personalizada de volta para o hub IoT. Se uma função de resposta para um comando não for fornecida, uma resposta genérica será enviada. Neste exemplo, somente o comando **getMaxMinReport** tem uma resposta personalizada.

1. Define uma função para enviar telemetria deste dispositivo. Tanto os termostatos quanto o componente padrão enviam telemetria. Essa função usa um parâmetro de nome de componente opcional para habilitá-la a identificar qual componente enviou a telemetria.

1. Define um ouvinte para solicitações de comando.

1. Define um ouvinte para as atualizações de propriedade desejadas.

1. Tem uma função `main` que:

    - Usa o SDK do dispositivo para criar um cliente de dispositivo e conectar-se ao hub IoT. O dispositivo envia a `model_id` para que o hub IoT possa identificar o dispositivo como um dispositivo do IoT Plug and Play.

    - Usa a função `create_reported_properties` no arquivo auxiliar para criar as propriedades. Passe o nome do componente e as propriedades como pares chave-valor para essa função.

    - Atualiza as propriedades legíveis para seus componentes chamando `patch_twin_reported_properties`.

    - Inicia a escuta de solicitações de comando usando a função `execute_command_listener`. A função configura um ouvinte para solicitações de comando do serviço. Ao configurar o ouvinte, você fornece um `method_name`, `user_command_handler`e um `create_user_response_handler` opcional como parâmetros.
        - O `method_name` define a solicitação de comando. Neste exemplo, o modelo define os comandos **reboot** e **getMaxMinReport**.
        - A função `user_command_handler` define o que o dispositivo deve fazer quando recebe um comando.
        - A função `create_user_response_handler` cria uma resposta a ser enviada ao hub IoT quando um comando é executado com êxito. Você pode vê-la no portal. Se essa função não for fornecida, uma resposta genérica será enviada ao serviço.

    - Usa `execute_property_listener` para escutar atualizações de propriedade.

    - Inicia o envio de telemetria usando `send_telemetry`. O código de exemplo usa um loop para chamar três funções de envio de telemetria. Cada uma é chamada a cada oito segundos

    - Desabilita todos os ouvintes e tarefas e sai do loop quando você pressiona **Q** ou **q**.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Use o seguinte comando para executar a amostra:

```cmd/sh
python temp_controller_with_thermostats.py
```

O dispositivo de exemplo envia mensagens de telemetria em intervalos de alguns segundos para o hub IoT.

Você vê a saída a seguir, que indica que o dispositivo está enviando dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade.

![Mensagens de confirmação do dispositivo](media/iot-pnp-multiple-components-python/multiple-component.png)

Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
