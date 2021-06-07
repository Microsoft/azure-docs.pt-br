---
title: Protegendo aplicativos móveis Web & de PaaS
titleSuffix: Azure App Service
description: 'Saiba mais sobre o Serviço de Aplicativo do Azure e as melhores práticas de segurança para proteger aplicativos Web e móveis de PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94701007"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Melhores práticas para proteger aplicativos PaaS móveis e Web usando o Serviço de Aplicativo do Azure

Neste artigo, discutiremos um conjunto de práticas recomendadas de segurança do [Serviço de Aplicativo do Azure](../../app-service/overview.md) para proteger seus aplicativos móveis e Web de PaaS. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

O Serviço de Aplicativo do Azure é uma oferta de PaaS (plataforma como serviço) que permite a você criar aplicativos Web e móveis para qualquer plataforma ou dispositivo e se conectar a dados em qualquer lugar, na nuvem ou local. O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem. O Serviço de Aplicativo proporciona, como um único serviço integrado, um avançado conjunto de recursos para cenários da Web, móveis e de integração.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autenticar por meio do Azure AD (Azure Active Directory)
O Serviço de Aplicativo fornece um serviço OAuth 2.0 para seu provedor de identidade. O OAuth 2.0 concentra-se na simplicidade do desenvolvedor cliente, fornecendo fluxos de autorização específicos para aplicativos Web, aplicativos da área de trabalho e celulares. O Azure AD usa o OAuth 2.0 para que você possa autorizar o acesso a aplicativos móveis e da Web. Para obter mais informações, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Restringir o acesso baseado em função
A restrição do acesso é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. Você pode usar o Azure RBAC (controle de acesso baseado em função) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo, como a necessidade de conhecer e os princípios de segurança de privilégios mínimos. Para saber mais sobre como conceder acesso a aplicativos aos usuários, confira [o que é o Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Proteja suas chaves
Não importa a qualidade de sua segurança se você perder suas chaves de assinatura. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves protegidas por HSMs (módulos de segurança de hardware). Para garantia extra, você pode importar ou gerar chaves em HSMs. Também é possível usar o Key Vault para gerenciar seus certificados TLS com renovação automática. Veja [O que é o Azure Key Vault](../../key-vault/general/overview.md) para saber mais.

## <a name="restrict-incoming-source-ip-addresses"></a>Restringir os endereços IP de origem de entrada
O [Ambiente de Serviço de Aplicativo](../../app-service/environment/intro.md) tem um recurso de integração de rede virtual que ajuda a restringir endereços IP de origem de entrada por meio dos NSGs (grupos de segurança de rede). Se você não estiver familiarizado com VNETs (Redes Virtuais do Azure), isso é um recurso que permite colocar muitos dos recursos do Azure em uma rede que não pode ser roteada pela Internet, cujo acesso você pode controlar. Para saber mais, consulte [integrar seu aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).

Para o serviço de aplicativo no Windows, você também pode restringir os endereços IP dinamicamente Configurando o web.config. Para obter mais informações, consulte [segurança de IP dinâmico](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou um conjunto de melhores práticas de segurança do Serviço de Aplicativo para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](paas-deployments.md)
- [Protegendo bancos de dados de PaaS no Azure](paas-applications-using-sql.md)