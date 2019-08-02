---
title: Autoprovisionar dispositivos com DPS usando atestado de chave simétrica-Azure IoT Edge | Microsoft Docs
description: Usar o atestado de chave simétrica para testar o provisionamento automático de dispositivos para Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c21c0bdce6f6a5cd3c8f634bf400600b30a8ead
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414598"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Criar e provisionar um dispositivo IoT Edge usando o atestado de chave simétrica

Dispositivos do Azure IoT Edge podem ser autoprovisionados usando o [Serviço de Provisionamento de Dispositivo](../iot-dps/index.yml) assim como os dispositivos que não são habilitados de borda. Se você não estiver familiarizado com o processo de provisionamento automático, revise os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como criar um registro individual de serviço de provisionamento de dispositivos usando o atestado de chave simétrica em um dispositivo IoT Edge com as seguintes etapas:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um registro individual para o dispositivo.
* Instale o IoT Edge Runtime e conecte-se ao Hub IoT.

O atestado de chave simétrica é uma abordagem simples para autenticar o dispositivo com uma instância do serviço de provisionamento de dispositivos. Esse método de atestado representa uma experiência de "Olá, Mundo" para desenvolvedores que são novos no provisionamento de dispositivos ou não tem requisitos de segurança rígidos. O atestado de dispositivo usando um [TPM](../iot-dps/concepts-tpm-attestation.md) é mais seguro e deve ser usado para requisitos de segurança mais rígidos.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT ativo
* Um dispositivo físico ou virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o tempo de execução do Azure IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolher uma ID de registro exclusiva para o dispositivo

Uma ID de registro exclusiva deve ser definida para identificar cada dispositivo. Você pode usar o endereço MAC, o número de série ou qualquer informação exclusiva do dispositivo.

Neste exemplo, usamos uma combinação de um endereço MAC e o número de série que formam a seguinte cadeia de caracteres de uma ID de registro.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Crie uma ID de registro exclusiva para seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e traço ('-').

## <a name="create-a-dps-enrollment"></a>Criar um registro de DPS

Use a ID de registro do dispositivo para criar um registro individual no DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-monitor.md).

> [!TIP]
> Os registros de grupo também são possíveis ao usar o atestado de chave simétrica e envolvem as mesmas decisões que os registros individuais.

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

1. Em **Configurações**, selecione **Gerenciar registros**.

1. Selecione **adicionar registro individual**, em seguida, conclua as seguintes etapas para configurar o registro:  

   1. Para **mecanismo**, selecione **chave simétrica**.

   1. Marque a caixa de seleção **gerar chaves automaticamente** .

   1. Forneça a **ID de registro** que você criou para seu dispositivo.

   1. Forneça uma **ID de dispositivo do Hub IOT** para seu dispositivo, se desejar. Você pode usar IDs de dispositivo para um dispositivo individual para a implantação do módulo de destino. Se você não fornecer uma ID de dispositivo, a ID de registro será usada.

   1. Selecione **true** para declarar que o registro é para um dispositivo IOT Edge. Para um registro de grupo, todos os dispositivos devem ser IoT Edge dispositivos ou nenhum deles pode ser.

   1. Aceite o valor padrão da política de alocação do serviço de provisionamento de dispositivos para **saber como você deseja atribuir dispositivos a hubs** ou escolha um valor diferente que seja específico para esse registro.

   1. Escolha o **IoT Hub** vinculado que você deseja conectar o dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   1. Escolha **como você deseja que os dados do dispositivo sejam manipulados** ao reprovisionar quando os dispositivos solicitam o provisionamento após a primeira vez.

   1. Adicionar um valor de marca para o **estado inicial do dispositivo gêmeo** se desejar. Você pode usar marcas para grupos de dispositivos de destino para a implantação do módulo. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Verifique se a **entrada habilitar** está definida como **habilitar**.

   1. Clique em **Salvar**.

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Certifique-se de copiar o valor de **chave primária** do registro a ser usado ao criar a chave do dispositivo.

## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo

Seu dispositivo usa a chave de dispositivo derivada com sua ID de registro exclusiva para executar o atestado de chave simétrica com o registro durante o provisionamento. Para gerar a chave do dispositivo, use a chave que você copiou de seu registro de DPS para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro exclusiva para o dispositivo e converta o resultado no formato base64.

Não inclua a chave primária ou secundária do registro em seu código de dispositivo.

### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se estiver usando uma estação de trabalho do Linux, você poderá usar openssl para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela ID de registro do dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em Windows

Se estiver usando uma estação de trabalho baseada no Windows, você poderá usar o PowerShell para gerar sua chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor de **CHAVE** pela **Chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela ID de registro do dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instalar o tempo de execução do Azure IoT Edge

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor de **escopo da ID** de DPS
* A **ID de registro** do dispositivo que você criou
* A chave de dispositivo derivada do dispositivo para o atestado de chave simétrica

### <a name="linux-device"></a>Dispositivo Linux

Siga as instruções para a arquitetura do dispositivo. Certifique-se de configurar o tempo de execução do IoT Edge para provisionamento automático, não manual.

[Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

A seção no arquivo de configuração para provisionamento de chave simétrica tem esta aparência:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Substitua os valores de espaço `{scope_id}`reservado `{registration_id}`para, `{symmetric_key}` e pelos dados coletados anteriormente.

### <a name="windows-device"></a>Dispositivo Windows

Siga as instruções para instalar o IoT Edge tempo de execução no dispositivo para o qual você gerou uma chave de dispositivo derivada. Certifique-se de configurar o tempo de execução do IoT Edge para provisionamento automático, não manual.

[Instalar e provisionar automaticamente IoT Edge no Windows](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o tempo de execução foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o tempo de execução instalado e iniciado com êxito.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o status do serviço do IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Módulos de execução da lista.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada.

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-monitor.md) ou [usando a CLI do Azure](how-to-deploy-monitor-cli.md).
