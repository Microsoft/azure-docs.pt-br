---
title: Criar uma oferta de módulo do Azure IoT Edge com o Partner Center no Azure Marketplace
description: Saiba como criar, configurar e publicar uma oferta de módulo IoT Edge no Azure Marketplace usando o Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 23af7b3f7cfd5ec0531c5cac73cf8a334ef4aa62
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096433"
---
# <a name="create-an-iot-edge-module-offer"></a>Criar uma oferta de módulo do IoT Edge

Este artigo descreve como criar e publicar uma oferta de módulo do IoT (Internet das Coisas) Edge para o Azure Marketplace. Antes de começar, [crie uma conta do marketplace comercial no Partner Center](create-account.md) se você ainda não tiver feito isso. Verifique se sua conta está inscrita no programa do marketplace comercial.

## <a name="create-a-new-offer"></a>Criar uma oferta

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação esquerdo, selecione **Marketplace Comercial** > **Visão geral**.
3. Na página Visão geral, selecione **+ Nova oferta** > **Módulo do IoT Edge**.

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Depois que uma oferta é publicada, as edições feitas nela no Partner Center aparecem somente em lojas online após a republicação da oferta. Depois de fazer alterações, é sempre necessário republicar.

### <a name="offer-id-and-alias"></a>ID e alias de oferta

Insira uma **ID da oferta**. Esse é um identificador exclusivo para cada oferta em sua conta.

- Essa ID é visível para os clientes no endereço Web para a oferta do Marketplace e nos modelos do Azure Resource Manager, se aplicável.
- Use apenas letras minúsculas e números. Ela pode incluir hifens e sublinhados, mas sem espaços, e está limitada a 50 caracteres. Por exemplo, se você inserir **test-offer-1**, o endereço Web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- A ID da oferta não pode ser alterada depois que você seleciona **Criar**.

Insira um **Alias da oferta**. Esse é o nome usado para a oferta no Partner Center.

- Esse nome não é usado no Marketplace e é diferente do nome da oferta e de outros valores mostrados aos clientes.
- Ele não poderá ser alterado depois que você selecionar **Criar**.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-overview"></a>Visão geral da oferta

A página **Visão geral da oferta** mostra uma representação visual das etapas necessárias para publicar essa oferta (concluída e próxima) e quanto tempo cada etapa deve levar para ser concluída.

Essa página inclui links para executar operações nessa oferta com base na seleção feita por você. Por exemplo:

