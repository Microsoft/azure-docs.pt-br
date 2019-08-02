---
title: Mover um cofre dos Serviços de Recuperação entre assinaturas do Azure ou para outro grupo de recursos
description: Instruções para mover o cofre dos serviços de recuperação entre grupos de recursos e assinaturas do Azure.
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: 0e351a842a303430e66a52a87305cba23f774ef2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465811"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mover um cofre dos serviços de recuperação entre assinaturas e grupos de recursos do Azure

Este artigo explica como mover um cofre dos Serviços de Recuperação configurado para Backup do Azure entre assinaturas do Azure ou para outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-region"></a>Região com suporte

A movimentação de recursos para o cofre dos serviços de recuperação é suportada no leste da Austrália, leste da Austrália, Canadá central, leste do Canadá, Sul Ásia Oriental, Ásia Oriental, EUA Central, norte EUA Central, leste dos EUA, leste dos EUA 2, centro-sul dos EUA, Oeste EUA Central, dos EUA 2 central ocidental, oeste dos EUA, Índia central, sul da Índia, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, Europa Setentrional, Europa Ocidental, norte da África do Sul, oeste da África do Sul, Sul do Reino Unido e Oeste do Reino Unido.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para mover o cofre dos serviços de recuperação

- Durante a movimentação do cofre entre grupos de recursos, os grupos de recursos de origem e de destino são bloqueados, impedindo as operações de gravação e exclusão. Para obter mais informações, veja este [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Somente a assinatura de administrador tem as permissões para mover um cofre.
- Para mover o cofre entre assinaturas, a assinatura de destino deve residir no mesmo locatário que a assinatura de origem e seu estado deve ser habilitado.
- Você deve ter permissão para executar operações de gravação no grupo de recursos de destino.
- Mover cofre altera apenas o grupo de recursos. O cofre dos serviços de recuperação residirá no mesmo local e não poderá ser alterado.
- Você pode mover apenas um cofre dos serviços de recuperação, por região, por vez.
- Se uma VM não se mover com o cofre dos serviços de recuperação entre assinaturas ou para um novo grupo de recursos, os pontos de recuperação da VM atuais permanecerão intactos no cofre até que expirem.
- Seja a VM movida com o cofre ou não, você sempre pode restaurar a VM do histórico de backup retido no cofre.
- A Azure Disk Encryption requer que o cofre de chaves e as VMs residam na mesma região e assinatura do Azure.
- Para mover uma máquina virtual com discos gerenciados, veja este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções de movimentação dos recursos implantados por meio do modelo Clássico apresentam diferenças dependendo de se você está movendo os recursos em uma assinatura ou para uma nova assinatura. Para obter mais informações, veja este [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Políticas de backup definidas para o cofre são mantidas após o cofre ser movido entre assinaturas ou para um novo grupo de recursos.
- Não há suporte para mover o cofre com os arquivos do Azure, Sincronização de Arquivos do Azure ou SQL em VMs IaaS entre assinaturas e grupos de recursos.
- Se você mover um cofre contendo dados de backup da VM entre assinaturas, deverá mover as VMs para a mesma assinatura e usar o mesmo grupo de recursos de destino para continuar com os backups.<br>

> [!NOTE]
>
> Cofres dos Serviços de Recuperação configurados para uso com o **Azure Site Recovery** ainda não podem ser movidos. Se você tiver configurado todas as VMs (IaaS do Azure, Hyper-V, VMware) ou computadores físicos para recuperação de desastre usando o **Azure Site Recovery**, a operação de movimentação será bloqueada. O recurso de movimentação de recursos para o serviço do Site Recovery ainda não está disponível.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para um grupo de recursos diferente

Para mover um cofre dos serviços de recuperação e seus recursos associados para um grupo de recursos diferentes

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra a lista de **Cofres dos Serviços de Recuperação** e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

   ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

   ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** ao lado de **Grupo de recursos** para abrir a folha **Mover recursos**.

   ![Alterar Grupo de Recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na folha **Mover recursos**, para o cofre selecionado, é recomendado mover os recursos relacionados opcionais marcando a caixa de seleção conforme mostrado na imagem a seguir.

   ![Mover assinatura](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme a opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** e, em seguida, clique em **OK** para concluir a movimentação do cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para uma assinatura diferente

Você pode mover um cofre dos Serviços de Recuperação e seus recursos associados para uma assinatura diferente

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

    ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

    ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** lado de **Assinatura** para abrir a folha **Mover recursos**.

   ![Alterar assinatura](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos. Aqui, recomendamos usar a opção **Selecionar Tudo** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a assinatura de destino na lista suspensa **Assinaturas** para a qual você deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Adicionar Assinatura](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique na opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** para confirmar e, em seguida, clique em **OK**.

> [!NOTE]
> Backup entre assinaturas (cofre RS e VMs protegidas estão em assinaturas diferentes) não é um cenário com suporte. Além disso, a opção de redundância de armazenamento de LRS (armazenamento com redundância local) para GRS (armazenamento com redundância global) e vice-versa não pode ser modificada durante a operação de movimentação de cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usar o PowerShell para mover o cofre dos serviços de recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o cmdlet `Move-AzureRMResource`. `Move-AzureRMResource` requer o nome do recurso e o tipo de recurso. Você pode obter ambos do cmdlet `Get-AzureRmRecoveryServicesVault`.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para uma assinatura diferente, inclua o parâmetro `-DestinationSubscriptionId`.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar os cmdlets acima, você será solicitado a confirmar se deseja mover os recursos especificados. Digite **Y** para confirmar. Após uma validação bem-sucedida, o recurso será movido.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usar a CLI para mover o cofre dos serviços de recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o seguinte cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

## <a name="post-migration"></a>Após a migração

1. Você precisa definir/verificar os controles de acesso para os grupos de recursos.  
2. O recurso de monitoramento e relatórios de Backup precisa ser configurado novamente para o cofre após a movimentação ser concluída. A configuração anterior será perdida durante a operação de movimentação.



## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
