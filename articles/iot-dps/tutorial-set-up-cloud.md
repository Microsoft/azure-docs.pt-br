---
title: Tutorial – Configurar a nuvem para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure no portal
description: Este tutorial mostra como configurar os recursos de nuvem para o provisionamento de dispositivos no [portal do Azure](https://portal.azure.com) usando o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316062"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Tutorial: Configurar recursos de nuvem para provisionamento de dispositivos com o Serviço de Provisionamento de Dispositivos no Hub IoT

Este tutorial mostra como configurar a nuvem para provisionamento automático de dispositivos usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o Portal do Azure para criar um Serviço de Provisionamento de Dispositivos no Hub IoT e obter o escopo da ID
> * Crie um hub IoT
> * Vincular o Hub IoT ao Serviço de Provisionamento de Dispositivos
> * Definir a política de alocação no Serviço de Provisionamento de Dispositivos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Criar uma instância do Serviço de Provisionamento de Dispositivos e obter o escopo da ID

Siga estas etapas para criar uma nova instância do Serviço de Provisionamento de Dispositivos.

1. No canto superior esquerdo do portal do Azure, clique em **Criar um recurso**.

2. Na caixa Pesquisa, digite **provisionamento de dispositivos**. 

3. Clique em **Serviço de Provisionamento de Dispositivos no Hub IoT**.

4. Preencha o formulário **Serviço de Provisionamento de Dispositivos no Hub IoT** com as seguintes informações:
    
   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome** | Qualquer nome exclusivo | -- | 
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). |
   | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |   

   ![Inserir as informações básicas sobre seu serviço Provisionamento de Dispositivos no portal](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Clique em **Criar**. Após alguns instantes, a instância do serviço de provisionamento do dispositivo é criada e a página **Visão geral** é exibida.

6. Na página **Visão geral** para a nova instância de serviço, copie o valor para o **escopo da ID** para uso posterior. Esse valor é usado para identificar as IDs de registro e fornece uma garantia de que a ID do registro é exclusiva.

7. Copie também o valor do **Ponto de extremidade de serviço** para uso posterior. 

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Recuperar a cadeia conexão para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Agora, você criou seu hub IoT e tem o nome de host e a cadeia de conexão de Hub IoT que precisa para concluir o restante deste tutorial.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Vincular o Serviço de Provisionamento de Dispositivos a um Hub IoT

A próxima etapa é vincular o Serviço de Provisionamento de Dispositivos e o Hub IoT para que o Serviço de Provisionamento de Dispositivos no Hub IoT possa registrar dispositivos para esse hub. O serviço só pode provisionar dispositivos para Hubs IoT que foram vinculados ao Serviço de Provisionamento de Dispositivos. Siga estas etapas.

1. Na página **Todos os recursos**, clique na instância do Serviço de Provisionamento de Dispositivos que você criou anteriormente.

2. Na página do Serviço de Provisionamento de Dispositivos, clique em **Hubs IoT Vinculados**.

3. Clique em **Adicionar**.

4. Na página **Adicionar link ao Hub IoT**, forneça as informações a seguir e clique em **Salvar**:

    * **Assinatura:** Verifique se a assinatura que contém o Hub IoT está selecionada. Você pode vincular ao Hub IoT que reside em uma assinatura diferente.

    * **Hub IoT:** Escolha o nome do Hub IoT que você deseja vincular a essa instância de serviço de provisionamento do dispositivo.

    * **Política de acesso:** Selecione **iothubowner** como as credenciais para estabelecer o link com o Hub IoT.

   ![Vincular o nome do hub para vincular ao serviço Provisionamento de Dispositivos no portal](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Definir a política de alocação no Serviço de Provisionamento de Dispositivos

A política de alocação é uma configuração do Serviço de Provisionamento de Dispositivos no Hub IoT que determina como os dispositivos são atribuídos a um Hub IoT. Há três políticas de alocação com suporte: 

1. **Menor latência**: dispositivos são provisionados para um Hub IoT com base no hub com a menor latência para o dispositivo.

2. **Distribuição igualmente ponderada** (padrão): Hubs IoT vinculados têm probabilidades iguais de ter dispositivos provisionados a eles. Essa é a configuração padrão. Se estiver provisionando dispositivos a apenas um Hub IoT, você poderá manter essa configuração. 

3. **Configuração estática por meio da lista de registro**: a especificação do Hub IoT desejado na lista de registro tem prioridade sobre a política de alocação no nível do Serviço de Provisionamento de Dispositivos.

Para definir a política de alocação, na página do Serviço de Provisionamento de Dispositivos, clique em **Gerenciar política de alocação**. Verifique se a política de alocação está definida como **Distribuição igualmente ponderada** (o padrão). Se fizer alguma alteração, clique em **Salvar** quando terminar.

![Gerenciar a política de alocação](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros tutoriais nesta coleção aproveitam esse tutorial. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse tutorial. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse tutorial no Portal do Azure.

1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione a instância do Serviço de Provisionamento de Dispositivos no Hub IoT. Na parte superior da página **Todos os recursos**, clique em **Excluir**.  

2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da página **Todos os recursos**, clique em **Excluir**.
 
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o Portal do Azure para criar um Serviço de Provisionamento de Dispositivos no Hub IoT e obter o escopo da ID
> * Crie um hub IoT
> * Vincular o Hub IoT ao Serviço de Provisionamento de Dispositivos
> * Definir a política de alocação no Serviço de Provisionamento de Dispositivos

Avance para o próximo tutorial para aprender a configurar seu dispositivo para provisionamento.

> [!div class="nextstepaction"]
> [Configurar o dispositivo para provisionamento](tutorial-set-up-device.md)
