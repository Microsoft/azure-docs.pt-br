## <a name="prepare-for-akv-integration"></a>Preparar-se para a integração de AKV
Para usar a integração do Cofre da Chave do Azure e configurar a VM do SQL Server, há vários pré-requisitos: 

1. [Instalar o Azure Powershell](#install-azure-powershell)
2. [Criar um Active Directory do Azure](#create-an-azure-active-directory)
3. [Criar um cofre de chave](#create-a-key-vault)

As seções a seguir descrevem esses pré-requisitos e as informações que você precisa coletar para executar posteriormente os cmdlets do PowerShell.

### <a name="install-azure-powershell"></a>Instalar o Azure Powershell
Verifique se você instalou o SDK mais recente do Azure PowerShell. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs):

### <a name="create-an-azure-active-directory"></a>Criar um Active Directory do Azure
Primeiro, você precisa ter um [Active Directory do Azure](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) em sua assinatura. Entre os diversos benefícios, isso permite que você conceda permissão de acesso ao seu cofre de chave para determinados usuários e aplicativos.

Em seguida, registre um aplicativo com o AAD Isso lhe dará uma conta de Entidade de Serviço com acesso ao cofre de chave de que sua máquina virtual precisará. No artigo Cofre de Chaves do Azure, você pode encontrar estas etapas na seção [Registrar um aplicativo com o Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register), ou pode ver as etapas com capturas de tela na seção **Obter uma identidade para o aplicativo** desta [ostagem de blog](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Antes de concluir essas etapas, observe que você precisa coletar as seguintes informações durante esse registro e que  serão necessárias mais tarde, quando você habilitar a integração da Chave do Cofre do Azure em sua VM do SQL.

* Após a adição do aplicativo, encontre a **ID do CLIENTE** na guia **CONFIGURAR**.   ![ID de cliente do Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
  
    A ID do cliente é atribuída posteriormente ao parâmetro **$spName** (nome da Entidade de Serviço) no script do PowerShell a fim de habilitar a integração do Cofre da Chave do Azure. 
* Além disso, durante essas etapas, ao criar a chave, copie o segredo de sua chave conforme mostra a seguinte captura de tela. Esse segredo é atribuído posteriormente ao parâmetro **$spSecret** (segredo da Entidade de serviço) no script do PowerShell.  
    ![Segredo do Azure Active Directory](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
* Você deve autorizar essa nova ID de cliente para ter as seguintes permissões de acesso: **encrypt**, **decrypt**, **wrapKey**, **unwrapKey**, **sign** e **verify**. Isso é feito com o cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) . Para saber mais, consulte [Autorizar o aplicativo a usar a chave ou o segredo](../articles/key-vault/key-vault-get-started.md#authorize).

### <a name="create-a-key-vault"></a>Criar um cofre de chave
Para usar o Cofre da Chave do Azure a fim de armazenar as chaves que você usará para criptografia em sua VM, você precisa ter acesso em um cofre de chave. Se você ainda não tiver configurado seu cofre de chave, crie um usando as etapas no tópico [Introdução ao Cofre da Chave do Azure](../articles/key-vault/key-vault-get-started.md) . Antes de concluir essas etapas, você precisa coletar algumas informações durante esse configuração que serão necessárias mais tarde quando você habilitar a integração do Cofre da Chave do Azure em sua VM do SQL.

Quando você chega à etapa Criar um cofre de chave, observe a propriedade **vaultUri** retornada, que é a URL do cofre de chave. No exemplo fornecido nesta etapa, mostrado abaixo, o nome do cofre de chaves é ContosoKeyVault, portanto a URL do cofre de chaves seria https://contosokeyvault.vault.azure.net/.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

A URL do cofre de chave será atribuída posteriormente ao parâmetro **$akvURL** no script do PowerShell a fim de habilitar a integração do Cofre da Chave do Azure.

