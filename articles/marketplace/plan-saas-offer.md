---
title: Como planejar uma oferta de SaaS para o Marketplace comercial da Microsoft
description: Como planejar uma nova oferta de SaaS (software como serviço) para listagem ou venda no Microsoft AppSource, no Azure Marketplace ou no programa CSP (provedor de soluções na nuvem) usando o programa do Marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 714b7bccea9b1e0c18ad78a4d98ed3526a279f5f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879251"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Como planejar uma oferta de SaaS para o Marketplace comercial

Este artigo explica as diferentes opções e requisitos para a publicação de uma oferta de SaaS (software como serviço) para o Marketplace comercial da Microsoft. As ofertas de SaaS permitem entregar e licenciar soluções de software para seus clientes por meio de uma assinatura online em vez da instalação local em computadores individuais. Este artigo o ajudará a preparar sua oferta para publicação no Marketplace comercial com o Partner Center.

## <a name="listing-options"></a>Opções de listagem

Ao se preparar para publicar uma nova oferta de SaaS, você precisa decidir qual opção de _listagem_ escolher. A opção de listagem escolhida determina quais informações adicionais você precisará fornecer ao criar sua oferta no Partner Center. Você definirá sua opção de listagem na página  **instalação da oferta** , conforme explicado em [como criar uma oferta de SaaS no Marketplace comercial](create-new-saas-offer.md).

A tabela a seguir mostra as opções de listagem para as ofertas de SaaS no Marketplace comercial.

