---
title: Use kubectl para implantar o aplicativo com estado kubernetes por meio do compartilhamento provisionado estaticamente no dispositivo pro Edge Azure Stack | Microsoft Docs
description: Descreve como criar e gerenciar uma implantação de aplicativo com estado kubernetes por meio de um compartilhamento provisionado estaticamente usando o kubectl em um dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610477"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Use kubectl para executar um aplicativo com estado kubernetes com um PersistentVolume no dispositivo pro Edge Azure Stack

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra como implantar um aplicativo com estado de instância única no kubernetes usando um PersistentVolume (PV) e uma implantação. A implantação usa `kubectl` comandos em um cluster kubernetes existente e implanta o aplicativo MySQL. 

Esse procedimento destina-se a aqueles que revisaram o [armazenamento kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md) e estão familiarizados com os conceitos do [armazenamento kubernetes](https://kubernetes.io/docs/concepts/storage/). 

O Azure Stack Edge pro também dá suporte à execução de contêineres do Azure SQL Edge e eles podem ser implantados de forma semelhante, conforme detalhado aqui para o MySQL. Para obter mais informações, consulte [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar o aplicativo com estado, conclua os seguintes pré-requisitos no seu dispositivo e o cliente que será usado para acessar o dispositivo:

### <a name="for-device"></a>Para dispositivo

- Você tem credenciais de entrada para um dispositivo Azure Stack Edge pro de 1 nó.
    - O dispositivo está ativado. Consulte [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    - O dispositivo tem a função de computação configurada por meio de portal do Azure e tem um cluster kubernetes. Consulte [Configurar computação](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Para cliente que acessa o dispositivo

- Você tem um sistema cliente Windows que será usado para acessar o dispositivo Azure Stack Edge pro.
    - O cliente está executando o Windows PowerShell 5,0 ou posterior. Para baixar a versão mais recente do Windows PowerShell, acesse [instalar o Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Você também pode ter qualquer outro cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Este artigo descreve o procedimento ao usar um cliente do Windows. 
    
    - Você concluiu o procedimento descrito em [acessar o cluster kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Você:
      - Criou um `userns1` namespace por meio do `New-HcsKubernetesNamespace` comando. 
      - Um usuário foi criado `user1` por meio do `New-HcsKubernetesUser` comando. 
      - Concedeu o `user1` acesso ao `userns1` por meio do `Grant-HcsKubernetesNamespaceAccess` comando.       
      - Instalado `kubectl` no cliente e salvo o `kubeconfig` arquivo com a configuração do usuário para C: \\ Users \\ &lt; username &gt; \\ . Kube. 
    
    - Verifique se a `kubectl` versão do cliente está distorcida sem mais de uma versão da versão mestre do kubernetes em execução no dispositivo pro Edge Azure Stack. 
        - Use `kubectl version` para verificar a versão do kubectl em execução no cliente. Anote a versão completa.
        - Na interface do usuário local do seu dispositivo Azure Stack Edge pro, acesse **visão geral** e anote o número do software kubernetes. 
        - Verifique essas duas versões para compatibilidade com o mapeamento fornecido na versão do kubernetes com suporte.<!-- insert link--> 


Você está pronto para implantar um aplicativo com estado em seu dispositivo Azure Stack Edge pro. 

## <a name="provision-a-static-pv"></a>Provisionar um PV estático

Para provisionar estaticamente um PV, você precisa criar um compartilhamento em seu dispositivo. Siga estas etapas para provisionar um VP em relação ao compartilhamento SMB. 

> [!NOTE]
> - O exemplo específico usado neste artigo de instruções não funciona com compartilhamentos NFS. Em geral, os compartilhamentos NFS podem ser provisionados em seu dispositivo de borda Azure Stack com aplicativos que não são de banco de dados.
> - Para implantar aplicativos com estado que usam volumes de armazenamento para fornecer armazenamento persistente, recomendamos que você use o `StatefulSet` . Este exemplo usa `Deployment` com apenas uma réplica e é adequado para desenvolvimento e teste. 

1. Escolha se deseja criar um compartilhamento de borda ou um compartilhamento local de borda. Siga as instruções em [Adicionar um compartilhamento](azure-stack-edge-manage-shares.md#add-a-share) para criar um compartilhamento. Certifique-se de marcar a caixa de seleção para **usar o compartilhamento com a computação de borda**.

    ![Compartilhamento local de borda para VP](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Em vez de criar um novo compartilhamento, se você decidir usar um compartilhamento existente, será necessário montar o compartilhamento.
    
        No portal do Azure para o recurso de borda do Azure Stack, vá para **compartilhamentos**. Na lista de compartilhamentos existentes, selecione e clique em um compartilhamento que você deseja usar.

        ![Selecione um compartilhamento local existente para VP](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Selecione **montar** e confirmar montagem quando solicitado.  

        ![Montar um compartilhamento local existente para VP](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Anote o nome do compartilhamento. Quando esse compartilhamento é criado, um objeto de volume persistente é criado automaticamente no cluster kubernetes correspondente ao compartilhamento SMB que você criou. 

## <a name="deploy-mysql"></a>Implantar MySQL

Agora você executará um aplicativo com estado criando uma implantação do kubernetes e conectando-o ao PV criado na etapa anterior usando um PVC (PersistentVolumeClaim). 

Todos os `kubectl` comandos que você usa para criar e gerenciar implantações de aplicativo com estado precisam especificar o namespace associado à configuração. Para especificar o namespace em um comando kubectl, use `kubectl <command> -n <your-namespace>` .

1. Obtenha uma lista de pods em execução no cluster kubernetes em seu namespace. Um pod é um contêiner de aplicativo, ou processo, em execução no cluster kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Aqui está um exemplo de uso de comando:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   A saída deve indicar que nenhum recurso (PODS) foi encontrado porque não há aplicativos em execução no cluster.

1. Você usará os seguintes arquivos YAML. O `mysql-deployment.yml` arquivo descreve uma implantação que executa o MySQL e faz referência ao PVC. O arquivo define uma montagem de volume para o `/var/lib/mysql` e, em seguida, cria um PVC que procura um volume de 20 GB. 

    Essa declaração é satisfeita por qualquer PV existente que foi provisionado estaticamente quando você criou o compartilhamento na etapa anterior. Em seu dispositivo, um grande PV de 32 TB é criado para cada compartilhamento. O VP atende aos requisitos estabelecidos pelo PVC e o PVC deve estar associado a este VP.

    Copie e salve o `mysql-deployment.yml` arquivo a seguir em uma pasta no cliente Windows que você está usando para acessar o dispositivo Azure Stack Edge pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Copie e salve como um `mysql-pv.yml` arquivo na mesma pasta em que você salvou o `mysql-deployment.yml` . Para usar o compartilhamento SMB que você criou anteriormente com o `kubectl` , defina o `volumeName` campo no objeto PVC como o nome do compartilhamento. 

    > [!NOTE] 
    > Certifique-se de que os arquivos YAML tenham recuo correto. Você pode verificar com [YAML pano](http://www.yamllint.com/) para validar e, em seguida, salvar.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Implante o `mysql-pv.yaml` arquivo.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Aqui está um exemplo de saída da implantação.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Observe o nome do PVC criado. Você irá usá-lo em uma etapa posterior. 

4. Implante o conteúdo do `mysql-deployment.yml` arquivo.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Aqui está um exemplo de saída da implantação.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Exibir informações sobre a implantação.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Listar os pods criados pela implantação.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Aqui está um exemplo de saída.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Inspecione o PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Aqui está um exemplo de saída.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verificar se o MySQL está em execução


Para executar um comando em um contêiner em um pod que esteja executando o MySQL, digite:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Aqui está um exemplo de saída.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Excluir uma implantação

Para excluir a implantação, exclua os objetos implantados por nome. Esses objetos incluem implantação, serviço e PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Aqui está a saída de exemplo de quando você exclui a implantação e o serviço.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Aqui está a saída de exemplo de quando você exclui o PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

O VP não está mais ligado ao PVC, pois o PVC foi excluído. Como o PV foi provisionado quando o compartilhamento foi criado, será necessário excluir o compartilhamento. Siga estas etapas:

1. Desmonte o compartilhamento. Em portal do Azure, acesse o **recurso Azure Stack Edge > compartilhamentos** e selecione e clique no compartilhamento que deseja desmontar. Selecione **desmontar** e confirme a operação. Aguarde até que o compartilhamento seja desmontado. A desmontagem libera o compartilhamento (e, portanto, o PersistentVolume associado) do cluster kubernetes. 

    ![Desmontar compartilhamento local para VP](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Agora você pode selecionar **excluir** e confirmar a exclusão para excluir seu compartilhamento. Isso também deve excluir o compartilhamento e o VP correspondente.

    ![Excluir compartilhamento local para VP](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Próximas etapas

Para entender como provisionar dinamicamente o armazenamento, consulte [implantar um aplicativo com estado por meio de provisionamento dinâmico em um dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)
