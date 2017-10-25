---
title: "Configurar o MPIO no host conectado à StorSimple Virtual Array | Microsoft Docs"
description: Descreve como configurar o MPIO (Multipath I/O) para sua matriz virtual StorSimple conectada a um host que esteja executando o Windows Server 2012 R2.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: alkohli
ms.openlocfilehash: c75c6ed40754aee964e2b68f4f569dc1422507f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurar o Multipath I/O no host do Windows Server para a Matriz Virtual StorSimple
## <a name="overview"></a>Visão geral
Este artigo descreve como instalar o recurso MPIO (Multipath I/O) no host do Windows Server, aplicar definições de configuração específica apenas para volumes StorSimple e verificar o MPIO para volumes StorSimple. O procedimento presume que sua Matriz Virtual 1200 StorSimple com duas interfaces de rede esteja conectada a um host do Windows Server com duas interfaces de rede. As informações contidas neste artigo aplicam-se apenas à matriz virtual. Para saber mais sobre dispositivos StorSimple 8000 series, acesse [Configurar o MPIO para host do StorSimple](storsimple-configure-mpio-windows-server.md). 

O recurso MPIO no Windows Server ajuda a criar configurações de armazenamento altamente disponíveis e tolerantes a falhas. O MPIO usa componentes redundantes do caminho físico – adaptadores, cabos e comutadores – para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, fazendo com que um caminho lógico falha, a lógica de vários caminhos usará um caminho alternativo de E/S para que os aplicativos ainda possam acessar seus dados. Além disso, dependendo da configuração, o MPIO também pode melhorar o desempenho balanceando novamente a carga em todos esses caminhos. Para obter mais informações, consulte [Visão geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "Visão geral do MPIO and features").

Para a alta disponibilidade de sua solução StorSimple, configure o MPIO nos hosts do Windows Server conectados à Matriz Virtual StorSimple (modelo 1200). Assim, os servidores de host podem tolerar uma falha de link, rede ou interface. 

Você precisará seguir estas etapas para configurar o MPIO: 

* Pré-requisitos de configuração
* Etapa 1: instalar o MPIO no host do Windows Server
* Etapa 2: configurar o MPIO para os volumes StorSimple
* Etapa 3: montar os volumes StorSimple no host

Cada uma das etapas acima é analisada nas seções a seguir.

## <a name="prerequisites"></a>Pré-requisitos
Esta seção detalha os pré-requisitos de configuração para o host do Windows Server e sua matriz virtual.

### <a name="on-windows-server-host"></a>No host do Windows Server
* Verifique se o servidor do Windows Server tem duas interfaces de rede habilitadas.

