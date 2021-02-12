---
title: Criar e gerenciar um cluster kubernetes no dispositivo de GPU pro do Azure Stack Edge | Microsoft Docs
description: Descreve como criar e gerenciar um cluster kubernetes no dispositivo Azure Stack Edge pro GPU por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: bf73640c1a4014b61809f407e1873686c229273f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954624"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Conectar e gerenciar um cluster kubernetes por meio do kubectl em seu dispositivo Azure Stack Edge pro GPU

Em seu dispositivo Azure Stack Edge pro, um cluster kubernetes é criado quando você configura a função de computação. Depois que o cluster kubernetes for criado, você poderá se conectar e gerenciar o cluster localmente de um computador cliente por meio de uma ferramenta nativa, como *kubectl*.

Este artigo descreve como se conectar a um cluster kubernetes no dispositivo Azure Stack Edge pro e gerenciá-lo usando o *kubectl*. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você tem acesso a um dispositivo Azure Stack Edge pro.

2. Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

3. Você habilitou a função de computação no dispositivo. Um cluster kubernetes também foi criado no dispositivo quando você configurou a computação no dispositivo de acordo com as instruções em [Configurar computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md).

4. Você tem acesso a um sistema cliente Windows executando o PowerShell 5,0 ou posterior para acessar o dispositivo. Você também pode ter qualquer outro cliente com um [sistema operacional com suporte](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . 

5. Você tem o ponto de extremidade da API do kubernetes na página **dispositivo** da interface do usuário da Web local. Para obter mais informações, consulte as instruções em [Get KUBERNETES API Endpoint](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)


## <a name="connect-to-powershell-interface"></a>Conectar-se à interface do PowerShell

Depois que o cluster kubernetes é criado, você pode acessar esse cluster para criar namespaces e usuários e atribuir usuários a namespaces. Isso exigirá que você se conecte à interface do PowerShell do dispositivo. Siga estas etapas no cliente Windows que executa o PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Configurar o acesso ao cluster via RBAC kubernetes

Depois que o cluster kubernetes for criado, você poderá usar o *kubectl* via cmdline para acessar o cluster. 

Nessa abordagem, você cria um namespace e um usuário. Em seguida, associe o usuário ao namespace. Você também precisa obter o arquivo de *configuração* que permite usar um cliente kubernetes para se comunicar diretamente com o cluster kubernetes que você criou sem precisar se conectar à interface do PowerShell de seu dispositivo Azure Stack Edge pro.

1. Criar um namespace. Tipo:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Para nomes de namespace e de usuário, as [convenções de nomenclatura de subdomínio de DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) se aplicam.

    Aqui está uma amostra de saída:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Crie um usuário e obtenha um arquivo de configuração. Tipo:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Você não pode usar *aseuser* como o nome de usuário, já que ele está reservado para uma usuária padrão associada ao namespace IoT para Azure Stack Edge pro.

    Aqui está um exemplo de saída do arquivo de configuração:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Um arquivo de configuração é exibido em texto sem formatação. Copie esse arquivo e salve-o como um arquivo de *configuração* . 

    > [!IMPORTANT]
    > Não salve o arquivo de configuração como arquivo *. txt* , salve o arquivo sem nenhuma extensão de arquivo.

4. O arquivo de configuração deve residir na `.kube` pasta do seu perfil de usuário no computador local. Copie o arquivo para essa pasta em seu perfil de usuário.

    ![Local do arquivo de configuração no cliente](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. Associe o namespace ao usuário que você criou. Tipo:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Aqui está uma amostra de saída:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Quando tiver o arquivo de configuração, você não precisará de acesso físico ao cluster. Se o cliente puder executar ping no IP do dispositivo Azure Stack Edge pro, você deverá ser capaz de direcionar o cluster usando comandos *kubectl* .

6. Inicie uma nova sessão do PowerShell em seu cliente. Você não precisa estar conectado à interface do dispositivo. Agora você pode instalar `kubectl` o no seu cliente usando o seguinte comando:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Por exemplo, se o nó mestre kubernetes estivesse executando o v 1.15.2, instale o v 1.15.2 no cliente.

    > [!IMPORTANT]
    > Baixe um cliente que não tenha mais do que uma versão secundária do mestre. A versão do cliente, mas pode levar o mestre até uma versão secundária. Por exemplo, um Mestre v 1.3 deve funcionar com nós v 1.1, v 1.2 e v 1.3 e deve funcionar com clientes v 1.2, v 1.3 e v 1.4. Para obter mais informações sobre a versão do cliente do kubernetes, consulte [versão do kubernetes e política de suporte à distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Para obter mais informações sobre a versão do kubernetes Server no Azure Stack Edge pro, acesse obter versão do servidor kubernetes.<!-- insert link-->
    > Às vezes, `kubectl` o é pré-instalado no seu sistema se você estiver executando o Docker for Windows ou outras ferramentas. É importante baixar a versão específica do `kubectl` conforme indicado nesta seção para trabalhar com esse cluster kubernetes. 

    A instalação leva vários minutos.

7. Verifique se a versão instalada é aquela que você baixou. Você deve especificar o caminho absoluto para onde o `kubectl.exe` foi instalado em seu sistema.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Para obter mais informações sobre `kubectl` os comandos usados para gerenciar o cluster kubernetes, acesse [visão geral de kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Adicione uma entrada DNS ao arquivo de hosts em seu sistema. 

    1. Execute o bloco de notas como administrador e abra o `hosts` arquivo localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Use as informações que você salvou na página do **dispositivo** na interface do usuário local na etapa anterior para criar a entrada no arquivo hosts. 

        Por exemplo, copie esse ponto de extremidade `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` para criar a seguinte entrada com o endereço IP do dispositivo e o domínio DNS: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Para verificar se você pode se conectar ao pods kubernetes, digite:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Agora você pode implantar seus aplicativos no namespace e, em seguida, exibir esses aplicativos e seus logs.

> [!IMPORTANT]   
> Há muitos comandos que você não poderá executar, por exemplo, os comandos que exigem que você tenha acesso de administrador. Você só pode executar as operações permitidas no namespace.


## <a name="remove-kubernetes-cluster"></a>Remover cluster kubernetes

Para remover o cluster kubernetes, será necessário remover a configuração de IoT Edge.

Para obter instruções detalhadas, vá para [remover configuração de IOT Edge](azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
   

## <a name="next-steps"></a>Próximas etapas

- [Implante um aplicativo sem estado em seu Azure Stack Edge pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
