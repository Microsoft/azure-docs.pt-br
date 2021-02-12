---
title: Gerenciar Azure Cosmos DB recursos usando Gerenciador de Armazenamento do Azure
description: Saiba como conectar o Azure Cosmos DB e gerenciar os recursos usando o Gerenciador de Armazenamento do Azure.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348646"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Gerenciar Azure Cosmos DB recursos usando Gerenciador de Armazenamento do Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Você pode usar o Gerenciador de armazenamento do Azure para se conectar ao Azure Cosmos DB. Ele permite que você se conecte a contas de Azure Cosmos DB hospedadas no Azure e em nuvens soberanas do Windows, macOS ou Linux.

Use a mesma ferramenta para gerenciar suas diferentes entidades do Azure em um único lugar. Você pode gerenciar Azure Cosmos DB entidades, manipular dados, atualizar procedimentos armazenados e gatilhos juntamente com outras entidades do Azure, como BLOBs de armazenamento e filas. O Gerenciador de Armazenamento do Azure dá suporte a contas Cosmos configuradas para APIs SQL, MongoDB, Graph e Table.

> [!NOTE]
> A integração do Azure Cosmos DB com o Gerenciador de Armazenamento foi preterida. Qualquer funcionalidade existente não será removida por um período mínimo de um ano a partir desta versão. Você deve usar o [portal do Azure](https://portal.azure.com/), o [aplicativo de área de trabalho do portal do Azure](https://portal.azure.com/App/Download) ou o [Gerenciador de Azure Cosmos DB](data-explorer.md) autônomo em vez disso. As opções alternativas contêm muitos recursos novos que atualmente não têm suporte no Gerenciador de Armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do cosmos com uma API do SQL ou uma API Azure Cosmos DB para MongoDB. Se você não tiver uma conta, poderá criar uma na portal do Azure. Consulte [Azure Cosmos DB: compilar um aplicativo Web da API do SQL com o .net e o portal do Azure](create-sql-api-dotnet.md) para obter mais informações.

## <a name="installation"></a>Instalação

Para instalar os bits de Gerenciador de Armazenamento do Azure mais recentes, consulte [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). Há suporte para versões do Windows, Linux e macOS.

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

1. Depois de instalar o **Gerenciador de armazenamento do Azure**, selecione o ícone de **plug-in** no painel esquerdo.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Captura de tela mostrando o ícone de plug-in no painel esquerdo.":::

1. Selecione **Adicionar uma Conta do Azure** e, em seguida, selecione **Entrar**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Captura de tela da janela conectar ao armazenamento do Azure mostrando o botão de opção Adicionar uma conta do Azure selecionado e o menu suspenso ambiente do Azure.":::

1. Na caixa de diálogo **logon do Azure** , selecione **entrar** e insira suas credenciais do Azure.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Captura de tela da janela de entrada mostrando onde inserir suas credenciais para sua assinatura do Azure.":::

1. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Captura de tela do painel gerenciamento de conta, mostrando uma lista de assinaturas e o botão Aplicar.":::

    O painel Gerenciador é atualizado e mostra as contas na assinatura selecionada.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Captura de tela do painel Gerenciador, atualizada para mostrar as contas na assinatura selecionada.":::

    Sua **conta de Cosmos DB** está conectada à sua assinatura do Azure.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Usar uma cadeia de conexão para se conectar ao Azure Cosmos DB

Você pode usar uma cadeia de conexão para se conectar a um Azure Cosmos DB. Esse método dá suporte apenas a APIs de tabela e SQL. Siga estas etapas para se conectar com uma cadeia de conexão:

1. Localize **local e anexado** na árvore à esquerda, clique com o botão direito do mouse em **contas de Cosmos DB** e, em seguida, selecione **conectar-se a Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Captura de tela mostrando o menu suspenso depois de clicar com o botão direito do mouse, com conectar ao Azure Cosmos D B realçado.":::

2. Na janela **conectar a Cosmos DB** :
   1. Selecione a API no menu suspenso.
   1. Cole a cadeia de conexão na caixa **cadeia de conexão** . Para saber como recuperar a cadeia de conexão primária, consulte [obter a cadeia de conexão](manage-with-powershell.md#list-keys).
   1. Insira um **rótulo de conta** e, em seguida, selecione **Avançar** para verificar o resumo.
   1. Selecione **conectar** para conectar a conta de Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Captura de tela da janela conectar a Cosmos D B, mostrando o menu suspenso API, a caixa Cadeia de conexão e a caixa rótulo da conta.":::

> [!NOTE]
> Se o Gerenciador de Armazenamento do Azure mostrar que a cadeia de conexão de Azure Cosmos DB está em um formato inválido, certifique-se de que a cadeia de conexão tenha um ponto-e-vírgula ( `;` ) no final. Um exemplo de uma cadeia de conexão de Azure Cosmos DB válida seria: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Usar um emulador local para se conectar ao Azure Cosmos DB

Use as etapas a seguir para se conectar a um Azure Cosmos DB com um emulador. Esse método só dá suporte a contas SQL.

1. Instale o emulador Cosmos DB e abra-o. Para saber como instalar o emulador, consulte [emulador de Cosmos DB](./local-emulator.md).

1. Localize **local e anexado** na árvore à esquerda, clique com o botão direito do mouse em **contas de Cosmos DB** e, em seguida, selecione **conectar ao emulador de Cosmos DB**.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com conectar ao emulador do Azure Cosmos D B realçado.":::

1. Na janela **conectar a Cosmos DB** :
   1. Cole a cadeia de conexão na caixa **cadeia de conexão** . Para obter informações sobre como recuperar a cadeia de conexão primária, consulte [obter a cadeia de conexão](manage-with-powershell.md#list-keys).
   1. Insira um **rótulo de conta** e, em seguida, selecione **Avançar** para verificar o resumo.
   1. Selecione **conectar** para conectar a conta de Azure Cosmos DB.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Captura de tela da janela conectar à Cosmos D B, mostrando a caixa Cadeia de conexão e a caixa rótulo da conta.":::

## <a name="azure-cosmos-db-resource-management"></a>Gerenciamento de recursos do Azure Cosmos DB

Use as seguintes operações para gerenciar uma conta de Azure Cosmos DB:

* Abra a conta no portal do Azure.
* Adicione o recurso à lista de acesso rápido.
* Pesquisar e atualizar recursos.
* Criar e excluir bancos de dados.
* Criar e excluir coleções.
* Criar, editar, excluir e filtrar documentos.
* Gerencie procedimentos armazenados, gatilhos e funções definidas pelo usuário.

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Você pode clicar com o botão direito do mouse em uma assinatura no painel do Explorer para executar muitas tarefas de ação rápida, por exemplo:

* Clique com o botão direito do mouse em uma conta ou banco de dados do Azure Cosmos DB e selecione **abrir no portal** para gerenciar o recurso no navegador no portal do Azure.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com abrir no portal realçado.":::

* Clique com o botão direito do mouse em um Azure Cosmos DB conta, banco de dados ou coleção e, em seguida, selecione **Adicionar ao acesso rápido** para adicioná-lo ao menu de acesso rápido.

* Selecione **Pesquisar aqui** para habilitar a pesquisa de palavra-chave no caminho selecionado.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Captura de tela mostrando a caixa de pesquisa realçada.":::

### <a name="database-and-collection-management"></a>Gerenciamento de banco de dados e coleção

#### <a name="create-a-database"></a>Criar um banco de dados

1. Clique com o botão direito do mouse na conta Azure Cosmos DB e selecione **criar banco de dados**.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com criar banco de dados realçado.":::

1. Insira o nome do banco de dados e pressione **Enter** para concluir.

#### <a name="delete-a-database"></a>Excluir um banco de dados

1. Clique com o botão direito do mouse no banco de dados e selecione **excluir banco de dados**. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com excluir banco de dados realçado.":::

1. Selecione **Sim** na janela pop-up. O nó do banco de dados é excluído e a conta do Azure Cosmos DB é atualizada automaticamente.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Captura de tela da janela de confirmação com o botão Sim realçado.":::

#### <a name="create-a-collection"></a>Criar uma coleção

1. Clique com o botão direito do mouse no banco de dados e selecione **criar coleção**.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com CREATE Collection realçado.":::

1. Na janela criar coleção, insira as informações solicitadas, como **ID da coleção** e **capacidade de armazenamento**, e assim por diante. Escolha **OK** para concluir.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Captura de tela da janela criar coleção, mostrando a caixa de coleção I e os botões de capacidade de armazenamento.":::

1. Selecione **ilimitado** para que você possa especificar uma chave de partição e, em seguida, selecione **OK** para concluir.

   > [!NOTE]
   > Se uma chave de partição for usada quando você criar uma coleção, depois que a criação for concluída, você não poderá alterar o valor da chave de partição na coleção.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Captura de tela da janela criar coleção, mostrando seleção ilimitada para capacidade de armazenamento e a caixa chave de partição realçada.":::

#### <a name="delete-a-collection"></a>Excluir uma coleção

- Clique com o botão direito do mouse na coleção, selecione **excluir coleção** e, em seguida, selecione **Sim** na janela pop-up.

    O nó da coleção é excluído e o banco de dados é atualizado automaticamente.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Captura de tela mostrando o menu exibido depois de clicar com o botão direito do mouse, com excluir coleção realçada.":::

### <a name="document-management"></a>Gerenciamento de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar documentos

- Abra **documentos** no painel esquerdo, selecione **novo documento**, edite o conteúdo no painel direito e, em seguida, selecione **salvar**.
- Você também pode atualizar um documento existente e, em seguida, selecionar **salvar**. Para descartar as alterações, selecione **descartar**.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Captura de tela mostrando documentos realçados no painel esquerdo. No painel direito, novo documento, salvar e descartar são realçados.":::

#### <a name="delete-a-document"></a>Excluir um documento

* Selecione o botão **excluir** para excluir o documento selecionado.

#### <a name="query-for-documents"></a>Consulta de documentos

* Para editar o filtro de documento, insira uma [consulta SQL](./sql-query-getting-started.md)e, em seguida, selecione **aplicar**.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Captura de tela do painel direito, mostrando os botões filtrar e aplicar, o número de ID e a caixa de consulta realçada.":::

### <a name="graph-management"></a>Gerenciamento de gráfico

#### <a name="create-and-modify-a-vertex"></a>Criar e modificar um vértice

* Para criar um novo vértice, abra o **grafo** no painel esquerdo, selecione **novo vértice**, edite o conteúdo e selecione **OK**.
* Para modificar um vértice existente, selecione o ícone de caneta no painel direito.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Captura de tela mostrando o gráfico selecionado no painel esquerdo e mostrando o novo vértice e o ícone de caneta realçados no painel direito.":::

#### <a name="delete-a-graph"></a>Excluir um gráfico

* Para excluir um vértice, selecione o ícone de Lixeira ao lado do nome do vértice.

#### <a name="filter-for-graph"></a>Filtro para gráficos

* Para editar o filtro de gráfico, insira uma [consulta Gremlin](gremlin-support.md)e, em seguida, selecione **aplicar filtro**.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Captura de tela mostrando o gráfico selecionado no painel esquerdo e mostrando aplicar filtro e a caixa de consulta realçada no painel direito.":::

### <a name="table-management"></a>Gerenciamento de tabela

#### <a name="create-and-modify-a-table"></a>Criar e modificar uma tabela

* Para criar uma nova tabela:
   1. No painel esquerdo, abra **entidades** e, em seguida, selecione **Adicionar**.
   1. Na caixa de diálogo **Adicionar entidade** , edite o conteúdo.
   1. Selecione o botão **Adicionar Propriedade** para adicionar uma propriedade.
   1. Selecione **Inserir**.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Captura de tela mostrando entidades realçadas no painel esquerdo e mostrando adicionar, editar, adicionar propriedade e inserir realçados no painel à direita.":::

* Para modificar uma tabela, selecione **Editar**, modifique o conteúdo e, em seguida, selecione **Atualizar**.

   

#### <a name="import-and-export-table"></a>Importar e exportar tabela

* Para importar, selecione o botão **importar** e, em seguida, escolha uma tabela existente.
* Para exportar, selecione o botão **Exportar** e, em seguida, escolha um destino.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Captura de tela mostrando os botões importar e exportar realçados no painel direito.":::

#### <a name="delete-entities"></a>Excluir entidades

* Selecione as entidades e, em seguida, selecione o botão **excluir** .

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Captura de tela mostrando o botão excluir realçado no painel direito e uma janela pop-up de confirmação com Sim realçado.":::

#### <a name="query-a-table"></a>Consultar uma tabela

- Selecione o botão **consulta** , insira uma condição de consulta e, em seguida, selecione o botão **Executar consulta** . Para fechar o painel de consulta, selecione o botão **fechar consulta** .

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Captura de tela do painel direito, mostrando o botão Executar consulta e o botão fechar consulta realçado.":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerenciar procedimentos armazenados, gatilhos e UDFs

* Para criar um procedimento armazenado:
  1. Na árvore à esquerda, clique com o botão direito do mouse em **procedimentos armazenados** e selecione **criar procedimento armazenado**.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Captura de tela do painel esquerdo, mostrando o menu exibido depois de clicar com o botão direito do mouse, com criar procedimento armazenado realçado.":::
  
  1. Insira um nome à esquerda, insira os scripts de procedimento armazenado no painel direito e, em seguida, selecione **criar**.
  
* Para editar um procedimento armazenado existente, clique duas vezes no procedimento, faça a atualização e, em seguida, selecione **Atualizar** para salvar. Você também pode selecionar **descartar** para cancelar a alteração.

* As operações para **gatilhos** e **UDF** são semelhantes aos **procedimentos armazenados**.

## <a name="troubleshooting"></a>Solução de problemas

Veja a seguir as soluções para problemas comuns que surgem quando você usa Azure Cosmos DB no Gerenciador de Armazenamento.

### <a name="sign-in-issues"></a>Problemas de entrada

Primeiro, reinicie o aplicativo para ver se isso corrige o problema. Se o problema persistir, continue a solução de problemas.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificado autoassinado na cadeia confiável

Há alguns motivos pelos quais você pode estar vendo esse erro, os dois mais comuns são:

* Você está protegido por um *proxy transparente*. Alguém, como seu departamento de ti, intercepta o tráfego HTTPS, descriptografa-o e, em seguida, criptografa-o usando um certificado autoassinado.

* Você está executando software, como um software antivírus. O Software injeta um certificado TLS/SSL autoassinado nas mensagens HTTPS que você recebe.

Quando Gerenciador de Armazenamento encontra um certificado autoassinado, ele não sabe se a mensagem HTTPS recebida é violada. Se você tiver uma cópia do certificado autoassinado, poderá informar Gerenciador de Armazenamento confiar nele. Se você não tiver certeza de quem inseriu o certificado, poderá seguir estas etapas para tentar descobrir:

1. Instale o OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): qualquer uma das versões leves está ok.
     - macOS e Linux: devem ser incluídos no seu sistema operacional.

1. Execute o OpenSSL:
    * Windows: Vá para o diretório de instalação, depois **/bin/** e clique duas vezes em **openssl.exe**.
    * Mac e Linux: execute **OpenSSL** a partir de um terminal.
1. Execute `s_client -showcerts -connect microsoft.com:443`.
1. Procurar certificados autoassinados. Se você não tiver certeza, que são autoassinados, procure em qualquer lugar que o assunto ("s:") e o emissor ("i:") sejam os mesmos.
1. Se você encontrar certificados autoassinados, copie e cole tudo de e incluindo **-----iniciar o-----de certificado** para **----------de certificado final** para um novo. Arquivo CER para cada um.
1. Abra Gerenciador de armazenamento e acesse editar certificados **Edit**  >  **SSL**  >  **importar certificados**. Use o seletor de arquivos para localizar, selecionar e abrir o. Arquivos CER que você criou.

Se você não encontrar nenhum certificado autoassinado, poderá enviar comentários para obter mais ajuda.

#### <a name="unable-to-retrieve-subscriptions"></a>Não é possível recuperar as assinaturas

Se não for possível recuperar suas assinaturas depois de entrar, tente estas sugestões:

* Verifique se sua conta tem acesso às assinaturas. Para fazer isso, entre no [portal do Azure](https://portal.azure.com/).
* Certifique-se de que você entrou no ambiente correto:
  * [Azure](https://portal.azure.com/)
  * [Azure China:](https://portal.azure.cn/)
  * [Azure Alemanha](https://portal.microsoftazure.de/)
  * [Governo dos EUA para Azure](https://portal.azure.us/)
  * Ambiente/Azure Stack personalizado
* Se você estiver protegido por um proxy, verifique se o proxy de Gerenciador de Armazenamento está configurado corretamente.
* Remova a conta e, em seguida, adicione-a novamente.
* Exclua os seguintes arquivos de seu diretório base (como: C:\Users\ContosoUser) e, em seguida, adicione a conta novamente:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Pressione a tecla F12 para abrir o console do desenvolvedor. Observe o console para verificar se há mensagens de erro ao entrar.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Captura de tela do console de ferramentas de desenvolvedor, mostrando o console realçado.":::

#### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se você não conseguir ver a página de autenticação:

* Dependendo da velocidade da conexão, pode levar algum tempo para que a página de entrada seja carregada. Aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Se você estiver protegido por um proxy, verifique se o proxy de Gerenciador de Armazenamento está configurado corretamente.
* No console de ferramentas de desenvolvedor (F12), observe as respostas para ver se você pode encontrar qualquer dica sobre por que a autenticação não está funcionando.

#### <a name="cant-remove-an-account"></a>Não é possível remover uma conta

Se não for possível remover uma conta ou se o link de reautenticação não fizer nada:

* Exclua os seguintes arquivos de seu diretório base e, em seguida, adicione a conta novamente:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Se você quiser remover SAS associado a recursos de Armazenamento, exclua:
  * Pasta %AppData%/StorageExplorer para Windows
  * /Users/<your_name>/library/Application Support SUpport/StorageExplorer para macOS
  * ~/.config/StorageExplorer para Linux
  
  > [!NOTE]
  > Se você excluir esses arquivos, **deverá reinserir todas as suas credenciais**.

### <a name="httphttps-proxy-issue"></a>Problema de proxy HTTP/HTTPS

Você não pode listar Azure Cosmos DB nós na árvore à esquerda ao configurar um proxy HTTP/HTTPS no ASE. Você pode usar Azure Cosmos DB data Explorer na portal do Azure como solução alternativa.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema de nó "Desenvolvimento" em "Local e Anexado"

Não há resposta depois de selecionar o nó de **desenvolvimento** no nó **local e anexado** na árvore à esquerda. O comportamento é esperado.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Captura de tela mostrando o nó de desenvolvimento selecionado.":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Anexar uma conta de Azure Cosmos DB no erro de nó **local e anexado**

Se você vir o erro a seguir depois de anexar uma conta de Azure Cosmos DB no nó **local e anexado** , verifique se você está usando a cadeia de conexão correta.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Captura de tela da janela pop-up de erro não é possível recuperar recursos filho, indicando getaddrinfo ENOTFOUND.":::

### <a name="expand-azure-cosmos-db-node-error"></a>Erro de expansão do nó do Azure Cosmos DB

Você pode ver o erro a seguir ao tentar expandir os nós na árvore à esquerda.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Captura de tela da janela pop-up de erro não é possível recuperar recursos filho, indicando que o não pode se conectar a essa conta do cosmos D B.":::

Experimente estas sugestões:

* Verifique se a conta de Azure Cosmos DB está em andamento do provisionamento. Tente novamente quando a conta estiver sendo criada com êxito.
* Se a conta estiver sob o **acesso rápido** ou nós **locais e anexados** , verifique se a conta foi excluída. Nesse caso, você precisa remover manualmente o nó.

## <a name="next-steps"></a>Próximas etapas

* Assista a este vídeo para ver como usar Azure Cosmos DB no Gerenciador de Armazenamento do Azure: [use Azure Cosmos DB no Gerenciador de armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Gerenciador de Armazenamento e conecte mais serviços em [Introdução ao Gerenciador de Armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
