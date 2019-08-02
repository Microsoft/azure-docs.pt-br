---
title: DNS personalizado da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 07/17/2019
ms.openlocfilehash: 674c5d48dad5d3cfd138853d7ea38ae4a216c93d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309873"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Uma Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Há alguns cenários (por exemplo, db mail, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem nomes de hosts privados para serem resolvidos da Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. 

Como Instância Gerenciada usa o mesmo DNS para seus trabalhos internos, você precisa configurar o servidor DNS personalizado para que ele possa resolver nomes de domínio públicos.

   > [!IMPORTANT]
   > Sempre use FQDNs (nomes de domínio totalmente qualificados) para os servidores de email, SQL Servers e outros serviços, mesmo que eles estejam dentro da zona DNS privado. Por exemplo, use `smtp.contoso.com` para o servidor de email porque `smtp` simples não será resolvido corretamente.

   > [!IMPORTANT]
   > A atualização de servidores DNS de rede virtual não afetará Instância Gerenciada imediatamente. Instância Gerenciada configuração do DNS será atualizada após a concessão do DHCP expirar ou após a upgarade da plataforma, o que ocorrer primeiro. **Os usuários são aconselhados a definir sua configuração de DNS de rede virtual antes de criar o primeiro Instância Gerenciada.**

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criando uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de Instâncias Gerenciadas](sql-database-managed-instance-connectivity-architecture.md)
