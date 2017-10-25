---
title: "Proteção dos aplicativos Web e móveis de PaaS usando o Serviço de Aplicativo do Azure | Microsoft Docs"
description: " Saiba mais sobre o Serviço de Aplicativo do Azure e as melhores práticas de segurança para proteger aplicativos Web e móveis de PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: terrylan
ms.openlocfilehash: f1f08133b4601c7260687a4b281b461dbf4d611e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Proteção dos aplicativos Web e móveis de PaaS usando o Serviço de Aplicativo do Azure

Neste artigo, discutiremos um conjunto de práticas recomendadas de segurança do [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) para proteger seus aplicativos móveis e Web de PaaS. Essas práticas recomendadas derivam da nossa experiência com o Azure e da experiência de clientes como você.

## <a name="azure-app-service"></a>Serviço de aplicativo do Azure
O [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md) é uma oferta de PaaS que permite a você criar aplicativos Web e móveis para qualquer plataforma ou dispositivo e se conectar a dados em qualquer lugar, na nuvem ou local. O Serviço de Aplicativo inclui os recursos móveis e da Web que antes eram fornecidos separadamente, como os Sites do Azure e os Serviços Móveis do Azure. Ele também inclui novos recursos para automatizar processos empresariais e hospedagem de APIs de nuvem. O Serviço de Aplicativo proporciona, como um único serviço integrado, um avançado conjunto de recursos para cenários da Web, móveis e de integração.

## <a name="best-practices"></a>Práticas recomendadas

Ao usar o Serviço de Aplicativo, siga estas melhores práticas:

- [Autenticar por meio do Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). O Serviço de Aplicativo fornece um serviço OAuth 2.0 para seu provedor de identidade. O OAuth 2.0 se concentra na simplicidade de desenvolvedor do cliente, fornecendo fluxos de autorização específicos para aplicativos Web, aplicativos da área de trabalho e telefones celulares. O Azure AD usa o OAuth 2.0 para que você possa autorizar o acesso a aplicativos móveis e da Web.
- Restringir o acesso com base nos princípios de segurança de divulgação restrita àqueles diretamente interessados e privilégios mínimos. A restrição do acesso é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados. O RBAC (Controle de Acesso baseado em função) pode ser usado para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. Para saber mais sobre a concessão de acesso aos aplicativos aos usuários, consulte [introdução ao gerenciamento de acesso](../active-directory/role-based-access-control-what-is.md).
- Proteja seus dados. Não importa a qualidade de sua segurança se você perder suas chaves de assinatura. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre de Chaves, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para garantia extra, você pode importar ou gerar chaves em HSMs. Confira [Azure Key Vault](../key-vault/key-vault-whatis.md) para saber mais. Também é possível usar o Key Vault para gerenciar seus certificados TLS com renovação automática.
- Restrinja os endereços IP de origem de entrada. O [Ambiente de Serviço de Aplicativo](../app-service/environment/intro.md) tem um recurso de integração de rede virtual que ajuda a restringir endereços IP de origem de entrada por meio dos NSGs (grupos de segurança de rede). Se você não estiver familiarizado com VNETs (Redes Virtuais do Azure), isso é um recurso que permite colocar muitos dos recursos do Azure em uma rede que não pode ser roteada pela Internet, cujo acesso você pode controlar. Para saber mais, confira [Integrar seu aplicativo com uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Próximas etapas
Este artigo apresentou um conjunto de melhores práticas de segurança do Serviço de Aplicativo para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteção de implantações de PaaS](security-paas-deployments.md)
- [Proteção de aplicativos Web e móveis de PaaS usando o Banco de Dados SQL do Azure e o SQL Data Warehouse](security-paas-applications-using-sql.md)
