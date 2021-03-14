---
title: Azure Key Vault solução no Azure Monitor | Microsoft Docs
description: Você pode usar a solução Azure Key Vault no Azure Monitor para examinar os logs de Azure Key Vault.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734189"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Solução de análise de Azure Key Vault no Azure Monitor

> [!NOTE]
> Esta solução foi preterida. [Agora é recomendável usar Azure monitor para Key Vault](./key-vault-insights-overview.md).

![Símbolo do Cofre de Chaves](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode usar a solução Azure Key Vault no Azure Monitor para examinar Azure Key Vault logs do AuditEvent.

Para usar a solução, você precisa habilitar o registro em log de diagnóstico do Azure Key Vault e direcionar tal diagnóstico para um espaço de trabalho do Log Analytics. Não é necessário gravar os logs no Armazenamento de Blobs do Azure.

> [!NOTE]
> Em janeiro de 2017, ocorreu uma mudança na maneira correta de envio de logs do Key Vault para o Log Analytics. Se a solução de Key Vault que você está usando mostrar *(preterido)* no título, consulte [Migrar da solução antiga de Key Vault](#migrating-from-the-old-key-vault-solution) para conhecer as etapas que você deve executar.
>
>

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as instruções a seguir para instalar e configurar a solução de Cofre de Chaves do Azure:

1. Use o processo descrito em [Adicionar soluções de Azure monitor da Galeria de soluções](./solutions.md) para adicionar a solução de Azure Key Vault ao espaço de trabalho do log Analytics.
2. Habilitar o registro em log de diagnóstico para os recursos do Key Vault a serem monitorados usando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou o [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Habilitar o diagnóstico de Key Vault no portal

1. No Portal do Azure, navegue até o recurso do Key Vault a ser monitorado
2. Selecione *configurações de diagnóstico* para abrir a página a seguir

   ![Captura de tela da página de configurações de diagnóstico para o recurso de Key Vault ContosoKVSCUS. a opção para ativar o diagnóstico é realçada.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique em *Ativar diagnóstico* para abrir a página seguinte

   ![Captura de tela da página para definir as configurações de diagnóstico. As opções para enviar para Log Analytics, log AuditEvent e Biometria são selecionadas.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Dê um nome para a configuração de diagnóstico.
5. Clique na caixa de seleção para *Enviar para o Log Analytics*
6. Selecione um espaço de trabalho do Log Analytics existente ou crie um espaço de trabalho
7. Para habilitar logs do *AuditEvent*, clique na caixa de seleção sob o Log
8. Clique em *salvar* para habilitar o log de diagnósticos para log Analytics espaço de trabalho.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Habilitar o diagnóstico do Key Vault usando o PowerShell
O script do PowerShell a seguir fornece um exemplo de como usar o `Set-AzDiagnosticSetting` para habilitar o log de recursos para Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Veja os detalhes da coleta de dados do Cofre de Chaves do Azure
A solução do Azure Key Vault coleta logs de diagnóstico diretamente do Key Vault.
Não é necessário gravar os logs no Armazenamento de Blobs do Azure e nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Cofre de Chaves do Azure.

| Plataforma | Agente direto | Agente do Systems Center Operations Manager | Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | na chegada |

## <a name="use-azure-key-vault"></a>Usar o Cofre de Chaves do Azure
Depois de [instalar a solução](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview), exiba os dados de Key Vault clicando no bloco **análise do Key Vault** na página **visão geral** Azure monitor. Abra essa página do **Azure Monitor** menu clicando em **Mais** na seção **Insights**. 

![Captura de tela do bloco Análise do Key Vault na página de visão geral Azure Monitor mostrando um grafo do volume de operações do cofre de chaves ao longo do tempo.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Depois de clicar no bloco **análise do Key Vault** , você poderá exibir resumos de seus logs e, em seguida, analisar os detalhes para as seguintes categorias:

* Volume de todas as operações do Cofre de Chaves ao longo do tempo
* Volumes de operação com falha ao longo do tempo
* Latência operacional média por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1.000 ms e uma lista das operações que levam mais de 1.000 ms

![Captura de tela do painel de Azure Key Vault mostrando blocos com dados gráficos para todas as operações, operações com falha e latência operacional média.](media/azure-key-vault/log-analytics-keyvault01.png)

![Captura de tela do painel de Azure Key Vault mostrando blocos com dados para latência operacional média, qualidade de serviço e pesquisas recomendadas.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para exibir detalhes de qualquer operação
1. Na página **visão geral** , clique no bloco **análise do Key Vault** .
2. Na painel **Cofre de Chaves do Azure**, examine as informações resumidas em uma das folhas e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página pesquisa de log.

    Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor
A solução de Cofre de Chaves do Azure analisa os registros que têm um tipo de **KeyVaults** que são coletados de [logs de AuditEvent](../../key-vault/general/logging.md) no Diagnóstico do Azure.  As propriedades desses registros são descritas na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Endereço IP do cliente que fez a solicitação |
| `Category` | *AuditEvent* |
| `CorrelationId` |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| `DurationMs` |Tempo necessário para atender à solicitação da API REST, em milissegundos. Esse tempo não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| `httpStatusCode_d` |Código de status HTTP retornado pela solicitação (por exemplo, *200*) |
| `id_s` |ID exclusiva da solicitação |
| `identity_claim_appid_g` | GUID para a ID do aplicativo |
| `OperationName` |Nome da operação conforme documentado no [Registro em Log do Cofre de Chaves do Azure](../../key-vault/general/logging.md) |
| `OperationVersion` |Versão da API REST solicitada pelo cliente (por exemplo, *2015-06-01*) |
| `requestUri_s` |O URI da solicitação |
| `Resource` |Nome do cofre de chaves |
| `ResourceGroup` |Grupo de recursos do cofre de chaves |
| `ResourceId` |ID do Recurso do Gerenciador de Recursos do Azure. Para os logs do Key Vault, isse será a ID do recurso do Key Vault. |
| `ResourceProvider` |*O. KEYVAULT* |
| `ResourceType` | *COFRES* |
| `ResultSignature` |Status do HTTP (por exemplo, *OK*) |
| `ResultType` |Resultado da solicitação da API REST (por exemplo, *Êxito*) |
| `SubscriptionId` |A ID da assinatura do Azure que contém o Cofre de Chaves |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrar da solução antiga de Key Vault
Em janeiro de 2017, ocorreu uma mudança na maneira correta de envio de logs do Key Vault para o Log Analytics. Essas alterações oferecem as seguintes vantagens:
+ Os logs são gravados diretamente em um espaço de trabalho Log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menor latência do momento em que os logs são gerados até eles serem disponibilizados no Log Analytics
+ Menos etapas de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para usar a solução atualizada:

1. [Configurar o diagnóstico para ser enviado diretamente a um Log Analytics espaço de trabalho de Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Habilite a solução de Azure Key Vault usando o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](./solutions.md)
3. Atualizar todas as consultas salvas, painéis ou alertas para usar o novo tipo de dados
   + O tipo mudou de KeyVaults para AzureDiagnostics. Use ResourceType para filtrar os logs do Key Vault.
   + Em vez de: `KeyVaults`, use`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Campos: (os nomes de campo diferenciam maiúsculas de minúsculas)
   + Para qualquer campo que tenha um sufixo de \_s, \_d ou \_g no nome, altere o primeiro caractere para minúsculo
   + Para qualquer campo que tenha um sufixo de \_o no nome, os dados são divididos em campos individuais com base nos nomes de campos aninhados. Por exemplo, o UPN do chamador é armazenado em um campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + O campo CallerIpAddress mudou para CallerIPAddress
   + O campo RemoteIPCountry não está mais presente
4. Remova a solução *Análise do Key Vault (preterida)*. Se você estiver usando o PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados coletados antes da alteração não estão visíveis na nova solução. Você pode continuar a consultar esses dados usando os nomes de campo e tipo antigos.

## <a name="troubleshooting"></a>Solução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Próximas etapas
* Use [consultas de log em Azure monitor](../logs/log-query-overview.md) para exibir dados detalhados de Azure Key Vault.

