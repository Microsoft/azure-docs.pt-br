---
title: Implantar VMs no dispositivo de GPU Azure Stack Edge Pro por meio da CLI do Azure e o Python
description: Descreve como criar e gerenciar VMs (máquinas virtuais) em um dispositivo de GPU Azure Stack Edge Pro usando a CLI do Azure e o Python.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 14d9c762702648d25efcbcc7ac85824659e20f05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304062"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Implantar VMs no dispositivo de GPU Azure Stack Edge Pro usando a CLI do Azure e o Python

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

Este tutorial descreve como criar e gerenciar uma VM no dispositivo Azure Stack Edge Pro usando a CLI (interface de linha de comando) do Azure e o Python.

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação da VM

O fluxo de trabalho de implantação é ilustrado no diagrama a seguir.

![Fluxo de trabalho de implantação da VM](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

O resumo de alto nível do fluxo de trabalho de implantação é o seguinte:

1. Conectar-se ao Azure Resource Manager
2. Criar um grupo de recursos
3. Criar uma conta de armazenamento
4. Adicionar o URI do blob ao arquivo hosts
5. Instalar certificados
6. Carregar um VHD
7. Criar discos gerenciados com base no VHD
8. Criar uma imagem de VM com base no disco gerenciado da imagem
9. Criar uma VM com os recursos criados anteriormente
10. Criar uma VNET
11. Criar uma vNIC usando a ID da sub-rede da VNet

Para obter uma explicação detalhada do diagrama de fluxo de trabalho, confira [Implantar VMs no dispositivo Azure Stack Edge Pro usando o Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). Para obter informações sobre como se conectar ao Azure Resource Manager, confira [Conectar-se ao Azure Resource Manager usando o Azure PowerShell](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerenciar uma VM no dispositivo Azure Stack Edge Pro usando a CLI do Azure e o Python, você precisará concluir os pré-requisitos listados nas seguintes etapas:

1. Você concluiu as configurações de rede no dispositivo Azure Stack Edge Pro, conforme descrito na [Etapa 1: Configurar o dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Você habilitou um adaptador de rede para computação. Esse IP do adaptador de rede é usado para criar um comutador virtual para a implantação da VM. As seguintes etapas descreverão o processo:

    1. Acesse **Computação**. Selecione o adaptador de rede que será usado para criar um comutador virtual.

        > [!IMPORTANT] 
        > Você só pode configurar uma porta para computação.

    2. Habilite a computação no adaptador de rede. O Azure Stack Edge Pro criará e gerenciará um comutador virtual correspondente a esse adaptador de rede.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Você criou e instalou todos os certificados no dispositivo Azure Stack Edge Pro e no repositório confiável do cliente. Siga o procedimento descrito na [Etapa 2: Criar e instalar certificados](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

4. Você criou um certificado *.cer* (formato PEM) codificado em Base-64 para o dispositivo Azure Stack Edge Pro. Esse certificado já foi carregado como uma cadeia de assinatura no dispositivo e instalado no repositório raiz confiável do cliente. Também é necessário que esse certificado esteja no formato *PEM* para que o Python funcione no cliente.

    Converta o certificado em formato `pem` usando o comando `certutil`. Você precisará executar esse comando no diretório que contém o certificado.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Veja um exemplo do uso do comando:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Você também adicionará esse `pem` ao repositório do Python posteriormente.

5. Você atribuiu o IP do dispositivo na página **Rede** à IU da Web local do dispositivo. Adicionar este IP a:

    - Ao arquivo de host no cliente OU
    - À configuração do servidor DNS
    
    > [!IMPORTANT]
    > Recomendamos que você modifique a configuração do servidor DNS para a resolução de nome do ponto de extremidade.

    1. Inicie o **Bloco de notas** como administrador (é necessário ter privilégios de Administrador para salvar o arquivo) e abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.
    
        ![Arquivo hosts do Windows Explorer](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Adicione as seguintes entradas ao arquivo **hosts** substituindo-as por valores apropriados para seu dispositivo:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Use a imagem a seguir para referência. Salve o arquivo **hosts**.

        ![Arquivo hosts no Bloco de notas](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Baixe o script Python](https://aka.ms/ase-vm-python) usado neste procedimento.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Etapa 1: Configurar a CLI do Azure/o Python no cliente

### <a name="verify-profile-and-install-azure-cli"></a>Confirmar o perfil e instalar a CLI do Azure

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Instale a CLI do Azure no cliente. Neste exemplo, a CLI do Azure 2.0.80 foi instalada. Para confirmar a versão da CLI do Azure, execute o comando `az --version`.

    Veja uma saída de exemplo do comando acima:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Caso você não tenha a CLI do Azure, baixe-a e [instale-a no Windows](/cli/azure/install-azure-cli-windows). Execute a CLI do Azure usando o prompt de comando do Windows ou por meio do Windows PowerShell.

2. Anote a localização do Python da CLI. Você precisará da localização do Python para determinar a localização do repositório de certificados raiz confiáveis da CLI do Azure.

3. Para executar o script de exemplo usado neste artigo, você precisará ter as seguintes versões da biblioteca do Python:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Para instalar as versões, execute o seguinte comando:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    A seguinte saída de exemplo mostra a instalação do Haikunator:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    A seguinte saída de exemplo mostra a instalação do Pip para `msrestazure`: 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Confiar no certificado raiz da AC do Azure Stack Edge Pro

1. Encontre a localização do certificado no computador. Ela poderá variar dependendo do local de instalação da `az cli`. Execute o Windows PowerShell como administrador. Alterne para o caminho em que a `az cli` instalou o Python: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`.

    Para obter a localização do certificado, digite o seguinte comando:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    O cmdlet retorna a localização do certificado, conforme mostrado abaixo:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Anote a localização, pois você a usará mais tarde: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Confie no certificado raiz da AC do Azure Stack Edge Pro acrescentando-o ao certificado existente do Python. Você fornecerá o caminho para a localização na qual o certificado PEM foi salvo anteriormente.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Um caminho de exemplo é "C:\VM-scripts\rootteam3device.pem"
    
    Em seguida, digite a seguinte série de comandos no Windows PowerShell:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Conectar-se ao Azure Stack Edge Pro

1. Registre seu ambiente do Azure Stack Edge Pro executando o comando `az cloud register`.

    Em alguns cenários, a conectividade de saída direta com a Internet é roteada por um proxy ou um firewall, o que impõe a interceptação de SSL. Nesses casos, o comando az cloud register pode falhar com um erro como \"Não é possível obter pontos de extremidade da nuvem.\" Para resolver esse erro, defina as seguintes variáveis de ambiente no Windows PowerShell:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Defina variáveis de ambiente do script no ponto de extremidade do Azure Resource Manager, no local em que os recursos foram criados e no caminho em que o VHD de origem se encontra. A localização dos recursos é fixa em todos os dispositivos Azure Stack Edge Pro e é definida como `dbelocal`. Você também precisa especificar os prefixos de endereço e o endereço IP privado. Todas as variáveis de ambiente a seguir são valores baseados nos seus valores, exceto `AZURE_RESOURCE_LOCATION`, que deve ser embutida em código em `"dbelocal"`.

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Registre seu ambiente. Use os seguintes parâmetros ao executar az cloud register:

    | Valor | Descrição | Exemplo |
    | --- | --- | --- |
    | Nome do ambiente | O nome do ambiente ao qual você está tentando se conectar | Forneça um nome, por exemplo, `aze-environ` |
    | Ponto de extremidade do Resource Manager | Esta URL é `https://Management.<appliancename><dnsdomain>`. <br> Para obter essa URL, acesse a página **Dispositivos** na IU da Web local do dispositivo. |Por exemplo, `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Veja um exemplo do uso do comando acima:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Defina o ambiente ativo usando os seguintes comandos:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Veja um exemplo do uso do comando acima:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Entre no ambiente do Azure Stack Edge Pro usando o comando `az login`. Você pode entrar no ambiente do Azure Stack Edge Pro como um usuário ou uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).

   Siga estas etapas para entrar nele como um *usuário*:

   Especifique o nome de usuário e a senha diretamente no comando `az login` ou autentique-se usando um navegador. Você precisará escolher a última opção se a sua conta tiver a autenticação multifator habilitada.

   Veja um exemplo do uso de `az login`:
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Depois de usar o comando de logon, você precisará fornecer uma senha. Forneça a senha do Azure Resource Manager.

   Veja uma saída de exemplo de uma conexão bem-sucedida após o fornecimento da senha:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Tome nota dos valores de `id` e `tenantId`, pois eles correspondem, respectivamente, à sua ID de assinatura do Azure Resource Manager e à ID de locatário do Azure Resource Manager, e serão usados em uma etapa posterior.
       
   As seguintes variáveis de ambiente precisarão ser definidas para que funcionem como uma *entidade de serviço*:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   A ID de cliente do Azure Resource Manager é embutida em código. A ID de locatário do Azure Resource Manager e a ID de assinatura do Azure Resource Manager estão presentes na saída do comando `az login` executado anteriormente. O segredo do cliente do Azure Resource Manager é a senha do Azure Resource Manager definida por você.

   Para obter mais informações, confira [Senha do Azure Resource Manager](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password).

5. Altere o perfil para a versão 2019-03-01-hybrid. Para alterar a versão do perfil, execute o seguinte comando:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Veja um exemplo do uso de `az cloud update`:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Etapa 2: Criar uma máquina virtual

Um script Python é fornecido para você criar uma VM. Dependendo se você estiver conectado como um usuário ou definido como uma entidade de serviço, o script usará a entrada de acordo e criará uma VM.

1. Execute o script Python no mesmo diretório em que o Python está instalado.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Quando o script for executado, o upload do VHD levará de 20 a 30 minutos. Para ver o progresso da operação de upload, use o Gerenciador de Armazenamento do Azure ou o AzCopy.

    Veja uma saída de exemplo de uma execução bem-sucedida do script. O script cria todos os recursos em um grupo de recursos, usa esses recursos para criar uma VM e, por fim, exclui o grupo de recursos, incluindo todos os recursos criados.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Próximas etapas

[Comandos comuns da CLI do Az para máquinas virtuais do Linux](../virtual-machines/linux/cli-manage.md)
