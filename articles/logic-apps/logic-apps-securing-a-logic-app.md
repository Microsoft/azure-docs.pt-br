---
title: Acesso seguro aos aplicativos lógicos do Azure
description: Adicionar segurança para Aplicativos Lógicos do Azure, incluindo gatilhos, entradas e saídas, parâmetros e outros serviços
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 65369e5747f17c0e420d5775f1c5704cac9ca21b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341098"
---
# <a name="secure-access-in-azure-logic-apps"></a>Proteger o acesso nos Aplicativos Lógicos do Azure

Aqui estão os elementos em seu aplicativo lógico no qual você pode proteger o acesso:

* [Gatilhos de Webhook ou Solicitação](#secure-triggers)
* [Operações como gerenciar, editar ou exibir](#secure-operations) seu aplicativo lógico
* [Entradas e saídas](#secure-run-history) do histórico de execução do aplicativo lógico
* [Entradas e parâmetros de ação](#secure-action-parameters)
* [Serviços que recebem solicitações](#secure-requests) do seu aplicativo lógico

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Proteger o acesso a gatilhos de solicitação

Quando seu aplicativo lógico usa um gatilho baseado em solicitação HTTP, como o gatilho [Solicitação](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md), você pode restringir o acesso para que somente clientes autorizados possam iniciar seu aplicativo lógico. Todas as solicitações recebidas por um aplicativo lógico são criptografadas e protegidas com o protocolo SSL (Secure Sockets Layer). Aqui estão as diferentes maneiras de proteger o acesso a esse tipo de gatilho:

* [Gerar assinaturas de acesso compartilhado](#sas)
* [Restringir endereços IP de entrada](#restrict-incoming-ip-addresses)
* [Adicionar Azure Active Directory, OAuth ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Gerar assinaturas de acesso compartilhado

Cada ponto de extremidade de solicitação em um aplicativo lógico inclui uma [SAS (Assinatura de Acesso Compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) na URL do ponto de extremidade. Cada URL contém um parâmetro de consulta `sp`, `sv` e `sig`:

* O `sp` especifica as permissões, que são mapeadas para os métodos HTTP permitidos para uso.
* O `sv` especifica a versão usada para gerar a assinatura.
* `sig` é usado para autenticar o acesso ao gatilho.

A assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos de URL e propriedades. A chave secreta nunca é exposta nem publicada e é mantida criptografada e armazenada como o aplicativo lógico. Seu aplicativo lógico autoriza somente gatilhos que contenham uma assinatura válida criada com a chave secreta. 

Eis aqui mais informações sobre como proteger o acesso com Assinatura de Acesso Compartilhado:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de chamada prestes a expirar](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para regenerar uma nova chave de acesso seguro a qualquer momento, use a API REST do Azure ou o portal do Azure. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não estão mais autorizadas a disparar o aplicativo lógico. As URLs obtidas após a regeneração são assinadas com a nova chave de acesso.

1. No portal do Azure, abra o aplicativo lógico que tem a chave que você deseja regenerar.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Chaves de Acesso**.

1. Selecione a chave que você deseja regenerar e conclua o processo.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Criar URLs de retorno de chamada com datas de término

Se você compartilhar uma URL de um ponto de extremidade de gatilho com base em solicitação com outras partes, poderá gerar URLs de retorno de chamada com chaves e datas de término específicas conforme necessário. Então você pode reverter ou restringir facilmente o acesso para o disparo de seu aplicativo lógico a um período específico. Você pode especificar uma data do término para uma URL usando a [API REST de Aplicativos Lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter`propriedade usando uma cadeia de caracteres de data JSON. Essa propriedade retorna uma URL de retorno de chamada válida somente até a data e hora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando você gera ou lista URLs de retorno de chamada para gatilhos com base em solicitações, você também pode especificar a chave a ser usada para assinar a URL. Você pode gerar uma URL que seja assinada por uma chave específica usando a [API REST de Aplicativos Lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`. Essa propriedade retorna uma URL assinada pela chave segura especificada.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Restringir endereços IP de entrada

Junto com a assinatura de acesso compartilhado, você talvez queira limitar os clientes específicos que podem chamar seu aplicativo lógico.  
Por exemplo, se você gerenciar seu ponto de extremidade de solicitação com o Gerenciamento de API do Azure, poderá restringir seu aplicativo lógico a aceitar somente solicitações do endereço IP da instância de Gerenciamento de API. 

#### <a name="set-ip-ranges---azure-portal"></a>Definir intervalos IP – portal do Azure

Para configurar essa restrição no portal do Azure, vá para as configurações do aplicativo lógico: 

1. No portal do Azure, abra o aplicativo lógico no Designer de Aplicativo lógico. 

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **Configuração de controle de acesso** > 
**Endereços IP de entrada permitidos**, selecione **Intervalos IP específicos**.

1. Em **Intervalos de IP para gatilhos**, especifique os intervalos de endereço IP que aceitam o gatilho. Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x* 

Se você quer que seu aplicativo lógico seja acionado apenas como um aplicativo lógico aninhado, na lista **Endereços IP de entrada permitidos**, selecione **Somente outros Aplicativos Lógicos**. Essa opção grava uma matriz vazia em seu recurso de aplicativo lógico de modo que somente chamadas do serviço de Aplicativos Lógicos (aplicativos lógicos pai) possam disparar o aplicativo lógico aninhado.

> [!NOTE]
> Independentemente do endereço IP, você ainda pode executar um aplicativo lógico que tenha um gatilho baseado em solicitação usando `/triggers/{triggerName}/run` por meio da API REST do Azure ou do Gerenciamento de API. Porém, esse cenário ainda requer autenticação em relação à API REST do Azure e todos os eventos aparecem no Log de Auditoria do Azure. Defina políticas de controle de acesso adequadamente.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Definir intervalos IP – modelo de implantação de aplicativo lógico

Se você estiver automatizando implantações de aplicativo lógico usando um [modelo de implantação do Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), poderá definir os intervalos de IP nesse modelo, por exemplo:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory, OAuth ou outra segurança

Para adicionar mais protocolos de autorização ao seu aplicativo lógico, considere usar [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/). Esse serviço oferece monitoramento avançado, segurança, política e documentação para qualquer ponto de extremidade e possibilita expor seu aplicativo lógico como uma API. O Gerenciamento de API pode expor um ponto de extremidade público ou privado para seu aplicativo lógico, que então pode usar Azure Active Directory, OAuth, certificado ou outros padrões de segurança. Quando o Gerenciamento de API recebe uma solicitação, o serviço envia a solicitação ao aplicativo lógico, fazendo também quaisquer transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o Gerenciamento de API dispare seu aplicativo lógico, você pode usar as configurações de intervalo IP de entrada do aplicativo lógico. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Proteger o acesso a operações de aplicativo lógico

Para permitir que somente usuários ou grupos específicos executem operações em seu aplicativo lógico, você pode restringir o acesso em tarefas, como gerenciar, editar e exibir. Aplicativos Lógicos dão suporte a [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/role-assignments-portal.md), que você pode personalizar ou atribuir funções internas a membros em sua assinatura, por exemplo:

* [Colaborador de Aplicativo Lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite gerenciar aplicativos lógicos, mas você não pode alterar o acesso a eles.

* [Operador de Aplicativo Lógico](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite que você leu, ativar e desativar aplicativos lógicos, mas você não pode editar ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar [Bloqueio de Recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md). Essa funcionalidade ajuda a evitar que outras pessoas alterem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Proteger o acesso ao histórico de execução do aplicativo lógico

Para proteger o conteúdo transmitido como entradas ou saídas de execuções do aplicativo lógico anteriores, você pode restringir o acesso aos intervalos de endereços IP específicos. Essa funcionalidade oferece a você mais controle de acesso. Todos os dados em execução de um aplicativo lógico são criptografados durante o trânsito e em repouso. Quando você solicita o histórico de execução de um aplicativo lógico, Aplicativos Lógicos autenticam a solicitação e fornecem links para as entradas e saídas das solicitações e das respostas no fluxo de trabalho do aplicativo lógico. Você pode proteger esses links para que somente as solicitações de um endereço IP específico retornem esse conteúdo. Por exemplo, você pode até mesmo especificar um endereço IP como `0.0.0.0-0.0.0.0` para que ninguém possa acessar as entradas e saídas. Apenas uma pessoa com permissões de administrador pode remover essa restrição, dando a possibilidade de acesso "Just-In-Time" ao conteúdo do aplicativo lógico.

### <a name="set-ip-ranges---azure-portal"></a>Definir intervalos IP – portal do Azure

Para configurar essa restrição no portal do Azure, vá para as configurações do aplicativo lógico:

1. No portal do Azure, abra o aplicativo lógico no Designer de Aplicativo lógico. 

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **Configuração de controle de acesso** > 
   **Endereços IP de entrada permitidos**, selecione **Intervalos IP específicos**.

1. Em **intervalos IP para conteúdo**, especifique os intervalos de endereços IP que podem acessar o conteúdo de entradas e saídas. 
   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Definir intervalos IP – modelo de implantação de aplicativo lógico

Se você estiver automatizando implantações de aplicativo lógico usando um [modelo de implantação do Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), poderá definir os intervalos de IP nesse modelo, por exemplo:

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Proteger entradas e parâmetros de ação

Ao implantar entre vários ambientes, você talvez queira parametrizar elementos específicos na definição de fluxo de trabalho do aplicativo lógico. Dessa forma, você pode fornecer entradas com base nos ambientes que você usa e proteger informações confidenciais. Por exemplo, se você estiver autenticando ações HTTP com [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), defina e proteja os parâmetros que aceitam a ID e o segredo do cliente usados para autenticação. Para esses parâmetros, a definição de aplicativo lógico tem a própria seção `parameters`.
Para acessar os valores do parâmetro durante o tempo de execução, é possível usar a expressão `@parameters('parameterName')`, fornecida pela [Linguagem de Definição de Fluxo de Trabalho](https://aka.ms/logicappsdocs). 

Para proteger parâmetros e valores que você não deseja mostrar ao editar seu aplicativo lógico ou exibir o histórico de execução, você pode definir os parâmetros com o tipo `securestring` e usar a codificação conforme necessário. Os parâmetros que têm esse tipo não são retornados com a definição de recurso e não são acessíveis ao exibir o recurso após a implantação.

> [!NOTE]
> Se você usa um parâmetro em cabeçalhos ou no corpo da solicitação, esse parâmetro pode ficar visível ao acessar o histórico de execuções do aplicativo lógico e a solicitação HTTP de saída. Defina também políticas de acesso ao conteúdo apropriadamente.
> Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, ele não será recuperável.

Para obter mais informações sobre como proteger parâmetros na lógica de definições de aplicativo, veja [Proteger parâmetros nas definições da lógica de aplicativo](#secure-parameters-workflow) posteriormente nesta página.

Se você estiver automatizando implantações com [modelos de implantação do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), também poderá usar parâmetros protegidos nesses modelos. Por exemplo, você pode usar parâmetros para obtenção de segredos do KeyVault ao criar seu aplicativo lógico. Sua definição de modelo de implantação tem a própria seção `parameters`, separada da seção `parameters` de seu aplicativo de lógica. Para obter mais informações sobre como proteger parâmetros nos modelos de implantação, veja [Proteger parâmetros nos modelos de implantação](#secure-parameters-deployment-template) posteriormente nesta página.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Proteger parâmetros nas definições de aplicativo lógico

Para proteger informações confidenciais em sua definição de fluxo de trabalho do aplicativo lógico, use parâmetros de seguros para que essas informações não estejam visíveis depois de salvar seu aplicativo lógico. Por exemplo, suponha que você esteja usando autenticação `Basic` em uma definição de ação HTTP. Este exemplo inclui uma seção `parameters` que define os parâmetros para a definição de ação além de uma seção `authentication` que aceita valores de parâmetro `username` e `password`. Para fornecer valores para esses parâmetros, você pode usar um arquivo de parâmetros separado, por exemplo:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

Se você usar segredos, poderá obter esses segredos no momento da implantação usando [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Proteger parâmetros nos modelos de implantação do Azure Resource Manager

Este exemplo mostra um modelo de implantação do Resource Manager que usa mais de um parâmetro de tempo de execução com o tipo `securestring`:

* `armTemplatePasswordParam`, que é a entrada para o parâmetro `logicAppWfParam` da definição de aplicativo lógico

* `logicAppWfParam`, que é a entrada para a ação HTTP usando autenticação Básica

Este exemplo inclui uma seção `parameters` interna, que pertence à sua definição de fluxo de trabalho do aplicativo lógico, e uma seção `parameters` externa, que pertence ao seu modelo de implantação. Para especificar os valores de ambiente para os parâmetros, você pode usar um arquivo de parâmetros separado. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Se você usar segredos, poderá obter esses segredos no momento da implantação usando [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Proteger o acesso aos serviços que recebem solicitações

Aqui estão algumas maneiras de proteger qualquer ponto de extremidade em que seu aplicativo lógico precise de acesso e envie solicitações.

### <a name="add-authentication-on-outbound-requests"></a>Adicionar autenticação em solicitações de saída

Ao trabalhar com uma ação HTTP, HTTP + Swagger (API Open) ou Webhook, você pode adicionar autenticação para a solicitação enviada pelo seu aplicativo lógico. Por exemplo, você usar autenticação Básica, autenticação de certificado ou autenticação do Azure Active Directory. Para obter mais informações, veja [Autenticar gatilhos ou ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Restringir acesso a endereços IP de aplicativo lógico

Todas as chamadas de aplicativos lógicos vêm de endereços IP específicos designados com base na região. Você pode adicionar filtragem que aceite solicitações somente de endereços IP. Para esses endereços IP, confira [Limites e configuração para Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Conectividade local segura

Os Aplicativos Lógicos do Azure fornecem integração com esses serviços para comunicação local segura e confiável.

#### <a name="on-premises-data-gateway"></a>Gateway de dados local

Muitos conectores gerenciados de Aplicativos Lógicos do Azure fornecem conexões seguras com sistemas locais, como Sistema de Arquivos, SQL, SharePoint, DB2 e outros. O gateway envia dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba [como o gateway de dados local funciona](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Gerenciamento de API do Azure

O [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) fornece opções de conexão locais, como rede privada virtual site a site e integração do ExpressRoute para proxy seguro e comunicação com sistemas locais. No Designer de Aplicativo Lógico, você pode selecionar uma API exposta pelo Gerenciamento de API do seu fluxo de trabalho do aplicativo lógico, fornecendo acesso rápido a sistemas locais.

## <a name="next-steps"></a>Próximas etapas

* [Criar um modelo de implantação](logic-apps-create-deploy-template.md)  
* [Manipulação de exceção](logic-apps-exception-handling.md)  
* [Monitorar seus aplicativos lógicos](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosticar falhas e problemas do aplicativo lógico](logic-apps-diagnosing-failures.md)  
