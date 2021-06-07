---
title: Como usar portal do Azure para invocar métodos diretos
description: Este artigo é uma visão geral que usa o portal do Azure para invocar métodos diretos.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492647"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Como usar portal do Azure para invocar métodos diretos

O Hub IoT oferece a capacidade de invocar [métodos diretos](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) em dispositivos de borda na nuvem. A LVA (Análise Dinâmica de Vídeo) no módulo do IoT Edge expõe vários [métodos diretos](./direct-methods.md) que podem ser usados para definir, implantar e instanciar diferentes fluxos de trabalho para análise dinâmica de vídeo.

Neste artigo, você aprenderá a invocar chamadas de método direto na Análise Dinâmica de Vídeo para um módulo do IoT Edge por meio do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Ter a Análise Dinâmica de Vídeo no módulo do IoT Edge em execução no seu dispositivo de borda, usando os métodos descritos em [Guia de início rápido: Análise Dinâmica de Vídeo no IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) ou usando o [portal.](./deploy-iot-edge-device.md)

* Entender a [Análise Dinâmica de Vídeo](./overview.md) e [o conceito de grafo de mídia](./media-graph-concept.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Como invocar métodos diretos pelo portal do Azure

Cada um dos [métodos diretos](./direct-methods.md) expostos pelo módulo LVA pode ser invocado pelo portal do Azure. As etapas a seguir fornecem os detalhes de um método direto. Você pode invocar outros métodos diretos usando etapas semelhantes. No entanto, cada método direto requer um corpo JSON específico.

Use a chamada de método `GraphTopologyList` para recuperar uma lista de todas as topologias de grafo atualmente implantadas na Análise Dinâmica de Vídeo no módulo do IoT Edge. Use as seguintes etapas para invocar esse método direto:

1. Entre no portal do Azure
1. Localize o grupo de recursos relevante na home page do portal para localizar o Hub IoT ou, se você souber o Hub IoT, selecione-o.
    ![grupo de recursos na home page do portal](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Na página do Hub IoT, selecione IoT Edge em Gerenciamento de Dispositivo Automático para listar as várias IDs do dispositivo. Selecione a identificação do dispositivo relevante para listar os módulos em execução no dispositivo.
    ![página do hub iot](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Selecione a Análise Dinâmica de Vídeo no módulo do IoT Edge para abrir a página de configuração.<br><br>
    ![Selecione a Análise Dinâmica de Vídeo no módulo do IoT Edge para abrir a página de configuração](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Selecione a opção de menu Método direto. <br><br>
    ![Clique na opção de menu Método direto](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Como você pode ver na página atual, talvez seja necessário adicionar um valor nas seções de Cadeia de conexão. Você não precisa ocultar ou alterar nada na seção Nome da configuração. Não há problema em deixá-lo público.

1. Digite *GraphTopologyList* no campo **Nome do Método**.
1. Copie e cole o JSON abaixo no campo **Conteúdo**.
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. Selecione o botão **Invocar Método** na parte superior da página.<br><br>
    ![botão invocar método](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Você deverá ver uma mensagem de status 200 na área **Resultado**.<br><br>
    ![tempo limite de conexão](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Respostas

| Condição             | Código de status | Código de Erro Detalhado |
|-----------------------|-------------|---------------------|
| Sucesso               | 200         | N/D                 |
| Erros gerais do usuário   | Intervalo de 400   |                     |
| Erros gerais do servidor | Intervalo de 500   |                     |

## <a name="next-steps"></a>Próximas etapas

Mais métodos diretos podem ser encontrados na página [métodos diretos](./direct-methods.md) .

> [!NOTE]
> Uma instância de grafo instancia uma topologia específica, portanto, verifique se você tem a topologia certa definida antes de criar uma instância de grafo.

[Início Rápido: Detectar eventos de emissão de movimento](./get-started-detect-motion-emit-events-quickstart.md) é uma boa referência para entender a sequência exata de chamadas de método direto a serem feitas.
