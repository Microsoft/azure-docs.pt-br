---
title: Gerenciar o acesso ao Azure Sentinel data por recurso | Microsoft Docs
description: Este artigo explica como você pode gerenciar o acesso ao Azure Sentinel data pelos recursos que um usuário pode acessar. O gerenciamento do acesso por recurso permite que você forneça acesso somente a dados específicos, sem toda a experiência do Azure Sentinel. Esse método também é conhecido como RBAC de contexto de recurso.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044168"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Gerenciar o acesso ao Azure sentinela de dados por recurso

Normalmente, os usuários que têm acesso a um espaço de trabalho do Azure Sentinel também têm acesso a todos os dados do espaço de trabalho, incluindo o conteúdo de segurança. Os administradores podem usar as [funções do Azure](roles.md) para configurar o acesso a recursos específicos no Azure Sentinel, dependendo dos requisitos de acesso em sua equipe.

No entanto, você pode ter alguns usuários que precisam acessar apenas dados específicos em seu espaço de trabalho do Azure Sentinel, mas não devem ter acesso a todo o ambiente do Azure Sentinel. Por exemplo, você pode desejar fornecer uma equipe de operações sem segurança (não SOC) com acesso aos dados de eventos do Windows para os servidores que eles possuem.

Nesses casos, recomendamos que você configure seu RBAC (controle de acesso baseado em função) com base nos recursos que são permitidos para seus usuários, em vez de fornecer acesso ao espaço de trabalho do Azure Sentinel ou a recursos específicos do Azure Sentinel. Esse método também é conhecido como configuração do **RBAC de contexto de recurso**.

Quando os usuários têm acesso ao Azure sentinela de dados por meio dos recursos que eles podem acessar em vez do espaço de trabalho do Azure Sentinel, eles podem exibir logs e pastas de trabalho usando os seguintes métodos:

- **Por meio do próprio recurso**, como uma máquina virtual do Azure. Use este método para exibir logs e pastas de trabalho somente para um recurso específico.

- **Via Azure monitor**. Use esse método quando desejar criar consultas que abranjam vários recursos e/ou grupos de recursos. Ao navegar para logs e pastas de trabalho no Azure Monitor, defina o escopo para um ou mais grupos de recursos ou recursos específicos.

