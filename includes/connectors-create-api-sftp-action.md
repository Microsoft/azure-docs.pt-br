Agora que você adicionou um gatilho, é hora de fazer algo interessante com os dados gerados por ele. Siga estas etapas para adicionar a ação **SFTP - extrair pasta** . Esta ação extrairá o conteúdo de um arquivo se as condições definidas forem atendidas. 

Para configurar essa ação, você precisará fornecer as seguintes informações. Você notará como é fácil usar os dados gerados pelo gatilho como entrada para algumas das propriedades para o novo arquivo:

| SFTP - extrair propriedade da pasta | Descrição |
| --- | --- |
| Caminho do arquivo de origem |Este é o caminho para o arquivo que está sendo extraído. Você pode selecionar um dos tokens de uma ação anterior ou procurar no servidor SFTP para encontrar o caminho do arquivo. |
| Caminho da pasta de destino |Este é o caminho no qual os arquivos extraídos serão colocados. Você pode selecionar um dos tokens de uma ação anterior como a pasta de destino ou procurar no servidor SFTP e selecionar um caminho. |
| Substituir? |Indica se, caso um arquivo com o mesmo nome que o arquivo extraído seja encontrado no caminho da pasta de destino, o arquivo existente deve ser substituído ou não. |

Vamos começar adicionando a ação para extrair os arquivos se a condição definida anteriormente for avaliada como *True*. 

1. Selecione **Adicionar uma ação**.        
   ![Imagem de condição de ação de SFTP 6](./media/connectors-create-api-sftp/condition-6.png)   
2. Selecione a ação **SFTP - Extrair pasta**      
   ![Imagem de condição de ação de SFTP 7](./media/connectors-create-api-sftp/condition-7.png)   
3. Selecione **Caminho do arquivo de origem**              
   ![Imagem de condição de ação de SFTP 9](./media/connectors-create-api-sftp/condition-9.png)   
4. Selecione o token **Caminho do arquivo** . Isso indica que você usará o caminho do arquivo que o gatilho encontrou como o caminho do arquivo de origem.           
   ![Imagem de condição de ação de SFTP 10](./media/connectors-create-api-sftp/condition-10.png)   
5. Selecione **Caminho da pasta de destino**           
   ![Imagem de condição de ação de SFTP 11](./media/connectors-create-api-sftp/condition-11.png)   
6. Selecione o token **Caminho do arquivo** . Isso indica que você usará o caminho do arquivo que o gatilho encontrou como o caminho de destino para os arquivos extraídos.   
7. Digite *\ExtractedFile* no controle **Caminho da pasta de destino**. Faça isso logo após o token do caminho do arquivo no controle Caminho de pasta de destino.         
   ![Imagem de condição de ação de SFTP 12](./media/connectors-create-api-sftp/condition-12.png)   
8. Digite *True* no controle **Substituir?* para indicar que os arquivos existentes deverão ser substituídos se tiverem o mesmo nome que os arquivos extraídos.      
   ![Imagem de condição de ação de SFTP 13](./media/connectors-create-api-sftp/condition-13.png)   
9. Salve as alterações no fluxo de trabalho  

