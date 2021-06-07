---
title: Entendendo Azure AD Connect 1.4. XX. x e desaparecem o dispositivo | Microsoft Docs
description: Este documento descreve um problema que surge com a versão 1.4. XX. x de Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "73176032"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Entendendo Azure AD Connect 1.4. XX. x e desaparecem o dispositivo
Com a versão 1.4. XX. x de Azure AD Connect, alguns clientes podem ver que alguns ou todos os seus dispositivos Windows desaparecem do Azure AD. Isso não é motivo de preocupação, pois essas identidades de dispositivo não são usadas pelo Azure AD durante a autorização de Acesso Condicional. Essa alteração não excluirá nenhum dispositivo Windows registrado corretamente com o Azure AD para ingresso híbrido no Azure AD.

Se você vir a exclusão de objetos de dispositivo no Azure AD excedendo o limite de exclusão de exportação, é recomendável que o cliente permita que as exclusões passem. [Como: permitir que as exclusões fluam quando excederem o limite de exclusão](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Segundo plano
Os dispositivos Windows registrados como ingressados no Azure AD híbrido são representados no Azure AD como objetos de dispositivo. Esses objetos de dispositivo podem ser usados para acesso condicional. Os dispositivos Windows 10 são sincronizados com a nuvem por meio de Azure AD Connect, os dispositivos Windows de nível inferior são registrados diretamente usando AD FS ou logon único contínuo.

## <a name="windows-10-devices"></a>Dispositivos Windows 10
Somente dispositivos Windows 10 com um valor de atributo usercertificar específico configurado pelo ingresso híbrido do Azure AD devem ser sincronizados com a nuvem pelo Azure AD Connect. Em versões anteriores do Azure AD Connect esse requisito não era rigorosamente imposto, resultando em objetos de dispositivo desnecessários no Azure AD. Esses dispositivos no Azure AD sempre permaneceram no estado "pendente" porque esses dispositivos não se destinam a serem registrados com o Azure AD. 

Esta versão do Azure AD Connect sincronizará somente dispositivos Windows 10 que estão configurados corretamente para serem ingressados no Azure AD híbrido. Os objetos de dispositivo do Windows 10 sem o ingresso do Azure AD específico de userCertificate serão removidos do Azure AD.

## <a name="down-level-windows-devices"></a>Down-Level dispositivos Windows
Azure AD Connect nunca deve estar sincronizando [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Todos os dispositivos no Azure AD anteriormente sincronizados incorretamente agora serão excluídos do Azure AD. Se Azure AD Connect estiver tentando excluir [dispositivos Windows de nível inferior](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), o dispositivo não será aquele que foi criado pelo [Microsoft Workplace Join para MSI de computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) e não poderá ser consumido por nenhum outro recurso do Azure AD.

Alguns clientes talvez precisem revisitar [como: planejar sua implementação de junção de Azure Active Directory híbrida](../devices/hybrid-azuread-join-plan.md) para colocar seus dispositivos Windows registrados corretamente e garantir que esses dispositivos possam participar totalmente do acesso condicional com base no dispositivo. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Como posso verificar quais dispositivos são excluídos com esta atualização?

Para verificar quais dispositivos são excluídos, você pode usar este script do PowerShell: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Esse script gera um relatório sobre certificados armazenados em Active Directory objetos de computador, especificamente, certificados emitidos pelo recurso de ingresso híbrido do Azure AD.
Ele verifica os certificados presentes na propriedade userCertificate de um objeto Computer no AD e, para cada certificado não expirado presente, valida se o certificado foi emitido para o recurso de ingresso híbrido do Azure AD (ou seja, o nome da entidade corresponde a CN = {objectGUID}).
Antes, Azure AD Connect seria sincronizado com o Azure AD qualquer computador que continha pelo menos um certificado válido, mas começando na versão 1,4 do Azure AD Connect, o mecanismo de sincronização pode identificar certificados de ingresso do Azure AD híbridos e ' cloudfilter ' o objeto de computador da sincronização para o Azure AD, a menos que haja um certificado de ingresso híbrido do Azure AD válido.
Os dispositivos do Azure AD que já foram sincronizados com o AD, mas que não têm um certificado de ingresso híbrido do Azure AD válido serão excluídos (CloudFiltered = TRUE) pelo mecanismo de sincronização.

## <a name="next-steps"></a>Próximas etapas
- [Histórico de versão do Azure AD Connect](reference-connect-version-history.md)
