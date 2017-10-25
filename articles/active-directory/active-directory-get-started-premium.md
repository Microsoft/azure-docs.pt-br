---
title: Inscrever-se no Azure Active Directory Premium | Microsoft Docs
description: "Explica como inscrever-se na edição do Azure Active Directory Premium"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;
ms.openlocfilehash: 4b86ececefd3724b9e7791d2609b146de052f605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>Início rápido: Inscrever-se no Azure Active Directory Premium
Para começar com o Azure Active Directory (Azure AD) Premium, você poderá comprar licenças e associá-las à sua assinatura do Azure. Se você criar uma nova assinatura do Azure, também precisará ativar seu plano de licenciamento e o acesso ao serviço do Azure AD conforme descrito nas seções a seguir. 

## <a name="sign-up-for-active-directory-premium"></a>Inscrever-se no Active Directory Premium
Para se inscrever no Active Directory Premium, você tem várias opções: 
* Usar sua assinatura do Azure ou do Office 365
* Use um plano de licenciamento Enterprise Mobility + Security
* Usar um plano de Licenciamento por Volume da Microsoft

### <a name="azure-or-office-365"></a>Azure ou Office 365 
Como assinante do Azure ou do Office 365, você pode comprar o Azure Active Directory Premium online. 

Para obter etapas detalhadas, confira [Como comprar o Azure Active Directory Premium ‒ clientes existentes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) ou [Como comprar o Azure Active Directory Premium ‒ novos clientes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
O Enterprise Mobility + Security (EMS) é uma forma econômica das organizações usarem os seguintes serviços em conjunto em um plano de licenciamento: Azure Active Directory Premium, Proteção de Informações do Azure e Microsoft Intune. Você pode aprender mais sobre o EMS no [site do Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security) e obter mais informações sobre os tipos de licença do EMS disponíveis para a compra na página [Opções de Preço do Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing).  

Você pode começar com o Azure AD via licenças do EMS usando uma das seguintes opções de licenciamento:

- Experimente o EMS com uma [assinatura de avaliação do Enterprise Mobility + Security E5 ](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1) gratuita
- Comprar [licenças do Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- Comprar [licenças do Enterprise Mobility + Security E3](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Licenciamento por volume da Microsoft
O Azure Active Directory Premium está disponível por meio de um [Contrato do Microsoft Enterprise](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 ou mais licenças) ou do programa [Licença de Volume Aberto](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licenças).

Você pode aprender mais sobre as opções de compra do licenciamento por volume na página [Como comprar com o licenciamento por Volume](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx).

> [!NOTE]
> As edições Premium e Basic do Active Directory do Azure estão disponíveis para clientes na China usando a instância mundial do Active Directory do Azure. As edições Azure Active Directory Premium e Basic não têm suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).

Se antes você adquiriu e ativou as licenças do Azure AD para a mesma assinatura do Azure usada nas etapas anteriores, as licenças serão ativadas automaticamente no mesmo diretório. Caso contrário, continue com as etapas descritas no restante deste artigo.

## <a name="activate-your-license-plan"></a>Ativar seu plano de licença
Este é seu primeiro plano de licença do Azure AD comprado na Microsoft? Nesse caso, um email de confirmação será gerado e enviado para você quando a compra for concluída. Você precisará desse email para ativar seu primeiro plano de licença.

**Para ativar o plano de licença, execute uma das seguintes etapas:**

1. Para iniciar a ativação, clique em **Entrar** ou em **Inscrever-se**.
   
    ![Entrar][1]

    - Se você tiver um locatário existente, clique em **Entrar** para entrar com sua conta de administrador existente. Entre com as credenciais de administrador global no locatário no qual as licenças devem ser ativadas.

    - Se você quiser criar um novo locatário do Azure AD para usar com seu plano de licenciamento, clique em **Inscrever-se** para abrir a caixa de diálogo **Criar Perfil da Conta**.

        ![Criar Perfil de Conta][2]

Quando terminar, a caixa de diálogo a seguir aparecerá como uma confirmação de ativação do plano de licença de seu locatário:

![Confirmação][3]

## <a name="activate-your-azure-active-directory-access"></a>Ativar o acesso ao Azure Active Directory
Se você estiver adicionando novas licenças do Azure AD Premium a uma assinatura existente, o acesso ao Azure AD já deverá estar ativado. Caso contrário, você precisará ativar o acesso ao Azure AD depois de receber o **email de boas-vindas**.  

Quando as licenças compradas forem provisionadas no diretório, um **email de boas-vindas** será enviado. O email confirma que você pode começar a gerenciar suas licenças e recursos do Azure Active Directory Premium ou Enterprise Mobility + Security. 

> [!TIP]
> Você não poderá acessar o Azure AD para o novo locatário até ativar o acesso ao diretório do Azure AD usando o email de boas-vindas enviado automaticamente quando a processo de provisionamento de licença foi concluído. 

**Para ativar o acesso ao Azure AD, execute as seguintes etapas:**

1. No **Email de boas-vindas**, clique em **Entrar**. 
   
    ![Email de boas-vindas][4]
2. Depois de entrar com êxito, você também precisará concluir a autenticação do segundo fator usando um dispositivo móvel:
   
    ![Verificação de celular][5]

A ativação só deve levar alguns minutos e, em seguida, você terá acesso para gerenciar o Azure AD. 

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você aprendeu como se inscrever no Azure AD Premium e ativar seu acesso ao Azure Active Directory. 

Se você já tiver uma assinatura do Azure, poderá usar o link a seguir para iniciar uma avaliação ou comprar licenças do Azure AD Premium no portal do Azure.

> [!div class="nextstepaction"]
> [Ativar licenças do Azure AD Premium](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png