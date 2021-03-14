---
title: Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes
description: Envie Azure Monitor logs e métricas de plataforma para Azure Monitor logs, armazenamento do Azure ou hubs de eventos do Azure usando uma configuração de diagnóstico.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 254d403adc687074eae772bcdcc55793bb25b336
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048906"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes
Os [logs de plataforma](./platform-logs-overview.md) no Azure, incluindo logs de recursos e log de atividades do Azure, apresentam informações detalhadas de diagnóstico e auditoria para recursos do Azure e para a plataforma do Azure da qual eles dependem. As [métricas da plataforma](./data-platform-metrics.md) são coletadas por padrão e normalmente armazenadas no banco de dados de métricas do Azure Monitor. Este artigo fornece detalhes sobre como criar e definir configurações de diagnóstico para enviar métricas de plataforma e logs de plataforma para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma configuração de diagnóstico para o log de atividades, primeiro você deve desabilitar qualquer configuração herdada. Consulte [métodos de coleta herdados](../essentials/activity-log.md#legacy-collection-methods) para obter detalhes.

Cada recurso do Azure requer sua própria configuração de diagnóstico, que define os seguintes critérios:

- As categorias de logs e os dados de métrica enviados aos destinos definidos na configuração. As categorias disponíveis variam para diferentes tipos de recursos.
- Um ou mais destinos para enviar os logs. Os destinos atuais incluem workspace do Log Analytics, Hubs de Eventos e Armazenamento do Azure.

Uma única configuração de diagnóstico pode definir, no máximo, um de cada um dos destinos. Se você quiser enviar dados para mais de um tipo de destino específico (por exemplo, dois workspaces do Log Analytics diferentes), crie várias configurações. Cada recurso pode ter até cinco configurações de diagnóstico.

O vídeo a seguir orienta você pelos logs da plataforma de roteamento com configurações de diagnóstico.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> As [métricas de plataforma](./metrics-supported.md) são enviadas automaticamente para [Azure monitor métricas](./data-platform-metrics.md). As configurações de diagnóstico podem ser usadas para enviar métricas para determinados serviços do Azure em Azure Monitor logs para análise com outros dados de monitoramento usando [consultas de log](../logs/log-query-overview.md) com determinadas limitações. 
>  
>  
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão. *Por exemplo*: a métrica ' IOReadBytes ' em um Blockchain pode ser explorada e grafada em um nível por nó. No entanto, quando exportadas por meio de configurações de diagnóstico, a métrica exportada representa como todos os bytes de leitura de todos os nós Além disso, devido a limitações internas, nem todas as métricas são exportáveis para Azure Monitor logs/Log Analytics. Para obter mais informações, consulte a [lista de métricas exportáveis](./metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Para contornar essas limitações para métricas específicas, sugerimos extraí-las manualmente usando a [API REST de métricas](/rest/api/monitor/metrics/list) e importá-las em Logs de Azure monitor usando a [API do coletor de dados Azure monitor](../logs/data-collector-api.md).  


## <a name="destinations"></a>Destinos
Os logs e as métricas da plataforma podem ser enviados para os destinos na tabela a seguir. 

| Destino | Descrição |
|:---|:---|
| [Espaço de Trabalho do Log Analytics](../logs/design-logs-deployment.md) | O envio de logs e métricas para um espaço de trabalho Log Analytics permite analisá-los com outros dados de monitoramento coletados por Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos de Azure Monitor, como alertas e visualizações. |
| [Hubs de Eventos](../../event-hubs/index.yml) | O envio de logs e métricas para hubs de eventos permite transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções do log Analytics.  |
| [Conta de Armazenamento do Azure](../../storage/blobs/index.yml) | O arquivamento de logs e métricas para uma conta de armazenamento do Azure é útil para auditoria, análise estática ou backup. Em comparação com os logs de Azure Monitor e um espaço de trabalho Log Analytics, o armazenamento do Azure é menos dispendioso e os logs podem ser mantidos indefinidamente.  |


### <a name="destination-requirements"></a>Requisitos de destino

Todos os destinos para a configuração de diagnóstico devem ser criados antes da criação das configurações de diagnóstico. O destino não precisa estar na mesma assinatura que o recurso que envia os logs, contanto que o usuário que define a configuração tenha o acesso RBAC do Azure apropriado a ambas as assinaturas. A tabela a seguir fornece requisitos exclusivos para cada destino, incluindo quaisquer restrições regionais.

| Destino | Requisitos |
|:---|:---|
| Espaço de trabalho do Log Analytics | O espaço de trabalho não precisa estar na mesma região que o recurso que está sendo monitorado.|
| Hubs de Eventos | A política de acesso compartilhado para o namespace define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciar, enviar e escutar. Para atualizar a configuração de diagnóstico para incluir streaming, você deve ter a permissão ListKey nessa regra de autorização de hubs de eventos.<br><br>O namespace do hub de eventos precisa estar na mesma região que o recurso que está sendo monitorado se o recurso for regional. |
| Conta de Armazenamento do Azure | Você não deve usar uma conta de armazenamento existente que tenha outros dados de não monitoramento armazenados nele para que você possa controlar melhor o acesso aos dados. Se você estiver arquivando o log de atividades e os logs de recursos juntos, poderá optar por usar a mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.<br><br>Para enviar os dados para o armazenamento imutável, defina a política imutável para a conta de armazenamento, conforme descrito em [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](../../storage/blobs/storage-blob-immutability-policies-manage.md). Você deve seguir todas as etapas neste artigo, incluindo a habilitação de gravações de blobs de acréscimo protegidos.<br><br>A conta de armazenamento precisa estar na mesma região que o recurso que está sendo monitorado se o recurso for regional. |

> [!NOTE]
> Atualmente, contas de Azure Data Lake Storage Gen2 não são compatíveis como destino para configurações de diagnóstico, embora elas possam estar listadas como uma opção válida na portal do Azure.

> [!NOTE]
> Azure Monitor (configurações de diagnóstico) não pode acessar recursos de hubs de eventos quando as redes virtuais estão habilitadas. Você precisa habilitar a opção permitir que os serviços confiáveis da Microsoft ignorem essa configuração de firewall no Hub de eventos, para que o serviço de Azure Monitor (configurações de diagnóstico) receba acesso aos seus recursos de hubs de eventos. 


## <a name="create-in-azure-portal"></a>Criar no portal do Azure

Você pode definir as configurações de diagnóstico no portal do Azure no menu Azure Monitor ou no menu do recurso.

1. Onde você define as configurações de diagnóstico no portal do Azure depende do recurso.

   - Para um único recurso, clique em **configurações de diagnóstico** em **Monitor** no menu do recurso.

        ![Captura de tela da seção monitoramento de um menu de recursos no portal do Azure com as configurações de diagnóstico realçadas.](media/diagnostic-settings/menu-resource.png)

   - Para um ou mais recursos, clique em **configurações de diagnóstico** em **configurações** no menu Azure monitor e clique no recurso.

        ![Captura de tela da seção configurações no menu Azure Monitor com configurações de diagnóstico realçadas.](media/diagnostic-settings/menu-monitor.png)

   - Para o log de atividades, clique em **log de atividades** no menu **Azure monitor** e, em seguida, **configurações de diagnóstico**. Certifique-se de desabilitar qualquer configuração herdada para o log de atividades. Consulte [desabilitar configurações existentes](./activity-log.md#legacy-collection-methods) para obter detalhes.

        ![Captura de tela do menu de Azure Monitor com o log de atividades selecionado e as configurações de diagnóstico realçadas na barra de menus do Monitor-Activity log.](media/diagnostic-settings/menu-activity-log.png)

2. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em **Adicionar configuração de diagnóstico**.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-settings/add-setting.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Clique em **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou **Editar configuração** para editar uma existente. Cada configuração não pode ter mais de um de cada um dos tipos de destino.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome à sua configuração se ela ainda não tiver uma.

    ![Adicionar configuração de diagnóstico](media/diagnostic-settings/setting-new-blank.png)

4. **Detalhes da categoria (o que rotear)** – marque a caixa para cada categoria de dados que você deseja enviar para os destinos especificados mais tarde. A lista de categorias varia para cada serviço do Azure.

     - As **biometrias roteiam** as métricas de plataforma de um recurso para o repositório de logs do Azure, mas no formulário de log. Essas métricas geralmente são enviadas somente para o banco de dados de série temporal de Azure Monitor métricas. Enviá-los para o repositório de logs de Azure Monitor (que é pesquisável via Log Analytics) ajuda a integrá-los em consultas que pesquisam em outros logs. Essa opção pode não estar disponível para todos os tipos de recurso. Quando há suporte, [Azure monitor métricas com suporte](./metrics-supported.md) lista as métricas coletadas para quais tipos de recursos.

       > [!NOTE]
       > Consulte limitação para métricas de roteamento para logs de Azure Monitor anteriormente neste artigo.  


     - **Logs** lista as diferentes categorias disponíveis dependendo do tipo de recurso. Verifique todas as categorias que você deseja rotear para um destino.

5. **Detalhes de destino** -marque a caixa para cada destino. Quando você marca cada caixa, as opções aparecem para permitir que você adicione informações adicionais.

      ![Enviar para Log Analytics ou hubs de eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -Insira a assinatura e o espaço de trabalho.  Se você não tiver um espaço de trabalho, precisará [criar um antes de continuar](../logs/quick-create-workspace.md).

    1. **Hubs de eventos** -especifique os seguintes critérios:
       - A assinatura da qual o Hub de eventos faz parte
       - O namespace do hub de eventos-se você ainda não tiver um, você precisará [criar um](../../event-hubs/event-hubs-create.md)
       - Um nome de Hub de eventos (opcional) para o qual enviar todos os dados. Se você não especificar um nome, um hub de eventos será criado para cada categoria de log. Se você estiver enviando várias categorias, talvez queira especificar um nome para limitar o número de hubs de eventos criados. Consulte [limites e cotas dos hubs de eventos do Azure](../../event-hubs/event-hubs-quotas.md) para obter detalhes.
       - Uma política de Hub de eventos (opcional) uma política define as permissões que o mecanismo de streaming tem. Para obter mais informações, consulte [eventos-hubs-recursos](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Armazenamento** -escolha a assinatura, a conta de armazenamento e a política de retenção.

        ![Enviar para o armazenamento](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere definir a política de retenção como 0 e excluir manualmente os dados do armazenamento usando um trabalho agendado para evitar uma possível confusão no futuro.
        >
        > Em primeiro lugar, se você estiver usando o armazenamento para arquivamento, geralmente desejará seus dados por mais de 365 dias. Em segundo lugar, se você escolher uma política de retenção maior que 0, a data de validade será anexada aos logs no momento do armazenamento. Você não pode alterar a data desses logs depois de armazenado.
        >
        > Por exemplo, se você definir a política de retenção para *WorkflowRuntime* como 180 dias e, em seguida, 24 horas depois defini-la como 365 dias, os logs armazenados durante essas primeiras 24 horas serão excluídos automaticamente após 180 dias, enquanto todos os logs subsequentes desse tipo serão excluídos automaticamente após 365 dias. A alteração da política de retenção mais tarde não faz com que as primeiras 24 horas de logs permaneçam por cerca de 365 dias.

6. Clique em **Save** (Salvar).

Após alguns instantes, a nova configuração aparecerá na lista de configurações desse recurso e os logs serão transmitidos para os destinos especificados à medida que novos dados de evento forem gerados. Pode levar até 15 minutos entre o momento em que um evento é emitido e quando ele [aparece em um espaço de trabalho log Analytics](../logs/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Criar usando o PowerShell

Use o cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma configuração de diagnóstico com [Azure PowerShell](../powershell-samples.md). Consulte a documentação deste cmdlet para obter descrições de seus parâmetros.

> [!IMPORTANT]
> Você não pode usar esse método para o log de atividades do Azure. Em vez disso, use a [configuração criar diagnóstico no Azure monitor usando um modelo do Resource Manager](./resource-manager-diagnostic-settings.md) para criar um modelo do Resource Manager e implantá-lo com o PowerShell.

Veja a seguir um exemplo de cmdlet do PowerShell para criar uma configuração de diagnóstico usando todos os três destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Criar usando CLI do Azure

Use o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) para criar uma configuração de diagnóstico com [CLI do Azure](/cli/azure/monitor). Consulte a documentação deste comando para obter descrições de seus parâmetros.

> [!IMPORTANT]
> Você não pode usar esse método para o log de atividades do Azure. Em vez disso, use a [configuração criar diagnóstico no Azure monitor usando um modelo do Resource Manager](./resource-manager-diagnostic-settings.md) para criar um modelo do Resource Manager e implantá-lo com a CLI.

Veja a seguir um exemplo de comando da CLI para criar uma configuração de diagnóstico usando todos os três destinos.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Criar usando o modelo do Resource Manager
Consulte [exemplos de modelo do Resource Manager para configurações de diagnóstico em Azure monitor](./resource-manager-diagnostic-settings.md) para criar ou atualizar configurações de diagnóstico com um modelo do Resource Manager.

## <a name="create-using-rest-api"></a>Criar usando a API REST
Consulte [configurações de diagnóstico](/rest/api/monitor/diagnosticsettings) para criar ou atualizar as configurações de diagnóstico usando a [API REST do Azure monitor](/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Criar usando Azure Policy
Como uma configuração de diagnóstico precisa ser criada para cada recurso do Azure, Azure Policy pode ser usada para criar automaticamente uma configuração de diagnóstico à medida que cada recurso é criado. Consulte [implantar Azure monitor em escala usando Azure Policy](../deploy-scale.md) para obter detalhes.

## <a name="metric-category-is-not-supported-error"></a>O erro de categoria de métrica não é suportado
Ao implantar uma configuração de diagnóstico, você receberá a seguinte mensagem de erro:

   "Não há suporte para a categoria de métrica '*xxxx*'"

Por exemplo:  

   "Não há suporte para a categoria de métrica ' ActionsFailed '"

onde anteriormente sua implantação foi bem-sucedida. 

O problema ocorre ao usar um modelo do Resource Manager, a API REST de configurações de diagnóstico, CLI do Azure ou Azure PowerShell. As configurações de diagnóstico criadas por meio do portal do Azure não são afetadas, pois apenas os nomes de categoria com suporte são apresentados.

O problema é causado por uma alteração recente na API subjacente. Não há suporte para categorias de métrica diferentes de ' biometria ' e nunca foram feitas, exceto, por alguns serviços do Azure muito específicos. No passado, outros nomes de categoria foram ignorados durante a implantação de uma configuração de diagnóstico. O back-end de Azure Monitor simplesmente redirecionava essas categorias para ' Biometrics '.  A partir de fevereiro de 2021, o back-end foi atualizado para confirmar especificamente que a categoria de métrica fornecida é precisa. Essa alteração causou a falha de algumas implantações.

Se você receber esse erro, atualize suas implantações para substituir quaisquer nomes de categoria de métricas por ' Biometrics ' para corrigir o problema. Se a implantação tiver sido adicionada anteriormente a várias categorias, somente uma com a referência ' Biometrics ' deverá ser mantida. Se você continuar tendo o problema, entre em contato com o suporte do Azure por meio do portal do Azure. 



## <a name="next-steps"></a>Próximas etapas

- [Leia mais sobre os logs da plataforma Azure](./platform-logs-overview.md)