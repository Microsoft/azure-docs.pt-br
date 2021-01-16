---
title: Habilitar o SGX Intel para seu banco de dados SQL do Azure
description: Saiba como habilitar o SGX Intel para Always Encrypted com o Secure enclaves no banco de dados SQL do Azure selecionando uma geração de hardware habilitada para SGX.
keywords: criptografar dados, criptografia do SQL, criptografia de banco de dados, informações confidenciais, Always Encrypted, enclaves seguro, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 4c77103dbb043ef9d6af9a4078b3e574ab5f953f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253303"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Habilitar o SGX Intel para seu banco de dados SQL do Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> O Always Encrypted com Secure enclaves para o banco de dados SQL do Azure está atualmente em **Visualização pública**.

[Always Encrypted com Secure enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) no banco de dados SQL do Azure usa o ENCLAVES [Intel SGX (extensões de software Intel)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) . Para que o Intel SGX esteja disponível, o banco de dados deve usar o [modelo vCore](service-tiers-vcore.md) e a geração de hardware da [série DC](service-tiers-vcore.md#dc-series) .

Configurar a geração de hardware da série DC para habilitar o enclaves de SGX Intel é a responsabilidade do administrador do banco de dados SQL do Azure. Consulte [funções e responsabilidades ao configurar o enclaves e o atestado de SGX](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> O Intel SGX não está disponível em gerações de hardware diferentes da série DC. Por exemplo, a Intel SGX não está disponível para hardware Gen5 e não está disponível para bancos de dados que usam o [modelo de DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar a geração de hardware da série DC para seu banco de dados, verifique a disponibilidade regional da série DC e se você entendeu suas limitações de desempenho. Para obter mais informações, consulte [série CC](service-tiers-vcore.md#dc-series).

Para obter instruções detalhadas sobre como configurar um banco de dados novo ou existente para usar uma geração de hardware específica, consulte [selecionando uma geração de hardware](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Próximas etapas

- [Configurar o atestado do Azure para seu servidor de banco de dados SQL do Azure](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Confira também

- [Tutorial: introdução ao Always Encrypted com o Secure enclaves no banco de dados SQL do Azure](always-encrypted-enclaves-getting-started.md)