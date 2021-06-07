---
title: Gerenciar conexões na Automação do Azure
description: Este artigo informa como gerenciar conexões de Automação do Azure com serviços ou aplicativos externos e como trabalhar com eles em runbooks.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054256"
---
# <a name="manage-connections-in-azure-automation"></a>Gerenciar conexões na Automação do Azure

Um ativo de conexão da Automação do Azure contém as informações listadas abaixo. Essas informações são necessárias para conexão com um aplicativo ou serviço externo de um runbook ou da configuração DSC. 

* Informações necessárias para autenticação, como nome de usuário e senha
* Informações de conexão, como URL ou porta

O ativo de conexão mantém todas as propriedades para se conectar a um aplicativo específico, tornando a criação de várias variáveis desnecessária. O usuário pode editar os valores para uma conexão em um único local, e você pode transmitir o nome de uma conexão para um runbook ou uma configuração DSC em um único parâmetro. O runbook ou a configuração acessa as propriedades de uma conexão usando o cmdlet `Get-AutomationConnection` interno.

Ao criar uma conexão, você deve especificar um tipo de conexão. O tipo de conexão é um modelo que define um conjunto de propriedades. Você pode adicionar um tipo de conexão à Automação do Azure usando um módulo de integração com um arquivo de metadados. Também é possível usar a [API da Automação do Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) para criar um tipo de conexão se o módulo de integração incluir um tipo de conexão e for importado para sua conta de Automação. 

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta da Automação do Azure. A Automação do Azure armazena a chave no Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a Automação do Azure carrega a chave do Key Vault e depois a usa para criptografar o ativo. 

## <a name="connection-types"></a>Tipos de conexão

A Automação do Azure torna os seguintes tipos de conexão internos disponíveis:

* `Azure` - Representa uma conexão usada para gerenciar os recursos clássicos.
* `AzureServicePrincipal` - Representa uma conexão usada pela conta Executar como do Azure.
* `AzureClassicCertificate` - Representa uma conexão usada pela conta Executar como clássica do Azure.

