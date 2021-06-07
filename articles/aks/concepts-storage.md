---
title: Conceitos - Armazenamento nos Serviços do Kubernetes do Azure (AKS)
description: Saiba mais sobre o Armazenamento nos Serviços do Kubernetes do Azure (AKS), incluindo volumes, volumes persistentes, classes de armazenamento e declarações
services: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: bf910c66694a62505f259c0a95a88f7dfed05d19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98127950"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções de armazenamento para aplicativos no Serviço de Kubernetes do Azure (AKS)

Aplicativos que são executados nos Serviços do Kubernetes do Azure (AKS) ) podem ser necessários para armazenar e recuperar dados. Para algumas cargas de trabalho do aplicativo, esse armazenamento de dados pode usar o armazenamento local rápido no nó que não é mais necessário quando os pods são excluídos. Outras cargas de trabalho de aplicativo podem exigir armazenamento persistente em volumes de dados mais regulares dentro da plataforma do Azure. Vários pods talvez precisem compartilhar os mesmo volumes de dados, ou anexar novamente os volumes de dados se o pod for reagendado em um nó diferente. Por fim, você precisa injetar dados confidenciais ou informações de configuração do aplicativo em pods.

![Opções de armazenamento para aplicativos em um cluster no Serviço de Kubernetes do Azure (AKS)](media/concepts-storage/aks-storage-options.png)

Este artigo apresenta os principais conceitos que fornecem armazenamento para seus aplicativos no AKS:

