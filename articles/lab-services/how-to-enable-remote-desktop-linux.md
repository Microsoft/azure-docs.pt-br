---
title: Habilitar a área de trabalho remota g para Linux no Azure Lab Services | Microsoft Docs
description: Saiba como usar a área de trabalho remota para máquinas virtuais do Linux em um laboratório no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647946"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Habilitar a área de trabalho remota gráfica para máquinas virtuais do Linux no Azure Lab Services
Este artigo mostra como fazer as seguintes tarefas:

- Habilitar sessões de área de trabalho remota gráficas para uma VM do Linux
- Como se conectar a uma VM do Linux usando o protocolo de área de trabalho remota (RDP) ou clientes de área de trabalho remota X2Go

## <a name="set-up-graphical-remote-desktop-solution"></a>Configurar a solução de área de trabalho remota gráfica
Quando um laboratório é criado a partir de uma imagem do **Linux**, o acesso **SSH** (Secure Shell) é configurado automaticamente para que o instrutor possa se conectar à VM do modelo por meio da linha de comando usando o SSH.  Da mesma forma, quando a VM de modelo é publicada, os alunos também podem se conectar às suas VMs usando o SSH.

Para se conectar a uma VM do Linux usando uma **GUI** (interface gráfica do usuário), recomendamos usar **RDP** ou **X2Go**.  Essas duas opções exigem que o instrutor faça algumas configurações adicionais na VM do modelo:

### <a name="rdp-setup"></a>Configuração de RDP
Para usar o RDP, o instrutor precisa:
  - Habilitar a conexão de área de trabalho remota. Isso é especificamente necessário para abrir a porta da VM para RDP.
  - Instalar o servidor de área de trabalho remota RDP.
  - Instalar um ambiente de área de trabalho gráfica do Linux (como MATE, XFCE etc.).

### <a name="x2go-setup"></a>Configuração do X2Go
Para usar o X2Go, o instrutor precisa:
- Instalar o servidor de área de trabalho remota do X2Go.
- Instalar um ambiente de área de trabalho gráfica do Linux (como MATE, XFCE etc.).

O X2Go usa a mesma porta que já está habilitada para SSH.  Como resultado, não é necessário fazer mais nenhuma configuração para abrir uma porta na VM para X2Go.

> [!NOTE]
> Em alguns casos, como com o Ubuntu LTS 18.04, o X2Go oferece melhor desempenho.  Se você usar o RDP e perceber que há latência ao interagir com o ambiente de área de trabalho gráfica, experimente o X2Go, pois ele pode melhorar o desempenho.

