---
title: Visão geral da integração do parceiro (grade de eventos do Azure)
description: Fornece uma visão geral de como você pode integrar o como um parceiro de grade de eventos.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081188"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Visão geral da integração do parceiro (grade de eventos do Azure)

Este artigo descreve como usar de forma privada os recursos do parceiro da grade de eventos do Azure e como se tornar um tipo de tópico de parceiro disponível publicamente.

Você não precisa de permissão especial para começar a usar os tipos de recursos da Grade de Eventos associados à publicação de eventos como um parceiro de Grade de Eventos. Na verdade, você pode usá-las hoje para publicar eventos de forma privada em suas próprias assinaturas do Azure e testar o modelo de recursos se estiver considerando tornar-se um parceiro.

> [!NOTE]
> Para obter instruções passo a passo sobre como integrar um parceiro de grade de eventos usando o portal do Azure, consulte [como integrar um parceiro de grade de eventos (Portal do Azure)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>Como funcionam os eventos de parceiro
O recurso de eventos de parceiro assume a arquitetura existente que a grade de eventos já usa para publicar eventos de recursos do Azure, como o armazenamento do Azure e o Hub IoT do Azure, e disponibiliza essas ferramentas publicamente para que qualquer pessoa use. O uso dessas ferramentas é privado por padrão somente para sua assinatura do Azure. Para disponibilizar seus eventos publicamente, preencha o formulário e [entre em contato com a equipe da grade de eventos](mailto:gridpartner@microsoft.com).

O recurso de eventos de parceiro permite que você publique eventos na grade de eventos do Azure para consumo multilocatário.

## <a name="onboarding-and-event-publishing-overview"></a>Visão geral da integração e publicação de eventos

### <a name="partner-flow"></a>Fluxo de parceiros

1. Crie um locatário do Azure caso você ainda não tenha um.
1. Use o CLI do Azure para criar uma nova grade de eventos `partnerRegistration` . Esse recurso inclui informações como nome de exibição, descrição, URI de instalação e assim por diante.

    ![Criar um tópico de parceiro](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Crie um ou mais namespaces de parceiro em cada região em que você deseja publicar eventos. O serviço de grade de eventos provisiona um ponto de extremidade de publicação (por exemplo, `https://contoso.westus-1.eventgrid.azure.net/api/events` ) e chaves de acesso.

    ![Criar um namespace de parceiro](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Forneça uma maneira para os clientes se registrarem no seu sistema de que desejam um tópico de parceiro.
1. Entre em contato com a equipe da grade de eventos para informar que você deseja que o tipo de tópico de parceiro se torne público.

### <a name="customer-flow"></a>Fluxo de cliente

1. O cliente visita a portal do Azure para observar a ID de assinatura do Azure e o grupo de recursos em que deseja que o tópico de parceiro seja criado.
1. O cliente solicita um tópico de parceiro por meio do seu sistema. Em resposta, você cria um túnel de eventos para seu namespace de parceiro.
1. A grade de eventos cria um tópico de parceiro **pendente** na assinatura do Azure do cliente e no grupo de recursos.

    ![Criar um canal de eventos](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. O cliente ativa o tópico parceiro por meio do portal do Azure. Os eventos agora podem fluir de seu serviço para a assinatura do Azure do cliente.

    ![Tópico ativar um parceiro](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modelo de recursos
O modelo de recurso a seguir é para eventos de parceiro.

### <a name="partner-registrations"></a>Registros de parceiros
* Recurso: `partnerRegistrations`
* Usado por: Parceiros
* Descrição: captura os metadados globais do parceiro SaaS (software como serviço) (por exemplo, nome, nome de exibição, descrição, URI de instalação).
    
    Criar ou atualizar um registro de parceiro é uma operação de autoatendimento para os parceiros. Essa capacidade de autoatendimento permite que os parceiros criem e testem o fluxo completo de ponta a ponta.
    
    Somente os registros de parceiros aprovados pela Microsoft são detectáveis pelos clientes.
* Escopo: criado na assinatura do Azure do parceiro. Os metadados são visíveis para os clientes depois de serem públicos.

### <a name="partner-namespaces"></a>Namespaces de parceiro
* Recurso: `partnerNamespaces`
* Usado por: Parceiros
* Descrição: Fornece um recurso regional para publicar eventos de cliente. Cada namespace de parceiro tem um ponto de extremidade de publicação e chaves de autenticação. O namespace também é como o parceiro solicita um tópico de parceiro para um determinado cliente e lista os clientes ativos.
* Escopo: vive na assinatura do parceiro.

### <a name="event-channel"></a>Canal de evento
* Recurso: `partnerNamespaces/eventChannels`
* Usado por: Parceiros
* Descrição: os canais de eventos são um espelho do tópico do parceiro do cliente. Ao criar um canal de evento e especificar a assinatura do Azure do cliente e o grupo de recursos nos metadados, você sinalizará a grade de eventos para criar um tópico de parceiro para o cliente. A grade de eventos emite uma chamada Azure Resource Manager para criar um tópico de parceiro correspondente na assinatura do cliente. O tópico de parceiro é criado em um estado pendente. Há um link de um-para-um entre cada canal de evento e o tópico de parceiro.
* Escopo: vive na assinatura do parceiro.

### <a name="partner-topics"></a>Tópicos de parceiros
* Recurso: `partnerTopics`
* Usado por: Clientes
* Descrição: os tópicos de parceiro são semelhantes aos tópicos personalizados e tópicos do sistema na grade de eventos. Cada tópico de parceiro é associado a uma fonte específica (por exemplo, `Contoso:myaccount` ) e a um tipo de tópico de parceiro específico (por exemplo, contoso). Os clientes criam assinaturas de evento no tópico do parceiro para rotear eventos para vários manipuladores de eventos.

    Os clientes não podem criar esse recurso diretamente. A única maneira de criar um tópico de parceiro é por meio de uma operação de parceiro que cria um canal de evento.
* Escopo: reside na assinatura do cliente.

### <a name="partner-topic-types"></a>Tipos de tópico de parceiro
* Recurso: `partnerTopicTypes`
* Usado por: Clientes
* Descrição: os tipos de tópico de parceiro são tipos de recursos de locatários que permitem que os clientes descubram a lista de tipos de tópicos de parceiros aprovados. A URL é semelhante a https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Escopo: Global

## <a name="publish-events-to-event-grid"></a>Publicar eventos na grade de eventos
Ao criar um namespace de parceiro em uma região do Azure, você obtém um ponto de extremidade regional e as chaves de autenticação correspondentes. Publique lotes de eventos nesse ponto de extremidade para todos os canais de evento do cliente nesse namespace. Com base no campo de origem no evento, a grade de eventos do Azure mapeia cada evento com os tópicos de parceiro correspondentes.

### <a name="event-schema-cloudevents-v10"></a>Esquema de evento: CloudEvents v 1.0
Publique eventos na grade de eventos do Azure usando o esquema CloudEvents 1,0. A Grade de Eventos dá suporte ao modo estruturado e ao modo em lote. CloudEvents 1,0 é o único esquema de evento com suporte para namespaces de parceiro.

### <a name="example-flow"></a>Fluxo de exemplo

1.  O serviço de publicação faz um HTTP POST para `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.
1.  Na solicitação, inclua um valor de cabeçalho chamado aeg-sas-key que contém uma chave para autenticação. Essa chave é provisionada durante a criação do namespace do parceiro. Por exemplo, um valor de cabeçalho válido é aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  Defina o cabeçalho Content-Type como "Application/cloudevents-batch + JSON; charset = UTF-8a ".
1.  Execute uma consulta HTTP POST para a URL de publicação com um lote de eventos que correspondem a essa região. Por exemplo: 

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Após o lançamento no ponto de extremidade do namespace do parceiro, você receberá uma resposta. A resposta é um código de resposta HTTP padrão. Algumas respostas comuns são:

| Result                             | Resposta              |
|------------------------------------|-----------------------|
| Sucesso                            | 200 OK                |
| Os dados de evento têm formato incorreto    | 400 Solicitação Inválida       |
| Chave de acesso inválida                 | 401 Não Autorizado      |
| Ponto de extremidade incorreto                 | 404 Não Encontrado         |
| Matriz ou evento excede os limites de tamanho | O conteúdo 413 é muito grande |

## <a name="references"></a>Referências

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modelo de ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Esquema de modelo ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [APIs REST](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Extensão da CLI](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDKs
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Próximas etapas
- [Visão geral dos tópicos de parceiros](partner-events-overview.md)
- [Formulário de integração de tópicos de parceiros](https://aka.ms/gridpartnerform)
- [Tópico de parceiro do Auth0](auth0-overview.md)
- [Como usar o tópico de parceiro do Auth0](auth0-how-to.md)
