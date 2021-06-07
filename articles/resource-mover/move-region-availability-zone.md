---
title: Mover VMs do Azure para zonas de disponibilidade em outra região com o Azure Resource mover
description: Saiba como mover VMs do Azure para zonas de disponibilidade com o Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542993"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Mover VMs do Azure para uma zona de disponibilidade em outra região

Neste artigo, saiba como mover as VMs do Azure (e os recursos de rede/armazenamento relacionados) para uma zona de disponibilidade em uma região diferente do Azure, usando o [Azure Resource mover](overview.md).

As [zonas de disponibilidade do Azure](../availability-zones/az-overview.md#availability-zones) ajudam a proteger sua implantação do Azure de falhas do datacenter. Cada zona de disponibilidade é composta de um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as [regiões habilitadas](../availability-zones/az-region.md). Usando o Resource mover, você pode mover:

- Uma única VM de instância para uma zona de disponibilidade/conjunto de disponibilidade na região de destino.
- Uma VM em um conjunto de disponibilidade para uma zona de disponibilidade/conjunto de disponibilidade na região de destino.
- Uma VM em uma zona de disponibilidade de região de origem para uma zona de disponibilidade na região de destino.


> [!IMPORTANT]
> O Azure Resource Mover está em versão prévia pública no momento.

Se você quiser mover VMs para uma zona de disponibilidade diferente na mesma região, [Leia este artigo](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Pré-requisitos

- Acesso de *proprietário* na assinatura na qual os recursos que você deseja mover estão localizados.
    - Na primeira vez que você adicionar um recurso para um mapeamento de origem e destino específico em uma assinatura do Azure, o Resource mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI (identificador de serviço gerenciado)) que é confiável para a assinatura.
    - Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
- A assinatura precisa de cota suficiente para criar os recursos de origem na região de destino. Caso contrário, solicite limites adicionais. [Saiba mais](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.
    


## <a name="check-vm-requirements"></a>Verificar os requisitos da VM

1. Verifique se as VMs que você deseja mover são compatíveis.

    - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows compatíveis.
    - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões de Kernel compatíveis.
    - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) compatíveis.
2. Verifique se as VMs que você deseja mover estão ativadas.
3. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. 
    - Em VMs do Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga as diretrizes do distribuidor do Linux para garantir que o computador tenha os certificados e a CRL mais recentes. 
4. Permitir conectividade de saída das VMs:
    - Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Caso esteja usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecionar recursos a serem movidos

Selecione os recursos que deseja mover.

- Você pode selecionar qualquer tipo de recurso com suporte nos grupos de recursos na região de origem selecionada.
- Você move os recursos para uma região de destino na assinatura da região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

1. No portal do Azure, pesquise por Resource Mover. Em seguida, em **Serviços**, selecione o **Azure Resource Mover**.

    ![Pesquisar o movimentador de recursos](./media/move-region-availability-zone/search.png)

2. Em **visão geral**, selecione **introdução**.

    ![Botão para começar](./media/move-region-availability-zone/get-started.png)

3. Em **Mover recursos** > **Origem + Destino**, selecione a assinatura e a região de origem.
4. Em **Destino**, selecione a região para a qual deseja mover as VMs. Em seguida, clique em **Próximo**.

     ![Página para preencher a assinatura/região de origem e de destino](./media/move-region-availability-zone/source-target.png)

6. Em **Recursos a serem movidos**, clique em **Selecionar recursos**.
7. Em **Selecionar recursos**, selecione a VM desejada. Você só pode adicionar recursos compatíveis para movimentação. Em seguida, clique em **Concluído**. Em **Recursos a serem movidos**, clique **Próximo**.

    ![Página para selecionar as VMs a serem movidas](./media/move-region-availability-zone/select-vm.png)
8. Em **Examinar + Adicionar**, verifique as configurações de origem e destino.

    ![Página para examinar as configurações e prosseguir com a movimentação](./media/move-region-availability-zone/review.png)

9. Clique em **Prosseguir**, para começar a adicionar os recursos.
10. Depois que o processo de adição for concluído com sucesso, clique em **Adicionando recursos a serem movidos** no ícone de notificação.

    ![Mensagem em notificações](./media/move-region-availability-zone/notification.png)

Depois de clicar na notificação, os recursos aparecem na página **entre regiões**

> [!NOTE]
> Depois de clicar na notificação, os recursos aparecem na página **entre regiões** , em um estado de *preparação pendente* .
> - Se você quiser remover um recurso de uma coleção de movimentação, o método para fazer isso dependerá do ponto do processo de movimentação em que você se encontra. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências

1. Se os recursos mostrarem uma mensagem *Validar dependências* na coluna **Problemas**, clique no botão **Validar dependências**. O processo de validação se seres.
2. Se forem encontradas dependências, clique em **Adicionar dependências**. 
3. Em **Adicionar dependências**, selecione os recursos dependentes > **Adicionar dependências**. Monitore o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-availability-zone/add-dependencies.png)

