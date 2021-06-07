---
title: Habilitar um registro de contêiner de borda no dispositivo Azure Stack GPU pro Edge
description: Descreve como habilitar um registro de contêiner de borda local no dispositivo Azure Stack GPU pro Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 56b691b2755b5e248b16e338f8fd82864f5bf218
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560320"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Habilitar o registro de contêiner de borda em seu dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como habilitar o registro de contêiner de borda e usá-lo de dentro do cluster kubernetes em seu dispositivo Azure Stack Edge pro. O exemplo usado no artigo fornece detalhes sobre como enviar por push uma imagem de um registro de origem, nesse caso, o registro de contêiner da Microsoft, para o registro no dispositivo Azure Stack Edge, o registro de contêiner do Edge.

### <a name="about-edge-container-registry"></a>Sobre o registro de contêiner do Edge

Os aplicativos de computação em contêineres são executados em imagens de contêiner e essas imagens são armazenadas em registros. Os registros podem ser públicos, como o Hub do Docker, privado ou o provedor de nuvem gerenciado, como o registro de contêiner do Azure. Para obter mais informações, consulte [sobre registros, repositórios e imagens](../container-registry/container-registry-concepts.md).

Um registro de contêiner de borda fornece um repositório na borda, em seu dispositivo Azure Stack Edge pro. Você pode usar esse registro para armazenar e gerenciar suas imagens de contêiner particulares.

Em um ambiente de vários nós, as imagens de contêiner podem ser baixadas e enviadas por push para o registro de contêiner de borda uma vez. Todos os aplicativos de borda podem usar o registro de contêiner de borda para implantações subsequentes.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo Azure Stack Edge pro.

1. Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Você habilitou a função de computação no dispositivo. Um cluster kubernetes também foi criado no dispositivo quando você configurou a computação no dispositivo de acordo com as instruções em [Configurar computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Você tem o ponto de extremidade da API do kubernetes na página **dispositivo** da interface do usuário da Web local. Para obter mais informações, consulte as instruções em [Get KUBERNETES API Endpoint](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Você tem acesso a um sistema cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Se estiver usando um cliente Windows, o sistema deverá executar o PowerShell 5,0 ou posterior para acessar o dispositivo.

    1. Se você quiser efetuar pull e enviar por push suas próprias imagens de contêiner, certifique-se de que o sistema tenha o cliente Docker instalado. Se estiver usando um cliente Windows, [Instale o Docker desktop no Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Habilitar o registro de contêiner como complemento

A primeira etapa é habilitar o registro de contêiner de borda como um complemento.

1. [Conecte-se à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Para habilitar o registro de contêiner como um complemento, digite: 

    `Set-HcsKubernetesContainerRegistry`
    
    Essa operação pode levar vários minutos para ser concluída.

    Este é o exemplo de saída deste comando:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Para obter os detalhes do registro de contêiner, digite:

    `Get-HcsKubernetesContainerRegistryInfo`

    Aqui está o exemplo deste comando:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Anote o nome de usuário e a senha da saída de `Get-HcsKubernetesContainerRegistryInfo` . Essas credenciais são usadas para entrar no registro de contêiner de borda ao enviar imagens por push.         


## <a name="manage-container-registry-images"></a>Gerenciar imagens do registro de contêiner

Talvez você queira acessar o registro de contêiner fora do seu dispositivo Azure Stack Edge. Você também pode querer enviar por Push ou efetuar pull de imagens no registro.

Siga estas etapas para acessar o registro de contêiner do Edge:

1. Obtenha os detalhes do ponto de extremidade para o registro de contêiner do Edge.
    1. Na interface do usuário local do dispositivo, vá para **dispositivo**.
    1. Localize o **ponto de extremidade do registro de contêiner do Edge**.
        ![Ponto de extremidade do registro de contêiner do Edge na página do dispositivo](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Copie esse ponto de extremidade e crie uma entrada DNS correspondente no `C:\Windows\System32\Drivers\etc\hosts` arquivo do cliente para se conectar ao ponto de extremidade do registro de contêiner de borda. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Adicionar entrada DNS para ponto de extremidade de registro de contêiner de borda](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Baixe o certificado de registro de contêiner de borda da interface do usuário local. 
    1. Na interface do usuário local do dispositivo, vá para **certificados**.
    1. Localize a entrada para o **certificado de registro de contêiner de borda**. À direita dessa entrada, selecione o **Download** para baixar o certificado de registro de contêiner de borda no sistema cliente que você usará para acessar seu dispositivo. 

        ![Baixar certificado do ponto de extremidade do registro de contêiner do Edge](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Instale o certificado baixado no cliente. Se estiver usando um cliente Windows, siga estas etapas: 
    1. Selecione o certificado e, no **Assistente para importação de certificados**, selecione local de armazenamento como **computador local**. 

        ![Instalar o certificado 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Instale o certificado em seu computador local no repositório de raiz confiável. 

        ![Instalar certificado 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Depois que o certificado for instalado, reinicie o cliente do Docker no sistema.

1. Entre no registro de contêiner do Edge. Tipo:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Forneça o ponto de extremidade do registro de contêiner do Edge na página **dispositivos** e o nome de usuário e a senha obtidos da saída de `Get-HcsKubernetesContainerRegistryInfo` . 

1. Use os comandos Docker Push ou pull para enviar por Push ou extrair imagens de contêiner do registro de contêiner.
 
    1. Efetuar pull de uma imagem da imagem do registro de contêiner da Microsoft. Tipo:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Crie um alias da imagem que você recebeu com o caminho totalmente qualificado para o registro.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Envie a imagem por push ao registro.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Execute a imagem enviada por push ao registro.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Aqui está um exemplo de saída dos comandos pull e push:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Navegue até `http://localhost:8080` para exibir o contêiner em execução. Nesse caso, você verá o servidor webnginx em execução.

    ![Exibir o contêiner em execução](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Para parar e remover o contêiner, pressione `Control+C`.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Usar imagens do registro de contêiner de borda via pods de kubernetes

Agora você pode implantar a imagem enviada por push no registro de contêiner de borda de dentro do pods kubernetes.

1. Para implantar a imagem, você precisa configurar o acesso ao cluster via *kubectl*. Crie um namespace, um usuário, conceda acesso de usuário ao namespace e obtenha um arquivo de *configuração* . Verifique se você pode se conectar ao pods kubernetes. 
    
    Siga todas as etapas em [conectar e gerenciar um cluster kubernetes por meio do kubectl em seu dispositivo de GPU Pro Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Aqui está um exemplo de saída para um namespace em seu dispositivo de onde o usuário pode acessar o cluster kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Os segredos de pull de imagem já estão definidos em todos os namespaces kubernetes em seu dispositivo. Você pode obter segredos usando o `get secrets` comando. Aqui está uma amostra de saída:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Implante um pod em seu namespace usando o kubectl. Use o seguinte `yaml` . 

    Substitua a imagem: `<image-name>` pela imagem enviada por push ao registro de contêiner. Consulte os segredos em seus namespaces usando imagePullSecrets com um nome: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Aplique a implantação no namespace que você criou usando o comando aplicar. Verifique se o contêiner está em execução. Aqui está uma amostra de saída:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Excluir imagens do registro de contêiner

O armazenamento do registro de contêiner do Edge é hospedado em um compartilhamento local dentro de seu dispositivo Azure Stack Edge pro, que é limitado pelo armazenamento disponível no dispositivo. É sua responsabilidade excluir imagens não utilizadas do Docker do registro de contêiner usando a API HTTP v2 do Docker ( https://docs.docker.com/registry/spec/api/) .  

Para remover uma ou mais imagens de contêiner, siga estas etapas:

1. Defina o nome da imagem para a imagem que você deseja excluir.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Definir o nome de usuário e a senha do registro de contêiner como uma credencial do PS

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Listar as marcas associadas à imagem

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Liste o resumo associado à marca que você deseja excluir. Isso usa $tags da saída do comando acima. Se você tiver várias marcas, selecione uma delas e use no próximo comando.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Excluir a imagem usando o resumo da imagem: marca

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Depois de excluir as imagens não usadas, o espaço associado às imagens não referenciadas é automaticamente recuperado por um processo que é executado à noite. 

## <a name="next-steps"></a>Próximas etapas

- [Implante um aplicativo sem estado em seu Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).