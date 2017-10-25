Vamos adicionar um gatilho.

1. Digite *sftp* na caixa de pesquisa no designer de aplicativos lógicos e selecione o gatilho **SFTP - Quando um arquivo é criado ou modificado**   
   ![Imagem do gatilho de SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. O controle **Quando um arquivo é adicionado ou modificado** é aberto  
   ![Imagem do gatilho de SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Escolha **...** , localizado no lado direito do controle. Isso abre o controle de seletor de pasta   
   ![Imagem do gatilho de SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
4. Selecione o **SFTP** para selecionar a pasta raiz da pasta para monitorar arquivos novos ou modificados. Observe que a pasta raiz agora é exibida no controle **Pasta** .  
   ![Imagem do gatilho de SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e as ações no fluxo de trabalho quando um arquivo é modificado ou criado na pasta SFTP específica. 

> [!NOTE]
> Para que um aplicativo lógico funcione, ele deve conter pelo menos um gatilho e uma ação. Siga as etapas na próxima seção para adicionar uma ação.  
> 
> 

