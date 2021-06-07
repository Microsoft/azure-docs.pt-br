---
title: Configurar uma identidade de laboratório no Azure DevTest Labs
description: Saiba como configurar uma identidade de laboratório no Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719607"
---
# <a name="configure-a-lab-identity"></a>Configurar uma identidade de laboratório

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais no código para autenticar serviços de nuvem. Proteger as credenciais é uma tarefa importante. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor e não sejam verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar credenciais, segredos e outras chaves com segurança, mas seu código deve se autenticar no Key Vault para recuperá-las. 

O recurso identidades gerenciadas para recursos do Azure no Azure Active Directory (Azure AD) resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código. Saiba mais sobre [identidades gerenciadas no Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Há dois tipos de identidades gerenciadas: 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema  

Uma **identidade gerenciada atribuída pelo sistema**   é habilitada diretamente em uma instância de serviço do Azure. Quando a identidade é habilitada, o Azure cria uma identidade para a instância do locatário do Azure AD confiado pela assinatura da instância. Depois que a identidade é criada, as credenciais são provisionadas para a instância. O ciclo de vida de uma identidade atribuída ao sistema está diretamente relacionado à instância de serviço do Azure na qual ela está habilitada. Se a instância é excluída, o Azure limpa automaticamente as credenciais e a identidade no Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Cenários para usar a identidade atribuída pelo sistema do laboratório  

Cada DevTest Labs é criado com uma identidade atribuída pelo sistema que permanece válida durante a vida útil do laboratório. A identidade atribuída pelo sistema é usada para as seguintes finalidades:  

- Todas as implantações baseadas em [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) usadas para criar várias VMs e/ou plataforma como um ambiente de serviço serão executadas usando a identidade atribuída pelo sistema do laboratório  
- A criptografia de disco para discos de laboratório usando uma chave gerenciada pelo cliente é suportada por meio da identidade atribuída pelo sistema do laboratório. Ao fornecer acesso explícito à identidade do laboratório para acessar o conjunto de criptografia do disco, o laboratório pode criptografar todos os discos da máquina virtual em seu nome. Saiba mais sobre [como habilitar a criptografia de disco](encrypt-disks-customer-managed-keys.md) para seus discos de laboratório usando uma chave gerenciada pelo cliente.  

### <a name="configure-identity"></a>Configurar identidade

Esta seção demonstra como configurar a política de identidade do laboratório.

> [!NOTE]
> Para os laboratórios criados antes de 8/10/2020, a identidade atribuída pelo sistema será definida como desativado. Como proprietário de um laboratório, você pode ativá-lo, caso pretenda usar os laboratórios para os fins listados na seção anterior.  
>
> Para novos laboratórios criados após 8/10/2020, a identidade atribuída do sistema do laboratório é definida como ativada por padrão e o proprietário do laboratório não poderá desativar isso para o ciclo de vida do laboratório.  

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure **DevTest Labs**.
1. Na lista de laboratórios, selecione o laboratório desejado.
1. Selecione a **configuração e a identidade das políticas**  ->  **(versão prévia)**. 

> [!div class="mx-imgBorder"]
> ![Configurar identidade](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário  

Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure. O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída. 

O DevTest Labs dá suporte a identidades de usuário atribuídas para máquinas virtuais e ambientes baseados em Azure Resource Manager.  Para mais informações, consulte os seguintes tópicos:

- [Adicionar uma identidade atribuída pelo usuário para implantar ambientes de Azure Resource Manager de laboratório](use-managed-identities-environments.md)
- [Adicionar identidades atribuídas ao usuário para implantar máquinas virtuais do laboratório](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Próximas etapas

Examinar [Configurar o gerenciamento de custos](devtest-lab-configure-cost-management.md)