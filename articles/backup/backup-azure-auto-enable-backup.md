---
title: Habilitar automaticamente o backup na criação da VM usando o Azure Policy
description: Um artigo que descreve como usar Azure Policy para habilitar o backup automaticamente para todas as VMs criadas em um determinado escopo
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707295"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Habilitar automaticamente o backup na criação da VM usando o Azure Policy

Uma das principais responsabilidades de um administrador de backup ou conformidade em uma organização é garantir que todos os computadores críticos para os negócios sejam submetidos a backup com a retenção apropriada.

Hoje, o backup do Azure fornece uma variedade de políticas internas (usando [Azure Policy](../governance/policy/overview.md)) para ajudá-lo a garantir automaticamente que suas máquinas virtuais do Azure estejam configuradas para backup. Dependendo de como suas equipes e recursos de backup são organizados, você pode usar qualquer uma das políticas abaixo:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Política 1-configurar o backup em VMs sem uma determinada marca para um cofre dos serviços de recuperação existente no mesmo local

Se sua organização tiver uma equipe de backup central que gerencia backups em equipes de aplicativos, você poderá usar essa política para configurar o backup em um cofre de serviços de recuperação central existente na mesma assinatura e no mesmo local que as VMs que estão sendo governadas. Você pode optar por **excluir** as VMs que contêm uma determinada marca, do escopo desta política.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Política 2-[visualização] configurar o backup em VMs com uma determinada marca para um cofre dos serviços de recuperação existente no mesmo local
Essa política funciona da mesma forma que a política 1 acima, com a única diferença de que você pode usar essa política para **incluir** VMs que contêm uma determinada marca, no escopo desta política. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Política 3-[visualização] configurar backup em VMs sem uma determinada marca para um novo cofre de serviços de recuperação com uma política padrão
Se você organizar aplicativos em grupos de recursos dedicados e desejar que eles tenham o backup feito pelo mesmo cofre, essa política permitirá que você gerencie automaticamente essa ação. Você pode optar por **excluir** as VMs que contêm uma determinada marca, do escopo desta política.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Política 4-[visualização] configurar o backup em VMs com uma determinada marca para um novo cofre de serviços de recuperação com uma política padrão
Essa política funciona da mesma forma que a política 3 acima, com a única diferença de que você pode usar essa política para **incluir** VMs que contêm uma determinada marca, no escopo desta política. 

Além do acima, o backup do Azure também fornece uma política [somente auditoria](../governance/policy/concepts/effects.md#audit) – o **backup do Azure deve ser habilitado para máquinas virtuais**. Essa política identifica quais máquinas virtuais não têm o backup habilitado, mas não configura automaticamente os backups para essas VMs. Isso é útil quando você está apenas procurando avaliar a conformidade geral das VMs, mas não está procurando executar a ação imediatamente.

## <a name="supported-scenarios"></a>Cenários com suporte

* Atualmente, a política interna tem suporte apenas para VMs do Azure. Os usuários devem tomar cuidado para garantir que a política de retenção especificada durante a atribuição seja uma política de retenção de VM. Consulte [este](./backup-azure-policy-supported-skus.md) documento para ver todos os SKUs de VM com suporte nesta política.

* As políticas 1 e 2 podem ser atribuídas a um único local e assinatura de cada vez. Para habilitar o backup para VMs em locais e assinaturas, várias instâncias da atribuição de política precisam ser criadas, uma para cada combinação de localização e assinatura.

* Para as políticas 1 e 2, o escopo do grupo de gerenciamento não tem suporte no momento.

* Para as políticas 1 e 2, o cofre especificado e as VMs configuradas para backup podem estar em grupos de recursos diferentes.

* As políticas 1, 2, 3 e 4 não estão disponíveis atualmente em nuvens nacionais.

* As políticas 3 e 4 podem ser atribuídas a uma única assinatura por vez (ou a um grupo de recursos em uma assinatura).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Usando as políticas internas

As etapas a seguir descrevem o processo de ponta a ponta de atribuição de política 1: **Configurar backup em VMs sem uma determinada marca para um cofre dos serviços de recuperação existente no mesmo local** para um determinado escopo. Instruções semelhantes serão aplicadas para as outras políticas. Uma vez atribuída, qualquer nova VM criada no escopo é configurada automaticamente para backup.

1. Entre no portal do Azure e navegue até o painel de **política** .
2. Selecione **definições** no menu à esquerda para obter uma lista de todas as políticas internas nos recursos do Azure.
3. Filtre a lista para **Category = backup** e selecione a política denominada ' Configurar backup em VMs de um local para um cofre central existente no mesmo local '.
![Painel de política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Selecione o nome da política. Você será redirecionado para a definição detalhada dessa política.
![Painel de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Selecione o botão **atribuir** na parte superior do painel. Isso redireciona você para o painel **atribuir política** .
6. Em **noções básicas**, selecione os três pontos ao lado do campo **escopo** . Isso abre um painel de contexto à direita no qual você pode selecionar a assinatura na qual a política será aplicada. Você também pode selecionar um grupo de recursos, para que a política seja aplicada somente a VMs em um determinado grupo de recursos.
![Noções básicas de atribuição de política](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na guia **parâmetros** , escolha um local na lista suspensa e selecione o cofre e a política de backup aos quais as VMs no escopo devem ser associadas. Você também pode optar por especificar um nome de marca e uma matriz de valores de marca. Uma VM que contém qualquer um dos valores especificados para a marca fornecida será excluída do escopo da atribuição de política.
![Parâmetros de atribuição de política](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Verifique se o **efeito** está definido como deployIfNotExists.
9. Navegue até **revisar + criar** e selecione **criar**.

> [!NOTE]
>
> Azure Policy também pode ser usado em VMs existentes, usando a [correção](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> É recomendável que essa política não seja atribuída a mais de 200 VMs por vez. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas depois daquela especificada pela agenda.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Azure Policy](../governance/policy/overview.md)