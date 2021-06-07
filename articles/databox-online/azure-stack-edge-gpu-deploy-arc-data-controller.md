---
title: Implantar um controlador de dados de arco do Azure em seu dispositivo de GPU pro do Azure Stack Edge | Microsoft Docs
description: Descreve como implantar um controlador de dados de arco do Azure e os serviços de dados do Azure em seu dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 53058d27e94c9fdf18d726369f6a1b75a9f34db9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567535"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Implantar os serviços de dados do Azure em seu dispositivo do Azure Stack Edge pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve o processo de criação de um controlador de dados de arco do Azure e, em seguida, a implantação dos serviços de dados do Azure em seu dispositivo de GPU pro Azure Stack Edge. 

O controlador de dados de arco do Azure é o plano de controle local que habilita os serviços de dados do Azure em ambientes gerenciados pelo cliente. Depois de criar o controlador de dados de arco do Azure no cluster kubernetes que é executado em seu dispositivo Azure Stack Edge pro, você pode implantar os serviços de dados do Azure, como SQL Instância Gerenciada (versão prévia) nesse controlador de dados.

O procedimento para criar um controlador de dados e, em seguida, implantar um SQL Instância Gerenciada envolve o uso do PowerShell e `kubectl` uma ferramenta nativa que fornece acesso de linha de comando ao cluster kubernetes no dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo Azure Stack Edge pro e ativou o dispositivo conforme descrito em ativar o [Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md).

