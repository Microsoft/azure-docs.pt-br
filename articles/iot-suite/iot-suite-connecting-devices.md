---
title: Conectar um dispositivo usando C no Windows | Microsoft Docs
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d222bcbd64f288d4091acb0ecd2922b9ceee57e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Criar um exemplo de solução C no Windows
As etapas a seguir mostram como criar um aplicativo cliente que se comunica com a solução pré-configurada de monitoramento remoto. Esse aplicativo é escrito em C e compilado e executado no Windows.

Crie um projeto inicial no Visual Studio 2015 ou Visual Studio 2017 e adicione os pacotes NuGet de cliente do dispositivo Hub IoT:

1. No Visual Studio, crie um aplicativo de console C usando o modelo **Aplicativo do Console Win32** do Visual C++. Dê o nome de **RMDevice**ao projeto.
2. Na página **Configurações de Aplicativos** no **Assistente do Aplicativo Win32**, verifique se **Aplicativo do Console** está selecionado e desmarque **Cabeçalho pré-compilado** e **Verificações do Security Development Lifecycle (SDL)**.
3. No **Gerenciador de Soluções**, exclua os arquivos stdafx.h, targetver.h e stdafx.cpp.
4. No **Gerenciador de Soluções**, renomeie o arquivo RMDevice.cpp para RMDevice.c.
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Gerenciar Pacotes NuGet**. Clique em **Procurar**, para encontrar e instalar os seguintes pacotes NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Propriedades** para abrir a caixa de diálogo **Páginas de Propriedade**. Para obter detalhes, confira [Configurando as propriedades do projeto no Visual C++][lnk-c-project-properties]. 
7. Clique na pasta **Vinculador** e clique na página de propriedade **Entrada**.
8. Adicione **crypt32.lib** à propriedade **Dependências Adicionais**. Clique em **OK** e **OK** novamente para salvar os valores da propriedade do projeto.

Adicione a biblioteca Parson JSON ao projeto **RMDevice** e adicione as instruções `#include` necessárias:

1. Em uma pasta adequada no computador, clone o repositório Parson GitHub usando o seguinte comando:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Copie os arquivos parson.h e parson.c da cópia local do repositório Parson para a pasta **RMDevice** do projeto.

1. No Visual Studio, clique com o botão direito do mouse no projeto **RMDevice**, clique em **Adicionar** e em **Item Existente**.

1. Na caixa de diálogo **Adicionar Item Existente**, selecione os arquivos parson.h e parson.c na pasta do projeto **RMDevice**. Em seguida, clique em **Adicionar** para adicionar esses dois arquivos ao projeto.

1. No Visual Studio, abra o arquivo RMDevice.c. Substitua as instruções `#include` existentes pelo seguinte código:
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Agora você pode verificar se o projeto tem as dependências corretas configuradas compilando-o.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Adicione código para invocar a função **remote\_monitoring\_run** e compile e execute o aplicativo do dispositivo.

1. Substitua a função **main** pelo seguinte código para invocar a função **remote\_monitoring\_run**:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Clique em **Compilar** e em **Compilar Solução** para compilar o aplicativo do dispositivo.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice**, clique em **Depurar** e em **Iniciar nova instância** para executar o exemplo. O console exibe mensagens quando o aplicativo envia a telemetria de exemplo para a solução pré-configurada, recebe os valores de propriedade desejados definidos no painel de solução e responde aos métodos invocados por meio do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
