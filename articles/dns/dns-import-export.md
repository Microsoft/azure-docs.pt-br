---
title: "Importar e exportar um arquivo de zona de domínio para o DNS do Azure usando a CLI 1.0 do Azure | Microsoft Docs"
description: Saiba como importar e exportar um arquivo de zona DNS para o DNS do Azure usando a CLI do Azure 1.0
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d6d3fa7aa0e8b2462b3a6b4b66d3d87ab5535314
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-10"></a>Importar e exportar um arquivo da zona DNS usando a CLI do Azure 1.0 

Este artigo explica como importar e exportar arquivos da zona DNS para o DNS do Azure usando a CLI do Azure 1.0.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro DNS (Sistema de Nomes de Domínio ) na zona. Ele segue um formato padrão, tornando-o adequado para transferir registros DNS entre sistemas DNS. O uso de um arquivo de zona é uma maneira rápida, confiável e conveniente de transferir uma zona DNS para dentro ou fora do DNS do Azure.

O DNS do Azure oferece suporte à importação e exportação de arquivos de zona usando a interface de linha de comando (CLI) do Azure. Atualmente, **não** há suporte para a importação do arquivo de zona por meio do Azure PowerShell ou do Portal do Azure.

A CLI do Azure 1.0 é uma ferramenta de linha de comando de plataforma cruzada empregada para gerenciar os serviços do Azure. Ela está disponível para as plataformas Windows, Mac e Linux por meio da [página de downloads do Azure](https://azure.microsoft.com/downloads/). O a plataforma cruzada é importante para importar e exportar arquivos de zona, pois o software para servidores de nomes mais comum, o [BIND](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

> [!NOTE]
> Atualmente, há duas versões da CLI do Azure. A CLI1.0 é baseada em Node.js e tem comandos que começam com “azure”.
> A CLI2.0 é baseada em Python e tem comandos que começam com “az”. Embora ambas as versões deem suporte à importação do arquivo de zona, é recomendável usar os comandos da CLI1.0, conforme descrito nesta página.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenha seu arquivo de zona DNS existente

Antes de importar um arquivo de zona DNS para o DNS do Azure, você precisa obter uma cópia do arquivo de zona. A origem do arquivo depende do local em que a zona DNS está hospedada no momento.

* Se a zona DNS for hospedada por um serviço de parceiro (como um registrador de domínio, um provedor de host DNS dedicado ou um provedor de nuvem alternativo), esse serviço deverá fornecer a capacidade de baixar o arquivo de zona DNS.
* Se a zona DNS estiver hospedada no DNS do Windows, a pasta padrão para os arquivos de zona será **%systemroot%\system32\dns**. O caminho completo de cada arquivo de zona também é mostrado na guia **Geral** do console de DNS.
* Se a zona DNS for hospedada usando o BIND, o local do arquivo de zona para cada zona será especificado no arquivo de configuração do BIND, **named.conf**.

> [!NOTE]
> Arquivos de zona baixados do GoDaddy têm um formato ligeiramente diferente do padrão. Você precisa corrigir o problema antes de importar esses arquivos de zona DNS do Azure.
>
> Nomes DNS no RDATA de cada registro DNS são especificados como nomes totalmente qualificados, mas não têm uma terminação “.” Isso significa que eles são interpretados por outros sistemas DNS como nomes relativos. Você precisa editar o arquivo de zona para acrescentar o '.' de terminação aos nomes antes de importá-los para o DNS do Azure.
>
> Por exemplo, o registro CNAME record “www 3600 IN CNAME contoso.com” deve ser alterado para “www 3600 IN CNAME contoso.com.”
> (com uma terminação “.”).

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um arquivo de zona DNS para o DNS do Azure

A importação de um arquivo de zona criará uma nova zona no DNS do Azure, se ela ainda não existir. Se a zona já existir, os conjuntos de registros no arquivo de zona deverão ser mesclados a conjuntos de registros existentes.

### <a name="merge-behavior"></a>Comportamento de mesclagem

* Por padrão, os conjuntos de registros novos e existentes são mesclados. Registros idênticos em um conjunto de registros mesclados têm a duplicação eliminada.
* Como alternativa, ao especificar a opção `--force`, o processo de importação substitui os conjuntos de registros existentes por novos conjuntos de registros. Os conjuntos de registros existentes que não tiverem um conjunto de registros correspondente no arquivo de zona importado não serão removidos.
* Quando os conjuntos de registros são mesclados, é usado o TTL dos conjuntos de registros já existentes. Quando `--force` é usado, o TTL do novo conjunto de registros é usado.
* Parâmetros SOA (Início de Autoridade) (exceto `host`) sempre são obtidos do arquivo de zona importado, independentemente do uso de `--force`. Da mesma forma, para o conjunto do registro de nome do servidor no ápice da zona, o TTL sempre é obtido do arquivo de zona importado.
* Um registro CNAME importado não substituirá um registro CNAME existente com o mesmo nome, a menos que o parâmetro `--force` seja especificado.
* Quando ocorre um conflito entre um registro CNAME e outro registro com o mesmo nome, mas com tipo diferente (independentemente de qual deles é existente ou novo), o registro existente é mantido. Isso é independente do uso de `--force`.

### <a name="additional-information-about-importing"></a>Informações adicionais sobre importação

As observações a seguir fornecem detalhes técnicos adicionais sobre o processo de importação de zona.

* A diretiva `$TTL` é opcional e tem suporte. Quando nenhuma diretiva `$TTL` for especificada, registros sem um TTL explícito são importados definidos com um TTL padrão de 3600 segundos. Quando dois registros no mesmo conjunto de registros especificarem TTLs diferentes, o menor valor será usado.
* A diretiva `$ORIGIN` é opcional e tem suporte. Quando nenhuma `$ORIGIN` for definida, o valor padrão usado será o nome da zona, conforme especificado na linha de comando (além do "." de terminação).
* As diretivas `$INCLUDE` e `$GENERATE` não têm suporte.
* Há suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registro SOA é criado automaticamente pelo DNS do Azure quando uma zona é criada. Quando você importa um arquivo de zona, todos os parâmetros SOA são extraídos do arquivo de zona, *exceto* o parâmetro `host`. Esse parâmetro usa o valor fornecido pelo DNS do Azure. Isso ocorre porque esse parâmetro deve referir-se ao servidor de nomes primário fornecido pelo DNS do Azure.
* O registro de nome do servidor definido no ápice da zona também é criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o TTL desse conjunto de registros é importado. Esses registros contêm os nomes de servidor de nome fornecidos pelo DNS do Azure. Os dados de registro não são substituídos pelos valores contidos no arquivo de zona importado.
* Durante a visualização pública, o DNS do Azure suporta apenas os registros TXT de cadeia de caracteres única. Registros TXT de cadeia de caracteres múltiplas são concatenados e truncados para 255 caracteres.

### <a name="cli-format-and-values"></a>Valores e formato da CLI

O formato do comando da CLI do Azure para importar uma zona DNS é:

```azurecli
azure network dns zone import [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser importado.

Se uma zona com esse nome não existir no grupo de recursos, ela será criada para você. Se a zona já existir, os conjuntos de registros importados serão mesclados aos conjuntos de registros existentes. Para substituir os conjuntos de registros existentes, use a opção `--force` .

Para verificar o formato de um arquivo de zona sem realmente importá-lo, use a opção `--parse-only` .

### <a name="step-1-import-a-zone-file"></a>Etapa 1. Importar um arquivo de zona

Para importar um arquivo de zona para a zona **contoso.com**.

1. Entre em sua assinatura do Azure usando a CLI do Azure 1.0.

    ```azurecli
    azure login
    ```

2. Selecione a assinatura em que você deseja criar a nova zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. O DNS do Azure é um serviço somente do Gerenciador de Recursos do Azure. A CLI do Azure deve ser alternada para o modo do Gerenciador de Recursos.

    ```azurecli
    azure config mode arm
    ```

4. Antes de usar o serviço DNS do Azure, você deve registrar sua assinatura para usar o provedor de recursos Microsoft.Network. (Essa operação deve ser executa apenas uma vez para cada assinatura.)

    ```azurecli
    azure provider register Microsoft.Network
    ```

5. Se ainda não o tiver, você também precisará criar um grupo de recursos do Gerenciador de Recursos.

    ```azurecli
    azure group create myresourcegroup westeurope
    ```

6. Para importar a zona **contoso.com** do arquivo **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup**, execute o comando `azure network dns zone import`.<BR>Esse comando carrega o arquivo de zona e analisa-o. O comando executa uma série de comandos no serviço DNS do Azure para criar a zona e todos os conjuntos de registro na zona. O comando relata o progresso na janela do console, bem como erros ou avisos. Como os conjuntos de registros são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

    ```azurecli
    azure network dns zone import myresourcegroup contoso.com contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Etapa 2. Verificar a zona

Para verificar a zona DNS após importar o arquivo, você pode usar qualquer um dos seguintes métodos:

* Você pode listar os registros usando o seguinte comando da CLI do Azure:

    ```azurecli
    azure network dns record-set list myresourcegroup contoso.com
    ```

* Você pode listar os registros usando o cmdlet do PowerShell `Get-AzureRmDnsRecordSet`.
* Você pode usar `nslookup` para verificar a resolução de nomes para os registros. Como a zona ainda não foi delegada, você precisará especificar os servidores de nomes DNS do Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes do servidor de nomes atribuídos à zona. Também mostra como consultar o registro "www" usando `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/.../resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Etapa 3. Atualizar a delegação DNS

Após verificar se a zona foi importada corretamente, você precisará atualizar a delegação DNS para apontar para os servidores de nomes DNS do Azure. Para saber mais, confira o artigo [Atualizar a delegação DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Como exportar um arquivo de zona DNS do DNS do Azure

O formato do comando da CLI do Azure para importar uma zona DNS é:

```azurecli
azure network dns zone export [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser exportado.

Como acontece com a importação de zona, você primeiro precisa fazer logon, escolher sua assinatura e configurar a CLI do Azure para usar o modo do Gerenciador de Recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um arquivo de zona

1. Entre em sua assinatura do Azure usando a CLI do Azure.

    ```azurecli
    azure login
    ```

2. Selecione a assinatura em que você deseja criar a zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. O Azure DNS é um serviço somente do Gerenciador de Recursos do Azure. A CLI do Azure deve ser alternada para o modo do Gerenciador de Recursos.

    ```azurecli
    azure config mode arm
    ```

4. Para exportar a zona DNS do Azure **contoso.com** existente no grupo de recursos **myresourcegroup** para o arquivo **contoso.com.txt** (na pasta atual), execute `azure network dns zone export`. Esse comando chama o serviço DNS do Azure para enumerar os conjuntos de registros na zona e exporta os resultados para um arquivo de zona compatível com BIND.

    ```azurecli
    azure network dns zone export myresourcegroup contoso.com contoso.com.txt
    ```
