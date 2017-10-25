---
title: "Conversão de dados no gateway IoT com o Azure IoT Edge | Microsoft Docs"
description: "Use o gateway IoT para converter o formato dos dados de sensor por meio de um módulo personalizado no Azure IoT Edge."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "conversão de dados do gateway iot, transformação de dados do gateway iot"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Usar o gateway IoT para transformação dos dados de sensor com o Azure IoT Edge

> [!NOTE]
> Antes de começar este tutorial, verifique se você concluiu as seguintes lições na sequência:
> * [Configurar a NUC Intel como um gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Usar o gateway IoT para conectar as coisas à nuvem – SensorTag para o Hub IoT do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Uma das finalidades de um gateway IoT é processar os dados coletados antes de enviá-los para a nuvem. O Azure IoT Edge apresenta os módulos que podem ser criados e montados para formar o fluxo de trabalho de processamento de dados. Um módulo recebe uma mensagem, executa alguma ação nela e, em seguida, move-a para que outros módulos a processem.

## <a name="what-you-learn"></a>O que você aprenderá

Você aprende a criar um módulo para converter mensagens do SensorTag em outro formato.

## <a name="what-you-do"></a>O que fazer

* Crie um módulo para converter uma mensagem recebida no formato .json.
* Compile o módulo.
* Adicione o módulo ao aplicativo de exemplo BLE no Azure IoT Edge.
* Execute o aplicativo de exemplo.

## <a name="what-you-need"></a>O que você precisa

* Os seguintes tutoriais concluídos em sequência:
  * [Configurar a NUC Intel como um gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Usar o gateway IoT para conectar as coisas à nuvem – SensorTag para o Hub IoT do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Um cliente SSH executado no computador host. Recomenda-se o PuTTY no Windows. O Linux e o macOS já vêm com um cliente SSH.
* Do endereço IP e do nome de usuário e a senha para acessar o gateway no cliente SSH.
* Uma conexão com a Internet.

## <a name="create-a-module"></a>Criar um módulo

1. No computador host, execute o cliente SSH e conecte-se ao gateway IoT.
1. Clone os arquivos de origem do módulo de conversão do GitHub para o diretório base do gateway IoT executando os seguintes comandos:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Esse é um módulo nativo do Azure Edge escrito na linguagem de programação C. O módulo converte o formato das mensagens recebidas no seguinte:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Compilar o módulo

Para compilar o módulo, execute os seguintes comandos:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Você obtém um arquivo `libmy_module.so` após a conclusão da compilação. Anote o caminho absoluto desse arquivo.

## <a name="add-the-module-to-the-ble-sample-application"></a>Adicionar o módulo ao aplicativo de exemplo BLE

1. Acesse a pasta de exemplos executando o seguinte comando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Abra o arquivo de configuração executando o seguinte comando:

   ```bash
   vi ble_gateway.json
   ```

1. Adicione um módulo inserindo o código a seguir à seção `modules`.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Substitua `[Your libmy_module.so path]` no código pelo caminho absoluto do arquivo libmy_module.so.
1. Substitua o código na seção `links` pelo seguinte:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Pressione `ESC` e, em seguida, digite `:wq` para salvar o arquivo.

## <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

1. Ligue o SensorTag.
1. Defina a variável de ambiente SSL_CERT_FILE executando o seguinte comando:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Execute o aplicativo de exemplo com o módulo adicionado executando o seguinte comando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Próximas etapas

Você usou o gateway IoT com êxito para converter a mensagem do SensorTag no formato .json.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
