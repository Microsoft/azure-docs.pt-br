---
title: Configurar o MPIO para o dispositivo StorSimple | Microsoft Docs
description: Descreve como configurar o MPIO (Multipath I/O) para seu dispositivo StorSimple conectado a um host que esteja executando o Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968801"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar o MPIO (Multipath I/O) para seu dispositivo StorSimple

Este tutorial descreve as etapas que você deve seguir para instalar e usar o recurso MPIO(Multipath I/O) em um host que executa o Windows Server 2012 R2 e conectado a um dispositivo físico StorSimple. As diretrizes deste artigo se aplicam somente a dispositivos físicos StorSimple da série 8000. Atualmente, não há suporte para MPIO em um Dispositivo de Nuvem StorSimple.

A Microsoft criou o suporte para o recurso MPIO (Multipath I/O) no Windows Server para ajudar a criar configurações de rede iSCSI altamente disponíveis e tolerantes a falhas. O MPIO usa componentes redundantes do caminho físico — adaptadores, cabos e comutadores — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, fazendo com que um caminho lógico falha, a lógica de vários caminhos usará um caminho alternativo de E/S para que os aplicativos ainda possam acessar seus dados. Além disso, dependendo da sua configuração, o MPIO também pode melhorar o desempenho ao rebalancear novamente a carga em todos esses caminhos. Para obter mais informações, consulte [visão geral do MPIO](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Visão geral e recursos do MPIO").

Para a alta disponibilidade da sua solução StorSimple, o MPIO deve ser configurado em seu dispositivo StorSimple. Quando o MPIO estiver instalado em seus servidores de host executando o Windows Server 2012 R2, os servidores poderão tolerar uma falha de link, rede ou interface.

## <a name="mpio-configuration-summary"></a>Resumo da configuração de MPIO

O MPIO é um recurso opcional no Windows Server e não é instalado por padrão. Ele deve ser instalado como um recurso por meio do Gerenciador de Servidores.

Siga estas etapas para configurar o MPIO em seu dispositivo StorSimple:

* Etapa 1: instalar o MPIO no host do Windows Server
* Etapa 2: configurar o MPIO para os volumes StorSimple
* Etapa 3: montar os volumes StorSimple no host
* Etapa 4: configurar o MPIO para ter alta disponibilidade e balanceamento de carga

Cada uma das etapas acima é analisada nas seções a seguir.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Etapa 1: instalar o MPIO no host do Windows Server

Para instalar esse recurso no host do Windows Server, conclua o procedimento a seguir.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar o MPIO no host

1. Abra o Gerenciador de Servidores no host do Windows Server. Por padrão, o Gerenciador do Servidor começa quando um membro do grupo Administradores faz logon em um computador que está executando o Windows Server 2012 R2 ou o Windows Server 2012. Se o Gerenciador do Servidor ainda não estiver aberto, clique em **Iniciar > Gerenciador do Servidor**.
   
   ![Gerenciador do Servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Clique em **Gerenciador de Servidores > Painel de Controle > Adicionar funções e recursos**. O assistente **Adicionar Funções e Recursos** será aberto.
   
   ![Adicionar Assistente de Funções e Recursos 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No assistente **Adicionar Funções e Recursos**, execute as seguintes etapas:
   
   1. Na página **Antes de começar** , clique em **Avançar**.
   2. Na página **Selecionar tipo de instalação**, aceite a configuração padrão da instalação **Baseada em função ou recurso**. Clique em **Avançar**.
   
       ![Adicionar Assistente de Funções e Recursos 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na página **Selecionar servidor de destino**, escolha **Selecionar um servidor do pool de servidores**. O servidor host deve ser descoberto automaticamente. Clique em **Avançar**.
   4. Na página **Selecionar funções de servidor**, clique em **Avançar**.
   5. Na página **Selecionar recursos**, selecione **Multipath I/O** e clique em **Avançar**.
   
       ![Adicionar Assistente de Funções e Recursos 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na página **confirmar seleções de instalação** , confirme a seleção e, em seguida, selecione **reiniciar o servidor de destino automaticamente, se necessário**, conforme mostrado abaixo. Clique em **Instalar**.
   
       ![Adicionar Assistente de Funções e Recursos 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Você será notificado quando a instalação for concluída. Clique em **Fechar** para fechar o assistente.
   
       ![Adicionar Assistente de Funções e Recursos 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Etapa 2: configurar o MPIO para os volumes StorSimple

O MPIO deve ser configurado para identificar os volumes StorSimple. Para configurar o MPIO para reconhecer os volumes StorSimple, execute as etapas a seguir.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Para configurar o MPIO para os volumes StorSimple

1. Abra a **Configuração do MPIO**. Clique em **Gerenciador de Servidores > Painel de Controle > Ferramentas > MPIO**.
2. Na caixa de diálogo **Propriedades do MPIO**, selecione a guia **Descobrir vários caminhos**.
3. Selecione **Adicionar suporte para dispositivos iSCSI** e depois clique em **Adicionar**.  
   ![Propriedades do MPIO para Descobrir Vários Caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Reinicialize o servidor quando solicitado.
5. Na caixa de diálogo **Propriedades do MPIO** , clique na guia **dispositivos do MPIO** . clique em **Adicionar**.
    </br>![Propriedades do MPIO para Dispositivos do MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na caixa de diálogo **Adicionar suporte a MPIO**, em **ID de Hardware do Dispositivo**, insira o número de série do dispositivo. Para obter o número de série do dispositivo, acesse o serviço do Gerenciador de Dispositivos do StorSimple. Navegue para **Dispositivos > Painel**. O número de série do dispositivo é exibido no painel **Visão Rápida** à direita do painel do dispositivo.
    </br>
    ![Adicionar Suporte do MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicialize o servidor quando solicitado.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Etapa 3: montar os volumes StorSimple no host

Depois do MPIO ser configurado no Windows Server, o(s) volume(s) criado(s) no dispositivo StorSimple poderá(ão) ser montado(s), em seguida, poderá(ão) aproveitar o MPIO para a redundância. Execute as seguintes etapas para montar um volume.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes no host

1. Abra a janela **Propriedades do Iniciador iSCSI** no host do Windows Server. Clique em **Gerenciador do Servidor > Painel > Ferramentas > Iniciador iSCSI**.
2. Na caixa de diálogo **Propriedades do iniciador iSCSI**, clique na guia Descoberta, em seguida, clique em **Descobrir Portal de Destino**.
3. Na caixa de diálogo **Descobrir Portal de Destino**, execute as seguintes etapas:
   
   1. Digite o endereço IP da porta DADOS do seu dispositivo StorSimple (por exemplo, digite DADOS 0).
   2. Clique em **OK** para voltar à caixa de diálogo **Propriedades do iniciador iSCSI**.
     
      > [!IMPORTANT]
      > **Se você estiver usando uma rede privada para as conexões iSCSI, digite o endereço IP da porta DADOS que está conectada à rede privada.**
    
4. Repita as etapas de 2 a 3 para uma segunda interface de rede (por exemplo, DADOS 1) em seu dispositivo. Lembre-se que essas interfaces devem ser habilitadas para o iSCSI. Para obter mais informações, consulte [Modificar interfaces de rede](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Selecione a guia **Destinos** na caixa de diálogo **Propriedades do iniciador iSCSI**. Você deverá ver o destino do dispositivo StorSimple IQN em **Destinos Descobertos**.

   ![Guia Destinos para Propriedades do Iniciador iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Clique em **Conectar** para estabelecer uma sessão iSCSI com o dispositivo StorSimple. Uma caixa de diálogo **Conectar ao Destino** é exibida.
7. Na caixa de diálogo **Conectar-se ao destino**, marque a caixa de seleção **Habilitar vários caminhos**. Clique em **Avançado**.
8. Na caixa de diálogo **Configurações Avançadas**, execute as seguintes etapas:
   
   1. Na lista suspensa **Adaptador local**, selecione **Iniciador iSCSI da Microsoft**.
   2. Na lista suspensa **IP do iniciador**, selecione o endereço IP do host.
   3. Na lista suspensa do IP **Portal de Destino** , selecione o IP da interface do dispositivo.
   4. Clique em **OK** para voltar à caixa de diálogo **Propriedades do iniciador iSCSI**.
9. Clique em **Propriedades**. Na caixa de diálogo **Propriedades**, clique em **Adicionar Sessão**.
10. Na caixa de diálogo **Conectar-se ao destino**, marque a caixa de seleção **Habilitar vários caminhos**. Clique em **Avançado**.
11. Na caixa de diálogo **Configurações avançadas**:

    1. Na lista suspensa **adaptador local** , selecione iniciador iSCSI da Microsoft.
    2. Na lista suspensa **IP do iniciador**, selecione o endereço IP correspondente ao host. Neste caso, você está conectando duas interfaces de rede no dispositivo a uma interface de rede no host. Portanto, essa interface é igual à fornecida para a primeira sessão.
    3. Na lista suspensa **IP do Portal de Destino** , selecione o endereço IP para a segunda interface de dados habilitada no dispositivo.
    4. Clique em **OK** para voltar para a caixa de diálogo Propriedades do Iniciador iSCSI. Você adicionou uma segunda sessão ao destino.
12. Abra **Gerenciamento do Computador** navegando para **Gerenciador do Servidor > Painel > Gerenciamento do Computador**. No painel esquerdo, clique em **Armazenamento > Gerenciamento de Disco**. Os volumes criados no dispositivo StorSimple visíveis para esse host aparecem em **Gerenciamento de Disco** como novos discos.
13. Inicialize o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
    
    ![Gerenciamento de disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Em **Gerenciamento de discos**, clique com o botão direito do mouse em **Discos** e selecione **Propriedades**.
15. Na caixa de diálogo do StorSimple Modelo #### **Propriedades do dispositivo dos discos de vários caminhos**, clique na guia **MPIO**.
    
    ![DeviceProp de disco de vários caminhos do StorSimple 8100.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. Na seção **Nome DSM**, clique em **Detalhes** e verifique se os parâmetros estão definidos para as configurações padrões. Os parâmetros padrão são:
    
    * Período de Verificação do Caminho = 30
    * Contagem de Repetição = 3
    * Período de Remoção PDO = 20
    * Intervalo de Repetição = 1
    * Verificação do Caminho habilitada = Desmarcada.

> [!NOTE]
> **Não modifique os parâmetros padrão.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Etapa 4: configurar o MPIO para ter alta disponibilidade e balanceamento de carga

Para a alta disponibilidade e o balanceamento de carga baseados em vários caminhos, várias sessões devem ser adicionadas manualmente para declarar os diversos caminhos disponíveis. Por exemplo, se o host tiver duas interfaces conectadas à iSCSI e o dispositivo tiver duas interfaces conectadas à iSCSI, você precisará de quatro sessões configuradas com permutações de caminho apropriadas (somente duas sessões serão necessárias se cada interface de DADOS e a interface de host estiverem em uma sub-rede de IP diferente e não forem roteáveis).

**É recomendável ter pelo menos oito sessões paralelas ativas entre o dispositivo e o host do aplicativo.** Isso pode ser obtido habilitando quatro interfaces de rede em seu sistema Windows Server. Use adaptadores de rede físicos ou interfaces virtuais via tecnologias de virtualização de rede no nível de hardware ou sistema operacional no host do Windows Server. Com as duas interfaces de rede no dispositivo, essa configuração resulta em oito sessões ativas. Essa configuração ajuda a otimizar a taxa de transferência do dispositivo e da nuvem.

> [!IMPORTANT]
> **Recomendamos que você não misture interfaces de rede de 1 GbE e 10 GbE. Se você usar duas interfaces de rede, ambas as interfaces deverão ser de um tipo idêntico.**

O procedimento a seguir descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está conectado a um host com duas interfaces de rede. Isso proporciona apenas quatro sessões. Use esse mesmo procedimento com um dispositivo StorSimple com dois adaptadores de rede conectados a um host com quatro adaptadores de rede. Você precisará configurar oito em vez das quatro sessões descritas aqui.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar o MPIO para ter alta disponibilidade e balanceamento de carga

1. Realize uma descoberta de destino: na caixa de diálogo **Propriedades do iniciador iSCSI**, na guia **Descoberta**, clique em **Portal de descoberta**.
2. Na caixa de diálogo **Conectar ao Destino** , digite o endereço IP de uma das interfaces de rede do dispositivo.
3. Clique em **OK** para voltar à caixa de diálogo **Propriedades do iniciador iSCSI**.
4. Na caixa de diálogo **Propriedades do iniciador iSCSI**, selecione a guia **Destinos**, selecione o destino descoberto e depois clique em **Conectar**. A caixa de diálogo **Conectar-se ao destino** é exibida.
5. Na caixa de diálogo **Conectar-se ao destino**:
   
   1. Deixe a configuração do destino padrão selecionada para **Adicionar esta conexão** à lista de destinos favoritos. Isso faz com que o dispositivo tente reiniciar automaticamente a conexão sempre que o computador for reiniciado.
   2. Marque a caixa de seleção **Habilitar vários caminhos**.
   3. Clique em **Avançado**.
6. Na caixa de diálogo **Configurações avançadas**:
   
   1. Na lista suspensa **Adaptador local**, selecione **Iniciador iSCSI da Microsoft**.
   2. Na lista suspensa **IP do Iniciador** , selecione o endereço IP correspondente à primeira interface no host (interface iSCS).
   3. Na lista suspensa **IP do Portal de Destino** , selecione o endereço IP para a primeira interface de dados habilitada no dispositivo.
   4. Clique em **OK** para voltar para a caixa de diálogo Propriedades do Iniciador iSCSI.
7. Clique em **Propriedades** e, na caixa de diálogo **Propriedades**, clique em **Adicionar sessão**.
8. Na caixa de diálogo **Conectar-se ao destino**, marque a caixa de seleção **Habilitar vários caminhos** e depois clique em **Avançado**.
9. Na caixa de diálogo **Configurações avançadas**:
   
   1. Na lista suspensa **Adaptador local**, selecione **Iniciador iSCSI da Microsoft**.
   2. Na lista suspensa **IP do Iniciador** , selecione o endereço IP correspondente à segunda iSCSI interface no host.
   3. Na lista suspensa **IP do Portal de Destino** , selecione o endereço IP para a segunda interface de dados habilitada no dispositivo.
   4. Clique em **OK** para voltar à caixa de diálogo **Propriedades do iniciador iSCSI**. Agora, você adicionou uma segunda sessão ao destino.
10. Repita as Etapas de 8 a 10 para adicionar outras sessões (caminhos) ao destino. Com duas interfaces no host e duas no dispositivo, você pode adicionar um total de quatro sessões.
11. Após adicionar as sessões desejadas (caminhos), na caixa de diálogo **Propriedades do iniciador iSCSI**, selecione o destino e clique em **Propriedades**. Na guia Sessões da caixa de diálogo **Propriedades** , observe os quatro identificadores da sessão que correspondem às permutações possíveis de caminho. Para cancelar uma sessão, marque a caixa de seleção ao lado de um identificador de sessão e depois clique em **Desconectar**.
12. Para exibir dispositivos apresentados em sessões, selecione a guia **dispositivos** . Para configurar a política de MPIO para um dispositivo selecionado, clique em **MPIO**. A caixa de diálogo **Detalhes do Dispositivo** é exibida. Na guia **MPIO**, selecione as configurações **Política de balanceamento de carga** apropriadas. Você também pode ver o tipo de caminho **Ativo** ou **Em espera**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como usar o serviço do Gerenciador de Dispositivos StorSimple para modificar sua configuração do dispositivo StorSimple](storsimple-8000-modify-device-config.md).