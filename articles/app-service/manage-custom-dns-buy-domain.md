---
title: Comprar o nome de domínio personalizado no Azure – Serviço de Aplicativo
description: Saiba como comprar um nome de domínio personalizado com um aplicativo Web no Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c59e892c8fd5a8bcc74d23e16eaabf1dc1a08f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61272288"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar um nome de domínio personalizado para Serviço de Aplicativo do Azure

Domínios do Serviço de Aplicativo (versão prévia) são domínios de nível superior gerenciados diretamente no Azure. Eles facilitam o gerenciamento de domínios personalizados para [Serviço de Aplicativo do Azure](overview.md). Este tutorial mostra como comprar um domínio de Serviço de Aplicativo e atribuir nomes DNS ao Serviço de Aplicativo do Azure.

Para a VM do Azure ou Armazenamento do Azure, consulte [Atribuir o domínio do Serviço de Aplicativo para a VM Azure ou o Armazenamento do Azure](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.
* [Remova o limite de gastos em minha assinatura](../billing/billing-spending-limit.md#remove). Não é possível comprar domínios do Serviço de Aplicativo com os créditos de assinatura gratuita.

## <a name="prepare-the-app"></a>Preparar o aplicativo

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Para usar domínios personalizados no Serviço de Aplicativo do Azure, o [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo deverá ser uma camada paga (**Compartilhado**, **Básico**, **Standard** ou **Premium**). Nesta etapa, você garante que o aplicativo está no tipo de preço com suporte.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **F1**. Não há suporte para DNS personalizado na camada **F1**. 

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano de serviço de aplicativo não está no **F1** camada, feche o **expandir** página e ir para [comprar o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="pricing-information"></a>Informações sobre preços
Para obter informações sobre preços nos Domínios do Serviço de Aplicativo do Azure, visite a [página Preço de Serviço do Aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/) e role para baixo até o Domínio do Serviço de Aplicativo.

### <a name="sign-in-to-azure"></a>Entrar no Azure
Abra o [portal do Azure](https://portal.azure.com/) e entre com sua conta do Azure.

### <a name="launch-buy-domains"></a>Inicializar Comprar domínio
Na guia **Serviços de Aplicativos**, clique no nome do aplicativo, selecione **Configurações** e, em seguida, selecione **Domínios personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na página **Domínios personalizados**, clique em **Comprar domínio**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se não conseguir ver a seção **Domínios do Serviço de Aplicativo**, você precisará remover o limite de gastos em sua conta do Azure (veja [Pré-requisitos](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Configurar a compra de domínio

Na página **Domínio de Serviço de Aplicativo**, na caixa **Pesquisar domínio**, digite o nome de domínio que você deseja comprar e digite `Enter`. Os domínios disponíveis sugeridos são mostrados logo abaixo da caixa de texto. Selecione um ou mais domínios que deseja comprar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Os [domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) a seguir têm suporte dos domínios do Serviço de Aplicativo: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ e _co.in_.
>
>

Clique em **Informações de Contato** e preencha o formulário de informações de contato do domínio. Quando terminar, clique em **OK** para retornar à página de Domínio do Serviço de Aplicativo.

É importante preencher todos os campos obrigatórios com a máxima precisão possível. Dados incorretos para informações de contato resultarão em falhas em comprar domínios.

Em seguida, selecione as opções desejadas para seu domínio. Consulte a tabela a seguir para obter explicações:

| Configuração | Valor sugerido | DESCRIÇÃO |
|-|-|-|
|Proteção de privacidade | Habilitar | Escolha "Proteção de privacidade," que está incluído no preço de compra _gratuitamente_. Alguns domínios de nível superior são gerenciados pelo registradores que não dão suporte à proteção de privacidade e eles são listados na página **Proteção de privacidade**. |
| Atribuir nomes de host padrão | **www** e **\@** | Se você quiser, selecione as associações de nome do host desejadas. Quando a operação de compra de domínio for concluída, o aplicativo poderá ser acessado nos nomes do host selecionados. Se o aplicativo estiver atrás do [Gerenciador de Tráfego do Microsoft Azure](https://azure.microsoft.com/services/traffic-manager/), você não verá a opção para atribuir o domínio raiz (@) porque o Gerenciador de Tráfego não dá suporte a registros A. Você pode fazer alterações às atribuições de nome do host após a compra de domínio ser concluída. |

### <a name="accept-terms-and-purchase"></a>Aceitar os termos e comprar

Clique em **Termos Legais** para examinar os termos e os encargos, clique em **Comprar**.

> [!NOTE]
> Domínios do serviço de aplicativo usam GoDaddy para registro de domínio e DNS do Azure para hospedar os domínios. Além da taxa de registro de domínio, encargos de uso do DNS do Azure se aplicam. Para obter informações, consulte [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta à página **Domínio de Serviço de Aplicativo**, clique em **OK**. Enquanto a operação estiver em andamento, você verá as seguintes notificações:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Se você tiver atribuído nomes do host padrão ao aplicativo, também verá uma notificação de êxito para cada nome do host selecionado. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Você também verá os nomes de host selecionados na página **Domínios personalizados** na seção **Nomes de host personalizados**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Para testar os nomes de host, navegue até os nomes de host listados no navegador. No exemplo na captura de tela anterior, tente navegar até _kontoso.net_ e _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Atribuir nomes do host ao aplicativo

Se você optar por não atribuir um ou mais nomes do host padrão ao aplicativo durante o processo de compra, ou se for necessário atribuir um nome do host não listado, você poderá atribuir um nome do host a qualquer momento.

Além disso, é possível atribuir nomes do host no Domínio do Serviço de Aplicativo a qualquer outro aplicativo. As etapas dependem se o Domínio do Serviço de Aplicativo e o aplicativo pertencerem à mesma assinatura.

- Assinatura diferente: Mapeie registros DNS personalizados do Domínio do Serviço de Aplicativo para o aplicativo, como um domínio comprado externamente. Para obter informações sobre como adicionar nomes DNS personalizados a um Domínio do Serviço de Aplicativo, consulte [Gerenciar registros de DNS personalizados](#custom). Para mapear um domínio comprado externo para um aplicativo, consulte [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md). 
- Mesma assinatura: Use as etapas a seguir.

### <a name="launch-add-hostname"></a>Inicializar adição de nome do host
Na página **Serviços de Aplicativos**, selecione o nome do aplicativo para o qual deseja atribuir nomes do host, selecione **Configurações** e, em seguida, selecione **Domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que seu domínio adquirido esteja listado na seção **Domínios do Serviço de Aplicativo**, mas não o selecione. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os Domínios do Serviço de Aplicativo na mesma assinatura são mostrados na página **Domínios personalizados** do aplicativo. Se o domínio estiver na assinatura do aplicativo, mas você não puder vê-lo no aplicativo **Domínios personalizados** do aplicativo, tente reabrir a página **Domínios personalizados** ou atualizar a página da Web. Além disso, verifique o sino de notificação na parte superior do portal do Azure para progresso ou falhas de criação.
>
>

Selecione **Adicionar nome do host**.

### <a name="configure-hostname"></a>Configurar nome do host
Na caixa de diálogo **Adicionar nome do host**, digite o nome de domínio totalmente qualificado do Domínio do Serviço de Aplicativo ou qualquer subdomínio. Por exemplo: 

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Quando terminar, selecione **Validar**. O tipo de registro de nome do host é selecionado automaticamente para você.

Selecione **Adicionar nome do host**.

Quando a operação for concluída, você verá uma notificação de êxito para o nome do host atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Feche Adicionar nome do host
Na página **Adicionar nome do host**, atribua qualquer outro nome do host ao aplicativo, conforme desejado. Quando terminar, feche a página **Adicionar nome do host**.

Agora você deve ver os nomes do host recentemente atribuídos na página **Domínios personalizados** do seu aplicativo.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Navegue até o nome do host listado no navegador. No exemplo na captura de tela anterior, tente navegar para _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio Serviço de Aplicativo que você comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio é configurado para ser renovado automaticamente cobrando seu método de pagamento para o próximo ano. Se você deseja desativar a renovação automática ou se quiser renovar manualmente seu domínio, siga as etapas aqui.

Na guia **Serviços de Aplicativos**, clique no nome do aplicativo, selecione **Configurações** e, em seguida, selecione **Domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na seção **Domínios do Serviço de Aplicativo**, selecione o domínio que deseja configurar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

No painel de navegação esquerdo do domínio, selecione **Renovação de domínio**. Para parar a renovação de seu domínio automaticamente, selecione **Desativar** e, em seguida, **Salvar**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Para renovar manualmente seu domínio, selecione **Renovar domínio**. No entanto, esse botão não estará ativo até 90 dias antes da expiração do domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerenciar registros DNS personalizados

No Azure, os registros DNS para um Domínio do Serviço de Aplicativo são gerenciados usando [DNS do Azure](https://azure.microsoft.com/services/dns/). Você pode adicionar, remover e atualizar registros DNS, assim como para um domínio adquirido externamente.

### <a name="open-app-service-domain"></a>Abrir o Domínio do Serviço de Aplicativo

No Portal do Azure, no menu esquerdo, selecione **Todos os serviços** > **Domínios do Serviço de Aplicativo**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a gerenciar. 

### <a name="access-dns-zone"></a>Acessar zona DNS

No menu da esquerda do domínio, selecione **zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Essa ação abre a página [Zona DNS](../dns/dns-zones-records.md) do seu Domínio do Serviço de Aplicativo no DNS do Azure. Para obter informações sobre como editar os registros DNS, consulte [Como gerenciar zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de comprar o Domínio do Serviço de Aplicativo, você tem cinco dias para cancelar sua compra para obter um reembolso integral. Depois de cinco dias, você pode excluir o Domínio do Serviço de Aplicativo, mas não pode receber um reembolso.

### <a name="open-app-service-domain"></a>Abrir o Domínio do Serviço de Aplicativo

No Portal do Azure, no menu esquerdo, selecione **Todos os serviços** > **Domínios do Serviço de Aplicativo**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio que você deseja cancelar ou excluir. 

### <a name="delete-hostname-bindings"></a>Excluir associações de nome de host

No menu da esquerda do domínio, selecione **Associações de nome do host**. As associações de nome do host de todos os serviços do Azure são listadas aqui.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Você não pode excluir o Domínio de Serviço de Aplicativo até que todas as associações de nome de host sejam excluídas.

Exclua cada associação de nome do host selecionando **…** > **Excluir**. Depois que todas as associações forem excluídas, selecione **Salvar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou excluir

No menu da esquerda do domínio, selecione **Visão geral**. 

Se o período de cancelamento do domínio adquirido não tiver se passado, selecione **Cancelar compra**. Caso contrário, você verá um botão **Excluir** em vez disso. Para excluir o domínio sem um reembolso, selecione **Excluir**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Para confirmar a operação, selecione **Sim**.

Depois que a operação estiver concluída, o domínio será liberado da sua assinatura e ficará disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar a URL padrão para um diretório personalizado

Por padrão, o Serviço de Aplicativo direciona solicitações da Web para o diretório raiz do seu código de aplicativo. Para direcioná-los para um subdiretório, como o `public`, veja [Direcionar a URL padrão para um diretório personalizado](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Mais recursos

[Perguntas frequentes: Domínio do Serviço de Aplicativo (versão prévia) e Domínios Personalizados](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
