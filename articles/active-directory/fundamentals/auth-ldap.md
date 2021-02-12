---
title: Autenticação LDAP com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação LDAP com Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168705"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Autenticação LDAP com Azure Active Directory

O Lightweight Directory Access Protocol (LDAP) é um protocolo de aplicativo para trabalhar com vários serviços de diretório. Serviços de diretório, como Active Directory, [armazenam informações de usuário e conta](https://www.dnsstuff.com/active-directory-service-accounts)e informações de segurança como senhas. Em seguida, o serviço permite que as informações sejam compartilhadas com outros dispositivos na rede. Aplicativos empresariais, como email, gerentes de relacionamento com o cliente (CRMs) e software de RH (recursos humanos), podem usar LDAP para autenticar, acessar e localizar informações. 

O Azure Active Directory (AD do Azure) dá suporte a esse padrão via Azure AD Domain Services (AD DS). Ele permite que as organizações que estão adotando uma estratégia de primeira nuvem para modernizar seu ambiente, movendo seus recursos LDAP locais para a nuvem. Os benefícios imediatos serão: 

* Integrado ao Azure AD. As inclusões de usuários e grupos ou alterações de atributo em seus objetos são sincronizadas automaticamente do seu locatário do Azure AD para AD DS. As alterações em objetos no local Active Directory são sincronizadas com o Azure AD e, em seguida, para AD DS.

* Simplifique operações. Reduz a necessidade de manter e corrigir manualmente as infraestruturas locais. 

* Confiável. Você obtém serviços gerenciados e altamente disponíveis 

## <a name="use-when"></a>Usar quando

Há a necessidade de um aplicativo ou serviço usar a autenticação LDAP.

![Diagrama de arquitetura](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: acessa aplicativos dependentes de LDAP por meio de um navegador.

* **Navegador da Web**: a interface com a qual o usuário interage para acessar a URL externa do aplicativo.

* **Rede virtual**: uma rede privada no Azure por meio da qual o aplicativo herdado pode consumir serviços LDAP. 

* **Aplicativos herdados**: cargas de trabalho de aplicativos ou servidores que exigem o LDAP implantado em uma rede virtual no Azure ou que têm visibilidade para AD DS IPS de instância por meio de rotas de rede. 

* **Azure ad**: sincroniza informações de identidade do diretório local da organização por meio de Azure ad Connect.

* **Azure AD Domain Services (AD DS)**: executa uma sincronização unidirecional do Azure ad para fornecer acesso a um conjunto central de usuários, grupos e credenciais. A instância de AD DS é atribuída a uma rede virtual. Aplicativos, serviços e VMs no Azure que se conectam à rede virtual atribuída a AD DS podem usar recursos comuns de AD DS, como LDAP, ingresso no domínio, diretiva de grupo, Kerberos e autenticação NTLM.
   > [!NOTE]
   >  Em ambientes em que a organização não pode sincronizar hashes de senha ou os usuários entram usando cartões inteligentes, recomendamos que você use uma floresta de recursos no AD DS. 

* **Azure ad Connect**: uma ferramenta para sincronizar informações de identidade local para Microsoft Azure AD. O assistente de implantação e experiências guiadas ajudam a configurar os pré-requisitos e os componentes necessários para a conexão, incluindo a sincronização e o logon do Active Directory ao Azure AD. 

* **Active Directory**: serviço de diretório que armazena [informações de identidade locais, como informações de usuário e conta](https://www.dnsstuff.com/active-directory-service-accounts), e informações de segurança como senhas.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementar a autenticação LDAP com o Azure AD

* [Criar e configurar uma instância de AD DS do Azure](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Configurar a rede virtual para uma instância de AD DS do Azure](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Configurar LDAP Seguro para um domínio gerenciado do AD DS do Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Criar uma relação de confiança de floresta de saída para um domínio local no Azure AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

