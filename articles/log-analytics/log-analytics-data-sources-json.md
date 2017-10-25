---
title: Coletar dados JSON personalizados no OMS Log Analytics | Microsoft Docs
description: "Fontes de dados JSON personalizados podem ser coletadas no Log Analytics usando o Agente do OMS para Linux.  Essas fontes de dados personalizados podem ser scripts simples retornando JSON, assim como curl ou um dos mais de 300 plug-ins do FluentD. Este artigo descreve a configuração necessária para essa coleta de dados."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Coletar fontes de dados JSON personalizados com o Agente do OMS para Linux em Log Analytics
Fontes de dados JSON personalizados podem ser coletadas no Log Analytics usando o Agente do OMS para Linux.  Essas fontes de dados personalizados podem ser scripts simples retornando JSON, assim como [curl](https://curl.haxx.se/) ou um dos [mais de 300 plug-ins do FluentD](http://www.fluentd.org/plugins/all). Este artigo descreve a configuração necessária para essa coleta de dados.

> [!NOTE]
> O Agente do OMS para Linux v1.1.0-217+ é necessário para dados JSON personalizados

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configurar plug-in de entrada

Para coletar dados JSON no Log Analytics, adicione `oms.api.` ao início de uma marcação FluentD em um plug-in de entrada.

Por exemplo, a seguir temos um arquivo de configuração separado `exec-json.conf` em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Isso usa o plug-in FluentD `exec` para executar um comando curl a cada 30 segundos.  A saída desse comando é coletada pelo plug-in de saída do JSON.

```
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

```
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

### <a name="restart-oms-agent-for-linux"></a>Reiniciar o Agente do OMS para Linux
Reinicie o serviço Agente do OMS para Linux com o comando a seguir.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Saída
Os dados serão coletados no Log Analytics com um tipo de registro de `<FLUENTD_TAG>_CL`.

Por exemplo, a marcação personalizada `tag oms.api.tomcat` no Log Analytics com um tipo de registro de `tomcat_CL`.  Você pode recuperar todos os registros desse tipo com a pesquisa de logs a seguir.

    Type=tomcat_CL

Fontes de dados JSON aninhados têm suporte, mas são indexadas sem se basear no campo pai. Por exemplo, os seguintes dados JSON são retornados de uma pesquisa do Log Analytics como `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções. 
 