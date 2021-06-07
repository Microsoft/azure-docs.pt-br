---
title: Alertas e resoluções comuns no Azure AD Domain Services | Microsoft Docs
description: Saiba como resolver alertas comuns gerados como parte do status de integridade para Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092884"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Problemas conhecidos: alertas e resoluções comuns no Azure Active Directory Domain Services

Como parte central da identidade e da autenticação para aplicativos, o Azure Active Directory Domain Services (AD DS do Azure) às vezes tem problemas. Se você tiver problemas, há alguns alertas comuns e etapas de solução de problemas associadas para ajudá-lo a fazer as coisas serem executadas novamente. A qualquer momento, você também pode [abrir uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

Este artigo fornece informações de solução de problemas para alertas comuns no Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Diretório ausente

### <a name="alert-message"></a>Mensagem de alerta

*O diretório do Azure AD associado ao domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

### <a name="resolution"></a>Resolução

Esse erro geralmente ocorre quando uma assinatura do Azure é movida para um novo diretório do Azure AD e o diretório do Azure AD antigo associado ao Azure AD DS é excluído.

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado existente](delete-aadds.md) e recrie-o no novo diretório. Se você tiver problemas ao excluir o domínio gerenciado, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: O Azure AD B2C está em execução neste diretório

### <a name="alert-message"></a>Mensagem de alerta

*Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.*

### <a name="resolution"></a>Resolução

O Azure AD DS sincroniza automaticamente com um diretório do Azure AD. Se o diretório do Azure AD estiver configurado para B2C, o Azure AD DS não poderá ser implantado e sincronizado.

Para usar o Azure AD DS, você deve recriar seu domínio gerenciado em um diretório não Azure AD B2C usando as seguintes etapas:

1. [Exclua o domínio gerenciado](delete-aadds.md) do seu diretório existente do AD do Azure.
1. Crie um novo diretório do AD do Azure que não seja um diretório Azure AD B2C.
1. [Crie um domínio gerenciado de substituição](tutorial-create-instance.md).

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>ADDS103: Endereço está em um intervalo de IP público

### <a name="alert-message"></a>Mensagem de alerta

*O intervalo de endereços IP para a rede virtual na qual você habilitou Azure AD Domain Services está em um intervalo de IP público. Azure AD Domain Services deve ser habilitado em uma rede virtual com um intervalo de endereços IP privado. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patches e sincronizar seu domínio gerenciado.*

### <a name="resolution"></a>Resolução

Antes de começar, certifique-se de entender os [espaços de endereço IP V4 privados](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro de uma rede virtual, as VMs podem fazer solicitações aos recursos do Azure no mesmo intervalo de endereços IP que foram configuradas para a sub-rede. Se você configurar um intervalo de endereços IP públicos para uma sub-rede, as solicitações roteadas em uma rede virtual poderão não alcançar os recursos da Web pretendidos. Essa configuração pode levar a erros imprevisíveis com o Azure AD DS.

> [!NOTE]
> Se você possui o intervalo de endereços IP na internet que está configurada em sua rede virtual, esse alerta pode ser ignorado. No entanto, Azure AD Domain Services não pode confirmar o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) com essa configuração, pois ela pode levar a erros imprevisíveis.

Para resolver esse alerta, exclua o domínio gerenciado existente e recrie-o em uma rede virtual com um intervalo de endereços IP privado. Esse processo é interrompido, pois o domínio gerenciado não está disponível e todos os recursos personalizados criados como UOs ou contas de serviço são perdidos.

1. [Exclua o domínio gerenciado](delete-aadds.md) do seu diretório.
1. Para atualizar o intervalo de endereços IP da rede virtual, procure e selecione *rede virtual* na portal do Azure. Selecione a rede virtual para o Azure AD DS que tenha incorretamente um conjunto de intervalos de endereços IP públicos.
1. Em **configurações**, selecione *espaço de endereço*.
1. Atualize o intervalo de endereços escolhendo o intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Verifique se o novo intervalo de endereços IP está em um intervalo de IP privado. Quando estiver pronto, **salve** as alterações.
1. Selecione **sub-redes** no painel de navegação à esquerda.
1. Escolha a sub-rede que você deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique um intervalo de endereços IP privado e **salve** as alterações.
1. [Crie um domínio gerenciado de substituição](tutorial-create-instance.md). Verifique se você selecionou a sub-rede de rede virtual atualizada com um intervalo de endereços IP privado.

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Assinatura do Azure não encontrada