> [!IMPORTANT]
>  Algumas imagens do Marketplace já têm um ambiente de área de trabalho gráfica e um servidor de área de trabalho remota instalados.  Por exemplo, a [Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) já tem [servidor XFCE e X2Go instalado e configurado para aceitar conexões do cliente](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Habilitar a conexão de área de trabalho remota para RDP

Esta etapa só é necessária para se conectar com o RDP.  Se você planeja usar o X2Go, pule para a próxima seção, já que o X2Go usa a porta SSH.

1.  Durante a criação do laboratório, o instrutor tem a opção de **Habilitar a conexão de área de trabalho remota**.  O instrutor deve **habilitar** essa opção para abrir a porta necessária na VM Linux para uma sessão de área de trabalho remota do RDP.  Caso contrário, se essa opção ficar **desabilitada**, somente a porta para o SSH será aberta.
  
    ![Captura de tela que mostra a janela "novo laboratório" com a opção "Habilitar Conexão de Área de Trabalho Remota".](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Na caixa de mensagem **Habilitar Conexão de Área de Trabalho Remota**, selecione **Continuar com Área de Trabalho Remota**. 

    ![Habilitar a conexão de área de trabalho remota para uma imagem do Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Instalar RDP ou X2Go

Após criar o laboratório, o instrutor precisa garantir que um ambiente de área de trabalho gráfica e um servidor de área de trabalho remota estejam instalados na VM do modelo.  Os instrutores devem primeiro usar o SSH para se conectar à VM do modelo e instalar os pacotes para:
- O servidor de área de trabalho remota RDP ou X2Go.
- Um ambiente de área de trabalho gráfica, como MATE, XFCE, etc.

Depois de fazer essa configuração, o instrutor poderá se conectar à VM do modelo usando o cliente da **Área de Trabalho Remota da Microsoft (RDP)** ou do **X2Go**.

Siga as etapas abaixo para configurar a VM do modelo:

1. Se você vir **Personalizar o modelo** na barra de ferramentas, selecione-o. Em seguida, selecione **Continuar** na caixa de diálogo **Personalizar modelo**. Essa ação inicia a VM modelo.  

    ![Personalizar modelo](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Depois que a VM modelo for iniciada, você poderá selecionar **Modelo de conexão** e, em seguida, **Conectar-se via SSH** na barra de ferramentas. 

    ![Conectar-se ao modelo via RDP após a criação do laboratório](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Você verá a seguinte caixa de diálogo: **Conectar-se à sua máquina virtual**. Selecione o botão **Copiar** ao lado da caixa de texto para copiá-lo para a área de transferência. Salve as informações de conexão SSH. Use essas informações de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para se conectar à máquina virtual.
 
    ![Cadeia de conexão SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Instale o RDP ou o X2Go juntamente com o ambiente de área de trabalho gráfica de sua escolha.  Consulte as instruções a seguir:
    - [Instalar e configurar RDP](../virtual-machines/linux/use-remote-desktop.md)
    - [Instalar e configurar X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Conectar-se à VM de modelo por meio da GUI

Depois de configurar a VM do modelo, o instrutor poderá se conectar por meio da GUI usando o cliente da **Área de Trabalho Remota da Microsoft (RDP)** ou do **X2Go**.  O cliente usado depende do servidor (RDP ou o X2Go) configurado como o servidor de área de trabalho remota na VM do modelo.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Cliente da Área de Trabalho Remota da Microsoft (RDP)

O cliente da Área de Trabalho Remota da Microsoft (RDP) é usado para se conectar à VM de um modelo que configurada para RDP.  O cliente da Área de Trabalho Remota pode ser usado no Windows, em Chromebooks, em Macs e muito mais.  Consulte o artigo em [clientes da Área de Trabalho Remota](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) para mais informações.

Siga as etapas abaixo com base no tipo de computador usado para se conectar à VM de modelo:

- Windows
  1. Clique em **Conectar ao modelo** na barra de ferramentas do laboratório e selecione **Conectar via RDP** para se conectar à VM do modelo. 
  1. Salve e use o arquivo RDP para se conectar à VM do modelo com o cliente da Área de Trabalho Remota. 
  1. Normalmente, o cliente da Área de Trabalho Remota já está instalado e configurado no Windows.  Como resultado, tudo o que você precisa fazer é clicar no arquivo RDP para abri-lo e iniciar a sessão remota.

- Mac
  1. Clique em **Conectar-se ao modelo** na barra de ferramentas do laboratório e, em seguida, selecione **Conectar via RDP** para salvar o arquivo RDP.  
  1. Em seguida, consulte o artigo de instruções [Conectar-se a uma VM usando o RDP em um Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Clique em **Conectar-se ao modelo** na barra de ferramentas do laboratório e, em seguida, selecione **Conectar via RDP** para salvar o arquivo RDP.  
  1. Em seguida, consulte o artigo de instruções [Conectar-se a uma VM usando o RDP em um Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Cliente do X2Go

O cliente do X2Go é usado para se conectar a uma VM de modelo que tem o X2Go configurado.  Use as informações de conexão de SSH da VM do modelo para seguir as etapas no artigo de instruções [Conectar-se a uma VM usando o X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Próximas etapas
Depois que um instrutor configurar e publicar o RDP ou o X2Go na VM do modelo, os alunos poderão se conectar às suas VMs por meio da área de trabalho remota ou do SSH da GUI.

Para obter mais informações, consulte:
 - [Conectar-se a uma VM do Linux](how-to-use-remote-desktop-linux-student.md)