---
title: Provisionar o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Saiba como provisionar o tempo de execução de integração do Azure SSIS no Azure Data Factory para que você possa implantar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484810"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Integration Runtime do Azure-SSIS no Azure Data Factory

Este tutorial fornece etapas de como usar o portal do Azure para provisionar um Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) no Azure Data Factory (ADF). Azure-SSIS IR oferece suporte à execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado pelo servidor do Banco de Dados SQL do Azure/Instância Gerenciada (modelo de implantação de projeto) e aqueles implantados em sistemas de arquivo/compartilhamentos de arquivos/Arquivos do Azure (modelo de implantação de pacote). Depois que o Azure-SSIS IR é provisionado, você pode usar ferramentas familiares, como o SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e os utilitários de linha de comando, como `dtinstall`/`dtutil`/`dtexec` para Implantar e executar os pacotes no Azure. Para obter informações conceituais sobre IRs do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você completa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Servidor do banco de dados SQL do Azure (opcional)** . Se ainda não tiver um servidor de banco de dados, crie um no portal do Azure antes de começar. O ADF criará o SSISDB neste servidor de banco de dados. É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução do SSISDB sem cruzar regiões do Azure. 
    - Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um banco de dados individual, parte de um pool elástico ou em uma Instância Gerenciada e acessível na rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, confira [Comparar bancos de dados individuais/pools elásticos do Banco de Dados SQL do Azure/Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você usa o servidor de Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o SSISDB ou exigir acesso a dados locais, é necessário associar o seu Azure-SSIS IR a uma rede virtual. Confira [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Isso não se aplica ao usar o servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviços de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o SSISDB. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, veja [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md).
    - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou autenticação do AAD (Azure Active Directory) com a identidade gerenciada para seu ADF. Para o último, você precisa adicionar o ADF a um grupo do AAD com permissões de acesso para o servidor de banco de dados. Confira [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme se seu servidor de banco de dados ainda não tem um SSISDB. O provisionamento do Azure-SSIS IR não oferece suporte ao uso de um SSISDB existente.

> [!NOTE]
> - Para obter uma lista de regiões do Azure nas quais o ADF e o Azure-SSIS IR estão disponíveis, confira [Disponibilidade do ADF + SSIS IR por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome. 
1. Entre no [Portal do Azure](https://portal.azure.com/). 
1. Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**. 

   ![Seleção de Data Factory no painel "Novo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** em **Nome**. 

   ![Página de "Novo data factory"](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, **&lt;yourname&gt;MyAzureSsisDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
1. Para **Grupo de Recursos**, realize uma das seguintes etapas: 

   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos. 

   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
1. Para **Versão**, selecione **V2 (Versão prévia)** . 
1. Em **Local**, selecione uma localização para o data factory. A lista mostra somente os locais com suporte para a criação de data factories. 
1. Selecione **Fixar no painel**. 
1. Selecione **Criar**. 
1. No painel, o bloco com o seguinte status é exibido **Implantando o Data Factory**: 

   ![Bloco "Implantando Data Factory"](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Após a criação, a página do **Data Factory** será exibida. 

   ![Home page do data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Selecione **Gerenciar e Monitorar** para abrir a interface do usuário (IU) do Data Factory em uma guia separada. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Da visão geral do Data Factory

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**. 

   ![Bloco "Configurar o bloco do Integration Runtime SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

### <a name="from-the-authoring-ui"></a>Da interface do usuário de criação

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar**, selecione **Conexões** e, em seguida, alterne para a guia **Tempos de Execução de Integração** para exibir os tempos de execução de integração existentes no data factory. 

   ![Seleções para exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um novo IR do Azure SSIS. 

   ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Na janela **Instalação do Integration Runtime**, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**. 

   ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure-SSIS

1. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, execute estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Para **Name**, insira o nome do seu tempo de execução de integração. 

   b. Para **Descrição**, insira a descrição do seu tempo de execução de integração. 

   c. Para **Local**, selecione o local do seu tempo de execução de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB. 

   d. Para **Tamanho do Nó**, selecione o tamanho do nó no cluster de tempo de execução de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (scale up), se você quiser executar muitos pacotes de computação / memória intensivos. 

   e. Para **Número do Nó**, selecione o número de nós em seu cluster de tempo de execução de integração. Somente os números de nós com suporte são exibidos. Selecione um grande cluster com muitos nós (scale out), se você deseja executar muitos pacotes em paralelo. 

   f. Para **Edition/License**, selecione a edição/licença do SQL Server para seu tempo de execução de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu tempo de execução de integração. 

   g. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure (AHB) para o tempo de execução de integração: Sim ou Não. Selecione Sim se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido. 

   h. Clique em **Próximo**. 

1. Na página **Configurações de SQL**, execute as seguintes etapas: 

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Na caixa de seleção **Criar catálogo SSIS...** , selecione o modelo de implantação dos pacotes a serem executados no Azure-SSIS IR: O Modelo de Implantação de Projetos em que os pacotes são implantados no SSISDB hospedado pelo servidor de banco de dados ou o Modelo de Implantação de Pacotes em que os pacotes são implantados em seus sistemas de arquivos/compartilhamentos de arquivos/Arquivos do Azure. Se você fizer isso, precisará trazer seu próprio servidor de banco de dados para hospedar o SSISDB que criaremos e gerenciaremos em seu nome.
   
   b. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

   c. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu tempo de execução de integração. 

   d. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um banco de dados individual, parte de um pool elástico ou em uma Instância Gerenciada e acessível na rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, confira [Comparar bancos de dados individuais/pools elásticos do Banco de Dados SQL do Azure/Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se você selecionar o servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviço de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o SSISDB ou exigir acesso a dados locais, será necessário associar o seu Azure-SSIS IR a uma rede virtual, confira [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Na caixa de seleção **Usar autenticação do AAD...** , selecione o método de autenticação para o servidor de banco de dados para hospedar o SSISDB: Autenticação do SQL ou a autenticação do AAD com a identidade gerenciada para o ADF. Se marcá-la, você precisará adicionar a identidade gerenciada do seu ADF a um grupo do AAD com permissões de acesso para o servidor de banco de dados. Confira [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Para **Nome de usuário do administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   g. Para **Nome de usuário do administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   h. Para **Nível do Serviço de Banco de Dados de Catálogo**, selecione a camada de serviço para o seu servidor de banco de dados hospedar SSISDB: Tipo Basic/Standard/Premium ou o nome do pool elástico. 

   i. Clique em **Conexão de teste** e, se tiver êxito, clique em **Próximo**. 

1. Na página **Configurações avançadas**, conclua as etapas a seguir: 

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Execuções paralelas máximas por nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster de tempo de execução de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande/ativado que seja de computação/memória intensiva. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos / leves em um único núcleo. 

   b. Para o  **Custom Storage Container SAS URI**, insira opcionalmente o Uniform Resource Identifier (URI) de Assinatura de Acesso Compartilhado (SAS) do seu contêiner Azob Storage Blob onde seu script de instalação e seus arquivos associados estão armazenados, consulte [ Configuração personalizada para o Azure-SSIS IR ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Na caixa de seleção **Selecionar uma rede virtual...** , selecione se você deseja ingressar o tempo de execução de integração para uma rede virtual. Você deve verificar isso se usar o servidor do Banco de Dados SQL do Azure com pontos de extremidade de serviços de rede virtual/Instância Gerenciada em uma rede virtual para hospedar o SSISDB ou exigir acesso a dados locais, confira [Criar o Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Clique em **Concluir** para iniciar a criação do tempo de execução de integração. 

   > [!NOTE]
   > Excluindo qualquer tempo de instalação personalizada, este processo deve ser concluído em cinco minutos.
   >
   > Se você usa o SSISDB, o serviço ADF conecta-se ao seu servidor de banco de dados para prepará-lo. 
   > 
   > Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel/Access e para várias fontes de dados do Azure, além das fontes de dados que já têm suporte dos componentes internos. Também é possível instalar componentes adicionais. Confira [Instalação personalizada para Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na guia **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Selecione **Atualizar** para atualizar o status. 

   ![Status de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

   ![Links na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS

Se você usa o SSISDB, é possível implantar seus pacotes nele e executá-lo no Azure-SSIS IR usando ferramentas SSDT/SSMS que conectam seu servidor de banco de dados por meio do ponto de extremidade do servidor. Para servidor do Banco de dados SQL do Azure/Instância Gerenciada com um ponto de extremidade público, o formato do ponto de extremidade do servidor é `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente. Se você não usa o SSISDB, é possível implantar seus pacotes em sistemas de arquivos/compartilhamentos de arquivos/Arquivos do Azure e executá-los no Azure-SSIS IR com os utilitários de linha de comando `dtinstall`/`dtutil`/`dtexec`. Para saber mais informações, confira [Implantar pacotes SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, você também pode executar pacotes implantados no Azure-SSIS IR usando a atividade executar Pacote do SSIS em pipelines ADF. Confira [Invocar a execução do pacote SSIS como uma atividade ADF de primeira classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Confira também os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar a execução de pacotes no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um tempo de execução de integração do Azure-SSIS.
> * Implantar pacotes do SSIS

Para saber mais sobre como personalizar o tempo de execução de integração do Azure-SSIS, avance para o artigo a seguir: 

> [!div class="nextstepaction"]
> [Personalizar o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)