---
title: Exibir logs de atividades do Azure para monitorar recursos | Microsoft Docs
description: "Use o log de atividade para examinar erros e ações do usuário. Mostra o Portal do Azure, PowerShell, CLI do Azure e REST."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.openlocfilehash: 9f90bc80c146c6c2da04aacbc110f7d389c0baa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Exibir logs de atividade para auditar ações em recursos
Com os logs de atividade, você pode determinar:

* quais operações foram executadas nos recursos em sua assinatura
* quem iniciou a operação (embora as operações iniciadas por um serviço de back-end não retornem um usuário como o chamador)
* quando a operação ocorreu
* o status da operação
* os valores de outras propriedades que podem ajudar você a pesquisar a operação

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

Você pode recuperar informações dos logs de atividade por meio do Portal, do PowerShell, da CLI do Azure, da API REST do Insights ou da [Biblioteca .NET do Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portal
1. Para exibir os logs de atividade no portal, escolha **Monitorar**.
   
    ![selecionar logs de atividade](./media/resource-group-audit/select-monitor.png)

   Ou, para filtrar automaticamente o log de atividades para um determinado recurso ou grupo de recursos, selecione **Log de atividades** na folha desse recurso. Observe que o log de atividades é automaticamente filtrado pelo recurso selecionado.
   
    ![filtrar por recurso](./media/resource-group-audit/filtered-by-resource.png)
2. Na folha **Log de Atividades**, você vê um resumo das operações recentes.
   
    ![mostrar ações](./media/resource-group-audit/audit-summary.png)
3. Para restringir o número de operações exibidas, selecione condições diferentes. Por exemplo, a imagem a seguir mostra os campos **Intervalo de tempo** e **Evento iniciado por** alterados para exibir as ações realizadas por um determinado usuário ou aplicativo no mês passado. Selecione **Aplicar** para exibir os resultados da consulta.
   
    ![definir opções de filtragem](./media/resource-group-audit/set-filter.png)

4. Se você precisar executar a consulta novamente mais tarde, selecione **Salvar** e nomeie a consulta.
   
    ![salvar consulta](./media/resource-group-audit/save-query.png)
5. Para executar rapidamente uma consulta, selecione uma das consultas internas, como falhas de implantação.

    ![selecionar consulta](./media/resource-group-audit/select-quick-query.png)

   A consulta selecionada define automaticamente os valores de filtro obrigatórios.

    ![exibir erros de implantação](./media/resource-group-audit/view-failed-deployment.png)   

6. Selecione uma das operações para ver um resumo do evento.

    ![exibir operação](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Para recuperar as entradas de log, execute o comando **Get-AzureRmLog** . Forneça parâmetros adicionais para filtrar a lista de entradas. Se você não especificar uma hora de início e de término, as entradas da última hora retornarão. Por exemplo, para recuperar as operações de um grupo de recursos durante a execução na última hora:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    O exemplo a seguir mostra como usar o log de atividades para operações de pesquisa executadas durante um período especificado. As datas de início e de término são especificadas em um formato de data.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Outra opção é usar funções de data para especificar o intervalo de datas, como os últimos 14 dias.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. Dependendo da hora de início que você especificar, os comandos anteriores poderão retornar uma longa lista de operações para o grupo de recursos. Você pode, pelo fornecimento de critérios de pesquisa, filtrar os resultados para o que você está procurando. Por exemplo, se estiver tentando pesquisar como um aplicativo Web foi interrompido, você poderá executar o comando a seguir:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Que, para este exemplo, mostra que a ação de parada foi executada por someone@contoso.com. 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Você pode procurar as ações realizadas por um determinado usuário, mesmo para um grupo de recursos que não existe mais.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Você pode filtrar para mostrar operações com falha.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Você pode focar em um erro examinando a mensagem de status dessa entrada.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Que retorna:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>CLI do Azure
* Para recuperar as entradas de log, execute o comando **azure group log show** .

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>API REST
As operações de REST para trabalhar com o log de atividade fazem parte da [API REST do Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar os eventos de log de atividade, confira [Listar os eventos de gerenciamento em uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Próximas etapas
* Os logs de atividade do Azure podem ser usados com o Power BI para obter mais informações sobre as ações em sua assinatura. Confira [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Exibir e analisar logs de atividade do Azure no Power BI e muito mais).
* Para aprender sobre como definir políticas de segurança, confira [Controle de acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md).
* Para saber mais sobre os comandos para exibir as operações de implantação, consulte [Exibir operações de implantação](resource-manager-deployment-operations.md).
* Para saber como impedir exclusões em um recurso para todos os usuários, confira [Bloquear recursos com o Azure Resource Manager](resource-group-lock-resources.md).

