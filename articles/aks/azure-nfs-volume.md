---
title: Criar volume do servidor de Ubuntu Linux NFS
titleSuffix: Azure Kubernetes Service
description: Saiba como criar manualmente um volume do servidor de Ubuntu Linux NFS para uso com o pods no serviço kubernetes do Azure (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86251987"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Criar e usar manualmente um volume de servidor NFS (Network File System) do Linux com o serviço de kubernetes do Azure (AKS)
O compartilhamento de dados entre contêineres é geralmente um componente necessário de serviços e aplicativos baseados em contêiner. Normalmente, você tem vários pods que precisam acessar as mesmas informações em um volume persistente externo.    
Embora os arquivos do Azure sejam uma opção, a criação de um servidor NFS em uma VM do Azure é outra forma de armazenamento compartilhado persistente. 

Este artigo mostrará como criar um servidor NFS em uma máquina virtual Ubuntu. Além disso, dê acesso aos seus contêineres do AKS a esse sistema de arquivos compartilhado.

## <a name="before-you-begin"></a>Antes de começar
Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Seu cluster AKS precisará residir em redes virtuais iguais ou emparelhadas como o servidor NFS. O cluster deve ser criado em uma VNET existente, que pode ser a mesma VNET que a VM.

As etapas para configurar o com uma VNET existente são descritas na documentação: [criando o cluster AKs na VNET existente][aks-virtual-network] e [conectando redes virtuais com emparelhamento VNET][peer-virtual-networks]

Ele também pressupõe que você criou uma máquina virtual Ubuntu Linux (por exemplo, 18, 4 LTS). As configurações e o tamanho podem ser de sua preferência e podem ser implantados por meio do Azure. Para o início rápido do Linux, consulte [Gerenciamento de VM do Linux][linux-create].

Se você implantar o cluster AKS primeiro, o Azure preencherá automaticamente o campo rede virtual ao implantar seu computador Ubuntu, tornando-os ativos na mesma VNET. Mas se você quiser trabalhar com redes emparelhadas, consulte a documentação acima.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Implantando o servidor NFS em uma máquina virtual
Este é o script para configurar um servidor NFS em sua máquina virtual Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
O servidor será reiniciado (por causa do script) e você poderá montar o servidor NFS para AKS.

>[!IMPORTANT]  
>Certifique-se de substituir o **AKS_SUBNET** com o correto do cluster ou, "*", abrirá o servidor NFS para todas as portas e conexões.

Depois de criar sua VM, copie o script acima em um arquivo. Em seguida, você pode movê-lo do computador local, ou onde quer que o script esteja, na VM usando: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Depois que o script estiver em sua VM, você poderá realizar o ssh na VM e executá-lo por meio do comando:
```console
sudo ./nfs-server-setup.sh
```
Se sua execução falhar devido a um erro de permissão negada, defina a permissão de execução por meio do comando:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Conectando o cluster AKS ao servidor NFS
Podemos conectar o servidor NFS ao nosso cluster provisionando um volume persistente e uma declaração de volume persistente que especifica como acessar o volume.

É necessário conectar os dois serviços nas mesmas ou nas redes virtuais emparelhadas. As instruções para configurar o cluster na mesma VNET estão aqui: [criando um cluster AKs na VNET existente][aks-virtual-network]

Quando estiverem na mesma rede virtual (ou emparelhada), você precisará provisionar um volume persistente e uma declaração de volume persistente em seu cluster AKS. Os contêineres podem montar a unidade NFS em seu diretório local.

Aqui está um exemplo de definição de kubernetes para o volume persistente (essa definição assume que o cluster e a VM estão na mesma VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Substitua **NFS_INTERNAL_IP**, **NFS_NAME** e **NFS_EXPORT_FILE_PATH** com informações do servidor NFS.

Você também precisará de um arquivo de declaração de volume persistente. Aqui está um exemplo do que incluir:

>[!IMPORTANT]  
>**"storageClassName"** precisa permanecer uma cadeia de caracteres vazia ou a declaração não funcionará.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Solução de problemas
Se você não puder se conectar ao servidor de um cluster, um problema poderá ser o diretório exportado, ou seu pai, não terá permissões suficientes para acessar o servidor.

Verifique se o diretório de exportação e seu diretório pai têm permissões de 777.

Você pode verificar as permissões executando o comando a seguir e os diretórios devem ter permissões de *' drwxrwxrwx '* :
```console
ls -l
```

## <a name="more-information"></a>Mais informações
Para obter uma explicação completa ou para ajudá-lo a depurar a configuração do servidor NFS, aqui está um tutorial detalhado:
  - [Tutorial de NFS][nfs-tutorial]

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, veja [Práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
