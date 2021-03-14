---
title: Entender o gerenciamento de armazenamento kubernetes no dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como o gerenciamento de armazenamento kubernetes ocorre em um dispositivo pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d848d663121474085935c68e62b8aa38f195ba8d
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442172"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-pro-gpu-device"></a>Gerenciamento de armazenamento kubernetes em seu dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Em seu dispositivo Azure Stack Edge pro, um cluster kubernetes é criado quando você configura a função de computação. Depois que o cluster kubernetes é criado, os aplicativos em contêineres podem ser implantados no cluster kubernetes em pods. Há maneiras distintas de fornecer armazenamento a pods no cluster kubernetes. 

Este artigo descreve os métodos para provisionar o armazenamento em um cluster kubernetes em geral e especificamente no contexto de seu dispositivo Azure Stack Edge pro. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Requisitos de armazenamento para pods kubernetes

Os pods Kubernetess são sem monitoração de estado, mas os aplicativos executados normalmente são monitorados. Como os pods podem ser de curta duração e reinicializar, fazer failover ou mover entre nós kubernetes, os requisitos a seguir devem ser atendidos para o armazenamento associado ao Pod. 

O armazenamento deve:

- Ao vivo fora do pod.
- Seja independente do ciclo de vida do pod.
- Estar acessível de todos os nós kubernetes.

Para entender como o armazenamento é gerenciado para kubernetes, é necessário entender dois recursos de API: 

- **PersistentVolume (PV)**: essa é uma parte do armazenamento no cluster kubernetes. O armazenamento kubernetes pode ser provisionado estaticamente como `PersistentVolume` . Ele também pode ser provisionado dinamicamente como  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: essa é uma solicitação de armazenamento por um usuário. Os PVCs consomem recursos VP. Os PVCs podem solicitar modos de tamanho e acesso específicos. 

    Como os usuários precisam de diversas `PersistentVolumes` Propriedades para diferentes problemas, portanto, os administradores de cluster precisam ser capazes de oferecer uma variedade de tipos `PersistentVolumes` que diferem de mais do que apenas os modos de tamanho e acesso. Para essas necessidades, você precisa do `StorageClass` recurso.

O provisionamento de armazenamento pode ser estático ou dinâmico. Cada um dos tipos de provisionamento é discutido nas seções a seguir.

## <a name="static-provisioning"></a>Provisionamento estático

Os administradores de cluster do Kubernetes podem provisionar estaticamente o armazenamento. Para fazer isso, eles podem usar o back-end de armazenamento com base em sistemas de arquivos SMB/NFS ou usar discos iSCSI que se conectam localmente pela rede em um ambiente local ou até mesmo usar arquivos do Azure ou discos do Azure na nuvem. Esse tipo de armazenamento não é provisionado por padrão e os administradores de cluster precisam planejar e gerenciar esse provisionamento. 
 
Aqui está um diagrama que descreve como o armazenamento provisionado estaticamente é consumido em kubernetes: 

