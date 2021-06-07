---
title: Criar, exibir e gerenciar alertas do log de atividades no Azure Monitor
description: Crie alertas do log de atividades usando o portal do Azure, um modelo do Azure Resource Manager e o Azure PowerShell.
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 26ca755f6675fa19c3b122c3528e05d1e8d76845
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045523"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor  

## <a name="overview"></a>Visão geral

Alertas do log de atividades são os alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta.

Esses alertas são para recursos do Azure e podem ser criados usando um modelo do Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Normalmente, você cria alertas do log de atividades para receber notificações quando ocorrem alterações específicas nos recursos da sua assinatura do Azure. Os escopos dos alertas geralmente são relacionados a recursos ou grupos de recursos específicos. Por exemplo, convém ser notificado quando qualquer máquina virtual no **myProductionResourceGroup** do grupo de recursos de exemplo for excluída. Ou você poderá receber uma notificação se funções novas forem atribuídas a um usuário em sua assinatura.

> [!IMPORTANT]
> Não é possível criar alertas de notificação de integridade do serviço por meio da interface para a criação de alerta do log de atividades. Para saber mais sobre como criar e usar notificações de integridade do serviço, veja [Receber alertas do log de atividades sobre as notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications-portal.md).

Ao criar as regras de alerta, assegure o seguinte:

- A assinatura no escopo não é diferente da assinatura em que o alerta foi criado.
- Os critérios devem ser nível, status, chamador, grupo de recursos, ID do recurso ou categoria de eventos do tipo de recurso no qual o alerta é configurado.
- Somente uma condição "allOf" é permitida.
- ' AnyOf ' pode ser usado para permitir várias condições em vários campos (por exemplo, se os campos "status" ou "substatus" forem iguais a um determinado valor). Observe que o uso de ' AnyOf ' está limitado atualmente à criação da regra de alerta usando uma implantação de modelo ARM.
- ' ContainsAny ' pode ser usado para permitir vários valores do mesmo campo (por exemplo, se "Operation" for igual a ' Delete ' ou ' Modify '). Observe que o uso de ' ContainsAny ' está limitado atualmente à criação da regra de alerta usando uma implantação de modelo ARM.
- Quando a categoria for "administrativa", você deverá especificar pelo menos um dos critérios anteriores no seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.
- Não é possível criar alertas para eventos na categoria de alerta do log de atividades.

## <a name="azure-portal"></a>Portal do Azure

Você pode usar o portal do Azure para criar e modificar as regras de alerta do log de atividades. A experiência é integrada a um log de atividades do Azure para garantir a criação de alertas contínuos para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal do Azure

Use o procedimento a seguir.

