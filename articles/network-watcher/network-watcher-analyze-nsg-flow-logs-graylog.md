---
title: Análise de logs de fluxo do grupo de segurança de rede do Azure – Graylog | Microsoft Docs
description: Como gerenciar e analisar os logs de fluxo do grupo de segurança de rede no Azure usando o Observador de Rede e o Graylog.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: a5fadcfce154740a79a8764f44f08b21ad18f4d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625135"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Gerenciar e analisar os logs de fluxo do grupo de segurança de rede no Azure usando o Observador de Rede e o Graylog

[Os logs de fluxo do Grupo de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md) fornecem informações que podem ser usadas para entender a entrada e a saída de tráfego IP em interfaces de rede do Azure. Os logs de fluxo exibem os fluxos de entrada e saída baseados em regras do Grupo de Segurança de Rede. O adaptador de rede de fluxo se aplica às informações de 5 tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino e Protocolo) e se o tráfego foi permitido ou negado.

Você pode ter muitos grupos de segurança de rede em sua rede com o registro em log de fluxo habilitado. Vários grupos de segurança de rede com o registro em log de fluxo habilitado podem dificultar a análise e a obtenção de insights de seus logs. Este artigo fornece uma solução para gerenciar centralmente esses logs de fluxo de do grupo de segurança de rede usando Graylog, um gerenciamento de log de software livre e ferramenta de análise, e Logstash, um pipeline de processamento de dados do lado do servidor de software livre.

> [!Warning]
> As etapas a seguir funcionam com os logs de fluxo versão 1. Para obter detalhes, consulte [Introdução ao fluxo de log para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções a seguir não funcionarão com a versão 2 dos arquivos de log, sem modificação.

## <a name="scenario"></a>Cenário

Os logs de fluxo de grupo de segurança de rede são habilitados usando o Observador de Rede. O logs de fluxo fluem no armazenamento de blobs do Azure. Um plug-in do Logstash é usado para conectar e processar os logs de fluxo do armazenamento de blobs e enviá-los ao Graylog. Depois que os logs de fluxo são armazenados no Graylog, eles podem ser analisados e visualizados em painéis personalizados.

![Fluxo de trabalho do Graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Etapas de instalação

### <a name="enable-network-security-group-flow-logging"></a>Habilite os registros em logs do fluxo do grupo de segurança de rede

Nessa situação, você deve habilitar o registro em log do fluxo do grupo de segurança de rede em um ou mais grupos de segurança de rede em sua conta. Veja o artigo [Introdução ao registro em log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md) para obter instruções sobre como habilitar os logs de fluxo do grupo de segurança de rede.

### <a name="setting-up-graylog"></a>Configuração do Graylog

Neste exemplo, o Graylog e o Logstash são configurados em um servidor do Ubuntu 14.04 implantado no Azure.

- Consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) do Graylog para obter instruções passo a passo sobre como instalar no Ubuntu.
- Certifique-se também de configurar a interface da web do Graylog seguindo a [documentação](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Este exemplo usa a configuração mínima do Graylog (isto é, uma única instância de um Graylog), mas o Graylog pode ser projetado para escala em recursos, dependendo de seu sistema e de necessidades de produção. Para obter mais informações sobre considerações de arquitetura ou um guia abrangente de arquitetura, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/architecture.html) e o [guia de arquitetura](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture) do Graylog.

O Graylog pode ser instalado de várias maneiras, dependendo de suas preferências e plataforma. Para obter uma lista completa dos métodos de instalação possíveis, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/installation.html) oficial do Graylog. O aplicativo para servidores do Graylog é executado em distribuições do Linux e tem os seguintes pré-requisitos:

