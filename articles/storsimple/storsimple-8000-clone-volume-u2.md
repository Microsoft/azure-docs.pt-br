---
title: Clonar um volume no StorSimple da série 8000| Microsoft Docs
description: Descreve os diferentes tipos de clone e seus usos, explicando como usar um conjunto de backup para clonar um volume individual em um dispositivo StorSimple da série 8000.
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
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637826"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple no Portal do Azure para clonar um volume

## <a name="overview"></a>Visão geral

Este tutorial descreve como usar um conjunto de backup para clonar um volume individual pela folha **Catálogo de backup**. Ele também explica a diferença entre os clones *transitório* e *permanente*. As diretrizes neste tutorial se aplicam a todos os dispositivos StorSimple série 8000 que executam a Atualização 3 ou posterior.

A folha **Catálogo de backup** do serviço do Gerenciador de Dispositivos do StorSimple exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Você pode selecionar um volume em um conjunto de backup para ser clonado.

 ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerações para clonar um volume

Considere as seguintes informações ao clonar um volume.

- Um clone se comporta da mesma maneira que um volume regular. Qualquer operação que é possível em um volume está disponível para o clone.

- O monitoramento e o backup padrão são desabilitados automaticamente em um volume clonado. Será necessário configurar um volume clonado para qualquer backup.

- Um volume afixado localmente será clonado como um volume em camadas. Se você precisar que o volume clonado seja fixado localmente, você pode converter o clone para um volume fixo local depois que a operação de clonagem for concluída com êxito. Para obter informações sobre como converter um volume em camadas para um volume fixado localmente, vá para [Alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Se você tentar converter um volume clonado do tipo em camadas para fixado localmente imediatamente após a clonagem (quando ele ainda é um clone transitório), a conversão falhará com a seguinte mensagem de erro:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Esse erro será recebido apenas se você estiver realizando a clonagem para um dispositivo diferente. Você pode converter com êxito o volume a ser fixado localmente se você converter o clone transitório em um clone permanente. Crie um instantâneo de nuvem do clone transitório para convertê-lo em um clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Criar clone de um volume

Você pode criar um clone no mesmo dispositivo, em outro dispositivo ou mesmo em um dispositivo de nuvem usando um instantâneo local ou na nuvem.

O procedimento a seguir descreve como criar um clone com base no catálogo de backup.  Um método alternativo para iniciar a clonagem é acessar **Volumes**, selecionar um volume e clicar com o botão direito para invocar o menu de contexto e selecionar **Clonar**.

Siga as seguintes etapas para criar um clone do seu volume com base no catálogo de backup.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Catálogo de Backup**.

2. Selecione um conjunto de backup desta maneira:
   
   1. Selecione o dispositivo adequado.
   2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique em **Aplicar** para executar esta consulta.

      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.
   
      ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expandir o conjunto de backup para exibir os volumes associados e selecionar um volume em um conjunto de backup. Clique com botão direito do mouse e, no menu de contexto, selecione **Clonar**.

   ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Na folha **Clonar**, execute as seguintes etapas:
   
   1. Identificar um dispositivo de destino. Esse é o local onde o clone será criado. Você pode escolher o mesmo dispositivo ou especificar outro dispositivo.

      > [!NOTE]
      > Verifique se a capacidade necessária para o clone é menor que a capacidade disponível no dispositivo de destino.
       
   2. Especifique um nome de volume exclusivo para o clone. O nome deve conter entre 3 e 127 caracteres.
      
       > [!NOTE]
       > O campo **Clonar Volume Como** será colocado **Em Camadas** mesmo que você esteja clonando um volume localmente afixado. Não é possível alterar esta configuração, no entanto, caso precise que o volume clonado também seja fixado localmente, você pode converter o clone para um volume fixado localmente após a criação com êxito do clone. Para obter informações sobre como converter um volume em camadas para um volume fixado localmente, vá para [Alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Em **Hosts conectados**, especifique um ACR (registro de controle de acesso) para o clone. Você pode adicionar um novo ACR ou escolher na lista existente. O ACR determinará quais hosts podem acessar esse clone.
      
       ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Clique em **Clonar** para concluir a operação.

4. Um trabalho de clonagem será iniciado e você será notificado quando o clone for criado com êxito. Clique na notificação do trabalho ou vá para a folha **Trabalhos** para monitorar o trabalho de clonagem.

    ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Depois que o trabalho de clonagem for concluído, acesse seu dispositivo e clique em **Volumes**. Na lista de volumes, você verá o clone que acabou de ser criado no mesmo contêiner de volume que o volume de origem.

    ![Lista de conjunto de backup](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Um clone criado dessa maneira é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [Clones transitórios versus permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Clones transitórios versus permanentes
Clones transitórios são criados apenas ao clonar para outro dispositivo. Você pode clonar um volume específico com base em um conjunto de backup para um dispositivo gerenciado diferente pelo Gerenciador de Dispositivos do StorSimple. O clone transitório terá referências aos dados no volume original e usará esses dados para ler e gravar localmente no dispositivo de destino.

Depois de criar um instantâneo de nuvem de um clone transitório, o clone resultante será um clone *permanente*. Durante esse processo, é criada uma cópia dos dados na nuvem e o tempo para copiar esses dados é determinado pelo tamanho dos dados e das latências do Azure (essa é uma cópia do Azure para o Azure). Esse processo pode levar de dias a semanas. O clone transitório se tornará um clone permanente e não fará nenhuma referência aos dados do volume original do qual ele foi clonado.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para os clones transitórios e permanentes
As seções a seguir descrevem as situações de exemplo nas quais os clones transitórios e permanentes podem ser usados.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Você precisa recuperar um arquivo de apresentação do Microsoft PowerPoint com um ano. O administrador de TI identificará o backup específico a partir desse momento e filtrará o volume. Então, o administrador clona o volume, localiza o arquivo que você está procurando e fornece a você. Nesse cenário, é usado um clone transitório.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testando no ambiente de produção com um clone permanente
Você precisa verificar um bug de teste no ambiente de produção. Você cria um clone do volume no ambiente de produção e, em seguida, faz um instantâneo de nuvem desse clone para criar um volume clonado independente. Nesse cenário, é usado um clone permanente.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de backups](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

