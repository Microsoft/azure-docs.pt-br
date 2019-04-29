---
title: Instalar a Atualização 0.5 em uma StorSimple Virtual Array | Microsoft Docs
description: Descreve como usar a IU da Web do StorSimple Virtual Array para aplicar atualizações usando o portal do Azure e o método de hotfix
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61445235"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalar a Atualização 0.5 em sua StorSimple Virtual Array

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 0.5 na sua StorSimple Virtual Array usando a interface do usuário da Web local e por meio do Portal do Azure. É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado.

Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Depois que os volumes ou compartilhamentos estiverem offline, você também deverá fazer um backup manual do dispositivo.

> [!IMPORTANT]
> - A Atualização de 0.5 corresponde à versão do software **10.0.10290.0** em seu dispositivo. Para obter informações sobre as novidades nessa versão, consulte as [Notas de versão da Atualização 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Caso esteja executando a Atualização 0.2 ou posterior, recomendamos que você instale as atualizações por meio do portal do Azure. Se estiver executando a Atualização 0.1 ou versões de software GA, você deverá usar o método de hotfix por meio da interface do usuário da Web local para instalar a Atualização 0.5.
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Caso esteja executando a Atualização 0.2 ou posterior, é recomendável que você instale atualizações por meio do portal do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Esse procedimento leva cerca de 7 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Após a instalação ser concluída, vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que você acabou de atualizar. Vá para **Configurações > Gerenciar > Atualizações de Dispositivo**. A versão de software exibida deve ser **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Caso esta seja a primeira vez que você usa o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Insira **4021576** para a Atualização 0.5 e clique em **Pesquisar**.
   
    A listagem de hotfix aparece, por exemplo, como **StorSimple Virtual Array Atualização 0.5**.
   
    ![Pesquisar o catálogo](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Clique em **Download**. 

5. Você deverá ver dois arquivos para download, um *.msu* e um *.cab*. Baixe cada um desses arquivos para uma pasta. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

6. Abra a pasta na qual os arquivos estão localizados.
    ![Arquivos no pacote](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Você verá:
    -  Um arquivo do Pacote Autônomo do Microsoft Update `WindowsTH-KB3011067-x64`. Esse arquivo é usado para atualizar o software do dispositivo.
    - Um arquivo do Pacote do Agente de Monitoramento Geneva `GenevaMonitoringAgentPackageInstaller`. Esse arquivo é usado para atualizar o agente de MDS (Monitoramento e Diagnóstico). Clique duas vezes no arquivo cab. Um .msi é exibido. Selecione o arquivo, clique com botão direito do mouse e, em seguida, utilize a opção **Extrair** para realizar a extração do arquivo. Você usará o arquivo _.msi_ para atualizar o agente.

        ![Extraia o arquivo de Atualização do Agente de MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação da atualização ou hotfix, certifique-se de que você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.

Use esse método para instalar atualizações em um dispositivo que executa as versões de software GA ou a Atualização 0.1. Esse procedimento leva menos de 2 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Será exibido um aviso. Dado que esse é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para confirmar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.0.0.0.10290.0** para a Atualização 0.5.
   
   > [!NOTE]
   > Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal do Azure. Por exemplo, a interface do usuário da Web local informa **10.0.0.0.0.10290** e o portal do Azure informa **10.0.10290.0** para a mesma versão.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. A próxima etapa é atualizar o agente de MDS. Na página **Atualização de Software**, vá até o **Caminho do arquivo de atualização** e navegue até o arquivo `GenevaMonitoringAgentPackageInstaller.msi`. Repita as etapas de 2 a 4. Depois que a matriz virtual for reiniciada, entre na interface do usuário da Web local.

A atualização está concluída.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

