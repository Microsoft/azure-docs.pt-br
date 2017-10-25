---
title: Clonar o volume StorSimple | Microsoft Docs
description: "Descreve os tipos diferentes de clone e quando usá-los, e explica como você pode usar um conjunto de backups para clonar um volume individual."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 8f1936fac543f559a44ad0f9c35b30d1a92dce68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Usar o serviço StorSimple Manager para clonar um volume
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Visão geral
A página **Catálogo de Backup** do serviço StorSimple Manager exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

![Página Catálogo de backup](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Este tutorial descreve como você pode usar um conjunto de backups para clonar um volume individual. Ele também explica a diferença entre os clones *transitório* e *permanente*. 

## <a name="create-a-clone-of-a-volume"></a>Criar clone de um volume
Você pode criar um clone no mesmo dispositivo, em outro dispositivo ou mesmo em uma máquina virtual usando um instantâneo local ou na nuvem.

#### <a name="to-clone-a-volume"></a>Para clonar um volume
1. Na página do serviço Gerenciador StorSimple, clique no **Catálogo de backup** e selecione um conjunto de backups.
2. Expanda o conjunto de backups para exibir os volumes associados. Clique e selecione um volume no conjunto de backups.
   
     ![Clonar um volume](./media/storsimple-clone-volume/HCS_Clone.png) 
3. Clique em **Clonar** para começar a clonar o volume selecionado.
4. No assistente Clonar Volume, em **Especificar nome e local**:
   
   1. Identificar um dispositivo de destino. Esse é o local onde o clone será criado. Você pode escolher o mesmo dispositivo ou especificar outro dispositivo. Se você escolher um volume associado a outros provedores de serviço de nuvem (não do Azure), a lista suspensa do dispositivo de destino mostrará apenas os dispositivos físicos. Não é possível clonar um volume associado a outros provedores de serviço de nuvem em um dispositivo virtual.
      
      > [!NOTE]
      > Verifique se a capacidade necessária para o clone é menor que a capacidade disponível no dispositivo de destino.
      > 
      > 
   2. Especifique um nome de volume exclusivo para o clone. O nome deve conter entre 3 e 127 caracteres.
   3. Clique no ícone de seta  ![ícone-de-seta](./media/storsimple-clone-volume/HCS_ArrowIcon.png) para continuar para a próxima página.
5. Em **Especificar os hosts que podem usar este volume**:
   
   1. Especificar um registro de controle de acesso (ACR) para o clone. Você pode adicionar um novo ACR ou escolher na lista existente.
   2. Clique no ícone de verificação  ![check-icon](./media/storsimple-clone-volume/HCS_CheckIcon.png)para concluir a operação.
6. Um trabalho de clone será iniciado e você será notificado quando o clone for criado com êxito. Clique em **Exibir Trabalho** para monitorar o trabalho de clone na página **Trabalhos**.
7. Depois do trabalho de clone ser concluído:
   
   1. Vá para a página **Dispositivos** e selecione a guia **Contêineres do Volume**. 
   2. Selecione o contêiner do volume associado ao volume de origem clonado. Na lista de volumes, você deve ver o clone que acabou de criar.

> [!NOTE]
> O monitoramento e o backup padrão são desabilitados automaticamente em um volume clonado.
> 
> 

Um clone criado dessa maneira é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [Clones transitórios versus permanentes](#transient-vs-permanent-clones).

O clone é agora um volume normal e qualquer operação que é possível em um volume estará disponível para o clone. Você precisará configurar este volume para qualquer backup.

## <a name="transient-vs-permanent-clones"></a>Clones transitórios versus permanentes
Os clones transitórios e permanentes são criados apenas quando a clonagem está sendo realizada para um dispositivo diferente. Você pode clonar um volume específico por meio de um conjunto de backups para um dispositivo diferente. Um clone criado dessa maneira é um clone *transitório* . O clone transitório terá referências para o volume original e usará esse volume para ler durante a gravação local. 

Depois de fazer um instantâneo de nuvem de um clone transitório, o clone resultante será um clone *permanente* . O clone permanente é independente e não tem nenhuma referência para o volume original do qual foi clonado.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para os clones transitórios e permanentes
As seções a seguir descrevem as situações de exemplo nas quais os clones transitórios e permanentes podem ser usados.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Você precisa recuperar um arquivo de apresentação do Microsoft PowerPoint com um ano. O administrador de TI identifica o backup específico desse intervalo de tempo, em seguida, filtra o volume. Então, o administrador clona o volume, localiza o arquivo que você está procurando e fornece a você. Nesse cenário, é usado um clone transitório. 

![Vídeo disponível](./media/storsimple-clone-volume/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar os recursos de clonagem e restauração no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testando no ambiente de produção com um clone permanente
Você precisa verificar um bug de teste no ambiente de produção. Você cria um clone do volume no ambiente de produção fazendo um instantâneo da nuvem desse clone. O volume clonado agora é independente. Nesse cenário, é usado um clone permanente.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de backups](storsimple-restore-from-backup-set.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

