---
title: Conectar fontes de dados por meio do Logstash ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o Logstash para encaminhar logs de fontes de dados externas para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 63b9d74fbbb1a79dd4f3d3e7c5fb094a372282e0
ms.sourcegitcommit: 5e2f5efba1957ba40bd951c3dcad42f4a00734ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96299625"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Usar o Logstash para conectar fontes de dados ao Azure Sentinel

> [!IMPORTANT]
> A ingestão de dados usando o plug-in de saída Logstash está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usando o novo plug-in de saída do Sentinela do Azure para o **mecanismo de coleta de dados Logstash**, agora você pode enviar qualquer tipo de log que desejar por meio do Logstash diretamente para seu espaço de trabalho do log Analytics no Azure sentinela. Seus logs serão enviados para uma tabela personalizada que você definirá usando o plug-in de saída.

Para saber mais sobre como trabalhar com o mecanismo de coleta de dados do Logstash, consulte [introdução ao Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Visão geral

### <a name="architecture-and-background"></a>Arquitetura e plano de fundo

![Diagrama da arquitetura stash de log.](./media/connect-logstash/logstash-architecture.png)

O mecanismo Logstash é composto por três componentes:

- Plug-ins de entrada: coleção personalizada de dados de várias fontes.
- Filtrar plug-ins: manipulação e normalização de dados de acordo com os critérios especificados.
- Plug-ins de saída: envio personalizado de dados coletados e processados para vários destinos.

> [!NOTE]
> O Azure Sentinel dá suporte apenas a seu próprio plug-in de saída fornecido. Ele não dá suporte a plug-ins de saída de terceiros para o Azure Sentinel ou qualquer outro plug-in Logstash de qualquer tipo.

O plug-in de saída do Sentinela do Azure para Logstash envia dados formatados em JSON para seu espaço de trabalho Log Analytics, usando a API REST do coletor de dados HTTP Log Analytics. Os dados são ingeridos em logs personalizados.

- Saiba mais sobre a [API REST do log Analytics](/rest/api/loganalytics/create-request).
- Saiba mais sobre [logs personalizados](../azure-monitor/platform/data-sources-custom-logs.md).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Implantar o plug-in de saída do Sentinela do Azure no Logstash

### <a name="step-1-installation"></a>Etapa 1: instalação

O plug-in de saída do Sentinela do Azure está disponível na coleção Logstash.

- Siga as instruções no documento Logstash [trabalhando com plugins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) para instalar o plug-in **_[Microsoft-Logstash-output-Azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)_* _.
   
- Se o sistema Logstash não tiver acesso à Internet, siga as instruções no documento de [Gerenciamento de plug-ins offline](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) do Logstash para preparar e usar um pacote de plugin offline. (Isso exigirá que você crie outro sistema Logstash com acesso à Internet.)

### <a name="step-2-configuration"></a>Etapa 2: configuração

Use as informações na estrutura Logstash [de um documento de arquivo de configuração](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) e adicione o plug-in de saída do Sentinela do Azure à configuração com as seguintes chaves e valores. (A sintaxe apropriada do arquivo de configuração é mostrada após a tabela.)

| Nome do campo | Tipo de dados | Descrição |
|----------------|---------------|-----------------|
| `workspace_id` | string | Insira seu GUID de ID do espaço de trabalho. _ |
| `workspace_key` | string | Insira o GUID de chave primária do espaço de trabalho. * |
| `custom_log_table_name` | string | Defina o nome da tabela na qual os logs serão ingeridos. Somente um nome de tabela por plug-in de saída pode ser configurado. A tabela de log será exibida no Azure Sentinel em **logs**, em **tabelas** na categoria **logs personalizados** , com um `_CL` sufixo. |
| `endpoint` | string | Campo opcional. Por padrão, esse é o ponto de extremidade Log Analytics. Use este campo para definir um ponto de extremidade alternativo. |
| `time_generated_field` | string | Campo opcional. Essa propriedade substitui o campo **TimeGenerated** padrão em log Analytics. Insira o nome do campo de carimbo de data/hora na fonte de dados. Os dados no campo devem estar de acordo com o formato ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ) |
| `key_names` | matriz | Insira uma lista de Log Analytics campos de esquema de saída. Cada item de lista deve ser colocado entre aspas simples e os itens separados por vírgulas e a lista inteira entre colchetes. Consulte o exemplo a seguir. |
| `plugin_flush_interval` | número | Campo opcional. Defina para definir o intervalo máximo (em segundos) entre as transmissões de mensagem para Log Analytics. O padrão é 5. |
    | `amount_resizing` | booleano | True ou false. Habilitar ou desabilitar o mecanismo de dimensionamento automático, que ajusta o tamanho do buffer de mensagens de acordo com o volume de dados de log recebidos. |
| `max_items` | número | Campo opcional. Aplica-se somente se `amount_resizing` definido como "false". Use para definir um limite no tamanho do buffer de mensagens (em registros). O padrão é 2000.  |

\* Você pode encontrar a ID do espaço de trabalho e a chave primária no recurso de espaço de trabalho, em **Gerenciamento de agentes**.

#### <a name="sample-configurations"></a>Exemplos de configuração

Aqui estão algumas configurações de exemplo que usam algumas opções diferentes.

- Uma configuração básica que usa um pipe de entrada de filesuperation:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Uma configuração básica que usa um pipe de entrada TCP:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Uma configuração avançada:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Visite o [repositório GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) do plug-in de saída para saber mais sobre suas configurações internas de trabalho, configuração e desempenho.

### <a name="step-3-restart-logstash"></a>Etapa 3: reiniciar o Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Etapa 4: exibir os logs de entrada no Azure Sentinel

1. Verifique se as mensagens estão sendo enviadas para o plug-in de saída.

1. No menu de navegação do Azure Sentinel, clique em **logs**. No título **tabelas** , expanda a categoria **logs personalizados** . Localize e clique no nome da tabela que você especificou (com um `_CL` sufixo) na configuração.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Captura de tela dos logs personalizados do Stash de log.":::

1. Para ver os registros na tabela, consulte a tabela usando o nome da tabela como o esquema.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Captura de tela de uma consulta de logs personalizados do Stash de log.":::

## <a name="monitor-output-plugin-audit-logs"></a>Monitorar logs de auditoria do plug-in de saída

Para monitorar a conectividade e a atividade do plug-in de saída do Sentinela do Azure, habilite o arquivo de log Logstash apropriado. Consulte o documento [layout do diretório Logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) para o local do arquivo de log.

Se você não estiver vendo nenhum dado nesse arquivo de log, gere e envie alguns eventos localmente (por meio dos plug-ins de entrada e filtro) para verificar se o plug-in de saída está recebendo dados. O Azure Sentinel dará suporte apenas a problemas relacionados ao plug-in de saída.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o Logstash para conectar fontes de dados externas ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [internas](tutorial-detect-threats-built-in.md) ou [personalizadas](tutorial-detect-threats-custom.md) .