3. Adicione outras dependências, se necessário, e valide as dependências novamente. 

    ![Página para adicionar dependências adicionais](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Na página **Entre regiões**, verifique se os recursos estão agora no estado de *Preparação pendente*, sem problemas.

    ![Página mostrando recursos no estado de preparação pendente](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de você poder preparar e mover VMs, o grupo de recursos de origem deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos de origem > **Preparar**.
2. Em **Preparar recursos**, clique em **Preparar**.

    ![Botão para preparar o grupo de recursos de origem](./media/move-region-availability-zone/prepare-resource-group.png)

    Durante o processo de Preparação, o Resource Mover gera modelos do ARM (Azure Resource Manager) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

> [!NOTE]
>  Após preparar o grupo de recursos, ele estará no estado de *Iniciar movimentação pendente*. 

![Status mostrando o estado de início pendente](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos > **Iniciar movimentação**
2. Em **Mover recursos**, clique em **Iniciar movimentação**. O grupo de recursos passa para o estado de *Iniciar movimentação em andamento*.
3. Após iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem passa para o estado de *Confirmar movimentação pendente*.

![Status mostrando a movimentação de confirmação](./media/move-region-availability-zone/commit-move-pending.png)

Para confirmar e concluir o processo de movimentação:

1. Em **várias regiões**, selecione o grupo de recursos > **confirmar movimentação**
2. Em **Mover recursos**, clique em **Confirmar**.

> [!NOTE]
> Após confirmar a movimentação, o grupo de recursos de origem estará no estado de *Excluir origem pendente*.


## <a name="add-a-target-availability-zone"></a>Adicionar uma zona de disponibilidade de destino

Antes de movermos o restante dos recursos, definiremos uma zona de disponibilidade de destino para a VM.

1. Na página **entre regiões** , clique no link na coluna **configuração de destino** da VM que você está movendo.

    ![Propriedades da VM](./media/move-region-availability-zone/select-vm-settings.png)

3. Em **definições de configuração**, especifique a configuração para a VM de destino. Você pode configurar uma VM na região de destino da seguinte maneira:
    -  Crie um novo recurso equivalente na região de destino. Exceto pelas configurações especificadas, o recurso de destino é criado com as mesmas configurações da origem.
    - Selecione uma VM existente na região de destino. 

4. Em **zonas**, selecione a zona na qual você deseja posicionar a VM quando ela se mover.

    As alterações são feitas apenas para o recurso que você está editando. Você precisa atualizar os recursos dependentes separadamente.

5. Em **SKU**, especifique a [camada do Azure](..//virtual-machines/sizes.md) que você deseja atribuir à VM de destino.
6. Em **conjunto de disponibilidade**, selecione um conjunto de disponibilidade se desejar que a VM de destino seja executada dentro de um conjunto de disponibilidade na zona de disponibilidade.
7. Selecione **Salvar alterações**.

    ![Configurações da VM](./media/move-region-availability-zone/vm-settings.png)


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

1. Em **Entre regiões**, selecione recursos com o estado de *Iniciar movimentação pendente*. Em seguida, clique em **Iniciar movimentação**
2. Em **Mover recursos**, clique em **Iniciar movimentação**.

    ![Página para iniciar a movimentação de recursos](./media/move-region-availability-zone/initiate-move.png)

3. Monitore o progresso da movimentação na barra de notificações.


> [!NOTE]
> - Para as VMs, são criadas réplicas delas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).
> - O Resource Mover recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há tempo de inatividade.
> - Depois de preparar os recursos, eles estão em um estado de *movimentação pendente de confirmação* .


![Página para mostrar os recursos em um estado de movimentação pendente de confirmação](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: talvez você descarte uma movimentação se estiver apenas testando e não quiser realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para o estado de *Iniciar movimentação pendente*.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, o recurso de origem estará no estado de *Excluir origem pendente* e você poderá decidir se deseja excluí-lo.

## <a name="discard-the-move"></a>Descartar a movimentação 

Você pode descartar a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione recursos com o estado de *Confirmar movimentação pendente* e clique em **Descartar movimentação**.
2. Em **Descartar movimentação**, clique em **Descartar**.
3. Monitore o progresso da movimentação na barra de notificações.
 

> [!NOTE]
> Para VMs, depois de descartar os recursos, eles estão em um estado *Iniciar movimentação pendente* .

## <a name="commit-the-move"></a>Confirmar a movimentação

Se você quiser concluir o processo de movimentação, confirme a movimentação. 

1. Em **Entre regiões**, selecione recursos com o estado de *Confirmar movimentação pendente* e clique em **Confirmar movimentação**.
2. Em **Confirmar recursos**, clique em **Confirmar**.

    ![Página para confirmar os recursos a fim de finalizar a movimentação](./media/move-region-availability-zone/commit-resources.png)

3. Monitore o progresso da confirmação na barra de notificações.

> [!NOTE]
> - Após confirmar a movimentação, a réplica das VMs é interrompida. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Após confirmar a movimentação, os recursos estarão no estado de *Excluir origem pendente*.

![Página mostrando recursos no estado de "Excluir origem pendente"](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

O serviço Mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o instalado caso planeje mover o servidor novamente.


## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após a confirmação

Após a movimentação, você terá a opção de excluir os recursos da região de origem.

1. Em **Entre regiões**, clique no nome de cada recurso de origem que deseja excluir.
2. Na página de propriedades de cada recurso, selecione **Excluir**.

## <a name="delete-additional-resources-created-for-move"></a>Excluir recursos adicionais criados para a movimentação

Após a movimentação, você poderá excluir manualmente a coleção da movimentação e os recursos do Site Recovery que foram criados.

- A coleção da movimentação fica oculta por padrão. Para vê-la, você precisará ativar os recursos ocultos.
- O armazenamento em cache tem um bloqueio que precisa ser liberado antes que o cache possa ser excluído.

Exclua da seguinte maneira: 

1. Localize os recursos no grupo de recursos ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Verifique se toda a VM e os outros recursos de origem da região de origem foram movidos ou excluídos. Isso verifica se não há recursos pendentes usando-os.
2. Exclua os recursos:

    - O nome da coleção da movimentação é ```movecollection-<sourceregion>-<target-region>```.
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](about-move-process.md) o processo de movimentação.