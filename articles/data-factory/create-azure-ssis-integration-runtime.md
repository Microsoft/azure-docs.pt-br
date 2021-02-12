---
title: Criar um Integration Runtime do Azure-SSIS no Azure Data Factory
description: Aprenda a criar um runtime de integração do Azure-SSIS no Azure Data Factory para poder implantar e executar pacotes do SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 62b1575e2ab379e6b4e61926e00dfad85ffeb6c0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556351"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Integration Runtime do Azure-SSIS no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece etapas para provisionar um tempo de execução de integração (IR) do Azure-SQL Server Integration Services (SSIS) no Azure Data Factory (ADF). Um Azure-SSIS IR dá suporte a:

- Execução de pacotes implantados no catálogo do SSIS (SSISDB) hospedado por uma Instância Gerenciada/servidor do Banco de Dados SQL do Azure (modelo de implantação de projeto)
- Execução de pacotes implantados no sistema de arquivos, nos Arquivos do Azure ou no banco de dados do SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote)

Depois que um Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares para implantar e executar seus pacotes no Azure. Essas ferramentas já estão habilitadas para o Azure e incluem o SSDT (SQL Server Data Tools), o SSMS (SQL Server Management Studio), além de utilitários de linha de comando como [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

O tutorial de [Azure-SSIS ir de provisionamento](./tutorial-deploy-ssis-packages-azure.md) mostra como criar um Azure-SSIS ir por meio do portal do Azure ou do aplicativo Data Factory. O tutorial também mostra como usar opcionalmente um servidor de banco de dados SQL do Azure ou uma instância gerenciada para hospedar o SSISDB. Este artigo expande o tutorial e descreve como executar essas tarefas opcionais:

- Use um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB. Como pré-requisito, você precisa configurar as permissões e as configurações de rede virtual para seu Azure-SSIS IR para ingressar em uma rede virtual.

- Use a autenticação do Azure Active Directory (AD do Azure) com a identidade gerenciada para seu data factory para se conectar a um servidor de banco de dados SQL do Azure ou a uma instância gerenciada. Como pré-requisito, você precisa adicionar a identidade gerenciada para seu data factory como um usuário de banco de dados que pode criar uma instância do SSISDB.

- Ingresse seu Azure-SSIS IR em uma rede virtual ou configure um IR auto-hospedado como proxy para o Azure-SSIS IR acessar dados no local.

Este artigo mostra como provisionar um Azure-SSIS IR usando o portal do Azure, Azure PowerShell e um modelo de Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você ainda não tiver uma assinatura, poderá criar uma conta de [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) .

- **Servidor de banco de dados SQL do Azure ou sql instância gerenciada (opcional)**. Se você ainda não tiver um servidor de banco de dados ou uma instância gerenciada, crie um no portal do Azure antes de começar. O Data Factory, por sua vez, criará uma instância do SSISDB neste servidor de banco de dados. 

  Recomendamos que você crie o servidor de banco de dados ou instância gerenciada na mesma região do Azure que o Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução do SSISDB sem cruzar regiões do Azure.

  Tenha estes pontos em mente:

  - A instância SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientação sobre como escolher entre o banco de dados SQL e o SQL Instância Gerenciada para hospedar o SSISDB, consulte a seção [comparar banco de dados SQL e sql instância gerenciada](#comparison-of-sql-database-and-sql-managed-instance) neste artigo. 
  
    Se você usar um servidor de banco de dados SQL do Azure com regras de firewall IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada do SQL com um ponto de extremidade privado para hospedar o SSISDB ou se precisar de acesso a dados locais sem configurar um IR auto-hospedado, será necessário ingressar seu Azure-SSIS IR em uma rede virtual. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Essa configuração não é aplicável quando você usa um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada do SQL com ponto de extremidade privado para hospedar o SSISDB. Para saber mais, confira [Proteger seu Banco de Dados SQL do Azure](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Para habilitar essa configuração usando o PowerShell, veja [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../azure-sql/database/firewall-configure.md).

  - Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou usando a autenticação do Azure AD com a identidade gerenciada para seu data factory. Para o último, você precisa adicionar a identidade gerenciada do data factory a um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, consulte [habilitar a autenticação do Azure ad para um Azure-SSIS ir](./enable-aad-authentication-azure-ssis-ir.md).

  - Verifique se seu servidor de banco de dados ainda não tem uma instância do SSISDB. O provisionamento do Azure-SSIS IR não dá suporte ao uso de uma instância existente do SSISDB.

- **Rede virtual do Azure Resource Manager (opcional)**. Você precisa ter uma rede virtual do Azure Resource Manager se pelo menos uma das seguintes condições for verdadeira:

  - Você está hospedando o SSISDB em um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado.

  - Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS em execução no seu Azure-SSIS IR sem configurar um IR via hospedagem interna.

- **Azure PowerShell (opcional)**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps), caso queira executar um script do PowerShell para provisionar o seu Azure-SSIS IR.

### <a name="regional-support"></a>Suporte regional

Para obter uma lista de regiões do Azure nas quais Data Factory e um Azure-SSIS IR estão disponíveis, consulte [Data Factory e a disponibilidade de ir do SSIS por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Comparação entre o banco de dados SQL e o SQL Instância Gerenciada

A tabela a seguir compara determinados recursos de um servidor de banco de dados SQL do Azure e o SQL Instância Gerenciada como eles se relacionam com o IR do Azure-SSIR:

| Recurso | Banco de Dados SQL| Instância gerenciada do SQL |
|---------|--------------|------------------|
| **Agendamento** | O SQL Server Agent não está disponível.<br/><br/>Consulte [agendar uma execução de pacote em um pipeline de data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| O agente de Instância Gerenciada está disponível. |
| **Autenticação** | Você pode criar uma instância SSISDB com um usuário de banco de dados independente que representa qualquer grupo do Azure AD com a identidade gerenciada do seu data factory como um membro na função **db_owner** .<br/><br/>Consulte [habilitar a autenticação do Azure ad para criar um SSISDB no servidor do banco de dados SQL do Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Você pode criar uma instância SSISDB com um usuário de banco de dados independente que representa a identidade gerenciada do seu data factory. <br/><br/>Consulte [habilitar a autenticação do Azure ad para criar um SSISDB no Azure SQL instância gerenciada](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Camada de serviço** | Ao criar um Azure-SSIS IR com o servidor de banco de dados SQL do Azure, você pode selecionar a camada de serviço para SSISDB. Há várias camadas de serviço. | Quando você cria um Azure-SSIS IR com sua instância gerenciada, não é possível selecionar a camada de serviço para SSISDB. Todos os bancos de dados em sua instância gerenciada compartilham o mesmo recurso alocado para essa instância. |
| **Rede virtual** | Seu Azure-SSIS IR poderá ingressar em uma rede virtual Azure Resource Manager se você usar um servidor de banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual. | Seu Azure-SSIS IR poderá ingressar em uma rede virtual Azure Resource Manager se você usar uma instância gerenciada com um ponto de extremidade privado. A rede virtual é necessária quando você não habilita um ponto de extremidade público para sua instância gerenciada.<br/><br/>Se você ingressar seu Azure-SSIS IR para a mesma rede virtual que sua instância gerenciada, verifique se o Azure-SSIS IR está em uma sub-rede diferente da instância gerenciada. Se você ingressar seu Azure-SSIS IR em uma rede virtual diferente de sua instância gerenciada, recomendamos um emparelhamento de rede virtual ou uma conexão de rede para rede. Consulte [conectar seu aplicativo a um instância gerenciada do banco de dados SQL do Azure](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transações distribuídas** | Esse recurso é suportado por meio de transações elásticas. As transações do Microsoft Distributed Transaction Coordinator (MSDTC) não possuem suporte. Se seus pacotes SSIS usam o MSDTC para coordenar transações distribuídas, considere migrar para transações elásticas para o banco de dados SQL do Azure. Para obter mais informações, consulte [transações distribuídas entre bancos de dados de nuvem](../azure-sql/database/elastic-transactions-overview.md). | Sem suporte. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Usar o portal do Azure para criar um Integration Runtime

Nesta seção, você usa o portal do Azure, especificamente a interface do usuário ou o aplicativo Data Factory, para criar um Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Criar uma data factory

Para criar seu data factory por meio do portal do Azure, siga as instruções passo a passo em [Criar um data factory por meio da interface do usuário](./quickstart-create-data-factory-portal.md#create-a-data-factory). Ao fazer isso, selecione **Fixar no painel** para permitir acesso rápido após a criação do data factory. 

Depois que o data factory for criado, abra a respectiva página de visão geral na portal do Azure. Selecione o bloco **criar & monitor** para abrir a página **vamos começar** em uma guia separada. Lá, você pode continuar a criar seu Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um runtime de integração do Azure-SSIS

Na página **introdução** , selecione o bloco **configurar o SSIS Integration Runtime** para abrir o painel configuração do **Integration Runtime** .

   ![Configurar o bloco do Integration Runtime SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   O painel **Instalação do runtime de integração** tem três páginas nas quais você definiu sucessivamente configurações gerais, de implantação e avançadas.

#### <a name="general-settings-page"></a>Página Configurações gerais

Na página **Configurações gerais** do painel **Instalação do runtime de integração**, conclua as etapas a seguir.

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Para o **Nome**, insira o nome do seu runtime de integração.

   2. Para **Descrição**, insira a descrição do seu runtime de integração.

   3. Para **Local**, selecione o local do seu runtime de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB.

   4. Para **tamanho do nó**, selecione o tamanho do nó em seu cluster do Integration Runtime. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (escalonamento vertical) se você quiser executar muitos pacotes com uso intensivo de computação ou de memória.
   > [!NOTE]
   > Se você precisar de [isolamento de computação](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), selecione o **Standard_E64i_v3** tamanho do nó. Esse tamanho de nó representa as máquinas virtuais isoladas que consomem todo o host físico e fornecem o nível necessário de isolamento exigido por determinadas cargas de trabalho, como as cargas de trabalho de impacto 5 (IL5) do departamento de defesa dos EUA.
   
   5. Para **Número do Nó**, selecione o número de nós em seu cluster de runtime de integração. Somente os números de nós com suporte são exibidos. Selecione um cluster grande com muitos nós (escalonamento horizontal) se você deseja executar muitos pacotes em paralelo.

   6. Para **Edição/Licença**, selecione a edição do SQL Server para seu runtime de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu runtime de integração.

   7. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure para o runtime de integração: **Sim** ou **Não**. Selecione **Sim** se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido.

   8. Selecione **Avançar**.

#### <a name="deployment-settings-page"></a>Página Configurações de implantação

Na página **Configurações da implantação** do painel **Instalação do Runtime de Integração**, você tem as opções para criar o SSISDB e/ou os armazenamentos de pacotes do Azure-SSIS IR.

##### <a name="creating-ssisdb"></a>Criar SSISDB

Na página **Configurações da implantação** do painel **Instalação do Runtime de Integração**, se você quiser implantar seus pacotes no SSISDB (modelo de implantação de projeto), marque a caixa de seleção **Criar catálogo do SSIS (SSISDB) hospedado pelo servidor do Banco de Dados SQL do Azure ou pela Instância Gerenciada para armazenar seus projetos/pacotes/ambientes/logs de execução**. Como alternativa, se você quiser implantar seus pacotes no sistema de arquivos, nos Arquivos do Azure ou no banco de dados SQL Server (MSDB) hospedado pela Instância Gerenciada de SQL do Azure (modelo de implantação de pacote), não será necessário criar o SSISDB nem marcar a caixa de seleção.

Independentemente do seu modelo de implantação, se desejar usar o SQL Server Agent hospedado pela Instância Gerenciada de SQL do Azure para orquestrar/agendar as execuções de pacote, isso será habilitado pelo SSISDB, portanto marque a caixa de seleção. Para obter mais informações, consulte [Agendar execuções de pacote SSIS por meio do Agente de Instância Gerenciada de SQL do Azure](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Se marcar a caixa de seleção, conclua as etapas a seguir para trazer seu próprio servidor de banco de dados para hospedar o SSISDB que criaremos e gerenciaremos em seu nome.

   ![Configurações de implantação para SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu runtime de integração.

   1. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. 
   
      Com base no servidor de banco de dados selecionado, a instância do SSISDB pode ser criada em seu nome como um banco de dados individual, como parte de um pool elástico ou em uma instância gerenciada. Ela pode ser acessível em uma rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, veja [Comparar o Banco de Dados SQL e a Instância Gerenciada de SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Se você selecionar um servidor do Banco de Dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com ponto de extremidade privado para hospedar o SSISDB ou se você exigir acesso a dados locais sem configurar o IR auto-hospedado, precisará associar o Azure-SSIS IR a uma rede virtual. Para obter mais informações, confira [Criar o Azure-SSIS IR em uma rede virtual]().

   1. Marque a caixa de seleção **Usar autenticação do Azure AD com a identidade gerenciada para o seu ADF** para escolher o método de autenticação para o servidor de banco de dados para hospedar o SSISDB. Você escolherá a autenticação do SQL ou a autenticação do Azure AD com a identidade gerenciada para seu data factory.

      Se selecionar a caixa de seleção, você precisará adicionar a identidade gerenciada do data factory em um grupo do Azure AD com permissões de acesso para o servidor de banco de dados. Para obter mais informações, confira [Criar Azure-SSIS IR com a autenticação do Azure AD]().
   
   1. Para **Nome de Usuário do Administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Senha do Administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   1. Para **Camada de Serviço de Banco de Dados de Catálogo**, selecione a camada de serviço para o seu servidor de banco de dados na qual hospedar o SSISDB. Selecione a camada Básica, Standard ou Premium ou selecione um nome de pool elástico.

Selecione **Testar conectividade** quando aplicável e, se tiver êxito, selecione **Avançar**.

> [!NOTE]
   > Se você usar o servidor de banco de dados SQL do Azure para hospedar o SSISDB, seus dados serão armazenados em armazenamento com redundância geográfica para backups por padrão. Se você não quiser que seus dados sejam replicados em outras regiões, siga as instruções para [Configurar a redundância de armazenamento de backup usando o PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Criação do repositórios de pacotes do Azure-SSIS IR

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

#### <a name="advanced-settings-page"></a>Página Configurações avançadas

Na página **Configurações avançadas** do painel **Instalação do runtime de integração**, conclua as etapas a seguir.

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Para **Execuções Paralelas Máximas por Nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster do runtime de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande que tenha uso intensivo de computação ou de memória. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos em um único núcleo.

   1. Marque a caixa de seleção **Personalizar seu Azure-SSIS Integration Runtime com configurações de sistema adicionais/instalações de componentes** para escolher se deseja adicionar as configurações personalizadas padrão/expressas no Azure-SSIS IR. Para obter mais informações, confira [Configuração personalizada para um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Configurações avançadas com configurações personalizadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Para o **URI de SAS do contêiner de instalação personalizada**, insira o URI de SAS do seu contêiner em que você armazena scripts e arquivos associados para configurações personalizadas padrão.

      1. Para **a instalação personalizada expressa**, selecione **novo** para abrir o painel **Adicionar configuração personalizada expressa** e, em seguida, selecione qualquer tipo no menu suspenso **tipo de instalação personalizada expressa** , por exemplo, **Execute o comando cmdkey**, **adicione a variável de ambiente**, **Instale o componente licenciado**, etc.

         Se você selecionar o tipo de **componente de instalação licenciado** , poderá selecionar todos os componentes integrados de nossos parceiros ISV no menu suspenso **nome do componente** e, se necessário, inserir a chave de licença do produto/carregar o arquivo de licença do produto que você comprou neles na caixa arquivo de licença de chave de **licença** /  .
  
         Suas configurações personalizadas expressas adicionais serão exibidas na página de **Configuração avançada** . Para removê-los, você pode selecionar suas caixas de seleção e, em seguida, selecionar **excluir**.

   1. Selecione a **opção Selecionar uma VNet para sua Azure-SSIS Integration Runtime para ingressar, permitir que o ADF crie determinados recursos de rede e, opcionalmente, traga seus próprios endereços IP públicos estáticos** para escolher se deseja unir seu tempo de execução de integração a uma rede virtual. 

      Selecione-o se você usar um servidor de banco de dados SQL do Azure com as regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB ou se precisar de acesso a dados locais (ou seja, se você tiver fontes de dados locais ou destinos em seus pacotes SSIS) sem configurar um IR via hospedagem interna. Para obter mais informações, consulte [unir Azure-SSIS ir a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Para **Assinatura**, selecione a assinatura do Azure que possui sua rede virtual.

      1. Para **Local**, o mesmo local do seu runtime de integração é selecionado.

      1. Para **tipo**, selecione o tipo de sua rede virtual: clássica ou Azure Resource Manager. Recomendamos que você selecione um Azure Resource Manager rede virtual, pois as redes virtuais clássicas serão preteridas em breve.

      1. Para **Nome da VNet**, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada com ponto de extremidades privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Para **Nome da sub-rede**, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

      1. Marque a caixa de seleção **colocar endereços IP públicos estáticos para seu Azure-SSIS Integration Runtime** para escolher se deseja trazer seus próprios endereços IP públicos estáticos para Azure-SSIS ir, para que você possa permiti-los no firewall para suas fontes de dados.

         Se você marcar a caixa de seleção, conclua as etapas a seguir.

         1. Para o **primeiro endereço IP público estático**, selecione o primeiro endereço IP público estático que atende aos requisitos para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.
      
         1. Para o **segundo endereço IP público estático**, selecione o segundo endereço IP público estático que atende aos requisitos para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.

   1. Marque a caixa de seleção **Configurar o runtime de integração auto-hospedada como um proxy para o Azure-SSIS Integration Runtime** para escolher se você deseja ou não configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR. Para obter mais informações, veja [Configurar um IR auto-hospedado como proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      ![Configurações avançadas com um IR auto-hospedado](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Para **Integration Runtime auto-hospedados**, selecione o ir auto-hospedado existente como um proxy para Azure-SSIS ir.

      1. Para o **serviço vinculado de armazenamento de preparo**, selecione o serviço vinculado do armazenamento de BLOBs do Azure existente ou crie um novo para preparo.

      1. Para **caminho de preparo**, especifique um contêiner de BLOB em sua conta de armazenamento de BLOBs do Azure selecionada ou deixe em branco para usar um padrão para preparo.

   1. Selecione **validação de VNet**  >  **continuar**. 

Na seção **Resumo**, examine todas as configurações de provisionamento, marque os links de documentação recomendados e selecione **Concluir** para iniciar a criação do runtime de integração.

   > [!NOTE]
   > Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
   >
   > Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
   > 
   > Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter mais informações sobre os componentes internos/pré-instalados, consulte [Componentes internos/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para mais informações sobre componentes adicionais que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Painel Conexões

No painel **Conexões** do hub **Gerenciar**, mude para a página **Runtimes de integração** e selecione **Atualizar**. 

   ![Painel Conexões](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Você pode editar/reconfigurar seu Azure-SSIS IR selecionando seu nome. Você também pode selecionar os botões relevantes para monitorar/iniciar/parar/excluir seu Azure-SSIS IR, gerar automaticamente um pipeline do ADF com a atividade Executar Pacote do SSIS para execução em seu Azure-SSIS IR e exibir o código/conteúdo JSON de seu Azure-SSIS IR.  Editar/excluir seu Azure-SSIS IR só pode ser feito quando for ele estiver interrompido.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>runtimes de integração do Azure SSIS no portal

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar** e selecione **Conexões**. Em seguida, alterne para a guia **Integration Runtimes** para exibir os runtimes de integração existentes em seu data factory.

   ![Exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Selecione **novo** para criar um novo Azure-SSIS ir e abra o painel **instalação do Integration Runtime** .

   ![runtime de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. No painel **Instalação do runtime de integração**, selecione o bloco **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**.

   ![Especificar o tipo de runtime de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Para as etapas restantes para configurar um Azure-SSIS IR, consulte a seção [provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Usar Azure PowerShell para criar um Integration Runtime

Nesta seção, você usará Azure PowerShell para criar um Azure-SSIS IR.

### <a name="create-variables"></a>Criar variáveis

Copie e cole o seguinte script. Especifique valores para as variáveis. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Entrar e selecionar uma assinatura

Adicione o script a seguir para entrar e selecionar sua assinatura do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Validar a conexão ao servidor de banco de dados

Adicione o script a seguir para validar o servidor do banco de dados SQL do Azure ou a instância gerenciada.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Configurar a rede virtual

Adicione o script a seguir para configurar automaticamente as permissões e as configurações de rede virtual para o tempo de execução de integração do Azure-SSIS ingressar.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo.

Se seu grupo de recursos já existir, não copie este código ao seu script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Criar uma data factory

Execute o comando a seguir para criar um data factory.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Criar um Integration Runtime

Execute os seguintes comandos para criar um runtime de integração do Azure-SSIS que executa pacotes do SSIS no Azure.

Se você não usar o SSISDB, poderá omitir `CatalogServerEndpoint` os `CatalogPricingTier` parâmetros, e `CatalogAdminCredential` .

Se você não usar um servidor de banco de dados SQL do Azure com as regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um EndPoint privado para hospedar o SSISDB ou precisar de acesso a dados locais, você poderá omitir os `VNetId` `Subnet` parâmetros e ou passar valores vazios para eles. Você também pode omiti-los se configurar um IR auto-hospedado como proxy para seu Azure-SSIS IR acessar dados no local. Caso contrário, você não pode omiti-los e deve passar valores válidos de sua configuração de rede virtual. Para obter mais informações, consulte [unir um Azure-SSIS ir a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

Se você usar a instância gerenciada para hospedar o SSISDB, poderá omitir o `CatalogPricingTier` parâmetro ou passar um valor vazio para ele. Caso contrário, você não pode omiti-lo e deve passar um valor válido da lista de tipos de preço com suporte para o banco de dados SQL do Azure. Para obter mais informações, consulte [limites de recursos do banco de dados SQL](../azure-sql/database/resource-limits-logical-server.md).

Se você usar a autenticação do Azure AD com a identidade gerenciada para seu data factory para se conectar ao servidor de banco de dados, poderá omitir o `CatalogAdminCredential` parâmetro. Mas você deve adicionar a identidade gerenciada para seu data factory em um grupo do Azure AD com permissões de acesso ao servidor de banco de dados. Para obter mais informações, consulte [habilitar a autenticação do Azure ad para um Azure-SSIS ir](./enable-aad-authentication-azure-ssis-ir.md). Caso contrário, você não pode omiti-lo e deve passar um objeto válido formado por seu nome de usuário do administrador do servidor e senha para autenticação do SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Iniciar o Integration Runtime

Execute o comando a seguir para iniciar o Integration Runtime do Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
>
> Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter mais informações sobre os componentes internos/pré-instalados, consulte [Componentes internos/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para mais informações sobre componentes adicionais que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Script completo

Este é o script completo que cria um tempo de execução de integração do Azure-SSIS.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Usar um modelo de Azure Resource Manager para criar um tempo de execução de integração

Nesta seção, você usa um modelo de Azure Resource Manager para criar o tempo de execução de integração do Azure-SSIS. Veja um exemplo de explicação:

1. Crie um arquivo JSON com o seguinte modelo do Azure Resource Manager. Substitua os valores nos colchetes angulares (espaços reservados) pelos seus próprios valores.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Para implantar o modelo de Azure Resource Manager, execute o `New-AzResourceGroupDeployment` comando, conforme mostrado no exemplo a seguir. No exemplo, `ADFTutorialResourceGroup` é o nome do seu grupo de recursos. `ADFTutorialARM.json` é o arquivo que contém a definição de JSON para seu data factory e o Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Esse comando cria o data factory e Azure-SSIS IR nele, mas não inicia o IR.

3. Para iniciar o Azure-SSIS IR, execute o `Start-AzDataFactoryV2IntegrationRuntime` comando:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Exceto por qualquer tempo decorrente de uma instalação personalizada, esse processo deve ser concluído em até cinco minutos. Mas pode levar de 20-30 minutos para que o Azure-SSIS IR ingresse em uma rede virtual.
>
> Se você usa o SSISDB, ele é preparado pelo serviço Data Factory, que se conecta ao seu servidor de banco de dados para esse fim. Ele também configura permissões e configurações para sua rede virtual, se especificado, e une seu Azure-SSIS IR à rede virtual.
> 
> Quando você provisiona o Azure-SSIS IR, o Azure Feature Pack para SSIS e o Access Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel, arquivos do Access e para várias fontes de dados do Azure, além das fontes de dados que já são compatíveis com os componentes internos. Para obter mais informações sobre os componentes internos/pré-instalados, consulte [Componentes internos/pré-instalados no Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Para mais informações sobre componentes adicionais que você pode instalar, confira [Instalação personalizada de um Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS

Se usar o SSISDB, você poderá implantar seus pacotes nele e executá-los no Azure-SSIS IR usando as SSDT habilitadas para Azure ou as ferramentas do SSMS. Essas ferramentas se conectam ao servidor de banco de dados por meio do ponto de extremidade do servidor: 

- Para um servidor do Banco de Dados SQL do Azure, o formato de ponto de extremidade do servidor é `<server name>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade privado, o formato de ponto de extremidade do servidor é `<server name>.<dns prefix>.database.windows.net`.
- Para uma instância gerenciada com ponto de extremidade público, o formato de ponto de extremidade do servidor é `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Se não usar o SSISDB, você poderá implantar os pacotes no sistema de arquivos, nos Arquivos do Azure ou no MSDB hospedado pela Instância Gerenciada de SQL do Azure e executá-los no Azure-SSIS IR usando os utilitários de linha de comando [dtutil](/sql/integration-services/dtutil-utility) e [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Para saber mais, confira [Implantar projetos/pacotes do SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Nos dois casos, você também pode executar os pacotes implantados no Azure-SSIS IR usando a atividade Executar Pacote SSIS em pipelines do Data Factory. Para obter mais informações, confira [Invocar execução do pacote SSIS como uma atividade de Data Factory de primeira classe](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Próximas etapas

Consulte outros tópicos Azure-SSIS IR nesta documentação:

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações sobre os tempos de execução de integração em geral, incluindo Azure-SSIS IR.
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar e compreender as informações sobre seu Azure-SSIS IR.
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou excluir seu Azure-SSIS IR. Ele também mostra como escalar horizontalmente sua Azure-SSIS IR adicionando mais nós.
- [Implantar, executar e monitorar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conectar-se ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Agendar a execução de pacotes no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)