---
title: Configurar o Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 em máquinas virtuais do Azure
description: Execute o aplicativo de Micro foco BankDemo em máquinas virtuais do Azure (VMs) para aprender a usar o Micro Focus Enterprise Server e do Enterprise Developer.
author: sread
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 4491fc137c2c85e2be605f5e58fde6fd422efbbe
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621330"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Configurar o Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 no Azure

Quando você configura Micro foco Enterprise Server 4.0 e 4.0 de desenvolvedor Enterprise no Azure, você pode testar as implantações de cargas de trabalho IBM z/OS. Este artigo mostra como configurar CICS BankDemo, um aplicativo de exemplo que vem com o Enterprise Developer.

CICs representa o sistema de controle de informações do cliente, a plataforma de transações usada por muitos dos aplicativos de mainframe on-line. O aplicativo BankDemo é ótimo para aprender como o Enterprise Server e do Enterprise Developer operam e como gerenciar e implantar um aplicativo real completo com terminais de tela verde.

## <a name="prerequisites"></a>Pré-requisitos

- Uma VM com [Enterprise Developer](set-up-micro-focus-azure.md). Tenha em mente que o desenvolvedor empresarial tem uma instância completa do Enterprise Server nele para fins de desenvolvimento e teste. Esta instância é a instância do Enterprise Server usados para a demonstração.

