Agora que você adicionou um gatilho, é hora de fazer algo interessante com os dados gerados por ele. Siga estas etapas para adicionar a ação **SharePoint Online – Criar arquivo** . Essa ação criará um arquivo no SharePoint Online sempre que o novo item de gatilho for acionado. 

Para configurar essa ação, você precisará fornecer as seguintes informações. Ao fornecer essas informações, você notará que é fácil usar dados gerados pelo gatilho como entrada para algumas das propriedades para o novo arquivo:

| Criar propriedade do arquivo | Descrição |
| --- | --- |
| URL do site |Esta é a URL do site do SharePoint Online em que você deseja criar o novo arquivo. Selecione o site na lista apresentada. |
| Caminho da pasta |Essa é a pasta (na URL de Site selecionado na etapa anterior) em que o novo arquivo será colocado. Procure e selecione a pasta. |
| Nome do arquivo |Esse é o nome do arquivo que está sendo criado. |
| Conteúdo do arquivo |O conteúdo que será gravado no arquivo. |

1. Selecione **+ Nova etapa** para adicionar a ação.  
   ![Imagem de ação 1 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Selecione o link **Adicionar uma ação** . Isso abre a caixa de pesquisa, na qual é possível procurar qualquer ação que você deseja realizar. Neste exemplo, as ações do SharePoint são pontos de interesse.    
   ![Imagem de ação 2 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Digite *sharepoint* para pesquisar as ações relacionadas ao SharePoint.
4. Selecione **SharePoint Online – Criar arquivo** como a ação a ser tomada.   **Observação**: você precisará autorizar seu aplicativo lógico para acessar sua conta do SharePoint se você não tiver criado uma conexão com o SharePoint Online anteriormente.    
   ![Imagem de ação 3 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-3.png)    
5. O controle **Criar arquivo** é aberto.   
   ![Imagem de ação 4 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Selecione **URL do Site** e navegue para localizar o site no qual você deseja criar o arquivo.     
   ![Imagem de ação 5 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Selecione **Caminho da pasta** e navegue para localizar a pasta em que o novo arquivo será colocado.  
   ![Imagem de ação 6 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Selecione o controle **Nome de arquivo** e insira o nome do arquivo que você deseja criar. Aqui, você pode inserir o nome do arquivo diretamente ou você pode usar qualquer uma das propriedades do gatilho criado anteriormente. Você pode fazer isso selecionando propriedades da lista de **Saídas de quando um novo item é criado**. Esta lista é somente exibição depois de selecionar o controle **Nome do arquivo** . Nesta explicação passo a passo, selecionei ID (ID do novo item de lista) como o nome do arquivo que está sendo criado pela ação **SharePoint Online – Criar arquivo** .    
   ![Imagem de ação 7 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Selecione o controle **Conteúdo do arquivo** e digite o conteúdo que será gravado no arquivo que será criado. Para o conteúdo do arquivo, observe que você pode usar as propriedades do gatilho criado anteriormente. Basta selecionar as propriedades da lista apresentada. Como alternativa, você pode inserir o texto **Conteúdo do arquivo** diretamente no controle. Neste exemplo, selecionei algumas propriedades e adicionei espaços e um hífen entre cada propriedade.        
   ![Imagem de ação 8 do SharePoint Online](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Salve as alterações no fluxo de trabalho  
11. Parabéns, agora você tem um aplicativo lógico totalmente funcional que é disparado quando um novo item é adicionado a uma lista do SharePoint Online. O aplicativo criará um arquivo, usando algumas das propriedades do novo item de lista.  Agora você pode testá-lo, criando um novo item na lista do SharePoint. 

