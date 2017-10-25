---
title: "Visão geral do Log de Atividades do Azure | Microsoft Docs"
description: "Saiba o que é o Log de Atividades do Azure e como usá-lo para compreender os eventos que ocorrem dentro de sua assinatura do Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem
ms.openlocfilehash: 8ff9f73fc0732cd2227b7e0cc1091e04d69014eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorar a atividade da assinatura com o Log de Atividades do Azure
O **Log de Atividades do Azure** é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de dados operacionais do Azure Resource Manager para atualizações em eventos de Integridade do Serviço. O Log de Atividades era conhecido como "Logs de Auditoria" ou "Logs Operacionais", já que a categoria Administrativa relata eventos de plano de controle de suas assinaturas. Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo Clássico/"RDFE".

![Logs de Atividade X outros tipos de logs ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Figura 1: Logs de Atividade X outros tipos de logs

O Log de Atividades difere dos [Logs de Diagnóstico](monitoring-overview-of-diagnostic-logs.md). Os Logs de Atividade fornecem dados sobre as operações em um recurso externo (o “plano de controle”). Os Logs de Diagnóstico são emitidos por um recurso e fornecem informações sobre a operação do recurso (o “plano de dados”).

Você pode recuperar os eventos de seu Log de Atividade usando o Portal do Azure, a CLI, cmdlets do PowerShell e a API REST do Azure Monitor.


> [!WARNING]
> O Log de Atividades do Azure é usado principalmente para atividades que ocorrem no Azure Resource Manager. Ele não controla os recursos usando o modelo Clássico/RDFE. Alguns tipos de recursos Clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se você interagir com um tipo de recurso Clássico por meio do Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no Log de Atividades. Se você interagir com um tipo de recurso clássico no portal Clássico ou fora dos proxies do Azure Resource Manager, suas ações somente serão registradas no Log de Operação. O Log de Operação só pode ser acessado no portal Clássico.
>
>

Exiba o vídeo de introdução do Log de Atividades a seguir.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>Categorias no Log de Atividades
O Log de Atividades contém várias categorias de dados. Para obter todos os detalhes sobre o esquema dessas categorias, [veja este artigo](monitoring-activity-log-schema.md). Estão incluídos:
* **Administrativos** -essa categoria contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Os exemplos dos tipos de eventos que você vê nessa categoria incluem "criar máquina virtual" e "excluir grupo de segurança". Cada ação tomada por um usuário ou um aplicativo usando o Resource Manager é modelada como uma operação em um tipo de recurso específico. Se o tipo de operação for Gravação, Exclusão ou Ação, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. A categoria administrativa também inclui alterações de controle de acesso baseado em função em uma assinatura.
* **Integridade do Serviço** - essa categoria contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo do tipo de evento que você vê nessa categoria é "SQL Azure no Leste dos EUA está passando por tempo de inatividade". Os eventos de serviço de integridade são fornecidos em cinco variedades: Ação Necessária, Recuperação Assistida, Incidente, Manutenção, Informações ou Segurança, e só aparecerão se você tiver um recurso na assinatura que seria afetada pelo evento.
* **Alerta** - esta categoria contém o registro de todas as ativações de alertas do Azure. Um exemplo do tipo de evento que você vê nessa categoria é "% de CPU em myVM foi 80 nos últimos 5 minutos." Uma variedade de sistemas do Azure têm um conceito de alerta – você pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem a essa regra. Cada vez que um tipo de alerta com suporte do Azure é 'ativado', ou as condições são atendidas para gerar uma notificação, um registro de ativação também é enviado para essa categoria de Log de Atividades.
* **Dimensionamento automático** -essa categoria contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo do tipo de evento que você veria nessa categoria é "Falha na ação de escalar horizontalmente do Dimensionamento Automático". Usando o dimensionamento automático, você pode dimensionar o número de instâncias em um tipo de recurso com suporte com base na hora do dia e/ou dados de carga (métricas) usando uma configuração de dimensionamento automático. Quando as condições de redimensionamento forem atendidas, os eventos iniciados e bem-sucedidos ou com falha serão registrados nessa categoria.
* **Recomendação** - essa categoria contém eventos de recomendação de certos tipos de recursos, como sites e servidores SQL. Estes eventos oferecem recomendações sobre como utilizar melhor seus recursos. Você só receberá eventos desse tipo se tiver recursos que emitam recomendações.
* **Política de segurança e integridade de recursos** -essas categorias não contêm eventos; elas estão reservadas para uso futuro.

## <a name="event-schema-per-category"></a>Esquema de eventos por categoria
[Veja este artigo para compreender o esquema de evento do Log de Atividades por categoria.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>O que você pode fazer com o Log de Atividades
Veja algumas coisas que você pode fazer com o Log de Atividades:

![Log de Atividades do Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Consultar e exibi-lo no **Portal do Azure**.
* [Criar um alerta em um evento do Log de Atividades.](monitoring-activity-log-alerts.md)
* [Transmiti-lo para um **Hub de Eventos**](monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço de terceiro ou solução de análise personalizada, como o PowerBI.
* Analisá-lo no PowerBI usando o [**Pacote de conteúdo do PowerBI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Salvá-lo em uma **Conta de Armazenamento** para inspeção manual ou arquivamento](monitoring-archive-activity-log.md). Você pode especificar o tempo de retenção (em dias) usando o **Perfil de Log**.
* Consultar por meio de Cmdlet do PowerShell, da CLI ou da API REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Consultar o Log de Atividades no Portal do Azure
No Portal do Azure, você pode exibir seu Log de Atividades em vários locais:
* A **folha Log de Atividades**, que pode ser acessada pesquisando Log de Atividades em "Mais Serviços" no painel de navegação esquerdo.
* A **folha Monitor**, que é exibida por padrão no painel de navegação esquerdo. O Log de Atividades é uma seção dessa folha do Azure Monitor.
* Qualquer **folha de recursos** do recurso, por exemplo, a folha de configuração de uma máquina virtual. O Log de Atividades é uma das seções na maioria das folhas de recursos, e ao clicar nele os eventos relacionados a esse recurso específico são filtrados automaticamente.

No Portal do Azure, você pode filtrar seu Log de Atividades por estes campos:
* Intervalo de tempo – A hora de início e de término dos eventos.
* Categoria – A categoria do evento conforme descrito acima.
* Assinatura – Um ou mais nomes de assinatura do Azure.
* Grupo de recursos – Um ou mais grupos de recursos nessas assinaturas.
* Recurso (nome) – O nome de um recurso específico.
* Tipo de recurso – O tipo de recurso, por exemplo, Microsoft.Compute/virtualmachines.
* Nome da operação – O nome de uma operação do Azure Resource Manager, por exemplo, Microsoft.SQL/servers/Write.
* Gravidade – O nível de gravidade do evento (Informativo, Aviso, Erro, Crítico).
* Evento iniciado por – O "chamador" ou o usuário que realizou a operação.
* Pesquisa aberta – Isso é uma caixa de pesquisa de texto aberto que procura essa cadeia de caracteres em todos os campos de todos os eventos.

Após a definição de um conjunto de filtros, salve-o como uma consulta que é persistente entre as sessões, se você precisar executar a mesma consulta com os filtros aplicados novamente. Você também pode fixar uma consulta em seu painel do Azure para sempre ficar atento a eventos específicos.

Clicar em "Aplicar" executa sua consulta e mostrar todos os eventos correspondentes. Clicar em qualquer evento na lista mostra o resumo desse evento, bem como o JSON bruto e completo desse evento.

Para ter ainda mais recursos, você pode clicar no ícone **Pesquisa de Log**, que exibe os dados de seu Log de Atividades na [solução de Análise do Log de Atividades do Log Analytics](../log-analytics/log-analytics-activity.md). A folha Log de Atividades oferece uma experiência básica de filtro/procura nos logs, mas o Log Analytics permite que você dinamize, consulte e visualize os dados de formas mais eficientes.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportar o Log de Atividades com um Perfil de Log
Um **Perfil de Log** controla o modo de exportação de seu Log de Atividades. Com um Perfil de Log, você pode configurar:

* Aonde o Log de Atividades deve ser enviado (Conta de Armazenamento ou Hubs de Eventos)
* Quais categorias de evento (Gravação, Exclusão, Ação) devem ser enviadas. *O significado de "categoria" em Perfis de Log e eventos de Log de Atividades é diferente. No Perfil de Log, "Category" representa o tipo de operação (Gravar, Excluir, Ação). Em um evento do Log de Atividades, a propriedade "category" representa a origem ou o tipo de evento (por exemplo, Administração, Serviço de Integridade, Alerta e muito mais).*
* Quais regiões (locais) devem ser exportados. Inclua "global", pois muitos eventos no Log de Atividades são eventos globais.
* Quanto tempo o Log de Atividades deve ser mantido em uma Conta de Armazenamento.
    - Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos.

Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura para emitir logs. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

Essas configurações podem ser definidas por meio da opção "Exportar" na folha do Log de Atividades no portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI. Uma assinatura pode ter somente um perfil de log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar os perfis de log usando o Portal do Azure
Você pode transmitir o Log de Atividades para um Hub de Eventos ou armazená-lo em uma Conta de Armazenamento usando a opção "Exportar" no Portal do Azure.

1. Navegue até a folha **Log de Atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de Atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da folha.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na folha que aparece, você pode selecionar:  
  * regiões para as quais você deseja exportar eventos
  * a conta de armazenamento na qual você deseja salvar os eventos
  * o número de dias para manter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
  * o namespace do Barramento de Serviço no qual você deseja que um Hub de Eventos seja criado para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar os perfis de log usando Cmdlets do Azure PowerShell
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| Nome |Sim |Nome de seu perfil de log. |
| StorageAccountId |Não |ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Categorias |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar os perfis de log usando a CLI de plataforma cruzada do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
A propriedade `name` deve ser o nome de seu perfil de log.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| name |Sim |Nome de seu perfil de log. |
| storageId |Não |ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Categorias |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Log de Atividades (anteriormente conhecido como Logs de Auditoria)](../azure-resource-manager/resource-group-audit.md)
* [Transmissão do Log de Atividades do Azure para os Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)
