---
title: Gerenciamento de clientes potenciais com um ponto de extremidade HTTPS - marketplace comercial da Microsoft
description: Saiba como usar o Power Automate e um ponto de extremidade HTTPS para gerenciar clientes potenciais no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247175"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Use um ponto de extremidade HTTPS para gerenciar clientes potenciais do marketplace comercial

Se o sistema de gerenciamento de relacionamentos com o cliente (CRM) não for explicitamente compatível com o Partner Center para receber os clientes potenciais do Microsoft AppSource e do Azure Marketplace, você pode usar um ponto de extremidade HTTPS no [Power Automate](https://powerapps.microsoft.com/automate-processes/) para lidar com esses clientes potenciais. Com um ponto de extremidade HTTPS, os clientes potenciais do marketplace comercial podem ser enviados como uma notificação por email ou podem ser gravados em um sistema de CRM compatível com o Power Automate.

Este artigo explica como criar um novo fluxo no Power Automate para gerar a URL HTTP POST que você usará para configurar clientes potenciais no Partner Center. Ele também inclui as etapas para testar o fluxo com o [Postman](https://www.getpostman.com/downloads/).

>[!NOTE]
>O conector do Power Automate usado nestas instruções requer uma assinatura paga para o Power Automate. Não deixe de levar isso em consideração antes de configurar esse fluxo.

## <a name="create-a-flow-by-using-power-automate"></a>Criar um fluxo usando o Power Automate

1. Abra a página da web [Power Automate](https://flow.microsoft.com/). Selecione **Entrar**. Se você ainda não tem uma conta, selecione **Inscrever-se gratuitamente** para criar uma conta gratuita do Power Automate.

1. Entre e selecione **Meus fluxos** no menu.

    ![Entrar em Meus fluxos](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Em **+ Novo**, selecione **+ Instantâneo - em branco**.

    ![Meus fluxos + automatizados - em branco](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nomeie o fluxo e, em **Escolher como acionar este fluxo**, selecione **Quando uma solicitação HTTP for recebida**.

    ![Criar uma janela de fluxo automatizado botão Ignorar](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Clique na etapa de fluxo para expandi-la.

    ![Expandir a etapa de fluxo](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Use um dos métodos a seguir para configurar o **Esquema JSON do Corpo da Solicitação**:

    - Copie o Esquema JSON na caixa de texto **Esquema JSON do Corpo da Solicitação**.
    - Selecione **Use o conteúdo de amostra para gerar o esquema**. Na caixa de texto **Digite ou cole uma carga de exemplo de conteúdo JSON**, cole o exemplo de JSON. Selecione **Concluído** para criar o esquema.

    **Esquema JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **Exemplo de JSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>Neste ponto da configuração, você pode escolher se conectar a um sistema de CRM ou configurar uma notificação por email. Siga as instruções restantes com base na sua escolha.

### <a name="connect-to-a-crm-system"></a>Conectar-se a um sistema de CRM

1. Selecione **+ Nova Etapa**.
1. Selecione o sistema de CRM de sua escolha em **Pesquisar conectores e ações**. Selecione-o na guia **Ações** com a ação para criar um novo registro. A tela a seguir mostra **Criar um novo registro (Dynamics 365)** como exemplo.

    ![Criar um novo registro](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Forneça o **Nome da organização** associado ao sistema de CRM. Selecione **Clientes potenciais** na lista suspensa **Nome da entidade**.

    ![Selecionar clientes potenciais](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. O Power Automate mostra um formulário para fornecer informações dos clientes potenciais. É possível mapear itens da solicitação de entrada, escolhendo adicionar conteúdo dinâmico. A tela a seguir mostra **OfferTitle** como exemplo.

    ![Adicionar conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mapeie os campos desejados e, em seguida, selecione **Salvar** para salvar o fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela **Quando uma solicitação HTTP for recebida**. Copie essa URL usando o controle de cópia que está localizado à direita da URL HTTP POST. O uso do controle de cópia é importante para que você não perca nenhuma parte da URL inteira. Salve esta URL porque você precisará dela ao configurar o gerenciamento de clientes potenciais no portal de publicação.

    ![Quando uma solicitação HTTP é recebida](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Configurar notificação por email

1. Agora que você concluiu o esquema JSON, selecione **+ Nova etapa**.
1. Em **Escolha uma ação**, selecione **Ações**.
1. Na guia **Ações**, selecione **Enviar um email (Office 365 Outlook)** .

    >[!NOTE]
    >Se você quiser usar um provedor de email diferente, procure e selecione **Enviar uma notificação por email (email)** como a ação.

    ![Adicionar uma ação de e-mail](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Na janela **Enviar um email**, configure os campos obrigatórios a seguir:

   - **Para**: Insira pelo menos um endereço de email válido para o qual os clientes potenciais serão enviados.
   - **Entidade**: O Power Automate oferece a opção de adicionar conteúdo dinâmico, como **LeadSource** mostrado na tela a seguir. Comece inserindo um nome de campo. Em seguida, escolha a lista de seleção de conteúdo dinâmico na janela pop-up. 

        >[!NOTE] 
        > Ao adicionar os nomes de campo, você pode colocar dois-pontos (:) depois de cada nome e, em seguida, selecionar **Inserir** para criar uma nova linha. Depois de adicionar os nomes de campo, você poderá adicionar cada parâmetro associado da lista de seleção dinâmica.

        ![Adicionar uma ação de email usando conteúdo dinâmico](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Corpo**: Na lista de seleção de conteúdo dinâmico, adicione as informações desejadas no corpo do email. Por exemplo, use Sobrenome, Nome, Email e Empresa. Quando você terminar de configurar a notificação por email, ela será semelhante ao exemplo na tela a seguir.


       ![Exemplo de notificação por email](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Selecione **Salvar** para concluir seu fluxo. Uma URL HTTP POST é criada e pode ser acessada na janela **Quando uma solicitação HTTP for recebida**. Copie essa URL usando o controle de cópia que está localizado à direita da URL HTTP POST. O uso desse controle é importante para que você não perca nenhuma parte da URL inteira. Salve esta URL porque você precisará dela ao configurar o gerenciamento de clientes potenciais no portal de publicação.

   ![URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testando

Você pode testar a configuração com o [Postman](https://app.getpostman.com/app/download/win64). Um download online do Postman está disponível para Windows. 

1. Inicie o Postman e selecione **Nova** > **Solicitação** para configurar a ferramenta de teste. 

   ![Solicitação para configurar a ferramenta de teste](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Preencha o formulário **Salvar Solicitação** e, em seguida, salve na pasta que você criou.

   ![Salvar formulário de solicitação](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Selecione **POST** na lista suspensa. 

   ![Testar meu fluxo](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Cole a URL HTTP POST do fluxo que você criou no Power Automate em **Inserir URL de solicitação**.

   ![Copie a URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Volte ao [Power Automate](https://flow.microsoft.com/). Encontre o fluxo que você criou para enviar os clientes potenciais, acessando **Meus fluxos** na barra de menus do Power Automate. Selecione as reticências ao lado do nome do fluxo para ver mais opções e selecione **Editar**.


1. Selecione **Testar** no canto superior direito, selecione **Realizarei a ação de gatilho** e, depois, selecione **Testar**. Você verá uma indicação na parte superior da tela informando que o teste foi iniciado.

   ![Opção Realizarei a ação de gatilho](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Volte ao aplicativo Postman e selecione **Enviar**.

   ![Botão Enviar](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Volte ao fluxo e verifique o resultado. Se tudo funcionar conforme o esperado, você verá uma mensagem que indica que o fluxo foi executado com sucesso.

   ![Verificar os resultados](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Além disso, você deve ter recebido um email. Verifique sua caixa de entrada de email. 

    >[!NOTE] 
    >Se você não encontrar um email do teste, verifique suas pastas de spam e lixo eletrônico. Na tela a seguir, você observará apenas os rótulos de campo que adicionou quando configurou a notificação por email. Se esse for um cliente potencial real gerado por meio da oferta, você também verá as informações reais do contato do cliente potencial no corpo e na linha de assunto.

   ![Email recebido](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configurar a oferta para enviar clientes potenciais ao ponto de extremidade HTTPS

Quando estiver pronto para configurar as informações de gerenciamento dos clientes potenciais para a oferta no portal de publicação, siga as etapas abaixo.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecione sua oferta e vá até a guia **Configuração de oferta**.

1. Na seção **Clientes potenciais**, selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Clientes potenciais":::

1. Na janela pop-up **Detalhes da conexão**, selecione **Ponto de extremidade HTTPS** para o **Destino do cliente potencial**. Cole a URL HTTP POST do fluxo que você criou seguindo as etapas anteriores no campo **URL do ponto de extremidade HTTPS**.
    ![Email de contato dos detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Em **Emails de contato**, insira os endereços de email das pessoas da sua empresa que devem receber notificações por email quando um novo cliente potencial for captado. Você pode inserir vários endereços de email, separando-os com um sinal de ponto e vírgula.

1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **Validar**. Se tiver se conectado com êxito, você terá um cliente potencial de teste no destino do cliente potencial.

>[!NOTE] 
>Conclua a configuração do restante da oferta e publique-a antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft os envia para o fluxo. Os clientes potenciais são roteados para o sistema de CRM ou para o endereço de email que você configurou.
