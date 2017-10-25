Agora que você adicionou uma condição, é hora de fazer algo interessante com os dados gerados pelo gatilho. Siga estas etapas para adicionar a ação **Salesforce - Obter objeto** . Essa ação obterá os dados sempre que um novo cliente potencial for criado. Você também adicionará uma segunda ação que usará os dados da ação Salesforce - Obter um objeto para enviar um email usando o conector do Office 365.  

Para configurar essa ação, você precisará fornecer as seguintes informações. Você notará como é fácil usar os dados gerados pelo gatilho como entrada para algumas das propriedades para o novo arquivo:

| Criar propriedade do arquivo | Descrição |
| --- | --- |
| Tipo de objeto |Esse é o tipo do objeto do Salesforce que lhe interessa. Alguns exemplos são Cliente Potencial, Conta, etc. |
| ID de objeto |Isso representa um identificador do objeto. |

1. Selecione o link **Adicionar uma ação** . Isso abre a caixa de pesquisa, na qual é possível procurar qualquer ação que você deseja realizar. Neste exemplo, as ações do Salesforce são pontos de interesse.      
   ![Imagem de ação do Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Digite *salesforce* para pesquisar as ações relacionadas ao salesforce.
3. Selecione **Salesforce - Obter objeto** como a ação a ser tomada.   **Observação**: será solicitado que você autorize o aplicativo lógico a acessar sua conta do Salesforce, caso já não tenha feito isso.    
   ![Imagem de ação do Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. O controle **Obter objeto** é aberto.  
5. Selecione *cliente potencial* como o tipo de objeto.
6. Selecione o controle **ID de Objeto** .
7. Selecione **...** para expandir a lista de tokens que podem ser usados como entrada para ações.       
   ![Imagem de ação do Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Selecione o controle **ID de Cliente Potencial** para abri-lo.   
   ![Imagem de ação do Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Observe que o token ID de Cliente Potencial agora está no controle de ID de Objeto, indicando que a ação Obter objeto procurará por um cliente potencial com uma ID igual à ID do cliente potencial do líder que disparou esse aplicativo lógico.  
   ![Imagem de ação do Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Salve seu trabalho. Ou seja, você adicionou a ação Obter objeto ao seu aplicativo lógico. O controle Obter objeto deve ser semelhante isso:     
    ![Imagem de ação do Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Agora que você adicionou uma ação para obter um cliente potencial, faça algo interessante com o cliente potencial recém-criado. Em uma empresa, você pode enviar um email para notificar uma lista de distribuição que um novo cliente potencial foi criado. Vamos usar o conector do Office 365 para enviar um email com algumas das informações relevantes do novo objeto de cliente potencial no Salesforce.  

1. Selecione **Adicionar uma ação** e digite *email* no controle de pesquisa. Isso filtra as ações relacionadas ao envio e recebimento de email.  
2. Selecione o item de lista **Office 365 Outlook - Enviar um email** . Se você ainda não tiver criado uma *conexão* com sua conta do Office 365, será solicitado que você insira suas credenciais do Office 365 para criá-la agora. Depois de terminar, o controle **Enviar um email** é aberto.        
   ![Imagem de ação do Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Insira o endereço de email para o qual você gostaria de enviar o email no controle **Para** .
4. No controle **Assunto**, digite *Novo cliente potencial criado* e selecione o token *Empresa*. Isso exibirá o campo *empresa* do novo cliente potencial criado no Salesforce.  
5. No controle **corpo** , você pode selecionar qualquer um dos tokens do novo objeto de cliente potencial e também pode inserir qualquer texto que você desejar exibir no corpo do email. Aqui está um exemplo:   
   ![Imagem de ação do Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Salve seu fluxo de trabalho.  

É isso. Seu aplicativo lógico está pronto.  

Agora você pode testar seu aplicativo lógico: no Salesforce, crie um novo cliente potencial que atenda à condição que você criou.  Se você seguiu totalmente esse passo a passo, basta criar um cliente potencial com um endereço de email contendo *amazon.com* . Depois de alguns segundos, seu aplicativo lógico deve ser disparado e os resultados podem ser semelhantes a:   
![Imagem de ação do Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  

