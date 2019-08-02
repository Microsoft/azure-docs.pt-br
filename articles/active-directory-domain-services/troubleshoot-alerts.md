---
title: 'Azure Active Directory Domain Services: Solucionar problemas de alertas | Microsoft Docs'
description: Alertas de solução de problemas para os Serviços de Domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 63f2e12b9b769e806f8bd052af5bc78aed6a1fe0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472300"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services – Alertas de solução de problemas
Este artigo fornece guias de solução de problemas para quaisquer alertas que podem ocorrer em seu domínio gerenciado.


Escolha as etapas de solução de problemas que correspondem à ID ou mensagem do alerta.

| **ID do alerta** | **Mensagem de alerta** | **Resolução** |
| --- | --- | :--- |
| AADDS001 | *LDAP Seguro pela internet está habilitado para o domínio gerenciado. No entanto, o acesso à porta 636 não é protegido usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado a ataques de força bruta da senha.* | [Configuração incorreta de LDAP segura](alert-ldaps.md) |
| AADDS100 | *O diretório do Azure AD associado a seu domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Diretório ausente](#aadds100-missing-directory) |
| AADDS101 | *Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.* | [O Azure AD B2C está em execução neste diretório](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Uma Entidade de Serviço necessária para o funcionamento correto do Azure AD Domain Services foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Entidade de Serviço ausente](alert-service-principal.md) |
| AADDS103 | *O intervalo de endereços IP para a rede virtual na qual você habilitou os serviços de domínio do Azure AD está em um intervalo de IP público. Serviços de domínio do Azure AD devem ser habilitados em uma rede virtual com um intervalo de endereços IP privados. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Endereço está em um intervalo de IP público](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft não consegue alcançar os controladores de domínio para este domínio gerenciado. Isso pode ocorrer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é a existência de uma rota definida pelo usuário que bloqueia o tráfego de entrada da internet.* | [Erro de Rede](alert-nsg.md) |
| AADDS105 | *A entidade de serviço com a ID do aplicativo “d87dcbc6-a371-462e-88e3-28ad15ec4e64” foi excluída e depois recriada. A recriação ignora permissões divergentes nos recursos do Azure AD Domain Services necessários para atender o domínio gerenciado. A sincronização de senhas no domínio gerenciado pode ser afetada.* | [O aplicativo de sincronização de senha está desatualizado](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Sua assinatura do Azure associada com seu domínio gerenciado foi excluída.  O Azure Active Directory Domain Services requer uma assinatura ativa para continuar funcionando corretamente.* | [Assinatura do Azure não encontrada](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  O Azure Active Directory Domain Services requer uma assinatura ativa para continuar funcionando corretamente.* | [Assinatura do Azure está desabilitada](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *A assinatura usada pelo Azure Active Directory Domain Services foi movida para outro diretório. O Azure Active Directory Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.* | [Diretórios de assinatura movida](#aadds108-subscription-moved-directories) |
| AADDS109 | *Um recurso que é usado para seu domínio gerenciado foi excluído. Esse recurso é necessário para que o Azure Active Directory Domain Services funcione corretamente.* | [Um recurso foi excluído](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *A sub-rede selecionada para a implantação do Azure Active Directory Domain Services está cheio e não tem espaço para o controlador de domínio adicional precisa ser criado.* | [A subrede está cheia](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Uma entidade de serviço que usa o Azure Active Directory Domain Services para atender seu domínio não está autorizada a gerenciar recursos na assinatura do Azure. A entidade de serviço precisa obter permissões para atender seu domínio gerenciado.* | [Entidade de serviço não autorizada](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Identificamos que a sub-rede da rede virtual neste domínio pode não ter endereços IP suficientes. O Azure Active Directory Domain Services precisa de pelo menos dois endereços IP disponíveis dentro da sub-rede que ele está habilitado. É recomendável ter pelo menos 3 a 5 endereços IP sobressalentes dentro da sub-rede. Isso pode ter ocorrido se outras máquinas virtuais são implantadas dentro da sub-rede, esgotar, portanto, o número de endereços IP disponíveis ou se há uma restrição no número de endereços IP disponíveis na sub-rede.* | [Não há endereços IP suficientes](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Os recursos usados pelo Azure Active Directory Domain Services foram detectados em um estado inesperado e não podem ser recuperados.* | [Recursos são irrecuperáveis](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *A sub-rede selecionada para a implantação do Azure Active Directory Domain Services é inválida e não pode ser usada.* | [Sub-rede inválida](#aadds114-subnet-invalid) |
| AADDS115 | *Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados uma vez que o escopo de destino foi bloqueado.* | [Recursos são bloqueados](#aadds115-resources-are-locked) |
| AADDS116 | *Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido às restrições de política.* | [Os recursos são inutilizáveis](#aadds116-resources-are-unusable) |
| AADDS500 | *O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários poderão não conseguir entrar no domínio gerenciado ou as associações de grupo poderão não ser sincronizadas com o Microsoft Azure Active Directory.* | [A sincronização não é realizada há algum tempo](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *O backup do domínio gerenciado foi feito pela última vez em [data].* | [O backup não é realizado há algum tempo](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *O certificado LDAP seguro para o domínio gerenciado expirará em [data].* | [Certificado LDAP seguro perto de expirar](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.* | [Suspensão devido a uma assinatura desativada](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço foi não tem conseguido gerenciar, aplicar patches ou atualizar os controladores de domínio para seu domínio gerenciado há muito tempo.* | [Suspensão devido a uma configuração inválida](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Diretório ausente
**Mensagem de alerta:**

*O diretório do Azure AD associado a seu domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

**Resolução:**

Esse erro geralmente é causado por mover incorretamente sua assinatura do Azure para um novo diretório do Azure AD e excluir o antigo diretório do Azure AD que ainda está associado com os serviços de domínio do Azure AD.

Esse erro é irrecuperável. Para resolver, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e recriá-lo no seu novo diretório. Se você estiver tendo problemas em excluir, entre em contato com a equipe de produto do Azure Active Directory Domain Services para [obter suporte](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: O Azure AD B2C está em execução neste diretório
**Mensagem de alerta:**

*Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.*

**Resolução:**

>[!NOTE]
>Para continuar usando os serviços de domínio do Azure AD, você deve recriar sua instância de serviços de domínio do Azure AD em um não diretório do Azure AD B2C.

Para restaurar seu serviço, siga estas etapas:

1. [Exclua seu domínio gerenciado](delete-aadds.md) do seu diretório do Azure AD existente.
2. Crie um novo diretório que não é um diretório do Azure AD B2C.
3. Siga o guia [Introdução](create-instance.md) para recriar um domínio gerenciado.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: Endereço está em um intervalo de IP público

**Mensagem de alerta:**

*O intervalo de endereços IP para a rede virtual na qual você habilitou os serviços de domínio do Azure AD está em um intervalo de IP público. Serviços de domínio do Azure AD devem ser habilitados em uma rede virtual com um intervalo de endereços IP privados. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

**Resolução:**

> [!NOTE]
> Para resolver esse problema, você deve excluir seu domínio gerenciado existente e recriá-lo em uma rede virtual com um intervalo de endereços IP privados. Esse processo é disruptivo.

Antes de começar, leia a seção **espaço do endereço IP privado v4** [neste artigo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro da rede virtual, as máquinas podem fazer solicitações para recursos do Azure que estão no mesmo intervalo de endereços IP dos configurados para a sub-rede. No entanto, como a rede virtual é configurada para esse intervalo, essas solicitações serão direcionadas dentro da rede virtual e não alcançarão os recursos web pretendidos. Essa configuração pode levar a erros imprevisíveis com os Azure Active Directory Domain Services.

**Se você possui o intervalo de endereços IP na internet que está configurada em sua rede virtual, esse alerta pode ser ignorado. No entanto, os Azure Active Directory Domain Services não podem confirmar o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] com essa configuração, pois isso poderá resultar em erros imprevisíveis.**


1. [Exclua seu domínio gerenciado](delete-aadds.md) do seu diretório.
2. Corrija o intervalo de endereços IP da sub-rede
   1. Navegue até a [página de Redes Virtuais no portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecione a rede virtual que você planeja usar para os Serviços de Domínio do Azure AD.
   3. Clique em **Espaço de Endereço** em Configurações
   4. Atualize o intervalo de endereços clicando no intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Verifique se o novo intervalo de endereço está em um intervalo IP privado. Salve suas alterações.
   5. Clique em **Sub-redes** no menu de navegação esquerdo.
   6. Clique na sub-rede que deseja editar na tabela.
   7. Atualize o intervalo de endereços e salve suas alterações.
3. Siga [o guia Introdução ao Uso dos Serviços de Domínio do Azure AD](create-instance.md) para recriar seu domínio gerenciado. Certifique-se de escolher uma rede virtual com um intervalo de endereços IP privados.
4. Para associar domínio às suas máquinas virtuais para seu novo domínio, siga [este guia](join-windows-vm.md).
8. Para garantir que o alerta seja resolvido, verifique a integridade do seu domínio em duas horas.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Sua assinatura do Azure não foi encontrada

**Mensagem de alerta:**

*Sua assinatura do Azure associada com seu domínio gerenciado foi excluída.  O Azure Active Directory Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

**Resolução:**

Serviços de domínio do Azure Active Directory Domain Services requerem uma assinatura de função e não podem ser movidos para uma assinatura diferente. Uma vez que a assinatura do Azure que seu domínio gerenciado foi associado foi excluída, você precisará recriar uma assinatura do Azure e o Azure AD Domain Services.

1. Cria uma assinatura do Azure
2. [Exclua seu domínio gerenciado](delete-aadds.md) do seu diretório do Azure AD existente.
3. Siga o guia [Introdução](create-instance.md) para recriar um domínio gerenciado.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Sua assinatura do Azure está desabilitada

**Mensagem de alerta:**

*Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  O Azure Active Directory Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

**Resolução:**


1. [Renovar sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Depois que a assinatura for renovada, o Azure AD Domain Services receberá uma notificação do Azure para habilitar novamente o seu domínio gerenciado.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: A assinatura mudou de diretório

**Mensagem de alerta:**

*A assinatura usada pelo Azure Active Directory Domain Services foi movida para outro diretório. O Azure Active Directory Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.*

**Resolução:**

Você pode mover a assinatura associada ao Azure Active Directory Domain Services de volta ao diretório anterior ou precisará [excluir seu domínio gerenciado](delete-aadds.md) do diretório existente e recriá-lo no diretório escolhido (com um nova assinatura ou alterando o diretório em que a instância do Azure Active Directory Domain Services se encontra).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Não foi possível encontrar recursos para seu domínio gerenciado

**Mensagem de alerta:**

*Um recurso que é usado para seu domínio gerenciado foi excluído. Esse recurso é necessário para que o Azure Active Directory Domain Services funcione corretamente.*

**Resolução:**

O Azure Active Directory Domain Services cria recursos específicos ao implantar para funcionar corretamente, incluindo um balanceador de carga, NICs e endereços IP públicos. Se qualquer um dos nomeado forem excluídos, isso torna o seu domínio gerenciado em um estado sem suporte e impede que o seu domínio gerenciado. Este alerta é encontrado quando alguém que possa editar os recursos do Azure AD Domain Services exclui um recurso necessário. As etapas a seguir descrevem como restaurar seu domínio gerenciado.

1. Navegue até a página de integridade do Azure Active Directory Domain Services
   1.    Desloque para a [página do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
   2.    No painel de navegação à esquerda, clique em **Integridade**
2. Verifique se o alerta tem menos de 4 horas
   1.    Na página de integridade, clique no alerta com a ID **AADDS109**
   2.    O alerta terá um carimbo de hora de quando foi encontrada pela primeira vez. Se esse carimbo de hora for menor que 4 horas atrás, há uma chance de que o Azure AD Domain Services possa recriar o recurso excluído.
3. Se o alerta tiver mais de 4 horas, o domínio gerenciado está em um estado irrecuperável. Você deve excluir e recriar o Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A sub-rede associada ao seu domínio gerenciado está cheia

**Mensagem de alerta:**

*A sub-rede selecionada para a implantação do Azure Active Directory Domain Services está cheio e não tem espaço para o controlador de domínio adicional precisa ser criado.*

**Resolução:**

Esse erro é irrecuperável. Para resolver, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e [recriar seu domínio gerenciado](create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Entidade de serviço não autorizada

**Mensagem de alerta:**

*Uma entidade de serviço que usa o Azure Active Directory Domain Services para atender seu domínio não está autorizada a gerenciar recursos na assinatura do Azure. A entidade de serviço precisa obter permissões para atender seu domínio gerenciado.*

**Resolução:**

Nossas entidades de serviço precisam de acesso para poder gerenciar e criar recursos em seu domínio gerenciado. Alguém negou acesso à entidade de serviço e agora ela não consegue gerenciar recursos. Siga as etapas para conceder acesso à entidade de serviço.

1. Leia sobre [Controle RBAC e como conceder acesso a aplicativos no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Examine o acesso que a entidade de serviço com a ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` e conceda o acesso que foi negado em uma data anterior.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereços IP suficientes no domínio gerenciado

**Mensagem de alerta:**

*Identificamos que a sub-rede da rede virtual neste domínio pode não ter endereços IP suficientes. O Azure Active Directory Domain Services precisa de pelo menos dois endereços IP disponíveis dentro da sub-rede que ele está habilitado. É recomendável ter pelo menos 3 a 5 endereços IP sobressalentes dentro da sub-rede. Isso pode ter ocorrido se outras máquinas virtuais são implantadas dentro da sub-rede, esgotar, portanto, o número de endereços IP disponíveis ou se há uma restrição no número de endereços IP disponíveis na sub-rede.*

**Resolução:**

1. Exclua seu domínio gerenciado de seu locatário.
2. Corrija o intervalo de endereços IP da sub-rede
   1. Navegue até a [página de Redes Virtuais no portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecione a rede virtual que você planeja usar para os Serviços de Domínio do Azure AD.
   3. Clique em **Espaço de Endereço** em Configurações
   4. Atualize o intervalo de endereços clicando no intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Salve suas alterações.
   5. Clique em **Sub-redes** no menu de navegação esquerdo.
   6. Clique na sub-rede que deseja editar na tabela.
   7. Atualize o intervalo de endereços e salve suas alterações.
3. Siga [o guia Introdução ao Uso dos Serviços de Domínio do Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) para recriar seu domínio gerenciado. Certifique-se de escolher uma rede virtual com um intervalo de endereços IP privados.
4. Para associar domínio às suas máquinas virtuais para seu novo domínio, siga [este guia](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Verifique a integridade de seu domínio em duas horas para garantir que você tenha concluído as etapas corretamente.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Recursos são irrecuperáveis

**Mensagem de alerta:**

*Os recursos usados pelo Azure Active Directory Domain Services foram detectados em um estado inesperado e não podem ser recuperados.*

**Resolução:**

Esse erro é irrecuperável. Para resolver, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e [recriar seu domínio gerenciado](create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Sub-rede inválida

**Mensagem de alerta:**

*A sub-rede selecionada para a implantação do Azure Active Directory Domain Services é inválida e não pode ser usada.*

**Resolução:**

Esse erro é irrecuperável. Para resolver, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e [recriar seu domínio gerenciado](create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

**Mensagem de alerta:**

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados uma vez que o escopo de destino foi bloqueado.*

**Resolução:**

1.  Logs de operação do Gerenciador de Recursos de Análise sobre os recursos de rede (isso deve fornecer informações no qual o bloqueio é evitar modificação).
2.  Remova os bloqueios nos recursos para que a entidade de serviço do Azure AD Domain Services possa operar sobre eles.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

**Mensagem de alerta:**

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido às restrições de política.*

**Resolução:**

1.  Examine os logs de operação do Resource Manager nos recursos de rede de seu domínio gerenciado.
2.  Reduza as restrições de política nos recursos para que a entidade de serviço do AAD-DS pode operar sobre elas.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não é realizada há um tempo

**Mensagem de alerta:**

*O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários poderão não conseguir entrar no domínio gerenciado ou as associações de grupo poderão não ser sincronizadas com o Microsoft Azure Active Directory.*

**Resolução:**

[Verifique a integridade do seu domínio](check-health.md) para todos os alertas que possam indicar problemas na configuração do seu domínio gerenciado. Às vezes, problemas com sua configuração podem impedir que a Microsoft sincronize seu domínio gerenciado. Se você conseguir resolver os alertas, aguarde duas horas e veja de novo se a sincronização foi concluída.

Veja alguns motivos comuns pelos quais a sincronização é interrompida em domínios gerenciados:
- A conexão de rede é bloqueada no domínio gerenciado. Para saber mais sobre a verificação da rede para localizar problemas, leia como a [solucionar problemas de Grupos de Segurança de Rede](alert-nsg.md) e [requisitos de rede para o Azure AD Domain Services](network-considerations.md).
-  A sincronização de senha nunca foi configurada ou concluída. Para configurar a sincronização de senha, leia [este artigo](active-directory-ds-getting-started-password-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: O backup não é realizado há algum tempo

**Mensagem de alerta:**

*O backup do domínio gerenciado foi feito pela última vez em [data].*

**Resolução:**

[Verifique a integridade do seu domínio](check-health.md) para todos os alertas que possam indicar problemas na configuração do seu domínio gerenciado. Às vezes, problemas com sua configuração podem impedir que a Microsoft faça backup de seu domínio gerenciado. Se você conseguir resolver os alertas, aguarde duas horas e veja de novo se o backup foi concluído.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a uma assinatura desabilitada

**Mensagem de alerta:**

*O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.*

**Resolução:**

> [!WARNING]
> Se seu domínio gerenciado ficar suspenso por um longo período de tempo, correrá o risco de ser excluído. É melhor tratar da suspensão assim que possível. Para ler mais, acesse [este artigo](suspension.md).

Para restaurar seu serviço, [renove sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) associada a seu domínio gerenciado.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

**Mensagem de alerta:**

*O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço foi não tem conseguido gerenciar, aplicar patches ou atualizar os controladores de domínio para seu domínio gerenciado há muito tempo.*

**Resolução:**

> [!WARNING]
> Se seu domínio gerenciado ficar suspenso por um longo período de tempo, correrá o risco de ser excluído. É melhor tratar da suspensão assim que possível. Para ler mais, acesse [este artigo](suspension.md).

[Verifique a integridade do seu domínio](check-health.md) para todos os alertas que possam indicar problemas na configuração do seu domínio gerenciado. Se puder resolver esses alertas, resolva. Depois, contate o suporte para habilitar novamente a sua assinatura.


## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](contact-us.md).
