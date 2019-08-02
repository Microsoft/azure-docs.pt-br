---
title: Log do Cofre de chaves do Azure | Microsoft Docs
description: Use este tutorial para ajudá-lo a começar a usar os logs do Cofre da Chave do Azure.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 0ae977215e52883e190ad3859eefac9e97462968
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205957"
---
# <a name="azure-key-vault-logging"></a>Log do Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres de chaves, você provavelmente desejará monitorar como e quando os cofres de chaves são acessados e por quem. Você pode fazer isso habilitando o registro em log para o Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure que você fornecer. Um novo contêiner denominado **insights-logs-auditevent** é criado automaticamente para sua conta de armazenamento especificado. Você pode usar essa mesma conta de armazenamento para coletar logs para vários cofres da chave.

Você pode acessar suas informações de log 10 minutos (no máximo) após a operação do Cofre de chaves. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Use este tutorial para ajudá-lo a começar a usar os logs do Cofre da Chave do Azure. Você vai criar uma conta de armazenamento, habilitar registro em log e interpretar as informações de log coletados.  

> [!NOTE]
> Este tutorial não inclui instruções sobre como criar cofres da chave, chaves ou segredos. Para obter essa informação, confira [O que é o Azure Key Vault?](key-vault-overview.md). Ou, para obter instruções de CLI do Azure de plataforma cruzada, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
> Este artigo fornece instruções de Azure PowerShell para atualizar o log de diagnóstico. Você também pode atualizar o log de diagnóstico usando o Azure Monitor na **logs de diagnóstico** seção do portal do Azure. 
>

