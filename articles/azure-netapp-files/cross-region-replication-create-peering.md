---
title: Criar replicação de volume para Azure NetApp Files | Microsoft Docs
description: Descreve como criar o emparelhamento de replicação de volume para Azure NetApp Files para configurar a replicação entre regiões.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 412724a072bfc03a67bf1005173702f757c6fdf4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249930"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Criar replicação de volume para Azure NetApp Files

> [!IMPORTANT]
> O recurso de replicação entre regiões está atualmente em visualização pública. Você precisa enviar uma solicitação Waitlist para acessar o recurso por meio da [página de envio de espera de replicação entre regiões Azure NetApp files](https://aka.ms/anfcrrpreviewsignup). Aguarde um email de confirmação oficial da equipe de Azure NetApp Files antes de usar o recurso de replicação entre regiões.

Este artigo mostra como configurar a replicação entre regiões criando o emparelhamento de replicação. 

A configuração do emparelhamento de replicação permite replicar dados de forma assíncrona de um Azure NetApp Files volume (origem) para outro volume de Azure NetApp Files (destino). O volume de origem e o volume de destino devem ser implantados em regiões separadas. O nível de serviço para o pool de capacidade de destino pode corresponder ao do pool de capacidade de origem, ou você pode selecionar um nível de serviço diferente.   

Atualmente, a replicação do Azure NetApp Files não dá suporte a várias assinaturas; todas as replicações devem ser executadas em uma única assinatura.

Antes de começar, verifique se você examinou os [requisitos e as considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Localizar a ID do recurso de volume de origem  

Você precisa obter a ID de recurso do volume de origem que deseja replicar. 

1. Vá para o volume de origem e selecione **Propriedades** em configurações para exibir a ID do recurso de volume de origem.   
    ![Localizar ID de recurso de volume de origem](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Copie a ID do recurso para a área de transferência.  Você precisará dela mais tarde.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Criar o volume de replicação de dados (o volume de destino)

Você precisa criar um volume de destino no qual deseja que os dados do volume de origem sejam replicados.  Para poder criar um volume de destino, você precisa ter uma conta do NetApp e um pool de capacidade na região de destino. 

1. A conta de destino deve estar em uma região diferente da região do volume de origem. Se necessário, crie uma conta do NetApp na região do Azure a ser usada para replicação seguindo as etapas em [criar uma conta do NetApp](azure-netapp-files-create-netapp-account.md).   
Você também pode selecionar uma conta existente do NetApp em uma região diferente.  

2. Se necessário, crie um pool de capacidade na conta do NetApp recém-criada seguindo as etapas em [configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).   

    Você também pode selecionar um pool de capacidade existente para hospedar o volume de destino de replicação.  

    O nível de serviço para o pool de capacidade de destino pode corresponder ao do pool de capacidade de origem, ou você pode selecionar um nível de serviço diferente.

3. Delegue uma sub-rede na região a ser usada para replicação seguindo as etapas em [delegar uma sub-rede para Azure NetApp files](azure-netapp-files-delegate-subnet.md).

4. Crie o volume de replicação de dados selecionando **volumes** em serviço de armazenamento na conta de destino da NetApp. Em seguida, clique no botão **+ adicionar replicação de dados** .  

    ![Adicionar replicação de dados](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Na página criar um volume que aparece, preencha os seguintes campos na guia **noções básicas** :
    * Nome do volume
    * Pool de capacidade
    * Cota de volume
        > [!NOTE] 
        > É recomendável que o tamanho da cota de volume espelhe o volume de origem.
    * Rede virtual 
    * Sub-rede

    Para obter detalhes sobre os campos, consulte [criar um volume de NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. Na guia **protocolo** , selecione o mesmo protocolo que o volume de origem.  
Para o protocolo NFS, verifique se as regras da política de exportação atendem aos requisitos de todos os hosts na rede remota que acessarão a exportação.  

7. Na guia **marcas** , crie pares de chave/valor conforme necessário.  

8. Na guia **replicação** , Cole a ID de recurso de volume de origem obtida em [localizar a ID do recurso de volume de origem](#locate-the-source-volume-resource-id)e selecione a agenda de replicação desejada. As opções de agendamento de replicação incluem: a cada 10 minutos, por hora, diariamente, semanalmente e mensalmente.  

    ![Criar replicação de volume](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Clique em **revisar + criar** e em **criar** para criar o volume de replicação de dados.   

    ![Examinar e criar a replicação](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autorizar a replicação do volume de origem  

Para autorizar a replicação, você precisa obter a ID de recurso do volume de destino de replicação e colá-la no campo autorizar do volume de origem de replicação. 

1. Na portal do Azure, navegue até Azure NetApp Files.

2. Vá para a conta do NetApp de destino e o pool de capacidade de destino onde o volume de destino de replicação está localizado.

3. Selecione o volume de destino de replicação, vá para **Propriedades** em configurações e localize a **ID de recurso** do volume de destino. Copie a ID de recurso de volume de destino para a área de transferência.

    ![ID do recurso de propriedades](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Em Azure NetApp Files, vá para a conta de origem de replicação e o pool de capacidade de origem. 

5. Localize o volume de origem de replicação e selecione-o. Acesse **replicação** em serviço de armazenamento e clique em **autorizar**.

    ![Autorizar a replicação](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. No campo autorizar, Cole a ID de recurso do volume de replicação de destino obtida na etapa 3 e clique em **OK**.

## <a name="next-steps"></a>Próximas etapas  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para usar a replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Exibir o status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Gerenciar recuperação de desastre](cross-region-replication-manage-disaster-recovery.md)
* [Excluir volumes ou replicações de volume](cross-region-replication-delete.md)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)

