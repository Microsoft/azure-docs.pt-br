---
title: Provisionar um StorSimple Virtual Array no Hyper-V | Microsoft Docs
description: Esse segundo tutorial sobre a implantação da Matriz Virtual StorSimple envolve o provisionamento de uma matriz virtual no Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070593"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Implantar o StorSimple Virtual Array - Provisionar no Hyper-V
![Diagrama mostrando as etapas necessárias para implantar uma matriz virtual. A primeira parte da segunda etapa é rotulada como provisionar no Hyper-V e é realçada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Visão geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como provisionar um StorSimple Virtual Array em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo à implantação de Matrizes Virtuais StorSimple no portal do Azure e na Nuvem do Microsoft Azure Governamental.

Você precisa de privilégios de administrador para provisionar e configurar uma matriz virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento
Aqui você encontrará os pré-requisitos para provisionar uma matriz virtual em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Para implantar o serviço Gerenciador de Dispositivos StorSimple
Antes de começar, verifique se:

* Você concluiu todas as etapas em [Preparar o portal para o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Você baixou a imagem da matriz virtual para o Hyper-V no portal do Azure. Para obter mais informações, consulte **Etapa 3: baixar a imagem do dispositivo virtual** do [guia Preparar o portal da Matriz Virtual StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > O software em execução na matriz virtual StorSimple só pode ser usado com o serviço Gerenciador StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Usar a matriz virtual StorSimple
Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V no Windows Server 2008 R2 ou superior que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos abaixo para provisionar seu dispositivo virtual:

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões de arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, analise os requisitos de rede para implantar uma Matriz Virtual StorSimple e configure a rede de datacenter de acordo com os eles. Para obter mais informações, consulte [Requisitos de rede do StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para provisionar e se conectar a uma matriz virtual, você precisará executar as seguintes etapas:

1. Verificar se o sistema de host tem recursos suficientes para cumprir os requisitos mínimos da matriz virtual.
2. Provisionar uma matriz virtual no seu hipervisor.
3. Iniciar a matriz virtual e obter o endereço IP.

Cada uma das etapas acima é explicada nas seções a seguir.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Etapa 1: verificar se o sistema de host cumpre os requisitos mínimos da matriz virtual
Para criar uma matriz virtual, você precisará de:

* A função do Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.
* Microsoft Hyper-V Manager em um cliente Microsoft Windows conectado ao host.

Faça com que o hardware subjacente (sistema de host) em que está criando a matriz virtual possa dedicar os recursos a seguir à sua matriz virtual:

* Um mínimo de quatro núcleos.
* Pelo menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões de arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
* Uma interface de rede.
* Um disco virtual de 500 GB para dados do sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Etapa 2: provisionar uma matriz virtual no hipervisor
Execute as etapas a seguir para provisionar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-array"></a>Para provisionar uma matriz virtual
1. No seu host do Windows Server, copie a imagem da matriz virtual em uma unidade local. Você baixou essa imagem (VHD ou VHDX) por meio do portal do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.
2. Abra o **Server Manager**. No canto superior direito, clique em **Ferramentas** e selecione **Gerenciador do Hyper-V**.

   ![Captura de tela de Gerenciador do Servidor mostrando um menu ferramentas expandido com o item Gerenciador do Hyper-V realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se você estiver executando o Windows Server 2008 R2, abra o Gerenciador do Hyper-V. No Gerenciador do Servidor, clique em **Funções > Hyper-V > Gerenciador do Hyper-V**.
3. No **Gerenciador do Hyper-V**, no painel de escopo, clique com o botão direito do mouse no nó do sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![Captura de tela do Gerenciador do Hyper-V mostrando o menu de atalho de um nó do sistema, com os itens novos e da máquina virtual realçados.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na página **Antes de começar** do Assistente de Nova Máquina Virtual, clique em **Avançar**.
5. Na página **Especificar nome e localização**, forneça um **Nome** para sua matriz virtual. Clique em **Avançar**.

   ![Captura de tela da página Especificar nome e local mostrando a caixa nome preenchida e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na página **Especificar geração**, escolha o tipo de imagem do dispositivo e clique em **Avançar**. Esta página não aparecerá se você estiver usando o Windows Server 2008 R2.

   * Escolha **Geração 2** se você baixou uma imagem .vhdx para o Windows Server 2012 ou posterior.
   * Escolha **Geração 1** se você baixou uma imagem .vhdx para o Windows Server 2008 R2 ou posterior.

   ![Captura de tela da página especificar geração mostrando a opção geração 2 selecionada e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na página **Atribuir memória**, especifique uma **Memória de inicialização** de pelo menos **8192 MB**, não habilite a memória dinâmica e clique em **Avançar**.

   ![Captura de tela da página atribuir memória mostrando um valor de 8192 na caixa memória de inicialização. O botão Avançar é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na página **Configurar rede**, especifique o comutador virtual que está conectado à Internet e clique em **Avançar**.

   ![Captura de tela da página Configurar rede mostrando o item do comutador virtual Hyper-V selecionado na caixa conexão e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na página **Conectar disco rígido virtual**, escolha **Usar um disco rígido virtual existente**, especifique a localização da imagem da matriz virtual (.vhdx ou .vhd) e clique em **Avançar**.

   ![Captura de tela da página conectar disco rígido virtual com usar um disco rígido virtual existente selecionado, um local inserido e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Examine o **Resumo** e clique em **Concluir** para criar a máquina virtual.

    ![Captura de tela da página Concluindo o assistente de nova máquina virtual mostrando uma descrição de uma máquina virtual. O botão Concluir é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Para cumprir os requisitos mínimos, você precisará de quatro núcleos. Para adicionar quatro processadores virtuais, selecione o sistema de host na janela **Gerenciador Hyper-V**. No painel à direita da lista de **máquinas virtuais**, localize a máquina virtual que você acabou de criar. Selecione e clique com o botão direito do mouse no nome do computador e selecione **Configurações**.

    ![Captura de tela da janela do Gerenciador do Hyper-V. Um computador é selecionado na lista e o item configurações é realçado no menu de atalho desse computador.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na página **Configurações**, no painel à esquerda, clique em **Processador**. No painel à direita, defina o **número de processadores virtuais** como quatro (ou mais). Clique em **Aplicar**.

    ![Captura de tela da página configurações com o item do processador realçado, o número de processadores virtuais definido como quatro e o botão Aplicar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Para cumprir os requisitos mínimos, você também precisa adicionar um disco de dados virtual de 500 GB. Na página de **Configurações** :

    1. No painel à esquerda, selecione **Controlador SCSI**.
    2. No painel à direita, selecione **Disco Rígido** e clique em **Adicionar**.

    ![Captura de tela da página configurações com o item do controlador SCSI selecionado, o item do disco rígido selecionado e o botão Adicionar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na página **Disco rígido**, selecione a opção **Disco rígido virtual** e clique em **Novo**. O **Assistente novo disco rígido Virtual** é iniciado.

    ![Captura de tela da página configurações com o item disco rígido selecionado, a opção disco rígido virtual selecionada e o botão novo realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na página **Antes de começar** do Assistente de Novo Disco Rígido Virtual, clique em **Avançar**.
16. Na página **Escolher Formato de Disco**, aceite a opção padrão de formato **VHDX**. Clique em **Próximo**. Esta tela não é apresentada se o Windows Server 2008 R2 está em execução.

    ![Captura de tela da página escolher formato de disco com a opção VHDX selecionada e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na página **Escolher Tipo de Disco**, defina o tipo de disco rígido virtual como **Expansão dinâmica** (recomendado). Um disco de **Tamanho fixo** funcionaria, mas talvez você precise aguardar bastante. É recomendável que você não use a opção **Diferenciar** . Clique em **Próximo**. No Windows Server 2012 R2 e no Windows Server 2012, **Expandir dinamicamente** é a opção padrão; já no Windows Server 2008 R2, o padrão é **Tamanho fixo**.

    ![Captura de tela da página escolher tipo de disco com a opção de expansão dinâmica selecionada e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na página **Especificar Nome e Localização**, forneça um **nome** e também uma **localização** (é possível navegar até um) para o disco de dados. Clique em **Próximo**.

    ![Captura de tela da página Especificar nome e local mostrando valores nas caixas nome e local. O botão Avançar é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na página **Configurar Disco**, selecione a opção **Criar um novo disco de rígido virtual em branco** e especifique o tamanho como **500 GB** (ou mais). Embora 500 GB seja o requisito mínimo, você sempre poderá provisionar um disco maior. Observe que, depois de provisionado, você não poderá expandir ou reduzir o disco. Para obter mais informações sobre o tamanho do disco a ser provisionado, examine a seção de dimensionamento no [documento de práticas recomendadas](storsimple-ova-best-practices.md). Clique em **Avançar**.

    ![Captura de tela da página Configurar disco com criar um novo disco rígido virtual em branco selecionado, o tamanho definido como 500 e o botão Avançar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na página **Resumo**, examine os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Concluir** para criar o disco. O assistente é fechado e um disco rígido virtual é adicionado à sua máquina.

    ![Captura de tela da página Concluindo o assistente de novo disco rígido virtual mostrando uma descrição de um disco rígido virtual. O botão Concluir é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Retorne para a página **Configurações** . Clique em **OK** para fechar a página **Configurações** e retornar à janela do Gerenciador do Hyper-V.

    ![Captura de tela da página Configurações. O botão OK é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Etapa 3: iniciar a matriz virtual e obter o IP
Execute as etapas a seguir para iniciar a matriz virtual e conectar-se a ela.

#### <a name="to-start-the-virtual-array"></a>Para iniciar a matriz virtual
1. Configure a matriz virtual.

   ![Captura de tela da janela do Gerenciador do Hyper-V. A nova matriz é selecionada na lista e o item inicial é realçado no menu de atalho dessa matriz.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Depois que o dispositivo estiver em execução, selecione-o, clique com o botão direito do mouse e selecione **Conectar**.

   ![Captura de tela da janela do Gerenciador do Hyper-V. A nova matriz é selecionada, seu estado é em execução e o item conectar é realçado em seu menu de atalho.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Você terá que esperar de 5 a 10 minutos para que o dispositivo esteja pronto. Será exibida uma mensagem de status no console para indicar o andamento. Depois que o dispositivo estiver pronto, vá para **Ação**. Pressione `Ctrl + Alt + Delete` para entrar na matriz virtual. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *Password1*.

   ![Captura de tela da guia do console do cliente vSphere mostrando caracteres indeterminados na caixa de entrada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Por motivos de segurança, a senha do administrador do dispositivo expira no primeiro logon. Você será solicitado a alterar a senha.

   ![Captura de tela da guia do console do cliente do vSphere. O texto na página informa que a senha deve ser alterada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Insira uma senha que contenha pelo menos oito caracteres. A senha deve cumprir pelo menos três dos quatro requisitos a seguir: caracteres maiúsculos, minúsculos, numéricos e especiais. Insira a senha novamente para confirmá-la. Você é notificado de que a senha foi alterada.

   ![Captura de tela da guia do console do cliente do vSphere. O texto na página informa que a senha foi alterada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Depois que a senha for alterada com êxito, a matriz virtual poderá ser reiniciada. Aguarde até o dispositivo iniciar.

   ![Captura de tela da página principal do StorSimpleAdmin. O texto na página solicita que o usuário aguarde o serviço de notificação de eventos do sistema.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    O console do Windows PowerShell do dispositivo é exibido junto com uma barra de progresso.

   ![Captura de tela mostrando uma janela de console com uma barra de progresso. O texto na janela declara que a configuração inicial está em andamento e solicita que o usuário aguarde.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. As etapas 6 a 8 se aplicam somente na inicialização de um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você verá a tela a seguir.

   ![Captura de tela mostrando uma janela de console com texto que descreve o dispositivo. O prompt de comando lê "controlador" e aparece pronto para entrada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Em seguida, configure a rede.
7. Use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em sua matriz virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

   ![Captura de tela mostrando uma janela de console com a saída do comando Get-HcsIpAddress. "Ethernet" é listado como o nome do dispositivo.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Consulte o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Captura de tela mostrando uma janela de console com a saída do comando Get-Help Set-HcsIpAddress e o uso correto do comando Set-HcsIpAddress.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Use esse endereço IP para se conectar à interface do usuário da Web da sua matriz virtual e concluir a configuração local e o registro.

   ![Captura de tela mostrando uma janela de console com texto de banner de dispositivo. Esse texto inclui o endereço IP do dispositivo e a URL.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcional) Execute esta etapa somente se você estiver implantando seu dispositivo na Nuvem do governo. Agora, você habilitará o modo FIPS (Federal Information Processing Standard) dos Estados Unidos em seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais.

    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicialize o dispositivo após ter habilitado o modo FIPS para que as validações criptográficas tenham efeito.

       > [!NOTE]
       > Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não tem suporte.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá o erro a seguir no texto da faixa (mostrado abaixo). Modifique a configuração do dispositivo para que o computador tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração na Etapa 1: verificar se o sistema de host atende aos requisitos mínimos da matriz virtual.

![Captura de tela mostrando uma janela de console com texto de banner de dispositivo. Esse texto inclui uma mensagem de erro que fornece uma URL para solucionar o problema.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Caso observe algum outro erro durante a configuração inicial usando a interface do usuário da Web local, veja os seguintes fluxos de trabalho:

* Execute testes de diagnóstico para [solucionar problemas de configuração da interface do usuário](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gere um pacote de log e exiba os arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Próximas etapas
* [Configurar sua StorSimple Virtual Array como um servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar sua StorSimple Virtual Array como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
