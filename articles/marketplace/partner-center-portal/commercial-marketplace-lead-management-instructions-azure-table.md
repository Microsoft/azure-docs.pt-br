---
title: Gerenciamento de clientes potenciais com o armazenamento de tabela do Azure - Marketplace comercial do Azure
description: Saiba como usar o armazenamento de tabela do Azure para configurar clientes potenciais para o Microsoft AppSource e o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586842"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Usar o armazenamento de tabela do Azure para gerenciar clientes potenciais do Marketplace comercial

Se o sistema de gerenciamento de relacionamentos com o cliente (CRM) não for explicitamente compatível com o Partner Center para receber os clientes potenciais do Microsoft AppSource e do Azure Marketplace, você pode usar um armazenamento de tabela do Azure para lidar com esses clientes potenciais. Você pode optar por exportar os dados e importá-los no sistema de CRM. Este artigo explica como criar uma conta de armazenamento do Azure e uma tabela nessa conta. Além disso, você pode criar um novo fluxo com o Power Automate para enviar uma notificação por email quando sua oferta receber um cliente potencial.

## <a name="configure-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Após a sua conta do Azure, entre no [portal do Azure](https://portal.azure.com).
1. No portal do Azure, use o seguinte procedimento para criar uma conta de armazenamento:

    1. Selecione **+ Criar um recurso** na barra de menu esquerda. O painel **Novo** é exibido à direita.
    1. Selecione **Armazenamento** no painel **Novo**. Uma lista **Em destaque** aparece à direita.
    1. Selecione a **conta de armazenamento**  para iniciar a criação da conta. Siga as instruções para [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Etapas para criar uma conta de armazenamento do Azure.":::

        Para obter mais informações sobre contas de armazenamento, veja [Tutorial de Início Rápido](../../storage/index.yml). Para obter mais informações sobre preços de armazenamento, veja [Preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

1. Aguarde até que sua conta de armazenamento seja provisionada. Esse processo normalmente leva alguns minutos.

## <a name="create-a-table-in-your-storage-account"></a>Criar uma tabela em sua conta de armazenamento

1. Na **Home** page do portal do Azure, selecione **Ver todos os seus recursos** para acessar sua conta de armazenamento. Você também pode selecionar **Todos os recursos** na barra de menus à esquerda da portal do Azure.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Acesse sua conta de armazenamento do Azure.":::

1. No painel da sua conta de armazenamento, selecione **Chaves de acesso** e copie o valor da **cadeia de conexão** para a chave. Salve esse valor, pois ele é o valor da **Cadeia de conexão da conta de armazenamento** que você precisará fornecer ao portal de publicação para receber clientes potenciais para sua oferta no Azure Marketplace.

    Aqui há um exemplo de uma cadeia de conexão.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Chave de armazenamento do Azure.":::


1. No painel da sua conta de armazenamento, escolha **Tabelas** e **+ Tabela** para criar uma tabela. Insira um nome para a tabela e selecione **OK**. Salve esse valor, pois você precisará dele se quiser configurar um fluxo para receber notificações por email quando os clientes potenciais forem recebidos.

    ![Tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Você pode usar o [Gerenciador de Armazenamento do Microsoft Azure](https://www.storageexplorer.com) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na tabela do Azure.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(Opcional) Use o Power Automate para obter notificações de clientes potenciais

Você pode usar o [Power Automate](/flow/) para automatizar as notificações sempre que um cliente potencial for adicionado à sua tabela do Armazenamento do Microsoft Azure. Se você ainda não tem uma conta, pode [inscrever-se em uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

O exemplo cria um fluxo que envia automaticamente uma notificação por email quando um novo cliente potencial é adicionado ao armazenamento de tabela do Azure. Este exemplo define uma recorrência para enviar informações de cliente potencial a cada hora se o armazenamento de tabela for atualizado.

1. Entre na sua conta do Power Automate.
1. Na barra à esquerda, selecione **Meus Fluxos**.
1. Na barra superior, selecione **+ Novo**.
1. Na lista suspensa, selecione **+ Agendado--de branco**.

   ![Meus fluxos + Agendado--de branco](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. Na janela **Criar um fluxo agendado**, para **Repetir a cada**, selecione **1** para o intervalo e **Hora** para a frequência. Além disso, dê um nome ao fluxo, se desejar. Selecione **Criar**.

   >[!NOTE]
   >Embora este exemplo use um intervalo de uma hora, você pode selecionar o intervalo e a frequência com que é melhor para suas necessidades de negócios.

   ![Criar um fluxo agendado](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Selecione **+ Nova Etapa**.
1. Na janela **Escolher uma ação**, procure **Obter horário passado**. Em **ações**, selecione **Obter horário passado**.

   ![Escolher uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. Na janela **Obter horário passado**, defina **Intervalo** como **1**. Na lista suspensa **Unidade de tempo**, escolha **Hora**.

    >[!IMPORTANT]
    >Verifique se o intervalo e a unidade de tempo que você enviou na etapa 8 correspondem ao intervalo e à frequência que você configurou para recorrência na etapa 5.

    ![Definir Obter horário passado](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente. Para verificar seu fluxo, selecione **Verificador de Fluxo** na barra de menus **Fluxo**.

   No próximo conjunto de etapas, você vai se conectar à sua tabela e configurar a lógica de processamento para lidar com novos clientes potenciais.

1. Selecione **+ Nova Etapa**. Em seguida, pesquise **Obter entidades** na janela **Escolher uma ação**.
1. Em **Ações**, escolha **Obter entidades (Armazenamento de Tabelas do Azure)** .
1. Na janela **Armazenamento de Tabelas do Azure**, inclua informações nas seguintes caixas e selecione **Criar**:

    * **Nome da Conexão**: Forneça um nome significativo para a conexão que você está estabelecendo entre esse fluxo e a tabela.
    * **Nome da Conta de Armazenamento**: Informe o nome da conta de armazenamento para sua tabela. Você pode encontrar esse nome na página **Chaves de acesso** da conta de armazenamento.
    * **Chave de Armazenamento Compartilhada**: Forneça o valor de chave para sua conta de armazenamento da tabela. Você pode encontrar esse valor na página **Chaves de acesso** da conta de armazenamento.

      ![Janela Armazenamento de Tabelas do Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Depois de selecionar **Criar**, a janela **Obter entidades** será exibida. Aqui, selecione **Mostrar opções avançadas** e inclua informações nas seguintes caixas:

   * **Tabela**: Selecione o nome da sua tabela (de [Criar uma tabela](#create-a-table-in-your-storage-account)). A imagem a seguir mostra o prompt quando a `marketplaceleads` tabela está selecionada para este exemplo.

     ![Obter janela de entidades](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Filtrar Consulta**: Marque essa caixa e cole essa função na caixa: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Obter entidades, caixa Filtrar Consulta](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Agora que você concluiu a configuração da conexão com a tabela do Azure, selecione **Nova Etapa** para adicionar uma condição que verifique a tabela do Azure em busca de novos clientes potenciais.

1. Na janela **Escolha uma ação**, selecione **Ações**. Em seguida, selecione **Controle de Condição**.

    ![Escolha uma janela de ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. Na janela **Condição**, selecione **Escolher um valor**. Em seguida, selecione **Expressão** na janela pop-up.

1. Cole `length(body('Get_entities')?['value'])` na caixa **fx**. Selecione **OK** para adicionar essa função.

1. Ao terminar de configurar a condição:
    1. Escolha **é maior que** na lista suspensa.
    2. Digite **0** como o valor.

        ![Janela de condição](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Nas próximas etapas, configure a ação para executar com base no resultado da condição:

   * Se a condição for resolvida para **Em caso negativo**, não faça nada.
   * Se a condição for resolvida como **Sim**, dispare uma ação que conecta sua conta corporativa ou de estudante para enviar um email. 

1. Selecione **Adicionar uma ação** em **Em caso afirmativo**.

    ![Janela de condição, Em caso afirmativo, adicionar uma ação](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Selecione **Enviar um email (Office 365 Outlook)** .

    ![Janela de condição, Em caso afirmativo, enviar um email](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Para usar um provedor de email diferente, procure e selecione **Enviar uma notificação por email (email)** como a ação. As instruções mostram como configurar usando o Office 365 Outlook, mas as instruções são semelhantes para um provedor de email diferente.

1. Na janela do Office 365 Outlook, inclua informações nas seguintes caixas:

    1. **Para**: Insira um endereço de email para todos que obterão essa notificação.
    1. **Entidade**: Indique o assunto do email. Um exemplo é **Novos clientes potenciais!**
    1. **Corpo**: Adicione o texto que você deseja incluir em cada email (opcional) e, em seguida, cole no corpo `body('Get_entities')?['value']`.

    >[!NOTE]
    >Você pode inserir os pontos de dados estáticos ou dinâmicos adicionais para o corpo deste email.

    ![Janela de condição, Em caso positivo, janela do Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Selecione **Salvar** para salvar o fluxo. O Power Automate testa automaticamente o fluxo em busca de erros. Se não houver erros, o fluxo começa a ser executado depois que é salvo.

A imagem a seguir mostra um exemplo de como o fluxo final deve parecer.

![Um exemplo do fluxo final](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Gerenciar seu fluxo

É fácil gerenciar seu fluxo depois que ser executado. Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A imagem a seguir mostra as opções que estão disponíveis para gerenciar um fluxo.

 ![Gerenciar um fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

O fluxo continua em execução até você pará-lo usando a opção **Desativar fluxo**.

Se você não estiver recebendo quaisquer notificações de email de cliente potencial, significa que novos clientes potenciais ainda não foram adicionados à tabela do Azure. Se houver quaisquer falhas de fluxo, você receberá um email semelhante ao exemplo.

 ![Notificação de e-mail de falha de fluxo](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Configurar a oferta para enviar clientes potenciais à tabela do Azure

Quando estiver pronto para configurar as informações de gerenciamento de clientes potenciais para suas ofertas no portal de publicação, siga as etapas abaixo.

1. Vá para a página **Configuração de oferta** da sua oferta.

1. Na seção **Clientes potenciais**, selecione **Conectar**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Clientes potenciais":::

1. Na janela pop-up **Detalhes da Conexão**, selecione **Tabela do Azure** para o **Destino de Clientes Potenciais**. 
     ![Gerenciamento de clientes potenciais, Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. Cole a cadeia de conexão da conta de armazenamento do Azure criada seguindo as etapas anteriores na caixa **Cadeia de conexão da conta de armazenamento**.
     ![Gerenciamento de clientes potenciais, Detalhes da conexão, conta de armazenamento](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **Email de contato**: Insira os endereços de email para as pessoas em sua empresa que devem receber notificações por email quando um novo cliente potencial for recebido. Você pode inserir vários endereços de email, separando-os com sinais de ponto e vírgula.

1. Selecione **OK**.

Para verificar se você se conectou com êxito a um destino de cliente potencial, selecione o botão **Validar**. Se tiver se conectado com êxito, você terá um cliente potencial de teste no destino do cliente potencial.

>[!NOTE]
>Conclua a configuração do restante da oferta e publique-a antes de receber clientes potenciais para a oferta.

Quando os clientes potenciais são gerados, a Microsoft os envia para a tabela do Azure. Se você configurou um fluxo, um email também será enviado para o endereço de email que você configurou.

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre gerenciamento de leads e solução de problemas](../lead-management-faq.md)
