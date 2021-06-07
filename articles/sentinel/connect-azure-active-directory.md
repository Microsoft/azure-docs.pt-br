---
title: Conectar dados de Azure Active Directory ao Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados de Azure Active Directory e transmitir logs de entrada, auditoria e provisionamento do Azure AD para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99251974"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Conectar dados do Azure Active Directory (Azure AD) ao Azure Sentinel

Você pode usar o conector interno do Sentinela do Azure para coletar dados de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-los para o Azure Sentinel. O conector permite que você transmita os seguintes tipos de log:

- [**Logs de entrada**](../active-directory/reports-monitoring/concept-all-sign-ins.md), que contêm informações sobre [entradas de usuário interativas](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) , em que um usuário fornece um fator de autenticação.

    O conector do AD do Azure agora inclui as três seguintes categorias adicionais de logs de entrada, tudo em **Visualização** no momento:
    
    - [**Logs de entrada de usuário não interativos**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), que contêm informações sobre entradas executadas por um cliente em nome de um usuário sem qualquer fator de interação ou autenticação do usuário.
    
    - [**Logs de entrada da entidade de serviço**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), que contêm informações sobre entradas por aplicativos e entidades de serviço que não envolvem nenhum usuário. Nessas entradas, o aplicativo ou serviço fornece uma credencial em seu próprio nome para autenticar ou acessar recursos.
    
    - [**Logs de entrada de identidade gerenciada**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), que contêm informações sobre entradas por recursos do Azure que têm segredos gerenciados pelo Azure. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Logs de auditoria**](../active-directory/reports-monitoring/concept-audit-logs.md), que contêm informações sobre atividade do sistema relacionadas ao gerenciamento de usuários e grupos, aplicativos gerenciados e atividades de diretório.

- [**Provisionando logs**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (também na versão **prévia**), que contêm informações de atividade do sistema sobre usuários, grupos e funções provisionados pelo serviço de provisionamento do Azure AD. 

> [!IMPORTANT]
> Como indicado acima, alguns dos tipos de log disponíveis estão atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
## <a name="prerequisites"></a>Pré-requisitos

- Qualquer licença do Azure AD (gratuito/O365/P1/P2) é suficiente para ingerir os logs de entrada no Sentinela do Azure. Encargos adicionais por gigabyte podem ser aplicados por Azure Monitor (Log Analytics) e pelo Azure Sentinel.

- O usuário deve receber a função colaborador do Azure Sentinel no espaço de trabalho.

- O usuário deve receber as funções de administrador global ou de administrador de segurança no locatário do qual você deseja transmitir os logs.

- Seu usuário deve ter permissões de leitura e gravação para as configurações de diagnóstico do Azure AD a fim de poder ver o status da conexão. 

## <a name="connect-to-azure-active-directory"></a>Conectar ao Active Directory do Azure

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Na Galeria de conectores de dados, selecione **Azure Active Directory** e, em seguida, selecione a **página abrir conector**.

1. Marque as caixas de seleção ao lado dos tipos de log que você deseja transmitir para o Azure Sentinel (veja acima) e clique em **conectar**.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **LogManagement** , nas tabelas a seguir:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Para consultar os logs do Azure AD, insira o nome da tabela relevante na parte superior da janela de consulta.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Azure Active Directory ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
