---
title: Como criar uma oferta do Dynamics 365 for Operations por meio do portal do Cloud Partner | Microsoft Docs
description: Como criar uma oferta do Dynamics 365 for Operations por meio do portal do Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776690"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Como criar uma oferta do Dynamics 365 for Operations por meio do portal do Cloud Partner

O portal de publicação fornece acesso baseado em função ao portal, permitindo que várias pessoas possam colaborar para a publicação de uma oferta. Veja [Gerenciar usuários do Cloud Portal](./cloud-partner-portal-manage-users.md) para obter mais informações.

Antes de publicar uma oferta em nome de uma conta de editor, uma das pessoas com função de \"proprietário\" precisa concordar em cumprir os [Termos de Uso](https://azure.microsoft.com/support/legal/website-terms-of-use/), a [Política de Privacidade da Microsoft](https://www.microsoft.com/privacystatement/default.aspx) e o [Contrato do Programa Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Como criar uma nova oferta do Dynamics 365 for Operations

Depois de atender a todos os pré-requisitos, você está pronto para começar a criar sua oferta do Dynamics 365 for Operations.

1. Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).
2. Na barra de navegação à esquerda, clique em \"+ Nova oferta\" e selecione \"Dynamics 365 for Operations\".
3. Uma exibição de \"Editor\" de nova oferta aparece agora para você e estamos prontos para começar a criar.
4. Os \"formulários\" que precisam ser preenchidos estão visíveis à esquerda na exibição de \"Editor\". Cada \"formulário\" consiste em um conjunto de campos que devem ser preenchidos. Os campos obrigatórios estão marcados com um asterisco vermelho (\*).

Há quatro formas principais de criar uma oferta do Dynamics 365 for Operations:

- Configurações da oferta
- Informações técnicas
- Detalhes da vitrine
- Contatos

## <a name="how-to-fill-out-the-offer-settings-form"></a>Como preencher o formulário de Configuração da oferta

O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta. Os diferentes campos são descritos abaixo.

### <a name="offer-id"></a>ID da oferta

Esse é um identificador exclusivo para a oferta em um perfil de fornecedor. Essa ID estará visível em URLs do produto. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada.

Por exemplo, se um publicador de contoso publisher cria uma oferta com o ID da oferta *dynamics365 de amostra para operações*, ele será exibido no AppSource como `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`.

### <a name="publisher-id"></a>ID do fornecedor

Este menu suspenso permite que você escolha o perfil de fornecedor em que deseja publicar essa oferta. Esse campo é bloqueado quando uma oferta é ativada.

NOME