Habilite o RBAC de contexto de recurso no Azure Monitor. Para obter mais informações, consulte [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Se seus dados não forem um recurso do Azure, como dados de syslog, CEF ou AAD, ou dados coletados por um coletor personalizado, você precisará configurar manualmente a ID de recurso usada para identificar os dados e habilitar o acesso.
>
> Para obter mais informações, consulte [configurar explicitamente o RBAC de contexto de recurso](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Cenários para RBAC de contexto de recurso

A tabela a seguir realça os cenários em que o RBAC de contexto de recurso é mais útil. Observe as diferenças nos requisitos de acesso entre as equipes do SOC e as equipes não SOC.

| Tipo de requisito |Equipe do SOC  |Equipe não SOC  |
|---------|---------|---------|
|**Permissões**     | Todo o espaço de trabalho        |   Somente recursos específicos      |
|**Acesso a dados**     |  Todos os dados no espaço de trabalho       | Somente os dados de recursos que a equipe tem autorização para acessar        |
|**Experiência**     |  A experiência completa do Azure Sentinel, possivelmente limitada pelas [permissões funcionais](roles.md) atribuídas ao usuário       |  Somente consultas e pastas de trabalho de log       |
|     |         |         |

Se sua equipe tiver requisitos de acesso semelhantes à equipe não SOC descrita na tabela acima, o RBAC de contexto de recurso poderá ser uma boa solução para sua organização.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Métodos alternativos para implementar o RBAC de contexto de recurso

Dependendo das permissões necessárias em sua organização, usar o RBAC de contexto de recurso pode não fornecer uma solução completa.

A lista a seguir descreve os cenários em que outras soluções de acesso a dados podem atender melhor às suas necessidades:

|Cenário  |Solução  |
|---------|---------|
|**Uma subsidiária tem uma equipe SoC que requer uma experiência completa do Azure Sentinel**.     |  Nesse caso, use uma arquitetura de vários espaços de trabalho para separar as permissões de dados. <br><br>Para obter mais informações, consulte: <br>- [Estenda o Azure Sentinel entre espaços de trabalho e locatários](extend-sentinel-across-workspaces-tenants.md)<br>    - [Trabalhe com incidentes em muitos espaços de trabalho ao mesmo tempo](multiple-workspace-view.md)          |
|**Você deseja fornecer acesso a um tipo específico de evento**.     |  Por exemplo, forneça um administrador do Windows com acesso aos eventos de segurança do Windows em todos os sistemas. <br><br>Nesses casos, use o [RBAC em nível de tabela](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) para definir permissões para cada tabela.       |
| **Limitar o acesso a um nível mais granular, não baseado no recurso ou apenas a um subconjunto dos campos em um evento**   |   Por exemplo, talvez você queira limitar o acesso aos logs do Office 365 com base na subsidiária de um usuário. <br><br>Nesse caso, forneça acesso a dados usando a integração interna com [Power bi dashboards e relatórios](../azure-monitor/visualize/powerbi.md).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Configurar explicitamente o RBAC de contexto de recurso

Use as etapas a seguir se desejar configurar o RBAC de contexto de recurso, mas seus dados não são um recurso do Azure.

Por exemplo, os dados em seu espaço de trabalho do Azure Sentinel que não são recursos do Azure incluem dados syslog, CEF ou AAD, ou dados coletados por um coletor personalizado.

**Para configurar explicitamente o RBAC de contexto de recurso**:

1. Certifique-se de que você [habilitou o RBAC de contexto de recurso](../azure-monitor/logs/manage-access.md) em Azure monitor. 

1. [Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md) para cada equipe de usuários que precisa acessar seus recursos sem todo o ambiente do Azure Sentinel.

    Atribua [permissões de leitor de log](../azure-monitor/logs/manage-access.md#resource-permissions) para cada membro da equipe.

1. Atribua recursos aos grupos da equipe de recursos que você criou e marque os eventos com as IDs de recurso relevantes.

    Quando os recursos do Azure enviam dados para o Azure Sentinel, os registros de log são automaticamente marcados com a ID de recurso da fonte de dados.

    > [!TIP]
    > É recomendável agrupar os recursos para os quais você está concedendo acesso em um grupo de recursos específico criado para fins de uso.
    >
    > Se você não puder, certifique-se de que sua equipe tenha permissões de leitor de log diretamente para os recursos que você deseja que eles acessem.
    >

    Para obter mais informações sobre IDs de recurso, consulte:

    - [IDs de recurso com encaminhamento de log](#resource-ids-with-log-forwarding)
    - [IDs de recurso com a coleção Logstash](#resource-ids-with-logstash-collection)
    - [IDs de recurso com a coleção de API Log Analytics](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>IDs de recurso com encaminhamento de log

Quando os eventos são coletados usando o [formato de evento comum (CEF)](connect-common-event-format.md) ou [syslog](connect-syslog.md), o encaminhamento de log é usado para coletar eventos de vários sistemas de origem.

Por exemplo, quando um CEF ou uma VM de encaminhamento de syslog escuta as fontes que enviam eventos de syslog e as encaminha para o Azure Sentinel, a ID de recurso da VM de encaminhamento de log é atribuída a todos os eventos que eles encaminham.

Se você tiver várias equipes, certifique-se de que você tenha VMs de encaminhamento de log separadas processando os eventos para cada equipe separada.

Por exemplo, separar suas VMs garante que os eventos de syslog que pertencem à equipe A sejam coletados usando a VM do coletor A.

> [!TIP]
> - Ao usar uma VM local ou outra VM de nuvem, como AWS, como encaminhador de log, verifique se ela tem uma ID de recurso implementando o [arco do Azure](../azure-arc/servers/overview.md).
> - Para dimensionar o ambiente de VM de encaminhamento de log, considere criar um [conjunto de dimensionamento de VM](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) para coletar os logs de CEF e Sylog.


### <a name="resource-ids-with-logstash-collection"></a>IDs de recurso com a coleção Logstash

Se você estiver coletando seus dados usando o [plug-in de saída Logstash](connect-logstash.md)do Azure Sentinel, use o campo **azure_resource_id** para configurar seu coletor personalizado para incluir a ID do recurso na saída.

Se você estiver usando o RBAC de contexto de recurso e quiser que os eventos coletados pela API estejam disponíveis para usuários específicos, use a ID de recurso do grupo de recursos que você [criou para os usuários](#explicitly-configure-resource-context-rbac).

Por exemplo, o código a seguir mostra um exemplo de arquivo de configuração Logstash:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Talvez você queira adicionar várias `output` seções para diferenciar as marcas aplicadas a diferentes eventos.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>IDs de recurso com a coleção de API Log Analytics

Ao coletar usando a [API do coletor de dados log Analytics](../azure-monitor/logs/data-collector-api.md), você pode atribuir a eventos com uma ID de recurso usando o cabeçalho de solicitação HTTP [*x-MS-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) .

Se você estiver usando o RBAC de contexto de recurso e quiser que os eventos coletados pela API estejam disponíveis para usuários específicos, use a ID de recurso do grupo de recursos que você [criou para os usuários](#explicitly-configure-resource-context-rbac).


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [permissões no Azure Sentinel](roles.md).