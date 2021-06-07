---
title: Mover recursos para outra região com o Azure Resource mover
description: Saiba como mover recursos dentro de um grupo de recursos para outra região com o Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797258"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Mover recursos entre regiões (do grupo de recursos)

Neste artigo, saiba como mover recursos em um grupo de recursos específico para uma região diferente do Azure. No grupo de recursos, você seleciona os recursos que deseja mover. Em seguida, você os move usando o [Azure Resource mover](overview.md).

> [!IMPORTANT]
> O Azure Resource Mover está em versão prévia pública no momento.


## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de acesso de *proprietário* na assinatura na qual os recursos que você deseja mover estão localizados.
    - Na primeira vez que você adicionar um recurso para um mapeamento de origem e destino específico em uma assinatura do Azure, o Resource mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI (identificador de serviço gerenciado)) que é confiável para a assinatura.
    - Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
- A assinatura precisa de cota suficiente para criar os recursos de origem na região de destino. Caso contrário, solicite limites adicionais. [Saiba mais](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.
- Verifique se os recursos que você deseja mover têm suporte do Resource mover:
    - VMs do Azure e discos associados
    - NICs
    - Conjuntos de disponibilidade
    - Redes virtuais do Azure
    - Endereços IP públicos
    - Grupos de segurança de rede (NSGs):
    - Balanceadores de carga internos e públicos
    - Bancos de dados SQL do Azure e pools elásticos


## <a name="check-vm-requirements"></a>Verificar os requisitos da VM

1. Verifique se as VMs que você deseja mover são compatíveis.

    - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows compatíveis.
    - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões de Kernel compatíveis.
    - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) compatíveis.
2. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. 
    - Em VMs do Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga as diretrizes do distribuidor do Linux para garantir que o computador tenha os certificados e a CRL mais recentes. 
3. Permitir conectividade de saída das VMs:
    - Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Caso esteja usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecionar recursos a serem movidos

Selecione os recursos que deseja mover. Você move os recursos para uma região de destino na assinatura da região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

> [!NOTE]
>  Não selecione discos associados ou a operação falhará. Os discos associados são incluídos automaticamente em uma movimentação de VM.

1. No portal do Azure, abra o grupo de recursos relevante.
2. Na página grupo de recursos, selecione os recursos que você deseja mover.
3. Selecione **mover**  >  **mover para outra região**.

    ![Seleção para mover recursos para uma região diferente](./media/move-region-within-resource-group/select-move-region.png)
    
4. Em **origem + destino**, selecione a região de destino para a qual você deseja mover os recursos. Em seguida, selecione **Avançar**.


    ![Página de origem e destino para selecionar a região de destino](./media/move-region-within-resource-group/source-target.png)


7. Em **recursos a serem movidos**, selecione **Avançar**.  
8. Em **selecionar recursos**, selecione o recurso que você deseja mover. Você só pode adicionar recursos compatíveis para movimentação. Em seguida, selecione **Concluído**.
9. Em **mover recursos**, selecione **Avançar**. 
10. Em **examinar + adicionar**, verifique os detalhes de origem e de destino.
11. Confirme que você entendeu que os metadados sobre os recursos que estão sendo movidos serão armazenados em um grupo de recursos criado para essa finalidade e que você permitirá que o motor de recurso Crie uma identidade gerenciada pelo sistema para acessar os recursos de assinatura.
1. Selecione **continuar** para começar a adicionar os recursos.

    ![Página de resumo para verificar os detalhes e continuar com a movimentação](./media/move-region-within-resource-group/summary.png)    

11. A operação de adição de recurso é iniciada. Quando a operação é concluída, as notificações mostram que os recursos foram adicionados e a implantação foi bem-sucedida.
13. Nas notificações, selecione **Adicionar recursos para mover**.

    ![Mensagem mostrada em notificações](./media/move-region-within-resource-group/notification.png)    


14. Depois de selecionar a notificação, os recursos selecionados são adicionados a uma coleção de movimentação no Hub do Azure Resource mover.  O Resource mover ajuda a verificar dependências e, em seguida, iniciar a movimentação de recursos para a região de destino.

