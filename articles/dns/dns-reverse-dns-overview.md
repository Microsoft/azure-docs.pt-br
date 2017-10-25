---
title: "Visão geral do DNS reverso no Azure | Microsoft Docs"
description: Saiba como funciona o DNS reverso e como ele pode ser usado no Azure
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
ms.openlocfilehash: 70a1ad070e812951fca3d2b19da12c67f0725dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Visão geral de DNS reverso e suporte no Azure

Este artigo fornece uma visão geral de como funciona o DNS reverso e os cenários de DNS reverso com suporte no Azure.

## <a name="what-is-reverse-dns"></a>O que é DNS reverso?

Os registros convencionais de DNS permitem um mapeamento de um nome DNS (por exemplo, 'www.contoso.com’) para um endereço IP (como 64.4.6.100).  O DNS reverso permite a conversão de um endereço IP (64.4.6.100) novamente para um nome ('www.contoso.com').

Registros DNS reversos são usados em uma variedade de situações. Por exemplo, registros DNS reversos são amplamente usados no combate ao spam de email ao verificarem o remetente de uma mensagem de email.  O servidor de recebimento de email recupera o registro de DNS reverso do endereço IP do servidor de envio e verifica se o host está autorizado a enviar email do domínio de origem. 

## <a name="how-reverse-dns-works"></a>Como funciona o DNS reverso

Os registros do DNS reverso são hospedados em zonas DNS especiais, conhecidas como zonas 'ARPA'.  Essas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal de hospedagem de domínios como ‘contoso.com’.

Por exemplo, o registro DNS 'www.contoso.com' é implementado usando um registro DNS 'A' com o nome ‘www’ na zona ‘contoso.com’.  Este registro A aponta para o endereço IP correspondente, neste caso, 64.4.6.100.  A pesquisa inversa é implementada separadamente, usando um registro 'PTR' chamado '100' na zona '6.4.64.in-addr.arpa' (observe que os endereços IP são revertidos em zonas ARPA).  Esse registro PTR, caso tenha sido configurado corretamente, aponta para o nome ‘www.contoso.com’.

Quando uma organização recebe um bloco de endereços IP, também adquirem o direito de gerenciar a zona ARPA correspondente. As zonas ARPA correspondentes aos blocos de endereços IP usados pelo Azure são hospedadas e gerenciadas pela Microsoft. Seu ISP pode hospedar a zona ARPA para seus próprios endereços IP para você, ou pode permitir que você hospede a zona ARPA em um serviço DNS de sua escolha, como o Azure DNS.

> [!NOTE]
> As pesquisas DNS diretas e as pesquisas DNS inversas são implementadas em hierarquias de DNS separadas e paralelas. A pesquisa inversa para 'www.contoso.com' **não** é hospedada na zona ‘contoso.com’, mas na região ARPA para o bloco de endereços IP correspondente. Zonas separadas são usadas para blocos de endereço IPv4 e IPv6.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa de IPv4 deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Por exemplo, ao criar uma zona inversa para hospedar registros de hosts com IPs que estão no prefixo 192.0.2.0/24, o nome da zona seria criado isolando o prefixo de rede do endereço (192.0.2) e, em seguida, invertendo a ordem (2.0.192) e adicionando o sufixo `.in-addr.arpa`.

|Classe de sub-rede|Prefixo de rede  |Prefixo de rede invertido  |Sufixo padrão  |Nome da zona inversa |
|-------|----------------|------------|-----------------|---------------------------|
|Classe A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Classe B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Classe C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegação de IPv4 sem classe

Em alguns casos, o intervalo de IP alocado a uma organização é menor do que o intervalo de uma Classe C (/24). Nesse caso, o intervalo de IP não está em um limite de zona dentro da hierarquia de zonas `.in-addr.arpa` e, portanto, não pode ser delegado como uma zona filho.

Em vez disso, um mecanismo diferente é usado para transferir o controle de registros individuais de pesquisa inversa (PTR) a uma zona DNS dedicada. Esse mecanismo delega uma zona filho para cada intervalo de IP, depois, mapeia cada endereço IP no intervalo individualmente para essa zona filho usando registros CNAME.

Por exemplo, suponha que uma organização receba o intervalo de IP 192.0.2.128/26 de seu ISP. Isso representa 64 endereços IP, de 192.0.2.128 a 192.0.2.191. O DNS reverso para esse intervalo é implementado da seguinte maneira:
- A organização cria uma zona de pesquisa inversa chamada 128-26.2.0.192.in-addr.arpa. O prefixo '128-26' representa o segmento de rede atribuído à organização dentro do intervalo da Classe C (/24).
- O ISP cria os registros NS para configurar a delegação de DNS para a zona acima a partir da zona pai da Classe C. Ele também cria registros CNAME na zona de pesquisa inversa pai (Classe C), mapeando cada endereço IP no intervalo de IP para a nova zona criada pela organização:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Em seguida, a organização gerencia os registros PTR individuais dentro de sua zona filho.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Uma pesquisa inversa para o endereço Ip '192.0.2.129' consulta um registro PTR chamado '129.2.0.192.in-addr.arpa'. Essa consulta é resolvida por meio do CNAME na zona pai para o registro PTR na zona filho.

### <a name="ipv6"></a>IPv6

O nome de uma zona de pesquisa inversa de IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`

Por exemplo, Ao criar uma zona inversa para hospedar registros de hosts com IPs que estão no prefixo 2001:db8:1000:abdc::/64, o nome da zona seria criado isolando o prefixo de rede do endereço (2001:db8:abdc::). Em seguida, expanda o prefixo de rede IPv6 para remover [compactação zero](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), se ela tiver sido usada para reduzir o prefixo de endereço IPv6 (2001:0db8:abdc:0000::). Inverta a ordem usando um período como o delimitador entre cada número hexadecimal no prefixo, a fim de compilar o prefixo de rede invertido (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) e adicionar o sufixo `.ip6.arpa`.


|Prefixo de rede  |Prefixo de rede expandido e invertido |Sufixo padrão |Nome da zona inversa  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Suporte do Azure para DNS reverso

O Azure dá suporte a dois cenários separados relacionados ao DNS reverso:

**Hospedagem da zona de pesquisa inversa correspondente ao seu bloco de endereços IP.**
O Azure DNS pode ser usado para [hospedar suas zonas de pesquisa inversa e gerenciar os registros PTR para cada pesquisa de DNS reverso](dns-reverse-dns-hosting.md) para IPv4 e IPv6.  O processo de criação da zona de pesquisa inversa (ARPA), de configuração da delegação e de configuração de registros PTR é o mesmo para as zonas DNS regulares.  As únicas diferenças são que a delegação deve ser configurada por meio de seu ISP em vez de um registrador de DNS, e somente o tipo de registro PTR deve ser usado.

**Configure o registro DNS reverso para o endereço IP atribuído ao seu serviço do Azure.** O Azure permite que você [configure a pesquisa inversa para os endereços IP alocados para seu serviço do Azure](dns-reverse-dns-for-azure-services.md).  Essa pesquisa inversa é configurada pelo Azure como um registro PTR na zona ARPA correspondente.  Essas zonas ARPA, correspondentes a todos os intervalos IP usados pelo Azure, são hospedadas pela Microsoft

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre DNS reverso, confira [Pesquisa de DNS reverso na Wikipédia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [hospedar a zona de pesquisa inversa para o intervalo de IP atribuído pelo ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).
<br>
Saiba como [gerenciar registros DNS reversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).