- Se a oferta for um rascunho – Excluir oferta rascunho
- Se a oferta estiver ativa – [Parar de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se a oferta estiver em versão prévia – [Ativar](../review-publish-offer.md#previewing-and-approving-your-offer)
- Se você ainda não tiver concluído a desconexão do editor – [Cancelar publicação.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Configuração da oferta

Siga estas etapas para configurar sua oferta.

### <a name="customer-leads"></a>Clientes potenciais

Ao publicar sua oferta no Marketplace com o Partner Center, você pode opcionalmente conectá-lo ao seu sistema CRM (gerenciamento de relacionamento com o cliente). Isso permite que você receba informações de contato do cliente assim que alguém expressar interesse em seu produto ou usá-lo.

1. Escolha um destino de cliente potencial para o qual você deseja que enviemos vendas potenciais de clientes. O Partner Center dá suporte aos seguintes sistemas de CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) para Participação do Usuário
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se seu sistema de CRM não estiver listado acima, use a [tabela do Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou [ponto de extremidade HTTPS](commercial-marketplace-lead-management-instructions-https.md) para armazenar dados de clientes potenciais e depois exporte os dados para seu sistema de CRM.

2. Conecte sua oferta ao destino do cliente potencial ao publicá-la no Partner Center.
3. Confirme se a conexão com o destino do cliente potencial está configurada corretamente. Depois de publicá-la no Partner Center, validaremos a conexão e enviaremos um cliente potencial de teste para você. Ao colocar a oferta em versão prévia antes de ela ficar ativa, você também pode testar a conexão do cliente potencial tentando comprar a oferta por conta própria no ambiente de versão prévia.
4. A conexão com o destino do cliente potencial deve permanecer atualizada para que você não perca nenhum cliente potencial.

Veja alguns recursos adicionais de gerenciamento de clientes potenciais:

- [Vendas potenciais do cliente da sua oferta do marketplace comercial](commercial-marketplace-get-customer-leads.md)
- [Perguntas comuns sobre o gerenciamento de vendas potenciais](../lead-management-faq.md#common-questions-about-lead-management)
- [Solucionando problemas de erros de configuração de Lead](../lead-management-faq.md#publishing-config-errors)
- PDF de [Visão geral do gerenciamento de clientes potenciais](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) (desative seu bloqueador de pop-ups).

Selecione **Salvar rascunho** antes de continuar.

### <a name="properties"></a>Propriedades

Essa página permite que você defina as categorias usadas para agrupar sua oferta no marketplace e os contratos legais que dão suporte à sua oferta.

#### <a name="category"></a>Categoria

Selecione categorias e subcategorias para posicionar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e uma secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for aplicável à sua oferta, selecione **não aplicável**.

Veja a lista completa de categorias e subcategorias nas [melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md). No Marketplace, os módulos IOT Edge são sempre mostrados na categoria  **Internet das coisas**  >  **IOT Edge Module**   .

#### <a name="legal"></a>Legal

Você precisa fornecer os termos e condições da oferta. Você tem duas opções:

- Usar o Contrato Standard para o marketplace comercial da Microsoft.
- Fornecer seus próprios termos e condições.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrato Standard para o marketplace comercial da Microsoft

Oferecemos um modelo de Contrato Standard para ajudar a facilitar as transações no marketplace comercial. Você pode optar por oferecer sua solução sob o Contrato Standard, o qual os clientes precisam verificar e aceitar apenas uma vez. Essa é uma boa opção caso você não queira criar termos e condições personalizados.

Para saber mais sobre o Contrato Standard, consulte [Usar o Contrato Standard para o marketplace comercial da Microsoft](../standard-contract.md). Você também pode baixar o arquivo PDF do [Contrato Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (verifique se seu bloqueador de pop-ups está desativado).

Para usar o Contrato Standard, marque a caixa de seleção **Usar o Contrato Standard para o marketplace comercial da Microsoft** e clique em **Aceitar**.

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Standard para o marketplace comercial da Microsoft, você não poderá mais usar seus termos e condições personalizados. Ofereça uma solução sob o Contrato Standard ou sob seus próprios termos e condições.

![Ilustra o uso do Contrato Standard para o marketplace comercial da Microsoft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Seus próprios termos e condições

Para fornecer seus termos e condições personalizados, insira-os na caixa **Termos e Condições**. É possível inserir uma quantidade ilimitada de caracteres de texto nessa caixa. Os clientes precisam aceitar esses termos antes de poderem experimentar sua oferta.

Selecione **Salvar rascunho** antes de seguir para a próxima seção chamada Listagem de ofertas.

## <a name="offer-listing"></a>Listagem de ofertas

Aqui, você definirá os detalhes da oferta que são mostrados no marketplace. Isso inclui o nome da oferta, a descrição, as imagens etc. É preciso que, ao configurar essa oferta, você siga as políticas detalhadas na página de políticas da Microsoft.

> [!NOTE]
> Os detalhes da oferta não precisarão estar em inglês se a descrição da oferta iniciar com a frase: "Este aplicativo só está disponível em [idioma diferente do inglês]". Também é válido fornecer um Link Útil para oferecer conteúdo em um idioma diferente daquele usado nos detalhes da Listagem de ofertas.

### <a name="name"></a>Nome

O nome que você digitar aqui será exibido como o título de sua oferta. Esse campo é preenchido com o nome que você inseriu na caixa **Alias da oferta** ao criar a oferta. Você pode alterar esse nome posteriormente.

O nome:

- Pode ser um nome comercial (e você pode incluir os símbolos de marca registrada ou de direitos autorais).
- Não pode ter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Descreva brevemente a sua oferta. Essa descrição pode ter até 100 caracteres e é usada nos resultados da pesquisa do marketplace.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais detalhada da sua oferta. Essa descrição pode ter até 256 caracteres e é usada nos resultados da pesquisa do marketplace.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

As ofertas do módulo do IoT Edge precisam incluir um parágrafo de requisitos mínimos de hardware na parte inferior da descrição, como:

- Requisitos mínimos de hardware: Linux x64 e arm32 do sistema operacional, 1 GB de RAM, 500 MB de armazenamento

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL da política de privacidade

Insira o endereço Web da política de privacidade da sua organização. Você é o responsável por garantir que sua oferta esteja em conformidade com as leis e os regulamentos de privacidade. Você também é o responsável por publicar uma política de privacidade válida em seu site.

#### <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Você pode adicionar até 25 links. Para adicionar um link, selecione **+ Adicionar um link** e, em seguida, preencha os seguintes campos:

- **Título** – os clientes verão o título na página de detalhes da sua oferta.
- **Link (URL)** – insira um link para que os clientes vejam seu documento online. O link deve começar com `http://` ou `https://` .

Adicione pelo menos um link à sua documentação e um link para os dispositivos compatíveis do IoT Edge do  [catálogo de dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Informações de contato

Você deve fornecer o nome, o email e o número de telefone de um **Contato de suporte** e de um **Contato de engenharia**. Essas informações não são mostradas aos clientes. Elas estão disponíveis para a Microsoft e podem ser fornecidas a parceiros CSP (provedores de solução de nuvem).

- Contato de suporte (obrigatório): Para questões gerais de suporte.
- Contato de engenharia (obrigatório): para questões técnicas e problemas de certificação.
- Contato de programa CSP (opcional): Para questões sobre o revendedor relacionadas ao programa CSP.

Na seção **Contato do suporte**, forneça o endereço Web do **Site de suporte** em que os parceiros podem encontrar suporte para sua oferta com base em se ela está disponível no Azure global, no Azure Government ou em ambos.

Na seção **Contato do Programa CSP**, forneça o link (**Materiais de marketing do Programa CSP**) em que os parceiros CSP podem encontrar materiais de marketing relacionados à sua oferta.

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do marketplace

Para saber mais sobre a criação de listagens de ofertas, confira [Melhores práticas de listagem de ofertas](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Imagens do Marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. Imagens borradas serão rejeitadas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

#### <a name="store-logos"></a>Armazenar logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Capturas de tela (opcional)

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada uma deve ter 1280 x 720 pixels de tamanho e estar no formato PNG.

#### <a name="videos-optional"></a>Vídeos (opcional)

Adicione até cinco vídeos que demonstrem sua oferta. Insira o nome do vídeo, seu endereço web e uma imagem PNG em miniatura do vídeo com tamanho de 1280 x 720 pixels.

#### <a name="marketplace--examples"></a>Exemplos do Marketplace

Aqui está um exemplo de como as informações de oferta são exibidas no Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Ilustra como essa oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo grande
2. Categorias
3. Endereço de suporte (link)
4. Termos e condições
5. Endereço de política de privacidade (link)
6. Nome
7. Resumo
8. Descrição
9. Links úteis
10. Capturas de tela/vídeos

<br>Aqui está um exemplo de como as informações de oferta são exibidas nos resultados da pesquisa do Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Ilustra como essa oferta aparece nos resultados da pesquisa do Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa

<br>Aqui está um exemplo de como as informações de oferta são exibidas no portal do Azure:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Ilustra como essa oferta aparece na portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome
2. Descrição
3. Links úteis
4. Capturas de tela

<br>Aqui está um exemplo de como as informações de oferta são exibidas nos resultados da pesquisa portal do Azure:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Ilustra como essa oferta aparece nos portal do Azure resultados da pesquisa.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa

<br>Selecione **Salvar rascunho** antes de prosseguir para a próxima seção, Versão prévia.

## <a name="preview"></a>Visualização

Na **guia Versão prévia**, você pode escolher um **Público-alvo de versão prévia** para validar sua oferta antes de publicá-la como ativa para o público-alvo geral do marketplace.

> [!IMPORTANT]
> Depois de exibir sua oferta como Versão prévia, você precisa selecionar **Ativar** para publicá-la para o público em geral.

Especifique o público-alvo da versão prévia usando por GUIDs de ID da assinatura do Azure juntamente com uma descrição opcional para cada um. Nenhum desses campos pode ser visto pelos clientes.

> [!NOTE]
> É possível encontrar a ID da assinatura do Azure na página Assinaturas no portal do Azure.

Adicione pelo menos uma ID da assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100). Ao adicionar essas IDs de assinatura, você define quem pode ter acesso à versão prévia de sua oferta antes que ela seja publicada como ativa. Caso sua oferta já esteja ativa, você poderá definir um público-alvo da versão prévia para testar alterações ou atualizações da oferta.

Selecione **Salvar rascunho** antes de prosseguir para a próxima seção, Visão geral do plano.

## <a name="plan-overview"></a>Visão geral do plano

Essa guia permite que você forneça opções de plano diferentes na mesma oferta no Partner Center. Os planos (anteriormente chamados de SKUs) podem diferir em termos de quais nuvens estão disponíveis, como nuvens globais, nuvens governamentais e a imagem referenciada pelo plano. Para listar sua oferta no marketplace, você precisa configurar pelo menos um plano.

Você pode criar até 100 planos para cada oferta: até 45 deles podem ser privados. Saiba mais sobre planos privados em [ofertas privadas no Microsoft Commercial Marketplace](../private-offers.md).

Depois de criar seus planos, a guia **Visão geral do plano** exibe:

- Nomes de plano
- Modelo de preços
- Regiões do Azure (global ou governamental)
- Status da publicação atual
- Nenhuma ação disponível

As ações disponíveis no painel Visão geral do plano variam de acordo com o status atual do seu plano. Elas incluem:

- **Excluir o rascunho**: se o status de plano for de rascunho.
- **Parar venda do plano**: se o status do plano for publicado e ativo.

### <a name="create-new-plan"></a>Criar plano

Selecione **Criar novo plano**. A caixa de diálogo **Novo plano** é exibida.

Na caixa **ID do plano**, crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes no endereço Web do produto. Use apenas letras minúsculas e números, traços ou sublinhados e, no máximo, 50 caracteres.

Na caixa **Nome do plano**, insira um nome para esse plano. Os clientes veem esse nome quando estão decidindo qual plano selecionar dentro da sua oferta. Crie um nome exclusivo para cada plano nessa oferta. Por exemplo, você pode usar um nome de oferta do **Windows Server** com planos **Windows Server 2016** e **Windows Server 2019**.

> [!NOTE]
> A ID do plano não pode ser alterada depois que você seleciona **Criar**.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Essa guia permite que você configure em quais nuvens o plano ficará disponível. Suas respostas nessa guia afetam quais campos serão exibidos em outras guias.

#### <a name="azure-regions"></a>Regiões do Azure

Todos os planos para ofertas de módulo IoT Edge são disponibilizados automaticamente no **Azure global**.  Seu plano pode ser usado por clientes em todas as regiões globais do Azure que usam o Marketplace. Para obter mais informações, consulte [Disponibilidade geográfica e suporte a moeda](../marketplace-geo-availability-currencies.md).

Selecione a opção [Azure governamental](../../azure-government/documentation-government-welcome.md) para que sua solução apareça aqui. Essa é uma nuvem de comunidade governamental com acesso controlado para clientes de agências federais, estaduais e locais ou tribais do governo dos EUA, bem como parceiros qualificados para atendê-las. Por ser o editor, você é responsável por eventuais controles de conformidade, medidas de segurança e melhores práticas dessa comunidade de nuvem. O Azure Governamental usa datacenters isolados fisicamente e redes (localizadas apenas nos EUA). Antes de [publicar](../../azure-government/documentation-government-manage-marketplace-partners.md) no Azure Government, teste e confirme sua solução dentro dessa área, pois os resultados podem ser diferentes. Para preparar e testar sua solução, solicite uma conta de avaliação em [Avaliação do Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois que o plano for publicado e disponível em uma região específica, você não poderá remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Azure Government

Essa opção só estará visível se o **Azure governamental** estiver selecionado em **regiões do Azure**.

Os serviços do Azure Governamental manipulam os dados que estão sujeitos a determinadas normas e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para oferecer reconhecimento das suas certificações para esses programas, você pode fornecer até 100 links que as descrevem. Eles podem ser links diretos para suas listagens no programa ou para seu próprio site. Esses links são visíveis somente para clientes do Azure Governamental.

### <a name="plan-listing"></a>Listagem de planos

Essa guia exibe informações específicas de cada plano diferente presente na mesma oferta.

### <a name="plan-name"></a>Nome do plano

Esse campo é pré-preenchido com o nome que você deu ao plano quando o criou. É possível alterar esse nome conforme o necessário. Ele pode ter até 50 caracteres. Esse nome aparece como o título deste plano no Azure Marketplace e o portal do Azure. Ele é usado como o nome do módulo padrão depois que o plano estiver pronto para ser usado.

### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não da oferta. Esse resumo aparece nos resultados da pesquisa do Azure Marketplace e pode conter até 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano exclusivo e descreva as diferenças entre os planos em sua oferta. Não descreva a oferta, apenas o plano. Essa descrição será exibida no Azure Marketplace e no portal do Azure na página Listagem de ofertas. Ela pode ter o mesmo conteúdo que você inseriu no resumo do plano e conter até 2.000 caracteres.

Selecione **Salvar rascunho** depois de preencher esses campos.

#### <a name="plan-examples"></a>Exemplos de planos

Aqui está um exemplo de detalhes do plano do Azure Marketplace (os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Ilustra os detalhes do plano do Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome da oferta
2. Nome do plano
3. Descrição do plano

<br>Aqui está um exemplo dos detalhes do plano de portal do Azure (todos os preços listados são apenas para fins de exemplo e não têm a finalidade de refletir os custos reais):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Ilustra os detalhes do plano de portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Nome da oferta
2. Nome do plano
3. Descrição do plano

### <a name="availability"></a>Disponibilidade

Caso queira ocultar sua oferta publicada para que os clientes não possam pesquisá-la, navegar por ela ou comprá-la no marketplace, marque a caixa de seleção **Ocultar plano** na guia Disponibilidade.

Esse campo será usado normalmente se:

- A oferta se destinar a ser usada apenas indiretamente quando referenciada por outro aplicativo.
- A oferta não deva ser adquirida individualmente.
- O plano for usado para o teste inicial e não for mais relevante.
- O plano tiver sido usado para ofertas temporárias ou sazonais e não deva mais ser oferecido.

## <a name="technical-configuration"></a>Configuração técnica

O tipo de oferta de **módulo do IoT Edge** é um tipo específico de contêiner em execução em um dispositivo do IoT Edge. Na guia **configuração técnica** , você fornecerá informações de referência para o repositório de imagens de contêiner dentro do [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/), juntamente com as definições de configuração que permitem que os clientes usem o módulo facilmente.

Assim que a oferta for publicada, a imagem de contêiner do IoT Edge será copiada para o Azure Marketplace em um registro de contêiner público específico. Todas as solicitações de usuários do Azure para usar seu módulo serão atendidas do registro de contêiner público do Azure Marketplace e não no seu registro de contêiner privado.

É possível direcionar a várias plataformas e fornecer várias versões da sua imagem de contêiner de módulo por meio de marcas. Para saber mais sobre marcas e controle de versão, confira [Preparar os ativos técnicos do módulo do IoT Edge](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Detalhes do repositório de imagens

Você fornecerá as informações a seguir na guia **Detalhes do repositório de imagens**.

**Selecione a origem da imagem**: selecione a opção **Registro de Contêiner do Azure**.

**ID da assinatura do Azure**: forneça a ID da assinatura na qual o uso do recurso é relatado e os serviços são cobrados para o Registro de Contêiner do Azure que inclui sua imagem de contêiner. É possível encontrar essa ID na [página Assinaturas](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

**Nome do grupo de recursos do Azure**: forneça o nome do [grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md) que contém o Registro de Contêiner do Azure com a imagem de contêiner. O grupo de recursos precisa poder ser acessado na ID da assinatura (acima). Você pode encontrar o nome na página [Grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) no portal do Azure.

**Nome do Registro de Contêiner do Azure**: forneça o nome do [Registro de Contêiner do Azure](../../container-registry/container-registry-intro.md) que tem sua imagem de contêiner. O registro de contêiner precisa estar presente no grupo de recursos do Azure que você forneceu anteriormente. Forneça somente o nome do Registro, não o nome completo do servidor de logon. Lembre-se de omitir **azurecr.io** do nome. Você pode encontrar o nome do registro na [página Registros de Contêiner](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) no portal do Azure.

**Nome de usuário do administrador para o registro de contêiner do Azure**: forneça o [nome de usuário do administrador](../../container-registry/container-registry-authentication.md#admin-account)associado ao registro de contêiner do Azure que tem sua imagem de contêiner. O nome de usuário e a senha são obrigatórios para garantir que sua empresa tenha acesso ao registro. Para obter o nome de usuário do administrador e a senha, defina a propriedade **admin-enabled** como **True** usando a CLI (interface de linha de comando) do Azure. Opcionalmente, você pode definir **Usuário administrador** como **Habilitar** no portal do Azure.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Ilustra a caixa de diálogo Atualizar registro de contêiner.":::

#### <a name="call-out-description"></a>Descrição de chamada

1. Usuário administrador

<br>**Senha para o Registro de Contêiner do Azure**: forneça a senha para o nome de usuário do administrador que está associado ao Registro de Contêiner do Azure e tem sua imagem de contêiner. O nome de usuário e a senha são obrigatórios para garantir que sua empresa tenha acesso ao Registro. Você pode obter a senha do portal do Azure acessando **Registro de Contêiner** > **Chaves de Acesso** ou com a CLI do Azure usando o [comando mostrar](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Ilustra a tela de chave de acesso na portal do Azure.":::

#### <a name="call-out-descriptions"></a>Descrições de chamada

1. Chaves de acesso
2. Nome de Usuário
3. Senha

**Nome do repositório no Registro de Contêiner do Azure**. Forneça o nome do repositório do Registro de Contêiner do Azure que tem sua imagem. Você especifica o nome do repositório quando envia a imagem por push para o registro. Você pode encontrar o nome do repositório acessando a página [Registro de Contêiner](https://azure.microsoft.com/services/container-registry/) > **Repositórios**. Para obter mais informações, confira [Exibir repositórios de registro de contêiner no portal do Azure](../../container-registry/container-registry-repositories.md). Depois de definir o nome, ele não poderá mais ser alterado. Use um nome exclusivo para cada oferta presente em sua conta.

> [!NOTE]
> Não há suporte para o registro de contêiner do Azure criptografado para certificação de módulo do Edge. O registro de contêiner do Azure deve ser criado sem criptografia habilitada.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Tags de imagem para novas versões da sua oferta

Os clientes devem ser capazes de obter as atualizações automaticamente do Azure Marketplace quando você as publicar. Caso não queiram fazer atualizações, eles deverão ser capazes de continuar usando uma versão específica da imagem. Você pode fazer isso adicionando novas marcas de imagem sempre que fizer uma atualização da imagem.

**Marca de imagem**. Esse campo deve incluir uma marca de **mais recente** que aponta para a última versão da sua imagem em todas as plataformas compatíveis. Ela também deve incluir uma tag de versão (por exemplo, começando com xx.xx.xx, em que xx é um número). Os clientes devem usar [tags de manifesto](https://github.com/estesp/manifest-tool) para segmentar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las. Todas as marcas de manifesto (exceto a última marca) devem começar com X.Y ou X.Y.Z, em que X, Y e Z são inteiros. Por exemplo, se uma tag mais recente aponta para 1.0.1-linux-x64, 1.0.1-linux-arm32 e 1.0.1-windows-arm32, essas seis marcas precisarão ser adicionadas a esse campo. Para obter detalhes sobre marcas e controle de versão, confira [Preparar os ativos técnicos do módulo do IoT Edge.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Configurações de implantação padrão (opcional)

Defina as configurações mais comuns para implantar seu módulo do IoT Edge. Otimize as implantações de clientes, permitindo que eles iniciem o módulo do IoT Edge pronto para uso com essas configurações padrão.

**Rotas padrão**. O Hub do IoT Edge gerencia a comunicação entre os módulos, o Hub IoT e os dispositivos. Você pode definir rotas para entrada e saída de dados entre módulos e o Hub IoT, o que oferece a você a flexibilidade necessária para enviar mensagens aos destinos corretos sem necessidade de escrever código adicional nem de usar serviços adicionais para processar as mensagens. As rotas são construídas usando pares nome/valor. Você pode definir até cinco nomes de rota padrão, cada um com até 512 caracteres de comprimento.

Certifique-se de usar a [sintaxe de rota](../../iot-edge/module-composition.md#declare-routes)correta) em seu valor de rota (geralmente definido como from/Message/* em $upstream). Isso significa que todas as mensagens enviadas por quaisquer módulos vão para o Hub IoT. Para referir-se ao módulo, use o nome do módulo padrão que será o **Nome da Oferta** sem espaços nem caracteres especiais. Para referir-se a outros módulos ainda não conhecidos, use a convenção <FROM_MODULE_NAME> para que os clientes saibam que precisam atualizar essas informações. Para obter detalhes sobre IoT Edge rotas, consulte [declarar rotas](../../iot-edge/module-composition.md#declare-routes)).

Por exemplo, se o módulo ContosoModule escuta entradas em ContosoInput e gera saída com os dados em ContosoOutput, é recomendável definir as duas rotas padrão a seguir:

- Nome n.° 1: ToContosoModule
- Valor n.° 1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nome n.° 2: FromContosoModuleToCloud
- Valor n.° 2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Propriedades desejadas do módulo gêmeo padrão**. Um módulo gêmeo é um documento JSON no Hub IoT que armazena as informações de estado para uma instância de módulo, incluindo as propriedades desejadas. As propriedades desejadas são usadas junto com as propriedades relatadas para sincronizar a configuração ou as condições do módulo. O back-end da solução pode definir as propriedades desejadas e o aplicativo de módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são criadas usando até cinco pares de nome/valor e cada valor padrão deve ter menos de 512 caracteres. É possível definir até cinco propriedades desejadas de nome/valor do gêmeo. Os valores das propriedades desejadas do gêmeo devem ser válidos como JSON, sem escape, sem matrizes e com uma hierarquia aninhada máxima de quatro níveis. Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), você pode adicionar um parâmetro como o valor padrão. Para saber mais sobre as propriedades desejadas de entrelaçamento, confira [definir ou atualizar as propriedades desejadas](../../iot-edge/module-composition.md#define-or-update-desired-properties)).

Por exemplo, se um módulo der suporte a uma taxa de atualização configurável dinamicamente usando as propriedades desejadas do gêmeo, é recomendável definir a seguinte propriedade desejada do gêmeo padrão:

- Nome n.° 1: RefreshRate
- Valor n.° 1: 60

**Variáveis de ambiente padrão**. Variáveis de ambiente fornecem informações suplementares a um módulo que ajuda no processo de configuração. As variáveis de ambiente são criadas usando pares de nome/valor. Cada nome e valor da variável de ambiente padrão deve ter menos de 512 caracteres e você pode definir até cinco. Em um cenário em que um parâmetro necessário para um valor padrão não faz sentido (por exemplo, o endereço IP do servidor de um cliente), você pode adicionar um parâmetro como o valor padrão.

Por exemplo, se um módulo precisar aceitar termos de uso antes de ser iniciado, será possível definir a seguinte variável de ambiente:

- Nome n.° 1: ACCEPT_EULA
- Valor n.° 1: S

**Opções de criação de contêiner padrão**. As opções de criação de contêiner direcionam a criação do contêiner do Docker do módulo do IoT Edge. O IoT Edge é compatível com as opções de criação de contêiner de API do mecanismo do Docker. Veja todas as opções em [Listar os contêineres.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) O campo de opções de criação deve ser JSON válido, sem escape e menos de 512 caracteres.

Por exemplo, se um módulo exigir associação a uma porta, defina as seguintes opções de criação:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Examinar e publicar

Depois de concluir todas as seções necessárias da oferta, você pode enviá-las para revisão e publicação.

No canto superior direito do portal, selecione **Examinar e publicar**.

Na página de revisão, você pode ver o status de publicação:

- Ver o status de conclusão de cada seção da oferta. Você não conseguirá publicar até que todas as seções da oferta estejam marcadas como concluídas.
    - **Não iniciada** – a seção não foi iniciada e precisa ser concluída.
    - **Incompleta** – a seção tem erros que precisam ser corrigidos ou requer que você forneça mais informações. Consulte as seções anteriores deste documento para obter orientações.
    - **Concluída** – a seção tem todos os dados necessários e não há erros. Todas as seções da oferta precisam ser concluídas para que você envie a oferta.
- Forneça as instruções de teste à equipe de certificação para garantir que sua oferta seja testada corretamente. Também forneça observações suplementares que ajudem a entender a sua oferta.

Para enviar a oferta para publicação, selecione **Publicar**.

Enviaremos um email para que você saiba quando uma versão prévia da oferta estiver disponível para revisão e aprovação. Para publicar sua oferta no público, vá para o Partner Center e selecione **Go-Live**.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no marketplace comercial](update-existing-offer.md)
