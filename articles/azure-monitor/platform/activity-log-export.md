---
title: Exportar o Log de atividades do Azure
description: Exporte log de atividades do Azure para o armazenamento do Azure ou arquivamento dos Hubs de eventos de exportação de fora do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248138"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportar log de atividades do Azure para armazenamento ou Hubs de eventos do Azure
O [Log de atividades do Azure](activity-logs-overview.md) fornece ideias sobre eventos de nível de assinatura que ocorreram na sua assinatura do Azure. Além de exibir o log de atividades no portal do Azure ou copiá-lo para um espaço de trabalho do Log Analytics onde ele pode ser analisado com outros dados coletados pelo Azure Monitor, você pode criar um perfil de log para arquivar o log de atividades para uma conta de armazenamento do Azure ou transmiti-lo para um  Hub de eventos.

## <a name="archive-activity-log"></a>Arquivar Log de atividades
O Log de atividades para uma conta de armazenamento de arquivamento é útil se você quiser manter seus dados de log mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisa manter seus eventos por 90 dias ou menos, não é necessário configurar arquivamento em uma conta de armazenamento, já que eventos de Log de atividades são mantidos na plataforma do Azure por 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Log de atividades do Stream para o Hub de eventos
[Os Hubs de evento](/azure/event-hubs/) é um serviço de ingestão de plataforma e eventos que pode receber o fluxo de dados e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Duas maneiras que você pode usar o recurso de streaming para o Log de atividades são:
* **Transmitir para sistemas de telemetria e registro em log de terceiros**: Ao longo do tempo, a transmissão de Hubs de Eventos do Azure se tornará o mecanismo para direcionar seu Log de Atividades para SIEMs e soluções de análise de logs de terceiros.
* **Criar uma plataforma de registro em log e telemetria personalizada**: se você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a ingestão flexível de log de atividades. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="storage-account"></a>Conta de armazenamento
Se você estiver arquivando o Log de atividades, você precisará [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) se você ainda não tiver um. Você não deve usar uma conta de armazenamento existente que tem dados de outros, sem monitoramento armazenados nela, para que você pode controlar melhor o acesso aos dados de monitoramento. Se você estiver arquivando também os Logs de diagnóstico e métricas para uma conta de armazenamento no entanto, você pode optar por usar a mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

A conta de armazenamento não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.
> [!NOTE]
>  Atualmente, você não pode arquivar dados em uma conta de armazenamento que esteja atrás de uma rede virtual protegida.

