---
title: Perguntas comuns sobre o Azure Resource mover?
description: Obtenha respostas para perguntas comuns sobre o Azure Resource mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: raynew
ms.openlocfilehash: a75cd3c5dbf205f49aa606bfe96623a61bce39db
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007049"
---
# <a name="common-questions"></a>Perguntas comuns

Este artigo responde a perguntas comuns sobre o [Azure Resource mover](overview.md).


## <a name="moving-across-regions"></a>Movendo entre regiões

### <a name="can-i-move-resources-across-any-regions"></a>Posso mover recursos entre todas as regiões?

No momento, você pode mover recursos de qualquer região pública de origem para qualquer região pública de destino, dependendo dos [tipos de recursos disponíveis nessa região](https://azure.microsoft.com/global-infrastructure/services/). Atualmente, não há suporte para mover recursos nas regiões do Azure governamental.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Quais recursos posso mover entre regiões usando o Resource mover?

Usando o Resource Mover, no momento, você pode mover os seguintes recursos entre regiões:

- VMs do Azure e discos associados
- NICs
- Conjuntos de disponibilidade 
- Redes virtuais do Azure 
- Endereços IP públicos
- Grupos de segurança de rede (NSGs):
- Balanceadores de carga internos e públicos 
- Bancos de dados SQL do Azure e pools elásticos

### <a name="can-i-move-disks-across-regions"></a>Posso mover discos entre regiões?

Não é possível selecionar discos como recursos para mover entre regiões. No entanto, os discos são movidos como parte de uma movimentação de VM.

### <a name="what-does-it-mean-to-move-a-resource-group"></a>O que significa mover um grupo de recursos?

Quando um recurso é selecionado para movimentação, o grupo de recursos correspondente é adicionado automaticamente para movimentação. Isso é necessário, pois o recurso de destino precisará ser colocado em um grupo de recursos como estava no destino. Você pode optar por personalizar e fornecer um grupo de recursos dos, depois que ele for adicionado para movimentação. Observe que, mover um grupo de recursos **não** significa que todos os recursos no grupo de recursos de origem serão movidos.

### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>Posso mover recursos entre assinaturas quando movê-los entre regiões?

Você pode alterar a assinatura depois de mover os recursos para a região de destino. [Saiba mais](../azure-resource-manager/management/move-resource-group-and-subscription.md) sobre como mover recursos para uma assinatura diferente. 

### <a name="does-azure-resource-move-service-store-customer-data"></a>O serviço de movimentação de recursos do Azure armazena dados do cliente? 
Não. O serviço de movimentação de recursos não armazena os dados do cliente, ele armazena apenas informações de metadados que facilitam o rastreamento e o progresso dos recursos selecionados para movimentação, pelo cliente.


### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Onde estão os metadados para mover entre regiões armazenadas?

Ele é armazenado em um banco de dados [Cosmos do Azure](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento de BLOBs do Azure](../storage/common/storage-service-encryption.md), em uma assinatura da Microsoft. Atualmente, os metadados são armazenados no leste dos EUA 2 e Europa Setentrional. Expandiremos essa cobertura para outras regiões. Isso não o impedirá de mover recursos entre regiões públicas.

### <a name="is-the-collected-metadata-encrypted"></a>Os metadados coletados são criptografados?

Sim, em trânsito e em repouso.
- Durante o trânsito, os metadados são enviados com segurança para o serviço de movimentação de recursos pela Internet usando HTTPS.
- No armazenamento, os metadados são criptografados.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>Como a identidade gerenciada é usada no movimentador de recursos?

A [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como identidade de serviço gerenciada (MSI)) fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD.
- O Resource mover usa identidade gerenciada para que possa acessar assinaturas do Azure para mover recursos entre regiões.
- Uma coleção de movimentação precisa de uma identidade atribuída pelo sistema, com acesso à assinatura que contém os recursos que você está movendo.

- Se você mover recursos entre regiões no portal, esse processo ocorrerá automaticamente.
- Se você mover recursos usando o PowerShell, execute os cmdlets para atribuir uma identidade atribuída pelo sistema à coleção e, em seguida, atribua uma função com as permissões de assinatura corretas para a entidade de identidade. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Para quais permissões de identidade gerenciada o movimentador de recursos precisa? 

A identidade gerenciada do Azure Resource Mover precisa de pelo menos estas permissões: 

- Permissão para gravar/criar recursos na assinatura do usuário, disponível com a função *colaborador* . 
- Permissão para criar atribuições de função. Normalmente, disponível com as funções de *administrador de acesso de usuário* ou *proprietário* , ou com uma função personalizada que tenha a *permissão Microsoft. Authorization/role assignments/Write* atribuída. Essa permissão não será necessária, se a identidade gerenciada do recurso de compartilhamento de dados já tiver acesso concedida ao armazenamento de dados do Azure. 
 
Quando você adiciona recursos no hub do Resource Mover no portal, as permissões são tratadas automaticamente, desde que o usuário tenha as permissões descritas acima. Se você adicionar recursos com o PowerShell, atribua permissões manualmente.

> [!IMPORTANT]
> É altamente recomendável que você não modifique ou remova as atribuições de função de identidade. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>O que devo fazer se não tiver permissões para atribuir a identidade da função?

**Causa possível** | **Recomendação**
--- | ---
Você não é um *colaborador* e *administrador de acesso do usuário* (ou *proprietário*) ao adicionar um recurso pela primeira vez. | Use uma conta com permissões de *colaborador* e *administrador de acesso do usuário* (ou *proprietário*) para a assinatura.
A identidade gerenciada do Resource mover não tem a função necessária. | Adicione as funções ' colaborador ' e ' administrador de acesso do usuário '.
A identidade gerenciada do Resource mover foi redefinida para *nenhuma*. | Reabilitar uma identidade atribuída pelo sistema na coleção de movimentação > **identidade**. Como alternativa, adicione o recurso novamente em **Adicionar recursos**, o que faz a mesma coisa.  
A assinatura foi movida para um locatário diferente. | Desabilite e habilite a identidade gerenciada para a coleção de movimentação.

### <a name="how-can-i-do-multiple-moves-together"></a>Como posso fazer várias movimentações juntas?

Altere as combinações de origem/destino conforme necessário usando a opção alterar no Portal.

### <a name="what-happens-when-i-remove-a-resource-from-a-list-of-move-resources"></a>O que acontece quando eu removo um recurso de uma lista de recursos de movimentação?

Você pode remover os recursos que adicionou à lista de movimentação. O comportamento quando você remove um recurso da lista depende do estado do recurso. [Saiba mais](remove-move-resources.md#vm-resource-state-after-removing).



## <a name="next-steps"></a>Próximas etapas

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e sobre o processo de movimentação.
