---
title: Instalar a atualização 1.1 no array virtual StorSimple | Microsoft Docs
description: Descreve como aplicar atualizações usando o portal do Azure e a interface da Web local para o StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408530"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Instalar a atualização 1.1 na sua matriz virtual StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 1.1 em seu StorSimple Virtual Array por meio da interface do usuário da Web local e por meio do portal do Azure.

É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado. Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Depois que os volumes ou compartilhamentos estiverem offline, você também deverá fazer um backup manual do dispositivo.

> [!IMPORTANT]
> - A atualização 1.1 corresponde a **10.0.10307.0** versão do software no seu dispositivo. Para obter informações sobre o que há de novo nesta atualização, acesse [Notas de versão da Atualização 1.1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade.
>
> - A atualização 1.1 está disponível no portal do Azure somente se a matriz virtual estiver executando a Atualização 1. Para matrizes virtuais que executam as versões da Atualização 0.6, você deve instalar a Atualização 1.0 primeiro e depois aplicar a Atualização 1.1.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Caso esteja executando a Atualização 0.2 ou posterior, é recomendável que você instale atualizações por meio do portal do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Dependendo da versão de software que a matriz virtual está executando, a aplicação da atualização por meio do Portal do Azure é diferente.

 - Se o seu array virtual estiver executando a Atualização 1, o portal do Azure instalará diretamente a Atualização 1.1 (10.0.10307.0) no seu dispositivo. Este procedimento leva cerca de 10 minutos para ser concluído.
 - Se o sua matriz virtual estiver executando o Update 0.6, a atualização será feita em dois estágios. O portal do Azure primeiro instala a Atualização 1.0 (10.0.10296.0) no seu dispositivo. A matriz virtual é reinicializado e o portal instala a Atualização 1.1 (10.0.10307.0) no seu dispositivo. Esse procedimento leva cerca de 15 minutos para ser concluído.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

Após a instalação ser concluída, vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que você acabou de atualizar. Vá para **Configurações > Gerenciar > Atualizações de Dispositivo**. A versão do software exibida deve ser **10.0.10307.0**.

![Versão do software após a atualização](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

> [!IMPORTANT] 
> **Continue com esta atualização somente se você estiver executando a Atualização 1 (10.0.10296.0). Se você estiver executando a Atualização 0.6, [ instale a Atualização 1 ](storsimple-virtual-array-install-update-1.md) em seu dispositivo primeiro e depois aplique a Atualização 1.1.**

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização 1.1 do catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Se você estiver usando o Catálogo do Microsoft Update pela primeira vez neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Digite **4337628** para a Atualização 1.1 e clique em **Pesquisar**.
   
    A listagem de hotfix aparece, por exemplo, **StorSimple Virtual Array Update 1.1**.
   
    ![Pesquisar o catálogo](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Clique em **Download**.

5. Baixe os dois arquivos para uma pasta. Você também pode copiar a pasta para um compartilhamento de rede que é acessível do dispositivo.

6. Abra a pasta na qual os arquivos estão localizados.

    ![Arquivos no pacote](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Você verá dois arquivos:
    -  Um arquivo do Pacote Autônomo do Microsoft Update `WindowsTH-KB3011067-x64`. Esse arquivo é usado para atualizar o software do dispositivo.
    - Um arquivo que contém as atualizações cumulativas para junho `Windows8.1-KB4284815-x64`. Para obter mais informações sobre o que está incluído neste pacote cumulativo de atualizações, acesse [rollup de segurança mensal de junho](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou hotfix, certifique-se de que:

 - Você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.
 - Sua matriz virtual está executando a Atualização 1 (10.0.10296.0). Se você estiver executando a Atualização 0.6, [instale a Atualização 1](storsimple-virtual-array-install-update-1.md) primeiro e instale a Atualização 1.1.

Esse procedimento leva cerca de 4 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. Anote a versão do software que você está executando. **Continue com esta atualização somente se você estiver executando a Atualização 1 (10.0.10296.0). Se você estiver executando a Atualização 0.6, [ instale a Atualização 1 ](storsimple-virtual-array-install-update-1.md) em seu dispositivo primeiro e depois aplique a Atualização 1.1.**
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. Será exibido um aviso. Como a matriz virtual é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para confirmar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.0.0.0.10307** para a Atualização 1.1.
   
   > [!NOTE]
   > Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal do Azure. Por exemplo, a interface do usuário da Web local relata **10.0.0.0.0.10307** e o portal do Azure relata **10.0.10307.0** para a mesma versão.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Repita as etapas 2 a 4 para instalar as correções de segurança do Windows usando arquivo `Windows8.1-KB4284815-x64`. A matriz virtual reinicia após a instalação e você precisa entrar na interface do usuário da Web local.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
