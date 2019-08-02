---
title: Recursos de Automação do Azure em soluções de gerenciamento | Microsoft Docs
description: A soluções de gerenciamento normalmente incluirão runbooks na Automação do Azure para automatizar processos como coleta e processamento de dados de monitoramento.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c9b13f44dae068597cb82a0aa803283ad5e67bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110354"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Adicionar recursos de Automação do Azure a uma solução de gerenciamento (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Os esquemas descritos a seguir estão sujeitos a alterações.   


As [Soluções de gerenciamento]( solutions.md) normalmente incluirão runbooks na Automação do Azure para automatizar processos como coleta e processamento de dados de monitoramento.  Além de runbooks, contas de automação incluem ativos, como variáveis e agendas que dão suporte a runbooks usados na solução.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns a soluções de gerenciamento e descritos em [Projetar e compilar uma solução de gerenciamento no Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com as informações a seguir.

- Como [criar uma solução de gerenciamento]( solutions-creating.md).
- A estrutura de um [arquivo de solução]( solutions-solution-file.md).
- Como [criar modelos do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Conta de automação
Todos os recursos na Automação do Azure estão contidos em um [Conta de automação](../../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito no [espaço de trabalho do Log Analytics e conta de Automação]( solutions.md#log-analytics-workspace-and-automation-account), a conta de Automação não está incluída na solução de gerenciamento, mas deverá existir antes da solução ser instalada.  Se ela não estiver disponível, a instalação da solução falhará.

O nome de cada recurso de Automação inclui o nome de sua conta de Automação.  Isso é feito na solução com o parâmetro **accountName**, conforme descrito no exemplo a seguir de um recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Você deve incluir todos os runbooks usados pela solução no arquivo de solução para que eles sejam criados quando a solução for instalada.  No entanto, você não pode incluir o corpo do runbook no modelo, de modo que é preciso publicar o runbook em um local público onde ele possa ser acessado por qualquer usuário que esteja instalando sua solução.

Os recursos do [runbook de Automação do Azure](../../automation/automation-runbook-types.md) têm o tipo **Microsoft.Automation/automationAccounts/runbooks** e a estrutura a seguir. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


As propriedades dos runbooks são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| runbookType |Especifica os tipos de runbook. <br><br> Script – Script do PowerShell <br>PowerShell – Fluxo de trabalho do PowerShell <br> GraphPowerShell – Runbook de script do PowerShell gráfico <br> GraphPowerShellWorkflow – Runbook de fluxo de trabalho do PowerShell gráfico |
| logProgress |Especifica se [registros de progresso](../../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| logVerbose |Especifica se [registros detalhados](../../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| description |Descrição opcional para o runbook. |
| publishContentLink |Especifica o conteúdo do runbook. <br><br>uri – URI do conteúdo do runbook.  Ele será um arquivo .ps1 para runbooks do PowerShell e Script e um arquivo de runbook grafo exportado para um runbook de Grafo.  <br> versão – a versão do runbook para seu próprio acompanhamento. |


## <a name="automation-jobs"></a>Trabalhos de automação
Quando você inicia um runbook na Automação do Azure, isso cria um trabalho de automação.  Você pode adicionar um recurso de trabalho de automação à solução para iniciar um runbook automaticamente quando a solução de gerenciamento for instalada.  Esse método normalmente é usado para iniciar runbooks que são usados para a configuração inicial da solução.  Para iniciar um runbook em intervalos regulares, crie um [agendamento](#schedules) e um [agendamento de trabalho](#job-schedules)

Os recursos de trabalho têm o tipo **Microsoft.Automation/automationAccounts/jobs** e a estrutura a seguir.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

As propriedades dos trabalhos de automação são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| runbook |Entidade de único nome com o nome do runbook a ser iniciado. |
| parameters |Entidade para cada valor de parâmetro exigido pelo runbook. |

O trabalho inclui o nome do runbook e valores de parâmetro a ser enviado para o runbook.  O trabalho deve [depender]( solutions-solution-file.md#resources) do runbook que está sendo iniciado, uma vez que o runbook deve ser criado antes do trabalho.  Caso tenha vários runbooks que devam ser iniciados, você pode definir a ordem deles com um trabalho que dependa de qualquer outro trabalho que deva ser executado primeiro.

O nome de um recurso de trabalho deve conter um GUID que normalmente é atribuído por um parâmetro.  Você pode ler mais sobre os parâmetros GUID em [Criar um arquivo de solução de gerenciamento no Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificados
Os [certificados de Automação do Azure](../../automation/automation-certificates.md) têm o tipo **Microsoft.Automation/automationAccounts/certificates** e a estrutura a seguir. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



As propriedades dos recursos de Certificado são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| base64Value |O valor de base 64 do certificado. |
| thumbprint |A impressão digital do certificado. |



## <a name="credentials"></a>Credenciais
As [credenciais de Automação do Azure](../../automation/automation-credentials.md) têm o tipo **Microsoft.Automation/automationAccounts/credentials** e a estrutura a seguir.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

As propriedades dos recursos de Credencial são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| userName |Nome de usuário da credencial. |
| password |Senha da credencial. |


## <a name="schedules"></a>Agendas
Os [agendamentos de Automação do Azure](../../automation/automation-schedules.md) têm o tipo **Microsoft.Automation/automationAccounts/schedules** e a estrutura a seguir. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

As propriedades de recursos de agendamento são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| description |Descrição opcional para o agendamento. |
| startTime |Especifica a hora de início de uma agenda como um objeto DateTime. Uma cadeia de caracteres pode ser fornecida se ele for convertido em um DateTime válido. |
| isEnabled |Especifica se o agendamento está habilitado. |
| interval |O tipo de intervalo para o agendamento.<br><br>dia<br>hora |
| frequency |Frequência em que o agendamento deve ser disparado em número de dias ou horas. |

Os agendamentos devem ter um horário de início com um valor posterior ao horário atual.  Você não pode fornecer esse valor com uma variável, uma vez que não teria como saber quando ele vai ser instalado.

Use uma das duas estratégias a seguir ao usar os recursos de agendamento em uma solução.

- Use um parâmetro para o horário de início do agendamento.  Essa ação solicitará que o usuário forneça um valor quando instalar a solução.  Caso tenha vários agendamentos, você poderá usar um único valor de parâmetro para mais de um deles.
- Crie os agendamentos usando um runbook que comece quando a solução é instalada.  Isso elimina a necessidade de o usuário especificar um horário, mas você não pode incluir o agendamento na sua solução, de modo que ele será removido quando a solução for removida.


### <a name="job-schedules"></a>Agendas de trabalho
Os recursos de agendamento vinculam um runbook a um agendamento.  Eles têm o tipo **Microsoft.Automation/automationAccounts/jobSchedules** e a estrutura a seguir.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


As propriedades dos agendamentos de trabalho são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| schedule name |Entidade de único **name** com o nome do agendamento. |
| runbook name  |Uma entidade com **name** único com o nome do runbook.  |



## <a name="variables"></a>variáveis
As [variáveis de Automação do Azure](../../automation/automation-variables.md) têm o tipo **Microsoft.Automation/automationAccounts/variables** e a estrutura a seguir.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

As propriedades dos recursos de variáveis são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| description | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável deve ser criptografada. |
| type | Essa propriedade atualmente está sem efeito.  O tipo de dados da variável será determinado pelo valor inicial. |
| value | Valor da variável. |

> [!NOTE]
> Atualmente, a propriedade **type** não tem efeito sobre a variável que está sendo criada.  O tipo de dados para a variável será determinado pelo valor.  

Se você definir o valor inicial da variável, ele deverá ser definido como o tipo de dados correto.  A tabela a seguir fornece os diferentes tipos de dados permitidos e sua sintaxe.  Observe que os valores em JSON devem sempre ser colocados entre aspas com caracteres especiais entre aspas.  Por exemplo, um valor de cadeia de caracteres deve ser especificado por aspas em volta da cadeia de caracteres (usando o caractere de escape \\) enquanto um valor numérico deve ser especificado por um conjunto de aspas.

| Tipo de dados | DESCRIÇÃO | Exemplo | É resolvido desta forma |
|:--|:--|:--|:--|
| string   | Coloque o valor entre aspas duplas.  | "\"Olá, Mundo\"" | "Olá, Mundo" |
| numeric  | Valor numérico com aspas simples.| "64" | 64 |
| boolean  | **true** ou **false** entre aspas.  Observe que esse valor deve estar em minúsculas. | "true" | verdadeiro |
| datetime | Valor de data serializada.<br>Você pode usar o cmdlet ConvertTo-Json no PowerShell para gerar esse valor para uma determinada data.<br>Exemplo: get-date "24/5/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Módulos
Sua solução de gerenciamento não precisa definir [módulos globais](../../automation/automation-integration-modules.md) usados pelos seus runbooks porque eles sempre estarão disponíveis na conta de Automação.  Você precisa incluir um recurso para qualquer outro módulo usado pelos seus runbooks.

Os [módulos de integração](../../automation/automation-integration-modules.md) têm o tipo **Microsoft.Automation/automationAccounts/modules** e a estrutura a seguir.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este snippet de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


As propriedades dos recursos de módulo são descritas na tabela a seguir.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| contentLink |Especifica o conteúdo do módulo. <br><br>uri – Uri para o conteúdo do módulo.  Ele será um arquivo .ps1 para runbooks do PowerShell e Script e um arquivo de runbook grafo exportado para um runbook de Grafo.  <br> version – a versão do módulo para seu próprio acompanhamento. |

O runbook deve depender do recurso de módulo para garantir que ele seja criado antes do runbook.

### <a name="updating-modules"></a>Atualizando módulos
Se você atualizar uma solução de gerenciamento que inclui um runbook que usa um agendamento e a nova versão da solução tiver um novo módulo usado pelo runbook, o runbook poderá usar a versão antiga do módulo.  Você deve incluir os seguintes runbooks em sua solução e criar um trabalho para executá-los antes de quaisquer outros runbooks.  Isso garantirá que todos os módulos estejam atualizados como necessário antes que os runbooks sejam carregados.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) garantirá que todos os módulos usados por runbooks em sua solução tenham a versão mais recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) registrará novamente todos os recursos de agendamento para garantir que os runbooks vinculados a eles usem os módulos mais recentes.




## <a name="sample"></a>Amostra
A seguir está um exemplo de uma solução que inclua que inclui os seguintes recursos:

- Runbook.  É um exemplo de runbook armazenado em um repositório público do GitHub.
- O trabalho de Automação que inicia o runbook quando a solução é instalada.
- O agendamento e o agendamento de trabalho para iniciar o runbook em intervalos regulares.
- Certificado.
- Credencial.
- Variável.
- Módulo.  Esse é o [módulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) para gravar dados no Log Analytics. 

O exemplo usa [parâmetros de solução padrão]( solutions-solution-file.md#parameters) variáveis que normalmente seriam usados em uma solução em vez de embutir valores nas definições de recurso.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Próximas etapas
* [Adicione um modo de exibição à sua solução]( solutions-resources-views.md) para visualizar os dados coletados.
