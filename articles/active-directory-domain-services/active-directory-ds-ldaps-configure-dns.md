---
title: Configurar o DNS para acessar um domínio gerenciado usando LDAPS pela internet | Microsoft Docs
description: Configurar o DNS para acessar um domínio gerenciado do Azure AD Domain Services usando LDAPS pela internet
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 1c5a119c686bc879b11b8fa87a9c81ba36b0beb4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474020"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Configurar o DNS para acessar um domínio gerenciado do Azure AD Domain Services usando LDAP (LDAPS) pela internet

## <a name="before-you-begin"></a>Antes de começar
Completar [Tarefa 3 – habilitar o LDAP seguro para o domínio gerenciado usando o portal do Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 4: Configurar o DNS para acessar o domínio gerenciado pela internet
> [!TIP]
> **Tarefa opcional** – Ignore esta tarefa de configuração se você não pretende acessar o domínio gerenciado usando LDAPS pela Internet.
>
>

Antes de iniciar esta tarefa, conclua as etapas descritas na [Tarefa 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Depois de habilitar o acesso LDAP seguro pela Internet, será necessário atualizar o DNS para que os computadores cliente possam localizar esse domínio gerenciado. Você verá um endereço IP externo na guia **Propriedades** em **ENDEREÇO IP EXTERNO PARA ACESSO a LDAPS**.

Configure seu provedor DNS externo para que o nome DNS do domínio gerenciado (por exemplo, 'ldaps.contoso100.com') aponte para esse endereço IP externo. Por exemplo, crie a seguinte entrada DNS:

    ldaps.contoso100.com  -> 52.165.38.113

É isso! Agora você está pronto para se conectar ao domínio gerenciado usando LDAP seguro pela Internet.

> [!WARNING]
> Lembre-se de que os computadores cliente devem confiar no emissor do certificado LDAPS para poderem se conectar com êxito ao domínio gerenciado usando LDAPS. Se estiver usando uma autoridade de certificação confiável publicamente, você não precisará fazer nada, pois os computadores cliente confiarão nesses emissores de certificado. Se estiver usando um certificado autoassinado, instale a parte pública do certificado autoassinado no repositório de certificados confiáveis no computador cliente.
>
>

## <a name="next-step"></a>Próxima etapa
[Tarefa 5: associar ao domínio gerenciado e bloquear o acesso LDAP seguro](active-directory-ds-ldaps-bind-lockdown.md)
