---
title: Tabela do Azure | Microsoft Docs
description: Configure o gerenciamento de clientes potenciais para a tabela do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a8ae3ab71b258d92d9761cc813b168717e44d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066374"
---
# <a name="lead-management-instructions-for-azure-table"></a>Instruções de gerenciamento de leads em tabelas do Azure

Este artigo descreve como configurar a Tabela do Azure para armazenar clientes potenciais de vendas. A Tabela do Azure permite que você armazene e personalize informações do cliente.

## <a name="to-configure-azure-table"></a>Para configurar a tabela do Azure

1.  Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2.  Após a sua conta do Azure, entre no [portal do Azure](https://portal.azure.com).
3.  No portal do Azure, crie uma conta de armazenamento. A próxima captura de tela mostra como criar uma conta de armazenamento. Para obter mais informações sobre preços de armazenamento, veja [preço de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copie a cadeia de conexão da conta de armazenamento para a chave e cole-a no campo **Cadeia de Conexão da Conta de Armazenamento** no Portal do Cloud Partner. Um exemplo de uma cadeia de caracteres de conexão é `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`
    
    ![Chave de armazenamento do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Você pode usar o [gerenciador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou qualquer outra ferramenta para ver os dados em sua tabela de armazenamento. Você também pode exportar os dados na Tabela do Azure.
dados.

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>**(Opcional)**  Use o Microsoft Flow com uma tabela do Azure

Você pode usar o [Microsoft Flow](https://docs.microsoft.com/flow/) para automatizar as notificações sempre que um cliente potencial for adicionado à tabela do Azure. Se você ainda não tem uma conta, você pode [inscrever-se em uma conta gratuita](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Exemplo de notificação de cliente potencial

Use este exemplo como um guia para criar um fluxo simples que automaticamente envia uma notificação por email quando um novo cliente potencial for adicionado a uma tabela do Azure. Este exemplo define uma recorrência para enviar informações de cliente potencial a cada hora se o armazenamento de tabela for atualizado.

1. Entre na sua conta da Microsoft Flow.
2. Na barra de navegação à esquerda, selecione **Meus Fluxos**.
3. Na barra de navegação à esquerda, selecione **+ Novo**.  
4. Na lista suspensa, selecione **+ Criar em branco**
5. Em Criar um fluxo em branco, selecione **Criar em branco**.

   ![Criar um novo fluxo do zero](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Na barra de pesquisa de conectores e gatilhos, digite **Gatilhos**.
7. Em **Gatilhos**, selecione **Recorrência**.
8. Na janela **Recorrência**, mantenha a configuração padrão de 1 para **Intervalo**. Na lista suspensa **Frequência**, selecione **Hora**.

   >[!NOTE] 
   >Embora este exemplo use um intervalo de 1 hora, você pode selecionar o intervalo e a frequência com que é melhor para suas necessidades de negócios.

   ![Defina a frequência de 1 hora de recorrência](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Selecione **+ Nova Etapa**.
10. Pesquise "Obter horário passado” e, em seguida, selecione **Obter horário passado** em Ações. 

    ![Encontre e selecione a ação passada](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. Na janela **Obter horário passado**, defina **Intervalo** como 1.  Na lista suspensa **Unidade de tempo**, selecione **Hora**.
    >[!IMPORTANT] 
    >Certifique-se de que essa unidade de intervalo e a hora correspondam ao intervalo e frequência que você configurou para a recorrência.

    ![Definir intervalo de tempo passado](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Você pode verificar seu fluxo a qualquer momento para verificar se cada etapa está configurada corretamente. Para verificar seu fluxo, selecione **Verificador de Fluxo** na barra de menus do Fluxo.

No próximo conjunto de etapas, você vai se conectar à sua tabela do Azure e configurar a lógica de processamento para lidar com novos clientes potenciais.

1. Depois de Obter etapa passada, selecione **+ Nova etapa**e, em seguida, pesquise por "Obter entidades".
2. Em **Ações**, selecione **Obter entidades**e, em seguida, selecione **Mostrar opções avançadas**.
3. Na janela **Detalhes**, forneça informações para os seguintes campos:

   - **Tabela** – insira o nome do seu Armazenamento de Tabelas do Azure. A próxima captura de tela mostra o prompt quando "MarketPlaceLeads" é inserido para este exemplo. 

     ![Escolha um valor personalizado para o nome da tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Consulta de filtro** – clique neste campo e o ícone Obter horário passado é exibido em uma janela pop-up. Selecione **Horário passado** upara sá-lo como o carimbo de hora para filtrar a consulta. Como alternativa, você pode colar essa função para o campo: `gt datetime'@{body('Get_past_time')}'`

     ![Configurar a função de filtro de consulta](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Selecione **Nova etapa** para adicionar uma condição para verificar a tabela do Azure para novos clientes potenciais.

   ![Use a Nova etapa para adicionar uma condição para verificar a tabela do Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. Na janela **Escolher uma ação**, selecione **Ações**e, em seguida, selecione o controle de **Condição**.

     ![Adicionar controle da condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. Na janela **Condição**, selecione o campo **Escolher um valor** e, em seguida, selecione **Expressão** na janela pop-up.
7. Cole `length(body('Get_entities')?['value'])` no campo ***fx***. Selecione **OK** para adicionar essa função. Ao terminar de configurar a condição:

   - Escolha “é maior que” na lista suspensa.
   - Digite 0 como o valor 

     ![Adicione uma função para a condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Configure a ação para executar com base no resultado da condição.

     ![Configurar ação com base nos resultados da condição](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Se a condição for resolvida para **Se nenhum**, não faça nada. 
10. Se a condição for resolvida para **Em caso afirmativo**, dispare uma ação que conecta sua conta do Office 365 para enviar um email. Escolha **Adicionar uma ação**.
11. Selecione **Enviar um email**. 
12. Na janela **Enviar um email**, forneça informações para os seguintes campos:

    - **Para** -Insira um endereço de email para todos que obterão essa notificação.
    - **Assunto** – Indique o assunto do email. Por exemplo:  Novos clientes potenciais!
    - **Corpo**:   Adicione o texto que você deseja incluir em cada email (opcional) e, em seguida, cole no corpo `('Get_entities')?['value']` como uma função para inserir informações de cliente potencial.

      >[!NOTE] 
      >Você pode inserir os pontos de dados estáticos ou dinâmicos adicionais para o corpo deste email.

       ![Configurar o email de notificação de cliente potencial](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Selecione **Salvar** para salvar o fluxo. O Microsoft Flow automaticamente irá testar o fluxo de erros. Se não houver erros, o fluxo começa a ser executado depois que é salvo.

A próxima captura de tela mostra um exemplo de como o fluxo final deve parecer.

 ![Sequência do fluxo final](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>Gerenciando seu fluxo

É fácil gerenciar seu fluxo depois que ser executado.  Você tem controle total sobre seu fluxo. Por exemplo, você pode interrompê-lo, editá-lo, ver um histórico e obter análises. A próxima captura de tela mostra as opções que estão disponíveis para gerenciar um fluxo. 

 ![Gerenciar um fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

O fluxo continua em execução até você pará-lo usando a opção **Desativar fluxo**.

Se você não estiver recebendo quaisquer notificações de email de cliente potencial, significa que novos clientes potenciais ainda não foram adicionados à tabela do Azure. Se houver quaisquer falhas de fluxo, você receberá um email semelhante ao exemplo na próxima captura de tela.

 ![Notificação de e-mail de falha de fluxo](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Próximas etapas

[Configurar clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)