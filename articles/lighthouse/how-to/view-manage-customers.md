---
title: Exibir e gerenciar clientes e recursos delegados no portal do Azure
description: Como um provedor de serviços que usa o gerenciamento de recursos delegados do Azure, é possível exibir todos os seus recursos e assinaturas de cliente delegados acessando meus clientes no portal do Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810840"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Exibir e gerenciar clientes e recursos delegados

Provedores de serviço que usam o [gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md) podem usar a página **Meus clientes** no [portal do Azure](https://portal.azure.com) para exibir recursos e assinaturas de clientes delegados. Embora nos refiramos aqui aos provedores de serviços e clientes, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Para acessar a página **Meus clientes** no portal do Azure, selecione **Todos os serviços**, pesquise **Meus clientes** e selecione-o. Também é possível encontrá-lo inserindo "Meus clientes" na caixa de pesquisa próxima à parte superior do portal do Azure.

Tenha em mente que a página **Meus clientes** mostra apenas informações sobre os clientes que delegaram assinaturas ou grupos de recursos. Se você trabalhar com outros clientes (por exemplo, por meio do [programa Provedor de Soluções na Nuvem](https://docs.microsoft.com/partner-center/csp-overview), você não verá informações sobre esses clientes aqui, a menos que integre seus recursos para o gerenciamento de recursos delegados.

> [!NOTE]
> Seus clientes podem exibir informações sobre provedores de serviços navegando até **Provedores de serviços** no portal do Azure. Para saber mais, confira [Exibir e gerenciar provedores de serviços](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Exibir e gerenciar detalhes do cliente

Para exibir os detalhes do cliente, selecione **Clientes** no lado esquerdo da página **Meus clientes**.

Para cada cliente, você verá o nome, a ID do cliente (ID do locatário) e a oferta associada ao envolvimento do cliente. Na coluna **Delegações**, você verá o número de assinaturas delegadas e/ou o número de grupos de recursos delegados.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de cliente ou filtre por clientes, ofertas ou palavras-chave específicos.

É possível exibir as seguintes informações nesta página:

- Para ver todas as assinaturas, ofertas e delegações associadas a um cliente, selecione o nome dele.
- Para ver mais detalhes sobre uma oferta e suas delegações, selecione o nome dela.
- Para exibir mais detalhes sobre atribuições de acesso para assinaturas ou grupos de recursos delegados, selecione a entrada na coluna **Delegações**.

## <a name="view-delegations"></a>Exibir delegações

As delegações mostram a assinatura/o grupo de recursos que foi delegado, junto com os usuários e permissões que têm acesso a ele. Para exibir essas informações, selecione **Delegações** no lado esquerdo da página **Meus clientes**.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de atribuição de acesso ou filtre por clientes, ofertas ou palavras-chave específicos.

Os usuários e as permissões associados a cada delegação aparecem na coluna **Atribuições de função**. É possível selecionar cada entrada para exibir a lista completa de usuários, de grupos e de entidades de serviço que receberam acesso à assinatura ou grupo de recursos. De lá, é possível selecionar um usuário específico, grupo ou nome da entidade de serviço para obter mais detalhes.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabalhar no contexto de uma assinatura delegada

É possível trabalhar diretamente no contexto de uma assinatura delegada dentro do portal do Azure, sem alternar o diretório no qual você está trabalhando. Para fazer isso:

1. Selecione o ícone **Diretório + Assinatura** próximo à parte superior do portal do Azure.
2. No filtro **Assinatura global**, verifique se somente a caixa dessa assinatura delegada está selecionada. É possível usar a caixa suspensa **Diretórios atuais + delegados** para mostrar apenas as assinaturas em um diretório específico. (Não use a opção **Alterar diretório**, pois isso altera o diretório no qual você está conectado.)

Se você acessar um serviço que dá suporte a [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md), ele usará como padrão o contexto da assinatura delegada que você selecionou. É possível alterar isso seguindo as etapas acima e marcando a caixa **Selecionar tudo** (ou escolhendo uma ou mais assinaturas para trabalhar).

> [!NOTE]
> Se você tiver recebido acesso a um ou mais grupos de recursos, em vez de acessar uma assinatura inteira, poderá selecionar a assinatura à qual o grupo de recursos pertence. Em seguida, você trabalhará no contexto dessa assinatura, mas só será capaz de acessar os grupos de recursos designados.

Também é possível acessar a funcionalidade relacionada a assinaturas delegadas ou a grupos de recursos de dentro de serviços que dão suporte a experiências de gerenciamento entre locatários selecionando a assinatura ou o grupo de recursos de dentro desse serviço.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- Saiba como seus clientes podem [exibir e gerenciar provedores de serviço](view-manage-service-providers.md) acessando **Provedores de serviço** no portal do Azure.