### <a name="event-hubs"></a>Hubs de Eventos
Se você estiver enviando seu Log de atividades para um hub de eventos, você precisará [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se você ainda não tiver um. Se você distribuiu anteriormente eventos de Log de atividades para esse namespace de Hubs de eventos, hub de eventos será reutilizado.

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. Streaming para Hubs de eventos exige permissões de gerenciar, enviar e escutar. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de Hubs de eventos no portal do Azure, na guia Configurar para seu namespace de Hubs de eventos.

Para atualizar o perfil de log do Log de atividades para incluir o streaming, você deve ter a permissão ListKey nessa regra de autorização de Hubs de eventos. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

Stream do Log de atividades para um Hub de eventos por [criando um perfil de Log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Criar um perfil de log
Você define como o seu log de atividades do Azure é exportado usando um **perfil de log**. Cada assinatura do Azure pode ter somente um perfil de log. Essas configurações podem ser definidas por meio de **exportar** opção na folha do Log de atividades no portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI.

O perfil de log define o seguinte.

**Onde o Log de atividades devem ser enviado.** Atualmente, as opções disponíveis são a conta de armazenamento ou Hubs de eventos.

**Quais categorias de evento devem ser enviadas.** O significado dos *categoria* no Log de atividades e perfis de Log de eventos é diferente. No perfil de Log, *categoria* representa o tipo de operação (gravação, exclusão, ação). Em um evento de Log de atividades, o *categoria*"* propriedade representa a origem ou o tipo de evento (por exemplo, administração, ServiceHealth e alerta).

**Quais regiões (locais) devem ser exportados.** Você deve incluir todos os locais, pois muitos eventos no Log de atividades são eventos globais.

**Quanto tempo o Log de atividades deve ser mantido em uma conta de armazenamento.** Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.

Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma conta de armazenamento está desabilitado, em seguida, as políticas de retenção não têm efeito. As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. A exclusão começa à meia-noite UTC, mas observe que pode levar até 24 horas para que os logs sejam excluídos da conta de armazenamento.



> [!WARNING]
> O formato dos dados de log na conta de armazenamento será alterado para Linhas JSON em 1º de novembro de 2018. [Confira este artigo para obter uma descrição do impacto e saber como atualizar suas ferramentas para manipular o novo formato.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de log usando o portal do Azure

Criar ou editar um perfil de log com o **exportar para o Hub de eventos** opção no portal do Azure.

1. Dos **Monitor** menu no portal do Azure, selecione **exportar para o Hub de eventos**.

    ![Botão Exportar no portal](media/activity-log-export/portal-export.png)

3. Na folha que aparece, especifique o seguinte:
   * Regiões com os eventos a serem exportados. Você deve selecionar todas as regiões para garantir que você não perca a eventos importantes, pois o Log de atividades é um log global de (não regional) e, portanto, a maioria dos eventos não têm uma região associada a eles. 
   * Se você quiser escrever a conta de armazenamento:
       * A conta de armazenamento para o qual você gostaria de salvar os eventos.
       * o número de dias que você deseja manter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
   * Se você quiser gravar no hub de eventos:
       * o Namespace de barramento de serviço no qual você gostaria de um Hub de eventos a serem criados para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.


### <a name="configure-log-profile-using-powershell"></a>Configurar perfil de log usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se um perfil de log já existir, você precisa primeiro remover o perfil de log existente e, em seguida, crie um novo.

1. Use `Get-AzLogProfile` para identificar se já existe um perfil de log.  Se existir um perfil de log, observe as *nome* propriedade.

1. Use `Remove-AzLogProfile` para remover o perfil de log usando o valor da propriedade *nome*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para criar um novo perfil de log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Obrigatório | DESCRIÇÃO |
    | --- | --- | --- |
    | Name |Sim |Nome de seu perfil de log. |
    | StorageAccountId |Não |ID do recurso da conta de armazenamento onde o Log de atividades deve ser salvo. |
    | serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Isso é uma cadeia de caracteres com o formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
    | RetentionInDays |Sim |Número de dias para os quais eventos devem ser mantidos na conta de armazenamento, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente. |
    | Category |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são _escrever_, _excluir_, e _ação_. |

### <a name="example-script"></a>Script de exemplo
A seguir está um exemplo de script do PowerShell para criar um perfil de log que grava o Log de atividades em ambos os um armazenamento conta e hub de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurar perfil de log usando a CLI do Azure

Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `az monitor log-profiles list` para identificar se já existe um perfil de log.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *nome*.
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Obrigatório | DESCRIÇÃO |
    | --- | --- | --- |
    | name |Sim |Nome de seu perfil de log. |
    | storage-account-id |Sim |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
    | locations |Sim |Lista separada por espaço de regiões para as quais você gostaria de coletar eventos do Log de Atividades. É possível exibir uma lista de todas as regiões para a assinatura usando `az account list-locations --query [].name`. |
    | days |Sim |Número de dias para quais eventos devem ser mantidos, entre 1 e 365. Um valor de zero armazenará os logs indefinidamente (para sempre).  Se zero, o parâmetro habilitado deverá ser definido como verdadeiro. |
    |enabled | Sim |Verdadeiro ou falso.  Usado para habilitar ou desabilitar a política de retenção.  Se for Verdadeiro, o parâmetro de dias deverá ser um valor maior que 0.
    | categories |Sim |Lista separada por espaço de categorias de eventos que devem ser coletadas. Os valores possíveis são Gravação, Exclusão e Ação. |



## <a name="activity-log-schema"></a>Esquema de log de atividades
Se enviado para o armazenamento do Azure ou Hub de eventos, os dados de log de atividade serão gravados em JSON com o seguinte formato.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
Os elementos neste JSON são descritos na tabela a seguir.

| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| time |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| resourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| category |Categoria da ação, por exemplo, Gravação, Leitura e Ação. |
| resultType |O tipo do resultado, por exemplo, Êxito, Falha e Início |
| resultSignature |Depende do tipo de recurso. |
| durationMs |Duração da operação em milissegundos |
| callerIpAddress |Endereço IP do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| correlationId |Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| identity |Blob JSON que descreve a autorização e as declarações. |
| authorization |Blob de propriedades RBAC do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| level |Nível do evento. Um dos seguintes valores: _Críticos_, _erro_, _aviso_, _informativa_, e _detalhado_ |
| location |Região na qual ocorreu o local (ou global). |
| properties |Conjunto de pares de `<Key, Value>` (ou seja, Dicionário) que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.



## <a name="next-steps"></a>Próximas etapas

* [Leia mais sobre o Log de Atividades](../../azure-resource-manager/resource-group-audit.md)
* [Coletar Log de atividades em Logs do Azure Monitor](activity-log-collect.md)
