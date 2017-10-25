---
title: "Comprar um nome de domínio personalizado para aplicativos Web do Azure"
description: "Saiba como comprar um nome de domínio personalizado com um aplicativo Web no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 3cb22b935624041ab51e64028a1b668fd694f9b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Comprar um nome de domínio personalizado para aplicativos Web do Azure

Domínios do Serviço de Aplicativo (versão prévia) são domínios de nível superior gerenciados diretamente no Azure. Eles facilitam o gerenciamento de domínios personalizados para [Aplicativos Web do Azure](app-service-web-overview.md). Este tutorial mostra como comprar um domínio de Serviço de Aplicativo e atribuir nomes DNS a Aplicativos Web do Azure.

Este artigo é para o Serviço de Aplicativo do Azure (aplicativos Web, aplicativos de API, aplicativos móveis, aplicativos lógicos). Para a VM do Azure ou Armazenamento do Azure, consulte [Atribuir o domínio do Serviço de Aplicativo para a VM Azure ou o Armazenamento do Azure](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para usar domínios personalizados em Aplicativos Web do Azure, o [plano de Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do seu aplicativo Web deve ser uma camada paga (**Shared**, **Basic**, **Standard** ou **Premium**). Nesta etapa, você precisa ter certeza de que seu aplicativo Web está no tipo de preço com suporte.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o [portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para o aplicativo no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicativos** e, em seguida, selecione o nome do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

A página de gerenciamento do aplicativo do Serviço de Aplicativo é exibida.  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da página do aplicativo, role até a seção **Configurações** e selecione **Escalar verticalmente (plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo não está na camada **Gratuita**. Não há suporte para DNS personalizado no tipo **Gratuito**. 

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do Serviço de Aplicativo não for **Gratuito**, feche a página **Escolher o tipo de preço** e vá para [Comprar o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não estão livres (**compartilhado**, **básica**, **padrão**, ou **Premium**). 

Clique em **Selecionar**.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você receber a notificação a seguir, a operação de escala terá sido concluída.

![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="sign-in-to-azure"></a>Entrar no Azure
Abra o [portal do Azure](https://portal.azure.com/) e entre com sua conta do Azure.

### <a name="launch-buy-domains"></a>Inicializar Comprar domínio
Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configuraçõe**s e selecione **Domínios personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na página **Domínios personalizados**, clique em **Comprar domínios**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

### <a name="configure-the-domain-purchase"></a>Configurar a compra de domínio

Na página **Domínio de Serviço de Aplicativo**, na caixa **Pesquisar domínio**, digite o nome de domínio que você deseja comprar e digite `Enter`. Os domínios disponíveis sugeridos são mostrados logo abaixo da caixa de texto. Selecione um ou mais domínios que deseja comprar. 
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

Clique em **Informações de Contato** e preencha o formulário de informações de contato do domínio. Quando terminar, clique em **OK** para retornar à página de Domínio do Serviço de Aplicativo.
   
É importante preencher todos os campos obrigatórios com a máxima precisão possível. Dados incorretos para informações de contato resultarão em falhas em comprar domínios. 

Em seguida, selecione as opções desejadas para seu domínio. Consulte a tabela a seguir para obter explicações:

| Configuração | Valor sugerido | Descrição |
|-|-|-|
|Renovação automática | **Habilitar** | Renova seu Domínio do Serviço de Aplicativo automaticamente todo ano. Seu cartão de crédito é cobrado no mesmo preço de compra no momento da renovação. |
|Proteção de privacidade | Habilitar | Escolha "Proteção de privacidade", que está incluída no preço de compra _gratuitamente_ (exceto por domínios de nível superior cujo registro não dê suporte a proteção de privacidade, como _.co.in_, _.co.uk_ e assim por diante). |
| Atribuir nomes de host padrão | **www** e **@** | Se você quiser, selecione as associações de nome do host desejadas. Quando a operação de compra de domínio for concluída, seu aplicativo Web pode ser acessado nos nomes de host selecionados. Se o aplicativo Web estiver por trás do [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/), você não verá a opção de atribuir o domínio raiz (@), pois o Gerenciador de Tráfego não dá suporte a registros A. Você pode fazer alterações às atribuições de nome do host após a compra de domínio ser concluída. |

### <a name="accept-terms-and-purchase"></a>Aceitar os termos e comprar

Clique em **Termos Legais** para examinar os termos e os encargos, clique em **Comprar**.

> [!NOTE]
> Domínios do Serviço de Aplicativo usam o DNS do Azure para hospedar os domínios. Além da taxa de registro de domínio, encargos de uso do DNS do Azure se aplicam. Para obter informações, consulte [Preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta à página **Domínio de Serviço de Aplicativo**, clique em **OK**. Enquanto a operação estiver em andamento, você verá as seguintes notificações:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Se você tiver atribuído nomes de host padrão ao seu aplicativo Web, também verá uma notificação de êxito para cada nome do host selecionado. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Você também verá os nomes de host selecionados na página **Domínios personalizados** na seção **Nomes de host**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Para testar os nomes de host, navegue até os nomes de host listados no navegador. O exemplo na captura de tela anterior, tente navegar para _kontoso.net_ e _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Atribuir nomes de host ao aplicativo Web

Se você optar por não atribuir um ou mais nomes de host padrão ao seu aplicativo Web durante o processo de compra ou, se for necessário atribuir um nome do host não listado, você poderá atribuir um nome do host a qualquer momento.

Você também pode atribuir nomes de host no Domínio de Serviço de Aplicativo a qualquer outro aplicativo Web. As etapas dependem se o Domínio do Serviço de Aplicativo e o aplicativo Web pertencem à mesma assinatura.

- Assinatura diferente: mapear registros de DNS personalizados do Domínio do Serviço de Aplicativo para o aplicativo Web como um domínio adquirido externamente. Para obter informações sobre como adicionar nomes DNS personalizados a um Domínio do Serviço de Aplicativo, consulte [Gerenciar registros de DNS personalizados](#custom). Para mapear um domínio adquirido externo para um aplicativo Web, consulte [Mapear um nome DNS personalizado existente para Aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md). 
- Mesma assinatura: use as etapas a seguir.

### <a name="launch-add-hostname"></a>Inicializar adição de nome do host
Na página **Serviços de Aplicativos**, selecione o nome do aplicativo Web ao qual você deseja atribuir nomes de host, selecione **Configurações** e, em seguida, selecione **Domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que seu domínio adquirido esteja listado na seção **Domínios do Serviço de Aplicativo**, mas não o selecione. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os Domínios de Serviço de Aplicativo na mesma assinatura são mostrados na página **Domínios personalizados** do aplicativo Web. Se seu domínio estiver na assinatura do aplicativo Web, mas você não conseguir vê-lo na página **Domínios personalizados** do aplicativo Web, tente reabrir a página **Domínios personalizados** ou atualizar a página Web. Além disso, verifique o sino de notificação na parte superior do portal do Azure para progresso ou falhas de criação.
>
>

Selecione **Adicionar nome do host**.

### <a name="configure-hostname"></a>Configurar nome do host
Na caixa de diálogo **Adicionar nome do host**, digite o nome de domínio totalmente qualificado do Domínio do Serviço de Aplicativo ou qualquer subdomínio. Por exemplo:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Quando terminar, selecione **Validar**. O tipo de registro de nome do host é selecionado automaticamente para você.

Selecione **Adicionar nome do host**.

Quando a operação for concluída, você verá uma notificação de êxito para o nome do host atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Feche Adicionar nome do host
Na página **Adicionar nome do host**, atribua qualquer outro nome do host ao seu aplicativo Web, conforme desejado. Quando terminar, feche a página **Adicionar nome do host**.

Agora você deve ver os nomes do host recentemente atribuídos na página **Domínios personalizados** do seu aplicativo.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Navegue até o nome do host listado no navegador. No exemplo na captura de tela anterior, tente navegar para _abc.kontoso.net_.

<a name="custom" />

## <a name="manage-custom-dns-records"></a>Gerenciar registros DNS personalizados

No Azure, os registros DNS para um Domínio do Serviço de Aplicativo são gerenciados usando [DNS do Azure](https://azure.microsoft.com/services/dns/). Você pode adicionar, remover e atualizar registros DNS, assim como para um domínio adquirido externamente.

### <a name="open-app-service-domain"></a>Abrir o Domínio do Serviço de Aplicativo

No portal do Azure, no menu à esquerda, selecione **Mais Serviços** > **Domínios do Serviço de Aplicativo**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a gerenciar. 

### <a name="access-dns-zone"></a>Acessar zona DNS

No menu da esquerda do domínio, selecione **zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Essa ação abre a página [Zona DNS](../dns/dns-zones-records.md) do seu Domínio do Serviço de Aplicativo no DNS do Azure. Para obter informações sobre como editar os registros DNS, consulte [Como gerenciar zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de comprar o Domínio do Serviço de Aplicativo, você tem cinco dias para cancelar sua compra para obter um reembolso integral. Depois de cinco dias, você pode excluir o Domínio do Serviço de Aplicativo, mas não pode receber um reembolso.

### <a name="open-app-service-domain"></a>Abrir o Domínio do Serviço de Aplicativo

No portal do Azure, no menu à esquerda, selecione **Mais Serviços** > **Domínios do Serviço de Aplicativo**.

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

Selecione **OK** para confirmar a operação. Se você não quiser continuar, clique em qualquer lugar fora da caixa de diálogo de confirmação.

Depois que a operação estiver concluída, o domínio será liberado da sua assinatura e ficará disponível para qualquer pessoa comprar novamente. 
