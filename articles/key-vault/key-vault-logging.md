---
title: Logs do Cofre de Chaves do Azure | Microsoft Docs
description: "Use este tutorial para ajudá-lo a começar a usar os logs do Cofre da Chave do Azure."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: cabailey
ms.openlocfilehash: e9a4f16f048833dab49f7db79892fe47a5aeff37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-logging"></a>Logs do Cofre da Chave do Azure
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres da chave serão acessados e por quem. Você pode fazer isso ao habilitar o log para o Cofre da Chave, que salva as informações em uma conta de armazenamento do Azure fornecida por você. Um novo contêiner chamado **insights-logs-auditevent** é automaticamente criado para a conta de armazenamento especificada e você poderá usar essa mesma conta de armazenamento para a coleta de logs de vários cofres da chave.

Você pode acessar suas informações de log em até 10 minutos após a operação do cofre da chave. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Use este tutorial para ajudá-lo a começar a usar os logs do Cofre da Chave do Azure, para criar sua conta de armazenamento, para habilitar o log e para interpretar as informações de log coletadas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre como criar cofres da chave, chaves ou segredos. Para obter essas informações, confira [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md). Ou, para obter instruções de Interface de linha de comando entre diferentes plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
> No momento, não é possível configurar o Cofre da Chave do Azure no portal do Azure. Em vez disso, use estas instruções do PowerShell do Azure.
>
>

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Um cofre da chave existente que você esteja usando.  
* Azure PowerShell, **versão mínima: 1.0.1**. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se você já tiver instalado o Azure PowerShell e não souber a versão, no console do Azure PowerShell, digite `(Get-Module azure -ListAvailable).Version`.  
* Armazenamento suficiente no Azure para seus logs do Cofre da Chave.

## <a id="connect"></a>Conectar-se a suas assinaturas
Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

    Login-AzureRmAccount

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a assinatura associada ao cofre de chaves do qual os logs serão registrados, digite:

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> Esta é uma etapa importante e será especialmente útil se você tiver várias assinaturas associadas à sua conta. Você poderá receber um erro para registrar Microsoft.Insights se esta etapa for ignorada.
>   
>

Para saber mais sobre a configuração do Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Criar uma nova conta de armazenamento para seus logs
Embora você possa usar uma conta de armazenamento existente para seus logs, criaremos uma nova conta de armazenamento que será dedicada aos logs do Cofre da Chave. Para conveniência quando tivermos de especificar isso posteriormente, armazenaremos os detalhes em uma variável chamada **sa**.

Para facilidade de gerenciamento, também usaremos o mesmo grupo de recursos que contém o cofre da chave. No [tutorial de introdução](key-vault-get-started.md), esse grupo de recursos é denominado **ContosoResourceGroup** e continuaremos a usar o local Ásia Oriental. Substitua esses valores pelos seus, conforme aplicável:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ela deverá usar a mesma assinatura do cofre de chaves e deverá usar o modelo de implantação do Gerenciador de Recursos em vez do modelo de implantação Clássico.
>
>

## <a id="identify"></a>Identificar o cofre da chave para seus logs
Em nosso guia de introdução, o nome do cofre de chaves era **ContosoKeyVault** e, portanto, continuaremos a usar esse nome e a armazenar os detalhes em uma variável chamada **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Habilitar o registro em log
Para habilitar o log para o Cofre da Chave, usaremos o cmdlet Set-AzureRmDiagnosticSetting, junto com as variáveis que criamos para nossa nova conta de armazenamento e nosso cofre da chave. Também definiremos o sinalizador **-Enabled** como **$true** e definiremos a categoria como AuditEvent (a única categoria para o log do Cofre de Chaves):

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

A saída para isso inclui:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Isso confirma que o log está habilitado para o cofre de chave, salvando as informações na conta de armazenamento.

Opcionalmente, você também pode definir a política de retenção para os logs, de modo que os logs mais antigos sejam automaticamente excluídos. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90** para que os logs de mais de 90 dias sejam automaticamente excluídos.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

