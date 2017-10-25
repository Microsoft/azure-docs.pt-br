### <a name="prerequisites"></a>Pré-requisitos
Você deve ter uma conta do [Barramento de Serviço](https://azure.microsoft.com/services/service-bus/).  

Antes de usar sua conta do Barramento de Serviço do Azure em um aplicativo lógico, você deve autorizá-lo a se conectar à conta do barramento de serviço. Felizmente, você pode fazer isso com facilidade usando seu aplicativo lógico no Portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à conta do Barramento de Serviço:  

1. Para criar uma conexão com o Barramento de Serviço, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa. Em seguida, digite **barramento de serviço** na caixa de pesquisa. Selecione o gatilho ou ação que você quer usar.  
    ![Imagem de conexão do Barramento de Serviço 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Se você ainda não tiver criado nenhuma conexão com o Barramento de Serviço, será solicitado que você informe suas credenciais do Barramento de Serviço. Essas credenciais são usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do Barramento de Serviço e a usá-los. O conector do Barramento de Serviço precisa da cadeia de conexão para o namespace do Barramento de Serviço. Ele também precisa **Gerenciar** permissões. Uma boa maneira de saber se a cadeia de conexão é para o namespace ou para uma entidade específica é ver se ele contém o parâmetro `EntityPath`. Em caso afirmativo, não será a cadeia de conexão correta para um aplicativo lógico.  
    ![Cadeia de conexão do Barramento de Serviço](./media/connectors-create-api-servicebus/connectionstring.png)
3. Após receber a cadeia de conexão para o namespace, você poderá usá-la para a conexão de API nos Aplicativos Lógicos.  
    ![Imagem de conexão do Barramento de Serviço 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Observe que a conexão foi criada e agora você pode continuar com as outras etapas em seu aplicativo lógico.  
    ![Imagem de conexão do Barramento de Serviço 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

