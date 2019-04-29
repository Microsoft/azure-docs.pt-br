---
title: Identidades gerenciadas para recursos do Azure com a versão prévia de Barramento de Serviço do Azure | Microsoft Docs
description: Use identidades gerenciadas para recursos do Azure com Barramento de Serviço do Azure
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
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317037"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Identidades gerenciadas para recursos do Azure com Barramento de Serviço 

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso do Azure que permite criar uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso que concedem permissões personalizadas para acessar os recursos do Azure específicos que seu aplicativo precisa.

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de tempo de execução. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Barramento de Serviço em execução dentro de um aplicativo de Serviço de Aplicativo do Azure ou em uma máquina virtual com entidades gerenciadas habilitadas para suporte de recursos do Azure não precisa lidar com regras e chaves de SAS nem com nenhum outro token de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace das Mensagens do Barramento de Serviço. Quando o aplicativo se conecta, o Barramento de Serviço associa o contexto de entidade gerenciada ao cliente em uma operação que será mostrada em um exemplo mais adiante neste artigo. Depois que está associado com uma identidade gerenciada, o cliente do Barramento de Serviço pode realizar todas as operações autorizadas. A autorização é concedida ao associar uma entidade gerenciada com as funções do Barramento de Serviço. 

## <a name="service-bus-roles-and-permissions"></a>Permissões e funções do Barramento de Serviço

Você só pode adicionar uma identidade gerenciada às funções "Proprietário" ou "Colaborador" de um namespace do Barramento de Serviço. Ele concede controle total à identidade em todas as entidades no namespace. No entanto, operações de gerenciamento que alteram a topologia de namespace são inicialmente compatíveis apenas com o Azure Resource Manager. Não é por meio da interface de gerenciamento REST do Barramento de Serviço nativo. Isso também significa que não é possível usar o objeto cliente [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do .NET Framework dentro de uma identidade gerenciada.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usar Barramento de Serviço com identidades gerenciadas para recursos do Azure

A seção a seguir descreve as etapas necessárias para criar e implantar um aplicativo de exemplo que é executado em uma identidade gerenciada, como conceder a essa identidade o acesso a um namespace das Mensagens do Barramento de Serviço e como o aplicativo interage com as entidades do Barramento de Serviço usando essa identidade.

Esta introdução descreve um aplicativo Web hospedado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/). As etapas necessárias para um aplicativo hospedado em VM são semelhantes.

### <a name="create-an-app-service-web-application"></a>Criar um aplicativo Web do Serviço de Aplicativo

A primeira etapa é criar um aplicativo ASP.NET do Serviço de Aplicativo. Se você não estiver familiarizado com a forma de fazer isso no Azure, siga [este guia de instruções](../app-service/app-service-web-get-started-dotnet-framework.md). No entanto, em vez de criar um aplicativo MVC, conforme é mostrado no tutorial, crie um aplicativo Web Forms.

### <a name="set-up-the-managed-identity"></a>Configurar a identidade gerenciada

Depois de criar o aplicativo, navegue até o aplicativo Web recém-criado no portal do Azure (também mostrado nas instruções) e, em seguida, navegue até a página **Identidade de Serviço Gerenciada** e habilite o recurso: 

![](./media/service-bus-managed-service-identity/msi1.png)

Depois de habilitar o recurso, uma nova identidade do serviço será criada no Azure Active Directory e configurada no host do Serviço de Aplicativo.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Criar um novo namespace das Mensagens do Barramento de Serviço

Em seguida, [crie um namespace das Mensagens do Barramento de Serviço](service-bus-create-namespace-portal.md) em uma das regiões do Azure que tenham suporte para a versão prévia do RBAC: **Leste dos EUA**, **Leste dos EUA 2** ou **Europa Ocidental**. 

Navegue até a página **Controle de Acesso (IAM)** do namespace no portal e, em seguida, clique em **Adicionar atribuição de função** para adicionar a identidade gerenciada à função **Proprietário**. Para fazer isso, procure o nome do aplicativo Web no campo **Selecionar** do painel **Adicionar permissões** e, em seguida, clique na entrada. Em seguida, clique em **Salvar**.

A identidade gerenciada do aplicativo Web agora tem acesso ao namespace do Barramento de Serviço e à fila criada anteriormente. 

### <a name="run-the-app"></a>Execute o aplicativo

Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Também é possível usar o código do aplicativo Web [deste repositório GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página Default.aspx é sua página de aterrissagem. O código pode ser encontrado no arquivo Default.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e os botões **enviar** e **receber** que se conectam ao Barramento de Serviço para enviar ou receber mensagens.

Observe como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de usar o provedor de token SAS (Token de Acesso Compartilhado), o código cria um provedor de token para a identidade gerenciada com a chamada `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)`. Dessa forma, não há nenhum segredo a ser retido e uso. O fluxo do contexto de identidade gerenciada para o Barramento de Serviço e o handshake de autorização são manipulados automaticamente pelo provedor de token. É um modelo mais simples que usar SAS.

Depois de fazer essas alterações, publique e execute o aplicativo. Você pode facilmente obter os dados de publicação corretos baixando e, em seguida, importando um perfil de publicação no Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou. Em seguida, clique em **envie** ou **receber**.


> [!NOTE]
> - A identidade gerenciada funciona somente dentro do ambiente do Azure, em serviços de aplicativos, VMs do Azure e nos conjuntos de dimensionamento. Para aplicativos .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication, que é usada pelo pacote NuGet do Barramento de Serviço, fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local. Essa biblioteca também permite testar o código localmente no computador de desenvolvimento, usando a conta de usuário do Visual Studio, a CLI 2.0 do Azure ou a Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte [Autenticação de serviço a serviço para o Azure Key Vault usando .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Atualmente, as identidades gerenciadas não funcionam com slots de implantação do Serviço de Aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)