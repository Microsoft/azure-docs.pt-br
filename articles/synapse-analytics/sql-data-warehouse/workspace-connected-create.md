---
title: Habilitando recursos do espaço de trabalho Synapse
description: Este documento descreve como um usuário pode habilitar os recursos de espaço de trabalho do Synapse em um pool SQL dedicado existente (anteriormente conhecido como SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071180"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando recursos de espaço de trabalho Synapse para um pool SQL dedicado (antigo SQL DW)

Todos os usuários do SQL data warehouse agora podem acessar e usar uma instância existente do pool SQL dedicado (anteriormente conhecido como SQL DW) por meio do Synapse Studio e do espaço de trabalho. Os usuários podem usar o Synapse Studio e o espaço de trabalho sem afetar a automação, as conexões ou as ferramentas. Este artigo explica como um usuário existente do Azure Synapse Analytics pode habilitar os recursos do espaço de trabalho Synapse para um pool SQL dedicado existente (anteriormente conhecido como SQL DW). O usuário pode expandir sua solução de análise existente aproveitando os novos recursos ricos em recursos agora disponíveis por meio do espaço de trabalho Synapse e do estúdio.   

## <a name="prerequisites"></a>Pré-requisitos
Antes de habilitar os recursos do espaço de trabalho do Synapse no seu data warehouse, você deve garantir que tenha o seguinte
- Direitos para criar e gerenciar os recursos do SQL hospedados no servidor lógico do SQL.
- Direitos para criar recursos do Azure Synapse.
- Um administrador de Azure Active Directory identificado no servidor lógico

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Habilitando recursos de espaço de trabalho Synapse para um pool SQL dedicado existente (antigo SQL DW)

Os recursos de espaço de trabalho do Synapse podem ser habilitados em qualquer pool SQL dedicado existente (anteriormente SQL DW) em uma região com suporte. Essa funcionalidade só está disponível por meio do portal do Azure.

Siga estas etapas para criar um espaço de trabalho Synapse para seu data warehouse existente.
1. Selecione um pool SQL dedicado existente (anteriormente conhecido como SQL DW) e abra a página Visão geral.
2. Selecione **novo espaço de trabalho Synapse** na barra de menus superior ou a mensagem imediatamente abaixo.
3. Depois de examinar a lista de data warehouses disponibilizados por meio do novo espaço de trabalho Synapse na página **criar novo espaço de trabalho Synapse do Azure** . Selecione **continuar** para continuar.
4. Na página noções básicas, o pool SQL dedicado existente (a seção detalhes do **projeto** deve ser preenchida previamente com a mesma **assinatura** e **grupo de recursos** implantado no servidor lógico. E, em **detalhes do espaço de trabalho**, o nome do espaço de **trabalho** é preenchido previamente com o mesmo nome e região do servidor lógico do SQL para garantir que a conexão entre o novo espaço de trabalho Synapse e o servidor lógico possa ser criada durante o processo de provisionamento. Após o provisionamento, essa conexão deve ser mantida para garantir o acesso contínuo às instâncias do pool de SQL dedicado (anteriormente conhecido como SQL DW) por meio do espaço de trabalho e do estúdio.
5. Navegue até Selecionar Data Lake Storage Gen 2.
6. Selecione **criar novo** ou selecione um **Data Lake Storage Gen2** existente antes de selecionar **Avançar: rede**.
7. Escolha uma **senha de administrador do SQL** para sua **instância sem servidor**. A alteração dessa senha não atualiza nem altera as credenciais do SQL do servidor lógico. Se você preferir uma senha definida pelo sistema, deixe esses campos vazios. Essa senha pode ser alterada a qualquer momento no novo espaço de trabalho. O nome do administrador deve ser o mesmo usado no SQL Server.
8. Selecione suas **configurações de rede** preferenciais e selecione **revisar + criar** para iniciar o provisionamento de espaço de trabalho.
9. Selecione **ir** para o recurso para abrir o novo espaço de trabalho.

    > [!NOTE]
    > Todas as instâncias do pool de SQL dedicado (anteriormente conhecido como SQL DW) hospedadas no servidor lógico estão disponíveis por meio do novo espaço de trabalho.

## <a name="post-provisioning-steps"></a>Etapas de provisionamento posteriores
As etapas a seguir devem ser concluídas para garantir que as instâncias existentes do pool SQL dedicado (antes do SQL DW) possam ser acessadas por meio do Synapse Studio.
1. Na página Visão geral do espaço de trabalho do Synapse, selecione **servidor conectado**. O **servidor conectado** leva você para o servidor lógico do SQL conectado que hospeda seus data warehouses. No menu essencial, selecione **servidor conectado**.
2. Abra **firewalls e redes virtuais** e verifique se o IP do cliente ou um intervalo de IP predeterminado tem acesso ao servidor lógico.
3. Abra **Active Directory admin** e verifique se um administrador do AAD foi definido no servidor lógico.
4. Selecione uma das instâncias do pool de SQL dedicado (anteriormente conhecido como SQL DW) hospedadas no servidor lógico. Na página Visão geral, selecione **iniciar o Synapse Studio** ou acesse a [entrada no Synapse Studio](https://web.azuresynapse.net) e entre no seu espaço de trabalho.

5. Abra o **Hub de dados** e expanda o pool SQL dedicado no Pesquisador de objetos para garantir que você tenha acesso e possa consultar sua data warehouse.

    > [!NOTE] 
    > Um espaço de trabalho conectado pode ser excluído a qualquer momento. Excluir o espaço de trabalho não excluirá o pool de SQL dedicado conectado (antigo SQL DW). O recurso de espaço de trabalho pode ser reabilitado no pool do SQL dedicado (anteriormente conhecido como SQL DW) quando a operação de exclusão for concluída.

## <a name="next-steps"></a>Próximas etapas
Introdução ao [Synapse Workspace and Studio](../get-started.md).
