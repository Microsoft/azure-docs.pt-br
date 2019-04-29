---
title: Diretrizes para o editor do Azure Marketplace e AppSource | Azure
description: Diretrizes para o Azure Marketplace e o AppSource para editores de aplicativo e serviço
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739434"
---
# <a name="guidelines"></a>Diretrizes  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Diretrizes para o Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Diretrizes para criar uma ID da Microsoft para gerenciar uma conta do Marketplace  
Se mais de uma pessoa requer acesso para a mesma ID da Microsoft usada para criar sua conta do marketplace, você deve seguir estas diretrizes para ajudá-lo a criar uma conta da empresa. 

>[!IMPORTANT]
>Para autorizar a vários usuários para acessar sua conta do Microsoft Developer Center (Centro de desenvolvimento), a Microsoft recomenda que você use o Azure Active Directory (Azure AD) para atribuir funções aos usuários individuais. Cada usuário deve acessar a conta entrando com credenciais individuais do Azure AD. Crie sua ID da Microsoft usando um endereço de email em um domínio registrado em sua empresa. A Microsoft sugere que o email não seja atribuído a um indivíduo. Um exemplo é `windowsapps@fabrikam.com`.  
>*   Para obter mais informações, visite o [problema: ID da Microsoft no Azure AD federado domínio](#issue-microsoft-id-in-an-azure-ad-federated-domain) seção.  

*   Limite o acesso à ID da Microsoft ao menor número possível de desenvolvedores. 
*   Configure uma lista de distribuição (DL) de email corporativo que inclui todas as pessoas que devem acessar sua conta do Centro de Desenvolvimento. Adicione o endereço de email da DL às informações de segurança. A DL permite que todos os funcionários na lista recebam códigos de segurança quando solicitado e gerenciem as informações de segurança da sua ID da Microsoft. Se a configuração de uma lista de distribuição não for viável, o proprietário da conta de email individual deve estar disponível para acessar e compartilhar o código de segurança quando solicitado.  
    *   Por exemplo, o proprietário é solicitado quando novas informações de segurança são adicionadas à ID da Microsoft ou quando a ID da Microsoft é acessada de um novo dispositivo.  
*   Adicione um número de telefone da empresa que não exija um ramal e possa ser acessado pelos principais membros da equipe.  
*   Em geral, você deve exigir que os desenvolvedores usem dispositivos confiáveis para entrar em sua conta do Centro de Desenvolvimento. Todos os principais membros da empresa devem ter acesso aos dispositivos confiáveis. Usar dispositivos confiáveis para acessar reduz o requisito para o envio de códigos de segurança quando alguém está acessando a conta do Centro de Desenvolvimento.  
*   Se você precisa conceder acesso para a conta do Centro de Desenvolvimento de um computador não confiável, você deve limitar o acesso aos desenvolvedores a não mais do que cinco. De modo ideal, seus desenvolvedores devem acessar a conta de computadores que compartilham o mesmo local geográfico e de rede.  
*   Com frequência, revise e verifique as informações de segurança.  
    *   Para exibir as informações de segurança, visite a página de Configurações de segurança localizada em [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Sua conta do Centro de Desenvolvimento deve ser acessada principalmente de computadores confiáveis. É importante que você acesse de computadores confiáveis, pois há um limite para o número de códigos gerados por conta do Centro de Desenvolvimento por semana. Usar computadores confiáveis também torna a experiência de entrar mais segura e consistente. 
*   Para obter mais informações sobre as diretrizes de conta e segurança adicionais do Centro de Desenvolvimento, visite a página Abrir uma conta de desenvolvedor localizada em [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problema: ID da Microsoft em um domínio federado do Azure AD  
Sua conta corporativa pode ser federada por meio do Azure Active Directory (Azure AD). Se você tentar criar uma ID da Microsoft usando um endereço de email corporativo que é federado com o Azure AD, você recebe um erro. Se você receber um erro, você deve verificar com sua equipe de TI para confirmar que sua conta é federada por meio do Azure AD. O email federado do Azure AD é um problema conhecido e a Microsoft está trabalhando para resolvê-lo.  
*   Para obter mais informações sobre o Azure AD, visite a página Documentação do Azure Active Directory localizada em [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft recomenda uma solução alternativa. Siga estas etapas para criar um novo endereço de email no domínio `outlook.com` e criar uma regra para encaminhar suas comunicações.  
1.  Acesse a página Criar conta e clique em Obter um novo link de endereço de email. 
    *   Para inscrever-se para a ID da Microsoft, visite a página Criar conta localizada em [signup.live.com/signup](https://signup.live.com/signup).  
2.  Crie o novo endereço de email e insira uma senha. É criada uma nova ID da Microsoft e uma caixa de correio de email no domínio `outlook.com`. Continue o processo de registro até a conta ser criada.  

    >[!IMPORTANT]
    >Você deve usar uma lista de distribuição ou o endereço de email que está registrado como uma ID da Microsoft para registrar no Centro de Desenvolvimento. A Microsoft recomenda que você use uma lista de distribuição para remover a dependência de pessoas físicas. Se sua lista de distribuição ou o endereço de email não estiver registrado, você deve registrar agora.    

    >[!Important]
    >Se qualquer endereço de email estiver localizado no domínio da empresa `Microsoft`, não será possível usá-la para registro no Centro de Desenvolvimento.  

3.  Depois de criar a ID da Microsoft com o endereço de email do Outlook, entre em sua caixa de correio do Outlook. Crie uma regra de encaminhamento de email. A regra de encaminhamento de email deve mover todos os emails que são recebidos na caixa de correio do Outlook para o endereço de email no seu domínio que você criou para gerenciar a conta do marketplace.  
    *   Para entrar em sua caixa de correio do Outlook, visite a página do Outlook localizada em [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Para obter mais informações sobre regras de encaminhamento, visite a página Usar regras no Outlook Web App para encaminhar automaticamente as mensagens para outra conta localizada em [support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A regra de encaminhamento envia todos os emails e comunicações recebidas na conta de email do Outlook para o endereço de email em um domínio registrado em sua empresa. O endereço de email `outlook.com` deve ser usado para autenticar-se no Centro de Desenvolvimento e no Portal do Cloud Partner.  

## <a name="next-steps"></a>Próximas etapas
*   Visite a página [ do Azure Marketplace e do AppSource Publisher Guide ](./marketplace-publishers-guide.md).  
 
---
