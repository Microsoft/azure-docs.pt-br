---
title: Data vazamento Protection para espaços de trabalho do Azure Synapse Analytics
description: Este artigo explicará a proteção de dados vazamento no Azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518253"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Data vazamento Protection para espaços de trabalho do Azure Synapse Analytics
Este artigo explicará a proteção de dados vazamento no Azure Synapse Analytics

## <a name="securing-data-egress-from-synapse-workspaces"></a>Protegendo os dados de saída de espaços de trabalho do Synapse
Espaços de trabalho do Azure Synapse Analytics dão suporte à habilitação da proteção de vazamento de dados para espaços de trabalho. Com a proteção do vazamento, você pode se proteger contra pessoas mal-intencionadas que acessam seus recursos do Azure e invasão dados confidenciais para locais fora do escopo da sua organização. No momento da criação do espaço de trabalho, você pode optar por configurar o espaço de trabalho com uma rede virtual gerenciada e proteção adicional contra vazamento de dados. Quando um espaço de trabalho é criado com uma [rede virtual gerenciada](./synapse-workspace-managed-vnet.md), a integração de dados e os recursos do Spark são implantados na rede virtual gerenciada. Os pools SQL dedicados do espaço de trabalho e os pools SQL sem servidor têm recursos de vários locatários e, como tal, precisam existir fora da rede virtual gerenciada. Para espaços de trabalho com proteção de vazamento de dados, os recursos na rede virtual gerenciada sempre se comunicam por pontos de extremidade [privados gerenciados](./synapse-workspace-managed-private-endpoints.md) e os recursos SQL do Synapse só podem se conectar a recursos autorizados do Azure (destinos de conexões de ponto de extremidade privadas gerenciadas aprovadas do espaço de trabalho). 

>[!Note]
>Você não pode alterar a configuração do espaço de trabalho para rede virtual gerenciada e proteção vazamento de dados depois que o espaço de trabalho é criado.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Gerenciando saída de dados de espaço de trabalho Synapse para destinos aprovados
Depois que o espaço de trabalho é criado com o vazamento de dados habilitado, os proprietários do recurso de espaço de trabalho podem gerenciar a lista de locatários do Azure AD aprovados para o espaço de trabalho. Os usuários com as [permissões corretas](./synapse-workspace-access-control-overview.md) no espaço de trabalho podem usar o Synapse Studio para criar solicitações de conexão de ponto de extremidade privado gerenciado para recursos nos locatários do Azure ad aprovados do espaço de trabalho. A criação do ponto de extremidade privado gerenciado será bloqueada se o usuário tentar criar uma conexão de ponto de extremidade privada para um recurso em um locatário não aprovado.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Espaço de trabalho de exemplo com a proteção de vazamento de dados habilitada
Vamos usar um exemplo para ilustrar a proteção de vazamento de dados para espaços de trabalho do Synapse. A contoso tem recursos do Azure no locatário A e no locatário B e é necessário que esses recursos se conectem com segurança. Um espaço de trabalho Synapse foi criado no locatário A com o locatário B adicionado como um locatário do Azure AD aprovado. O diagrama mostra as conexões de ponto de extremidade privado com as contas de armazenamento do Azure no locatário A e o locatário B que foram aprovados pelos proprietários da conta de armazenamento. O diagrama também mostra a criação de ponto de extremidade particular bloqueado. A criação desse ponto de extremidade privado foi bloqueada como direcionada a uma conta de armazenamento do Azure no locatário do Azure AD da Fabrikam, que não é um locatário do Azure AD aprovado para o espaço de trabalho da contoso. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Este diagrama mostra como a proteção de vazamento de dados é implementada para espaços de trabalho do Synapse":::

>[!IMPORTANT]
>Os recursos em locatários diferentes do locatário do espaço de trabalho não devem bloquear as regras de firewall em vigor para que os pools do SQL se conectem a eles. Os recursos dentro da rede virtual gerenciada do espaço de trabalho, como clusters do Spark, podem se conectar por meio de links privados gerenciados para recursos protegidos por firewall.
## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um espaço de trabalho com a proteção de vazamento de dados habilitada](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Saiba mais sobre a [rede virtual do espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
