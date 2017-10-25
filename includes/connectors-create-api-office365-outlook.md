#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma conta do [Office 365](https://office365.com)  

Antes de usar sua conta do Office 365 em um aplicativo lógico, autorize-o a se conectar à conta do Office 365. Você pode fazer isso de forma fácil usando seu aplicativo lógico no portal do Azure.  

Autorize seu aplicativo lógico a se conectar à sua conta do Office 365 com as seguintes etapas:

1. Crie um aplicativo lógico. No designer de Aplicativos Lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e então digite "office 365" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
    ![Etapa de criação de conexão com o Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Se ainda não tiver criado conexões com o Office 365, você será solicitado a entrar usando suas credenciais do Office 365:  
    ![Etapa de criação de conexão com o Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Selecione **Entrar** e insira seu nome de usuário e senha. Selecione **Entrar**:  
    ![Etapa de criação de conexão com o Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Essas credenciais são usadas para autorizar o aplicativo lógico a se conectar e acessar sua conta do Office 365. 
4. Observe que a conexão foi criada. Agora, continue com as outras etapas no seu aplicativo lógico:   
    ![Etapa de criação de conexão com o Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