O que é registrado em log:

* Todas as solicitações de API REST autenticadas são registradas, o que inclui as solicitações que falharam devido a permissões de acesso, erros do sistema ou solicitações inválidas.
* As operações do próprio cofre da chave, o que inclui a criação, a exclusão, a configuração de políticas de acesso ao cofre da chave e a atualização dos atributos do cofre da chave, como as marcas.
* As operações em chaves e segredos no cofre da chave, o que inclui criar, modificar ou excluir essas chaves ou segredos; as operações como assinar, verificar, criptografar, descriptografar, encapsular e desencapsular chaves, obter segredos, listar chaves e segredos e suas versões.
* Solicitações não autenticadas que resultam em uma resposta 401. Por exemplo, solicitações que não têm um token de portador, estão malformadas ou expiradas ou têm um token inválido.  

## <a id="access"></a>Acessar seus logs
Os logs do cofre de chaves são armazenados no contêiner **insights-logs-auditevent** na conta de armazenamento que você forneceu. Para listar todos os blobs desse contêiner, digite:

Primeiro, crie uma variável para o nome do contêiner. Isso será usado no restante do passo a passo.

    $container = 'insights-logs-auditevent'

Para listar todos os blobs desse contêiner, digite:

    Get-AzureStorageBlob -Container $container -Context $sa.Context
A saída será parecida com esta:

**URI de contêiner: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Nome**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

Como você pode ver nessa saída, os blobs seguem uma convenção de nomenclatura: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Os valores de data e hora usam UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, a ID do recurso completa no nome do blob será muito útil para acessar ou baixar apenas os blobs que você precisa. Mas, antes de fazer isso, primeiro vamos mostrar como baixar todos os blobs.

