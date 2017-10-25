---
title: "Proteger o acesso aos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Adicione segurança para proteger o acesso a gatilhos, entradas e saídas, parâmetros de ação e serviços usados com fluxos de trabalho em Aplicativos Lógicos do Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 45a4e476f930e0f5f6633dc5b3b35b66dc6dfa20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-access-to-your-logic-apps"></a>Proteger o acesso aos aplicativos lógicos

Há muitas ferramentas disponíveis para ajudar a proteger seu aplicativo lógico.

* Proteger o acesso para acionar um aplicativo lógico (gatilho de solicitação de HTTP)
* Proteger o acesso para gerenciar, editar ou ler um aplicativo lógico
* Proteger o acesso ao conteúdo de entradas e saídas para uma execução
* Proteger parâmetros ou entradas em ações em um fluxo de trabalho
* Proteger o acesso aos serviços que recebem solicitações de um fluxo de trabalho

## <a name="secure-access-to-trigger"></a>Proteger o acesso a gatilhos

Ao trabalhar com um aplicativo lógico que é acionado em uma solicitação HTTP ([Solicitação](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md)), você pode restringir o acesso para que somente clientes autorizados possam acionar o aplicativo lógico. Todas as solicitações para um aplicativo lógico são criptografadas e protegidas por SSL.

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

Cada ponto de extremidade de solicitação para um aplicativo lógico inclui uma [Assinatura de Acesso Compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS) como parte da URL. Cada URL contém um parâmetro de consulta `sp`, `sv`, e `sig`. As permissões são especificadas por `sp`e correspondem aos métodos HTTP permitidos, `sv` é a versão usada para gerar e `sig` é usado para autenticar o acesso para disparar. A assinatura é gerada usando o algoritmo SHA256 com uma chave secreta em todos os caminhos de URL e propriedades. A chave secreta nunca é exposta e publicada e é mantida criptografada e armazenada como parte do aplicativo lógico. Seu aplicativo lógico somente autoriza gatilhos que contêm uma assinatura válida criada com a chave secreta.

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Você pode regenerar uma nova chave segura a qualquer momento por meio da API REST ou do Portal do Azure. Todas as URLs atuais que foram geradas anteriormente usando a chave antiga são invalidadas e têm sua autorização para acionar o aplicativo lógico revogada.

1. No portal do Azure, abra o aplicativo lógico que você cuja chave você deseja regenerar
1. Clique no item de menu **Chaves de Acesso** em **Configurações**
1. Escolha a chave a regenerar e conclua o processo

As URLs obtidas após a regeneração são assinadas com a nova chave de acesso.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Criar URLs de retorno de chamada com uma data de expiração

