---
title: Usar drivers do CSI (interface de armazenamento de contêiner) para discos do Azure no serviço kubernetes do Azure (AKS)
description: Saiba como usar os drivers da CSI (interface de armazenamento de contêiner) para discos do Azure em um cluster do AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178874"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usar os drivers da interface de armazenamento de contêiner de disco do Azure (CSI) no serviço kubernetes do Azure (AKS) (visualização)
O driver CSI (Azure Disk contêiner Storage interface) é um driver compatível com a [especificação CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usado pelo AKs (serviço kubernetes do Azure) para gerenciar o ciclo de vida dos discos do Azure.

O CSI é um padrão para expor sistemas de blocos e de armazenamento de arquivos arbitrários a cargas de trabalho em contêineres no kubernetes. Ao adotar e usar o CSI, o AKS pode escrever, implantar e iterar plug-ins para expor novos ou aprimorar sistemas de armazenamento existentes no kubernetes sem precisar tocar no código de kubernetes principal e aguardar seus ciclos de liberação.

Para criar um cluster AKS com suporte ao driver do CSI, consulte [habilitar drivers do CSI para discos do Azure e arquivos do Azure no AKs](csi-storage-drivers.md).

>[!NOTE]
> Os *drivers na árvore* referem-se aos drivers de armazenamento atuais que fazem parte do código principal do kubernetes versus os novos drivers do CSI, que são plug-ins.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Usar volumes persistentes do CSI com discos do Azure

Um [volume persistente](concepts-storage.md#persistent-volumes) (VP) representa um pedaço de armazenamento provisionado para uso com kubernetes pods. Um VP pode ser usado por um ou vários pods e pode ser provisionado de forma dinâmica ou estática. Este artigo mostra como criar dinamicamente PVs com discos do Azure para uso por um único pod em um cluster AKS. Para provisionamento estático, consulte [criar e usar manualmente um volume com discos do Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para obter mais informações sobre o Kubernetes, veja [Opções de armazenamento para aplicativos no AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Criar dinamicamente o Azure Disk PVs usando as classes de armazenamento internas

Uma classe de armazenamento é usada para definir como uma unidade de armazenamento é criada dinamicamente com um volume persistente. Para obter mais informações sobre classes de armazenamento kubernetes, consulte [classes de armazenamento kubernetes][kubernetes-storage-classes]. Quando você usa drivers de armazenamento do CSI em AKS, há dois outros internos `StorageClasses` que usam os drivers de armazenamento do Azure Disk CSI. As classes adicionais de armazenamento do CSI são criadas com o cluster juntamente com as classes de armazenamento padrão na árvore.

- `managed-csi`: Usa o Azure SSD Standard armazenamento com redundância local (LRS) para criar um disco gerenciado.
- `managed-csi-premium`: Usa o LRS Premium do Azure para criar um disco gerenciado.

A política de recuperação em ambas as classes de armazenamento garante que o disco subjacente do Azure seja excluído quando o respectivo PV for excluído. As classes de armazenamento também configuram o PVs para que seja expansível. Você só precisa editar o PVC (declaração de volume persistente) com o novo tamanho.

Para aproveitar essas classes de armazenamento, crie um [PVC](concepts-storage.md#persistent-volume-claims) e o respectivo Pod que os referencie e os utilize. Um PVC é usado para provisionar automaticamente o armazenamento com base em uma classe de armazenamento. Um PVC pode usar uma das classes de armazenamento criadas previamente ou uma classe de armazenamento definida pelo usuário para criar um disco gerenciado pelo Azure para o SKU e o tamanho desejados. Quando você cria uma definição de Pod, o PVC é especificado para solicitar o armazenamento desejado.

Crie um pod de exemplo e o respectivo PVC com o comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Depois que o Pod estiver no estado executando, crie um novo arquivo chamado `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Agora você pode validar que o disco está montado corretamente executando o seguinte comando e verificando se você vê o `test.txt` arquivo na saída:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Criar uma classe de armazenamento personalizada

As classes de armazenamento padrão se adaptam aos cenários mais comuns, mas não todas. Para alguns casos, talvez você queira ter sua própria classe de armazenamento personalizada com seus próprios parâmetros. Por exemplo, temos um cenário em que você talvez queira alterar a `volumeBindingMode` classe.

As classes de armazenamento padrão usam uma `volumeBindingMode: Immediate` classe que garante que ocorra imediatamente depois que o PVC for criado. Nos casos em que os pools de nós são restritos à topologia, por exemplo, usando zonas de disponibilidade, o PVs seria associado ou provisionado sem o conhecimento dos requisitos de agendamento do Pod (nesse caso, para estar em uma zona específica).

Para resolver esse cenário, você pode usar `volumeBindingMode: WaitForFirstConsumer` o, que atrasa a ligação e o provisionamento de um VP até que um pod que usa o PVC seja criado. Dessa forma, o PV estará em conformidade e será provisionado na zona de disponibilidade (ou em outra topologia) que é especificada pelas restrições de agendamento do pod.

Crie um arquivo chamado `sc-azuredisk-csi-waitforfirstconsumer.yaml` e cole o manifesto a seguir.
A classe de armazenamento é a mesma que nossa `managed-csi` classe de armazenamento, mas com uma `volumeBindingMode` classe diferente.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Crie a classe de armazenamento com o comando [kubectl Apply][kubectl-apply] e especifique o `sc-azuredisk-csi-waitforfirstconsumer.yaml` arquivo:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Instantâneos de volume

O driver do Azure Disk CSI dá suporte à criação [de instantâneos de volumes persistentes](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Como parte desse recurso, o driver pode executar instantâneos *completos* ou [ *incrementais*](../virtual-machines/disks-incremental-snapshots.md) , dependendo do valor definido no `incremental` parâmetro (por padrão, é true).

Para obter detalhes sobre todos os parâmetros, consulte [parâmetros de classe de instantâneo de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Criar um instantâneo de volume

Para obter um exemplo desse recurso, crie uma [classe de instantâneo de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) com o comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Agora, vamos criar um [instantâneo de volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) do PVC que [criamos dinamicamente no início deste tutorial](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes), `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Verifique se o instantâneo foi criado corretamente:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Criar um novo PVC com base em um instantâneo de volume

Você pode criar um novo PVC com base em um instantâneo de volume. Use o instantâneo criado na etapa anterior e crie um [novo PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) e um [novo pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) para consumi-lo.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Por fim, vamos verificar se é o mesmo PVC criado antes de verificar o conteúdo.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Como esperado, ainda podemos ver nosso arquivo criado anteriormente `test.txt` .

## <a name="clone-volumes"></a>Clonar volumes

Um volume clonado é definido como uma duplicata de um volume kubernetes existente. Para obter mais informações sobre como clonar volumes no kubernetes, consulte a documentação conceitual para a [clonagem de volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

O driver CSI para discos do Azure dá suporte à clonagem de volume. Para demonstrar, crie um [volume clonado](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) do [criado anteriormente](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` e [um novo pod para consumi-lo](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Agora podemos verificar o conteúdo do volume clonado executando o comando a seguir e confirmando que ainda vemos nosso `test.txt` arquivo criado.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Redimensionar um volume persistente

Em vez disso, você pode solicitar um volume maior para um PVC. Edite o objeto PVC e especifique um tamanho maior. Essa alteração dispara a expansão do volume subjacente que faz o backup do VP.

> [!NOTE]
> Um novo VP nunca é criado para atender à declaração. Em vez disso, um volume existente é redimensionado.

No AKS, a `managed-csi` classe de armazenamento interna já permite a expansão, portanto, use o [PVC criado anteriormente com essa classe de armazenamento](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). O PVC solicitou um volume persistente de 10 Gi. Podemos confirmar que, executando:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Atualmente, o driver do Azure Disk CSI dá suporte apenas a redimensionamento de PVCs sem pods associado (e o volume não montado em um nó específico).

Como tal, vamos excluir o Pod que criamos anteriormente:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Vamos expandir o PVC aumentando o `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Vamos confirmar se o volume agora é maior:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> O PVC não refletirá o novo tamanho até que ele tenha um pod associado a ele novamente.

Vamos criar um novo pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

E, por fim, confirme o tamanho do PVC e dentro do pod:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Disco compartilhado

Os [discos compartilhados do Azure](../virtual-machines/disks-shared.md) são um recurso do Azure Managed disks que permite anexar um disco do Azure a nós de agente simultaneamente. A anexação de um disco gerenciado a vários nós de agente permite, por exemplo, implantar novos aplicativos de cluster existentes ou migrar para o Azure.

> [!IMPORTANT] 
> Atualmente, somente o dispositivo de bloco bruto ( `volumeMode: Block` ) é suportado pelo driver CSI do Azure Disk. Os aplicativos devem gerenciar a coordenação e o controle de gravações, leituras, bloqueios, caches, montagens e isolamento no disco compartilhado, que é exposto como um dispositivo de bloco bruto.

Vamos criar um arquivo chamado `shared-disk.yaml` copiando o seguinte comando que contém a classe de armazenamento de disco compartilhado e o PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Crie a classe de armazenamento com o comando [kubectl Apply][kubectl-apply] e especifique o `shared-disk.yaml` arquivo:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Agora, vamos criar um arquivo chamado `deployment-shared.yml` copiando o seguinte comando:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Crie a implantação com o comando [kubectl Apply][kubectl-apply] e especifique o `deployment-shared.yml` arquivo:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Por fim, vamos verificar o dispositivo de bloco dentro do pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Contêineres do Windows

O driver do Azure Disk CSI também dá suporte a nós e contêineres do Windows. Se você quiser usar contêineres do Windows, siga o [tutorial contêineres do Windows](windows-container-cli.md) para adicionar um pool de nós do Windows.

Depois de ter um pool de nós do Windows, agora você pode usar as classes de armazenamento internas como `managed-csi` . Você pode implantar um [conjunto com estado baseado no Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) de exemplo que economiza carimbos de data/hora no arquivo `data.txt` implantando o comando a seguir com o comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Agora você pode validar o conteúdo do volume executando:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Próximas etapas

- Para saber como usar os drivers do CSI para arquivos do Azure, confira [usar os arquivos do Azure com drivers do CSI](azure-files-csi.md).
- Para obter mais informações sobre as práticas recomendadas de armazenamento, consulte [práticas recomendadas para armazenamento e backups no serviço kubernetes do Azure][operator-best-practices-storage].


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register