---
title: Implantar ElasticSearch em uma máquina virtual de desenvolvimento no Azure
description: Tutorial – instalar Elastic Stack na VM do Linux de desenvolvimento no Azure
services: virtual-machines
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: b3213d7de9aee88486fa1db1388c51948fbde430
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557804"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Instalar o Elastic Stack em uma VM do Azure

Este artigo orienta como implantar [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) e [Kibana](https://www.elastic.co/products/kibana) em uma VM Ubuntu no Azure. Para ver o Elastic Stack em ação, opcionalmente você pode se conectar ao Kibana e trabalhar com alguns dados de log de exemplo. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie uma VM do Ubuntu em um grupo de recursos do Azure
> * Instale Elasticsearch, Logstash e Kibana na VM
> * Envie dados de exemplo para Elasticsearch com Logstash 
> * Abra as portas e trabalhe com os dados no console do Kibana


 Essa implantação é adequada para desenvolvimento básico com Elastic Stack. Para saber mais sobre Elastic Stack, incluindo recomendações para um ambiente de produção, consulte a [documentação do Elastic](https://www.elastic.co/guide/index.html) e o [Azure Architecture Center](/azure/architecture/elasticsearch/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm). 

O exemplo a seguir cria uma VM denominada *myVM* e cria chaves SSH, se elas ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>SSH em sua VM

Se você ainda não souber o endereço IP público de sua VM, execute o comando [az network public-ip list](/cli/azure/network/public-ip):

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Use o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP público correto de sua máquina virtual. Neste exemplo, o endereço IP é *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Instalar o Elastic Stack

Importe a chave de assinatura Elasticsearch e atualize sua lista de fontes APT para incluir o repositório do pacote do Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Instale o Java Virtual na VM e configure a variável JAVA_HOME. Isso é necessário para que os componentes do Elastic Stack sejam executados.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Execute os seguintes comandos para atualizar as fontes do pacote e instalar Elasticsearch, Kibana e Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Instruções de instalação detalhadas, incluindo layouts de diretório e configuração inicial, são mantidas na [documentação do Elastic](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Iniciar Elasticsearch 

Inicie o Elasticsearch na sua VM com o seguinte comando:

```bash
sudo systemctl start elasticsearch.service
```

Este comando não produz resultados, portanto verifique se o Elasticsearch está em execução na VM com este comando `curl`:

```bash
sudo curl -XGET 'localhost:9200/'
```

Se Elasticsearch estiver em execução, você verá a saída semelhante ao seguinte:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Iniciar Logstash e adicionar dados ao Elasticsearch

Inicie o Logstash com o seguinte comando:

```bash 
sudo systemctl start logstash.service
```

Teste o Logstash no modo interativo para garantir que eles esteja funcionando corretamente:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Este é um [pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) do logstash básico que ecoa entrada padrão para saída padrão. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Configure o Logstash para encaminhar mensagens de kernel dessa VM para Elasticsearch. Crie um novo arquivo em um diretório vazio chamado `vm-syslog-logstash.conf` e cole a seguinte configuração Logstash:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Teste esta configuração e envie os dados do syslog para Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Você pode ver as entradas do syslog em seu terminal ecoadas conforme elas são enviadas para Elasticsearch. Use `CTRL+C` para sair do Logstash depois de enviar dados.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Inicie o Kibana e visualize os dados no Elasticsearch

Edite `/etc/kibana/kibana.yml` e altere o endereço IP que o Kibana escuta para que você pode acessá-lo do seu navegador da Web.

```bash
server.host: "0.0.0.0"
```

Inicie o Kibana com o seguinte comando:

```bash
sudo systemctl start kibana.service
```

Abra porta 5601 da CLI do Azure para permitir o acesso remoto ao console Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Abra o console do Kibana e selecione **Criar** para gerar um índice padrão com base nos dados de syslog enviados para o Elasticsearch anteriormente. 

![Captura de tela que mostra o console do Kibana e realça o botão Criar.](media/elasticsearch-install/kibana-index.png)

Selecione **Descobrir** no console do Kibana para pesquisar, procurar e filtrar os eventos de syslog.

![Procurar eventos de Syslog no Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou o Elastic Stack em uma VM de desenvolvimento no Azure. Você aprendeu a:

> [!div class="checklist"]
> * Crie uma VM do Ubuntu em um grupo de recursos do Azure
> * Instale Elasticsearch, Logstash e Kibana na VM
> * Enviar dados de exemplo para Elasticsearch do Logstash 
> * Abra as portas e trabalhe com os dados no console do Kibana
