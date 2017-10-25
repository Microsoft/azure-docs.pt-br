Neste exemplo, mostrarei como usar o gatilho **SharePoint Online – Quando um novo item é criado** para iniciar um fluxo de trabalho do aplicativo lógico quando um novo item é criado em uma lista do SharePoint Online.

> [!NOTE]
> Você será solicitado a entrar na sua conta do SharePoint se você ainda não tiver criado uma *conexão* no SharePoint Online.  
> 
> 

1. Digite *sharepoint* na caixa de pesquisa no designer de aplicativos lógicos e selecione gatilho **SharePoint Online – Quando um novo item é criado** .  
   ![Imagem de gatilho 4 do SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. O controle **Quando um novo item é criado** aparece.  
   ![Imagem de gatilho 2 do SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Selecione uma **URL do Site**. Esse é o site do SharePoint Online que você deseja monitorar para novos itens dispararem o fluxo de trabalho.  
   ![Imagem de gatilho 3 do SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Selecione um **nome da lista**. Essa é a lista no site do SharePoint Online que você deseja monitorar para novos itens que vão disparar seu fluxo de trabalho.  
   ![Imagem de gatilho 4 do SharePoint Online](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e ações no fluxo de trabalho. Isso ocorrerá sempre que um novo item é criado na lista do SharePoint Online que você selecionou.  

