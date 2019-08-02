---
title: Configurar o LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure AD |Microsoft Docs
description: Configurar o LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: iainfou
ms.openlocfilehash: 1cb9ff70b41b63f4a3e3d45d744037195fdf28a7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473766"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services
Este artigo mostra como você pode habilitar o protocolo LDAPS para seu domínio gerenciado dos Serviços de Domínio do Azure AD. O LDAP Seguro também é conhecido como “LDAP sobre protocolo SSL/TLS”.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:

1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Um **certificado a ser usado para habilitar o LDAP seguro**.

   * **Recomendado** - Obtenha um certificado da uma autoridade de certificação pública confiável. Essa opção de configuração é a mais segura.
   * Como alternativa, você também pode optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) como mostrado neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Obtenha um certificado válido que esteja de acordo com as diretrizes a seguir, antes de habilitar o LDAP seguro. Você encontrará falhas se tentar habilitar o LDAP seguro para seu domínio gerenciado com um certificado inválido/incorreto.

1. **Emissor confiável** – o certificado deve ser emitido por uma autoridade confiável para os computadores que se conectarem ao domínio gerenciado usando LDAP seguro. Essa autoridade pode ser uma autoridade de certificação (CA) pública de confiança nesses computadores.
2. **Tempo de vida** : o certificado deve ser válido por, pelo menos, os próximos três a seis meses. O acesso LDAP seguro para seu domínio gerenciado é interrompido quando o certificado expira.
3. **Nome da entidade**: o nome da entidade no certificado deve ser seu domínio gerenciado. Por exemplo, se o seu domínio tiver o nome 'contoso100.com', o nome do assunto do certificado deverá ser 'contoso100.com'. Defina o nome DNS (nome alternativo do assunto) como um nome curinga para o seu domínio gerenciado.
4. **Uso de chave** : o certificado deve ser configurado para estas utilizações - assinaturas digitais e codificação de chave.
5. **Finalidade do certificado** : o certificado deve ser válido para autenticação de servidor SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 – obter um certificado para LDAP seguro
A primeira tarefa envolve a obtenção de um certificado que será usado para acesso LDAP seguro ao domínio gerenciado. Você tem duas opções:

* Obtenha um certificado de uma CA pública ou CA corporativa.
* Crie um certificado autoassinado.

> [!NOTE]
> Computadores cliente que precisam se conectar ao domínio gerenciado usando o LDAP seguro devem confiar no emissor do certificado LDAP seguro.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção A (recomendada) - obter um certificado LDAP seguro de uma autoridade de certificação
Se sua organização obtiver seus certificados de uma CA pública, obtenha o certificado LDAP seguro da CA pública. Se você implantar uma CA corporativa, obtenha o certificado LDAP seguro da CA corporativa.

> [!TIP]
> **Use certificados autoassinados para domínios gerenciados com sufixos de domínio '.onmicrosoft.com'.**
> Se o nome de domínio DNS do seu domínio gerenciado terminar em '.onmicrosoft.com', não será possível obter um certificado LDAP seguro de uma autoridade de certificação pública. Como a Microsoft detém o domínio 'onmicrosoft.com', as autoridades de certificação pública se recusam a emitir um certificado LDAP seguro para você para um domínio com esse sufixo. Nesse cenário, crie um certificado autoassinado e use-o para configurar o LDAP seguro.
>

Certifique-se de que o certificado obtido da autoridade de certificação pública atenda a todos os requisitos descritos em [requisitos para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para LDAP seguro
Se você não pretende usar um certificado de uma autoridade de certificação pública, crie um certificado autoassinado para LDAP seguro. Escolha esta opção se o nome de domínio DNS do seu domínio gerenciado terminar em '.onmicrosoft.com'.

**Criar um certificado autoassinado usando o PowerShell**

No computador Windows, abra uma nova janela do PowerShell como **Administrador** e digite os comandos a seguir para criar um novo certificado autoassinado.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

No exemplo anterior, substitua "contoso100.com" pelo nome do domínio DNS de seu domínio gerenciado. Por exemplo, se você tiver criado um domínio gerenciado chamado "contoso100.onmicrosoft.com", substitua "contoso100.com" no atributo Subject por "contoso100.onmicrosoft.com" e " *.contoso100.com" no atributo DnsName por "* .contoso100.onmicrosoft.com").

![Selecionar um diretório do AD do Azure](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado recém-criado é colocado no repositório de certificados do computador local.


## <a name="next-step"></a>Próxima etapa
[Tarefa 2 – exportar o certificado LDAP seguro para um arquivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