- [Volumes](#volumes)
- [Volumes persistentes](#persistent-volumes)
- [Classes de armazenamento](#storage-classes)
- [Declarações de volume persistente](#persistent-volume-claims)

## <a name="volumes"></a>Volumes

Geralmente, os aplicativos precisam ser capazes de armazenar e recuperar dados. Como o Kubernetes normalmente trata pods individuais como recursos descartáveis e efêmeros, estão disponíveis abordagens diferentes para uso de aplicativos e para manter os dados conforme necessário. Um *volume* representa uma maneira de armazenar, recuperar e persistir dados entre os pods e por meio do ciclo de vida do aplicativo.

Volumes tradicionais para armazenar e recuperar os dados são criados como recursos de Kubernetes apoiados pelo Armazenamento do Microsoft Azure. Manualmente, você pode criar esses volumes de dados a serem atribuído aos pods diretamente ou fazer com que os Kubernetes criem-nos automaticamente. Esses volumes de dados podem usar discos o Azure Disks ou os Arquivos do Azure:

- *Azure Disks* pode ser usado para criar um recurso de Kubernetes *DataDisk*. Os discos podem usar o armazenamento Premium do Azure, apoiados por SSDs de alto desempenho, ou o armazenamento Standard do Azure, apoiado por HDDs regulares. Para a maioria das cargas de trabalho de desenvolvimento e produção, use o Armazenamento Premium. Os discos do Azure são montados como *ReadWriteOnce*, portanto, estão disponíveis apenas para um único Pod. Para volumes de armazenamento que podem ser acessados por vários pods simultaneamente, use os arquivos do Azure.
- *Os Arquivos do Azure* podem ser usados para montar um compartilhamento SMB 3.0 de apoio de uma conta de Armazenamento do Microsoft Azure com os pods. Os arquivos permitem que você compartilhe dados em vários nós e pods. Os arquivos podem usar o armazenamento standard do Azure apoiado por HDDs regulares ou pelo armazenamento Premium do Azure, apoiado por SSDs de alto desempenho.

No Kubernetes, os volumes podem representar mais do que apenas um disco tradicional onde as informações podem ser armazenadas e recuperadas. Volumes Kubernetes também podem ser usados como uma forma de injetar dados em um pod para uso pelos contêineres. Tipos comuns de volume adicional no Kubernetes incluem:

- *emptyDir* - este volume normalmente é usado como espaço temporário para um pod. Todos os contêineres dentro de um pod podem acessar os dados no volume. Os dados gravados para esse tipo de volume persistem somente para o tempo de vida do pod - quando o pod é excluído, o volume é excluído. Esse volume normalmente usa o armazenamento em disco do nó local subjacente, embora ele também possa existir apenas na memória do nó.
- *segredo* - esse volume é usado para injetar dados confidenciais em pods, como senhas. Você primeiro criae um segredo usando a API do Kubernetes. Quando você define seu pod ou implantação, um segredo específico pode ser solicitado. Segredos são fornecidos apenas para nós que têm um pod agendado que precisa dele, e o segredo é armazenado no *tmpfs*, não gravado no disco. Quando o último pod em um nó que exige um segredo for excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados dentro de um determinado namespace e só podem ser acessados pelo pods no mesmo namespace.
- *configMap* - esse tipo de volume é usado para injetar as propriedades de par chave-valor no pods, como informações de configuração do aplicativo. Em vez de definir informações de configuração de aplicativo dentro de uma imagem de contêiner, você pode defini-lo como um recurso Kubernetes que pode ser facilmente atualizado e aplicado a novas instâncias de pods conforme são implantadas. Como usar um segredo, você primeiro cria um ConfigMap usando a API do Kubernetes. Este ConfigMap, em seguida, pode ser solicitado quando você defininir um pod ou implantação. Os ConfigMaps são armazenados dentro de um determinado namespace e só podem ser acessados pelo pods no mesmo namespace.

## <a name="persistent-volumes"></a>Volumes persistentes

Os volumes definidos e criados como parte do ciclo de vida de pod existem somente até o pod ser excluído. Pods geralmente esperam que seu armazenamento permaneça se um pod ser reagendado em um host diferente durante um evento de manutenção, especialmente em StatefulSets. Um *volume persistente* (VP) é um recurso de armazenamento criado e gerenciado pela API do Kubernetes que pode existir além do tempo de vida de um pod individual.

Arquivos ou discos do Azure são usados para fornecer o PersistentVolume. Conforme observado na seção anterior em Volumes, a escolha de discos ou arquivos geralmente é determinada pela necessidade de acesso simultâneo a dados ou o nível de desempenho.

![Volumes persistentes em um cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volumes.png)

Um PersistentVolume pode ser *estaticamente* criado por um administrador de cluster, ou *dinamicamente* criado pelo servidor de API do Kubernetes. Se um pod for agendado e as solicitações de armazenamento que não estiverem disponíveis no momento, o Kubernetes pode criar o armazenamento de arquivos ou o disco do Azure subjacente e anexá-lo ao pod. A dinâmica de provisionamento usa um *StorageClass* para identificar qual tipo de armazenamento do Azure precisa ser criado.

## <a name="storage-classes"></a>Classes de armazenamento

Para definir diferentes camadas de armazenamento, como Premium e Standard, você pode criar uma *StorageClass*. O StorageClass também define a *reclaimPolicy*. Este reclaimPolicy controla o comportamento do recurso de armazenamento do Azure subjacente quando o pod é excluído e o volume persistente talvez não seja mais necessário. O recurso de armazenamento subjacente pode ser excluído ou retido para uso com um pod futuro.

No AKS, quatro iniciais `StorageClasses` são criadas para cluster usando os plug-ins de armazenamento na árvore:

- `default` -Usa o armazenamento StandardSSD do Azure para criar um disco gerenciado. A política de recuperação garante que o disco subjacente do Azure seja excluído quando o volume persistente que o usou for excluído.
- `managed-premium` -Usa o armazenamento Premium do Azure para criar um disco gerenciado. A política de recuperação novamente garante que o disco subjacente do Azure seja excluído quando o volume persistente que o utilizou for excluído.
- `azurefile` -Usa o armazenamento standard do Azure para criar um compartilhamento de arquivos do Azure. A política de recuperação garante que o compartilhamento de arquivos do Azure subjacente seja excluído quando o volume persistente que o usou for excluído.
- `azurefile-premium` -Usa o armazenamento Premium do Azure para criar um compartilhamento de arquivos do Azure. A política de recuperação garante que o compartilhamento de arquivos do Azure subjacente seja excluído quando o volume persistente que o usou for excluído.

Para clusters que usam os novos plug-ins externos da CSI (interface de armazenamento de contêiner) (versão prévia), são criados os seguintes adicionais `StorageClasses` :
- `managed-csi` -Usa o LRS (armazenamento com redundância local) do Azure StandardSSD para criar um disco gerenciado. A política de recuperação garante que o disco subjacente do Azure seja excluído quando o volume persistente que o usou for excluído. A classe de armazenamento também configura os volumes persistentes para que sejam expansíveis, você só precisa editar a declaração de volume persistente com o novo tamanho.
- `managed-csi-premium` -Usa o LRS (armazenamento com redundância local) Premium do Azure para criar um disco gerenciado. A política de recuperação novamente garante que o disco subjacente do Azure seja excluído quando o volume persistente que o utilizou for excluído. Da mesma forma, essa classe de armazenamento permite que volumes persistentes sejam expandidos.
- `azurefile-csi` -Usa o armazenamento standard do Azure para criar um compartilhamento de arquivos do Azure. A política de recuperação garante que o compartilhamento de arquivos do Azure subjacente seja excluído quando o volume persistente que o usou for excluído.
- `azurefile-csi-premium` -Usa o armazenamento Premium do Azure para criar um compartilhamento de arquivos do Azure. A política de recuperação garante que o compartilhamento de arquivos do Azure subjacente seja excluído quando o volume persistente que o usou for excluído.

Se nenhum StorageClass for especificado para um volume persistente, o padrão StorageClass será usado. Tome cuidado ao solicitar volumes persistentes, para que eles usem o armazenamento adequado, que você precisa. Você pode criar um StorageClass para necessidades adicionais usando `kubectl`. O exemplo a seguir usa o Managed Disks Premium e especifica que o Azure Disk subjacente deve ser *retidos* quando o pod é excluído:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> O AKS reconcilia as classes de armazenamento padrão e substituirá as alterações feitas nessas classes de armazenamento.

## <a name="persistent-volume-claims"></a>Declarações de volume persistente

Solicitações PersistentVolumeClaim em armazenamento de Disco ou Arquivo de um StorageClass particular, modo de acesso e tamanho. O servidor de API do Kubernetes pode provisionar dinamicamente o recurso de armazenamento subjacente no Azure se não houver nenhum recurso existente para atender a declaração com base no StorageClass definido. A definição de pod inclui a montagem de volume depois que o volume for conectado no pod.

![Declarações de volumes persistentes em um cluster de serviços de Kubernetes do Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

É um PersistentVolume *vinculado* a um PersistentVolumeClaim depois de atribuir a um recurso de armazenamento disponível para o pod solicitá-lo. Há um mapeamento 1:1 de volumes persistentes para declarações.

O manifesto YAML do exemplo a seguir mostra uma declaração de volume persistente que usa o *Storage Class* premium gerenciado e solicita um Disco *Gi 5* de tamanho:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Quando você cria uma definição de pod, a declaração de volume persistente é especificada para solicitar o armazenamento desejado. Você também especifica o *volumeMount* para seus aplicativos lerem e gravar dados. O manifesto YAML do exemplo a seguir mostra como a declaração anterior de volume persistente pode ser usada para montar um volume em */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Para montar um volume em um contêiner do Windows, especifique a letra da unidade e o caminho. Por exemplo:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Próximas etapas

Para obter as práticas recomendadas associadas, veja [Práticas recomendadas para armazenamento e backups no AKS][operator-best-practices-storage].

Para ver como criar volumes dinâmicos e estáticos que usam Discos do Azure ou Arquivos do Azure, consulte os artigos de instruções a seguir:

- [Criar um volume estático usando os Discos do Azure][aks-static-disks]
- [Criar um volume estático usando os Arquivos do Azure][aks-static-files]
- [Criar um volume estático usando os Discos do Azure][aks-dynamic-disks]
- [Criar um volume estático usando os Arquivos do Azure][aks-dynamic-files]

Para obter informações adicionais sobre os principais conceitos do Kubernetes e do AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identidade do Kubernetes/AKS][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Redes virtuais do Kubernetes/AKS][aks-concepts-network]
- [Escala do Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
