---
title: Como instalar e executar o contêiner de Análise Espacial – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: O contêiner de Análise Espacial permite que você detecte pessoas e distâncias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d257a77940b460bf8be64e3f8376353a859365f7
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284712"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Instalar e executar o contêiner de Análise Espacial (Versão prévia)

O contêiner de Análise Espacial permite que você analise vídeos de streaming em tempo real para entender as relações espaciais entre as pessoas, a movimentação delas e a interação delas com os objetos em ambientes físicos. Contêineres são excelentes para especificar requisitos de segurança e governança de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Criar um recurso de Pesquisa Visual Computacional"  target="_blank">crie um recurso de Pesquisa Visual Computacional </a> da camada Standard S1 no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para executar o contêiner de Análise Espacial. Você usará a chave e ponto de extremidade mais tarde.


### <a name="spatial-analysis-container-requirements"></a>Requisitos do contêiner de Análise Espacial

Para executar o contêiner de Análise Espacial, você precisa de um dispositivo de computação com uma [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). É recomendado que você use o [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) com aceleração de GPU. No entanto, o contêiner pode ser executado em qualquer outro computador desktop que atenda aos requisitos mínimos. Mencionaremos esse dispositivo como o computador host.

#### <a name="azure-stack-edge-device"></a>[Dispositivo do Azure Stack Edge](#tab/azure-stack-edge)

