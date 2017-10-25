---
title: "Instalar a Atualização 0.6 em um StorSimple Virtual Array | Microsoft Docs"
description: "Descreve como usar a IU da Web do StorSimple Virtual Array para aplicar atualizações usando o portal do Azure e o método de hotfix"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalar a Atualização 0.6 em seu StorSimple Virtual Array

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 0.6 no seu StorSimple Virtual Array usando a interface do usuário da Web local e por meio do Portal do Azure. É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado.

Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos. Depois que os volumes ou compartilhamentos estiverem offline, você também deverá fazer um backup manual do dispositivo.

> [!IMPORTANT]
> - A Atualização 0.6 corresponde à versão do software **10.0.10293.0** em seu dispositivo. Para saber mais sobre as novidades nessa atualização, veja as [Notas de versão da Atualização 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Caso esteja executando a Atualização 0.2 ou posterior, recomendamos que você instale as atualizações por meio do portal do Azure. Se estiver executando a Atualização 0.1 ou versões de software GA, você deverá usar o método de hotfix por meio da interface do usuário da Web local para instalar a Atualização 0.6.
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Caso esteja executando a Atualização 0.2 ou posterior, é recomendável que você instale atualizações por meio do portal do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Esse procedimento leva cerca de 7 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Após a instalação ser concluída, vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que você acabou de atualizar. Vá para **Configurações > Gerenciar > Atualizações de Dispositivo**. A versão de software exibida deve ser **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se você estiver usando o Catálogo do Microsoft Update pela primeira vez neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Insira **4023268** para a Atualização 0.6 e clique em **Pesquisar**.
   
    A listagem de hotfixes aparece, por exemplo, como **StorSimple Virtual Array Atualização 0.6**.
   
    ![Pesquisar o catálogo](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Clique em **Download**.

5. Você deve ver cinco arquivos para download. Baixe cada um desses arquivos para uma pasta. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

6. Abra a pasta na qual os arquivos estão localizados.
    ![Arquivos no pacote](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Você verá:
    -  Um arquivo do Pacote Autônomo do Microsoft Update `WindowsTH-KB3011067-x64`. Esse arquivo é usado para atualizar o software do dispositivo.
    - Um arquivo do Pacote do Agente de Monitoramento Geneva `GenevaMonitoringAgentPackageInstaller`. Esse arquivo é usado para atualizar o agente de MDS (Monitoramento e Diagnóstico). Clique duas vezes no arquivo cab. Um _.msi_ é exibido. Selecione o arquivo, clique com botão direito do mouse e, em seguida, utilize a opção **Extrair** para realizar a extração do arquivo. Você usará o arquivo _.msi_ para atualizar o agente.

        ![Extraia o arquivo de Atualização do Agente de MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Você não precisará atualizar o agente do MDS, se estiver executando a Atualização do StorSimple 0.5 (0.0.10293.0).

    - Três arquivos que contêm atualizações críticas de segurança do Windows, `windows8.1-kb4012213-x64`, `windows8.1-kb3205400-x64` e `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação da atualização ou hotfix, certifique-se de que você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.

Use esse método para instalar atualizações em um dispositivo que executa as versões de software GA ou a Atualização 0.1. Este procedimento leva cerca de 12 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. Anote a versão do software que você está executando. Se você estiver executando **10.0.10290.0**, não será necessário atualizar o agente MDS na etapa 6.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Será exibido um aviso. Como a matriz virtual é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para confirmar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.0.0.0.10293** para a Atualização 0.6.
   
   > [!NOTE]
   > Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal do Azure. Por exemplo, a interface do usuário da Web local informa **10.0.0.0.0.10293** e o Portal do Azure informa **10.0.10293.0** para a mesma versão.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Ignore esta etapa se você executou a Atualização 0.5 da Matriz Virtual do StorSimple (**10.0.10290.0**) antes de aplicar essa atualização. Você fez uma anotação da versão do software na etapa 1 antes de começar a atualizar. Se você executou a Atualização 0.5, seu agente do MDS já está atualizado.

    Se você estiver executando uma versão do software antes da Atualização 0.5, a próxima etapa será a atualização do agente do MDS. Na página **Atualização de Software**, vá até o **Caminho do arquivo de atualização** e navegue até o arquivo `GenevaMonitoringAgentPackageInstaller.msi`. Repita as etapas de 2 a 4. Depois que a matriz virtual for reiniciada, entre na interface do usuário da Web local.

7. Repita a etapa 2 a 4 para instalar as correções de segurança do Windows usando arquivos os `windows8.1-kb4012213-x64`, `windows8.1-kb3205400-x64` e `windows8.1-kb4019213-x64`. A matriz virtual reinicia após cada instalação e você precisa entrar na interface do usuário da Web local.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

