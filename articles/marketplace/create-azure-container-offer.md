---
title: Criar uma oferta de contêiner do Azure – Azure Marketplace
description: Saiba como criar e publicar uma oferta de contêiner no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: bcb334074d842fa5c35e4619f1c5d654405388a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459563"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Criar uma oferta de contêiner do Azure no Azure Marketplace

Este artigo descreve como criar e publicar uma oferta de contêiner para o Azure Marketplace. Antes de começar, [crie uma conta do Marketplace comercial no Partner Center](./partner-center-portal/create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).

2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.

3. Na página Visão geral, selecione **+ Nova oferta** > **Contêiner do Azure**.

   ![Ilustra o menu de navegação à esquerda.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

### <a name="offer-id-and-alias"></a>ID e alias de oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço da Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não poderá ser alterada depois que você selecionar a opção **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Ele não poderá ser alterado depois que você selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Visão geral da oferta

A página **Visão geral da oferta** mostra uma representação visual das etapas necessárias para publicar essa oferta (concluída e próxima) e quanto tempo cada etapa deve levar para ser concluída.

Essa página mostra links diferentes com base no status atual da oferta. Por exemplo:

- Se a oferta for um rascunho – Excluir oferta rascunho
- Se a oferta estiver ativa – [Parar de vender a oferta](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se a oferta estiver em versão prévia – [Ativar](review-publish-offer.md#previewing-and-approving-your-offer)
- Se você ainda não tiver concluído a desconexão do editor – [Cancelar publicação.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configuração da oferta

Siga estas etapas para configurar sua oferta.

### <a name="customer-leads--optional"></a>Clientes potenciais – opcional

Ao publicar sua oferta no marketplace comercial com o Partner Center, você pode conectá-la ao seu sistema de CRM (Gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo.

1. **Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes.** O Partner Center dá suporte aos seguintes sistemas de CRM:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Se seu sistema de CRM não estiver listado acima, use a [tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) ou [ponto de extremidade Https](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de clientes potenciais e depois exporte os dados para seu sistema de CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicá-la no Partner Center.
3. Confirme que a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la no Partner Center, validaremos a conexão e enviaremos um cliente potencial de teste para você. Ao colocar a oferta em versão prévia antes de ela ficar ativa, você também pode testar a conexão do cliente potencial tentando comprar a oferta por conta própria no ambiente de versão prévia.
4. A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

Veja alguns recursos adicionais de gerenciamento de clientes potenciais:

- [Vendas potenciais do cliente da sua oferta do marketplace comercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Perguntas comuns sobre o gerenciamento de vendas potenciais](lead-management-faq.md#common-questions-about-lead-management)
- [Solucionando problemas de erros de configuração de Lead](lead-management-faq.md#publishing-config-errors)
- PDF de [Visão geral do gerenciamento de clientes potenciais](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (desative seu bloqueador de pop-ups).

Selecione **Salvar rascunho** antes de continuar.

### <a name="properties"></a>Propriedades

Essa página permite que você defina as categorias usadas para agrupar sua oferta no marketplace e os contratos legais que dão suporte à sua oferta.

#### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md). Os contêineres sempre aparecem sob **contêineres** e, em seguida, na categoria **imagens de contêiner** .

#### <a name="legal"></a>Legal

Você deve fornecer os termos e as condições da oferta. Há duas opções:

- Usar o Contrato Standard para o marketplace comercial da Microsoft.
- Fornecer seus próprios termos e condições.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Standard para o marketplace comercial da Microsoft

Oferecemos um modelo de Contrato Standard para ajudar a facilitar as transações no marketplace comercial. Você pode optar por oferecer sua solução sob o Contrato Standard, o qual os clientes precisam verificar e aceitar apenas uma vez. Essa é uma boa opção caso você não queira criar termos e condições personalizados.

Para saber mais sobre o Contrato Standard, consulte [Usar o Contrato Standard para o marketplace comercial da Microsoft](standard-contract.md). Você também pode baixar o arquivo PDF do [Contrato Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se seu bloqueador de pop-ups está desativado).

Para usar o contrato padrão, selecione o * * usar o contrato padrão do Marketplace comercial da Microsoft] (.. /standard-contract.md)

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Standard para o marketplace comercial da Microsoft, você não poderá mais usar seus termos e condições personalizados. Ofereça uma solução sob o Contrato Standard ou sob seus próprios termos e condições.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustra a caixa de seleção Usar o Contrato Standard para o marketplace comercial da Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer seus termos e condições personalizados, insira-os na caixa **Termos e condições**. Você pode inserir um número ilimitado de caracteres de texto nesta caixa. Os clientes precisam aceitar esses termos antes de poderem experimentar sua oferta.

Selecione **Salvar rascunho** antes de seguir para a próxima seção chamada Listagem de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Essa página permite que você defina os detalhes da oferta exibidos no marketplace comercial. Isso inclui o nome da oferta, a descrição e as imagens.

> [!NOTE]
> Os detalhes da oferta não precisam estar em inglês se a descrição da oferta iniciar com a frase: “Este aplicativo só está disponível em [idioma, exceto inglês].” Também é bom fornecer um link útil para oferecer conteúdo em um idioma diferente daquele usado nos detalhes da Listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** ao criar a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser um nome comercial (e você pode incluir os símbolos de marca registrada e de copyright).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Ela pode ter até 100 caracteres e é usada nos resultados da pesquisa do marketplace.

### <a name="long-summary"></a>Resumo longo

Uma descrição mais detalhada da sua oferta. Ela pode ter até 256 caracteres e é usada nos resultados da pesquisa do marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira o endereço web da política de privacidade da sua organização. Você é o responsável por garantir que sua oferta esteja em conformidade com as leis e os regulamentos de privacidade. Você também é o responsável por publicar uma política de privacidade válida em seu site.

#### <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Você pode adicionar até 25 links. Para adicionar um link, selecione **+ Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Título** – os clientes o verão na página de detalhes da sua oferta.
- **Link (URL)** – insira um link para que os clientes vejam seu documento online. O link deve começar com http:// ou https://.

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone de um **Contato de suporte** e de um **Contato de engenharia**. Essas informações não são mostradas aos clientes, mas ficarão disponíveis para a Microsoft. Elas também podem ser fornecidas para parceiros CSP (Provedor de Soluções na Nuvem).

- Contato de suporte (obrigatório): para questões gerais de suporte.
- Contato de engenharia (obrigatório): para questões técnicas e problemas de certificação.
- Contato de programa CSP (opcional): para questões sobre o revendedor relacionadas ao programa CSP.

Na seção **Contato do suporte**, forneça o **Site de suporte** em que os parceiros podem encontrar suporte para sua oferta com base em se ela está disponível no Azure global, no Azure Governamental ou em ambos.

Na seção **Contato do Programa CSP**, forneça o link (**Materiais de marketing do Programa CSP**) em que os parceiros CSP podem encontrar materiais de marketing relacionados à sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

Para saber mais sobre a criação de listagens de ofertas, consulte [Práticas recomendadas de listagem de ofertas](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. Imagens borradas serão rejeitadas.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada uma deve ter 1280 x 720 pixels de tamanho e estar no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstrem sua oferta. Insira o nome do vídeo, seu endereço web e uma imagem PNG em miniatura do vídeo com tamanho de 1280 x 720 pixels.

#### <a name="offer-examples"></a>Exemplos de oferta

Os exemplos a seguir mostram como os campos da listagem de oferta aparecem em diferentes locais da oferta.

Isso mostra a página **Listagem de oferta** no Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustra a página Listagem de ofertas no Azure Marketplace." :::

A imagem mostra os resultados da pesquisa no Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustra os resultados da pesquisa no Azure Marketplace.":::

A imagem mostra a página **Listagem de ofertas** no portal do Azure:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustra a página Listagem de ofertas no portal do Azure.":::

A imagem mostra os resultados da pesquisa no portal do Azure:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustra os resultados da pesquisa no portal do Azure.":::

## <a name="preview"></a>Visualização

Na guia Versão prévia, você pode escolher um **Público-alvo da versão prévia** limitado para validar sua oferta antes de publicá-la e torná-la ativa.

> [!IMPORTANT]
> Depois de exibir sua oferta como **Versão prévia**, você deve selecionar **Ativar** para publicá-la.

Especifique o público-alvo da versão prévia usando por GUIDs de ID da assinatura do Azure juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes.

> [!NOTE]
> É possível encontrar a ID da assinatura na página Assinaturas no portal do Azure.

Adicione pelo menos uma ID da assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você determina quem pode ter acesso à versão prévia de sua oferta antes que ela seja publicada como ativa. Caso sua oferta já esteja ativa, você poderá escolher um público-alvo da versão prévia para testar alterações ou atualizações da oferta.

Selecione **Salvar rascunho** antes de continuar.

## <a name="plan-overview"></a>Visão geral do plano

Essa guia permite que você forneça opções de plano diferentes na mesma oferta. Os planos (anteriormente chamados de SKUs) podem diferir em termos de quais nuvens estão disponíveis, como nuvens globais, nuvens governamentais e a imagem referenciada pelo plano. Para listar sua oferta no marketplace comercial, você deve configurar pelo menos um plano.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

Depois de criar seus planos, a guia **Visão geral do plano** exibe:

- Nomes de plano
- Modelo de preços
- Regiões do Azure (global ou governamental)
- Status da publicação atual
- Nenhuma ação disponível

As ações disponíveis no painel Visão geral do plano variam de acordo com o status atual do seu plano. Elas incluem:

- **Excluir rascunho** – se o status de plano for um rascunho.
- **Interromper plano de venda** – se o status do plano for publicado e ativo.

### <a name="create-new-plan"></a>Criar novo plano

Selecione **Criar novo plano**. A caixa de diálogo **Novo plano** é exibida.

Na caixa **ID do plano**, crie um identificador de plano exclusivo para cada plano nessa oferta. Essa ID será visível para os clientes no endereço web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

> [!NOTE]
> A ID do plano não poderá ser alterada depois que você selecionar a opção **Criar**.

Na caixa **Nome do plano**, insira um nome para esse plano. Os clientes veem esse nome quando estão decidindo qual plano selecionar dentro da sua oferta. Crie um nome exclusivo para cada plano nessa oferta. Por exemplo, você pode usar um nome de oferta do **Windows Server** com planos **Windows Server 2016** e **Windows Server 2019**.

### <a name="plan-setup"></a>Configuração do plano

Essa guia permite que você escolha em quais nuvens o plano ficará disponível. Suas respostas nessa guia afetam quais campos serão exibidos em outras guias.

#### <a name="azure-regions"></a>Regiões do Azure

Todos os planos para ofertas de contêiner do Azure são disponibilizados automaticamente no **Azure global**.  Seu plano pode ser usado por clientes em todas as regiões globais do Azure que usam o Marketplace comercial. Para obter mais informações, consulte [Disponibilidade geográfica e suporte a moeda](marketplace-geo-availability-currencies.md).

Selecione a opção [Azure governamental](../azure-government/documentation-government-welcome.md) para que sua solução apareça aqui. Essa é uma nuvem de comunidade governamental com acesso controlado para clientes de agências federais, estaduais e locais ou tribais do governo dos EUA, bem como parceiros qualificados para atendê-las. Por ser o editor, você é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas dessa comunidade de nuvem. O Azure Governamental usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA). Antes de [publicar](../azure-government/documentation-government-manage-marketplace-partners.md) no Azure Governamental, teste e confirme sua solução dentro dessa área, pois os resultados podem ser diferentes. Para criar e testar sua solução, solicite uma conta de avaliação em [Avaliação do Microsoft Azure Governamental](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e disponível em uma região específica, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só poderá ser vista se o **Azure governamental** estiver selecionado em **regiões do Azure**.

Os serviços do Azure Governamental manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS.

Para exibir certificações desses programas, você pode fornecer até 100 links que as descrevem. Podem ser links diretos para suas listagens no programa ou para seu próprio site. Esses links são visíveis somente para clientes do Azure Governamental.

### <a name="plan-listing"></a>Listagem de planos

Essa guia exibe informações específicas de cada plano diferente presente na oferta atual.

### <a name="plan-name"></a>Nome do plano

Esse campo é pré-preenchido com o nome que você deu ao plano quando o criou. É possível alterar esse nome conforme o necessário. Ele pode ter até 50 caracteres. Esse nome aparece como o título desse plano no Azure Marketplace e no portal do Azure. Ele é usado como o nome do módulo padrão depois que o plano estiver pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Um breve resumo do seu plano de software (não da oferta). Esse resumo aparece nos resultados de pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software exclusivo e descreva as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. Essa descrição será exibida no Azure Marketplace e no portal do Azure na página **Listagem de ofertas**. Ela pode ter o mesmo conteúdo que você inseriu no resumo do plano e conter até 2.000 caracteres.

Selecione **Salvar** depois de preencher esses campos.

#### <a name="plan-examples"></a>Exemplos de planos

Os exemplos a seguir mostram como os campos de listagem de plano são exibidos em modos de exibição diferentes.

A imagem a seguir mostra os campos do Azure Marketplace ao exibir detalhes do plano:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustra os campos que são vistos ao exibir detalhes do plano no Azure Marketplace.":::

A imagem a seguir mostra os detalhes do plano no portal do Azure:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustra os detalhes do plano no portal do Azure.":::

### <a name="plan-availability"></a>Disponibilidade do plano

Caso queira ocultar sua oferta publicada para que os clientes não possam pesquisá-la, navegar por ela ou comprá-la no marketplace, marque a caixa de seleção **Ocultar plano** na guia **Disponibilidade**.

Esse campo é usado quando:

- A oferta destina-se a ser usada indiretamente quando referenciada por outro aplicativo.
- A oferta não deva ser adquirida individualmente.
- O plano for usado para o teste inicial e não for mais relevante.
- O plano tiver sido usado para ofertas temporárias ou sazonais e não deva mais ser oferecido.

## <a name="technical-configuration"></a>Configurações técnicas

As imagens de contêiner devem ser hospedadas em um [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) que seja privado. Na guia **Configuração Técnica**, forneça informações de referência para seu repositório de imagens de contêiner dentro do Registro de Contêiner do Azure.

Assim que a oferta for publicada, a imagem de contêiner será copiada para o Azure Marketplace em um registro de contêiner público específico. Todas as solicitações para usar sua imagem de contêiner são ofertadas no registro de contêiner público do Azure Marketplace e não no seu registro privado. Para obter detalhes, consulte [Preparar seus ativos técnicos do Contêiner do Azure](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Detalhes do repositório de imagens

Forneça as informações a seguir na guia **Detalhes do repositório de imagens**.

**ID da assinatura do Azure** – forneça a ID da assinatura na qual o uso é relatado e os serviços são cobrados para o Registro de Contêiner do Azure que inclui sua imagem de contêiner. É possível encontrar essa ID na [página Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

**Nome do grupo de recursos do Azure** – forneça o nome do [grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md) que contém o Registro de Contêiner do Azure com a imagem de contêiner. O grupo de recursos deve poder ser acessado na ID da assinatura (acima). Você pode encontrar o nome na página [Grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) no portal do Azure.

**Nome do Registro de Contêiner do Azure** – forneça o nome do [Registro de Contêiner do Azure](../container-registry/container-registry-intro.md) que tenha sua imagem de contêiner. O registro de contêiner deve estar no grupo de recursos do Azure que fornecido anteriormente. Inclua somente o nome do registro, não o nome completo do servidor de logon. Lembre-se de omitir o **azurecr.io** do nome. Você pode encontrar o nome do registro na [página Registros de Contêiner](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) no portal do Azure.

**Nome de usuário do administrador para o registro de contêiner do Azure** – forneça o [nome de usuário do administrador](../container-registry/container-registry-authentication.md#admin-account)) vinculado ao registro de contêiner do Azure que tem sua imagem de contêiner. O nome de usuário e a senha são obrigatórios para garantir que sua empresa tenha acesso ao registro. Para obter o nome de usuário do administrador e a senha, defina a propriedade **admin-enabled** como **True** usando a CLI (Interface de Linha de Comando) do Azure. Como opção, você pode definir **Usuário administrador** como **Habilitar** no portal do Azure.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustra a caixa de diálogo Atualizar registro de contêiner.":::

**Senha para o Registro de Contêiner do Azure** – forneça a senha para o nome de usuário do administrador que está associado ao Registro de Contêiner do Azure e tem sua imagem de contêiner. O nome de usuário e a senha são obrigatórios para garantir que sua empresa tenha acesso ao registro. Você pode obter a senha do portal do Azure acessando **Registro de Contêiner** > **Chaves de Acesso** ou com a CLI do Azure usando o [comando mostrar](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustra o menu Chave de Acesso.":::

**Nome do repositório no Registro de Contêiner do Azure**. Forneça o nome do repositório do Registro de Contêiner do Azure que tem sua imagem. Inclua o nome do repositório quando enviar a imagem por push para o registro. Você pode encontrar o nome do repositório acessando a página [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/) > **Repositórios**. Para obter mais informações, consulte [Exibir repositórios de registro de contêiner no portal do Azure](../container-registry/container-registry-repositories.md).

> [!NOTE]
> Depois de definir o nome, ele não poderá mais ser alterado. Use um nome exclusivo para cada oferta presente em sua conta.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tags de imagem para novas versões da sua oferta

Os clientes devem ser capazes de obter as atualizações automaticamente do Azure Marketplace quando você as publicar. Caso não queiram fazer atualizações, eles deverão ser capazes de continuar usando uma versão específica da imagem. Você pode fazer isso adicionando novas tags de imagem sempre que fizer uma atualização da imagem.

### <a name="image-tag"></a>Tag de imagem

Esse campo deve incluir uma tag de **última** que aponta para a última versão da sua imagem em todas as plataformas com suporte. Ela também deve incluir uma tag de versão (por exemplo, começando com xx.xx.xx, em que xx é um número). Os clientes devem usar [tags de manifesto](https://github.com/estesp/manifest-tool) para segmentar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las.

Todas as marcas de manifesto (exceto a tag última) devem começar com X.Y **-** ou X.Y.Z-, em que X, Y, Z são inteiros. Por exemplo, se uma tag **última** pontos para 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, essas seis tags precisarão ser adicionadas a esse campo. Para obter detalhes, consulte [Preparar seus ativos técnicos do Contêiner do Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> Lembre-se de adicionar uma tag de teste à sua imagem para que você possa identificar a imagem durante o teste.

## <a name="review-and-publish"></a>Revisar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-las para revisão e publicação.

No canto superior direito do portal, selecione **Revisar e** **publicar**.

Na página de revisão, você pode:

- Ver o status de conclusão de cada seção da oferta. Você não conseguirá publicar até que todas as seções da oferta estejam marcadas como concluídas.
  - **Não iniciada** – a seção não foi iniciada e precisa ser concluída.
  - **Incompleta** – a seção tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores deste documento para obter ajuda.
  - **Concluída** – a seção conta com todos os dados necessários e não apresenta erros. Todas as seções da oferta precisam ser concluídas para que você envie a oferta.
- Forneça as instruções de teste à equipe de certificação para garantir que sua oferta seja testada corretamente. Também forneça observações suplementares que ajudem a entender a sua oferta.

Para enviar a oferta para publicação, selecione **Publicar**.

Enviaremos um email para que você saiba quando uma versão prévia da oferta estiver disponível para revisão e aprovação.

Para publicar sua oferta no público, vá para o Partner Center e selecione **Go-Live**.

## <a name="next-step"></a>Próxima etapa

- [Atualizar uma oferta existente no marketplace comercial](./partner-center-portal/update-existing-offer.md)