---
title: Referência de conteúdo de introdução
titleSuffix: Azure SQL Managed Instance
description: 'Uma referência de conteúdo que ajuda você a começar a usar a Instância Gerenciada de SQL do Azure. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: ec767e5f3a88c52f9686eec8f7e458ab517ee35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784327"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Introdução à Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A [Instância Gerenciada de SQL do Azure](sql-managed-instance-paas-overview.md) cria um banco de dados com quase 100% de compatibilidade com o mecanismo de banco de dados do SQL Server mais recente (Edição Enterprise), fornecendo uma implementação de [VNet (rede virtual)](../../virtual-network/virtual-networks-overview.md) nativa que resolve preocupações comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes existentes do SQL Server.

Neste artigo, você vai encontrar referências de conteúdo que ensina como configurar e criar rapidamente uma Instância Gerenciada de SQL e como migrar seus bancos de dados.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Os inícios rápidos a seguir permitem que você crie rapidamente uma Instância Gerenciada de SQL, configure uma máquina virtual ou aponte para a conexão VPN do site para o aplicativo cliente e restaure um banco de dados em sua nova Instância Gerenciada do SQL usando um arquivo `.bak`.

### <a name="configure-environment"></a>Configurar ambiente

Como uma primeira etapa, você precisaria criar sua primeira Instância Gerenciada de SQL com o ambiente de rede em que ela será colocada e habilitar a conexão do computador ou da máquina virtual em que você está executando consultas para a Instância Gerenciada de SQL. É possível usar as seguintes orientações:

