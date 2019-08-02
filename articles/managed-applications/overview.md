---
title: Visão geral de aplicativos gerenciados do Azure | Microsoft Docs
description: Descreve os conceitos de aplicativos gerenciados do Azure
author: tfitzmac
ms.service: managed-applications
ms.topic: overview
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 97d6a229651f1c3fbcdbb79c7ae7d1c1f855882b
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234749"
---
# <a name="azure-managed-applications-overview"></a>Visão geral de aplicativos gerenciados do Azure

Os aplicativos gerenciados do Azure permitem que você ofereça soluções de nuvem fáceis de implantar e operar. Implemente a infraestrutura e forneça suporte contínuo. Para disponibilizar um aplicativo gerenciado para todos os clientes, publique-o no marketplace do Azure. Para disponibilizá-lo somente para usuários em sua organização, publique-o em um catálogo interno. 

Um aplicativo gerenciado é semelhante a um modelo de solução no Marketplace, com uma diferença importante. Em um aplicativo gerenciado, os recursos são implantados em um grupo de recursos gerenciado pelo distribuidor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. Como fornecedor, você deve especificar o custo do suporte contínuo da solução.

## <a name="advantages-of-managed-applications"></a>Vantagens dos aplicativos gerenciados

Os aplicativos gerenciados reduzem as barreiras para os consumidores que usam suas soluções. Eles não precisam de conhecimento especializado sobre a infraestrutura de nuvem para usar sua solução. Os consumidores têm acesso limitado aos recursos críticos e não precisam se preocupar com a possibilidade de cometer erros durante o gerenciamento. 

Os aplicativos gerenciados permitem que você estabeleça uma relação contínua com seus consumidores. Defina as condições para o gerenciamento do aplicativo, e todos os encargos serão manipulados por meio da cobrança do Azure.

Embora os clientes implantem esses aplicativos gerenciados em suas assinaturas, eles não precisam fazer a manutenção, atualizar nem repará-las. Você pode garantir que todos os clientes estejam usando versões aprovadas. Os clientes não precisam desenvolver conhecimento de domínio específico do aplicativo para gerenciarem esses aplicativos. Os clientes adquirem automaticamente atualizações do aplicativo sem precisar se preocupar com o diagnóstico e a solução de problemas com o aplicativo. 

Para equipes de TI, os aplicativos gerenciados permitem a oferta de soluções pré-aprovadas aos usuários na organização. Você sabe que essas soluções estão em conformidade com os padrões organizacionais.

Os Aplicativos Gerenciados dão suporte a [identidades gerenciadas em recursos do Azure](./publish-managed-identity.md).

## <a name="types-of-managed-applications"></a>Tipos de aplicativos gerenciados

Você pode publicar seu aplicativo gerenciado externamente ou internamente.

![Publicar interna ou externamente](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catálogo de serviços

O catálogo de serviços é um catálogo interno das soluções aprovadas para os usuários em uma organização. Use o catálogo para atender aos padrões organizacionais enquanto oferece soluções para as organizações. Os funcionários usam o catálogo para encontrar com facilidade aplicativos recomendados e aprovados pelos departamentos de TI. Eles veem os aplicativos gerenciados que outras pessoas na organização compartilham com eles.

Para obter informações sobre como publicar um aplicativo gerenciado do Catálogo de Serviços, confira [Criar o aplicativo do catálogo de serviços](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Os fornecedores que desejam cobrar por seus serviços podem disponibilizar um aplicativo gerenciado no marketplace do Azure. Depois que o fornecedor publicar um aplicativo, ele ficará disponível para os usuários fora da organização. Com essa abordagem, os MSPs (provedores de serviços gerenciados), ISVs (fornecedores de software independentes) e SIs (integradores de sistema) podem oferecer suas soluções a todos os clientes do Azure.

Para obter informações sobre como publicar aplicativos gerenciados para o Marketplace, confira [Criar aplicativo do marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupos de recursos para aplicativos gerenciados

Normalmente, os recursos de um aplicativo gerenciado estão em dois grupos de recursos. O consumidor gerencia um grupo de recursos, e o fornecedor gerencia o outro grupo de recursos. Ao definir o aplicativo gerenciado, o fornecedor especifica os níveis de acesso. O publicador pode solicitar uma atribuição de função permanente, ou [acesso just-in-time](request-just-in-time-access.md) para uma atribuição que é restrita a um período de tempo.

Atualmente não há suporte para restringir o acesso para [operações de dados](../role-based-access-control/role-definitions.md) para todos os provedores de dados no Azure.

A imagem a seguir mostra um cenário no qual o fornecedor solicita a função de proprietário para o grupo de recursos gerenciados. O fornecedor aplicou um bloqueio somente leitura nesse grupo de recursos para o consumidor. As identidades do distribuidor que recebem acesso ao grupo de recursos gerenciados são isentas de bloqueio.

![Acesso ao grupo de recursos](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupo de recursos do aplicativo

Esse grupo de recursos armazena a instância do aplicativo gerenciado. Esse grupo de recursos pode conter apenas um recurso. O tipo de recurso do aplicativo gerenciado é **Microsoft.Solutions/applications**.

O consumidor tem acesso total ao grupo de recursos e o utiliza para gerenciar o ciclo de vida do aplicativo gerenciado.

### <a name="managed-resource-group"></a>Grupo de recursos gerenciado

Este grupo de recursos contém todos os recursos necessários ao aplicativo gerenciado. Por exemplo, esse grupo de recursos contém as máquinas virtuais, contas de armazenamento e redes virtuais para a solução. O consumidor tem acesso limitado a esse grupo de recursos, pois não gerencia os recursos individuais do aplicativo gerenciado. O acesso do fornecedor a este grupo de recursos corresponde à função especificada na definição do aplicativo gerenciado. Por exemplo, o fornecedor pode solicitar a função de Proprietário ou Colaborador para este grupo de recursos. O acesso é permanente ou limitado a uma hora específica.

Ao publicar o [aplicativo gerenciado no marketplace](publish-marketplace-app.md), o editor pode conceder aos consumidores a capacidade de executar ações específicas em recursos no grupo de recursos gerenciados. Por exemplo, o editor pode especificar que os consumidores podem reiniciar as máquinas virtuais. Todas as outras ações além das ações de leitura ainda são negadas.

Quando o consumidor exclui o aplicativo gerenciado, o grupo de recursos gerenciado também é excluído.

## <a name="azure-policy"></a>Azure Policy

Você pode aplicar uma [política do Azure](../governance/policy/overview.md) ao seu aplicativo gerenciado. Aplique políticas para garantir que instâncias implantadas de seu aplicativo gerenciado atendam requisitos de segurança e de dados. Se o aplicativo interage com os dados confidenciais, verifique se você avaliou como eles devem ser protegidos. Por exemplo, se seu aplicativo interage com os dados do Office 365, aplique uma política para garantir que a criptografia de dados esteja habilitada.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os benefícios do uso de aplicativos gerenciados. Va até o próximo artigo para criar uma definição de aplicativo gerenciado.

> [!div class="nextstepaction"]
> [Início Rápido: Publicar uma definição de aplicativo gerenciado do Azure](publish-managed-app-definition-quickstart.md)
