---
title: Proteja o acesso externo ao Microsoft Teams, SharePoint e OneDrive com o Azure Active Directory
description: Proteja o acesso aos serviços Microsoft 365 como parte de sua segurança de acesso externo geral.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565131"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Proteja o acesso externo ao Microsoft Teams, SharePoint e OneDrive for Business 

O Microsoft Teams, o SharePoint e o OneDrive for Business são três das maneiras mais usadas de colaborar e compartilhar conteúdo com usuários externos. Se os métodos "aprovados" forem muito restritivos, os usuários ficarão fora dos métodos aprovados enviando por email o conteúdo ou Configurando aplicativos e processos externos inseguros, como um DropBox ou OneDrive pessoal. Seu objetivo é equilibrar suas necessidades de segurança com a facilidade de colaboração.

Este artigo orienta você a determinar e configurar a colaboração externa para atender às suas metas de negócios usando o Microsoft Teams e o SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>A governança começa no Azure Active Directory

O compartilhamento no Microsoft 365 está em parte governada pelas [identidades externas | Configurações de colaboração externas](https://aad.portal.azure.com/) no Azure Active Directory (AD do Azure). Se o compartilhamento externo estiver desabilitado ou restrito no Azure AD, ele substituirá as configurações de compartilhamento definidas em Microsoft 365. Uma exceção é que se a integração B2B do Azure AD não estiver habilitada, o SharePoint e o OneDrive poderão ser configurados para dar suporte ao compartilhamento ad hoc por meio de senha de uso único (OTP).

![Captura de tela das configurações de colaboração externas](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Acesso dos usuários convidados

Há três opções de acesso de usuário convidado, que controlam o que os usuários convidados podem ver depois de serem convidados. 

Para impedir que os usuários convidados vejam detalhes de outros usuários convidados e sejam capazes de enumerar a associação de grupo, escolha usuários convidados têm acesso limitado a propriedades e associações de objetos de diretório.

### <a name="guest-invite-settings"></a>Configurações de convite de convidado

Essas configurações determinam quem pode convidar convidados e como esses convidados podem ser convidados. Essas configurações só serão habilitadas se a integração com o B2B estiver habilitada.

É recomendável habilitar administradores e usuários na função emissor do emissor convidado. Essa configuração permite que processos de colaboração controlados sejam configurados, como no exemplo a seguir.

* O proprietário da equipe envia um tíquete para receber a função de emissor do convidado e

   * Torna-se responsável por todos os convites para convidados.

   * Concorda em não adicionar usuários diretamente ao SharePoint subjacente

   * É possível executar revisões regulares de acesso e revogar o acesso conforme apropriado.

* Central ele faz o seguinte

   * Permite o compartilhamento externo, concedendo a função solicitada após a conclusão do treinamento.

   * Atribui a licença do Azure AD P2 ao proprietário do grupo de Microsoft 365 para habilitar as revisões de acesso.
   * Cria uma análise de acesso de Microsoft 365 grupo.

   * Confirma que as revisões de acesso estão ocorrendo.

   * Remove os usuários adicionados diretamente ao SharePoint subjacente.

 Defina **habilitar senha de uso único de email para convidados (versão prévia) e habilitar o logon de autoatendimento do convidado via fluxos de usuário** para **Sim**. Essa configuração aproveita a integração com as configurações de colaboração externa do Azure AD.

### <a name="collaboration-restrictions"></a>Restrições de colaboração

Há três opções em restrições de colaboração. Seus requisitos de negócios determinam qual será sua escolha.

* **Permitir que os convites sejam enviados a qualquer domínio** significa que qualquer usuário pode ser convidado para colaborar.

* **Negue convites para os domínios especificados** significa que qualquer usuário fora deles pode ser convidado para colaborar.

* **Permitir convites somente para domínios especificados** significa que qualquer usuário fora desses domínios especificados não pode ser convidado. 

## <a name="govern-access-in-teams"></a>Controlar o acesso em equipes

[As equipes diferenciam entre usuários externos (qualquer pessoa fora da sua organização) e os usuários convidados (aqueles com contas de convidado)](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)). Você gerencia a configuração de colaboração no [portal de administração de equipes](https://admin.teams.microsoft.com/company-wide-settings/external-communications) em configurações de toda a organização. 

> [!NOTE]
> As configurações de colaboração de identidades externas no Azure Active Directory controlar as permissões efetivas. Você pode aumentar as restrições nas equipes, mas não diminuí-las do que está definido no Azure AD.

* **Configurações de acesso externo**. Por padrão, as equipes permitem acesso externo, o que significa que a organização pode se comunicar com todos os domínios externos. Se você quiser restringir ou permitir domínios específicos apenas para equipes, poderá fazer isso aqui.

* **Acesso de convidado**. O acesso de convidado controla o que os usuários convidados podem fazer em equipes.

Para saber mais sobre como gerenciar o acesso externo em equipes, consulte os recursos a seguir.

* [Gerenciar o acesso externo no Microsoft Teams](/microsoftteams/manage-external-access)

* [Microsoft 365 modelos de identidade e Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Modelos de identidade e autenticação para o Microsoft Teams](/MicrosoftTeams/identify-models-authentication)

* [Rótulos de sensibilidade para o Microsoft Teams](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Controlar o acesso no SharePoint e no OneDrive

Os administradores do SharePoint têm muitas configurações disponíveis para colaboração. As configurações de toda a organização são gerenciadas no centro de administração do SharePoint. As configurações podem ser ajustadas para cada site do SharePoint. É recomendável que as configurações de toda a organização estejam em seus níveis mínimos de segurança necessários e que você aumente a segurança em sites específicos, conforme necessário. Por exemplo, para um projeto de alto risco, talvez você queira restringir os usuários a determinados domínios e desabilitar a capacidade dos membros de convidar convidados.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integrando o SharePoint e um-drive ao Azure AD B2B

Como parte de sua estratégia geral para administrar a colaboração externa, recomendamos que você [habilite a visualização da integração do SharePoint e do onedrive com o Azure ad B2B](/sharepoint/sharepoint-azureb2b-integration-preview) .

O Azure AD B2B fornece autenticação e gerenciamento de usuários convidados. Com a integração do SharePoint e do OneDrive, as senhas de uso [único do Azure ad B2B](../external-identities/one-time-passcode.md) são usadas para o compartilhamento externo de arquivos, pastas, itens de lista, bibliotecas de documentos e sites. Esse recurso fornece uma experiência atualizada da experiência de [destinatário de compartilhamento externo seguro](/sharepoint/what-s-new-in-sharing-in-targeted-release)existente.

> [!NOTE]
> Se você habilitar a visualização para a integração B2B do Azure AD, o compartilhamento do SharePoint e do OneDrive estará sujeito às configurações de relações organizacionais do Azure AD, como **os membros podem convidar** e **convidados podem convidar**.

### <a name="sharing-policies"></a>Compartilhando políticas

O *compartilhamento externo* pode ser definido para o SharePoint e o onedrive. As restrições do OneDrive não podem ser mais permissivas do que as configurações do SharePoint.

 ![Captura de tela das configurações de compartilhamento externo no SharePoint e no OneDrive](media/secure-external-access/9-sharepoint-settings.png)

A integração do SharePoint com o Azure AD B2B altera como os controles interagem com as contas.

* **Qualquer pessoa**. Não recomendado

   * Independentemente do status da integração, habilitar os links de qualquer pessoa significa que nenhuma política do Azure será aplicada quando esse tipo de link for usado. 

   * Em um cenário de colaboração governada, não habilite essa funcionalidade. 
   > [!NOTE]
   > Você pode encontrar um cenário em que você precisa habilitar essa configuração para um site específico; nesse caso, você a habilitará aqui e definirá a restrição maior em sites individuais.

* **Convidados novos e existentes**. Recomendado se você tiver a integração habilitada.

   * **Com a integração B2B do Azure ad** habilitada, os convidados novos e existentes terão uma conta de convidado do Azure ad B2B que pode ser gerenciada com as políticas do Azure AD.

   * **Sem a integração B2B do Azure ad** habilitada, novos convidados não terão uma conta B2B do Azure ad criada e não poderão ser gerenciados do Azure AD. Se os convidados existentes têm uma conta B2B do Azure AD depende de como o convidado foi criado.

* **Convidados existentes**. Recomendado se você não tiver a integração habilitada.

   * Com isso habilitado, os usuários podem compartilhar somente com outros usuários que já estão em seu diretório.

* **Somente as pessoas da sua organização**. Não é recomendado quando você precisa colaborar com usuários externos.

   * Independentemente do status da integração, os usuários só poderão compartilhar com usuários em sua organização. 

* **Limite o compartilhamento externo por domínio**. Por padrão, o SharePoint permite o acesso externo, o que significa que o compartilhamento é permitido com todos os domínios externos. Se você quiser restringir ou permitir domínios específicos apenas para o SharePoint, poderá fazer isso aqui.

* **Permitir que somente os usuários em grupos de segurança específicos sejam compartilhados externamente**.  Essa configuração restringe quem pode compartilhar conteúdo no SharePoint e no OneDrive, enquanto a configuração no Azure AD se aplica a todos os aplicativos. Restringir quem pode compartilhar pode ser útil se você quiser exigir que seus usuários façam um treinamento sobre compartilhamento de forma segura e, em seguida, adicione-os a um grupo de segurança de compartilhamento aprovado. Se essa configuração estiver selecionada e os usuários não tiverem uma maneira de obter acesso a um "compartilhamento aprovado", eles poderão encontrar maneiras não aprovadas de compartilhar. 

* **Permitir que os convidados compartilhem itens que não possuem**. É recomendável deixar essa desabilitada.

* **As pessoas que usam um código de verificação devem autenticar novamente após esse número de dias (o padrão é 30)**. É recomendável habilitar essa configuração.

### <a name="access-controls"></a>Controles de acesso

A configuração de controles de acesso afetará todos os usuários em sua organização. Considerando que talvez você não consiga controlar se os usuários externos têm dispositivos em conformidade, não iremos abordar esses controles aqui. 

* **Logout da sessão ociosa**. Recomendamos que você habilite esse controle, o que permite avisar e sair de usuários em dispositivos não gerenciados após um período de inatividade. Você pode configurar o período de inatividade e o aviso. 

* **Local de rede**. Definir esse controle significa que você pode permitir o acesso apenas a endereços IP de formulário que sua organização possui. Em cenários de colaboração externa, defina isso somente se todos os seus parceiros externos acessarão apenas os recursos em sua rede ou por meio de sua VPN.

### <a name="file-and-folder-links"></a>Links de arquivos e pastas

No centro de administração do SharePoint, você também pode definir como os links de arquivo e pasta são compartilhados. Você também pode definir essas configurações para cada site. 

 ![Captura de tela das configurações de link de arquivo e pasta](media/secure-external-access/9-file-folder-links.png)

Se você tiver habilitado a integração com o Azure AD B2B, o compartilhamento de arquivos e pastas com aqueles fora da organização resultará em um usuário B2B sendo criado quando os arquivos e a pasta forem compartilhados.

É recomendável definir o tipo de link padrão para **somente pessoas em sua organização** e as permissões padrão para **Editar**. Isso garante que os itens sejam compartilhados de forma cuidadosa. Você pode personalizar essa configuração para o padrão por site que atende às necessidades específicas de colaboração.

### <a name="anyone-links"></a>Links para qualquer pessoa

Não recomendamos habilitar links de qualquer pessoa. Se você fizer isso, é recomendável definir uma expiração e considerar restringi-las para exibir permissões. Se você escolher permissões somente de exibição para arquivos ou pastas, os usuários não poderão alterar nenhum link para incluir privilégios de edição.

Para saber mais sobre como governar o acesso externo ao SharePoint, consulte o seguinte:

* [Visão geral do compartilhamento externo do SharePoint](/sharepoint/external-sharing-overview)

* [Integração do SharePoint e do OneDrive com o Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir sobre como proteger o acesso externo aos recursos. Recomendamos que você execute as ações na ordem listada.

1. [Determinar sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descobrir seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governança](3-secure-access-plan.md)

4. [Usar grupos de segurança](4-secure-access-groups.md)

5. [Transição para B2B do Azure AD](5-secure-access-b2b.md)

6. [Acesso seguro com gerenciamento de direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com rótulos de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Proteja o acesso ao Microsoft Teams, onedrive e SharePoint](9-secure-access-teams-sharepoint.md) (você está aqui).