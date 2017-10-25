---
title: "Especificando as configurações DNS em um arquivo de configuração de Rede Virtual | Microsoft Docs"
description: "Como alterar as configurações do servidor DNS em uma rede virtual usando um arquivo de configuração de rede virtual no modelo de implantação clássica"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.openlocfilehash: ec33268915a1888509834ce6a5b2bc782a12ce4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificando as configurações de DNS em um arquivo de configuração de rede virtual
Um arquivo de configuração de rede tem dois elementos que você pode usar para especificar as configurações do Sistema de Nome de Domínio (DNS): **DnsServers** e **DnsServerRef**. Você pode adicionar uma lista de servidores DNS especificando seus endereços IP e fazendo referência a nomes para o elemento **DnsServers** . Você pode usar um elemento **DnsServerRef** para especificar quais entradas do servidor DNS do elemento DnsServers serão usadas para sites de rede diferentes dentro da sua rede virtual.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico.

O arquivo de configuração de rede pode conter os seguintes elementos. O título de cada elemento é vinculado a uma página que fornece informações adicionais sobre as configurações de valores de elemento.

> [!IMPORTANT]
> Para obter informações sobre como configurar o arquivo de configuração de rede, consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento contido no arquivo de configuração de rede, consulte [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> O atributo **nome** no elemento **DnsServer** é usado apenas como uma referência para o elemento**DnsServerRef**. Ele não representa o nome do host para o servidor DNS. Cada valor do atributo **DnsServer** deve ser exclusivo a toda a assinatura do Microsoft Azure
> 
> 

[Elemento de Sites de Rede Virtual](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Para especificar esta configuração para o elemento de Sites de Rede Virtual, ela deve ser anteriormente definida no elemento de DNS. O *nome* DnsServerRef no elemento Sites de Rede Virtual deve se referir a um valor de nome especificado no elemento de DNS para o *nome* DnsServer.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Entenda o [Esquema de configuração da Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
* Entenda o [Esquema de configuração do Serviço do Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configure uma rede virtual usando os arquivos de configuração de Rede](virtual-networks-using-network-configuration-file.md).