## <a name="resolve-dependencies"></a>Resolver dependências

Os recursos que você está movendo aparecem na página **entre regiões** , em um estado de *preparação pendente* . Inicie a validação da seguinte maneira:

1. Se os recursos mostrarem uma mensagem *validar dependências* na coluna **problemas** , selecione o botão **validar dependências** . O processo de validação começa.

    ![Botão para validar dependências](./media/move-region-within-resource-group/validate-dependencies.png)

2. Se forem encontradas dependências, selecione **Adicionar dependências**. 
3. Em **Adicionar dependências**, selecione os recursos dependentes > **Adicionar dependências**. Monitore o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-within-resource-group/add-dependencies.png)

3. Adicione outras dependências, se necessário, e valide as dependências conforme necessário. Selecione **Atualizar** para garantir que os recursos mostrem um estado atualizado.

4. Na página **Entre regiões**, verifique se os recursos estão agora no estado de *Preparação pendente*, sem problemas.

    ![Página para mostrar o estado de preparação pendente para todos os recursos](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de você poder preparar e mover recursos, o grupo de recursos de origem deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos de origem > **Preparar**.
2. Em **preparar recursos**, selecione **preparar**.
1. 
    ![Botão para preparar o grupo de recursos de origem](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Durante o processo de Preparação, o Resource Mover gera modelos do ARM (Azure Resource Manager) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.
    
> [!NOTE]
>  Após preparar o grupo de recursos, ele estará no estado de *Iniciar movimentação pendente*. Atualize para mostrar o estado mais recente.

![Status mostrando o estado de início pendente](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos > **Iniciar movimentação**
2. ln **mover recursos**, selecione **Iniciar movimentação**. O grupo de recursos passa para o estado de *Iniciar movimentação em andamento*.
3. Após iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem passa para o estado de *Confirmar movimentação pendente*.

![Status mostrando a movimentação de confirmação](./media/move-region-availability-zone/commit-move-pending.png)

Para confirmar e concluir o processo de movimentação:

1. Em **várias regiões**, selecione o grupo de recursos > **confirmar movimentação**
2. ln **mover recursos**, selecione **confirmar**.

> [!NOTE]
> Após confirmar a movimentação, o grupo de recursos de origem estará no estado de *Excluir origem pendente*.

## <a name="modify-target-settings"></a>Modificar configurações de destino

Se você não quiser mover um recurso de origem, poderá fazer o seguinte:

- Crie um recurso na região de destino com o mesmo nome e as mesmas configurações que o recurso na região de origem.
- Crie um novo recurso equivalente na região de destino. Exceto pelas configurações especificadas, o recurso de destino é criado com as mesmas configurações da origem.
- Use um recurso existente na região de destino.

Modifique uma configuração da seguinte maneira:

1. Para modificar uma configuração, selecione a entrada na coluna **configuração de destino** do recurso.
2. Na página **configuração de destino** , especifique as configurações de destino que você deseja usar.
    As alterações são feitas apenas para o recurso que você está editando. Você precisa atualizar os recursos dependentes separadamente.   
    
As configurações exatas que você modifica dependem do tipo de recurso. [Saiba mais](modify-target-settings.md) sobre como editar as configurações de destino.

## <a name="prepare-resources-to-move"></a>Preparar os recursos a serem movidos

Agora que o grupo de recursos de origem foi movido, você pode se preparar para mover os outros recursos.

1. Em **Entre regiões**, selecione os recursos que deseja preparar. 

    ![Página para selecionar Preparar para outros recursos](./media/move-region-availability-zone/prepare-other.png)

2. Selecione **Preparar**.

> [!NOTE]
> - Durante o processo de preparação, o agente de mobilidade Azure Site Recovery é instalado em VMs, para replicação.
> - Os dados das VMs são replicados periodicamente para a região de destino. Isso não afeta as VMs de origem.
> - A Movimentação de Recursos gera modelos ARM para os outros recursos de origem.
> - Após preparar os recursos, eles estarão no estado de *Iniciar movimentação pendente*.

![Página mostrando recursos no estado de Iniciar movimentação pendente](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Inicie a movimentação

Com os recursos preparados, agora você pode iniciar a movimentação.

1. Em **Entre regiões**, selecione recursos com o estado de *Iniciar movimentação pendente*. Em seguida, selecione **Iniciar movimentação**.
2. Em **mover recursos**, selecione **Iniciar movimentação**.

    ![Selecione para o botão iniciar movimentação](./media/move-region-within-resource-group/initiate-move.png)

3. Monitore o progresso da movimentação na barra de notificações.


> [!NOTE]
> - Para as VMs, são criadas réplicas delas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).<br/>
> - O Resource Mover recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há tempo de inatividade.<br/> 
> - Para balanceadores de carga, as regras de NAT não são copiadas. Crie-os na região de destino depois de confirmar a movimentação.
> - Para endereços IP públicos, o rótulo de nome DNS não é copiado. Recrie o rótulo depois de confirmar a movimentação.
> - Depois de preparar os recursos, eles estão em um estado de *movimentação pendente de confirmação* .


## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: talvez você descarte uma movimentação se estiver apenas testando e não quiser realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para o estado de *Iniciar movimentação pendente*.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, o recurso de origem estará no estado de *Excluir origem pendente* e você poderá decidir se deseja excluí-lo.


## <a name="discard-the-move"></a>Descartar a movimentação 

Você pode descartar a movimentação da seguinte maneira:

1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente* e selecione **descartar movimentação**.
2. Em **descartar movimentação**, selecione **descartar**.
3. Monitore o progresso da movimentação na barra de notificações.
4. Quando as notificações mostrarem que a movimentação foi bem-sucedida, selecione **Atualizar**. 

> [!NOTE]
> Para VMs, depois de descartar os recursos, eles estão em um estado *Iniciar movimentação pendente* .

## <a name="commit-the-move"></a>Confirmar a movimentação

Se você quiser concluir o processo de movimentação, confirme a movimentação. 


1. Em **várias regiões**, selecione recursos com o estado *confirmação de movimentação pendente* e selecione **confirmar movimentação**.
2. Em **confirmar recursos**, selecione **confirmar**.

    ![Página para confirmar os recursos a fim de finalizar a movimentação](./media/move-region-within-resource-group/commit-resources.png)

3. Monitore o progresso da confirmação na barra de notificações.

> [!NOTE]
> - Após confirmar a movimentação, a réplica das VMs é interrompida. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Após confirmar a movimentação, os recursos estarão no estado de *Excluir origem pendente*.

## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

1. Como os rótulos de nome DNS não são copiados para endereços IP públicos, após a conclusão da movimentação, navegue até os recursos de destino e atualize o rótulo. 
2. Para balanceadores de carga internos, como as regras de NAT não são copiadas, navegue até os recursos criados na região de destino e atualize as regras de NAT.
3. O serviço Mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o instalado caso planeje mover o servidor novamente.
## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após a confirmação

Após a movimentação, você terá a opção de excluir os recursos da região de origem. 

1. Em **várias regiões**, selecione o nome de cada recurso de origem que você deseja excluir.
2. Na página de propriedades de cada recurso, selecione **Excluir**.

## <a name="delete-additional-resources-created-for-move"></a>Excluir recursos adicionais criados para a movimentação

Após a movimentação, você poderá excluir manualmente a coleção da movimentação e os recursos do Site Recovery que foram criados.

- A coleção da movimentação fica oculta por padrão. Para vê-la, você precisará ativar os recursos ocultos.
- O armazenamento em cache tem um bloqueio que precisa ser liberado antes que o cache possa ser excluído.

Exclua da seguinte maneira: 

1. Localize os recursos no grupo de recursos ```RegionMoveRG-<sourceregion>-<target-region>``` , na região de origem.
2. Verifique se toda a VM e outros recursos de origem na coleção de movimentação foram movidos/excluídos. Isso verifica se não há recursos pendentes usando-os.
2. Exclua os recursos:

    - O nome da coleção da movimentação é ```movecollection-<sourceregion>-<target-region>```.
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Próximas etapas


[Saiba mais sobre](about-move-process.md) o processo de movimentação.