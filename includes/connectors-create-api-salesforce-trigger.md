Neste passo a passo, você aprenderá a usar o gatilho **Salesforce - Quando um objeto é criado** para iniciar um fluxo de trabalho de aplicativo lógico quando um novo cliente potencial é criado no Salesforce.

> [!NOTE]
> Será solicitado que você entre na sua conta do Salesforce se ainda não tiver criado uma *conexão* com o Salesforce.  
> 
> 

1. Digite *salesforce* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **Salesforce — Quando um objeto é criado** .  
   ![Imagem de gatilho do Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. O controle **Quando um objeto é criado** é exibido.  
   ![Imagem de gatilho do Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Escolha o **Tipo de Objeto** e selecione *Cliente Potencial* na lista de objetos. Nessa etapa, instruímos sobre a criação de um gatilho que notificará seu aplicativo lógico sempre que um novo cliente potencial for criado no Salesforce.   
   ![Imagem de gatilho do Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. É isso. Você criou o gatilho. No entanto, é necessário criar pelo menos uma ação para que esse seja um aplicativo lógico válido.    
   ![Imagem de gatilho do Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e as ações no fluxo de trabalho quando um novo item for criado no Salesforce.  

