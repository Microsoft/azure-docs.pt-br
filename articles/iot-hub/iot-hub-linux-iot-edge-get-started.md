---
title: "Introdução ao Azure IoT Edge (Linux) |Microsoft Docs"
description: "Como criar um gateway em um computador Linux e saber mais sobre os principais conceitos Edge IoT do Azure, tais como módulos e arquivos de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb65e3c34d2b2a14370792d8506c13c8c5fb522e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorar a arquitetura do Edge IoT do Azure no Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Como executar a amostra

O script **build.sh** gera sua saída na pasta **build** na cópia local do repositório **iot-edge**. Essa saída inclui os dois módulos do Edge IoT usados neste exemplo.

O script de build coloca **liblogger.so** na pasta **build/modules/logger/** e **libhello\_world.so** na pasta **build/modules/hello_world/**. Use esses caminhos para os valores de **caminho do módulo**, conforme mostrado no arquivo de configurações do JSON de exemplo.

O processo hello\_world\_sample leva o caminho até um arquivo de configuração JSON como um argumento na linha de comando. O arquivo JSON de exemplo a seguir é fornecido no repositório SDK em **samples/hello\_world/src/hello\_world\_lin.json**. Este arquivo de configuração funciona da forma como está, a menos que você tenha modificado o script de build para colocar os módulos ou os executáveis de exemplo do IoT Edge em locais não padrão.

> [!NOTE]
> Os caminhos do módulo são relativos ao diretório de trabalho atual de onde o executável hello\_world\_sample é iniciado, não ao diretório onde o executável está localizado. O arquivo de configuração do JSON de exemplo assume o padrão de gravar 'log.txt' no diretório de trabalho atual.

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. Navegue até a pasta **build** na raiz da sua cópia local do repositório **iot-edge**.

1. Execute o comando a seguir:

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

