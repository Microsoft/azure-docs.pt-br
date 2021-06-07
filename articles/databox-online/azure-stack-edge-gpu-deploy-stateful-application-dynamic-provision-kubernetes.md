---
title: Use o kubectl para implantar o aplicativo com estado kubernetes por meio do compartilhamento provisionado dinamicamente no dispositivo de GPU pro Azure Stack Edge | Microsoft Docs
description: Descreve como criar e gerenciar uma implantação de aplicativo com estado kubernetes por meio de um compartilhamento provisionado dinamicamente usando o kubectl em um dispositivo de GPU pro Microsoft Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520686"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Use kubectl para executar um aplicativo com estado kubernetes com StorageClass em seu dispositivo de Azure Stack Edge pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo mostra como implantar um aplicativo com estado de instância única no kubernetes usando um StorageClass para provisionar dinamicamente o armazenamento e uma implantação. A implantação usa `kubectl` comandos em um cluster kubernetes existente e implanta o aplicativo MySQL. 

Esse procedimento destina-se a aqueles que revisaram o [armazenamento kubernetes no dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md) e estão familiarizados com os conceitos do [armazenamento kubernetes](https://kubernetes.io/docs/concepts/storage/).


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
        - Verifique essas duas versões para compatibilidade com o mapeamento fornecido na versão do kubernetes com suporte<!-- insert link-->. 


Você está pronto para implantar um aplicativo com estado em seu dispositivo Azure Stack Edge pro. 


## <a name="deploy-mysql"></a>Implantar MySQL

Agora, você executará um aplicativo com estado criando uma implantação do kubernetes e conectando-o ao StorageClass interno usando um PersistentVolumeClaim (PVC). 

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

1. Você usará os seguintes arquivos YAML. O `mysql-deployment.yml` arquivo descreve uma implantação que executa o MySQL e faz referência ao PVC. O arquivo define uma montagem de volume para o `/var/lib/mysql` e, em seguida, cria um PVC que procura um volume de 20 GB. Um PV dinâmico é provisionado e o PVC está associado a este VP.

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
              claimName: mysql-pv-claim-sc
    ```
    
2. Copie e salve como um `mysql-pvc.yml` arquivo na mesma pasta em que você salvou o `mysql-deployment.yml` . Para usar o StorageClass interno que Azure Stack dispositivo Pro de borda em um disco de dados anexado, defina o `storageClassName` campo no objeto de PVC como `ase-node-local` e accessModes deve ser `ReadWriteOnce` . 

    > [!NOTE] 
    > Certifique-se de que os arquivos YAML tenham recuo correto. Você pode verificar com [YAML pano](http://www.yamllint.com/) para validar e, em seguida, salvar.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Implante o `mysql-pvc.yaml` arquivo.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Aqui está um exemplo de saída da implantação.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Observe o nome do PVC criado – neste exemplo, `mysql-pv-claim-sc` . Você irá usá-lo em uma etapa posterior.

4. Implante o conteúdo do `mysql-deployment.yml` arquivo.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Aqui está um exemplo de saída da implantação.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Exibir informações sobre a implantação.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Listar os pods criados pela implantação.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Aqui está um exemplo de saída.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Inspecione o PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Aqui está um exemplo de saída.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verificar se o MySQL está em execução

Para verificar se o aplicativo está em execução, digite:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Quando solicitado, digite a senha. A senha está em seu `mysql-deployment` arquivo.

Aqui está um exemplo de saída.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Próximas etapas

Para entender como configurar a rede via kubectl, consulte [implantar um aplicativo sem estado em um dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)