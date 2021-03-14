---
title: Habilitar uma identidade gerenciada para eventos de roteamento (visualização)-Portal
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar uma identidade atribuída pelo sistema para o gêmeos digital do Azure e usá-la para encaminhar eventos, usando o portal do Azure.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703075"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Habilitar uma identidade gerenciada para roteamento de eventos de gêmeos digital do Azure (versão prévia): portal do Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Este artigo descreve como habilitar uma [identidade atribuída pelo sistema para uma instância do gêmeos digital do Azure](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (atualmente em versão prévia) e usar a identidade ao encaminhar eventos para destinos com suporte, como [Hub de eventos](../event-hubs/event-hubs-about.md), destinos do [barramento de serviço](../service-bus-messaging/service-bus-messaging-overview.md)   e [contêiner de armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

Este artigo percorre o processo usando o [**portal do Azure**](https://portal.azure.com).

Aqui estão as etapas abordadas neste artigo: 

1. Crie uma instância do gêmeos digital do Azure com uma identidade atribuída pelo sistema ou habilite a identidade atribuída pelo sistema em uma instância existente do gêmeos do Azure digital. 
1. Adicione uma função ou funções apropriadas à identidade. Por exemplo, atribua a função de **remetente de dados do hub de eventos do Azure** à identidade se o ponto de extremidade for Hub de eventos ou **função de remetente de dados do barramento de serviço do Azure** se o ponto de extremidade for barramento de serviço.
1. Crie um ponto de extremidade no Azure digital gêmeos que possa usar identidades atribuídas pelo sistema para autenticação.

## <a name="enable-system-managed-identities-for-an-instance"></a>Habilitar identidades gerenciadas pelo sistema para uma instância 

Quando você habilita uma identidade atribuída pelo sistema em sua instância de gêmeos digital do Azure, o Azure cria automaticamente uma identidade para ela no [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Essa identidade pode então ser usada para autenticar os pontos de extremidade do Azure digital gêmeos para encaminhamento de eventos.

Você pode habilitar identidades gerenciadas pelo sistema para uma instância do gêmeos digital do Azure **como parte da configuração inicial da instância** ou **habilitá-la posteriormente em uma instância que já existe**.

Qualquer um desses métodos de criação fornecerá as mesmas opções de configuração e o mesmo resultado final para sua instância. Esta seção descreve como fazer as duas.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adicionar uma identidade gerenciada pelo sistema durante a criação da instância

Nesta seção, você aprenderá a habilitar uma identidade gerenciada pelo sistema em uma instância do gêmeos digital do Azure que está sendo criada no momento. Esta seção se concentra na etapa de identidade gerenciada do processo de criação; para obter uma explicação completa da criação de uma nova instância de gêmeos digital do Azure, consulte [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md).

A opção de identidade gerenciada pelo sistema está localizada na guia **avançado** da configuração da instância.

Nessa guia, selecione a opção **ativado** para **identidade gerenciada pelo sistema** para ativar esse recurso.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Captura de tela da portal do Azure mostrando a guia Avançado da caixa de diálogo Criar recurso para o gêmeos digital do Azure. Há um realce em volta do nome da guia, a opção on para a identidade gerenciada pelo sistema e os botões de navegação (revisão + criar, anterior, próximo: avançado).":::

Você pode usar os botões de navegação inferiores para continuar com o restante da configuração da instância.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adicionar uma identidade gerenciada pelo sistema a uma instância existente

Nesta seção, você adicionará uma identidade gerenciada pelo sistema a uma instância do gêmeos digital do Azure que já existe.

1. Primeiro, navegue até o [portal do Azure](https://portal.azure.com) em um navegador.

1. Procure o nome da sua instância na barra de pesquisa do portal e selecione-o para exibir seus detalhes.

1. Selecione **identidade (versão prévia)** no menu à esquerda.

1. Nessa página, selecione a opção **Ativar** para ativar esse recurso.

1. Pressione o botão **salvar** e **Sim** para confirmar.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Captura de tela da portal do Azure mostrando a página de identidade (versão prévia) para uma instância do gêmeos digital do Azure. Há um realce em volta do nome da página no menu da instância do gêmeos digital do Azure, a opção on para status, o botão salvar e o botão de confirmação Sim.":::

Depois que a alteração for salva, mais campos aparecerão nessa página para a **ID de objeto** e **as permissões** da nova identidade.

Você pode copiar a **ID de objeto** aqui, se necessário, e usar o botão **permissões** para exibir as funções do Azure que são atribuídas à identidade. Para configurar algumas funções, continue na próxima seção.

## <a name="assign-azure-roles-to-the-identity"></a>Atribuir funções do Azure à identidade 

Depois que uma identidade atribuída pelo sistema for criada para sua instância de gêmeos digital do Azure, você precisará atribuir as funções apropriadas para autenticar com diferentes tipos de [pontos de extremidade](concepts-route-events.md) para encaminhar eventos para destinos com suporte. Esta seção descreve as opções de função e como atribuí-las à identidade atribuída pelo sistema.

>[!NOTE]
> Essa é uma etapa importante — sem ele, a identidade não será capaz de acessar seus pontos de extremidade e eventos não serão entregues.

### <a name="supported-destinations-and-azure-roles"></a>Destinos com suporte e funções do Azure 

Aqui estão as funções mínimas que uma identidade precisa para acessar um ponto de extremidade, dependendo do tipo de destino. Funções com permissões mais altas (como funções de proprietário de dados) também funcionarão.

| Destino | Função do Azure |
| --- | --- |
| Hubs de eventos do Azure | Remetente de Dados dos Hubs de Eventos do Azure |
| Barramento de Serviço do Azure | Remetente de Dados do Barramento de Serviço do Azure |
| Contêiner de armazenamento do Azure | Colaborador de dados de blob de armazenamento |

Para obter mais informações sobre pontos de extremidade, rotas e os tipos de destinos com suporte para roteamento no Azure digital gêmeos, consulte [*conceitos: rotas de eventos*](concepts-route-events.md).

### <a name="assign-the-role"></a>Atribuir a função

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Para atribuir uma função à identidade, comece abrindo o [portal do Azure](https://portal.azure.com).

1. Navegue até o recurso de ponto de extremidade (seu hub de eventos, tópico do barramento de serviço ou contêiner de armazenamento) pesquisando seu nome na barra de pesquisa do Portal. 
1. Selecione **controle de acesso (iam)** no menu à esquerda.
1. Selecione o botão **+ Adicionar** para adicionar uma nova atribuição de função.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Captura de tela da portal do Azure mostrando a página de controle de acesso (IAM) para um hub de eventos. O botão + Adicionar é realçado." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Na página **Adicionar atribuição de função** a seguir, preencha os valores:
    * **Função**: selecione a função desejada no menu suspenso.
    * **Atribuir acesso a**: em **identidade gerenciada atribuída pelo sistema**, selecione **digital gêmeos**.
    * **Assinatura**: Selecione sua assinatura. Isso exibirá todas as identidades gerenciadas do gêmeos digital do Azure na assinatura selecionada.
    * **Selecione**: aqui, você selecionará a identidade gerenciada de sua instância do gêmeos digital do Azure que está sendo atribuída à função. O nome da identidade gerenciada corresponde ao nome da instância, portanto, escolha o nome da instância de gêmeos digital do Azure. Ao selecioná-lo, a identidade da instância será exibida na seção **Membros selecionados** na parte inferior do painel.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Preenchendo os campos listados na caixa de diálogo ' Adicionar atribuição de função '":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Quando tiver terminado de inserir os detalhes, selecione **salvar**.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Criar um ponto de extremidade com autenticação baseada em identidade

Depois de configurar uma identidade gerenciada pelo sistema para sua instância do gêmeos digital do Azure e atribuir a ela as funções apropriadas, você pode criar [pontos de extremidade](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) gêmeos do Azure digital que são capazes de usar a identidade para autenticação. Essa opção só está disponível para os pontos de extremidade do hub de eventos e do tipo de barramento de serviço (não há suporte para a grade de eventos).

>[!NOTE]
> Você não pode editar um ponto de extremidade que já foi criado com a identidade baseada em chave para mudar para a autenticação baseada em identidade. Você deve escolher o tipo de autenticação quando o ponto de extremidade é criado pela primeira vez.

Siga as [instruções para criar um ponto de extremidade de gêmeos digital do Azure](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Quando você chegar à etapa de concluir os detalhes necessários para o tipo de ponto de extremidade, certifique-se de selecionar **baseado em identidade** para o tipo de autenticação.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Captura de tela da criação de um ponto de extremidade do tipo Hub de eventos." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Conclua a configuração de seu ponto de extremidade e selecione **salvar**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerações sobre a desabilitação de identidades gerenciadas pelo sistema

Como uma identidade é gerenciada separadamente dos pontos de extremidade que o utilizam, é importante considerar os efeitos que qualquer alteração na identidade ou suas funções podem ter nos pontos de extremidade em sua instância de gêmeos digital do Azure. Se a identidade estiver desabilitada ou se uma função necessária para um ponto de extremidade for removida, o ponto de extremidade poderá se tornar inacessível e o fluxo de eventos será interrompido.

Para continuar usando um ponto de extremidade que foi configurado com uma identidade gerenciada que agora foi desabilitada, você precisará excluir o ponto de extremidade e [recriá-lo](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) com um tipo de autenticação diferente. Pode levar até uma hora para que os eventos retomem a entrega para o ponto de extremidade após essa alteração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre identidades gerenciadas no Azure AD: 
* [*Identidades gerenciadas dos recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md)