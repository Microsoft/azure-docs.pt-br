---
title: Início Rápido – Instância gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como começar rapidamente a usar o Banco de Dados SQL do Azure – instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 97f7393622311103d94d3067205f902e3eec6843
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567349"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introdução à instância gerenciada do Banco de Dados SQL do Azure

A opção de implantação de [instância gerenciada](sql-database-managed-instance-index.yml) cria um banco de dados com quase 100% de compatibilidade com o mecanismo de banco de dados (Enterprise Edition) local do SQL Server, fornecendo uma implementação de [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) nativa que resolve preocupações comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do SQL Server locais. Neste artigo, você aprenderá como configurar e criar rapidamente uma instância gerenciada e como migrar seus bancos de dados.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Os inícios rápidos a seguir permitem que você crie rapidamente uma instância gerenciada, configure uma máquina virtual ou aponte para a conexão VPN do site para o aplicativo cliente e restaure um banco de dados para sua nova instância gerenciada usando um arquivo `.bak`.

### <a name="configure-environment"></a>Configurar ambiente

Como uma primeira etapa, você precisaria criar sua primeira Instância Gerenciada com o ambiente de rede em que ela será colocada e habilitar a conexão do computador ou máquina virtual em que você está executando consultas para a Instância Gerenciada. É possível usar as seguintes orientações:

