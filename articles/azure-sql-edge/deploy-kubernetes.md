---
title: Implantar um contêiner do SQL do Azure no Edge no Kubernetes – SQL do Azure no Edge
description: Saiba mais sobre como implantar um contêiner do SQL do Azure no Edge no Kubernetes
keywords: SQL no Edge, contêiner, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395284"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Implantar um contêiner do SQL do Azure no Edge no Kubernetes

O SQL do Azure no Edge pode ser implantado em um cluster de Kubernetes tanto como um módulo IoT Edge por meio do SQL do Azure no Edge em execução no Kubernetes ou como um pod de contêiner autônomo. Para o restante deste artigo, nos concentraremos na implantação de contêiner autônomo em um cluster de kubernetes. Para obter informações sobre como implantar o SQL do Azure no Edge no Kubernetes, confira [Azure IoT Edge no Kubernetes (versão prévia)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

Este tutorial demonstra como configurar uma instância do SQL do Azure no Edge altamente disponível em um contêiner em um cluster de kubernetes. 

> [!div class="checklist"]
> * Criar uma senha SA
> * Criar armazenamento
> * Criar a implantação
> * Conectar-se com o SSMS (SQL Server Management Studio)
> * Verificar falha e recuperação

O Kubernetes 1.6 e posterior tem suporte para [classes de armazenamento](https://kubernetes.io/docs/concepts/storage/storage-classes/), [declarações de volume persistentes](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims) e o [tipo de volume de disco do Azure](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Você pode criar e gerenciar suas instâncias de SQL do Azure no Edge nativamente no Kubernetes. O exemplo neste artigo mostra como criar uma [implantação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) para obter uma configuração de alta disponibilidade semelhante a uma instância de cluster de failover de disco compartilhado. Nessa configuração, o Kubernetes desempenha a função do orquestrador de cluster. Quando uma instância de SQL do Azure no Edge em um contêiner falha, o orquestrador inicializa outra instância do contêiner que é anexada ao mesmo armazenamento persistente.

![SQL do Azure no Edge em um cluster de Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

No diagrama anterior, `azure-sql-edge` é um contêiner em um [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). O Kubernetes orquestra os recursos no cluster. Um [conjunto de réplicas](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) verifica se o pod está recuperado automaticamente após uma falha de nó. Os aplicativos conectam-se ao serviço. Nesse caso, o serviço representa um balanceador de carga que hospeda um endereço IP que permanece o mesmo após a falha do `azure-sql-edge`.

No diagrama a seguir, o contêiner `azure-sql-edge` falhou. Como o orquestrador, o Kubernetes garante que a contagem correta de instâncias íntegras no conjunto de réplicas e inicia um novo contêiner de acordo com a configuração. O orquestrador inicia um novo pod no mesmo nó e `azure-sql-edge` se reconecta ao mesmo armazenamento persistente. O serviço conecta-se ao `azure-sql-edge` recriado.

![SQL do Azure no Edge em um cluster de Kubernetes após a falha do pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

No diagrama a seguir, o nó que hospeda o contêiner `azure-sql-edge` falhou. O orquestrador inicia o novo pod em um nó diferente e `azure-sql-edge` reconecta-se ao mesmo armazenamento persistente. O serviço conecta-se ao `azure-sql-edge` recriado.

![SQL do Azure no Edge em um cluster de Kubernetes após a falha do nó](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Pré-requisitos

* **Cluster do Kubernetes**
   - O tutorial requer um cluster do Kubernetes. As etapas usam [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) para gerenciar o cluster. 

   - Para os fins deste tutorial, usaremos o Serviço de Kubernetes do Azure para implantar o SQL do Azure no Edge. Confira [Implantar um cluster do AKS (Serviço de Kubernetes do Azure)](../aks/tutorial-kubernetes-deploy-cluster.md) para criar e conectar-se a um cluster Kubernetes de nó único no AKS com `kubectl`. 

   >[!NOTE]
   >Para proteger contra falhas de nó, um cluster Kubernetes requer mais de um nó.

* **CLI do Azure**
   - As instruções neste tutorial foram validadas com relação à CLI do Azure 2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Criar um namespace de kubernetes para implantação do SQL no Edge

Crie um namespace no cluster de kubernetes. Esse namespace será usado para implantar o SQL no Edge e todos os artefatos necessários. Para obter mais informações sobre namespaces do Kubernetes, confira [namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Criar uma senha SA

Crie a senha SA no cluster Kubernetes. O Kubernetes pode gerenciar informações de configuração confidenciais, por exemplo, senhas como [segredos](https://kubernetes.io/docs/concepts/configuration/secret/).

O comando a seguir cria uma senha para a conta SA:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Substitua `MyC0m9l&xP@ssw0rd` por uma senha complexa.

## <a name="create-storage"></a>Criar armazenamento

Configure um [volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) e uma [declaração de volume persistente](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) no cluster Kubernetes. Conclua as seguintes etapas: 

1. Crie um manifesto para definir a classe de armazenamento e a declaração de volume persistente.  O manifesto especifica o provisionador de armazenamento, os parâmetros e a [política de recuperação](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). O cluster Kubernetes usa esse manifesto para criar o armazenamento persistente. 

   O exemplo de yaml a seguir define uma classe de armazenamento e uma declaração de volume persistente. O provisionador de classe de armazenamento é `azure-disk`, porque esse cluster Kubernetes está no Azure. O tipo de conta de armazenamento é `Standard_LRS`. A declaração de volume persistente é denominada `mssql-data`. Os metadados da declaração de volume persistente incluem uma anotação que os conecta de volta à classe de armazenamento. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Salve o arquivo (por exemplo, **pvc.yaml**).

2. Crie a declaração de volume persistente no Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` é a localização em que você salvou o arquivo.

   O volume persistente é criado automaticamente como uma conta de armazenamento do Azure e associado à declaração de volume persistente. 

    ![Captura de tela do comando da declaração de volume persistente](media/deploy-kubernetes/pvc-cmd.png)

3. Verifique a declaração de volume persistente.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` é o nome da declaração de volume persistente.

   Na etapa anterior, a declaração de volume persistente é denominada `mssql-data`. Para ver os metadados sobre a declaração de volume persistente, execute o seguinte comando:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Os metadados retornados incluem um valor chamado `Volume`. Esse valor é mapeado para o nome do blob.

   ![Captura de tela dos metadados retornados, incluindo Volume](media/deploy-kubernetes/describe-volume.png)

4. Verifique o volume persistente.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` retorna metadados sobre o volume persistente que foi criado automaticamente e associado à declaração de volume persistente. 

## <a name="create-the-deployment"></a>Criar a implantação

Neste exemplo, o contêiner que hospeda a instância do SQL do Azure no Edge é descrito como um objeto de implantação Kubernetes. A implantação cria um conjunto de réplicas. O conjunto de réplicas cria o pod. 

Nesta etapa, crie um manifesto para descrever o contêiner com base na imagem do Docker do SQL do Azure no Edge. O manifesto referencia a declaração de volume persistente do `mssql-data` e o segredo `mssql` que você já aplicou ao cluster Kubernetes. O manifesto também descreve um [serviço](https://kubernetes.io/docs/concepts/services-networking/service/). Esse serviço é um balanceador de carga. O balanceador de carga garante que o endereço IP persiste após a recuperação da instância do SQL do Azure no Edge. 

1. Crie um manifesto (um arquivo YAML) para descrever a implantação. O exemplo a seguir descreve uma implantação, incluindo um contêiner baseado na imagem de contêiner do SQL do Azure no Edge.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Copie o código anterior para um novo arquivo, denominado `sqldeployment.yaml`. Atualize os seguintes valores: 

   * MSSQL_PID `value: "Developer"`: Define o contêiner para executar o SQL do Azure no Edge Developer Edition. O Developer Edition não é licenciado para dados de produção. Se a implantação for para uso em produção, defina a edição como `Premium`. 

      >[!NOTE]
      >Para saber mais, confira [Como licenciar o SQL do Azure no Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: esse valor requer uma entrada para `claimName:` mapeada para o nome usado para a declaração de volume persistente. Este tutorial usa `mssql-data`. 

   * `name: SA_PASSWORD`: configura a imagem de contêiner para definir a senha SA, conforme definido nesta seção.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Quando o Kubernetes implanta o contêiner, ele refere-se ao segredo chamado `mssql` para obter o valor da senha. 

   >[!NOTE]
   >Usando o tipo de serviço `LoadBalancer`, a instância do SQL do Azure no Edge pode ser acessada remotamente (pela Internet) na porta 1433.

   Salve o arquivo (por exemplo, **sqledgedeploy.yaml**).

2. Crie a implantação.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` é a localização em que você salvou o arquivo.

   ![Captura de tela do comando de implantação](media/deploy-kubernetes/deploy-sql-cmd.png)

   A implantação e o serviço são criados. A instância do SQL do Azure no Edge está em um contêiner, conectada ao armazenamento persistente.

   Para exibir o status do pod, digite `kubectl get pod -n <namespace name>`.

   ![Captura de tela do comando get pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Na imagem anterior, o pod tem um status de `Running`. Esse status indica que o contêiner está pronto. Isso pode levar alguns minutos.

   >[!NOTE]
   >Após a criação da implantação, pode levar alguns minutos antes que o pod fique visível. O atraso é porque o cluster extrai a imagem de contêiner do SQL do Azure no Edge do Docker Hub. Depois o pull da imagem for efetuado pela primeira vez, implantações subsequentes poderão ser mais rápidas se a implantação for para um nó que já tem a imagem armazenada em cache. 

3. Verifique se os serviços estão em execução. Execute o comando a seguir:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Esse comando retorna serviços em execução, assim como endereços IP internos e externos para os serviços. Observe o endereço IP externo do serviço `mssql-deployment`. Use este endereço IP para se conectar ao SQL do Azure no Edge. 

   ![Captura de tela do comando get service](media/deploy-kubernetes/get-service-cmd.png)

   Para saber mais sobre o status dos objetos no cluster Kubernetes, execute:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Conectar-se à instância do SQL do Azure no Edge

Se você tiver configurado o contêiner, conforme descrito, poderá conectar-se com um aplicativo de fora da rede virtual do Azure. Use a conta `sa` e o endereço IP externo para o serviço. Use a senha que você configurou como o segredo Kubernetes. Para obter mais informações sobre como se conectar a uma instância do SQL do Azure no Edge, confira [Conectar-se ao SQL do Azure no Edge](connect.md).

## <a name="verify-failure-and-recovery"></a>Verificar falha e recuperação

Para verificar a falha e a recuperação, exclua o pod. Execute as seguintes etapas:

1. Listar o pod que executa o SQL do Azure no Edge.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Anote o nome do pod que está executando o SQL do Azure no Edge.

2. Excluir o pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` é o valor retornado da etapa anterior para o nome do pod. 

O Kubernetes recria automaticamente o pod para recuperar uma instância do SQL do Azure no Edge e conectar-se ao armazenamento persistente. Use `kubectl get pods` para verificar se um novo pod foi implantado. Use `kubectl get services` para verificar se o endereço IP do novo contêiner é o mesmo. 

## <a name="summary"></a>Resumo

Neste tutorial, você aprendeu a implantar contêineres do SQL do Azure no Edge em um cluster Kubernetes para alta disponibilidade. 

> [!div class="checklist"]
> * Criar uma senha SA
> * Criar armazenamento
> * Criar a implantação
> * Conectar-se ao SSMS (Management Studio do SQL do Azure no Edge)
> * Verificar falha e recuperação

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Kubernetes](../aks/intro-kubernetes.md)
- [Machine learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).
- [Como criar uma solução de IoT de ponta a ponta com o SQL no Edge usando o IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados no SQL do Azure no Edge](stream-data.md)