-  Java SE 8 ou posterior – [documentação Azul Azure JDK](https://aka.ms/azure-jdks)
-  Elastic Search 2.x (2.1.0 ou posterior) – [documentação de instalação do Elasticsearch 2.x](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 ou posterior – [documentação de instalação do MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Instalar Logstash

O Logstash é usado para mesclar os logs de fluxo formatados em JSON para um nível de tupla de fluxo. Mesclar os logs de fluxo torna mais fácil organizar e pesquisar logs no Graylog.

1. Para instalar o Logstash, execute os seguintes comandos:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Configure o Logstash para analisar os logs de fluxo e enviá-los ao Graylog. Crie um arquivo Logstash.conf:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adicione o seguinte conteúdo ao arquivo. Altere os valores realçados para refletir os detalhes da conta de armazenamento:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   O arquivo de configuração do Logstash fornecido é composto de três partes: a entrada, o filtro e a saída. A seção de entrada designa a fonte de entrada de logs que o Logstash processará. Nesse caso, você usará um plug-in de entrada do blob do Azure (instalado nas etapas a seguir) que permitirá acessar os arquivos de JSON do log de fluxo do grupo de segurança de rede armazenado no armazenamento de blobs.

A seção de filtro, em seguida, mescla cada arquivo de log do fluxo para que cada tupla de fluxo e suas propriedades associadas se tornem um evento de Logstash separado.

Por fim, a seção de saída encaminha cada evento de Logstash para o servidor do Graylog. Para atender a suas necessidades específicas, modifique o arquivo de configuração do Logstash.

   > [!NOTE]
   > O arquivo de configuração anterior pressupõe que o servidor Graylog foi configurado no endereço IP 127.0.0.1 de loopback do host local. Caso contrário, certifique-se de alterar o parâmetro do host na seção de saída para o endereço IP correto.

Para obter mais informações sobre como instalar o Logstash, confira a [documentação](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) do Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Instalar o plugin de entrada do Logstash para o armazenamento de blobs do Azure

O plug-in do Logstash permite o acesso direto aos logs do fluxo por meio da conta de armazenamento de blobs designada. Para instalar o plug-in, do diretório de instalação do Logstash padrão (nesse caso, /usr/share/logstash/bin), execute o comando a seguir:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para obter mais informações sobre esse plug-in, consulte a [documentação](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Configurar a conexão do Logstash para Graylog

Agora que você criou uma conexão para os logs de fluxo usando o Logstash e configuramos o servidor Graylog, é preciso configurar o Graylog para aceitar os arquivos de log de entrada.

1. Navegue até sua interface web do servidor Graylog usando a URL configurada para ele. Você pode acessar a interface direcionando seu navegador para `http://<graylog-server-ip>:9000/`

2. Para navegar até a página de configuração, selecione o menu suspenso **Sistema** na barra de navegação superior à direita e, em seguida, clique em **Entradas**.
   Como alternativa, navegue até `http://<graylog-server-ip>:9000/system/inputs`

   ![Introdução](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Para iniciar a nova entrada, selecione *GELF UDP* na lista suspensa **Selecionar entrada** e, em seguida, preencha o formulário. GELF significa Formato de Log Estendido do Graylog. O formato GELF é desenvolvido pela Graylog. Para saber mais sobre suas vantagens, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/gelf.html) do Graylog.

   Certifique-se de associar a entrada ao IP que você configurou seu servidor Graylog. O endereço IP deve corresponder ao campo **host** da saída UDP do arquivo de configuração do Logstash. A porta padrão deve ser *12201*. Certifique-se de que a porta corresponda ao campo da **porta** na saída UDP designada no arquivo de configuração do Logstash.

   ![Entradas](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Assim que você abrir a entrada, você verá ela aparecer sob a seção **Entradas locais**, conforme mostrado na figura a seguir:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Para saber mais sobre as entradas de mensagens do Graylog, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Depois que essas configurações forem feitas, você poderá iniciar o Logstash para começar a ler nos logs de fluxo com o seguinte comando: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Pesquisar por meio de mensagens do Graylog

Após dar algum tempo para o servidor do Graylog coletar mensagens, será possível pesquisar as mensagens. Para verificar as mensagens enviadas ao servidor do Graylog, na página de configuração **Entradas**, clique no botão "**Mostrar mensagens recebidas**" da entrada GELF UDP criada. Você será direcionado para uma tela semelhante à imagem a seguir: 

![Histograma](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Clicar no link azul "% {Message}" expande cada mensagem para mostrar os parâmetros de cada tupla do fluxo, conforme mostrado na figura a seguir:

![Mensagens](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Por padrão, todos os campos de mensagem são incluídos na pesquisa se você não selecionar um campo de mensagem específica para pesquisar. Se você quiser pesquisar mensagens específicas (isto é, tuplas de fluxo de um IP de fonte específica), você poderá usar a linguagem de consulta de pesquisa do Graylog conforme [documentado](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analisar logs de fluxo do grupo de segurança de rede usando o Graylog

Agora que o Graylog está configurado e em execução, você pode usar algumas das suas funcionalidades para entender melhor os dados de log do fluxo. Um modo é usar os painéis para criar exibições específicas de seus dados.

### <a name="create-a-dashboard"></a>Criar um painel

1. Na barra de navegação superior, selecione **Painéis** ou navegue até `http://<graylog-server-ip>:9000/dashboards/`

2. A partir daí, clique no botão verde **Criar painel** e preencha o formulário curto com o título e a descrição do seu painel. Clique no botão **Salvar** para criar um novo painel. Você verá um painel semelhante à seguinte imagem:

    ![Painéis](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Adicionar widgets

Você pode clicar no título do painel para vê-lo, mas agora ele está vazio, pois ainda não adicionamos nenhum widget. Um widget de tipo útil e fácil para adicionar ao painel são gráficos de **valores rápido**, que exibem uma lista de valores do campo selecionado, bem como sua distribuição.

1. Navegue de volta para os resultados da pesquisa da entrada UDP que está recebendo logs de fluxo selecionando **Pesquisas** na barra de navegação superior.

2. Sob o painel **Resultado da pesquisa** no lado esquerdo da tela, localize a guia **Campos**, que lista os vários campos de cada mensagem recebida da tupla de fluxo.

3. Selecione qualquer parâmetro desejado no qual deseja visualizar (neste exemplo, o IP de origem está selecionado). Para exibir a lista de possíveis widgets, clique na seta suspensa azul à esquerda do campo e selecione **Valores rápidos** para gerar o widget. Você deverá ver algo semelhante à seguinte figura:

   ![IP de origem](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. A partir daí, você pode selecionar o botão **Adicionar ao painel** no canto superior direito do widget e selecione o painel correspondente para adicionar.

5. Navegue de volta para o painel para ver o widget que você acabou de adicionar.

   Você pode adicionar uma variedade de outros widgets, como contagens e histogramas, ao seu painel para acompanhar as métricas importantes, como o painel de exemplo mostrado na figura a seguir:

   ![Painel do Flowlogs](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Para obter mais explicações sobre os outros tipos de widgets e painéis, consulte a [documentação](https://docs.graylog.org/en/2.2/pages/dashboards.html) do Graylog.

Ao integrar o Observador de Rede com o Graylog, você agora tem uma maneira centralizada e conveniente de gerenciar e visualizar os logs de fluxo do grupo de segurança de rede. O Graylog tem vários outros recursos avançados, como streams e alertas, que também podem ser usados para gerenciar os logs de fluxo e entender melhor o tráfego de rede. Agora que você tem o Graylog configurado e conectado ao Azure, fique à vontade para continuar a explorar outras funcionalidades que ele oferece.

## <a name="next-steps"></a>Próximas etapas

Para saber como visualizar os logs de fluxo do grupo de segurança de rede com o Power BI, acesse [Como visualizar logs de fluxos do grupo de segurança de rede com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