O Azure Stack Edge é uma solução de hardware como serviço e um dispositivo de computação de borda habilitado para IA com funcionalidades de transferência de dados de rede. Para obter instruções detalhadas de preparação e configuração, confira a [Documentação do Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Computador desktop](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware

* 4 GB de RAM do sistema
* 4 GB de RAM da GPU
* CPU com oito núcleos
* 1 GPU NVIDIA Tesla T4
* 20 GB de espaço do HD

#### <a name="recommended-hardware"></a>Hardware recomendado

* 32 GB de RAM do sistema
* 16 GB de RAM da GPU
* CPU com oito núcleos
* 2 GPUs NVIDIA Tesla T4
* 50 GB de espaço de SSD

Neste artigo, você baixará e instalará os pacotes de software a seguir. O computador host precisa ser capaz de executar o seguinte (confira as instruções abaixo):

* [Drivers de gráficos NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) e [Kit de ferramentas NVIDIA CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Configurações para [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (serviço de vários processos).
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) e [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* Runtime do [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md).

#### <a name="azure-vm-with-gpu"></a>[VM do Azure com GPU](#tab/virtual-machine)
No exemplo, utilizaremos uma [VM da série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que tem uma GPU K80.

---

| Requisito | Descrição |
|--|--|
| Câmera | O contêiner de Análise Espacial não está vinculado a nenhuma marca de câmera específica. O dispositivo de câmera precisa: dar suporte ao protocolo RTSP (Real-Time Streaming Protocol) e à codificação H.264, ser acessível ao computador host e ser capaz de fazer streaming a 15 FPS e com a resolução de 1080p. |
| Sistema operacional Linux | O [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) precisa ser instalado no computador host.  |


## <a name="request-approval-to-run-the-container"></a>Solicitar aprovação para executar o contêiner

Preencha e envie o [formulário de solicitação](https://aka.ms/csgate) a fim de solicitar aprovação para executar o contêiner.

O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de enviar o formulário, a equipe dos Serviços Cognitivos do Azure o examinará e enviará uma decisão por email.

> [!IMPORTANT]
> * No formulário, você precisa usar um endereço de email associado a uma ID da assinatura do Azure.
> * O recurso de Pesquisa Visual Computacional usado para executar o contêiner precisa ter sido criado com a ID da assinatura do Azure aprovada.

Após sua aprovação, você poderá executar o contêiner depois de baixá-lo do MCR (Microsoft Container Registry), o que será descrito posteriormente neste artigo.

Você não poderá executar o contêiner se a sua assinatura do Azure não for aprovada.

## <a name="set-up-the-host-computer"></a>Configurar o computador host

É recomendado que você use um dispositivo do Azure Stack Edge para o computador host. Clique em **Computador Desktop** se você estiver configurando um dispositivo diferente ou em **Máquina Virtual** se estiver utilizando uma VM.

#### <a name="azure-stack-edge-device"></a>[Dispositivo do Azure Stack Edge](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Configurar a computação no portal do Azure Stack Edge 
 
A Análise Espacial usa os recursos de computação do Azure Stack Edge para executar uma solução de IA. Para habilitar os recursos de computação, verifique se: 

* Você [conectou e ativou](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) o dispositivo do Azure Stack Edge. 
* Você tem um sistema cliente Windows executando o PowerShell 5.0 ou posterior, para acessar o dispositivo.  
* Para implantar um cluster do Kubernetes, você precisa configurar o dispositivo do Azure Stack Edge por meio da **IU Local** no [portal do Azure](https://portal.azure.com/): 
  1. Habilite o recurso de computação no dispositivo do Azure Stack Edge. Para habilitar a computação, acesse a página **Computação** na interface da Web do dispositivo. 
  2. Selecione o adaptador de rede que você deseja habilitar para a computação e clique em **Habilitar**. Isso criará um comutador virtual no dispositivo, nesse adaptador de rede.
  3. Deixe em branco os endereços IP do nó de teste do Kubernetes e os endereços IP dos serviços externos do Kubernetes.
  4. Clique em **Aplicar**. Essa operação pode demorar cerca de dois minutos. 

![Configurar a computação](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Configurar uma função de computação de borda e criar um recurso de Hub IoT

No [portal do Azure](https://portal.azure.com/), navegue até o recurso do Azure Stack Edge. Na página **Visão geral** ou na lista de navegação, clique no botão **Introdução** da computação de borda. No bloco  **Configurar a Computação de borda** , clique em **Configurar**. 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

Na página  **Configurar a Computação de borda** , escolha um Hub IoT existente ou escolha criar outro. Por padrão, um tipo de preço Standard (S1) é usado para criar um recurso de Hub IoT. Para usar um recurso de Hub IoT de camada gratuita, crie e selecione-o. O recurso de Hub IoT usa a mesma assinatura e o mesmo grupo de recursos usados pelo recurso do Azure Stack Edge 

Clique em **Criar**. A criação do recurso de Hub IoT demora alguns minutos. Depois que o recurso de Hub IoT for criado, o bloco  **Configurar a computação de borda** será atualizado para mostrar a nova configuração. Para confirmar se a função de computação de borda foi configurada, selecione  **Exibir a configuração** no bloco  **Configurar a computação** .

Quando a função de computação de borda está configurada no dispositivo de borda, são criados dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. O runtime do Azure IoT Edge já estará em execução no dispositivo do IoT Edge.

> [!NOTE]
> * No momento, somente a plataforma Linux é compatível com os dispositivos do IoT Edge. Para obter ajuda com a solução de problemas do dispositivo do Azure Stack Edge, confira o artigo [Registro em log e solução de problemas](spatial-analysis-logging.md).
> * Para saber mais sobre como configurar um dispositivo do IoT Edge para se comunicar por meio de um servidor proxy, confira [Configurar um dispositivo do IoT Edge para se comunicar por meio de um servidor proxy](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>Habilitar o MPS no Azure Stack Edge 

1. Execute uma sessão do Windows PowerShell como Administrador. 

2. Verifique se o serviço de Gerenciamento Remoto do Windows está em execução no cliente. No terminal do PowerShell, use os seguintes comandos 
    
    ```powershell
    winrm quickconfig
    ```
    
    Se aparecerem avisos sobre uma exceção de firewall, verifique o tipo de conexão de rede e confira a documentação do [Gerenciamento Remoto do Windows](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management).

3. Atribua uma variável ao endereço IP do dispositivo. 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. Para adicionar o endereço IP do dispositivo à lista de hosts confiáveis do cliente, use o seguinte comando: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Inicie uma sessão do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Forneça a senha quando solicitada. Use a mesma senha usada para entrar na IU da Web local. A senha padrão da IU da Web local é `Password1`.

Digite `Start-HcsGpuMPS` para iniciar o serviço MPS no dispositivo. 

Para solucionar problemas de um dispositivo do Azure Stack Edge, confira [Solução de problemas do dispositivo do Azure Stack Edge](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[Computador desktop](#tab/desktop-machine)

Siga estas instruções se o computador host não é um dispositivo do Azure Stack Edge.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Instalar o Kit de ferramentas NVIDIA CUDA e os drivers de gráficos NVIDIA no computador host

Use o script Bash a seguir para instalar os drivers de gráficos NVIDIA necessários e o Kit de ferramentas CUDA.

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

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Instalar o Docker CE e o nvidia-docker2 no computador host

Instale o Docker CE no computador host.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Instale o pacote de software *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Habilitar o MPS do NVIDIA no computador host

> [!TIP]
> * Não instale o MPS se a funcionalidade de computação da GPU for inferior a 7.x (anterior à Volta). Confira [Compatibilidade do CUDA](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) para obter uma referência. 
> * Execute as instruções do MPS em uma janela de terminal no computador host. Não dentro da instância de contêiner do Docker.

Para obter o melhor nível de desempenho e utilização, configure a GPU do computador host para [MPS (Serviço de Vários Processos) do NVIDIA](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf). Execute as instruções do MPS em uma janela de terminal no computador host.

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

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Configurar o Azure IoT Edge no computador host

Para implantar o contêiner de Análise Espacial no computador host, crie uma instância de um serviço [Hub IoT do Azure](../../iot-hub/iot-hub-create-through-portal.md) usando o tipo de preço Standard (S1) ou Gratuito (F0). 

Como usar a CLI do Azure para criar uma instância do Hub IoT do Azure. Substitua os parâmetros quando apropriado. Como alternativa, você pode criar o Hub IoT do Azure no [portal do Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Confira [Suporte de Região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) para saber quais são as regiões disponíveis.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Será necessário instalar o [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) versão 1.0.9. Siga estas etapas para baixar a versão correta:

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
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Atualize as listas de pacotes no dispositivo.

```bash
sudo apt-get update
```

Instale a versão 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Depois, registre o computador host como um dispositivo do IoT Edge na instância do Hub IoT usando uma [cadeia de conexão](../../iot-edge/how-to-register-device.md).

Você precisa conectar o dispositivo do IoT Edge com o Hub IoT do Azure. Você precisa copiar a cadeia de conexão do dispositivo do IoT Edge já criado. Como alternativa, você pode executar o comando abaixo na CLI do Azure.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

No computador host, abra `/etc/iotedge/config.yaml` para edição. Substitua `ADD DEVICE CONNECTION STRING HERE` pela cadeia de conexão. Salve e feche o arquivo. Execute este comando para reiniciar o serviço IoT Edge no computador host.

```bash
sudo systemctl restart iotedge
```

Implante o contêiner de Análise Espacial como um Módulo de IoT no computador host usando o [portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou a [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Se você estiver usando o portal, defina o URI da imagem para a localização do Registro de Contêiner do Azure. 

Siga as etapas abaixo para implantar o contêiner usando a CLI do Azure.

#### <a name="azure-vm-with-gpu"></a>[VM do Azure com GPU](#tab/virtual-machine)

Também é possível usar uma máquina virtual do Azure com uma GPU para executar a Análise Espacial. O exemplo abaixo usará uma VM da [série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que tenha uma GPU K80.

#### <a name="create-the-vm"></a>Criar a VM

Abra o assistente [Criar uma máquina virtual](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) no portal do Azure.

Dê um nome à VM e selecione a região como (EUA) Oeste dos EUA 2. Defina `Availability Options` como "A redundância de infraestrutura não é necessária". Confira a figura abaixo para obter a configuração completa e a próxima etapa como uma ajuda para localizar o tamanho correto da VM. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Detalhes da configuração da máquina virtual." lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Para localizar o tamanho da VM, selecione "Ver todos os tamanhos" e veja a lista de "Tamanhos de VM de armazenamento que não são Premium", mostrada abaixo.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="Tamanhos de máquina virtual." lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Depois, selecione **NC6** ou **NC6_Promo**.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="seleção promocional" lightbox="media/spatial-analysis/promotional-selection.png":::

Depois, crie a VM. Após a criação, navegue até o recurso de VM no portal do Azure e selecione `Extensions` no painel esquerdo. A janela de extensões será exibida com todas as extensões disponíveis. Selecione `NVIDIA GPU Driver Extension`, clique em criar e conclua o assistente.

Depois que a extensão for aplicada com êxito, navegue até a página principal da VM no portal do Azure e clique em `Connect`. A VM pode ser acessada por meio de SSH ou RDP. O RDP será útil, pois habilitará a exibição da janela do visualizador (explicada posteriormente). Configure o acesso de RDP seguindo [estas etapas](../../virtual-machines/linux/use-remote-desktop.md) e abrindo uma conexão de área de trabalho remota com a VM.

### <a name="verify-graphics-drivers-are-installed"></a>Verificar se os drivers de gráficos estão instalados

Execute o comando a seguir para verificar se os drivers de gráficos foram instalados com êxito. 

```bash
nvidia-smi
```

A saída a seguir será exibida.

![Saída do driver NVIDIA](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Instalar o Docker CE e o nvidia-docker2 na VM

Execute os comandos a seguir, um de cada vez, para instalar o Docker CE e o nvidia-docker2 na VM.

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


Instale o pacote de software *nvidia-docker-2*.

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

Agora que você instalou e configurou a VM, siga as etapas abaixo para configurar o Azure IoT Edge. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Configurar o Azure IoT Edge na VM

Para implantar o contêiner de Análise Espacial na VM, crie uma instância de um serviço [Hub IoT do Azure](../../iot-hub/iot-hub-create-through-portal.md) usando o tipo de preço Standard (S1) ou Gratuito (F0).

Como usar a CLI do Azure para criar uma instância do Hub IoT do Azure. Substitua os parâmetros quando apropriado. Como alternativa, você pode criar o Hub IoT do Azure no [portal do Azure](https://portal.azure.com/).

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Confira [Suporte de Região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) para saber quais são as regiões disponíveis.
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Será necessário instalar o [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) versão 1.0.9. Siga estas etapas para baixar a versão correta:

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
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Atualize as listas de pacotes no dispositivo.

```bash
sudo apt-get update
```

Instale a versão 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Depois, registre a VM como um dispositivo do IoT Edge na instância do Hub IoT usando uma [cadeia de conexão](../../iot-edge/how-to-register-device.md).

Você precisa conectar o dispositivo do IoT Edge com o Hub IoT do Azure. Você precisa copiar a cadeia de conexão do dispositivo do IoT Edge já criado. Como alternativa, você pode executar o comando abaixo na CLI do Azure.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Na VM, abra o `/etc/iotedge/config.yaml` para edição. Substitua `ADD DEVICE CONNECTION STRING HERE` pela cadeia de conexão. Salve e feche o arquivo. Execute este comando para reiniciar o serviço IoT Edge na VM.

```bash
sudo systemctl restart iotedge
```

Implante o contêiner de Análise Espacial como um Módulo de IoT na VM usando o [portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou a [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows). Se você estiver usando o portal, defina o URI da imagem para a localização do Registro de Contêiner do Azure. 

Siga as etapas abaixo para implantar o contêiner usando a CLI do Azure.

---

### <a name="iot-deployment-manifest"></a>Manifesto de implantação de IoT

Para simplificar a implantação do contêiner em vários computadores host, você pode criar um arquivo de manifesto de implantação para especificar as opções de criação do contêiner e as variáveis de ambiente. Veja no GitHub um exemplo de um manifesto de implantação [para o Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [outros computadores desktop](https://go.microsoft.com/fwlink/?linkid=2152270) e [a VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189).

A tabela a seguir mostra as diversas variáveis de ambiente usadas pelo Módulo do IoT Edge. Você também pode defini-las no manifesto de implantação vinculado acima, usando o atributo `env` em `spatialanalysis`:

| Nome da Configuração | Valor | Descrição|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info; Verbose | Nível de registros em log. Selecione um dos dois valores|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Não modifique o valor|
| ARCHON_PERF_MARKER| false| Defina esse valor como true para log de desempenho, caso contrário, defina-o como false| 
| ARCHON_NODES_LOG_LEVEL | Info; Verbose | Nível de registros em log. Selecione um dos dois valores|
| OMP_WAIT_POLICY | PASSIVE | Não modifique o valor|
| QT_X11_NO_MITSHM | 1 | Não modifique o valor|
| APIKEY | sua chave de API| Colete esse valor no portal do Azure, no recurso de Pesquisa Visual Computacional. Encontre-o na seção **Chave e ponto de extremidade** do recurso. |
| COBRANÇA | seu URI de ponto de extremidade| Colete esse valor no portal do Azure, no recurso de Pesquisa Visual Computacional. Encontre-o na seção **Chave e ponto de extremidade** do recurso.|
| EULA | accept | Esse valor precisa ser definido como *accept* para que o contêiner seja executado |
| DISPLAY | :1 | Esse valor precisa ser igual ao da saída de `echo $DISPLAY` no computador host. Os dispositivos do Azure Stack Edge não têm nenhuma exibição. Essa configuração não é aplicável|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Adicione essa variável de ambiente se a GPU **não** for T4 ou NVIDIA 2080 Ti|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Adicione essa variável de ambiente se a GPU **não** for T4 ou NVIDIA 2080 Ti|
| KEY_ENV | Chave de criptografia do ASE | Adicione essa variável de ambiente se Video_URL for uma cadeia de caracteres oculta |
| IV_ENV | Vetor de inicialização | Adicione essa variável de ambiente se Video_URL for uma cadeia de caracteres oculta|

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

Depois de atualizar o manifesto de implantação para [dispositivos do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [um computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou [uma VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) com suas configurações e sua seleção de operações, você poderá usar o comando da [CLI do Azure](../cognitive-services-apis-create-account-cli.md?tabs=windows) abaixo para implantar o contêiner no computador host, como um módulo do IoT Edge.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parâmetro  |Descrição  |
|---------|---------|
| `--hub-name` | O nome do Hub IoT do Azure. |
| `--content` | O nome do arquivo de implantação. |
| `--target-condition` | O nome do dispositivo do IoT Edge para o computador host. |
| `-–subscription` | ID ou nome da assinatura. |

Esse comando iniciará a implantação. Navegue até a página da instância do Hub IoT do Azure no portal do Azure para ver o status da implantação. O status talvez seja mostrado como *417 – A configuração de implantação do dispositivo não está definida* até que o dispositivo termine de baixar as imagens de contêiner e inicie a execução.

## <a name="validate-that-the-deployment-is-successful"></a>Validar se a implantação foi bem-sucedida

Há várias maneiras de validar se um contêiner está em execução. Localize o *Status do Runtime* nas **Configurações do Módulo do IoT Edge** do módulo de Análise Espacial na instância do Hub IoT do Azure no portal do Azure. Valide se o **Valor desejado** e o **Valor relatado** para o *Status do Runtime* são *Em execução*.

![Verificação da implantação de exemplo](./media/spatial-analysis/deployment-verification.png)

Depois que a implantação for concluída e o contêiner estiver em execução, o **computador host** começará a enviar eventos ao Hub IoT do Azure. Se você usou a versão `.debug` das operações, verá uma janela do visualizador para cada câmera configurada no manifesto de implantação. Agora você pode definir as linhas e as zonas que deseja monitorar no manifesto de implantação e seguir as instruções para fazer uma nova implantação. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Configurar as operações executadas pela Análise Espacial

Você precisará usar as [operações de Análise Espacial](spatial-analysis-operations.md) a fim de configurar o contêiner para usar câmeras conectadas, configurar as operações e fazer outras configurações. Para cada dispositivo de câmera configurado, as operações da Análise Espacial vão gerar um fluxo de saída de mensagens JSON enviadas à instância do Hub IoT do Azure.

## <a name="use-the-output-generated-by-the-container"></a>Usar a saída gerada pelo contêiner

Se você deseja começar a consumir a saída gerada pelo contêiner, confira os seguintes artigos:

*    Use o SDK do Hub de Eventos do Azure da linguagem de programação escolhida para se conectar com o ponto de extremidade do Hub IoT do Azure e receber os eventos. Confira [Ler as mensagens de dispositivo para nuvem do ponto de extremidade interno](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para obter mais informações. 
*    Configure o roteamento de mensagens no Hub IoT do Azure para enviar os eventos a outros pontos de extremidade ou salvar os eventos no Armazenamento de Blobs do Azure etc. Confira [Roteamento de mensagens do Hub IoT](../../iot-hub/iot-hub-devguide-messages-d2c.md) para obter mais informações. 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Executando a Análise Espacial com um arquivo de vídeo gravado

Você pode usar a Análise Espacial com vídeo gravado ou ao vivo. Para usar a Análise Espacial de vídeo gravado, tente gravar um arquivo de vídeo e salvá-lo como um arquivo mp4. Crie uma conta do Armazenamento de Blobs no Azure ou use uma conta existente. Depois, atualize as seguintes configurações do Armazenamento de Blobs no portal do Azure:
    1. Altere a opção **Transferência segura obrigatória** para **Desabilitada**
    2. Altere a opção **Permitir acesso público ao Blob** como **Habilitada**

Navegue até a seção **Contêiner** e crie um contêiner ou use um existente. Depois, carregue o arquivo de vídeo no contêiner. Expanda as configurações de arquivo do arquivo carregado e selecione **Gerar SAS**. Defina a **Data de Expiração** com tempo suficiente para cobrir o período de teste. Defina **Protocolos Permitidos** como *HTTP* (não há suporte para *HTTPS*).

Clique em **Gerar o Token e a URL SAS** e copie a URL SAS do blob. Substitua o `https` inicial por `http` e teste a URL em um navegador com suporte à reprodução de vídeo.

Substitua `VIDEO_URL` no manifesto de implantação do [dispositivo Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), do [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou da [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) pela URL que você criou, para todos os grafos. Defina `VIDEO_IS_LIVE` como `false` e reimplante o contêiner de Análise Espacial com o manifesto atualizado. Veja o exemplo abaixo.

O módulo de Análise Espacial começará a consumir o arquivo de vídeo e será reproduzido automaticamente de modo contínuo.


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

Se você encontrar problemas ao iniciar ou executar o contêiner, confira [Telemetria e solução de problemas](spatial-analysis-logging.md) a fim de obter as etapas para solucionar problemas comuns. Este artigo também contém informações sobre como gerar e coletar logs e como coletar a integridade do sistema.

## <a name="billing"></a>Cobrança

Os contêineres de Análise Espacial enviam informações de cobrança ao Azure usando um recurso de Pesquisa Visual Computacional na sua conta do Azure. O uso da Análise Espacial na versão prévia pública é gratuito no momento. 

Os contêineres dos Serviços Cognitivos do Azure não estão licenciados para execução sem estarem conectados com o ponto de extremidade de medição/cobrança. Você precisa permitir que os contêineres comuniquem as informações de cobrança com o ponto de extremidade de cobrança em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam para a Microsoft os dados do cliente, como o vídeo ou a imagem que está sendo analisada.


## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar os contêineres de Análise Espacial. Em resumo:

* A Análise Espacial é um contêiner do Linux para o Docker.
* As imagens de contêiner são baixadas do Microsoft Container Registry.
* As imagens de contêiner são executadas como Módulos de IoT no Azure IoT Edge.
* Como configurar o contêiner e implantá-lo em um computador host.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento de câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e de linha](spatial-analysis-zone-line-placement.md)
