---
title: Coletando fontes de dados JSON personalizadas com o agente do Log Analytics para Linux no Azure Monitor
description: As fontes de dados JSON personalizadas podem ser coletadas no Azure Monitor usando o agente do Log Analytics para Linux.  Essas fontes de dados personalizados podem ser scripts simples retornando JSON, assim como curl ou um dos mais de 300 plug-ins do FluentD. Este artigo descreve a configuração necessária para essa coleta de dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aa6931fc91838173a856ef500145fa49f2606271
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655196"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Coletando fontes de dados JSON personalizadas com o agente do Log Analytics para Linux no Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

As fontes de dados JSON personalizadas podem ser coletadas no [Azure Monitor](data-platform.md) usando o agente do Log Analytics para Linux.  Essas fontes de dados personalizadas podem ser scripts simples retornando JSON, como [ondulação](https://curl.haxx.se/) ou um dos mais de [300 plug-ins do fluentd](https://www.fluentd.org/plugins/all). Este artigo descreve a configuração necessária para essa coleta de dados.


> [!NOTE]
> O agente do Log Analytics para Linux v1.1.0-217+ é necessário para dados JSON personalizados

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configurar plug-in de entrada

Para coletar dados JSON no Azure Monitor, adicione `oms.api.` ao início de uma marca FluentD em um plug-in de entrada.

Por exemplo, a seguir temos um arquivo de configuração separado `exec-json.conf` em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Isso usa o plug-in FluentD `exec` para executar um comando curl a cada 30 segundos.  A saída desse comando é coletada pelo plug-in de saída do JSON.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

O arquivo de configuração adicionado a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` precisará ter sua propriedade alterada com o comando a seguir.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurar o plug-in de saída 
Adicione a configuração de plug-in de saída a seguir à configuração principal em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou como um arquivo de configuração separado colocado em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Reiniciar o agente do Log Analytics para Linux
Reinicie o serviço agente do Log Analytics para Linux com o comando a seguir.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Saída
Os dados serão coletados nos logs do Azure Monitor com um tipo de registro igual a `<FLUENTD_TAG>_CL`.

Por exemplo, a marca personalizada `tag oms.api.tomcat` no Azure Monitor com um tipo de registro igual a `tomcat_CL`.  Você pode recuperar todos os registros desse tipo com a consulta de log a seguir.

```console
Type=tomcat_CL
```

Fontes de dados JSON aninhados têm suporte, mas são indexadas sem se basear no campo pai. Por exemplo, os dados JSON a seguir são retornados de uma consulta de log como `tag_s : "[{ "a":"1", "b":"2" }]`.

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 
