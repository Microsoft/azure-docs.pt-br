---
title: Filtragem do Gerenciador de firewall do Azure em regras de rede (versão prévia)
description: Como usar a filtragem de FQDN em regras de rede
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460143"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtragem de FQDN em regras de rede (versão prévia)

> [!IMPORTANT]
> A filtragem de FQDN nas regras de rede está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um FQDN (nome de domínio totalmente qualificado) representa um nome de domínio de um host ou endereço (s) IP. Você pode usar FQDNs em regras de rede com base na resolução de DNS no firewall do Azure e na política de firewall. Esse recurso permite filtrar o tráfego de saída com qualquer protocolo TCP/UDP (incluindo NTP, SSH, RDP e muito mais). Você deve habilitar o proxy DNS para usar FQDNs em suas regras de rede. Para obter mais informações, consulte [configurações de DNS da política de firewall do Azure (versão prévia)](dns-settings.md).

## <a name="how-it-works"></a>Como ele funciona

Depois de definir qual servidor DNS sua organização precisa (DNS do Azure ou seu próprio DNS personalizado), o Firewall do Azure converte o FQDN para um ou mais endereços IP com base no servidor DNS selecionado. Essa tradução ocorre para o processamento de regras de aplicativo e de rede.

Qual é a diferença entre usar nomes de domínio em regras de aplicativo em comparação com as regras de rede? 

- A filtragem de FQDN nas regras de aplicativo para HTTP/S e MSSQL é baseada em um proxy transparente de nível de aplicativo e no cabeçalho SNI. Como tal, ele pode discernir entre dois FQDNs que são resolvidos para o mesmo endereço IP. Esse não é o caso da filtragem de FQDN nas regras de rede. Sempre use regras de aplicativo quando possível.
- Em regras de aplicativo, você pode usar HTTP/S e MSSQL como seus protocolos selecionados. Em regras de rede, você pode usar qualquer protocolo TCP/UDP com seus FQDNs de destino.

## <a name="next-steps"></a>Próximas etapas

[Configurações de DNS do firewall do Azure](dns-settings.md)
