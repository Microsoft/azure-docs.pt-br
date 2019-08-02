---
title: Perguntas Frequentes de DNS privado do Azure
description: Perguntas frequentes sobre o DNS privado do Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 480cf22491dbbfcb9fe1961b5c9a7aa6fe12a0cb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274043"
---
# <a name="azure-private-dns-faq"></a>Perguntas Frequentes de DNS privado do Azure

> [!IMPORTANT]
> O DNS privado do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>O DNS do Azure oferece suporte a domínios particulares?

Suporte para domínios privados com suporte usando o recurso de zonas de DNS privado do Azure. As zonas DNS privadas são gerenciadas usando as mesmas ferramentas que as zonas de DNS do Azure para a internet. Eles são resolvíveis apenas de dentro de suas redes virtuais especificadas. Para mais informações, consulte a [visão geral](private-dns-overview.md).

Para obter informações sobre outras opções de DNS interno no Azure, consulte [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>As Zonas Privadas do DNS do Azure funcionarão em todas as regiões do Azure?

Sim. As zonas privadas são suportadas para resolução de DNS entre redes virtuais nas regiões do Azure. As zonas privadas funcionam mesmo sem espiar explicitamente as redes virtuais. Todas as redes virtuais devem ser especificadas como redes virtuais de resolução para a zona privada. Os clientes podem precisar que as redes virtuais sejam analisadas para que o tráfego TCP / HTTP flua de uma região para outra.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>A conectividade com a Internet de redes virtuais é necessária para zonas privadas?

Não. As zonas privadas funcionam junto com as redes virtuais. Os clientes os usam para gerenciar domínios para máquinas virtuais ou outros recursos dentro e entre redes virtuais. A conectividade com a Internet não é necessária para a resolução de nomes.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A mesma zona privada pode ser usada para várias redes virtuais para resolução?

Sim. Você pode associar até 1000 de redes virtuais com uma única zona privada.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Uma rede virtual que pertence a uma assinatura diferente pode ser adicionada como uma rede virtual vinculada a uma zona privada?

Sim. Você precisa ter permissão de operação de gravação nas redes virtuais e na zona DNS privado. A permissão de gravação pode ser concedida para várias funções do RBAC. Por exemplo, a função de RBAC do Colaborador da Rede Clássica tem permissões de gravação nas redes virtuais. Para obter mais informações sobre funções do RBAC, consulte [Controle de acesso baseado em função](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Serão os registros DNS da máquina de virtual registrados automaticamente em uma zona privada excluídos automaticamente quando você exclui a máquina virtual?

Sim. Se você excluir uma máquina virtual em uma rede virtual vinculada com o registro automático habilitado, os registros são excluídos automaticamente.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Um registro de máquina de virtual registrados automaticamente em uma zona privada de uma rede virtual vinculada pode ser excluído manualmente?

Sim. Você pode regravar os registros DNS registrados automaticamente com um registro DNS criado manualmente na zona. A pergunta e resposta a seguir abordam este tópico.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>O que acontece quando eu tento criar manualmente um novo registro DNS em uma zona privada que tem o mesmo nome de host como uma máquina de virtual registrada automaticamente em uma rede virtual vinculada?

Tente criar manualmente um novo registro DNS em uma zona privada que tem o mesmo nome de host como uma máquina virtual existente e registrada automaticamente em uma rede virtual vinculada. Quando você faz isso, o novo registro DNS sobrescreve o registro de máquina virtual registrado automaticamente. Se você tentar excluir esse registro DNS criado manualmente da zona novamente, a exclusão será bem-sucedida. O registro automático acontece novamente, contanto que a máquina virtual ainda exista e tenha um IP privado anexado a ela. O registro DNS é recriado automaticamente na zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>O que acontece quando desvinculamos uma rede virtual vinculada de uma zona privada? Os registros da máquina virtual registrados automaticamente da rede virtual também serão removidos da zona?

Sim. Para desvincular uma rede virtual vinculada de uma zona privada, você deve atualizar a zona DNS para remover o link de rede virtual associada. Nesse processo, os registros da máquina virtual que foram registrados automaticamente são removidos da zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>O que acontece quando excluímos uma rede virtual vinculada que esteja vinculada a uma zona privada? Precisamos atualizar manualmente a zona privada para desvincular a rede virtual como uma rede virtual vinculada da zona?

Sim. Quando você exclui uma rede virtual vinculada sem desvinculá-la de uma zona privada primeiro, a operação de exclusão for bem-sucedida. Mas a rede virtual não é desvinculada automaticamente da sua zona privada, se houver. Você deve desvincular manualmente a rede virtual da zona privada. Por esse motivo, desvincule sua rede virtual da sua zona privada antes de excluí-la.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Resolução DNS, usando o padrão FQDN (internal.cloudapp.net) ainda funciona mesmo quando uma zona privada (por exemplo, private.contoso.com) é vinculada a uma rede virtual?

Sim. As zonas privadas não substituem as resoluções DNS padrão usando a zona interna.cloudapp.net fornecida pelo Azure. É oferecido como um recurso ou aprimoramento adicional. Independentemente de você confiar no internal.cloudapp.net fornecido pelo Azure ou em sua própria zona privada, use o FQDN da zona que deseja resolver.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>O sufixo DNS em máquinas virtuais em uma rede virtual vinculada será alterado para o da zona privada?

Não. O sufixo DNS nas máquinas virtuais em sua rede virtual vinculada permanece como o sufixo fornecido pelo Azure padrão ("*.internal.cloudapp.net"). Você pode alterar manualmente esse sufixo DNS em suas máquinas virtuais para aquela da zona privada.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Quais são os limites de uso de DNS privado do Azure?

Os limites padrão a seguir se aplicam quando você usa o DNS privado do Azure.

| Resource | Limite padrão |
| --- | --- |
|Zonas DNS privadas por assinatura|1000|
|Conjuntos de registros por zona de DNS privado|25.000|
|Registros por conjunto de registros|20|
|Links de rede virtual por zona de DNS privado|1000|
|Links de redes virtuais por zonas DNS privadas com o registro automático habilitado|100|
|Número de zonas DNS privadas, que uma rede virtual pode obter vinculada com registro automático habilitado|1|
|Número de zonas DNS privadas que pode obter vinculada a uma rede virtual|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Existe suporte de portal para zonas privadas?

Sim e as zonas privadas que já foram criadas por meio de APIs, PowerShell, CLI e SDKs são visíveis no portal do Azure.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Por que não Minhas zonas DNS privadas existentes são exibidos na nova experiência do portal?

Como parte da versão de atualização da visualização, lançamos um novo modelo de recurso para as zonas DNS privadas. As zonas DNS privadas existentes precisará ser migrados para o novo modelo de recurso antes que elas podem aparecer na nova experiência de portal. Consulte abaixo para obter instruções sobre como migrar para o novo modelo de recurso.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Como migrar meu zonas privadas do DNS existentes para o novo modelo?
É altamente recomendável que você migre para o novo modelo de recurso assim que possível. Modelo de recursos herdados terão suporte, no entanto, ainda mais recursos serão não ser desenvolvioas sobre esse modelo. No futuro, pretendemos descontinuá-la em favor do novo modelo de recurso. Para obter orientação sobre como migrar seu DNS privada existente Consulte zonas do novo modelo de recurso[guia de migração para zonas privadas do DNS do Azure](private-dns-migration-guide.md).

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre DNS privado do Azure](private-dns-overview.md)