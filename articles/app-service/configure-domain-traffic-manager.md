---
title: Configurar nomes DNS com o Gerenciador de tráfego
description: Saiba como configurar um domínio personalizado para um aplicativo de serviço de Azure App que se integre ao Gerenciador de tráfego para balanceamento de carga.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 2910ea3f896ba3920126737965ca9c9dbabcfeb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709097"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Configurar um nome de domínio personalizado no serviço de Azure App com a integração do Gerenciador de tráfego

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Quando você usa o [Gerenciador de tráfego do Azure](../traffic-manager/index.yml) para balancear a carga do tráfego para [Azure app serviço](overview.md), o aplicativo do serviço de aplicativo pode ser acessado usando **\<traffic-manager-endpoint> . trafficmanager.net**. Você pode atribuir um nome de domínio personalizado, como www \. contoso.com, ao seu aplicativo do serviço de aplicativo para fornecer um nome de domínio mais reconhecível para seus usuários.

Este artigo mostra como configurar um nome de domínio personalizado com um aplicativo do serviço de aplicativo que é integrado com o [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Somente os registros [CNAME](https://en.wikipedia.org/wiki/CNAME_record) têm suporte quando você configura um nome de domínio usando o ponto de extremidade do Gerenciador de tráfego. Como não há suporte para registros, um mapeamento de domínio raiz, como contoso.com, também não tem suporte.
> 

## <a name="prepare-the-app"></a>Preparar o aplicativo

Para mapear um nome DNS personalizado para um aplicativo integrado com o Gerenciador de tráfego do Azure, o [plano do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo Web deve estar na camada **Standard** ou superior. Nesta etapa, você verifica se o aplicativo do Serviço de Aplicativo está no tipo de preço com suporte.

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

Na [portal do Azure](https://portal.azure.com), procure e selecione serviços de **aplicativos**.

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

No painel de navegação à esquerda da página do aplicativo, selecione **escalar verticalmente (plano do serviço de aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

A camada atual do aplicativo é realçada por uma borda azul. Verifique se o aplicativo está na camada **Standard** ou acima (qualquer camada na categoria de **produção** ou **isolada** ). Em caso afirmativo, feche a página **escalar verticalmente** e pule para [criar o mapeamento CNAME](#create-the-cname-mapping).

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Escalar verticalmente o plano do Serviço de Aplicativo

Se você precisar escalar verticalmente seu aplicativo, selecione qualquer tipo de preço na categoria **produção** . Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

## <a name="create-traffic-manager-endpoint"></a>Criar ponto de extremidade do Gerenciador de tráfego

Seguindo as etapas em [Adicionar ou excluir pontos](../traffic-manager/traffic-manager-manage-endpoints.md)de extremidade, adicione seu aplicativo do serviço de aplicativo como um ponto de extremidades em seu perfil do Gerenciador de tráfego.

Depois que o aplicativo do serviço de aplicativo estiver em um tipo de preço com suporte, ele aparecerá na lista de destinos do serviço de aplicativo disponíveis quando você adicionar o ponto de extremidade. Se seu aplicativo não estiver listado, [Verifique o tipo de preço do seu aplicativo](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Criar o mapeamento de CNAME
> [!NOTE]
> Para configurar um [domínio do serviço de aplicativo que você comprou](manage-custom-dns-buy-domain.md), pule esta seção e vá para [habilitar domínio personalizado](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Embora as especificidades de cada provedor de domínio variem, você mapeia *de* um [nome de domínio personalizado não raiz](#what-about-root-domains) (como **www.contoso.com**) *para* o nome de domínio do Traffic Manager (**contoso.trafficmanager.net**) que é integrado ao seu aplicativo. 

> [!NOTE]
> Se um registro já está em uso e você precisa associar preventivamente seus aplicativos a ele, pode criar um registro CNAME adicional. Por exemplo, para ligar preemptivamente **o \. contoso.com da www** ao seu aplicativo, crie um registro CNAME de **awverify. www** para **contoso.trafficmanager.net**. Em seguida, você pode adicionar "www \. contoso.com" ao seu aplicativo sem a necessidade de alterar o registro CNAME "www". Para obter mais informações, consulte [migrar um nome DNS ativo para Azure app serviço](manage-custom-dns-migrate-domain.md).

Depois de terminar a adição ou a modificação de registros DNS no provedor de domínios, salve as alterações.

### <a name="what-about-root-domains"></a>E quanto aos domínios raiz?

Como o Gerenciador de tráfego só dá suporte ao mapeamento de domínio personalizado com registros CNAME, e como os padrões de DNS não dão suporte a registros CNAME para mapear domínios raiz (por exemplo, **contoso.com**), o Gerenciador de tráfego não dá suporte ao mapeamento para domínios raiz. Para contornar esse problema, use um redirecionamento de URL do no nível do aplicativo. Em ASP.NET Core, por exemplo, você pode usar a [regravação de URL](/aspnet/core/fundamentals/url-rewriting). Em seguida, use o Gerenciador de tráfego para balancear a carga do subdomínio (**www.contoso.com**). Outra abordagem é que você pode [criar um registro de alias para o Apex do nome de domínio para fazer referência a um perfil do Gerenciador de tráfego do Azure](../dns/tutorial-alias-tm.md). Um exemplo é contoso.com. Em vez de usar um serviço de redirecionamento, você pode configurar o DNS do Azure para fazer referência a um perfil do Gerenciador de tráfego diretamente da zona. 

Para cenários de alta disponibilidade, você pode implementar uma configuração de DNS de balanceamento de carga sem o Gerenciador de tráfego criando vários *registros a* que apontam do domínio raiz para o endereço IP de cada cópia de aplicativo. Em seguida, [mapeie o mesmo domínio raiz para todas as cópias de aplicativo](app-service-web-tutorial-custom-domain.md#map-an-a-record). Como o mesmo nome de domínio não pode ser mapeado para dois aplicativos diferentes na mesma região, essa configuração só funciona quando as cópias do aplicativo estão em regiões diferentes.

## <a name="enable-custom-domain"></a>Habilitar domínio personalizado
Depois que os registros de seu nome de domínio forem propagados, use o navegador para verificar se o nome de domínio personalizado é resolvido para seu aplicativo do serviço de aplicativo.

> [!NOTE]
> Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. Você pode usar um serviço como <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.
> 
> 

1. Depois que a resolução de domínio for realizada com sucesso, para voltar à página do aplicativo no [portal do Azure](https://portal.azure.com)
2. No painel de navegação esquerdo, selecione **domínios personalizados**  >  **adicionar nome do host**.
4. Digite o nome de domínio personalizado que você mapeou anteriormente e selecione **validar**.
5. Certifique-se de que **Tipo de registro do nome do host** esteja definido como **CNAME (www\.exemplo.com ou qualquer subdomínio)** .

6. Como o aplicativo do serviço de aplicativo agora está integrado a um ponto de extremidade do Traffic Manager, você deve ver o nome de domínio do Traffic Manager em **configuração de CNAME**. Selecione-o e clique em **Adicionar domínio personalizado**.

    ![Adicionar nome DNS para o aplicativo](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Proteger um nome DNS personalizado com uma associação SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)