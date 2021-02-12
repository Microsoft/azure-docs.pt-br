---
title: Modificar as configurações de destino ao mover as VMs do Azure entre regiões com o Azure Resource mover
description: Saiba como modificar as configurações de destino ao mover as VMs do Azure entre regiões com o Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: eb28e4c8f6b465e2a9b38cc4571bc4a00baf4ef7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979616"
---
# <a name="modify-target-settings"></a>Modificar configurações de destino

Este artigo descreve como modificar as configurações de destino ao mover recursos entre regiões do Azure com o [Azure Resource mover](overview.md).


## <a name="modify-vm-settings"></a>Modificar configurações da VM

Ao mover as VMs do Azure e os recursos associados, você pode modificar as configurações de destino. 

- Recomendamos que você altere apenas as configurações de destino depois que a coleção de movimentação for validada.
- Recomendamos que você modifique as configurações antes de preparar os recursos, pois algumas propriedades de destino podem estar indisponíveis para edição após a conclusão da preparação.

No entanto:
- Se você estiver movendo o recurso de origem, geralmente poderá modificar as configurações de destino até iniciar o processo de início de movimentação.
- Se você atribuir um recurso existente na região de origem, poderá modificar as configurações de destino até que a confirmação de movimentação seja concluída.

### <a name="settings-you-can-modify"></a>Configurações que você pode modificar

As definições de configuração que você pode modificar são resumidas na tabela.

