---
title: "Instruções do Azure - Como usar modelos de segurança de hardware diferentes com o SDK do Cliente de Serviço de Provisionamento de Dispositivos no Azure | Microsoft Docs"
description: "Instruções do Azure - Como usar modelos de segurança de hardware diferentes com o SDK do Cliente de Serviço de Provisionamento de Dispositivos no Azure"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 620d86b62cf43c3e1a5f7f5c724fcf00174f30e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Como usar Módulos de Segurança de Hardware diferentes com o SDK do Cliente do Serviço de Provisionamento de Dispositivos
Estas etapas mostram como usar [HSMs (Módulos de Segurança de Hardware)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)diferentes com o SDK do Cliente do Serviço de Provisionamento de Dispositivos em C usando dispositivo físico e simulador.  O serviço de provisionamento dá suporte a dois modos de autenticação: X**.**509 e TPM (Trusted Platform Module).

## <a name="prerequisites"></a>Pré-requisitos

Prepare o ambiente de desenvolvimento de acordo com a seção "Preparar o ambiente de desenvolvimento" no guia [Criar e provisionar dispositivo simulado] (./quick-create-simulated-device.md).

## <a name="enable-authentication-with-different-hsms"></a>Habilitar autenticação com HSMs diferentes

O modo de autenticação (X**.** 509 ou TPM) deve estar habilitado para o simulador ou dispositivo físico antes de poderem ser registrados no Portal do Azure.  Navegue até a pasta raiz para azure-iot-sdk-c.  Execute o comando especificado, dependendo do modo de autenticação que você escolher.

### <a name="use-x509-with-simulator"></a>Usar X**.**509 com simulador

O serviço de provisionamento é fornecido com o emulador DICE (Device Identity Composition Engine), que gera um certificado X**.**509 para autenticar o dispositivo.  Execute o seguinte comando para habilitar a autenticação X**.**509:

```
cmake -Ddps_auth_type=x509 ..
```

Informações sobre hardware com DICE podem ser encontradas [aqui](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Usar X**.** 509 com hardware

O serviço de provisionamento pode ser usado com X**.** 509 em outro hardware.  Uma interface entre o hardware e o SDK é necessária para estabelecer conexão.  Fale com o fabricante do HSM para obter informações sobre a interface.

### <a name="use-tpm"></a>Usar TPM

O serviço de provisionamento pode se conectar a chips de TPM de hardware Windows e Linux com token SAS.  Execute o comando a seguir para habilitar a autenticação do TPM:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Usar o TPM com simulador

Se você não tiver um dispositivo com chips TPM, poderá usar um simulador para fins de desenvolvimento no sistema operacional Windows.  Execute o seguinte comando para habilitar a autenticação de TPM e executar o simulador do TPM:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Criar o SDK
Crie o SDK antes de criar o registro do dispositivo.

### <a name="linux"></a>Linux
- Para criar o SDK do Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Para criar Binários de depuração, adicione a opção do CMake correspondente ao comando de geração de projeto, por exemplo:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Existem várias [opções de configuração do CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) disponíveis para criar o SDK. Por exemplo, você pode desabilitar uma das pilhas de protocolo disponível adicionando um argumento ao comando de geração de projeto do CMake:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Você também pode compilar e executar o teste de unidade:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Para criar o SDK do Windows, siga estas etapas para gerar arquivos de projeto:
    - Abra o “Prompt de comando do desenvolvedor para VS2015”
    - Execute os seguintes comandos do CMake na raiz do repositório:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Esse comando cria bibliotecas de x86. Para compilar para x64, modifique o argumento do gerador de cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Se a geração de projeto for concluída com êxito, você deverá ver um arquivo de solução do Visual Studio (.sln) na pasta `cmake`. Para construir o SDK:
    - Abra **cmake\azure_iot_sdks.sln** no Visual Studio e compile-o, **ou**
    - Execute o seguinte comando no prompt de comando usado para gerar os arquivos de projeto:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Para criar Binários de depuração, use o argumento correspondente do MSBuild: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Existem várias opções de configuração do CMake disponíveis para criar o SDK. Por exemplo, você pode desabilitar uma das pilhas de protocolo disponível adicionando um argumento ao comando de geração de projeto do CMake:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Você também pode compilar e executar o teste de unidade:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotecas a incluir
- Essas bibliotecas devem ser incluídas em seu SDK:
    - O serviço de provisionamento: dps_http_transport, dps_client, dps_security_client
    - Segurança do IoTHub: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>Criar uma entrada de registro de dispositivo no DPS

### <a name="tpm"></a>TPM
Se você estiver usando o TPM, siga as instruções em ["Criar e provisionar um dispositivo simulado usando o Serviço de Provisionamento de Dispositivos do Hub IoT"](./quick-create-simulated-device.md) para criar uma entrada de registro de dispositivo no DPS e simular a primeira inicialização.

### <a name="x509"></a>X**.** 509
1. Para registrar um dispositivo no serviço de provisionamento, você precisa anotar a Chave de Endosso e a ID de Registro para cada dispositivo, que são exibidas na Ferramenta de Provisionamento fornecida pelo SDK do Cliente. Execute o seguinte comando para imprimir o certificado de Autoridade de Certificação raiz (para grupos de registro) e o certificado do signatário (para registro individual):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Faça logon no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o DPS.
   - Registro individual do X**.**509: na folha de provisionamento de dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** guia e clique no botão **Adicionar** na parte superior. Selecione **X**. **509** como o *Mecanismo* de atestado de identidade e carregue o certificado do signatário conforme exigido pela folha. Uma vez concluído, clique no botão **Salvar**. 
   - Registro em grupo  X**.**509: na folha de provisionamento de dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros em grupo** e clique no botão **Adicionar** na parte superior. Selecione **X**. **509** como o *Mecanismo* de atestado de identidade, insira o nome do grupo e o nome da certificação e carregue o certificado de Autoridade de Certificação raiz conforme exigido pela folha. Uma vez concluído, clique no botão **Salvar**. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Conectar-se ao Hub IoT após a configuração

Depois que o dispositivo foi configurado com o serviço de provisionamento, essa API usará o modo de autenticação do HSM para se conectar ao Hub IoT: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
