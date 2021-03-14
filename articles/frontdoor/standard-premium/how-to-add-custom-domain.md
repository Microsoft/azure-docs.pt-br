---
title: Como adicionar um domínio personalizado à sua configuração de SKU Standard/Premium de porta de front-end do Azure
description: Neste tutorial, você aprenderá a integrar um domínio personalizado ao SKU Standard/Premium da porta de front-end do Azure.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098403"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Criar um domínio personalizado no SKU Standard/Premium da porta do Azure (versão prévia) usando o portal do Azure

> [!Note]
> Esta documentação é para o Azure front door Standard/Premium (versão prévia). Procurando informações sobre a porta frontal do Azure? Veja [aqui](../front-door-overview.md).

Quando você usa o Azure front door Standard/Premium para entrega de aplicativos, um domínio personalizado é necessário se você quiser que seu próprio nome de domínio fique visível nas solicitações do usuário final. Ter um nome de domínio visível pode ser conveniente para os clientes e útil para fins de identidade visual.

Depois de criar um perfil padrão/Premium da porta do Azure, o host de front-end padrão terá um subdomínio de azurefd.net. Esse subdomínio é incluído na URL quando o Standard/Premium da porta do Azure entrega o conteúdo do seu back-end por padrão. Por exemplo, `https://contoso-frontend.azurefd.net/activeusers.htm`. Para sua conveniência, o Azure Front Door oferece a opção de associar um domínio personalizado ao host padrão. Com essa opção, você entrega seu conteúdo com um domínio personalizado em sua URL em vez de um nome de domínio de propriedade padrão/Premium da porta do Azure. Por exemplo, https://www.contoso.com/photo.png.

