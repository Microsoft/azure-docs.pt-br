---
title: Scripts para Azure IoT Edge com contêineres do Windows | Microsoft Docs
description: Informações de referência para IoT Edge scripts do PowerShell a serem instalados, desinstalados ou atualizados em dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489517"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Scripts do PowerShell para IoT Edge com contêineres do Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Entenda os scripts do PowerShell que instalam, atualizam ou desinstalam IoT Edge em dispositivos Windows.

Os comandos descritos neste artigo são do `IoTEdgeSecurityDaemon.ps1` arquivo que é lançado com cada versão de [IOT Edge](https://github.com/Azure/azure-iotedge/releases). A versão mais recente do script está sempre disponível em aka.ms/iotedge-win.

Você pode executar qualquer um dos comandos usando o `Invoke-WebRequest` cmdlet para acessar a versão mais recente do script. Por exemplo:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Você também pode baixar esse script ou uma versão do script de uma versão específica, para executar os comandos. Por exemplo:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

O script fornecido está assinado para aumentar a segurança. Você pode verificar a assinatura baixando o script em seu dispositivo e, em seguida, executando o seguinte comando do PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

O status de saída será **válido** se a assinatura for verificada.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge baixa e implanta o daemon de segurança do IoT Edge e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Deploy-IoTEdge -full` .  

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura IoT Edge com a cadeia de conexão do dispositivo e detalhes operacionais. Grande parte das informações geradas por esse comando é armazenada no arquivo iotedge\config.YAML. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Initialize-IoTEdge -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Nenhum | **Parâmetro de opção**. **Valor padrão**. Se nenhum tipo de provisionamento for especificado, o provisionamento manual com uma cadeia de conexão será o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão do dispositivo para provisionar o dispositivo manualmente. |
| **ManualX509** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o provisionamento manual com uma cadeia de conexão será o valor padrão.<br><br>Declara que você fornecerá um certificado de identidade e uma chave privada para provisionar o dispositivo manualmente.
| **DpsTpm** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o provisionamento manual com uma cadeia de conexão será o valor padrão.<br><br>Declara que você fornecerá uma ID do escopo do DPS (Serviço de Provisionamento de Dispositivos) e a ID de Registro do dispositivo para provisionar por meio do DPS.  |
| **DpsSymmetricKey** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o provisionamento manual com uma cadeia de conexão será o valor padrão.<br><br>Declara que você fornecerá uma ID de escopo do DPS (serviço de provisionamento de dispositivos) e a ID de registro do dispositivo para provisionar por meio do DPS, juntamente com uma chave simétrica para atestado. |
| **DpsX509** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o provisionamento manual com uma cadeia de conexão será o valor padrão.<br><br>Declara que você fornecerá uma ID de escopo do DPS (serviço de provisionamento de dispositivos) e a ID de registro do dispositivo para provisionar por meio do DPS, juntamente com um certificado de identidade X. 509 e uma chave privada para atestado.  |
| **DeviceConnectionString** | Uma cadeia de conexão de um dispositivo do IoT Edge registrado em um Hub IoT, entre aspas simples | **Necessário** para provisionamento manual com uma cadeia de conexão. Se você não fornecer uma cadeia de conexão nos parâmetros do script, você será solicitado a fornecer uma. |
| **IotHubHostName** | O nome do host do Hub IoT ao qual um dispositivo se conecta. | **Necessário** para provisionamento manual com certificados X. 509. Usa o formato *{Hub Name}. Azure-Devices.net*. |
| **DeviceId** | A ID do dispositivo de uma identidade de dispositivo registrada no Hub IoT. | **Necessário** para provisionamento manual com certificados X. 509. |
| **ScopeId** | Uma ID do escopo de uma instância do Serviço de Provisionamento de Dispositivos associada ao Hub IoT. | **Necessário** para provisionamento de DPS. Se você não fornecer uma ID de escopo nos parâmetros de script, você será solicitado a fornecer uma. |
| **RegistrationId** | Uma ID de registro gerada pelo dispositivo | **Necessário** para provisionamento de DPS se usar o TPM ou atestado de chave simétrica. **Opcional** se estiver usando o atestado de certificado X. 509. |
| **X509IdentityCertificate** | O caminho do URI para o certificado de identidade do dispositivo X. 509 no dispositivo. | **Necessário** para o provisionamento manual ou de DPS se estiver usando o atestado de certificado X. 509. |
| **X509IdentityPrivateKey** | O caminho do URI para a chave de certificado de identidade do dispositivo X. 509 no dispositivo. | **Necessário** para o provisionamento manual ou de DPS se estiver usando o atestado de certificado X. 509. |
| **SymmetricKey** | A chave simétrica usada para provisionar a IoT Edge identidade do dispositivo ao usar o DPS | **Necessário** para provisionamento de DPS se estiver usando atestado de chave simétrica. |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão.<br><br>Para contêineres do Windows, IoT Edge usa o mecanismo de contêiner Moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **DeviceCACertificate** | O caminho do URI para o certificado de autoridade de certificação do dispositivo X. 509 no dispositivo. | Também podem ser configurados no `C:\ProgramData\iotedge\config.yaml` arquivo. Para obter mais informações, consulte [gerenciar certificados em um dispositivo IOT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | O caminho do URI para a chave privada da AC do dispositivo X. 509 no dispositivo. | Também podem ser configurados no `C:\ProgramData\iotedge\config.yaml` arquivo. Para obter mais informações, consulte [gerenciar certificados em um dispositivo IOT Edge](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de imagem do agente do IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca sem interrupção mais recente para a imagem do agente do IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer a própria imagem de agente. Para obter mais informações, consulte [Reconhecer as marcas do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de usuário** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Senha** | Cadeia de caracteres de senha de segurança | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça a senha para acessar o registro. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema operacional do contêiner for especificado, o Windows será o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro estiver incluído, o instalador verificará o diretório listado quanto aos arquivos MSI de tempo de execução do IoT Edge cab e do VC Runtime necessários para a instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estiverem no diretório, você poderá instalar IoT Edge sem uma conexão com a Internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de implantação reinicie o computador sem avisar, se necessário. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Aplicação** | none | Esse sinalizador força a desinstalação caso a tentativa anterior de desinstalar não tenha sido bem-sucedida.
| **RestartIfNeeded** | none | Esse sinalizador permite que o script de desinstalação reinicie o computador sem avisar, se necessário. |

## <a name="next-steps"></a>Próximas etapas

Saiba como usar esses comandos no seguinte artigo:

* [Instalar ou desinstalar o Azure IoT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md)
