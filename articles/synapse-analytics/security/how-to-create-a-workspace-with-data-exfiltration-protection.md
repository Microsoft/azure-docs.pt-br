---
title: Criar um espaço de trabalho com o data vazamento Protection habilitado
description: Este artigo explicará como criar um espaço de trabalho com o data vazamento Protection no Azure Synapse Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598075"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Criar um espaço de trabalho com o data vazamento Protection habilitado
Este artigo descreve como criar um espaço de trabalho com a proteção de vazamento de dados habilitada e como gerenciar os locatários do Azure AD aprovados para este espaço de trabalho.

>[!Note]
>Você não pode alterar a configuração do espaço de trabalho para rede virtual gerenciada e proteção vazamento de dados depois que o espaço de trabalho é criado.

## <a name="prerequisites"></a>Pré-requisitos
- Permissões para criar um recurso de espaço de trabalho no Azure.
- Permissões do espaço de trabalho Synapse para criar pontos de extremidade privados gerenciados.
- Assinaturas registradas para o provedor de recursos de rede. [Saiba mais.](../../azure-resource-manager/management/resource-providers-and-types.md)

Siga as etapas listadas em [início rápido: criar um espaço de trabalho Synapse](../quickstart-create-workspace.md) para começar a criar seu espaço de trabalho. Antes de criar seu espaço de trabalho, use as informações abaixo para adicionar a proteção de vazamento de dados ao seu espaço de trabalho.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Adicionar o data vazamento Protection ao criar seu espaço de trabalho
1. Na guia rede, marque a caixa de seleção "Habilitar rede virtual gerenciada".
1. Selecione "Sim" para a opção "permitir tráfego de dados de saída somente para destinos aprovados".
1. Escolha os locatários aprovados do Azure AD para este espaço de trabalho.
1. Examine a configuração e crie o espaço de trabalho.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Captura de tela que mostra um espaço de trabalho criar Synapse com ' habilitar gerenciar rede virtual ' selecionada.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Gerenciar locatários de Azure Active Directory aprovados para o espaço de trabalho
1. Na portal do Azure do espaço de trabalho, navegue até "locatários aprovados do Azure AD". A lista de locatários aprovados do Azure AD para o espaço de trabalho será listada aqui. O locatário do espaço de trabalho é incluído por padrão e não está listado.
1. Use "+ Adicionar" para incluir novos locatários na lista aprovada.
1. Para remover um locatário do Azure AD da lista aprovada, selecione o locatário e selecione "excluir" e, em seguida, "salvar".
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Criar um espaço de trabalho com o data vazamento Protection":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Conectando-se aos recursos do Azure em locatários aprovados do Azure AD

Você pode criar pontos de extremidade privados gerenciados para se conectar aos recursos do Azure que residem em locatários do Azure AD, que são aprovados para um espaço de trabalho. Siga as etapas listadas no guia para [criar pontos de extremidade privados gerenciados](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Os recursos em locatários diferentes do locatário do espaço de trabalho não devem bloquear as regras de firewall em vigor para que os pools do SQL se conectem a eles. Os recursos dentro da rede virtual gerenciada do espaço de trabalho, como clusters do Spark, podem se conectar por meio de links privados gerenciados para recursos protegidos por firewall.

## <a name="known-limitations"></a>Limitações conhecidas
Os usuários podem fornecer um arquivo de configuração de ambiente para instalar pacotes python de repositórios públicos, como PyPI. Em espaços de trabalho protegidos por data vazamento, as conexões com repositórios de saída são bloqueadas. Como resultado, a biblioteca do Python instalada a partir de repositórios públicos, como PyPI, não é suportada. 

Como alternativa, os usuários podem carregar pacotes de espaço de trabalho ou criar um canal privado dentro de sua conta de Azure Data Lake Storage primária. Para obter mais informações, visite [Gerenciamento de pacotes no Azure Synapse Analytics](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) 
  
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [proteção de vazamento de dados em espaços de trabalho do Synapse](./workspace-data-exfiltration-protection.md)

Saiba mais sobre a [rede virtual do espaço de trabalho gerenciado](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [Pontos de extremidade privados gerenciados](./synapse-workspace-managed-private-endpoints.md)

[Criar Pontos de extremidade privados gerenciados para suas fontes de dados](./how-to-create-managed-private-endpoints.md)
