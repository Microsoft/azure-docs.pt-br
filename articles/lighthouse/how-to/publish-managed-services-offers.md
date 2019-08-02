---
title: Publicar uma oferta de serviços gerenciados no Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerenciado que integre os clientes ao gerenciamento de recursos delegados do Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810830"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicar uma oferta de serviços gerenciados no Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta pública ou privada de serviços gerenciados no [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o [Portal do Microsoft Cloud Partner](https://cloudpartner.azure.com/), o que permite que um cliente que compre a oferta seja integrado no gerenciamento de recursos delegados do Azure. 

> [!NOTE]
> Você precisa ter uma [conta válida no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para criar e publicar essas ofertas. Se você ainda não tiver uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) o conduzirá pelas etapas de criação de uma conta no Partner Center e de registro no programa comercial do Marketplace. Sua ID do MPN (Microsoft Partner Network) será [associada automaticamente](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) às ofertas que você publicar para controlar seu impacto na participação dos clientes.
>
> Se não quiser publicar uma oferta no Azure Marketplace, você poderá integrar os clientes manualmente usando modelos do Azure Resource Manager. Para saber mais, confira [Integrar um cliente no gerenciamento de recursos delegados do Azure](onboard-customer.md).

A publicação de uma oferta de Serviços Gerenciados é semelhante à publicação de qualquer outro tipo de oferta no Azure Marketplace. Para saber mais sobre esse processo, confira o [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [Gerenciar ofertas do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> Cada plano em uma oferta de serviços gerenciados inclui uma seção **Detalhes do Manifesto**, na qual você define as entidades do Azure AD (Azure Active Directory) em seu locatário que terão acesso aos grupos de recursos delegados e/ou às assinaturas para os clientes que comprarem o plano. É importante estar ciente de que qualquer grupo (ou entidade de serviço ou usuário) que você incluir aqui terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes ao trabalho com cada cliente, você precisará publicar um plano privado separado que seja exclusivo de cada cliente.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Criar sua oferta no Portal do Cloud Partner

1. Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2. No menu de navegação à esquerda, selecione **Nova oferta** e selecione **Serviços gerenciados**.
3. Você verá uma seção **Editor** para a oferta com quatro partes a serem preenchidas: **Configurações da Oferta**, **Planos**, **Marketplace** e **Suporte**. Continue lendo para obter diretrizes sobre como concluir essas seções.

## <a name="enter-offer-settings"></a>Inserir configurações da oferta

Na seção **Configurações da oferta**, forneça o seguinte:

|Campo  |DESCRIÇÃO  |
|---------|---------|
|**ID da oferta**     | Um identificador exclusivo para a oferta (dentro do seu perfil de fornecedor). Essa ID pode conter apenas caracteres alfanuméricos em minúsculas, traços e sublinhados, com no máximo 50 caracteres. Tenha em mente que a ID da oferta pode ficar visível para clientes em locais como URLs de produtos e relatórios de cobrança. Depois de publicar a oferta, você não poderá alterar esse valor.        |
|**ID do Editor**     | A ID do fornecedor que será associada à oferta. Se você tem mais de uma ID de fornecedor, pode selecionar aquela que deseja usar para essa oferta.       |
|**Nome**     | O nome da oferta (com até 50 caracteres) que os clientes verão no Azure Marketplace e no portal do Azure. Use um nome de marca reconhecível que os clientes compreendam; se você estiver promovendo essa oferta em seu próprio site, use exatamente o mesmo nome aqui.        |

Quando terminar, selecione **Salvar**. Agora você está pronto para passar para a seção **Planos**.

## <a name="create-plans"></a>Criar planos

Cada oferta deve ter um ou mais planos (às vezes chamados de SKUs). Você pode adicionar vários planos para dar suporte a diferentes conjuntos de recursos com preços diferentes ou para personalizar um plano específico para um público seleto de clientes específicos. Os clientes podem ver os planos disponíveis para eles na oferta pai.

Na seção Planos, selecione **Novo Plano** para cada plano que quiser criar. Em seguida, insira uma **ID de Plano**. Essa ID pode conter apenas caracteres alfanuméricos em minúsculas, traços e sublinhados, com no máximo 50 caracteres. A ID do plano pode ficar visível para clientes em locais como URLs de produtos e relatórios de cobrança. Depois de publicar a oferta, você não poderá alterar esse valor.

Em seguida, preencha as seções na seguinte seção **Detalhes do Plano**:

|Campo  |DESCRIÇÃO  |
|---------|---------|
|**Título**     | Nome amigável do plano para exibição. Comprimento máximo de 50 caracteres.        |
|**Resumo**     | Descrição sucinta do plano para exibição abaixo do título. Comprimento máximo de 100 caracteres.        |
|**Descrição**     | Texto de descrição que fornece uma explicação mais detalhada do plano.         |
|**Modelo de cobrança**     | Há dois modelos de cobrança mostrados aqui, mas você deve escolher **Traga sua própria licença** para ofertas de serviços gerenciados. Isso significa que você cobrará de seus clientes diretamente os custos relacionados a essa oferta, e a Microsoft não cobrará nenhum valor por você.   |
|**Trata-se de um plano privado?**     | Indica se a SKU é pública ou privada. O padrão é **Não** (público). Se você deixar essa seleção, seu plano não será restrito a clientes específicos (ou a determinado número de clientes); depois de publicar um plano público, você não poderá alterá-lo posteriormente para privado. Para disponibilizar esse plano somente para clientes específicos, selecione **Sim**. Ao fazer isso, você precisará identificar os clientes fornecendo suas IDs de assinatura. Elas podem ser inseridas uma a uma (até 10 assinaturas) ou pelo upload de um arquivo .csv (até 20.000 assinaturas). Inclua suas próprias assinaturas aqui para que você possa testar e validar a oferta. Para saber mais, confira [SKUs Privados e Planos](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Por fim, preencha a seção **Detalhes do Manifesto**. Isso criará um manifesto com informações de autorização para gerenciar recursos do cliente. As informações fornecidas aqui são necessárias para integrar seus clientes no gerenciamento de recursos delegados do Azure. Conforme observado acima, essas permissões serão aplicadas a todos os clientes que comprarem o plano e, portanto, se você quiser limitar o acesso a um cliente específico, precisará publicar um plano privado para seu uso exclusivo.

- Primeiro, forneça uma **Versão** para o manifesto. Use o formato *n.n.n* (por exemplo, 1.2.5).
- Em seguida, insira sua **ID de Locatário**. Ela é um GUID associado à ID de locatário do Azure Active Directory da sua organização (ou seja, o locatário no qual você trabalhará para gerenciar os recursos de seus clientes). Se você não tiver isso à mão, poderá encontrá-lo passando o mouse sobre o nome da conta no lado superior direito do portal do Azure ou selecionando o **Mudar diretório**. 
- Por fim, adicione uma ou mais entradas de **Autorização** ao plano. As autorizações definem as entidades que podem acessar recursos e assinaturas de clientes que compram o plano. Você deve fornecer essas informações para acessar recursos em nome do cliente usando o gerenciamento de recursos delegados do Azure.
  Para cada autorização, forneça o que é pedido a seguir. Em seguida, você pode selecionar **Nova autorização** quantas vezes forem necessárias para adicionar mais definições de usuários/funções.
  - **ID do Objeto do Azure AD**: o identificador Azure AD de um usuário, grupo de usuários ou aplicativo que receberá determinadas permissões (conforme descrito pela definição de função) relativas aos recursos dos clientes.
  - **Nome de exibição do objeto do Azure AD**: um nome amigável para ajudar o cliente a entender a finalidade da autorização. O cliente verá esse nome ao delegar recursos.
  - **Definição de Função**: Selecione uma das funções internas do Azure AD disponíveis na lista. Essa função determinará as permissões que o usuário no campo **ID do Objeto do Azure AD** terá nos recursos dos clientes. Para saber mais sobre essas funções, confira [Funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Funções atribuíveis**: se você selecionou Administrador de Acesso de Usuário na **Definição de Função** para essa autorização, poderá adicionar uma ou mais funções atribuíveis aqui. O usuário no campo **ID de Objeto do Azure AD** poderá atribuir essas **Funções Atribuíveis** a [identidades gerenciadas](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Observe que nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário. (Se você não selecionou Administrador de Acesso do Usuário para a Definição de Função desse usuário, o campo não terá efeito.)

> [!TIP]
> Na maioria dos casos, é melhor atribuir permissões a um grupo de usuários ou entidade de serviço do Azure AD, em vez de a uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados.

Quando terminar de adicionar planos, selecione **Salvar** e continue na seção **Marketplace**.

## <a name="provide-marketplace-text-and-images"></a>Fornecer texto e imagens ao Marketplace

A seção **Marketplace** é onde você fornece o texto e as imagens que os clientes verão no Azure Marketplace e no portal do Azure.

Forneça informações para os seguintes campos na seção **Visão geral**:

|Campo  |DESCRIÇÃO  |
|---------|---------|
|**Título**     |  Título da oferta, geralmente o nome longo e formal. Este título será exibido com destaque no mercado. Comprimento máximo de 50 caracteres. Na maioria dos casos, ele deve ser igual ao **Nome** inserido na seção **Configurações da Oferta**.       |
|**Resumo**     | Breve propósito ou função da solução. Isso geralmente é exibido abaixo do título. Comprimento máximo de 100 caracteres.        |
|**Resumo longo**     | Um resumo mais longo da finalidade ou da função da oferta. Comprimento máximo de 256 caracteres.        |
|**Descrição**     | Mais informações sobre a oferta. É um campo com tamanho máximo de 3000 caracteres e dá suporte à formatação HTML simples.        |
|**Identificador de marketing**     | Um identificador amigável de URL exclusivo. Ele será usado nas URLs do Marketplace para essa oferta. Por exemplo, se a ID do editor for *contoso* e o identificador de marketing for *sampleApp*, a URL da oferta no Azure Marketplace será *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Visualizar códigos de inscrição**     | Adicione 1 a 100 identificadores de assinatura. Os clientes associados a essas assinaturas poderão exibir a oferta no Azure Marketplace antes de ser ativada. Sugerimos incluir suas próprias assinaturas aqui para que você possa visualizar como a oferta aparece no Azure Marketplace antes de disponibilizá-la para os clientes.  (As equipes de suporte e engenharia da Microsoft também poderão exibir a oferta durante esse período de versão prévia.)   |
|**Links úteis**     | URLs relacionadas à oferta, como documentação, notas de versão, perguntas frequentes, etc.        |
|**Categorias sugeridas (máximo 5)**     | Uma ou mais categorias (até cinco) que se aplicam à oferta. Essas categorias ajudam os clientes a descobrir a oferta no Azure Marketplace e no portal do Azure.        |

Na seção **Artefatos de Marketing**, você pode fazer upload de logotipos e outros ativos para serem mostrados com a oferta. Opcionalmente, você pode fazer upload de capturas de tela ou links para vídeos que podem ajudar os clientes a entender a oferta.

São necessários quatro tamanhos de logotipo: **pequeno (40 x 40)** , **médio (90 x 90)** , **grande (115 x 115)** e **largo (255 x 155)** . Siga estas diretrizes para seus logotipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
- As cores do tema do portal são branca e preta. Não use essas cores como a cor de fundo de seu logotipo. Use uma cor que destaque seu logotipo no portal. É recomendável usar cores primárias simples.
- Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não estão em branco, preto ou azul.
- A aparência de seu logotipo deve ser simples e evitar gradientes. Não use um fundo gradiente no logotipo.
- Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa.
- Verifique se o logotipo não está esticado.

O logotipo **Hero** (815 x 290) é opcional, mas recomendado. Se você incluir um logotipo Hero, siga estas diretrizes:

- Não inclua nenhum texto no logotipo Hero e lembre-se de deixar 415 pixels de espaço vazio no lado direito do logotipo. Isso é necessário para dar espaço a elementos de texto que serão inseridos programaticamente: o nome de exibição do editor, o título do plano, o resumo longo da oferta.
- A tela de fundo do logotipo Hero não pode estar em preto, branco ou transparente. Verifique se a cor do plano de fundo não é muito clara, pois o texto inserido será exibido em branco.
- Depois de publicar sua oferta com um ícone Hero, você não poderá removê-lo (embora seja possível atualizá-lo com uma versão diferente, se desejar).

Na seção **Gerenciamento de leads**, você poderá selecionar o sistema CRM no qual seus leads serão armazenados, se desejar. 

Por fim, forneça a **URL da Política de Privacidade** e os **Termos de Uso** na seção **Jurídico**. Você também pode especificar aqui se deseja ou não usar o [Contrato Standard](https://docs.microsoft.com/azure/marketplace/standard-contract) na oferta.

Lembre-se de salvar as alterações antes de passar para a seção **Suporte**.

## <a name="add-support-info"></a>Adicionar informações de suporte

Na seção **Suporte**, forneça o nome, o email e o número de telefone de um contato de engenharia e de um contato do suporte ao cliente. Você também precisará fornecer URLs de suporte. A Microsoft poderá usar essas informações quando precisar entrar em contato com você sobre problemas comerciais e de suporte.

Depois de adicionar essas informações, selecione **Salvar.**

## <a name="publish-your-offer"></a>Publicar sua oferta

Quando estiver satisfeito com todas as informações fornecidas, a próxima etapa será publicar a oferta no Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de ativação da oferta. Para saber mais sobre esse processo, confira [Publicar ofertas do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
