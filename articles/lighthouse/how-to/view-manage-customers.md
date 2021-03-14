---
title: Exibir e gerenciar clientes e recursos delegados no portal do Azure
description: Como um provedor de serviços ou uma empresa usando o Azure Lighthouse, você pode exibir todos os seus recursos e assinaturas delegados Acessando meus clientes na portal do Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419321"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Exibir e gerenciar clientes e recursos delegados no portal do Azure

Provedores de serviço usando o [Azure Lighthouse](../overview.md) podem usar a página **meus clientes** no [portal do Azure](https://portal.azure.com) para exibir recursos e assinaturas de clientes delegados.

> [!TIP]
> Embora possamos nos referir aos provedores de serviços e clientes aqui, as [empresas que gerenciam vários locatários](../concepts/enterprise.md) podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **Meus clientes** no portal do Azure, selecione **Todos os serviços**, pesquise **Meus clientes** e selecione-o. Também é possível encontrá-lo inserindo "Meus clientes" na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a seção principais **clientes** da página **meus clientes** mostra apenas informações sobre os clientes que delegaram assinaturas ou grupos de recursos ao seu locatário Azure Active Directory (Azure AD) por meio do Azure Lighthouse. Se você trabalhar com outros clientes (por exemplo, por meio do [programa CSP)](/partner-center/csp-overview), não verá informações sobre esses clientes na seção **clientes** , a menos que tenha [integrado seus recursos ao Lighthouse do Azure](onboard-customer.md) (embora você possa ver detalhes sobre determinados clientes do CSP na [seção provedor de soluções na nuvem (visualização)](#cloud-solution-provider-preview) abaixo na página).

> [!NOTE]
> Seus clientes podem exibir informações sobre provedores de serviços navegando até **Provedores de serviços** no portal do Azure. Para saber mais, confira [Exibir e gerenciar provedores de serviços](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Exibir e gerenciar detalhes do cliente

Para exibir os detalhes do cliente, selecione **Clientes** no lado esquerdo da página **Meus clientes**.

> [!IMPORTANT]
> Para ver essas informações, os usuários devem ter recebido a função [leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclua acesso de leitor) no processo de integração.

Para cada cliente, você verá o nome do cliente, a ID do cliente (ID do locatário) e a **ID da oferta** e a versão da **oferta** associada ao envolvimento. Na coluna **Delegações**, você verá o número de assinaturas delegadas e/ou o número de grupos de recursos delegados.

As opções na parte superior da página permitem que você classifique, filtre e agrupe suas informações de cliente por clientes, ofertas ou palavras-chave específicas.

Você pode exibir as seguintes informações desta página:

- Para ver todas as assinaturas, ofertas e delegações associadas a um cliente, selecione o nome dele.
- Para ver mais detalhes sobre uma oferta e suas delegações, selecione o nome dela.
- Para exibir mais detalhes sobre atribuições de função para assinaturas ou grupos de recursos delegados, selecione a entrada na coluna **Delegações**.

## <a name="view-and-manage-delegations"></a>Exibir e gerenciar delegações

As delegações mostram a assinatura ou o grupo de recursos que foi delegado, junto com os usuários e permissões que têm acesso a ele. Para exibir essas informações, selecione **Delegações** no lado esquerdo da página **Meus clientes**.

As opções na parte superior da página permitem que você classifique, filtre e agrupe essas informações por clientes, ofertas ou palavras-chave específicas.

### <a name="view-role-assignments"></a>Exibir atribuições de função

Os usuários e as permissões associados a cada delegação aparecem na coluna **Atribuições de função**. É possível selecionar cada entrada para exibir a lista completa de usuários, de grupos e de entidades de serviço que receberam acesso à assinatura ou grupo de recursos. De lá, é possível selecionar um usuário específico, grupo ou nome da entidade de serviço para obter mais detalhes.

### <a name="remove-delegations"></a>Remover delegações

Se você tiver incluído usuários com a [função de exclusão de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao integrar um cliente ao Azure Lighthouse, esses usuários poderão remover uma delegação selecionando o ícone de lixeira que aparece na linha para essa delegação. Quando eles fizerem isso, nenhum usuário no locatário do provedor de serviços poderá acessar os recursos que foram previamente delegados.

Para obter mais informações, consulte [remover o acesso a uma delegação](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Exibir atividade de alteração de delegação

A seção **log de atividades** da página **meus clientes** controla cada vez que as assinaturas de clientes ou grupos de recursos são delegados para seu locatário e sempre que os recursos delegados anteriormente são removidos. Essas informações só podem ser exibidas por usuários que foram [atribuídos à função leitor de monitoramento no escopo raiz](monitor-delegation-changes.md).

Para obter mais informações, consulte [Exibir alterações de delegação no portal do Azure](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabalhar no contexto de uma assinatura delegada

Você pode trabalhar diretamente no contexto de uma assinatura delegada dentro do portal do Azure, sem alternar o diretório no qual você está conectado. Para fazer isso:

1. Selecione o ícone **Diretório + Assinatura** próximo à parte superior do portal do Azure.
2. No **filtro de assinatura padrão**, certifique-se de que somente a caixa para essa assinatura delegada esteja selecionada. É possível usar a caixa suspensa **Diretórios atuais + delegados** para mostrar apenas as assinaturas em um diretório específico. (Não use a opção **Alterar diretório**, pois isso altera o diretório no qual você está conectado.)

Se você acessar um serviço que dá suporte a [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md), ele usará como padrão o contexto da assinatura delegada que você selecionou. É possível alterar isso seguindo as etapas acima e marcando a caixa **Selecionar tudo** (ou escolhendo uma ou mais assinaturas para trabalhar).

> [!NOTE]
> Se você tiver recebido acesso a um ou mais grupos de recursos, em vez de acesso a uma assinatura inteira, selecione a assinatura à qual o grupo de recursos pertence. Em seguida, você trabalhará no contexto dessa assinatura, mas só será capaz de acessar os grupos de recursos designados.

Também é possível acessar a funcionalidade relacionada a assinaturas delegadas ou a grupos de recursos de dentro de serviços que dão suporte a experiências de gerenciamento entre locatários selecionando a assinatura ou o grupo de recursos de dentro desse serviço.

## <a name="cloud-solution-provider-preview"></a>Provedor de soluções na nuvem (versão prévia)

Uma seção separada do **provedor de soluções na nuvem (versão prévia)** da página **meus clientes** mostra informações de cobrança e recursos para os clientes do CSP que [assinaram o Microsoft Customer Agreement (MCA)](/partner-center/confirm-customer-agreement) e estão [no plano do Azure](/partner-center/azure-plan-get-started). Para obter mais informações, consulte Introdução [à sua conta de cobrança do Microsoft Partner Agreement](../../cost-management-billing/understand/mpa-overview.md).

Esses clientes do CSP serão exibidos nesta seção se você também os tiver integrado ao Azure Lighthouse. Da mesma forma, um cliente CSP não precisa aparecer na seção **provedor de soluções na nuvem (versão prévia)** de **meus clientes** para que você possa integrá-los ao Azure Lighthouse.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- Saiba como seus clientes podem [exibir e gerenciar provedores de serviço](view-manage-service-providers.md) acessando **Provedores de serviço** no portal do Azure.