Se você estiver compartilhando a URL com terceiros, você poderá gerar URLs com chaves específicas e datas de vencimento, conforme necessário. Você poderá então reverter chaves perfeitamente ou então assegurar que o acesso para acionar um aplicativo fique restrito a um determinado período de tempo. Você pode especificar uma data de expiração para uma URL por meio da [API REST dos Aplicativos Lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers):

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `NotAfter` como uma cadeia de caracteres de data JSON, que retorna uma URL de retorno de chamada que só é válida até a data e hora `NotAfter`.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando você gera ou lista URLs de retorno de chamada para gatilhos com base em solicitações, você também pode especificar qual chave usar para acessar a URL.  Você pode gerar uma URL assinada por uma chave específica por meio da [API REST de aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers) da seguinte maneira:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`.  Isso retorna uma URL assinada pela chave segura especificada.

### <a name="restrict-incoming-ip-addresses"></a>Restringir endereços IP de entrada

Além da Assinatura de Acesso Compartilhado, talvez você queira restringir chamando um aplicativo lógico somente de clientes específicos.  Por exemplo, se você gerenciar seu ponto de extremidade por meio do Gerenciamento de API do Azure, você pode restringir o aplicativo lógico para só aceitar a solicitação quando a solicitação é proveniente do endereço IP de instância de Gerenciamento de API.

Essa configuração pode ser definida nas configurações de aplicativo lógico:

1. No portal do Azure, abra o aplicativo lógico cuja chave você deseja regenerar
1. Clique no item de menu **Configuração de Controle de Acesso** em **Configurações**
1. Especifique a lista de intervalos de endereços IP a serem aceitos pelo gatilho

Um intervalo IP válido assume o formato `192.168.1.1/255`. Se quiser que o aplicativo lógico seja acionado apenas como um aplicativo lógico aninhado, selecione a opção **Somente outros aplicativos lógicos**. Essa opção grava uma matriz vazia para o recurso, o que significa que somente chamadas do serviço em si (aplicativos lógicos pai) acionam com êxito.

> [!NOTE]
> Você ainda pode executar um aplicativo lógico com um gatilho de solicitação por meio de `/triggers/{triggerName}/run` da API REST/Gerenciamento, independentemente do IP. Esse cenário requer autenticação em relação à API REST do Azure e todos os eventos apareceriam no Log de Auditoria do Azure. Defina políticas de controle de acesso de apropriadamente.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Definir intervalos de IP na definição de recurso

Se você estiver usando um [modelo de implantação](logic-apps-create-deploy-template.md) para automatizar suas implantações, as configurações de intervalo IP podem ser configuradas no modelo de recurso.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory, OAuth, ou outra proteção

Para adicionar mais protocolos de autorização sobre um aplicativo lógico, o [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) oferece monitoramento avançado, segurança, política e documentação para qualquer ponto de extremidade com a capacidade de expor um aplicativo lógico como uma API. O Gerenciamento de API do Azure pode expor um ponto de extremidade público ou privado para o aplicativo lógico, que pode usar o Azure Active Directory, certificado, OAuth ou outros padrões de segurança. Quando uma solicitação é recebida, o Gerenciamento de API do Azure encaminha a solicitação para o aplicativo lógico (executando quaisquer transformações necessárias ou restrições em andamento). Você pode usar as configurações de intervalo IP de entrada no aplicativo lógico para permitir que somente o aplicativo lógico seja disparado do Gerenciamento de API.

## <a name="secure-access-to-manage-or-edit-logic-apps"></a>Proteger o acesso para gerenciar ou editar aplicativos lógicos

Você pode restringir o acesso a operações de gerenciamento em um aplicativo lógico para que somente usuários ou grupos específicos sejam capazes de realizar operações no recurso. Aplicativos lógicos usam o recurso de [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md) do Azure e podem ser personalizados com as mesmas ferramentas.  Há algumas funções internas que você pode aos atribuir membros da sua assinatura:

* **Colaborador do Aplicativo Lógico** - Fornece acesso para exibir, editar e atualizar um aplicativo lógico.  Não pode remover o recurso ou executar operações de administração.
* **Operador de Aplicativo Lógico** - Pode exibir o aplicativo lógico e histórico de execução e habilitar/desabilitar.  Não é pode editar ou atualizar a definição.

Você também pode usar o [Bloqueio de Recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para evitar a alteração ou exclusão de aplicativos lógicos. Essa funcionalidade é importante para evitar que os recursos de produção sejam alterados ou excluídos.

## <a name="secure-access-to-contents-of-the-run-history"></a>Proteger o acesso ao conteúdo do histórico de execução

Você pode restringir o acesso ao conteúdo de entradas ou saídas de execuções anteriores para intervalos de endereços IP específicos.  

Todos os dados dentro de uma execução de fluxo de trabalho são criptografados em trânsito e em repouso. Quando uma chamada para o histórico de execução é feita, o serviço autentica a solicitação e fornece links para a solicitação e resposta de entradas e saídas. Esse link pode ser protegido para que somente solicitações para exibir o conteúdo de um intervalo de endereços IP designado retornem o conteúdo. Você pode usar essa funcionalidade para controle de acesso adicional. Você pode até mesmo especificar um endereço IP como `0.0.0.0` para que ninguém possa acessar entradas/saídas. Somente alguém com permissões de administrador poderá remover essa restrição, fornecendo a possibilidade para acesso 'just-in-time' ao conteúdo de fluxo de trabalho.

Essa configuração pode ser definida nas configurações de recurso do portal do Azure:

1. No portal do Azure, abra o aplicativo lógico cuja chave você deseja regenerar
1. Clique no item de menu **Configuração de Controle de Acesso** em **Configurações**
1. Especificar a lista de intervalos de endereços IP para acesso ao conteúdo

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Definir intervalos de IP na definição de recurso

Se você estiver usando um [modelo de implantação](logic-apps-create-deploy-template.md) para automatizar suas implantações, as configurações de intervalo IP podem ser configuradas no modelo de recurso.  

``` json
{
    "properties": {
        "definition": {
        },
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
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Proteger parâmetros e entradas em um fluxo de trabalho

Você pode querer parametrizar alguns aspectos de uma definição de fluxo de trabalho para implantação entre ambientes. Além disso, alguns parâmetros podem ser parâmetros seguros que você não deseja que apareçam ao editar um fluxo de trabalho, como uma ID de cliente e o segredo do cliente para a [Autenticação do Azure Active Directory](../connectors/connectors-native-http.md#authentication) de uma ação HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Usar parâmetros e proteger parâmetros

A [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) fornece uma operação `@parameters()` para acessar o valor de um parâmetro de recurso em tempo de execução. Além disso, você pode [especificar parâmetros no modelo de implantação do recurso](../azure-resource-manager/resource-group-authoring-templates.md#parameters). Mas se você especificar o tipo de parâmetro para ser `securestring`, ele não será retornado com o restante da definição de recurso e não poderá ser acessado ao exibir o recurso após a implantação.

> [!NOTE]
> Se o parâmetro for usado nos cabeçalhos ou no corpo de uma solicitação, ele poderá ser visível ao acessar o histórico de execução e a solicitação HTTP de saída. Certifique-se de definir as políticas de acesso ao conteúdo apropriadamente.
> Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Então, se o segredo estiver sendo usado ali, esse segredo não será recuperável.

#### <a name="resource-deployment-template-with-secrets"></a>Modelo de implantação de recursos com segredos

O exemplo a seguir mostra uma implantação que faz referência a um parâmetro seguro de `secret` em tempo de execução. Em um arquivo de parâmetros separado, você poderia especificar o valor de ambiente para o `secret` ou usar o [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) para recuperar os segredos no momento da implantação.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
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
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Proteger o acesso aos serviços que recebem solicitações de um fluxo de trabalho

Há várias maneiras para ajudar a proteger qualquer ponto de extremidade que o aplicativo lógico precisa acessar.

### <a name="using-authentication-on-outbound-requests"></a>Usar a autenticação em solicitações de saída

Ao trabalhar com uma ação HTTP, HTTP + Swagger (API Open) ou Webhook, você pode adicionar autenticação para a solicitação que está sendo enviada. Você pode incluir a autenticação básica, autenticação de certificado ou autenticação do Azure Active Directory. Detalhes sobre como configurar essa autenticação podem ser encontrados [neste artigo](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Restringir o acesso a endereços IP de aplicativo lógico

Todas as chamadas de aplicativos lógicos vêm de um conjunto específico de endereços IP por região. Você pode adicionar filtragem adicional para aceitar somente solicitações desses endereços IP designados. Para obter uma lista desses endereços IP, consulte [limites e configuração do aplicativo lógico](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Conectividade local

Aplicativos lógicos fornecem integração com diversos serviços que fornecem comunicação local segura e confiável.

#### <a name="on-premises-data-gateway"></a>Gateway de dados local

Muitos conectores gerenciados de aplicativo lógico fornecem conectividade segura para sistemas locais, incluindo o sistema de arquivos, SQL, SharePoint, DB2 e muito mais. O gateway retransmite dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba mais sobre [como o gateway de dados funciona](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>Gerenciamento de API do Azure

O [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) tem opções de conectividade local incluindo integração VPN e ExpressRoute site a site para proxy seguro e a comunicação com sistemas locais. No Designer de Aplicativos Lógicos, você pode selecionar rapidamente uma API exposta do Gerenciamento de API do Azure em um fluxo de trabalho, fornecendo acesso rápido a sistemas locais.

## <a name="next-steps"></a>Próximas etapas
[Criar um modelo de implantação](logic-apps-create-deploy-template.md)  
[Manipulação de exceção](logic-apps-exception-handling.md)  
[Monitorar seus aplicativos lógicos](logic-apps-monitor-your-logic-apps.md)  
[Diagnosticando falhas e problemas nos aplicativos lógicos](logic-apps-diagnosing-failures.md)  
