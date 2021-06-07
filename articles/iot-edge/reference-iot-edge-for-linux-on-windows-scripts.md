---
title: Scripts do PowerShell para Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Informações de referência para Azure IoT Edge para o Linux em scripts do Windows PowerShell para implantar, provisionar e status IoT Edge para Linux em máquinas virtuais do Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 41827c5db58f3d4755fb34e46067357cd0255676
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612765"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Scripts do PowerShell para IoT Edge para Linux no Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Entenda os scripts do PowerShell que implantam, provisionam e obtêm o status de seu IoT Edge para Linux na máquina virtual do Windows.

Os comandos descritos neste artigo são do `AzureEFLOW.psm1` arquivo, que pode ser encontrado no seu sistema em seu `WindowsPowerShell` diretório em `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

O comando **Deploy-eFlow** é o principal método de implantação. O comando de implantação cria a máquina virtual, provisiona arquivos e implanta o módulo IoT Edge Agent. Embora nenhum dos parâmetros seja necessário, eles podem ser usados para provisionar o dispositivo IoT Edge durante a implantação e modificar as configurações da máquina virtual durante a criação. Para obter uma lista completa, use o comando `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Para o tipo de provisionamento, o **X509** atualmente refere-se exclusivamente ao provisionamento X509 usando um [serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/about-iot-dps.md). O método de provisionamento X509 manual não tem suporte no momento.

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| eflowVhdxDir | Caminho do diretório | Diretório em que a implantação armazena o arquivo VHDX para a VM. |
| provisioningType | **manual**, **TPM**, **X509** ou **simétrico** |  Define o tipo de provisionamento que você deseja usar para o dispositivo IoT Edge. |
| devConnString | A cadeia de conexão do dispositivo de um dispositivo de IoT Edge existente | Cadeia de conexão do dispositivo para provisionamento manual de um dispositivo IoT Edge (**manual**). |
| symmKey | A chave primária para um registro de DPS existente ou a chave primária de um dispositivo de IoT Edge existente registrado usando chaves simétricas | Chave simétrica para provisionar um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| scopeId | A ID de escopo para uma instância de DPS existente. | ID de escopo para provisionamento de um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| registrationId | A ID de registro de um dispositivo de IoT Edge existente | ID de registro para provisionamento de um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| identityCertLocVm | Caminho do diretório; deve estar em uma pasta que pode pertencer ao `iotedge` serviço | Caminho de destino absoluto do certificado de identidade em sua máquina virtual para provisionar um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| identityCertLocWin | Caminho do diretório | Caminho de origem absoluto do certificado de identidade no Windows para provisionamento de um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| identityPkLocVm |  | Caminho do diretório; deve estar em uma pasta que pode pertencer ao `iotedge` serviço | Caminho de destino absoluto da chave privada de identidade em sua máquina virtual para provisionar um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| identityPkLocWin | Caminho do diretório | Caminho de origem absoluto da chave privada de identidade no Windows para o provisionamento de um dispositivo de IoT Edge (**X509** ou **simétrico**). |
| vmSizeDefintion | Não mais de 30 caracteres | Definição do número de núcleos e RAM disponível para a máquina virtual. **Valor padrão**: Standard_K8S_v1. |
| vmDiskSize | Entre 8 GB e 256 GB | Tamanho máximo do disco do disco rígido virtual de expansão dinâmica. **Valor padrão**: 16 GB. |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |
| vnetType | **Transparente** ou **ICS** | O tipo de comutador virtual. **Valor padrão**: transparente. Transparente refere-se a um comutador externo, enquanto o ICS se refere a um comutador interno. |
| vnetName | Não mais de 64 caracteres | O nome do comutador virtual. **Valor padrão**: externo. |
| enableVtpm | Nenhum | **Parâmetro de opção**. Crie a máquina virtual com o TPM habilitado ou desabilitado. |
| mobyPackageVersion | Não mais de 30 caracteres |  Versão do pacote Moby a ser verificada ou instalada na máquina virtual.  **Valor padrão:** 19.03.11. |
| iotedgePackageVersion | Não mais de 30 caracteres | Versão do pacote de IoT Edge a ser verificada ou instalada na máquina virtual. **Valor padrão:** 1.1.0. |
| installPackages | Nenhum | **Parâmetro de opção**. Quando alternado, o script tentará instalar os pacotes Moby e IoT Edge em vez de apenas verificar se os pacotes estão presentes. |

>[!NOTE]
>Por padrão, se o processo não encontrar um comutador externo com o nome `External` , ele pesquisará qualquer comutador externo existente por meio do qual obter um endereço IP. Se não houver nenhum comutador externo disponível, ele procurará um comutador interno. Se não houver nenhum comutador interno disponível, ele tentará criar o comutador padrão por meio do qual obter um endereço IP.

## <a name="verify-eflowvm"></a>Verify-EflowVm

