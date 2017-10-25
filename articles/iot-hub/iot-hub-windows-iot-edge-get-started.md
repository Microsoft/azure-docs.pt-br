---
title: "Introdução ao Azure IoT Edge (Windows) | Microsoft Docs"
description: "Como criar um gateway em do Azure IoT Edge um computador Windows e saber mais sobre os principais conceitos do Azure IoT Edge, como módulos e arquivos de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorar a arquitetura do Azure IoT Edge no Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Execute o exemplo

O script **build.cmd** gera sua saída na pasta **build** na cópia local do repositório **iot-edge**. Essa saída inclui muitos arquivos, mas este exemplo se concentra em três:
- Dois módulos do Azure IoT Edge: **build\\modules\\logger\\Debug\\logger.dll** e **build\\modules\\hello_world\\Debug\\hello\_world.dll**. 
- Um arquivo executável: **build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**. Esse processo leva um arquivo de configuração JSON como um argumento na linha de comando.

O quarto arquivo que você usa neste exemplo não está na pasta de build, mas foi incluído quando você clonou o repositório iot-edge:
- Um arquivo de configuração JSON: **samples\\hello\_world\\src\\hello\_world\_win.json**. Este arquivo contém os caminhos para os dois módulos. Ele também declara onde o logger.dll grava a saída. O padrão é **log.txt** no diretório de trabalho atual. 

   >[!NOTE]
   >Se você move os módulos de amostra ou adiciona seus próprios para testes, atualize os valores de **module.path** no arquivo de configuração para fazer a correspondência. Os caminhos de módulo são relativos ao diretório em que se encontra o **hello\_world\_sample.exe**. 

Para executar o exemplo, siga estas etapas:

1. Navegue até a pasta **build** na raiz da sua cópia local do repositório **iot-edge**.

1. Execute o comando a seguir:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. A seguinte saída significa que o exemplo está em execução com êxito:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Pressione a tecla **Enter** tecla para interromper o processo.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