![Provisionamento estático via PersistentVolumes](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

Ocorrem as seguintes etapas: 

1. **Provisionar o armazenamento**: o administrador do cluster provisiona o armazenamento. Neste exemplo, o administrador do cluster cria um ou mais compartilhamentos SMB que criam automaticamente objetos de volume persistentes no cluster kubernetes correspondente a esses compartilhamentos. 

1. **Armazenamento de declarações**: você envia uma implantação de PVC que solicita o armazenamento. Essa declaração de armazenamento é o PersistentVolumeClaim (PVC). Se o tamanho e o modo de acesso do PV corresponderem ao do PVC, o PVC será associado ao VP. O PVC e o PV mapeiam um-para-um.

1. **Montar o PVC para o contêiner**: depois que o PVC estiver associado ao PV, você poderá montar esse PVC em um caminho em seu contêiner. Quando a lógica do aplicativo no contêiner lê/grava de/para esse caminho, os dados são gravados no armazenamento SMB.
 

## <a name="dynamic-provisioning"></a>Provisionamento dinâmico

Aqui está um diagrama que descreve como o armazenamento provisionado estaticamente é consumido em kubernetes: 

![Provisionamento dinâmico via StorageClasses](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

Ocorrem as seguintes etapas: 

1. **Definir classe de armazenamento**: o administrador de cluster define uma classe de armazenamento dependendo do ambiente operacional do cluster kubernetes. O administrador de cluster também implanta um provisionamento, que ainda é outro Pod ou aplicativo implantado no cluster kubernetes. O provisionador tem todos os detalhes para provisionar os compartilhamentos dinamicamente.  

1. **Armazenamento de declarações**: você envia um aplicativo que alegaria o armazenamento. Depois que um PVC é criado com essa referência de classe de armazenamento, o provisionamento é invocado. 

1. **Provisionar o armazenamento dinamicamente**: o provisionamento cria dinamicamente o compartilhamento associado ao armazenamento em disco local. Depois que o compartilhamento é criado, ele também cria um objeto VP que corresponde automaticamente a esse compartilhamento.

1. **Montar PVC para contêiner**: depois que o PVC estiver associado ao PV, você poderá montar o PVC no contêiner em um caminho da mesma forma que o provisionamento estático e leitura ou gravação no compartilhamento.


## <a name="storage-provisioning-on-azure-stack-edge-pro"></a>Provisionamento de armazenamento no Azure Stack Edge pro

No dispositivo Azure Stack Edge pro, o provisionamento estático `PersistentVolumes` é criado usando os recursos de armazenamento do dispositivo. Quando você provisiona um compartilhamento e **usa a opção compartilhar com a computação de borda** está habilitada, essa ação cria um recurso VP automaticamente no cluster kubernetes.

![Criação de compartilhamento local em portal do Azure para provisionamento estático](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

Para usar a camada de nuvem, você pode criar um compartilhamento de nuvem de borda com a opção usar o compartilhamento com a computação de borda habilitada. Um PV é novamente criado automaticamente para esse compartilhamento. Todos os dados do aplicativo gravados no compartilhamento de borda são em camadas para a nuvem. 

![Criação de compartilhamento de nuvem em portal do Azure para provisionamento estático](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

Você pode criar compartilhamentos SMB e NFS para provisionar estaticamente o PVs no dispositivo pro Azure Stack Edge. Depois que o PV for provisionado, você enviará um PVC para reivindicar esse armazenamento. Aqui está um exemplo de uma implantação de PVC `yaml` que declara o armazenamento e usa os compartilhamentos que você provisionou.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

Para obter mais informações, consulte [implantar um aplicativo com estado por meio de provisionamento estático em seu Azure Stack Edge pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Para acessar o mesmo armazenamento provisionado estaticamente, as opções de montagem de volume correspondentes para associações de armazenamento para IoT são as seguintes. O `/home/input` é o caminho no qual o volume pode ser acessado dentro do contêiner.

```
{
"HostConfig": {
"Mounts": [
{
"Target": "/home/input",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
},
{
"Target": "/home/output",
"Source": "<nfs-or-smb-share-name-here>",
"Type": "volume"
}]
}
}
```

Azure Stack o Edge pro também tem um Builtin `StorageClass` chamado `ase-node-local` que usa um armazenamento em disco de dados anexado ao nó kubernetes. Isso `StorageClass` dá suporte ao provisionamento dinâmico. Você pode fazer uma `StorageClass` referência nos aplicativos pod e um VP é criado automaticamente para você. Para obter mais informações, consulte o [painel do kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) para consultar `ase-node-local StorageClass` .

![Classe de armazenamento interna no painel do kubernetes](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

Para obter mais informações, consulte [implantar um aplicativo com estado por meio de provisionamento dinâmico em seu Azure Stack Edge pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).

## <a name="choose-storage-type"></a>Escolher tipo de armazenamento

Talvez seja necessário escolher o tipo de armazenamento dependendo da carga de trabalho que você está implantando. 

- Se você quiser `ReadWriteMany` o modo de acesso para seu `PersistentVolumes` local em que os volumes são montados como leitura-gravação por vários nós implantando, use o provisionamento estático para os compartilhamentos SMB/NFS.

- Se os aplicativos que você está implantando tiverem um requisito de conformidade POSIX, por exemplo, aplicativos como MongoDB, PostgreSQL, MySQL ou Prometheus, use o StorageClass interno. Os modos de acesso são `ReadWriteOnce` ou o volume é montado como leitura/gravação por um único nó. 


Para obter mais informações sobre modos de acesso, consulte [modo de acesso de volumes kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes).


## <a name="next-steps"></a>Próximas etapas

Para entender como você pode provisionar estaticamente um `PersistentVolume` , consulte:

- [Implante um aplicativo com estado por meio de provisionamento estático em seu Azure Stack Edge pro via kubectl](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

Para saber como você pode provisionar dinamicamente um `StorageClass` , consulte:

- [Implante um aplicativo com estado por meio de provisionamento dinâmico em seu Azure Stack Edge pro via kuebctl](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md).