Na maioria dos casos, você não precisa criar um recurso de conexão, pois ele é gerado quando ao criar uma [conta Executar como](automation-security-overview.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Cmdlets do PowerShell para acessar conexões

Os cmdlets na tabela a seguir são usados para criar e gerenciar credenciais de Automação com o PowerShell. Eles são fornecidos como parte de [módulos Az](shared-resources/modules.md#az-modules).

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Recupera informações sobre uma conexão.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Cria uma nova conexão.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Remove uma conexão existente.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Define o valor de determinado campo para uma conexão existente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlets internos para acessar conexões

As cmdlets internos na tabela a seguir são usados para acessar conexões em seus runbooks ou configurações DSC. Esse cmdlet vem com o módulo global`Orchestrator.AssetManagement.Cmdlets`. Para obter mais informações, confira [Cmdlets internos](shared-resources/modules.md#internal-cmdlets).

|Cmdlet Interno|Descrição|
|---|---|
|`Get-AutomationConnection` | Recupera os valores dos diferentes campos na conexão e os retorna como uma [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Você pode usar essa tabela de hash com os comandos apropriados no runbook ou na configuração DSC.|

>[!NOTE]
>Evite usar variáveis com o parâmetro `Name` de `Get-AutomationConnection`. O uso de variáveis neste caso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de conexão em tempo de design.

## <a name="python-functions-to-access-connections"></a>Funções do Python para acessar conexões

A função na tabela a seguir é usada para acessar conexões em um runbook do Python 2 e 3. Atualmente, os runbooks do Python 3 estão em versão prévia.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma conexão. Retorna um dicionário com as propriedades da conexão. |

> [!NOTE]
> É necessário importar o módulo `automationassets`, na parte superior do runbook do Python para acessar as funções do ativo.

## <a name="create-a-new-connection"></a>Crie uma nova conexão

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova conexão com o portal do Azure

Para criar uma nova conexão no portal do Azure:

1. Na sua Conta de Automação, clique em **Conexões** em **Recursos Compartilhados**.
2. Clique em **+ Adicionar uma conexão** na página Conexões.
4. No campo **Tipo** no painel Nova Conexão, selecione o tipo de conexão a ser criado. Suas opções são `Azure`, `AzureServicePrincipal` e `AzureClassicCertificate`. 
5. O formulário apresenta propriedades para o tipo de conexão escolhida. Preencha o formulário e clique em **Criar** para salvar a nova conexão.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova conexão com o Windows PowerShell

Criar uma nova conexão com o Windows PowerShell usando o cmdlet `New-AzAutomationConnection`. Esse cmdlet tem um parâmetro `ConnectionFieldValues` que espera uma tabela de hash que define os valores para cada uma das propriedades definidas pelo tipo de conexão.

Você pode usar os seguintes exemplo de comando como uma alternativa para criar a conta Executar como no portal para criar um novo ativo de conexão.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando você cria sua conta de Automação, ela inclui vários módulos globais por padrão, juntamente com o tipo de conexão `AzureServicePrincipal` para criar o ativo de conexão `AzureRunAsConnection`. Se você tentar criar um novo ativo de conexão para se conectar a um serviço ou aplicativo com um método de autenticação diferente, a operação falhará porque o tipo de conexão já não está definido na sua conta de Automação. Para obter mais informações sobre como criar seu próprio tipo de conexão para um módulo personalizado, consulte [Adicionar um tipo de conexão](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Adicionar um tipo de conexão

Se o runbook ou a configuração DSC se conectar a um serviço externo, você precisará definir um tipo de conexão em um [módulo personalizado](shared-resources/modules.md#custom-modules) chamado de módulo de integração. Esse módulo inclui um arquivo de metadados que especifica as propriedades do tipo de conexão e é tem o nome **&lt;ModuleName&gt;-Automation.json**, localizado na pasta do módulo do arquivo compactado **.zip**. Esse arquivo contém os campos de uma conexão que são necessários para estabelecer conexão com o sistema ou serviço que o módulo representa. Use esse arquivo para definir os nomes de campo, os tipos de dados, o status de criptografia e o status opcional para o tipo de conexão. 

O exemplo a seguir é um modelo no formato de arquivo **.json** que define as propriedades de nome de usuário e senha para um tipo de conexão personalizado chamado `MyModuleConnection`:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Obter uma conexão em um runbook ou configuração DSC

Recuperar uma conexão em um runbook ou configuração DSC com o cmdlet `Get-AutomationConnection` interno. Prefira esse cmdlet ao `Get-AzAutomationConnection`, pois ele recupera os valores de conexão em vez de informações sobre a conexão.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como usar a conta Executar como para autenticar os recursos em seu runbook com Azure Resource Manager. Eles usam um ativo de conexão que representa a conta Executar como, que faz referência à entidade de serviço baseada em certificado.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

O exemplo a seguir mostra como autenticar usando a conexão executar como em um runbook do Python 2 e 3.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Exemplos de runbook gráfico

Você pode adicionar uma atividade para o cmdlet `Get-AutomationConnection` interno a um runbook gráfico. Clique com o botão direito do mouse na conexão no painel Biblioteca do editor gráfico e selecione **Adicionar à tela**.

![adicionar à tela](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma objeto de conexão em um runbook gráfico. Este exemplo usa o conjunto de dados `Constant value` para a atividade `Get RunAs Connection`, que usa um objeto de conexão para autenticação. Um [link de pipeline](automation-graphical-authoring-intro.md#use-links-for-workflow) é usado aqui, pois o conjunto de parâmetros `ServicePrincipalCertificate` está esperando um único objeto.

![obter conexões](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar conexões, consulte [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).
* Para obter informações gerais sobre runbooks, consulte [Execução de runbook na Automação do Azure](automation-runbook-execution.md).
* Para obter detalhes sobre as configurações de DSC, consulte [Visão geral do State Configuration](automation-dsc-overview.md).