---
title: Solucionar problemas de uma VM do Azure com falha usando a virtualização aninhada no Azure | Microsoft Docs
description: Como solucionar um problema de VM do Azure usando a virtualização aninhada no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: f950cb63b5083a85ab5420434abdd9a720115b1a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734541"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Solucionar problemas de uma VM do Azure com falha usando a virtualização aninhada no Azure

Este artigo mostra como criar um ambiente de virtualização aninhado no Microsoft Azure, para que você possa montar o disco da VM com falha no host do Hyper-V (VM de resgate) para fins de solução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar a VM com falha, a VM de resgate deve usar o mesmo tipo de conta de armazenamento (Standard ou Premium) que a VM com falha.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Etapa 1: criar uma VM de resgate e instalar a função Hyper-V

1.  Criar nova VM de resgate:

    -  Sistema operacional: Windows Server 2016 Datacenter

    -  Tamanho: qualquer série V3 com pelo menos dois núcleos que dão suporte à virtualização aninhada. Para obter mais informações, consulte [Introdução aos novos tamanhos de VM Dv3 e Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesmo local, conta de armazenamento e grupo de recursos que a VM com falha.

    -  Selecione o mesmo tipo de armazenamento que a VM com falha (Standard ou Premium).

2.  Após a VM de resgate ser criada, acesse a VM de resgate via área de trabalho remota.

3.  Em Gerenciador do servidor, selecione **gerenciar**  >  **adicionar funções e recursos**.

4.  Na seção **Tipo de Instalação**, selecione **Instalação baseada em função ou recurso**.

5.  Na seção **Selecionar servidor de destino**, verifique se a VM de resgate está selecionada.

6.  Selecione a **função Hyper-V**  >  **Adicionar recursos**.

7.  Selecione **Próximo** na seção **Recursos**.

8.  Se um comutador virtual estiver disponível, selecione-o. Caso contrário, selecione **Avançar**.

9.  Na seção **Migração**, selecione **Avançar**

10. Na seção **Repositórios Padrão**, selecione **Avançar**.

11. Marque a caixa para reiniciar o servidor automaticamente se necessário.

12. Selecione **Instalar**.

13. Permita que o servidor instale a função Hyper-V. Isso leva alguns minutos e o servidor será reiniciado automaticamente.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Etapa 2: criar a VM defeituosa no servidor Hyper-V da VM de resgate

1.  [Crie um disco de instantâneo](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) para o disco do sistema operacional da VM que tem o problema e anexe o disco de instantâneo à VM recuse.

2.  Área de trabalho remota para a VM de resgate.

3.  Abra o Disk Management (diskmgmt.msc). Verifique se o disco da VM com falha está definido como **offline**.

4.  Abra o Gerenciador do Hyper-V: em **Gerenciador do Servidor**, selecione a **Função do Hyper-V**. Clique com o botão direito do mouse no servidor e, em seguida, selecione o **Gerenciador do Hyper-V**.

5.  No Gerenciador do Hyper-V, clique com o botão direito do mouse na VM de resgate e selecione **nova**  >  **máquina virtual**  >  **Avançar**.

6.  Digite um nome para a VM e, em seguida, selecione **Avançar**.

7.  Selecione **Geração 1**.

8.  Defina a memória de inicialização em 1.024 MB ou mais.

9. Se aplicável, selecione o comutador de rede do Hyper-V que foi criado. Caso contrário, vá para a próxima página.

10. Selecione **Anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a opção Anexar um disco rígido virtual posteriormente](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Selecione **Concluir** quando a VM é criada.

12. Clique com o botão direito do mouse na VM que você criou e, em seguida, selecione **Configurações**.

13. Selecione o **Controlador IDE 0**, selecione **Disco Rígido** e, em seguida, clique em **Adicionar**.

    ![a imagem sobre adicionar novo disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Em **disco rígido físico**, selecione o disco da VM com falha que você ANEXOU à VM do Azure. Se você não vê todos os discos listados, verifique se o disco está definido como offline usando o gerenciamento de disco.

    ![a imagem sobre montar o disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecione **Aplicar** e, depois, **OK**.

16. Clique duas vezes na VM e, em seguida, inicie-a.

17. Agora você pode trabalhar na VM como a VM local. Você poderá seguir as etapas de solução de problemas que forem necessárias.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Etapa 3: substituir o disco do sistema operacional usado pela VM com falha

1.  Depois de colocar a VM novamente online, desligue-a no Gerenciador do Hyper-V.

2.  [Desmonte e desanexe o disco do sistema operacional reparado](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk
).
3.  [Substitua o disco do sistema operacional usado pela VM pelo disco do sistema operacional reparado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Próximas etapas

Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](troubleshoot-rdp-connection.md) (Solucionar conexões RDP a uma VM do Azure). Para problemas com o acesso a aplicativos executados na VM, consulte [Solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).