Para obter informações gerais sobre o Key Vault, consulte [o que é o Azure Key Vault?](key-vault-whatis.md). Para obter informações sobre onde o Cofre de chaves está disponível, consulte o [página de preços](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Um cofre da chave existente que você esteja usando.  
* O Azure PowerShell, versão mínima do 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se você já tiver instalado o Azure PowerShell e não souber a versão do console do PowerShell do Azure, insira `$PSVersionTable.PSVersion`.  
* Armazenamento suficiente no Azure para seus logs do Cofre da Chave.

## <a id="connect"></a>Conectar-se à sua assinatura do Cofre de chaves

A primeira etapa na configuração de log de chave é apontar o Azure PowerShell para o Cofre de chaves que você deseja registrar.

Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas essa conta. Por padrão, o PowerShell usa o primeiro deles.

Você terá que especificar a assinatura que você usou para criar o Cofre de chaves. Insira o seguinte comando para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura que está associada com o Cofre de chaves que você vai estar fazendo o logon, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontando o PowerShell para a assinatura correta é uma etapa importante, especialmente se você tiver várias assinaturas associadas à sua conta. Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a id="storage"></a>Criar uma conta de armazenamento para seus logs

Embora você possa usar uma conta de armazenamento existente para seus logs, vamos criar uma conta de armazenamento que será dedicada aos logs do Cofre de chaves. Para sua conveniência quando tivermos de especificar isso posteriormente, armazenaremos os detalhes em uma variável chamada **sa**.

Para facilidade de gerenciamento adicional, também usaremos o mesmo grupo de recursos que contém o Cofre de chaves. Dos [tutorial de Introdução](key-vault-get-started.md), esse grupo de recursos é denominado **ContosoResourceGroup**, e continuaremos a usar o local Ásia Oriental. Substitua esses valores pelos seus próprios, conforme aplicável:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ele deve usar a mesma assinatura como o Cofre de chaves. E ele deve usar o modelo de implantação do Azure Resource Manager, em vez do modelo de implantação clássico.
>
>

## <a id="identify"></a>Identificar o cofre da chave para seus logs

No [tutorial de Introdução](key-vault-get-started.md), o nome do Cofre de chaves era **ContosoKeyVault**. Continuaremos a usar esse nome e armazena os detalhes em uma variável chamada **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Habilitar o registro em log

Para habilitar o registro em log para o Cofre de chaves, vamos usar o **AzDiagnosticSetting conjunto** cmdlet, junto com as variáveis que criamos para a nova conta de armazenamento e o Cofre de chaves. Também vamos definir a **-habilitado** sinalizador como **$true** e defina a categoria como **AuditEvent** (a única categoria para registro em log do Cofre de chaves):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A saída se parece com esta:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Essa saída confirma que o log está habilitado para o Cofre de chaves, ele salvará informações em sua conta de armazenamento.

Opcionalmente, você pode definir uma política de retenção para os logs, de modo que os logs mais antigos são excluídos automaticamente. Por exemplo, definir a política de retenção, definindo o **- RetentionEnabled** sinalizador como **$true**e defina o **- RetentionInDays** parâmetro **90**para que os logs de mais de 90 dias sejam automaticamente excluídos.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registrado em log:

* Todos os autenticados solicitações da API REST, incluindo solicitações que falharam devido a permissões de acesso, erros de sistema ou solicitações inválidas.
* Operações na chave do cofre em si, incluindo criação, exclusão, políticas de acesso do Cofre de chaves de configuração e atualizar os atributos de Cofre de chaves, como marcas.
* Operações em chaves e segredos no cofre de chaves, incluindo:
  * Criar, modificar ou excluir essas chaves ou segredos.
  * Assinatura, verificando, criptografar, descriptografar, encapsular e descodificar chaves, obter segredos e listagem de chaves e segredos (e suas versões).
* Solicitações não autenticadas que resultam em uma resposta 401. Os exemplos são as solicitações que não têm um token de portador, que estão malformadas ou expiradas ou que têm um token inválido.  

## <a id="access"></a>Acessar seus logs

Logs do Cofre de chaves são armazenados do **insights-logs-auditevent** contêiner na conta de armazenamento que você forneceu. Para exibir os logs, você precisará baixar blobs.

Primeiro, crie uma variável para o nome do contêiner. Você usará essa variável em todo o restante do passo a passo.

```powershell
$container = 'insights-logs-auditevent'
```

Para listar todos os blobs nesse contêiner, digite:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A saída deve ser semelhante a esta:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Como você pode ver neste resultado, os blobs seguem uma convenção de nomenclatura: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores de data e hora usam UTC.

Como você pode usar a mesma conta de armazenamento para coletar logs de vários recursos, a ID do recurso completa no nome do blob é útil para acessar ou baixar apenas os blobs que você precisa. Mas, antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Crie uma pasta para baixar os blobs. Por exemplo:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirecione essa lista pelo **Get-AzStorageBlobContent** para baixar os blobs para a pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando você executa esse segundo comando, o delimitador **/** nos nomes de blob cria uma estrutura de pastas completa na pasta de destino. Você usará essa estrutura para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo:

* Se você tiver vários cofres da chave e quiser baixar logs de apenas um cofre da chave, chamado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se você quiser baixar todos os logs do mês de janeiro de 2019, use `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora você está pronto para começar a examinar o conteúdo dos logs. Mas, antes de passarmos para que, você deve saber mais dois:

* Para consultar o status das configurações de diagnóstico do recurso cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desabilitar o log do recurso cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretar os logs do Cofre de Chave

Os blobs individuais são armazenados como texto, formatados como um blob JSON. Vamos examinar um exemplo de entrada de log. Execute este comando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Retorna uma entrada de log de maneira semelhante a esta:

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A tabela a seguir lista os nomes de campo e descrições:

| Nome do campo | DESCRIÇÃO |
| --- | --- |
| **time** |Data e hora em UTC. |
| **resourceId** |ID do Recurso do Azure Resource Manager. Para os logs do Cofre da Chave, isso sempre será a ID do recurso do Cofre da Chave. |
| **operationName** |Nome da operação, como documentado na tabela a seguir. |
| **operationVersion** |Versão da API REST solicitada pelo cliente. |
| **category** |Tipo de resultado. Para logs do Cofre de chaves **AuditEvent** é o único valor disponível. |
| **resultType** |Resultado da solicitação da API REST. |
| **resultSignature** |Código de status HTTP. |
| **resultDescription** |Descrição adicional sobre o resultado, quando disponível. |
| **durationMs** |Tempo necessário para atender à solicitação da API REST, em milissegundos. Isso não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez a solicitação. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| **identidade** |Identidade do token que foi apresentado na solicitação de API REST. Isso geralmente é um "usuário", uma "entidade de serviço" ou a combinação "usuário + appId," como no caso de uma solicitação que é resultante de um cmdlet do PowerShell do Azure. |
| **properties** |Informações que variam de acordo com a operação (**operationName**). Na maioria dos casos, esse campo contém informações de cliente (a sequência de agente usuário passada pelo cliente), o URI de solicitação de API REST exato e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, **KeyCreate** ou **VaultGet**), ele também contém a chave URI (como "id"), URI ou o URI do segredo do cofre. |

O **operationName** valores de campo são na *ObjectVerb* formato. Por exemplo:

* Todas as operações do Cofre de chaves têm o `Vault<action>` Formatar, como `VaultGet` e `VaultCreate`.
* Todas as operações de chave têm o `Key<action>` Formatar, como `KeySign` e `KeyList`.
* Todas as operações do segredo têm o `Secret<action>` Formatar, como `SecretGet` e `SecretListVersions`.

A seguinte tabela lista os **operationName** valores e comandos da API REST correspondentes:

| operationName | Comando da API REST |
| --- | --- |
| **Autenticação** |Autenticar por meio do ponto de extremidade do Azure Active Directory |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Excluir um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre da chave](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Listar todos os cofres de chaves em um grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Fazer backup de uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Excluir uma chave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Restaurar uma chave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Assinar com uma chave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificar com uma chave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Encapsular uma chave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Desencapsular uma chave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Criptografar com uma chave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Descriptografar com uma chave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Atualizar uma chave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Listar as chaves em um cofre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Listar as versões de uma chave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Criar um segredo](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obter um segredo](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Atualizar um segredo](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Excluir um segredo](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Listar segredos em um cofre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Listar versões de um segredo](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Usar logs do Azure Monitor

Você pode usar a solução de Cofre de chaves nos logs do Azure Monitor para examinar o Key Vault **AuditEvent** logs. Nos logs do Azure Monitor, você pode usar consultas de log para analisar dados e obter as informações que necessárias. 

Para obter mais informações, incluindo como configurar isso, consulte [solução de Cofre de chaves do Azure nos logs do Azure Monitor](../azure-monitor/insights/azure-key-vault.md). Este artigo também contém instruções se você precisar migrar da solução antiga de Key Vault que foi oferecida durante os logs do Azure Monitor de visualização, onde você primeiramente roteado seus logs para uma conta de armazenamento do Azure e logs de Monitor do Azure configurado para ler a partir daí.

## <a id="next"></a>Próximas etapas

Para obter um tutorial que usa o Azure Key Vault em um aplicativo da web .NET, consulte [usar o Azure Key Vault de um aplicativo web](tutorial-net-create-vault-azure-web-app.md).

Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).

Para obter uma lista dos cmdlets do Azure PowerShell 1.0 para o Azure Key Vault, consulte [cmdlets do Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Para obter um tutorial sobre rotação de chaves e o log de auditoria com o Azure Key Vault, consulte [configurar o Cofre de chaves com a rotação de chaves de ponta a ponta e auditoria](key-vault-key-rotation-log-monitoring.md).
