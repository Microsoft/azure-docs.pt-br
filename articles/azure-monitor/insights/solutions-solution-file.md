---
title: Criar um arquivo de solução de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento fornecem cenários de gerenciamento empacotados que os clientes podem adicionar ao ambiente do Azure.  Este artigo fornece detalhes sobre como criar soluções de gerenciamento para usar em seu próprio ambiente ou disponibilizar para os clientes.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e5c27911fe86a6916235014f8602327df929e20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595761"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Criar um arquivo de solução de gerenciamento no Azure (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no Azure que atualmente estão em versão prévia. Os esquemas descritos a seguir estão sujeitos a alterações.  

As soluções de gerenciamento no Azure são implementadas como [modelos do Resource Manager](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  A principal tarefa ao aprender a criar soluções personalizadas é aprender como [criar um modelo](../../azure-resource-manager/resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos sobre os modelos usados para as soluções e como configurar recursos típicos de soluções.


## <a name="tools"></a>Ferramentas

É possível usar qualquer editor de texto para trabalhar com arquivos de solução, mas recomendamos aproveitar os recursos fornecidos no Visual Studio ou no Visual Studio Code, conforme descrito nos próximos artigos.

- [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Trabalhando com Modelos do Azure Resource Manager no Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Estrutura
A estrutura básica de um arquivo de solução de gerenciamento é a mesma que um [modelo do Gerenciador de Recursos](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), que é da maneira demonstrada a seguir.  Cada uma das seções abaixo descreve os elementos de nível superior e seu conteúdo em uma solução.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>parâmetros
[Parâmetros](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) são valores que exige dos usuários quando eles instalam a solução de gerenciamento.  Eles são parâmetros padrão que todas as soluções terão; além disso, você pode adicionar parâmetros adicionais conforme necessário para sua solução específica.  O modo como os usuários fornecerão valores de parâmetro quando instalarem sua solução dependerá do parâmetro específico e do modo como a solução estiver sendo instalada.

Quando um usuário instala a solução de gerenciamento por meio do [Azure Marketplace](solutions.md#install-a-monitoring-solution) ou dos Modelos de Início Rápido do Azure, será solicitado que ele selecione uma Conta de automação e um [Espaço de Trabalho do Log Analytics](solutions.md#log-analytics-workspace-and-automation-account).  Eles são usados para preencher os valores de cada um dos parâmetros padrão.  Não é solicitado que o usuário forneça diretamente os valores dos parâmetros padrão, mas será solicitado que ele forneça valores para eventuais parâmetros adicionais.


Um parâmetro de exemplo é mostrado abaixo.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela a seguir descreve os atributos de um parâmetro.

| Atributo | DESCRIÇÃO |
|:--- |:--- |
| Tipo |Tipo de dados para o parâmetro. O controle de entrada exibido para o usuário depende do tipo de dados.<br><br>bool – Caixa suspensa<br>cadeia de caracteres – caixa de texto<br>int – Caixa de texto<br>securestring – Campo de senha<br> |
| categoria |Categoria opcional para o parâmetro.  Parâmetros na mesma categoria são agrupados. |
| controle |Funcionalidade adicional para parâmetros de cadeia de caracteres.<br><br>datetime – O controle datetime é exibido.<br>GUID – O valor de GUID é gerado automaticamente e o parâmetro não é exibido. |
| Descrição |Descrição opcional para o parâmetro.  Exibido em um balão de informações ao lado do parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela a seguir lista os parâmetros padrão para todas as soluções de gerenciamento.  Esses valores são populados para o usuário em vez de solicitados a eles quando a solução é instalada dos modelos do Azure Marketplace ou de Início Rápido.  Se a solução for instalada com outro método, o usuário deverá fornecer valores para eles.

> [!NOTE]
> A interface do usuário nos modelos do Azure Marketplace e de Início Rápido espera os nomes de parâmetro na tabela.  Se você usar nomes de parâmetro diferentes, será solicitado que o usuário os forneça e eles não serão automaticamente populados.
>
>

| Parâmetro | Type | DESCRIÇÃO |
|:--- |:--- |:--- |
| accountName |cadeia de caracteres |Nome da conta de Automação do Azure. |
| pricingTier |cadeia de caracteres |Tipo de preço do espaço de trabalho do Log Analytics e da conta de Automação do Azure. |
| regionId |cadeia de caracteres |Região da conta de Automação do Azure. |
| solutionName |cadeia de caracteres |O nome da solução.  Se você estiver implantando a solução por meio de modelos de Início Rápido, defina solutionName como um parâmetro para que seja possível definir uma cadeia de caracteres, em vez de exigir que o usuário especifique um. |
| workspaceName |cadeia de caracteres |O nome do espaço de trabalho do Log Analytics. |
| workspaceRegionId |cadeia de caracteres |A região do espaço de trabalho do Log Analytics. |


A seguir está a estrutura dos parâmetros padrão que você pode copiar e colar em seu arquivo de solução.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Consulte os valores de parâmetro em outros elementos da solução com a sintaxe **parameters('nome do parâmetro')**.  Por exemplo, para acessar o nome do workspace, você usaria **parameters('workspaceName')**

## <a name="variables"></a>variáveis
[Variáveis](../../azure-resource-manager/resource-group-authoring-templates.md#variables) são valores que serão usados no restante da solução de gerenciamento.  Esses valores não são expostos ao usuário que instala a solução.  Eles se destinam a fornecer ao autor um único local onde ele pode gerenciar os valores que podem ser usados várias vezes em toda a solução. É necessário colocar os valores específicos à solução em variáveis, em vez de embuti-los em código no elemento **resources**.  Isso torna o código mais legível e permite que você altere esses valores facilmente em versões posteriores.

A seguir está um exemplo de um elemento **variables** com parâmetros típicos usado em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Você consulta os valores de variáveis por toda a solução com a sintaxe **variables('nome da variável')**.  Por exemplo, para acessar a variável SolutionName, você usará **variables('SolutionName')**.

Você também pode definir variáveis complexas que têm vários conjuntos de valores.  Elas são particularmente úteis em soluções de gerenciamento, quando você estiver definindo várias propriedades para diferentes tipos de recursos.  Por exemplo, é possível reestruturar as variáveis da solução mostradas acima, conforme indicado a seguir.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Nesse caso, você consulta os valores de variáveis por meio da solução com a sintaxe **variables('variable name').property**.  Por exemplo, para acessar a variável Solution Name, você usará **variables('Solution').Name**.

## <a name="resources"></a>Recursos
Os [recursos](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definem os diferentes recursos que a solução de gerenciamento instalará e configurará.  Essa será a maior e mais complexa parte do modelo.  É possível obter a estrutura e a descrição completa dos elementos de recursos em [Criando modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Recursos diferentes que normalmente são definidos são detalhados em outros artigos desta documentação. 


### <a name="dependencies"></a>Dependências
O elemento **dependsOn** especifica uma [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) de outro recurso.  Quando a solução é instalada, um recurso não é criado até que todas as suas dependências tenham sido criadas.  Por exemplo, sua solução pode [iniciar um runbook](solutions-resources-automation.md#runbooks) quando ele é instalado usando um [recurso de trabalho](solutions-resources-automation.md#automation-jobs).  O recurso de trabalho seria dependente do recurso de runbook para assegurar que o runbook fosse criado antes do trabalho.

### <a name="log-analytics-workspace-and-automation-account"></a>Espaço de Trabalho do Log Analytics e Conta de automação
Soluções de gerenciamento exigem que um [espaço de trabalho do Log Analytics](../../azure-monitor/platform/manage-access.md) contenha modos de exibição e que uma [Conta de automação](../../automation/automation-security-overview.md#automation-account-overview) contenha runbooks e recursos relacionados.  Eles devem estar disponíveis antes que os recursos na solução sejam criados e não devem ser definidos na solução em si.  O usuário [especificará uma conta e workspace](solutions.md#log-analytics-workspace-and-automation-account) quando implantar a sua solução mas, na condição de autor, você deve considerar os pontos a seguir.


## <a name="solution-resource"></a>Recurso da solução
Cada solução exige uma entrada de recurso no elemento **resources** que define a solução em si.  Isso terá um tipo **Microsoft.OperationsManagement/solutions** e terá a estrutura a seguir. Isso inclui [parâmetros padrão](#parameters) e [variáveis](#variables) que normalmente são usados para definir propriedades da solução.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Dependências
O recurso da solução deve ter uma [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) em todos os outros recursos da solução, pois precisam existir antes que a solução possa ser criada.  Você pode fazer isso adicionando uma entrada para cada recurso no elemento **dependsOn**.

### <a name="properties"></a>propriedades
O recurso da solução tem as propriedades na tabela a seguir.  Isso inclui os recursos referenciados e contidos pela solução que define como os recursos são gerenciados após a instalação da solução.  Cada recurso na solução deve ser listado na propriedade **referencedResources** ou **containedResources**.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| workspaceResourceId |ID do espaço de trabalho do Log Analytics no formulário  *\<ID do grupo de recursos > /providers/Microsoft.OperationalInsights/workspaces/\<nome do espaço de trabalho\>*. |
| referencedResources |Lista de recursos na solução que não deverão ser removidos quando a solução for removida. |
| containedResources |Lista de recursos na solução que deverão ser removidos quando a solução for removida. |

O exemplo acima é uma solução com um runbook, um cronograma e uma exibição.  O agendamento e o runbook são *referenciados* no elemento **properties** para que eles não sejam removidos quando a solução for removida.  A exibição é *contida* para que ela seja removido quando a solução for removida.

### <a name="plan"></a>Plano
A entidade **plano** do recurso da solução tem as propriedades na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Nome |O nome da solução. |
| version |Versão da solução conforme determinado pelo autor. |
| product |Cadeia de caracteres exclusiva para identificar a solução. |
| publicador |O publicador da solução. |



## <a name="next-steps"></a>Próximas etapas
* [Adicionar alertas e pesquisas salvas](solutions-resources-searches-alerts.md) à sua solução de gerenciamento.
* [Adicionar exibições](solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicionar runbooks e outros recursos da Automação](solutions-resources-automation.md) à solução de gerenciamento.
* Aprenda os detalhes da [Criação de modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquise entre os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de diferentes modelos do Resource Manager.
