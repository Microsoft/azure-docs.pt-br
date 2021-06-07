---
title: Tutorial sobre como provisionar o Azure Data Box Gateway no Hyper-V | Microsoft Docs
description: O segundo tutorial de implantação do Azure Data Box Gateway envolve o provisionamento de um dispositivo virtual no Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727678"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Tutorial: Provisionar o Azure Data Box Gateway no Hyper-V

## <a name="overview"></a>Visão geral

Este tutorial descreve como provisionar um Data Box Gateway em um sistema host que executa o Hyper-V no Windows Server 2016, Windows Server 2012 R2 ou Windows Server 2012.

Você precisa de privilégios de administrador para provisionar e configurar um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Veja a seguir os pré-requisitos para provisionar um dispositivo virtual em um sistema host que executa o Hyper-V no Windows Server 2016 ou no Windows Server 2012 R2.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso de Data Box Gateway

Antes de começar, verifique se:

* Você concluiu todas as etapas em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).
* Você fez o download da imagem do dispositivo virtual para Hyper-V do portal do Azure, conforme descrito em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Para o dispositivo virtual do Data Box Gateway

Antes de implantar um dispositivo, verifique se:

* Você tem acesso a um sistema de host que executa o Hyper-V no Windows Server 2012 R2 ou superior que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. É altamente recomendável ter pelo menos 16 GB de RAM.
  * Uma interface de rede.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para armazenar dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

