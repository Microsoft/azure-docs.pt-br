---
title: "Gerenciar logs de fluxo do Grupo de Segurança de Rede com o Observador de Rede e o Grafana | Microsoft Docs"
description: "Gerenciar e analisar os logs de fluxo do Grupo de Segurança de Rede no Azure usando o Observador de Rede e o Grafana."
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Gerenciar e analisar os logs de fluxo do Grupo de Segurança de Rede usando o Observador de Rede e o Grafana

[Os logs de fluxo do NSG (Grupo de Segurança de Rede)](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser usadas para entender a entrada e a saída de tráfego IP em interfaces de rede. Esses logs de fluxo exibem os fluxos de entrada e saída baseados em regras do NSG. A NIC de fluxo se aplica às informações de 5 tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino e Protocolo) e se o tráfego foi permitido ou negado.

Você pode ter muitos NSGs em sua rede com o registro em log de fluxo habilitado. Essa quantidade de dados de registro em log torna difícil a análise e a obtenção de insights de seus logs. Este artigo fornece uma solução para gerenciar centralmente esses logs de fluxo do NSG usando o Grafana, uma ferramenta gráfica de software livre, o ElasticSearch, uma pesquisa distribuída e mecanismo de análise e o Logstash, que é um pipeline de processamento de dados do servidor do lado do servidor de software livre.  

## <a name="scenario"></a>Cenário

Logs de fluxo do NSG são habilitados usando o Observador de Rede e são armazenados no armazenamento de blob do Azure. Um plug-in Logstash é usado para conectar e processar os logs de fluxo do armazenamento de blobs e enviá-los ao ElasticSearch.  Depois que os logs de fluxo são armazenados no ElasticSearch, eles podem ser analisados e visualizados em painéis personalizados no Grafana.

![Grafana do Observador de Rede do NSG](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Etapas de instalação

### <a name="enable-network-security-group-flow-logging"></a>Habilitar os registros em logs do fluxo do Grupo de Segurança de Rede

Nessa situação, você deve habilitar o Registro em Log do Fluxo do Grupo de Segurança de Rede em um ou mais Grupos de Segurança de Rede em sua conta. Confira o artigo [Introdução ao registro em log do fluxo para Grupos de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md) para obter instruções sobre como habilitar os Logs do Fluxo de Segurança de Rede.

### <a name="setup-considerations"></a>Considerações sobre a instalação

Neste exemplo, Grafana, ElasticSearch e Logstash são configurados em um servidor do Ubuntu 16.04 LTS implantado no Azure. Essa configuração mínima é usada para executar todos os três componentes. Todos são executados na mesma VM. Essa configuração só deve ser usada para cargas de trabalho de teste e não críticas. Logstash, Elasticsearch e Grafana podem ser projetados para dimensionar de modo independente entre várias instâncias. Para obter mais informações, consulte a documentação de cada um desses componentes.

### <a name="install-logstash"></a>Instalar Logstash

Você pode usar o Logstash para mesclar os logs de fluxo formatados em JSON para um nível de tupla de fluxo.

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure o Logstash para analisar os logs de fluxo e enviá-los ao ElasticSearch. Crie um arquivo Logstash.conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao arquivo. Altere a chave de acesso e o nome da conta de armazenamento para refletir os detalhes da conta de armazenamento:

    ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
    ```

O arquivo de configuração do Logstash fornecido é composto de três partes: a entrada, o filtro e a saída. A seção de entrada designa a fonte de entrada de logs que o Logstash processará. Nesse caso, vamos usar um plug-in de entrada do "azureblob" (instalado nas etapas a seguir) que permitirá acessar os arquivos de JSON do log de fluxo do NSG armazenado no armazenamento de blobs. 

A seção de filtro, em seguida, mescla cada arquivo de log do fluxo para que cada tupla de fluxo e suas propriedades associadas se tornem um evento de Logstash separado.

Por fim, a seção de saída encaminha cada evento de Logstash para o servidor do ElasticSearch. Fique à vontade para modificar o arquivo de configuração do Logstash para atender às suas necessidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o plugin de entrada do Logstash para o armazenamento de blobs do Azure

Esse plug-in do Logstash permite o acesso direto aos logs do fluxo por meio da conta de armazenamento de blobs designada. Para instalar esse plug-in, do diretório de instalação Logstash padrão (nesse caso, /usr/share/logstash/bin), execute o comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre esse plug-in, consulte [Plug-in de entrada do Logstash para o Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalar ElasticSearch

Você pode usar o script a seguir para instalar o ElasticSearch. Para obter informações sobre como instalar o ElasticSearch, consulte [Pilha elástica](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Instalar o Grafana

Para instalar e executar o Grafana, execute os seguintes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para obter informações adicionais de instalação, consulte [Instalar em Debian/Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adicione o servidor do ElasticSearch como uma fonte de dados

Em seguida, você precisa adicionar o índice do ElasticSearch que contém os logs de fluxo como uma fonte de dados. Você pode adicionar uma fonte de dados selecionando **Adicionar fonte de dados** e preenchendo o formulário com as informações relevantes. Um exemplo dessa configuração pode ser encontrado na seguinte captura de tela:

![Adicionar fonte de dados](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Criar um painel

Agora que você configurou com êxito o Grafana para ler o índice do ElasticSearch que contém os logs de fluxo do NSG, você poderá criar e personalizar os painéis. Para criar um novo painel, selecione **Criar seu primeiro painel**. A configuração de exemplo de gráfico a seguir mostra os fluxos segmentados por regra do NSG:

![Gráfico do painel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

A captura de tela a seguir mostra um gráfico e um grafo mostrando os fluxos principais e sua frequência. Os fluxos também são mostrados por regra do NSG e fluxos por decisão. O Grafana é altamente personalizável, portanto, é recomendável que você crie painéis para atender às suas necessidades de monitoramentos específicas. O exemplo a seguir mostra um painel típico:

![Gráfico do painel](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusão

Ao integrar o Observador de Rede com o ElasticSearch e o Grafana, você tem uma maneira centralizada e conveniente de gerenciar e visualizar os logs de fluxo do NSG, bem como outros dados. O Grafana tem vários outros recursos gráficos avançados que também podem ser usados para gerenciar os logs de fluxo e entender melhor o tráfego de rede. Agora que você tem uma instância do Grafana configurada e conectada ao Azure, fique à vontade para continuar a explorar outras funcionalidades que ele oferece.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como usar o [Observador de Rede](network-watcher-monitoring-overview.md).

