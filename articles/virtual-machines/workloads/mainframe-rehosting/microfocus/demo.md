---
title: Configurar o micro Focus CICS BankDemo para o micro Focus Enterprise Developer 4,0 em máquinas virtuais do Azure
description: Execute o aplicativo micro Focus BankDemo nas VMs (máquinas virtuais) do Azure para aprender a usar o micro Focus Enterprise Server e o desenvolvedor corporativo.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 2d48c767b836771600b3491299e0bdb33aacb75b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733142"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurar o micro Focus CICS BankDemo para o micro Focus Enterprise Developer 4,0 no Azure

Ao configurar o micro Focus Enterprise Server 4,0 e o Enterprise Developer 4,0 no Azure, você pode testar as implantações de cargas de trabalho do IBM z/OS. Este artigo mostra como configurar o CICS BankDemo, um aplicativo de exemplo fornecido com o Enterprise Developer.

CICs significa sistema de controle de informações do cliente, a plataforma de transação usada por muitos dos aplicativos de mainframe online. O aplicativo BankDemo é ótimo para aprender como o Enterprise Server e o desenvolvedor corporativo operam e como gerenciar e implantar um aplicativo real completo com terminais de tela verdes.

> [!NOTE]
> Em breve: instruções para configurar o [micro Focus Enterprise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) em VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma VM com [desenvolvedor empresarial](set-up-micro-focus-azure.md). Tenha em mente que o desenvolvedor empresarial tem uma instância completa do servidor corporativo para fins de desenvolvimento e teste. Esta instância é a instância do Enterprise Server usada para a demonstração.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Baixe e instale-o na VM do desenvolvedor empresarial. O servidor corporativo requer um banco de dados para o gerenciamento de regiões CICS e o aplicativo BankDemo também usa um banco de dados SQL Server chamado BANKDEMO. Esta demonstração pressupõe que você esteja usando SQL Server Express para ambos os bancos de dados. Ao instalar o, selecione a instalação básica.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). O SSMS é usado para gerenciar os bancos de dados e executar um script T-SQL. Baixe e instale-o na VM do desenvolvedor empresarial.

