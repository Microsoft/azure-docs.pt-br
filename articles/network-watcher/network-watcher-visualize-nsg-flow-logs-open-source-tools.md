---
title: Gerenciar e analisar os logs de fluxo do Grupo de Segurança de Rede usando o Observador de Rede e a Pilha Elástica | Microsoft Docs
description: Gerencie e analise os Logs de Fluxo do Grupo de Segurança de Rede no Azure usando o Observador de Rede e a Pilha Elástica.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 7361eff0f76271564fd5a0e9b8a18221ec4138e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859988"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizar logs de fluxo NSG do Observador de Rede do Azure usando ferramentas de código aberto

Os logs de fluxo do Grupo de Segurança de Rede fornecem informações que podem ser usadas para entender a entrada e a saída de tráfego IP em Grupos de Segurança de Rede. Esses logs de fluxo exibem os fluxos de entrada e saída baseados em regras. A NIC de fluxo se aplica às informações de 5 tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino e Protocolo) e se o tráfego foi permitido ou negado.

Esses logs de fluxo podem ser difíceis de serem analisados e de obter ideias de forma manual. No entanto, há várias ferramentas de código livre que podem ajudar a visualizar esses dados. Esse artigo fornece uma solução para visualizar esses logs usando o Elastic Stack que permite indexar e visualizar seus logs de fluxo em um painel Kibana.

