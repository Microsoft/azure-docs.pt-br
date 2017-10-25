---
title: Hospedagem de zonas de pesquisa de DNS reverso no Azure DNS | Microsoft Docs
description: Aprenda a usar o DNS do Azure para hospedar as zonas de pesquisa inversas de DNS para seus intervalos de IP
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Hospedagem de zonas de pesquisa de DNS reverso no Azure DNS

Este artigo explica como hospedar as zonas de pesquisa inversas do DNS para seus intervalos de IP atribuídos no DNS do Azure. Os intervalos de IP representados pela zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente por seu ISP.

Para configurar o DNS reverso para o endereço IP de propriedade do Azure atribuído ao serviço do Azure, veja [Configurar a pesquisa inversa para os endereços IP alocados em seu serviço do Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, você deve estar familiarizado com essa [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md).

Este artigo explica as etapas para criar sua primeira zona DNS de pesquisa inversa e registro DNS usando o Portal do Azure, Azure PowerShell, CLI 1.0 do Azure ou CLI 2.0 do Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona DNS de pesquisa inversa

1. Entre no [Portal do Azure](https://portal.azure.com)
1. No menu Hub, clique em **Novo** > **Rede** > e, em seguida, clique em **Zona DNS** para abrir a folha **Criar zona DNS**.

   ![Zona DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. Na folha **Criar zona DNS**, dê um nome para sua zona DNS. O nome da zona é elaborado diferente para prefixos de IPv4 e IPv6. Use as instruções para [IPV4](#ipv4) ou [IPv6](#ipv6) para nomear sua zona. Ao concluir, clique em **Criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa de IPv4 baseia-se no intervalo de IP que ela representa. Deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para obter exemplos, veja [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Ao criar zonas de pesquisa de DNS reverso sem classe no DNS do Azure, você deverá usar um hífen (`-`) em vez de barra invertida ('/') no nome da zona.
>
> Por exemplo, para o intervalo de IP 192.0.2.128/26, você deve usar `128-26.2.0.192.in-addr.arpa` como o nome da zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Isso ocorre porque, embora ambos tenham suporte dos padrões de DNS, os nomes de zona DNS que contêm a barra invertida (`/`) não têm suporte no DNS do Azure.

O exemplo a seguir mostra como criar uma zona DNS reversa 'Classe C' chamada `2.0.192.in-addr.arpa` no DNS do Azure por meio do Portal do Azure:

 ![criar zona DNS](./media/dns-reverse-dns-hosting/figure2.png)

O 'Local de grupo de recursos' define o local do grupo de recursos e não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é 'global' e não é exibido.

Os exemplos a seguir mostram como concluir essa tarefa com o Azure PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI 1.0 do Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa de IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para obter exemplos, veja [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo a seguir mostra como criar uma zona de pesquisa inversa de DNS de IPv6 chamada `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` no DNS do Azure por meio do Portal do Azure:

 ![criar zona DNS](./media/dns-reverse-dns-hosting/figure3.png)

O 'Local de grupo de recursos' define o local do grupo de recursos e não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é 'global' e não é exibido.

Os exemplos a seguir mostram como concluir essa tarefa com o Azure PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar uma zona de pesquisa inversa de DNS

Depois de criar a zona de pesquisa inversa de DNS, você deverá garantir que a zona seja delegada a partir da zona pai. A delegação de DNS permite que o processo de resolução de nome DNS localize os servidores de nomes que hospedam a zona de pesquisa inversa de DNS. Isso permite que os servidores de nomes respondam a consultas inversas de DNS para os endereços IP em seu intervalo de endereços.

Para zonas de pesquisa direta, o processo de delegação de uma zona DNS está descrito em [Delegar seu domínio ao DNS do Azure](dns-delegate-domain-azure-dns.md). A delegação de zonas de pesquisa inversa funciona da mesma maneira. A única diferença é que você precisa configurar os servidores de nome com o provedor que forneceu o intervalo de IP, em vez de seu registrador de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registro PTR DNS

### <a name="ipv4"></a>IPv4

O exemplo a seguir explica o processo de criação de um registro PTR em uma zona DNS reverso no DNS do Azure. Para outros tipos de registro e para modificar os registros existentes, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).

1.  Na parte superior da folha **zona DNS**, selecione **+Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.

 ![Zona DNS](./media/dns-reverse-dns-hosting/figure4.png)

1. Na folha **Adicionar conjunto de registros**. 
1. Selecione **PTR** do menu de registro "**Tipo**".  
1. O nome do registro definido para um registro PTR precisa ser o restante do endereço IPv4 na ordem inversa. Neste exemplo, os três primeiros octetos já estão preenchidos como parte do nome da zona (.2.0.192). Portanto, apenas o último octeto é fornecido no campo de nome. Por exemplo, você poderia nomear seu conjunto de registros "**15**" para um recurso cujo endereço IP é 192.0.2.15.  
1. No campo "**Nome de Domínio**", insira o nome de domínio totalmente qualificado (FQDN) do recurso usando o IP.
1. Selecione **OK** na parte inferior da folha para criar o registro DNS.

 ![adicionar conjunto de registros](./media/dns-reverse-dns-hosting/figure5.png)

Os exemplos a seguir mostram como concluir essa tarefa com o PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

O exemplo a seguir explica o processo de criação de um novo registro 'PTR'. Para outros tipos de registro e para modificar os registros existentes, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).

1. Na parte superior da folha **zona DNS**, selecione **+Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.

  ![folha de zona DNS](./media/dns-reverse-dns-hosting/figure6.png)

2. Na folha **Adicionar conjunto de registros**. 
3. Selecione **PTR** do menu de registro "**Tipo**".  
4. O nome do registro definido para um registro PTR precisa ser o restante do endereço IPv6 na ordem inversa. Ele não deve incluir compactação. Neste exemplo, os primeiros 64 bits do IPv6 já estão preenchidos como parte do nome da zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Portanto, apenas os últimos 64 bits são fornecidos no campo de nome. Os últimos 64 bits do endereço IP são inseridos na ordem inversa, usando um período como o delimitador entre cada número hexadecimal. Por exemplo, você poderia nomear seu conjunto de registros "**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**" para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
5. No campo "**Nome de Domínio**", insira o nome de domínio totalmente qualificado (FQDN) do recurso usando o IP.
6. Selecione **OK** na parte inferior da folha para criar o registro DNS.

![adicionar conjunto de registros](./media/dns-reverse-dns-hosting/figure7.png)

Os exemplos a seguir mostram como concluir essa tarefa com o PowerShell e a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Exibir registros

Para exibir os registros que você criou, navegue até sua zona DNS no Portal do Azure. Na parte inferior da folha **Zona DNS**, é possível ver os registros da zona DNS. Você deve ver os registros NS e SOA padrão, que são criados em cada zona, além de quaisquer registros novos que você criou.

### <a name="ipv4"></a>IPv4

Folha de zona DNS, mostrando os registros PTR de IPv4:

![folha de zona DNS](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos a seguir mostram como exibir os registros PTR usando o PowerShell ou a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI 1.0 do Azure

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Folha de zona DNS, mostrando os registros PTR de IPv6:

![folha de zona DNS](./media/dns-reverse-dns-hosting/figure9.png)

Os exemplos a seguir mostram como exibir os registros usando o PowerShell ou a CLI do Azure:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI 1.0 do Azure

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para meus blocos IP atribuídos pelo ISP no DNS do Azure?

Sim. A hospedagem das zonas de pesquisa inversa (ARPA) para seus próprios intervalos de IP no DNS do Azure tem suporte total.

Crie a zona de pesquisa inversa no DNS do Azure conforme explicado neste artigo e trabalhe com seu provedor de serviços de Internet para [delegar a zona](dns-domain-delegation.md).  Em seguida, você pode gerenciar os registros PTR para cada pesquisa inversa da mesma forma que outros tipos de registro.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto custa hospedar minha zona de pesquisa inverso de DNS?

A hospedagem da zona de pesquisa inversa de DNS para o bloco IP atribuído pelo ISP no DNS do Azure é cobrado usando [taxas padrão do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para endereços IPv4 e IPv6 no DNS do Azure?

Sim. Este artigo explica como criar zonas de pesquisa inversa de DNS de IPv4 e IPv6 no DNS do Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Posso importar uma zona de pesquisa inversa de DNS existente?

Sim. Você pode usar a CLI do Azure para importar as zonas DNS existentes para o DNS do Azure. Isso funciona para zonas de pesquisa direta e zonas de pesquisa inversa.

Para saber mais, veja [Importar e exportar um arquivo de zona DNS usando a CLI do Azure](dns-import-export.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre DNS reverso, confira [Pesquisa de DNS reverso na Wikipédia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerenciar registros DNS reversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).
