---
title: Usando o DNS dinâmico para registrar os nomes do host no Azure | Microsoft Docs
description: Saiba como configurar o DNS dinâmico para registrar os nomes do host em seus próprios servidores DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640371"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Usar o DNS dinâmico para registrar os nomes do host em seu próprio servidor DNS

O [Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para VMs (máquinas virtuais) e instâncias de função. Quando a resolução de nomes precisar ir além dos recursos fornecidos pelo DNS padrão do Azure, você pode fornecer seus próprios servidores DNS. Usar seus próprios servidores DNS permite que você personalize sua solução de DNS para ajustá-la às suas próprias necessidades específicas. Por exemplo, você poderá precisar acessar recursos locais por meio do controlador de domínio Active Directory.

Quando os servidores DNS personalizados forem hospedados como VMs do Azure, você poderá encaminhar consultas de nome do host para a mesma rede virtual do Azure para resolver nomes de host. Se não desejar usar essa opção, você poderá registrar os nomes do host da VM no servidor DNS usando o DDNS (DNS dinâmico). O Azure não tem as credenciais para criar diretamente registros nos servidores DNS, de modo que disposições alternativas muitas vezes são necessárias. Veja a seguir alguns cenários comuns, com alternativas:

## <a name="windows-clients"></a>Clientes do Windows
Clientes do Windows não ingressados no domínio tentam atualizar DDNS não seguros quando eles são inicializados ou quando seu endereço IP é alterado. O nome DNS é o nome do host mais o sufixo DNS primário. O Azure deixa o sufixo DNS primário em branco, mas você pode defini-lo na VM, por meio da [interface do usuário](https://technet.microsoft.com/library/cc794784.aspx) ou do [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Os clientes do Windows ingressados no domínio registram seus endereços IP com o controlador de domínio usando DDNS seguro. O processo de ingresso no domínio define o sufixo DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux
Clientes Linux geralmente não se registram no servidor DNS na inicialização, eles supõem que o servidor DHCP faça isso. Os servidores DHCP do Azure não têm as credenciais para manter os registros no seu servidor DNS. Use uma ferramenta chamada `nsupdate`, que está incluída no pacote de Associação, para enviar atualizações do DDNS. Como o protocolo DDNS é padronizado, você pode usar `nsupdate` mesmo quando não estiver usando a Associação no servidor DNS.

Você pode usar os ganchos que são fornecidos pelo cliente DHCP para criar e manter a entrada do nome do host no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts em */etc/dhcp/dhclient-exit-hooks.d/*. Você pode usar os ganchos para registrar o novo endereço IP usando `nsupdate`. Por exemplo: 

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Você também pode usar o comando `nsupdate` para executar atualizações de DDNS seguras. Por exemplo, quando você estiver usando um servidor DNS de associação, um par de chaves públicas-privadas será [gerado](http://linux.yyz.us/nsupdate/). O servidor DNS é [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte pública da chave para que ele possa verificar a assinatura da solicitação. Para fornecer o par de chaves para `nsupdate`, use a opção `-k` para a solicitação de atualização de DDNS ser assinada.

Quando estiver usando um servidor DNS do Windows, você poderá usar a autenticação Kerberos com o parâmetro `-g` em `nsupdate`, mas não está disponível na versão do Windows de `nsupdate`. Para usar o Kerberos, use `kinit` para carregar as credenciais. Por exemplo, você pode carregar as credenciais de um [arquivo keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), em seguida, `nsupdate -g` coleta as credenciais, do cache.

Se necessário, você pode adicionar um sufixo de pesquisa DNS em suas VMs. O sufixo DNS é especificado no arquivo */etc/resolv.conf* . A maioria das distribuições de Linux gerencia automaticamente o conteúdo desse arquivo, então, normalmente você não pode editá-lo. No entanto, você pode substituir o sufixo usando o comando `supersede` do cliente DHCP. Para substituir o sufixo, adicione a linha a seguir ao arquivo */etc/dhcp/dhclient.conf*:

```
supersede domain-name <required-dns-suffix>;
```
