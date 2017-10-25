---
title: Usar o Docker Machine para criar hosts Linux no Azure | Microsoft Docs
description: Descreve como usar o Docker Machine para criar hosts do Docker no Azure.
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: 7772381e9796ddc2e0db215bab4f230473eaa462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Como usar o Docker Machine para criar hosts no Azure
Este artigo fornece detalhes de como usar o [Docker Machine](https://docs.docker.com/machine/) para criar hosts no Azure. O comando `docker-machine` cria uma VM (máquina virtual) Linux no Azure e, em seguida, instala o Docker. Depois, você pode gerenciar seus hosts do Docker no Azure usando os mesmos fluxos de trabalho e ferramentas locais. Para usar docker-machine no Windows 10, use o Bash do Linux.

## <a name="create-vms-with-docker-machine"></a>Criar VMs com o computador Docker
Primeiro, obtenha a ID da assinatura do Azure com [az account show](/cli/azure/account#show) da seguinte maneira:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Crie VMs host do Docker no Azure com `docker-machine create` especificando *azure* como o driver. Para obter mais informações, consulte a [documentação do Driver do Docker Azure](https://docs.docker.com/machine/drivers/azure/)

O exemplo a seguir cria uma VM chamada *myVM*, com base no plano “Standard D2 v2”, cria uma conta de usuário chamada *azureuser* e abre a porta *80* na VM host. Siga os prompts para fazer logon em sua conta do Azure e conceder permissões ao Docker Machine para criar e gerenciar recursos.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2 \
    myvm
```

A saída deve ser semelhante ao seguinte exemplo:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>Configurar o shell do Docker
Para se conectar ao host do Docker no Azure, defina as configurações de conexão apropriadas. Conforme observado ao final do resultado, exiba as informações de conexão para o host do Docker da seguinte maneira: 

```bash
docker-machine env myvmdocker
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

Para definir as configurações de conexão, execute o comando de configuração sugerido (`eval $(docker-machine env myvmdocker)`) ou defina as variáveis de ambiente manualmente. 

## <a name="run-a-container"></a>Executar um contêiner
Para ver um contêiner em ação, vamos executar um servidor Web NGINX básico. Crie um contêiner com `docker run` e exponha a porta 80 para o tráfego da Web da seguinte maneira:

```bash
docker run -d -p 80:80 --restart=always nginx
```

A saída deverá ser semelhante ao seguinte exemplo:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Exiba os contêineres em execução com `docker ps`. O seguinte resultado de exemplo mostra o contêiner NGINX em execução com a porta 80 exposta:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Testar o contêiner
Obtenha o endereço IP público do host do Docker da seguinte maneira:


```bash
docker-machine ip myvmdocker
```

Para ver o contêiner em ação, abra um navegador da Web e insira o endereço IP público indicado na saída do comando anterior:

![Contêiner ngnix em execução](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Próximas etapas
Você também pode criar hosts com a [Extensão de VM do Docker](dockerextension.md). Para obter exemplos de como usar o Docker Compose, consulte [Introdução ao Docker e ao Compose no Azure](docker-compose-quickstart.md).
