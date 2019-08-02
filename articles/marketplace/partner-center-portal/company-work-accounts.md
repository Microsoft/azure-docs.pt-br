---
title: Contas de trabalho da empresa e do Partner Center
description: Como verificar se sua empresa tem uma conta de trabalho configurado com a Microsoft, crie uma nova conta de trabalho ou configurar várias contas de trabalho para usar com o Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619559"
---
# <a name="company-work-accounts-and-partner-center"></a>Contas de trabalho da empresa e do Partner Center

Partner Center usa contas de trabalho da empresa, também conhecidas como locatários do Azure Active Directory (AD), para gerenciar o acesso de conta para vários usuários, permissões de controle, grupos de hosts e aplicativos e manter os dados de perfil. Ao vincular o domínio de conta de email de trabalho da sua empresa à sua conta do Centro de parceiros, funcionários da sua empresa podem entrar no Partner Center para gerenciar as ofertas do marketplace usando seus próprios nomes de usuário de conta de trabalho e senhas.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Verifique se sua empresa já tem uma conta de trabalho

Se sua empresa assina um serviço de nuvem da Microsoft, como Azure, Microsoft Intune ou Office 365, em seguida, você já tem um domínio de conta de email do trabalho (também conhecido como um locatário do Azure Active Directory) e que pode ser usado com o Partner Center.

Siga estas etapas para verificar:
1. Entrar no portal de administração do Azure em https://portal.azure.com.
2. Selecione **Azure Active Directory** no menu de navegação à esquerda e selecione **nomes de domínio personalizados**.
3. Se você já tiver uma conta de trabalho, seu nome de domínio será listado.

Se sua empresa ainda não tiver uma conta de trabalho, um será criado para você durante o processo de registro do Partner Center.

## <a name="set-up-multiple-work-accounts"></a>Configurar várias contas de trabalho

Antes de decidir usar uma conta de trabalho existente, considere quantos usuários na conta de trabalho precisar de acesso do Partner Center. Se você tiver usuários na conta de trabalho que não precisa acessar o Centro de parceiros, convém considerar a criação de várias contas de trabalho, para que somente os usuários que precisam acessar o Centro de parceiros são representados em uma conta específica.

## <a name="create-a-new-work-account"></a>Criar uma nova conta de trabalho

Para criar uma nova conta de trabalho para sua empresa, siga as etapas abaixo. Você talvez precise solicitar assistência de quem tiver permissões administrativas na conta do Microsoft Azure da sua empresa.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).
2. No menu de navegação à esquerda, selecione o **Azure Active Directory** -> **usuários**.
3. Selecione **novo usuário** e criar uma nova conta de trabalho do Azure inserindo um nome e endereço de email. Certifique-se de que o **função de diretório** é definido como **usuário** e selecione o **Mostrar senha** caixa de seleção na parte inferior para exibir e anote a senha gerada automaticamente.
4. Selecione **criar** para salvar o novo usuário.

O endereço de email da conta de usuário deve ser um nome de domínio verificado no seu diretório. Você pode listar todos os domínios verificados no seu diretório, selecionando **Azure Active Directory** -> **nomes de domínio personalizados** no menu de navegação à esquerda.

Para saber mais sobre como adicionar domínios personalizados no Azure Active Directory, consulte [adicionar ou associar um domínio do AD do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Solucionar problemas de trabalho email entrar

Se você estiver tendo problemas para entrar na sua conta de trabalho (também conhecido como locatário do Azure AD), encontre que o cenário no diagrama abaixo que melhor corresponde à sua situação e siga as etapas recomendadas.

![Diagrama para solucionar problemas de entrada-na conta de trabalho](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar sua conta do Marketplace comercial no Partner Center](./manage-account.md) 
