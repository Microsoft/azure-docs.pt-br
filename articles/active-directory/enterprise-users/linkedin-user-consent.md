---
title: Compartilhamento e consentimento de dados do LinkedIn – Azure Active Directory | Microsoft Docs
description: Explica como a integração do LinkedIn compartilha dados por meio dos aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545903"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Consentimento e compartilhamento de dados de conexões de contas do LinkedIn

Você pode permitir que usuários em sua organização do Active Directory (Azure AD) consentim para conectar sua conta corporativa ou de estudante da Microsoft à sua conta do LinkedIn. Depois que um usuário conecta suas contas, as informações e os destaques do LinkedIn estão disponíveis em alguns aplicativos e serviços da Microsoft. Os usuários também podem esperar a sua experiência de rede no LinkedIn a ser aprimorada e enriquecida com informações da Microsoft.

Para obter informações do LinkedIn nos serviços e aplicativos da Microsoft, os usuários devem consentir para conectar suas contas da Microsoft e do LinkedIn. Os usuários são solicitados a conectar suas contas na primeira vez que clicarem para ver as informações do LinkedIn de outra pessoa em um cartão de perfil no Outlook, OneDrive ou SharePoint Online. As conexões de conta do LinkedIn não são totalmente habilitadas para os usuários até que consentirem para a experiência e conectar suas contas.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Benefícios do compartilhamento de informações do LinkedIn

Acesse as informações do LinkedIn nos serviços de aplicativos da Microsoft e torne mais fácil para seus usuários para se conectar, envolva-se e criar relações profissionais com colegas, clientes e parceiros dentro e fora da sua organização. Novos usuários podem agilizar mais rapidamente conectando-se com seus colegas, aprender mais sobre eles e ter acesso fácil para obter mais informações. Aqui está um exemplo de como as informações do LinkedIn aparecem no cartão de perfil em aplicativos da Microsoft:

![Habilitando a integração do LinkedIn em sua organização](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Habilitar e anunciar a integração do LinkedIn

Você deve ser um administrador do Microsoft Azure Active Directory para gerenciar a configuração para sua organização. Você pode habilitá-la para todos os usuários ou para um conjunto específico de usuários.

1. Para habilitar ou desabilitar a integração, siga as etapas em [consentir a integração do LinkedIn para sua organização do Azure ad](linkedin-integration.md).
2. Quando você anuncia a integração do LinkedIn em sua organização, aponte os usuários para as perguntas Frequentes sobre [informações do LinkedIn nos serviços e aplicativos da Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). O artigo fornece informações sobre onde as informações do LinkedIn são mostradas, [compartilhamento de dados e privacidade](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [como conectar contas](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) e muito mais.

Você deve anunciar a integração do LinkedIn aos seus usuários, fornecendo a eles todas as informações relacionadas ao [compartilhamento de dados e à privacidade com a integração do LinkedIn](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400). 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentimento do usuário para acesso a dados na Microsoft e no LinkedIn

Dados que são acessados no LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Dados que são acessados da Microsoft não são armazenados permanentemente com o LinkedIn, exceto para contatos. Os Contatos da Microsoft são armazenados no LinkedIn até os usuários os removam, conforme descrito em [excluindo contatos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando os usuários conectam suas contas, informações e destaques do LinkedIn ficam disponíveis em alguns aplicativos da Microsoft, como o cartão de perfil. Os usuários também podem esperar a sua experiência de rede no LinkedIn a ser aprimorada e enriquecida com informações da Microsoft.
Quando os usuários na organização conectam suas contas corporativas ou de estudante do LinkedIn e da Microsoft, eles têm duas opções:

* Dar permissão para que dados sejam acessados de ambas as contas. Isso significa que permitem que sua conta da Microsoft ou de trabalho acesse os dados da sua conta do LinkedIn e para funcionar [sua conta do LinkedIn para acessar os dados de seu trabalho da Microsoft ou de estudante](https://www.linkedin.com/help/linkedin/answer/84077).
* Conceder permissão somente para os dados do LinkedIn a serem acessado por conta de estudante e de trabalho da Microsoft.

Os usuários podem desconectar as contas e remover permissões de acesso de dados a qualquer momento, e [usuários podem controlar como o seu próprio perfil do LinkedIn é exibido](https://www.linkedin.com/help/linkedin/answer/83), incluindo se seu perfil pode ser exibido em aplicativos da Microsoft.

### <a name="linkedin-account-data"></a>Dados de conta do LinkedIn

Quando você conectar suas contas Microsoft e LinkedIn, permitirá que o LinkedIn forneça os seguintes dados à Microsoft:

* Dados de perfil - inclui a identidade do LinkedIn, informações de contato e as informações que você compartilha com outras pessoas em sua [perfil do LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dados de interesse - inclui interesses no LinkedIn, como as pessoas e os tópicos a seguir, grupos de cursos e conteúdo que você desejar e compartilhar.
* Dados de assinaturas - inclui assinaturas para aplicativos do LinkedIn e serviços, juntamente com os dados associados. 
* Dados de conexões- inclui sua [rede LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) incluindo perfis e informações de contato das conexões de 1º grau.

Dados que são acessados no LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Para obter mais informações sobre o uso da Microsoft de dados pessoais, consulte a [declaração de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Contas corporativas ou de estudante da Microsoft

Quando você conectar suas contas Microsoft e LinkedIn, permitirá que a Microsoft forneça dados ao LinkedIn:

* Dados do perfil - inclui informações como seu nome, sobrenome, foto de perfil, endereço de e-mail, gerente e as pessoas que você gerencia.
* Dados do calendário - inclui reuniões em seus calendários, seus tempos, locais e as informações de contato dos participantes. Informações sobre a reunião, como a agenda, o conteúdo ou o título de reunião não estão incluídas nos dados de calendário.
* Dados de interesse: inclui os interesses associados à sua conta, com base no uso de serviços Microsoft, como o Cortana e Bing para empresas.
* Dados de assinaturas-inclui assinaturas fornecidas pela sua organização para aplicativos e serviços da Microsoft, como Microsoft 365.
* Dados de contato - inclui listas de contatos no Outlook, Skype e outros serviços de conta da Microsoft, incluindo as informações de contato para as pessoas se comunicam com frequência ou colaboram. Contatos serão periodicamente importados, armazenados e usado pelo LinkedIn, por exemplo, sugerir conexões, ajudar a organizar contatos e mostrar atualizações sobre contatos.

Dados que são acessados da Microsoft não são armazenados permanentemente com o LinkedIn, exceto para contatos. Os Contatos da Microsoft são armazenados no LinkedIn até que os usuários os removem. Saiba mais sobre [excluindo contatos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Para obter mais informações sobre o uso dos dados pessoais do LinkedIn, consulte a [Política de privacidade do LinkedIn](https://www.linkedin.com/legal/privacy-policy). Para serviços do LinkedIn, transferência de dados e armazenamento, os dados podem fluir da União Europeia para a Estados Unidos e voltar, e sua privacidade é protegida conforme descrito em [transferências de dados da União Europeia](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Próximas etapas

* [LinkedIn em aplicativos da Microsoft com sua conta corporativa ou de estudante](https://www.linkedin.com/help/linkedin/answer/84077)
