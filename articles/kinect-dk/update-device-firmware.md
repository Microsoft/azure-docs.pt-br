---
title: Atualizar o firmware do Azure Kinect DK
description: Saiba como atualizar o firmware do dispositivo Azure Kinect DK usando a ferramenta de firmware Kinect do Azure.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, atualização, recuperação
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356210"
---
# <a name="update-azure-kinect-dk-firmware"></a>Atualizar o firmware do Azure Kinect DK

Este documento fornece orientação sobre como atualizar o firmware do dispositivo no Azure Kinect DK.

O Azure Kinect DK não atualiza o firmware automaticamente. Você pode usar a [ferramenta de firmware Kinect do Azure](azure-kinect-firmware-tool.md) para atualizar o firmware manualmente para a versão mais recente disponível.

## <a name="prepare-for-firmware-update"></a>Preparar para atualização de firmware

1. [Baixe o SDK](sensor-sdk-download.md).
2. Instale o SDK.
3. No local de instalação do SDK em (local de instalação do SDK) \Tools\, você deve encontrar:

    - AzureKinectFirmwareTool.exe
    - Um arquivo firmware. bin na pasta firmware, como *AzureKinectDK_Fw_1.5.926614. bin*.

4. Conecte seu dispositivo ao PC host e ligue-o também.

> [!IMPORTANT]
> Mantenha a fonte de alimentação e USB conectada durante a atualização do firmware. A remoção da conexão durante a atualização pode colocar o firmware em um estado corrompido.

## <a name="update-device-firmware"></a>Atualizar o firmware do dispositivo

1. Abra um prompt de comando na pasta (local de instalação do SDK) \Tools\.
2. Atualizar o firmware usando a ferramenta de firmware Kinect do Azure

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Exemplo:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Aguarde até que a atualização do firmware seja concluída. Pode levar alguns minutos, dependendo do tamanho da imagem.

### <a name="verify-device-firmware-version"></a>Verificar a versão do firmware do dispositivo

1. Verifique se o firmware está atualizado.

    `AzureKinectFirmwareTool.exe -q`

2. Exiba o exemplo a seguir.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Se você vir a saída acima, o firmware será atualizado.

4. Após a atualização do firmware, você pode executar o [Azure Kinect Viewer](azure-kinect-viewer.md) para verificar se todos os sensores estão funcionando conforme o esperado.

## <a name="troubleshooting"></a>Solução de problemas

As atualizações de firmware podem falhar por vários motivos. Quando uma atualização de firmware falhar, tente as seguintes etapas de mitigação:

1. Tente executar o comando de atualização de firmware uma segunda vez.

2. Confirme se o dispositivo ainda está conectado consultando a versão do firmware.        AzureKinectFirmareTool.exe

3. Se todos os outros falharem, siga as etapas de [recuperação](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) para reverter para o firmware de fábrica e tente novamente.

Para problemas adicionais, consulte [páginas de suporte da Microsoft](./index.yml)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Ferramenta de firmware Kinect do Azure](azure-kinect-firmware-tool.md)