---
title: Usar as Ferramentas do Azure Data Lake para Visual Studio Code
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para criar, testar e executar scripts U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751352"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio Code

Neste artigo, saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code (VS Code) para criar, testar e executar scripts U-SQL. As informações também são abordadas no vídeo a seguir:

[![Player de vídeo: ferramentas de Azure Data Lake para VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas do Azure Data Lake para VS Code dão suporte para Windows, Linux e macOS. A execução local do U-SQL e a depuração local funcionam somente no Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS e Linux:

- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalar Ferramentas do Azure Data Lake

Após a instalação dos pré-requisitos, você pode instalar as Ferramentas do Azure Data Lake para VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Para instalar Ferramentas do Azure Data Lake

1. Abra o Visual Studio Code.
2. Selecione **Extensões**, no painel esquerdo. Digite **Ferramentas do Azure Data Lake** na caixa de pesquisa.
3. Selecione **Instalar** ao lado de **Ferramentas do Azure Data Lake**.

   ![Seleções para instalar as ferramentas do Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.
4. Selecione **Recarregar** para ativar a extensão **Ferramentas do Azure Data Lake**.
5. Clique em **Recarregar Janela** para confirmar. Você pode ver **Azure data Lake ferramentas** no painel **extensões** .

## <a name="activate-azure-data-lake-tools"></a>Ativar Ferramentas do Azure Data Lake

Crie um arquivo .usql ou abra um existente para ativar a extensão.

## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

Você precisa abrir um arquivo U-SQL ou uma pasta para trabalhar com o U-SQL.

### <a name="to-open-the-sample-script"></a>Para abrir o script de exemplo

Abra a paleta de comandos (Ctrl + Shift + P) e insira **ADL: Abrir Script de Exemplo**. Isso abre outra instância deste exemplo. Você também pode editar, configurar e enviar um script nessa instância.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Para abrir uma pasta para o projeto U-SQL

1. No Visual Studio Code, selecione o menu **Arquivo** e, em seguida, selecione **Abrir Pasta**.
2. Especifique uma pasta e selecione **Selecionar Pasta**.
3. Selecione o menu **Arquivo** e selecione **Novo**. Um arquivo Sem título-1 é adicionado ao projeto.
4. Insira o código a seguir no arquivo Sem título-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   SAÍDA @departments     para "/Output/departments.csv" usando Outputters.Csv ();

    O script cria um arquivo departments.csv com alguns dados incluídos na pasta /output.

5. Salve o arquivo como **myUSQL.usql** na pasta aberta.

### <a name="to-compile-a-u-sql-script"></a>Para compilar um script U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Compilar Script**. Os resultados da compilação aparecem na janela **Saída**. Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Compilar Script** para compilar um trabalho em U-SQL. O resultado da compilação aparece no painel **Saída**.

### <a name="to-submit-a-u-sql-script"></a>Para enviar um script U-SQL

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Enviar Trabalho**. Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Enviar Trabalho**.

Depois de enviar um trabalho em U-SQL, os logs de envio aparecerão na janela **Saída** no VS Code. O modo de exibição de trabalho é exibido no painel direito. Se o envio for bem-sucedido, a URL do trabalho também será exibida. Você pode abrir a URL do trabalho em um navegador da Web para acompanhar o status do trabalho em tempo real.

Na guia **RESUMO** da exibição do trabalho, é possível ver os detalhes do trabalho. As principais funções incluem reenviar um script, duplicar um script e abrir no portal. Na guia **DADOS** da exibição do trabalho, é possível consultar os arquivos de entrada, arquivos de saída e arquivos de recursos. Os arquivos podem ser baixados para o computador local.

![Guia Resumo na exibição do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Guia Dados na exibição do trabalho](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Para definir o contexto padrão

É possível definir o contexto padrão para aplicar essa configuração a todos os arquivos de script caso não tenha definido parâmetros para arquivos individualmente.

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Definir contexto padrão**. Ou clique com o botão direito do mouse no editor de scripts e selecione **ADL: Definir contexto padrão**.
3. Escolha a conta, o banco de dados e o esquema que você deseja. A configuração é salva no arquivo de configuração xxx_settings.json.

   ![Conta, banco de dados e esquema definidos como o contexto padrão](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Para definir parâmetros de script

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Definir parâmetros de script**.
3. O arquivo xxx_settings.json é aberto com as propriedades a seguir:

   - **conta**: uma conta do Azure Data Lake Analytics na sua assinatura do Azure que é necessária para compilar e executar trabalhos de U-SQL. É necessário configurar a conta de computador antes de compilar e executar trabalhos do U-SQL.
   - **banco de dados**: um banco de dados em sua conta. O padrão é **Master**.
   - **esquema**: um esquema sob seu banco de dados. O padrão é **dbo**.
   - **optionalSettings**:
        - **prioridade**: o intervalo de prioridade é de 1 a 1000, sendo que 1 é a prioridade mais alta. O valor padrão é **1000**.
        - **degreeOfParallelism**: o intervalo de paralelismo é de 1 a 150. O valor padrão é o paralelismo máximo permitido em sua conta do Azure Data Lake Analytics.

   ![Conteúdos do arquivo JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Depois que a configuração é salva, a conta, o banco de dados e as informações de esquema são exibidas na barra de status no canto inferior esquerdo do arquivo .usql correspondente se você não tiver configurado o contexto padrão.

### <a name="to-set-git-ignore"></a>Para configurar Git Ignore

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Set Git Ignore**.

   - Se você não tiver um arquivo **.gitignore** na pasta de trabalho do VS Code, um arquivo chamado **.gitignore** será criado na pasta. Quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) são adicionados no arquivo por padrão. É possível fazer mais atualizações se for preciso.
   - Se você já tiver um arquivo **.gitignore** na pasta de trabalho do VS Code, a ferramenta adicionará quatro itens (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) em seu arquivo **.gitignore** caso os quatro itens não tenham sido incluídos no arquivo.

   ![Itens no arquivo .gitignore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Trabalhar com arquivos code-behind: C Sharp, Python e R

As ferramentas do Azure Data Lake oferecem suporte a vários códigos personalizados. Para obter instruções, consulte [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Trabalhar com assemblies

Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs]() (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).

Use as Ferramentas do Data Lake para registrar assemblies de código personalizado no catálogo do Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Para registrar um assembly

É possível registrar o assembly através do comando **ADL: Registrar Assembly** ou **ADL: Registrar Assembly (Avançado)**.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Para registrar por meio do comando ADL: Registrar Assembly

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Digite **ADL: Registrar Assembly**.
3. Especifique o caminho do assembly local.
4. Selecione uma conta do Data Lake Analytics.
5. Selecione um banco de dados.

O portal será aberto em um navegador e exibirá o processo de registro do assembly.  

Uma forma mais conveniente de disparar o comando **ADL: Registrar Assembly** é clicar com o botão direito do mouse no arquivo .dll no Explorador de Arquivos.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Para registrar por meio do comando ADL: Registrar Assembly (Avançado)

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: Registrar Assembly (Avançado)**.
3. Especifique o caminho do assembly local.
4. O arquivo JSON será exibido. Examine e edite as dependências do assembly e os parâmetros de recursos, se necessário. As instruções são exibidas na janela **saída** . Para prosseguir com o registro do assembly, salve (CTRL+S) o arquivo JSON.

   ![Arquivo JSON com dependências do assembly e parâmetros de recursos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- As ferramentas do Azure Data Lake detectam automaticamente se a DLL tem dependências do assembly. As dependências são exibidas no arquivo JSON depois de serem detectadas.
>- É possível carregar os recursos de DLL (por exemplo, .txt, .png e .csv) como parte do registro do assembly.

Outra maneira para disparar o comando **ADL: Registrar Assembly (Avançado)** é clicar com o botão direito no arquivo .dll no Explorador de Arquivos.

O código de U-SQL a seguir demonstra como chamar um assembly. No exemplo, o nome do assembly é *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Usar execução local e depuração local do U-SQL para usuários do Windows

A execução local do U-SQL testa seus dados locais e valida o script localmente, antes que seu código seja publicado no Data Lake Analytics. É possível usar o recurso de depuração local para concluir as seguintes tarefas antes que seu código seja enviado ao Data Lake Analytics:

- Depure o code-behind em C#.
- Explore o código.
- Valide o script localmente.

O recurso de depuração local e execução local funciona apenas em ambientes Windows e não tem suporte em sistemas operacionais baseados em Linux e macOS.

Para obter instruções sobre a execução e a depuração local, confira [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Antes de compilar e executar scripts U-SQL no Data Lake Analytics, você deve se conectar à sua conta do Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Para se conectar ao Azure usando um comando

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.

2. Digite **ADL: Logon**. As informações de logon são exibidas no canto inferior direito.

   ![Inserindo o comando de logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Notificação de entrada e autenticação](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Selecione **Copiar e Abrir** para abrir a [página da Web de logon](https://aka.ms/devicelogin). Cole o código na caixa e selecione **Continuar**.

    ![Página da Web para logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Siga as instruções para entrar na página da Web. Quando você estiver conectado, o nome da conta do Azure será exibido na barra de status no canto inferior esquerdo da janela do VS Code.

> [!NOTE]
>
> - Ferramentas do Data Lake conectam você automaticamente na próxima vez que você não sair.
> - Se sua conta tiver a autenticação por dois fatores habilitada, recomendamos o uso da autenticação por telefone em vez de usar um PIN.

Para sair, insira o comando **ADL: Logout**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Para se conectar ao Azure no explorer

Expanda **AZURE DATALAKE**, selecione **Entrar no Azure** e depois execute as etapas 3 e 4 de [Para se conectar ao Azure usando um comando](#sign-in-by-command).

![Seleção de “Entrar no Azure” no explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Você não pode sair pelo explorer. Para sair, confira [Para se conectar ao Azure usando um comando](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Criar um script de extração

Você pode criar script de extração para arquivos .csv, .tsv, .txt usando o comando **ADL: Criar Script EXTRACT** ou a partir do explorer do Azure Data Lake.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Para criar um script de extração usando um comando

1. Selecione Ctrl+Shift+P para abrir a paleta de comandos e digite **ADL: Criar Script EXTRACT**.
2. Especifique o caminho completo para um arquivo do Armazenamento do Azure e pressione Enter.
3. Selecione uma conta.
4. Para um arquivo .txt, selecione um delimitador para extrair o arquivo.

![Processo para a criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

O script de extração é gerado com base nas suas entradas. Para um script que não pode detectar as colunas, escolha uma das duas opções. Caso contrário, somente um script será gerado.

![Resultado da criação de um script de extração](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Para criar um script de extração a partir do explorer

Outra maneira de criar o script de extração é clicando com o botão direito do mouse no menu (atalho) nos arquivos .csv, .tsv ou .txt no Azure Data Lake Store ou no Armazenamento de blobs do Azure.

![Comando “Criar Script EXTRACT” a partir do menu de atalho](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Próximas etapas

- [Desenvolver U-SQL com Python, R e C Sharp para Azure Data Lake Analytics no VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Execução local e depuração local do U-SQL com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)