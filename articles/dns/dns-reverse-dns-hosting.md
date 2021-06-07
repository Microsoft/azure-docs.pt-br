---
title: Hospedar zonas de pesquisa de DNS reverso no DNS do Azure
description: Aprenda a usar o DNS do Azure para hospedar as zonas de pesquisa inversas de DNS para seus intervalos de IP
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525120"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hospedar zonas de pesquisa de DNS reverso no DNS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como hospedar as zonas de pesquisa inversas do DNS para seus intervalos de IP atribuídos no DNS do Azure. Os intervalos de IP representados pela zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente por seu ISP.

Para configurar DNS reverso para um endereço IP de propriedade do Azure que seja atribuído ao seu serviço do Azure, consulte [Configurar DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, você deve estar familiarizado com a [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md).

Este artigo explica as etapas para criar sua primeira zona DNS de pesquisa inversa e registro DNS usando o portal do Azure, Azure PowerShell, CLI clássica do Azure ou CLI do Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona DNS de pesquisa inversa

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu **Hub**, selecione **Nova** > **Rede** e, em seguida, selecione **Zona DNS**.

   ![Seleção de "Zona DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. No painel **Criar zona DNS**, dê um nome para sua zona DNS. O nome da zona é elaborado diferente para prefixos de IPv4 e IPv6. Use as instruções para [IPv4](#ipv4) ou [IPv6](#ipv6) para nomear sua zona. Quando terminar, selecione **Criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa de IPv4 baseia-se no intervalo de IP que ela representa. Deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para obter exemplos, veja [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Ao criar zonas de pesquisa de DNS reverso sem classe no DNS do Azure, você deverá usar um hífen (`-`) em vez de barra invertida (`/`) no nome da zona.
>
> Por exemplo, para o intervalo de IP 192.0.2.128/26, você deve usar `128-26.2.0.192.in-addr.arpa` como o nome da zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Embora os padrões de DNS ofereçam suporte aos dois métodos, o DNS do Azure não dá suporte a nomes de zona DNS que contêm o caractere de barra ( `/` ).

O exemplo a seguir mostra como criar uma zona DNS reverso de Classe C chamada `2.0.192.in-addr.arpa` no DNS do Azure por meio do Portal do Azure:

 ![Captura de tela que mostra como criar uma zona DNS reversa de classe C chamada 2.0.192.in-addr. arpa no DNS do Azure por meio do portal do Azure.](./media/dns-reverse-dns-hosting/figure2.png)

O **Local do grupo de recursos** define o local do grupo de recursos. Ele não tem impacto sobre a zona DNS. O local da zona DNS sempre é “global” e não é exibido.

Os exemplos a seguir mostram como concluir essa tarefa usando o Azure PowerShell e a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa de IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para obter exemplos, veja [Visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo a seguir mostra como criar uma zona de pesquisa inversa de DNS de IPv6 chamada `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` no DNS do Azure por meio do Portal do Azure:

 ![Painel "Criar zona DNS", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure3.png)

O **Local do grupo de recursos** define o local do grupo de recursos. Ele não tem impacto sobre a zona DNS. O local da zona DNS sempre é “global” e não é exibido.

Os exemplos a seguir mostram como concluir essa tarefa usando o Azure PowerShell e a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar uma zona de pesquisa inversa de DNS

Depois de criar a zona de pesquisa inversa de DNS, você deverá garantir que a zona seja delegada a partir da zona pai. A delegação de DNS permite que o processo de resolução de nome DNS localize os servidores de nomes que hospedam a zona de pesquisa inversa de DNS. Esses servidores de nomes podem, então, responder às consultas inversas de DNS para os endereços IP em seu intervalo de endereços.

Para zonas de pesquisa direta, o processo de delegação de uma zona DNS está descrito em [Delegar seu domínio ao DNS do Azure](dns-delegate-domain-azure-dns.md). A delegação de zonas de pesquisa inversa funciona da mesma maneira. A única diferença é que você precisa configurar os servidores de nome com o provedor que forneceu o intervalo de IP, em vez de seu registrador de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registro PTR DNS

### <a name="ipv4"></a>IPv4

O exemplo a seguir explica o processo de criação de um registro PTR em uma zona DNS reverso no DNS do Azure. Para outros tipos de registro e para modificar os registros existentes, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).

1. Na parte superior do painel **zona DNS**, selecione **+Conjunto de registros** para abrir o painel **Adicionar conjunto de registros**.

   ![Captura de tela do painel zona DNS com uma seta apontando para o botão + conjunto de registros.](./media/dns-reverse-dns-hosting/figure4.png)

1. O nome do registro definido para um registro PTR precisa ser o restante do endereço IPv4 na ordem inversa. 

   Neste exemplo, os três primeiros octetos já estão preenchidos como parte do nome da zona (.2.0.192). Portanto, apenas o último octeto é fornecido na caixa **Nome**. Por exemplo, você poderia nomear seu conjunto de registros **15** para um recurso cujo endereço IP é 192.0.2.15.  
1. Para **Tipo**, selecione **PTR**.  
1. Para **NOME DE DOMÍNIO**, insira o nome de domínio totalmente qualificado (FQDN) do recurso usando o IP.
1. Selecione **OK** na parte inferior do painel para criar o registro DNS.

   ![Painel “Adicionar conjunto de registros”, com caixas preenchidas](./media/dns-reverse-dns-hosting/figure5.png)

Os exemplos a seguir mostram como concluir essa tarefa usando o Azure PowerShell ou a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

O exemplo a seguir explica o processo de criação de um novo registro PTR. Para outros tipos de registro e para modificar os registros existentes, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).

1. Na parte superior do painel **zona DNS**, selecione **+Conjunto de registros** para abrir o painel **Adicionar conjunto de registros**.

   ![Botão para criar um conjunto de registros](./media/dns-reverse-dns-hosting/figure6.png)

2. O nome do registro definido para um registro PTR precisa ser o restante do endereço IPv6 na ordem inversa. Ele não deve incluir compactação. 

   Neste exemplo, os primeiros 64 bits do IPv6 já estão preenchidos como parte do nome da zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Portanto, apenas os últimos 64 bits são fornecidos na caixa **Nome**. Os últimos 64 bits do endereço IP são inseridos na ordem inversa, com um período como o delimitador entre cada número hexadecimal. Por exemplo, você poderia nomear seu conjunto de registros **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Para **Tipo**, selecione **PTR**.  
4. Para **NOME DE DOMÍNIO**, insira o FQDN do recurso usando o IP.
5. Selecione **OK** na parte inferior do painel para criar o registro DNS.

![Captura de tela que mostra o painel "Adicionar conjunto de registros" com uma seta apontando para o valor no campo tipo.](./media/dns-reverse-dns-hosting/figure7.png)

Os exemplos a seguir mostram como concluir essa tarefa usando o Azure PowerShell ou a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Exibir registros

Para exibir os registros que você criou, navegue até sua zona DNS no Portal do Azure. Na parte inferior do painel **Zona DNS**, é possível ver os registros da zona DNS. Você deve ver registros SOA e NS padrão, além de qualquer registro novo que você tenha criado. Os registros NS e SOA são criados em cada região. 

### <a name="ipv4"></a>IPv4

O painel **Zona DNS** mostra os registros de PTR IPv4:

![O painel "Zona DNS" com registros de IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos a seguir mostram como exibir os registros PTR usando o PowerShell ou a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O painel **Zona DNS** mostra os registros de PTR IPv6:

![O painel "Zona DNS" com registros de IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Os exemplos a seguir mostram como exibir os registros usando o PowerShell ou a CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Perguntas frequentes

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para meus blocos IP atribuídos pelo ISP no DNS do Azure?

Sim. A hospedagem das zonas de pesquisa inversa (ARPA) para seus próprios intervalos de IP no DNS do Azure tem suporte total.

Crie a zona de pesquisa inversa no DNS do Azure conforme explicado neste artigo e trabalhe com seu provedor de serviços de Internet para [delegar a zona](dns-domain-delegation.md). Em seguida, você pode gerenciar os registros PTR para cada pesquisa inversa da mesma forma que outros tipos de registro.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto custa hospedar minha zona de pesquisa inverso de DNS?

A hospedagem da zona de pesquisa inversa de DNS para o bloco IP atribuído pelo ISP no DNS do Azure é cobrado usando [taxas padrão do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para endereços IPv4 e IPv6 no DNS do Azure?

Sim. Este artigo explica como criar zonas de pesquisa inversa de DNS de IPv4 e IPv6 no DNS do Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Posso importar uma zona de pesquisa inversa de DNS existente?

Sim. Você pode usar a CLI do Azure para importar as zonas DNS existentes para o DNS do Azure. Esse método funciona para zonas de pesquisa direta e zonas de pesquisa inversa.

Para saber mais, veja [Importar e exportar um arquivo de zona DNS usando a CLI do Azure](dns-import-export.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre DNS reverso, confira [Pesquisa de DNS reverso na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerenciar registros DNS reversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).
