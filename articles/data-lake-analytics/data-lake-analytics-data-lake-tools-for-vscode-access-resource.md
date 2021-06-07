---
title: Acessando recursos com ferramentas de Data Lake
description: Saiba como usar as ferramentas de Azure Data Lake para acessar recursos de Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754567"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Acessando recursos com ferramentas de Azure Data Lake

Você pode acessar Azure Data Lake Analytics recursos com os comandos ou ações do Azure Data Tools em VS Code facilmente.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrar ao Azure Data Lake Analytics usando um comando

Você pode acessar recursos do Azure Data Lake Analytics para listar contas, acessar metadados e exibir trabalhos de análise.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Para listar as contas do Azure Data Lake Analytics em sua assinatura do Azure

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: List Accounts**. As contas são exibidas no painel **Saída**.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Para acessar os metadados do Azure Data Lake Analytics

1. Pressione CTRL+SHIFT+P e insira **ADL: Listar Tabelas**.
2. Selecione uma das contas do Data Lake Analytics.
3. Selecione um dos bancos de dados do Data Lake Analytics.
4. Selecione um dos esquemas. Você pode ver a lista de tabelas.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Para exibir trabalhos do Azure Data Lake Analytics

1. Abra a paleta de comandos (Ctrl+Shift+P) e escolha **ADL: Mostrar Trabalhos**.
2. Selecione uma conta do Data Lake Analytics ou local.
3. Aguarde a exibição da lista de trabalhos da conta.
4. Selecione um trabalho da lista de trabalhos. As Ferramentas do Data Lake abrem a exibição do trabalho no painel direito e exibem algumas informações na saída do VS Code.

    ![Lista de trabalhos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrar ao Azure Data Lake Store usando um comando

É possível usar comandos relacionados ao Azure Data Lake Store para:

- [Procurar nos recursos do Azure Data Lake Store](#list-the-storage-path)
- [Visualizar o arquivo do Azure Data Lake Store](#preview-the-storage-file)
- Carregar o arquivo diretamente no Azure Data Lake Storage no VS Code
- Baixar o arquivo diretamente do Azure Data Lake Storage no VS Code

### <a name="list-the-storage-path"></a>Listar caminho de armazenamento

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Para listar o caminho de armazenamento por meio da paleta de comandos

1. Clique com o botão direito do mouse no editor de scripts e selecione **ADL: Listar Caminho**.
2. Escolha a pasta na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)
3. Selecione sua conta do Data Lake Analytics.
4. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/).  

A paleta de comandos lista as informações de caminho com base nas suas entradas.

![Resultados do caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma maneira mais adequada de listar o caminho relativo é pelo menu de atalho.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Para listar o caminho de armazenamento por meio do menu de atalho

Clique com o botão direito do mouse na cadeia de caracteres do caminho e selecione **Listar Caminho**.

![“Listar Caminho” no menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Visualizar arquivo de armazenamento

1. Clique no editor de scripts com o botão direito do mouse e selecione **ADL: Visualizar Caminho**.
2. Selecione sua conta do Data Lake Analytics.
3. Insira um caminho de arquivo do Armazenamento do Azure (por exemplo, /output/SearchLog.txt).

O arquivo é aberto no VS Code.

![Etapas e resultados da visualização do arquivo de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra maneira de visualizar um arquivo é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts.

### <a name="upload-a-file-or-folder"></a>Carregar um arquivo ou uma pasta

1. Clique com o botão direito no editor de scripts e selecione **Carregar Arquivo** ou **Carregar Pasta**.
2. Escolha um ou vários arquivos caso tenha selecionado **Carregar Arquivo** ou escolha a pasta inteira caso tenha selecionado **Carregar Pasta**. Em seguida, selecione **Carregar**.
3. Escolha a pasta de armazenamento na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)
4. Selecione sua conta do Data Lake Analytics.
5. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/).
6. Selecione **Escolher Pasta Atual** para especificar o destino do upload.

![Etapas e o resultado ao carregar um arquivo ou uma pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Outra maneira de carregar arquivos para armazenamento é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de script.

É possível [monitorar o status de upload](#check-storage-tasks-status).

### <a name="download-a-file"></a>Baixar um arquivo

É possível baixar um arquivo inserindo os comandos **ADL: Baixar Arquivo** ou **ADL: Baixar Arquivo (Avançado)**.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Para baixar os arquivos por meio do comando ADL: Baixar Arquivo (Avançado)

1. Clique com o botão direito do mouse no editor de scripts e, depois, selecione **Baixar Arquivo (Avançado)**.
2. O VS Code exibe um arquivo JSON. É possível inserir os caminhos de arquivo e baixar vários arquivos ao mesmo tempo. As instruções são exibidas na janela **saída** . Para prosseguir com o download dos arquivos, salve (CTRL+S) o arquivo JSON.

    ![Arquivo JSON com os caminhos para download de arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A janela **Saída** exibe o status de download do arquivo.

![Janela de saída com o status do download](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

É possível [monitorar o status de download](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Para baixar os arquivos por meio do comando ADL: Baixar Arquivo

1. Clique com botão direito do mouse no editor de scripts, selecione **Baixar Arquivo** e, em seguida, selecione a pasta de destino na caixa de diálogo **Selecionar Pasta**.

1. Escolha a pasta na lista ou selecione **Inserir um caminho** ou **Procurar no caminho raiz**. (**Inserir um caminho** está sendo usado como um exemplo.)

1. Selecione sua conta do Data Lake Analytics.

1. Navegue até o caminho da pasta de armazenamento ou digite-o (por exemplo: /output/) e escolha um arquivo para baixar.

![Etapas e o resultado ao baixar um arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Outra maneira de baixar arquivos de armazenamento é pelo menu de atalho no caminho completo do arquivo ou no caminho relativo do arquivo no editor de script.

É possível [monitorar o status de download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Verificar o status de tarefas de armazenamento

O status de upload e download aparece na barra de status. Selecione a barra de status e, em seguida, o status aparece na guia **SAÍDA**.

![Barra de status e detalhes de saída](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrar ao Azure Data Lake Analytics pelo explorer

Após o logon, todas as assinaturas em sua conta do Azure são listadas no painel esquerdo do **AZURE DATALAKE**.

![Explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navegação dos metadados do Data Lake Analytics

Expanda sua assinatura do Azure. No nó **Bancos de dados U-SQL**, é possível procurar por meio do banco de dados do U-SQL e exibir pastas como **Esquemas**, **Credenciais**, **Assemblies**, **Tabelas** e **Índice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gerenciamento de entidade dos metadados do Data Lake Analytics

Expanda o **Bancos de dados U-SQL**. É possível criar um banco de dados, esquema, tabela, tipo de tabela, índice ou estatística clicando com o botão direito do mouse no nó correspondente e, em seguida, selecionando **Script para Criar** no menu de atalho. Na página de script aberta, edite o script de acordo com suas necessidades. Em seguida, envie o trabalho clicando com o botão direito do mouse e selecionando **ADL: Enviar Trabalho**.

Depois de terminar de criar o item, clique com botão direito no nó e selecione **Atualizar** para mostrar o item. Também é possível excluir o item clicando com o botão direito do mouse ele e selecionando **Excluir**.

![Comando “Script para Criar” no menu de atalho no Explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Página de script para o novo item](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registro de assembly do Data Lake Analytics

É possível registrar um assembly no banco de dados correspondente clicando com o botão direito do mouse no nó **Assemblies** e selecionando **Registrar assembly**.

![Comando “Registrar assembly” no menu de atalho para o nó de Assemblies](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrar ao Azure Data Lake Store pelo explorer

Navegue até **Data Lake Store**:

- É possível clicar com o botão direito do mouse no nó da pasta e usar os comandos **Atualizar**, **Excluir**, **Carregar**, **Carregar Pasta**, **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

   ![Comandos do menu de atalho para um nó de pasta no explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- É possível clicar com o botão direito do mouse no nó do arquivo e usar os comandos **Visualizar**, **Baixar**, **Excluir**, **Criar Script EXTRACT** (disponível apenas para arquivos CSV, TSV e TXT), **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

   ![Comandos do menu de atalho para um nó de arquivo no explorer do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrar ao armazenamento de Blobs do Azure pelo explorer

Navegar até o armazenamento de Blobs:

- É possível clicar com o botão direito do mouse no nó do contêiner de blobs e usar os comandos **Atualizar**, **Excluir Contêiner de Blobs** e **Carregar Blob** no menu de atalho.

   ![Comandos do menu de atalho para um nó do contêiner de blob no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- É possível clicar com o botão direito do mouse no nó da pasta e usar os comandos **Atualizar** e **Carregar Blob** no menu de atalho.

   ![Comandos do menu de atalho para um nó da pasta no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- É possível clicar com o botão direito do mouse no nó do arquivo e usar os comandos **Visualizar/Editar**, **Baixar**, **Excluir**, **Criar Script EXTRACT** (disponível apenas para arquivos CSV, TSV e TXT), **Copiar Caminho Relativo** e **Copiar Caminho Completo** no menu de atalho.

    ![Comandos do menu de atalho para um nó do arquivo no armazenamento de Blobs](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Abrir o explorer do Data Lake no portal

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **Abrir Gerenciador do Armazenamento do Azure da Web** ou clique com o botão direito do mouse em um caminho relativo, ou no caminho completo no editor de scripts, e escolha **Abrir o Gerenciador de Armazenamento do Azure da Web**.
3. Selecione uma conta do Data Lake Analytics.

As Ferramentas do Data Lake abrem o caminho do Armazenamento do Azure no portal do Azure. Você pode encontrar o caminho e visualizar o arquivo na Web.

## <a name="additional-features"></a>Recursos adicionais

As Ferramentas do Data Lake para VS Code dão suporte aos seguintes recursos:

- **Preenchimento automático de IntelliSense**: as sugestões são exibidas em janelas pop-up ao redor dos itens, como palavras-chave, métodos e variáveis. Os diferentes ícones representam diferentes tipos de objetos:

  - Tipo de dados de Scala
  - Tipos de dados complexos
  - UDTs Internos
  - Coleções e classes .Net
  - Expressões em C#
  - UDFs, UDOs e UDAAGs internos de C#
  - Funções em U-SQL
  - Função de janelas do U-SQL

    ![Tipos de objeto do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Preenchimento automático do IntelliSense em metadados de data Lake Analytics**: o data Lake Tools baixa as informações de metadados de data Lake Analytics localmente. O recurso IntelliSense preenche automaticamente os objetos de metadados do Data Lake Analytics. Esses objetos incluem o banco de dados, esquema, tabela, exibição, função com valor de tabela, procedimentos e assemblies do C#.

  ![Metadados do IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Marcador de erro do IntelliSense**: as Ferramentas do Data Lake sublinham os erros de edição de U-SQL e C#.
- **Destaques de sintaxe**: as Ferramentas do Data Lake usam cores diferentes para diferenciar itens, como variáveis, palavras-chave, tipo de dados e funções.

    ![Sintaxe com várias cores](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Recomenda-se que você atualize para a versão 2.3.3000.4 ou posterior das Ferramentas do Azure Data Lake para Visual Studio. As versões anteriores não estão disponíveis para download e foram preteridas.  

## <a name="next-steps"></a>Próximas etapas

- [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Execução local e depuração local do U-SQL com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)