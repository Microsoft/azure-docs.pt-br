1. Em uma nova janela, entre no [portal do Azure](https://portal.azure.com/).

2. No painel esquerdo, selecione **Novo** > **Bancos de Dados** > **Azure Cosmos DB** > **Criar**.
   
   ![Painel “Bancos de dados” do portal do Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Em **Nova conta**, especifique a configuração desejada para essa conta do Azure Cosmos DB. 

    Com o BD Cosmos do Azure, você pode escolher um dos quatro modelos de programação: Gremlin (gráfico), MongoDB, SQL (DocumentDB) e Tabela (chave-valor). Atualmente, cada modelo requer uma conta separada.
       
    Neste artigo de Início Rápido, programaremos a API do Graph de modo que você escolherá **Gremlin (gráfico)** quando preencher o formulário. Se você tiver dados de documento de um aplicativo de catálogo, dados de chave/valor (tabela) ou dados migrados de um aplicativo do MongoDB, perceba que o Azure Cosmos DB pode fornecer uma plataforma de serviço de banco de dados altamente disponível, distribuída globalmente para todos os aplicativos críticos.

    Preencha os campos da folha **Nova conta** usando as informações na próxima captura de tela como guia. Os valores podem ser diferentes dos valores na captura de tela.
 
    ![Folha "Nova conta"](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Configuração|Valor sugerido|Descrição
    ---|---|---
    ID|*Valor exclusivo*|Um nome exclusivo que identifica essa conta do Azure Cosmos DB. Como *documents.Azure.com* é acrescentado à ID que você fornece para criar o URI, use uma ID exclusiva mas identificável. A ID deve conter apenas letras minúsculas, números e o caractere de hífen (-). Deve conter de 3 a 50 caracteres.
    API|Gremlin (gráfico)|Nós programaremos a [API do Graph](../articles/cosmos-db/graph-introduction.md) posteriormente neste artigo.|
    Assinatura|*Sua assinatura*|A assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|*O mesmo valor que a ID*|O novo nome de grupo de recursos para sua conta. Para simplificar, você pode usar um nome igual à sua ID. 
    Local|*A região mais próxima de seus usuários*|A localização geográfica na qual hospedar a sua conta do BD Cosmos do Azure. Escolha o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.

4. Selecione **Criar** para criar a conta.

5. Na barra de ferramentas superior, clique no ícone **Notificações** ![ícone de Notificações](./media/cosmos-db-create-dbaccount-graph/notification-icon.png) para monitorar o processo de implantação.

    ![Painel “Notificações” do portal do Azure](./media/cosmos-db-create-dbaccount-graph/notification.png)

6. Quando a janela **Notificações** indicar que a implantação foi bem-sucedida, feche a janela. Abra a nova conta do bloco **Todos os recursos** no **Painel**. 

    ![Bloco "Todos os recursos"](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)