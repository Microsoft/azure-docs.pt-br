---
title: Alias DNS
description: Seus aplicativos podem se conectar a um alias para o nome do servidor para o banco de dados SQL do Azure. Ao mesmo tempo, você pode alterar o Banco de Dados SQL para o qual o alias aponta a qualquer momento no intuito de facilitar testes e assim por diante.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 25c2157a8de237d0ec66caa72d59c810d419ac76
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989115"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias do DNS para Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

O Banco de Dados SQL do Azure tem um servidor de DNS (Sistema de Nomes de Domínio). As APIs REST e do PowerShell aceitam [chamadas para criar e gerenciar aliases DNS](#anchor-powershell-code-62x) para o nome [lógico do SQL Server](logical-servers.md) .

Um *alias DNS* pode ser usado no lugar do nome do servidor. Os programas cliente podem usar o alias em suas cadeias de conexão. O alias do DNS oferece uma camada de conversão que pode redirecionar os programas cliente a diferentes servidores. Essa camada poupa as dificuldades de ter que localizar e editar todos os clientes e suas cadeias de conexão.

Alguns usos comuns para um alias do DNS incluem os seguintes casos:

- Crie um nome fácil de lembrar para um servidor.
- Durante o desenvolvimento inicial, seu alias pode se referir a um servidor de teste. Quando o aplicativo for lançado, você poderá modificar o alias para se referir ao servidor de produção. A transição do teste para a produção não requer nenhuma modificação nas configurações de vários clientes que se conectam ao servidor.
- Suponha que o único banco de dados em seu aplicativo seja movido para outro servidor. Você pode modificar o alias sem precisar modificar as configurações de vários clientes.
- Durante uma interrupção regional, você usa a restauração geográfica para recuperar o banco de dados em um servidor e região diferentes. Você pode modificar seu alias existente para apontar para o novo servidor para que o aplicativo cliente existente possa se conectar novamente a ele.

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Sistema de Nomes de Domínio) da Internet

A Internet depende do DNS. O DNS traduz seus nomes amigáveis para o nome do seu servidor.

## <a name="scenarios-with-one-dns-alias"></a>Cenários com um alias do DNS

Suponha que você precise mudar seu sistema para um novo servidor. No passado, você precisava localizar e atualizar cada cadeia de conexão em cada programa cliente. Mas, agora, se as cadeias de conexão usam um alias do DNS, apenas uma propriedade do alias deve ser atualizada.

O recurso alias do DNS do Banco de Dados SQL do Azure pode ajudar nas seguintes situações:

### <a name="test-to-production"></a>Teste para a produção

Quando você começar a desenvolver os programas cliente, faça com que eles usem um alias do DNS nas cadeias de conexão. Você faz as propriedades do alias apontarem para uma versão de teste do seu servidor.

Posteriormente, quando o novo sistema ficar ativo em produção, você poderá atualizar as propriedades do alias para apontar para o servidor de produção. Não haverá necessidade de alteração nos programas cliente.

### <a name="cross-region-support"></a>Suporte entre regiões

Uma recuperação de desastre pode mudar o servidor para uma região geográfica diferente. Para um sistema que estava usando um alias DNS, a necessidade de encontrar e atualizar todas as cadeias de conexão para todos os clientes pode ser evitada. Em vez disso, você pode atualizar um alias para se referir ao novo servidor que agora hospeda seu banco de dados SQL do Azure.

## <a name="properties-of-a-dns-alias"></a>Propriedades de um alias do DNS

As propriedades a seguir se aplicam a cada alias DNS para seu servidor:

- *Nome exclusivo:* Cada nome de alias que você cria é exclusivo em todos os servidores, assim como os nomes de servidor.
- *Servidor é necessário:* um alias de DNS não pode ser criado se não faz referência a exatamente um servidor e esse servidor já deve existir. Um alias atualizado sempre deve fazer referência a exatamente um servidor existente.
  - Quando você remove um servidor, o sistema do Azure também descarta todos os aliases DNS que se referem ao servidor.
- *Não associado a nenhuma região:* aliases de DNS não são associados a uma região. Todos os aliases de DNS podem ser atualizados para se referir a um servidor que reside em qualquer região geográfica.
  - No entanto, ao atualizar um alias para se referir a outro servidor, ambos os servidores devem existir na mesma *assinatura* do Azure.
- *Permissões:* para gerenciar um alias de DNS, o usuário deve ter permissões de *Colaborador do Server* ou superior. Para obter mais informações, consulte Introdução [ao controle de acesso baseado em função do Azure no portal do Azure](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Gerenciar seus aliases de DNS

Os cmdlets do PowerShell e das APIs REST estão disponíveis para que você possa gerenciar seus aliases de DNS de forma programática.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>APIs REST para gerenciar seus aliases de DNS

A documentação das APIs REST está disponível perto do seguinte local:

- [API REST do Banco de Dados SQL do Azure](/rest/api/sql/)

Além disso, as APIs REST podem ser vistas no GitHub em:

- [APIs REST do alias DNS do banco de dados SQL do Azure](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell para gerenciar seus aliases de DNS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Os cmdlets do PowerShell que chamam as APIs REST estão disponíveis.

Há um exemplo de código de cmdlets do PowerShell usados para gerenciar aliases de DNS documentado em:

- [PowerShell para alias do DNS para o Banco de Dados SQL do Azure](dns-alias-powershell-create.md)

Os cmdlets usados no exemplo de código são os seguintes:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): cria um novo alias DNS no sistema de serviço do banco de dados SQL do Azure. O alias se refere ao servidor 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): obter e listar todos os aliases de DNS atribuídos ao servidor 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modifica o nome do servidor ao qual o alias está configurado para se referir, do servidor 1 ao servidor 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove o alias de DNS do servidor 2, usando o nome do alias.

## <a name="limitations-during-preview"></a>Limitações durante a versão prévia

Atualmente, um alias de DNS tem as seguintes limitações:

- *Atraso de até 2 minutos:* leva até 2 minutos para que um alias de DNS seja atualizado ou removido.
  - Independentemente de qualquer breve atraso, o alias interrompe imediatamente as conexões de cliente que fazem referência ao servidor herdado.
- *Pesquisa de DNS:* por enquanto, a única maneira oficial de verificar a qual servidor um alias de DNS específico faz referência é por meio da execução de uma [pesquisa de DNS](/windows-server/administration/windows-commands/nslookup).
- _Não há suporte para a auditoria de tabela:_ Você não pode usar um alias DNS em um servidor que tenha a *auditoria de tabela* habilitada em um banco de dados.
  - A auditoria de tabela foi preterida.
  - É recomendável que você mude para a [Auditoria de Blob](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Recursos relacionados

- [Visão geral da continuidade de negócios com o Banco de Dados SQL do Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md), incluindo recuperação de desastre.
- [Referência de API REST do Azure](/rest/api/azure/)
- [API de aliases de DNS do servidor](/rest/api/sql/serverdnsaliases)

## <a name="next-steps"></a>Próximas etapas

- [PowerShell para alias do DNS para o Banco de Dados SQL do Azure](dns-alias-powershell-create.md)