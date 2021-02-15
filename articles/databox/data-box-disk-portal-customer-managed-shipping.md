---
title: Remessa autogerenciada do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Descreve o fluxo de trabalho de remessa autogerenciada para dispositivos Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526323"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Use a remessa autogerenciada para Azure Data Box Disk no portal do Azure

Este artigo descreve as tarefas de remessa autogerenciada para solicitar, pegar e retirar o Azure Data Box Disk. Você pode gerenciar o Data Box Disk usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A remessa autogerenciada está disponível como uma opção quando você [Compra o Azure Data Box Disk](data-box-disk-deploy-ordered.md). A remessa autogerenciada está disponível nas seguintes regiões:

* Governo dos EUA
* Reino Unido
* Europa Ocidental
* Austrália
* Japão
* Singapura
* Coreia do Sul
* África do Sul
* Índia (versão prévia)

## <a name="use-self-managed-shipping"></a>Usar remessa autogerenciada

Ao fazer o pedido do Data Box Disk, você pode escolher a opção de remessa autogerenciada.

1. Em seu pedido do Azure Data Box Disk, em **Detalhes de contato**, selecione **+ Adicionar Endereço para Entrega**.

   ![Captura de tela do assistente para pedidos mostrando a etapa detalhes de contato com a opção Adicionar endereço de envio chamada out.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Ao escolher tipo de remessa, selecione a opção **Remessa autogerenciada**. Essa opção só estará disponível se você estiver em uma região com suporte, conforme descrito em pré-requisitos.

3. Depois de fornecer seu endereço de envio, você precisará validá-lo e concluir seu pedido.

   ![Captura de tela da caixa de diálogo Adicionar Endereço para Entrega com as opções de Enviar usando e a opção Adicionar endereço para entrega em destaque.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Depois que o dispositivo tiver sido preparado e você receber uma notificação por email, você poderá agendar uma retirada. Em seu pedido do Azure Data Box Disk, acesse **Visão geral** e, em seguida, selecione **Agendar retirada**.

   ![Pedir um dispositivo Data Box para retirada](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Siga as instruções em **Agendar a retirada do Azure**. Para obter seu código de autorização, envie um email para [adbops@microsoft.com](mailto:adbops@microsoft.com) a fim de agendar a retirada do dispositivo do datacenter da sua região.

   ![Agendar retirada](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Depois de agendar o recebimento do dispositivo, você pode exibir seu código de autorização na **retirada do agendamento para o Azure**.

   ![Captura de tela da caixa de diálogo Selecionar agenda para o Azure com o código de autorização para retirada da caixa de texto chamada.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Anote esse código de autorização.

   De acordo com os requisitos de segurança, no momento da retirada do agendamento, é necessário apresentar o nome da pessoa que vai chegar à escolha.

   Você também precisa fornecer detalhes de quem vai para o datacenter para a escolha. Você ou o ponto de contato devem conter uma ID de foto aprovada pelo governo que será validada no datacenter.

   A pessoa que está selecionando o dispositivo também precisa ter o código de autorização. O código de autorização é exclusivo para uma retirada ou uma lista suspensa e é validado no datacenter.

7. Seu pedido é movido automaticamente para o estado de **retirada** depois que o dispositivo é obtido do datacenter.

   ![Recolhido](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Depois que o dispositivo for retirado, você poderá copiar dados para o Data Box Disk em seu site. Após a conclusão da cópia dos dados, você poderá preparar o Data Box Disk para o envio.

   Depois de concluir a cópia de dados, entre em contato com as operações para agendar um compromisso para a lista suspensa. Você precisará compartilhar os detalhes da pessoa que chega ao datacenter para retirar os discos. O datacenter também precisará verificar o código de autorização no momento da retirada. Você encontrará o código de autorização para a lista suspensa na portal do Azure em **agendar descartar**.

   > [!NOTE]
   > Não compartilhe o código de autorização por email. Isso só deve ser verificado no datacenter durante a remoção.

9. Depois que você receber um compromisso para a desativação, a ordem deverá estar no estado **pronto para receber no datacenter do Azure** na portal do Azure.

   ![Captura de tela da caixa de diálogo Adicionar endereço de envio com a opção Enviar usando as opções de saída e adicionar endereço de envio chamado.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Depois que a ID e o código de autorização tiverem sido verificados e você tiver removido o dispositivo no datacenter, o status do pedido deverá ser **recebido**.

    ![Recebido concluído](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Depois que o dispositivo for recebido, a cópia de dados continuará. Quando a cópia for concluída, o pedido será concluído.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar o Azure Data Box Disk usando o portal do Azure](data-box-disk-quickstart-portal.md)
