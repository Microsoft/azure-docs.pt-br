---
title: Migrar um Azure Monitor Application Insights recurso clássico para um recurso baseado em espaço de trabalho | Microsoft Docs
description: Saiba mais sobre as etapas necessárias para atualizar seu Azure Monitor Application Insights recurso clássico para o novo modelo baseado em espaço de trabalho.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584254"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrar para recursos de Application Insights baseados em espaço de trabalho

Este guia explicará o processo de migração de um recurso de Application Insights clássico para um recurso baseado em espaço de trabalho. Os recursos baseados em espaço de trabalho dão suporte à integração completa entre Application Insights e Log Analytics. Os recursos baseados em espaço de trabalho enviam Application Insights telemetria para um espaço de trabalho Log Analytics comum, que permite que você acesse [os recursos mais recentes do Azure monitor](#new-capabilities) ao mesmo tempo em que mantém os logs de aplicativos, infraestrutura e plataforma em um único local consolidado.

Recursos baseados em espaço de trabalho habilita o Azure RBAC (controle de acesso baseado em função) comum em seus recursos e elimina a necessidade de consultas entre aplicativos/espaços de trabalho.

**Os recursos baseados em espaço de trabalho estão disponíveis atualmente em todas as regiões comerciais e no governo dos EUA do Azure**

## <a name="new-capabilities"></a>Novos recursos

Os Application Insights baseados em espaço de trabalho permitem aproveitar todos os recursos mais recentes de Azure Monitor e Log Analytics, incluindo:

* [As chaves gerenciadas pelo cliente (CMK)](../logs/customer-managed-keys.md) fornecem criptografia em repouso para seus dados com chaves de criptografia somente às quais você tem acesso.
* O [Link Privado do Azure](../logs/private-link-security.md) permite vincular com segurança os serviços PaaS do Azure a sua rede virtual usando pontos de extremidade privados.
* [Traga seu próprio armazenamento (BYOS) para o criador de perfil e depurador de instantâneos](./profiler-bring-your-own-storage.md) oferece controle total sobre a política de criptografia em repouso, a política de gerenciamento de tempo de vida e o acesso à rede para todos os dados associados a Application Insights Profiler e depurador de instantâneos. 
* As [camadas de reserva de capacidade](../logs/manage-cost-storage.md#pricing-model) permitem que você economize até 25% em comparação com o preço pago conforme o uso. 
* Ingestão de dados mais rápida por meio de Log Analytics ingestão de streaming.

## <a name="migration-process"></a>Processo de migração

Quando você migra para um recurso baseado em espaço de trabalho, nenhum dado é transferido do armazenamento do seu recurso clássico para o novo armazenamento baseado em espaço de trabalho. Optar por migrar alterará o local em que os novos dados são gravados em um espaço de trabalho Log Analytics enquanto preserva o acesso aos dados do recurso clássico. 

Seus dados de recursos clássicos serão mantidos e estarão sujeitos às configurações de retenção em seu recurso de Application Insights clássico. Todos os novos dados ingeridos após a migração estarão sujeitos às [configurações de retenção](../logs/manage-cost-storage.md#change-the-data-retention-period) do espaço de trabalho log Analytics associado, que também oferece suporte a [diferentes configurações de retenção por tipo de dados](../logs/manage-cost-storage.md#retention-by-data-type).
O processo de migração é **permanente e não pode ser revertido**. Depois de migrar um recurso para Application Insights com base no espaço de trabalho, ele sempre será um recurso baseado em espaço de trabalho. No entanto, depois de migrar, você poderá alterar o espaço de trabalho de destino sempre que necessário. 

> [!NOTE]
> A ingestão de dados e a retenção de recursos de Application Insights baseados em espaço de trabalho são [cobradas por meio do espaço de trabalho log Analytics](../logs/manage-cost-storage.md) onde os dados estão localizados. Se você tiver selecionado a retenção de dados com mais de 90 dias nos dados ingeridos no recurso de Application Insights clássico antes da migração, a retenção de dados continuará a ser cobrada por meio desse recurso de Application Insights. [Saiba mais]( ./pricing.md#workspace-based-application-insights) sobre a cobrança de recursos do Application Insights baseados em espaço de trabalho.

Se você não precisar migrar um recurso existente e, em vez disso, quiser criar um novo recurso de Application Insights baseado no espaço de trabalho, use o [Guia de criação de recursos baseado em espaço de trabalho](create-workspace-resource.md).

## <a name="pre-requisites"></a>Pré-requisitos 

- Um espaço de trabalho Log Analytics com o modo de controle de acesso definido como a **`use resource or workspace permissions`** configuração. 

    - Os recursos de Application Insights baseados em espaço de trabalho não são compatíveis com espaços de trabalho definidos para a **`workspace based permissions`** configuração dedicada. Para saber mais sobre Log Analytics controle de acesso do espaço de trabalho, consulte o [log Analytics diretrizes para configurar o modo de controle de acesso](../logs/manage-access.md#configure-access-control-mode)

    - Se você ainda não tiver um espaço de trabalho do Log Analytics, [confira a documentação de criação de espaço de trabalho do Log Analytics](../logs/quick-create-workspace.md).
    
- A exportação contínua não tem suporte para recursos baseados em espaço de trabalho e deve ser desabilitada.
Quando a migração for concluída, você poderá usar [as configurações de diagnóstico](../essentials/diagnostic-settings.md) para configurar o arquivamento de dados para uma conta de armazenamento ou streaming para o Hub de eventos do Azure.  

- Verifique suas configurações de retenção atuais em uso **geral**  >  e a retenção de dados de **custos estimados**  >   para seu espaço de trabalho log Analytics. Essa configuração afetará por quanto tempo todos os novos dados ingeridos serão armazenados depois que você migrar o recurso de Application Insights. Se atualmente você armazena dados Application Insights por mais tempo do que o padrão de 90 dias e deseja manter esse período de retenção maior, talvez seja necessário ajustar as configurações de retenção do espaço de trabalho.

## <a name="migrate-your-resource"></a>Migrar seu recurso

Esta seção explicará o processo de migração de um recurso de Application Insights clássico para o novo tipo de recurso baseado em espaço de trabalho.

1. Em seu recurso de Application Insights, selecione **Propriedades** no título **Configurar** na barra de menus à esquerda.

    ![Propriedades realçadas na caixa vermelha](./media/convert-classic-resource/properties.png)

2. Selecione **`Migrate to Workspace-based`**.
    
     ![Botão migrar recurso](./media/convert-classic-resource/migrate.png)

3. Escolha o espaço de trabalho Log Analytics onde você deseja que todas as telemetrias de Application Insights ingeridas futuras sejam armazenadas.

     ![Interface do usuário do assistente de migração com opção para selecionar o espaço de trabalho des](./media/convert-classic-resource/migration.png)
    

Depois que o recurso for migrado, você verá as informações de espaço de trabalho correspondentes no painel **visão geral** :

![Nome do espaço de trabalho](./media/create-workspace-resource/workspace-name.png)

Clique no texto do link azul para ir para o espaço de trabalho do Log Analytics associado, onde você poderá aproveitar o novo ambiente unificado de consulta de espaço de trabalho.

## <a name="understanding-log-queries"></a>Noções básicas sobre consultas de log

Ainda fornecemos compatibilidade total com versões anteriores para as consultas de recursos clássicos, as pastas de trabalho e os alertas baseados em log do Application Insights na experiência do Application Insights. 

Para gravar consultas em relação à [nova estrutura/esquema de tabela baseada em espaço de trabalho](apm-tables.md), você deve primeiro navegar para seu espaço de trabalho log Analytics. 

Ao consultar diretamente da interface do usuário do Log Analytics em seu espaço de trabalho, você verá apenas os dados que são ingeridos após a migração. Para ver os dados clássicos Application Insights + novos dados ingeridos após a migração em uma experiência de consulta unificada, use a exibição de consulta logs (análise) de dentro do recurso de Application Insights migrado.

## <a name="programmatic-resource-migration"></a>Migração de recursos programático

### <a name="azure-cli"></a>CLI do Azure

Para acessar a versão prévia dos comandos da CLI do Azure do Application Insights, primeiro você precisa executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar o comando `az extension add`, verá a seguinte mensagem de erro: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar o recurso do Application Insights:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Para obter a documentação completa de CLI do Azure para este comando, confira a [documentação CLI do Azure](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

O `Update-AzApplicationInsights` comando do PowerShell atualmente não dá suporte à migração de um recurso de Application insights clássico para o baseado em espaço de trabalho. Para criar um recurso baseado em espaço de trabalho com o PowerShell, você poderá usar os modelos do Azure Resource Manager abaixo e implantar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modificação do espaço de trabalho associado

Depois que um recurso do Application Insights baseado em espaço de trabalho tiver sido criado, você poderá modificar o espaço de trabalho associado do Log Analytics.

No painel de recursos Application insights, selecione **Propriedades**  >  **alterar espaço** de trabalho  >  **log Analytics espaços de trabalho**.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="access-mode"></a>Modo de acesso

**Mensagem de erro:** *o espaço de trabalho selecionado está configurado com o modo de acesso baseado no espaço de trabalho. Alguns recursos do APM podem ser afetados. Selecione outro espaço de trabalho ou permita o acesso baseado em recurso nas configurações do espaço de trabalho. Você pode substituir esse erro usando a CLI.* 

Para que o recurso de Application Insights baseado no espaço de trabalho opere corretamente, você precisa alterar o modo de controle de acesso de seu espaço de trabalho de Log Analytics de destino para a configuração **permissões de recurso ou espaço de trabalho** . Essa configuração está localizada na interface do usuário do log Analytics espaço de trabalho em **Propriedades**  >  **modo de controle de acesso**. Para obter instruções detalhadas, consulte a [log Analytics configurar a orientação do modo de controle de acesso](../logs/manage-access.md#configure-access-control-mode). Se o modo de controle de acesso for definido como a configuração exclusiva **exigir permissões de espaço de trabalho** , a migração por meio da experiência de migração do portal permanecerá bloqueada.

Se você não puder alterar o modo de controle de acesso por motivos de segurança para seu espaço de trabalho de destino atual, é recomendável criar um novo espaço de trabalho Log Analytics para usar para a migração. 

### <a name="continuous-export"></a>Exportação contínua

**Mensagem de erro:** *a exportação contínua precisa ser desabilitada antes de continuar. Após a migração, use as configurações de diagnóstico para exportar.* 

A funcionalidade de exportação contínua herdada não tem suporte para recursos baseados em espaço de trabalho. Antes da migração, você precisa desabilitar a exportação contínua.

1. Na exibição de recurso Application Insights, no cabeçalho **Configurar** , selecione **exportação contínua**.

    ![Item de menu de exportação contínua](./media/convert-classic-resource/continuous-export.png)

2. Selecione **Desabilitar**.

    ![Botão de desabilitação da exportação contínua](./media/convert-classic-resource/disable.png)

- Depois de selecionar desabilitar, você pode navegar de volta para a interface do usuário de migração. Se a página Editar exportação contínua solicitar que suas configurações não sejam salvas, você poderá selecionar OK para esse prompt, pois ela não pertence à desabilitação/habilitação da exportação contínua.

- Depois de migrar com êxito o recurso de Application Insights para o baseado em espaço de trabalho, você pode usar as configurações de diagnóstico para substituir a funcionalidade que a exportação contínua usou para fornecer. Selecione **configurações de diagnóstico**  >  **Adicionar configuração de diagnóstico** de dentro de seu recurso de Application insights. Você pode selecionar todas as tabelas ou um subconjunto de tabelas para arquivar em uma conta de armazenamento ou transmitir para um hub de eventos do Azure. Para obter orientações detalhadas sobre as configurações de diagnóstico, consulte as [diretrizes de configurações de diagnóstico Azure monitor](../essentials/diagnostic-settings.md).

### <a name="retention-settings"></a>Configurações de retenção

**Mensagem de aviso:** *suas configurações personalizadas de retenção de Application insights não serão aplicadas aos dados enviados para o espaço de trabalho. Você precisará reconfigurar isso separadamente.*

Você não precisa fazer nenhuma alteração antes da migração, mas essa mensagem é para alertá-lo de que suas configurações atuais de retenção de Application Insights não estão definidas para o período de retenção padrão de 90 dias. Essa mensagem de aviso significa que você pode querer modificar as configurações de retenção para seu espaço de trabalho do Log Analytics antes de migrar e começar a ingerir novos dados. 

Você pode verificar suas configurações de retenção atuais para obter log Analytics em uso **geral**  >  **e custos estimados**  >  de **retenção de dados** de dentro da interface do usuário do log Analytics. Essa configuração afetará por quanto tempo todos os novos dados ingeridos serão armazenados depois que você migrar o recurso de Application Insights.

## <a name="next-steps"></a>Próximas etapas

* [Explorar métricas](../essentials/metrics-charts.md)
* [Escrever consultas do Analytics](../logs/log-query-overview.md)
