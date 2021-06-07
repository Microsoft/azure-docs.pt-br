---
title: Amostras do PowerShell para gerenciamento de aplicativos do Azure Active Directory
description: Estas amostras do PowerShell são usadas para aplicativos que você gerencia em seu locatário do Azure Active Directory. Você pode usar estes scripts de amostra para encontrar informações de validade sobre segredos e certificados.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378815"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Amostras do PowerShell do Azure Active Directory para gerenciamento de aplicativos

A tabela a seguir inclui links para amostras de scripts do PowerShell para gerenciamento de aplicativos do Azure AD. Essas amostras exigem:
- O [módulo PowerShell do Azure AD V2 para Graph](/powershell/azure/active-directory/install-adv2) ou,
- A [versão prévia do módulo PowerShell do Azure AD V2 para Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), salvo indicação em contrário.

Para saber mais sobre os cmdlets usados nessas amostras, confira [Aplicativos](/powershell/module/azuread/#applications).

| Link | Descrição |
|---|---|
|**Scripts de gerenciamento de aplicativos**||
| [Exportar segredos e certificados (registros de aplicativo)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exporte segredos e certificados para registros de aplicativo no locatário do Azure Active Directory. |
| [Exportar segredos e certificados (aplicativos empresariais)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exporte segredos e certificados para aplicativos empresariais no locatário do Azure Active Directory. |
| [Exportar segredos e certificados expirados](scripts/powershell-export-apps-with-expriring-secrets.md) | Exporte registros de aplicativo com segredos e certificados expirados e os respectivos proprietários no locatário do Azure Active Directory. |
| [Exportar segredos e certificados que expiram além da data necessária](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exporte Registros de Aplicativo com segredos e certificados com expiração posterior à data exigida no locatário do Azure Active Directory. Isso usa o fluxo OAuth não interativo Client_Credentials. |