Nome de exibição da oferta. Esse é o nome que será exibido no [AppSource](https://appsource.microsoft.com). Ele pode ter um máximo de 50 caracteres.

Clique em \"Salvar\" para salvar o progresso. A próxima etapa seria preencher as informações técnicas da oferta.

## <a name="how-to-fill-out-the-technical-info-form"></a>Como preencher o formulário de informações técnicas

O formulário de informações técnicas contém informações que serão exibidas na página da oferta. Instruções para os diferentes campos são descritas abaixo.

![Tela de nova oferta](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identificador da solução

O primeiro item é o Identificador da solução.

1. Para localizar esse identificador, vá para os Serviços de ciclo de vida e selecione Gerenciamento de soluções.
2. Depois de escolher a solução apropriada, você verá o Identificador da solução na Visão geral do pacote. \*\*Se o identificador estiver em branco, selecione editar e republique seu pacote para que o Identificador da solução apareça.

### <a name="validation-assets"></a>Ativos de validação

Carregue seus CAR (relatórios de análise de personalização) aqui.

### <a name="does-solution-enable-translations"></a>A solução permite traduções?

Selecione \'Sim\' ou \'Não\'.

### <a name="does-solution-include-localizations"></a>A solução inclui localizações?

Selecione \'Sim\' ou \'Não\'.

### <a name="product-version"></a>Versão do produto

Selecione Novo AX. Por fim, clique em salvar.

## <a name="how-to-fill-out-storefront-details-form"></a>Como preencher o formulário de Detalhes da vitrine eletrônica.

O primeiro item são os Detalhes da oferta.

1. **Resumo da oferta**

    \- Insira um breve resumo da solução (máximo de 100 caracteres).

2. **Descrição da oferta**

    \- Insira uma breve descrição da solução. A descrição deve ter o volume funcional da sua solução e deve alinhar-se diretamente com a biblioteca do BPM. Por exemplo, se você disser que tem os recursos x, y e z no conteúdo de marketing, durante a revisão final, nós asseguraremos que eles estejam documentados na biblioteca do BPM dentro do LCS.

![tela de detalhes da vitrine](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Detalhes da listagem

![Tela de detalhes da vitrine](./media/publish_d365_new_offer/storefront_details.png)

1. **Setores** – marque no máximo dois setores entre as opções especificadas.
2. **Categorias** – marque no máximo três categorias entre as opções especificadas.
3. **Tipo de aplicativo** – selecione entre as opções especificadas.
4. **Link de ajuda para seu aplicativo** – insira o link de ajuda para a solução.
5. **Países/regiões compatíveis** – marque entre as opções fornecidas.
6. **Idiomas compatíveis** – marque entre as opções fornecidas.
7. **Versão do aplicativo** – insira a versão da solução que está sendo lançada. (por exemplo, 1.0.0.0)
8. **Data de lançamento do aplicativo** – insira a data de lançamento da solução (mm/dd/aaaa).
9. **Produtos com os quais o aplicativo funciona** – produtos específicos de lista com os quais o aplicativo funciona. Você pode listar no máximo três produtos. Para listar um produto, clique no sinal de mais (ao lado do novo) e será criado um novo campo de texto em aberto para que você insira o nome de um produto com o qual o aplicativo funciona.
10. **Pesquisar palavras-chave** – insira termos comuns que os usuários podem usar para localizar a solução durante uma pesquisa. \*\*Essas palavras-chave não serão exibidas no Marketplace.
11. **Chave de ocultação** – essa é uma chave que será combinada com a URL de visualização da oferta para ocultá-la da exibição pública. Ela não é uma senha. Você pode inserir qualquer cadeia de caracteres aqui.

### <a name="marketing-artifacts"></a>Artefatos de marketing

1. Em seguida, é a vez de carregar os **logotipos** e **capturas de tela**. \*\*Observe os tamanhos para cada upload e lembre-se de que todas as imagens devem estar no formato PNG.
2. **Vídeos de demonstração** – clique em \"+novo\". Carregue um vídeo de demonstração da solução (somente links do YouTube ou do Vimeo)\*\*. Observe que você deve carregar uma miniatura de tamanho especificado para fazer com que o vídeo apareça no ambiente de preparo.
3. **Documentos** – carregue quaisquer documentos relacionados à solução e lembre-se de inserir um nome para o documento.

### <a name="legal"></a>Legal

Essa informação será vinculada à sua Política de Privacidade e Termos de Uso. Insira a URL da Política de Privacidade da solução e seus Termos de Uso. \*\*O cliente será capaz de ver essas políticas no portal.

### <a name="customer-support"></a>Suporte ao cliente

A URL de suporte só será vista no portal pelos seus usuários.

### <a name="leads-management"></a>Gerenciamento de leads

Selecione um sistema CRM onde você lidera será armazenado. Selecione \"tabelas do Azure\" aqui se você tiver um dos seguintes sistemas CRM: Salesforce, Marketo, Microsoft Dynamics CRM. O sistema CRM que você seleciona aqui é onde escreveremos os detalhes dos usuários finais que experimentam seu aplicativo no AppSource (leads). Dependendo do sistema CRM que você selecionar, clique na URL correspondente abaixo para obter informações sobre como concluir o próximo conjunto de campos.

![Detalhes do gerenciamento de leads](./media/publish_d365_new_offer/leads.png)

1. Para a Tabela do Azure, consulte [aqui](https://aka.ms/leadsettingforazuretable)
2. Para o Dynamics CRM Online, consulte [aqui](https://aka.ms/leadsettingfordynamicscrm)
3. Para o Marketo, [aqui](https://aka.ms/leadsettingformarketo)
4. Para o Salesforce, consulte [aqui](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Como preencher o formulário de Contatos.

Essas informações serão usadas para a Microsoft e para o atendimento ao cliente. Insira o contato de engenharia e de atendimento ao cliente para a empresa, bem como a URL de suporte para a solução. Essas informações serão usadas como um ponto de contato único caso a Microsoft tenha uma pergunta sobre a solução, além de serem usadas para atendimento ao cliente.

![Tela de contatos da oferta](./media/publish_d365_new_offer/Contacts.png)
