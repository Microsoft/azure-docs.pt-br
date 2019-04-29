---
title: Identidades gerenciadas para recursos do Azure - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar as entidades gerenciadas para os recursos do Azure com os Hubs de Eventos do Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 784d8c9280aeff7224f90ecee0b16c9c30381aeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746874"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Identidades gerenciadas para recursos do Azure com Hubs de Eventos

[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso do Azure que permite criar uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso que concedem permissões personalizadas para acessar os recursos do Azure específicos que seu aplicativo precisa. 

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de tempo de execução. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Hubs de Eventos em execução dentro de um aplicativo de Serviço de Aplicativo do Azure ou em uma máquina virtual com suporte para entidades gerenciadas para recursos do Azure habilitado não precisa lidar com regras e chaves de SAS nem com nenhum outro token de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace dos Hubs de Eventos. Quando o aplicativo se conecta, os Hubs de Eventos associam o contexto da identidade gerenciada com o cliente em uma operação que será mostrada em um exemplo mais adiante neste artigo.

Quando um cliente dos Hubs de Eventos está associado a uma identidade gerenciada, ele pode realizar todas as operações autorizadas. A autorização é concedida ao associar uma identidade gerenciada com funções de Hubs de Eventos. 

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções dos Hubs de Eventos

É possível adicionar apenas uma identidade gerenciada nas funções "Proprietário" ou "Colaborador" de um namespace dos Hubs de Eventos, que concede controle total à identidade em todas as entidades no namespace. No entanto, operações de gerenciamento que alteram a topologia de namespace são inicialmente compatíveis apenas com o Azure Resource Manager. Não é por meio da interface de gerenciamento REST de Hubs de Eventos nativa. Isso também significa que não é possível usar o objeto cliente [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do .NET Framework dentro de uma identidade gerenciada. 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Usar Hubs de Eventos com identidades gerenciadas para Recursos do Azure

A seção a seguir descreve as etapas a seguir:

1. Criar e implantar um aplicativo de exemplo executado em uma identidade gerenciada.
2. Conceda a essa identidade o acesso a um namespace de Hubs de eventos.
3. Como o aplicativo interage com os hubs de eventos usando essa identidade.

Esta introdução descreve um aplicativo Web hospedado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/). As etapas necessárias para um aplicativo hospedado em VM são semelhantes.

### <a name="create-an-app-service-web-application"></a>Criar um aplicativo Web do Serviço de Aplicativo

A primeira etapa é criar um aplicativo ASP.NET do Serviço de Aplicativo. Se você não estiver familiarizado com a forma de fazer isso no Azure, siga [este guia de instruções](../app-service/app-service-web-get-started-dotnet-framework.md). No entanto, em vez de criar um aplicativo MVC, conforme é mostrado no tutorial, crie um aplicativo Web Forms.

### <a name="set-up-the-managed-identity"></a>Configurar a identidade gerenciada

Depois de criar o aplicativo, navegue até o aplicativo Web recém-criado no portal do Azure (também mostrado nas instruções) e, em seguida, navegue até a página **Identidade de Serviço Gerenciada** e habilite o recurso: 

![Página de Identidade de Serviço Gerenciada](./media/event-hubs-managed-service-identity/msi1.png)
 
Depois de habilitar o recurso, uma nova identidade do serviço será criada no Azure Active Directory e configurada no host do Serviço de Aplicativo.

### <a name="create-a-new-event-hubs-namespace"></a>Criar um novo namespace dos Hubs de Eventos

Em seguida, [crie um namespace de Hubs de Eventos](event-hubs-create.md) em uma das regiões do Azure com suporte de versão prévia para identidades gerenciadas para recursos do Azure: **Leste dos EUA**, **Leste dos EUA 2** ou **Europa Ocidental**. 

Navegue até a página **Controle de Acesso (IAM)** do namespace no portal e, em seguida, clique em **Adicionar atribuição de função** para adicionar a identidade gerenciada à função **Proprietário**. Para fazer isso, procure o nome do aplicativo Web no campo **Selecionar** do painel **Adicionar permissões** e, em seguida, clique na entrada. Em seguida, clique em **Salvar**. A identidade gerenciada para o aplicativo Web agora tem acesso ao namespace de Hubs de Eventos e ao hub de eventos criado anteriormente. 

### <a name="run-the-app"></a>Execute o aplicativo

Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Você também pode usar o código do aplicativo Web [deste repositório GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Depois de iniciar o aplicativo, aponte o navegador para EventHubsMSIDemo.aspx. Você também pode defini-lo como sua página inicial. O código pode ser encontrado no arquivo EventHubsMSIDemo.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e os botões **enviar** e **receber** que se conectam aos Hubs de Eventos para enviar ou receber eventos. 

Observe como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de usar o provedor de token SAS (Token de Acesso Compartilhado), o código cria um provedor de token para a identidade gerenciada com a chamada `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Dessa forma, não há segredos a serem salvos e usados. O fluxo do contexto da identidade gerenciada para o Hubs de Eventos e o handshake de autorização são manipulados automaticamente pelo provedor de token, que é um modelo mais simples do que o uso do SAS.

Depois de fazer essas alterações, publique e execute o aplicativo. Você pode obter os dados de publicação corretos baixando e, em seguida, importando um perfil de publicação no Visual Studio:

![Importar o perfil de publicação](./media/event-hubs-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou e, em seguida, clique em **enviar** ou **receber**. 
 
A identidade gerenciada só funciona dentro do ambiente do Azure e somente na implantação do Serviço de Aplicativo em que você a configurou. Identidades gerenciadas não funcionam com slots de implantação do Serviço de Aplicativo no momento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Detalhes dos Preços de Hubs de Evento](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