* Revise os requisitos de rede para implantar um Data Box Gateway e configurar a rede de datacenter de acordo com os requisitos. Saiba mais em [Requisitos de rede do Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
* Garanta que a largura de banda mínima da Internet seja de 20 Mbps, para permitir o funcionamento ideal do dispositivo.

## <a name="check-the-host-system"></a>Verificar o sistema host

Para criar um dispositivo virtual, você precisa de:

* A função do Hyper-V instalada no Windows Server 2016, Windows Server 2012 R2 ou Windows Server 2012.
* Microsoft Hyper-V Manager em um cliente Microsoft Windows conectado ao host.
* Verifique se o hardware subjacente (sistema host) em que está criando o dispositivo virtual possa dedicar os recursos a seguir ao seu dispositivo virtual:

  * Um mínimo de 4 processadores virtuais.
  * Pelo menos 8 GB de RAM.
  * Uma interface de rede conectada à rede capaz de rotear o tráfego para a Internet.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para armazenar dados do sistema.

## <a name="bitlocker-considerations"></a>Considerações sobre o BitLocker

* Recomendamos que você habilite o BitLocker em sua máquina virtual do Data Box Gateway. Por padrão, o BitLocker não fica habilitado. Para obter mais informações, consulte:
  * [Configurações de suporte para criptografia no Gerenciador do Hyper-V](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Suporte para BitLocker em uma máquina virtual](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Provisionar um dispositivo virtual no hipervisor

Execute as etapas a seguir para provisionar um dispositivo no seu hipervisor.

1. No seu host do Windows Server, copie a imagem do dispositivo virtual para uma unidade local. Você baixou essa imagem VHDX por meio do portal do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.

2. Abra o **Server Manager**. No canto superior direito, clique em **Ferramentas** e selecione **Gerenciador do Hyper-V**.

    ![Selecione o Gerenciador do Hyper-V no Gerenciador do Servidor](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. No **Gerenciador do Hyper-V**, no painel de escopo, clique com o botão direito do mouse no nó do sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![Crie uma máquina virtual no Gerenciador do Hyper-V](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Na página **Antes de começar** do Assistente de Nova Máquina Virtual, clique em **Avançar**.
5. Na página **Especificar nome e localização**, forneça um **Nome** para seu dispositivo virtual. Clique em **Próximo**.

   ![Especifique o nome e a página de localização](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Na página **Especificar geração**, escolha **Geração 2** para o tipo de imagem do dispositivo vhdx e clique em **Avançar**.    

   ![Especificar a página de geração](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Na página **Atribuir memória**, especifique uma **Memória de inicialização** de pelo menos **8192 MB**, não habilite a memória dinâmica e clique em **Avançar**.

   ![Atribuir uma página de memória](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Na página **Configurar rede**, especifique o comutador virtual que está conectado à Internet e clique em **Avançar**.

   ![Configurar a página de rede](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Na página **Conectar disco rígido virtual**, escolha **Usar um disco rígido virtual existente**, especifique a localização da imagem do dispositivo virtual e clique em **Avançar**.

   ![Conecte-se à página de disco rígido virtual](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Examine o **Resumo** e clique em **Concluir** para criar a máquina virtual.

    ![Completando a página de assistente da Nova Máquina Virtual](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Será necessário obter quatro processadores virtuais para atender aos requisitos mínimos. Para adicionar quatro processadores virtuais, selecione o sistema host na janela **Gerenciador do Hyper-V**. No painel à direita da lista de **máquinas virtuais**, localize a máquina virtual que você acabou de criar. Selecione e clique com o botão direito do mouse no nome do computador e selecione **Configurações**.

    ![Configurações da máquina virtual](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Na página **Configurações**, no painel à esquerda, clique em **Processador**. No painel à direita, defina o **número de processadores virtuais** como quatro (ou mais). Clique em **Aplicar**.

    ![Definir o número de processadores virtuais na página Configurações](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Também será necessário adicionar um disco de dados virtual de 2 TB para atender aos requisitos mínimos. Na página de **Configurações** :

    1. No painel à esquerda, selecione **Controlador SCSI**.
    2. No painel à direita, selecione **Disco Rígido** e clique em **Adicionar**.

    ![Adicionar o disco rígido na página de Configurações](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Na página **Disco rígido**, selecione a opção **Disco rígido virtual** e clique em **Novo**. O **Assistente novo disco rígido Virtual** é iniciado.

    ![Novo Assistente de Disco Rígido Virtual](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Na página **Antes de começar** do Assistente de Novo Disco Rígido Virtual, clique em **Avançar**.
16. Na página **Escolher Formato de Disco**, aceite a opção padrão de formato **VHDX**. Clique em **Próximo**.
17. Na página **Escolher Tipo de Disco**, defina o tipo de disco rígido virtual como **Expansão dinâmica** (recomendado). Um disco de **Tamanho fixo** funcionaria, mas talvez você precise aguardar bastante. É recomendável que você não use a opção **Diferenciar** . Clique em **Próximo**.

    ![Escolha a página de Tipo de Disco](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Na página **Especificar Nome e Localização**, forneça um **nome** e uma **localização** (será possível procurá-los) para o disco de dados. Clique em **Próximo**.

    ![Especifique o Nome e a página de Localização](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Na página **Configurar Disco**, selecione a opção **Criar um disco rígido virtual em branco** e especifique o tamanho como **2 TB** (ou superior).

    Embora 2 TB seja o requisito mínimo, você sempre poderá provisionar um disco maior. Observe que não será possível reduzir o disco após o provisionamento. A tentativa de reduzir o disco resulta na perda de todos os dados locais no dispositivo. Não há suporte para a expansão do disco de dados. Clique em **Próximo**.

    ![Configurar a página de Disco](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Na página **Resumo**, examine os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Concluir** para criar o disco. O assistente é fechado e um disco rígido virtual é adicionado à sua máquina.

    ![Completando a página do Assistente do Novo Disco Rígido Virtual](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Retorne para a página **Configurações** . Clique em **OK** para fechar a página **Configurações** e retornar à janela do Gerenciador do Hyper-V.

    ![Página Configurações](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

Sua máquina virtual já está totalmente configurada.

> [!NOTE]
> Não será possível provisionar um novo Data Box Gateway copiando o VHD configurado. Cada dispositivo virtual novo do Data Box Gateway deverá ser provisionado de uma imagem do dispositivo virtual para o Hyper-V que será baixado do portal do Azure.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Iniciar o dispositivo virtual e obter o IP

Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1. Inicie o dispositivo virtual.

   ![Iniciar o dispositivo virtual](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Depois que o dispositivo estiver em execução, selecione-o, clique com o botão direito do mouse e selecione **Conectar**.

3. Você terá que esperar de 10 a 15 minutos para que o dispositivo esteja pronto. Será exibida uma mensagem de status no console para indicar o andamento. Depois que o dispositivo estiver pronto, vá para **Ação**. Pressione `Ctrl + Alt + Delete` para entrar no dispositivo virtual. O usuário padrão é *EdgeUser* e a senha padrão é *Password1*.

   ![Entrar no dispositivo virtual](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. As etapas 5 a 7 se aplicam somente na inicialização de um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você uma mensagem sobre isso.

5. Para configurar a rede, use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

6. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Consulte o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Use esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e ativação.

   ![Faixa do dispositivo virtual com endereço IP e URL de conexão](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa. Modifique a configuração do dispositivo para que o computador tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração em [Verificar se o sistema de host atende aos requisitos mínimos do dispositivo virtual](#check-the-host-system).

Caso observe algum outro erro durante a configuração inicial usando a interface do usuário da Web local, veja os seguintes fluxos de trabalho:

* [Execute testes de diagnóstico para solucionar problemas na configuração da interface do usuário da Web](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Gere um pacote de log e exiba os arquivos de log](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Vá para o próximo tutorial para aprender a conectar, configurar e ativar suas redes virtuais.

> [!div class="nextstepaction"]
> [Conectar-se e configurar seu Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md)
