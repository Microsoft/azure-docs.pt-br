### <a name="troubleshoot-azure-diagnostics"></a>Solucionar problemas de diagnóstico do Azure

Se você receber a seguinte mensagem de erro, o provedor de recursos Microsoft.insights não está registrado:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Para registrar o provedor de recursos, execute as seguintes etapas no portal do Azure:

1.  No painel de navegação à esquerda, clique em *Assinaturas*
2.  Selecione a assinatura identificada na mensagem de erro
3.  Clique em *Provedores de Recursos*
4.  Localize o provedor *Microsoft.insights*
5.  Clique o *registrar* link

![Registrar o provedor de recursos microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Uma vez o *Microsoft.insights* provedor de recursos é registrado, configurando o diagnóstico de repetição.


No PowerShell, se você receber a seguinte mensagem de erro, precisará atualizar sua versão do PowerShell:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Atualize sua versão do PowerShell para novembro de 2016 (v2.3.0) ou posterior usando as instruções no artigo [Introdução aos cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
