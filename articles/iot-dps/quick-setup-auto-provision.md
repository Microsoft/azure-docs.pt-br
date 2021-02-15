---
title: Início Rápido – Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT no portal do Azure
description: Início Rápido – Configurar o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 644635d1ab7b0a43c8df3e10bbbd02d1ea67a94a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91297225"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Início Rápido: Configurar o Serviço de Provisionamento de Dispositivos do Hub IoT com o portal do Azure

O Serviço de Provisionamento de Dispositivos no Hub IoT pode ser usado com o Hub IoT para habilitar o provisionamento de toque zero, Just-In-Time no Hub IoT correto sem necessidade de intervenção humana, permitindo que os clientes provisionem milhões de dispositivos IoT de modo seguro e escalonável. O Serviço de Provisionamento de Dispositivos no Hub IoT do Azure é compatível com dispositivos IoT com autenticações de certificado X.509, de chave simétrica e TPM. Para obter mais informações, confira a [Visão geral do Serviço de Provisionamento de Dispositivos no Hub IoT](./about-iot-dps.md)

Neste início rápido, você aprenderá a configurar o Serviço de Provisionamento de Dispositivos no Hub IoT no portal do Azure para provisionar seus dispositivos com as seguintes etapas:

* Use o portal do Azure para criar um Hub IoT
* Usar o Portal do Azure para criar um Serviço de Provisionamento de Dispositivos no Hub IoT e obter o escopo da ID
* Vincular o Hub IoT ao Serviço de Provisionamento de Dispositivos


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Criar um Serviço de Provisionamento de Dispositivos no Hub IoT

1. Selecione o botão **+ Criar um recurso** novamente.

2. *Pesquise no Marketplace* o **Serviço de Provisionamento de Dispositivos**. Selecione o botão **Serviço de Provisionamento de Dispositivos no Hub IoT** e clique em **Criar**. 

3. Forneça as seguintes informações para a nova instância de serviço de provisionamento de dispositivos e clique em **Criar**.

    * **Nome:** forneça um nome exclusivo para sua nova instância do Serviço de Provisionamento de Dispositivos. Caso o nome inserido esteja disponível, uma marca de seleção verde será exibida.
    * **Assinatura:** escolha a assinatura que você deseja usar para criar essa instância do Serviço de Provisionamento de Dispositivos.
    * **Grupo de recursos:** este campo permite que você crie um novo grupo de recursos ou escolha um existente para conter a nova instância. Escolha o mesmo grupo de recursos que contenha o hub Iot criado acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados em um grupo, você pode gerenciá-los juntos. Por exemplo, a exclusão do grupo de recursos exclui todos os recursos contidos nesse grupo. Para obter mais informações, veja [Gerenciar grupos de recursos do Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Localização:** selecione a localização mais próxima de seus dispositivos.

      ![Inserir as informações básicas sobre sua instância do Serviço Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecione o botão de notificação para monitorar a criação da instância do recurso. Depois que o serviço é implantado com êxito, selecione **Fixar no painel** e, então, **Ir para o recurso**.

    ![Monitorar a notificação de implantação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vincular o Hub IoT e o Serviço de Provisionamento de Dispositivos

Nesta seção, você adicionará uma configuração para a instância do Serviço de Provisionamento de Dispositivos. Essa configuração define o hub IoT para o qual os dispositivos serão provisionados.

1. Selecione o botão **Todos os recursos** no menu à esquerda do portal do Azure. Selecione a instância de Serviço de Provisionamento de Dispositivos que você criou na seção anterior. 

    Se o menu estiver configurado usando o **Submenu** em vez do modo **Encaixado** nas configurações do portal, você precisará clicar nas três linhas na parte superior esquerda para abrir o menu do portal à esquerda.  

2. No menu do Serviço de Provisionamento de Dispositivos, selecione **Hubs IoT Vinculados**. Clique no botão **+ Adicionar** na parte superior. 

3. Sobre o **Adicionar link para o hub IoT**, forneça as informações a seguir para vincular a nova instância de serviço de provisionamento de dispositivo a um hub IoT. Em seguida, clique em **Salvar**. 

    * **Assinatura:** selecione a assinatura que contém o hub IoT que você deseja vincular com a nova instância do Serviço de Provisionamento de Dispositivos.
    * **Hub IoT:** selecione o hub IoT para vincular com a nova instância do Serviço de Provisionamento de Dispositivos.
    * **Política de acesso:** selecione **iothubowner** como as credenciais para estabelecer o link com o hub IoT.  

      ![Vincular o nome do hub para vincular à instância do Serviço de Provisionamento de Dispositivos na folha do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora você deve ver o hub selecionado na folha **Hubs IoT Vinculados**. Talvez seja necessário clicar em **Atualizar** para que ele apareça.


## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1. No menu à esquerda no Portal do Azure, selecione **Todos os recursos** e selecione o Serviço de Provisionamento de Dispositivos. Na parte superior do painel de detalhes do dispositivo, selecione **Excluir**.  
2. No menu à esquerda no portal do Azure, selecione **Todos os recursos** e seu Hub IoT. Na parte superior do painel de detalhes do hub, selecione **Excluir**.  

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, bem como vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Início Rápido para criar um dispositivo simulado](./quick-create-simulated-device-symm-key.md)
