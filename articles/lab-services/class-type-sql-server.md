---
title: Configurar um laboratório para gerenciar e desenvolver com o banco de dados SQL do Azure | Azure Lab Services
description: Saiba como configurar um laboratório para gerenciar e desenvolver com o banco de dados SQL do Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: fb1b9e3458d08b8387c7f3978ff83c097fad2375
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644038"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Configurar um laboratório para gerenciar e desenvolver com SQL Server

Este artigo descreve como configurar um laboratório para uma classe básica de desenvolvimento e gerenciamento de SQL Server no Azure Lab Services.  Os conceitos de banco de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. O linguagem SQL (SQL) é um padrão internacional.  O SQL é a linguagem padrão para gerenciamento de banco de dados de relações, incluindo a adição, o acesso e o gerenciamento de conteúdo em um banco de dados.  Ele é mais observado para seu processamento rápido, confiabilidade comprovada, facilidade e flexibilidade de uso.

Neste artigo, mostraremos como configurar um modelo de máquina virtual em um laboratório com o [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  Para este laboratório, usaremos um [banco de dados SQL Server](../azure-sql/database/sql-database-paas-overview.md) compartilhado para todo o laboratório. O [banco de dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md) é uma plataforma como serviço (PaaS) mecanismo de banco de dados oferta do Azure.

## <a name="lab-configuration"></a>Configuração do laboratório

Para configurar este laboratório, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [tutorial para configurar uma conta de laboratório](./tutorial-setup-lab-account.md). Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte [especificar imagens do Marketplace disponíveis para criadores de laboratório](specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem do Marketplace | Habilite a "comunidade do Visual Studio 2019 (versão mais recente) na imagem do Windows 10 Enterprise N (x64)" para uso em sua conta de laboratório. |

### <a name="shared-resource-configuration"></a>Configuração de recurso compartilhado

Para usar um recurso compartilhado no Lab Services, primeiro você precisa criar a rede virtual e os próprios recursos.  Para criar a rede virtual e conectá-la ao laboratório, siga [como criar um laboratório com um recurso compartilhado no Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Lembre-se de que todos os recursos externos aos serviços de laboratório serão cobrados separadamente e não serão incluídos nas estimativas de custo do laboratório.

>[!WARNING]
>Os recursos compartilhados para um laboratório devem ser configurados antes que o laboratório seja criado.  Se a vnet não estiver [emparelhada com a conta de laboratório](how-to-connect-peer-virtual-network.md) *antes* da criação do laboratório, o laboratório não terá acesso ao recurso compartilhado.

Agora que o lado da rede das coisas é tratado, vamos criar um banco de dados SQL Server.  Vamos criar um [banco de dados individual](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) , pois é a opção de implantação mais rápida para o banco de dados SQL do Azure.  Para outras opções de implantação, crie um [pool elástico](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), uma [instância gerenciada](../azure-sql/managed-instance/instance-create-quickstart.md)ou uma [máquina virtual do SQL](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. No menu portal do Azure, escolha **criar novo recurso**.
2. Escolha **banco de dados SQL** e clique no botão **criar** .
3. Na guia **noções básicas** do formulário **criar banco de dados SQL** , selecione o grupo de recursos para o banco de dados.  Usaremos *SQLDB-RG*.
4. Para **nome do banco de dados**, insira *classlabdb*.
5. Na configuração do **servidor** , clique em **criar novo** para criar um novo servidor para manter o banco de dados.
6. No submenu **novo servidor** , insira o nome do servidor.  Usaremos *classlabdbserver*.  O nome do servidor deve ser globalmente exclusivo.
7. Insira *azureuser* para o **logon de administrador do servidor**.
8. Insira uma senha fácil de memorizar.  A senha deve ter pelo menos oito caracteres de comprimento e conter caracteres especiais.
9. Escolha região para o **local**.  Se possível, insira o mesmo local que a conta de laboratório e a vnet emparelhada para minimizar a latência.
10. Clique em **OK** para retornar ao formulário **criar banco de dados SQL** .
11. Clique no link **configurar banco de dados** na configuração **computação + armazenamento** .
12. Modifique as configurações de banco de dados conforme necessário para a classe.  Você pode escolher entre opções provisionadas e sem servidor.  Para este exemplo, usaremos a opção de servidor autodimensionada com Max vCores de 4, min vCores of 1. Manteremos a configuração de autopausa no mínimo de 1 hora. Clique em **Aplicar**.
13. Clique no botão **Avançar: rede** .
14. Na guia rede, escolha ponto de extremidade privado para o **método de conectividade**.
15. Na seção **pontos** de extremidade particulares, clique em **Adicionar ponto de extremidades privado**.
16. No submenu **criar ponto de extremidade privado** , escolha o mesmo grupo de recursos que sua rede virtual emparelhada com a conta do laboratório.
17. Para **local**, escolha o mesmo local da rede virtual.
18. Para **nome**, digite *labsql-Endpt*.
19. Deixe o subrecurso de destino definido como SqlServer.
20. Para **rede virtual**, escolha a mesma rede virtual emparelhada com a conta do laboratório.
21. Para **sub-rede**, escolha a sub-rede na qual você deseja hospedar o ponto de extremidade.  O IP atribuído ao ponto de extremidade será do intervalo atribuído a essa sub-rede.
22. Defina **integrar com DNS privado** como **não**. Para simplificar, usaremos o DNS do Azure pela própria zona DNS privada ou pelos nossos próprios servidores DNS.
23. Clique em **OK**.
24. Clique em **Avançar: configurações adicionais**.
25. Para a configuração **usar dados existentes** , escolha **exemplo**.  Os dados do AdventureWorksLT Database serão usados quando o banco de dado for criado.
26. Clique em **Revisar + Criar**.
27. Clique em **Criar**.

Depois que a implantação do banco de dados SQL for concluída com êxito, podemos criar o laboratório e instalar o software no computador do modelo de laboratório.

### <a name="lab-settings"></a>Configurações do laboratório

Use as configurações na tabela abaixo ao configurar um laboratório de sala de aula. Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [configurar um tutorial de laboratório de sala de aula](tutorial-setup-classroom-lab.md).

| Configurações do laboratório | Valor/instruções |
| ------------ | ------------------ |
| Tamanho da Máquina Virtual | Média: Este tamanho é mais adequado a bancos de dados relacionais, cache na memória e análise. |
| Imagem de máquina virtual | Comunidade do Visual Studio 2019 (versão mais recente) no Windows 10 Enterprise N (x64) |

Agora que o nosso laboratório foi criado, vamos modificar o computador do modelo com o software de que precisamos.

## <a name="visual-studio"></a>Visual Studio

A imagem escolhida acima inclui a [comunidade do Visual Studio 2019](https://visualstudio.microsoft.com/vs/community/).  Todas as cargas de trabalho e conjuntos de ferramentas já estão instalados na imagem.  Use o Instalador do Visual Studio para [instalar as ferramentas opcionais](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) que você pode desejar.  [Entre no Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019&preserve-view=true#how-to-sign-in-to-visual-studio) para desbloquear a Community Edition.

O Visual Studio inclui o conjunto de ferramentas de **armazenamento e processamento de dados** , que inclui SQL Server Data Tools (SSDT).  Para obter mais informações sobre os recursos do SSDT, consulte [visão geral de SQL Server Data Tools](/sql/ssdt/sql-server-data-tools).  Para verificar se a conexão com o SQL Server compartilhado para a classe será bem-sucedida, consulte [conectar-se a um banco de dados e procurar objetos existentes](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects). Se solicitado, adicione o IP do computador de modelo à [lista de computadores permitidos](../azure-sql/database/firewall-configure.md) que podem se conectar à sua instância do SQL Server.

O Visual Studio dá suporte a várias cargas de trabalho, incluindo cargas do & **Web & Cloud** e **desktops móveis** .  Ambas as cargas de trabalho oferecem suporte a SQL Server como uma fonte de dados. Para obter mais informações sobre como usar ASP.NET Core para SQL Server, consulte [compilar um ASP.NET Core e um aplicativo de banco de dados SQL no tutorial do serviço Azure app](../app-service/tutorial-dotnetcore-sqldb-app.md) .  Use a biblioteca [System. Data. SqlClient](/dotnet/api/system.data.sqlclient) para se conectar a um banco de dados SQL de um aplicativo [Xamarin](/xamarin) .

## <a name="install-azure-data-studio"></a>Instalar o Azure Data Studio

O [Azure Data Studio](https://github.com/microsoft/azuredatastudio) é um ambiente de área de trabalho multiplataforma entre plataformas para profissionais de dados que usam a família de plataformas de dados locais e na nuvem no Windows, no MacOS e no Linux.

1. Baixe o [instalador do *sistema* Azure Data Studio para Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Para localizar instaladores para outros sistemas operacionais com suporte, vá para a página de download do [Azure Data Studio](/sql/azure-data-studio/download) .
2. Na página **contrato de licença** , selecione **aceito o contrato**. Clique em **Avançar**.
3. Na página **Selecionar Local de Destino**, clique em **Avançar**.
4. Na página **Selecionar Pasta do Menu Iniciar**, clique em **Avançar**.
5. Na página **selecionar tarefas adicionais** , marque **criar um ícone de área de trabalho** se desejar um ícone de área de trabalho.  Clique em **Avançar**.
6. Em **pronto para instalar**, clique em **Avançar**.
7. Aguarde a execução do instalador.  Clique em **Concluir**.

Agora que temos Azure Data Studio instalado, vamos configurar a conexão com o banco de dados SQL do Azure.

1. Na página de **boas-vindas** para Azure Data Studio, clique no link **nova conexão** .
2. Na caixa **detalhes da conexão** , preencha as informações necessárias.
    - Definir **servidor** como *classlabdbserver.Database.Windows.net*
    - Definir nome de **usuário** como *azureuser*
    - Defina **senha** como senha usada para criar o banco de dados.
    - Marque a opção **lembrar senha**.
    - Para **banco de dados**, selecione *classlabdb*.
3. Clique em **Conectar**.

## <a name="install-sql-server-management-studio"></a>Instale o SQL Server Management Studio

O [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) é um ambiente integrado para gerenciar qualquer infraestrutura do SQL.  O SSMS é uma ferramenta usada por administradores de banco de dados para implantar, monitorar e atualizar a infraestrutura de dado.

1. [Baixar o SQL Server Management Studio](https://aka.ms/ssmsfullsetup). Depois de baixado, inicie o instalador.
2. Na página de **boas-vindas** , clique em **instalar**.
3. Na página **instalação concluída** , clique em **fechar**.
4. Inicie o SQL Server Management Studio.  
5. Na página **processo de configuração de dependência** , clique em **fechar**.

Não que o SSMS esteja instalado, você pode [se conectar e consultar um SQL Server](/sql/ssms/tutorials/connect-query-sql-server). Ao configurar a conexão, use os seguintes valores:

- Tipo de servidor: mecanismo de banco de dados
- Nome do servidor: *classlabdbserver.Database.Windows.net*
- Autenticação: autenticação de SQL Server
- Logon: *azureuser*
- Senha: senha usada para criar o banco de dados.

## <a name="cost-estimate"></a>Estimativa de custo

Vamos abordar uma possível estimativa de custo para essa classe. A estimativa não inclui o custo da execução do SQL Server.  Consulte [preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database) para obter detalhes atuais sobre o preço do banco de dados.

Usaremos uma classe de 25 alunos. Há 20 horas de tempo de classe agendada. Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado. O tamanho da máquina virtual que escolhemos era médio, que é 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custo para esta classe:

25 alunos \* (20 horas agendadas \+ 10 horas de cota) * us $0,42 USD por hora = us $315, 0

>[!IMPORTANT]
>A estimativa de custo é apenas para fins de exemplo. Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar, gerenciar e publicar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)