---
title: Armazenamento de tabelas do Azure | Azure Marketplace
description: Configure o gerenciamento de leads no armazenamento de tabelas do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a53ed93813215655c4a165faa0bce36d9249e8e6
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227896"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instruções de gerenciamento de Lead para armazenamento de tabelas

Este artigo descreve como configurar o armazenamento de tabelas do Azure para gerenciar clientes potenciais de vendas. O armazenamento de tabelas ajuda a armazenar e modificar as informações do cliente.

## <a name="configure-table-storage"></a>Configurar o armazenamento de Tabela

1. Se você não tiver uma conta do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Depois que sua conta estiver ativa, entre no [portal do Azure](https://portal.azure.com).
1. No portal do Azure, siga estas etapas:  
    1. Selecione **+ criar um recurso** no painel no lado esquerdo. O **novo** painel será aberto.
    1. No painel **novo** , selecione **armazenamento**. Uma lista em **destaque** será aberta no lado direito.
    1. Selecione **conta de armazenamento**. Em seguida, siga as instruções em [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Para obter mais informações sobre contas de armazenamento, consulte os [tutoriais de início rápido](https://docs.microsoft.com/azure/storage/). Para obter informações sobre preços, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Aguarde até que sua conta de armazenamento seja provisionada, o que normalmente leva alguns minutos. Em seguida, acesse a conta do home page do portal do Azure: Selecione **Ver todos os seus recursos** ou **todos os recursos** no painel de navegação.

    ![Acessar sua conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. No painel da sua conta de armazenamento, copie a cadeia de conexão da conta de armazenamento para a chave. Cole-o no campo **cadeia de conexão** da conta de armazenamento na portal do Cloud Partner.

    Exemplo de cadeia de conexão:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Você pode usar [Gerenciador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou uma ferramenta semelhante para ver os dados no armazenamento de tabelas. Você também pode exportar dados dele.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Usar Microsoft Flow com armazenamento de tabela (*opcional*)

Você pode usar [Microsoft Flow](https://docs.microsoft.com/flow/) para enviar notificações automaticamente quando um cliente potencial for adicionado ao armazenamento de tabelas. Se você não tiver uma conta de Microsoft Flow, [Inscreva-se para obter uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

Este exemplo mostra como criar um fluxo básico. O fluxo envia automaticamente uma notificação por email por hora quando novos clientes potenciais são adicionados ao armazenamento de tabelas.

1. Entre na sua conta da Microsoft Flow.
1. No painel de navegação no lado esquerdo, selecione **meus fluxos**.
1. Na barra de navegação superior, selecione **+ novo**.  
1. Na lista suspensa, selecione **+ criar em branco**.
1. Em **criar um fluxo de em branco**, selecione **criar em branco**.

   ![Criar um novo fluxo do zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na barra de pesquisa de conectores e gatilhos, digite **Gatilhos**.
1. Em **Gatilhos**, selecione **Recorrência**.
1. Na janela  de recorrência, mantenha a configuração padrão de **1** para **intervalo**. Na lista suspensa **frequência** , selecione **hora**.

   >[!NOTE] 
   >Este exemplo usa um intervalo de uma hora. Mas você pode selecionar um intervalo e uma frequência que melhor se adaptam às suas necessidades de negócios.

   ![Definir uma frequência de 1 hora para recorrência](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Selecione **+ nova etapa**.
1. Pesquise o **Get passado**e selecione **obter o horário passado** em **escolher uma ação**.

    ![Localizar e selecionar a ação "obter última hora"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. Na janela **obter tempo anterior** , defina o **intervalo** como **1**.  Na lista suspensa **unidade de tempo** , selecione **hora**.
    >[!IMPORTANT] 
    >Verifique se o **intervalo** e a **unidade de tempo** correspondem ao intervalo e à frequência que você configurou para a recorrência (etapa 8).

    ![Definir o intervalo de obtenção de tempo passado](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente: Selecione **Verificador de fluxo** na barra de menus do fluxo.

No próximo conjunto de etapas, você se conecta à tabela de armazenamento e configura a lógica de processamento para lidar com novos clientes potenciais.

1. Após a etapa **obter hora anterior** , selecione **+ nova etapa**e, em seguida, pesquise **obter entidades**.
1. Em **Ações**, selecione **Obter entidades**e, em seguida, selecione **Mostrar opções avançadas**.
1. Na janela **obter entidades** , preencha os seguintes campos:

   - **Table**: o nome do armazenamento de tabela. A imagem a seguir mostra "MarketPlaceLeads" inserido:

     ![Escolha um valor personalizado para o nome da tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Consulta de filtro**: Quando você seleciona esse campo, o ícone **obter hora passada** é exibido em uma janela pop-up. Selecione **última hora** para usar esse valor como um carimbo de data/hora para filtrar a consulta. Ou, você pode colar a seguinte função no campo:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Configurar a função de consulta de filtro](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Selecione **nova etapa** para adicionar uma condição para verificar o armazenamento de tabela em busca de novos clientes potenciais.

   ![Use a "nova etapa" para adicionar uma condição para verificar o armazenamento da tabela](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. Na janela **escolher uma ação** , selecione **ações**e, em seguida, selecione **controle de condição**.

     ![Adicionar um controle de condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. Na janela **condição** , selecione **escolher um valor**e, em seguida, selecione **expressão** na janela pop-up.
1. Cole `length(body('Get_entities')?['value'])` no campo ***FX*** . Selecione **OK** para adicionar essa função. 



     ![Adicione uma função para a condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Configure a ação para executar com base no resultado da condição.

    1. Select **é maior que** a partir da lista suspensa.
   1. Insira **0** como o valor.

     ![Configurar uma ação com base nos resultados da condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Se a condição for resolvida como "se não", não faça nada.

    Se a condição for resolvida como "se sim", acione uma ação que conecta sua conta do Office 365 para enviar um email:
   1. Escolha **Adicionar uma ação**.
   1. Selecione **Enviar um email**.
   1. Na janela **enviar um email** , insira as informações nos seguintes campos:

      - **Para**: um endereço de email para todos que receberão a notificação.
      - **Assunto**: um assunto para o email. Por exemplo:  *Novos leads!*
      - **Corpo**: o texto que você deseja incluir em cada email (opcional). Cole `body('Get_entities')?['value']` também como uma função para inserir informações de Lead.

        >[!NOTE] 
        >Você pode inserir pontos de dados estáticos ou dinâmicos adicionais no corpo do email.

      ![Configurar o email de notificação de cliente potencial](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Selecione **Salvar** para salvar o fluxo. Microsoft Flow irá testá-lo automaticamente para erros. Se não houver erros, o fluxo começa a ser executado depois que é salvo.

    A imagem a seguir mostra um exemplo de como o fluxo final deve ser examinado.

    [![Sequência de fluxo final](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Selecione a imagem para aumentá-la.* )

### <a name="manage-your-flow"></a>Gerenciar seu fluxo

É fácil gerenciar seu fluxo depois que ele está em execução. Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A imagem a seguir mostra as opções de gerenciamento de fluxo.

 ![Opções de gerenciamento de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo continua em execução até que você selecione **desativar o fluxo**.

Se você não estiver obtendo notificações por email de Lead, nenhum novo cliente potencial será adicionado ao armazenamento de tabelas.
Você receberá um email como o exemplo a seguir se ocorrer uma falha de fluxo:

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Próximas etapas

[Configurar clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
