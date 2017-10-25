---
title: "Solução de Cofre de Chaves do Azure no Log Analytics | Microsoft Docs"
description: "Você pode usar a solução de Cofre de Chaves do Azure no Log Analytics para examinar logs do Cofre de Chaves do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 651586e0846ffb22a23e64b73c2cc614980d9b92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Solução do Azure Key Vault Analytics no Log Analytics

![Símbolo do Cofre de Chaves](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Você pode usar a solução de Cofre de Chaves do Azure no Log Analytics para examinar logs AuditEvent do Cofre de Chaves do Azure.

Para usar a solução, você precisa habilitar o registro em log de diagnóstico do Azure Key Vault e direcionar tal diagnóstico para um espaço de trabalho do Log Analytics. Não é necessário gravar os logs no Armazenamento de Blobs do Azure.

> [!NOTE]
> Em janeiro de 2017, ocorreu uma mudança na maneira correta de envio de logs do Key Vault para o Log Analytics. Se a solução de Key Vault que você está usando mostrar *(preterido)* no título, consulte [Migrar da solução antiga de Key Vault](#migrating-from-the-old-key-vault-solution) para conhecer as etapas que você deve executar.
>
>

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as instruções a seguir para instalar e configurar a solução de Cofre de Chaves do Azure:

1. Habilite a solução de Azure Key Vault do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. Habilitar o registro em log de diagnóstico para os recursos do Key Vault a serem monitorados usando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou o [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Habilitar o diagnóstico de Key Vault no portal

1. No Portal do Azure, navegue até o recurso do Key Vault a ser monitorado
2. Selecione *Logs de diagnóstico* para abrir a página seguinte

   ![imagem do bloco Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique em *Ativar diagnóstico* para abrir a página seguinte

   ![imagem do bloco Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Para ativar o diagnóstico, clique em *Ativar* em *Status*
5. Clique na caixa de seleção para *Enviar para o Log Analytics*
6. Selecione um espaço de trabalho existente do Log Analytics ou crie um espaço de trabalho
7. Para habilitar logs do *AuditEvent*, clique na caixa de seleção sob o Log
8. Clique em *Salvar* para habilitar o registro em log de diagnóstico para o Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Habilitar o diagnóstico do Key Vault usando o PowerShell
O script do PowerShell a seguir fornece um exemplo de como usar o `Set-AzureRmDiagnosticSetting` para habilitar o registro em log de diagnóstico para o Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Veja os detalhes da coleta de dados do Cofre de Chaves do Azure
A solução do Azure Key Vault coleta logs de diagnóstico diretamente do Key Vault.
Não é necessário gravar os logs no Armazenamento de Blobs do Azure e nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Cofre de Chaves do Azure.

| Plataforma | Agente direto | Agente do Systems Center Operations Manager | As tabelas | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| As tabelas |  |  |&#8226; |  |  | na chegada |

## <a name="use-azure-key-vault"></a>Usar o Cofre de Chaves do Azure
Após a [instalação da solução](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), consulte os dados do Key Vault clicando no bloco **Azure Key Vault** na página **Visão geral** do Log Analytics.

![imagem do bloco Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Depois de clicar no bloco **Visão Geral**, você pode exibir resumos dos seus logs e então aprofundar-se nos detalhes das seguintes categorias:

* Volume de todas as operações do Cofre de Chaves ao longo do tempo
* Volumes de operação com falha ao longo do tempo
* Latência operacional média por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1.000 ms e uma lista das operações que levam mais de 1.000 ms

![imagem do painel Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![imagem do painel Cofre de Chaves do Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para exibir detalhes de qualquer operação
1. Na página **Visão Geral**, clique no bloco **Cofre de Chaves do Azure**.
2. Na painel **Cofre de Chaves do Azure**, examine as informações resumidas em uma das folhas e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página pesquisa de log.

    Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="log-analytics-records"></a>Registros do Log Analytics
A solução de Cofre de Chaves do Azure analisa os registros que têm um tipo de **KeyVaults** que são coletados de [logs de AuditEvent](../key-vault/key-vault-logging.md) no Diagnóstico do Azure.  As propriedades desses registros são descritas na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*AzureDiagnostics* |
| SourceSystem |*As tabelas* |
| CallerIpAddress |Endereço IP do cliente que fez a solicitação |
| Categoria | *AuditEvent* |
| CorrelationId |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| DurationMs |Tempo necessário para atender à solicitação da API REST, em milissegundos. Esse tempo não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| httpStatusCode_d |Código de status HTTP retornado pela solicitação (por exemplo, *200*) |
| id_s |ID exclusiva da solicitação |
| identity_claim_appid_g | GUID para a ID do aplicativo |
| OperationName |Nome da operação conforme documentado no [Registro em Log do Cofre de Chaves do Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Versão da API REST solicitada pelo cliente (por exemplo, *2015-06-01*) |
| requestUri_s |O URI da solicitação |
| Recurso |Nome do cofre de chaves |
| ResourceGroup |Grupo de recursos do cofre de chaves |
| ResourceId |ID do Recurso do Gerenciador de Recursos do Azure. Para os logs do Key Vault, isse será a ID do recurso do Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Status do HTTP (por exemplo, *OK*) |
| ResultType |Resultado da solicitação da API REST (por exemplo, *Êxito*) |
| SubscriptionId |A ID da assinatura do Azure que contém o Cofre de Chaves |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrar da solução antiga de Key Vault
Em janeiro de 2017, ocorreu uma mudança na maneira correta de envio de logs do Key Vault para o Log Analytics. Essas alterações oferecem as seguintes vantagens:
+ Os logs são gravados diretamente no Log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menor latência do momento em que os logs são gerados até eles serem disponibilizados no Log Analytics
+ Menos etapas de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para usar a solução atualizada:

1. [Configure o envio do diagnóstico diretamente para o Log Analytics do Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Habilite a solução de Azure Key Vault usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md)
3. Atualizar todas as consultas salvas, painéis ou alertas para usar o novo tipo de dados
  + O tipo mudou de KeyVaults para AzureDiagnostics. Use ResourceType para filtrar os registros do Key Vault.
  - Em vez de: `Type=KeyVaults`, use`Type=AzureDiagnostics ResourceType=VAULTS`
  + Campos: (os nomes de campo diferenciam maiúsculas de minúsculas)
  - Para qualquer campo que tenha um sufixo de \_s, \_d ou \_g no nome, altere o primeiro caractere para minúsculo
  - Para qualquer campo que tenha um sufixo de \_o no nome, os dados são divididos em campos individuais com base nos nomes de campos aninhados. Por exemplo, o UPN do chamador é armazenado em um campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - O campo CallerIpAddress mudou para CallerIPAddress
   - O campo RemoteIPCountry não está mais presente
4. Remova a solução *Análise do Key Vault (preterida)*. Se você estiver usando o PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados coletados antes da alteração não estão visíveis na nova solução. Você pode continuar a consultar esses dados usando os nomes de campo e tipo antigos.

## <a name="troubleshooting"></a>Solucionar problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Próximas etapas
* Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Cofre de Chaves do Azure.
