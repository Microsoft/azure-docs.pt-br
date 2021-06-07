---
title: Filtragem de FQDN do firewall do Azure em regras de rede
description: Como usar a filtragem de FQDN do firewall do Azure em regras de rede
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695938"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Usar a filtragem de FQDN em regras de rede

Um FQDN (nome de domínio totalmente qualificado) representa um nome de domínio de um host ou endereço (s) IP. Você pode usar FQDNs em regras de rede com base na resolução de DNS no firewall do Azure e na política de firewall. Esse recurso permite filtrar o tráfego de saída com qualquer protocolo TCP/UDP (incluindo NTP, SSH, RDP e muito mais). Você deve habilitar o proxy DNS para usar FQDNs em suas regras de rede. Para obter mais informações, consulte [configurações de DNS do firewall do Azure](dns-settings.md).

> [!NOTE]
> Por design, a filtragem de FQDN não dá suporte a curingas.

## <a name="how-it-works"></a>Como ele funciona

Depois de definir qual servidor DNS sua organização precisa (DNS do Azure ou seu próprio DNS personalizado), o Firewall do Azure converte o FQDN para um ou mais endereços IP com base no servidor DNS selecionado. Essa tradução ocorre para o processamento de regras de aplicativo e de rede.

Quando ocorre uma nova resolução de DNS, novos endereços IP são adicionados às regras de firewall. Endereços IP antigos que não são mais retornados pelo servidor DNS expiram em 15 minutos. As regras de firewall do Azure são atualizadas a cada 15 segundos a partir da resolução DNS dos FQDNs nas regras de rede.

### <a name="differences-in-application-rules-vs-network-rules"></a>Diferenças nas regras de aplicativo vs. regras de rede

- A filtragem de FQDN nas regras de aplicativo para HTTP/S e MSSQL é baseada em um proxy transparente de nível de aplicativo e no cabeçalho SNI. Como tal, ele pode discernir entre dois FQDNs que são resolvidos para o mesmo endereço IP. Esse não é o caso da filtragem de FQDN nas regras de rede. 

   Sempre use regras de aplicativo quando possível:
     - Se o protocolo for HTTP/S ou MSSQL, use regras de aplicativo para a filtragem de FQDN.
   - Para outros protocolos além de HTTP/S ou MSSQL, você pode usar regras de aplicativo ou de rede para filtragem de FQDN.

## <a name="next-steps"></a>Próximas etapas

[Configurações de DNS do firewall do Azure](dns-settings.md)
