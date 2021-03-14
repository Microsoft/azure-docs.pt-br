---
title: Instalar o Azure IoT Edge para Windows | Microsoft Docs
description: Instalar Azure IoT Edge para contêineres do Windows em dispositivos Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201641"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Instalar e gerenciar Azure IoT Edge com contêineres do Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Há duas etapas para configurar um dispositivo IoT Edge. A primeira etapa é instalar o tempo de execução e suas dependências. A segunda etapa é conectar o dispositivo à sua identidade na nuvem e configurar a autenticação com o Hub IoT.

Este artigo lista as etapas para instalar o Azure IoT Edge tempo de execução com contêineres do Windows. Se você pretende usar contêineres do Linux em um dispositivo Windows, consulte o artigo [Azure IOT Edge para Linux no Windows](how-to-install-iot-edge-on-windows.md) .

>[!NOTE]
>Azure IoT Edge com contêineres do Windows não terão suporte a partir da versão 1,2 do Azure IoT Edge.
>
>Considere o uso do novo método para executar IoT Edge em dispositivos Windows, [Azure IOT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo Windows

  IoT Edge com contêineres do Windows requer o Windows versão 1809/Build 17763, que é a mais recente [compilação de suporte a longo prazo do Windows](/windows/release-information/). Certifique-se de examinar a [lista de sistemas com suporte](support.md#operating-systems) para obter uma lista de SKUs com suporte.

* Uma [ID de dispositivo registrada](how-to-register-device.md)

  Se você registrou seu dispositivo com a autenticação de chave simétrica, tenha a cadeia de conexão do dispositivo pronta.

  Se você registrou seu dispositivo com a autenticação de certificado autoassinado X. 509, tenha pelo menos um dos certificados de identidade que você usou para registrar o dispositivo e sua chave privada privada correspondente disponível no seu dispositivo.

## <a name="install-a-container-engine"></a>Instalar um mecanismo de contêiner

Azure IoT Edge se baseia em um tempo de execução de contêiner compatível com OCI como o [Moby](https://github.com/moby/moby). Um mecanismo baseado em Moby que está incluído no script de instalação. Não há etapas adicionais para instalar o mecanismo.

## <a name="install-the-iot-edge-security-daemon"></a>Instalar o daemon de segurança do IoT Edge

1. Execute o PowerShell como administrador.

   Use uma sessão AMD64 do PowerShell, não o PowerShell (x86). Se não tiver certeza de qual tipo de sessão você está usando, execute o seguinte comando:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Execute o comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , que executa as seguintes tarefas:

   * Verifica se o computador Windows está em uma versão com suporte.
   * Ativa o recurso de contêineres.
   * Baixa o mecanismo Moby e o tempo de execução IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Reinicie o dispositivo, se solicitado.

Ao instalar o IoT Edge em um dispositivo, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Aponte o instalador para um diretório local para instalação offline.

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge com contêineres do Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionar o dispositivo com sua identidade de nuvem

Agora que o mecanismo de contêiner e o tempo de execução do IoT Edge estão instalados em seu dispositivo, você está pronto para a próxima etapa, que é configurar o dispositivo com sua identidade de nuvem e informações de autenticação.

Escolha a próxima seção com base no tipo de autenticação que você deseja usar:

* [Opção 1: autenticar com chaves simétricas](#option-1-authenticate-with-symmetric-keys)
* [Opção 2: autenticar com certificados X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opção 1: autenticar com chaves simétricas

Neste ponto, o tempo de execução do IoT Edge é instalado em seu dispositivo Windows e você precisa provisionar o dispositivo com suas informações de autenticação e identidade de nuvem.

Esta seção percorre as etapas para provisionar um dispositivo com a autenticação de chave simétrica. Você deve ter registrado seu dispositivo no Hub IoT e recuperado a cadeia de conexão das informações do dispositivo. Caso contrário, siga as etapas em [registrar um dispositivo de IOT Edge no Hub IOT](how-to-register-device.md).

1. No dispositivo IoT Edge, execute o PowerShell como administrador.

2. Use o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de execução de IOT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se você baixou o script IoTEdgeSecurityDaemon.ps1 em seu dispositivo para uma instalação de versão específica ou offline, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na seção anterior. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT e fornece informações de autenticação.

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Ao provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem de contêiner edgeAgent específica e forneça credenciais se ela estiver em um registro privado

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge com contêineres do Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opção 2: autenticar com certificados X. 509

Neste ponto, o tempo de execução do IoT Edge é instalado em seu dispositivo Windows e você precisa provisionar o dispositivo com suas informações de autenticação e identidade de nuvem.

Esta seção percorre as etapas para provisionar um dispositivo com autenticação de certificado X. 509. Você deve ter registrado seu dispositivo no Hub IoT, fornecendo impressões digitais que correspondam ao certificado e à chave privada localizadas em seu dispositivo de IoT Edge. Caso contrário, siga as etapas em [registrar um dispositivo de IOT Edge no Hub IOT](how-to-register-device.md).

1. No dispositivo IoT Edge, execute o PowerShell como administrador.

2. Use o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de execução de IOT Edge em seu computador.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se você baixou o script IoTEdgeSecurityDaemon.ps1 em seu dispositivo para uma instalação de versão específica ou offline, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça as seguintes informações:

   * **IotHubHostName**: nome do host do Hub IOT ao qual o dispositivo se conectará. Por exemplo, `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: a ID que você forneceu quando registrou o dispositivo.
   * **X509IdentityCertificate**: caminho absoluto para um certificado de identidade no dispositivo. Por exemplo, `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: caminho absoluto para o arquivo de chave privada para o certificado de identidade fornecido. Por exemplo, `C:\path\identity_key.pem`.

Ao provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem de contêiner edgeAgent específica e forneça credenciais se ela estiver em um registro privado

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge com contêineres do Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Instalação offline ou de versão específica (opcional)

As etapas nesta seção são para cenários não cobertos pelas etapas de instalação padrão. Isso pode incluir:

* Instalar IoT Edge enquanto estiver offline
* Instalar uma versão Release Candidate
* Instalar uma versão diferente da mais recente

Durante a instalação, três arquivos são baixados:

* Um script do PowerShell, que contém as instruções de instalação
* Microsoft Azure IoT Edge CAB, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo de contêiner do Moby e a CLI do Moby
* Instalador do pacote redistribuível (tempo de execução do VC) Visual C++

Se o dispositivo ficar offline durante a instalação ou se você quiser instalar uma versão específica do IoT Edge, poderá baixar esses arquivos antecipadamente para o dispositivo. Quando for o momento de instalar, aponte o script de instalação no diretório que contém os arquivos baixados. O instalador verifica primeiro o diretório e, em seguida, baixa apenas os componentes que não foram encontrados. Se todos os arquivos estiverem disponíveis offline, você poderá instalar o sem conexão com a Internet.

1. Para obter os arquivos de instalação mais recentes do IoT Edge juntamente com as versões anteriores, consulte [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases).

2. Localize a versão que você deseja instalar e baixe os seguintes arquivos da seção **ativos** das notas de versão para seu dispositivo IOT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab do canal de versão 1,1.

   É importante usar o script do PowerShell da mesma versão que o arquivo. cab que você usa porque a funcionalidade muda para dar suporte aos recursos em cada versão.

3. Se o arquivo. cab baixado tiver um sufixo de arquitetura, renomeie o arquivo para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Opcionalmente, baixe um instalador para Visual C++ redistribuível. Por exemplo, o script do PowerShell usa esta versão: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Salve o instalador na mesma pasta em seu dispositivo IoT que os arquivos de IoT Edge.

5. Para instalar com componentes offline, [dot Source](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a cópia local do script do PowerShell.

6. Execute o comando [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) com o `-OfflineInstallationPath` parâmetro. Forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   O comando de implantação usará todos os componentes encontrados no diretório de arquivos local fornecido. Se o arquivo. cab ou o instalador do Visual C++ estiver ausente, ele tentará baixá-los.

## <a name="update-iot-edge"></a>Atualizar IoT Edge

Use o `Update-IoTEdge` comando para atualizar o daemon de segurança. O script obtém automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

A execução do comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens de contêiner de tempo de execução. O arquivo config.yaml é mantido no dispositivo, bem como os dados do mecanismo de contêiner Moby. Manter as informações de configuração significa que você não precisa fornecer à cadeia de conexão ou às informações do serviço de provisionamento de dispositivos para seu dispositivo novamente durante o processo de atualização.

Se você quiser atualizar para uma versão específica do daemon de segurança, localize a versão do canal de lançamento 1,1 que você deseja direcionar de [IOT Edge versões](https://github.com/Azure/azure-iotedge/releases). Nessa versão, baixe o arquivo de **Microsoft-Azure-IoTEdge.cab** . Em seguida, use o `-OfflineInstallationPath` parâmetro para apontar para o local do arquivo local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um arquivo chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Renomeie o arquivo para remover o sufixo de arquitetura, se ele tiver um.

Se você quiser atualizar um dispositivo offline, localize a versão que você deseja direcionar de [Azure IOT Edge versões](https://github.com/Azure/azure-iotedge/releases). Nessa versão, baixe os arquivos de *IoTEdgeSecurityDaemon.ps1* e *Microsoft-Azure-IoTEdge.cab* . É importante usar o script do PowerShell da mesma versão que o arquivo. cab que você usa porque a funcionalidade muda para dar suporte aos recursos em cada versão.

Se o arquivo. cab baixado tiver um sufixo de arquitetura, renomeie o arquivo para apenas **Microsoft-Azure-IoTEdge.cab**.

Para atualizar com componentes offline, [dot Source](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) a cópia local do script do PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do `Update-IoTEdge` comando e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Para obter mais informações sobre as opções de atualização, use o comando `Get-Help Update-IoTEdge -full` ou consulte os [scripts do PowerShell para IOT Edge com contêineres do Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do seu dispositivo, use os comandos a seguir.

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) em uma janela administrativa do PowerShell. Esse comando remove o runtime do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Para obter mais informações sobre as opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Próximas etapas

Continue a [implantar módulos IOT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos em seu dispositivo.
