
## <a name="start-your-powershell-session"></a>Iniciar sua sessão do PowerShell
Primeiro, você precisa ter o [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) mais recente instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Os exemplos neste tópico usam o [Modelo de implantação do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) e, portanto, usam [cmdlets do Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Execute o cmdlet [**Add-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) e você verá uma tela de entrada para inserir suas credenciais. Use as mesmas credenciais que você usa para entrar no Portal do Azure.

    Add-AzureRmAccount

Se você tiver várias assinaturas, use o cmdlet [**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) para selecionar qual assinatura sua sessão do PowerShell deve usar. Para ver qual assinatura a sessão atual do PowerShell está usando, execute [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Para ver todas as suas assinaturas, execute [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

