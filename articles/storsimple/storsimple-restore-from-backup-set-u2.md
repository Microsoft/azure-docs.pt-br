---
title: Restaurar um volume do StorSimple de um backup | Microsoft Docs
description: "Explica como usar a página Catálogo de Backup do serviço StorSimple Manager para restaurar um volume do StorSimple de um conjunto de backup."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/22/2017
ms.author: alkohli
ms.openlocfilehash: 99b76e3bc2939c65654cbf606fda6f8a45e0c44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Restaurar um volume do StorSimple de um conjunto de backup (Atualização 2)
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Visão geral
A página **Catálogo de Backup** exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Use esta página para listar e gerenciar backups, restaurar de um conjunto de backups ou clonar um volume.

 ![Página Catálogo de Backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

Este tutorial explica como usar a página **Catálogo de Backup** para restaurar seu dispositivo a partir de um conjunto de backup.

Você pode restaurar um volume de um backup local ou na nuvem. Em ambos os casos, a operação de restauração coloca o volume online imediatamente, enquanto dados são baixados em segundo plano. 

## <a name="before-you-restore"></a>Antes de restaurar
Antes de iniciar uma operação de restauração, fique ciente das seguintes desvantagens:

* **Coloque o volume offline** – Coloque o volume offline no host e no dispositivo antes de iniciar a operação de restauração. Embora a operação de restauração coloque automaticamente o volume online no dispositivo, você deve colocar o dispositivo online manualmente no host. Você pode colocar o volume online no host quando o volume estiver online no dispositivo. (Não é necessário aguardar até que a operação de restauração seja concluída.) Para obter os procedimentos, acesse [Colocar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Tipo de volume após a restauração** – Volumes excluídos são restaurados com base no tipo do instantâneo. Os volumes que foram fixados localmente são restaurados como volumes fixados localmente, e os volumes separados em camadas são restaurados como volumes em camadas.
  
    Para os volumes existentes, o tipo de uso atual do volume substitui o tipo que é armazenado no instantâneo. Por exemplo, se você restaurar um volume de um instantâneo tirado quando o tipo de volume estava em camadas e esse tipo de volume agora estiver fixado localmente (devido a uma operação de conversão), o volume será restaurado como um volume fixado localmente. Da mesma forma, se um volume fixado localmente existente tiver sido expandido e, mais tarde, restaurado de um instantâneo mais antigo tirado quando o volume era menor, o volume restaurado manterá o tamanho expandido atual.
  
    Não é possível converter um volume de um volume em camadas para um volume fixado localmente, ou _vice-versa_, enquanto o volume estiver sendo restaurado. Aguarde até que a operação de restauração seja concluída para poder converter o volume em outro tipo. Para obter informações sobre como converter um volume, vá para [Alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **O tamanho do volume é refletido no volume restaurado** – Essa é uma consideração importante se estiver restaurando um volume fixado localmente que foi excluído (já que os volumes localmente fixados são totalmente provisionados). Certifique-se de que você tem espaço suficiente antes de tentar restaurar um volume fixado localmente que foi excluído antes. 
* **Não é possível expandir um volume enquanto ele está sendo restaurado** – Aguarde até que a operação de restauração seja concluída antes de tentar expandir o volume. Para obter informações sobre como expandir um volume, vá para [Modificar um volume](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Você pode executar um backup enquanto estiver restaurando um volume local** – Para obter os procedimentos, vá para [Usar o serviço do StorSimple Manager para gerenciar políticas de backup](storsimple-manage-backup-policies.md).
* **Você pode cancelar uma operação de restauração** – Se você cancelar o trabalho de restauração, o volume será revertido para seu estado anterior à restauração. Para obter os procedimentos, vá para [Cancelar um trabalho](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Como restaurar o trabalho
Para dispositivos que estão executando a Atualização 4 ou posterior, uma restauração baseada em mapa de calor é implementada. Quando as solicitações do host para acessar os dados chegam no dispositivo, essas solicitações são rastreadas e um mapa de calor é criado. Uma taxa de solicitação alta resulta em partes de dados com mais calor, enquanto uma taxa de solicitação menor é convertida em partes com menos calor. Você deve acessar os dados pelo menos duas vezes para que eles sejam marcados como _quentes_. Um arquivo que é modificado também é marcado como _quente_. Depois de iniciar a restauração, ocorre uma hidratação proativa de dados com base no mapa de calor. Para versões anteriores à Atualização 4, os dados foram baixados durante a restauração com base somente no acesso. 

O controle baseado em mapa de calor é habilitado somente para volumes com camadas, e não há suporte para volumes fixados localmente. Também não há suporte para a restauração baseada em mapa de calor ao clonar um volume para outro dispositivo. Se houver uma restauração local, e existir um instantâneo local para o volume a ser restaurado no dispositivo, não reidrataremos (pois os dados já estão disponíveis localmente). Por padrão, quando você restaura, os trabalhos de reidratação começam e reidratam proativamente os dados com base no mapa de calor. Na Atualização 4, os cmdlets do Windows PowerShell podem ser usados para consultar trabalhos reidratação em execução, cancelar um trabalho de reidratação e obter o status do trabalho de reidratação.

* `Get-HcsRehydrationJob` - Este cmdlet obtém o status do trabalho de reidratação. Um único trabalho de reidratação é disparado para um volume.
* `Set-HcsRehydrationJob` - Este cmdlet permite que você pause, interrompa, continue o trabalho de reidratação, quando a reidratação estiver em andamento. 

Para saber mais sobre como usar esse cmdlet, acesse [Referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Com a reidratação automática, espera-se um desempenho de leitura transitório normalmente mais alto. A magnitude real dos aprimoramentos depende de vários fatores, como o padrão de acesso, variação de dados e tipo de dados. Para cancelar um trabalho de reidratação, use o cmdlet do PowerShell. Se você quiser desativar permanentemente os trabalhos de reidratação de todas as restaurações futuras, entre em contato com o Suporte da Microsoft.

## <a name="how-to-use-the-backup-catalog"></a>Como usar o catálogo de backup
A página **Catálogo de Backup** oferece uma consulta que ajuda a restringir sua seleção de conjuntos de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

* **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.
* **Política** ou **volume** de Backup – A política ou volume de backup associado a este conjunto de backup.
* **De** e **Para** – O intervalo de datas e horas em que o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – O intervalo de data e hora quando os backups foram criados. 
* **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados de volume armazenados localmente no dispositivo. Um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciada por** – Os backups podem ser iniciados de forma automática, de acordo com uma agenda ou manualmente por você. (Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção **Fazer backup** para fazer um backup interativo).

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume StorSimple de um backup
É possível usar a página **Catálogo de Backup** para restaurar o volume do StorSimple de um backup específico. No entanto, tenha em mente que a restauração de um volume reverte o volume ao estado em que ele estava quando o backup foi feito. Todos os dados adicionados após a operação de backup são perdidos.

> [!WARNING]
> A restauração de um backup substitui os volumes existentes do backup. Isso pode causar a perda de todos os dados gravados depois que o backup tiver sido feito.
> 
> 

### <a name="to-restore-your-volume"></a>Para restaurar seu volume
1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup** .
   
    ![Catálogo de Backup](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Selecione um conjunto de backup desta maneira:
   
   1. Selecione o dispositivo adequado.
   2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para executar esta consulta.
      
      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.
3. Expanda o conjunto de backup para exibir os volumes associados. Esses volumes devem ficar offline no host e no dispositivo antes que você possa restaurá-los. Acesse os volumes na página **Contêineres de Volume** e, em seguida, siga as etapas em [Colocar um volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline) para colocá-los offline.
   
   > [!IMPORTANT]
   > Verifique se você colocou os volumes offline primeiro no host antes de colocar os volumes offline no dispositivo. Se você não colocar os volumes offline no host, poderá ocorrer corrupção nos dados.
   > 
   > 
4. Navegue de volta para a guia **Catálogo de Backup** e selecione um conjunto de backup.
5. Clique em **Restaurar** na parte inferior da página.
6. Você recebe uma solicitação para fornecer sua confirmação. Examine as informações de restauração e marque a caixa de seleção de confirmação.
   
    ![Página de confirmação](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Um trabalho de restauração começa. Você pode ver o trabalho acessando a página **Trabalhos**. 
8. Após a conclusão da restauração, você poderá verificar se o conteúdo de seus volumes foi substituído pelos volumes do backup.

![Vídeo disponível](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar os recursos de clonagem e restauração no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se a restauração falhar
Você recebe um alerta se a operação de restauração falhar por qualquer motivo. Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido e você estiver restaurando da nuvem, problemas de conectividade podem estar causando o problema. 

Para concluir a operação de restauração, coloque o volume offline no host e repita a operação de restauração. Quaisquer modificações nos dados do volume executadas durante o processo de restauração são perdidas.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Gerenciar volumes do StorSimple](storsimple-manage-volumes-u2.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