- [Criar uma Instância Gerenciada de SQL usando o portal do Azure](instance-create-quickstart.md). No portal do Azure, você configura os parâmetros necessários (nome de usuário/senha, número de núcleos e quantidade de armazenamento máxima) e cria automaticamente o ambiente de rede do Azure sem precisar conhecer os detalhes da rede e os requisitos de infraestrutura. Verifique se você tem um [tipo de assinatura](resource-limits.md#supported-subscription-types) que tenha, no momento, permissão para criar uma Instância Gerenciada de SQL. Se você tiver sua própria rede que deseja usar ou se quiser personalizar a rede, confira [Configurar uma rede virtual existente para a Instância Gerenciada de SQL do Azure](vnet-existing-add-subnet.md) ou [Criar uma rede virtual para a Instância Gerenciada do SQL do Azure](virtual-network-subnet-create-arm-template.md).
- Uma Instância Gerenciada de SQL é criada na própria VNet sem nenhum ponto de extremidade público. Para ter acesso a aplicativos cliente, é possível **criar uma VM na mesma VNet (sub-rede diferente)** ou **criar uma conexão VPN ponto a site com a VNet do computador cliente** usando um desses inícios rápidos:
  - Habilite o [ponto de extremidade público](public-endpoint-configure.md) em sua Instância Gerenciada de SQL para acessar os dados diretamente do ambiente.
  - Crie uma [Máquina Virtual do Azure na VNet da Instância Gerenciada de SQL](connect-vm-instance-configure.md) para obter conectividade de aplicativo cliente, incluindo o SQL Server Management Studio.
  - Configure a [conexão VPN ponto a site com sua Instância Gerenciada de SQL](point-to-site-p2s-configure.md) do computador cliente no qual você tem o SQL Server Management Studio e outros aplicativos de conectividade de cliente. Esta é a outra das duas opções para conectividade com a sua Instância Gerenciada de SQL e com a sua VNet.

  > [!NOTE]
  > - Também é possível usar a rota expressa ou a conexão site a site da rede local, mas essas abordagens estão fora do escopo desses inícios rápidos.
  > - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (os logs gravados durante o período em que a retenção era definida como ilimitada serão preservados, mesmo após a retenção ser habilitada).

Como alternativa à criação manual da Instância Gerenciada de SQL, você pode usar o [PowerShell](scripts/create-configure-managed-instance-powershell.md), o [PowerShell com o modelo do Resource Manager](./create-template-quickstart.md) ou a [CLI do Azure](/cli/azure/sql/mi#az_sql_mi_create) para criar scripts e automatizar esse processo.

### <a name="migrate-your-databases"></a>Migrar seus bancos de dados

Após você criar uma Instância Gerenciada de SQL e configurar o acesso, você poderá começar a migrar os bancos de dados do SQL Server. A migração poderá falhar se você tiver alguns recursos sem suporte no banco de dados de origem que deseja migrar. Para evitar falhas e verificar a compatibilidade, você pode usar o [AMD (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595), para analisar seus bancos de dados no SQL Server e encontrar qualquer problema que possa bloquear a migração para a Instância Gerenciada de SQL, como a existência de [FileStream](/sql/relational-databases/blob/filestream-sql-server) ou de vários arquivos de log. Se você resolver esses problemas, seus bancos de dados estarão prontos para migrar para a Instância Gerenciada de SQL. O [Assistente para Experimentos de Banco de Dados](/sql/dea/database-experimentation-assistant-overview) é outra ferramenta útil que pode registrar a carga de trabalho no SQL Server e reproduzi-la em uma Instância Gerenciada de SQL para que você possa determinar se haverá problemas de desempenho se você migrar para uma Instância Gerenciada do SQL.

Após ter a certeza de que você pode migrar o banco de dados para uma Instância Gerenciada de SQL, será possível usar as funcionalidades nativas de restauração do SQL Server para restaurar um banco de dados em uma Instância Gerenciada do SQL com base em um arquivo `.bak`. Você pode usar esse método para migrar bancos de dados do mecanismo de banco de dados do SQL Server instalado localmente ou nas Máquinas Virtuais do Azure. Para um início rápido, confira [Restore from backup to a managed instance](restore-sample-database-quickstart.md) (Restaurar do backup para uma Instância Gerenciada de SQL). Neste início rápido, você restaurará de um arquivo `.bak` armazenado no Armazenamento de Blobs do Azure usando o comando `RESTORE` Transact-SQL.

> [!TIP]
> Para usar o comando `BACKUP` Transact-SQL para criar um backup do banco de dados no Armazenamento de Blobs do Azure, confira [Backup do SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes inícios rápidos permitem que você crie, configure e restaure um backup de banco de dados rapidamente para uma Instância Gerenciada de SQL. Em alguns cenários, você precisaria personalizar ou automatizar a implantação da Instância Gerenciada de SQL e do ambiente de rede necessário. Esses cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar ambiente de rede

Embora a rede virtual/sub-rede possa ser configurada automaticamente quando a instância é criada usando o [portal do Azure](instance-create-quickstart.md), talvez seja bom criá-la antes de começar a criar as instâncias em Instância Gerenciada de SQL, porque é possível configurar os parâmetros da rede virtual e da sub-rede. A maneira mais fácil de criar e configurar o ambiente de rede é usar o modelo de [implantação de Recursos do Azure](virtual-network-subnet-create-arm-template.md), que criar e configurar sua rede e a sub-rede onde a instância será colocada. Basta pressionar o botão de implantação do Azure Resource Manager e preencher o formulário com os parâmetros.

Como alternativa, também é possível usar este [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se você já tiver uma VNet e uma sub-rede em que você gostaria de implantar sua Instância Gerenciada de SQL, será necessário garantir que a VNet e a sub-rede satisfaçam os [requisitos de rede](connectivity-architecture-overview.md#network-requirements). Use este [script do PowerShell para verificar se sua sub-rede foi configurada corretamente](vnet-existing-add-subnet.md). Este script valida sua rede e relata quaisquer problemas, informando o que deve ser alterado e, em seguida, oferecendo-se para efetuar as alterações necessárias em sua VNet/sub-rede. Execute este script se você não desejar configurar sua VNet/sub-rede manualmente. Também será possível executá-lo após qualquer importante reconfiguração da infraestrutura de rede. Se desejar criar e configurar sua própria rede, leia [arquitetura de conectividade](connectivity-architecture-overview.md) e este [guia definitivo para criar e configurar um ambiente de Instância Gerenciada de SQL](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrar para uma Instância Gerenciada de SQL

Os inícios rápidos mencionados anteriormente permitem que você configure rapidamente uma Instância Gerenciada de SQL e mova seus bancos de dados usando a funcionalidade `RESTORE` nativa. Esse é um bom ponto de partida quando você quer executar provas de conceito rápidas e verificar se sua solução pode funcionar na Instância Gerenciada.

No entanto, para migrar seu banco de dados de produção ou até mesmo bancos de dados de desenvolvimento/teste que deseja usar para algum teste de desempenho, você precisará considerar o uso de algumas técnicas adicionais, como:

- Teste de desempenho – você deve medir as métricas de desempenho de linha de base da sua instância do SQL Server de origem e compará-las com as métricas de desempenho da Instância Gerenciada de SQL de destino para a qual migrou o banco de dados. Saiba mais sobre as [melhores práticas de comparação de desempenho](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migração online – com o `RESTORE` nativo descrito neste artigo, é necessário aguardar até que os bancos de dados sejam restaurados (e copiados para o Armazenamento de Blobs do Azure caso ainda não estejam armazenados lá). Isso causa algum tempo de inatividade do seu aplicativo, principalmente para bancos de dados maiores. Para mover o banco de dados de produção, use o [DMS (Serviço de Migração de Dados)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) para migrar o banco de dados com tempo de inatividade mínimo. O DMS faz isso enviando por push as alterações feitas no banco de dados de origem em incrementos para o banco de dados da Instância Gerenciada de SQL que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

Saiba mais sobre o [processo de migração recomendado](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Próximas etapas

- Encontre uma [lista de alto nível dos recursos com suporte na Instância Gerenciada de SQL aqui](../database/features-comparison.md) e [detalhes e problemas conhecidos aqui](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre as [características técnicas da Instância Gerenciada de SQL](resource-limits.md#service-tier-characteristics).
- Encontre instruções mais avançadas em [Como usar uma Instância Gerenciada de SQL](how-to-content-reference-guide.md).
- [Identifique a SKU de Instância Gerenciada de SQL do Azure correta para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).