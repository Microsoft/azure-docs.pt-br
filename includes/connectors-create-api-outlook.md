## <a name="connect-to-outlookcom"></a>Conectar ao Outlook.com
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Outlook.com

Antes de usar sua conta do Outlook.com em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do Outlook.com. Felizmente, você pode fazer isso de forma fácil usando seu aplicativo lógico no Portal do Azure. 

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do Outlook.com:

1. Todos os aplicativos lógicos precisam ser iniciados por um gatilho, de modo que, depois que você cria seu aplicativo lógico, o designer é aberto e exibe uma lista de gatilhos que pode usar para iniciar seu aplicativo lógico:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Insira "outlook" na caixa de pesquisa. Observe que a lista está filtrada para listar todos os gatilhos com "Outlook" no nome: ![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Selecione **Office 365 Outlook - no novo email**.   
   Se você não tiver criado quaisquer conexões Outlook.com antes, será solicitado a fornecer suas credenciais do Outlook.com. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do Outlook.com e usá-los: ![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Forneça suas credenciais para o Outlook e entre: ![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   É isso. Agora você criou uma conexão com o Outlook. Esta conexão estará disponível para uso em qualquer outro aplicativo lógico que você criar.

