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
ms.date: 12/13/2018
ms.openlocfilehash: bb5890b883b6062d834b928bff28a26a3664fb64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700390"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Uma Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Há alguns cenários (por exemplo, db mail, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem nomes de hosts privados para serem resolvidos da Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. Uma vez que a Instância Gerenciada usa o mesmo DNS para seu funcionamento interno, a configuração de DNS da rede virtual deverá ser compatível com a Instância Gerenciada.

   > [!IMPORTANT]
   > Sempre use FQDNs (nomes de domínio totalmente qualificados) para os servidores de email, SQL Servers e outros serviços, mesmo que eles estejam dentro da zona DNS privado. Por exemplo, use `smtp.contoso.com` para o servidor de email porque `smtp` simples não será resolvido corretamente.

Para tornar uma configuração personalizada do DNS compatível com a Instância Gerenciada, é necessário:

- Configurar o servidor DNS personalizado para que ele seja capaz de resolver nomes de domínio público
- Colocar o endereço IP de DNS 168.63.129.16 do resolvedor recursivo do Azure no final da lista DNS de rede virtual

## <a name="setting-up-custom-dns-servers-configuration"></a>Definir uma configuração de servidores DNS personalizada

1. No portal do Azure, encontre opção de DNS personalizado para sua rede virtual.

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png)

2. Alterne para Personalizado e insira o endereço IP do servidor DNS personalizado, bem como o endereço IP de resolvedores recursivos do Azure, 168.63.129.16.

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png)

   > [!IMPORTANT]
   > Não definir o resolvedor recursivo do Azure na lista de DNS pode fazer com que a Instância Gerenciada entre em um estado de falha quando os servidores DNS personalizados estão indisponíveis por algum motivo. A recuperação desse estado pode exigir a criação de uma nova instância em uma VNet com as políticas de rede compatíveis, recriar dados em nível de instância e restaurar os bancos de dados. Definir o resolvedor recursivo do Azure como a última entrada na lista de DNS garante que os nomes públicos ainda poderão ser resolvidos, mesmo quando todos os servidores DNS personalizados falharem.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criando uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de Instâncias Gerenciadas](sql-database-managed-instance-connectivity-architecture.md)
