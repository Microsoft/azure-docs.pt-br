---
title: Gerenciamento de clientes potenciais no Salesforce – marketplace comercial da Microsoft
description: Saiba como usar o Salesforce para configurar clientes potenciais para o Microsoft AppSource e o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489311"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurar o gerenciamento de clientes potenciais para o Salesforce

Este artigo descreve como configurar o sistema de Salesforce para processar clientes potenciais de suas ofertas no Microsoft AppSource e no Azure Marketplace.

> [!NOTE]
> O Azure Marketplace não dá suporte a listas preenchidas previamente, como uma lista de valores para o campo **País**. Confira se não há nenhuma lista configurada antes de continuar. Como alternativa, você pode configurar um [ponto de extremidade HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou uma [tabela do Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para receber clientes potenciais.

## <a name="set-up-your-salesforce-system"></a>Configurar seu sistema Salesforce

1. Entre no Salesforce.
1. Navegue até as configurações do **Web-to-Lead**. 
    
    Se você estiver usando a experiência Lightning do Salesforce
    1. Selecione **Instalação** na home page do Salesforce.

       ![Configuração do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na página **Instalação**, vá para **Ferramentas da plataforma** > **Configurações de recursos** > **Marketing** > **Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Se você estiver usando a experiência clássica do Salesforce:

    1. Selecione **Instalação** na home page do Salesforce.

       ![Configuração clássica do Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na página **Instalação**, selecione **Criar** > **Personalizar** > **Clientes potenciais** > **Web-to-Lead**.

        ![Salesforce Web-to-Lead clássico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   As etapas restantes são as mesmas para as duas experiências do Salesforce.

1. Na página **Instalação do Web-to-Lead**, selecione o botão **Criar formulário Web-to-Lead**.
1. Em **Instalação do Web-to-Lead**, selecione **Criar formulário Web-to-Lead**.

    ![Instalação do Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Em **Criar um formulário Web-to-Lead**, verifique se a instalação de `Include reCAPTCHA in HTML` está desmarcada e selecione **Gerar**.

    ![O Salesforce cria um painel Formulário Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Você verá um texto HTML. Procure o texto "OID" e copie o **valor "OID"** do texto HTML (apenas o texto entre aspas) e salve-o. Você colará esse valor no campo **Identificador da organização** no portal de publicação.

    ![O Salesforce cria um Formulário Web-to-Lead mostrando o valor HTML "OID"](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selecione **Concluído**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurar oferta para enviar clientes potenciais para o Salesforce

Quando estiver pronto para configurar as informações de gerenciamento de clientes potenciais para suas ofertas no portal de publicação, siga as etapas abaixo.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá até a guia **Configuração de oferta**.

1. Na seção **Clientes potenciais**, selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Clientes potenciais":::

1. Na janela pop-up de **Detalhes da conexão**, selecione **Salesforce** para o **Destino dos clientes potenciais** e cole o valor `oid` do Formulário Web-to-Lead criado no campo **Identificador da organização**.

    ![Na janela pop-up de Detalhes da conexão, valide a caixa Email de contato](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Em **Email de contato**, insira os endereços de email das pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode inserir vários endereços de email, separando-os com um sinal de ponto e vírgula.

1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione **Validar**. Se tiver se conectado com êxito, você terá um cliente potencial de teste no destino do cliente potencial.

>[!NOTE]
>Conclua a configuração do restante da oferta e publique-a antes de receber clientes potenciais para a oferta.
