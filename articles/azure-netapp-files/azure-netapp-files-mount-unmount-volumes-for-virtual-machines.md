---
title: Montar Azure NetApp Files volumes para máquinas virtuais
description: Saiba como montar ou desmontar um volume para máquinas virtuais do Windows ou máquinas virtuais do Linux no Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842250"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux 

Você pode montar ou desmontar um volume para máquinas virtuais do Windows ou do Linux conforme necessário.  As instruções de montagem para máquinas virtuais do Linux estão disponíveis no Azure NetApp Files.  

## <a name="requirements"></a>Requisitos 

* Você deve ter pelo menos uma política de exportação para poder acessar um volume do NFS.
* Para montar um volume NFS com êxito, verifique se as seguintes portas NFS estão abertas entre o cliente e os volumes de NFS:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (somente NFSv3)
    * 4046 TCP/UDP = `status` (somente NFSv3)

## <a name="steps"></a>Etapas

1. Clique na folha **volumes** e selecione o volume para o qual você deseja montar. 
2. Clique em **instruções de montagem** do volume selecionado e siga as instruções para montar o volume. 

    ![Instruções de montagem NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instruções de montagem SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Se você estiver montando um volume NFS, certifique-se de usar a `vers` opção no `mount` comando para especificar a versão do protocolo NFS que corresponde ao volume que você deseja montar. 
    * Se você estiver usando o NFSv 4.1, use o seguinte comando para montar o sistema de arquivos:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Se você usar o NFSv 4.1, verifique se todas as VMs montando a exportação usam nomes de host exclusivos.

3. Se você quiser que um volume NFS seja montado automaticamente quando uma VM do Azure for iniciada ou reiniciada, adicione uma entrada ao `/etc/fstab` arquivo no host. 

    Por exemplo: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` é o endereço IP do volume de Azure NetApp Files encontrado na folha de propriedades do volume.
    * `$FILEPATH` é o caminho de exportação do volume de Azure NetApp Files.
    * `$MOUNTPOINT` é o diretório criado no host do Linux usado para montar a exportação do NFS.

4. Se você quiser montar o volume para o Windows usando NFS:

    a. Monte o volume em uma VM UNIX ou Linux primeiro.  
    b. Execute um `chmod 777` `chmod 775` comando ou no volume.  
    c. Monte o volume por meio do cliente NFS no Windows.
    
5. Se você quiser montar um volume Kerberos do NFS, consulte [Configurar a criptografia do nfsv 4.1](configure-kerberos-encryption.md) para obter detalhes adicionais. 

## <a name="next-steps"></a>Próximas etapas

* [Configurar o domínio padrão do NFSv4.1 para o Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Perguntas frequentes sobre NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Visão geral do Network File System](/windows-server/storage/nfs/nfs-overview)
* [Montar um volume Kerberos do NFS](configure-kerberos-encryption.md#kerberos_mount)
