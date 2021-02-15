---
title: Criar e carregar um VHD do Ubuntu Linux no Azure
description: Saiba como criar e carregar um disco rígido virtual (VHD) do Azure que contém o sistema operacional Ubuntu Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: ca1aae3e819d58ea32453f2549d162bbfc9c91ae
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203211"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Ubuntu para o Azure


O Ubuntu agora publica VHDs oficiais do Azure para download em [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Se você precisar compilar sua própria imagem do Ubuntu especializada para o Azure, em vez de usar o procedimento manual abaixo, é recomendável começar com esses VHDs de trabalho conhecidos e personalizá-los conforme necessário. As versões mais recentes da imagem sempre podem ser encontradas nos seguintes locais:

* Ubuntu 16.04/Xenial: [Ubuntu-16, 4-Server-cloudimg-AMD64-DISK1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-AMD64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já instalou um sistema operacional Ubuntu Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Notas de instalação do Ubuntu**

* Veja também as [Notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não tem suporte no Azure, somente o **VHD fixo**.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o `Convert-VHD` cmdlet.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) em discos de dados.
* Não configure uma partição de permuta ou um Swapfile no disco do sistema operacional. O agente de provisionamento Cloud-init pode ser configurado para criar um arquivo de permuta ou uma partição de permuta no disco de recursos temporário. Verifique as etapas a seguir para obter mais informações a esse respeito.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco não processado para VHD, certifique-se de que o tamanho do disco não processado seja um múltiplo de 1 MB antes da conversão. Consulte [Notas de Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="manual-steps"></a>Etapas manuais
> [!NOTE]
> Antes de tentar criar sua própria imagem personalizada do Ubuntu para o Azure, considere usar as imagens previamente compiladas e testadas em [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) vez disso.
> 
> 

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3. Substitua os repositórios atuais na imagem para usar o repositório do Azure do Ubuntu.

    Antes de editar `/etc/apt/sources.list`, é recomendável fazer um backup:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16, 4 e Ubuntu 18, 4:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. As imagens do Ubuntu Azure agora estão usando o [kernel personalizado pelo Azure](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Atualize o sistema operacional para o kernel mais recente personalizado do Azure e instale as ferramentas Linux do Azure (incluindo as dependências do Hyper-V) executando os seguintes comandos:

    Ubuntu 16, 4 e Ubuntu 18, 4:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Modifique a linha de inicialização para o Grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto, localize a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicione-a, se necessário) e edite-a para incluir os seguintes parâmetros:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Salve e feche esse arquivo e execute `sudo update-grub`. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte técnico do Azure com problemas de depuração.

6. Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

7. Instale o Cloud-init (o agente de provisionamento) e o agente Linux do Azure (o manipulador de extensões de convidado). Cloud-init usa o netplan para configurar a configuração de rede do sistema durante o provisionamento e cada inicialização subsequente.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  O `walinuxagent` pacote pode remover o `NetworkManager` e `NetworkManager-gnome` pacotes, se estiverem instalados.

8. Remova as configurações padrão de Cloud-init e os artefatos restantes do netplan que podem entrar em conflito com o provisionamento de inicialização de nuvem no Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Configure Cloud-init para provisionar o sistema usando a fonte de origem do Azure:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Configure o agente Linux do Azure para contar com a inicialização de nuvem para executar o provisionamento. Confira o [projeto WALinuxAgent](https://github.com/Azure/WALinuxAgent) para obter mais informações sobre essas opções.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Limpar artefatos e logs de tempo de execução do agente do Azure e do Cloud-init:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    > [!NOTE]
    > O `sudo waagent -force -deprovision+user` comando tentará limpar o sistema e torná-lo adequado para reprovisionamento. A `+user` opção exclui a última conta de usuário provisionada e os dados associados.

    > [!WARNING]
    > O desprovisionamento usando o comando acima não garante que a imagem seja desmarcada de todas as informações confidenciais e seja adequada para redistribuição.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V.

14. O Azure aceita somente VHDs de tamanho fixo. Se o disco do sistema operacional da VM não for um VHD de tamanho fixo, use o `Convert-VHD` cmdlet do PowerShell e especifique a `-VHDType Fixed` opção. Consulte os documentos `Convert-VHD` aqui: [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd para o Azure, consulte [Criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).