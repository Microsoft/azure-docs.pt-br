---
title: Restaurar um volume de um backup em um StorSimple série 8000 | Microsoft Docs
description: Explica como usar a página Catálogo de Backup do serviço Gerenciador de Dispositivos do StorSimple para restaurar um volume do StorSimple de um conjunto de backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723320"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume do StorSimple de um conjunto de backup

## <a name="overview"></a>Visão geral

Este tutorial descreve a operação de restauração executada em um dispositivo StorSimple série 8000 usando um conjunto de backup existente. Use a folha **Catálogo de backup** para restaurar um volume de um backup local ou na nuvem. A folha **Catálogo de Backup** exibe todos os conjuntos de backup criados quando são realizados backups manuais ou automatizados. A operação de restauração de um conjunto de backup coloca o volume online imediatamente enquanto dados são baixados em segundo plano.

Um método alternativo para iniciar a restauração é ir para **Dispositivos > [Seu dispositivo] > Volumes**. Na folha **Volumes**, selecione um volume, clique com o botão direito do mouse para invocar o menu de contexto e, em seguida, selecione **Restaurar**.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar uma restauração, examine as seguintes advertências:

* **É necessário colocar o volume offline** – Coloque o volume offline no host e no dispositivo antes de iniciar a operação de restauração. Embora a operação de restauração coloque automaticamente o volume online no dispositivo, você deve colocar o dispositivo online manualmente no host. Você pode colocar o volume online no host, assim que o volume estiver online no dispositivo. (Não é necessário aguardar até que a operação de restauração seja concluída.) Para obter os procedimentos, acesse [Colocar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo de volume após a restauração** – Os volumes excluídos são restaurados com base no tipo do instantâneo; ou seja, volumes que foram fixados localmente são restaurados como volumes fixados localmente e volumes que estavam organizados em camadas são restaurados como volumes em camadas.

    Para os volumes existentes, o tipo de uso atual do volume substitui o tipo que é armazenado no instantâneo. Por exemplo, se você restaurar um volume de um instantâneo tirado quando o tipo de volume estava em camadas e esse tipo de volume agora está fixado localmente (devido a uma operação de conversão realizada), o volume será restaurado como um volume fixado localmente. Da mesma forma, se um volume fixado localmente existente foi expandido e, mais tarde, restaurado de um instantâneo mais antigo tirado quando o volume era menor, o volume restaurado manterá o tamanho expandido atual.

    Não é possível converter um volume de um volume em camadas para um volume fixado localmente ou de um volume fixado localmente para um volume em camadas enquanto o volume está sendo restaurado. Aguarde até que a operação de restauração seja concluída para poder converter o volume em outro tipo. Para obter informações sobre como converter um volume, vá para [Alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **O tamanho do volume é refletido no volume restaurado** – Essa é uma consideração importante se estiver restaurando um volume fixado localmente que foi excluído (já que os volumes localmente fixados são totalmente provisionados). Certifique-se de que você tem espaço suficiente antes de tentar restaurar um volume fixado localmente que foi excluído antes.

* **Não é possível expandir um volume enquanto ele está sendo restaurado** – Aguarde até que a operação de restauração seja concluída antes de tentar expandir o volume. Para obter informações sobre como expandir um volume, vá para [Modificar um volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Será possível executar um backup enquanto restaurar um volume local** – Para ver os procedimentos, acesse [Usar o serviço Gerenciador de Dispositivos do StorSimple para gerenciar políticas de backup](storsimple-8000-manage-backup-policies-u2.md).

* **Você pode cancelar uma operação de restauração** – Se você cancelar o trabalho de restauração, o volume será revertido para seu estado anterior à operação de restauração. Para obter os procedimentos, vá para [Cancelar um trabalho](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Como restaurar o trabalho

Para dispositivos que estão executando a Atualização 4 ou posterior, uma restauração baseada em mapa de calor é implementada. Quando as solicitações do host para acessar os dados chegam no dispositivo, essas solicitações são rastreadas e um mapa de calor é criado. Uma taxa de solicitação alta resulta em partes de dados com mais calor, enquanto uma taxa de solicitação menor é convertida em partes com menos calor. Você deve acessar os dados pelo menos duas vezes para ser marcado como _quente_. Um arquivo que é modificado também é marcado como _quente_. Depois de iniciar a restauração, ocorre uma hidratação proativa de dados com base no mapa de calor. Para versões anteriores à Atualização 4, os dados são baixados durante a restauração com base somente no acesso.

As advertências a seguir são aplicáveis a restaurações baseadas em mapa de calor:

* O acompanhamento de mapa de calor é habilitado somente para volumes em camadas e não há suporte para volumes fixados localmente.

* Não há suporte para a restauração baseada em mapa de calor ao clonar um volume para outro dispositivo. 

* Se houver uma restauração local, e existir um instantâneo local para o volume a ser restaurado no dispositivo, não reidrataremos (pois os dados já estão disponíveis localmente). 

* Por padrão, quando você restaura, os trabalhos de reidratação começam e reidratam proativamente os dados com base no mapa de calor. 

Na Atualização 4, os cmdlets do Windows PowerShell podem ser usados para consultar trabalhos reidratação em execução, cancelar um trabalho de reidratação e obter o status do trabalho de reidratação.

* `Get-HcsRehydrationJob` - Este cmdlet obtém o status do trabalho de reidratação. Um único trabalho de reidratação é disparado para um volume.

* `Set-HcsRehydrationJob` - Este cmdlet permite que você pause, interrompa, continue o trabalho de reidratação, quando a reidratação estiver em andamento.

Para saber mais sobre como usar esse cmdlet, acesse [Referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Com a reidratação automática, espera-se um desempenho de leitura transitório normalmente mais alto. A magnitude real dos aprimoramentos depende de vários fatores, como o padrão de acesso, variação de dados e tipo de dados. 

Para cancelar um trabalho de reidratação, use o cmdlet do PowerShell. Se você desejar desabilitar permanentemente os trabalhos de reidratação para todas as restaurações futuras, contate o [Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Como usar o catálogo de backup

A folha **Catálogo de Backup** oferece uma consulta que ajuda a restringir sua seleção de conjunto de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

* **Intervalo de tempo** – O intervalo de datas e horas em que o conjunto de backup foi criado.
* **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.
* **Política de backup** ou **Volume** – A política de backup ou volume associado a este conjunto de backup.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.
* **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados do volume armazenadas localmente no dispositivo, enquanto um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – O intervalo de data e hora quando os backups foram criados. 
* **Volumes** – O número de volumes associados ao conjunto de backup.
* **Iniciado** – Os backups podem ser iniciados de forma automática, de acordo com uma agenda ou de forma manual por um usuário. (Você pode usar uma política de backup para agendar backups. Ou é possível usar a opção **Fazer backup** para fazer um backup interativo ou sob demanda).

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume StorSimple de um backup

É possível usar a folha **Catálogo de Backup** para restaurar o volume do StorSimple de um backup específico. No entanto, tenha em mente que a restauração de um volume reverterá o volume ao estado em que ele estava quando o backup foi feito. Todos os dados adicionados após a operação de backup serão perdidos.

> [!WARNING]
> A restauração de um backup substituirá os volumes existentes do backup. Isso pode causar a perda de todos os dados gravados depois que o backup tiver sido feito.


### <a name="to-restore-your-volume"></a>Para restaurar seu volume
1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Catálogo de Backup**.

2. Selecione um conjunto de backup desta maneira:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo adequado.
   3. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.
   4. Clique em **Aplicar** para executar esta consulta.

      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.
   
      ![Lista de conjunto de backup](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda o conjunto de backups para exibir os volumes associados. Esses volumes devem ficar offline no host e no dispositivo antes que você possa restaurá-los. Acesse os volumes na folha **Volumes** do seu dispositivo e siga as etapas em [Colocar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) para colocá-los offline.
   
   > [!IMPORTANT]
   > Verifique se você colocou os volumes offline primeiro no host antes de colocar os volumes offline no dispositivo. Se você não colocar os volumes offline no host, poderá ocorrer corrupção nos dados.
   
4. Navegue de volta para a guia **Catálogo de Backup** e selecione um conjunto de backup. Clique com botão direito do mouse e, no menu de contexto, selecione **Restaurar**.

    ![Lista de conjunto de backup](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Será solicitada a sua confirmação. Examine as informações de restauração e marque a caixa de seleção de confirmação.
   
    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Clique em **Restaurar**. Isso iniciará um trabalho de restauração que poderá ser exibido acessando a página **Trabalhos**.

   ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Após a conclusão da restauração, verifique se o conteúdo de seus volumes foi substituído pelos volumes do backup.


## <a name="if-the-restore-fails"></a>Se a restauração falhar

Você receberá um alerta se a operação de restauração falhar por qualquer motivo. Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido e você estiver restaurando da nuvem, problemas de conectividade podem estar causando o problema.

Para concluir a operação de restauração, coloque o volume offline no host e repita a operação de restauração. Observe que quaisquer modificações no volume de dados executadas durante o processo de restauração serão perdidas.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Gerenciar volumes do StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

