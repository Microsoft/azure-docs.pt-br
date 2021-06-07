---
title: Planejar o enclaves e o atestado do Intel SGX no banco de dados SQL do Azure
description: Planeje a implantação do Always Encrypted com Secure enclaves no banco de dados SQL do Azure.
keywords: criptografar dados, criptografia do SQL, criptografia de banco de dados, informações confidenciais, Always Encrypted, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732737"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Planejar o enclaves e o atestado do Intel SGX no banco de dados SQL do Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Atualmente, o Always Encrypted com enclaves seguros para o Banco de Dados SQL do Azure está em **versão prévia pública**.

[Always Encrypted com Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) no banco de dados SQL do Azure usa o ENCLAVES [Intel SGX (Intel Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) e requer [atestado de Microsoft Azure](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Planejar o Intel SGX no banco de dados SQL do Azure

O Intel SGX é uma tecnologia de ambiente de execução confiável baseada em hardware. O Intel SGX está disponível para bancos de dados que usam o [modelo vCore](service-tiers-vcore.md) e a geração de hardware da [série DC](service-tiers-vcore.md?#dc-series) . Portanto, para garantir que você possa usar Always Encrypted com o Secure enclaves no seu banco de dados, você precisa selecionar a geração de hardware da série DC ao criar o banco de dados ou pode atualizar seu banco de dados existente para usar a geração de hardware da série DC.

> [!NOTE]
> O Intel SGX não está disponível em gerações de hardware diferentes da série DC. Por exemplo, a Intel SGX não está disponível para hardware Gen5 e não está disponível para bancos de dados que usam o [modelo de DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar a geração de hardware da série DC para seu banco de dados, verifique a disponibilidade regional da série DC e se você entendeu suas limitações de desempenho. Para obter detalhes, consulte [série CC](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Planejar o atestado no banco de dados SQL do Azure

O [atestado de Microsoft Azure](../../attestation/overview.md) (versão prévia) é uma solução para atestar TEEs (ambientes de execução confiável), incluindo o Intel SGX enclaves em bancos de dados SQL do Azure usando a geração de hardware da série DC.

Para usar o atestado do Azure para atestado do Intel SGX enclaves no banco de dados SQL do Azure, você precisa:

1. Crie um [provedor de atestado](../../attestation/basic-concepts.md#attestation-provider) e configure-o com uma política de atestado. 

2. Conceda o acesso do servidor lógico do Azure SQL ao provedor de atestado criado.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Funções e responsabilidades ao configurar o enclaves de SGX e o atestado

Configurar seu ambiente para dar suporte a Intel SGX enclaves e atestado para Always Encrypted no banco de dados SQL do Azure envolve a configuração de componentes de tipos diferentes: atestado de Microsoft Azure, banco de dados SQL do Azure e aplicativos que disparam o atestado enclave. A configuração de componentes de cada tipo é executada por usuários supondo uma das funções distintas abaixo:

- Administrador de atestado – cria um provedor de atestado no atestado de Microsoft Azure, autores da política de atestado, concede acesso ao servidor lógico do Azure SQL ao provedor de atestado e compartilha a URL de atestado que aponta para a política para os administradores de aplicativos.
- Administrador do banco de dados SQL do Azure – habilita a enclaves de SGX em bancos de dados selecionando a geração de hardware da série DC e fornece ao administrador de atestado a identidade do servidor lógico do SQL do Azure que precisa acessar o provedor de atestado.
- Administrador de aplicativos – configura aplicativos com a URL de atestado obtida do administrador de atestado.

Em ambientes de produção (manipulando dados confidenciais reais), é importante que sua organização obedeça à separação de funções ao configurar o atestado, em que cada função distinta é assumida por pessoas diferentes. Em particular, se o objetivo da implantação de Always Encrypted em sua organização é reduzir a área da superfície de ataque, garantindo que os administradores do banco de dados SQL do Azure não possam acessar informações confidenciais, os administradores do banco de dados SQL do Azure não devem controlar as políticas de atestado.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar o SGX Intel para seu banco de dados SQL do Azure](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Veja também

- [Tutorial: Introdução ao Always Encrypted com enclaves seguros no Banco de Dados SQL do Azure](always-encrypted-enclaves-getting-started.md)