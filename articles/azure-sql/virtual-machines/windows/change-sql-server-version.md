---
title: Alteração in-loco da versão SQL Server
description: Saiba como alterar a versão de sua máquina virtual SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358836"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Alteração in-loco da versão SQL Server na VM do Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como alterar a versão do Microsoft SQL Server em uma VM (máquina virtual) do Windows no Microsoft Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma atualização in-loco do SQL Server, as seguintes condições se aplicam:

- A mídia de instalação da versão desejada do SQL Server é necessária. Os clientes que têm [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter a mídia de instalação deles do [Centro de Licenciamento por Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não têm o Software Assurance podem usar a mídia de instalação de um Azure Marketplace SQL Server imagem VM que tem uma versão mais recente do SQL Server (normalmente localizada em C:\SQLServerFull).
- As atualizações de edição devem seguir os [caminhos de atualização de suporte](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15).

## <a name="planning-for-version-change"></a>Planejando a alteração da versão

Recomendamos que você revise os seguintes itens antes de fazer a alteração de versão:

1. Verifique as novidades na versão para a qual você está planejando atualizar:

   - O que há de novo no [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - O que há de novo no [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - O que há de novo no [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Recomendamos que você verifique a [certificação de compatibilidade](/sql/database-engine/install-windows/compatibility-certification) para a versão que você vai alterar para que você possa usar os modos de compatibilidade de banco de dados para minimizar o efeito da atualização.
1. Você pode examinar os seguintes artigos para ajudar a garantir um resultado bem-sucedido:

   - [Vídeo: modernização SQL Server | Lahoud do Pam & Pedro Lopes | 20 anos de aprovação](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Assistente para Experimentos de Banco de Dados para teste AB](/sql/dea/database-experimentation-assistant-overview)
   - [Atualizando bancos de dados usando o Assistente de Ajuste de Consulta](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Alterar o nível de compatibilidade do banco de dados e usar o Repositório de Consultas](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Atualizar versão do SQL

> [!WARNING]
> A atualização da versão do SQL Server reiniciará o serviço para SQL Server além de quaisquer serviços associados, como Analysis Services e R Services.

Para atualizar a versão do SQL Server, obtenha a mídia de instalação do SQL Server para a versão mais recente que [oferece suporte ao caminho de atualização](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) de SQL Server e execute as seguintes etapas:

1. Faça backup dos bancos de dados, incluindo o sistema (exceto tempdb) e bancos de dados de usuário, antes de iniciar o processo. Você também pode criar um backup em nível de VM consistente com o aplicativo usando os serviços de backup do Azure.
1. Inicie o Setup.exe da mídia de instalação do SQL Server.
1. O assistente de instalação inicia o centro de instalação SQL Server. Para atualizar uma instância existente do SQL Server, selecione **instalação** no painel de navegação e, em seguida, selecione **Atualizar de uma versão anterior do SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Seleção para atualizar a versão do SQL Server":::

1. Na página **chave do produto (Product Key** ), selecione uma opção para indicar se você está atualizando para uma edição gratuita do SQL Server ou se você tem uma chave de PID para uma versão de produção do produto. Para obter mais informações, consulte [edições e recursos com suporte do SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15) e [atualizações de versão e edição com suporte (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades).
1. Selecione **Avançar** até chegar à página **pronto para atualizar** e, em seguida, selecione **Atualizar**. A janela de instalação pode parar de responder por vários minutos, enquanto a alteração está sendo efetivada. Uma página **completa** confirmará que a atualização foi concluída. Para obter um procedimento passo a passo para atualizar, consulte [o procedimento completo](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure).

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Página completa":::

Se você alterou a edição do SQL Server além de alterar a versão, atualize também a edição e consulte a seção **verificar versão e edição no portal** para alterar a instância da VM do SQL.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Alterar metadados da versão":::

## <a name="downgrade-the-version-of-sql-server"></a>Fazer downgrade da versão do SQL Server

Para fazer o downgrade da versão do SQL Server, é necessário desinstalar completamente SQL Server e reinstalá-lo usando a versão desejada. Isso é semelhante a uma nova instalação do SQL Server porque você não poderá restaurar o banco de dados anterior de uma versão posterior para a versão mais recente instalada. Os bancos de dados precisarão ser recriados do zero. Se você também alterou a edição do SQL Server durante a atualização, altere a propriedade de **edição** da VM SQL Server no portal do Azure para o novo valor de edição. Isso atualiza os metadados e a cobrança associados a essa VM.

> [!WARNING]
> Não há suporte para o downgrade in-loco do SQL Server.

Você pode fazer o downgrade da versão do SQL Server seguindo estas etapas:

1. Certifique-se de que você não está usando nenhum recurso [disponível somente na versão posterior](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Faça backup de todos os bancos de dados, incluindo o sistema (exceto tempdb) e bancos de dados de usuário.
1. Exporte todos os objetos de nível de servidor necessários (como gatilhos de servidor, funções, logons, servidores vinculados, trabalhos, credenciais e certificados).
1. Se você não tiver scripts para recriar seus bancos de dados de usuário na versão anterior, você deverá gerar scripts de todos os objetos e exportar todos eles usando BCP.exe, SSIS ou DACPAC.

   Certifique-se de selecionar as opções corretas ao gerar script de itens como a versão de destino, objetos dependentes e opções avançadas.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Opções de script":::

1. Desinstale completamente o SQL Server e todos os serviços associados.
1. Reinicie a VM.
1. Instale SQL Server usando a mídia para a versão desejada do programa.
1. Instale os service packs e as atualizações cumulativas mais recentes.
1. Importe todos os objetos de nível de servidor necessários (que foram exportados na etapa 3).
1. Recrie todos os bancos de dados de usuário necessários do zero (usando scripts criados ou os arquivos da etapa 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>Verificar a versão e a edição no portal

Depois de alterar a versão do SQL Server, registre a VM do SQL Server com a [extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md) novamente para que você possa usar o portal do Azure para exibir a versão do SQL Server. O número de versão listado agora deve refletir a versão e a edição recém atualizadas da instalação do SQL Server.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Verificar versão":::

> [!NOTE]
> Se você já se registrou com a extensão do SQL IaaS Agent, cancele o [registro do RP](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) e, em seguida, [Registre o recurso da VM do SQL](sql-agent-extension-manually-register-single-vm.md#register-with-extension) novamente para que ele detecte a versão e a edição corretas do SQL Server que está instalado na VM. Isso atualiza os metadados e as informações de cobrança associadas a essa VM.

## <a name="remarks"></a>Comentários

- Recomendamos que você inicie backups/atualize estatísticas/recrie índices/Verifique a consistência após a conclusão da atualização. Você também pode verificar os níveis de compatibilidade do banco de dados individual para ter certeza de que eles refletem o nível desejado.
- Depois que SQL Server for atualizado na VM, verifique se a propriedade de **edição** de SQL Server na portal do Azure corresponde ao número de edição instalado para cobrança.
- A capacidade de [alterar a edição](change-sql-server-edition.md#change-edition-in-portal) é um recurso da extensão do agente IaaS do SQL. A implantação de uma imagem do Azure Marketplace por meio do portal do Azure registra automaticamente uma VM SQL Server com a extensão. No entanto, os clientes que estiverem instalando automaticamente SQL Server precisarão [registrar manualmente sua VM SQL Server](sql-agent-extension-manually-register-single-vm.md).
- Se você descartar o recurso de VM SQL Server, a configuração de edição embutida em código da imagem será restaurada.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
- [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
- [Notas sobre a versão do SQL Server em uma VM do Windows](doc-changes-updates-release-notes.md)