---
title: Como criar planos para sua oferta de serviço gerenciado no Azure Marketplace
description: Saiba como criar planos para sua oferta de serviço gerenciado no Azure Marketplace usando o Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 624a3a194b451dc1f498a0ded9d68f641b304eab
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918078"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Como criar planos para sua oferta de serviço gerenciado

As ofertas de serviço gerenciado vendidas por meio do Microsoft Commercial Marketplace devem ter pelo menos um plano. Você pode criar uma variedade de planos com diferentes opções na mesma oferta. Esses planos (às vezes chamados de SKUs) podem diferir em termos de versão, monetização ou camadas de serviço. Para obter orientações detalhadas sobre planos, consulte [planos e preços para ofertas de Marketplace comercial](./plans-pricing.md).

## <a name="create-a-plan"></a>Criar um plano

1. Na guia **visão geral do plano** de sua oferta no Partner Center, selecione **+ criar novo plano**.
2. Na caixa de diálogo exibida, em **ID do plano**, insira uma ID de plano exclusiva. Use até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Você não pode modificar a ID do plano depois de selecionar **criar**. Essa ID ficará visível para seus clientes.
3. Na caixa **nome do plano** , insira um nome exclusivo para este plano. Use no máximo 50 caracteres. Esse nome ficará visível para seus clientes.
4. Selecione **Criar**.

## <a name="define-the-plan-listing"></a>Definir a listagem do plano

Na guia **lista de planos** , defina o nome e a descrição do plano como você deseja que eles apareçam no mercado comercial.

1. A caixa **nome do plano** exibe o nome que você forneceu anteriormente para este plano. Você pode alterá-lo a qualquer momento. Esse nome aparecerá no Marketplace comercial como o título do plano da sua oferta.
2. Na caixa **Resumo do plano** , forneça uma breve descrição do seu plano, que pode ser usada nos resultados da pesquisa do Marketplace.
3. Na caixa **Descrição do plano** , explique o que torna esse plano exclusivo e diferente de outros planos dentro de sua oferta.
4. Selecione **salvar rascunho** antes de continuar para a próxima guia.

## <a name="define-pricing-and-availability"></a>Definir preços e disponibilidade

O único modelo de preços disponível para ofertas de serviço gerenciado é **BYOL (traga sua própria licença)**. Isso significa que você cobra seus clientes diretamente por custos relacionados a essa oferta, e a Microsoft não cobra nenhuma taxa.

Você pode configurar cada plano para ser visível para todos (público) ou para apenas um público específico (privado).

> [!NOTE]
> Não há suporte para planos privados com assinaturas estabelecidas por meio de um revendedor do programa CSP (provedor de soluções na nuvem).

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Você pode remover o acesso a uma delegação depois que um cliente aceitar uma oferta somente se você tiver incluído uma Autorização com a Definição de função definida como Função de exclusão de registro de serviços gerenciados ao publicar a oferta. Você também pode entrar em contato com o cliente e pedir que eles removam seu acesso.

## <a name="make-your-plan-public"></a>Torne seu plano público

1. Em **visibilidade do plano**, selecione **público**.
2. Selecione **Salvar rascunho**. Para retornar à guia Visão geral do plano, selecione **visão geral do plano** no canto superior esquerdo.
3. Para criar outro plano para esta oferta, selecione **+ criar novo plano** na guia **visão geral do plano** .

## <a name="make-your-plan-private"></a>Torne seu plano privado

Você concede acesso a um plano privado usando IDs de assinatura do Azure. Você pode adicionar no máximo 10 IDs de assinatura manualmente ou até 10.000 IDs de assinatura usando um. Arquivo CSV.

Para adicionar até 10 IDs de assinatura manualmente:

1. Em **visibilidade do plano**, selecione **privado**.
2. Insira a ID de assinatura do Azure do público ao qual você deseja conceder acesso.
3. Opcionalmente, insira uma descrição desse público na caixa **Descrição** .
4. Para adicionar outra ID, selecione **Adicionar ID (máx. 10)**.
5. Quando você terminar de adicionar IDs, selecione **salvar rascunho**.

Para adicionar até 10.000 IDs de assinatura com um. Arquivo CSV:

