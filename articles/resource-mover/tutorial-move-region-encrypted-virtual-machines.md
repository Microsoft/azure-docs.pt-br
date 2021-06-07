---
title: Mover VMs do Azure criptografadas entre regiões com o Azure Resource Mover
description: Saiba como mover VMs do Azure criptografadas para outra região com o Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361002"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutorial: Mover VMs do Azure criptografadas entre regiões

Neste artigo, saiba como mover VMs do Azure criptografadas para uma região do Azure diferente usando o [Azure Resource Mover](overview.md). Veja o que queremos dizer com criptografia:

- VMs que têm discos com a criptografia de disco do Azure habilitada. [Saiba mais](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Ou VMs que usam CMKs (chaves gerenciadas pelo cliente) para criptografia em repouso (criptografia do lado do servidor). [Saiba mais](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar pré-requisitos. 
> * Para VMs com a criptografia de disco do Azure habilitada, copiar chaves e segredos do cofre de chaves da região de origem para o cofre de chaves da região de destino.
> * Preparar VMs para movê-las e selecionar recursos na região de origem que deseja mover.
> * Resolver as dependências do recurso.
> * Para VMs com a criptografia de disco do Azure habilitada, atribuir manualmente o cofre de chaves de destino. Para VMs que usam a criptografia do lado do servidor com chaves gerenciadas pelo cliente, atribuir manualmente um conjunto de criptografia de disco na região de destino.
> * Mover o cofre de chaves e/ou o conjunto de criptografia de disco.
> * Preparar e mover o grupo de recursos de origem. 
> * Preparar e mover os outros recursos.
> * Decidir se deseja descartar ou confirmar a movimentação. 
> * Opcionalmente, remover os recursos da região de origem após a movimentação.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

**Requisito** |**Detalhes**
--- | ---
**Permissões de assinatura** | Verifique se você tem acesso de *Proprietário* na assinatura que contém os recursos que deseja mover.<br/><br/> **Por que preciso de acesso de Proprietário?** Na primeira vez que você adicionar um recurso para um par de origem e destino específico em uma assinatura do Azure, o Resource Mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI [Identidade Gerenciada de Serviço]) que é confiável para a assinatura. Para criar a identidade e atribuir a ela a função necessária (colaborador e administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
**Suporte à VM** | Verifique se as VMs que você deseja mover são compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões de kernel compatíveis.<br/><br/> - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) compatíveis.
**Requisitos do cofre de chaves (Azure Disk Encryption)** | Se tiver o Azure Disk Encryption habilitado para as VMs, além do cofre de chaves na região de origem, você precisará de um cofre de chaves na região de destino. [Criar um cofre de chaves](../key-vault/general/quick-create-portal.md).<br/><br/> Para os cofres de chaves nas regiões de origem e de destino, você precisará destas permissões:<br/><br/> - Permissões de chave: Operações de gerenciamento de chaves (Obter, Listar); operações criptográficas (Descriptografar e Criptografar).<br/><br/> - Permissões de segredo: Operações de gerenciamento de segredos (Obter, Listar e Definir)<br/><br/> - Certificado (Listar e Obter).
**Conjunto de criptografia de disco (criptografia no lado do servidor com CMK)** | Se estiver usando VMs com criptografia no lado do servidor usando um CMK, além do conjunto de criptografia de disco na região de origem, você precisará do conjunto de criptografia de disco na região de destino. [Criar um conjunto de criptografia de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Não há suporte para a movimentação entre regiões quando você está usando chaves HSM para chaves gerenciadas pelo cliente.
**Cota da região de destino** | A assinatura precisa de cota suficiente para criar os recursos que você está movendo na região de destino. Se não houver cota, [solicite limites adicionais](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Preços da região de destino** | Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Verificar permissões de usuário no cofre de chaves para VMS usando o ADE (Azure Disk Encryption)

Se você estiver movendo as VMs que têm o Azure Disk Encryption habilitado, você precisará executar um script, conforme mencionado [abaixo](#copy-the-keys-to-the-destination-key-vault), para o qual o usuário que está executando o script deve ter as permissões apropriadas. Veja abaixo a tabela abaixo para saber mais sobre as permissões necessárias. As opções para alterar as permissões podem ser encontradas navegando até o cofre de chaves no portal do Azure, em **Configurações**, selecione **Políticas de acesso**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Botão para abrir as políticas de acesso do cofre de chaves." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Se não houver permissões de usuário, selecione **Adicionar Política de Acesso** e especifique as permissões. Se a conta de usuário já tiver uma política, em **Usuário**, defina as permissões conforme a tabela abaixo.

As VMs do Azure que usam o ADE podem ter as variações a seguir e as permissões precisam ser definidas adequadamente para os componentes relevantes.
- Opção padrão em que o disco é criptografado usando apenas segredos
- Segurança adicionada usando a [chave de criptografia de chave](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Cofre de chaves da região de origem

As permissões abaixo precisam ser definidas para o usuário que está executando o script 

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Permissão Get <br> </br> Em **Permissões do segredo**>  **Operações de Gerenciamento de Segredos**, selecione **Get** 
simétricas <br> </br> Se você estiver usando a KEK (chave de criptografia de chave), precisará dessa permissão, além dos segredos| Permissão Get e Decrypt <br> </br> Em **Permissões de Chave** > **Operações de Gerenciamento de Chave**, selecione **Get**. Em **Operações Criptográficas**, selecione **Descriptografar**.

### <a name="destination-region-keyvault"></a>Cofre de chaves da região de destino

Em **Políticas de acesso**, verifique se **Azure Disk Encryption para a criptografia de volume** está habilitado. 

As permissões abaixo precisam ser definidas para o usuário que está executando o script 

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Permissão Set <br> </br> Em **Permissões do segredo**>  **Operações de Gerenciamento de Segredos**, selecione **Set** 
simétricas <br> </br> Se você estiver usando a KEK (chave de criptografia de chave), precisará dessa permissão, além dos segredos| Permissões Get, Create e Encrypt <br> </br> Em **Permissões de Chave** > **Operações de Gerenciamento de Chave**, selecione **Get** e **Create**. Em **Operações Criptográficas**, selecione **Criptografar**.

Além das permissões acima, no cofre de chaves de destino, você precisa adicionar permissões para a [Identidade do Sistema Gerenciado](./common-questions.md#how-is-managed-identity-used-in-resource-mover) que o Resource Mover usa para acessar os recursos do Azure no seu nome. 

1. Em **Configurações**, selecione **Adicionar políticas de acesso**. 
2. Em **Selecionar entidade de segurança**, pesquise o MSI. O nome do MSI é ```movecollection-<sourceregion>-<target-region>-<metadata-region>```. 
3. Adicionar as permissões abaixo para o MSI

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Permissão Get e List <br> </br> Em **Permissões do segredo**>  **Operações de Gerenciamento de Segredos**, selecione **Get** e **List** 
simétricas <br> </br> Se você estiver usando a KEK (chave de criptografia de chave), precisará dessa permissão, além dos segredos| Permissão de Get e List <br> </br> Em **Permissões de Chave** > **Operações de Gerenciamento de Chave**, selecione **Get** e **List**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copiar as chaves para o cofre de chaves de destino

Você precisa copiar os segredos e as chaves de criptografia do cofre de chaves de origem para o cofre de chaves de destino usando um script que fornecemos.

- Execute o script no PowerShell. É recomendável executar a versão mais recente do PowerShell.
- Especificamente, o script requer estes módulos:
    - Az.Compute
    - Az.KeyVault (versão 3.0.0)
    - Az.Accounts (versão 2.2.3)

Execute da seguinte maneira:

1. Navegue até o [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) no GitHub.
2. Copie o conteúdo do script para um arquivo local e nomeie-o como *Copy-keys.ps1*.
3. Execute o script.
4. Entre no Azure.
5. No item pop-up **Entrada de Usuário**, selecione a assinatura de origem, o grupo de recursos e a VM de origem. Em seguida, selecione a localização de destino e os cofres de destino para a criptografia de disco e chave.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Pop-up para inserir os valores de script." :::


6. Quando o script for concluído, a saída da tela indicará que CopyKeys foi bem-sucedido.

## <a name="prepare-vms"></a>Como preparar as VMs

1. Após [verificar se as VMs atendem aos requisitos](#prerequisites), verifique se as VMs que deseja mover estão ativadas. Todos os discos de VMs que você deseja disponibilizar na região de destino devem ser anexados e inicializados na VM.
3. Verifique se as VMs têm os certificados raiz confiáveis mais recentes e uma CRL (lista de certificados revogados) atualizada. Para fazer isso:
    - Nas VMs do Windows, instale as atualizações mais recentes do Windows.
    - Nas VMs do Linux, siga as diretrizes do distribuidor para que os computadores tenham os certificados mais recentes e a CRL atualizada. 
4. Permita conectividade de saída das VMs da seguinte maneira:
    - Caso esteja usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita acesso a estas [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Caso esteja usando regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, crie essas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecionar recursos a serem movidos


- Você pode selecionar qualquer tipo de recurso compatível em qualquer grupo de recursos da região de origem que selecionar.  
- Você moverá os recursos para uma região de destino na mesma assinatura que a região de origem. Se você quiser alterar a assinatura, poderá fazer isso depois que os recursos forem movidos.

Selecione os recursos da seguinte maneira:

1. No portal do Azure, pesquise por *Resource Mover*. Em seguida, em **Serviços**, selecione o **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Resultados da pesquisa por Resource Mover no portal do Azure." :::

2. Em **Visão geral**, clique em **Mover entre regiões**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Botão para adicionar recursos a serem movidos para outra região." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Em **Mover recursos** > **Origem + Destino**, selecione a assinatura e a região de origem.
4. Em **Destino**, selecione a região para a qual deseja mover as VMs. Em seguida, clique em **Próximo**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Página para selecionar a região de origem e de destino." :::

5. Em **Recursos a serem movidos**, clique em **Selecionar recursos**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Botão para selecionar o recurso a ser movido.]." :::

6. Em **Selecionar recursos**, selecione as VMs. Você só pode adicionar recursos [compatíveis para movimentação](#prepare-vms). Em seguida, clique em **Concluído**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Página para selecionar as VMs a serem movidas." :::

    > [!NOTE]
    >  Neste tutorial, estamos selecionando uma VM que usa a criptografia do lado do servidor (rayne-vm) com uma chave gerenciada pelo cliente e uma VM com a criptografia de disco habilitada (rayne-vm-ade).

7.  Em **Recursos a serem movidos**, clique **Próximo**.
8. Em **Examinar**, verifique as configurações de origem e de destino. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Página para examinar as configurações e prosseguir com a movimentação." :::

9. Clique em **Prosseguir**, para começar a adicionar os recursos.
10. Selecione o ícone de notificações para acompanhar o progresso. Após o processo de adição ser concluído com sucesso, selecione **Recursos adicionados para movimentação** nas notificações.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Notificação para confirmar que os recursos foram adicionados com êxito." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Depois de clicar na notificação, examine os recursos da página **Entre regiões**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Páginas mostrando recursos adicionados com a preparação pendente." :::

> [!NOTE]
> - Os recursos que você adiciona são colocados em um estado de *Preparação pendente*.
> - O grupo de recursos para as VMs é adicionado automaticamente.
> - Se você modificar as entradas da **Configuração de destino** para usar um recurso que já existe na região de destino, o estado do recurso será definido como *Confirmação pendente*, pois você não precisará iniciar uma movimentação para ela.
> - Se você quiser remover um recurso que foi adicionado, o método para fazer isso dependerá do ponto do processo de movimentação em que você se encontra. [Saiba mais](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Resolver dependências

1. Se algum recurso mostrar uma mensagem *Validar dependências* na coluna **Problemas**, selecione o botão **Validar dependências**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Botão para verificar dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    O processo de validação começa.
2. Se forem encontradas dependências, clique em **Adicionar dependências**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Botão para adicionar dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. Em **Adicionar dependências**, deixe a opção padrão **Mostrar todas as dependências**.

    - **Mostrar todas as dependências** itera em todas as dependências diretas e indiretas de um recurso. Por exemplo, para uma VM, ela mostra a NIC, a rede virtual, os NSGs (grupos de segurança de rede) etc.
    - **Mostrar somente dependências de primeiro nível** mostra apenas dependências diretas. Por exemplo, para uma VM, mostra a NIC, mas não a rede virtual.
 
4. Selecione os recursos dependentes que deseja adicionar > **Adicionar dependências**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Selecionar dependências na lista de dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Valide as dependências novamente. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Página para validar novamente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Atribuir recursos de destino

Os recursos de destino associados à criptografia precisam de atribuição manual.

- Se você estiver movendo uma VM que tem o ADE (Azure Disk Encryption), o cofre de chaves na região de destino aparecerá como uma dependência.
- Se você estiver movendo uma VM que tem criptografia do lado do servidor e que usa CMKs (chaves gerenciadas personalizadas), o conjunto de criptografia de disco na região de destino aparecerá como uma dependência. 
- Como este tutorial está movendo uma VM com o ADE habilitado e uma VM usando um CMK, o cofre de chaves de destino e o conjunto de criptografia de disco aparecem como dependências.

Atribua manualmente da seguinte maneira:

1. Na entrada do conjunto de criptografia de disco, selecione **Recurso não atribuído** na coluna **Configuração de destino**.
2. Em **Parâmetros de configuração**, selecione o conjunto de criptografia de disco de destino. Em seguida, selecione **Salvar alterações**.
3. Você pode optar por salvar e validar as dependências do recurso que está modificando ou pode apenas salvar as alterações e validar tudo o que modificar em um só lugar.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Página para selecionar o conjunto de criptografia de disco na região de destino." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Após adicionar o recurso de destino, o status do conjunto de criptografia de disco passa a ser *Confirmação de movimentação pendente*.
3. Na entrada do cofre de chaves, selecione **Recurso não atribuído** na coluna **Configuração de destino**. Em **Parâmetros de configuração**, selecione o cofre de chaves de destino. Salve as alterações. 

Neste estágio, tanto o conjunto de criptografia de disco quanto o status do cofre de chaves se transformam em *Confirmação de movimentação pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Página para selecionar Preparar para outros recursos." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Para confirmar e concluir o processo de movimentação dos recursos de criptografia.

1. Em **Entre regiões**, selecione o recurso (conjunto de criptografia de disco ou cofre de chaves) > **Confirmar movimentação**.
2. Em **Mover recursos**, clique em **Confirmar**.

> [!NOTE]
> Após confirmar a movimentação, o recurso está no estado de *Excluir origem pendente*.


## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Para preparar e mover as VMs, o grupo de recursos de VM deve estar presente na região de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Durante o processo de Preparação, o Resource Mover gera modelos do ARM (Azure Resource Manager) usando as configurações do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

Prepare da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos de origem > **Preparar**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Preparar grupo de recursos." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Em **Preparar recursos**, clique em **Preparar**.

> [!NOTE]
> Após preparar o grupo de recursos, ele estará no estado de *Iniciar movimentação pendente*. 

 
### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie a movimentação da seguinte maneira:

1. Em **Entre regiões**, selecione o grupo de recursos > **Iniciar movimentação**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Botão para iniciar a movimentação." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. Em **Mover recursos**, clique em **Iniciar movimentação**. O grupo de recursos passa para o estado de *Iniciar movimentação em andamento*.   
3. Após iniciar a movimentação, o grupo de recursos de destino é criado, com base no modelo ARM gerado. O grupo de recursos de origem passa para o estado de *Confirmar movimentação pendente*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Examinar o estado de confirmar movimentação pendente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Para confirmar e concluir o processo de movimentação:

1. Em **Entre regiões**, selecione o grupo de recursos > **Confirmar movimentação**.
2. Em **Mover recursos**, clique em **Confirmar**.

> [!NOTE]
> Após confirmar a movimentação, o grupo de recursos de origem estará no estado de *Excluir origem pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Examinar o estado de excluir movimentação pendente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Preparar os recursos a serem movidos

Agora que os recursos de criptografia e o grupo de recursos de origem foram movidos, você pode se preparar para mover outros recursos que estão no estado *Preparação pendente*.


1. Em **Entre regiões**, valide novamente e resolva eventuais problemas.
2. Se você quiser editar as configurações de destino antes de iniciar a movimentação, selecione o link na coluna **Configuração de destino** para o recurso e edite as configurações. Se você editar as configurações da VM de destino, o tamanho da VM de destino não deverá ser menor que o tamanho da VM de origem.
3. Selecione **Preparar** para os recursos no estado *Preparação pendente* que deseja mover.
3. Em **Preparar recursos**, selecione **Preparar**

    - Durante o processo de preparação, o agente Mobilidade do Azure Site Recovery é instalado nas VMs, para replicá-las.
    - Os dados das VMs são replicados periodicamente para a região de destino. Isso não afeta as VMs de origem.
    - A Movimentação de Recursos gera modelos ARM para os outros recursos de origem.

Após preparar os recursos, eles estarão no estado de *Iniciar movimentação pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Página mostrando recursos no estado de Iniciar movimentação pendente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Inicie a movimentação

Com os recursos preparados, agora você pode iniciar a movimentação. 

1. Em **Entre regiões**, selecione recursos com o estado de *Iniciar movimentação pendente*. Então, clique em **Iniciar movimentação**.
2. Em **Mover recursos**, clique em **Iniciar movimentação**.
3. Monitore o progresso da movimentação na barra de notificações.

    - Para as VMs, são criadas réplicas delas na região de destino. A VM de origem é desligada e ocorre algum tempo de inatividade (geralmente minutos).
    - O Resource Mover recria outros recursos usando os modelos ARM que foram preparados. Geralmente, não há tempo de inatividade.
    - Após mover os recursos, eles estarão no estado de *Confirmar movimentação pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Página mostrando recursos no estado de Confirmar movimentação pendente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Página para confirmar os recursos a fim de finalizar a movimentação." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Monitore o progresso da confirmação na barra de notificações.

> [!NOTE]
> - Após confirmar a movimentação, a réplica das VMs é interrompida. A VM de origem não é afetada pela confirmação.
> - A confirmação não afeta os recursos de rede de origem.
> - Após confirmar a movimentação, os recursos estarão no estado de *Excluir origem pendente*.



## <a name="configure-settings-after-the-move"></a>Definir as configurações após a movimentação

- O serviço Mobilidade não é desinstalado automaticamente das VMs. Desinstale-o manualmente ou deixe-o instalado caso planeje mover o servidor novamente.
- Modifique as regras do Azure RBAC (controle de acesso baseado em função do Azure) após a movimentação.

## <a name="delete-source-resources-after-commit"></a>Excluir recursos de origem após a confirmação

Após a movimentação, você terá a opção de excluir os recursos da região de origem. 

1. Em **Entre regiões**, selecione cada recurso de origem que deseja excluir. Em seguida, selecione **Excluir origem**.
2. Em **Excluir origem**, examine o que você pretende excluir e, em **Confirmar exclusão**, digite **sim**. A ação é irreversível, portanto, verifique atentamente!
3. Depois de digitar **sim**, selecione **Excluir origem**.

> [!NOTE]
>  No portal de Movimentação de Recursos, você não pode excluir grupos de recursos, cofres de chaves nem servidores do SQL Server. Você precisa excluí-los individualmente na página de propriedades de cada recurso.


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
> * Moveu VMs do Azure criptografadas e seus recursos dependentes para outra região do Azure.


Agora, vamos tentar mover pools elásticos e bancos de dados SQL do Azure para outra região.

> [!div class="nextstepaction"]
> [Mover recursos do SQL do Azure](./tutorial-move-region-sql.md)
