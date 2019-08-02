---
title: Aplicativos curinga no proxy do aplicativo do Azure Active Directory | Microsoft Docs
description: Saiba como usar aplicativos curinga no proxy do aplicativo do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d3b8176566593c5c9e9ff63a6ccbafcb2a35cd5
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828000"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Aplicativos curinga no proxy do aplicativo do Azure Active Directory

No Azure Active Directory (Azure AD), a configuração de um grande número de aplicativos locais poderá se tornar rapidamente incontrolável e apresentar riscos desnecessários de erros de configuração, se muitas deles exigirem as mesmas configurações. Com [Proxy de Aplicativo do Azure AD](application-proxy.md), você pode resolver esse problema usando a publicação de aplicativos curinga para publicar e gerenciar muitos aplicativos de uma só vez. Esta é uma solução que permite:

- Simplificar a sobrecarga administrativa
- Reduzir o número de erros de configuração em potencial
- Permitir que os usuários acessem mais recursos de maneira segura

Este artigo apresenta as informações de que você precisa para configurar a publicação de aplicativos curinga no ambiente.

## <a name="create-a-wildcard-application"></a>Criar um aplicativo curinga

Você poderá criar um aplicativo curinga (*), se tiver um grupo de aplicativos com a mesma configuração. Os candidatos em potencial para um aplicativo curinga são aplicativos que compartilham as seguintes configurações:

- O grupo de usuários que têm acesso a eles
- O método SSO
- O protocolo de acesso (http, https)

Você poderá publicar aplicativos com curingas se ambas as URLs, interna e externa, estiverem no seguinte formato:

> http(s)://*.\<domain\>

Por exemplo: `http(s)://*.adventure-works.com`.

Embora as URLs interna e externa possam usar domínios diferentes, como melhor prática, elas devem ser iguais. Ao publicar o aplicativo, você verá um erro se uma das URLs não tiver um curinga.

Se tiver aplicativos adicionais com definições de configuração diferentes, você deverá publicar essas exceções como aplicativos à parte para substituir os padrões definidos para o curinga. Aplicativos sem um curinga sempre têm precedência sobre aplicativos curinga. Do ponto de vista da configuração, esses são aplicativos "apenas" regulares.

A criação de um aplicativo curinga se baseia no mesmo [fluxo de publicação do aplicativo](application-proxy-add-on-premises-application.md) disponível para todos os outros aplicativos. A única diferença é que você inclui um curinga na URL e, talvez, a configuração SSO.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que atender esses requisitos.

### <a name="custom-domains"></a>Domínios personalizados

Embora [domínios personalizados](application-proxy-configure-custom-domain.md) sejam opcionais para todos os outros aplicativos, eles são um pré-requisito para aplicativos curinga. A criação de domínios personalizados requer:

1. Crie um domínio verificado no Azure.
1. Carregue um certificado SSL no formato PFX para o proxy do aplicativo.

Você deve considerar o uso de um certificado curinga de acordo com o aplicativo que pretende criar. Você também pode usar um certificado que liste apenas aplicativos específicos. Neste caso, somente os aplicativos listados no certificado serão acessíveis por meio desse aplicativo curinga.

Por motivos de segurança, este é um requisito fixo, e não daremos suporte a curingas para aplicativos que não possam usar um domínio personalizado para a URL externa.

### <a name="dns-updates"></a>Atualizações DNS

