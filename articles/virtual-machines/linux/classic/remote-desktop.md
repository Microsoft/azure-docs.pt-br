---
title: "Área de Trabalho Remota para uma VM do Linux | Microsoft Docs"
description: "Saiba como instalar e configurar a Área de Trabalho Remota para se conectar a uma VM Linux do Microsoft Azure para o modelo de implantação clássico"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: mingzhan
ms.openlocfilehash: 68031d548bdbeda9a83d1bceaaea7c5bbcab3188
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Usando a Área de Trabalho Remota para se conectar a uma VM do Linux do Microsoft Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para a versão atualizada do Resource Manager deste artigo, clique [aqui](../use-remote-desktop.md).

## <a name="overview"></a>Visão geral
O protocolo RDP é um protocolo proprietário usado para o Windows. Como podemos usar o RDP para se conectar remotamente a uma VM (máquina virtual) do Linux?

Essa orientação fornecerá a resposta! Ela ajudará você com a instalação e configuração de xrdp em sua VM Linux do Microsoft Azure, que permite que você se conecte a ela com a Área de Trabalho Remota de um computador com Windows. Usaremos a VM do Linux executando o Ubuntu ou OpenSUSE como o exemplo neste guia.

A ferramenta xrdp é um servidor RDP de software livre que permite a conexão do servidor Linux com a Área de Trabalho Remota de um computador com Windows. O RDP tem um desempenho melhor do que a VNC (Computação de Rede Virtual). A VNC renderiza usando gráficos de qualidade de JPEG e pode ser lenta, enquanto o RDP é rápido e claro.

> [!NOTE]
> Você já deve ter uma VM do Microsoft Azure executando o Linux. Para criar e configurar uma VM do Linux, veja o [Tutorial de VM do Linux do Azure](createportal.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Criar um ponto de extremidade para a Área de Trabalho Remota
Usaremos o ponto de extremidade padrão 3389 para a Área de Trabalho Remota neste documento. Configure o ponto de extremidade 3389 como a `Remote Desktop` para sua VM Linux como abaixo:

![imagem](./media/remote-desktop/endpoint-for-linux-server.png)

Se você não souber como configurar um ponto de extremidade para sua VM, consulte [este guia](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Instalar o Gnome Desktop
Conecte-se à sua VM Linux por meio do `Gnome Desktop` e instale o `putty`.

Para o Ubuntu, use:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Para OpenSUSE, use:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Instalar o xrdp
Para o Ubuntu, use:

    #sudo apt-get install xrdp

Para OpenSUSE, use:

> [!NOTE]
> Atualize a versão do OpenSUSE com a versão que você está usando no seguinte comando. O exemplo abaixo é para o `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Inicie o xrdp e defina o serviço xdrp na inicialização
Para OpenSUSE, use:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Para Ubuntu, o xrdp será iniciado e habilitado automaticamente na inicialização após a instalação.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Usando xfce se você estiver usando uma versão do Ubuntu posterior ao Ubuntu 12.04LTS
Como a versão atual do xrdp não dá suporte ao Gnome Desktop para versões do Ubuntu posteriores ao Ubuntu 12.04LTS, usaremos o `xfce` Desktop em vez disso.

Para instalar o `xfce`, use este comando:

    #sudo apt-get install xubuntu-desktop

Em seguida, habilite `xfce` usando este comando:

    #echo xfce4-session >~/.xsession

Edite o arquivo de configuração `/etc/xrdp/startwm.sh`:

    #sudo vi /etc/xrdp/startwm.sh   

Adicione a linha `xfce4-session` antes da linha `/etc/X11/Xsession`.

Para reiniciar o serviço xrdp, use:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Conectar-se à VM Linux de uma máquina com Windows
Em um computador Windows, inicie o cliente de Área de Trabalho Remota e insira o nome DNS da VM Linux. Ou vá para o Painel da VM no Portal do Azure e clique em `Connect` para se conectar à VM Linux. Nesse caso, você vê a janela de logon:

![imagem](./media/remote-desktop/no2.png)

Faça logon com o nome de usuário e a senha da sua VM Linux.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o uso do xrdp, consulte [http://www.xrdp.org/](http://www.xrdp.org/).
