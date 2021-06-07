---
title: Os conceitos de conjuntos de réplicas para Azure AD Domain Services | Microsoft Docs
description: Saiba quais conjuntos de réplicas estão em Azure Active Directory Domain Services e como eles fornecem redundância para aplicativos que exigem serviços de identidade.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: 8eb1560887c08c3f64fa599c39e5577242d2a1e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689055"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>A réplica define conceitos e recursos para Azure Active Directory Domain Services

Ao criar um domínio gerenciado do Azure Active Directory Domain Services (AD DS do Azure), você define um namespace exclusivo. Esse namespace é o nome de domínio, por exemplo, *aaddscontoso.com*, e dois DCs (controladores de domínio) são implantados na região do Azure selecionada. Essa implantação de DCs é conhecida como conjunto de réplicas.

Você pode expandir um domínio gerenciado para ter mais de um conjunto de réplicas por locatário do Azure AD. Os conjuntos de réplicas podem ser adicionados a qualquer rede virtual emparelhada em qualquer região do Azure com suporte para o Azure AD DS. Conjuntos de réplicas adicionais em diferentes regiões do Azure fornecem recuperação de desastre geográfica para aplicativos herdados se uma região do Azure fica offline.

> [!NOTE]
> Os conjuntos de réplicas não permitem que você implante vários domínios gerenciados exclusivos em um único locatário do Azure. Cada conjunto de réplicas contém os mesmos dados.

## <a name="how-replica-sets-work"></a>Como funcionam os conjuntos de réplicas

Quando você cria um domínio gerenciado, como *aaddscontoso.com*, um conjunto de réplicas inicial é criado. Os conjuntos de réplicas adicionais compartilham os mesmos namespace e configuração. As alterações ao Azure AD DS, incluindo configuração, identidade do usuário e credenciais, grupos, objetos de política de grupo, objetos de computador e outras alterações, são aplicadas a todos os conjuntos de réplicas no domínio gerenciado usando a replicação do AD DS.

Você cria cada conjunto de réplicas em uma rede virtual. Cada rede virtual deve ser emparelhada a todas as outras redes virtuais que hospedam um conjunto de réplicas de um domínio gerenciado. Essa configuração cria uma topologia de rede de malha que dá suporte à replicação de diretório. Uma rede virtual pode dar suporte a vários conjuntos de réplicas, desde que cada conjunto de réplicas esteja em uma sub-rede virtual diferente.

Todos os conjuntos de réplicas são colocados no mesmo site Active Directory. Como resultado, todas as alterações são propagadas usando a replicação intra-site para convergência rápida.

> [!NOTE]
> Não é possível definir sites separados e definir configurações de replicação entre conjuntos de réplicas.

O diagrama a seguir mostra um domínio gerenciado com dois conjuntos de réplicas. O primeiro conjunto de réplicas é criado com o namespace de domínio. Um segundo conjunto de réplicas é criado depois disso:

![Diagrama de exemplo de domínio gerenciado com dois conjuntos de réplicas](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> Os conjuntos de réplicas garantem a disponibilidade dos serviços de autenticação em regiões em que um conjunto de réplicas está configurado. Para que um aplicativo tenha redundância geográfica se houver uma interrupção regional, a plataforma de aplicativo que se baseia no domínio gerenciado também deve residir na outra região.
>
> Resiliência de outros serviços necessários para que o aplicativo funcione, como VMs do Azure ou serviços de Azure App, não é fornecido por conjuntos de réplicas. O design de disponibilidade de outros componentes de aplicativos precisa considerar os recursos de resiliência para os serviços que compõem o aplicativo.

O exemplo a seguir mostra um domínio gerenciado com três conjuntos de réplicas para fornecer ainda mais resiliência e garantir a disponibilidade dos serviços de autenticação. Em ambos os exemplos, as cargas de trabalho do aplicativo existem na mesma região que o conjunto de réplicas de domínio gerenciado:

![Diagrama de exemplo de domínio gerenciado com três conjuntos de réplicas](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Considerações de implantação

O SKU padrão para um domínio gerenciado é o SKU *corporativo* , que dá suporte a vários conjuntos de réplicas. Para criar conjuntos de réplicas adicionais, se você tiver alterado para o SKU *Standard* , [atualize o domínio gerenciado](change-sku.md) para *Enterprise* ou *Premium*.

O número máximo de conjuntos de réplicas com suporte é quatro, incluindo a primeira réplica criada quando você criou o domínio gerenciado.

A cobrança de cada conjunto de réplicas baseia-se na SKU de configuração de domínio. Por exemplo, se você tiver um domínio gerenciado que usa o SKU *corporativo* e tiver três conjuntos de réplicas, sua assinatura será cobrada por hora para cada um dos três conjuntos de réplicas.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Posso criar um conjunto de réplicas na assinatura diferente do meu domínio gerenciado?

Não. Os conjuntos de réplicas devem estar na mesma assinatura que o domínio gerenciado.

### <a name="how-many-replica-sets-can-i-create"></a>Quantos conjuntos de réplica posso criar?

Você pode criar no máximo quatro conjuntos de réplicas — o conjunto de réplicas inicial para o domínio gerenciado, além de três conjuntos de réplicas adicionais.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Como as informações de usuário e grupo são sincronizadas com os conjuntos de réplicas?

Todos os conjuntos de réplicas são conectados entre si usando um emparelhamento de rede virtual de malha. Um conjunto de réplicas recebe atualizações de usuário e grupo do Azure AD. Essas alterações são replicadas para os outros conjuntos de réplicas usando a replicação de AD DS intra-site na rede emparelhada.

Assim como ocorre com AD DS locais, um estado estendido desconectado pode causar interrupção na replicação. Como as redes virtuais emparelhadas não são transitivas, os requisitos de design para conjuntos de réplicas exigem uma topologia de rede totalmente integrada.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Como fazer fazer alterações no meu domínio gerenciado depois que eu tiver conjuntos de réplicas?

As alterações no domínio gerenciado funcionam exatamente como faziam anteriormente. Você [cria e usa uma VM de gerenciamento com as ferramentas do RSAT que ingressaram no domínio gerenciado](tutorial-create-management-vm.md). Você pode unir tantas VMs de gerenciamento ao domínio gerenciado como desejar.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os conjuntos de réplicas, [crie e configure um domínio gerenciado do Azure AD DS][tutorial-create-advanced]. Quando implantado, [crie e use conjuntos de réplicas adicionais][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
