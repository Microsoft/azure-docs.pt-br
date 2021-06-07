---
title: Mover VMs do Azure entre regiões com o Azure Resource Mover
description: Saiba como mover VMs do Azure para outra região com o Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565726"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Tutorial: mover VMs do Azure entre regiões

Neste artigo, saiba como mover VMs do Azure e recursos de rede/armazenamento relacionados para uma região do Azure diferente, usando o [Azure Resource Mover](overview.md).
.


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos e os requisitos.
> * Selecionar os recursos que deseja mover.
> * Resolver as dependências do recurso.
> * Preparar e mover o grupo de recursos de origem. 
> * Preparar e mover os outros recursos.
> * Decidir se deseja descartar ou confirmar a movimentação. 
> * Opcionalmente, remover os recursos da região de origem após a movimentação.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos
**Requisito** | **Descrição**
--- | ---
**Suporte do Resource Mover** | [Examine](common-questions.md) as regiões com suporte e outras perguntas comuns.
**Permissões de assinatura** | Verifique se você tem acesso de *Proprietário* na assinatura que contém os recursos que deseja mover<br/><br/> **Por que preciso de acesso de Proprietário?** Na primeira vez que você adicionar um recurso para um par de origem e destino específico em uma assinatura do Azure, o Resource Mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI [Identidade Gerenciada de Serviço]) que é confiável para a assinatura. Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
**Suporte à VM** |  Verifique se as VMs que você deseja mover são compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões de kernel compatíveis.<br/><br/> - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) compatíveis.
**Assinatura de destino** | A assinatura na região de destino precisa de cota suficiente para criar os recursos que você está movendo na região de destino. Se não houver cota, [solicite limites adicionais](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Preços da região de destino** | Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.
    

## <a name="prepare-vms"></a>Como preparar as VMs

1. Após verificar se as VMs atendem aos requisitos, verifique se as VMs que deseja mover estão ativadas. Todos os discos de VMs que você deseja disponibilizar na região de destino devem ser anexados e inicializados na VM.
1. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. Para fazer isso:
    - Nas VMs do Windows, instale as atualizações mais recentes do Windows.
    - Nas VMs do Linux, siga as diretrizes do distribuidor para que os computadores tenham os certificados mais recentes e a CRL atualizada. 
1. Permitir conectividade de saída das VMs:
    - Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Caso esteja usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Selecionar recursos 

Selecione os recursos que deseja mover.

- Todos os tipos de recursos compatíveis nos grupos de recursos dentro da região de origem selecionada são exibidos.
- Os recursos que já foram adicionados para executar a movimentação entre as regiões não são exibidos.
- Você moverá os recursos para uma região de destino na mesma assinatura que a região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

1. No portal do Azure, pesquise por *Resource Mover*. Em seguida, em **Serviços**, selecione o **Azure Resource Mover**.

    ![Resultados da pesquisa por Resource Mover no portal do Azure](./media/tutorial-move-region-virtual-machines/search.png)

2. Em **Visão Geral**, clique em **Introdução**.

    ![Botão para adicionar recursos a serem movidos para outra região](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Em **Mover recursos** > **Origem + Destino**, selecione a assinatura e a região de origem.
4. Em **Destino**, selecione a região para a qual deseja mover as VMs. Em seguida, clique em **Próximo**.

    ![Página para selecionar a região de origem e de destino](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Em **Recursos a serem movidos**, clique em **Selecionar recursos**.
7. Em **Selecionar recursos**, selecione a VM desejada. Você só pode adicionar [recursos compatíveis para movimentação](#prepare-vms). Em seguida, clique em **Concluído**.

    ![Página para selecionar as VMs a serem movidas](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Em **Recursos a serem movidos**, clique **Próximo**.
9. Em **Examinar**, verifique as configurações de origem e de destino. 

    ![Página para examinar as configurações e prosseguir com a movimentação](./media/tutorial-move-region-virtual-machines/review.png)
10. Clique em **Prosseguir**, para começar a adicionar os recursos.
11. Depois que o processo de adição for concluído com sucesso, clique em **Adicionando recursos a serem movidos** no ícone de notificação.
12. Depois de clicar na notificação, examine os recursos da página **Entre regiões**.

> [!NOTE]
> - Os recursos adicionados estão no estado de *Preparação pendente*.
> - O grupo de recursos para as VMs é adicionado automaticamente.
> - Se você quiser remover um recurso de uma coleção de movimentação, o método para fazer isso dependerá do ponto do processo de movimentação em que você se encontra. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências

1. Se os recursos mostrarem uma mensagem *Validar dependências* na coluna **Problemas**, clique no botão **Validar dependências**. O processo de validação começa.
2. Se forem encontradas dependências, clique em **Adicionar dependências**. 
3. Em **Adicionar dependências**, deixe a opção padrão **Mostrar todas as dependências**.

    - Mostrar todas as dependências itera em todas as dependências diretas e indiretas de um recurso. Por exemplo, para uma VM, ela mostra a NIC, a rede virtual, os NSGs (grupos de segurança de rede) etc.
    - Mostrar somente dependências de primeiro nível mostra apenas dependências diretas. Por exemplo, para uma VM, mostra a NIC, mas não a rede virtual.


4. Selecione os recursos dependentes que deseja adicionar > **Adicionar dependências**. Monitore o progresso nas notificações.

    ![Adicionar dependências](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Valide as dependências novamente. 
    ![Página para adicionar dependências adicionais](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Para preparar e mover as VMs, o grupo de recursos de VM deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Durante o processo de Preparação, o Resource Mover gera modelos do ARM (Azure Resource Manager) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

Prepare da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos de origem > **Preparar**.
2. Em **Preparar recursos**, clique em **Preparar**.

    ![Preparar grupo de recursos](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Após preparar o grupo de recursos, ele estará no estado de *Iniciar movimentação pendente*. 

 
### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos > **Iniciar movimentação**
2. Em **Mover recursos**, clique em **Iniciar movimentação**. O grupo de recursos passa para o estado de *Iniciar movimentação em andamento*.
3. Após iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem passa para o estado de *Confirmar movimentação pendente*.

    ![Clicar no botão iniciar movimentação](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Para confirmar e concluir o processo de movimentação:

1. Em **Entre regiões**, selecione o grupo de recursos > **Confirmar movimentação**.
2. Em **Mover recursos**, clique em **Confirmar**.

> [!NOTE]
> Após confirmar a movimentação, o grupo de recursos de origem estará no estado de *Excluir origem pendente*.

## <a name="prepare-resources-to-move"></a>Preparar os recursos a serem movidos

Agora que o grupo de recursos de origem foi movido, você pode se preparar para mover outros recursos que estão no estado *Preparação pendente*.

1. Em **Entre regiões**, verifique se os recursos estão agora no estado de *Preparação pendente*, sem problemas. Se não estiverem, valide novamente e resolva eventuais problemas pendentes.

    ![Página mostrando recursos no estado de preparação pendente](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Se você quiser editar as configurações de destino antes de iniciar a movimentação, selecione o link na coluna **Configuração de destino** para o recurso e edite as configurações. Se você editar as configurações da VM de destino, o tamanho da VM de destino não deverá ser menor que o tamanho da VM de origem.  

Agora que o grupo de recursos de origem foi movido, você pode se preparar para mover os outros recursos.

3. Selecionar os recursos que deseja preparar. 

    ![Página para selecionar Preparar para outros recursos](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Selecione **Preparar**. 

> [!NOTE]
> - Durante o processo de preparação, o agente Mobilidade do Azure Site Recovery é instalado nas VMs, para replicá-las.
> - Os dados das VMs são replicados periodicamente para a região de destino. Isso não afeta as VMs de origem.
> - A Movimentação de Recursos gera modelos ARM para os outros recursos de origem.
> - Após preparar os recursos, eles estarão no estado de *Iniciar movimentação pendente*.

![Página mostrando recursos no estado de Iniciar movimentação pendente](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Inicie a movimentação

Com os recursos preparados, agora você pode iniciar a movimentação. 

1. Em **Entre regiões**, selecione recursos com o estado de *Iniciar movimentação pendente*. Então, clique em **Iniciar movimentação**.
2. Em **Mover recursos**, clique em **Iniciar movimentação**.

    ![Clicar no botão Iniciar movimentação](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Monitore o progresso da movimentação na barra de notificações.

> [!NOTE]
> - Para as VMs, são criadas réplicas delas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).
> - O Resource Mover recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há tempo de inatividade.
> - Após mover os recursos, eles estarão no estado de *Confirmar movimentação pendente*.

![Página mostrando recursos no estado de "Excluir origem pendente"](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> Após descartar os recursos, as VMs estarão no estado de *Iniciar movimentação pendente*.

## <a name="commit-the-move"></a>Confirmar a movimentação

Se você quiser concluir o processo de movimentação, confirme a movimentação. 

1. Em **Entre regiões**, selecione recursos com o estado de *Confirmar movimentação pendente* e clique em **Confirmar movimentação**.
2. Em **Confirmar recursos**, clique em **Confirmar**.

    ![Página para confirmar os recursos a fim de finalizar a movimentação](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Monitore o progresso da confirmação na barra de notificações.

> [!NOTE]
> - Após confirmar a movimentação, a réplica das VMs é interrompida. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Após confirmar a movimentação, os recursos estarão no estado de *Excluir origem pendente*.

![Página mostrando recursos no estado de "Excluir origem pendente"](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

- O serviço Mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o instalado caso planeje mover o servidor novamente.
- Modifique as regras do Azure RBAC (controle de acesso baseado em função do Azure) após a movimentação.


## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após a confirmação

Após a movimentação, você terá a opção de excluir os recursos da região de origem. 

> [!NOTE]
> Alguns recursos, por exemplo, cofres de chaves e servidores do SQL Server, não podem ser excluídos do portal e precisam ser excluídos da página de propriedades do recurso.

1. Em **Entre regiões**, clique no nome de cada recurso de origem que deseja excluir.
2. Selecione **Excluir origem**.

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

Neste tutorial, você:

> [!div class="checklist"]
> * Moveu VMs do Azure para outra região do Azure.
> * Moveu recursos associados às VMs para outra região.

Agora, vamos tentar mover pools elásticos e bancos de dados SQL do Azure para outra região.

> [!div class="nextstepaction"]
> [Mover recursos do SQL do Azure](./tutorial-move-region-sql.md)
