## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configurando o PowerShell para modelos do Gerenciador de recursos
Antes de poder usar o Azure PowerShell com o Gerenciador de Recursos, você precisará ter as versões corretas do Windows PowerShell e do Azure PowerShell.

### <a name="verify-powershell-versions"></a>Verificar as versões do PowerShell
Verifique se você tem o Windows PowerShell versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, digite este comando no prompt de comando do Windows PowerShell.

    $PSVersionTable

Você receberá o seguinte tipo de informações:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Caso não seja, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure
Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra um prompt de comando do Azure PowerShell e faça logon no Azure com este comando.

    Login-AzureRmAccount

Se você tiver várias assinaturas do Azure, você pode listar suas assinaturas do Azure com este comando.

    Get-AzureRmSubscription

Você receberá o seguinte tipo de informações:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Você pode definir a assinatura do Azure atual ao executar estes comandos no prompt de comando do Azure PowerShell. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelo nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Para saber mais sobre contas e assinaturas do Azure, consulte [Como conectar-se à sua assinatura](/powershell/azureps-cmdlets-docs#step-3-connect).

