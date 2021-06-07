---
title: Campos de registro do portal do Azure para aplicativos personalizados
description: Diretrizes para registrar um aplicativo personalizado desenvolvido com o Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 82c3dd4ce7f5e7e9f3d5a226bfe65e27eca2d3d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103236"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Campos de registro do portal do Azure para aplicativos personalizados

Este artigo oferece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

-   Para registrar um novo aplicativo, navegue até o <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.

-   No painel de navegação esquerdo, clique em **Azure Active Directory.**

-   Escolha **Registros do aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos do formulário de registro do aplicativo

| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome do aplicativo. Ele deve ter um mínimo de quatro caracteres.                |
| Tipos de conta com suporte| Selecione as contas para as quais o aplicativo deverá dar suporte: apenas contas neste diretório organizacional, contas em qualquer diretório organizacional ou contas em qualquer diretório organizacional mais contas Microsoft pessoais.  |
| Redirecionar URI (opcional) | Selecione o tipo de aplicativo que você está compilando, **Web** ou **Cliente público (dispositivos móveis e desktop)** , e insira o URI de redirecionamento (ou a URL de resposta) do aplicativo. Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, http://localhost:31544 pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web. Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico ao aplicativo, como myapp://auth. Veja exemplos específicos para aplicativos Web ou aplicativos nativos nos [guias de início rápido](./index.yml).|

Após o preenchimento dos campos acima, o aplicativo será registrado no portal do Azure, e você será redirecionado para a página de visão geral do aplicativo. As páginas de configurações no painel esquerdo em **Gerenciar** têm mais campos para personalizar o aplicativo. As tabelas a seguir descrevem todos os campos. Você verá apenas um subconjunto desses campos, dependendo do tipo de aplicativo que você criou, um aplicativo Web ou aplicativo de cliente público.

### <a name="overview"></a>Visão geral

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID do aplicativo  | Quando você registra um aplicativo, o Azure AD atribui a seu aplicativo uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente o aplicativo nas solicitações de autenticação no Azure AD, além de acessar recursos, como a API do Graph.                                                          |
| URI da ID do aplicativo      | Deve ser um URI exclusivo, geralmente com o formato **https://&lt;locatário\_nome&gt;/&lt;aplicativo\_nome&gt;.** Ele é usado durante o fluxo de concessão de autorização como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também se torna a declaração "aud" no token de acesso emitido. |

### <a name="branding"></a>Identidade visual

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logotipo | Pode ser usado para carregar um logotipo para seu aplicativo. O logotipo deve estar no formato .bmp, .jpg ou .png, e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com as dimensões da imagem central de 94x94 pixels.|
| URL da home page   | É a URL de logon especificada durante o registro do aplicativo.|

### <a name="authentication"></a>Autenticação

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL de logoff de front-channel      | É a URL de logoff único. O Azure AD envia uma solicitação de logoff para essa URL quando o usuário limpa a sessão com o Azure AD usando qualquer outro aplicativo registrado.|
| Tipos de conta com suporte  | Esta opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.|
| URLs de redirecionamento      | As URLs de redirecionamento ou de resposta são pontos de extremidade em que o Azure AD retorna os tokens que o aplicativo solicita. Para aplicativos nativos, é para onde o usuário é enviado após uma autorização bem-sucedida. O Azure AD verifica se o URI de redirecionamento que o aplicativo informou na solicitação OAuth 2.0 corresponde a um dos valores registrados no portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos do cliente            | Você pode criar segredos do cliente ou chaves para acessar programaticamente APIs Web protegidas pelo Azure AD sem interação do usuário. Na página **Novo segredo do cliente**, insira uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-la em algum lugar seguro, pois você não poderá acessá-la depois.             |

## <a name="next-steps"></a>Próximas etapas

[Gerenciamento de aplicativos com o Active Directory do Azure](../manage-apps/what-is-application-management.md)