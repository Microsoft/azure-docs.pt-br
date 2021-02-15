---
title: Provisionar o Azure-SSIS Integration Runtime
description: Saiba como provisionar o runtime de integração do Azure SSIS no Azure Data Factory para que você possa implantar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 98d18c1cd65ccd50d120f8a9edd693f79e87787e
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555828"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionar o Azure-SSIS Integration Runtime no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este tutorial fornece etapas de como usar o portal do Azure para provisionar um Azure-SSIS IR (SQL Server Integration Services Integration Runtime) no ADF (Azure Data Factory). Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado por uma Instância Gerenciada/servidor do Banco de Dados SQL do Azure (modelo de implantação de projeto)
- Execução de pacotes implantados no sistema de arquivos, nos Arquivos do Azure ou no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote)

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas já estão habilitadas para o Azure e incluem o SSDT (SQL Server Data Tools), o SSMS (SQL Server Management Studio), além de utilitários de linha de comando como [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Para obter informações conceituais sobre IRs do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você completa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um runtime de integração do Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Servidor do banco de dados SQL do Azure (opcional)** . Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância do SSISDB neste servidor de banco de dados. 

  É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução do SSISDB sem cruzar regiões do Azure.

  Tenha estes pontos em mente:

  - Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, veja [Comparar o Banco de Dados SQL e a Instância Gerenciada de SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Se você usar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](./create-azure-ssis-integration-runtime.md).

  - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Essa configuração não se aplica quando você usa um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Para saber mais, confira [Proteger seu Banco de Dados SQL do Azure](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, veja [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../azure-sql/database/firewall-configure.md).

  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada do data factory a um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, confira [Criar Azure-SSIS IR com a autenticação do Azure AD](./create-azure-ssis-integration-runtime.md).

  - Verifique se seu servidor de banco de dados ainda não tem uma instância do SSISDB. O provisionamento do Azure-SSIS IR não dá suporte ao uso de uma instância existente do SSISDB.

> [!NOTE]
> Para obter uma lista de regiões do Azure nas quais o Data Factory e um Azure-SSIS IR estão disponíveis, confira [Disponibilidade do Data Factory + SSIS IR por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Criar uma data factory

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [Criar um data factory por meio da interface do usuário](./quickstart-create-data-factory-portal.md#create-a-data-factory). Ao fazer isso, selecione **Fixar no painel** para permitir acesso rápido após a criação do data factory. 

Depois que o data factory for criado, abra a respectiva página de visão geral na portal do Azure. Selecione o bloco **Criar e Monitorar** para abrir a página de **Introdução** em uma guia separada. Nela você pode continuar a criar seu Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS

### <a name="from-the-data-factory-overview"></a>Da visão geral do Data Factory

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**. 

   ![Bloco "Configurar o bloco do Integration Runtime SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Consulte a seção [Provisionar um runtime de integração do Azure-SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

### <a name="from-the-authoring-ui"></a>Da interface do usuário de criação

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **Conexões**. Em seguida, alterne para a guia **Integration Runtimes** para exibir os runtimes de integração existentes em seu data factory. 

   ![Seleções para exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **Novo** para criar um Azure-SSIS IR e abra o painel **Configuração do runtime de integração**. 

   ![runtime de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel **Instalação do runtime de integração**, selecione o bloco **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**.

   ![Especificar o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consulte a seção [Provisionar um runtime de integração do Azure-SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um runtime de integração do Azure-SSIS

O painel **Instalação do runtime de integração** tem três páginas nas quais você definiu sucessivamente configurações gerais, de implantação e avançadas.

### <a name="general-settings-page"></a>Página Configurações gerais

Na página **Configurações gerais** do painel **Instalação do runtime de integração**, conclua as etapas a seguir. 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Para o **Nome**, insira o nome do seu runtime de integração. 

   1. Para **Descrição**, insira a descrição do seu runtime de integração. 

   1. Para **Local**, selecione o local do seu runtime de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Tamanho do Nó**, selecione o tamanho do nó no cluster de runtime de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (escalonamento vertical) se você quiser executar muitos pacotes com uso intensivo de computação ou de memória. 

   1. Para **Número do Nó**, selecione o número de nós em seu cluster de runtime de integração. Somente os números de nós com suporte são exibidos. Selecione um cluster grande com muitos nós (escalonamento horizontal) se você deseja executar muitos pacotes em paralelo. 

   1. Para **Edição/Licença**, selecione a edição do SQL Server para seu runtime de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu runtime de integração. 

   1. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure para o runtime de integração: **Sim** ou **Não**. Selecione **Sim** se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido. 

   1. Selecione **Avançar**. 

### <a name="deployment-settings-page"></a>Página Configurações de implantação

Na página **Configurações da implantação** do painel **Instalação do Runtime de Integração**, você tem as opções para criar o SSISDB e/ou os armazenamentos de pacotes do Azure-SSIS IR.

#### <a name="creating-ssisdb"></a>Criar SSISDB

Na página **Configurações da implantação** do painel **Instalação do Runtime de Integração**, se você quiser implantar seus pacotes no SSISDB (modelo de implantação de projeto), marque a caixa de seleção **Criar catálogo do SSIS (SSISDB) hospedado pelo servidor do Banco de Dados SQL do Azure ou pela Instância Gerenciada para armazenar seus projetos/pacotes/ambientes/logs de execução**. Como alternativa, se você quiser implantar seus pacotes no sistema de arquivos, nos Arquivos do Azure ou no banco de dados SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote), não será necessário criar o SSISDB nem marcar a caixa de seleção.

Independentemente do seu modelo de implantação, se desejar usar o SQL Server Agent hospedado pela Instância Gerenciada de SQL do Azure para orquestrar/agendar as execuções de pacote, isso será habilitado pelo SSISDB, portanto marque a caixa de seleção. Para obter mais informações, consulte [Agendar execuções de pacote SSIS por meio do Agente de Instância Gerenciada de SQL do Azure](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Se marcar a caixa de seleção, conclua as etapas a seguir para trazer seu próprio servidor de banco de dados para hospedar o SSISDB que criaremos e gerenciaremos em seu nome.

   ![Configurações de implantação para SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu runtime de integração.

   1. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. 
   
      Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, veja [Comparar o Banco de Dados SQL e a Instância Gerenciada de SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se você selecionar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](./create-azure-ssis-integration-runtime.md).

   1. Marque a caixa de seleção **Usar autenticação do Azure AD com a identidade gerenciada para o seu ADF** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory.

      Se selecionar a caixa de seleção, você precisará adicionar a identidade gerenciada do data factory em um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, confira [Criar Azure-SSIS IR com a autenticação do Azure AD](./create-azure-ssis-integration-runtime.md).
   
   1. Para **Nome de Usuário do Administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Senha do Administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Camada de Serviço de Banco de Dados de Catálogo**, selecione a camada de serviço para o seu servidor de banco de dados na qual hospedar o SSISDB. Selecione a camada Básica, Standard ou Premium ou selecione um nome de pool elástico.

Selecione **Testar conectividade** quando aplicável e, se tiver êxito, selecione **Avançar**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Criação do repositórios de pacotes do Azure-SSIS IR

Na página **Configurações da implantação** do painel **Instalação do Runtime de Integração**, se você quiser gerenciar seus pacotes que são implantados no MSDB, no sistema de arquivos ou nos Arquivos do Azure (modelo de implantação de pacote) com repositórios de pacotes do Azure-SSIS IR, marque a caixa de seleção **Criar repositórios de pacotes para gerenciar seus pacotes implantados no sistema de arquivos/nos Arquivos do Azure, no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure**.
   
O repositório de pacotes do Azure-SSIS IR permite que você importe/exporte/exclua/execute pacotes e monitore/interrompa a execução de pacotes por meio do SSMS de modo semelhante ao [repositório de pacotes SSIS herdado](/sql/integration-services/service/package-management-ssis-service). Para obter mais informações, consulte [Gerenciar pacotes SSIS com os repositórios de pacotes Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Se você marcar essa caixa de seleção, poderá adicionar vários repositórios de pacotes ao seu Azure-SSIS IR selecionando **Novo**. Por outro lado, um repositório de pacotes pode ser compartilhado por vários Azure-SSIS IRs.

![Configurações de implantação para MSDB/sistema de arquivos/Arquivos do Azure](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

No painel **Adicionar repositório de pacotes**, conclua as seguintes etapas.
   
   1. Para **Nome do repositório de pacotes**, insira o nome do seu repositório de pacotes. 

   1. Para **Serviço vinculado do repositório de pacotes**, selecione o serviço vinculado existente que armazena as informações de acesso do sistema de arquivos/Arquivos do Azure/Instância Gerenciada de SQL do Azure em que os pacotes são implantados ou crie um selecionando **Novo**. No painel **Novo serviço vinculado**, complete as seguintes etapas. 

      > [!NOTE]
      > É possível usar o **Armazenamento de Arquivos do Azure** ou os serviços vinculados do **Sistema de Arquivos** para acessar os Arquivos do Azure. Caso use o serviço vinculado do **Armazenamento de Arquivos do Azure**, o repositório de pacotes do Azure-SSIS IR será compatível somente com o método de autenticação **Básica** (não com a **Chave de conta** nem com o **URI de SAS**) por enquanto. Para usar a autenticação **Básica** no serviço vinculado do **Armazenamento de Arquivos do Azure** é possível acrescentar `?feature.upgradeAzureFileStorage=false` à URL do portal do ADF em seu navegador. Como alternativa, você pode usar o serviço vinculado do **Sistema de Arquivos** para acessar os Arquivos do Azure. 

      ![Configurações de implantação para serviços vinculados](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Para **Nome**, insira o nome do seu serviço vinculado. 
         
      1. Para **Descrição**, insira a descrição do seu serviço vinculado. 
         
      1. Para **Tipo**, selecione **Armazenamento de Arquivos do Azure**, **Instância Gerenciada de SQL do Azure** ou **Sistema de Arquivos**.

      1. Você pode ignorar **Conectar via runtime de integração**, pois sempre usamos seu Azure-SSIS IR para buscar as informações de acesso para os repositórios de pacotes.

      1. Se você selecionar **Armazenamento de Arquivos do Azure**, conclua as etapas a seguir. 

         1. Para **Método de seleção de conta**, selecione **Da assinatura do Azure** ou **Inserir manualmente**.
         
         1. Se você selecionar **Da assinatura do Azure**, selecione a **Assinatura do Azure** relevante, o **Nome da conta de armazenamento** e o **Compartilhamento de arquivo**.
            
         1. Se você selecionar **Inserir manualmente**, insira `\\<storage account name>.file.core.windows.net\<file share name>` para **Host**, `Azure\<storage account name>` para **Nome de usuário** e `<storage account key>` para **Senha** ou selecione **Azure Key Vault** no local em que ele está armazenado como um segredo.

      1. Se você selecionar **Instância Gerenciada de SQL do Azure**, conclua as etapas a seguir. 

         1. Selecione **Cadeia de conexão** para inserção manual ou **Azure Key Vault** no local em que ela está armazenada como um segredo.
         
         1. Se você selecionar **Cadeia de conexão**, conclua as etapas a seguir. 

            1. Para **Nome de domínio totalmente qualificado**, insira `<server name>.<dns prefix>.database.windows.net` ou `<server name>.public.<dns prefix>.database.windows.net,3342` como o ponto de extremidade público ou privado de sua Instância Gerenciada de SQL do Azure, respectivamente. Se você inserir o ponto de extremidade privado, **Testar conectividade** não será aplicável, já que a interface do usuário do ADF não conseguirá acessá-lo.

            1. Para **Nome do banco de dados**, insira `msdb`.
               
            1. Para **Tipo de autenticação**, selecione **Autenticação do SQL**, **Identidade Gerenciada** ou **Entidade de Serviço**.

            1. Se você selecionar **Autenticação do SQL**, insira o **Nome de usuário** relevante e a **Senha** ou selecione o **Azure Key Vault** no local em que ele está armazenado como um segredo.

            1. Se você selecionar **Identidade Gerenciada**, conceda à identidade gerenciada do ADF acesso à sua Instância Gerenciada de SQL do Azure.

            1. Se você selecionar **Entidade de Serviço**, insira a **ID da entidade de serviço** relevante e a **Chave da entidade de serviço** ou selecione o **Azure Key Vault** no local em que ele está armazenado como um segredo.

      1. Se você selecionar **Sistema de arquivos**, insira o caminho UNC da pasta na qual os pacotes estão implantados para **Host**, bem como o **Nome de usuário** relevante e a **Senha** ou selecione seu **Azure Key Vault** no local em que ele está armazenado como um segredo.

      1. Selecione **Testar conectividade** quando aplicável e, se tiver êxito, selecione **Criar**.

   1. Os repositórios de pacotes adicionados serão exibidos na página **Configurações de implantação**. Para removê-los, marque suas caixas de seleção e, em seguida, selecione **Excluir**.

Selecione **Testar conectividade** quando aplicável e, se tiver êxito, selecione **Avançar**.

### <a name="advanced-settings-page"></a>Página Configurações avançadas

Na página **Configurações avançadas** do painel **Instalação do runtime de integração**, conclua as etapas a seguir. 

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **Execuções Paralelas Máximas por Nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster do runtime de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande que tenha uso intensivo de computação ou de memória. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos em um único núcleo. 

   1. Marque a caixa de seleção **Personalizar seu Azure-SSIS Integration Runtime com configurações de sistema adicionais/instalações de componentes** para escolher se deseja adicionar as configurações personalizadas padrão/expressas no Azure-SSIS IR. Para obter mais informações, confira [Configuração personalizada para um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Marque a caixa de seleção **Selecionar uma VNet para seu Azure-SSIS Integration Runtime para ingressar e permitir que o Data Factory crie determinados recursos de rede e, opcionalmente, traga seus próprios endereços IP públicos estáticos** para escolher se deseja ingressar o Azure-SSIS IR em uma rede virtual.

      Selecione se você usar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar um IR auto-hospedado. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual](./create-azure-ssis-integration-runtime.md). 
   
   1. Marque a caixa de seleção **Configurar o runtime de integração auto-hospedada como um proxy para o Azure-SSIS Integration Runtime** para escolher se você deseja ou não configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR. Para obter mais informações, veja [Configurar um IR auto-hospedado como proxy](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Selecione **Continuar**. 

Na página **Resumo** do painel **Instalação do runtime de integração**, examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **Concluir** para iniciar a criação do runtime de integração. 

   > [!NOTE]
   > Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos.
   >
   > Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. 
   > 
   > Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter mais informações sobre os componentes internos/pré-instalados, consulte [Componentes internos/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para mais informações sobre componentes adicionais que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Painel Conexões

No painel **Conexões** do hub **Gerenciar**, mude para a página **Runtimes de integração** e selecione **Atualizar**. 

   ![Painel Conexões](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Você pode editar/reconfigurar seu Azure-SSIS IR selecionando seu nome. Você também pode selecionar os botões relevantes para monitorar/iniciar/parar/excluir seu Azure-SSIS IR, gerar automaticamente um pipeline do ADF com a atividade Executar Pacote do SSIS para execução em seu Azure-SSIS IR e exibir o código/conteúdo JSON de seu Azure-SSIS IR.  Editar/excluir seu Azure-SSIS IR só pode ser feito quando for ele estiver interrompido.

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS

Se usar o SSISDB, você poderá implantar seus pacotes nele e executá-los no Azure-SSIS IR usando as SSDT habilitadas para Azure ou as ferramentas do SSMS. Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor do Banco de Dados SQL do Azure, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se não usar o SSISDB, você poderá implantar os pacotes no sistema de arquivos, nos Arquivos do Azure ou no MSDB hospedado pela Instância Gerenciada de SQL do Azure e executá-los no Azure-SSIS IR usando os utilitários de linha de comando [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Para saber mais, confira [Implantar projetos/pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Nos dois casos, você também pode executar os pacotes implantados no Azure-SSIS IR usando a atividade Executar Pacote SSIS em pipelines do Data Factory. Para obter mais informações, confira [Invocar execução do pacote SSIS como uma atividade de Data Factory de primeira classe](./how-to-invoke-ssis-package-ssis-activity.md).

Confira também a documentação do SSIS a seguir: 

- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar a execução de pacotes no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o artigo a seguir: 

> [!div class="nextstepaction"]
> [Personalizar um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)