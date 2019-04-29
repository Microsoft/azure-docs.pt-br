---
title: Instalar Atualizações em um StorSimple Virtual Array | Microsoft Docs
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: b67fcb82bdcc94d7faeceedb7420a869e6578cad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436261"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Instalar a Atualização 0.4 em seu StorSimple Virtual Array

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar a Atualização 0.4 na sua Matriz Virtual StorSimple usando a interface do usuário da Web local e por meio do portal do Azure. É necessário aplicar atualizações de software ou hotfixes para manter seu StorSimple Virtual Array atualizado. 

Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Uma vez que o StorSimple Virtual Array é um dispositivo de nó único, qualquer processo de E/S em andamento será interrompido e o dispositivo apresentará tempo de inatividade. 

Antes de aplicar uma atualização, é recomendável que você deixe os volumes ou compartilhamentos offline no host primeiro e, em seguida, no dispositivo. Isso minimiza a possibilidade de dados corrompidos.

> [!IMPORTANT]
> Se estiver executando a Atualização 0.1 ou versões de software GA, você deverá usar o método de hotfix por meio da interface do usuário da Web local para instalar a atualização 0.3. Caso esteja executando a Atualização 0.2 ou posterior, recomendamos que você instale as atualizações por meio do portal do Azure.
 

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

* Baixe a atualização ou o hotfix
* Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

2. Caso esta seja a primeira vez que você usa o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

3. Na caixa de pesquisa do catálogo do Microsoft Update, insira o número da KB (base de dados de conhecimento) do hotfix que deseja baixar. Insira **3216577** para a Atualização 0.4 e clique em **Pesquisar**.
   
    A listagem de hotfix aparece, por exemplo, como **StorSimple Virtual Array Atualização 0.4**.
   
    ![Pesquisar o catálogo](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao carrinho de compras.

5. Clique em **Exibir carrinho**.

6. Clique em **Download**. Especifique ou **Procure** a localização em que deseja que o download apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

7. Abra a pasta copiada, você verá um arquivo `WindowsTH-KB3011067-x64`do Pacote Autônomo do Microsoft Update. Esse arquivo é usado para instalar a atualização ou hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação da atualização ou hotfix, certifique-se de que você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede. 

Use esse método para instalar atualizações em um dispositivo que executa as versões de software GA ou a Atualização 0.1. Esse procedimento leva menos de 2 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update/update1m.png)

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. Clique em **Aplicar**.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update/update2m.png)

3. Será exibido um aviso. Dado que esse é um dispositivo de nó único, depois que a atualização for aplicada, o dispositivo será reiniciado e haverá tempo de inatividade. Clique no ícone de verificação.
   
   ![atualizar dispositivo](./media/storsimple-virtual-array-install-update/update3m.png)

4. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update/update5m.png)

5. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para confirmar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**. A versão de software exibida deve ser **10.0.0.0.0.10289.0** para a Atualização 0.4.
   
   > [!NOTE]
   > Relatamos as versões de software de maneira ligeiramente diferente na interface do usuário da Web local e no portal do Azure. Por exemplo, a interface do usuário da Web local informa **10.0.0.0.0.10289** e o portal do Azure informa **10.0.10289.0** para a mesma versão.
   
    ![atualizar dispositivo](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Caso esteja executando a Atualização 0.2 ou posterior, é recomendável que você instale atualizações por meio do portal do Azure. O procedimento do portal requer que o usuário verifique, baixe e instale as atualizações. Esse procedimento leva cerca de 7 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Após a instalação ser concluída (conforme indicado pelo status do trabalho em 100%), vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Selecione **Dispositivos** e selecione e clique no dispositivo que deseja atualizar a partir da lista de dispositivos conectados ao serviço. Na folha **Configurações**, vá para a seção **Gerenciar** e selecione **Atualizações de dispositivo**. A versão de software exibida deve ser **10.0.10289.0**.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

