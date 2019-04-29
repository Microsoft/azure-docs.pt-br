---
title: Monitorar e gerenciar o Hadoop com a API REST do Ambari – Azure HDInsight
description: Aprenda a usar o Ambari para monitorar e gerenciar clusters Hadoop no Azure HDInsight. Neste documento, você aprenderá a usar a API REST do Ambari incluída com clusters HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: d15b61c70f1587cdd1c0d76d2e3eab81294674fc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761137"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerenciar clusters HDInsight usando a API REST do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar a API REST do Apache Ambari para gerenciar e monitorar clusters Apache Hadoop no Azure HDInsight.

## <a id="whatis"></a>O que é o Apache Ambari
[O Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento e monitoramento de clusters de Hadoop, fornecendo um interface do usuário com o respaldo de web fácil de usar sua [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por padrão com os clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos
* **Um cluster Hadoop no HDInsight**. Ver [Introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash no Ubuntu no Windows 10**.  Os exemplos neste artigo usam o shell Bash no Windows 10. Ver [subsistema do Windows para Linux instalação guia para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para etapas de instalação.  Outros [shells do Unix](https://www.gnu.org/software/bash/) funcionará bem.  Os exemplos, com algumas pequenas modificações, podem funcionar em um prompt de comando do Windows.  Como alternativa, você pode usar o Windows PowerShell.

* **jq**, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Como alternativa, você pode usar [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Base de URI para a API de Rest do Ambari

 É o URI de base para a API REST do Ambari no HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, onde `CLUSTERNAME` é o nome do seu cluster.  Nomes de cluster nos URIs são **diferencia maiusculas de minúsculas**.  Embora o nome do cluster na parte do nome de domínio totalmente qualificado (FQDN) do URI (CLUSTERNAME.azurehdinsight.net) diferencie maiúsculas de minúsculas, outras ocorrências no URI diferenciam maiúsculas de minúsculas.

## <a name="authentication"></a>Authentication

Conectar-se ao Ambari no HDInsight requer HTTPS. Use o nome da conta do administrador (o padrão é **admin**) e a senha fornecidos durante a criação do cluster.

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Instalação (preservar credenciais)
Preserve suas credenciais para evitar a reinserção-los para cada exemplo.  O nome do cluster será preservado em uma etapa separada.

**A. Bash**  
Edite o script abaixo, substituindo `PASSWORD` com sua senha real.  Em seguida, digite o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificar o nome do cluster com maiusculas e minúsculas corretamente
A real maiusculas e minúsculas do nome do cluster podem ser diferente do que o esperado, dependendo de como o cluster foi criado.  As etapas aqui mostram o uso de maiusculas e minúsculas real e, em seguida, armazená-lo em uma variável para todos os exemplos subsequentes.

Editar os scripts a seguir para substituir `CLUSTERNAME` pelo nome do cluster. Em seguida, digite o comando. (O nome do cluster para o FQDN não diferencia maiusculas de minúsculas).

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analisar dados JSON

O exemplo a seguir usa [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e exibir somente o `health_report` informações dos resultados.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Obtenha o FQDN de nós de cluster

Ao trabalhar com o HDInsight, você precisará saber o nome de domínio totalmente qualificado (FQDN) de um nó do cluster. Você pode recuperar facilmente o FQDN para vários nós no cluster usando os seguintes exemplos:

**Todos os nós**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nós de cabeçalho**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós de trabalho**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós do Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Obter o endereço IP interno de nós de cluster

Os endereços IP retornados pelos exemplos nesta seção não estão diretamente acessíveis pela internet. Eles só são acessíveis na rede Virtual do Azure que contém o cluster HDInsight.

Para saber mais sobre como trabalhar com o HDInsight e com as redes virtuais, veja [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

Para localizar o endereço IP, você deve saber o FQDN (nome de domínio totalmente qualificado) interno dos nós de cluster. Uma vez que o FQDN, em seguida, você pode obter o endereço IP do host. Os exemplos a seguir primeiro consultar o Ambari para o FQDN de todos os nós de host e consultar o Ambari para o endereço IP de cada host.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Obter o armazenamento padrão

Quando você cria um cluster HDInsight, será necessário usar uma conta do Armazenamento do Azure ou o Data Lake Storage como o armazenamento padrão para o cluster. Você pode usar o Ambari para recuperar essas informações após a criação do cluster. Por exemplo, se você quiser ler/gravar dados no contêiner fora do HDInsight.

Os exemplos a seguir recuperar a configuração de armazenamento padrão do cluster:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Estes exemplos retornam a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém essas informações. Se você recuperar um valor que foi modificado após a criação do cluster, talvez seja necessário listar as versões de configuração e recuperar a mais recente.

O valor de retorno é semelhante a um dos exemplos a seguir:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-Este valor indica que o cluster está usando uma conta de armazenamento do Azure para armazenamento padrão. O valor `ACCOUNTNAME` é o nome da conta de armazenamento. O `CONTAINER` parte é o nome do contêiner de blob na conta de armazenamento. O contêiner é a raiz de armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Este valor indica que o cluster está usando um Azure Data Lake Storage Gen2 para armazenamento padrão. O `ACCOUNTNAME` e `CONTAINER` valores têm os mesmos significados que para o Armazenamento do Microsoft Azure mencionados anteriormente.

* `adl://home`: este valor indica que o cluster está usando um Azure Data Lake Storage Gen1 para armazenamento padrão.

    Para localizar o nome da conta do Data Lake Storage, use os exemplos a seguir:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    O valor de retorno é semelhante ao `ACCOUNTNAME.azuredatalakestore.net`, onde `ACCOUNTNAME` é o nome da conta do Data Lake Storage.

    Para localizar o diretório no Data Lake Storage que contém o armazenamento do cluster, use os exemplos a seguir:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    O valor de retorno é semelhante ao `/clusters/CLUSTERNAME/`. Esse valor é um caminho dentro da conta do Data Lake Storage. Esse caminho é a raiz do sistema de arquivos compatível com HDFS para o cluster.  

> [!NOTE]  
> O [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet fornecido pelo [do Azure PowerShell](/powershell/azure/overview) também retorna as informações de armazenamento para o cluster.


### <a name="get-all-configurations"></a> Obter todas as configurações

Obtenha as configurações que estão disponíveis para o seu cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Esse exemplo retorna um documento JSON contendo a configuração atual (identificada pelo valor *tag* ) para os componentes instalados no cluster. O exemplo a seguir é um trecho dos dados retornados de um tipo de cluster Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Obter configuração de componente específico

Obtenha a configuração para o componente em que você tem interesse. No exemplo a seguir, substitua `INITIAL` pelo valor retornado da solicitação anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual do componente `livy2-conf`.

### <a name="update-configuration"></a>Atualizar configuração

1. Crie `newconfig.json`.  
   Modifique e, em seguida, insira os comandos a seguir:

   * Substitua `livy2-conf` com o componente desejado.
   * Substitua `INITIAL` com o valor real recuperado para `tag` partir [obter todas as configurações de](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **Powershell b.**  
     O script do PowerShell usa [jq](https://stedolan.github.io/jq/).  Edite `C:\HD\jq\jq-win64` abaixo para refletir o caminho real e a versão do [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     O jq é usado para transformar os dados recuperados do HDInsight em um novo modelo de configuração. Especificamente, esses exemplos executam as seguintes ações:

   * Cria um valor exclusivo que contém a cadeia de caracteres "version" e a data, que é armazenada em `newtag`.

   * Cria um documento raiz para a nova configuração desejada.

   * Obtém o conteúdo da matriz `.items[]` e o adiciona sob o elemento **desired_config**.

   * Exclui os elementos `href`, `version` e `Config`, pois eles não são necessários para enviar uma nova configuração.

   * Adiciona um elemento `tag` com um valor de `version#################`. A parte numérica tem base na data atual. Cada configuração deve ter uma marca exclusiva.

     Por fim, os dados são salvos no documento `newconfig.json`. A estrutura do documento deve ser semelhante a este exemplo:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Editar `newconfig.json`.  
   Abra o documento `newconfig.json` e modifique/adicione valores no objeto `properties`. O exemplo a seguir altera o valor de `"livy.server.csrf_protection.enabled"` desde `"true"` até `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Quando terminar de fazer modificações, salve o arquivo.

3. Enviar `newconfig.json`.  
   Use os seguintes comandos para enviar a configuração atualizada ao Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Esses comandos enviam o conteúdo do arquivo **newconfig.json** para o cluster como a nova configuração desejada. A solicitação retorna um documento JSON. O elemento **versionTag** nesse documento deverá corresponder à versão enviada e o objeto **configs** conterá as alterações de configuração solicitadas.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, se você examinar a interface do usuário da Web do Ambari, o serviço do Spark indicará que ele precisa ser reiniciado para que a nova configuração entre em vigor. Use as etapas a seguir para reiniciar o serviço.

1. Use o seguinte para habilitar o modo de manutenção para o serviço Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Verifique se o modo de manutenção  

    Estes comandos enviam um documento JSON para o servidor que ativa o modo de manutenção. Você pode verificar se o serviço está em modo de manutenção usando a seguinte solicitação:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    O valor de retorno é `ON`.

3. Em seguida, use o seguinte para desativar o serviço Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    A resposta é semelhante ao seguinte exemplo:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > O valor `href` retornado por esse URI usa o endereço IP interno do nó de cluster. Para usá-lo de fora do cluster, substitua a parte '10.0.0.18:8080' pelo FQDN do cluster.  

4. Verifique se a solicitação.  
    Editar o comando abaixo, substituindo `29` com o valor real para `id` retornado da etapa anterior.  Os comandos a seguir recuperam o status da solicitação:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Uma resposta de `COMPLETED` indica que a solicitação foi concluída.

5. Quando a solicitação anterior for concluída, use o seguinte para iniciar o serviço Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    O serviço agora está usando a nova configuração.

6. Por fim, use o seguinte para desativar o modo de manutenção:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Próximas etapas

Para obter uma referência completa da API REST, consulte [Referência de API do Apache Ambari, V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

