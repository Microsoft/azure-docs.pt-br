---
title: Usar o Docker Compose em uma VM Linux no Azure | Microsoft Docs
description: Como instalar e usar o Docker e o Compose em máquinas virtuais Linux com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474056"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e Compose para definir e executar um aplicativo de vários contêineres no Azure
Com o [Compose](https://github.com/docker/compose), você usa um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você acelera seu aplicativo com um único comando que faz tudo que é necessário para implantar o ambiente definido. Por exemplo, este artigo mostra como configurar rapidamente um blog WordPress com um banco de dados SQL MariaDB de back-end em uma VM Ubuntu. Você também pode usar o Redigir para configurar aplicativos mais complexos.

Este artigo foi testado pela última vez em 14/02/2019 usando o [Azure Cloud Shell](https://shell.azure.com/bash) e a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) versão 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Criar host do Docker com o CLI do Azure
Instale a [CLI do Azure](/cli/azure/install-az-cli2) mais recente do Azure e faça logon em uma conta do Azure usando [az login](/cli/azure/reference-index).

Primeiro, crie um grupo de recursos para seu ambiente do Docker com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Crie um arquivo chamado *cloud-init.txt* e cole a seguinte configuração. Insira `sensible-editor cloud-init.txt` para criar o arquivo e ver uma lista de editores disponíveis. 

```yaml
#include https://get.docker.com
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utiçize o `--custom-data` parâmetro para passar no arquivo de configuração de inicialização de nuvem. Forneça o caminho completo para a configuração *cloud-init.txt* se você salvou o arquivo fora do seu diretório de trabalho atual. O exemplo a seguir cria uma VM denominada *myDockerVM* e abre a porta 80 para tráfego da web.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Demora alguns minutos para que a VM seja criada, os pacotes para instalar e iniciar o aplicativo. Há tarefas em segundo plano que continuarão em execução depois que a CLI do Azure faz você voltar para o prompt. Quando a VM tiver sido criada, observe o `publicIpAddress` exibido pela CLI do Azure. 

                 

## <a name="install-compose"></a>Instalar o Compose


SSH para a nova VM host do Docker. Forneça seu próprio endereço IP.

```bash
ssh azureuser@10.10.111.11
```

Instalar o Compose na VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um arquivo de configuração docker-compose.yml
Criar um arquivo de configuração `docker-compose.yml`para definir os contêineres Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner, as variáveis de ambiente e as dependências necessárias, as portas e os links entre contêineres. Para obter detalhes sobre a sintaxe do arquivo yml, veja a [referência de arquivo do Redigir](https://docs.docker.com/compose/compose-file/).

Crie um arquivo *docker-compose.yml*. Use seu editor de texto favorito para adicionar alguns dados ao arquivo. O exemplo a seguir cria o arquivo com um prompt para `sensible-editor` escolher um editor que você queira usar.

```bash
sensible-editor docker-compose.yml
```

Cole o exemplo a seguir em seu arquivo do Docker Compose. Essa configuração usa imagens do [Registro do DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blog e gerenciamento de conteúdo de software livre) e um banco de dados SQL MariaDB de back-end vinculado. Insira seu próprio *MYSQL_ROOT_PASSWORD*.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Iniciar os contêineres com o Compose
No mesmo diretório que seu arquivo *docker-compose.yml*, execute o comando a seguir (dependendo do seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`):

```bash
sudo docker-compose up -d
```

Esse comando inicia os contêineres do Docker especificados em *docker-compose.yml*. Leva um ou dois minutos para concluir esta etapa. Você verá uma saída semelhante ao seguinte:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Para verificar se os contêineres estão ativos, digite `sudo docker-compose ps`. Você deve ver algo como:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora você pode conectar o WordPress diretamente na VM na porta 80. Abra um navegador da Web e digite o nome do endereço IP de sua VM. Agora você deve ver a tela inicial do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Próximas etapas
* Confira a [referência da linha de comando do Compose](https://docs.docker.com/compose/reference/) e o [guia do usuário](https://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos com vários contêineres.
* Use um modelo do Gerenciador de Recursos do Azure, seu ou da do [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com Docker e um aplicativo configurado com o Redigir. Por exemplo, o modelo [Implantar um blog WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa Docker e Redigir para implantar rapidamente o WordPress com um back-end do MySQL em uma VM do Ubuntu.
* Tente integrar o Docker Compose com um cluster do Docker Swarm. Veja [Using Compose with Swarm (Uso do Redigir com o Swarm)](https://docs.docker.com/compose/swarm/) para obter os cenários.

