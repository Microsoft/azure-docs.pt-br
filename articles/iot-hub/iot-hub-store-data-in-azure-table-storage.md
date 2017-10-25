---
title: Salvar suas mensagens do hub IoT no armazenamento de dados do Azure | Microsoft Docs
description: "Use o roteamento de mensagens do Hub IoT para salvar as mensagens do Hub IoT em seu armazenamento de blobs do Azure. As mensagens do hub IoT contêm informações como dados de sensor, que são enviadas do seu dispositivo IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: armazenamento de dados iot, armazenamento de dados de sensor iot
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: aa33800de82b27d4819fe0eade127c2a40e3a493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Salvar mensagens do Hub IoT que contêm dados de sensor em seu armazenamento de blobs do Azure

![Diagrama de ponta a ponta](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Saiba como criar uma conta de armazenamento do Azure e um aplicativo de funções do Azure para armazenar mensagens do hub IoT no armazenamento de tabelas.

## <a name="what-you-do"></a>O que fazer

- Crie uma conta de armazenamento do Azure.
- Prepare seu Hub IoT para rotear mensagens para o armazenamento.

## <a name="what-you-need"></a>O que você precisa

- [Configure seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) para abranger os seguintes requisitos:
  - Uma assinatura ativa do Azure
  - Um hub IoT em sua assinatura 
  - Um aplicativo em execução que envia mensagens ao seu hub IoT

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Armazenamento** > **Conta de armazenamento** > **Criar**.

2. Insira as informações necessárias para a conta de armazenamento:

   ![Criar uma conta de armazenamento no portal do Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Name**: o nome da conta de armazenamento. O nome deve ser globalmente exclusivo.

   * **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   * **Fixar no painel**: selecione essa opção para ter fácil acesso ao Hub IoT no painel.

3. Clique em **Criar**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Preparar seu Hub IoT para rotear mensagens para o armazenamento

O Hub IoT nativamente dá suporte ao roteamento de mensagens para o armazenamento do Azure como blobs.

### <a name="add-storage-as-a-custom-endpoint"></a>Adicionar o armazenamento como um ponto de extremidade personalizado

Vá ao Hub IoT no portal do Azure. Clique em **Pontos de extremidade** > **Adicionar**. Nomeie o ponto de extremidade e selecione **Contêiner de Armazenamento do Azure** como o tipo de ponto de extremidade. Use o seletor para selecionar a conta de armazenamento que você criou na seção anterior. Crie um contêiner de armazenamento, selecione-o e clique em **OK**.

  ![Criar um ponto de extremidade personalizado no Hub IoT](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Adicionar uma rota para rotear dados para o armazenamento

Clique em **Rotas** > **Adicionar** e digite um nome para a rota. Selecione **Mensagens de Dispositivo** como a fonte de dados e selecione o ponto de extremidade de armazenamento que você acabou de criar como o ponto de extremidade na rota. Digite `true` como a cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.

  ![Criar uma rota no Hub IoT](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Adicionar uma rota de telemetria de afunilamento (opcional)

Por padrão, o Hub IoT roteia todas as mensagens que não correspondem a nenhuma outra rota para o ponto de extremidade interno. Como todas as mensagens de telemetria agora correspondem à regra que roteia as mensagens para o armazenamento, você precisa adicionar outra rota para que mensagens sejam gravadas no ponto de extremidade interno. Não há nenhum custo adicional para rotear mensagens para vários pontos de extremidade.

> [!NOTE]
> Você pode ignorar esta etapa se não estiver fazendo um processamento adicional em suas mensagens de telemetria.

Clique em **Adicionar** no painel de Rotas e digite um nome para a rota. Selecione **Mensagens de Dispositivo** como a fonte de dados e **eventos** como o ponto de extremidade. Digite `true` como a cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.

  ![Criar uma rota de afunilamento no Hub IoT](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Verificar a mensagem no contêiner de armazenamento

1. Execute o aplicativo de exemplo em seu dispositivo para enviar mensagens para o Hub IoT.

2. [Baixe e instale o Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

3. Abra o Gerenciador de Armazenamento, clique em **Adicionar uma Conta do Azure** > **Entrar** e entre em sua conta do Azure.

4. Clique em sua assinatura do Azure > **Contas de Armazenamento** > sua conta de armazenamento > **Contêineres de Blobs** > seu contêiner.

   Você deve ver as mensagens enviadas do seu dispositivo ao seu Hub IoT registradas no contêiner de blobs.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito sua conta de armazenamento do Azure e as mensagens roteadas do Hub IoT para um contêiner de blobs na conta de armazenamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
