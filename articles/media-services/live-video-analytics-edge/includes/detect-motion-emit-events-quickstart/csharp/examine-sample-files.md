---
ms.openlocfilehash: 1e3ba4b39baa045f35c232fa97c14bc78d8de5ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88690886"
---
1. No Visual Studio Code, vá até *src/edge*. Você verá o arquivo *.env* e alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são usadas variáveis para algumas propriedades. O arquivo *.env* contém os valores dessas variáveis.
1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:

    * ***c2d-console-app.csproj*** – o arquivo de projeto para o Visual Studio Code.
    * ***operations.json*** – uma lista das operações que você deseja que o programa execute.
    * ***Program.cs*** – o código do programa de exemplo. Esse código:
    
      * Carrega as configurações do aplicativo.
      * Invoca métodos diretos que são expostos pela Análise Dinâmica de Vídeo no módulo do IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](../../../direct-methods.md).
      * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos gerados pelo módulo na janela **SAÍDA**.
      * Invoca métodos diretos para limpar recursos.   