### <a name="alert-message"></a>Mensagem de alerta

*Sua assinatura do Azure associada ao domínio gerenciado foi excluída.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma assinatura ativa e não pode ser movido para uma assinatura diferente. Se a assinatura do Azure à qual o domínio gerenciado foi associado for excluída, você deverá recriar uma assinatura do Azure e um domínio gerenciado.

1. [Crie uma assinatura do Azure](../cost-management-billing/manage/create-subscription.md).
1. [Exclua o domínio gerenciado](delete-aadds.md) do seu diretório existente do AD do Azure.
1. [Crie um domínio gerenciado de substituição](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Sua Assinatura do Azure está desabilitada

### <a name="alert-message"></a>Mensagem de alerta

*Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

### <a name="resolution"></a>Resolução

O AD DS do Azure requer uma assinatura ativa. Se a assinatura do Azure à qual o domínio gerenciado estava associado não estiver ativa, você deverá renová-la para reativar a assinatura.

1. [Renovar sua assinatura do Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Depois que a assinatura é renovada, uma notificação AD DS do Azure permite reabilitar o domínio gerenciado.

Quando o domínio gerenciado é habilitado novamente, a integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Diretórios de assinatura movida

### <a name="alert-message"></a>Mensagem de alerta

*A assinatura usada pelo Azure AD Domain Services foi movida para outro diretório. Azure AD Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma assinatura ativa e não pode ser movido para uma assinatura diferente. Se a assinatura do Azure à qual o domínio gerenciado foi associado for movida, mova a assinatura de volta para o diretório anterior ou [exclua o domínio gerenciado](delete-aadds.md) do diretório existente e [crie um domínio gerenciado de substituição na assinatura escolhida](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Recursos para seu domínio gerenciado que não pode ser encontrado

### <a name="alert-message"></a>Mensagem de alerta

*Um recurso que é usado para o domínio gerenciado foi excluído. Esse recurso é necessário para que Azure AD Domain Services funcionem corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS cria recursos adicionais para funcionar corretamente, como endereços IP públicos, interfaces de rede virtual e um balanceador de carga. Se qualquer um desses recursos for excluído, o domínio gerenciado estará em um estado sem suporte e impedirá que o domínio seja gerenciado. Para obter mais informações sobre esses recursos, consulte [recursos de rede usados pelo Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Esse alerta é gerado quando um desses recursos necessários é excluído. Se o recurso foi excluído há menos de 4 horas, há uma chance de que a plataforma Azure possa recriar automaticamente o recurso excluído. As etapas a seguir descrevem como verificar o status de integridade e o carimbo de data/hora para a exclusão de recursos:

1. Na portal do Azure, procure e selecione serviços de **domínio**. Escolha o domínio gerenciado, como *aaddscontoso.com*.
1. No painel de navegação à esquerda, selecione **integridade**.
1. Na página integridade, selecione o alerta com a ID *AADDS109*.
1. O alerta tem um carimbo de data/hora para quando ele foi encontrado pela primeira vez. Se esse carimbo de data/hora for inferior a 4 horas, a plataforma do Azure poderá ser capaz de recriar automaticamente o recurso e resolver o alerta por si só.

    Se o alerta tiver mais de 4 horas, o domínio gerenciado está em um estado irrecuperável. [Exclua o domínio gerenciado](delete-aadds.md) e [crie um domínio gerenciado de substituição](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A sub-rede associada ao seu domínio gerenciado está cheia

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para a implantação do Azure Active Directory Domain Services está cheio e não tem espaço para o controlador de domínio adicional precisa ser criado.*

### <a name="resolution"></a>Resolução

A sub-rede da rede virtual para o Azure AD DS precisa de endereços IP suficientes para os recursos criados automaticamente. Esse espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implante recursos adicionais, como suas próprias VMs, na mesma sub-rede de rede virtual que o domínio gerenciado.

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado existente](delete-aadds.md) e recrie-o. Se você tiver problemas ao excluir o domínio gerenciado, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: entidade de serviço não autorizada

### <a name="alert-message"></a>Mensagem de alerta

*Uma entidade de serviço que o Azure AD Domain Services usa para atender seu domínio não está autorizada a gerenciar recursos na assinatura do Azure. A entidade de serviço precisa obter permissões para atender ao domínio gerenciado.*

### <a name="resolution"></a>Resolução

Algumas entidades de serviço geradas automaticamente são usadas para gerenciar e criar recursos para um domínio gerenciado. Se as permissões de acesso para uma dessas entidades de serviço forem alteradas, o domínio não poderá gerenciar corretamente os recursos. As etapas a seguir mostram como entender e conceder permissões de acesso a uma entidade de serviço:

1. Leia sobre [o controle de acesso baseado em função do Azure e como conceder acesso a aplicativos no portal do Azure](../role-based-access-control/role-assignments-portal.md).
2. Examine o acesso que a entidade de serviço com a ID *abba844e-bc0e-44b0-947a-dc74e5d09022* tem e conceda o acesso que foi negado em uma data anterior.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereços IP suficientes disponíveis no domínio gerenciado

### <a name="alert-message"></a>Mensagem de alerta

*Identificamos que a sub-rede da rede virtual nesse domínio pode não ter endereços IP suficientes. Azure AD Domain Services precisa de pelo menos dois endereços IP disponíveis na sub-rede em que está habilitado. É recomendável ter pelo menos 3-5 endereços IP de reposição na sub-rede. Isso pode ter ocorrido se outras máquinas virtuais forem implantadas na sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição no número de endereços IP disponíveis na sub-rede.*

### <a name="resolution"></a>Resolução

A sub-rede da rede virtual para o Azure AD DS precisa de endereços IP suficientes para os recursos criados automaticamente. Esse espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implante recursos adicionais, como suas próprias VMs, na mesma sub-rede de rede virtual que o domínio gerenciado.

Para resolver esse alerta, exclua o domínio gerenciado existente e recrie-o em uma rede virtual com um grande intervalo de endereços IP suficiente. Esse processo é interrompido, pois o domínio gerenciado não está disponível e todos os recursos personalizados criados como UOs ou contas de serviço são perdidos.

1. [Exclua o domínio gerenciado](delete-aadds.md) do seu diretório.
1. Para atualizar o intervalo de endereços IP da rede virtual, procure e selecione *rede virtual* na portal do Azure. Selecione a rede virtual para o domínio gerenciado que tem o pequeno intervalo de endereços IP.
1. Em **configurações**, selecione *espaço de endereço*.
1. Atualize o intervalo de endereços escolhendo o intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Verifique se o novo intervalo de endereços IP é grande o suficiente para o intervalo de sub-rede do domínio gerenciado. Quando estiver pronto, **salve** as alterações.
1. Selecione **sub-redes** no painel de navegação à esquerda.
1. Escolha a sub-rede que você deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique um intervalo de endereços IP grande o suficiente e **salve** as alterações.
1. [Crie um domínio gerenciado de substituição](tutorial-create-instance.md). Verifique se você selecionou a sub-rede de rede virtual atualizada com um grande intervalo de endereços IP suficiente.

A integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Recursos são irrecuperáveis

### <a name="alert-message"></a>Mensagem de alerta

*Os recursos usados pelo Azure Active Directory Domain Services foram detectados em um estado inesperado e não podem ser recuperados.*

### <a name="resolution"></a>Resolução

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado existente](delete-aadds.md) e recrie-o. Se você tiver problemas ao excluir o domínio gerenciado, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Sub-rede inválida

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para implantação de Azure AD Domain Services é inválida e não pode ser usada.*

### <a name="resolution"></a>Resolução

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado existente](delete-aadds.md) e recrie-o. Se você tiver problemas ao excluir o domínio gerenciado, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados uma vez que o escopo de destino foi bloqueado.*

### <a name="resolution"></a>Resolução

Os bloqueios de recursos podem ser aplicados aos recursos do Azure para evitar alterações ou exclusões. Como o Azure AD DS é um serviço gerenciado, a plataforma do Azure precisa da capacidade de fazer alterações de configuração. Se um bloqueio de recurso for aplicado em alguns dos componentes de AD DS do Azure, a plataforma do Azure não poderá executar suas tarefas de gerenciamento.

Para verificar se há bloqueios de recursos nos componentes do AD DS do Azure e removê-los, conclua as seguintes etapas:

1. Para cada um dos componentes de rede do domínio gerenciado em seu grupo de recursos, como rede virtual, interface de rede ou endereço IP público, verifique os logs de operação no portal do Azure. Esses logs de operação devem indicar por que uma operação está falhando e onde um bloqueio de recurso é aplicado.
1. Selecione o recurso em que um bloqueio é aplicado e, em **bloqueios**, selecione e remova os bloqueios.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido às restrições de política.*

### <a name="resolution"></a>Resolução

As políticas são aplicadas aos recursos do Azure e aos grupos de recursos que controlam quais ações de configuração são permitidas. Como o Azure AD DS é um serviço gerenciado, a plataforma do Azure precisa da capacidade de fazer alterações de configuração. Se uma política for aplicada em alguns dos componentes de AD DS do Azure, a plataforma do Azure poderá não ser capaz de executar suas tarefas de gerenciamento.

Para verificar as políticas aplicadas nos componentes do AD DS do Azure e atualizá-los, conclua as seguintes etapas:

1. Para cada um dos componentes de rede do domínio gerenciado em seu grupo de recursos, como rede virtual, NIC ou endereço IP público, verifique os logs de operação no portal do Azure. Esses logs de operação devem indicar por que uma operação está falhando e onde uma política restritiva é aplicada.
1. Selecione o recurso no qual uma política é aplicada e, em **políticas**, selecione e edite a política para que ela seja menos restritiva.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não é realizada há um tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários podem não conseguir entrar no domínio gerenciado ou as associações de grupo podem não estar sincronizadas com o Azure AD.*

### <a name="resolution"></a>Resolução

[Verifique a integridade de AD DS do Azure](check-health.md) em busca de alertas que indiquem problemas na configuração do domínio gerenciado. Problemas com a configuração de rede podem bloquear a sincronização do Azure AD. Se você conseguir resolver alertas que indicam um problema de configuração, aguarde duas horas e verifique novamente se a sincronização foi concluída com êxito.

Os motivos comuns a seguir fazem com que a sincronização pare em um domínio gerenciado:

* A conectividade de rede necessária está bloqueada. Para saber mais sobre como verificar se há problemas na rede virtual do Azure e o que é necessário, consulte [solucionar problemas de grupos de segurança de rede](alert-nsg.md) e os [requisitos de rede para o Azure AD DS](network-considerations.md).
*  A sincronização de senha não foi configurada ou concluída com êxito quando o domínio gerenciado foi implantado. Você pode configurar a sincronização de senha para [usuários somente de nuvem](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) ou [usuários híbridos no local](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: O backup não é realizado há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O backup do domínio gerenciado foi feito pela última vez em [data].*

### <a name="resolution"></a>Resolução

[Verifique a integridade de AD DS do Azure](check-health.md) em busca de alertas que indicam problemas na configuração do domínio gerenciado. Problemas com a configuração de rede podem bloquear a plataforma do Azure de fazer backups com êxito. Se você conseguir resolver alertas que indicam um problema de configuração, aguarde duas horas e verifique novamente se a sincronização foi concluída com êxito.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a uma assinatura desativada

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerenciado for suspenso por um longo período de tempo, haverá um perigo de que ele seja excluído. Resolva o motivo da suspensão o mais rápido possível. Para obter mais informações, consulte [entender os Estados suspensos do Azure AD DS](suspension.md).

O AD DS do Azure requer uma assinatura ativa. Se a assinatura do Azure à qual o domínio gerenciado estava associado não estiver ativa, você deverá renová-la para reativar a assinatura.

1. [Renovar sua assinatura do Azure](../cost-management-billing/manage/subscription-disabled.md).
2. Depois que a assinatura é renovada, uma notificação AD DS do Azure permite reabilitar o domínio gerenciado.

Quando o domínio gerenciado é habilitado novamente, a integridade do domínio gerenciado se atualiza automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço não pôde gerenciar, aplicar patches ou atualizar os controladores de domínio para seu domínio gerenciado por um longo tempo.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerenciado for suspenso por um longo período de tempo, haverá um perigo de que ele seja excluído. Resolva o motivo da suspensão o mais rápido possível. Para obter mais informações, consulte [entender os Estados suspensos do Azure AD DS](suspension.md).

[Verifique a integridade de AD DS do Azure](check-health.md) em busca de alertas que indicam problemas na configuração do domínio gerenciado. Se você conseguir resolver alertas que indicam um problema de configuração, aguarde duas horas e verifique novamente se a sincronização foi concluída. Quando estiver pronto, [abra uma solicitação de suporte do Azure][azure-support] para reabilitar o domínio gerenciado.

## <a name="next-steps"></a>Próximas etapas

Se ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para obter assistência de solução de problemas adicional.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md