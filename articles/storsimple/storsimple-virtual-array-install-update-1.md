---
title: Instalar a Atualização 1.0 em um StorSimple Virtual Array | Microsoft Docs
description: Descreve como usar a IU da Web do StorSimple Virtual Array para aplicar atualizações usando o portal do Azure e o método de hotfix
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61411322"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalar a Atualização 1.0 em seu StorSimple Virtual Array

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 1.0 no seu StorSimple Virtual Array usando a interface do usuário da Web local e por meio do Portal do Azure.

É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado. Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Depois que os volumes ou compartilhamentos estiverem offline, você também deverá fazer um backup manual do dispositivo.

> [!IMPORTANT]
> - A Atualização 1.0 corresponde à versão do software **10.0.10296.0** em seu dispositivo. Para saber mais sobre as novidades nessa atualização, veja as [Notas de versão da Atualização 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade.
>
> - A Atualização 1 estará disponível no Portal do Azure somente se a matriz virtual estiver executando a Atualização 0.6. Para matrizes virtuais que executam versões de pré-Atualização 0.6, você deve primeiro instalar a Atualização 0.6 e depois instalar a Atualização 1.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Caso esteja executando a Atualização 0.2 ou posterior, é recomendável que você instale atualizações por meio do portal do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Dependendo da versão de software que a matriz virtual está executando, a aplicação da atualização por meio do Portal do Azure é diferente.

 - Se sua matriz virtual estiver executando a Atualização 0.6, o Portal do Azure instalará a Atualização 1 (10.0.10296.0) diretamente no dispositivo. Esse procedimento leva cerca de 7 minutos para ser concluído.
 - Se sua matriz virtual estiver executando uma versão anterior à Atualização 0.6, a atualização será feita em dois estágios. Primeiro, o Portal do Azure instala a atualização 0.6 (10.0.10293.0) em seu dispositivo. A matriz virtual reinicia e o portal, em seguida, instala a Atualização 1 (10.0.10296.0) em seu dispositivo. Esse procedimento leva cerca de 15 minutos para ser concluído.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Após a instalação ser concluída, vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que você acabou de atualizar. Vá para **Configurações > Gerenciar > Atualizações de Dispositivo**. A versão de software exibida deve ser **10.0.10296.0**.

![Versão do software após a atualização](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

> [!IMPORTANT] 
> **Continue com essa atualização apenas se você estiver executando a Atualização 0.6 (10.0.10293.0). Se você estiver executando uma versão anterior, [Instale a Atualização 0.6](storsimple-virtual-array-install-update-06.md) em seu dispositivo primeiro e, em seguida, aplique a Atualização 1.**

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Se sua matriz virtual estiver executando a Atualização 0.6, execute as seguintes etapas para baixar a Atualização 1 do Catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Se você estiver usando o Catálogo do Microsoft Update pela primeira vez neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Insira **4047203** para a Atualização 1.0 e clique em **Pesquisar**.
   
    A listagem de hotfixes aparece, por exemplo, como **StorSimple Virtual Array Atualização 1.0**.
   
    ![Pesquisar o catálogo](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Clique em **Download**.

5. Baixe os dois arquivos para uma pasta. Você também pode copiar a pasta para um compartilhamento de rede que é acessível do dispositivo.

6. Abra a pasta na qual os arquivos estão localizados.

    ![Arquivos no pacote](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Você verá dois arquivos:
    -  Um arquivo do Pacote Autônomo do Microsoft Update `WindowsTH-KB3011067-x64`. Esse arquivo é usado para atualizar o software do dispositivo.
    - Um arquivo que contém as atualizações cumulativas do `windows8.1-kb4034681-x64` de agosto. Para obter mais informações sobre o que está incluído neste pacote cumulativo de atualizações, consulte [Pacote cumulativo de atualizações de segurança mensal de agosto](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou hotfix, certifique-se de que:

 - Você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.
 - A matriz virtual está executando a Atualização 0.6 (10.0.10293.0). Se você estiver executando uma versão anterior à Atualização 0.6, [Instale a atualização 0.6](storsimple-virtual-array-install-update-06.md) primeiro e, em seguida, instale a Atualização 1.

Esse procedimento leva cerca de 4 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. Anote a versão do software que você está executando. **Continue com essa atualização apenas se você estiver executando a Atualização 0.6 (10.0.10293.0). Se você estiver executando uma versão anterior, [Instale a Atualização 0.6](storsimple-virtual-array-install-update-06.md) em seu dispositivo primeiro e, em seguida, aplique a Atualização 1.**
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Será exibido um aviso. Como a matriz virtual é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para confirmar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.0.0.0.10296** para a Atualização 1.0.
   
   > [!NOTE]
   > Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal do Azure. Por exemplo, a interface do usuário da Web local informa **10.0.0.0.0.10296** e o Portal do Azure informa **10.0.10296.0** para a mesma versão.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Repita as etapas 2 a 4 para instalar as correções de segurança do Windows usando arquivo `windows8.1-kb4012213-x64`. A matriz virtual reinicia após a instalação e você precisa entrar na interface do usuário da Web local.

> [!NOTE]
> Se você aplicou a Atualização 1 diretamente em um dispositivo executando uma versão anterior à Atualização 0.6, você não tem algumas atualizações. Contate o Suporte da Microsoft para obter as próximas etapas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
