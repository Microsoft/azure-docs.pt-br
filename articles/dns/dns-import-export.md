---
title: Importar e exportar um arquivo de zona de domínio para DNS do Azure usando CLI do Azure | Microsoft Docs
description: Saiba como importar e exportar um arquivo de zona DNS para o DNS do Azure usando a CLI do Azure
services: dns
author: vhorne
ms.service: dns
ms.date: 4/3/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: b65b70e7a994d7d49b2282d7e193fe6e7b84cfca
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612762"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um arquivo de zona DNS usando a CLI do Azure

Este artigo explica como importar e exportar arquivos da zona DNS para DNS do Azure usando a CLI do Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro DNS (Sistema de Nomes de Domínio ) na zona. Ele segue um formato padrão, tornando-o adequado para transferir registros DNS entre sistemas DNS. O uso de um arquivo de zona é uma maneira rápida, confiável e conveniente de transferir uma zona DNS para dentro ou fora do DNS do Azure.

O DNS do Azure oferece suporte à importação e exportação de arquivos de zona usando a interface de linha de comando (CLI) do Azure. Atualmente, **não** há suporte para a importação do arquivo de zona por meio do Azure PowerShell ou do Portal do Azure.

A CLI do Azure é uma ferramenta de linha de comando de plataforma cruzada usada para gerenciar os serviços do Azure. Ela está disponível para as plataformas Windows, Mac e Linux por meio da [página de downloads do Azure](https://azure.microsoft.com/downloads/). O a plataforma cruzada é importante para importar e exportar arquivos de zona, pois o software para servidores de nomes mais comum, o [BIND](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenha seu arquivo de zona DNS existente

Antes de importar um arquivo de zona DNS para o DNS do Azure, você precisa obter uma cópia do arquivo de zona. A origem do arquivo depende do local em que a zona DNS está hospedada no momento.

* Se a zona DNS for hospedada por um serviço de parceiro (como um registrador de domínio, um provedor de host DNS dedicado ou um provedor de nuvem alternativo), esse serviço deverá fornecer a capacidade de baixar o arquivo de zona DNS.
* Se a zona DNS estiver hospedada no DNS do Windows, a pasta padrão para os arquivos de zona será **%systemroot%\system32\dns**. O caminho completo de cada arquivo de zona também é mostrado na guia **Geral** do console de DNS.
* Se a zona DNS for hospedada usando o BIND, o local do arquivo de zona para cada zona será especificado no arquivo de configuração do BIND, **named.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um arquivo de zona DNS para o DNS do Azure

A importação de um arquivo de zona criará uma nova zona no DNS do Azure, se ela ainda não existir. Se a zona já existir, os conjuntos de registros no arquivo de zona deverão ser mesclados a conjuntos de registros existentes.

### <a name="merge-behavior"></a>Comportamento de mesclagem

* Por padrão, os conjuntos de registros novos e existentes são mesclados. Registros idênticos em um conjunto de registros mesclados têm a duplicação eliminada.
* Quando os conjuntos de registros são mesclados, é usado o TTL dos conjuntos de registros já existentes.
* Os parâmetros de início de autoridade (SOA) (exceto `host`) são sempre obtidos do arquivo de zona importado. Da mesma forma, para o conjunto do registro de nome do servidor no ápice da zona, o TTL sempre é obtido do arquivo de zona importado.
* Um registro CNAME importado não substitui um registro CNAME existente pelo mesmo nome.  
* Quando ocorre um conflito entre um registro CNAME e outro registro com o mesmo nome, mas com tipo diferente (independentemente de qual deles é existente ou novo), o registro existente é mantido. 

### <a name="additional-information-about-importing"></a>Informações adicionais sobre importação

As observações a seguir fornecem detalhes técnicos adicionais sobre o processo de importação de zona.

* A diretiva `$TTL` é opcional e tem suporte. Quando nenhuma diretiva `$TTL` for especificada, registros sem um TTL explícito são importados definidos com um TTL padrão de 3600 segundos. Quando dois registros no mesmo conjunto de registros especificarem TTLs diferentes, o menor valor será usado.
* A diretiva `$ORIGIN` é opcional e tem suporte. Quando nenhuma `$ORIGIN` for definida, o valor padrão usado será o nome da zona, conforme especificado na linha de comando (além do "." de terminação).
* As diretivas `$INCLUDE` e `$GENERATE` não têm suporte.
* Estes tipos de registro são permitidos: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registro SOA é criado automaticamente pelo DNS do Azure quando uma zona é criada. Quando você importa um arquivo de zona, todos os parâmetros SOA são extraídos do arquivo de zona, *exceto* o parâmetro `host`. Esse parâmetro usa o valor fornecido pelo DNS do Azure. Isso ocorre porque esse parâmetro deve referir-se ao servidor de nomes primário fornecido pelo DNS do Azure.
* O registro de nome do servidor definido no ápice da zona também é criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o TTL desse conjunto de registros é importado. Esses registros contêm os nomes de servidor de nome fornecidos pelo DNS do Azure. Os dados de registro não são substituídos pelos valores contidos no arquivo de zona importado.
* Durante a visualização pública, o DNS do Azure suporta apenas os registros TXT de cadeia de caracteres única. Registros TXT de cadeia de caracteres múltiplas são concatenados e truncados para 255 caracteres.

### <a name="cli-format-and-values"></a>Valores e formato da CLI

O formato do comando da CLI do Azure para importar uma zona DNS é:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser importado.

Se uma zona com esse nome não existir no grupo de recursos, ela será criada para você. Se a zona já existir, os conjuntos de registros importados serão mesclados aos conjuntos de registros existentes. 

### <a name="step-1-import-a-zone-file"></a>Etapa 1. Importar um arquivo de zona

Para importar um arquivo de zona para a zona **contoso.com**.

1. Se você ainda não tiver um, precisará criar um grupo de recursos do Gerenciador de Recursos.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Para importar a zona **contoso.com** do arquivo **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup**, execute o comando `az network dns zone import`.<BR>Este comando carrega o arquivo de zona e analisa-o. O comando executa uma série de comandos no serviço DNS do Azure para criar a zona e todos os conjuntos de registro na zona. O comando relata o progresso na janela do console, bem como erros ou avisos. Como os conjuntos de registros são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Etapa 2. Verificar a zona

Para verificar a zona DNS após importar o arquivo, você pode usar qualquer um dos seguintes métodos:

* Você pode listar os registros usando o seguinte comando da CLI do Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Você pode listar os registros usando o comando `az network dns record-set ns list` da CLI do Azure.
* Você pode usar `nslookup` para verificar a resolução de nomes para os registros. Como a zona ainda não foi delegada, você precisará especificar os servidores de nomes DNS do Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes do servidor de nomes atribuídos à zona. Também mostra como consultar o registro "www" usando `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Etapa 3. Atualizar a delegação DNS

Após verificar se a zona foi importada corretamente, você precisará atualizar a delegação DNS para apontar para os servidores de nomes DNS do Azure. Para saber mais, confira o artigo [Atualizar a delegação DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Como exportar um arquivo de zona DNS do DNS do Azure

O formato do comando da CLI do Azure para exportar uma zona DNS é:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser exportado.

Como acontece com a importação de zona, você primeiro precisa fazer logon, escolher sua assinatura e configurar a CLI do Azure para usar o modo do Gerenciador de Recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um arquivo de zona

Para exportar a zona DNS do Azure **contoso.com** existente no grupo de recursos **myresourcegroup** para o arquivo **contoso.com.txt** (na pasta atual), execute `azure network dns zone export`. Esse comando chama o serviço DNS do Azure para enumerar os conjuntos de registros na zona e exporta os resultados para um arquivo de zona compatível com BIND.

```
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como [gerenciar conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) em sua zona DNS.

* Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).
