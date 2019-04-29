---
title: Gerenciar registros DNS no DNS do Azure usando o Azure CLI | Microsoft Docs
description: Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure.
services: dns
documentationcenter: na
author: WenJason
manager: digimobile
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
origin.date: 05/15/2018
ms.date: 04/15/2019
ms.author: v-jay
ms.openlocfilehash: 4864a46b91b4e243ce6a2ae3d9d36df28fe74d8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293313"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar registros DNS para sua zona DNS usando a CLI do Azure de plataforma cruzada, que está disponível para Windows, Mac e Linux. Você também pode gerenciar seus registros DNS usando o [Azure PowerShell](dns-operations-recordsets.md) ou o [Portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo pressupõem que você já [instalou o Azure CLI, entrou e criou uma zona DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introdução

Antes de criar registros DNS no DNS do Azure, primeiro você precisa entender como o DNS do Azure organiza registros DNS em conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registros DNS no DNS do Azure, confira [Zonas e registros DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `az network dns record-set <record-type> add-record` (em que `<record-type>` é o tipo do registro, ou seja, a, srv, txt etc.) Para obter ajuda, consulte `az network dns record-set --help`.

Ao criar um registro, você precisa especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de registros, o tipo do registro e os detalhes do registro que está sendo criado. O nome do conjunto de registros fornecido deve ser um nome *relativo*, significando que ele deve excluir o nome da zona.

Se o conjunto de registros não existir, este comando o criará para você. Se o conjunto de registros já existir, este comando adicionará o registro que você especifica para o conjunto de registros existente.

Se um novo conjunto de registros for criado, um TTL padrão de 3600 será usado. Para obter instruções sobre como usar TTLs diferentes, consulte [Criar um conjunto de registros DNS](#create-a-dns-record-set).

O exemplo a seguir cria um registro A chamado *www* na zona *contoso.com* no grupo de recursos *MyResourceGroup*. O endereço IP do registro A é *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do Registro "\@", incluindo as aspas:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Criar um conjunto de registros DNS

Nos exemplos acima, o registro DNS ou foi adicionado a um conjunto de registros existente ou então o conjunto de registros foi criado *implicitamente*. Você também pode criar o conjunto de registros *explicitamente* antes de adicionar registros a ele. O DNS do Azure também dá suporte a conjuntos de registros 'vazios', que podem agir como um espaço reservado para reservar um nome DNS antes da criação de registros DNS. Os conjuntos de registros vazios são visíveis no painel de controle do DNS do Azure, mas não aparecem nos servidores de nome DNS do Azure.

Os conjuntos de registros são criados usando o comando `az network dns record-set <record-type> create`. Para obter ajuda, consulte `az network dns record-set <record-type> create --help`.

Criar o registro definido explicitamente permite que você especifique propriedades do conjunto de registros como o [TTL (vida útil)](dns-zones-records.md#time-to-live) e os metadados. Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar os dados específicos do aplicativo com cada conjunto de registros, como pares de chave-valor.

O exemplo a seguir cria um conjunto de registros vazio do tipo ‘A’ com um TTL de 60 segundos, usando o parâmetro `--ttl` (forma abreviada `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

O exemplo a seguir cria um conjunto de registros com duas entradas de metadados, "dept=finance" e "environment=production", pelo uso do parâmetro `--metadata`:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Depois de criar um conjunto de registros vazio, registros podem ser adicionados usando `azure network dns record-set <record-type> add-record` conforme descrito em [Criar um registro DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar outros tipos de registro

Depois de ter visto detalhadamente como criar os registros 'A', os exemplos a seguir mostram como criar registros de outros tipos, aos quais o DNS do Azure dá suporte.

Os parâmetros usados para especificar os dados de registro variam dependendo do tipo de registro. Por exemplo, para um registro do tipo "A", você especifica o endereço IPv4 com o parâmetro `--ipv4-address <IPv4 address>`. Os parâmetros para cada tipo de registro podem ser listados usando `az network dns record-set <record-type> add-record --help`.

Em cada caso, mostraremos como criar um único registro. O registro é adicionado ao conjunto de registros existente ou um conjunto de registros é criado implicitamente. Para obter mais informações sobre como criar conjuntos de registros e definir o parâmetro do conjunto de registros explicitamente, consulte [Criar um conjunto de registros DNS](#create-a-dns-record-set).

Não podemos dar um exemplo para criar um conjunto de registros SOA, pois os SOAs são criados e excluídos com cada zona DNS e não podem ser criados ou excluídos separadamente. No entanto, [o SOA pode ser modificado, como mostrado no exemplo mais adiante](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Criar um registro AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Criar um registro CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Criar um registro CNAME

> [!NOTE]
> Os padrões do DNS não permitem registros CNAME no ápice de uma zona (`--Name "@"`), nem permitem conjuntos de registros que contêm mais de um registro.
> 
> Para obter mais informações, consulte [Registros CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um registro MX

Neste exemplo, usamos o nome do conjunto de registros "\@" para criar o registro MX no vértice da zona (nesse caso, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registro NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um registro PTR

Neste caso, 'minha-zona-arpa.com' representa a zona ARPA que apresenta o intervalo de IP. Cada registro PTR definido nesta zona corresponde a um endereço IP nesse intervalo de IP.  O nome do registro '10' é o último octeto do endereço IP dentro desse intervalo IP representado por esse registro.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Criar um registro SRV

Ao criar um [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique o *\_serviço* e o *\_protocolo* no nome do conjunto de registros. Não é necessário incluir "\@" no nome do conjunto de registros ao criar um conjunto de registros SRV definido no ápice da zona.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Criar um registro TXT

O exemplo a seguir mostra como criar um registro TXT. Para obter mais informações sobre o tamanho máximo suportado pelos registros TXT, consulte [Registros TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `az network dns record-set <record-type> show`. Para obter ajuda, consulte `az network dns record-set <record-type> show --help`.

Assim como acontece ao criar um registro ou conjunto de registros, o nome do conjunto de registros fornecido deve ser um nome *relativo*, significando que ele deve excluir o nome da zona. Você também precisa especificar o tipo de registro, a zona que contém o conjunto de registros e o grupo de recursos que contém a zona.

O exemplo a seguir recupera o registro *www* de tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Listar os conjuntos de registros

Você pode listar todos os registros em uma zona DNS com o comando `az network dns record-set list` . Para obter ajuda, consulte `az network dns record-set list --help`.

Este exemplo retorna todos os conjuntos de registros na zona *contoso.com* e no grupo de recursos *MyResourceGroup*, independentemente do nome ou tipo de registro:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Este exemplo retorna todos os conjuntos de registros correspondentes ao tipo de registro determinado (nesse caso, registros 'A'):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registros existente

Você pode usar `az network dns record-set <record-type> add-record` tanto para criar um registro em um novo conjunto de registros quanto para adicionar um registro a um conjunto de registros existente.

Para obter mais informações, consulte [Criar um registro DNS](#create-a-dns-record) e [Criar registros de outros tipos](#create-records-of-other-types), acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro de um conjunto de registros existente.

Para remover um registro DNS de um conjunto de registros existente, use `az network dns record-set <record-type> remove-record`. Para obter ajuda, consulte `az network dns record-set <record-type> remove-record -h`.

Esse comando exclui um registro DNS de um conjunto de registros. Se o último registro de um conjunto de registros é excluído, o conjunto de registros em si também é excluído. Para manter o conjunto de registros vazio em vez disso, use a opção `--keep-empty-record-set`.

Você precisa especificar o registro a ser excluído e a zona da qual ele deve ser excluído, usando os mesmos parâmetros usados ao criar um registro usando `az network dns record-set <record-type> add-record`. Esses parâmetros são descritos em [Criar um registro DNS](#create-a-dns-record) e [Criar registros de outros tipos](#create-records-of-other-types), acima.

O exemplo a seguir cria um registro A com o valor '1.2.3.4' do conjunto de recursos chamado *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registros existente

Cada conjunto de registros contém um [TTL (vida útil)](dns-zones-records.md#time-to-live), [metadados](dns-zones-records.md#tags-and-metadata) e registros DNS. As seções a seguir explicam como modificar cada uma dessas propriedades.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um registro A, AAAA, CAA, MX, NS, PTR, SRV ou TXT

Para modificar um registro existente do tipo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT, primeiro você deve adicionar um novo registro e, em seguida, excluir o registro existente. Para obter instruções detalhadas sobre como excluir e adicionar registros, consulte as seções anteriores deste artigo.

O exemplo a seguir mostra como modificar um registro 'A', alterando o endereço IP 1.2.3.4 para o endereço IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Não é possível adicionar, remover nem modificar os registros no conjunto de registros NS criado automaticamente no ápice da zona (`--Name "@"`, incluindo as aspas). Para este conjunto de registros, as únicas alterações permitidas são modificar o TTL do conjunto de registros e os metadados.

### <a name="to-modify-a-cname-record"></a>Para modificar um registro CNAME

Ao contrário da maioria dos outros tipos de registro, um conjunto de registros CNAME pode conter apenas um único registro.  Portanto, você não pode substituir o valor atual adicionando um novo registro e removendo o registro existente, como para outros tipos de registro.

Em vez disso, para modificar um registro CNAME, use `az network dns record-set cname set-record`. Para obter ajuda, consulte `az network dns record-set cname set-record --help`

O exemplo modifica o conjunto de registros CNAME *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, para apontar para 'www.fabrikam.net' em vez de seu valor existente:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Ao contrário da maioria dos outros tipos de registro, um conjunto de registros CNAME pode conter apenas um único registro.  Portanto, você não pode substituir o valor atual adicionando um novo registro e removendo o registro existente, como para outros tipos de registro.

Em vez disso, para modificar um registro SOA, use `az network dns record-set soa update`. Para obter ajuda, consulte `az network dns record-set soa update --help`.

O exemplo a seguir mostra como definir a propriedade 'email' do registro SOA para a zona *contoso.com*, no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar registros NS no apex da zona

O registro NS definido no apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nome adicionais a esse conjunto de registros NS para dar suporte à co-hospedagem de domínios com mais de um provedor DNS. Você também pode modificar o TTL e os metadados para esse conjunto de registros. No entanto, você não pode remover nem modificar os servidores de nome DNS do Azure previamente populados.

Observe que isso se aplica somente ao conjunto de registros NS definido no apex da zona. Outros conjuntos de registros NS na sua zona (conforme utilizados para delegar zonas filho) podem ser modificados sem restrição.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao conjunto de registros NS no apex da zona:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.cn
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar o TTL de um conjunto de registros existente

Para modificar o TTL de um conjunto de registros existente, use `azure network dns record-set <record-type> update`. Para obter ajuda, consulte `azure network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um conjunto de registros TTL, nesse caso para 60 segundos:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registros existente

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar os dados específicos do aplicativo com cada conjunto de registros, como pares de chave-valor. Para modificar os metadados de um conjunto de registros existente, use `az network dns record-set <record-type> update`. Para obter ajuda, consulte `az network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um conjunto de registros com duas entradas de metadados, "dept=finance" e "environment=production". Observe que os metadados existentes são *substituídos* pelos valores fornecidos.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o comando `az network dns record-set <record-type> delete`. Para obter ajuda, consulte `azure network dns record-set <record-type> delete --help`. Excluir um conjunto de registros também exclui todos os registros no conjunto de registros.

> [!NOTE]
> Não é possível excluir os conjuntos de registro SOA e NS no ápice da zona (`--name "@"`).  Eles são criados automaticamente quando a zona foi criada e são excluídos automaticamente quando a zona é excluída.

O exemplo a seguir exclui o conjunto de registros chamado *www* de tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Será solicitado que você confirme a operação de exclusão. Para suprimir esse prompt, use a opção `--yes`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [zonas e os registros no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger seus registros e zonas](dns-protect-zones-recordsets.md) ao usar o DNS do Azure.
