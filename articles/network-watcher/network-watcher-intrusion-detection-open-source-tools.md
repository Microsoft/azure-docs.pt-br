---
title: Executar a detecção de invasão de rede com o Observador de Rede do Azure e ferramentas de software livre | Microsoft Docs
description: Este artigo descreve como usar o Observador de Rede do Azure e ferramentas de software livre para executar a detecção de invasão de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 41b039c6598334d49b9ddb0608d7ad2ae045b223
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115476"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Executar a detecção de invasão de rede com o Observador de Rede e ferramentas de software livre

Captura de pacote é um componente-chave para implementar sistemas de detecção de invasão de rede (IDS) e executar o Monitoramento de Segurança de Rede (NSM). Há várias ferramentas de IDS de software livre que processam capturas de pacote e procuram assinaturas de possíveis invasões de rede e atividades mal-intencionadas. Usar as capturas de pacote fornecidas pelo Observador de Rede, você pode analisar sua rede para invasões prejudiciais ou vulnerabilidades.

Uma dessas ferramentas de software livre é o Suricata, um mecanismo de IDS que usa conjuntos de regras para monitorar o tráfego de rede e dispara alertas sempre que ocorrerem eventos suspeitos. O Suricata oferece um mecanismo de vários segmentos, o que significa que ele pode realizar análise de tráfego de rede com mais velocidade e eficiência. Para obter mais detalhes sobre o Suricata e seus recursos, visite o site em https://suricata-ids.org/.

## <a name="scenario"></a>Cenário

Este artigo explica como configurar seu ambiente para executar a detecção de invasão de rede usando o Observador de Rede, o Suricata e o Elastic Stack. O Observador de Rede fornece as capturas de pacote usadas para executar a detecção de invasão de rede. O Suricata processa a captura de pacote e dispara alertas com base nos pacotes que correspondem ao conjunto de regras de ameaças. Esses alertas são armazenados em um arquivo de log no computador local. Usando o Elastic Stack, os logs gerados pelo Suricata podem ser indexados e usados para criar um painel Kibana, fornecendo uma representação visual dos logs e um meio de obter rapidamente as ideias para possíveis vulnerabilidades de rede.  

![cenário de aplicativo Web simples][1]

Ambas as ferramentas de software livre podem ser configuradas em uma VM do Azure, permitindo que você execute essa análise em seu próprio ambiente de rede do Azure.

## <a name="steps"></a>Etapas

### <a name="install-suricata"></a>Instalar o Suricata

Para todos os outros métodos de instalação, visite https://suricata.readthedocs.io/en/latest/install.html

1. No terminal de linha de comando da sua VM, execute os seguintes comandos:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Para verificar a instalação, execute o comando `suricata -h` para ver a lista completa de comandos.

### <a name="download-the-emerging-threats-ruleset"></a>Baixar o conjunto de regras de Ameaças Emergentes

Nesse estágio, não temos nenhuma regra para o Suricata executar. Você pode criar suas próprias regras se há ameaças específicas à sua rede que você deseja detectar ou você também pode usar desenvolvido conjuntos de regras de um número de provedores, como ameaças emergentes ou regras VRT Snort. Usamos o conjunto de regras Ameaças Emergentes livremente acessível aqui:

Baixe o conjunto de regras e as copie para o diretório:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Capturas de pacotes de processo com o Suricata

Para processar o pacote de captura usando o Suricata, execute o seguinte comando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Para verificar os alertas resultantes, leia o arquivo fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Configurar o Elastic Stack

Enquanto os logs que produz Suricata contêm informações importantes sobre o que está acontecendo em nossa rede, esses arquivos de log não são mais fáceis de ler e entender. Conectando Suricata com a pilha elástica, podemos criar um painel Kibana o que nos permite pesquisar, grafo, analisar e derivar informações de nossos logs.

#### <a name="install-elasticsearch"></a>Instalar Elasticsearch

