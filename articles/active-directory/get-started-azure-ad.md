---
title: "Introdução ao Azure Active Directory | Microsoft Docs"
description: 
keywords: 
author: jeffgilb
manager: femila
ms.author: jeffgilb
ms.reviewer: jsnow
ms.date: 10/04/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
service: active-directory
custom: it-pro
ms.openlocfilehash: 2e9d9955810c01eb916df2580f623373aebb9d1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-ad"></a>Introdução ao Azure AD
O gerenciamento de identidade moderno requer confiabilidade consistente e dimensionável para garantir a disponibilidade de aplicativos e serviços somente a usuários autenticados. Para oferecer suporte adequado às necessidades de gerenciamento de identidade de usuários, TI precisa de uma maneira de fornecer acesso a aplicativos SaaS (software como serviço) públicos aprovados, uma maneira de hospedar aplicativos de linha de negócios internos e até mesmo maneiras de melhorar o desenvolvimento e o uso de aplicativo local. Todos esses requisitos apontam para a necessidade de uma solução de gerenciamento de identidade baseada em nuvem.      

O Azure AD (Azure Active Directory) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, governança avançada de identidade e gerenciamento de acesso a aplicativos. O projeto multilocatário, distribuído geograficamente e de alta disponibilidade do AD do Azure significa que você pode confiar nele para suas necessidades comerciais mais críticas.

O Azure AD inclui um pacote completo de recursos de gerenciamento de identidade, incluindo a capacidade de sincronizar as informações de recursos locais, identidade visual da empresa personalizável, gerenciamento de licenças simples e até mesmo gerenciamento de senha por autoatendimento.  Esses recursos fáceis de configurar podem ajudar a começar a usar o Azure AD para proteger aplicativos baseados em nuvem, simplificar os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa sejam atingidas.

![AD do Azure ](./media/get-started-azure-ad/Azure_Active_Directory.png)

O restante deste artigo mostra como começar a usar o Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Inscrever-se no Azure Active Directory Premium
Para [começar a usar com o Azure AD (Azure Active Directory) Premium](active-directory-get-started-premium.md), você pode comprar licenças e associá-las à sua assinatura do Azure. Se você criar uma nova assinatura do Azure, também precisará ativar seu plano de licenciamento e o acesso ao serviço do Azure AD. 

Para se inscrever no Active Directory Premium, você tem várias opções: 

- Usar sua assinatura do Azure ou do Office 365
- Use um plano de licenciamento Enterprise Mobility + Security
- Usar um plano de Licenciamento por Volume da Microsoft

> ### <a name="verification-step"></a>Etapa de verificação
> Depois de ativar a assinatura, verifique se você consegue fazer logon no serviço.

## <a name="add-a-custom-domain-name"></a>Adicionar um nome de domínio personalizado
Todo diretório do Azure AD vem com um nome de domínio inicial no formato *nomededomínio*.onmicrosoft.com. O nome de domínio inicial não pode ser alterado nem excluído, mas você também pode [adicionar seu nome de domínio corporativo ao Azure AD](add-custom-domain.md). Por exemplo, sua organização provavelmente tem outros nomes de domínio que são usados para fazer negócios e usuários que entram usando seu nome de domínio corporativo. Adicionar nomes de domínio personalizados ao Azure AD permite que você atribua nomes de usuário no diretório que são familiares para seus usuários, tais como "alice@contoso.com". Em vez de ‘alice@.onmicrosoft.com’. O processo é simples:

1. Adicionar o nome de domínio personalizado ao diretório
2. Adicionar uma entrada DNS para o nome de domínio no registrador de nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

> ### <a name="verification-step"></a>Etapa de verificação
> Depois de adicionar um domínio personalizado, verifique se ele tem o status **Verificado** exibido na folha **Nomes de domínio** do portal do Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Adicionar identidade visual da empresa à página de entrada 
Para evitar confusão, muitas empresas desejam aplicar uma aparência consistente em todos os sites e serviços que elas gerenciam. O Azure AD (Azure Active Directory) fornece essa capacidade permitindo que você [personalize a aparência da página de entrada com esquemas de cores personalizados e o logotipo da empresa](customize-branding.md). A página de entrada é a que aparece quando você entra no Office 365 ou em outros aplicativos baseados na Web que estejam usando o Azure AD como provedor de identidade. Você interage com essa página para inserir suas credenciais.

> ### <a name="verification-step"></a>Etapa de verificação
> Faça logon no portal do Azure e verifique se a sua página de entrada personalizada e quaisquer personalizações de idioma adicionais foram configuradas corretamente. 

## <a name="add-new-users"></a>Adicionar novos usuários
Você pode [adicionar novos usuários ao Azure AD da sua organização](add-users-azure-active-directory.md) um por vez usando o portal do Azure ou sincronizando seus dados do recurso do Windows Server AD local. Você pode adicionar usuários baseados em nuvem diretamente do portal do Azure AD ou sincronizar informações do usuário local.

Para habilitar a sincronização de identidades local ao Azure AD, você precisa instalar e configurar o [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) em um servidor na sua organização. Esse aplicativo lida com a sincronização de usuários e grupos da fonte de identidade existentes para seu locatário do Azure AD.

> ### <a name="verification-step"></a>Etapa de verificação
> Depois de criar ou sincronizar os novos usuários, verifique se eles estão visíveis no Azure AD.

## <a name="assign-licenses"></a>Atribuir licenças
Embora você precise apenas obter uma assinatura para configurar funcionalidades pagas, ainda é preciso [atribuir licenças de usuário](license-users-groups.md) para recursos pagos Premium do Azure AD. Qualquer usuário que deve ter acesso ou é gerenciado por meio de um recurso pago do Azure AD deve ter uma licença atribuída. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como o Azure AD Premium, Básico ou o Enterprise Mobility + Security.

Você pode usar atribuição de licença baseada em grupo para configurar as regras como os seguintes exemplos:

- Todos os usuários em seu diretório obtêm automaticamente uma licença
- Todos com o cargo apropriado obtêm uma licença
- Você pode delegar a decisão a outros gerentes na organização (usando grupos de autoatendimento)

> ### <a name="verification-step"></a>Etapa de verificação
> Examine as licenças atribuídas e disponíveis em **Azure Active Directory** > **Licenças** > **Todos os produtos**.

## <a name="configure-self-service-password-reset"></a>Configurar a redefinição de senha de autoatendimento
A [redefinição da senha de autoatendimento (SSPR)](active-directory-passwords-getting-started.md) oferece um meio simples para os administradores de TI habilitarem os usuários a redefinir ou desbloquear suas senhas ou contas. O sistema inclui relatórios detalhados para acompanhar quando os usuários utilizam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

> ### <a name="verification-step"></a>Etapa de verificação
> Examine as propriedades SSPR habilitadas em **Azure Active Directory** > **Redefinição de senha** para garantir que as atribuições adequadas de usuário e grupo tenham sido feitas. 


### <a name="learn-more"></a>Saiba mais
[Página do produto do Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Página de informações sobre preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)