O comando **Verify-EflowVm** é uma função exposta para verificar se o IOT Edge para Linux na máquina virtual do Windows foi criado. Ele usa apenas parâmetros comuns e retornará **true** se a máquina virtual tiver sido criada e **false** se não. Para obter informações adicionais, use o comando `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

O comando **Provision-EflowVm** adiciona as informações de provisionamento do dispositivo IOT Edge ao arquivo de IOT Edge da máquina virtual `config.yaml` . O provisionamento também pode ser feito durante a fase de implantação definindo parâmetros no comando **Deploy-eFlow** . Para obter informações adicionais, use o comando `Get-Help Provision-EflowVm -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |
| provisioningType | **manual**, **TPM**, **X509** ou **simétrico** |  Define o tipo de provisionamento que você deseja usar para o dispositivo IoT Edge. |
| devConnString | A cadeia de conexão do dispositivo de um dispositivo de IoT Edge existente | Cadeia de conexão do dispositivo para provisionamento manual de um dispositivo IoT Edge (**manual**). |
| symmKey | A chave primária para um registro de DPS existente ou a chave primária de um dispositivo de IoT Edge existente registrado usando chaves simétricas | Chave simétrica para provisionar um dispositivo de IoT Edge (**DPS**, **simétrico**). |
| scopeId | A ID de escopo para uma instância de DPS existente. | ID de escopo para provisionamento de um dispositivo de IoT Edge (**DPS**). |
| registrationId | A ID de registro de um dispositivo de IoT Edge existente | ID de registro para provisionamento de um dispositivo de IoT Edge (**DPS**). |
| identityCertLocVm | Caminho do diretório; deve estar em uma pasta que pode pertencer ao `iotedge` serviço | Caminho de destino absoluto do certificado de identidade em sua máquina virtual para provisionar um dispositivo de IoT Edge (**DPS**, **X509**). |
| identityCertLocWin | Caminho do diretório | Caminho de origem absoluto do certificado de identidade no Windows para o provisionamento de um dispositivo de IoT Edge (**DPS**, **X509**). |
| identityPkLocVm |  | Caminho do diretório; deve estar em uma pasta que pode pertencer ao `iotedge` serviço | Caminho de destino absoluto da chave privada de identidade em sua máquina virtual para provisionar um dispositivo de IoT Edge (**DPS**, **X509**). |
| identityPkLocWin | Caminho do diretório | Caminho de origem absoluto da chave privada de identidade no Windows para o provisionamento de um dispositivo de IoT Edge (**DPS**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

O comando **Get-EflowVmName** é usado para consultar o nome de host atual da máquina virtual. Esse comando existe para considerar o fato de que o nome de host do Windows pode ser alterado ao longo do tempo. Ele usa apenas parâmetros comuns e retornará o nome de host atual da máquina virtual. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

O comando **Get-EflowLogs** é usado para coletar e agrupar logs do IOT Edge para implantação do Linux no Windows. Ele gera os logs agrupados na forma de uma `.zip` pasta. Para obter informações adicionais, use o comando `Get-Help Get-EflowLogs -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

O comando **Get-EflowVmTpmProvisioningInfo** é usado para coletar e exibir as informações de provisionamento do vTPM da máquina virtual. Se a máquina virtual foi criada sem vTPM, o comando retornará que não foi possível encontrar as informações de provisionamento do TPM. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

O comando **Get-EflowVmAddr** é usado para localizar e exibir os endereços IP e Mac da máquina virtual. Ele usa apenas parâmetros comuns. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

O comando **Get-EflowVmSystemInformation** é usado para coletar e exibir informações do sistema da máquina virtual, como uso de memória e armazenamento. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmSystemInformation -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

O comando **Get-EflowVmEdgeInformation** é usado para coletar e exibir informações de IOT Edge da máquina virtual, como a versão do IOT Edge a máquina virtual está em execução. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmEdgeInformation -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

O comando **Get-EflowVmEdgeModuleList** é usado para consultar e exibir a lista de módulos IOT Edge em execução na máquina virtual. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

O comando **Get-EflowVmEdgeStatus** é usado para consultar e exibir o status de IOT Edge tempo de execução na máquina virtual. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmEdgeStatus -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

O comando **Get-EflowVmUserName** é usado para consultar e exibir o nome de usuário da máquina virtual que foi usado para criar a máquina virtual do registro. Ele usa apenas parâmetros comuns. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

O comando **Get-EflowVmSshKey** é usado para consultar e exibir a chave SSH usada pela máquina virtual. Ele usa apenas parâmetros comuns. Para obter informações adicionais, use o comando `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

O comando **SSH-EflowVm** é usado para ssh na máquina virtual. A única conta permitida para o SSH para a máquina virtual é o usuário que a criou. Para obter informações adicionais, use o comando `Get-Help Ssh-EflowVm -full` .

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de usuário para fazer logon na máquina virtual. |

## <a name="next-steps"></a>Próximas etapas

Saiba como usar esses comandos no seguinte artigo:

* [Instalar o Azure IoT Edge para Linux no Windows](./how-to-install-iot-edge-windows-on-windows.md)

* Consulte [a referência de script do IOT Edge para Linux no Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) para todos os comandos disponíveis por meio do PowerShell.