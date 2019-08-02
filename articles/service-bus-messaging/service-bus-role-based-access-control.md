---
title: Versão prévia do RBAC (controle de acesso baseado em função) do Barramento de Serviço do Azure| Microsoft Docs
description: Controle de acesso baseado em função do Barramento de Serviço do Azure
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 6f5390162ce56a0e77ef41740d7e88f3546c5530
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444726"
---
# <a name="active-directory-role-based-access-control-preview"></a>Controle de acesso baseado em função do Active Directory (versão prévia)

O Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (Azure AD). Com o Azure AD, você pode gerenciar contas de usuário e aplicativos especificamente para aplicativos baseados no Azure ou federar sua infraestrutura existente do Active Directory com o Azure AD para logon único em toda a empresa, abrangendo também os recursos do Azure e os aplicativos hospedados do Azure. Em seguida, você pode atribuir essas identidades de usuário e de aplicativo do Azure AD para funções globais e específicas do serviço para permitir acesso aos recursos do Azure.

Para o Barramento de Serviço do Azure, o gerenciamento de namespaces e de todos os recursos relacionados por meio do portal do Azure e da API de gerenciamento de recursos do Azure já está protegido pelo modelo *RBAC (controle de acesso baseado em função)* . Agora, o recurso RBAC para operações de tempo de execução está na versão prévia pública.

Um aplicativo que usa o RBAC do Azure AD não precisa manipular regras e chaves de SAS nem outros tokens de acesso específicos do Barramento de Serviço. O aplicativo cliente interage com o Azure AD para estabelecer um contexto de autenticação e adquirir um token de acesso para o Barramento de Serviço. Com contas de usuário de domínio que exigem logon interativo, o aplicativo nunca lida diretamente com as credenciais.

## <a name="service-bus-roles-and-permissions"></a>Permissões e funções do Barramento de Serviço

O Azure fornece as funções RBAC internas para autorizar o acesso a um namespace do barramento de serviço abaixo:

* [Proprietário de dados do barramento de serviço do Azure (visualização)](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner-preview): Permite o acesso de dados para o namespace do barramento de serviço e suas entidades (filas, tópicos, assinaturas e filtros)

>[!IMPORTANT]
> Damos suporte anteriormente adicionando uma identidade gerenciada para o **"Proprietário"** ou **"Colaborador"** função.
>
> No entanto, privilégios de acesso a dados **"Proprietário"** e **"Colaborador"** função não será respeitada. Se você estivesse usando o **"Proprietário"** ou **"Colaborador"** função, então eles precisarão ser adaptado para utilizar o **"Do Azure Service Bus proprietário dos dados (visualização)"** função.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Usar o Barramento de Serviço com uma conta de usuário de domínio do Azure AD

A seção a seguir descreve as etapas necessárias para criar e executar um aplicativo de exemplo que solicita um Azure interativo entrada do usuário do AD, como conceder acesso do barramento de serviço para essa conta de usuário e como usar essa identidade para acessar os Hubs de eventos.

Esta introdução descreve um aplicativo de console simples, [cujo código está no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Criar uma conta de usuário do Active Directory

A primeira etapa é opcional. Cada assinatura do Azure é emparelhada automaticamente com um locatário do Azure Active Directory e, se você tem acesso a uma assinatura do Azure, sua conta de usuário já está registrada. Isso significa que você já pode usar sua conta.

Se você ainda desejar criar uma conta específica para este cenário, [siga estas etapas](../automation/automation-create-aduser-account.md). Você deve ter permissão para criar contas no locatário do Azure Active Directory, que pode não ser o caso para cenários de empresa maiores.

### <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Em seguida, [criar um namespace de mensagens do barramento de serviço](service-bus-create-namespace-portal.md).

Depois de criar o namespace, navegue para a página **Controle de Acesso (IAM)** no portal e, em seguida, clique em **Adicionar atribuição de função** para adicionar a conta de usuário do Microsoft Azure Active Directory à função Proprietário. Se você usar sua própria conta de usuário e já tiver criado o namespace, você já estará na função Proprietário. Para adicionar uma conta diferente à função, procure o nome do aplicativo Web no campo **Selecionar** do painel **Adicionar permissões** e, em seguida, clique na entrada. Em seguida, clique em **Salvar**.

Agora, a conta de usuário tem acesso ao namespace do Barramento de Serviço e à fila criada anteriormente.

### <a name="register-the-application"></a>Registrar o aplicativo

Antes de executar o aplicativo de exemplo, registre-o no Azure AD e aprove a solicitação de consentimento que permite que o aplicativo acesse o Barramento de Serviço do Azure em seu nome.

Como o aplicativo de exemplo é um aplicativo de console, você deve registrar um aplicativo nativo e adicionar as permissões de API para **Microsoft.ServiceBus** ao conjunto de "permissões necessárias". Os aplicativos nativos também precisam de um **URI de redirecionamento** no Azure AD que funcione como um identificador. O URI não precisa ser um destino de rede. Use `https://servicebus.microsoft.com` para este exemplo, porque o exemplo de código já usa esse URI.

As etapas de registro detalhadas são explicadas [neste tutorial](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Siga as etapas para registrar um aplicativo **Nativo** e, em seguida, siga as instruções de atualização para adicionar a API **Microsoft.ServiceBus** às permissões necessárias. Ao seguir as etapas, anote o **TenantId** e o **ApplicationId**, pois você precisará desses valores para executar o aplicativo.

### <a name="run-the-app"></a>Execute o aplicativo

Antes de executar o exemplo, edite o arquivo App.config e, dependendo do cenário, defina os seguintes valores:

- `tenantId`: Defina-a com o valor **TenantId**.
- `clientId`: Defina-a com o valor **ApplicationId**.
- `clientSecret`: Se você quiser entrar usando o segredo do cliente, crie-o no Microsoft Azure Active Directory. Além disso, use um aplicativo Web ou uma API em vez de um aplicativo nativo. E também adicione o aplicativo no **Controle de Acesso (IAM)** no namespace que você criou anteriormente.
- `serviceBusNamespaceFQDN`: Defina-o com o nome DNS completo do namespace do Barramento de Serviço recém-criado; por exemplo, `example.servicebus.windows.net`.
- `queueName`: Defina-o com o nome da fila criada.
- O URI de redirecionamento que você especificou no aplicativo nas etapas anteriores.

Ao executar o aplicativo de console, você precisará selecionar um cenário, clique em **Logon de Usuário Interativo** digitando seu número e pressione Enter. O aplicativo exibe uma janela de logon, solicita seu consentimento para acessar o Barramento de Serviço e, em seguida, usa o serviço para executar o cenário de envio/recebimento usando a identidade de logon.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
