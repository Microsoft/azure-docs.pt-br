---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7c4392aa67fd5e995e93b2e5b0c188f07f598aa4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561226"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Para montar, inicializar e formatar um volume
1. Inicie o iniciador iSCSI da Microsoft.
2. Na janela **Propriedades do Iniciador iSCSI** na guia **Descoberta**, clique em **Descobrir Portal**.
3. Na caixa de diálogo **Descobrir Portal de Destino**, forneça o endereço IP de seu adaptador de rede habilitado para iSCSI e clique em **OK**. 
4. Na janela **Propriedades do Iniciador iSCSI** na guia **Destinos**, localize os **Destinos descobertos**. O status do dispositivo deve aparecer como **Inativo**.
5. Selecione o dispositivo de destino e depois clique em **Conectar**. Após o dispositivo ter sido conectado, o status deverá mudar para **Conectado**. (Para obter mais informações sobre como usar o iniciador iSCSI da Microsoft, consulte [Instalando e Configurando o iniciador iSCSI da Microsoft][1]).
6. No host do Windows, aperte a tecla do logotipo do Windows + X e depois clique em **Executar**. 
7. Na caixa de diálogo **Executar**, digite **Diskmgmt.msc**. Clique em **OK** e a caixa de diálogo **Gerenciamento de disco** será exibida. O painel do lado direito exibirá os volumes do seu host.
8. Na janela **Gerenciamento de Disco**, os volumes montados serão exibidos conforme mostrado na ilustração. Clique com o botão direito do mouse no volume descoberto (clique no nome do disco) e, em seguida, clique em **Online**.
   
     ![Inicializar formatação de volume](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 
9. Clique com o botão direito do mouse no volume (clique no nome do disco) novamente e, em seguida, clique em **Inicializar**.
10. Para formatar um volume simples, realize as seguintes etapas:
    
    1. Selecione o volume, clique com o botão direito do mouse sobre ele (clique na área da direita) e clique em **Novo Volume Simples**.
    2. No assistente para Novo Volume Simples, especifique o tamanho do volume e a letra do drive e configure o volume como um sistema de arquivos NTFS.
    3. Especifique um tamanho de unidade de alocação com 64 KB. Esse tamanho de unidade de alocação funciona bem com os algoritmos de eliminação de duplicação usados na solução StorSimple.
    4. Realize uma formatação rápida.

![Vídeo disponível](./media/storsimple-mount-initialize-format-volume/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como montar, inicializar e formatar um volume do StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)