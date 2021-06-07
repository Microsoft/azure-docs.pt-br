---
title: Provedor de soluções integradas - Microsoft Commercial Marketplace
description: Saiba como vender suas ofertas por meio do canal de parceiros do programa CSP (Provedor de Soluções de Nuvem) da Microsoft no marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131166"
---
# <a name="cloud-solution-provider-program"></a>Programa do provedor de soluções de nuvem

Este artigo explica como configurar sua oferta para estar disponível para o programa CSP (Provedor de Soluções de Nuvem). Além de publicar suas ofertas por meio de [lojas online do Marketplace comercial](overview.md#commercial-marketplace-online-stores), você também pode vender por meio do programa CSP para alcançar milhões de clientes qualificados da Microsoft que o programa atende.

Você pode configurar ofertas novas ou existentes para disponibilidade no programa CSP em uma base opcional, o que permite que os parceiros CSP vendam seus produtos e criem soluções agrupadas para os clientes.

Os editores são responsáveis por dar suporte à interrupção/reparo para os clientes finais e por fornecer um mecanismo para parceiros no programa CSP e/ou clientes para entrar em contato com você para obter suporte. A melhor prática é fornecer aos parceiros do programa CSP a documentação do usuário, o treinamento e as notificações de integridade/interrupção do serviço (conforme aplicável) para que os parceiros no programa CSP estejam equipados para lidar com as solicitações de suporte da camada 1 dos clientes.  

As ofertas a seguir estão qualificadas para aceitação para serem vendidas por parceiros no programa CSP:

- Ofertas de transação de software como serviço (SaaS)
- Máquinas Virtuais (VMs)
- Modelos de Solução
- Aplicativos gerenciados

> [!NOTE]
> Os planos de VM de contêineres e BYOL (traga sua própria licença) são aceitos para serem vendidos por parceiros no programa CSP por padrão.

## <a name="how-to-configure-an-offer"></a>Como configurar uma oferta

Configure a configuração de aceitação do programa CSP ao criar a oferta no Partner Center. [Saiba mais sobre mudança na experiência do editor](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Aceitação do Partner Center

A experiência de aceitação está localizada no módulo público do revendedor CSP:

![Audiência do revendedor do CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Escolha entre três opções:

1. Qualquer parceiro no programa CSP.
2. Parceiros específicos no programa CSP que seleciono.
3. Nenhum parceiro no programa CSP.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Opção 1: qualquer parceiro no programa CSP

![Qualquer parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ao escolher essa opção, todos os parceiros no programa CSP estão qualificados para revender sua oferta para os clientes.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Opção 2: parceiros específicos no programa CSP que seleciono

![Parceiros específicos no programa CSP que eu selecionei](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ao escolher essa opção, você autoriza quais parceiros no programa CSP estão qualificados para revender sua oferta.

Para autorizar parceiros, selecione **selecionar parceiros do CSP** e um menu aparece, que permite pesquisar por nome do parceiro ou ID do locatário do Azure AD (Azure Active Directory do CSP).

![Selecione o menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Você pode aplicar filtros de pesquisa como **País**, **Competência** ou **Habilidade**.

![Filtros de país/região, competência e habilidade para pesquisa de parceiros](media/marketplace-publishers-guide/csp-add-resellers.png)

Depois de escolher a lista de parceiros, selecione **Adicionar**.

![Exemplo de lista dos parceiros autorizados no programa CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Uma tabela que mostra a lista de parceiros selecionados é exibida na página de audiência do Revendedor do CSP.

![Tabela com a lista de parceiros na página de audiência do Revendedor do CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selecione **Salvar rascunho** para registrar suas alterações.

Se essa oferta não for publicada, será necessário publicar sua oferta para disponibilizá-la para os parceiros selecionados.

>[!NOTE]
>Se você autorizar um parceiro no programa CSP em determinada região, ele poderá vender a oferta para qualquer cliente que pertença a essa região específica. Para obter mais informações sobre como as ofertas do CSP são classificadas em regiões, consulte [nuvem, mercados regionais e moeda](/partner-center/regional-authorization-overview).

Se você estiver atualizando a lista de CSP de uma oferta já publicada, adicione os parceiros adicionais e selecione **Sincronizar audiência do CSP**.

Se você tiver uma oferta que já tenha uma lista de parceiros autorizados e quiser usar a mesma lista para outra oferta, use **Importação/Exportação**. Navegue até a oferta que tem a lista de CSP e selecione **Exportar CSPs**. A função desenvolve um arquivo .csv que pode ser importado para outra oferta.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Opção 3: nenhum parceiro no programa CSP

![Nenhum parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ao escolher essa opção, você está recusando a oferta do programa CSP. Você pode alterar essa seleção a qualquer momento.

## <a name="deauthorize-partners-in-the-csp-program"></a>Desautorizar parceiros no programa CSP

Se você tiver autorizado um parceiro no programa CSP e esse parceiro já tiver revendido o produto aos clientes, você não terá permissão para desautorizar esse parceiro.

Se um parceiro no programa CSP não tiver vendido seu produto aos clientes e você quiser remover o CSP após a sua oferta ter sido publicada, use as seguintes instruções:

1. Acesse a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente.

   > [!NOTE]
   > Não altere as seleções dos menus suspensos preenchidas previamente.

2. Para **Selecione a versão do produto**, escolha **Gerenciamento de ofertas em tempo real**.
3. Para **Selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva a questão**, escolha **Atualizar oferta existente**.
5. Selecione **Próximo** para ser direcionado para a **página de detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use **Desautorizar CSP** como o título do problema e preencha o restante das seções obrigatórias.

## <a name="navigate-between-options"></a>Navegar entre opções

Use esta seção para navegar entre as três opções de revendedor do CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navegar na Opção um: Qualquer parceiro no programa CSP

Se, no momento, sua oferta está na **Opção 1: Qualquer parceiro no programa CSP** e você gostaria de navegar para uma das outras duas opções, use as instruções a seguir para criar uma solicitação:

1. Acesse a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente.

   > [!NOTE]
   > Não altere as seleções dos menus suspensos preenchidas previamente.

2. Para **Selecione a versão do produto**, escolha **Gerenciamento de ofertas em tempo real**.
3. Para **Selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva a questão**, escolha **Atualizar oferta existente**.
5. Selecione **Próximo** para ser direcionado para a **página de detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use **Desautorizar CSP** como o título do problema e preencha o restante das seções obrigatórias.

> [!NOTE]
> Se você está tentando navegar para a opção dois e um parceiro no programa CSP já revendeu a oferta aos clientes, esse parceiro já está na lista de parceiros autorizados, por padrão.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navegar na Opção dois: Parceiros específicos no programa CSP que eu selecionei

Se, no momento, sua oferta está na **Opção 2: Parceiros específicos no programa CSP que eu selecionei** e você gostaria de navegar para a **Opção um: Qualquer parceiro no programa CSP**, use as instruções a seguir para criar uma solicitação:

1. Acesse a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente.

   > [!NOTE]
   > Não altere as seleções dos menus suspensos preenchidas previamente.

2. Para **Selecione a versão do produto**, escolha **Gerenciamento de ofertas em tempo real**.
3. Para **Selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva a questão**, escolha **Atualizar oferta existente**.
5. Selecione **Próximo** para ser direcionado para a **página de detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use **Desautorizar CSP** como o título do problema e preencha o restante das seções obrigatórias.

 Se, no momento, sua oferta está na **Opção 2: Parceiros específicos no programa CSP que eu selecionei** e você gostaria de navegar para a **Opção 3: Nenhum parceiro no programa CSP**, você só pode navegar para essa opção se os parceiros no programa CSP que você autorizou anteriormente não revenderam sua oferta aos clientes finais. Use as instruções a seguir para criar uma solicitação:

1. Acesse a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente.

   > [!NOTE]
   > Não altere as seleções dos menus suspensos preenchidas previamente.

2. Para **Selecione a versão do produto**, escolha **Gerenciamento de ofertas em tempo real**.
3. Para **Selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreva a questão**, escolha **Atualizar oferta existente**.
5. Selecione **Próximo** para ser direcionado para a **página de detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use **Desautorizar CSP** como o título do problema e preencha o restante das seções obrigatórias.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navegar na Opção 3: Nenhum parceiro no programa CSP

Se, no momento, sua oferta está na **Opção 3: Nenhum parceiro no programa CSP**, você pode navegar para uma das outras duas opções a qualquer momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Compartilhamento de materiais de vendas e suporte com parceiros no programa CSP

Para ajudar os parceiros no programa do provedor de soluções na nuvem a representar a sua oferta de maneira mais eficaz e se envolver com sua organização, você deve enviar materiais de vendas e de suporte que estarão disponíveis para os revendedores. Esses recursos não serão expostos aos clientes nas lojas online.

### <a name="partner-center-csp-channel"></a>Canal do CSP no Partner Center

Se você tiver optado pelo canal CSP no Partner Center, os editores deverão inserir uma URL que hospede materiais de marketing relevantes e informações de contato do canal no módulo listagem de oferta.

![Informações sobre o material de apoio do CSP no Partner Center](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [serviços de entrada no mercado](https://partner.microsoft.com/reach-customers/gtm).
- Entre no [Central de Parceiros](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou configurar sua oferta.