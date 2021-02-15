---
title: Planejar uma oferta de Aplicativo Azure para o Marketplace comercial
description: Saiba como planejar uma nova oferta de aplicativo do Azure para listagem ou venda no Azure Marketplace ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: f98f128e56810e3dd710ce454c8dc34b333690d1
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879285"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Planejar uma oferta de Aplicativo Azure para o Marketplace comercial

Este artigo explica as diferentes opções e requisitos para publicar uma oferta de Aplicativo Azure no Microsoft Commercial Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Projetar, criar e testar ofertas do aplicativo Azure exige conhecimento técnico da plataforma do Azure e das tecnologias usadas para criar a oferta. A equipe de engenharia deverá ter conhecimento das seguintes tecnologias Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/).
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/).
- Conhecimento prático de [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rede do Azure](https://azure.microsoft.com/services/?filter=networking#networking).
- Conhecimento de prático do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático de [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Documentação técnica e recursos

Examine os recursos a seguir ao planejar sua oferta de aplicativo do Azure para o Marketplace comercial.

- [Compreender os modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- Inícios Rápidos:
    - [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/)
    - [Guia de práticas recomendadas de modelos do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Publicar definição do aplicativo](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Implantar aplicativo do catálogo de serviços](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Tutoriais:
    - [Criar arquivos de definição](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Exemplos:
    - [CLI do Azure](../azure-resource-manager/managed-applications/cli-samples.md)
    - [PowerShell do Azure](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Soluções de aplicativo gerenciado](../azure-resource-manager/managed-applications/sample-projects.md)

O vídeo [Criação de modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) fornece uma introdução abrangente ao tipo de oferta de aplicativo do Azure:

- Quais tipos de oferta estão disponíveis
- Quais ativos técnicos são necessários
- Como criar um modelo de Azure Resource Manager
- Desenvolvendo e testando a interface do usuário do aplicativo
- Como publicar a oferta de aplicativo
- O processo de revisão do aplicativo

### <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos dos seguintes ambientes de script para ajudar a gerenciar seu aplicativo Azure:

- [PowerShell do Azure](/powershell/azure/)
- [CLI do Azure](/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) (VS Code) com as extensões a seguir:
    - Extensão: [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Analise as ferramentas disponíveis na página [Ferramentas para Desenvolvedores do Azure](https://azure.microsoft.com/tools/). Se você estiver usando o Visual Studio, consulte a [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Opções de listagem

Depois que sua oferta for publicada, as opções de listagem para sua oferta serão exibidas como um botão no canto superior esquerdo da página de listagem da sua oferta. Por exemplo, a captura de tela a seguir mostra uma página de listagem de oferta no Azure Marketplace com o botão _obter agora_ . Se você optou por oferecer um test drive, o botão _Test Drive_ também seria mostrado.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Ilustra uma página de listagem no Azure Marketplace.":::

## <a name="test-drive"></a>Test drive

Você pode optar por habilitar um test drive para sua oferta de Aplicativo Azure que permite aos clientes experimentar sua oferta antes de adquiri-la. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](what-is-test-drive.md). Para obter informações sobre como configurar diferentes tipos de unidades de teste, consulte [testar a configuração técnica](test-drive-technical-configuration.md).

Você também pode ler sobre [Test Drive práticas recomendadas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) e baixar a [visão geral do test drives](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (verifique se o bloqueador de pop-ups está desativado).

> [!NOTE]
> As informações que o usuário deve observar mesmo que skimmingBecause todos os aplicativos do Azure sejam implementados usando um modelo de Azure Resource Manager, o único tipo de test drive disponível para uma [aplicativo Azure é uma Test Drive baseada em Azure Resource Manager](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Clientes potenciais

Você deve conectar sua oferta ao seu sistema de gerenciamento de relacionamento com o cliente (CRM) para coletar informações do cliente. Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a loja online onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. O Marketplace comercial dá suporte a uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela do Azure ou configurar um ponto de extremidade HTTPS usando a automatização de energia.

Você pode adicionar ou modificar uma conexão do CRM a qualquer momento durante ou após a criação da oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Categorias e subcategorias

Você pode escolher pelo menos uma e até duas categorias para agrupar sua oferta nas áreas de pesquisa do Marketplace comercial apropriadas. Você pode escolher até duas subcategorias para cada categoria primária e secundária. Para obter uma lista completa de categorias e subcategorias, consulte [práticas recomendadas de listagem de ofertas](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão que você pode usar para suas ofertas no mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez e você não precisa criar termos e condições personalizados.

Se você optar por usar o contrato padrão, terá a opção de adicionar os termos de emenda universal e até 10 emendas personalizadas ao contrato Standard. Você também pode usar seus próprios termos e condições em vez do contrato padrão. Você gerenciará esses detalhes na página **Propriedades** . Para obter informações detalhadas, consulte [contrato padrão do Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão para o mercado comercial, você não pode usar seus próprios termos e condições personalizados. Esse é um cenário com duas possibilidades mutuamente exclusivas. Você pode oferecer sua solução sob o contrato Standard ou seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="offer-listing-details"></a>Detalhes da listagem de ofertas

Ao criar uma nova oferta de Aplicativo Azure no Partner Center, você inserirá texto, imagens, vídeos opcionais e outros detalhes na página de listagem da oferta. Essas são as informações que os clientes verão quando descobrirem sua listagem de ofertas no Azure Marketplace, conforme mostrado no exemplo a seguir.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo
2. Categorias
3. Endereço de suporte (link)
4. Termos de uso
5. Endereço de política de privacidade (link)
6. Nome da oferta
7. Resumo
8. Descrição
9. Capturas de tela/vídeos

A captura de tela a seguir mostra como as informações de oferta aparecem no portal do Azure:

[![Ilustra como essa oferta aparece na portal do Azure.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Título
2. Descrição
3. Links úteis
4. Capturas de tela

> [!NOTE]
> O conteúdo de listagem da oferta não precisará estar em inglês se a descrição da oferta começar com a frase "este aplicativo está disponível somente em [idioma diferente do inglês]".

Para ajudar a criar sua oferta com mais facilidade, prepare alguns desses itens antecipadamente. Os itens a seguir são necessários, salvo indicação em contrário.

- **Nome**: esse nome será exibido como o título da sua listagem de ofertas no Marketplace comercial. Uma marca comercial pode ser atribuída ao nome. Ele não pode conter emojis (a menos que sejam os símbolos de marca registrada e direitos autorais) e precisam ser limitados a 50 caracteres.
- **Resumo dos resultados da pesquisa**: Descreva a finalidade ou a função de sua oferta como uma única frase, em texto sem formatação sem quebras de linha, em 100 caracteres ou menos. Esse resumo é usado nos resultados da pesquisa de listagem (s) do Marketplace comercial.
- **Descrição breve**: forneça até 256 caracteres de texto sem formatação. Este resumo será exibido na página de detalhes da sua oferta.
- **Descrição**: essa descrição será exibida na visão geral de listagem (s) do Azure Marketplace. Considere incluir uma proposta de valor, os principais benefícios, a base de usuários pretendida, qualquer associação de categoria ou do setor, oportunidades de compra no aplicativo, necessidade do cliente ou dificuldade de que a oferta atenda, quaisquer divulgações necessárias e um link para saber mais.

    Essa caixa de texto tem controles de editor de texto avançado que você pode usar para tornar sua descrição mais atraente. Você também pode usar marcas HTML para formatar sua descrição. Você pode inserir até 3.000 caracteres de texto nesta caixa, que inclui marcação e espaços HTML. Para obter dicas adicionais, consulte [escrever uma ótima descrição do aplicativo](/windows/uwp/publish/write-a-great-app-description) e [marcas HTML com suporte nas descrições da oferta do Marketplace comercial](supported-html-tags.md).

- **Pesquisar palavras-chave** (opcional): forneça até três palavras-chave de pesquisa que os clientes podem usar para localizar sua oferta na loja online. Para obter melhores resultados, use essas palavras-chave em sua descrição também. Você não precisa incluir o **nome** e a **Descrição** da oferta. Esse texto é incluído automaticamente na pesquisa.
- **Link de política de privacidade**: a URL para a política de privacidade da sua empresa. Você deve fornecer uma política de privacidade válida e é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade.
- **Links úteis** (opcional): você pode fornecer links para vários recursos para os usuários de sua oferta. Por exemplo, fóruns, perguntas frequentes e notas de versão.
- **Informações de contato**: você deve designar os seguintes contatos de sua organização:
  - **Contato de suporte**: forneça o nome, o telefone e o email para que os parceiros da Microsoft usem quando seus clientes abrirem tíquetes. Você também deve incluir a URL para o site de suporte.
  - **Contato de engenharia**: forneça o nome, telefone e email para a Microsoft usar diretamente quando houver problemas com sua oferta. Essas informações de contato não estão listadas no Marketplace comercial.
  - **Contato do programa CSP** (opcional): forneça o nome, o telefone e o email se você optar pelo programa CSP (provedor de soluções na nuvem), para que esses parceiros possam contatá-lo com qualquer dúvida. Você também pode incluir uma URL para seus materiais de marketing.
- **Mídia – logotipos**: forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.
  - Grande (de 216 x 216 a 350 x 350 px, required)
  - Médio (90 x 90 px, opcional)
  - Pequeno (48 x 48 px, opcional)

  Esses logotipos são usados em locais diferentes nas lojas online:
  - O logotipo pequeno aparece nos resultados da pesquisa do Azure Marketplace.
  - O logotipo médio é exibido quando você cria um novo recurso no Microsoft Azure.
  - O logotipo grande aparece na sua página de listagem de ofertas no Azure Marketplace.

  Siga estas diretrizes para seus logotipos:

  - O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
  - As cores do tema do portal são branca e preta. Não use essas cores como a cor de fundo de seu logotipo. Use uma cor que destaque seu logotipo no portal. É recomendável usar cores primárias simples.
  - Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não estão em branco, preto ou azul.
  - A aparência do seu logotipo deve ser simples e evitar gradientes no logotipo ou no plano de fundo. Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa. As imagens borradas farão com que seu envio seja rejeitado.
  - Verifique se o logotipo não está esticado.

- **Mídia – capturas de tela** (opcional): Recomendamos que você adicione capturas de tela que mostram como sua oferta funciona. Você pode adicionar até cinco capturas de tela com os seguintes requisitos, que mostram como sua oferta funciona:
  - 1280 x 720 pixels
  - arquivo. png
  - Deve incluir uma legenda
- **Mídia – vídeos** (opcional): você pode adicionar até cinco vídeos com os seguintes requisitos, que demonstram sua oferta:
  - Nome
  - URL: deve ser hospedada somente no YouTube ou Vimeo.
  - Miniatura: 1280 x 720. arquivo PNG

> [!NOTE]
> Sua oferta deve atender às políticas gerais de [certificação do Marketplace comercial](/legal/marketplace/certification-policies#100-general.md) a serem publicadas no Marketplace comercial.

## <a name="preview-audience"></a>Público-alvo de versão prévia

Um público de visualização pode acessar sua oferta antes de ser publicado em lojas online para testar a funcionalidade de ponta a ponta antes de você publicá-la em tempo real.

> [!NOTE]
> Um público de visualização difere de um plano privado. Um plano privado é aquele que você disponibiliza apenas para um público específico que escolher. Isso permite que você negocie um plano personalizado com clientes específicos.

Você define o público de visualização usando as IDs de assinatura do Azure, juntamente com uma descrição opcional para cada uma. Nenhum desses campos pode ser visto pelos clientes.

## <a name="technical-configuration"></a>Configurações técnicas

Para aplicativos gerenciados que emitem eventos de medição usando as [APIs do serviço de medição do Marketplace](partner-center-portal/marketplace-metering-service-apis.md), você deve fornecer a identidade que seu serviço usará ao emitir eventos de medição.

Essa configuração será necessária se você quiser usar [evento de uso do lote](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event). Caso queira enviar o evento de [uso](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event), você também pode usar o [serviço de metadados de instância](../active-directory/managed-identities-azure-resources/overview.md) para obter o token de [portador JWT (token Web JSON)](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)).

- **Azure Active Directory ID do locatário** (obrigatório): dentro do portal do Azure, você deve [criar um aplicativo Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) para que possamos validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para localizar a [ID do locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) do seu aplicativo Azure Active Directory (Azure AD), na folha [Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em seu Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo. Em seguida, procure **Propriedades** e, em seguida, para a **ID do diretório (locatário)** (por exemplo `50c464d3-4930-494c-963c-1e951d15360e` ).
- **ID do aplicativo Azure Active Directory** (obrigatório): você também precisa da [ID do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) e de uma chave de autenticação. Para localizar a ID do aplicativo, vá para a folha [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em você é o Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo e procure a ID do **aplicativo (cliente)** (por exemplo `50c464d3-4930-494c-963c-1e951d15360e` ). Para localizar a chave de autenticação, acesse **Configurações** e selecione **Chaves**. Você precisará fornecer uma descrição e uma duração e, em seguida, um valor numérico será fornecido.

> [!NOTE]
> A ID do aplicativo do Azure será associada à sua ID do editor e só poderá ser usada nesta conta do Publicador.

## <a name="additional-sales-opportunities"></a>Oportunidades de vendas adicionais

Você pode optar por aceitar os canais de marketing e vendas com suporte da Microsoft. Ao criar sua oferta no Partner Center, você verá duas guias em direção ao final do processo:

- **Revenda por meio de CSPs**: Use esta opção para permitir que os parceiros de CSP (provedores de soluções) Microsoft Cloud revendam sua solução como parte de uma oferta agrupada. Consulte [programa do provedor de soluções na nuvem](./cloud-solution-providers.md) para obter mais informações.
- **Covenda com a Microsoft**: essa opção permite que as equipes de vendas da Microsoft considerem sua solução qualificada de venda conjunta de IP ao avaliar as necessidades dos clientes. Para obter informações detalhadas sobre como preparar sua oferta para avaliação, consulte [a opção de venda conjunta no Marketplace comercial](commercial-marketplace-co-sell.md). Para obter detalhes sobre os requisitos de venda de IP, consulte [requisitos para o status de televenda](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP, consulte [provedores de soluções de nuvem](cloud-solution-providers.md).

Para saber mais, confira [aumentar seu negócio na nuvem com o Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Planos

Aplicativo Azure ofertas exigem pelo menos um plano. Um plano define o escopo e os limites da solução e os preços associados, se aplicável. Você pode criar vários planos para sua oferta a fim de fornecer a seus clientes opções de preços e técnicas diferentes.

Para obter diretrizes gerais sobre planos, incluindo modelos de preços e planos privados, consulte [planos e preços para ofertas de Marketplace comercial](plans-pricing.md). As seções a seguir discutem informações adicionais específicas para Aplicativo Azure planos.

### <a name="types-of-plans"></a>Tipos de planos

Há dois tipos de planos de aplicativo do Azure: _modelo de solução_ e _aplicativo gerenciado_. Ambos os tipos de plano dão suporte à automatização da implantação e configuração de uma solução além de uma única VM (máquina virtual). Você pode automatizar o processo de fornecimento de vários recursos, incluindo VMs, rede e recursos de armazenamento para fornecer soluções complexas, como soluções de IaaS. Ambos os tipos de plano podem empregar vários tipos diferentes de recursos do Azure, incluindo, mas não se limitando a VMs.

- Os planos de **modelo de solução** são uma das principais maneiras de publicar uma solução no Marketplace comercial. Os planos de modelo de solução não são pagos no mercado comercial, mas podem ser usados para implantar ofertas de VM pagas que são cobradas por meio do Marketplace comercial. Use o tipo de plano de modelo de solução quando o cliente gerenciar a solução e as transações forem cobradas por meio de outro plano. Para obter mais informações sobre como criar modelos de solução, consulte [o que é Azure Resource Manager?](../azure-resource-manager/management/overview.md)
- Os planos de **aplicativos gerenciados** permitem que você crie e forneça facilmente aplicativos prontos para uso e totalmente gerenciados para seus clientes. Eles têm os mesmos recursos que os planos de modelo de solução, com algumas diferenças importantes:
    - Os recursos são implantados em um grupo de recursos e são gerenciados pelo editor do aplicativo. O grupo de recursos está presente na assinatura do consumidor, mas uma identidade no locatário do fornecedor tem acesso ao grupo de recursos. 
    - Como o Publicador, você especifica o custo para o suporte contínuo da solução e as transações têm suporte por meio do Marketplace comercial.
 
    Use o tipo de plano de aplicativo gerenciado quando você ou seu cliente exigir que a solução seja gerenciada por um parceiro ou se você implantar uma solução baseada em assinatura. Para obter mais informações sobre as vantagens e os tipos de aplicativos gerenciados, consulte [visão geral dos aplicativos gerenciados do Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Próximas etapas

- Para planejar um modelo de solução, consulte [planejar um modelo de solução para uma oferta de aplicativo do Azure](plan-azure-app-solution-template.md).
- Para planejar um aplicativo gerenciado do Azure, consulte [planejar um aplicativo gerenciado do Azure para uma oferta de aplicativo do Azure](plan-azure-app-managed-app.md).