Ao usar domínios personalizados, você precisa criar uma entrada DNS com um registro CNAME para a URL externa (por exemplo, `*.adventure-works.com`) apontando para a URL externa do ponto de extremidade de proxy do aplicativo. Para aplicativos curinga, o registro CNAME deve apontar para as URLs externas relevantes:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Para confirmar se configurou o CNAME corretamente, você pode usar [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) em um dos pontos de extremidade de destino, por exemplo, `expenses.adventure-works.com`.  A resposta deve incluir o alias já mencionado (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Considerações

Aqui estão algumas considerações que você deve levar em conta para aplicativos curinga.

### <a name="accepted-formats"></a>Formatos aceitos

Para aplicativos curinga, a **URL interna** deve ser formatada como `http(s)://*.<domain>`.

![Para a URL interna, use o formato http :/ / *. \<domínio >](./media/application-proxy-wildcard/22.png)

Ao configurar uma **URL externa**, você deve usar o seguinte formato: `https://*.<custom domain>`

![Para a URL externa, use o formato https://*. \<domínio personalizado >](./media/application-proxy-wildcard/21.png)

Outras posições do curinga, vários curingas ou outras cadeias de caracteres regex não são compatíveis e estão causando erros.

### <a name="excluding-applications-from-the-wildcard"></a>Exclusão de aplicativos do curinga

Você pode excluir um aplicativo do aplicativo curinga

- Publicando o aplicativo de exceção como aplicativo regular
- Habilitando o curinga somente para aplicativos específicos por meio das configurações DNS

A publicação de um aplicativo como aplicativo regular é o método preferido para excluir um aplicativo de um curinga. Você deve publicar os aplicativos excluídos antes dos aplicativos curinga para garantir que as exceções sejam impostas desde o início. O aplicativo mais específico sempre terá precedência – um aplicativo publicado como `budgets.finance.adventure-works.com` tem precedência sobre o aplicativo `*.finance.adventure-works.com`, que, por sua vez, tem precedência sobre o aplicativo `*.adventure-works.com`.

Você também pode limitar o curinga para funcionar apenas para aplicativos específicos por meio do gerenciamento DNS. Como melhor prática, você deve criar uma entrada CNAME que inclua um curinga e corresponda ao formato da URL externa configurada. No entanto, em vez disso, você pode apontar as URLs dos aplicativos específicos para os curingas. Por exemplo, em vez de `*.adventure-works.com`, aponte `hr.adventure-works.com`, `expenses.adventure-works.com` e `travel.adventure-works.com individually` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Se usar essa opção, você também precisará de outra entrada CNAME para o valor `AppId.domain`, por exemplo, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, também apontando para o mesmo local. Você pode encontrar o **AppId** na página de propriedades do aplicativo curinga:

![Localizar a ID do aplicativo na página de propriedades do aplicativo](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Definindo a URL da home page do painel MyApps

O aplicativo curinga é representado com apenas um bloco no [painel MyApps](https://myapps.microsoft.com). Por padrão, esse bloco permanece oculto. Para mostrar o bloco e fazer os usuários aterrissarem em uma página específica:

1. Siga as diretrizes para [definir uma URL da home page](application-proxy-configure-custom-home-page.md).
1. Defina **Mostrar Aplicativo** como **verdadeiro** na página de propriedades do aplicativo.

### <a name="kerberos-constrained-delegation"></a>Delegação restrita de Kerberos

Para aplicativos que usem [delegação restrita de Kerberos (KCD) como o método SSO](application-proxy-configure-single-sign-on-with-kcd.md), o SPN listado para o método SSO também pode precisar de um curinga. Por exemplo, o SPN pode ser: `HTTP/*.adventure-works.com`. Você ainda precisa ter os SPNs individuais configurados nos servidores back-end (por exemplo, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Cenário 1: Aplicativo curinga geral

Neste cenário, você tem três aplicativos diferentes que deseja publicar:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Todos os três aplicativos:

- São usados por todos os usuários
- Usam *Autenticação Integrada do Windows*
- Têm as mesmas propriedades

Você pode publicar o aplicativo curinga usando as etapas descritas em [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md). Este cenário pressupõe:

- Um locatário com a seguinte ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Um domínio verificado chamado `adventure-works.com` foi configurado.
- Uma entrada **CNAME** que aponta `*.adventure-works.com` para `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` foi criada.

Seguindo as [etapas documentadas](application-proxy-add-on-premises-application.md), você cria um novo aplicativo de proxy do aplicativo no locatário. Neste exemplo, o curinga está nos seguintes campos:

- URL Interna:

    ![Exemplo: Curinga na URL interna](./media/application-proxy-wildcard/42.png)

- URL Externa:

    ![Exemplo: Curinga na URL externa](./media/application-proxy-wildcard/43.png)

- SPN do Aplicativo Interno:

    ![Exemplo: Curinga na configuração de SPN](./media/application-proxy-wildcard/44.png)

Publicando o aplicativo curinga, você já pode acessar os três aplicativos navegando até as URLs a que já está acostumado (por exemplo, `travel.adventure-works.com`).

A configuração implementa a seguinte estrutura:

![Mostra a estrutura implementada pela configuração de exemplo](./media/application-proxy-wildcard/05.png)

| Cor | DESCRIÇÃO |
| ---   | ---         |
| Azul  | Aplicativos publicados explicitamente e visíveis no portal do Azure. |
| Cinza  | Os aplicativos que você pode acessar por meio do aplicativo pai. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Cenário 2: Aplicativo curinga geral com exceção

Neste cenário, você tem, além dos três aplicativos gerais, outro aplicativo, `finance.adventure-works.com`, que só deve ser acessado pela divisão de finanças. Com a estrutura do aplicativo atual, o aplicativo de finanças seria acessível por meio do aplicativo curinga e por todos os funcionários. Para mudar isso, você exclui o aplicativo do curinga configurando o de finanças como um aplicativo à parte com permissões mais restritivas.

Você precisa se certificar de que os registros CNAME existam apontando `finance.adventure-works.com` para o ponto de extremidade específico do aplicativo, indicado na página Proxy do aplicativo. Neste cenário, `finance.adventure-works.com` aponta para `https://finance-awcycles.msappproxy.net/`.

Após as [etapas documentadas](application-proxy-add-on-premises-application.md), este cenário requer as seguintes configurações:

- Na **URL Interna**, você define **finanças**, em vez de um curinga.

    ![Exemplo: Defina finanças, em vez de um caractere curinga na URL interna](./media/application-proxy-wildcard/52.png)

- Na **URL Externa**, você define **finanças**, em vez de um curinga.

    ![Exemplo: Definir finanças, em vez de um caractere curinga na URL externa](./media/application-proxy-wildcard/53.png)

- SPN do aplicativo interno definido como **finanças**, em vez de um curinga.

    ![Exemplo: Conjunto de finanças, em vez de um caractere curinga na configuração de SPN](./media/application-proxy-wildcard/54.png)

Esta configuração implementa o seguinte cenário:

![Mostra a configuração implementada pelo cenário de exemplo](./media/application-proxy-wildcard/09.png)

Como `finance.adventure-works.com` é uma URL mais específica que `*.adventure-works.com`, ela tem precedência. Os usuários que navegarem para `finance.adventure-works.com` têm a experiência especificada no aplicativo de recursos de finanças. Neste caso, somente os funcionários de finanças podem acessar `finance.adventure-works.com`.

Se tiver vários aplicativos publicados para finanças e `finance.adventure-works.com` como um domínio verificado, você poderá publicar outro aplicativo curinga `*.finance.adventure-works.com`. Como é mais específica do que a `*.adventure-works.com` genérica, ela tem precedência, caso um usuário acesse um aplicativo no domínio de finanças.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre **domínios personalizados**, consulte [trabalhando com domínios personalizados no Proxy de aplicativo do Azure AD](application-proxy-configure-custom-domain.md).
- Para saber mais sobre **publicação de aplicativos**, consulte [publicar aplicativos usando o Proxy de aplicativo do Azure AD](application-proxy-add-on-premises-application.md)
