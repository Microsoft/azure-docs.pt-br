---
title: "Usando o DNS Dinâmico para registrar nomes de host"
description: "Esta página fornece detalhes sobre como configurar o DNS dinâmico para registrar os nomes de host em seus próprios servidores DNS."
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 440a062e5fff73526b2d77d7d0a7c52ca72a66f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Usando o DNS dinâmico para registrar os nomes de host em seu próprio servidor DNS
[Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para VMs (máquinas virtuais) e instâncias de função. Contudo, quando a resolução de nome precisar ir além do que é fornecido pelo Azure, você pode fornecer seus próprios servidores DNS. Isso permite que você personalize sua solução DNS para ajustá-la às suas próprias necessidades específicas. Por exemplo, você poderá precisar acessar recursos locais por meio do controlador de domínio Active Directory.

Quando os servidores DNS personalizados forem hospedados como VMs do Azure, você poderá encaminhar consultas de nome de host para a mesma rede virtual do Azure para resolver nomes de host. Se você não desejar usar essa rota, você poderá registrar os nomes de host da VM no servidor DNS usando o DNS Dinâmico.  O Azure não tem a capacidade (por exemplo, credenciais) para criar diretamente registros nos servidores DNS, de modo que organizações alternativas são muitas vezes necessárias. Aqui estão alguns cenários comuns com alternativas.

## <a name="windows-clients"></a>Clientes do Windows
Clientes do Windows não unidos por domínio tentam atualizações não seguras de DNS dinâmico (DDNS) quando eles são inicializados ou quando seu endereço IP for alterado. O nome DNS é o nome do host mais o sufixo DNS primário. O Azure deixa o sufixo DNS primário em branco, mas você pode configurá-lo na VM, por meio da [interface do usuário](https://technet.microsoft.com/library/cc794784.aspx) ou [usando a automação](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Os clientes unidos por domínio do Windows registram seus endereços IP com o controlador de domínio usando o DNS dinâmico seguro. O processo de ingresso no domínio define o sufixo DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux
Clientes Linux geralmente não se registram no servidor DNS na inicialização, eles supõem que o servidor DHCP faça isso. Os servidores DHCP do Azure não são capazes ou não tem as credenciais para manter os registros no seu servidor DNS.  Você pode usar uma ferramenta chamada *nsupdate*, que está incluída no pacote de Associação, para enviar atualizações do DNS dinâmico. Como o protocolo DNS dinâmico é padronizado, você pode usar *nsupdate* mesmo quando não estiver usando Associação no servidor DNS.

Você pode usar os ganchos que são fornecidos pelo cliente DHCP para criar e manter a entrada do nome do host no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts em */etc/dhcp/dhclient-exit-hooks.d/*. Isso pode ser usado para registrar o novo endereço IP usando *nsupdate*. Por exemplo:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

Você também pode usar o comando *nsupdate* para executar atualizações DNS Dinâmicas. Por exemplo, quando você estiver usando um servidor DNS de associação, um par de chaves públicas-privadas será [gerado](http://linux.yyz.us/nsupdate/).  O servidor DNS é [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte pública da chave para que ele possa verificar a assinatura da solicitação. Você deve usar a opção *-k* para fornecer o par de chaves para *nsupdate* para que o DNS dinâmico atualize a solicitação a ser assinada.

Quando estiver usando um servidor DNS do Windows, você poderá usar a autenticação Kerberos com o parâmetro *-g* em *nsupdate* (não está disponível na versão do Windows *nsupdate*). Para fazer isso, use *kinit* para carregar as credenciais (por exemplo, um [arquivo keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Em seguida, *nsupdate -g* selecionará as credenciais do cache.

Se necessário, você pode adicionar um sufixo de pesquisa DNS em suas VMs. O sufixo DNS é especificado no arquivo */etc/resolv.conf* . A maioria das distribuições de Linux gerencia automaticamente o conteúdo desse arquivo, então, normalmente você não pode editá-lo. No entanto, você pode substituir o sufixo, usando o comando *substituem* do cliente DHCP. Para fazer isso, em */etc/dhcp/dhclient.conf*, adicione:

        supersede domain-name <required-dns-suffix>;

