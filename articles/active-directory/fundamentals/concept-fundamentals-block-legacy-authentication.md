---
title: Bloqueando protocolos de autenticação herdados no Azure AD
description: Saiba como e por que as organizações devem bloquear protocolos de autenticação herdados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 24640254f32270b8c96c790dca7db31e285cc27f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895281"
---
# <a name="blocking-legacy-authentication"></a>Bloqueio da autenticação herdada
 
Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. Autenticação herdada é um termo que se refere a uma solicitação de autenticação feita por:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Qualquer cliente que usa protocolos de email herdados, como IMAP/SMTP/POP3

Hoje, a maioria de todas as tentativas de entrada comprometentes vêm da autenticação herdada. A autenticação herdada não dá suporte à autenticação multifator (MFA). Mesmo que você tenha uma política de MFA habilitada em seu diretório, um ator inadequado pode autenticar usando um protocolo herdado e ignorar MFA. A melhor maneira de proteger sua conta contra solicitações de autenticação mal-intencionadas feitas por protocolos herdados é bloquear completamente essas tentativas.

## <a name="identify-legacy-authentication-use"></a>Identificar uso de autenticação herdada

Antes de poder bloquear a autenticação herdada em seu diretório, primeiro você precisará entender se os usuários têm aplicativos que usam autenticação herdada e como ele afeta o diretório geral. Os logs de entrada do Azure AD podem ser usados para entender se você está usando a autenticação herdada.

1. Navegue até o **portal do Azure** > **Azure Active Directory** > **Entradas**.
1. Adicione a coluna **aplicativo cliente** se ela não for exibida clicando em  **colunas**   >  **aplicativo cliente**.
1. Filtrar por **aplicativo cliente** > verificar todas as opções de **clientes de autenticação herdadas** apresentadas.
1. Filtrar por **status** de  >  **êxito**. 
1. Expanda seu intervalo de datas, se necessário, usando o filtro de **Data** .
1. Se você ativou a [nova visualização de relatórios de atividade de entrada](../reports-monitoring/concept-all-sign-ins.md), repita as etapas acima também na guia entradas de **usuário (não interativa)** .

A filtragem mostrará apenas as tentativas de entrada bem-sucedidas feitas pelos protocolos de autenticação herdados selecionados. Clicar em cada tentativa de entrada individual mostrará detalhes adicionais. A coluna aplicativo cliente ou o campo aplicativo cliente na guia informações básicas, depois de selecionar uma linha individual de dados, indicará qual protocolo de autenticação herdado foi usado. Esses logs indicarão quais usuários ainda estão dependendo da autenticação herdada e quais aplicativos estão usando protocolos herdados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e são confirmados para não usar a autenticação herdada, implemente uma política de acesso condicional ou habilite a política de linha de base: bloquear a autenticação herdada somente para esses usuários.

## <a name="moving-away-from-legacy-authentication"></a>Afastando da autenticação herdada 

Depois de ter uma ideia melhor de quem está usando a autenticação herdada em seu diretório e quais aplicativos dependem dele, a próxima etapa é atualizar seus usuários para usar a autenticação moderna. A autenticação moderna é um método de gerenciamento de identidades que oferece autenticação e autorização de usuário mais seguras. Se você tiver uma política de MFA em vigor no seu diretório, a autenticação moderna garantirá que o usuário seja solicitado para a MFA quando necessário. É a alternativa mais segura para protocolos de autenticação herdados.

Esta seção fornece uma visão geral passo a passo sobre como atualizar seu ambiente para a autenticação moderna. Leia as etapas abaixo antes de habilitar uma política de bloqueio de autenticação herdada em sua organização.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Etapa 1: habilitar a autenticação moderna em seu diretório

A primeira etapa para habilitar a autenticação moderna é verificar se o diretório dá suporte à autenticação moderna. A autenticação moderna é habilitada por padrão para diretórios criados em ou após 1º de agosto de 2017. Se seu diretório foi criado antes dessa data, você precisará habilitar manualmente a autenticação moderna para seu diretório usando as seguintes etapas:

1. Verifique se o diretório já dá suporte à autenticação moderna executando  `Get-CsOAuthConfiguration`   no módulo do [PowerShell do Skype for Business online](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se o comando retornar uma  `OAuthServers`   Propriedade vazia, a autenticação moderna será desabilitada. Atualize a configuração para habilitar a autenticação moderna usando  `Set-CsOAuthConfiguration` . Se sua  `OAuthServers`   Propriedade contiver uma entrada, você estará pronto para começar.

Certifique-se de concluir esta etapa antes de prosseguir. É essencial que suas configurações de diretório sejam alteradas primeiro porque ditam qual protocolo será usado por todos os clientes do Office. Mesmo se você estiver usando clientes do Office que dão suporte à autenticação moderna, eles usarão como padrão os protocolos herdados se a autenticação moderna estiver desabilitada em seu diretório.

### <a name="step-2-office-applications"></a>Etapa 2: aplicativos do Office

Depois de habilitar a autenticação moderna em seu diretório, você pode iniciar a atualização de aplicativos habilitando a autenticação moderna para clientes do Office. Os clientes do Office 2016 ou posterior dão suporte à autenticação moderna por padrão. Nenhuma etapa extra é necessária.

Se você estiver usando clientes Windows do Office 2013 ou mais antigo, recomendamos atualizar para o Office 2016 ou posterior. Mesmo depois de concluir a etapa anterior de habilitar a autenticação moderna em seu diretório, os aplicativos mais antigos do Office continuarão a usar protocolos de autenticação herdados. Se você estiver usando clientes do Office 2013 e não puder atualizar imediatamente para o Office 2016 ou posterior, siga as etapas no artigo a seguir para [habilitar a autenticação moderna para o office 2013 em dispositivos Windows](/office365/admin/security-and-compliance/enable-modern-authentication). Para ajudar a proteger sua conta enquanto você estiver usando a autenticação herdada, é recomendável usar senhas fortes em seu diretório. Confira a [proteção de senha do Azure ad](../authentication/concept-password-ban-bad.md)   para proibir senhas fracas em seu diretório.

O Office 2010 não oferece suporte à autenticação moderna. Será necessário atualizar todos os usuários com o Office 2010 para uma versão mais recente do Office. É recomendável atualizar para o Office 2016 ou posterior, pois ele bloqueia a autenticação herdada por padrão.

Se você estiver usando o macOS, recomendamos atualizar para o Office para Mac 2016 ou posterior. Se você estiver usando o cliente de email nativo, será necessário ter o macOS versão 10,14 ou posterior em todos os dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Etapa 3: Exchange e SharePoint

Para clientes do Outlook baseados no Windows usarem a autenticação moderna, o Exchange Online também deve ser uma autenticação moderna habilitada. Se a autenticação moderna estiver desabilitada para o Exchange Online, os clientes do Outlook baseados no Windows que dão suporte à autenticação moderna (Outlook 2013 ou posterior) usarão a autenticação básica para se conectarem às caixas de correio do Exchange Online.

O SharePoint Online está habilitado para o padrão de autenticação moderna. Para diretórios criados após 1º de agosto de 2017, a autenticação moderna é habilitada por padrão no Exchange Online. No entanto, se você tiver desabilitado anteriormente a autenticação moderna ou estiver usando um diretório criado antes dessa data, siga as etapas no artigo a seguir para [habilitar a autenticação moderna no Exchange Online](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Etapa 4: Skype for Business

Para evitar solicitações de autenticação herdadas feitas pelo Skype for Business, é necessário habilitar a autenticação moderna para o Skype for Business online. Para diretórios criados após 1º de agosto de 2017, a autenticação moderna para o Skype for Business é habilitada por padrão.

Sugerimos que você migre para o Microsoft Teams, que dá suporte à autenticação moderna por padrão. No entanto, se não for possível migrar no momento, será necessário habilitar a autenticação moderna para o Skype for Business online para que os clientes do Skype for Business comecem a usar a autenticação moderna. Siga as etapas neste artigo [topologias do Skype for Business com suporte com autenticação moderna](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), para habilitar a autenticação moderna para o Skype for Business.

Além de habilitar a autenticação moderna para o Skype for Business Online, é recomendável habilitar a autenticação moderna para o Exchange Online ao habilitar a autenticação moderna para o Skype for Business. Esse processo ajudará a sincronizar o estado da autenticação moderna no Exchange Online e no Skype for Business Online e impedirá várias solicitações de entrada para clientes do Skype for Business.

### <a name="step-5-using-mobile-devices"></a>Etapa 5: usando dispositivos móveis

Os aplicativos em seu dispositivo móvel precisam bloquear a autenticação herdada também. É recomendável usar o Outlook para dispositivos móveis. O Outlook para dispositivos móveis dá suporte à autenticação moderna por padrão e atenderá a outras políticas de proteção de linha de base do MFA.

Para usar o cliente de correio iOS nativo, você precisará executar o iOS versão 11,0 ou posterior para garantir que o cliente de email tenha sido atualizado para bloquear a autenticação herdada.

### <a name="step-6-on-premises-clients"></a>Etapa 6: clientes locais

Se você for um cliente híbrido usando o Exchange Server local e o Skype for Business local, ambos os serviços precisarão ser atualizados para habilitar a autenticação moderna. Ao usar a autenticação moderna em um ambiente híbrido, você ainda está Autenticando os usuários no local. A história de autorizar o acesso a recursos (arquivos ou emails) é alterada.

Antes de começar a habilitar a autenticação moderna no local, certifique-se de que você atendeu aos pré-requisitos. Agora você está pronto para habilitar a autenticação moderna no local.

As etapas para habilitar a autenticação moderna podem ser encontradas nos seguintes artigos:

* [Como configurar o Exchange Server local para usar a autenticação moderna híbrida](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Como usar a ADAL (autenticação moderna) com o Skype for Business](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o Exchange Server local para usar a autenticação moderna híbrida](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Como usar a ADAL (autenticação moderna) com o Skype for Business](/skypeforbusiness/manage/authentication/use-adal)
- [Bloquear a autenticação herdada](../conditional-access/block-legacy-authentication.md)