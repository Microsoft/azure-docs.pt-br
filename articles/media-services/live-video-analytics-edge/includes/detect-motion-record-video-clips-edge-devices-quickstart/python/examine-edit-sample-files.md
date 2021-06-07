---
ms.openlocfilehash: 6ea9be8e7e67a8e52412e7011cfb1d33c9929191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486730"
---
Como parte dos pré-requisitos deste início rápido, você baixou o código de exemplo para uma pasta. Siga estas etapas para examinar e editar o código de exemplo.

1. No Visual Studio Code, vá até *src/edge*. Você verá seu arquivo *.env* e alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação do dispositivo de borda, onde são usadas variáveis para algumas propriedades. O arquivo *.env* inclui os valores dessas variáveis.
1. Vá até a pasta *src/cloud-to-device-console-app*. Aqui, você vê o arquivo *appsettings.json* e alguns outros arquivos:
    * ***operations.json*** – a lista de operações que você deseja que o programa execute.
    * ***main.py***: o código do programa de exemplo. Esse código:

        * Carrega as configurações do aplicativo.
        * Invoca métodos diretos expostos pelo módulo da Análise Dinâmica de Vídeo no IoT Edge. Use o módulo para analisar fluxos de vídeo ao vivo invocando seus [métodos diretos](../../../direct-methods.md). 
        * Pausa para você examinar a saída do programa na janela **TERMINAL** e os eventos gerados pelo módulo na janela **SAÍDA**.
        * Invoca métodos diretos para limpar recursos.

1. Edite o arquivo *operations.json*:
    * Altere o link para a topologia do grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * Em `GraphInstanceSet`, edite o nome da topologia de grafo para corresponder ao valor no link anterior:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edite também a URL RTSP para apontar para o arquivo de vídeo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Em `GraphTopologyDelete`, edite o nome:

        `"name": "EVRToFilesOnMotionDetection"`