1. O Elastic Stack da versão 5.0 e superior exige o Java 8. Execute o comando `java -version` para verificar sua versão. Se você não tiver o Java instalado, veja a documentação sobre os [JDKs com suporte do Azure](https://aka.ms/azure-jdks).

1. Baixe o pacote de binários correto para seu sistema:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Outros métodos de instalação podem ser encontrados em [Instalação do Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Verifique se o Elasticsearch está sendo executado com o comando:

    ```
    curl http://127.0.0.1:9200
    ```

    Você deve ver uma resposta semelhante a essa:

    ```
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

Para obter instruções adicionais sobre a instalação da pesquisa elástica, consulte a página [Instalação](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Instalar Logstash

1. Para instalar o Logstash, execute os seguintes comandos:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Em seguida, precisamos configurar Logstash a leitura da saída do arquivo eve.json. Crie um arquivo logstash.conf usando:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adicione o seguinte conteúdo para o arquivo (certifique-se de que o caminho para o arquivo eve.json está correto):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Certifique-se de conceder as permissões corretas para o arquivo eve.json para que Logstash pode incluir o arquivo.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Para iniciar o Logstash, execute o comando:

    ```
    sudo /etc/init.d/logstash start
    ```

Para saber mais sobre como instalar o Logstash, veja a [documentação oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Instalar Kibana

1. Execute os seguintes comandos para instalar o Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Para executar o Kibana, use os comandos:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Para exibir a interface da Web do Kibana, navegue até `http://localhost:5601`
1. Para este cenário, é o padrão de índice usado para os logs de Suricata "logstash-*"

1. Se você quiser exibir o painel de Kibana remotamente, criar uma regra NSG entrada permitindo o acesso ao **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Criar um painel Kibana

Neste artigo, nós fornecemos um painel de exemplo para exibir detalhes e tendências em seus alertas.

1. Baixe o arquivo do painel [aqui](https://aka.ms/networkwatchersuricatadashboard), o arquivo de visualização [aqui](https://aka.ms/networkwatchersuricatavisualization)e o arquivo de pesquisa salva [aqui](https://aka.ms/networkwatchersuricatasavedsearch).

1. Na guia **Management** (Gerenciamento) do Kibana, navegue até **Saved Objects** (Objetos Salvos) e importe todos os três arquivos. Em seguida, na guia **Painel**, você pode abrir e carregar o painel de exemplo.

Você também pode criar suas próprias visualizações e painéis personalizados para métricas de seu próprio interesse. Leia mais sobre como criar visualizações do Kibana na [documentação oficial](https://www.elastic.co/guide/en/kibana/current/visualize.html) do Kibana.

![painel kibana][2]

### <a name="visualize-ids-alert-logs"></a>Visualizar logs de alerta de IDS

O painel de exemplo fornece várias visualizações dos logs de alerta Suricata:

1. Alertas por GeoIP – um mapa mostrando a distribuição de alertas de seu país de origem com base na localização geográfica (determinada por IP)

    ![ip geográfico][3]

1. Os 10 alertas principais – um resumo do 10 mais frequentes disparados alertas e suas descrições. Filtra clicando em um alerta individual para baixo o painel de controle para as informações referentes a esse alerta específico.

    ![image 4][4]

1. Número de Alertas – a contagem total de alertas disparados pelo conjunto de regras

    ![imagem 5][5]

1. Principais 20 origem/destino IPs/portas - gráficos de pizza mostrando os principais 20 IPs e portas que alertas foram acionadas. Você pode filtrar para baixo em IPs/portas específicas para ver quantos e quais tipos de alertas de estão sendo disparadas.

    ![imagem 6][6]

1. Resumo de alertas – uma tabela de resumo de detalhes específicos de cada alerta individual. Você pode personalizar essa tabela para mostrar outros parâmetros de interesse para cada alerta.

    ![imagem 7][7]

Para obter mais documentação sobre a criação de painéis e visualizações personalizadas, consulte [documentação oficial do Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Conclusão

Combinando pacote captura fornecida pelo Observador de Rede e ferramentas de IDS de código-fonte aberto como Suricata, você pode executar detecção de invasão de rede para uma ampla variedade de ameaças. Esses painéis permitem identificar rapidamente as tendências e anomalias em sua rede, como dig bem nos dados para descobrir a causa raiz de alertas como agentes de usuário mal-intencionado ou portas vulneráveis. Com esses dados extraídos, você pode tomar decisões informadas sobre como reagir a proteger sua rede contra todas as tentativas de intrusão prejudicial e criar regras para evitar futuras invasões à sua rede.

## <a name="next-steps"></a>Próximas etapas

Aprenda a acionar capturas de pacote com base em alertas visitando [usar a captura de pacote para fazer o monitoramento de rede proativo com Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Saiba como visualizar os logs de fluxo NSG com o Power BI visitando [fluxos de NSG visualizar logs com o Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
