---
title: Grupos de confiança do servidor
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre o grupo de confiança do servidor e como gerenciar a confiança entre instâncias gerenciadas do SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790722"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Usar grupos de confiança do servidor para configurar e gerenciar a confiança entre instâncias gerenciadas do SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O grupo de confiança do servidor é um conceito usado para gerenciar a confiança entre instâncias gerenciadas do SQL do Azure. Ao criar um grupo, uma relação de confiança baseada em certificado é estabelecida entre seus membros. Essa relação de confiança pode ser usada para diferentes cenários de instância cruzada. A remoção de servidores do grupo ou a exclusão do grupo remove a relação de confiança entre os servidores. Para criar ou excluir o usuário do grupo de confiança do servidor precisa ter permissões de gravação em Instância Gerenciada.
O [grupo de confiança do servidor](/azure/templates/microsoft.sql/allversions) é um objeto Azure Resource Manager que foi rotulado como **grupo de confiança do SQL** no portal do Azure.

> [!NOTE]
> O grupo de confiança do servidor é introduzido na visualização pública de transações distribuídas entre instâncias gerenciadas do SQL do Azure e atualmente tem algumas limitações que serão descritas posteriormente neste artigo.

## <a name="server-trust-group-setup"></a>Configuração do grupo de confiabilidade do servidor

A seção a seguir descreve a instalação do grupo de confiança do servidor.

1. Vá para o [Portal do Azure](https://portal.azure.com/).

2. Navegue até o Azure SQL Instância Gerenciada que você planeja adicionar a um grupo de confiança do servidor recém-criado.

3. Nas configurações de **segurança** , selecione a guia **grupos de confiabilidade do SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Grupos de confiança do servidor":::

4. Na página configuração do grupo de confiabilidade do servidor, selecione o ícone **novo grupo** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Grupos de confiança do servidor":::

5. Na folha criar **grupo de confiabilidade do SQL** , defina o nome do **grupo** . Ele precisa ser exclusivo em todas as regiões em que os membros do grupo residem. O **escopo de confiança** define o tipo de cenário de instância cruzada que é habilitado com o grupo de confiança do servidor. Na visualização, o único escopo de confiança aplicável é **transações distribuídas** , portanto, ele é preselecionado e não pode ser alterado. Todos os **membros do grupo** devem pertencer à mesma **assinatura** , mas podem estar em grupos de recursos diferentes. Selecione o **grupo de recursos** e **SQL Server/instância** para escolher o instância gerenciada SQL do Azure que será membro do grupo.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Grupos de confiança do servidor":::

6. Depois que todos os campos obrigatórios forem preenchidos, clique em **salvar** .

## <a name="server-trust-group-maintenance-and-deletion"></a>Manutenção e exclusão do grupo de confiança do servidor

O grupo de confiança do servidor não pode ser editado. Para remover um Instância Gerenciada de um grupo, você precisa excluir o grupo e criar um novo.

A seção a seguir descreve o processo de exclusão de grupos de confiança do servidor. 
1. Vá para o portal do Azure.
2. Navegue até um Instância Gerenciada que pertence ao grupo de confiança.
3. Nas configurações de **segurança** , selecione a guia **grupos de confiabilidade do SQL** .
4. Selecione o grupo de confiança que você deseja excluir.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Grupos de confiança do servidor":::
5. Clique em **excluir grupo** .
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Grupos de confiança do servidor":::
6. Digite o nome do grupo de confiança do servidor para confirmar a exclusão e clique em **excluir** .
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Grupos de confiança do servidor":::

> [!NOTE]
> Excluir o grupo de confiança do servidor pode não remover imediatamente a relação de confiança entre as duas instâncias gerenciadas. A remoção de confiança pode ser imposta invocando um [failover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) de instâncias gerenciadas. Verifique os [problemas conhecidos](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) para as atualizações mais recentes sobre isso.

## <a name="limitations"></a>Limitações

Durante a visualização pública, as seguintes limitações se aplicam aos grupos de confiança do servidor.
 * O nome do grupo de confiança do servidor deve ser exclusivo em todas as regiões em que seus membros são.
 * O grupo pode conter apenas instâncias gerenciadas do SQL do Azure e deve estar na mesma assinatura do Azure.
 * Na visualização, o grupo pode ter exatamente duas instâncias gerenciadas. Para executar transações distribuídas em mais de duas instâncias gerenciadas, você precisará criar um grupo de confiança do servidor para cada par de instâncias gerenciadas.
 * As transações distribuídas são o único escopo aplicável para os grupos de confiança do servidor.
 * O grupo de confiança do servidor só pode ser gerenciado a partir de portal do Azure. O suporte ao PowerShell e à CLI será fornecido mais tarde.
 * O grupo de confiança do servidor não pode ser editado no portal do Azure. Ele só pode ser criado ou descartado.
 * Limitações adicionais de transações distribuídas podem estar relacionadas ao seu cenário. O mais notável é que deve haver conectividade entre as instâncias gerenciadas por pontos de extremidade privados, por meio de emparelhamento VNET ou VNET. Verifique se você está ciente das limitações atuais de [transações distribuídas para instância gerenciada](../database/elastic-transactions-overview.md#limitations).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre transações distribuídas no Azure SQL Instância Gerenciada, consulte [transações distribuídas](../database/elastic-transactions-overview.md).
* Para ver as atualizações de versão e o estado de problemas conhecidos, consulte [instância gerenciada notas de versão](../database/doc-changes-updates-release-notes.md).
* Se você tiver solicitações de recursos, adicione-as ao [Fórum de instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance).