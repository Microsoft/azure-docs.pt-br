### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de Armazenamento de Blobs do Azure](../articles/storage/common/storage-create-storage-account.md), incluindo o nome da conta de armazenamento e sua chave de acesso. Essa informação é listada nas propriedades da conta de armazenamento no Portal do Azure. Saiba mais sobre o [Armazenamento do Azure](../articles/storage/common/storage-introduction.md).

Antes de usar sua conta do Armazenamento de Blobs do Azure em um aplicativo lógico, conecte-se à sua conta do Armazenamento de Blobs do Azure. Você pode fazer isso de forma fácil usando seu aplicativo lógico no portal do Azure.  

Conecte-se à sua conta do Armazenamento de Blobs do Azure seguindo essas etapas:  

1. Crie um aplicativo lógico. No designer de Aplicativos Lógicos, adicione um gatilho e, em seguida, uma ação. Selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e digite “blob” na caixa de pesquisa. Selecione uma das ações:  
   
    ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Se ainda não tiver criado conexões com o Armazenamento do Azure, você deverá fornecer os detalhes de conexão:   
   
    ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. Insira os detalhes da conta de armazenamento. As propriedades com um asterisco são obrigatórias.
   
   | Propriedade | Detalhes |
   | --- | --- |
   | Nome da Conexão * |Digite um nome para a conexão. |
   | Nome da Conta de Armazenamento do Azure * |Digite o nome da conta de armazenamento. O nome da conta de armazenamento é exibido nas propriedades de armazenamento no portal do Azure. |
   | Chave de Acesso da Conta de Armazenamento do Azure * |Insira a chave da conta de armazenamento. As chaves de acesso são exibidas nas propriedades de armazenamento no portal do Azure. |
   
    Essas credenciais são usadas para autorizar o aplicativo lógico a se conectar e acessar seus dados. 
4. Selecione **Criar**.
5. Observe que a conexão foi criada. Agora, continue com as outras etapas no seu aplicativo lógico: 
   
    ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

