---
title: "Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como migrar um nome de domínio DNS personalizado que já está atribuído a um site ativo ao Serviço de Aplicativo do Azure sem nenhum tempo de inatividade."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: a1fe545e4a341709232cba36c6e3cf3b4ce82e80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrar um nome DNS ativo para o Serviço de Aplicativo do Azure

Este artigo mostra como migrar um nome DNS ativo para o [Serviço de Aplicativo do Azure](../app-service/app-service-web-overview.md) sem nenhum tempo de inatividade.

Quando você migra um site ativo e seu nome de domínio DNS para o Serviço de Aplicativo, o nome DNS já está atendendo ao tráfego em tempo real. Evite o tempo de inatividade na resolução do DNS durante a migração associando o nome DNS ativo preventivamente ao aplicativo Serviço de Aplicativo.

Caso não esteja preocupado com o tempo de inatividade na resolução do DNS, consulte [Mapear um nome DNS personalizado existente para Aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estas instruções:

- [Verifique se o aplicativo Serviço de Aplicativo não está na camada GRATUITA](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vincular o nome de domínio preventivamente

Ao vincular um domínio personalizado preventivamente, você obtém o seguinte antes mesmo de fazer quaisquer alterações nos registros DNS:

- Verificar a propriedade de domínio
- Habilitar o nome de domínio para seu aplicativo

Quando você finalmente migrar o nome DNS personalizado do site antigo para o aplicativo Serviço de Aplicativo, não haverá nenhum tempo de inatividade na resolução do DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Criar registro de verificação de domínio

Para verificar a propriedade do domínio, adicione um registro TXT. O registro TXT mapeia de _awverify.&lt;subdomain>_ para _&lt;appname>.azurewebsites.net_. 

O registro TXT necessário depende do registro DNS que você deseja migrar. Para obter exemplos, consulte a seguinte tabela (`@` normalmente representa o domínio raiz):  

| Exemplo de registro DNS | Host TXT | Valor TXT |
| - | - | - |
| @ (raiz) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (curinga) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na página de registros DNS, anote o tipo de registro do nome DNS que você deseja migrar. O Serviço de Aplicativo dá suporte a mapeamentos de CNAME e registros A.

### <a name="enable-the-domain-for-your-app"></a>Habilitar o domínio para o aplicativo

No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda da página do aplicativo, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, selecione o ícone **+** ao lado de **Adicionar nome do host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado ao qual você adicionou o registro TXT, como `www.contoso.com`. Para um domínio de curinga (como \*.contoso.com), você pode usar qualquer nome DNS que corresponde ao domínio de curinga. 

Selecione **Validar**.

O botão **Adicionar nome do host** é ativado. 

Verifique se **Tipo de registro do nome do host** está definido como o tipo de registro DNS que você deseja migrar.

Selecione **Adicionar nome do host**.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode levar algum tempo para que o novo nome do host seja refletido na página **Domínios personalizados** do aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Agora, o nome DNS personalizado está habilitado no Azure App. 

## <a name="remap-the-active-dns-name"></a>Remapear o nome DNS ativo

Agora só resta remapear o registro DNS ativo para que ele aponte para o Serviço de Aplicativo. No momento, ele ainda aponta para o site antigo.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Copiar o endereço IP do aplicativo (somente o registro A)

Se estiver fazendo o remapeamento de um registro CNAME, ignore esta seção. 

Para remapear um registro A, você precisa do endereço IP externo do aplicativo Serviço de Aplicativo, que é mostrado na página **Domínios personalizados**.

Feche a página **Adicionar nome do host** selecionando **X** no canto superior direito. 

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Atualizar o registro DNS

Novamente na página de registros DNS do provedor de domínio, selecione o registro DNS a ser remapeado.

Para o exemplo de domínio raiz `contoso.com`, remapeie o registro A ou CNAME como os exemplos na seguinte tabela: 

| Exemplo de FQDN | Tipo de registro | Host | Valor |
| - | - | - | - |
| contoso.com (raiz) | O  | `@` | Endereço IP de [Copiar o endereço IP do aplicativo](#info) |
| www.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com (curinga) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Salve suas configurações.

As consultas DNS devem começar a serem resolvidas para o aplicativo Serviço de Aplicativo imediatamente após a propagação do DNS.

## <a name="next-steps"></a>Próximas etapas

Saiba como associar um certificado SSL personalizado ao Serviço de Aplicativo.

> [!div class="nextstepaction"]
> [Associar um certificado SSL personalizado existente a Aplicativos Web do Azure](app-service-web-tutorial-custom-ssl.md)
