---
title: Como instalar e executar o contêiner de análise espacial-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: O contêiner análise espacial permite que você possa detectar pessoas e distâncias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: db21f1170dacbfa1e4367e7f22143ec3d0b0f6e4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737329"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Instalar e executar o contêiner de análise espacial (versão prévia)

O contêiner análise espacial permite que você analise o vídeo de streaming em tempo real para entender as relações espaciais entre as pessoas, o movimento e as interações com objetos em ambientes físicos. Contêineres são excelentes para especificar requisitos de segurança e governança de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Depois de ter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de pesquisa Visual computacional "  target="_blank"> criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> </a> de pesquisa Visual computacional para a camada Standard S1 no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para executar o contêiner de análise espacial. Você usará sua chave e ponto de extremidade mais tarde.


### <a name="spatial-analysis-container-requirements"></a>Requisitos de contêiner de análise espacial

Para executar o contêiner de análise espacial, você precisa de um dispositivo de computação com uma [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). É recomendável que você use [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) com aceleração de GPU, no entanto, o contêiner é executado em qualquer outro computador desktop que atenda aos requisitos mínimos. Iremos nos referir a esse dispositivo como o computador host.

#### <a name="azure-stack-edge-device"></a>[Azure Stack dispositivo do Edge](#tab/azure-stack-edge)

