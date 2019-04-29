---
title: Gerenciar recursos do Azure Cosmos DB usando o Gerenciador de Armazenamento do Azure
description: Saiba como conectar o Azure Cosmos DB e gerenciar os recursos usando o Gerenciador de Armazenamento do Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 8700d0988927b221ace82a492e9902f1f36a562b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950597"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Trabalhar com os dados usando o Gerenciador de Armazenamento do Azure

O uso do Azure Cosmos DB no Gerenciador de Armazenamento do Azure permite que os usuários gerenciem entidades do Azure Cosmos DB, manipulem dados, atualizem procedimentos armazenados e gatilhos junto com outras entidades do Azure, como os blobs de armazenamento e as filas. Agora você pode usar a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único local. Neste momento, o Gerenciador de Armazenamento do Microsoft Azure oferece suporte para contas do Cosmos, configuradas para SQL, MongoDB, Graph e APIs de Tabela.


## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Cosmos com API SQL ou API do Azure Cosmos DB para MongoDB. Se você não tiver uma conta, poderá criar uma no portal do Azure, conforme descrito em [Azure Cosmos DB: Crie um aplicativo Web de API de SQL com .NET e o portal do Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Instalação

Instale os novos bits do Gerenciador de Armazenamento do Azure aqui: [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), agora com suporte para versões do Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. Depois de instalar o **Gerenciador de Armazenamento do Azure**, clique no ícone de **plug-in** à esquerda, conforme mostrado na imagem a seguir:
       
   ![Ícone do plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Selecione **Adicionar uma Conta do Azure** e, em seguida, clique em **Entrar**.

   ![Conectar-se a uma assinatura do Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Na caixa de diálogo **Entrar no Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![Entrar](./media/storage-explorer/sign-in.png)

3. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    ![Aplicar](./media/storage-explorer/apply-subscription.png)

    O painel do Explorer atualiza e exibe as contas na assinatura selecionada.

    ![Lista de contas](./media/storage-explorer/account-list.png)

    Você conectou sua **Conta do Cosmos DB** à sua assinatura do Azure com êxito.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Conectar-se ao Azure Cosmos DB usando uma cadeia de conexão

Um modo alternativo de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Siga as etapas abaixo para se conectar usando uma cadeia de conexão.

1. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Cosmos DB** e escolha **Conectar-se ao Cosmos DB...**

    ![Conectar-se ao Cosmos DB por uma cadeia de conexão](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Atualmente, só há suporte para API do SQL e de Tabela. Escolha a API, cole a **Cadeia de Conexão**, insira o **Rótulo de conta**, clique em **Próximo** para verificar o resumo e depois clique em **Conectar** para conectar a conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Cadeia de conexão](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Conectar-se ao Azure Cosmos DB usando um emulador local

Use as seguintes etapas para se conectar a um Azure Cosmos DB pelo Emulador. Atualmente, só há suporte para conta do SQL.

1. Instale o emulador e inicialize-o. Para saber como instalar o emulador, consulte [Emulador Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)

2. Encontre a opção **Local e Anexo** na árvore à esquerda, clique com o botão direito do mouse em **Contas do Cosmos DB** e escolha **Conectar-se ao Emulador Cosmos DB...**

    ![Conecte-se ao Cosmos DB pelo emulador](./media/storage-explorer/emulator-entry.png)

3. Atualmente, só há suporte para API do SQL. Cole a **Cadeia de Conexão**, insira o **Rótulo de conta**, clique em **Próximo** para verificar o resumo e depois clique em **Conectar** para conectar a conta do Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de conexão, consulte [Obtenha a cadeia de conexão](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Conecte-se ao Cosmos DB pelo diálogo do emulador](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Gerenciamento de recursos do Azure Cosmos DB

Você pode gerenciar uma conta do Azure Cosmos DB seguindo as operações a seguir:
* Abra a conta no Portal do Azure
* Adicione o recurso à lista de Acesso Rápido
* Pesquise e atualize os recursos
* Crie e exclua bancos de dados
* Crie e exclua coleções
* Crie, edite, exclua e filtre documentos
* Gerencie procedimentos armazenados, gatilhos e funções definidas pelo usuário

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Ao clicar com o botão direito do mouse em uma assinatura no painel do Explorer, você poderá executar várias tarefas de ação rápida:

* Clique com o botão direito do mouse em uma conta ou banco de dados do Azure Cosmos DB. Você pode escolher a opção **Abrir no Portal** e gerenciar o recurso no navegador do Portal do Azure.

     ![Abrir no portal](./media/storage-explorer/open-in-portal.png)

* Você também pode adicionar a conta, o banco de dados e a coleção do Azure Cosmos DB no **Acesso Rápido**.
* A opção **Pesquisar daqui** habilita a pesquisa de palavra-chave no caminho selecionado.

    ![Pesquisar daqui](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gerenciamento de banco de dados e coleção
#### <a name="create-a-database"></a>Criar um banco de dados 
-   Clique com o botão direito do mouse na conta do Azure Cosmos DB, escolha **Criar Banco de Dados**, insira o nome do banco de dados e pressione **Enter** para concluir.
       
    ![Criar banco de dados](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Excluir um banco de dados
- Clique com o botão direito do mouse no banco de dados, clique em **Excluir Banco de Dados** e em **Sim** na janela pop-up. O nó do banco de dados é excluído e a conta do Azure Cosmos DB é atualizada automaticamente.

    ![Excluir database1](./media/storage-explorer/delete-database1.png)  

    ![Excluir database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Criar uma coleção
1. Clique com o botão direito do mouse no banco de dados, escolha **Criar Coleção** e, em seguida, forneça as seguintes informações como **ID da Coleção**, **Capacidade de Armazenamento**, etc. Clique em **OK** para concluir. 

    ![Criar collection1](./media/storage-explorer/create-collection.png)

    ![Criar collection2](./media/storage-explorer/create-collection2.png) 

2. Selecione **Unlimited** para ser capaz de especificar a chave de partição, depois clique em **OK** para concluir.

    Se uma chave de partição for usada ao criar uma coleção, depois que a criação for concluída, o valor da chave de partição não poderá ser alterado na coleção.

    ![Chave de partição](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Excluir uma coleção
- Clique com o botão direito do mouse na coleção, clique em **Excluir Coleção** e, em seguida, clique em **Sim** na janela pop-up. 

    O nó da coleção é excluído e o banco de dados é atualizado automaticamente.

    ![Excluir coleção](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gerenciamento de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos
- Para criar um novo documento, abra **Documentos** na janela esquerda, clique em **Novo Documento**, edite o conteúdo no painel direito e clique em **Salvar**. Você também pode atualizar um documento existente e, em seguida, clicar em **Salvar**. As alterações podem ser descartadas clicando em **Descartar**.

    ![Documento](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Excluir um documento
- Clique no botão **Excluir** para excluir o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos
- Edite o filtro de documentos inserindo uma [Consulta SQL](how-to-sql-query.md) e, em seguida, clique em **Aplicar**.

    ![Filtro de documentos](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Gerenciamento de gráfico

#### <a name="create-and-modify-vertex"></a>Criar e modificar vértice
1. Para criar um novo vértice, abra o **Gráfico** na janela esquerda, clique em **Novo vértice**, edite o conteúdo e clique em **OK**.    
2. Para modificar um vértice existente, clique no ícone de caneta no painel direito.   

    ![Grafo](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Excluir um gráfico
- Para excluir um vértice, clique no ícone de lixeira ao lado do nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráficos
- Edite o filtro de gráficos inserindo uma [consulta gremlin](gremlin-support.md), depois clique em **Aplicar Filtro**.

    ![Filtro do gráfico](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Gerenciamento de tabela

#### <a name="create-and-modify-table"></a>Criar e modificar tabela
1. Para criar uma nova tabela, abra **Entidades** na janela esquerda, clique em **Adicionar**, edite o conteúdo na caixa de diálogo **Adicionar entidade**, adicione a propriedade clicando no botão **Adicionar Propriedade**, em seguida, clique em **Inserir**.
2. Para modificar uma tabela, clique em **Editar**, modifique o conteúdo e clique em **Atualizar**.

    ![Tabela](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importar e exportar tabela
1. Para importar, clique no botão **Importar** e escolha uma tabela existente.
2. Para exportar, clique no botão **Exportar** e escolha uma tabela existente.

    ![Importação e exportação de tabela](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Excluir entidades
- Selecione as entidades e clique no botão **Excluir**.

    ![Exclusão de tabela](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Tabela de consulta
- Clique no botão **Consulta**, insira a condição de consulta e clique no botão **Executar consulta**. Feche o painel de consulta clicando no botão **Fechar consulta**.

    ![Consulta de tabela](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerenciar procedimentos armazenados, gatilhos e UDFs
* Para criar um procedimento armazenado, na árvore à esquerda, clique com botão direito do mouse em **Procedimento Armazenado**, escolha **Criar Procedimento Armazenado**, insira um nome à esquerda, digite os scripts do procedimento armazenado na janela à direita e, em seguida, clique em **Criar**. 
* Você também pode editar os procedimentos armazenados existentes ao clicar neles duas vezes, fazer a atualização e, em seguida, clicar em **Atualizar** para salvar ou clicar em **Descartar** para cancelar a alteração.

    ![Procedimento armazenado](./media/storage-explorer/stored-procedure.png)
* As operações para **Gatilhos** e **UDF** são semelhantes aos **Procedimentos Armazenados**.

## <a name="troubleshooting"></a>solução de problemas

[Azure Cosmos DB no Gerenciador de Armazenamento](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) é um aplicativo autônomo que permite que você se conecte a contas do Azure Cosmos DB hospedadas nas nuvens do Azure e soberanas do Windows, do macOS ou do Linux. Ele permite a você gerenciar entidades do Azure Cosmos DB, manipular dados, atualizar procedimentos armazenados e gatilhos junto com outras entidades do Azure, como blobs e filas do Armazenamento.

Estas são as soluções de problemas comuns do Azure Cosmos DB encontradas no Gerenciador de Armazenamento.

### <a name="sign-in-issues"></a>Problemas de entrada

Antes de continuar, tente reiniciar o aplicativo e ver se os problemas podem ser corrigidos.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia confiável

Há alguns motivos que podem exibir esse erro. Os dois mais comuns são:

+ Você está por trás de um *proxy transparente*, o que significa que alguém (por exemplo, o departamento de TI) está interceptando o tráfego HTTPS, descriptografando-o e criptografando-o usando um certificado autoassinado.

+ Você está executando software, por exemplo, um software antivírus, que está injetando certificados SSL autoassinados nas mensagens HTTPS recebidas.

Quando o Gerenciador de Armazenamento encontrar um desses "certificados autoassinados", ele pode não saber se a mensagem HTTPS recebida foi adulterada. No entanto, se você tiver uma cópia do certificado autoassinado, poderá ordenar o Gerenciador de Armazenamento para confiar nele. Se você não tiver certeza de quem está injetando o certificado, tente localizá-lo por conta própria seguindo as etapas abaixo:

1. Instalar o Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves serve)
     - Mac e Linux: Deve ser incluído no sistema operacional
2. Executar Open SSL
    - Windows: Vá para o diretório de instalação, em **/bin/** e, em seguida, clique duas vezes em **openssl.exe**.
    - Mac e Linux: execute **openssl** em um terminal
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procurar certificados autoassinados. Se você não tiver certeza quais são autoassinados, procure onde o assunto ("s") e o emissor ("i") são os mesmos.
5.  Depois de encontrar os certificados autoassinados, copie e cole tudo desde, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer para cada um.
6.  Abra o Gerenciador de Armazenamento e vá para **Editar** > **Certificados SSL** > **Importar Certificados**. Usando o seletor de arquivos, encontre, selecione e abra os arquivos .cer criados.

Se você não conseguir localizar todos os certificados autoassinados usando as etapas acima, envie comentários para obter mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível recuperar as assinaturas

Se não for possível recuperar suas assinaturas depois de entrar com êxito:

- Verifique se sua conta tem acesso às assinaturas entrando no [Portal do Azure](https://portal.azure.com/)
- Verifique se você entrou usando o ambiente certo ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemanha](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) ou Ambiente Personalizado/Azure Stack)
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Tentar remover e readicionar a conta
- Tente excluir os seguintes arquivos do diretório pessoal (como: C:\Users\ContosoUser) e, em seguida, leia a conta:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Veja se aparecem mensagens de erro no console de ferramentas de desenvolvedor (F12) ao entrar

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação 

Se você não conseguir ver a página de autenticação:

- Dependendo da velocidade de sua conexão, talvez demore algum tempo para a página de entrada carregar. Aguarde pelo menos um minuto antes de fechar o diálogo de autenticação
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Exiba o console do desenvolvedor pressionando a tecla F12. Veja as respostas no console do desenvolvedor e tente encontrar alguma dica do motivo do não funcionamento da autenticação

#### <a name="cannot-remove-account"></a>Não é possível remover a conta

Se você não conseguir remover uma conta, ou se o link de nova autenticação não fizer nada

- Tente excluir os seguintes arquivos do diretório inicial e adicione a conta novamente:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se você quiser remover SAS associado a recursos de Armazenamento, exclua:
  - Pasta %AppData%/StorageExplorer para Windows
  - /Usuários/<seu_nome>/Library/Application SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Será necessário reinserir todas as suas credenciais** se você excluir esses arquivos


### <a name="httphttps-proxy-issue"></a>Problema de proxy HTTP/HTTPS

Não é possível listar os nós do Azure Cosmos DB na árvore à esquerda na hora de configurar o proxy HTTP/HTTPS no ASE. Esse é um problema conhecido e será corrigido na próxima versão. Você pode usar o Data Explorer do Azure Cosmos DB no Portal do Azure como uma solução alternativa no momento. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema de nó "Desenvolvimento" em "Local e Anexado"

Não há nenhuma resposta depois de clicar no nó "Desenvolvimento" no nó "Local e Anexado" na árvore à esquerda.  O comportamento é esperado. O emulador de local do Azure Cosmos DB terá suporte a partir da próxima versão.

![Nó de desenvolvimento](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Erro de anexação de conta do Azure Cosmos DB ao nó "Local e Anexado"

Se você vir o erro abaixo depois de anexar a conta do Azure Cosmos DB ao nó "Local e Anexado", verifique se você está usando a cadeia de conexão certa.

![Erro de anexação do Azure Cosmos DB a "Local e Anexado"](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Erro de expansão do nó do Azure Cosmos DB

Você pode ver o erro abaixo ao tentar expandir os nós da árvore à esquerda. 

![Erro de expansão](./media/storage-explorer/expand-error.png)

Tente as seguintes sugestões:

- Verifique se a conta do Azure Cosmos DB está sendo provisionada e tente novamente quando a conta for criada com êxito.
- Se a conta estiver no nó "Acesso Rápido" ou "Local e Anexado", verifique se a conta foi excluída. Nesse caso, você precisará remover o nó manualmente.

## <a name="contact-us"></a>Fale conosco

Se nenhuma das soluções funcionar, envie um email à equipe das Ferramentas de Desenvolvimento do Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) com detalhes sobre o problema, para que possa ser corrigido.

## <a name="next-steps"></a>Próximas etapas

* Assista ao vídeo a seguir para ver como usar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure: [Use o Azure Cosmos DB no Gerenciador de Armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Gerenciador de Armazenamento e conecte mais serviços em [Introdução ao Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

