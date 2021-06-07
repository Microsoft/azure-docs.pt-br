---
title: Redefinição de senha por autoatendimento da licença – Azure Active Directory
description: Saiba mais sobre a diferença Azure Active Directory requisitos de licenciamento de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952364"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisitos de licenciamento para Azure Active Directory redefinição de senha de autoatendimento

Para reduzir as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo, as contas de usuário no Azure Active Directory (Azure AD) podem ser habilitadas para SSPR (redefinição de senha de autoatendimento). Os recursos que compõem SSPR incluem alteração de senha, redefinição, desbloqueio e Write-back para um diretório local. Os recursos básicos do SSPR estão disponíveis no Microsoft 365 Business Standard ou superior e em todos os Azure AD Premium SKUs sem nenhum custo.

Este artigo detalha as diferentes maneiras pelas quais a redefinição de senha de autoatendimento pode ser licenciada e usada. Para obter detalhes específicos sobre preços e cobrança, consulte a [página de preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Comparar edições e recursos

SSPR requer uma licença somente para o locatário. 

A tabela a seguir descreve os diferentes cenários de SSPR para alteração de senha, redefinição ou Write-back local e quais SKUs fornecem o recurso.

| Recurso | AD do Azure Gratuito | Microsoft 365 Business Standard | Microsoft 365 Business Premium | O Azure AD Premium P1 ou P2 |
| --- |:---:|:---:|:---:|:---:|
| **Alteração de senha de usuário somente em nuvem**<br />Quando um usuário no Azure AD sabe sua senha e deseja alterá-la para algo novo. | ● | ● | ● | ● |
| **Redefinição de senha de usuário somente em nuvem**<br />Quando um usuário no Azure AD esqueceu sua senha e precisa redefini-la. | | ● | ● | ● |
| **Alteração ou redefinição de senha de usuário híbrido com write-back local**<br />Quando um usuário no Azure AD é sincronizado de um diretório local usando Azure AD Connect deseja alterar ou redefinir sua senha e também gravar a nova senha no local. | | | ● | ● |

> [!WARNING]
> Os planos de licenciamento básico e padrão Microsoft 365 autônomos não dão suporte a SSPR com write-back local. O recurso de write-back local requer Azure AD Premium P1, Premium P2 ou Microsoft 365 Business Premium.

Para obter informações adicionais sobre licenciamento, incluindo custos, consulte as seguintes páginas:

* [Preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Recursos e funcionalidades do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: habilitar redefinição de senha de autoatendimento (SSPR)](tutorial-enable-sspr.md)