O Azure Stack Edge é uma solução de hardware como serviço e um dispositivo de computação de borda habilitado para ia com recursos de transferência de dados de rede. Para obter instruções de preparação e configuração detalhadas, consulte a [documentação do Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Computador desktop](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware

* RAM do sistema de 4 GB
* 4 GB de RAM de GPU
* CPU de 8 núcleos
* 1 GPU NVIDIA Tesla T4
* 20 GB de espaço do HDD

#### <a name="recommended-hardware"></a>Hardware recomendado

* RAM do sistema de 32 GB
* 16 GB de RAM de GPU
* CPU de 8 núcleos
* 2 GPUs NVIDIA Tesla T4
* 50 GB de espaço SSD

Neste artigo, você baixará e instalará os seguintes pacotes de software. O computador host deve ser capaz de executar o seguinte (consulte abaixo para obter instruções):

* [Drivers gráficos NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) e [Kit de ferramentas NVIDIA CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configurações para [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (serviço de vários processos).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) e [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* Tempo de execução de [Azure IOT Edge](../../iot-edge/how-to-install-iot-edge.md) .

#### <a name="azure-vm-with-gpu"></a>[VM do Azure com GPU](#tab/virtual-machine)
Em nosso exemplo, usaremos uma VM da [série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que tem uma GPU K80.

---

| Requisito | Descrição |
|--|--|
| Câmera | O contêiner de análise espacial não está vinculado a uma marca de câmera específica. O dispositivo de câmera precisa: suporte para o protocolo RTSP (Real-Time Streaming Protocol) e a codificação H. 264, ser acessível ao computador host e ser capaz de fazer streaming em 15FPS e na resolução de 1080p. |
| Sistema operacional Linux | O [Ubuntu Desktop 18, 4 LTS](http://releases.ubuntu.com/18.04/) deve ser instalado no computador host.  |


## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contêiner

Preencha e envie o [formulário de solicitação](https://aka.ms/csgate) para solicitar aprovação para executar o contêiner.

O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de enviar o formulário, a equipe de serviços cognitivas do Azure irá examiná-lo e enviar uma decisão por email.

> [!IMPORTANT]
> * No formulário, você deve usar um endereço de email associado a uma ID de assinatura do Azure.
> * O recurso de Pesquisa Visual Computacional que você usa para executar o contêiner deve ter sido criado com a ID de assinatura aprovada do Azure.

Depois de ser aprovado, você poderá executar o contêiner depois de baixá-lo do MCR (registro de contêiner da Microsoft), descrito posteriormente neste artigo.

Você não poderá executar o contêiner se sua assinatura do Azure não tiver sido aprovada.

## <a name="set-up-the-host-computer"></a>Configurar o computador host

É recomendável que você use um dispositivo de borda Azure Stack para o computador host. Clique em **computador desktop** se você estiver configurando um dispositivo diferente ou **máquina virtual** se estiver utilizando uma VM.

#### <a name="azure-stack-edge-device"></a>[Azure Stack dispositivo do Edge](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Configurar a computação no portal do Azure Stack Edge 
 
A análise espacial usa os recursos de computação do Azure Stack Edge para executar uma solução de ia. Para habilitar os recursos de computação, verifique se: 

* Você [conectou e ativou](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) seu Azure Stack dispositivo de borda. 
* Você tem um sistema cliente Windows executando o PowerShell 5,0 ou posterior para acessar o dispositivo.  
* Para implantar um cluster kubernetes, você precisa configurar o dispositivo de borda Azure Stack por meio da **interface do usuário local** na [portal do Azure](https://portal.azure.com/): 
  1. Habilite o recurso de computação em seu dispositivo Azure Stack Edge. Para habilitar a computação, vá para a página **computação** na interface da Web do seu dispositivo. 
  2. Selecione uma interface de rede que você deseja habilitar para computação e clique em **habilitar**. Isso criará um comutador virtual em seu dispositivo, nessa interface de rede.
  3. Deixe os endereços IP do nó de teste do kubernetes e os endereços IP dos serviços externos do kubernetes em branco.
  4. Clique em **Aplicar**. Esta operação pode levar cerca de dois minutos. 

![Configurar a computação](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Configurar uma função de computação de borda e criar um recurso do Hub IoT

Na [portal do Azure](https://portal.azure.com/), navegue até o recurso de borda do Azure Stack. Na página **visão geral** ou na lista de navegação, clique no **botão introdução** à computação de borda. No bloco  **Configurar computação de borda**   , clique em **Configurar**. 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

Na página **Configurar computação de borda**   , escolha um hub IOT existente ou escolha criar um novo. Por padrão, um tipo de preço padrão (S1) é usado para criar um recurso do Hub IoT. Para usar um recurso de Hub IoT de camada gratuita, crie um e, em seguida, selecione-o. O recurso do Hub IoT usa a mesma assinatura e grupo de recursos que é usada pelo Azure Stack recurso do Edge 

Clique em **Criar**. A criação de recursos do Hub IoT pode levar alguns minutos. Depois que o recurso do Hub IoT for criado, o bloco **Configurar computação de borda** será atualizado para mostrar a nova configuração. Para confirmar que a função de computação de borda foi configurada, selecione **Exibir configuração** no bloco **Configurar computação**   .

Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. O tempo de execução de Azure IoT Edge já estará em execução no dispositivo IoT Edge.

> [!NOTE]
> * Atualmente, apenas a plataforma Linux tem suporte para dispositivos IoT Edge. Para obter ajuda para solucionar problemas do dispositivo Azure Stack Edge, consulte o artigo [registro em log e solução de problemas](spatial-analysis-logging.md) .
> * Para saber mais sobre como configurar um dispositivo de IoT Edge para se comunicar por meio de um servidor proxy, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Habilitar MPS no Azure Stack Edge 

1. Execute uma sessão do Windows PowerShell como administrador. 

2. Verifique se o serviço de Gerenciamento Remoto do Windows está em execução no seu cliente. No terminal do PowerShell, use o comando a seguir 
    
    ```powershell
    winrm quickconfig
    ```
    
    Se você vir avisos sobre uma exceção de firewall, verifique o tipo de conexão de rede e veja a documentação do [gerenciamento remoto do Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) .

3. Atribua uma variável ao endereço IP do dispositivo. 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. Para adicionar o endereço IP do seu dispositivo à lista de hosts confiáveis do cliente, use o seguinte comando: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Inicie uma sessão do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Forneça a senha quando solicitado. Use a mesma senha usada para entrar na interface do usuário da Web local. A senha padrão da interface do usuário da Web local é `Password1` .

Digite `Start-HcsGpuMPS` para iniciar o serviço MPS no dispositivo. 

Para obter ajuda para solucionar problemas do dispositivo Azure Stack Edge, consulte [Solucionando problemas do dispositivo Azure Stack Edge](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Computador desktop](#tab/desktop-machine)

Siga estas instruções se o computador host não for um dispositivo Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Instalar o NVIDIA CUDA Toolkit e os drivers gráficos NVIDIA no computador host

Use o script bash a seguir para instalar os drivers gráficos NVIDIA necessários e o kit de ferramentas CUDA.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Reinicialize o computador e execute o comando a seguir.

```bash
nvidia-smi
```

A saída a seguir será exibida.

![Saída do driver NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Instalar o Docker CE e o NVIDIA-docker2 no computador host

Instale o Docker CE no computador host.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Instale o pacote de software *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Habilitar o NVIDIA MPS no computador host

> [!TIP]
> * Não instale os MPS se o recurso de computação de sua GPU for menor que 7. x (pré-voltar). Consulte [compatibilidade CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) para referência. 
> * Execute as instruções de MPS em uma janela de terminal no computador host. Não dentro de sua instância de contêiner do Docker.

Para obter o melhor desempenho e utilização, configure a GPU do computador host para [MPS (serviço de vários processos NVIDIA)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Execute as instruções de MPS em uma janela de terminal no computador host.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Configurar Azure IoT Edge no computador host

Para implantar o contêiner de análise espacial no computador host, crie uma instância de um serviço de [Hub IOT do Azure](../../iot-hub/iot-hub-create-through-portal.md) usando o tipo de preço Standard (S1) ou Free (F0). Se o computador host for um Azure Stack Edge, use a mesma assinatura e grupo de recursos que é usado pelo recurso de borda do Azure Stack.

Use o CLI do Azure para criar uma instância do Hub IoT do Azure. Substitua os parâmetros quando apropriado. Como alternativa, você pode criar o Hub IoT do Azure no [portal do Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
sudo az login
sudo az account set --subscription <name or ID of Azure Subscription>
sudo az group create --name "test-resource-group" --location "WestUS"

sudo az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

sudo az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

Se o computador host não for um dispositivo Azure Stack Edge, será necessário instalar [Azure IOT Edge](../../iot-edge/how-to-install-iot-edge.md) versão 1.0.9. Siga estas etapas para baixar a versão correta:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Copie a lista gerada.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Instale a chave pública do Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Atualize as listas de pacotes em seu dispositivo.

```bash
sudo apt-get update
```

Instale a versão 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Em seguida, registre o computador host como um dispositivo IoT Edge em sua instância do Hub IoT usando uma [cadeia de conexão](../../iot-edge/how-to-manual-provision-symmetric-key.md?view=iotedge-2018-06).

Você precisa conectar o dispositivo IoT Edge ao Hub IoT do Azure. Você precisa copiar a cadeia de conexão do dispositivo IoT Edge criado anteriormente. Como alternativa, você pode executar o comando abaixo na CLI do Azure.

```bash
sudo az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

No computador host, abra  `/etc/iotedge/config.yaml` para edição. Substituir `ADD DEVICE CONNECTION STRING HERE` pela cadeia de conexão. Salve e feche o arquivo. Execute este comando para reiniciar o serviço de IoT Edge no computador host.

```bash
sudo systemctl restart iotedge
```

Implante o contêiner de análise espacial como um módulo IoT no computador host, seja da [portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Se você estiver usando o portal, defina o URI da imagem para o local do registro de contêiner do Azure. 

Use as etapas a seguir para implantar o contêiner usando o CLI do Azure.

#### <a name="azure-vm-with-gpu"></a>[VM do Azure com GPU](#tab/virtual-machine)

Uma máquina virtual do Azure com uma GPU também pode ser usada para executar análise espacial. O exemplo a seguir usará uma VM da [série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que tenha uma GPU K80.

#### <a name="create-the-vm"></a>Criar a VM

Abra o assistente para [criar uma máquina virtual](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) na portal do Azure.

Dê um nome à sua VM e selecione a região a ser (EUA) oeste dos EUA 2. Certifique-se de definir `Availability Options` como "sem redundância de infraestrutura necessária". Consulte a figura abaixo para obter a configuração completa e a próxima etapa para ajudar a localizar o tamanho correto da VM. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.png" alt-text="Detalhes da configuração da máquina virtual." lightbox="media/spatial-analysis/virtual-machine-instance-details.png":::

Para localizar o tamanho da VM, selecione "Ver todos os tamanhos" e, em seguida, exiba a lista de "tamanhos de VM de armazenamento não Premium", mostrada abaixo.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Tamanhos de máquina virtual." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Em seguida, selecione **NC6** ou **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="seleção promocional" lightbox="media/spatial-analysis/promotional-selection.png":::

Em seguida, crie a VM. Depois de criado, navegue até o recurso da VM na portal do Azure e selecione `Extensions` no painel esquerdo. A janela extensões será exibida com todas as extensões disponíveis. Selecione `NVIDIA GPU Driver Extension` , clique em criar e conclua o assistente.

Depois que a extensão for aplicada com êxito, navegue até a página principal da VM na portal do Azure e clique em `Connect` . A VM pode ser acessada por meio de SSH ou RDP. O RDP será útil, pois será habilitado a exibição da janela do visualizador (explicado posteriormente). Configure o acesso de RDP seguindo [estas etapas](../../virtual-machines/linux/use-remote-desktop.md) e abrindo uma conexão de área de trabalho remota para a VM.

### <a name="verify-graphics-drivers-are-installed"></a>Verificar se os drivers gráficos estão instalados

Execute o comando a seguir para verificar se os drivers gráficos foram instalados com êxito. 

```bash
nvidia-smi
```

A saída a seguir será exibida.

![Saída do driver NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Instalar o Docker CE e o NVIDIA-docker2 na VM

Execute os comandos a seguir um de cada vez para instalar o Docker CE e o NVIDIA-docker2 na VM.

Instale o Docker CE no computador host.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Instale o pacote de software *NVIDIA-Docker-2* .

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Agora que você configurou e configurou sua VM, siga as etapas abaixo para implantar o contêiner de análise espacial. 

---

### <a name="iot-deployment-manifest"></a>Manifesto de implantação de IoT

Para simplificar a implantação de contêiner em vários computadores host, você pode criar um arquivo de manifesto de implantação para especificar as opções de criação de contêiner e variáveis de ambiente. Você pode encontrar um exemplo de um manifesto de implantação [para Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [outros computadores desktop](https://go.microsoft.com/fwlink/?linkid=2152270)e [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) no github.

A tabela a seguir mostra as várias variáveis de ambiente usadas pelo módulo IoT Edge. Você também pode defini-los no manifesto de implantação vinculado acima, usando o `env` atributo em `spatialanalysis` :

| Nome da Configuração | Valor | Descrição|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Detalhes Extensa | Nível de log, selecione um dos dois valores|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Não modificar|
| ARCHON_PERF_MARKER| false| Defina como verdadeiro para o log de desempenho, caso contrário, isso deve ser falso| 
| ARCHON_NODES_LOG_LEVEL | Detalhes Extensa | Nível de log, selecione um dos dois valores|
| OMP_WAIT_POLICY | PASSIVO | Não modificar|
| QT_X11_NO_MITSHM | 1 | Não modificar|
| APIKEY | sua chave de API| Colete esse valor de portal do Azure de seu recurso de Pesquisa Visual Computacional. Você pode encontrá-lo na seção **chave e ponto de extremidade** do recurso. |
| COBRANÇA | seu URI de ponto de extremidade| Colete esse valor de portal do Azure de seu recurso de Pesquisa Visual Computacional. Você pode encontrá-lo na seção **chave e ponto de extremidade** do recurso.|
| EULA | aceitar | Esse valor precisa ser definido para *aceitar* o contêiner a ser executado |
| DISPLAY | : 1 | Esse valor precisa ser igual ao da saída do `echo $DISPLAY` no computador host. Os dispositivos Azure Stack Edge não têm uma exibição. Essa configuração não é aplicável|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Adicione essa variável de ambiente se sua GPU **não** for T4 ou NVIDIA 2080 it|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Adicione essa variável de ambiente se sua GPU **não** for T4 ou NVIDIA 2080 it|
| KEY_ENV | Chave de criptografia do ASE | Adicionar esta variável de ambiente se Video_URL for uma cadeia de caracteres ofuscada |
| IV_ENV | Vetor de inicialização | Adicionar esta variável de ambiente se Video_URL for uma cadeia de caracteres ofuscada|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

Depois de atualizar o manifesto de implantação para [dispositivos Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [um computador desktop ou uma](https://go.microsoft.com/fwlink/?linkid=2152270) [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) com suas próprias configurações e seleção de operações, você pode usar o comando a seguir [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows) para implantar o contêiner no computador host, como um módulo IOT Edge.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json --subscription "<subscriptionId>"
```

|Parâmetro  |Descrição  |
|---------|---------|
| `--hub-name` | O nome do Hub IoT do Azure. |
| `--content` | O nome do arquivo de implantação. |
| `--target-condition` | O nome do dispositivo IoT Edge para o computador host. |
| `-–subscription` | ID ou nome da assinatura. |

Esse comando iniciará a implantação. Navegue até a página da instância do Hub IoT do Azure no portal do Azure para ver o status da implantação. O status pode ser mostrado como *417 – a configuração de implantação do dispositivo não está definida* até que o dispositivo termine de baixar as imagens de contêiner e comece a ser executado.

## <a name="validate-that-the-deployment-is-successful"></a>Validar se a implantação foi bem-sucedida

Há várias maneiras de validar se um contêiner está em execução. Localize o *status de tempo de execução* nas **configurações do módulo IOT Edge** para o módulo análise espacial em sua instância do Hub IoT do Azure na portal do Azure. Valide se o valor **desejado** e o **valor relatado** para o *status de tempo de execução* estão *em execução*.

![Exemplo de verificação de implantação](./media/spatial-analysis/deployment-verification.png)

Depois que a implantação for concluída e o contêiner estiver em execução, o **computador host** começará a enviar eventos para o Hub IOT do Azure. Se você usou a `.debug` versão das operações, verá uma janela do visualizador para cada câmera configurada no manifesto de implantação. Agora você pode definir as linhas e zonas que deseja monitorar no manifesto de implantação e seguir as instruções para implantar novamente. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Configurar as operações executadas pela análise espacial

Você precisará usar [operações de análise espacial](spatial-analysis-operations.md) para configurar o contêiner para usar câmeras conectadas, configurar as operações e muito mais. Para cada dispositivo de câmera que você configurar, as operações para análise espacial gerarão um fluxo de saída de mensagens JSON, enviadas à sua instância do Hub IoT do Azure.

## <a name="redeploy-or-delete-the-deployment"></a>Reimplantar ou excluir a implantação

Se precisar atualizar a implantação, você precisará verificar se as implantações anteriores foram implantadas com êxito ou se precisa excluir IoT Edge implantações de dispositivo que não foram concluídas. Caso contrário, essas implantações continuarão, deixando o sistema em um estado inadequado. Você pode usar o portal do Azure ou o [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows).

## <a name="use-the-output-generated-by-the-container"></a>Usar a saída gerada pelo contêiner

Se você quiser começar a consumir a saída gerada pelo contêiner, consulte os seguintes artigos:

*   Use o SDK do hub de eventos do Azure para a linguagem de programação escolhida para se conectar ao ponto de extremidade do Hub IoT do Azure e receber os eventos. Consulte [ler mensagens do dispositivo para a nuvem do ponto de extremidade interno](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
*   Configure o roteamento de mensagens no Hub IoT do Azure para enviar os eventos a outros pontos de extremidade ou salvar os eventos no armazenamento de BLOBs do Azure, etc. Consulte [Roteamento de mensagens do Hub IOT](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Executando análise espacial com um arquivo de vídeo gravado

Você pode usar a análise espacial com vídeo gravado ou ao vivo. Para usar a análise espacial do vídeo gravado, tente gravar um arquivo de vídeo e salvá-lo como um arquivo MP4. Crie uma conta de armazenamento de BLOBs no Azure ou use uma existente. Em seguida, atualize as seguintes configurações de armazenamento de BLOBs no portal do Azure:
    1. Alterar **transferência segura necessária** para **desabilitada**
    2. Alterar **permitir acesso público de blob** para **habilitado**

Navegue até a seção **contêiner** e crie um novo contêiner ou use um existente. Em seguida, carregue o arquivo de vídeo no contêiner. Expanda as configurações de arquivo para o arquivo carregado e selecione **gerar SAS**. Certifique-se de definir a **data de expiração** de tempo suficiente para cobrir o período de teste. Definir **protocolos permitidos** para *http* (não há suporte para *https* ).

Clique em **gerar token e URL de SAS** e copie a URL de SAS do blob. Substitua o Iniciando `https` por `http` e teste a URL em um navegador que ofereça suporte à reprodução de vídeo.

Substitua `VIDEO_URL` no manifesto de implantação para seu [dispositivo Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) com a URL que você criou, para todos os grafos. Defina `VIDEO_IS_LIVE` como `false` e reimplante o contêiner de análise espacial com o manifesto atualizado. Veja o exemplo abaixo.

O módulo análise espacial começará a consumir o arquivo de vídeo e será continuamente reproduzido automaticamente.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar problemas ao iniciar ou executar o contêiner, consulte [telemetria e solução de problemas](spatial-analysis-logging.md) para obter as etapas para problemas comuns. Este artigo também contém informações sobre como gerar e coletar logs e como coletar a integridade do sistema.

## <a name="billing"></a>Cobrança

O contêiner de análise espacial envia informações de cobrança ao Azure, usando um recurso de Pesquisa Visual Computacional em sua conta do Azure. O uso da análise espacial na visualização pública é gratuito no momento. 

Os contêineres de serviços cognitivas do Azure não são licenciados para serem executados sem serem conectados ao ponto de extremidade de medição/cobrança. Você precisa permitir que os contêineres comuniquem as informações de cobrança com o ponto de extremidade de cobrança em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente, como o vídeo ou a imagem que está sendo analisada para a Microsoft.


## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar o contêiner de análise espacial. Em resumo:

* A análise espacial é um contêiner do Linux para o Docker.
* As imagens de contêiner são baixadas do registro de contêiner da Microsoft.
* As imagens de contêiner são executadas como módulos IoT no Azure IoT Edge.
* Como configurar o contêiner e implantá-lo em um computador host.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)