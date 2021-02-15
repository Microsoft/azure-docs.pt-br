---
title: Visão geral-automatizar a implantação para aplicativos lógicos do Azure
description: Saiba mais sobre modelos de Azure Resource Manager para automatizar a implantação para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: 2e1536d4f2ea7d71691c611e9127109c154f3266
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807336"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Visão geral: automatizar a implantação para aplicativos lógicos do Azure usando modelos de Azure Resource Manager

Quando estiver pronto para automatizar a criação e a implantação de seu aplicativo lógico, você poderá expandir a definição de fluxo de trabalho subjacente do aplicativo lógico para um [modelo de Azure Resource Manager](../azure-resource-manager/management/overview.md). Este modelo define a infraestrutura, os recursos, os parâmetros e outras informações para provisionar e implantar seu aplicativo lógico. Definindo parâmetros para valores que variam na implantação, também conhecido como *parametrização*, você pode implantar aplicativos lógicos de forma repetida e consistente com base em diferentes necessidades de implantação.

Por exemplo, se você implantar em ambientes para desenvolvimento, teste e produção, provavelmente usará cadeias de conexão diferentes para cada ambiente. Você pode declarar parâmetros de modelo que aceitam cadeias de conexão diferentes e, em seguida, armazenar essas cadeias de caracteres em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md)separado. Dessa forma, você pode alterar esses valores sem precisar atualizar e reimplantar o modelo. Para cenários em que você tem valores de parâmetro que são confidenciais ou que devem ser protegidos, como senhas e segredos, você pode armazenar esses valores em [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) e fazer com que o arquivo de parâmetros recupere esses valores. No entanto, nesses cenários, você reimplantaria para recuperar os valores atuais.