- O [visual studio 2019](https://azure.microsoft.com/downloads/) com a mais recente Service Pack ou a [comunidade do Visual Studio](https://visualstudio.microsoft.com/vs/community/), que você pode baixar gratuitamente.

- Rumba desktop ou outro emulador 3270.

## <a name="configure-the-windows-environment"></a>Configurar o ambiente do Windows

Depois de instalar o Enterprise Developer 4,0 na VM, você deve configurar a instância do servidor corporativo que vem com ele. Para fazer isso, você precisa instalar alguns recursos adicionais do Windows da seguinte maneira.

1. Use o RDP para fazer logon na VM do Enterprise Server 4,0 que você criou.

2. Clique no ícone de **pesquisa** ao lado do botão **Iniciar** e digite **recursos do Windows**. O assistente Gerenciador do Servidor adicionar funções e recursos é aberto.

3. Selecione **função do servidor Web (IIS)** e, em seguida, verifique as seguintes opções:

    - Ferramentas de gerenciamento da Web
    - Compatibilidade de gerenciamento do IIS 6 (selecione todos os recursos disponíveis)
    - Console de Gerenciamento IIS
    - Scripts e Ferramentas de Gerenciamento do IIS
    - Serviço de gerenciamento do IIS

4. Selecione **serviços de World Wide Web** e verifique as seguintes opções:

     Recursos de desenvolvimento de aplicativos:
    - Extensibilidade .NET
    - ASP.NET
    - Recursos HTTP comuns: adicionar todos os recursos disponíveis
    - Integridade e diagnóstico: adicionar todos os recursos disponíveis
    - Segurança:
        - Autenticação Básica
        - Autenticação do Windows

5. Selecione **serviço de ativação de processos do Windows** e todos os seus filhos.

6. Para **recursos**, marque **Microsoft .NET Framework 3.5.1** e verifique as seguintes opções:

    - Windows Communication Foundation ativação HTTP
    - Windows Communication Foundation ativação não HTTP

7. Para **recursos**, verifique **Microsoft .NET Framework 4,6** e verifique as seguintes opções:

   - Ativação de pipe nomeado
   - Ativação TCP
   - Compartilhamento de porta TCP

     ![Assistente para adicionar funções e recursos: serviços de função](media/01-demo-roles.png)

8. Quando você tiver selecionado todas as opções, clique em **Avançar** para instalar.

9. Após os recursos do Windows, vá até **sistema do painel \> de controle e \> Ferramentas administrativas de segurança** e selecione **Serviços**. Role para baixo e verifique se os seguintes serviços estão em execução e definidos como **automáticos**:

    - **NetTcpPortSharing**
    - **Adaptador ouvinte do Net.Pipe**
    - **Adaptador de escuta net. TCP**

10. Para configurar o IIS e o WAS support, no menu, localize **micro Focus Enterprise prompt de comando do desenvolvedor (64 bits)** e execute como **administrador**.

11. Digite **wassetup – i** e pressione **Enter**.

12. Depois que o script for executado, você poderá fechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurar a conta do sistema local para SQL Server

Alguns processos do servidor corporativo precisam ser capazes de entrar SQL Server e criar bancos de dados e outros objetos. Esses processos usam a conta sistema local, portanto, você deve conceder autoridade sysadmin para essa conta.

1. Inicie o **SSMS** e clique em **conectar** para conectar-se ao servidor SQLExpress local usando a autenticação do Windows. Ele deve estar disponível na lista **nome do servidor** .

2. À esquerda, expanda a pasta **segurança** e selecione **logons**.

3. Selecione **\\ sistema de Autoridade NT** e selecione **Propriedades**.

4. Selecione **funções de servidor** e verifique **sysadmin**.

     ![Janela do pesquisador de objetos do SSMS: Propriedades de logon](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Criar o banco de dados BankDemo e todos os seus objetos

1. Abra o **Windows Explorer** e navegue até **C: \\ usuários \\ Public \\ Documents \\ micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ dotnet \\ BankDemo \\ SQL**.

2. Copie o conteúdo do arquivo **BankDemoCreateAll. SQL** para a área de transferência.

3. Abra o **SSMS**. À direita, clique em **servidor** e selecione **nova consulta**.

4. Cole o conteúdo da área de transferência na caixa **nova consulta** .

5. Execute o SQL clicando em **executar** na guia de **comando** acima da consulta.

A consulta deve ser executada sem erros. Quando estiver concluído, você terá o banco de dados de exemplo para o aplicativo BankDemo.

![Saída de SQLQuery1. SQL](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verifique se as tabelas e os objetos do banco de dados foram criados

1. Clique com o botão direito do mouse no banco de dados **BANKDEMO** e selecione **Atualizar**.

2. Expanda o **banco de dados** e selecione **tabelas**. Você deverá ver algo semelhante ao seguinte.

     ![Tabela BANKDEMO expandida no Pesquisador de objetos](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Crie o aplicativo no desenvolvedor empresarial

1. Abra o Visual Studio e entre.

2. Na opção de menu **arquivo** , selecione **projeto/solução aberta**, navegue até **C: \\ usuários \\ públicos \\ documentos \\ micro foco \\ Enterprise Developer \\ exemplos de \\ mainframe \\ CICS \\ dotnet \\ BankDemo** e selecione o arquivo **sln** .

3. Reserve algum tempo para examinar os objetos. Os programas COBOL são mostrados em Gerenciador de Soluções com a extensão CBL, juntamente com CopyBooks (CPY) e JCL.

4. Clique com o botão direito do mouse no projeto **BankDemo2** e selecione **definir como projeto de inicialização**.

    > [!NOTE]
    > O projeto BankDemo usa HCOSS (opção de compatibilidade de host para SQL Server), que não é usada para esta demonstração.

5. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **BankDemo2** e selecione **Compilar**.

    > [!NOTE]
    > A criação no nível da solução resulta em erros, pois HCOSS não foi configurado.

6. Quando o projeto for compilado, examine a janela **saída** . Ele deverá ter a aparência mostrada na imagem abaixo.

     ![Janela de saída mostrando compilação bem-sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implantar o aplicativo BankDemo no banco de dados de região

1. Abra um prompt de comando do desenvolvedor empresarial (64 bits) como administrador.

2. Navegue até o **% Public% \\ Documents \\ micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ dotnet \\ BankDemo**.

3. No prompt de comando, execute **bankdemodbdeploy** e inclua o parâmetro do banco de dados no qual implantar, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de usar uma barra (/) não uma barra invertida ( \\ ). Esse script é executado por um tempo.

![Administração: janela do Prompt de Comando do Desenvolvedor empresarial](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Criar a região BankDemo no administrador corporativo para .NET

1. Abra o **servidor Enterprise para** a interface do usuário de administração do .net.

2. Para iniciar o snap-in do MMC, no menu **Iniciar** do Windows, escolha **micro Focus Enterprise Developer \> Configuration \> Enterprise Server para .net admin**. (Para o Windows Server, escolha **micro Focus Enterprise Developer \> Enterprise Server para .net admin**).

3. Expanda o contêiner **regiões** no painel esquerdo e clique com o botão direito do mouse em **CICS**.

4. Selecione **definir região** para criar uma nova região CICS chamada **BANKDEMO**, hospedada no banco de dados (local).

5. Forneça a instância do servidor de banco de dados, clique em **Avançar** e digite o nome da região **BANKDEMO**.

     ![Caixa de diálogo Definir região](media/07-demo-cics.png)

6. Para selecionar o arquivo de definição de região para o banco de dados entre regiões, localize **região \_ bankdemo \_db.config** em **C: \\ usuários \\ público \\ documentos \\ micro Focus \\ Enterprise Developer \\ Samples \\ mainframe \\ CICS \\ dotnet \\ bankdemo**.

     ![Definir região-nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **Concluir**.

## <a name="create-xa-resource-definitions"></a>Criar definições de recursos XA

1. No painel esquerdo do **servidor corporativo para** a interface do usuário do .net Administration, expanda **sistema** e, em seguida, **definições de recurso XA**. Essa configuração define como a região interopera com o servidor corporativo e os bancos de dados de aplicativo.

2. Clique com o botão direito do mouse em **definições de recursos XA** e selecione **Adicionar instância de servidor**.

3. Na caixa suspensa, selecione **instância do serviço de banco de dados**. Será o computador local SQLExpress.

4. Selecione a instância sob o contêiner de **definições de recursos XA (MachineName \\ SQLExpress)** e clique em **Adicionar**.

5. Selecione **definição de recurso xa de banco de dados** e digite **BANKDEMO** para o **nome** e a **região**.

     ![Tela de definição de recurso XA do novo banco de dados](media/09-demo-xa.png)

6. Clique nas reticências (**...**) para abrir o assistente de cadeia de conexão. Para **nome do servidor**, digite **(local) \\ SQLExpress**. Para **logon**, selecione **autenticação do Windows**. Para nome do banco de dados, digite **BANKDEMO**

     ![Tela Editar Cadeia de conexão](media/10-demo-string.png)

7. Teste a conexão.

## <a name="start-the-bankdemo-region"></a>Iniciar a região BANKDEMO

> [!NOTE]
> A primeira etapa é importante: você deve definir a região para usar a definição de recurso XA que você acabou de criar.

1. Navegue até a **região BANDEMO CICS** sob o **contêiner regiões** e, em seguida, selecione **Editar arquivo de inicialização de região** no painel **ações** . Role para baixo até as propriedades do SQL e insira **bankdemo** para o **nome do recurso XA** ou use as reticências para selecioná-lo.

2. Clique no ícone **salvar** para salvar as alterações.

3. Clique com o botão direito do mouse em **BANKDEMO CICS região** no painel de **console** e selecione **iniciar/parar região**.

4. Na parte inferior da caixa **iniciar/parar região** que aparece no painel central, selecione **Iniciar**. Depois de alguns segundos, a região é iniciada.

     ![Caixa de início/parada do SQL](media/11-demo-sql.png)

     ![CICS região BANKDEMO – tela iniciada](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Criar um ouvinte

Crie um ouvinte para as sessões TN3270 que acessam o aplicativo BankDemo.

1. No painel esquerdo, expanda **Configuration Editors** e selecione **Listener**.

2. Clique no ícone **Abrir arquivo** e selecione o arquivo de **seelistener.exe.config** . Esse arquivo será editado e carregado toda vez que o Enterprise Server for iniciado.

3. Observe as duas regiões definidas anteriormente (ESDEMO e JCLDEMO).

4. Para criar uma nova região para BANKDEMO, clique com o botão direito do mouse em **regiões** e selecione **Adicionar região**.

5. Selecione a **região BANKDEMO**.

6. Adicione um canal do TN3270 clicando com o botão direito do mouse em **região BANKDEMO** e selecionando **Adicionar canal**.

7. Para **nome**, insira **TN3270**. Para **porta**, insira **9024**. O aplicativo ESDEMO usa a porta 9230 para que você precise usar uma porta diferente.

8. Para salvar o arquivo, clique no ícone **salvar** ou escolha **arquivo** \> **salvar**.

9. Para iniciar o ouvinte, clique no ícone **Iniciar ouvinte** ou escolha **Opções** \> **Iniciar ouvinte**.

     ![Janelas do editor de configuração do ouvinte](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurar o rumba para acessar o aplicativo BankDemo

A coisa final que você precisa fazer é configurar uma sessão 3270 usando rumba, um emulador 3270. Esta etapa permite que você acesse o aplicativo BankDemo por meio do ouvinte que você criou.

1. No menu **Iniciar** do Windows, inicie o rumba desktop.

2. No item de menu **conexões** , selecione **TN3270**.

3. Clique em **Inserir** e digite **127.0.0.1** para o endereço IP e **9024** para a porta definida pelo usuário.

4. Na parte inferior da caixa de diálogo, clique em **conectar**. Uma tela CICS preta é exibida.

5. Digite **Bank** para exibir a tela inicial de 3270 para o aplicativo BankDemo.

6. Para ID de usuário, digite **B0001** e para a senha, digite qualquer coisa. A primeira tela BANK20 é aberta.

![Vídeo de tela de boas-vindas do mainframe exibição ](media/14-demo.png)
 ![ -rumba-tela de demonstração do subsistema](media/15-demo.png)

Parabéns! Agora você está executando um aplicativo CICS no Azure usando o micro Focus Enterprise Server.

## <a name="next-steps"></a>Próximas etapas

- [Executar o servidor corporativo em contêineres do Docker no Azure](run-enterprise-server-container.md)
- [Migração de mainframe-Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Máquinas virtuais](../../../linux/overview.md)
- [Solução de problemas](../../../troubleshooting/index.yml)
- [Desmistificando a migração do mainframe para o Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
