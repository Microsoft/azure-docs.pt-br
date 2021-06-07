---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358179"
---
1. No Visual Studio Code, navegue até src/edge. Você verá o arquivo .env criado junto com alguns arquivos do modelo de implantação.

    O modelo de implantação refere-se ao manifesto de implantação para o dispositivo de borda com alguns valores de espaço reservado. O arquivo .env tem os valores para essas variáveis.
1. Em seguida, navegue até a pasta src/cloud-to-device-console-app. Aqui, você verá o arquivo appsettings.json que foi criado junto com alguns outros arquivos:

    * c2d-console-app.csproj: esse é o arquivo de projeto do Visual Studio Code.
    * operations.json: esse arquivo lista as diferentes operações que você deseja que o programa execute.
    * Program.cs: esse código de programa de exemplo:

        * Carrega as configurações do aplicativo.
        * Invoque a Análise Dinâmica de Vídeo nos métodos diretos do módulo do IoT Edge para criar a topologia, criar uma instância do grafo e ativar o grafo.
        * Pausa para examinar a saída do grafo na janela **TERMINAL** e os eventos enviados ao hub IoT na janela de **SAÍDA**.
        * Desativa a instância do grafo, exclui a instância do grafo e exclui a topologia do grafo.
