---
title: Perguntas frequentes sobre o DNS privado do Azure
description: Neste artigo, Aprenda as perguntas frequentes sobre o Azure DNS privado
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/15/2021
ms.author: rohink
ms.openlocfilehash: 3272f4fea4169fd5e91b1ba2b97ad72dfbb49080
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247379"
---
# <a name="azure-private-dns-faq"></a>Perguntas frequentes sobre o DNS privado do Azure

Veja a seguir as perguntas frequentes sobre o DNS privado do Azure.

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios particulares?

Os domínios privados têm suporte usando o recurso zonas do Azure DNS privado. DNS privado zonas podem ser resolvidas somente de redes virtuais especificadas. Para obter mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções de DNS interno no Azure, consulte [resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>As zonas do Azure DNS privado funcionarão nas regiões do Azure?

Sim. As zonas privadas são suportadas para resolução de DNS entre redes virtuais nas regiões do Azure. As zonas privadas funcionam mesmo sem espiar explicitamente as redes virtuais. Todas as redes virtuais devem estar vinculadas à zona DNS privada.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. Você os usa para gerenciar domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. A conectividade com a Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. Você pode vincular uma zona DNS privada com milhares de redes virtuais. Para obter mais informações, consulte [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser vinculada a uma zona privada?

Sim. Você precisa ter permissão de operação de gravação nas redes virtuais e na zona DNS privado. A permissão de gravação pode ser concedida a várias funções do Azure. Por exemplo, a função de colaborador de rede clássica do Azure tem permissões de gravação para redes virtuais e a função de colaborador de zonas de DNS privado tem permissões de gravação nas zonas DNS privadas. Para obter mais informações sobre as funções do Azure, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Os registros DNS da máquina virtual automaticamente registrados em uma zona privada serão excluídos automaticamente quando você excluir a máquina virtual?

Sim. Se você excluir uma máquina virtual em uma rede virtual vinculada com o registro automático habilitado, os registros registrados serão excluídos automaticamente.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Reconfigurei o sistema operacional em minha máquina virtual para ter um novo nome de host ou endereço IP estático. Por que não vejo essa alteração refletida na zona privada?

Os registros da zona privada são preenchidos pelo serviço DHCP do Azure; as mensagens de registro do cliente são ignoradas. Se você desabilitou o suporte de cliente DHCP na VM Configurando um endereço IP estático, as alterações no nome do host ou IP estático na VM não serão refletidas na zona.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Configurei um sufixo DNS preferencial em minha máquina virtual do Windows. Por que meus registros ainda estão registrados na zona vinculada à rede virtual?

O serviço DHCP do Azure ignora qualquer sufixo DNS quando registra a zona DNS privada. Por exemplo, se sua máquina virtual estiver configurada `contoso.com` como o sufixo DNS primário, mas a rede virtual estiver vinculada à `fabrikam.com` zona DNS privada, o registro da máquina virtual será exibido na `fabrikam.com` zona DNS privada.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Um registro de máquina virtual registrado automaticamente em uma zona privada de uma rede virtual vinculada pode ser excluído manualmente?

Sim. Você pode regravar os registros DNS registrados automaticamente com um registro DNS criado manualmente na zona. A pergunta e resposta a seguir abordam este tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando tento criar manualmente um novo registro DNS em uma zona privada que tenha o mesmo nome de host que uma máquina virtual existente registrada automaticamente em uma rede virtual vinculada?

Você tenta criar manualmente um novo registro DNS em uma zona privada que tem o mesmo nome de host que uma máquina virtual existente registrada automaticamente em uma rede virtual vinculada. Quando você faz isso, o novo registro DNS sobrescreve o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão será bem-sucedida. O registro automático acontece novamente, contanto que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual vinculada de uma zona privada? Os registros da máquina virtual registrados automaticamente da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual vinculada de uma zona privada, você atualiza a zona DNS para remover o link de rede virtual associado. Nesse processo, os registros da máquina virtual que foram registrados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual vinculada que está vinculada a uma zona privada? Precisamos atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual vinculada da zona?

Não. Quando você exclui uma rede virtual vinculada sem desvinculá-la de uma zona privada primeiro, sua operação de exclusão é realizada com sucesso e os links para a zona DNS são automaticamente apagados.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A resolução de DNS usando o FQDN padrão (internal.cloudapp.net) ainda funcionará mesmo quando uma zona privada (por exemplo, private.contoso.com) estiver vinculada a uma rede virtual?

Sim. As zonas privadas não substituem a zona de internal.cloudapp.net fornecida pelo Azure padrão. Independentemente de você confiar no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que deseja resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo fornecido pelo Azure padrão ("*.internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para aquela da zona privada.
Para obter orientação sobre como alterar esse sufixo, consulte [usar o DNS dinâmico para registrar nomes de host em seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Quais são os limites de uso para as zonas privadas do DNS do Azure?

Consulte [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) para obter detalhes sobre os limites de uso para zonas privadas do DNS do Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Por que minhas zonas DNS privadas existentes aparecem na nova experiência do portal?

Se sua zona DNS privada existente tiver sido criada usando a API de visualização, você deverá migrar essas zonas para o novo modelo de recurso. DNS privado zonas criadas usando a API de visualização não aparecerão na nova experiência do Portal. Consulte abaixo para obter instruções sobre como migrar para o novo modelo de recurso.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como fazer migrar minhas zonas DNS privadas existentes para o novo modelo?

É altamente recomendável que você migre para o novo modelo de recurso assim que possível. O modelo de recurso herdado terá suporte, no entanto, outros recursos não serão desenvolvidos sobre esse modelo. No futuro, pretendemos substituí-lo em favor do novo modelo de recurso. Para obter orientação sobre como migrar suas zonas DNS privadas existentes para o novo modelo de recurso, consulte[Guia de migração para zonas privadas do DNS do Azure](private-dns-migration-guide.md).

### <a name="does-azure-dns-private-zones-store-any-customer-content"></a>As zonas privadas do DNS do Azure armazenam qualquer conteúdo do cliente?

Não, as zonas privadas do DNS do Azure não armazenam nenhum conteúdo do cliente.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Azure DNS privado](private-dns-overview.md)
