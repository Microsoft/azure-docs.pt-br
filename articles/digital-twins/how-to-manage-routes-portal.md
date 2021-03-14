---
title: Gerenciar pontos de extremidade e rotas (Portal)
titleSuffix: Azure Digital Twins
description: Consulte como configurar e gerenciar pontos de extremidade e rotas de eventos para dados de gêmeos digital do Azure usando o portal do Azure.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e61d2ff3807b65022d72414615af9fa729359358
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715710"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gerenciar pontos de extremidade e rotas no gêmeos digital do Azure (Portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

No Azure digital gêmeos, você pode rotear [notificações de eventos](how-to-interpret-event-data.md) para serviços de downstream ou recursos de computação conectados. Isso é feito primeiro configurando **pontos de extremidade** que podem receber os eventos. Em seguida, você pode criar [**rotas de eventos**](concepts-route-events.md) que especificam quais eventos gerados pelo Azure digital gêmeos são entregues a quais pontos de extremidade.

Este artigo orienta você pelo processo de criação de pontos de extremidade e rotas usando o [portal do Azure](https://portal.azure.com).

Como alternativa, você também pode gerenciar pontos de extremidade e rotas com as [APIs de rotas de eventos](/rest/api/digital-twins/dataplane/eventroutes), os [SDKS](how-to-use-apis-sdks.md#overview-data-plane-apis)ou a [CLI do gêmeos digital do Azure](how-to-use-cli.md). Para obter uma versão deste artigo que usa esses mecanismos em vez do portal, consulte [*como: gerenciar pontos de extremidade e rotas (APIs e CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

* Você precisará de uma **conta do Azure** (você pode configurar uma gratuitamente [aqui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Você precisará de uma **instância do gêmeos digital do Azure** em sua assinatura do Azure. Se você ainda não tiver uma instância, poderá criar uma usando as etapas em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md). Faça com que os seguintes valores da configuração sejam úteis para uso posterior neste artigo:
    - Nome da instância
    - Resource group

Você pode encontrar esses detalhes no [portal do Azure](https://portal.azure.com) depois de configurar sua instância. Faça logon no portal e procure o nome da sua instância na barra de pesquisa do Portal.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Captura de tela da barra de pesquisa portal do Azure." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Selecione sua instância nos resultados para ver esses detalhes na visão geral da sua instância:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Captura de tela da página de visão geral de uma instância do gêmeos digital do Azure. O nome e o grupo de recursos são realçados.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Criar um ponto de extremidade para o gêmeos digital do Azure

Estes são os tipos de pontos de extremidade com suporte que você pode criar para sua instância:
* [Grade de Eventos](../event-grid/overview.md) 
* [Hubs de Evento](../event-hubs/event-hubs-about.md)
* [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)

Para obter mais informações sobre os diferentes tipos de ponto de extremidade, consulte [*escolher entre os serviços de mensagens do Azure*](../event-grid/compare-messaging-services.md).

Esta seção explica como criar um desses pontos de extremidade no [portal do Azure](https://portal.azure.com).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Criar o ponto de extremidade 

Depois de criar os recursos do ponto de extremidade, você poderá usá-los para um ponto de extremidade do Azure digital gêmeos. Para criar um novo ponto de extremidade, vá para a página da sua instância na [portal do Azure](https://portal.azure.com) (você pode encontrar a instância inserindo seu nome na barra de pesquisa do Portal).

1. No menu instância, selecione _pontos de extremidade_. Em seguida, na página *pontos de extremidade* a seguir, selecione *+ criar um ponto de extremidade*. Isso abrirá a página *criar um ponto de extremidade* , onde você preencherá os campos nas etapas a seguir.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo grade de eventos." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Insira um **nome** para o ponto de extremidade e escolha o **tipo de ponto de extremidade**.

1. Preencha os outros detalhes necessários para o tipo de ponto de extremidade, incluindo sua assinatura e os recursos de ponto de extremidade descritos [acima](#prerequisite-create-endpoint-resources).
    1. Para os pontos de extremidade do hub de eventos e do barramento de serviço, você deve selecionar um **tipo de autenticação**. Você pode usar a autenticação baseada em chave com uma regra de autorização criada previamente ou a autenticação baseada em identidade se usar o ponto de extremidade com uma [identidade gerenciada](concepts-security.md#managed-identity-for-accessing-other-resources-preview) para sua instância do gêmeos digital do Azure. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo Hub de eventos." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Conclua a criação do ponto de extremidade selecionando _salvar_.

>[!IMPORTANT]
> Para usar a autenticação baseada em identidade para o ponto de extremidade com êxito, você precisará criar uma identidade gerenciada para a instância seguindo as etapas em [*como: habilitar uma identidade gerenciada para eventos de roteamento (versão prévia)*](./how-to-enable-managed-identities-portal.md).

Depois de criar seu ponto de extremidade, você pode verificar se o ponto de extremidade foi criado com êxito verificando o ícone de notificação na barra de portal do Azure superior: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Captura de tela de notificação para verificar a criação do ponto de extremidade. O ícone em forma de sino da barra superior do portal está selecionado e há uma notificação informando que ' Endpoint ADT-eh-Endpoint foi criado com êxito '.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Se a criação do ponto de extremidade falhar, observe a mensagem de erro e tente novamente após alguns minutos.

Você também pode exibir o ponto de extremidade que foi criado de volta na página *pontos de extremidade* para sua instância do gêmeos digital do Azure.

Agora, a grade de eventos, o Hub de eventos ou o tópico do barramento de serviço está disponível como um ponto de extremidade dentro do Azure digital gêmeos, sob o nome escolhido para o ponto de extremidade. Normalmente, você usará esse nome como o destino de uma **rota de evento**, que será criada [posteriormente neste artigo](#create-an-event-route).

### <a name="create-an-endpoint-with-dead-lettering"></a>Criar um ponto de extremidade com mensagens mortas

Quando um ponto de extremidade não pode entregar um evento dentro de um determinado período de tempo ou depois de tentar entregar o evento um determinado número de vezes, ele pode enviar o evento não entregue para uma conta de armazenamento. Esse processo é conhecido como **mensagens mortas**.

Para criar um ponto de extremidade com mensagens mortas habilitadas, você deve usar os [comandos da CLI](how-to-use-cli.md) ou as APIs do [plano de controle](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) para criar seu ponto de extremidade, em vez do portal do Azure.

Para obter instruções sobre como fazer isso com essas ferramentas, consulte a versão de [*APIs e CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) deste artigo.

## <a name="create-an-event-route"></a>Criar uma rota de eventos

Para realmente enviar dados do Azure digital gêmeos para um ponto de extremidade, você precisará definir uma **rota de evento**. Essas rotas permitem que os desenvolvedores conectem o fluxo de eventos, em todo o sistema e em serviços downstream. Leia mais sobre as rotas de eventos em [*conceitos: roteamento de eventos do gêmeos digital do Azure*](concepts-route-events.md).

**Pré-requisito**: você precisa criar pontos de extremidade conforme descrito anteriormente neste artigo antes de passar para a criação de uma rota. Você pode prosseguir com a criação de uma rota de evento quando seus pontos de extremidade tiverem concluído a configuração.

>[!NOTE]
>Se você tiver implantado seus pontos de extremidade recentemente, valide que eles concluíram a implantação **antes** de tentar usá-los para uma nova rota de evento. Se não for possível configurar a rota porque os pontos de extremidade não estão prontos, aguarde alguns minutos e tente novamente.

### <a name="creation-steps-with-the-azure-portal"></a>Etapas de criação com o portal do Azure

Uma definição de rota de evento contém estes elementos:
* O nome da rota que você deseja usar
* O nome do ponto de extremidade que você deseja usar
* Um filtro que defina quais eventos são enviados para o ponto de extremidade
    - Para desabilitar a rota para que nenhum evento seja enviado, use um valor de filtro de `false`
    - Para habilitar uma rota sem filtragem específica, use um valor de filtro de `true`
    - Para obter detalhes sobre qualquer outro tipo de filtro, consulte a seção [*filtrar eventos*](#filter-events) abaixo.

Uma única rota pode permitir que várias notificações e tipos de eventos sejam selecionados.

Para criar uma rota de evento, vá para a página de detalhes da instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com) (você pode encontrar a instância inserindo seu nome na barra de pesquisa do Portal).

No menu instância, selecione _rotas de eventos_. Em seguida, na página *rotas de eventos* a seguir, selecione *+ criar uma rota de evento*. 

Na página *criar uma rota de eventos* que é aberta, escolha no mínimo:
* Um nome para a rota no campo _nome_
* O _ponto de extremidade_ que você gostaria de usar para criar a rota 

Para que a rota seja habilitada, você também deve **Adicionar um filtro de rota de evento** de pelo menos `true` . (Deixar o valor padrão de `false` criará a rota, mas nenhum evento será enviado a ela). Para fazer isso, alterne a opção do _Editor avançado_ para habilitá-lo e escreva `true` na caixa de *filtro* .

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Captura de tela da criação de rota de evento para sua instância." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Quando terminar, pressione o botão _salvar_ para criar a rota de eventos.

## <a name="filter-events"></a>Filtrar eventos

Conforme descrito acima, as rotas têm um campo de **filtro** . Se o valor do filtro em sua rota for `false` , nenhum evento será enviado ao seu ponto de extremidade. 

Depois de habilitar o filtro mínimo do `true` , os pontos de extremidade receberão uma variedade de eventos do Azure digital gêmeos:
* Telemetria acionada por [gêmeos digital](concepts-twins-graph.md) usando a API do serviço gêmeos do Azure digital
* Notificações de alteração de propriedade de cópia, disparadas em alterações de propriedade para qualquer cópia na instância de gêmeos digital do Azure
* Eventos de ciclo de vida, acionados quando gêmeos ou relações são criados ou excluídos

Você pode restringir os tipos de eventos que estão sendo enviados definindo um filtro mais específico.

Para adicionar um filtro de eventos durante a criação de uma rota de eventos, use a seção _Adicionar um filtro de rota de eventos_ da página *criar uma rota de eventos* . 

Você pode selecionar entre algumas opções básicas de filtro comum ou usar as opções de filtro avançado para escrever seus próprios filtros personalizados.

#### <a name="use-the-basic-filters"></a>Usar os filtros básicos

Para usar os filtros básicos, expanda a opção _tipos de evento_ e marque as caixas de seleção correspondentes aos eventos que você gostaria de enviar para o ponto de extremidade. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Captura de tela da criação de uma rota de eventos com um filtro básico. Marcando as caixas de seleção dos eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Isso irá preencher automaticamente a caixa de texto de filtro com o texto do filtro que você selecionou:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Captura de tela da criação de uma rota de eventos com um filtro básico. Mostrando o texto do filtro preenchido automaticamente após a seleção dos eventos.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Usar os filtros avançados

Como alternativa, você pode usar a opção de filtro avançado para escrever seus próprios filtros personalizados.

Para criar uma rota de eventos com opções de filtro avançadas, alterne a opção para o _Editor avançado_ para habilitá-la. Em seguida, você pode escrever seus próprios filtros de eventos na caixa de *filtro* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Captura de tela da criação de uma rota de evento com um filtro avançado.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Aqui estão os filtros de rota com suporte. Os detalhes na coluna *esquema de texto de filtro* são o texto que pode ser inserido na caixa de filtro.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Próximas etapas

Leia sobre os diferentes tipos de mensagens de evento que você pode receber:
* [*Como: interpretar dados de evento*](how-to-interpret-event-data.md)