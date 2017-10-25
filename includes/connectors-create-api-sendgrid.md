### <a name="prerequisites"></a>Pré-requisitos
* Uma conta [SendGrid](https://www.SendGrid.com/) 

Antes de usar sua conta do SendGrid em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do SendGrid. Felizmente, você pode fazer isso de forma fácil usando seu aplicativo lógico no Portal do Azure. 

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do SendGrid:

1. Para criar uma conexão com o SendGrid, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *SendGrid* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar:   
   ![Etapa 1 do SendGrid](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Se você não tiver criado nenhuma conexão SendGrid antes, será solicitado a fornecer suas credenciais do SendGrid. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados da sua conta do SendGrid:  
   ![Etapa 2 do SendGrid](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico:   
   ![Etapa 3 do SendGrid](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

