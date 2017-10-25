---
title: "Configurar o LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure AD |Microsoft Docs"
description: "Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.openlocfilehash: 93afa49166c5b31d23237c308b9d34f6d6f3507d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services
Este artigo mostra como você pode habilitar o protocolo LDAPS para seu domínio gerenciado dos Serviços de Domínio do Azure AD. O LDAP Seguro também é conhecido como “LDAP sobre protocolo SSL/TLS”.

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](active-directory-ds-getting-started.md).
4. Um **certificado a ser usado para habilitar o LDAP seguro**.

   * **Recomendado** - Obtenha um certificado da uma autoridade de certificação pública confiável. Essa opção de configuração é a mais segura.
   * Como alternativa, você também pode optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) como mostrado neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Obtenha um certificado válido que esteja de acordo com as diretrizes a seguir, antes de habilitar o LDAP seguro. Você encontrará falhas se tentar habilitar o LDAP seguro para seu domínio gerenciado com um certificado inválido/incorreto.

1. **Emissor confiável** – o certificado deve ser emitido por uma autoridade confiável para os computadores que se conectarem ao domínio gerenciado usando LDAP seguro. Essa autoridade pode ser uma autoridade de certificação pública de confiança nesses computadores.
2. **Tempo de vida** : o certificado deve ser válido por, pelo menos, os próximos três a seis meses. O acesso LDAP seguro para seu domínio gerenciado é interrompido quando o certificado expira.
3. **Nome da entidade** : o nome da entidade do certificado deve ser um caractere curinga para seu domínio gerenciado. Por exemplo, se o domínio se chamar “contoso100.com”, o nome da entidade do certificado deverá ser “*.contoso100.com”. Defina o nome DNS (nome alternativo da entidade) como esse nome curinga.
4. **Uso de chave** : o certificado deve ser configurado para estas utilizações - assinaturas digitais e codificação de chave.
5. **Finalidade do certificado** : o certificado deve ser válido para autenticação de servidor SSL.

> [!NOTE]
> **Autoridades de Certificação Corporativas:** o Azure AD Domain Services não dá suporte ao uso de certificados LDAP seguros emitidos pela autoridade de certificação corporativa de sua organização. Essa restrição ocorre porque o serviço não confia em sua autoridade de certificação corporativa como uma autoridade de certificação raiz. 
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 – obter um certificado para LDAP seguro
A primeira tarefa envolve a obtenção de um certificado que será usado para acesso LDAP seguro ao domínio gerenciado. Você tem duas opções:

* Obtenha um certificado de uma autoridade de certificação. A autoridade pode ser uma autoridade de certificação pública.
* Crie um certificado autoassinado.

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção A (recomendada) - obter um certificado LDAP seguro de uma autoridade de certificação
Se sua organização obtiver seus certificados de uma autoridade de certificação pública, será necessário obter o certificado LDAP seguro da autoridade de certificação pública.

Ao solicitar um certificado, verifique se você satisfaz todos os requisitos descritos em [Requisitos para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [!NOTE]
> Computadores cliente que precisam se conectar ao domínio gerenciado usando o LDAP seguro devem confiar no emissor do certificado LDAP seguro.
>
>

### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para LDAP seguro
Se você não pretende usar um certificado de uma autoridade de certificação pública, crie um certificado autoassinado para LDAP seguro.

**Criar um certificado autoassinado usando o PowerShell**

No computador Windows, abra uma nova janela do PowerShell como **Administrador** e digite os comandos a seguir para criar um novo certificado autoassinado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

No exemplo anterior, substitua "*.contoso100.com" pelo nome do domínio DNS de seu domínio gerenciado. Por exemplo, se você tiver criado um domínio gerenciado chamado “contoso100.onmicrosoft.com”, substitua “*.contoso100.com” no script acima por “*.contoso100.onmicrosoft.com”).

![Selecionar um diretório do Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado recém-criado é colocado no repositório de certificados do computador local.


## <a name="next-step"></a>Próxima etapa
[Tarefa 2 – exportar o certificado LDAP seguro para um arquivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
