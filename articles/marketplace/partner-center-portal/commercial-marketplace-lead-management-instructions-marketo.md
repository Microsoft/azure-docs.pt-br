---
title: Gerenciamento de clientes potenciais no Marketo – Marketplace comercial da Microsoft
description: Saiba como usar o sistema de CRM Marketo para gerenciar clientes potenciais do Microsoft AppSource e do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491068"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Use o Marketo para gerenciar clientes potenciais do marketplace comercial

Este artigo descreve como configurar seu sistema de CRM Marketo para processar os clientes potenciais de suas ofertas no Microsoft AppSource e no Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configuração do sistema de CRM Marketo

1. Entre no Marketo.

1. Selecione **Design Studio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selecione **Novo Formulário**.

    ![Novo formulário do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Preencha os campos obrigatórios na caixa de diálogo **Novo Formulário** e, em seguida, selecione **Criar**.

    ![Criação de novo formulário no Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na página **Detalhes do Campo**, selecione **Concluir**.

    ![Formulário de conclusão do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Aprovar e fechar.

1. Na guia **MarketplaceLeadBackend**, selecione **Código de Inserção**. 

    ![Código de Inserção do Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. O Código de Inserção do Marketo exibe um código semelhante ao exemplo a seguir.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copie os valores para os campos a seguir mostrados no formulário de Código de Inserção. Esses valores serão usados para configurar a oferta para clientes potenciais na próxima etapa. Use o próximo exemplo como um guia para obter as IDs necessárias no exemplo do Código de Inserção do Marketo.

    - ID do servidor = **ys12**
    - ID do Munchkin = **123-PQR-789**
    - ID do formulário = **1179**

    A seguir está outra maneira de descobrir esses valores:

    - A ID do servidor pode ser encontrada na URL da sua instância do Marketo, por exemplo, `serverID.marketo.com`.
    - Obtenha a ID do Munchkin da sua assinatura acessando o menu **Admin** > **Munchkin** no campo **ID da Conta do Munchkin**, ou a partir da primeira parte do subdomínio do host da API REST do Marketo: `https://{Munchkin ID}.mktorest.com`.
    - ID do formulário é a ID do formulário de Código de Inserção criado na etapa 7 para encaminhar clientes potenciais do marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configuração da oferta para enviar clientes potenciais para o Marketo

Quando estiver pronto para configurar as informações de gerenciamento de clientes potenciais para a oferta no portal de publicação, siga as etapas abaixo. 

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá até a guia **Configuração de oferta**.

1. Na seção **Clientes potenciais**, selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Clientes potenciais":::

1. Na janela pop-up **Detalhes da conexão**, selecione **Marketo** para o **Destino do cliente potencial**.

    ![Escolha do destino do cliente potencial](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Forneça a **ID do servidor**, a **ID da conta do Munchkin** e a **ID do formulário**.

    > [!NOTE]
    > Conclua a configuração do restante da oferta e publique-a antes de receber clientes potenciais para a oferta. 

1. Para **Emails de contato**, insira os endereços de email das pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode inserir vários endereços de email separando-os com um sinal de ponto e vírgula.

1. Selecione **OK**.

   Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione **Validar**. Se tiver se conectado com êxito, você terá um cliente potencial de teste no destino do cliente potencial.

   ![Janela pop-up de Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