Primeiro, crie uma pasta para baixar os blobs. Por exemplo:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Em seguida, obtenha uma lista de todos os blobs:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Redirecione essa lista pelo 'Get-AzureStorageBlobContent' para baixar os blobs em nossa pasta de destino:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Quando você executar esse segundo comando, o delimitador **/** nos nomes de blob criará uma estrutura de pastas completa na pasta de destino e essa estrutura será usada para baixar e armazenar os blobs como arquivos.

Use caracteres curinga para baixar seletivamente os blobs. Por exemplo:

* Se você tiver vários cofres da chave e quiser baixar logs de apenas um cofre da chave, chamado CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* Se você tiver vários grupos de recursos e quiser baixar os logs para apenas um grupo de recursos, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* Se você quiser baixar todos os logs do mês de janeiro de 2016, use `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Agora você está pronto para começar a examinar o conteúdo dos logs. Mas antes de fazer isso, há mais dois parâmetros para Get-AzureRmDiagnosticSetting que você precisa conhecer:

* Para consultar o status das configurações de diagnóstico do recurso cofre de chaves: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desabilitar o log do recurso cofre de chaves: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Interpretar os logs do Cofre de Chave
Os blobs individuais são armazenados como texto, formatados como um blob JSON. Este é um exemplo de entrada de log a partir da execução de `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

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


A tabela a seguir lista os nomes e as descrições de campo.

| Nome do campo | Descrição |
| --- | --- |
| tempo real |Data e hora (UTC). |
| resourceId |ID do Recurso do Gerenciador de Recursos do Azure. Para os logs do Cofre de Chaves, isso sempre será a ID do recurso do Cofre de Chaves. |
| operationName |Nome da operação, como documentado na tabela a seguir. |
| operationVersion |É a versão da API REST solicitada pelo cliente. |
| categoria |Para logs do Cofre da Chave, AuditEvent é o único valor disponível. |
| resultType |Resultado da solicitação da API REST. |
| resultSignature |Código de status HTTP. |
| resultDescription |Descrição adicional sobre o resultado, quando disponível. |
| durationMs |Tempo necessário para atender à solicitação da API REST, em milissegundos. Isso não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| callerIpAddress |Endereço IP do cliente que fez a solicitação. |
| correlationId |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| identidade |Identidade do token que foi apresentado ao fazer a solicitação da API REST. Isso geralmente é um "usuário", uma "entidade de serviço" ou uma combinação "usuário+appId", como no caso de uma solicitação, resultado de um cmdlet do Azure PowerShell. |
| propriedades |Esse campo conterá informações diferentes com base na operação (operationName). Na maioria dos casos, contém informações de cliente (a cadeia de caracteres useragent passada pelo cliente), o URI exato da solicitação da API REST e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, KeyCreate ou VaultGet), também conterá o URI da Chave (como "id"), o URI do Cofre ou o URI do Segredo. |

Os valores do campo **operationName** estão no formato ObjectVerb. Por exemplo:

* Todas as operações do cofre de chaves têm o formato 'Vault`<action>`', como `VaultGet` e `VaultCreate`.
* Todas as operações da chave têm o formato 'Key`<action>`', como `KeySign` e `KeyList`.
* Todas as operações do segredo têm o formato 'Secret`<action>`', como `SecretGet` e `SecretListVersions`.

A tabela a seguir lista o operationName e o comando da API REST correspondente.

| operationName | Comando da API REST |
| --- | --- |
| Autenticação |Via ponto de extremidade do Active Directory do Azure |
| VaultGet |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx) |
| VaultPut |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx) |
| VaultDelete |[Excluir um cofre de chaves](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx) |
| VaultPatch |[Atualizar um cofre da chave](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Listar todos os cofres de chaves em um grupo de recursos](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx) |
| KeyCreate |[Criar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx) |
| KeyGet |[Obter informações sobre uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx) |
| KeyImport |[Importar uma chave para um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx) |
| KeyBackup |[Fazer backup de uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx). |
| KeyDelete |[Excluir uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx) |
| KeyRestore |[Restaurar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx) |
| KeySign |[Assinar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx) |
| KeyVerify |[Verificar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx) |
| KeyWrap |[Encapsular uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) |
| KeyUnwrap |[Desencapsular uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx) |
| KeyEncrypt |[Criptografar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx) |
| KeyDecrypt |[Descriptografar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx) |
| KeyUpdate |[Atualizar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx) |
| KeyList |[Listar as chaves em um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx) |
| KeyListVersions |[Listar as versões de uma chave](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx) |
| SecretSet |[Criar um segredo](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx) |
| SecretGet |[Obter um segredo](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx) |
| SecretUpdate |[Atualizar um segredo](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx) |
| SecretDelete |[Excluir um segredo](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx) |
| SecretList |[Listar segredos em um cofre](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx) |
| SecretListVersions |[Listar versões de um segredo](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Usar Log Analytics

Você pode usar a solução de Cofre de Chaves do Azure no Log Analytics para examinar logs AuditEvent do Cofre de Chaves do Azure. Para obter mais informações, incluindo como configurar isso, consulte [Solução Azure Key Vault no Log Analytics](../log-analytics/log-analytics-azure-key-vault.md). Este artigo também contém instruções se você precisar migrar da solução Key Vault antiga que foi oferecida durante a versão prévia do Log Analytics, em que você primeiro roteava os logs para uma conta de armazenamento do Azure e configurava o Log Analytics para ler daquela conta.

## <a id="next"></a>Próximas etapas
Para obter um tutorial que usa o Cofre de Chaves do Azure em um aplicativo Web, confira [Usar o Cofre de Chaves do Azure em um Aplicativo Web](key-vault-use-from-web-application.md).

Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).

Para obter uma lista dos cmdlets do Azure PowerShell 1.0 para o Cofre de Chaves do Azure, confira [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).

Para obter um tutorial sobre a rotação de chaves e o log de auditoria com o Cofre de Chaves do Azure, confira [Como configurar o Cofre de Chaves com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md).