> [!IMPORTANT]
> O Azure front door Standard/Premium (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para saber mais, confira os [**Termos de uso complementares das versões prévias do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos
* Antes de poder concluir as etapas neste tutorial, é necessário criar primeiro um Front Door. Para obter mais informações, consulte [início rápido: criar um padrão de porta frontal/Premium](create-front-door-portal.md).

* Se você ainda não tiver um domínio personalizado, deverá primeiro comprar um com um provedor de domínio. Por exemplo, confira [Comprar um nome de domínio personalizado](../../app-service/manage-custom-dns-buy-domain.md).

* Se você estiver usando o Azure para hospedar seus [domínios DNS](../../dns/dns-overview.md), você deve delegar o DNS (sistema de nomes de domínio) do provedor de domínio para um DNS do Azure. Confira [Delegar um domínio ao DNS do Azure](../../dns/dns-delegate-domain-azure-dns.md)para saber mais. Caso contrário, se você estiver usando um provedor de domínio para manipular seu domínio DNS, você deverá validar manualmente o domínio inserindo registros TXT solicitados do DNS.

## <a name="add-a-new-custom-domain"></a>Adicionar um novo domínio personalizado

Uma seção domínio personalizado é gerenciada por domínios no Portal. Um domínio personalizado pode ser criado e validado antes da associação a um ponto de extremidade. Um domínio personalizado e seus subdomínios podem ser associados a apenas um único ponto de extremidade por vez. No entanto, você pode usar diferentes subdomínios do mesmo domínio personalizado para diferentes portas de frente. Você também pode mapear domínios personalizados com subdomínios diferentes para o mesmo ponto de extremidade de porta frontal.

1. Em configurações para o perfil de porta de recepção do Azure, selecione *domínios*  e, em seguida, o botão **Adicionar um domínio** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Captura de tela do botão Adicionar domínio na página inicial do domínio.":::

1. A página **Adicionar um domínio** será exibida onde você pode inserir informações sobre o domínio personalizado. Você pode escolher o DNS gerenciado pelo Azure, que é recomendado ou pode optar por usar seu próprio provedor DNS. Se você escolher DNS gerenciado pelo Azure, selecione uma zona DNS existente e, em seguida, selecione um subdomínio personalizado ou crie um novo. Se você estiver usando outro provedor DNS, insira manualmente o nome de domínio personalizado. Selecione **Adicionar** para adicionar seu domínio personalizado.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Captura de tela da página Adicionar um domínio.":::

    Um novo domínio personalizado é criado com um estado de validação de **envio**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Captura de tela de envio do estado de validação de domínio.":::

    Aguarde até que o estado de validação seja alterado para **pendente**. Esta operação pode levar alguns minutos.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Captura de tela do estado de validação de domínio pendente.":::

1. Selecione o estado de validação **pendente** . Uma nova página será exibida com as informações de registro TXT do DNS necessárias para validar o domínio personalizado. O registro TXT está na forma de `_dnsauth.<your_subdomain>` . Se você estiver usando a zona baseada em DNS do Azure, selecione o botão **Adicionar** e um novo registro txt com o valor de registro exibido será criado na zona DNS do Azure. Se você estiver usando outro provedor DNS, crie manualmente um novo registro TXT de nome `dnsauth.<your_subdomain>` com o valor do registro, conforme mostrado na página.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Captura de tela da página validar domínio personalizado.":::

1. Selecione o status de atualização. Depois que o domínio for validado usando o registro TXT do DNS, o status de validação será alterado para **verificado**. Esta operação pode levar alguns minutos para ser validada.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Captura de tela do domínio personalizado verificado.":::

1. Feche a página para retornar à página de aterrissagem da lista de domínios personalizados. O estado de provisionamento do domínio personalizado deve mudar para **provisionado** e o estado de validação deve mudar para **aprovado**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Captura de tela de status provisionado e aprovado.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associar o domínio personalizado ao ponto de extremidade de porta frontal

Depois de validar seu domínio personalizado, você poderá adicioná-lo ao ponto de extremidade padrão/Premium da porta de front-end do Azure.

1. Depois que o domínio personalizado for validado, você poderá associá-lo a um ponto de extremidade e rota Premium e padrão do Azure front door. Selecione o link **Associação de ponto de extremidade** para abrir a página **associar ponto de extremidade e rotas** . Selecione um ponto de extremidade e as rotas às quais você deseja associar. Em seguida, selecione **associar**. Feche a página quando a operação de associação for concluída.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Captura de tela da página associar ponto de extremidade e rotas.":::

    O status de associação do ponto de extremidade deve ser alterado para refletir o ponto de extremidade ao qual o domínio personalizado está associado no momento. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Captura de tela do link de associação do ponto de extremidade.":::

1. Selecione o link do estado DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Captura de tela do link de estado DNS.":::

1. A página **Adicionar ou atualizar o registro CNAME** será exibida e exibirá as informações de registro CNAME que devem ser fornecidas antes que o tráfego possa começar a fluir. Se você estiver usando zonas hospedadas do DNS do Azure, os registros CNAME poderão ser criados selecionando o botão **Adicionar** na página. Se você estiver usando outro provedor DNS, deverá inserir manualmente o nome e o valor do registro CNAME, conforme mostrado na página.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Captura de tela de adicionar ou atualizar registro CNAME.":::

1. Depois que o registro CNAME for criado e o domínio personalizado estiver associado ao ponto de extremidade da porta do Azure for concluído, o fluxo de tráfego começará a fluir.

    > [!NOTE]
    > Se o HTTPS estiver habilitado, o provisionamento e a propagação de certificados poderão levar alguns minutos porque a propagação está sendo feita em todos os locais de borda. 

## <a name="verify-the-custom-domain"></a>Verificar o domínio personalizado

Depois de validar e associar o domínio personalizado, verifique se o domínio personalizado está referenciado corretamente em seu ponto de extremidade.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Captura de tela de domínio personalizado validado e associado.":::

Em seguida, por fim, valide se o conteúdo do aplicativo está sendo servido usando um navegador.

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar o HTTPS para o domínio personalizado, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar HTTPS para um domínio personalizado]()
