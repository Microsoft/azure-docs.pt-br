---
title: Gerenciar registros DNS na DNS do Azure usando o Azure PowerShell | Microsoft Docs
description: Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registros e registros.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011584"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Gerenciar registros e conjuntos de registros DNS no DNS do Azure usando o Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI clássica do Azure](./dns-operations-recordsets-cli.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar os registros de DNS para sua zona de DNS usando o Azure PowerShell. Os registros DNS também podem ser gerenciados usando a [CLI do Azure](dns-operations-recordsets-cli.md) da plataforma cruzada ou o [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo pressupõem que você já [instalou o Azure PowerShell, conectou e criou uma zona DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introdução

Antes de criar registros DNS no DNS do Azure, primeiro você precisa entender como o DNS do Azure organiza registros DNS em conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registros DNS no DNS do Azure, confira [Zonas e registros DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Criar um novo registro DNS

Se o novo registro tem o mesmo nome e tipo de um registro existente, você precisa [adicioná-lo ao conjunto de registros existente](#add-a-record-to-an-existing-record-set). Se o novo registro tiver um nome e tipo diferentes para todos os registros existentes, você precisará criar um novo conjunto de registros. 

### <a name="create-a-records-in-a-new-record-set"></a>Criar registros 'A' em um novo conjunto de registros

Você cria conjuntos de registros usando o cmdlet `New-AzDnsRecordSet`. Ao criar um conjunto de registros, você precisa especificar o nome do conjunto de registros, a zona, o TTL (vida útil), o tipo de registro e os registros a serem criados.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo "A", você precisa especificar o endereço IP usando o parâmetro `-IPv4Address`. Outros parâmetros são usados para outros tipos de registro. Consulte Exemplos adicionais dos tipos de registro para obter detalhes.

O exemplo a seguir cria um conjunto de registros com o nome relativo "www" na Zona DNS "contoso.com". O nome totalmente qualificado do conjunto de registros é "www.contoso.com". O tipo de registro é 'A' e o TTL é 3600 segundos. O conjunto de registros contém um único registro, com o endereço IP "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para criar um conjunto de registros no "ápice" de uma zona (neste caso, "contoso.com"), use o nome do conjunto de registros "\@" (excluindo as aspas):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Se você precisar criar um conjunto de registros contendo mais de um registro, primeiro deve criar uma matriz local e adicionar os registros e, então, passar a matriz para `New-AzDnsRecordSet` como a seguir:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar os dados específicos do aplicativo com cada conjunto de registros, como pares de chave-valor. O exemplo a seguir mostra como criar um conjunto de registros com duas entradas de metadados, "dept=finance" e "environment=production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

O DNS do Azure também oferece suporte a conjuntos de registros 'vazios', que podem agir como um espaço reservado para reservar um nome DNS antes de criar os registros DNS. Os conjuntos de registros vazios são visíveis no painel de controle do DNS do Azure, mas aparecem nos servidores de nome DNS do Azure. O exemplo a seguir cria um conjunto de registros vazio:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Criar outros tipos de registro

Depois de ter visto detalhadamente como criar os registros 'A', os exemplos a seguir mostram como criar outros tipos de registro suportados pelo DNS do Azure.

Em cada caso, mostraremos como criar um conjunto de registro que contém um único registro. Os exemplos anteriores dos registros 'A' podem ser adaptados para criar outros tipos de conjuntos de registros que contêm vários registros, com metadados ou criar conjuntos de registros vazios.

Não podemos dar um exemplo para criar um conjunto de registros SOA, pois os SOAs são criados e excluídos com cada zona DNS e não podem ser criados ou excluídos separadamente. No entanto, [o SOA pode ser modificado, como mostrado no exemplo mais adiante](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registros AAAA com um registro único

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Criar um conjunto de registros CAA com um único registro

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registros CNAME com um registro único

> [!NOTE]
> Os padrões do DNS não permitem registros CNAME no ápice de uma zona (`-Name '@'`), nem permitem conjuntos de registros que contêm mais de um registro.
> 
> Para obter mais informações, consulte [Registros CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registros MX com um registro único

Neste exemplo, usamos o nome do conjunto de registros "\@" para criar um registro MX no ápice da zona (nesse caso, "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registros NS com um registro único

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registros PTR com um único registro

Neste caso, 'my-arpa-zone.com' representa a zona de pesquisa inversa ARPA que apresenta o intervalo de IPs. Cada registro PTR definido nesta zona corresponde a um endereço IP nesse intervalo de IP. O nome do registro '10' é o último octeto do endereço IP dentro desse intervalo IP representado por esse registro.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registros SRV com um registro único

Ao criar um [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique o *\_serviço* e o *\_protocolo* no nome do conjunto de registros. Não é necessário incluir "\@" no nome do conjunto de registros ao criar um conjunto de registros SRV definido no ápice da zona.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Criar um conjunto de registros TXT com um registro único

O exemplo a seguir mostra como criar um registro TXT. Para obter mais informações sobre o tamanho máximo suportado pelos registros TXT, consulte [Registros TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `Get-AzDnsRecordSet`. Esse cmdlet retorna um objeto local que representa o conjunto de registros no DNS do Azure.

Assim como acontece com `New-AzDnsRecordSet`, o nome do conjunto de registros fornecido deve ser um nome *relativo*, significando que ele deve excluir o nome da zona. Você também precisa especificar o tipo de registro e a zona que contém o conjunto de registros.

O exemplo a seguir mostra como recuperar um conjunto de registros. Neste exemplo, a zona é especificada usando os parâmetros `-ZoneName` e `-ResourceGroupName`.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, você também pode especificar a zona usando um objeto de zona, passado usando o parâmetro `-Zone`.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Listar os conjuntos de registros

Você também pode usar `Get-AzDnsZone` para listar os conjuntos de registros em uma zona, omitindo os parâmetros `-Name` e/ou `-RecordType`.

O exemplo a seguir retorna todos os conjuntos de registro na zona:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

O exemplo a seguir mostra como todos os conjuntos de registro de um determinado tipo podem ser recuperados especificando o tipo de registro ao omitir o nome do conjunto de registros:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para recuperar todos os conjuntos de registro com um determinado nome, em todos os tipos de registro, você precisa recuperar todos os conjuntos de registros, em seguida, filtrar os resultados:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Em todos os exemplos acima, a zona pode ser especificada usando os parâmetros `-ZoneName` e `-ResourceGroupName` (como mostrado) ou especificando um objeto da zona:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registros existente

Para adicionar um registro a um conjunto de registros existente, siga estas três etapas:

1. Obter o conjunto de registros existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo registro ao conjunto de registros local. Esta é uma operação offline.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a mudança no serviço DNS do Azure. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Usar `Set-AzDnsRecordSet` *substitui* o conjunto de registros existente no DNS do Azure (e todos os registros que ele contém) pelo conjunto de registros especificado. As [verificações de Etag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar o argumento `-Overwrite` opcional para omitir essas verificações.

Esta sequência de operações também pode ser *redirecionada*, significando que você passa o objeto do conjunto de registros usando a barra vertical, em vez de passá-lo como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Os exemplos acima mostram como adicionar um registro 'A' a um conjunto de registros existente do tipo 'A'. Uma sequência de operações parecida é usada para adicionar registros a outros tipos de conjuntos de registro, substituindo o parâmetro `-Ipv4Address` de `Add-AzDnsRecordConfig` por outros parâmetros específicos para cada tipo de registro. Os parâmetros para cada tipo de registro são iguais para o cmdlet `New-AzDnsRecordConfig`, como mostrado em Exemplos adicionais dos tipos de registro acima.

Os conjuntos de registro do tipo 'CNAME' ou 'SOA' não podem conter mais de um registro. Essa restrição resulta dos padrões do DNS. Não é uma limitação do DNS do Azure.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro em um conjunto de registros existente

O processo para remover um registro de um conjunto de registros é semelhante ao processo para adicionar um registro a um conjunto de registros existente:

1. Obter o conjunto de registros existente

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Remova o registro do objeto do conjunto de registros local. Esta é uma operação offline. O registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a mudança no serviço DNS do Azure. Use o argumento opcional `-Overwrite` para omitir as [verificações de Etag](dns-zones-records.md#etags) das alterações simultâneas.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Usar a sequência acima para remover o último registro de um conjunto de registros não exclui o conjunto de registros, ao contrário, deixa um conjunto de registros vazio. Para remover totalmente um conjunto de registros, consulte [Excluir um conjunto de registros](#delete-a-record-set).

Da mesma forma para adicionar registros a um conjunto de registros, a sequência de operações para remover um conjunto de registros também pode ser redirecionada:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Diferentes tipos de registro são suportados passando os parâmetros específicos do tipo apropriados para `Remove-AzDnsRecordSet`. Os parâmetros para cada tipo de registro são iguais para o cmdlet `New-AzDnsRecordConfig`, como mostrado em Exemplos adicionais dos tipos de registro acima.


## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registros existente

As etapas para modificar um conjunto de registros existente são semelhantes às etapas realizadas ao adicionar ou remover os registros de um conjunto de registros:

1. Recupere o conjunto de registros existente usando `Get-AzDnsRecordSet`.
2. Modifique o objeto do conjunto de registros local:
    * adicionando ou removendo os registros
    * alterando os parâmetros dos registros existentes
    * alterando os metadados do conjunto de registros e a vida útil (TTL)
3. Confirme as alterações usando o cmdlet `Set-AzDnsRecordSet` . Isso *substitui* o conjunto de registros existente no DNS do Azure pelo conjunto de registros especificado.

Ao usar `Set-AzDnsRecordSet`, as [verificações de Etag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar o argumento `-Overwrite` opcional para omitir essas verificações.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registro em um conjunto de registros existente

Neste exemplo, alteramos o endereço IP de um registro "A" existente:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Não é possível adicionar nem remover registros do conjunto de registros SOA criado automaticamente no ápice da zona (`-Name "@"`, incluindo as aspas). No entanto, é possível modificar qualquer um dos parâmetros no registro SOA (exceto o “Host”) e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade *Email* do registro SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar registros NS no apex da zona

O registro NS definido no apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nome adicionais a esse conjunto de registros NS para dar suporte à co-hospedagem de domínios com mais de um provedor DNS. Você também pode modificar o TTL e os metadados para esse conjunto de registros. No entanto, você não pode remover nem modificar os servidores de nome DNS do Azure previamente populados.

Observe que isso se aplica somente ao conjunto de registros NS definido no apex da zona. Outros conjuntos de registros NS na sua zona (conforme utilizados para delegar zonas filho) podem ser modificados sem restrição.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao conjunto de registros NS no apex da zona:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar os metadados do conjunto de registros

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar os dados específicos do aplicativo com cada conjunto de registros, como pares de chave-valor.

O exemplo a seguir mostra como modificar os metadados de um conjunto de registros existente:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o cmdlet `Remove-AzDnsRecordSet` . Excluir um conjunto de registros também exclui todos os registros no conjunto de registros.

> [!NOTE]
> Não é possível excluir os conjuntos de registro SOA e NS no ápice da zona (`-Name '@'`).  O DNS do Azure foi criado automaticamente quando a zona foi criada e é excluído automaticamente quando a zona é excluída.

O exemplo a seguir mostra como excluir um conjunto de registros. Neste exemplo, o nome do conjunto de registros, tipo, nome da zona e grupo de recursos são especificados explicitamente.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, o conjunto de registros pode ser especificado pelo nome e tipo, e a zona é especificada usando um objeto:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como uma terceira opção, o próprio conjunto de registros pode ser especificado usando um objeto do conjunto de registros:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Quando você especifica o conjunto de registros a ser excluído usando um objeto do conjunto de registros,as [verificações de Etag](dns-zones-records.md#etags) são usadas para garantir que as alterações simultâneas não sejam excluídas. Você pode usar o argumento `-Overwrite` opcional para omitir essas verificações.

O objeto do conjunto de registros também pode ser redirecionado em vez de ser passado como um parâmetro:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Prompts de confirmação

Os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` oferecem suporte aos prompts de confirmação.

Cada cmdlet solicita confirmação caso a variável de preferência do PowerShell `$ConfirmPreference` tenha um valor `Medium` ou inferior. Como o valor padrão para `$ConfirmPreference` é `High`, esses prompts não são fornecidos ao usar as configurações padrão do PowerShell.

Você pode substituir a configuração `$ConfirmPreference` atual usando o parâmetro `-Confirm`. Se você especificar `-Confirm` ou `-Confirm:$True`, o cmdlet solicitará uma confirmação antes de executar. Se você especificar `-Confirm:$False`, o cmdlet não solicitará uma confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, consulte [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [zonas e os registros no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger seus registros e zonas](dns-protect-zones-recordsets.md) ao usar o DNS do Azure.
<br>
Examine a [documentação de referência do PowerShell do DNS do Azure](/powershell/module/az.dns).