### <a name="on-storsimple-virtual-array"></a>Na matriz virtual do StorSimple
* A matriz virtual deve estar configurada como um servidor iSCSI. Para saber mais, confira o artigo sobre como [configurar a matriz virtual como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md). Uma ou mais interfaces de rede devem estar habilitadas na matriz.   
* As interfaces de rede na matriz virtual devem poder ser acessadas pelo host do Windows Server.
* Um ou mais volumes devem ser criados na Matriz Virtual StorSimple. Para saber mais, confira [Adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) na Matriz Virtual StorSimple. Neste procedimento, criamos 3 volumes (1 localmente fixo e 2 volumes em camadas, como mostrado abaixo) na matriz virtual.
  
    ![mpio0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuração do hardware para Matriz Virtual do StorSimple
A figura a seguir mostra a configuração de hardware para alta disponibilidade e múltiplos caminhos do balanceamento de carga para o host do Windows Server e a Matriz Virtual StorSimple usados neste procedimento.

![configuração de hardware do mpio](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

Como mostrado na figura anterior:

* A Matriz Virtual StorSimple provisionada no Hyper-V é um dispositivo ativo de único nó configurado como um servidor iSCSI.
* Duas interfaces de rede virtual são habilitadas na sua matriz. Na interface de usuário da Web local da sua matriz virtual, verifique se duas interfaces de rede estão habilitadas navegando até **Configurações de rede**, como mostrado abaixo:
  
    ![Interfaces de rede habilitadas na 1200](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    Observe os endereços IPv4 das interfaces de rede habilitadas (Ethernet, Ethernet 2 por padrão) e salve para uso posterior no host.
* Duas interfaces de rede são habilitadas no host do Windows Server. Se as interfaces conectadas para host e matriz estiverem na mesma sub-rede, haverá quatro caminhos disponíveis. Esse era o caso neste procedimento. No entanto, se cada interface de rede na interface da matriz e do host estiver em uma sub-rede IP diferente (e não roteável), então somente 2 caminhos estarão disponíveis.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Etapa 1: instalar o MPIO no host do Windows Server
O MPIO é um recurso opcional no Windows Server e não é instalado por padrão. Ele deve ser instalado como um recurso por meio do Gerenciador de Servidores. Para instalar esse recurso no host do Windows Server, conclua o procedimento a seguir.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Etapa 2: configurar o MPIO para os volumes StorSimple
O MPIO precisa ser configurado para identificar os volumes StorSimple. Para configurar o MPIO para reconhecer os volumes StorSimple, execute as etapas a seguir.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Etapa 3: montar os volumes StorSimple no host
Depois de configurar o MPIO no Windows Server, os volumes criados na matriz StorSimple poderão ser montados, em seguida, poderão aproveitar o MPIO para redundância. Execute as seguintes etapas para montar um volume.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes no host
1. Abra a janela **Propriedades do Iniciador iSCSI** no host do Windows Server. Vá até **Gerenciador do Servidor > Painel > Ferramentas > Iniciador iSCSI**.
2. Na caixa de diálogo **Propriedades do Iniciador iSCSI**, clique em **Descoberta**, em seguida, clique em **Descobrir Portal de Destino**.
3. Na caixa de diálogo **Descobrir Portal de Destino** , faça o seguinte:
   
    1. Insira o endereço IP da primeira interface de rede habilitada na Matriz Virtual StorSimple. Por padrão, seria **Ethernet**. 
    2. Clique em **OK** para voltar para a caixa de diálogo **Propriedades do Iniciador iSCSI**.
     
    > [!IMPORTANT]
    > Se você estiver usando uma rede privada para as conexões iSCSI, digite o endereço IP da porta DADOS que está conectada à rede privada.
     
4. Repita as etapas 2 e 3 para uma segunda interface de rede (por exemplo, Ethernet 2) na sua matriz. 
5. Selecione a guia **Destinos** na caixa de diálogo **Propriedades do Iniciador iSCSI**. Para sua matriz virtual, você deve ver a superfície de cada volume como um destino em **Destinos Descobertos**. Nesse caso, três destinos (correspondente ao três volumes) devem ser descobertos.
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. Clique em **Conectar** para estabelecer uma sessão iSCSI com a Matriz Virtual StorSimple. Uma caixa de diálogo **Conectar ao Destino** será exibida. Marque a caixa de seleção **Habilitar vários caminhos** . Clique em **Avançado**.
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. Na caixa de diálogo **Configurações Avançadas** , faça o seguinte:                                        
   
    1. Na lista suspensa **Adaptador local**, selecione o **Iniciador iSCSI da Microsoft**.
    2. Na lista suspensa **IP do Iniciador** , selecione o endereço IP do host.
    3. Na lista suspensa do IP **Portal de Destino** , selecione o IP da interface da matriz.
    4. Clique em **OK** para voltar para a caixa de diálogo **Propriedades do Iniciador iSCSI**.
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. Clique em **Propriedades**. 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. Na caixa de diálogo **Propriedades**, clique em **Adicionar Sessão**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. Na caixa de diálogo **Conectar ao Destino**, marque a caixa de seleção **Habilitar vários caminhos**. Clique em **Avançado**.
11. Na caixa de diálogo **Configurações Avançadas** :                                        
    
    1. Na lista suspensa **Adaptador local** , selecione o Iniciador do Microsoft iSCSI.

    2. Na lista suspensa **IP do Iniciador** , selecione o endereço IP correspondente do host. Nesse caso, você está conectando duas interfaces de rede na matriz a uma única interface de rede no host. Portanto, essa interface é igual à fornecida para a primeira sessão.

    3. Na lista suspensa **IP do Portal de Destino** , escolha o endereço IP para a segunda interface de dados habilitada na matriz.

    4. Clique em **OK** para voltar para a caixa de diálogo Propriedades do Iniciador iSCSI. Você adicionou uma segunda sessão ao destino.
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. Depois de adicionar as sessões desejadas (caminhos), na caixa de diálogo **Propriedades do Iniciador iSCSI**, selecione o destino e clique em **Propriedades**. Na guia Sessões da caixa de diálogo **Propriedades** , observe os quatro identificadores da sessão que correspondem às permutações possíveis de caminho. Para cancelar uma sessão, marque a caixa de seleção ao lado de um identificador de sessão e clique em **Desconectar**.

    6. Para exibir os dispositivos apresentados nas sessões, selecione a guia **Dispositivos** . Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**.

    7. A caixa de diálogo **Detalhes** será exibida. Na guia **MPIO**, você pode selecionar as devidas configurações da **Política de Balanceamento de Carga**. Você também pode exibir o tipo de caminho **Ativo** ou **Em Espera**.
12. Repita as etapas de 8 a 11 para adicionar outras sessões (caminhos) ao destino. Com duas interfaces no host e duas na matriz virtual, você pode adicionar um total de quatro sessões para cada destino. 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. Você precisará repetir essas etapas para cada volume (superfícies como um destino).
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. Abra **Gerenciamento do Computador** navegando para **Gerenciador de Servidores > Painel > Gerenciamento do Computador**. No painel esquerdo, clique em **Armazenamento > Gerenciamento de Disco**. Os volumes criados na Matriz Virtual StorSimple visíveis para este host aparecerão sob **Gerenciamento de Disco** como novo(s) disco(s).
15. Inicialize o disco e crie um novo volume. Durante o processo de formato, escolha um AUS (tamanho de unidade de alocação) de 64 KB. Repita o processo para todos os volumes disponíveis.
    
    ![Gerenciamento de Disco](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. Em **Gerenciamento de Disco**, clique com o botão direito do mouse em **Disco** e selecione **Propriedades**.
17. Na caixa de diálogo **Propriedades do Dispositivo de Disco com Vários Caminhos**, clique na guia **MPIO**.
    
    ![Propriedades de Disco](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. Na seção **Nome DSM**, clique em **Detalhes** e verifique se os parâmetros estão definidos para os parâmetros padrão. Os parâmetros padrão são:
    
    * Período de Verificação do Caminho = 30
    * Contagem de Repetição = 3
    * Período de Remoção PDO = 20
    * Intervalo de Repetição = 1
    * Verificação do Caminho habilitada = Desmarcada.
    
    > [!NOTE]
    > **Não modifique os parâmetros padrão.**
   
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [usar o Gerenciador de Dispositivo StorSimple para administrar a Matriz Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md).

