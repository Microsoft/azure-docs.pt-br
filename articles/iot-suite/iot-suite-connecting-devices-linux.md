---
title: Conectar um dispositivo usando o C no Linux | Microsoft Docs
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 9adbc9cc13f0b4cafa3a3a7703c46f8085b15232
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Criar e executar um cliente C de exemplo Linux
As etapas a seguir mostram como criar um aplicativo cliente que se comunica com a solução pré-configurada de monitoramento remoto. Esse aplicativo é escrito em C e compilado e executado no Ubuntu Linux.

Para concluir essas etapas, você precisa de um dispositivo em execução na versão 15.04 ou 15.10 do Ubuntu. Antes de continuar, instale os pacotes de pré-requisitos no seu dispositivo Ubuntu usando o seguinte comando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalar as bibliotecas de cliente no dispositivo
As bibliotecas de cliente do Hub IoT do Azure estão disponíveis como um pacote que você pode instalar em seu dispositivo Ubuntu usando o comando **apt-get** . Conclua as seguintes etapas para instalar o pacote que contém os arquivos de biblioteca e cabeçalho do cliente Hub IoT em seu computador Ubuntu:

1. Em um shell, adicione o repositório AzureIoT ao computador:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Instale o pacote azure-iot-sdk-c-dev
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Instalar o analisador JSON Parson
As bibliotecas de cliente do Hub IoT usam o analisador JSON Parson para analisar cargas de mensagem. Em uma pasta adequada no computador, clone o repositório Parson GitHub usando o seguinte comando:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Preparar seu projeto
No computador Ubuntu, crie uma pasta chamada **remote\_monitoring**. Na pasta **remote\_monitoring**:

- Crie os quatro arquivos **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h** e **CMakeLists.txt**.
- Crie uma pasta chamada **parson**.

Copie os arquivos **parson.c** e **parson.h** de sua cópia local do repositório Parson para a pasta **remote\_monitoring/parson**.

Em um editor de texto, abra o arquivo **remote\_monitoring.c**. Adicione as seguintes declarações de `#include` :
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Chamar a função remote\_monitoring\_run
Em um editor de texto, abra o arquivo **remote_monitoring.h**. Adicione os códigos a seguir:

```
void remote_monitoring_run(void);
```

Em um editor de texto, abra o arquivo **main.c** . Adicione os códigos a seguir:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo
As etapas a seguir descrevem como usar *CMake* para compilar o aplicativo cliente.

1. Em um editor de texto, abra o arquivo **CMakeLists.txt** na pasta **remote_monitoring**.

1. Adicione as seguintes instruções para definir como criar o aplicativo cliente:
   
    ```
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
1. Na pasta **remote_monitoring**, crie uma pasta para armazenar os arquivos *make* que CMake gera e execute os comandos **cmake** e **make** como se segue:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Execute o aplicativo cliente e envie telemetria ao Hub IoT:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