| Opção de listagem | Processo de transação |
| ------------ | ------------- |
| Entrar em contato comigo | O cliente entra em contato com você diretamente das informações na sua listagem.``*`` |
| Avaliação gratuita | O cliente é redirecionado para a URL de destino por meio do Azure Active Directory (Azure AD).``*`` |
| Obtenha agora (gratuito) | O cliente é redirecionado para a URL de destino por meio do Azure AD.``*`` |
| Vender por meio da Microsoft  | As ofertas vendidas pela Microsoft são chamadas de ofertas de _Transações_ . Uma oferta que é propagada é aquela na qual a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor. Cobramos ofertas de SaaS usando o modelo de preços que você escolher e gerenciar transações de clientes em seu nome. As tarifas de uso da infraestrutura do Azure são cobradas para você, o parceiro, diretamente. Você deve considerar os custos de infraestrutura em seu modelo de preços. Isso é explicado em mais detalhes na [cobrança de SaaS](#saas-billing) abaixo.  |
|||

``*`` Os Publicadores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros, ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção.

Para obter mais informações sobre essas opções de listagem, consulte [recursos de transação do Marketplace comercial](marketplace-commercial-transaction-capabilities-and-considerations.md).

Depois que sua oferta for publicada, a opção de listagem escolhida para sua oferta será exibida como um botão no canto superior esquerdo da página de listagem da sua oferta. Por exemplo, a captura de tela a seguir mostra uma página de listagem de oferta no Azure Marketplace com os botões **contate-me** e **Test Drive** .

![Ilustra uma listagem de oferta na loja online.](./media/listing-options.png)

## <a name="technical-requirements"></a>Requisitos técnicos

Os requisitos técnicos diferem de acordo com a opção de listagem escolhida para sua oferta.

A opção de listagem _entre em contato comigo_ não tem requisitos técnicos. Você tem a opção de conectar um sistema de gerenciamento de relacionamento com o cliente (CRM) para gerenciar clientes potenciais do cliente. Isso é descrito na seção [clientes potenciais do cliente](#customer-leads) , mais adiante neste artigo.

As opções de listagem _obter agora (gratuito)_, _avaliação gratuita_ e _vender por meio da Microsoft_ têm os seguintes requisitos técnicos:

- Seu aplicativo SaaS deve ser uma solução multilocatário.
- Você pode habilitar as contas da Microsoft (MSA) e o [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para autenticar usuários.
- Você deve criar uma página de aterrissagem. Depois que um usuário adquire sua oferta, ele é direcionado para a página de aterrissagem. Isso ajuda a concluir qualquer provisionamento ou configuração adicional necessária. Para obter orientação sobre como criar a página de aterrissagem, consulte estes artigos:
  - [Crie a página de aterrissagem para sua oferta de SaaS transactável no Marketplace comercial](azure-ad-transactable-saas-landing-page.md)
  - [Crie a página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação no Marketplace comercial](azure-ad-free-or-trial-landing-page.md)

Esses requisitos técnicos adicionais se aplicam apenas à opção de listagem _vender por meio da Microsoft_ (de transações):

- O Azure AD com autenticação e gerenciamento de identidade de SSO (logon único) é necessário para que o usuário de compra acesse a página de aterrissagem. Para obter diretrizes detalhadas, consulte [Azure AD e ofertas de SaaS transactáveis no Marketplace comercial](azure-ad-saas.md).
- Você deve usar as [APIs de preenchimento de SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md) para integrar com o Azure Marketplace e Microsoft AppSource. Você deve expor um serviço que possa interagir com a assinatura de SaaS para criar, atualizar e excluir uma conta de usuário e um plano de serviço. As alterações críticas na API devem ter suporte em até 24 horas. As alterações não críticas de API serão lançadas periodicamente. Os diagramas e explicações detalhadas que descrevem o uso dos campos coletados estão disponíveis na documentação para as [APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Você deve criar pelo menos um plano para sua oferta. Seu plano é cobrado com base no modelo de preços selecionado antes da publicação: _taxa fixa_ ou _por usuário_. Mais detalhes sobre os [planos](#plans) são fornecidos posteriormente neste artigo.
- O cliente pode cancelar sua oferta a qualquer momento.

### <a name="technical-information"></a>Informações técnicas

Se você estiver criando uma oferta de transação, precisará reunir as informações a seguir para a página de **configuração técnica** . Se você optar por processar as transações de forma independente em vez de criar uma oferta de transação, pule esta seção e vá para [test drives](#test-drives).

- **URL da página de aterrissagem**: a URL do site de SaaS (por exemplo: `https://contoso.com/signup` ) para a qual os usuários serão direcionados depois de adquirir sua oferta do Marketplace comercial, disparando o processo de configuração da assinatura SaaS recém-criada. Essa URL receberá um token que pode ser usado para chamar as APIs de preenchimento para obter detalhes de provisionamento para sua página de registro interativa.

  Essa URL será chamada com o parâmetro de token de identificação de compra do Marketplace que identifica exclusivamente a compra de SaaS do cliente específico. Você deve trocar esse token para os detalhes da assinatura SaaS correspondente usando a [API de resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Esses detalhes e quaisquer outros que você queira coletar devem ser usados como parte de uma página da Web interativa para o cliente, criada em sua experiência para concluir o registro do cliente e ativar sua compra. Nessa página, o usuário deve se inscrever através da autenticação com um clique usando Azure Active Directory (AD do Azure).

  Esta URL com o parâmetro de token de identificação de compra do Marketplace também será chamada quando o cliente iniciar uma experiência SaaS gerenciada do centro de administração portal do Azure ou M365. Você deve lidar com ambos os fluxos: quando o token é fornecido pela primeira vez após uma nova compra de cliente, e quando ele é fornecido novamente para um cliente existente que gerencia sua solução de SaaS.

    A página de aterrissagem configurada deve estar em funcionamento em execução 24/7. Essa é a única maneira de você ser notificado sobre novas compras de suas ofertas de SaaS feitas no Marketplace comercial ou solicitações de configuração para uma assinatura ativa de uma oferta.

- **Webhook de conexão**: para todos os eventos assíncronos que a Microsoft precisa enviar para você (por exemplo, quando uma assinatura de SaaS tiver sido cancelada), exigimos que você forneça uma URL de webhook de conexão. Chamaremos essa URL para notificá-lo sobre o evento.

  O webhook que você fornece deve estar em funcionamento em execução 24/7. Essa é a única maneira de você ser notificado sobre atualizações sobre as assinaturas de SaaS de seus clientes adquiridas por meio do Marketplace comercial.

  > [!NOTE]
  > Dentro do portal do Azure, exigimos que você crie um aplicativo de Azure Active Directory de locatário único [(AD do Azure)](../active-directory/develop/howto-create-service-principal-portal.md) para habilitar uma ID de Azure app a ser usada para autenticar a conexão entre nossos dois serviços. Para localizar a [ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), vá para o Azure Active Directory e selecione **Propriedades** e procure o número de ID de diretório listado. Por exemplo, `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID de locatário Azure Active Directory**: (também conhecido como ID de diretório). Dentro do portal do Azure, exigimos que você [registre um aplicativo Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) para que possamos adicioná-lo à ACL (lista de controle de acesso) da API para garantir que você está autorizado a chamá-lo. Para localizar a ID do locatário para seu aplicativo Azure Active Directory (AD), vá para a folha [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo. Em seguida, procure o número de **ID do diretório (locatário)** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **ID do aplicativo Azure Active Directory**: você também precisa da [ID do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Para obter seu valor, vá para a folha de [registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) em Azure Active Directory. Na coluna **nome de exibição** , selecione o aplicativo. Em seguida, procure o número de ID do aplicativo (cliente) listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

  A ID do aplicativo do Azure AD está associada à sua ID de editor em sua conta do Partner Center. Você deve usar a mesma ID de aplicativo para todas as ofertas nessa conta.

  > [!NOTE]
  > Se o Publicador tiver duas ou mais contas diferentes no Partner Center, duas ou mais IDs de aplicativo diferentes do Azure AD devem ser usadas, cada uma para uma das contas. Cada conta de parceiro no Partner Center deve usar uma ID de aplicativo do Azure AD exclusiva para todas as ofertas de SaaS que são publicadas por meio dessa conta.

## <a name="test-drives"></a>Test drives
Você pode optar por habilitar um test drive para seu aplicativo SaaS. As unidades de teste oferecem aos clientes acesso a um ambiente pré-configurado por um número fixo de horas. Você pode habilitar as unidades de teste para qualquer opção de publicação, no entanto, esse recurso tem requisitos adicionais. Para saber mais sobre as unidades de teste, consulte [o que é um test drive?](what-is-test-drive.md). Para obter informações sobre como configurar diferentes tipos de unidades de teste, consulte [testar a configuração técnica](test-drive-technical-configuration.md).

> [!TIP]
> Uma test drive é diferente de uma [avaliação gratuita](plans-pricing.md#free-trials). Você pode oferecer um test drive, uma avaliação gratuita ou ambos. Ambos fornecem aos seus clientes sua solução por um período de tempo fixo. No entanto, um test drive também inclui um tour interativo prático dos principais recursos do produto e dos benefícios demonstrados em um cenário de implementação do mundo real.

## <a name="customer-leads"></a>Clientes potenciais

Você deve conectar sua oferta ao seu sistema de gerenciamento de relacionamento com o cliente (CRM) para coletar informações do cliente. Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a loja online onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. O Marketplace comercial dá suporte a uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela do Azure ou configurar um ponto de extremidade HTTPS usando a automatização de energia.

Você pode adicionar ou modificar uma conexão do CRM a qualquer momento durante ou após a criação da oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Selecionando uma loja online

Quando você publica uma oferta de SaaS, ela será listada no Microsoft AppSource, no Azure Marketplace ou em ambos. Cada loja online atende aos requisitos exclusivos do cliente. O AppSource é para soluções de negócios e o Azure Marketplace é para soluções de ti. Seu tipo de oferta, recursos de transação e categorias determinarão onde sua oferta será publicada. Categorias e subcategorias são mapeadas para cada loja online com base no tipo de solução. 

Se sua oferta de SaaS *for uma solução de ti* (Azure Marketplace) e uma solução de negócios (AppSource), selecione uma categoria e uma subcategoria aplicáveis a cada loja online. As ofertas publicadas em lojas online devem ter uma proposta de valor como uma solução de ti *e* uma solução de negócios.

> [!IMPORTANT]
> As ofertas de SaaS com [cobrança limitada](partner-center-portal/saas-metered-billing.md) estão disponíveis por meio do Azure Marketplace e do portal do Azure. As ofertas de SaaS com apenas planos privados estão disponíveis por meio do portal do Azure.

| Cobrança limitada | Plano público | Plano particular | Disponível em: |
|---|---|---|---|
| Sim             | Sim         | Não           | Azure Marketplace e portal do Azure |
| Sim             | Sim         | Sim          | Azure Marketplace e portal do Azure * |
| Sim             | Não          | Sim          | Somente portal do Azure |
| Não              | Não          | Sim          | Somente portal do Azure |
|||||

&#42; o plano privado da oferta só estará disponível por meio do portal do Azure

Por exemplo, uma oferta com cobrança limitada e um plano privado somente (sem plano público) serão adquiridas pelos clientes na portal do Azure. Saiba mais sobre as [ofertas privadas no Microsoft Commercial Marketplace](private-offers.md).

Para obter informações detalhadas sobre as opções de listagem com suporte nas lojas online, consulte [lista e opções de preços por loja online](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Para obter mais informações sobre categorias e subcategorias, consulte [categorias e subcategorias no Marketplace comercial](categories.md).

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão que você pode usar para suas ofertas no mercado comercial. Quando você oferece seu software sob o contrato Standard, os clientes precisam apenas lê-lo e aceitá-lo uma vez e você não precisa criar termos e condições personalizados.

Se você optar por usar o contrato padrão, terá a opção de adicionar os termos de emenda universal e até 10 emendas personalizadas ao contrato Standard. Você também pode usar seus próprios termos e condições em vez do contrato padrão. Você gerenciará esses detalhes na página **Propriedades** . Para obter informações detalhadas, consulte [contrato padrão do Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão para o mercado comercial, você não pode usar seus próprios termos e condições personalizados. Esse é um cenário com duas possibilidades mutuamente exclusivas. Você pode oferecer sua solução sob o contrato Standard ou seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="offer-listing-details"></a>Detalhes da listagem de ofertas

Ao [criar uma nova oferta de SaaS](create-new-saas-offer.md) no Partner Center, você inserirá texto, imagens, vídeos opcionais e outros detalhes na página de **listagem da oferta** . Essas são as informações que os clientes verão quando descobrirem sua listagem de ofertas no Marketplace comercial, conforme mostrado no exemplo a seguir.

:::image type="content" source="./media/example-saas-1.png" alt-text="Ilustra como essa oferta aparece em Microsoft AppSource.":::

**Descrições de chamada**

1. Logotipo
2. Categorias
3. Setores
4. Endereço de suporte (link)
5. Termos de uso
6. Política de privacidade
7. Nome da oferta
8. Resumo
9. Descrição
10. Capturas de tela/vídeos
11. Documentos

O exemplo a seguir mostra uma listagem de oferta no portal do Azure.

![Ilustra uma listagem de oferta no portal do Azure.](./media/example-managed-service-azure-portal.png)

**Descrições de chamada**

1. Título
1. Descrição
1. Links úteis
1. Capturas de tela

> [!NOTE]
> O conteúdo de listagem da oferta não precisará estar em inglês se a descrição da oferta começar com a frase "este aplicativo está disponível somente em [idioma diferente do inglês]".

Para ajudar a criar sua oferta com mais facilidade, prepare alguns desses itens antecipadamente. Os itens a seguir são necessários, salvo indicação em contrário.

- **Nome**: esse nome será exibido como o título da sua listagem de ofertas no Marketplace comercial. Uma marca comercial pode ser atribuída ao nome. Ele não pode conter emojis (a menos que sejam os símbolos de marca registrada e direitos autorais) e precisam ser limitados a 50 caracteres.
- **Resumo dos resultados da pesquisa**: Descreva a finalidade ou a função de sua oferta como uma única frase sem quebras de linha em 100 caracteres ou menos. Esse resumo é usado nos resultados da pesquisa de listagem (s) do Marketplace comercial.
- **Descrição**: essa descrição será exibida na visão geral de listagem (s) do Marketplace comercial. Considere incluir uma proposta de valor, os principais benefícios, a base de usuários pretendida, qualquer associação de categoria ou do setor, oportunidades de compra no aplicativo, todas as divulgações necessárias e um link para saber mais.

    Essa caixa de texto tem controles de editor de texto avançado que você pode usar para tornar sua descrição mais atraente. Você também pode usar marcas HTML para formatar sua descrição. Você pode inserir até 3.000 caracteres de texto nesta caixa, incluindo marcação HTML. Para obter dicas adicionais, confira [Escrever uma ótima descrição do aplicativo](/windows/uwp/publish/write-a-great-app-description).

- **Introdução instruções**: se você optar por vender sua oferta por meio da Microsoft (oferta de transação), esse campo será necessário. Essas instruções ajudam os clientes a se conectarem à sua oferta de SaaS. Você pode adicionar até 3.000 caracteres de texto e links para uma documentação online mais detalhada.
- **Pesquisar palavras-chave** (opcional): forneça até três palavras-chave de pesquisa que os clientes podem usar para localizar sua oferta nas lojas online. Você não precisa incluir o **nome** e a **Descrição** da oferta: esse texto é incluído automaticamente na pesquisa.
- **Link de política de privacidade**: a URL para a política de privacidade da sua empresa. Você deve fornecer uma política de privacidade válida e é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade.
- **Informações de contato**: você deve fornecer os seguintes contatos da sua organização:
  - **Contato de suporte**: forneça o nome, o telefone e o email para que os parceiros da Microsoft usem quando seus clientes abrirem tíquetes. Você também deve incluir a URL para o site de suporte.
  - **Contato de engenharia**: forneça o nome, telefone e email para a Microsoft usar diretamente quando houver problemas com sua oferta. Essas informações de contato não estão listadas no Marketplace comercial.
  - **Contato do programa CSP** (opcional): forneça o nome, telefone e email se você aceitar o programa CSP, para que esses parceiros possam contatá-lo com qualquer dúvida. Você também pode incluir uma URL para seus materiais de marketing.
- **Links úteis** (opcional): você pode fornecer links para vários recursos para os usuários de sua oferta. Por exemplo, fóruns, perguntas frequentes e notas de versão.
- **Documentos de suporte**: você pode fornecer até três documentos voltados para o cliente, como White papers, folhetos, listas de verificação ou apresentações do PowerPoint.
- **Mídia – logotipos**: forneça um arquivo PNG para o logotipo **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

   - Grande (de 216 x 216 a 350 x 350 px, required)
   - Médio (90 x 90 px, opcional)
   - Pequeno (48 x 48 px, opcional)

  Esses logotipos são usados em locais diferentes nas lojas online:

  - O logotipo pequeno aparece nos resultados da pesquisa do Azure Marketplace e na página de Microsoft AppSource página principal e nos resultados da pesquisa.
  - O logotipo médio é exibido quando você cria um novo recurso no Microsoft Azure.
  - O logotipo grande aparece na sua página de listagem de ofertas no Azure Marketplace e Microsoft AppSource.

- **Mídia – capturas de tela**: você deve adicionar pelo menos uma e até cinco capturas de tela com os seguintes requisitos, que mostram como sua oferta funciona:
  - 1280 x 720 pixels
  - arquivo. png
  - Deve incluir uma legenda
- **Mídia-vídeos** (opcional): você pode adicionar até quatro vídeos com os seguintes requisitos, que demonstram sua oferta:
  - Nome
  - URL: deve ser hospedada somente no YouTube ou Vimeo.
  - Miniatura: 1280 x 720. arquivo PNG

> [!Note]
> Sua oferta deve atender as políticas gerais de [certificação do Marketplace comercial](/legal/marketplace/certification-policies#100-general) e as [políticas de software como serviço](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) a serem publicadas no mercado comercial.

## <a name="preview-audience"></a>Público-alvo de versão prévia
Um público de visualização pode acessar sua oferta antes de ser publicado em lojas online para testar a funcionalidade de ponta a ponta antes de você publicá-la em tempo real. Na página do **público de visualização** , você pode definir um público de visualização limitado. Essa configuração não estará disponível se você optar por processar as transações de forma independente, em vez de vender sua oferta pela Microsoft. Nesse caso, você pode ignorar esta seção e ir para [oportunidades de vendas adicionais](#additional-sales-opportunities).

> [!NOTE]
> Um público de visualização difere de um plano privado. Um plano privado é aquele que você disponibiliza apenas para um público específico que escolher. Isso permite que você negocie um plano personalizado com clientes específicos. Para obter mais informações, consulte a próxima seção: planos.

Você pode enviar convites para endereços de email da MSA (conta da Microsoft) ou do Azure Active Directory (Azure AD). Adicione até 10 endereços de email manualmente ou importe até 20 com um arquivo. csv. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar quaisquer alterações ou atualizações na sua oferta.

## <a name="plans"></a>Planos

As ofertas de transações exigem pelo menos um plano. Um plano define o escopo e os limites da solução e os preços associados. Você pode criar vários planos para sua oferta a fim de fornecer a seus clientes opções de preços e técnicas diferentes. Se você optar por processar as transações independentemente, em vez de criar uma oferta de transação, a página **planos** não estará visível. Nesse caso, pule esta seção e vá para [oportunidades de vendas adicionais](#additional-sales-opportunities).

Consulte [planos e preços para ofertas de mercado comercial](plans-pricing.md) para obter diretrizes gerais sobre planos, incluindo modelos de preços, avaliações gratuitas e planos privados. As seções a seguir discutem informações adicionais específicas para ofertas de SaaS.

### <a name="saas-pricing-models"></a>Modelos de preço do SaaS

As ofertas de SaaS podem usar um dos dois modelos de preços com cada plano: uma _taxa fixa_ ou _por usuário_. Todos os planos na mesma oferta devem ser associados ao mesmo modelo de preços. Por exemplo, uma oferta não pode ter um plano que seja uma taxa fixa e outro plano por usuário.

**Taxa fixa** – habilite o acesso à sua oferta com um único preço de taxa mensal ou anual. Isso é, às vezes, chamado de preço baseado em site. Com esse modelo de preços, opcionalmente, você pode definir planos medidos que usam a API do serviço de medição do Marketplace para cobrar os clientes pelo uso que não está coberto pela taxa fixa. Para obter mais informações sobre cobrança limitada, consulte [cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial](./partner-center-portal/saas-metered-billing.md). Você também deve usar essa opção se o comportamento de uso para o serviço SaaS estiver em intermitências.

**Por usuário** – habilite o acesso à sua oferta com um preço com base no número de usuários que podem acessar a oferta ou ocupar estações. Com esse modelo baseado em usuário, você pode definir o número mínimo e máximo de usuários com suporte no plano. Você pode criar vários planos para configurar diferentes pontos de preço com base no número de usuários. Esses campos são opcionais. Se deixado desmarcado, o número de usuários será interpretado como não tendo um limite (mínimo de 1 e máximo de quantos o serviço pode dar suporte). Esses campos podem ser editados como parte de uma atualização para seu plano.

> [!IMPORTANT]
> Depois que sua oferta for publicada, você não poderá alterar o modelo de preços. Além disso, todos os planos para a mesma oferta precisam compartilhar o mesmo modelo de preços.

### <a name="saas-billing"></a>Cobrança de SaaS

Para aplicativos SaaS que são executados em sua assinatura do Azure (do editor), o uso da infraestrutura é cobrado diretamente; Os clientes não veem as tarifas de uso da infraestrutura real. Você deve agrupar as taxas de uso de infraestrutura do Azure em seu preço de licença de software para compensar o custo da infraestrutura implantada para executar a solução.

As ofertas de aplicativo SaaS que são vendidas por meio da Microsoft dão suporte à cobrança mensal ou anual com base em uma taxa fixa, por usuário ou cobranças de consumo usando o [serviço de cobrança limitado](./partner-center-portal/saas-metered-billing.md). O Marketplace comercial opera em um modelo de agência, no qual os editores definem preços, a Microsoft Bills Customers e a Microsoft paga receita aos Publicadores ao mesmo tempo em que a agência é cobrada.

O exemplo a seguir mostra uma divisão de exemplo de custos e pagamentos para demonstrar o modelo de agência. Neste exemplo, a Microsoft cobra US$ 100,00 do cliente pela sua licença de software e paga US$ 80,00 ao editor.

| Custo de sua licença | US$ 100 por mês |
| ------------ | ------------- |
| Custo de uso do Azure (D1/1-Núcleo) | Cobrados diretamente para o publicador, não o cliente |
| O cliente é cobrado pela Microsoft | US$ 100,00 por mês (o editor precisa contabilizar os custos incorridos ou de infraestrutura de passagem no valor da licença) |
| **Faturas da Microsoft** | **US$ 100 por mês** |
| A Microsoft paga para você 80% do seu custo de licença<br>`*` Para aplicativos SaaS qualificados, a Microsoft paga 90% do seu custo de licença| US $80,00 por mês<br>``*`` US $90,00 por mês |
|||

**`*` Redução da taxa de serviço do Marketplace** – para determinadas ofertas de SaaS que você publicou no mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%. Para que suas ofertas sejam qualificadas, suas ofertas devem ter sido designadas pela Microsoft como o IP do Azure co-vender incentivados. A qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do fim de cada mês civil para receber a taxa de serviço do Marketplace reduzida. Depois que a qualificação for atendida, a taxa de serviço reduzida será concedida a todas as transações efetivas no primeiro dia do mês seguinte e continuará a ser aplicada até que o status de incentivados de covenda de IP do Azure seja perdido. Para obter detalhes sobre a qualificação de venda de IP, consulte [requisitos para o status de venda](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). A taxa reduzida de serviço do Marketplace também se aplica ao IP do Azure para venda de VMs incentivadoss, aplicativos gerenciados e qualquer outra oferta de IaaS pagamente qualificada disponibilizada por meio do Marketplace comercial.

## <a name="additional-sales-opportunities"></a>Oportunidades de vendas adicionais

Você pode optar por aceitar os canais de marketing e vendas com suporte da Microsoft. Ao criar sua oferta no Partner Center, você verá duas guias em direção ao final do processo:

- **Revenda por meio de CSPs**: Use esta opção para permitir que os parceiros de CSP (provedores de soluções) Microsoft Cloud revendam sua solução como parte de uma oferta agrupada. Para saber mais sobre esse programa, confira [programa do provedor de soluções na nuvem](cloud-solution-providers.md).

- **Covenda com a Microsoft**: essa opção permite que as equipes de vendas da Microsoft considerem sua solução qualificada de venda conjunta de IP ao avaliar as necessidades dos clientes. Para obter detalhes sobre a qualificação para venda conjunta, consulte [requisitos para o status de televenda](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Para obter informações detalhadas sobre como preparar sua oferta para avaliação, confira [a opção de venda conjunta no Partner Center](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma oferta de SaaS no Marketplace comercial](create-new-saas-offer.md)
- [Práticas recomendadas de listagem de ofertas](gtm-offer-listing-best-practices.md)