- [Criar uma instância gerenciada usando o portal do Azure](sql-database-managed-instance-get-started.md). No portal do Azure, você configura os parâmetros necessários (nome de usuário/senha, número de núcleos e quantidade de armazenamento máxima) e cria automaticamente o ambiente de rede do Azure sem precisar conhecer os detalhes da rede e os requisitos de infraestrutura. Verifique se você tem [tipo de assinatura](sql-database-managed-instance-resource-limits.md#supported-subscription-types) que tenha, no momento, permissão para criar uma instância gerenciada. Se você tiver sua própria rede que você deseja usar ou quiser personalizar a rede, veja [Configurar uma rede virtual existente para a instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-configure-vnet-subnet.md) ou [Criar uma rede virtual para a instância gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-create-vnet-subnet.md).
- Uma instância gerenciada é criada na própria VNet sem nenhum ponto de extremidade público. Para ter acesso a aplicativos cliente, é possível **criar uma VM na mesma VNet (sub-rede diferente)** ou **criar uma conexão VPN ponto a site com a VNet do computador cliente** usando um desses inícios rápidos:
  - Habilite o [ponto de extremidade público](sql-database-managed-instance-public-endpoint-configure.md) em sua Instância Gerenciada para acessar seus dados diretamente de seu ambiente.
  - Crie uma [máquina virtual do Azure na VNet de instância gerenciada](sql-database-managed-instance-configure-vm.md) para obter conectividade de aplicativo cliente, incluindo o SQL Server Management Studio.
  - Configure a [conexão VPN ponto a site com sua instância gerenciada](sql-database-managed-instance-configure-p2s.md) do computador cliente no qual você tem o SQL Server Management Studio e outros aplicativos de conectividade de cliente. Esta é a outra das duas opções para conectividade com sua instância gerenciada e com sua VNet.

  > [!NOTE]
  > Também é possível usar a rota expressa ou a conexão site a site da rede local, mas essas abordagens estão fora do escopo desses inícios rápidos.

Como alternativa à criação manual da Instância Gerenciada, você pode usar o [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), o [PowerShell com o modelo do Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) para criar scripts e automatizar esse processo.

### <a name="migrate-your-databases"></a>Migrar seus bancos de dados

Após você criar uma instância gerenciada e configurar o acesso, será possível começar a migrar os bancos de dados do SQL Server local ou em VMs do Azure. A migração falhará se você tiver alguns recursos sem suporte no banco de dados de origem que você deseja migrar. Para evitar falhas e verificar a compatibilidade, você pode instalar o [AMD (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595), que analisa seus bancos de dados no SQL Server e encontra qualquer problema que possa bloquear a migração para uma instância gerenciada, como a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou de vários arquivos de log. Se você resolver esses problemas, seus bancos de dados estarão prontos para migrar para a instância gerenciada. O [Assistente para Experimentos de Banco de Dados](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) é outra ferramenta útil que pode registrar a carga de trabalho no SQL Server e reproduzi-la em uma instância gerenciada para que você possa determinar se haverá problemas de desempenho se você migrar para uma instância gerenciada.

Após ter a certeza de que você pode migrar o banco de dados para uma instância gerenciada, será possível usar as funcionalidades nativas de restauração do SQL Server para restaurar um banco de dados em uma instância gerenciada de um arquivo `.bak`. Você pode usar esse método para migrar bancos de dados do mecanismo de banco de dados do SQL Server instalado localmente ou na VM do Azure. Para um início rápido, confira [Restore from backup to a managed instance](sql-database-managed-instance-get-started-restore.md) (Restaurar do backup para uma instância gerenciada). Neste início rápido, você restaurará de um arquivo `.bak` armazenado no Armazenamento de Blobs do Azure usando o comando `RESTORE` Transact-SQL.

> [!TIP]
> Para usar o comando `BACKUP` Transact-SQL para criar um backup do banco de dados no Armazenamento de Blobs do Azure, confira [Backup do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes inícios rápidos permitem que você crie, configure e restaure um backup de banco de dados rapidamente para uma instância gerenciada. Em alguns cenários, você precisaria personalizar ou automatizar a implantação de instâncias gerenciadas e do ambiente de rede necessário. Esses cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar ambiente de rede

Embora a rede virtual/sub-rede possa ser configurada automaticamente quando a instância é criada usando o [portal do Azure](sql-database-managed-instance-get-started.md), talvez seja bom criá-la antes de começar a criar as Instâncias Gerenciadas, porque é possível configurar os parâmetros da rede virtual e da sub-rede. A maneira mais fácil de criar e configurar o ambiente de rede é usar o modelo de [implantação de Recursos do Azure](sql-database-managed-instance-create-vnet-subnet.md), que criará e configurará sua rede e a sub-rede onde a instância será colocada. Basta pressionar o botão de implantação do Azure Resource Manager e preencher o formulário com os parâmetros.

Como alternativa, você pode usar o [script do PowerShell](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Como alternativa, também é possível usar este [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se você já tiver uma VNet e uma sub-rede em que você gostaria de implantar sua instância gerenciada, será necessário garantir que a VNet e a sub-rede satisfaçam os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Use este [script do PowerShell para verificar se sua sub-rede foi configurada corretamente](sql-database-managed-instance-configure-vnet-subnet.md). Este script valida sua rede, relata quaisquer problemas e informa o que deve ser alterado e, em seguida, se oferece para fazer as alterações necessárias em sua VNet/sub-rede. Execute este script se você não desejar configurar sua VNet/sub-rede manualmente. Também será possível executá-lo após qualquer importante reconfiguração da infraestrutura de rede. Se desejar criar e configurar sua própria rede, leia [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md) e este [guia definitivo para criar e configurar um ambiente de instância gerenciada](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrar para uma instância gerenciada

Os artigos nestes inícios rápidos permitem que você configure uma instância gerenciada rapidamente e mova seus bancos de dados usando a funcionalidade `RESTORE` nativa. Esse é um bom ponto de partida quando você quer executar provas de conceito rápidas e verificar se sua solução pode funcionar na Instância Gerenciada. 

No entanto, para migrar seu banco de dados de produção ou até mesmo bancos de dados de desenvolvimento/teste que deseja usar para algum teste de desempenho, você precisará considerar o uso de algumas técnicas adicionais, como:
- Teste de desempenho – você deve medir o desempenho de linha de base de sua instância do SQL Server de origem e compará-lo com o desempenho da Instância Gerenciada de destino para a qual migrou o banco de dados. Saiba mais sobre as [melhores práticas de comparação de desempenho](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migração online – com o `RESTORE` nativo descrito neste artigo, é necessário aguardar até que os bancos de dados sejam restaurados (e copiados para o Armazenamento de Blobs do Azure caso ainda não estejam armazenados lá). Isso causa algum tempo de inatividade do seu aplicativo, principalmente para bancos de dados maiores. Para mover o banco de dados de produção, use o [DMS (Serviço de Migração de Dados)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar o banco de dados com tempo de inatividade mínimo. O DMS faz isso enviando por push as alterações feitas no banco de dados de origem em incrementos para o banco de dados da instância gerenciada que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

Saiba mais sobre o [processo de migração recomendado](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Próximas etapas

- Encontre uma [lista de alto nível dos recursos com suporte na instância gerenciada aqui](sql-database-features.md) e [detalhes e problemas conhecidos aqui](sql-database-managed-instance-transact-sql-information.md).
- Saiba mais sobre as [características técnicas da instância gerenciada](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Encontre instruções mais avançadas em [como usar uma instância gerenciada no Banco de Dados SQL do Azure](sql-database-howto-managed-instance.md).
- [Identifique a SKU de banco de dados SQL/instância gerenciada do Azure do banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).
