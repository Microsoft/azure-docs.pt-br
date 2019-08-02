---
title: Gerenciamento de dispositivo IoT do Azure com as Ferramentas IoT do Azure para Visual Studio Code | Microsoft Docs
description: Use as Ferramentas IoT do Azure para Visual Studio Code para gerenciar dispositivos Hub IoT do Azure com métodos diretos e opções de gerenciamento de propriedades desejadas de Gêmeo.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: f5697e0b51e8349de62e71be1471beb76aa48765
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838732"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Use as Ferramentas IoT do Azure para Visual Studio Code para gerenciar dispositivos Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

As [Ferramentas IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão útil do Visual Studio Code que facilitam o gerenciamento do Hub IoT e o desenvolvimento de aplicativos para IoT. Ela é fornecida com opções de gerenciamento que podem ser usadas para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Leitura de dispositivo gêmeo           | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Atualizar dispositivo gêmeo         | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da nuvem para o dispositivo   | Envie notificações para um dispositivo. Por exemplo, “É muito provável que chova hoje. Não se esqueça de levar um guarda-chuva”.              |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a usar as Ferramentas IoT do Azure para o Visual Studio Code com várias opções de gerenciamento na sua máquina de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute as Ferramentas IoT do Azure para o Visual Studio Code com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

* Uma assinatura ativa do Azure.
* Um hub IoT do Azure em sua assinatura.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas IoT do Azure para VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou [abrir este link no Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

1. Na exibição do **Explorer** do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure** no canto inferior esquerdo.

2. Clique no menu de contexto **Selecionar Hub IoT**.

3. Um pop-up aparecerá no canto inferior direito para permitir que você entre no Azure pela primeira vez.

4. Depois de entrar, sua lista de Assinatura do Azure será mostrada, então selecione a Assinatura do Azure e o Hub IoT.

5. A lista de dispositivos será mostrada na guia **Dispositivos do Hub IoT do Azure** em poucos segundos.

   > [!Note]
   > Você também pode concluir a configuração escolhendo **Definir cadeia de conexão do Hub IoT**. Insira o **iothubowner** cadeia de conexão da política do hub IoT que seu dispositivo IoT se conecta na janela pop-up.

## <a name="direct-methods"></a>Métodos diretos

1. Clique com o botão direito no seu dispositivo e selecione **Invocação de Método Direto**. 

2. Insira o nome do método e a carga na caixa de entrada.

3. Os resultados serão mostrados na exibição **SAÍDA** > **Kit de Ferramentas do Hub IoT do Azure**.

## <a name="read-device-twin"></a>Leitura de dispositivo gêmeo

1. Clique com o botão direito e selecione **Editar Gêmeo de Dispositivo**. 

2. Um arquivo **azure-iot-dispositivo-twin.json** será aberto com o conteúdo do dispositivo gêmeo.

## <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

1. Faça algumas edições de **marcas** ou campo **properties.desired**.

2. Clique com botão direito no arquivo **azure-iot-dispositivo-twin.json**.

3. Selecione **Atualizar Dispositivo Gêmeo** para atualizar o dispositivo gêmeo.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo

Para enviar uma mensagem do Hub IoT para o dispositivo, siga estas etapas:
 
1. Clique com o botão direito do mouse no seu dispositivo e selecione **Enviar Mensagem de C2D para Dispositivo**. 

2. Insira a mensagem na caixa de entrada.

3. Os resultados serão mostrados na exibição **SAÍDA** > **Kit de Ferramentas do Hub IoT do Azure**.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar a extensão Ferramentas IoT do Azure para Visual Studio Code com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
