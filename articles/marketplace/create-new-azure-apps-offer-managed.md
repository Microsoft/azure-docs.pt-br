---
title: Configurar um plano de aplicativo gerenciado
description: Saiba como configurar um plano de aplicativo gerenciado para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744628"
---
# <a name="configure-a-managed-application-plan"></a>Configurar um plano de aplicativo gerenciado

Este artigo aplica-se somente a planos de aplicativo gerenciados para uma oferta de aplicativo do Azure. Se você estiver configurando um plano de modelo de solução, vá para [configurar um plano de modelo de solução](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definir mercados, preços e disponibilidade

Cada plano precisa estar disponível em pelo menos um mercado. Na guia **preços e disponibilidade** , você pode configurar os mercados em que esse plano estará disponível, o preço e se deseja tornar o plano visível para todos ou apenas para clientes específicos (também chamados de plano privado).

1. Em **mercados**, selecione o link **Editar mercados** .
1. Na caixa de diálogo que aparece, selecione os locais de mercado onde você deseja disponibilizar seu plano. Você deve selecionar no mínimo um e no máximo 141 mercados.

    > [!NOTE]
    > Essa caixa de diálogo inclui uma caixa de pesquisa e uma opção para filtrar somente os países "impostos remetidos", no qual a Microsoft remete as vendas e o imposto sobre o uso em seu nome.

1. Selecione **salvar** para fechar a caixa de diálogo.

## <a name="define-pricing"></a>Definir preços

Na caixa **preço** , forneça o preço por mês para este plano. Esse preço é além de qualquer infraestrutura do Azure ou custos baseados em uso incorridos pelos recursos implantados por essa solução.

Além do preço por mês, você também pode definir preços para consumo de unidades não padrão usando [cobrança limitada](partner-center-portal/azure-app-metered-billing.md). Você pode definir o preço por mês como zero e cobrar exclusivamente usando a cobrança limitada, se desejar.

Os preços são definidos em USD (USD = Estados Unidos dólar) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar, exportando a planilha de preços e examinando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços.

### <a name="add-a-custom-meter-dimension-optional"></a>Adicionar uma dimensão de medidor personalizado (opcional)

1. Em **dimensões do serviço de medição do Marketplace**, selecione o link **Adicionar uma dimensão de medição personalizada (máx. 18)** .
1. Na caixa **ID** , insira a referência de identificador imutável ao emitir eventos de uso.
1. Na caixa **nome de exibição** , insira o nome de exibição associado à dimensão. Por exemplo, "mensagens de texto enviadas".
1. Na caixa **unidade de medida** , insira a descrição da unidade de cobrança. Por exemplo, "mensagem por texto" ou "por email 100".
1. No **preço por unidade na caixa USD** , insira o preço de uma unidade da dimensão.
1. Na caixa de **dados quantidade mensal incluída na base** , insira a quantidade (como um número inteiro) da dimensão que é incluída todos os meses para os clientes que pagam a taxa mensal recorrente. Para definir uma quantidade ilimitada, marque a caixa de seleção em vez disso.
1. Para adicionar outra dimensão de medidor personalizado, repita as etapas de 1 a 7.

### <a name="set-custom-prices-optional"></a>Definir preços personalizados (opcional)

Os preços definidos em US$ (US$ = dólares americanos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando salvos. Valide esses preços antes de publicar, exportando a planilha de preços e examinando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços.

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado.

> [!NOTE]
> Depois que um preço para um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

Para definir preços personalizados em um mercado individual, exporte, modifique e importe a planilha de preços. Você é responsável por validar esse preço e possuir essas configurações. Para obter informações detalhadas, consulte [preços personalizados](plans-pricing.md#custom-prices).

1. É necessário primeiro salvar as alterações de preços para habilitar a exportação de dados sobre preços. Próximo à parte inferior da guia **preços e disponibilidade** , selecione **salvar rascunho**.
1. Em **preços**, selecione o link **exportar dados de preços** .
1. Abra o arquivo exportedPrice.xlsx no Microsoft Excel.
1. Na planilha, faça as atualizações desejadas para suas informações de preço e salve o arquivo.

   Talvez seja necessário habilitar a edição no Excel antes que você possa atualizar o arquivo.

1. Na guia **preços e disponibilidade** , em **preços**, selecione o link **importar dados de preços** .
1. Na caixa de diálogo exibida, clique em **Sim**.
1. Selecione o arquivo de exportedPrice.xlsx que você atualizou e clique em **abrir**.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver seu plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um público privado usando IDs de assinatura do Azure com a opção de incluir uma descrição de cada ID de assinatura atribuída. Você pode adicionar no máximo 10 IDs de assinatura manualmente ou até 10.000 IDs de assinatura usando um. Arquivo CSV. As IDs de assinatura do Azure são representadas como GUIDs e todas as letras precisam estar em minúsculas.

> [!NOTE]
> Se você publicar um plano privado, poderá alterar sua visibilidade para o público posteriormente. No entanto, depois de publicar um plano público, você não pode alterar sua visibilidade para particular.

Em **visibilidade do plano**, siga um destes procedimentos:

- Para tornar o plano público, selecione o botão de opção **pública** (também conhecido como um _botão de opção_).
- Para tornar o plano privado, selecione o botão de opção **particular** e, em seguida, adicione as IDs de assinatura do Azure manualmente ou com um arquivo CSV.

> [!NOTE]
> Um público-alvo privado ou restrito é diferente do público-alvo de versão prévia que você definiu no painel **Versão prévia**. Um público-alvo de versão prévia pode acessar sua oferta antes de ela ser publicada como ativa nos marketplaces. Embora a escolha de público-alvo privado se aplique somente a um plano específico, o público-alvo da versão prévia pode ver todos os planos públicos e privados para fins de validação.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Adicionar manualmente IDs de assinatura do Azure para um plano privado

1. Em **visibilidade do plano**, selecione o botão de opção **particular** .
1. Na caixa **ID de assinatura do Azure** que aparece, insira a ID de assinatura do Azure do público que você deseja conceder acesso a este plano privado. É necessário um mínimo de uma ID de assinatura.
1. Adicional Insira uma descrição desse público na caixa **Descrição** .
1. Para adicionar outra ID de assinatura, selecione o link **Adicionar ID (máx. 10)** e repita as etapas 2 e 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Use um. Arquivo CSV para adicionar IDs de assinatura do Azure para um plano privado

1. Em **visibilidade do plano**, selecione o botão de opção **particular** .
1. Selecione o link **Exportar audiência (CSV)** .
1. Abra o. Arquivo CSV e adicione as IDs de assinatura do Azure às quais você deseja conceder acesso à oferta privada para a coluna **ID** .
1. Opcionalmente, insira uma descrição para cada público na coluna **Descrição** .
1. Adicione "SubscriptionId" na coluna **tipo** para cada linha com uma ID de assinatura.
1. Salve o. Arquivo CSV.
1. Na guia **disponibilidade** , em **visibilidade do plano**, selecione o link **importar audiência (CSV)** .
1. Na caixa de diálogo exibida, selecione **Sim**.
1. Selecione o. Arquivo CSV e, em seguida, selecione **abrir**. É exibida uma mensagem indicando que o. O arquivo CSV foi importado com êxito.

## <a name="define-the-technical-configuration"></a>Definir a configuração técnica

Na guia **configuração técnica** , você carregará o pacote de implantação que permite que os clientes implantem seu plano e forneçam um número de versão para o pacote. Você também fornecerá outras informações técnicas.

> [!NOTE]
> Essa guia não estará visível se você optar por reutilizar pacotes de outro plano na guia **configuração do plano** . Nesse caso, vá para [exibir seus planos](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Atribuir um número de versão para o pacote

Na caixa **versão** , forneça a versão atual da configuração técnica. Aumente essa versão sempre que publicar uma alteração nesta página. O número de versão deve estar no formato: inteiro. inteiro. inteiro. Por exemplo, `1.0.2`.

### <a name="upload-a-package-file"></a>Carregar um arquivo de pacote

Em **arquivo de pacote (. zip)**, arraste o arquivo de pacote para a caixa cinza ou selecione o link **procurar seu (s) arquivo (es)** .

> [!NOTE]
> Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço `https://upload.xboxlive.com` que é usado pelo Partner Center.

#### <a name="previously-published-packages"></a>Pacotes publicados anteriormente

A subguia **Pacotes publicados anteriormente** permite exibir todas as versões publicadas de sua configuração técnica.

### <a name="enable-just-in-time-jit-access-optional"></a>Habilitar o acesso JIT (just-in-time) (opcional)

Para habilitar o acesso JIT para este plano, marque a caixa de seleção **habilitar acesso JIT (just-in-time)** . Para exigir que consumidores do seu aplicativo gerenciado permitam à sua conta acesso permanente, deixe essa opção desmarcada. Para saber mais sobre essa opção, consulte [acesso just in time (JIT)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Selecionar um modo de implantação

Selecione o modo de implantação **completo** ou **incremental** .

- No modo **completo** , uma reimplantação do aplicativo pelo cliente resultará na remoção de recursos no grupo de recursos gerenciados se os recursos não estiverem definidos no [mainTemplate.jsem](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- No modo **incremental** , uma reimplantação do aplicativo deixa os recursos existentes inalterados.

Para saber mais sobre modos de implantação, confira [Modos de implantação do Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Forneça uma URL de ponto de extremidade de notificação

Na caixa **URL do ponto de extremidade de notificação** , forneça um ponto de extremidade de webhook HTTPS para receber notificações sobre todas as operações CRUD em instâncias de aplicativo gerenciado desta versão do plano.

### <a name="customize-allowed-customer-actions-optional"></a>Personalizar ações de cliente permitidas (opcional)

1. Para especificar quais ações os clientes podem executar nos recursos gerenciados, além das `*/read` ações "" que estão disponíveis por padrão, selecione a caixa **Personalizar ações do cliente permitidas** .
1. Nas caixas que aparecem, forneça as ações de controle adicionais e as ações de dados permitidas que você deseja permitir que o cliente execute, separados por ponto-e-vírgula. Por exemplo, para permitir que os consumidores reiniciem máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` à caixa **ações de controle permitidas** .

### <a name="choose-who-can-manage-the-application"></a>Escolha quem pode gerenciar o aplicativo

Indique quem deve ter acesso de gerenciamento a esse aplicativo gerenciado em cada região do Azure selecionada: nuvem _global do_ Azure e _Azure governamental_. Você usará as identidades do Azure AD para identificar os usuários, grupos ou aplicativos aos quais você deseja conceder permissão para o grupo de recursos gerenciado. Para obter mais informações, consulte [planejar um aplicativo gerenciado do Azure para uma oferta de aplicativo Azure](plan-azure-application-offer.md).

Conclua as etapas a seguir para a nuvem global do Azure e do Azure governamental, conforme aplicável.

1. Na caixa **ID de locatário Azure Active Directory** , insira a ID de locatário do Azure AD (também conhecida como ID de diretório) que contém as identidades dos usuários, grupos ou aplicativos aos quais você deseja conceder permissões.
1. Na caixa **ID da entidade de segurança** , forneça a ID de objeto do Azure AD do usuário, grupo ou aplicativo para o qual você deseja receber permissão para o grupo de recursos gerenciado. Identifique o usuário por sua ID de entidade de segurança, que pode ser encontrada na [folha Azure Active Directory usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) no portal do Azure.
1. Na lista **definição de função** , selecione uma função interna do Azure AD. A função selecionada descreve as permissões que a entidade de segurança terá nos recursos na assinatura do cliente.
1. Para adicionar outra autorização, selecione o link **Adicionar autorização (máx. 100)** e repita as etapas 1 a 3.

### <a name="policy-settings-optional"></a>Configurações de política (opcional)

Você pode configurar um máximo de cinco políticas e apenas uma instância de cada opção de políticas. Algumas políticas não requerem parâmetros adicionais.

1. Em **configurações de política**, selecione o link **+ Adicionar política (máx. 5)** .
1. Na caixa **nome** , insira o nome da atribuição de política (limitado a 50 caracteres).
1. Na caixa de listagem **políticas** , selecione a política do Azure que será aplicada aos recursos criados pelo aplicativo gerenciado na assinatura do cliente.
1. Na caixa **parâmetros de política** , forneça o parâmetro no qual as políticas de auditoria e configurações de diagnóstico devem ser aplicadas.
1. Na caixa de listagem **SKU da política** , selecione o tipo de SKU política.

    > [!NOTE]
    > A SKU da _política padrão_ é necessária para políticas de auditoria.

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