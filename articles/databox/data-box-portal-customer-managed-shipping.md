---
title: Remessa autogerenciada do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve o fluxo de trabalho de remessa autogerenciada para dispositivos Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524542"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Use a remessa autogerenciada para Azure Data Box no portal do Azure

Este artigo descreve as tarefas de remessa autogerenciada para solicitar, pegar e remover um dispositivo Azure Data Box. Você pode gerenciar o dispositivo Data Box pelo portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A remessa autogerenciada está disponível como uma opção quando você [solicita o Azure Data Box](data-box-deploy-ordered.md). A remessa autogerenciada está disponível nas seguintes regiões:

* Governo dos EUA
* Reino Unido
* Europa Ocidental
* Japão
* Singapura
* Coreia do Sul
* Índia
* África do Sul
* Austrália

## <a name="use-self-managed-shipping"></a>Usar remessa autogerenciada

Ao fazer um pedido de Data Box, você pode escolher a opção de envio autogerenciado.

1. Em seu pedido do Azure Data Box,em **Detalhes de contato do** , selecione **+ Adicionar Endereço para Entrega**.
 
   ![Entrega autogerenciada, adicionar endereço de remessa](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher um tipo de envio, selecione a opção de **envio autogerenciado** . Essa opção só estará disponível se você estiver em uma região com suporte, conforme descrito em pré-requisitos.

3. Depois de fornecer seu endereço de envio, você precisará validá-lo e concluir seu pedido.

   ![Entrega autogerenciada, validar e adicionar endereço](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Depois que o dispositivo tiver sido preparado e você receber uma notificação por email para ele, você poderá agendar uma retirada.

   Em seu pedido do Azure Data Box, acesse **Visão geral** e, em seguida, selecione **Agendar retirada**.

   ![Ordem de Data Box, opção de retirada de agenda](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Siga as instruções em **Agendar a retirada do Azure**.

   Para obter seu código de autorização, envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com) a fim de agendar a retirada do dispositivo do datacenter da sua região.

   ![Agendar retirada para instruções do Azure](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Depois de agendar o recebimento do dispositivo, você poderá exibir o código de autorização do dispositivo no painel de **retirada do agendamento para o Azure** .

   ![Exibindo o código de autorização do dispositivo](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Anote este **Código de autorização**. De acordo com os requisitos de segurança, no momento da retirada do agendamento, é necessário apresentar o nome da pessoa que chegaria à escolha.

   Você também precisa fornecer detalhes de quem vai para o datacenter para retirada. Você ou o ponto de contato deve estar com uma ID de foto oficial que será validada no datacenter.

   Além disso, a pessoa que está pegando o dispositivo também precisa ter o **Código de autorização**. O código de autorização é validado no datacenter na hora da retirada.

7. Seu pedido será movido automaticamente para o estado **Retirado** depois que o dispositivo tiver sido obtido do datacenter.

    ![Um pedido no estado de retirada](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Depois que o dispositivo for retirado, copie os dados para o Data Box em seu site. Após a conclusão da cópia dos dados, você pode preparar a remessa do Data Box. Para obter mais informações, consulte [Preparar para o envio](data-box-deploy-picked-up.md#prepare-to-ship).

   A etapa **preparação para o envio** precisa ser concluída sem erros críticos. Caso contrário, você precisará executar essa etapa novamente depois de fazer as correções necessárias. Depois que a etapa de **preparação para o envio** for concluída com êxito, você poderá exibir o código de autorização para a lista suspensa na interface do usuário local do dispositivo.

   > [!NOTE]
   > Não compartilhe o código de autorização por email. Isso só deve ser verificado no datacenter durante a coleta.

9. Se você recebeu um compromisso para a lista suspensa, a ordem deve estar **pronta para receber no status do datacenter do Azure** no portal do Azure. Siga as instruções em **Agendar a devolução** para devolver o dispositivo.

   ![Instruções para a remoção do dispositivo](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Depois que a ID e o código de autorização são verificados e você removeu o dispositivo no datacenter, o status do pedido deve ser **recebido**.

    ![Um pedido com status recebido](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Depois que o dispositivo for recebido, a cópia de dados continuará. Quando a cópia for concluída, o pedido será concluído.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Box](data-box-quickstart-portal.md)