1. No portal do Azure, selecione **Monitor** > **Alertas**.
2. Selecione **Nova regra de alerta** no canto superior esquerdo da janela **Alertas**.

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     A janela **Criar regra** é exibida.

      ![Opções de nova regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Em **Definir a condição de alerta**, forneça as seguintes informações e clique em **Concluído**:

   - **Destino de Alerta:** Para ver e selecionar o destino do novo alerta, use **Filtrar por assinatura** / **Filtrar por tipo de recurso**. Selecione o recurso ou o grupo de recursos na lista exibida.

     > [!NOTE]
     > 
     > Você pode selecionar um recurso rastreado, um grupo de recursos ou uma assinatura inteira do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) para o sinal do log de atividades. 

     **Exibição de exemplo do destino de alerta**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - Em **Critérios de destino**, selecione **Adicionar critérios**. Todos os sinais disponíveis para o destino são exibidos, o que inclui aqueles de várias categorias do **Log de Atividades**. O nome da categoria é anexado ao nome do **Serviço de Monitor**.

   - Selecione o sinal na lista exibida com as várias operações possíveis para o tipo **Log de Atividades**.

     É possível selecionar a linha do tempo do histórico de log e a lógica de alerta correspondente para esse sinal de destino:

     **Tela Adicionar critérios**

     ![Adicionar critérios](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  Para ter uma alta qualidade e regras efetivas, solicitamos que adicione pelo menos mais uma condição às regras com o sinal "Tudo Administrativo". 
     > Como parte da definição do alerta, você deve preencher um dos menus suspensos: "Nível de evento", "Status" ou "Iniciado por" e, com isso, a regra será mais específica.

     - **Tempo de histórico**: os eventos disponíveis para a operação selecionada podem ser plotados durante as últimas 6, 12 ou 24 horas ou durante a última semana.

     - **Lógica de Alerta**:

       - **Nível do evento**: o nível de severidade do evento: _Detalhado_, _Informativo_, _Aviso_, _Erro_ ou _Crítico_.
       - **Status**: O status do evento: _Iniciado_, _Com falha_ ou _Bem-sucedido_.
       - **Evento iniciado por**: também conhecido como o chamador. O endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

       Esse gráfico de sinal de exemplo com a lógica de alerta aplicada:

       ![critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Em **Definir detalhes de alerta**, forneça os seguintes detalhes:

    - **Nome da regra de alerta**: o nome da nova regra de alerta.
    - **Descrição**: a descrição para a nova regra de alerta.
    - **Salvar o alerta para o grupo de recursos**: selecione o grupo de recursos no qual deseja salvar essa nova regra.

5. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Ou [crie um grupo de ações](./action-groups.md) e atribua à nova regra. Para criar um grupo, selecione **+ Novo grupo**.

6. Para habilitar as regras após criá-lo, selecione **Sim** da opção **Habilitar regra após a criação**.
7. Selecione **Criar regra de alerta**.

    A regra de alerta para o log de atividades é criada e uma mensagem de confirmação aparece no canto superior direito da janela.

    É possível habilitar, desabilitar, editar ou excluir uma regra. Saiba mais sobre como gerenciar as regras do log de atividades.


Uma analogia simples para entender as condições nas quais as regras de alerta podem ser criadas em um log de atividades é explorar ou filtrar eventos por meio do [log de atividades no portal do Azure](../essentials/activity-log.md#view-the-activity-log). Na tela **Azure Monitor – Log de atividades**, é possível filtrar ou localizar os eventos necessários e, em seguida, criar um alerta usando o botão **Adicionar alerta do log de atividades**. Em seguida, siga as etapas 4 a 7, conforme mostrado anteriormente.
    
 ![Adicionar alerta do log de atividades](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Exibir e gerenciar no portal do Azure

1. No portal do Azure, selecione **Monitor** > **Alertas**. Selecione **Gerenciar regras de alerta** no canto superior esquerdo da janela.

    ![Captura de tela mostra o log de atividades com a caixa de pesquisa realçada.](media/alerts-activity-log/manage-alert-rules.png)

    A lista das regras disponíveis é exibida.

2. Pesquise a regra do log de atividades para modificar.

    ![Pesquisar regras de alerta do log de atividades](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Você pode usar os filtros disponíveis, _Assinatura_, _Grupo de recursos_, _Recurso_, _Tipo de sinal_ ou _Status_, para localizar a regra de atividade que deseja editar.

   > [!NOTE]
   > 
   > É possível editar apenas a **Descrição**, os **Critérios de destino** e os **Grupos de ações**.

3. Selecione a regra e clique duas vezes para editar as opções da regra. Faça as alterações necessárias e selecione **Salvar**.

   ![Gerenciar regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. É possível habilitar, desabilitar ou excluir uma regra. Selecione a opção apropriada na parte superior da janela após selecionar a regra, conforme descrito na etapa 2.


## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Para criar uma regra de alerta do log de atividades usando um modelo do Azure Resource Manager, você cria um recurso do tipo `microsoft.insights/activityLogAlerts`. Em seguida, você preencherá todas as propriedades relacionadas. Veja abaixo um modelo que cria uma regra de alerta do log de atividades:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
O JSON de exemplo anterior pode ser salvo como, por exemplo, sampleActivityLogAlert.json para este passo a passo e pode ser implantado usando o [Azure Resource Manager no portal do Azure](../../azure-resource-manager/templates/deploy-portal.md).

  > [!NOTE]
  > 
  > Observe que os alertas do log de atividades de nível mais alto podem ser definidos como assinatura.
  > Ou seja, não há nenhuma opção para definir o alerta em duas assinaturas, portanto, a definição deve ser alertado por assinatura.

Os seguintes campos são as opções que você pode usar no modelo do Azure Resource Manager para os campos de condições: Observe que "Resource Health", "Assistente" e "Integridade do Serviço" têm campos de propriedades extras para seus campos especiais. 
1. resourceId:  a ID do recurso afetado no evento do log de atividades no qual o alerta deve ser gerado.
2. categoria: a categoria do evento do log de atividades. Por exemplo:  Administrativa, ServiceHealth, ResourceHealth, Dimensionamento Automático, Segurança, Recomendação e Política.
3. chamador: o endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação do evento do log de atividades.
4. nível: o nível da atividade no evento do log de atividades no qual o alerta deve ser gerado. Por exemplo:  Crítico, Erro, Aviso, Informativo, Detalhado.
5. operationName: o nome da operação no evento do log de atividades. Por exemplo:  Microsoft.Resources/deployments/write
6. resourceGroup: nome do grupo de recursos para o recurso afetado no evento do log de atividades.
7. resourceProvider: [Explicação sobre os provedores e tipos de recursos do Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, confira [Provedores de recursos para os serviços do Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: cadeia de caracteres que descreve o status da operação no evento de atividade. Por exemplo:  Iniciado, Em Progresso, Sucesso, Falha, Ativo, Resolvido
9. subStatus: Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus.   Por exemplo:  OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204) Solicitação Incorreta (Código de Status HTTP: 400) Não Localizado (Código de Status HTTP: 404) Conflito (código de status HTTP: 409) Erro Interno do Servidor (Código de Status HTTP: 500) Serviço Não Disponível (Código de Status HTTP: 503) Tempo Limite de Gateway (Código de Status HTTP: 504).
10. resourceType: o tipo de recurso que foi afetado por um evento. Por exemplo:  Microsoft.Resources/deployments

Por exemplo:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Mais detalhes sobre os campos do log de atividades podem ser encontrados [aqui](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Pode levar até cinco minutos para que uma nova regra de alerta do log de atividades fique ativa.

## <a name="rest-api"></a>API REST 
A [API de Alertas do Log de Atividades do Azure Monitor](/rest/api/monitor/activitylogalerts) é a API REST. Ela é totalmente compatível com a API REST do Azure Resource Manager. Ela pode ser usada por meio do PowerShell usando o cmdlet do Resource Manager ou a CLI do Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implantar o modelo do Resource Manager com o PowerShell
Para usar o PowerShell para implantar o modelo do Resource Manager de exemplo mostrado na seção [Modelo do Azure Resource Manager](#azure-resource-manager-template) anterior, use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

em que o sampleActivityLogAlert.parameters.json tem os valores fornecidos para os parâmetros necessários para a criação da regra de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Usar cmdlets do PowerShell do log de atividades

Os alertas do log de atividades têm cmdlets do PowerShell dedicados disponíveis:

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert): cria um alerta do log de atividades ou atualiza um alerta do log de atividades existente.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert): obtém um ou mais recursos de alerta do log de atividades.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert): habilita um alerta do log de atividades existente e define suas marcas.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert): desabilita um alerta do log de atividades existente e define suas marcas.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert): remove um alerta do log de atividades.

## <a name="azure-cli"></a>CLI do Azure

Os comandos da CLI do Azure dedicados sob o conjunto [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) estão disponíveis para gerenciar regras de alerta do log de atividades.

Para criar uma regra de alerta do log de atividades, use os seguintes comandos nessa ordem:

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): criar um recurso da regra de alerta do log de atividades.
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope): adicionar escopo para a regra de alerta do log de atividades criado.
1. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group): um adicionar grupo de ações para a regra de alerta do log de atividades.

Para recuperar um recurso da regra de alerta do log de atividades, use o comando [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) da CLI do Azure. Para ver todos os recursos da regra de alerta do log de atividades em um grupo de recursos, use [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Os recursos da regra de alerta do log de atividades podem ser movidos usando o comando [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) da CLI do Azure.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [esquema de webhook para os logs de atividades](./activity-log-alerts-webhook.md).
- Leia uma [visão geral dos logs de atividades](./activity-log-alerts.md).
- Saiba mais sobre [grupos de ação](./action-groups.md).  
- Saiba mais sobre as [notificações de integridade do serviço](../../service-health/service-notifications.md).
