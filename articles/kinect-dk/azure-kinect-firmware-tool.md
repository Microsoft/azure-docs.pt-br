---
title: Ferramenta de Firmware do Azure Kinect
description: Entenda como consultar e atualizar o firmware do dispositivo usando a ferramenta de firmware Kinect do Azure.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, firmware, atualização
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276353"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Ferramenta de firmware do Azure Kinect DK

A ferramenta de firmware Kinect do Azure pode ser usada para consultar e atualizar o firmware do dispositivo do Azure Kinect DK.

## <a name="list-connected-devices"></a>Listar dispositivos conectados

Você pode obter uma lista de dispositivos conectados usando a opção-l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Verificar a versão do firmware do dispositivo

Você pode verificar as versões de firmware atuais do primeiro dispositivo conectado usando a opção-q, por exemplo, `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Se houver mais de um dispositivo anexado, você poderá especificar o dispositivo que deseja consultar adicionando o número de série completo ao comando, como:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Atualizar o firmware do dispositivo

O uso mais comum dessa ferramenta é atualizar o firmware do dispositivo. Faça a atualização chamando a ferramenta usando a `-u` opção. Uma atualização de firmware pode levar alguns minutos, dependendo de quais arquivos de firmware devem ser atualizados.

Para obter instruções de atualização passo a passo do firmware, consulte [atualização do firmware Kinect do Azure](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Se houver mais de um dispositivo anexado, você poderá especificar o dispositivo que deseja consultar adicionando o número de série completo ao comando.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Redefinir o dispositivo

Um Azure Kinect DK anexado pode ser redefinido com a opção-r, se você precisar colocar o dispositivo em um estado conhecido.

Se houver mais de um dispositivo anexado, você poderá especificar o dispositivo que deseja consultar adicionando o número de série completo ao comando.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Inspecionar o firmware

Inspecionar o firmware permite que você obtenha as informações de versão de um arquivo de compartimento de firmware antes de atualizar um dispositivo real.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Opções da ferramenta de atualização de firmware

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Instruções detalhadas para atualizar o firmware do dispositivo](update-device-firmware.md)