- [SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Baixe e instale-o na VM Enterprise Developer. Enterprise Server requer um banco de dados para o gerenciamento de regiões do CICS e o aplicativo de BankDemo também usa um banco de dados do SQL Server chamado BANKDEMO. Esta demonstração pressupõe que você estiver usando o SQL Server Express para ambos os bancos de dados. Durante a instalação, selecione a instalação básica.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS é usado para gerenciar os bancos de dados e executar um script T-SQL. Baixe e instale-o na VM Enterprise Developer.

- [Visual Studio de 2019](https://azure.microsoft.com/downloads/) com o service pack mais recente ou [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), que pode ser baixado gratuitamente.

- Rumba a área de trabalho ou em outro emulador 3270.

## <a name="configure-the-windows-environment"></a>Configurar o ambiente do Windows

Depois de instalar o Enterprise Developer 4.0 na VM, você deve configurar a instância do servidor corporativo que o acompanha. Para fazer isso, você precisa instalar alguns recursos adicionais do Windows da seguinte maneira.

1. Use o RDP para fazer logon no Enterprise Server 4.0 VM criada.

2. Clique no **pesquisa** ícone Avançar para o **iniciar** botão e o tipo **recursos do Windows**. Abre o assistente Gerenciador do servidor adicionar funções e recursos.

3. Selecione **função de servidor Web (IIS)** e, em seguida, verifique as seguintes opções:

    - Ferramentas de gerenciamento da Web
    - Compatibilidade de gerenciamento do IIS 6 (Selecionar todos os recursos disponíveis)
    - Console de gerenciamento do IIS
    - Ferramentas e Scripts de gerenciamento do IIS
    - Serviço de gerenciamento do IIS

4. Selecione **serviços da World Wide Web**e verifique as seguintes opções:

     Recursos de desenvolvimento de aplicativo:
    - Extensibilidade .NET
    - ASP.NET
    - Recursos HTTP comuns: Adicionar todos os recursos disponíveis
    - Integridade e diagnóstico: Adicionar todos os recursos disponíveis
    - Segurança:
        - Autenticação básica
        - Autenticação do Windows

5. Selecione **Windows Process Activation Service** e todos os seus filhos.

6. Para **recursos**, verifique **Microsoft .NET framework 3.5.1**e verifique as seguintes opções:

    - Ativação do Windows Communication Foundation HTTP
    - Ativação não HTTP do Windows Communication Foundation

7. Para **recursos**, verifique **Microsoft .NET framework 4.6**e verifique as seguintes opções:

   - Ativação de Pipe nomeado
   - Ativação TCP
   - Compartilhamento de porta TCP

     ![Adicione Assistente de funções e recursos: Serviços de função](media/01-demo-roles.png)

8. Quando você tiver selecionado todas as opções, clique em **próxima** para instalar.

9. Após os recursos do Windows, vá para **painel de controle \> sistema e segurança \> ferramentas administrativas**e selecione **serviços**. Role para baixo e verifique se os seguintes serviços estão em execução e definido como **automática**:

    - **NetTcpPortSharing**
    - **Adaptador de escuta NET. pipe**
    - **Adaptador de escuta NET. TCP**

10. Para configurar o suporte do IIS e WAS, no menu, localize **Prompt de comando Micro foco Enterprise Developer (64 bits)** e executar como **administrador**.

11. Tipo de **wassetup – i** e pressione **Enter**.

12. Depois que o script é executado, você pode fechar a janela.

## <a name="configure-the-local-system-account-for-sql-server"></a>Configurar a conta sistema local para o SQL Server

Alguns processos de Enterprise Server precisam ser capaz de entrar no SQL Server e criar bancos de dados e outros objetos. Esses processos usam a conta sistema local, portanto você deve fornecer a autoridade de sysadmin nessa conta.

1. Inicie o **SSMS** e clique em **Connect** para se conectar ao servidor SQLEXPRESS local usando a autenticação do Windows. Ele deve estar disponível na **nome do servidor** lista.

2. À esquerda, expanda o **segurança** pasta e selecione **logons**.

3. Selecione **Autoridade NT\\SYSTEM** e selecione **propriedades**.

4. Selecione **funções de servidor** e verifique **sysadmin**.

     ![Janela do Pesquisador de objetos do SSMS: Propriedades de Logon](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Criar o banco de dados BankDemo e todos os seus objetos

1. Abra **Windows Explorer** e navegue até **c:\\usuários\\público\\documentos\\Micro Focus\\Enterprise Developer\\ Amostras\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Copie o conteúdo da **BankDemoCreateAll.SQL** arquivo na área de transferência.

3. Abra **SSMS**. À direita, clique em **Server** e selecione **nova consulta**.

4. Cole o conteúdo da área de transferência para o **nova consulta** caixa.

5. Execute o SQL clicando **Execute** da **comando** guia a consulta acima.

A consulta deve ser executado sem erros. Quando ela for concluída, você tem o banco de dados de exemplo para o aplicativo BankDemo.

![SQLQuery1.sql output](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Verificar se os objetos e tabelas de banco de dados foi criados

1. Clique com botão direito do **BANKDEMO** do banco de dados e selecione **atualizar**.

2. Expanda o **banco de dados** e selecione **tabelas**. Você deve ver algo semelhante ao seguinte.

     ![Tabela BANKDEMO expandida no Pesquisador de objetos](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Compile o aplicativo no Enterprise Developer

1. Abra o Visual Studio e entre.

2. Sob o **arquivo** opção de menu, selecione **Abrir projeto/solução**, navegue até **c:\\usuários\\público\\documentos\\Micro Foco\\Enterprise Developer\\amostras\\Mainframe\\CICS\\DotNet\\BankDemo**e selecione o **sln**arquivo.

3. Levar algum tempo para examinar os objetos. Programas de COBOL são mostrados no Gerenciador de soluções com a extensão CBL juntamente com CopyBooks (CPY) e JCL.

4. Clique com botão direito do **BankDemo2** projeto e selecione **definir como projeto de inicialização**.

    > [!NOTE]
    > O projeto BankDemo utiliza HCOSS (opção de compatibilidade de Host para o SQL Server,) que não é usado para esta demonstração.

5. Na **Gerenciador de soluções**, clique com botão direito do **BankDemo2** projeto e selecione **Build**.

    > [!NOTE]
    > Criando no nível da solução resulta em erros, como HCOSS não foi configurado.

6. Quando o projeto é compilado, examine os **saída** janela. Deve se parecer com a imagem a seguir.

     ![Janela de saída, mostrando a compilação bem-sucedida](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Implantar o aplicativo BankDemo no banco de dados de região

1. Abra um prompt de comando do Enterprise Developer (64 bits) como administrador.

2. Navegue até o **% % pública\\documentos\\Micro Focus\\Enterprise Developer\\samples\\Mainframe\\CICS\\DotNet\\ BankDemo**.

3. No prompt de comando, execute **bankdemodbdeploy** e incluir o parâmetro para o banco de dados implantar, por exemplo:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Certifique-se de usar uma barra (/), não uma barra invertida (\\). Esse script é executado por algum tempo.

![Administração: Janela do Prompt de comando de desenvolvedor Enterprise](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Criar a região BankDemo no administrador de empresa para .NET

1. Abra o **Enterprise Server para administração de .NET** interface do usuário.

2. Para iniciar o snap-in do MMC a partir do Windows **inicie** menu, escolha **Micro foco Enterprise Developer \> Configuration \> Enterprise Server para administração de .NET**. (Para o Windows Server, escolha **Micro foco Enterprise Developer \> Enterprise Server para administração de .NET**).

3. Expanda o **regiões** contêiner no painel esquerdo e, em seguida, clique com botão direito **CICS**.

4. Selecione **região definem** para criar uma nova região CICS chamado **BANKDEMO**, hospedado no banco de dados (local).

5. Fornecer a instância do servidor de banco de dados, clique em **próxima**e, em seguida, insira o nome da região **BANKDEMO**.

     ![Definir caixa de diálogo de região](media/07-demo-cics.png)

6. Para selecionar o arquivo de definição de região para o banco de dados entre regiões, localize **região\_bankdemo\_db.config** na **c:\\usuários\\público\\ Documentos\\Micro Focus\\Enterprise Developer\\amostras\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Defina a região - nome da região: BANKDEMO](media/08-demo-cics.png)

7. Clique em **Finalizar**.

## <a name="create-xa-resource-definitions"></a>Criar definições de recursos XA

1. No painel esquerdo do **Enterprise Server para a administração do .NET** interface do usuário, expanda **sistema**e, em seguida, **definições de recursos XA**. Essa configuração define como a região interopera com o Enterprise Server e os bancos de dados do aplicativo.

2. Clique duas vezes em **definições de recursos XA** e selecione **Adicionar instância de servidor**.

3. Na caixa suspensa, selecione **instância de serviço de banco de dados**. Ele será o SQLEXPRESS do computador local.

4. Selecione a instância de sob o **definições de recursos XA (machinename\\sqlexpress)** contêiner e clique em **Add**.

5. Selecione **definição de recurso do banco de dados XA** e, em seguida, digite **BANKDEMO** para o **nome** e **região**.

     ![Nova tela de definição de recurso do banco de dados XA](media/09-demo-xa.png)

6. Clique nas reticências ( **...** ) para abrir o Assistente de cadeia de caracteres de Conexão. Para **nome do servidor**, digite **(local)\\SQLEXPRESS**. Para **Logon**, selecione **autenticação do Windows**. Nome do banco de dados, digite **BANKDEMO**

     ![Cadeia de caracteres de Conexão de tela de edição](media/10-demo-string.png)

7. Teste a conexão.

## <a name="start-the-bankdemo-region"></a>Iniciar a região BANKDEMO

> [!NOTE]
> A primeira etapa é importante: Você deve definir a região para usar a definição de recurso XA que acabou de criar.

1. Navegue até a **BANDEMO CICS região** sob o **contêiner regiões**e, em seguida, selecione **Editar arquivo de inicialização de região** do **ações** painel. Role para baixo até as propriedades do SQL e insira **bankdemo** para o **nome do recurso XA**, ou use o botão de reticências para selecioná-lo.

2. Clique o **salvar** ícone para salvar suas alterações.

3. Clique com botão direito **BANKDEMO CICS região** na **Console** painel e selecione **iniciar/parar região**.

4. Na parte inferior da **região de iniciar/parar** caixa que aparece no painel central, selecione **iniciar**. Depois de alguns segundos, a região é iniciado.

     ![Caixa de início/parada de SQL](media/11-demo-sql.png)

     ![Região do CICS BANKDEMO - tela Introdução](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Criar um ouvinte

Crie um ouvinte para as sessões de TN3270 que acessam o aplicativo BankDemo.

1. No painel esquerdo, expanda **editores de configuração** e selecione **ouvinte**.

2. Clique o **abrir arquivo** ícone e selecione o **seelistener.exe.config** arquivo. Esse arquivo será editado e é carregado sempre Enterprise Server for iniciado.

3. Observe que as duas regiões definido anteriormente (ESDEMO e JCLDEMO).

4. Para criar uma nova região para BANKDEMO, clique com botão direito **regiões**e selecione **adicionar região**.

5. Selecione **BANKDEMO região**.

6. Adicione um canal TN3270 clicando **região BANKDEMO** e selecionando **Adicionar canal**.

7. Para **nome**, insira **TN3270**. Para **porta**, insira **9024**. O aplicativo ESDEMO usa a porta 9230 para que você precise usar uma porta diferente.

8. Para salvar o arquivo, clique o **salve** ícone ou escolha **arquivo** \> **salvar**.

9. Para iniciar o ouvinte, clique o **iniciar o ouvinte** ícone ou escolha **opções** \> **ouvinte iniciar**.

     ![Janelas do Editor de configuração de ouvinte](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Configurar Rumba para acessar o aplicativo BankDemo

A última tarefa que você precisa fazer é configurar uma sessão de 3270 usando Rumba, um emulador 3270. Esta etapa permite que você acesse o aplicativo BankDemo através do ouvinte que você criou.

1. De que o Windows **iniciar** menu, inicie o Rumba Desktop.

2. Sob o **conexões** item de menu, selecione **TN3270**.

3. Clique em **inserir** e digite **127.0.0.1** para o endereço IP e **9024** para a porta definida pelo usuário.

4. Na parte inferior da caixa de diálogo, clique em **Connect**. É exibida uma tela preta do CICS.

5. Tipo de **bank** para exibir a tela 3270 inicial para o aplicativo BankDemo.

6. Para a ID de usuário, digite **B0001** e a senha, digite qualquer coisa. A primeira tela BANK20 é aberto.

![Tela de boas-vindas do vídeo de mainframe](media/14-demo.png)
![tela de demonstração do subsistema de vídeo de Mainframe - Rumba -](media/15-demo.png)

Parabéns! Agora você está executando um aplicativo do CICS no Azure usando o Micro Focus Enterprise Server.

## <a name="next-steps"></a>Próximas etapas

- [Executar o Enterprise Server em contêineres do Docker no Azure](run-enterprise-server-container.md)
- [Migração de mainframe - Portal](https://blogs.msdn.microsoft.com/azurecat/2018/11/16/mainframe-migration-to-azure-portal/)
- [Máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/overview)
- [Solução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Desmistificando mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
