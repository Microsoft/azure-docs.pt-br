## <a name="set-up-azure-powershell-for-azure-dns"></a>Configurar o Azure PowerShell para DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Você precisa instalar a versão mais recente dos cmdlets do Azure Resource Manager do PowerShell. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs):

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Abra o console do PowerShell e conecte-se à sua conta. Para obter mais informações, confira [Usar o PowerShell com o Gerenciador de Recursos](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selecionar a assinatura
 
Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Criar um grupos de recursos

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrar provedor de recursos

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Para que você possa usar o DNS do Azure, sua assinatura do Azure deve ser registrada para usar esse provedor de recursos. Essa operação deve ser executa apenas uma vez para cada assinatura.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```