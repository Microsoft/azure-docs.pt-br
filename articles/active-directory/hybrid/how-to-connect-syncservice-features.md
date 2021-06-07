---
title: Recursos e configurações do serviço de sincronização do Azure AD Connect | Microsoft Docs
description: Descreve os recursos do serviço de sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410531"
---
# <a name="azure-ad-connect-sync-service-features"></a>Recursos do serviço de sincronização do Azure AD Connect

O recurso de sincronização do Azure AD Connect tem dois componentes:

* O componente local denominado **Sincronização do Azure AD Connect**, também chamado de **mecanismo de sincronização**.
* O serviço que reside no Azure AD, também conhecido como **Serviço de sincronização do Azure AD Connect**

Este tópico explica como os seguintes recursos do **Serviço de sincronização do Azure AD Connect** funcionam e como você pode configurá-los usando o Windows PowerShell.

Estas configurações são definidas pelo [Módulo do Azure Active Directory para Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)). Baixe e instale-o separadamente do Azure AD Connect. Os cmdlets documentados neste tópico foram introduzidos na [versão de março de 2016 (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se você não tiver os cmdlets documentados neste tópico ou se eles não produzirem o mesmo resultado, certifique-se de executar a versão mais recente.

Para ver a configuração em seu diretório do Azure AD, execute `Get-MsolDirSyncFeatures`.  
![Resultado de Get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Muitas dessas configurações podem ser alteradas somente pelo Azure AD Connect.

As configurações a seguir podem ser definidas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Permite que os objetos se unam em userPrincipalName além do endereço SMTP primário. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Permite que o mecanismo de sincronização atualize o atributo userPrincipalName para usuários gerenciados/licenciados (não federados). |

Depois que você habilitar um recurso, ele não poderá ser desabilitado novamente.

> [!NOTE]
> A partir de 24 de agosto de 2016 o recurso *Duplicar a resiliência do atributo* é habilitado por padrão para novos diretórios do Azure AD. Este recurso também será distribuído e habilitado em diretórios criados antes dessa data. Você receberá uma notificação por email quando o diretório estiver prestes a habilitar esse recurso.
> 
> 

As configurações a seguir são definidas pelo Azure AD Connect e não podem ser modificadas por `Set-MsolDirSyncFeature`:

| DirSyncFeature | Comentário |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: habilitar o write-back de dispositivo](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Sincronização do Azure AD Connect: extensões de diretório](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Permite que um atributo seja colocado em quarentena quando ele é uma duplicata de outro objeto, em vez de causar falha de todo o objeto durante a exportação. |
| Sincronização de hash de senha |[Implementação de sincronização de hash de senha com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Autenticação de Passagem|[Entrada do usuário com autenticação de passagem do Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Write-back de grupo|
| UserWriteback |Sem suporte no momento. |

## <a name="duplicate-attribute-resiliency"></a>Duplicar a resiliência do atributo

Em vez de falhar ao provisionar objetos com UPNs/proxyAddresses duplicados, o atributo duplicado é "colocado em quarentena" e um valor temporário é atribuído. Quando o conflito é resolvido, o UPN temporário é alterado para o valor correto automaticamente. Para obter mais detalhes, consulte [Sincronização de identidades e resiliência do atributo duplicado](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondência suave de UserPrincipalName

Quando este recurso é habilitado, a correspondência suave é habilitada para UPN além do [endereço SMTP primário](https://support.microsoft.com/kb/2641663), que está sempre habilitado. A correspondência suave é usada para fazer a correspondência de usuários na nuvem existentes no Azure AD com usuários locais.

Se você precisar corresponder as contas AD locais com contas existentes criadas na nuvem e você não estiver usando o Exchange Online, este recurso será útil. Nesse cenário, você normalmente não tem um motivo para definir o atributo SMTP na nuvem.

O recurso fica ativado por padrão para diretórios recém-criados do Azure AD. Você pode ver se este recurso está habilitado executando:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se o recurso não estiver habilitado para seu diretório do Azure AD, você poderá habilitá-lo executando:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar atualizações de userPrincipalName

Historicamente, atualizações do atributo UserPrincipalName usando o serviço de sincronização local são bloqueadas, a menos que estas duas condições sejam verdadeiras:

* O usuário é gerenciado (não federado).
* Não foi atribuída uma licença ao usuário.

> [!NOTE]
> A partir de março de 2019, é permitida a sincronização de alterações de UPN para contas de usuário federadas.
> 

Habilitar o recurso permite que o mecanismo de sincronização atualize o userPrincipalName quando ele é alterado localmente e você usa a sincronização de hash da senha ou autenticação de passagem.

O recurso fica ativado por padrão para diretórios recém-criados do Azure AD. Você pode ver se este recurso está habilitado executando:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se o recurso não estiver habilitado para seu diretório do Azure AD, você poderá habilitá-lo executando:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de habilitar esse recurso, os valores existentes de userPrincipalName permanecerão os mesmos. Na próxima alteração do atributo userPrincipalName local, a sincronização delta normal dos usuários atualizará o UPN.  

## <a name="see-also"></a>Confira também

* [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).