Esta visão geral descreve os atributos em um modelo do Resource Manager que inclui uma definição de fluxo de trabalho do aplicativo lógico. O modelo e sua definição de fluxo de trabalho usam a sintaxe JSON, mas existem algumas diferenças porque a definição de fluxo de trabalho também segue o [esquema de linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). Por exemplo, expressões de modelo e expressões de definição de fluxo de trabalho diferem em como eles [fazem referência a parâmetros](#parameter-references) e os valores que eles podem aceitar.

> [!TIP]
> Para obter uma maneira mais fácil de obter um modelo de aplicativo lógico parametrizado válido que está, na maioria das vezes, pronto para implantação, use o Visual Studio (versão gratuita da Comunidade ou superior) e as ferramentas de aplicativos lógicos do Azure para Visual Studio. Você pode [criar seu aplicativo lógico no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) ou [Localizar e baixar um aplicativo lógico existente do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Ou, você pode criar modelos de aplicativo lógico usando [Azure PowerShell com o módulo LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

O exemplo de aplicativo lógico neste tópico usa um [gatilho do Outlook do Office 365](/connectors/office365/) que é acionado quando um novo email chega e uma [ação de armazenamento de BLOBs do Azure](/connectors/azureblob/) que cria um blob para o corpo do email e carrega esse blob em um contêiner de armazenamento do Azure. Os exemplos também mostram como parametrizar valores que variam na implantação.

Para obter mais informações sobre modelos do Resource Manager, consulte estes tópicos:

* [Estrutura e sintaxe do modelo de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Práticas recomendadas para modelos do Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md)
* [Desenvolva modelos do Azure Resource Manager para consistência de nuvem](../azure-resource-manager/templates/templates-cloud-consistency.md)

Para informações de recursos de modelo específicas para aplicativos lógicos, contas de integração, artefatos de conta de integração e ambientes de serviço de integração, consulte [tipos de recursos Microsoft. Logic](/azure/templates/microsoft.logic/allversions).

Para obter exemplos de modelos de aplicativo lógico, consulte estes exemplos:

* [Modelo completo](#full-example-template) usado para os exemplos deste tópico
* [Exemplo de modelo de aplicativo lógico de início rápido](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) no github

Para a API REST dos aplicativos lógicos, comece com a [visão geral da API REST dos aplicativos lógicos do Azure](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Estrutura do modelo

No nível superior, um modelo do Resource Manager segue essa estrutura, que é totalmente descrita na [estrutura do modelo de Azure Resource Manager e](../azure-resource-manager/templates/template-syntax.md) no tópico de sintaxe:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Para um modelo de aplicativo lógico, você trabalha principalmente com estes objetos de modelo:

| Atributo | Descrição |
|-----------|-------------|
| `parameters` | Declara os [parâmetros de modelo](../azure-resource-manager/templates/template-syntax.md#parameters) para aceitar os valores a serem usados ao criar e personalizar recursos para implantação no Azure. Por exemplo, esses parâmetros aceitam os valores para o nome e o local do seu aplicativo lógico, as conexões e outros recursos necessários para a implantação. Você pode armazenar esses valores de parâmetro em um [arquivo de parâmetros](#template-parameter-files), que é descrito posteriormente neste tópico. Para obter detalhes gerais, consulte [parâmetros – estrutura e sintaxe do modelo do Resource Manager](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Define os [recursos](../azure-resource-manager/templates/template-syntax.md#resources) para criar ou atualizar e implantar em um grupo de recursos do Azure, como seu aplicativo lógico, conexões, contas de armazenamento do Azure e assim por diante. Para obter detalhes gerais, consulte [recursos – estrutura e sintaxe do modelo do Resource Manager](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Seu modelo de aplicativo lógico usa este formato de nome de arquivo:

**<*Logic-app-Name* # C0.jsem**

> [!IMPORTANT]
> A sintaxe do modelo diferencia maiúsculas de minúsculas, portanto, certifique-se de usar maiúsculas e minúsculas. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parâmetros de modelo

Um modelo de aplicativo lógico tem vários `parameters` objetos que existem em diferentes níveis e executa funções diferentes. Por exemplo, no nível superior, você pode declarar [parâmetros de modelo](../azure-resource-manager/templates/template-syntax.md#parameters) para os valores a serem aceitos e usados na implantação ao criar e implantar recursos no Azure, por exemplo:

* Seu aplicativo lógico
* Conexões que sua lógica usa para acessar outros serviços e sistemas por meio de [conectores gerenciados](../connectors/apis-list.md)
* Outros recursos de que seu aplicativo lógico precisa para implantação

  Por exemplo, se seu aplicativo lógico usa uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para cenários B2B (entre empresas), o objeto de nível superior do modelo `parameters` declara o parâmetro que aceita a ID do recurso para essa conta de integração.

Aqui está a estrutura geral e a sintaxe para uma definição de parâmetro, que é totalmente descrita por [parâmetros – estrutura de modelo do Resource Manager e sintaxe](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Este exemplo mostra apenas os parâmetros de modelo para os valores usados para criar e implantar esses recursos no Azure:

* Nome e local para seu aplicativo lógico
* ID a ser usada para uma conta de integração vinculada ao aplicativo lógico

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Exceto para parâmetros que manipulam valores que são confidenciais ou devem ser protegidos, como nomes de dados, senhas e segredos, todos esses parâmetros incluem `defaultValue` atributos, embora em alguns casos os valores padrão sejam valores vazios. Os valores de implantação a serem usados para esses parâmetros de modelo são fornecidos pelo [arquivo de parâmetros](#template-parameter-files) de exemplo descrito posteriormente neste tópico.

Para obter mais informações sobre como proteger os parâmetros de modelo, consulte estes tópicos:

* [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Melhorar a segurança para parâmetros de modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Passar valores de parâmetros protegidos com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Outros objetos de modelo geralmente referenciam parâmetros de modelo para que eles possam usar os valores que passam por parâmetros de modelo, por exemplo:

* O [objeto de recursos do modelo](#template-resources), descrito mais adiante neste tópico, define cada recurso no Azure que você deseja criar e implantar, como a [definição de recurso do aplicativo lógico](#logic-app-resource-definition). Esses recursos geralmente usam valores de parâmetro de modelo, como o nome do aplicativo lógico e a localização e as informações de conexão.

* Em um nível mais profundo na definição de recurso do aplicativo lógico, o [objeto de parâmetros da definição de fluxo de trabalho](#workflow-definition-parameters) declara parâmetros para os valores a serem usados no tempo de execução do aplicativo lógico. Por exemplo, você pode declarar parâmetros de definição de fluxo de trabalho para o nome de usuário e a senha que um gatilho HTTP usa para autenticação. Para especificar os valores para parâmetros de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* de sua definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso do aplicativo lógico. Nesse objeto externo `parameters` , você pode referenciar os parâmetros de modelo declarados anteriormente, que podem aceitar valores na implantação de um arquivo de parâmetros.

Ao referenciar parâmetros, as funções e expressões de modelo usam sintaxe diferente e se comportam de forma diferente das expressões e funções de definição de fluxo de trabalho Para obter mais informações sobre essas diferenças, consulte [References to Parameters](#parameter-references) , mais adiante neste tópico.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Práticas recomendadas-parâmetros de modelo

Aqui estão algumas práticas recomendadas para definir parâmetros:

* Declare parâmetros somente para valores que variam de acordo com suas necessidades de implantação. Não declare parâmetros para valores que permanecem os mesmos em diferentes requisitos de implantação.

* Inclua o `defaultValue` atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores que são confidenciais ou devem ser protegidos. Sempre use parâmetros protegidos para nomes de usuário, senhas e segredos. Para ocultar ou proteger os valores de parâmetros confidenciais, siga as orientações nestes tópicos:

  * [Recomendações de segurança para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Melhorar a segurança para parâmetros de modelo](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Passar valores de parâmetros protegidos com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Para diferenciar nomes de parâmetro de modelo dos nomes de parâmetros de definição de fluxo de trabalho, você pode usar nomes de parâmetro de modelo descritivos, por exemplo: `TemplateFabrikamPassword`

Para obter mais práticas recomendadas de modelo, consulte [práticas recomendadas para parâmetros de modelo](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Arquivo de parâmetros de modelo

Para fornecer os valores para parâmetros de modelo, armazene esses valores em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md). Dessa forma, você pode usar arquivos de parâmetros diferentes com base nas suas necessidades de implantação. Este é o formato de nome de arquivo a ser usado:

* Nome do arquivo de modelo do aplicativo lógico: **< *Logic-app-Name* # C0.jsem**
* Nome do arquivo de parâmetros: **< *Logic-app-Name* # C0.parameters.jsem**

Aqui está a estrutura dentro do arquivo de parâmetros, que inclui uma referência do Key Vault para [passar um valor de parâmetro protegido com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Este arquivo de parâmetros de exemplo especifica os valores para os parâmetros de modelo declarados anteriormente neste tópico:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Recursos de modelo

Seu modelo tem um `resources` objeto, que é uma matriz que contém definições para cada recurso a ser criado e implantado no Azure, como a [definição de recurso do aplicativo lógico](#logic-app-resource-definition), as [definições de recurso de conexão](#connection-resource-definitions)e quaisquer outros recursos que seu aplicativo lógico precisa para a implantação.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Os modelos podem incluir definições de recursos para vários aplicativos lógicos, portanto, certifique-se de que todos os recursos do aplicativo lógico especifiquem o mesmo grupo de recursos do Azure. Ao implantar o modelo em um grupo de recursos do Azure usando o Visual Studio, você será solicitado a inserir o aplicativo lógico que deseja abrir. Além disso, o projeto do grupo de recursos do Azure pode conter mais de um modelo, portanto, certifique-se de selecionar o arquivo de parâmetros correto quando solicitado.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Exibir definições de recursos

Para examinar as definições de recursos de todos os recursos em um grupo de recursos do Azure, [Baixe seu aplicativo lógico do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), que é a maneira mais fácil de criar um modelo de aplicativo lógico com parâmetros válido que esteja quase sempre pronto para implantação.

Para obter informações gerais sobre os recursos de modelo e seus atributos, consulte estes tópicos:

* [Recursos – estrutura e sintaxe do modelo do Resource Manager](../azure-resource-manager/templates/template-syntax.md#resources)
* [Práticas recomendadas para recursos de modelo](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definição de recurso de aplicativo lógico

A definição de recurso do fluxo de trabalho do aplicativo lógico [em um modelo](/azure/templates/microsoft.logic/workflows) começa com o `properties` objeto, que inclui essas informações:

* O estado do aplicativo lógico na implantação
* A ID de qualquer conta de integração usada pelo seu aplicativo lógico
* A definição de fluxo de trabalho do aplicativo lógico
* Um `parameters` objeto que define os valores a serem usados em tempo de execução
* Outras informações de recurso sobre seu aplicativo lógico, como nome, tipo, local, quaisquer parâmetros de configuração de tempo de execução e assim por diante

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Aqui estão os atributos que são específicos para sua definição de recurso de aplicativo lógico:

| Atributo | Obrigatório | Type | Descrição |
|-----------|----------|------|-------------|
| `state` | Sim | String | O estado do aplicativo lógico na implantação, em que `Enabled` significa que seu aplicativo lógico está ativo e `Disabled` significa que seu aplicativo lógico está inativo. Por exemplo, se você não estiver pronto para que seu aplicativo lógico fique ativo, mas queira implantar uma versão de rascunho, você poderá usar a `Disabled` opção. |
| `integrationAccount` | Não | Objeto | Se seu aplicativo lógico usa uma conta de integração, que armazena artefatos para cenários B2B (entre empresas), esse objeto inclui o `id` atributo, que especifica a ID da conta de integração. |
| `definition` | Sim | Objeto | A definição de fluxo de trabalho subjacente do aplicativo lógico, que é o mesmo objeto que aparece na exibição de código e é totalmente descrita no tópico [referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md) . Nesta definição de fluxo de trabalho, o `parameters` objeto declara parâmetros para os valores a serem usados no tempo de execução do aplicativo lógico. Para obter mais informações, consulte [definição de fluxo de trabalho e parâmetros](#workflow-definition-parameters). <p><p>Para exibir os atributos na definição de fluxo de trabalho do aplicativo lógico, alterne de "modo de exibição de design" para "exibição de código" no portal do Azure ou no Visual Studio ou usando uma ferramenta como [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Não | Objeto | Os [valores de parâmetro de definição de fluxo de trabalho](#workflow-definition-parameters) a serem usados no tempo de execução do aplicativo lógico As definições de parâmetro para esses valores aparecem dentro do [objeto de parâmetros da definição de fluxo de trabalho](#workflow-definition-parameters). Além disso, se seu aplicativo lógico usar [conectores gerenciados](../connectors/apis-list.md) para acessar outros serviços e sistemas, esse objeto incluirá um `$connections` objeto que define os valores de conexão a serem usados no tempo de execução. |
| `accessControl` | Não | Objeto | Para especificar atributos de segurança para seu aplicativo lógico, como restringir o acesso IP para solicitações de gatilhos ou entradas e saídas de histórico de execução. Para obter mais informações, consulte [proteger o acesso aos aplicativos lógicos](../logic-apps/logic-apps-securing-a-logic-app.md). |
| `runtimeConfiguration` | Não | Objeto | Para especificar qualquer `operationOptions` propriedade que controle a forma como seu aplicativo lógico se comporta em tempo de execução. Por exemplo, você pode executar seu aplicativo lógico no [modo de alta taxa de transferência](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode). |
|||||

Para obter mais informações sobre definições de recursos para esses objetos de aplicativos lógicos, consulte [tipos de recursos Microsoft. Logic](/azure/templates/microsoft.logic/allversions):

* [Definição de recurso de fluxo de trabalho](/azure/templates/microsoft.logic/workflows)
* [Definição de recurso de ambiente do serviço de integração](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Definição de recurso de API gerenciada do ambiente do serviço de integração](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Definição de recurso da conta de integração](/azure/templates/microsoft.logic/integrationaccounts)

* Artefatos da conta de integração:

  * [Definição de recurso do contrato](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Definição de recurso de assembly](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Definição de recurso de configuração de lote](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Definição de recurso de certificado](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Definição de recurso de mapa](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Definição de recurso de parceiro](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Definição de recurso de esquema](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Definição de recurso de sessão](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definição e parâmetros de fluxo de trabalho

A definição de fluxo de trabalho do aplicativo lógico aparece no `definition` objeto, que aparece no `properties` objeto dentro da definição de recurso do aplicativo lógico. Esse `definition` objeto é o mesmo objeto que aparece na exibição de código e é totalmente descrito no tópico [referência de esquema para linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md) . Sua definição de fluxo de trabalho inclui um `parameters` objeto de declaração interno no qual você pode definir novos parâmetros existentes ou editá-los para os valores que são usados pela definição de fluxo de trabalho em tempo de execução. Em seguida, você pode fazer referência a esses parâmetros dentro do gatilho ou ações em seu fluxo de trabalho. Por padrão, esse `parameters` objeto está vazio, a menos que seu aplicativo lógico crie conexões com outros serviços e sistemas por meio de [conectores gerenciados](../connectors/apis-list.md).

Para definir os valores dos parâmetros de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* de sua definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso do aplicativo lógico. Nesse objeto externo `parameters` , você pode fazer referência aos parâmetros de modelo declarados anteriormente, que podem aceitar valores na implantação de um arquivo de parâmetros.

> [!TIP]
>
> Como prática recomendada, não referencie diretamente os parâmetros de modelo, que são avaliados na implantação, de dentro da definição de fluxo de trabalho. Em vez disso, declare um parâmetro de definição de fluxo de trabalho, que você pode definir no `parameters` objeto que está *fora* de sua definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso do aplicativo lógico. Para obter mais informações, consulte [References to Parameters](#parameter-references).

Essa sintaxe mostra onde você pode declarar parâmetros nos níveis de definição de modelo e de fluxo de trabalho, juntamente com o local em que você pode definir esses valores de parâmetro referenciando o modelo e os parâmetros de definição de fluxo de trabalho:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parâmetros de definição de fluxo de trabalho seguro

Para um parâmetro de definição de fluxo de trabalho que manipula informações confidenciais, senhas, chaves de acesso ou segredos em tempo de execução, declare ou edite o parâmetro para usar o `securestring` `secureobject` tipo de parâmetro ou. Você pode fazer referência a esse parâmetro em toda a sua definição de fluxo de trabalho. No nível superior do modelo, declare um parâmetro que tenha o mesmo tipo para lidar com essas informações na implantação.

Para definir o valor do parâmetro de definição de fluxo de trabalho, use o `parameters` objeto que está *fora* de sua definição de fluxo de trabalho, mas ainda *dentro* de sua definição de recurso de aplicativo lógico para referenciar o parâmetro de modelo. Por fim, para passar o valor para o parâmetro de modelo na implantação, armazene esse valor em [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) e faça referência a esse cofre de chaves no [arquivo de parâmetros](#template-parameter-files) que é usado pelo modelo na implantação.

Este modelo de exemplo mostra como você pode concluir essas tarefas definindo parâmetros protegidos quando necessário para que você possa armazenar seus valores no Azure Key Vault:

* Declare parâmetros protegidos para os valores usados para autenticar o acesso.
* Use esses valores nos níveis de definição de modelo e de fluxo de trabalho.
* Forneça esses valores usando um arquivo de parâmetros.

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Arquivo de parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Práticas recomendadas-parâmetros de definição de fluxo de trabalho

Para certificar-se de que o designer de aplicativo lógico possa mostrar corretamente os parâmetros de definição de fluxo de trabalho, siga estas práticas recomendadas:

* Inclua o `defaultValue` atributo, que pode especificar valores vazios, para todos os parâmetros, exceto para valores que são confidenciais ou devem ser protegidos.

* Sempre use parâmetros protegidos para nomes de usuário, senhas e segredos. Para ocultar ou proteger os valores de parâmetros confidenciais, siga as orientações nestes tópicos:

  * [Recomendações de segurança para parâmetros de ação](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Recomendações de segurança para parâmetros em definições de fluxo de trabalho](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Passar valores de parâmetros seguros com Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Para obter mais informações sobre parâmetros de definição de fluxo de trabalho, consulte [parâmetros-linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definições de recurso de conexão

Quando seu aplicativo lógico cria e usa conexões com outros serviços e sistema usando [conectores gerenciados](../connectors/apis-list.md), o objeto do modelo `resources` contém as definições de recurso para essas conexões. Embora você crie conexões de dentro de um aplicativo lógico, as conexões são recursos do Azure separados com suas próprias definições de recurso. Para examinar essas definições de recursos de conexão, [Baixe seu aplicativo lógico do Azure para o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), que é a maneira mais fácil de criar um modelo de aplicativo lógico com parâmetros válido que está, na maioria das vezes, pronto para implantação.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

As definições de recurso de conexão fazem referência aos parâmetros de nível superior do modelo para seus valores, para que você possa fornecer esses valores na implantação usando um arquivo de parâmetros. Verifique se as conexões usam o mesmo grupo de recursos do Azure e o mesmo local que seu aplicativo lógico.

Aqui está um exemplo de definição de recurso para uma conexão do Outlook do Office 365 e os parâmetros de modelo correspondentes:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

A definição de recurso do aplicativo lógico também funciona com definições de recurso de conexão das seguintes maneiras:

* Dentro de sua definição de fluxo de trabalho, o `parameters` objeto declara um `$connections` parâmetro para os valores de conexão a serem usados no tempo de execução do aplicativo lógico. Além disso, o gatilho ou a ação que cria uma conexão usa os valores correspondentes que passam por esse `$connections` parâmetro.

* *Fora* de sua definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso do aplicativo lógico, outro `parameters` objeto define os valores a serem usados no tempo de execução para o `$connections` parâmetro referenciando os parâmetros de modelo correspondentes. Esses valores usam expressões de modelo para referenciar recursos que armazenam com segurança os metadados para as conexões em seu aplicativo lógico.

  Por exemplo, os metadados podem incluir cadeias de conexão e tokens de acesso, que você pode armazenar em [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores para os parâmetros de modelo, você faz referência a esse cofre de chaves no [arquivo de parâmetros](#template-parameter-files) que é usado pelo modelo na implantação. Para obter mais informações sobre as diferenças em parâmetros de referência, consulte [References to Parameters](#parameter-references) , mais adiante neste tópico.

  Quando você abre a definição de fluxo de trabalho do aplicativo lógico na exibição de código por meio do portal do Azure ou do Visual Studio, o `$connections` objeto aparece fora da definição de fluxo de trabalho, mas no mesmo nível. Essa ordenação na exibição de código torna esses parâmetros mais fáceis de referenciar quando você atualiza manualmente a definição de fluxo de trabalho:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A definição de recurso do aplicativo lógico tem um `dependsOn` objeto que especifica as dependências nas conexões usadas pelo seu aplicativo lógico.

Cada conexão que você cria tem um nome exclusivo no Azure. Quando você cria várias conexões com o mesmo serviço ou sistema, cada nome de conexão é anexado com um número, que é incrementado com cada nova conexão criada, por exemplo,, `office365` `office365-1` e assim por diante.

Este exemplo mostra as interações entre a definição de recurso do aplicativo lógico e uma definição de recurso de conexão para o Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         <other-logic-app-resource-information>,
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parâmetros de conexão segura

Para um parâmetro de conexão que manipula informações confidenciais, senhas, chaves de acesso ou segredos, a definição de recurso da conexão inclui um `parameterValues` objeto que especifica esses valores no formato de par nome-valor. Para ocultar essas informações, você pode declarar ou editar os parâmetros de modelo para esses valores usando os `securestring` `secureobject` tipos de parâmetro ou. Em seguida, você pode armazenar essas informações em [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Para passar esses valores para os parâmetros de modelo, você faz referência a esse cofre de chaves no [arquivo de parâmetros](#template-parameter-files) que é usado pelo modelo na implantação.

Aqui está um exemplo que fornece o nome da conta e a chave de acesso para uma conexão de armazenamento de BLOBs do Azure:

**Arquivo de parâmetros**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Modelo**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Autenticar conexões

Após a implantação, seu aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, você ainda deve autorizar quaisquer conexões OAuth para gerar tokens de acesso válidos para [autenticar suas credenciais](../active-directory/develop/authentication-vs-authorization.md). Para obter mais informações, consulte [autorizar conexões OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Algumas conexões dão suporte ao uso de uma entidade de [serviço](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (AD do Azure) para autorizar conexões para um aplicativo lógico que é [registrado no Azure ad](../active-directory/develop/quickstart-register-app.md). Por exemplo, essa Azure Data Lake definição de recurso de conexão mostra como referenciar os parâmetros de modelo que manipulam as informações da entidade de serviço e como o modelo declara esses parâmetros:

**Definição de recurso de conexão**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Atributo | Descrição |
|-----------|-------------|
| `token:clientId` | A ID do aplicativo ou do cliente associada à sua entidade de serviço |
| `token:clientSecret` | O valor de chave associado à entidade de serviço |
| `token:TenantId` | A ID de diretório para seu locatário do Azure AD |
| `token:grantType` | O tipo de concessão solicitado, que deve ser `client_credentials` . Para obter mais informações, consulte [plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definições de parâmetro de modelo**

O objeto de nível superior do modelo `parameters` declara esses parâmetros para a conexão de exemplo:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Para obter mais informações sobre como trabalhar com entidades de serviço, consulte estes tópicos:

* [Criar uma entidade de serviço usando o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Criar uma entidade de serviço do Azure usando Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Criar uma entidade de serviço com um certificado usando Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Referências a parâmetros

Para referenciar parâmetros de modelo, você pode usar expressões de modelo com [funções de modelo](../azure-resource-manager/templates/template-functions.md), que são avaliadas na implantação. As expressões de modelo usam colchetes (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Para referenciar parâmetros de definição de fluxo de trabalho, use [expressões e funções de linguagem de definição de fluxo de trabalho](../logic-apps/workflow-definition-language-functions-reference.md), que são avaliadas no tempo de execução Você pode observar que algumas funções de modelo e funções de definição de fluxo de trabalho têm o mesmo nome. As expressões de definição de fluxo de trabalho começam com o símbolo "at" ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Você pode passar valores de parâmetro de modelo para sua definição de fluxo de trabalho para seu aplicativo lógico usar no tempo de execução. No entanto, evite usar parâmetros de modelo, expressões e sintaxe em sua definição de fluxo de trabalho porque o designer de aplicativo lógico não dá suporte a elementos de modelo. Além disso, a sintaxe do modelo e as expressões podem complicar seu código devido a diferenças em quando as expressões são avaliadas.

Em vez disso, siga estas etapas gerais para declarar e referenciar os parâmetros de definição de fluxo de trabalho a serem usados em tempo de execução, declare e referencie os parâmetros de modelo a serem usados na implantação e especifique os valores a serem passados na implantação usando um arquivo de parâmetros. Para obter detalhes completos, consulte a seção [definição e parâmetros de fluxo de trabalho](#workflow-definition-parameters) anteriormente neste tópico.

1. Crie seu modelo e declare os parâmetros de modelo para os valores a serem aceitos e usados na implantação.

1. Em sua definição de fluxo de trabalho, declare os parâmetros para os valores a serem aceitos e usados no tempo de execução. Em seguida, você pode fazer referência a esses valores em toda a sua definição de fluxo de trabalho.

1. No `parameters` objeto que está *fora* de sua definição de fluxo de trabalho, mas ainda *dentro* da definição de recurso do aplicativo lógico, defina os valores para os parâmetros de definição do fluxo de trabalho referenciando os parâmetros de modelo correspondentes. Dessa forma, você pode passar valores de parâmetro de modelo para seus parâmetros de definição de fluxo de trabalho.

1. No arquivo de parâmetros, especifique os valores para o modelo a ser usado na implantação.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Modelo de exemplo completo

Este é o modelo de exemplo com parâmetros que é usado pelos exemplos deste tópico:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar modelos de aplicativo lógico](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
