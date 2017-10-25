

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Novo** > **Web + Móvel** > **Hub de Notificação**.
   
      ![Portal do Azure - criar um hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Na caixa **Hub de Notificação** , digite um nome exclusivo. Selecione sua **Região**, **Assinatura** e **Grupo de Recursos** (se você já tiver um). 
   
    Se você já tiver um namespace de barramento de serviço que você deseja criar o hub, faça o seguinte:

    a. Na área **Namespace**, selecione o link **Selecione Existente**. 
   
    b. Selecione **Criar**.

    Se você ainda não tiver um namespace de barramento de serviço, você pode usar o nome padrão, que é criado com base no nome do hub (se o nome do namespace estiver disponível).
   
      ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    Depois de criar o namespace e hub de notificação, abra o portal do Azure. 
   
      ![Portal do Azure - página do portal do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. Selecione **Configurações** > **Políticas de Acesso**. Observe as cadeias de caracteres de duas conexão que estão disponíveis para você. Você precisará delas para tratar notificações por push mais tarde.
   
      ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

