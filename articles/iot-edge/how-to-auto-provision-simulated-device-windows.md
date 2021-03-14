---
title: Provisionar dispositivos Windows automaticamente com DPS-Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo simulado em seu computador Windows para testar o provisionamento automático de dispositivos para o Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 11279c552cb599c24b72473d6574175450ca7ab0
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200845"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Criar e provisionar um dispositivo de IoT Edge simulado com um TPM virtual no Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge dispositivos podem ser provisionados automaticamente usando o serviço de [provisionamento de dispositivos](../iot-dps/index.yml) , assim como os dispositivos que não estão habilitados para a borda. Se você não estiver familiarizado com o processo de provisionamento automático, examine a visão geral de [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

O DPS dá suporte a atestado de chave simétrica para dispositivos IoT Edge no registro individual e no registro de grupo. Para o registro de grupo, se você marcar a opção "é IoT Edge dispositivo" como verdadeira no atestado de chave simétrica, todos os dispositivos registrados nesse grupo de registro serão marcados como IoT Edge dispositivos.

Este artigo mostra como testar o provisionamento automático em um dispositivo IoT Edge simulado com as seguintes etapas:

* Criar uma nova instância para o Serviço de Provisionamento de Dispositivos (DPS) no Hub IoT.
* Crie um dispositivo simulado em seu computador Windows com um simulado Trusted Platform Module (TPM) para segurança de hardware.
* Crie um registro individual para o dispositivo.
* Instale o runtime do IoT Edge e conecte o dispositivo ao Hub IoT.

> [!TIP]
> Este artigo descreve como testar o provisionamento automático usando o atestado de TPM em dispositivos virtuais, mas grande parte dele se aplica ao usar o hardware TPM físico também.

## <a name="prerequisites"></a>Pré-requisitos

* Computador de desenvolvimento do Windows. Este artigo usa Windows 10.
* Um Hub IoT ativo.

> [!NOTE]
> O TPM 2,0 é necessário ao usar o atestado de TPM com o DPS e só pode ser usado para criar registros individuais, não de grupo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT

Criar uma nova instância do Serviço de Provisionamento de Dispositivos no Hub IoT no Microsoft Azure e vincular ao seu hub IoT. Você pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de executar o Serviço de Provisionamento de Dispositivo, copie o valor do **Escopo de ID** da página de visão geral. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

> [!TIP]
> Se você estiver usando um dispositivo TPM físico, você precisará determinar a **chave de endosso**, que é exclusiva de cada chip TPM e é obtida do fabricante do chip TPM associado a ela. Você pode derivar uma **ID de registro** exclusiva para seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções no artigo [como gerenciar registros de dispositivo com o portal do Azure](../iot-dps/how-to-manage-enrollments.md) para criar seu registro no DPS e continue com a seção [instalar o IOT Edge Runtime](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado em sua máquina de desenvolvimento do Windows. Recupere a **ID de registro** e a **chave de endosso** para seu dispositivo e use-as para criar uma entrada de registro individual no DPS.

Ao criar uma inscrição no DPS, tem a oportunidade de declarar um **Estado inicial do dispositivo duplo**. No dispositivo gêmeo, você pode definir tags para agrupar dispositivos por qualquer métrica que precisar em sua solução, como região, ambiente, local ou tipo de dispositivo. Essas marcas são usadas para criar [implantações automáticas](how-to-deploy-at-scale.md).

Escolha o idioma SDK que você deseja usar para criar o dispositivo simulado e siga as etapas até que você crie o registro individual.

Ao criar o registro individual, selecione **true** para declarar que o dispositivo TPM simulado em seu computador de desenvolvimento do Windows é um **dispositivo IOT Edge**.

> [!TIP]
> No CLI do Azure, você pode criar um [registro](/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um [grupo de registro](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usar o sinalizador **habilitado para borda** para especificar que um dispositivo ou grupo de dispositivos é um dispositivo IOT Edge.

Dispositivo simulado e guias de inscrição individuais:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar o registro individual, salve o valor do **ID de registro**. Você usa esse valor ao configurar o runtime do Azure IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do Azure IoT Edge

O runtime do IoT Edge é implantado em todos os dispositivos IoT Edge. Seus componentes são executados em contêineres e permitem implantar contêineres adicionais no dispositivo para que você possa executar o código na borda. Instale o IoT Edge tempo de execução no dispositivo que está executando o TPM simulado.

Siga as etapas em [instalar o Azure IOT Edge Runtime](how-to-install-iot-edge.md)e, em seguida, retorne a este artigo para provisionar o dispositivo.

> [!TIP]
> Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste.

## <a name="configure-the-device-with-provisioning-information"></a>Configurar o dispositivo com informações de provisionamento

Depois que o tempo de execução for instalado em seu dispositivo, configure o dispositivo com as informações que ele usa para se conectar ao serviço de provisionamento de dispositivos e ao Hub IoT.

1. Conheça o **escopo da ID** de DPS e a **ID de registro** do dispositivo que foram coletadas nas seções anteriores.

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o runtime do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. Use o `-Dps` sinalizador para usar o serviço de provisionamento de dispositivos em vez do provisionamento manual.

   Substitua os valores de espaço reservado para `{scope_id}` e `{registration_id}` pelos dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Se o runtime foi iniciado com êxito, você pode entrar em seu Hub IoT e iniciar a implantação de módulos do IoT Edge em seu dispositivo. Use os seguintes comandos em seu dispositivo para verificar o runtime instalado e iniciado com êxito.  

Verifique o status do serviço do IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista.

```powershell
iotedge list
```

## <a name="next-steps"></a>Próximas etapas

O processo de registro do serviço de provisionamento de dispositivo permite definir a ID do dispositivo e as marcas do dispositivo gêmeo ao mesmo tempo, como provisionar o novo dispositivo. Você pode usar esses valores para dispositivos individuais ou grupos de dispositivos usando o gerenciamento automático de dispositivo de destino. Saiba como [Implantar e monitorar os módulos de IoT Edge em escala usando o portal do Azure](how-to-deploy-at-scale.md) ou [usando a CLI do Azure](how-to-deploy-cli-at-scale.md)
