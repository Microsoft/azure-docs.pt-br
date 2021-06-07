---
title: Benefício Híbrido do Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Use licenças de SQL Server existentes para os descontos do banco de dados SQL do Azure e do SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: f7a37e761e37e295bbb92e442b1813ebded2a7cd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955271"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Benefício Híbrido do Azure-banco de dados SQL do Azure & SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Na camada de computação provisionada do modelo de compra baseado em vCore, você pode trocar suas licenças existentes por tarifas com desconto no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada usando [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Esse benefício do Azure permite que você economize até 30 por cento ou até mesmo mais no banco de dados SQL & SQL Instância Gerenciada usando suas licenças de SQL Server com o Software Assurance. A página [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) tem uma calculadora para ajudar a determinar a economia.  Observe que o Benefício Híbrido do Azure não se aplica ao servidor do banco de dados SQL do Azure.

> [!NOTE]
> Alterar para Benefício Híbrido do Azure não requer nenhum tempo de inatividade.

![estrutura de preços VCORE](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Escolher um modelo de licença

Com Benefício Híbrido do Azure, você pode optar por pagar apenas pela infraestrutura subjacente do Azure usando sua licença de SQL Server existente para o mecanismo de banco de dados SQL Server em si (preço de computação base) ou pode pagar pela infraestrutura subjacente e pela licença de SQL Server (preço incluído na licença).

Você pode escolher ou alterar seu modelo de licenciamento no portal do Azure: 
- Para novos bancos de dados, durante a criação, selecione **configurar banco de dados** na guia **noções básicas** e selecione a opção para economizar dinheiro.
- Para bancos de dados existentes, selecione **Configurar** no menu **configurações** e selecione a opção para economizar dinheiro.

Você também pode configurar um banco de dados novo ou existente usando uma das seguintes APIs:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para definir ou atualizar o tipo de licença usando o PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para definir ou atualizar o tipo de licença usando o CLI do Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Para definir ou atualizar o tipo de licença usando a API REST:

- [Banco de Dados – Criar ou Atualizar](/rest/api/sql/databases/createorupdate)
- [Bancos de Dados – Atualizar](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Perguntas do Benefício Híbrido do Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para SQL Server?

Você tem 180 dias de direitos de uso duplo da licença para garantir que as migrações estejam executando perfeitamente. Após esse período de 180 dias, você só poderá usar a licença SQL Server na nuvem no banco de dados SQL. Você não tem mais direitos de uso duplo local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como o Benefício Híbrido do Azure do SQL Server difere da mobilidade de licenças?

Oferecemos benefícios de mobilidade de licença para SQL Server clientes com Software Assurance. Isso permite a reatribuição de suas licenças para os servidores compartilhados de um parceiro. Você pode usar esse benefício no Azure IaaS e AWS EC2.

O Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes do SQL Server Enterprise Edition podem obter quatro núcleos no Azure no SKU do Uso Geral para cada núcleo que eles possuem localmente para aplicativos altamente virtualizados. A mobilidade de licenças não permite nenhum benefício de custo especial para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece um destino de PaaS no Azure (SQL Instância Gerenciada) que é altamente compatível com SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido do Azure para SQL Server?

Os clientes do banco de dados SQL e do SQL Instância Gerenciada têm os seguintes direitos associados ao Benefício Híbrido do Azure para SQL Server:

|Superfície da licença|O que Benefício Híbrido do Azure para SQL Server você consegue?|
|---|---|
|Clientes principais do SQL Server Enterprise Edition com SA|<li>Pode pagar a taxa base em um SKU de hiperescala, Uso Geral ou Comercialmente Crítico</li><br><li>1 núcleo local = 4 núcleos na SKU de hiperescala</li><br><li>1 núcleo local = 4 núcleos na SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo em SKU para Comercialmente Crítico</li>|
|Clientes principais do SQL Server Standard Edition com SA|<li>Pode pagar a taxa base em um SKU de hiperescala, Uso Geral ou Comercialmente Crítico</li><br><li>1 núcleo local = 1 núcleo na SKU de hiperescala</li><br><li>1 núcleo local = 1 núcleo na SKU de Uso Geral</li><br><li>4 núcleos locais = 1 núcleo no SKU Comercialmente Crítico</li>|
|||


## <a name="next-steps"></a>Próximas etapas

- Para obter ajuda com a escolha de uma opção de implantação do SQL Azure, consulte [escolher a opção de implantação correta no SQL do Azure](azure-sql-iaas-vs-paas-what-is-overview.md).
- Para obter uma comparação dos recursos do banco de dados SQL e do SQL Instância Gerenciada, consulte [SQL database & recursos do sql instância gerenciada](database/features-comparison.md).
