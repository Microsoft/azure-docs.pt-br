---
title: Gerenciando aplicativos com o Azure Active Directory | Microsoft Docs
description: "Este artigo apresenta os benefícios da integração do Active Directory do Azure com seus aplicativos locais, de nuvem e SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: asteen
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: b8f0cfdb468094bc761d6b939ca318fcfbea3ea4
ms.contentlocale: pt-br
ms.lasthandoff: 07/19/2017

---
# <a name="managing-applications-with-azure-active-directory"></a>Gerenciamento de aplicativos com o Active Directory do Azure
Além do fluxo de trabalho ou do conteúdo real, as empresas têm dois requisitos básicos para todos os aplicativos:

1. Para aumentar a produtividade, os aplicativos devem ser fáceis de descobrir e acessar
2. Para habilitar a segurança e governança, a organização precisa controlar e supervisionar quem pode e, na verdade, está acessando cada aplicativo

No mundo dos aplicativos na nuvem, a melhor maneira de obter isso é usar uma identidade para controlar “*QUEM tem permissão para fazer O QUE*”.

Na terminologia de computação:

* *Quem* é conhecido como *identidade* — o gerenciamento de usuários e grupos
* *O que* é conhecido como *gerenciamento de acesso* — o gerenciamento de acesso aos recursos protegidos

Ambos os componentes juntos são conhecidos como *IAM (Gerenciamento de Identidades e Acessos)*, que é definido pelo grupo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) como "*a disciplina de segurança que permite que os indivíduos certos acessem os recursos certos nos momentos certos para os motivos certos*".

Certo, então, qual é o problema? Se o IAM *não for gerenciado* em um só lugar com uma solução integrada:

* Os administradores de identidades precisam criar e atualizar individualmente as contas de usuário em todos os aplicativos de maneira separada, uma atividade demorada e redundante.
* Os usuários precisam memorizar várias credenciais para acessar os aplicativos com que precisam trabalhar. Como resultado, os usuários tendem a anotar suas senhas ou usar outras soluções de gerenciamento de senhas que apresentam outros riscos de segurança de dados.
* Atividades demoradas e redundantes reduzem a quantidade de tempo que os usuários e administradores trabalham em atividades comerciais que aumentam o resultado dos negócios.

Portanto, o que geralmente impede que as organizações adotem soluções integradas de IAM?

* A maioria das soluções técnicas baseiam-se em plataformas de software que precisam ser implantadas e adaptadas por cada organização para seus próprios aplicativos.
* Aplicativos de nuvem normalmente são adotados a uma taxa mais alta do que a organização de TI pode integrar com as soluções de IAM existentes.
* As ferramentas de monitoramento e de segurança exigem integração e personalização adicional para obter cenários de E2E abrangentes.

## <a name="azure-active-directory-integrated-with-applications"></a>Active Directory do Azure integrado com aplicativos
O Active Directory do Azure é a solução IDaaS (Identidade como Serviço) abrangente da Microsoft que:

* Habilita o IAM como um serviço de nuvem 
* Fornece gerenciamento de acesso central, logon único (SSO) e relatórios 
* Oferece suporte ao gerenciamento de acesso integrado para [milhares de aplicativos](https://azure.microsoft.com/marketplace/active-directory/) na galeria de aplicativos, incluindo Salesforce, Google Apps, Box, Concur e outros. 

Com o Azure Active Directory, os aplicativos que você publica para seus parceiros e clientes (comerciais ou consumidores) têm os mesmos recursos de gerenciamento de identidades e de acesso.<br> Isso permite que você reduza significativamente os custos operacionais.

E se você precisar implementar um aplicativo que ainda não está listado na galeria de aplicativos? Embora isso seja um pouco mais demorado do que configurar o SSO para aplicativos da galeria de aplicativos, o AD do Azure fornece um assistente que ajuda você com a configuração.

O valor do AD do Azure vai além de “simples” aplicativos em nuvem. Você também pode usar com aplicativos locais, fornecendo acesso remoto seguro. Com o acesso remoto seguro, você pode eliminar a necessidade de VPNs ou outras implantações de gerenciamento de acesso remoto tradicionais.

Ao fornecer o gerenciamento de acesso central e SSO (logon único) para todos os seus aplicativos, o AD do Azure oferece a solução para os principais problemas de produtividade e de segurança de dados.

* Os usuários podem acessar vários aplicativos com um único logon, proporcionando mais tempo para a geração de receitas ou para a realização de atividades de operações de negócios.
* Os administradores de identidades podem gerenciar o acesso a aplicativos em um único lugar.

O benefício para o usuário e para a sua empresa é óbvio. Vamos examinar mais de perto os benefícios para um administrador de identidades e para a organização.

## <a name="integrated-application-benefits"></a>Benefícios de aplicativos integrados
O processo de SSO tem duas etapas:

* Autenticação, o processo de validação da identidade do usuário.
* Autorização, a decisão para habilitar ou bloquear o acesso a um recurso com uma política de acesso.

Ao usar o AD do Azure para gerenciar aplicativos e habilitar o SSO:

* A autenticação é feita na conta local (por exemplo, AD) ou do AD do Azure do usuário.
* A autorização é executada na política de atribuição e de proteção do AD do Azure, garantindo uma experiência do usuário final consistente e permitindo que você adicione atribuição, locais e condições de MFA em qualquer aplicativo, independentemente de seus recursos internos.

É importante entender que a forma como a autorização é imposta no aplicativo de destino varia, dependendo de como o aplicativo foi integrado com o AD do Azure.

* **Aplicativos pré-integrados pelo provedor de serviços** Como o Office 365 e o Azure, estes são aplicativos criados diretamente no AD do Azure e que dependem dele para seus recursos abrangentes de gerenciamento de identidades e de acesso. O acesso a esses aplicativos é habilitado por meio de informações de diretório e emissão de token.
* **Aplicativos pré-integrados pela Microsoft e aplicativos personalizados** Estes são aplicativos de nuvem independentes que dependem de um diretório interno de aplicativos e que podem funcionar independentemente do AD do Azure. O acesso a esses aplicativos é habilitado por meio da emissão de uma credencial específica de aplicativo mapeada para uma conta de aplicativo. Dependendo dos recursos do aplicativo, a credencial pode ser um token de federação ou o nome de usuário e a senha de uma conta provisionada anteriormente no aplicativo.
* **Aplicativos locais** Os aplicativos publicados por meio do proxy de aplicativo do AD do Azure que habilitam principalmente o acesso aos aplicativos locais. Esses aplicativos dependem de um diretório local central, como o Windows Server Active Directory. O acesso a esses aplicativos é habilitado por meio do disparo do proxy para entregar o conteúdo do aplicativo para o usuário final, ao mesmo tempo que respeita o requisito de logon local.

Por exemplo, se um usuário ingressar em sua organização, você precisa criar uma conta para o usuário no AD do Azure para as operações de logon primárias. Se esse usuário precisar ter acesso a um aplicativo gerenciado, como o Salesforce, você também precisará criar uma conta para este usuário no Salesforce e vinculá-la à conta do Azure para que o SSO funcione. Quando o usuário sair de sua organização, é aconselhável excluir a conta do AD do Azure e todas as contas equivalentes nos repositórios de IAM dos aplicativos aos quais o usuário tinha acesso.

## <a name="access-detection"></a>Detecção de acesso
Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que são usados. Em conjunto com o Cloud App Discovery, o AD do Azure fornece uma solução para detectar esses aplicativos.

## <a name="account-management"></a>Gerenciamento de contas
Tradicionalmente, o gerenciamento de contas nos vários aplicativos é um processo manual realizado pela equipe de TI ou de suporte na organização. O AD do Azure automatizou por completo o gerenciamento de contas em todos os aplicativos integrados do provedor de serviços e os aplicativos pré-integrados pela Microsoft que dão suporte ao provisionamento automatizado de usuários ou JIT do SAML.

## <a name="automated-user-provisioning"></a>Provisionamento automatizado de usuários
Alguns aplicativos fornecem interfaces de automação para a criação e remoção (ou desativação) de contas. Se um provedor oferecer uma interface desse tipo, ele será aproveitado pelo AD do Azure. Isso reduz os custos operacionais, pois as tarefas administrativas ocorrem automaticamente, e melhora a segurança do seu ambiente, já que ela diminui a probabilidade de acesso não autorizado.

## <a name="access-management"></a>gerenciamento de acesso
Com o AD do Azure, você pode gerenciar o acesso a aplicativos que usam atribuições individuais ou orientadas por regras. Você também pode delegar o gerenciamento de acesso para as pessoas certas na organização, garantindo a melhor supervisão e reduzindo a carga sobre a assistência técnica.

## <a name="on-premises-applications"></a>Aplicativos locais
O proxy de aplicativo interno permite que você publique seus aplicativos locais para seus usuários, resultando em uma experiência de acesso consistente com o aplicativo em nuvem moderno e nos benefícios dos recursos de monitoramento, relatórios e segurança do AD do Azure.

## <a name="reporting-and-monitoring"></a>Relatórios e monitoramento
O AD do Azure fornece recursos integrados de relatórios e monitoramento que permitem saber quem tem acesso aos aplicativos e quando eles realmente os usaram.

## <a name="related-capabilities"></a>Recursos relacionados
Com o AD do Azure, é possível proteger seus aplicativos com políticas de acesso granular e MFA pré-integrado. Para saber mais sobre o Azure MFA, veja [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Introdução
Para começar a integrar aplicativos com o AD do Azure, confira o [Guia de introdução: integrando o Active Directory do Azure com aplicativos](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Consulte também
[Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)


