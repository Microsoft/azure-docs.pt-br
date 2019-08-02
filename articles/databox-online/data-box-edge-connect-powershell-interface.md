---
title: Conectar e gerenciar o dispositivo de borda da caixa de dados do Microsoft Azure por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como conectar e, em seguida, gerenciar dados de caixa de borda por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 6af95b7f8bde6e77ba356fec9dde123e26a9a4a8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448645"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gerenciar um dispositivo de borda de caixa de dados do Azure por meio do Windows PowerShell

Solução de borda da caixa de dados do Azure permite processar dados e enviá-lo pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo de borda da caixa de dados. Você pode usar o portal do Azure, interface do usuário da web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que usando a interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conectar-se à interface do PowerShell
- Criar um pacote de suporte
- Carregar um certificado
- Redefinir o dispositivo
- Exibir informações de dispositivo
- Obter logs de computação
- Monitorar e solucionar problemas de módulos de computação

## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar um certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Você também pode carregar certificados de IoT Edge para habilitar uma conexão segura entre seu dispositivo IoT Edge e os dispositivos downstream que podem se conectar a ele. Há três certificados do IoT Edge ( *. PEM* formato) que você precisa instalar:

- Certificado de autoridade de certificação raiz ou o proprietário da autoridade de certificação
- Certificado de autoridade de certificação de dispositivo
- Certificado de chave do dispositivo

O exemplo a seguir mostra o uso desse cmdlet para instalar certificados do IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando você executa esse cmdlet, você deverá fornecer a senha para o compartilhamento de rede.

Para obter mais informações sobre certificados, vá para [certificados do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instalar certificados em um gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Exibir informações de dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Redefinir seu dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter logs de computação

Se a função de computação é configurada em seu dispositivo, você também pode obter os logs de computação por meio da interface do PowerShell.

1. [Conectar-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os logs de computação para seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Aqui está uma descrição dos parâmetros usados para o cmdlet:
    - `Path`: Forneça um caminho de rede para o compartilhamento no qual você deseja criar o pacote de log de computação.
    - `Credential`: Forneça o nome de usuário para o compartilhamento de rede. Quando você executa esse cmdlet, você precisará fornecer a senha de compartilhamento.
    - `FullLogCollection`: Esse parâmetro garante que o pacote de log conterá todos os logs de computação. Por padrão, o pacote de log contém apenas um subconjunto de logs.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorar e solucionar problemas de módulos de computação

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Saia da sessão remota

Para encerrar a sessão remota do PowerShell, feche a janela do PowerShell.

## <a name="next-steps"></a>Próximas etapas

- Implante o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.