1. Em **visibilidade do plano**, selecione **privado**.
2. Selecione o link **Exportar audiência (CSV)** . Isso fará o download de um. Arquivo CSV.
3. Abra o. Arquivo CSV. Na coluna **ID** , insira as IDs de assinatura do Azure às quais você deseja conceder acesso.
4. Na coluna **Descrição**   , você tem a opção de adicionar uma descrição para cada entrada.
5. Na coluna **tipo**   , adicione **SubscriptionId**   a cada linha que tenha uma ID.
6. Salve o arquivo como um. Arquivo CSV.
7. No Partner Center, selecione o link **importar audiência (CSV)** .
8. Na caixa de diálogo **confirmar**   , selecione **Sim** e, em seguida, carregue o. Arquivo CSV.
9. Selecione **salvar rascunho**.

## <a name="technical-configuration"></a>Configurações técnicas

Esta seção cria um manifesto com informações de autorização para gerenciar recursos do cliente. Essas informações são necessárias para habilitar o [gerenciamento de recursos delegados do Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

Examine [locatários, funções e usuários em cenários de Lighthouse do Azure](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais funções têm suporte e as práticas recomendadas para definir suas autorizações.

> [!NOTE]
> Os usuários e funções em suas entradas de autorização serão aplicados a todos os clientes que ativarem o plano. Se você quiser limitar o acesso a um cliente específico, será necessário publicar um plano privado para uso exclusivo.

### <a name="manifest"></a>Manifesto

1. Em **manifesto**, forneça uma **versão** para o manifesto. Use o formato n.n.n (por exemplo, 1.2.5).
2. Insira sua **ID de locatário**. Este é um GUID associado à ID de locatário do Azure Active Directory (Azure AD) da sua organização; ou seja, o locatário de gerenciamento do qual você vai acessar os recursos de seus clientes. Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da conta no lado superior direito do portal do Azure ou selecionando o **Mudar diretório**.

Se você publicar uma nova versão da sua oferta e precisar criar um manifesto atualizado, selecione **+ Novo manifesto**. Certifique-se de aumentar o número de versão da versão de manifesto anterior.

### <a name="authorizations"></a>Autorizações

As autorizações definem as entidades em seu locatário de gerenciamento que podem acessar recursos e assinaturas para clientes que compram o plano. Cada uma dessas entidades é atribuída a uma função interna que concede níveis específicos de acesso.

Você pode criar até 20 autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, você desejará atribuir funções a um grupo de usuários ou entidade de serviço do Azure AD, em vez de uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados. Ao atribuir funções aos grupos do Azure AD, [o tipo de grupo deve ser segurança e não Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para obter recomendações adicionais, confira [Locatários, funções e usuários em cenários do Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md).

Para cada autorização, você precisará fornecer as informações a seguir. Em seguida, você pode selecionar **+ Adicionar autorização** quantas vezes forem necessárias para adicionar mais usuários e definições de função.

* **ID de objeto do AAD**: o identificador do Azure AD de um usuário, grupo de usuários ou aplicativo ao qual serão concedidas determinadas permissões (conforme definido pela definição de função) aos recursos dos seus clientes.
* **Nome de exibição do objeto do AAD**: um nome amigável para ajudar o cliente a entender a finalidade dessa autorização. O cliente verá esse nome ao delegar recursos.
* **Definição de função**: selecione uma das funções internas do Azure ad disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID da entidade de segurança** terá nos recursos de seus clientes. Para obter descrições dessas funções, consulte [funções internas](../role-based-access-control/built-in-roles.md) e [suporte de função para o Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> Conforme as novas funções internas aplicáveis forem adicionadas ao Azure, elas ficarão disponíveis aqui. Pode haver algum atraso antes que apareçam.

* **Funções atribuíveis**: essa opção será exibida somente se você tiver selecionado administrador de acesso de usuário na **definição de função** para essa autorização. Nesse caso, você deve adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **ID de objeto do Azure ad** poderá atribuir essas funções a identidades gerenciadas, o que é necessário para [implantar políticas que podem ser corrigidas](../lighthouse/how-to/deploy-policy-remediation.md). Nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

> [!TIP]
> Para garantir que você possa [remover o acesso a uma delegação](../lighthouse/how-to/remove-delegation.md) se necessário, inclua uma **Autorização** com a **Definição de função** definida como [Função Excluir Atribuição de Registro de Serviços Gerenciados](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.

Depois de concluir todas as seções para seu plano, você pode selecionar **+ criar novo plano** para criar planos adicionais. Quando terminar, selecione **salvar rascunho** antes de continuar.

## <a name="next-steps"></a>Próximas etapas

* [Examinar e publicar](review-publish-offer.md)