1. Você habilitou a função de computação no dispositivo. Um cluster kubernetes também foi criado no dispositivo quando você configurou a computação no dispositivo de acordo com as instruções em [Configurar computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Você tem o ponto de extremidade da API do kubernetes na página **dispositivo** da interface do usuário da Web local. Para obter mais informações, consulte as instruções em [Get KUBERNETES API Endpoint](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Você tem acesso a um cliente que se conectará ao seu dispositivo. 
    1. Este artigo usa um sistema cliente Windows executando o PowerShell 5,0 ou posterior para acessar o dispositivo. Você pode usar qualquer outro cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Instale `kubectl` o em seu cliente. Para a versão do cliente:
        1. Identifique a versão do servidor do kubernetes instalada no dispositivo. Na interface do usuário local do dispositivo, vá para a página **atualizações de software** . Observe a **versão do servidor kubernetes** nesta página.
        1. Baixe um cliente que não tenha mais do que uma versão secundária do mestre. A versão do cliente, mas pode levar o mestre até uma versão secundária. Por exemplo, um Mestre v 1.3 deve funcionar com nós v 1.1, v 1.2 e v 1.3 e deve funcionar com clientes v 1.2, v 1.3 e v 1.4. Para obter mais informações sobre a versão do cliente do kubernetes, consulte [versão do kubernetes e política de suporte à distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Opcionalmente, [Instale as ferramentas de cliente para implantar e gerenciar os serviços de dados habilitados para Arc do Azure](../azure-arc/data/install-client-tools.md). Essas ferramentas não são necessárias, mas recomendadas.  
1. Verifique se você tem recursos suficientes disponíveis em seu dispositivo para provisionar um controlador de dados e um Instância Gerenciada do SQL. Para o controlador de dados e um Instância Gerenciada SQL, você precisará de, no mínimo, 16 GB de RAM e 4 núcleos de CPU. Para obter diretrizes detalhadas, vá para [requisitos mínimos para implantação de serviços de dados habilitados para o Azure Arc](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Configurar IPs de serviço externo kubernetes

1. Vá para a interface do usuário da Web local do dispositivo e, em seguida, vá para **computação**.
1. Selecione a rede habilitada para computação. 

    ![Página de computação na IU local 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Certifique-se de fornecer três IPs adicionais de serviço externo kubernetes (além dos IPs que você já configurou para outros serviços ou contêineres externos). O controlador de dados usará dois IPs de serviço e o terceiro IP será usado quando você criar um Instância Gerenciada SQL. Você precisará de um IP para cada serviço de dados adicional que será implantado. 

    ![Página de computação na IU local 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Aplique as configurações e esses novos IPs entrarão em vigor imediatamente em um cluster kubernetes já existente. 


## <a name="deploy-azure-arc-data-controller"></a>Implantar o Controlador de Dados do Azure Arc

Antes de implantar um controlador de dados, você precisará criar um namespace.

### <a name="create-namespace"></a>Criar um namespace 

Crie um novo namespace dedicado no qual você implantará o controlador de dados. Você também criará um usuário e, em seguida, concederá ao usuário o acesso ao namespace que você criou. 

> [!NOTE]
> Para nomes de namespace e de usuário, as [convenções de nomenclatura de subdomínio de DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) se aplicam.

1. [Conecte-se à interface do PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Criar um namespace. Tipo:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Crie um usuário. Tipo: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Um arquivo de configuração é exibido em texto sem formatação. Copie esse arquivo e salve-o como um arquivo de *configuração* . 

    > [!IMPORTANT]
    > Não salve o arquivo de configuração como arquivo *. txt* , salve o arquivo sem nenhuma extensão de arquivo.

1. O arquivo de configuração deve residir na `.kube` pasta do seu perfil de usuário no computador local. Copie o arquivo para essa pasta em seu perfil de usuário.

    ![Local do arquivo de configuração no cliente](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Conceda ao usuário acesso ao namespace que você criou. Tipo: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Aqui está um exemplo de saída dos comandos anteriores. Neste exemplo, criamos um `myadstest` namespace, um `myadsuser` usuário e concedemos acesso ao namespace ao usuário.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Adicione uma entrada DNS ao arquivo de hosts em seu sistema. 

    1. Execute o bloco de notas como administrador e abra o `hosts` arquivo localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Use as informações que você salvou na página do **dispositivo** na interface do usuário local (pré-requisito) para criar a entrada no arquivo de hosts. 

        Por exemplo, copie esse ponto de extremidade `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` para criar a seguinte entrada com o endereço IP do dispositivo e o domínio DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Para verificar se você pode se conectar ao pods kubernetes, inicie um prompt cmd ou uma sessão do PowerShell. Tipo:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Agora você pode implantar seu controlador de dados e aplicativos de serviços de dados no namespace, em seguida, exibir os aplicativos e seus logs.

### <a name="create-data-controller"></a>Criar controlador de dados

O controlador de dados é uma coleção de pods que são implantados em seu cluster kubernetes para fornecer uma API, o serviço do controlador, o bootstrapper e os bancos de dados e painéis de monitoramento. Siga estas etapas para criar um controlador de dados no cluster kubernetes que existe em seu dispositivo de borda Azure Stack no namespace que você criou anteriormente.   

1. Reúna as seguintes informações que você precisará para criar um controlador de dados:

    
    |Column1  |Column2  |
    |---------|---------|
    |Nome do controlador de dados     |Um nome descritivo para o controlador de dados. Por exemplo, `arctestdatacontroller`.         |
    |Nome de usuário do controlador de dados     |Qualquer nome de usuário do administrador do controlador de dados. O nome de usuário e a senha do controlador de dados são usados para autenticar a API do controlador de dados para executar funções administrativas.          |
    |Senha do controlador de dados     |Uma senha para o usuário administrador do controlador de dados. Escolha uma senha segura e compartilhe-a com apenas aquelas que precisam ter privilégios de administrador de cluster.         |
    |Nome do seu namespace kubernetes     |O nome do namespace kubernetes no qual você deseja criar o controlador de dados.         |
    |ID da assinatura do Azure     |O GUID de assinatura do Azure para onde você deseja que o recurso de controlador de dados seja criado no Azure.         |
    |Nome do grupo de recursos do Azure     |O nome do grupo de recursos em que você deseja que o recurso de controlador de dados seja criado no Azure.         |
    |Localização do Azure     |O local do Azure onde os metadados de recurso do controlador de dados serão armazenados no Azure. Para obter uma lista de regiões disponíveis, consulte infraestrutura global/produtos do Azure por região.|


1. Conecte-se à interface do PowerShell. Para criar o controlador de dados, digite: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Aqui está um exemplo de saída dos comandos anteriores.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    A implantação pode levar aproximadamente 5 minutos para ser concluída.

    > [!NOTE]
    > O controlador de dados criado no cluster kubernetes em seu dispositivo Azure Stack Edge pro funciona apenas no modo desconectado na versão atual.

### <a name="monitor-data-creation-status"></a>Monitorar o status de criação de dados

1. Abra outra janela do PowerShell.
1. Use o comando a seguir `kubectl` para monitorar o status de criação do controlador de dados. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Quando o controlador é criado, o status deve ser `Ready` .
    Aqui está um exemplo de saída do comando anterior:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Para identificar os IPs atribuídos aos serviços externos em execução no controlador de dados, use o `kubectl get svc -n <namespace>` comando. Aqui está uma amostra de saída:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Implantar instância gerenciada do SQL

Depois de criar com êxito o controlador de dados, você pode usar um modelo para implantar um Instância Gerenciada do SQL no controlador de dados.

### <a name="deployment-template"></a>Modelo de implantação

Use o modelo de implantação a seguir para implantar um SQL Instância Gerenciada no controlador de dados em seu dispositivo.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Nome dos metadados
    
O nome dos metadados é o nome do Instância Gerenciada do SQL. O Pod associado no precedente `deployment.yaml` será nome como `sqlex-n` ( `n` é o número de pods associado ao aplicativo). 
    
#### <a name="password-and-username-data"></a>Senha e dados de nome de usuário

O nome de usuário e a senha do controlador de dados são usados para autenticar a API do controlador de dados para executar funções administrativas. O segredo kubernetes para o nome de usuário do controlador de dados e a senha no modelo de implantação são cadeias de caracteres codificadas em base64. 

Você pode usar uma ferramenta online para codificar o nome de usuário e a senha desejados ou pode usar ferramentas de CLI internas, dependendo da sua plataforma. Ao usar uma ferramenta de codificação de Base64 online, forneça as cadeias de caracteres de nome de usuário e senha (que você inseriu ao criar o controlador de dados) na ferramenta e a ferramenta irá gerar as cadeias de caracteres codificadas em base64 correspondentes.
    
#### <a name="service-type"></a>Tipo de serviço

O tipo de serviço deve ser definido como `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nome da classe de armazenamento

Você pode identificar a classe de armazenamento em seu Azure Stack dispositivo de borda que será usado pela implantação para dados, backups, logs de dados e logs. Use o  `kubectl get storageclass` comando para obter a classe de armazenamento implantada em seu dispositivo.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
Na saída de exemplo anterior, a classe de armazenamento `ase-node-local` em seu dispositivo deve ser especificada no modelo.
 
#### <a name="spec"></a>6224F

Para criar um Instância Gerenciada do SQL em seu dispositivo Azure Stack Edge, você pode especificar os requisitos de memória e CPU na seção espec do `deployment.yaml` . Cada instância gerenciada do SQL deve solicitar um mínimo de 2 GB de memória e 1 núcleo de CPU, conforme mostrado no exemplo a seguir. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Para obter diretrizes de dimensionamento detalhadas para controlador de dados e 1 Instância Gerenciada SQL, examine [detalhes de dimensionamento da instância gerenciada do SQL](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Executar modelo de implantação

Execute o `deployment.yaml` seguinte comando:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Aqui está um exemplo de saída do comando a seguir:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

O `sqlex-0` pod na saída de exemplo indica o status do instância gerenciada do SQL.

## <a name="remove-data-controller"></a>Remover controlador de dados

Para remover o controlador de dados, exclua o namespace dedicado no qual você o implantou.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Próximas etapas

- [Implante um aplicativo sem estado em seu Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).