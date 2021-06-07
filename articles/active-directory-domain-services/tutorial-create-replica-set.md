---
title: Tutorial – Criar um conjunto de réplicas no Azure AD Domain Services | Microsoft Docs
description: Saiba como criar e usar conjuntos de réplicas no portal do Azure para resiliência de serviço com Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: justinha
ms.openlocfilehash: a016287fedbd303a5571100130769ce4299828bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798537"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Tutorial: Criar e usar conjuntos de réplicas para resiliência ou geolocalização no Azure Active Directory Domain Services

Para melhorar a resiliência de um domínio gerenciado do Azure AD DS (Azure Active Directory Domain Services) ou implantar em locais geográficos adicionais próximos aos aplicativos, é possível usar *conjuntos de réplicas*. Cada namespace de domínio gerenciado do Azure AD DS, como *aaddscontoso.com*, contém um conjunto de réplicas inicial. A capacidade de criar conjuntos de réplicas adicionais em outras regiões do Azure fornece resiliência geográfica para um domínio gerenciado.

Você pode adicionar um conjunto de réplicas a qualquer rede virtual emparelhada em qualquer região do Azure que dê suporte ao Azure AD DS.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Compreender os requisitos de rede virtual
> * Criar um conjunto de réplicas
> * Excluir um conjunto de réplicas

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services criado usando um modelo de implantação do Azure Resource Manager e configurado no locatário do Azure AD.
    * Se necessário, [crie e configure um domínio gerenciado do Azure Active Directory Domain Services][tutorial-create-instance].

    > [!IMPORTANT]
    > Os domínios gerenciados criados usando o modelo de implantação Clássico não podem usar conjuntos de réplicas. Você também precisa usar o mínimo do SKU *Enterprise* para seu domínio gerenciado. Se necessário, [altere o SKU de um domínio gerenciado][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Neste tutorial, você criará e gerenciará conjuntos de réplicas usando o portal do Azure. Para começar, primeiro entre no [portal do Azure](https://portal.azure.com).

## <a name="networking-considerations"></a>Considerações de rede

As redes virtuais que hospedam conjuntos de réplicas precisam ser capazes de se comunicar entre si. Aplicativos e serviços que dependem do Azure AD DS também precisam da conectividade de rede com as redes virtuais que hospedam os conjuntos de réplicas. O emparelhamento de rede virtual do Azure deve ser configurado entre todas as redes virtuais para criar uma rede totalmente em malha. Esses emparelhamentos permitem uma replicação entre sites eficaz entre conjuntos de réplicas.

Para poder usar conjuntos de réplicas no Azure AD DS, examine os seguintes requisitos de rede virtual do Azure:

* Evite sobreposição de espaços de endereço IP para permitir o emparelhamento e o roteamento de rede virtual.
* Criar sub-redes com endereços IP suficientes para dar suporte ao seu cenário.
* Verifique se o Azure AD DS tem a própria sub-rede. Não compartilhe essa sub-rede de rede virtual com VMs de aplicativo e serviços.
* Redes virtuais emparelhadas NÃO são transitivas.

> [!TIP]
> Quando você cria um conjunto de réplicas no portal do Azure, os emparelhamentos de rede entre redes virtuais são criados para você.
>
> Se necessário, você pode criar uma rede virtual e uma sub-rede ao adicionar um conjunto de réplicas no portal do Azure. Você pode ainda escolher os recursos de rede virtual existentes na região de destino para um conjunto de réplicas e permitir que os emparelhamentos sejam criados automaticamente, caso ainda não existam.

## <a name="create-a-replica-set"></a>Criar um conjunto de réplicas

Quando você cria um domínio gerenciado, como *aaddscontoso.com*, um conjunto de réplicas inicial é criado. Os conjuntos de réplicas adicionais compartilham os mesmos namespace e configuração. As alterações ao Azure AD DS, incluindo configuração, identidade do usuário e credenciais, grupos, objetos de política de grupo, objetos de computador e outras alterações, são aplicadas a todos os conjuntos de réplicas no domínio gerenciado usando a replicação do AD DS.

Neste tutorial, você criará um conjunto de réplicas adicional em uma região do Azure diferente daquela do conjunto de réplicas inicial do Azure AD DS.

Para criar um conjunto de réplicas adicional, conclua as seguintes etapas:

1. No portal do Azure, pesquise e selecione **Azure AD Domain Services**.
1. Escolha o domínio gerenciado, como *aaddscontoso.com*.
1. No lado esquerdo, selecione **Conjuntos de réplicas**. Cada domínio gerenciado inclui um conjunto de réplicas inicial na região selecionada, conforme mostrado na seguinte captura de tela de exemplo:

    ![Captura de tela de exemplo para exibir e adicionar um conjunto de réplicas no portal do Azure](./media/tutorial-create-replica-set/replica-set-list.png)

    Para criar um conjunto de réplicas adicional, selecione **+ Adicionar**.

1. Na janela *Adicionar um conjunto de réplicas*, selecione a região de destino, como *Leste dos EUA*.

    Selecione uma rede virtual na região de destino, como *vnet-eastus*, em seguida, escolha uma sub-rede como *aadds-subnet*. Se necessário, escolha **Criar** para adicionar uma rede virtual na região de destino e, em seguida, **Gerenciar** para criar uma sub-rede para o Azure AD DS.

    Se eles ainda não existirem, os emparelhamentos de rede virtual do Azure serão criados automaticamente entre a rede virtual do seu domínio gerenciado existente e a rede virtual de destino.

    A captura de tela de exemplo a seguir mostra o processo para criar um conjunto de réplicas no *Leste dos EUA*:

    ![Captura de tela de exemplo para criar um conjunto de réplicas no portal do Azure](./media/tutorial-create-replica-set/create-replica-set.png)

1. Quando estiver pronto, selecione **Salvar**.

O processo para criar o conjunto de réplicas leva algum tempo enquanto os recursos são criados na região de destino. O próprio domínio gerenciado é então replicado usando a replicação do AD DS.

O conjunto de réplicas é relatado como *Em provisionamento* conforme a implantação continua, conforme mostrado na captura de tela de exemplo a seguir. Ao concluir, o conjunto de réplicas é mostrado como *Em execução*.

![Captura de tela de exemplo do status de implantação do conjunto de réplicas no portal do Azure](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Excluir um conjunto de réplicas

No momento, um domínio gerenciado está limitado a quatro réplicas – o conjunto de réplicas inicial e três conjuntos de réplicas adicionais. Se você não precisar mais de um conjunto de réplicas ou se quiser criar um conjunto de réplicas em outra região, poderá excluir conjuntos de réplicas desnecessários.

> [!IMPORTANT]
> Não é possível excluir o último conjunto de réplicas em um domínio gerenciado.

Para excluir um conjunto de réplicas, conclua as seguintes etapas:

1. No portal do Azure, pesquise e selecione **Azure AD Domain Services**.
1. Escolha o domínio gerenciado, como *aaddscontoso.com*.
1. No lado esquerdo, selecione **Conjuntos de réplicas**. Na lista de conjuntos de réplicas, selecione o menu de contexto **...** ao lado do conjunto de réplicas que você deseja excluir.
1. Selecione **Excluir** no menu de contexto e, em seguida, confirme que deseja excluir o conjunto de réplicas.

> [!NOTE]
> A exclusão do conjunto de réplicas pode ser uma operação demorada.

Se você não precisar mais da rede virtual ou do emparelhamento usado pelo conjunto de réplicas, também poderá excluir esses recursos. Verifique se nenhum outro recurso de aplicativo na outra região precisa das conexões de rede antes de excluí-las.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configure emparelhamento de rede virtual
> * Criar um conjunto de réplicas em uma região geográfica diferente
> * Excluir um conjunto de réplicas

Para obter mais informações conceituais, saiba como os conjuntos de réplicas funcionam no Azure AD DS.

> [!div class="nextstepaction"]
> [Conceitos e recursos dos conjuntos de réplicas][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
