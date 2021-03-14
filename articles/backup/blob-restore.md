---
title: Restaurar BLOBs do Azure
description: Saiba como restaurar BLOBs do Azure (em versão prévia).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744162"
---
# <a name="restore-azure-blobs-in-preview"></a>Restaurar BLOBs do Azure (em versão prévia)

Blobs de blocos em contas de armazenamento com backup operacional configurado podem ser restaurados para qualquer ponto no tempo dentro do período de retenção. Além disso, você pode fazer o escopo de suas restaurações para todos os blobs de blocos na conta de armazenamento ou para um subconjunto de BLOBs.

## <a name="before-you-start"></a>Antes de começar

- Os BLOBs serão restaurados para a mesma conta de armazenamento. Portanto, os blobs que sofreram alterações feitas desde o momento em que você está restaurando serão substituídos.
- Somente blobs de blocos em uma conta de armazenamento padrão de uso geral v2 podem ser restaurados como parte de uma operação de restauração. Blobs de acréscimo, blobs de página e blobs de blocos Premium não são restaurados.
- Enquanto um trabalho de restauração está em andamento, os BLOBs no armazenamento não podem ser lidos ou gravados.
- Um blob com uma concessão ativa não pode ser restaurado. Se um blob com uma concessão ativa estiver incluído no intervalo de BLOBs a serem restaurados, a operação de restauração falhará automaticamente. Interrompa todas as concessões ativas antes de iniciar a operação de restauração.
- Os instantâneos não são criados ou excluídos como parte de uma operação de restauração. Somente o blob de base é restaurado para seu estado anterior.
- Se você excluir um contêiner da conta de armazenamento chamando a operação **excluir contêiner** , esse contêiner não poderá ser restaurado com uma operação de restauração. Em vez de excluir um contêiner inteiro, exclua BLOBs individuais se você quiser restaurá-los mais tarde. Além disso, a Microsoft recomenda a habilitação da exclusão reversível para contêineres, além do backup operacional, para proteger contra a exclusão acidental de contêineres.
- Consulte a [matriz de suporte](blob-backup-support-matrix.md) para todas as limitações e cenários com suporte.

## <a name="restore-blobs"></a>Restaurar BLOBs

Você pode iniciar uma restauração por meio do centro de backup.

1. Em centro de backup, vá para **restaurar** na barra superior.

    ![Restaurar no centro de backup](./media/blob-restore/backup-center-restore.png)

1. Na guia **Iniciar restauração** , escolha **BLOBs do Azure (armazenamento do Azure)** como o tipo DataSource e selecione a **instância de backup** que você deseja restaurar. A instância de backup aqui é a conta de armazenamento que contém os blobs que você deseja restaurar.

     ![Selecionar instância de backup](./media/blob-restore/select-backup-instance.png)

1. Na guia **selecionar ponto de recuperação** , escolha a data e a hora em que você deseja restaurar os dados. Você também pode usar o controle deslizante para escolher o ponto no tempo de restauração. A bolha de informações ao lado da data mostra a duração válida a partir da qual você pode restaurar seus dados. O backup operacional para BLOBs que estão sendo contínuos de backup oferece controle granular sobre pontos dos quais recuperar dados.

    >[!NOTE]
    > A hora representada aqui é a hora local.

    ![Data e hora da restauração](./media/blob-restore/date-and-time.png)

1. Na guia **restaurar parâmetros** , escolha se deseja restaurar todos os BLOBs na conta de armazenamento, contêineres específicos ou um subconjunto de BLOBs usando correspondência de prefixo. Ao usar a correspondência de prefixo, você pode especificar até 10 intervalos de prefixos ou de caminho de File. Mais detalhes sobre como usar a correspondência de prefixo [aqui](#use-prefix-match-for-restoring-blobs).

    ![Restaurar parâmetros](./media/blob-restore/restore-parameters.png)

    Escolha uma destas opções:

    - **Restaurar todos os BLOBs na conta de armazenamento**: usar essa opção restaura todos os blobs de blocos na conta de armazenamento, redistribuindo-os de volta para o ponto selecionado no tempo. As contas de armazenamento que contêm grandes quantidades de dados ou a testemunha de uma alta rotatividade podem levar mais tempo para serem restauradas.

    - **Procurar e restaurar contêineres selecionados**: usar essa opção permite que você procure e selecione até 10 contêineres para restaurar. Você deve ter permissões suficientes para exibir os contêineres na conta de armazenamento ou pode não conseguir ver o conteúdo da conta de armazenamento.

    - **Selecionar BLOBs para restaurar usando correspondência de prefixo**: essa opção permite restaurar um subconjunto de BLOBs usando uma correspondência de prefixo. Você pode especificar até 10 intervalos lexicográfica de BLOBs em um único contêiner ou em vários contêineres para retornar esses BLOBs para seu estado anterior em um determinado momento. Aqui estão algumas coisas para ter em mente:

        - Você pode usar uma barra (/) para delinear o nome do contêiner do prefixo do blob
        - O início do intervalo especificado é inclusivo, no entanto, o intervalo especificado é exclusivo.

    Para obter mais informações sobre como usar prefixos para restaurar intervalos de BLOB, consulte [esta seção](#use-prefix-match-for-restoring-blobs).

1. Quando terminar de especificar quais BLOBs restaurar, continue na guia **revisar + restaurar** e selecione **restaurar** para iniciar a restauração.

1. **Rastrear restauração**: Use a exibição de **trabalhos de backup** para acompanhar os detalhes e o status das restaurações. Para fazer isso, navegue até os trabalhos de backup do centro de **backup**  >  . O status será exibido **em andamento** enquanto a restauração estiver sendo executada.

    ![Guia trabalhos de backup](./media/blob-restore/backup-jobs.png)

    Quando a operação de restauração for concluída com êxito, o status será alterado para **concluído**. Depois que a restauração for concluída com êxito, você poderá ler e gravar BLOBs na conta de armazenamento novamente.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="use-prefix-match-for-restoring-blobs"></a>Usar correspondência de prefixo para restaurar BLOBs

Considere o seguinte exemplo:

![Restaurar com correspondência de prefixo](./media/blob-restore/prefix-match.png)

A operação de restauração mostrada na imagem executa as seguintes ações:

- Ele restaura todo o conteúdo de *Container1*.
- Ele restaura os BLOBs no intervalo de lexicográfica *blob1* por meio de *blob5* no *container2*. Esse intervalo restaura os BLOBs com nomes como *blob1*, *blob11*, *blob100*, *blob2* e assim por diante. Como o final do intervalo é exclusivo, ele restaura os BLOBs cujos nomes começam com *blob4*, mas não restaura os BLOBs cujos nomes começam com *blob5*.
- Ele restaura todos os BLOBs em *container3* e *Container4*. Como o final do intervalo é exclusivo, esse intervalo não restaura *container5*.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do backup operacional para BLOBs do Azure (em versão prévia)](blob-backup-overview.md)
