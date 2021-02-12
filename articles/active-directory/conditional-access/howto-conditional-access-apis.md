---
title: APIs de acesso condicional e PowerShell-Azure Active Directory
description: Usando as APIs de acesso condicional do Azure AD e o PowerShell para gerenciar políticas como código
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860206"
---
# <a name="conditional-access-programmatic-access"></a>Acesso condicional: acesso programático

Muitas organizações expressam sua necessidade de gerenciar o máximo de seus ambientes, como o código possível. Usando Microsoft Graph você pode tratar políticas de acesso condicional como qualquer outra parte do código em seu ambiente.

O Microsoft Graph fornece um modelo de programação unificado que as organizações podem usar para interagir com os dados no Microsoft 365, no Windows 10 e no Enterprise Mobility + Security. Para obter mais informações sobre Microsoft Graph, consulte o artigo [visão geral do Microsoft Graph](/graph/overview).

![Uma imagem que mostra os principais recursos e relações que fazem parte do grafo](./media/howto-conditional-access-apis/microsoft-graph.png)

Os exemplos a seguir são fornecidos como estão sem suporte. Você pode usar esses exemplos como base para ferramentas em sua organização. 

Muitos dos exemplos a seguir usam ferramentas como [identidades gerenciadas](../managed-identities-azure-resources/overview.md), [aplicativos lógicos](../../logic-apps/logic-apps-overview.md), [onedrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [equipes](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)e [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Configurar

### <a name="powershell"></a>PowerShell

Para muitos administradores, o PowerShell já é uma ferramenta de script compreendida. O exemplo a seguir mostra como usar o [módulo do PowerShell do Azure ad](https://www.powershellgallery.com/packages/AzureAD) para gerenciar políticas de acesso condicional.

- [Configurar políticas de acesso condicional com comandos do PowerShell do Azure AD](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>API do Graph

Este exemplo mostra as opções CRUD (criação, leitura, atualização e exclusão) básicas disponíveis nas APIs do grafo de acesso condicional. O exemplo também inclui alguns modelos JSON que você pode usar para criar algumas políticas de exemplo.

- [Configurar políticas de acesso condicional com chamadas à API de Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Configurar usando modelos

Use APIs de acesso condicional para implantar políticas de acesso condicional em seu ambiente de pré-produção usando um modelo.

- [Configurar políticas de acesso condicional com modelos de API de Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Teste

Este exemplo modela práticas de implantação mais seguras com fluxos de trabalho de aprovação que podem copiar políticas de acesso condicional de um ambiente, como pré-produção, para outro, como seu ambiente de produção.

- [Promover políticas de acesso condicional de ambientes de teste](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Implantar

Este exemplo fornece um mecanismo para executar políticas de acesso condicional de implantação em etapas gradualmente para sua população de usuário, permitindo que você gerencie o impacto de suporte e os problemas pontuais no início.

- [Implantar políticas de acesso condicional para ambientes de produção com fluxos de trabalho de aprovação](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitoramento

Este exemplo fornece um mecanismo para monitorar as alterações de política de acesso condicional ao longo do tempo e pode disparar alertas quando as políticas de chave são alteradas.

- [Monitorar políticas de acesso condicional implantadas para alterações e disparar alertas](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Gerenciar

### <a name="backup-and-restore"></a>Backup e restauração

Automatize o backup e a restauração de políticas de acesso condicional com aprovações em equipes usando este exemplo.

- [Gerenciar o processo de backup e restauração de políticas de acesso condicional usando chamadas à API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Contas de acesso de emergência

Vários administradores podem criar políticas de acesso condicional e podem esquecer de adicionar suas [contas de acesso de emergência](../roles/security-emergency-access.md) como uma exclusão para essas políticas. Este exemplo garante que todas as políticas sejam atualizadas para incluir suas contas de acesso de emergência designadas.

- [Gerenciar a atribuição de contas de acesso de emergência a políticas de acesso condicional usando chamadas à API de Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Planejamento de Contingência

As coisas nem sempre funcionam da maneira desejada, quando isso acontece, você precisa de uma maneira de voltar a um estado em que o trabalho possa continuar. O exemplo a seguir fornece uma maneira de reverter suas políticas para um plano de contingência bom conhecido e desabilitar outras políticas de acesso condicional.

- [Gerenciar a ativação de políticas de contingência de acesso condicional usando chamadas à API de Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Contribuição da Comunidade

Esses exemplos estão disponíveis em nosso [repositório GitHub](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Estamos felizes em dar suporte a contribuições de comunidade com problemas e solicitações de pull completos do GitHub.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Microsoft Graph](/graph/overview)

- [API de acesso condicional](/graph/api/resources/conditionalaccesspolicy)

- [API de localização nomeada](/graph/api/resources/namedlocation)