**Recurso** | **Opções** 
--- | --- | --- 
**Nome da VM** | Opções:<br/><br/> -Crie uma nova VM com o mesmo nome na região de destino.<br/><br/> -Crie uma nova VM com um nome diferente na região de destino.<br/><br/> -Use uma VM existente na região de destino.<br/><br/> Se você criar uma nova VM, com exceção das configurações que você modificar, a nova VM de destino receberá as mesmas configurações que a origem.
**Zona de disponibilidade da VM** | A zona de disponibilidade na qual a VM de destino será colocada. Selecione **não aplicável** se você não quiser alterar as configurações de origem ou se não quiser posicionar a VM em uma zona de disponibilidade.
**SKU da VM** | O [tipo de VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (disponível na região de destino) que será usado para a VM de destino.<br/><br/> A VM de destino selecionada não deve ser menor que a VM de origem.
* * Conjunto de disponibilidade de VM | O conjunto de disponibilidade no qual a VM de destino será colocada. Selecione **não aplicável**  você não deseja alterar as configurações de origem ou se não quiser posicionar a VM em um conjunto de disponibilidade.
**Cofre de chaves de VM** | O cofre de chaves associado ao habilitar a criptografia de disco do Azure em uma VM.
**Conjunto de criptografia de disco** | O conjunto de criptografia de disco associado se a VM usa uma chave gerenciada pelo cliente para criptografia do lado do servidor.
**Grupo de recursos** | O grupo de recursos no qual a VM de destino será colocada.
**Recursos de rede** | Opções para interfaces de rede, redes virtuais (VNets/) e grupos de segurança de rede/interfaces de rede:<br/><br/> -Crie um novo recurso com o mesmo nome na região de destino.<br/><br/> -Crie um novo recurso com um nome diferente na região de destino.<br/><br/> -Use um recurso de rede existente na região de destino.<br/><br/> Se você criar um novo recurso de destino, com exceção das configurações que você modificar, ele receberá as mesmas configurações que o recurso de origem.
**Nome do endereço IP público, SKU e zona** | Especifica o nome, a [SKU](../virtual-network/public-ip-addresses.md#sku)e a [zona](../virtual-network/public-ip-addresses.md#standard) para endereços IP públicos padrão.<br/><br/> Se você quiser que ele seja com redundância de zona, insira como com **redundância de zona**.
* * Nome do balanceador de carga, SKU e zona * * | Especifica o nome, SKU (básico ou padrão) e zona para o balanceador de carga.<br/><br/> É recomendável usar o sKU Standard.<br/><br/> Se você quiser que ele seja de redundância de zona, especifique como com **redundância de zona**.
**Dependências de recursos** | Opções para cada dependência:<br/><br/>-O recurso usa recursos dependentes de origem que serão movidos para a região de destino.<br/><br/> -O recurso usa diferentes recursos dependentes localizados na região de destino. Nesse caso, você pode escolher entre qualquer recurso semelhante na região de destino.

### <a name="edit-vm-target-settings"></a>Editar configurações de destino da VM

Se não quiser que os recursos dependentes da região de origem para o destino, você terá algumas outras opções:

- Crie um novo recurso na região de destino. A menos que você especifique configurações diferentes, o novo recurso terá as mesmas configurações que o recurso de origem.
- Use um recurso existente na região de destino.

O comportamento exato depende do tipo de recurso. [Saiba mais](modify-target-settings.md) sobre como modificar as configurações de destino.

Você modifica as configurações de destino de um recurso usando a entrada de **configuração de destino** na coleção de movimentação de recursos. 

Para modificar uma configuração: 

1. Na página **entre regiões** > coluna **configuração de destino** , clique no link para a entrada de recurso.
2. Em **definições de configuração**, você pode criar uma nova VM na região de destino.
3. Atribua uma nova zona de disponibilidade, conjunto de disponibilidade ou SKU à VM de destino. **Zona de disponibilidade** e **SKU**.

As alterações são feitas apenas para o recurso que você está editando. Você precisa atualizar qualquer recurso dependente separadamente.


## <a name="modify-sql-settings"></a>Modificar configurações de SQL

Ao mover os recursos do banco de dados SQL do Azure, você pode modificar as configurações de destino para a movimentação. 

- Para o banco de dados SQL:
    - Recomendamos que você modifique as definições de configuração de destino antes de prepará-las para movimentação.
    - Você pode modificar as configurações do banco de dados de destino e a redundância de zona para o banco de dados.
- Para pools elásticos:
    -  Você pode modificar a configuração de destino a qualquer momento antes de iniciar a movimentação.
    - Você pode modificar o pool elástico de destino e a redundância de zona para o pool. 

### <a name="sql-settings-you-can-modify"></a>Configurações do SQL que você pode modificar

**Configuração** | **Banco de dados SQL** | **Pool elástico**
--- | --- | ---
**Nome** | Crie um novo banco de dados com o mesmo nome na região de destino.<br/><br/> Crie um novo banco de dados com um nome diferente na região de destino.<br/><br/> Use um banco de dados existente na região de destino. | Crie um novo pool elástico com o mesmo nome na região de destino.<br/><br/> Crie um novo pool elástico com um nome diferente na região de destino.<br/><br/> Use um pool elástico existente na região de destino.
**Redundância de zona** | Para mover de uma região que dá suporte à redundância de zona para uma região que não faz isso, digite **Disable** na configuração de zona.<br/><br/> Para mover de uma região que não dá suporte à redundância de zona para uma região, digite **habilitar** na configuração de zona. | Para mover de uma região que dá suporte à redundância de zona para uma região que não faz isso, digite **Disable** na configuração de zona.<br/><br/> Para mover de uma região que não dá suporte à redundância de zona para uma região, digite **habilitar** na configuração de zona.

### <a name="edit-sql-target-settings"></a>Editar configurações de destino do SQL

Você modifica as configurações de destino para um recurso de banco de dados SQL do Azure da seguinte maneira: 

1. Em **várias regiões**, para o recurso que você deseja modificar, clique na entrada de **configuração de destino** .
2. Em **definições de configuração**, especifique as configurações de destino resumidas na tabela acima.

## <a name="next-steps"></a>Próximas etapas

[Mover uma VM do Azure](tutorial-move-region-virtual-machines.md) para outra região.