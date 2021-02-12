---
title: Configurar um plano de modelo de solução
description: Saiba como configurar um plano de modelo de solução para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744611"
---
# <a name="configure-a-solution-template-plan"></a>Configurar um plano de modelo de solução

Este artigo se aplica somente aos planos de modelo de solução para uma oferta de aplicativo do Azure. Se você estiver configurando um plano de aplicativo gerenciado, vá para [configurar um plano de aplicativo gerenciado](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver seu plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um público privado usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura atribuída. Você pode adicionar no máximo 10 IDs de assinatura manualmente ou até 10.000 IDs de assinatura usando um. Arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

> [!NOTE]
> Se você publicar um plano privado, poderá alterar sua visibilidade para o público posteriormente. No entanto, depois de publicar um plano público, você não pode alterar sua visibilidade para particular.

Na guia **disponibilidade** , em **visibilidade do plano**, siga um destes procedimentos:

- Para tornar o plano público, selecione o botão de opção **pública** (também conhecido como um _botão de opção_).
- Para tornar o plano privado, selecione o botão de opção **particular** e, em seguida, adicione as IDs de assinatura do Azure manualmente ou com um arquivo CSV.

    > [!NOTE]
    > Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta antes de ela ser publicada como ativa nos marketplaces. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Adicionar manualmente IDs de assinatura do Azure para um plano privado

1. Em **visibilidade do plano**, selecione o botão de opção **particular** .
1. Na caixa **ID de assinatura do Azure** que aparece, insira a ID de assinatura do Azure do público que você deseja conceder acesso a este plano privado. É necessário um mínimo de uma ID de assinatura.
1. Adicional Insira uma descrição desse público na caixa **Descrição** .
1. Para adicionar outra ID de assinatura, selecione o link **Adicionar ID (máx. 10)** e repita as etapas 2 e 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Use um. Arquivo CSV para adicionar IDs de assinatura do Azure para um plano privado

1. Em **visibilidade do plano**, selecione o botão de opção **particular** .
1. Selecione o link **Exportar audiência (CSV)** .
1. Abra o. Arquivo CSV e adicione as IDs de assinatura do Azure às quais você deseja conceder acesso à oferta privada para a coluna **ID** .
1. Opcionalmente, insira uma descrição para cada público na coluna **Descrição** .
1. Adicione "SubscriptionId" na coluna **tipo** para cada linha com uma ID de assinatura.
1. Salve o. Arquivo CSV.
1. Na guia **disponibilidade** , em **visibilidade do plano**, selecione o link **importar audiência (CSV)** .
1. Na caixa de diálogo exibida, selecione **Sim**.
1. Selecione o. Arquivo CSV e, em seguida, selecione **abrir**. É exibida uma mensagem indicando que o. O arquivo CSV foi importado com êxito.

### <a name="hide-your-plan"></a>Ocultar seu plano

Se o seu modelo de solução pretende ser implantado apenas indiretamente quando referenciado por outro modelo de solução ou aplicativo gerenciado, marque a caixa de seleção em **ocultar plano** para publicar seu modelo de solução, mas oculte-o de clientes que pesquisam e naveguem diretamente.

Selecione **salvar rascunho** antes de continuar para a próxima seção: definir a configuração técnica.

## <a name="define-the-technical-configuration"></a>Definir a configuração técnica

Na guia **configuração técnica** , você carregará o pacote de implantação que permite que os clientes implantem seu plano e forneçam um número de versão para o pacote.

> [!NOTE]
> Essa guia não estará visível se você optar por reutilizar pacotes de outro plano na guia **configuração do plano** . Nesse caso, vá para [exibir seus planos](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Atribuir um número de versão para o pacote

Na caixa **versão** , forneça a versão atual da configuração técnica. Aumente essa versão sempre que publicar uma alteração nesta página. O número de versão deve estar no formato: inteiro. inteiro. inteiro. Por exemplo, `1.0.2`.

### <a name="upload-a-package-file"></a>Carregar um arquivo de pacote

Em **arquivo de pacote (. zip)**, arraste o arquivo de pacote para a caixa cinza ou selecione o link **procurar seu (s) arquivo (es)** .

> [!NOTE]
> Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço `https://upload.xboxlive.com` que é usado pelo Partner Center.

### <a name="previously-published-packages"></a>Pacotes publicados anteriormente

Depois de publicar sua oferta em tempo real, a subguia **pacotes publicados anteriormente** é exibida na página **configuração técnica** . Essa guia lista todas as versões publicadas anteriormente de sua configuração técnica.

## <a name="view-your-plans"></a>Exibir seus planos

- Selecione **salvar rascunho** e, no canto superior esquerdo da página, selecione **visão geral do plano** para retornar à página **visão geral do plano** .

Depois de criar um ou mais planos, você verá o nome do plano, a ID do plano, o tipo de plano, a disponibilidade (pública ou privada), o status de publicação atual e as ações disponíveis na guia **visão geral do plano** .

As ações disponíveis na coluna **ação** da guia **visão geral do plano** variam de acordo com o status do plano e podem incluir o seguinte:

- Se o status do plano for **rascunho**, o link na coluna **ação** dirá **excluir rascunho**.
- Se o status do plano for **dinâmico**, o link na coluna **ação** será parar o **plano de venda** ou **sincronizar público privado**. O link **sincronizar audiência privada** publicará apenas as alterações em seus públicos privados, sem publicar outras atualizações que você tenha feito na oferta.
- Para criar outro plano para esta oferta, na parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**. Em seguida, repita as etapas em [como criar planos para sua oferta de aplicativo do Azure](create-new-azure-apps-offer-plans.md). Caso contrário, se você terminar de criar planos, vá para a próxima seção: próximas etapas.

## <a name="next-steps"></a>Próximas etapas

- [Como testar e publicar sua oferta de aplicativo Azure](create-new-azure-apps-offer-test-publish.md).
- Saiba [como vender sua oferta de aplicativo Azure](create-new-azure-apps-offer-marketing.md) por meio da venda conjunta com a Microsoft e revenda por meio de programas de CSPs.