> [!Warning]  
> As etapas a seguir funcionam com os logs de fluxo versão 1. Para obter detalhes, consulte [Introdução ao fluxo de log para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções a seguir não funcionarão com a versão 2 dos arquivos de log, sem modificação.

## <a name="scenario"></a>Cenário

Neste artigo, vamos configurar uma solução que permitirá a visualização dos logs de fluxo do Grupo de Segurança de Rede usando o Elastic Stack.  Um plug-in de entrada Logstash obterá os logs de fluxo diretamente do blob de armazenamento configurado para conter os logs do fluxo. Em seguida, usando o Elastic Stack, os logs do fluxo serão indexados e usados para criar um painel Kibana para visualizar as informações.

![cenário][scenario]

## <a name="steps"></a>Etapas

### <a name="enable-network-security-group-flow-logging"></a>Habilitar os registros em logs do fluxo do Grupo de Segurança de Rede
Nessa situação, você deve habilitar o Registro em Log do Fluxo do Grupo de Segurança de Rede em um ou mais Grupos de Segurança de Rede em sua conta. Confira o artigo [Introdução ao registro em log do fluxo para Grupos de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md) para obter instruções sobre como habilitar os Logs do Fluxo de Segurança de Rede.

### <a name="set-up-the-elastic-stack"></a>Configurar o Elastic Stack
Ao conectar os logs de fluxo NSG ao Elastic Stack, podemos criar um painel Kibana que nos permitirá pesquisar, criar grafos, analisar e derivar informações de nossos logs.

#### <a name="install-elasticsearch"></a>Instalar Elasticsearch

1. O Elastic Stack da versão 5.0 e superior exige o Java 8. Execute o comando `java -version` para verificar sua versão. Se você não tiver o Java instalado, veja a documentação sobre os [JDKs com suporte do Azure](https://aka.ms/azure-jdks).
2. Baixe o pacote de binários correto para seu sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Outros métodos de instalação podem ser encontrados em [Instalação do Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Verifique se o Elasticsearch está sendo executado com o comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Você deve ver uma resposta semelhante a essa:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Para obter instruções adicionais sobre a instalação da pesquisa elástica, consulte as [Instruções de instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalar Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Em seguida, precisamos configurar o Logstash para acessar e analisar os logs de fluxo. Crie um arquivo logstash.conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adicione o seguinte conteúdo ao arquivo:

   ```
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

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
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
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
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

Para obter mais informações sobre como instalar o Logstash, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalar o plugin de entrada do Logstash para o armazenamento de blobs do Azure

Esse plug-in do Logstash permitirá o acesso direto aos logs do fluxo por meio da conta de armazenamento designada. Para instalar esse plug-in, no diretório de instalação padrão do Logstash (nesse caso, /usr/share/logstash/bin), execute o comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar o Logstash, execute o comando:

```bash
sudo /etc/init.d/logstash start
```

Para obter mais informações sobre esse plug-in, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalar Kibana

1. Execute os seguintes comandos para instalar o Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para executar o Kibana, use os comandos:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para exibir a interface da Web do Kibana, navegue até `http://localhost:5601`
4. Para esse cenário, o padrão de índice usado para os logs do fluxo é "nsg-flow-logs". Você pode alterar o padrão de índice na seção "saída" do arquivo logstash.conf.
5. Se você quiser exibir o painel Kibana remotamente, crie uma regra NSG de entrada permitindo o acesso à **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um painel Kibana

Um painel de exemplo para exibir detalhes e tendências em seus alertas é mostrado na imagem a seguir:

![figura 1][1]

Baixe o [arquivo do painel](https://aka.ms/networkwatchernsgflowlogdashboard), o [arquivo de visualização](https://aka.ms/networkwatchernsgflowlogvisualizations)e o [arquivo de pesquisa salva](https://aka.ms/networkwatchernsgflowlogsearch).

Na guia **Management** (Gerenciamento) do Kibana, navegue até **Saved Objects** (Objetos Salvos) e importe todos os três arquivos. Em seguida, na guia **Painel**, você pode abrir e carregar o painel de exemplo.

Você também pode criar suas próprias visualizações e painéis personalizados para métricas de seu próprio interesse. Leia mais sobre como criar visualizações do Kibana a partir da [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html) do Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualizar logs de fluxo NSG

O painel de exemplo fornece várias visualizações dos logs de fluxo:

1. Fluxos por Decisão/Direção ao Longo do Tempo - grafos da série de tempo mostrando o número de fluxos durante o período de tempo. Você pode editar a unidade de tempo e o alcance das duas visualizações. Os Fluxos por Decisão mostram a proporção de permitir ou negar decisões tomadas, enquanto os Fluxos por Direção mostram a proporção do tráfego de entrada e de saída. Com esses elementos visuais, você pode examinar as tendências de tráfego ao longo do tempo e procure por picos ou padrões incomuns.

   ![figura2][2]

2. Fluxos por Porta de Origem/Destino - gráficos de pizza mostrando a divisão dos fluxos pelas respectivas portas. Nesta exibição, você pode ver as portas usadas com mais frequência. Se você clicar em uma porta específica dentro do gráfico de pizza, o restante do painel filtrará em busca de fluxos dessa porta.

   ![figura3][3]

3. Número de Fluxos e Hora do Primeiro Log - métricas mostrando o número de fluxos registrados e a data do primeiro log capturado.

   ![figura4][4]

4. Fluxos de NSG e Regra - um grafo de barras mostrando a distribuição dos fluxos em cada NSG, além da distribuição de regras em cada NSG. A partir daqui, você pode ver quais regras e NSG geraram mais tráfego.

   ![figura5][5]

5. Os 10 Principais IPs de Origem/Destino - gráficos de barras mostrando os 10 principais IPs de origem e de destino. Você pode ajustar esses gráficos para mostrar mais ou menos IPs principais. A partir daqui, você pode ver os IPs que ocorrem mais frequentemente, além da decisão de tráfego (permitir ou negar) tomada para cada IP.

   ![figura6][6]

6. Tuplas de Fluxo - essa tabela mostra as informações contidas em cada tupla de fluxo, além da regra e NGS correspondentes.

   ![figura7][7]

Usando a barra de consulta na parte superior do painel, você pode filtrar o conteúdo do painel com base nos parâmetros dos fluxos, como a ID da assinatura, grupos de recursos, regra ou qualquer outra variável de interesse. Para obter mais informações sobre consultas e filtros do Kibana, consulte a [documentação oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusão

Combinando os logs de fluxo do Grupo de Segurança de Rede com o Elastic Stack, podemos encontrar uma maneira poderosa e personalizável para visualizar o tráfego de rede. Esses painéis permitem obter e compartilhar informações sobre o tráfego de rede com rapidez, além de filtrar e investigar quaisquer possíveis anomalias. Com o Kibana, você pode personalizar esses painéis e criar visualizações específicas para atender às necessidades de segurança, auditoria e conformidade.

## <a name="next-steps"></a>Próximas etapas

Para saber como visualizar os logs de fluxo NSG com o Power BI, veja [Como visualizar logs de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
