---
title: Como obter os nomes de host dos nós de cluster do Azure HDInsight
description: Saiba mais sobre como obter nomes de host e nome FQDN de nós de cluster do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111420"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>Localizar os nomes de host dos nós de cluster

O cluster HDInsight é criado com clustername.azurehdinsight.net de DNS público. Quando você usa o SSH em nós individuais ou define a conexão com nós de cluster com na mesma rede virtual personalizada, você precisa usar o nome do host ou nomes de domínio totalmente qualificados (FQDN) de nós de cluster.

Neste artigo, você aprenderá a obter os nomes de host dos nós de cluster. Você pode obtê-lo manualmente por meio da interface do usuário da Web do Ambari ou automaticamente por meio da API REST do Ambari.

> [!WARNING]
> Use as seguintes abordagens recomendadas para buscar nomes de host de nós de cluster. Os números no nome do host não são garantidos em sequência e o HDInsight pode alterar o formato do nome do host para se alinhar com as VMs com a atualização da versão. Não use a dependência de uma determinada Convenção de nomenclatura que exista hoje. 
>

Você pode obter os nomes de host por meio da interface do usuário do amAmbari ou da API REST do Ambari. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Obter os nomes de host da interface do usuário da Web do amAmbari
Você pode usar a interface do usuário da Web do amAmbari para obter os nomes de host ao usar o SSH para o nó. A exibição de hosts da Web do amAmbari está disponível em seu cluster HDInsight em `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , em que `CLUSTERNAME` é o nome do cluster.

![Get-Host-Names-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Obter os nomes de host da API REST do Ambari
Ao criar scripts de automação, você pode usar a API REST do amAmbari para obter os nomes de host antes de fazer conexões com os hosts. Os números no nome do host não são garantidos em sequência e o HDInsight pode alterar o formato do nome do host para se alinhar com as VMs com a atualização da versão. Não use a dependência de uma determinada Convenção de nomenclatura que exista hoje. 

Aqui estão alguns exemplos de como recuperar o FQDN para os nós no cluster. Para obter mais informações sobre a API REST do Ambari, consulte [gerenciar clusters HDInsight usando a API REST do Apache Ambari](.\hdinsight-hadoop-manage-ambari-rest-api.md)

O exemplo a seguir usa [JQ](https://stedolan.github.io/jq/) ou [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar o documento de resposta JSON e exibir apenas os nomes de host.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```