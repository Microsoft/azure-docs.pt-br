---
title: Solucionar problemas nos projetos de Migrações para Azure
description: Ajuda a solucionar problemas com a criação e o gerenciamento de projetos de migrações para Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753375"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Solucionar problemas nos projetos de Migrações para Azure

Este artigo ajuda a solucionar problemas ao criar e gerenciar projetos de [migrações para Azure](migrate-services-overview.md) .

## <a name="how-to-add-new-project"></a>Como adicionar um novo projeto?

Você pode ter vários projetos das Migrações para Azure em uma assinatura. [Saiba como](./create-manage-projects.md) criar um projeto pela primeira vez ou [Adicionar outros](create-manage-projects.md#create-additional-projects) projetos.

## <a name="what-azure-permissions-are-needed"></a>Quais permissões do Azure são necessárias?

Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto das Migrações para Azure.

## <a name="cant-find-a-project"></a>Não é possível localizar um projeto

Encontrar um projeto de migrações para Azure existente depende se você está usando a versão atual ou antiga das migrações para Azure. [Seguir](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Não é possível encontrar uma geografia

Você pode criar um projeto de migrações para Azure em geografias com suporte para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>O que são limites de VM?

Você pode avaliar até 35.000 VMs VMware ou até 35.000 VMs Hyper-V em um único projeto. Um projeto pode conter VMs do VMware e do Hyper-V, até os limites da avaliação.

## <a name="can-i-upgrade-old-project"></a>Posso atualizar o projeto antigo?

Os projetos da versão anterior do migrações para Azure não podem ser atualizados. Você precisa [criar um novo projeto](./create-manage-projects.md)e adicionar ferramentas a ele.

## <a name="cant-create-a-project"></a>Não é possível criar um projeto

Se você tentar criar um projeto e encontrar um erro de implantação:

- Tente criar o projeto novamente caso seja um erro transitório. Em **implantações**, clique em **reimplantar** para tentar novamente.
- Verifique se você tem permissões de proprietário ou colaborador na assinatura.
- Se você estiver implantando em uma geografia recém adicionada, aguarde um pouco e tente novamente.
- Se você receber o erro "as solicitações devem conter cabeçalhos de identidade do usuário", isso pode indicar que você não tem acesso ao locatário do Azure Active Directory (Azure AD) da organização. Nesse caso:
    - Quando você é adicionado a um locatário do Azure AD pela primeira vez, você recebe um convite por email para ingressar no locatário.
    - Aceite o convite a ser adicionado ao locatário.
    - Se você não conseguir ver o email, entre em contato com um usuário com acesso ao locatário e peça para [reenviar o convite](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) para você.
    - Depois de receber o email de convite, abra-o e selecione o link para aceitar o convite. Em seguida, saia do portal do Azure e entre novamente. (a atualização do navegador não funcionará.) Em seguida, você pode começar a criar o projeto de migração.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto

[Siga estas instruções](create-manage-projects.md#delete-a-project) para excluir um projeto. Observe que quando você exclui um projeto, o projeto e os metadados sobre computadores descobertos no projeto são excluídos.

## <a name="added-tools-dont-show"></a>As ferramentas adicionadas não são mostradas

Verifique se você tem o projeto correto selecionado. No Hub migrações para Azure > **servidores** ou em **bancos de dados**, clique em **alterar** ao lado de **migrar projeto (alterar)** no canto superior direito da tela. Escolha a assinatura correta e o nome do projeto > **OK**. A página deve ser atualizada com as ferramentas adicionadas do projeto selecionado.

## <a name="next-steps"></a>Próximas etapas

Adicione ferramentas de [avaliação](how-to-assess.md) ou [migração](how-to-migrate.md) para projetos de migrações para Azure.