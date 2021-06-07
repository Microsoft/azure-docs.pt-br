---
title: Planejar uma oferta de serviço gerenciado para o Marketplace comercial da Microsoft
description: Como planejar uma nova oferta de serviço gerenciado para o Azure Marketplace usando o programa do Marketplace comercial no Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371933"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Como planejar uma oferta de serviço gerenciado para o Marketplace comercial da Microsoft

Este artigo apresenta os requisitos para a publicação de uma oferta de serviço gerenciado no Microsoft Commercial Marketplace por meio do Partner Center.

Os serviços gerenciados são ofertas do Azure Marketplace que habilitam o gerenciamento entre locatários e multilocatários com o Azure Lighthouse. Para saber mais, consulte [o que é o Azure Lighthouse?](../lighthouse/overview.md) Quando um cliente adquire uma oferta de serviço gerenciado, ele é capaz de delegar uma ou mais assinaturas ou grupo de recursos. Em seguida, você pode trabalhar com esses recursos usando os recursos de [Gerenciamento de recursos delegados](../lighthouse/concepts/azure-delegated-resource-management.md) do Azure do Azure Lighthouse.

## <a name="eligibility-requirements"></a>Requisitos de qualificação

Para publicar uma oferta de serviço gerenciado, você deve ter conquistado uma competência Gold ou prata da Microsoft na plataforma de nuvem. Essa competência demonstra sua experiência com os clientes. Para obter mais informações, consulte [competências de Microsoft Partner Network](https://partner.microsoft.com/membership/competencies).

As ofertas devem atender a todas as [políticas de certificação do Marketplace comercial](/legal/marketplace/certification-policies) aplicáveis a serem publicadas no Azure Marketplace.

## <a name="customer-leads"></a>Clientes potenciais

Você deve conectar sua oferta ao seu sistema de gerenciamento de relacionamento com o cliente (CRM) para coletar informações do cliente. Será solicitado ao cliente que forneça permissão para compartilhar as respectivas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a loja online onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. O Marketplace comercial dá suporte a diferentes tipos de sistemas de CRM, juntamente com a opção de usar uma tabela do Azure ou configurar um ponto de extremidade HTTPS usando a automatização de energia.

Você pode adicionar ou modificar uma conexão do CRM a qualquer momento durante ou após a criação da oferta. Para obter diretrizes detalhadas, confira [leads do cliente de sua oferta do Marketplace comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Contratos legais

Na página Propriedades do Partner Center, você será solicitado a fornecer os **termos e condições** para o uso de sua oferta. Você pode inserir seus termos diretamente no Partner Center ou fornecer a URL onde eles podem ser encontrados. Os clientes serão solicitados a aceitar esses termos e condições antes de adquirir sua oferta.

## <a name="offer-listing-details"></a>Detalhes da listagem de ofertas

Ao criar sua oferta de serviço gerenciado no Partner Center, você inserirá texto, imagens, documentos e outros detalhes da oferta. Isso é o que os clientes verão quando descobrirem sua oferta no Azure Marketplace. Consulte o seguinte exemplo:

:::image type="content" source="media/example-managed-service.png" alt-text="Ilustra como uma oferta de serviço gerenciado é exibida no Azure Marketplace.":::

**Descrições de chamada**

1. Logotipo
1. Nome
1. Descrição breve
1. Categorias
1. Contratos legais e política de privacidade
1. Descrição
1. Capturas de tela/vídeos
1. Links úteis

Veja um exemplo de como a listagem da oferta aparece no portal do Azure:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Ilustra como essa oferta aparece na portal do Azure.":::

**Descrições de chamada**

1. Nome
2. Descrição
3. Links úteis
4. Capturas de tela/vídeos

> [!NOTE]
> Se sua oferta estiver em um idioma diferente do inglês, a listagem da oferta poderá estar nesse idioma, mas a descrição deve começar ou terminar com a frase em inglês "este serviço está disponível no &lt; idioma do seu conteúdo da oferta>". Você também pode fornecer documentos de suporte em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

Para ajudar a criar sua oferta com mais facilidade, prepare alguns desses itens antecipadamente. Os itens a seguir são necessários, salvo indicação em contrário.

**Nome**: isso será exibido como o título da sua listagem de ofertas no Marketplace comercial. Uma marca comercial pode ser atribuída ao nome. Ele não pode conter emojis (a menos que sejam a marca registrada e os símbolos de direitos autorais) e deve ser limitado a 50 caracteres.

**Resumo dos resultados da pesquisa**: Descreva a finalidade ou o objetivo da sua oferta em 100 caracteres ou menos. Esse resumo é usado no Marketplace comercial listando os resultados da pesquisa. Ele não deve ser idêntico ao título. Considere a inclusão de suas principais palavras-chave de SEO.

**Descrição breve**: forneça uma breve descrição da sua oferta (até 256 caracteres). Ele será exibido na sua listagem de ofertas em portal do Azure.

**Descrição**: Descreva sua oferta em 3.000 caracteres ou menos. Essa descrição será exibida na listagem do Marketplace comercial. Considere incluir uma proposta de valor, um benefício importante, uma categoria ou associações do setor e todas as divulgações necessárias.

Aqui estão algumas dicas para escrever sua descrição:

* Descreva claramente o valor de sua oferta em algumas frases, incluindo:
    * O tipo de usuário que se beneficia da oferta.
    * O que o cliente precisa ou emite os endereços da oferta.
* Lembre-se de que as primeiras frases podem ser exibidas nos resultados da pesquisa.
* Use vocabulário específico do setor.

Você pode usar marcas HTML para formatar sua descrição. Para obter informações sobre formatação HTML, consulte [marcas HTML com suporte nas descrições da oferta do Marketplace comercial](./supported-html-tags.md).

**Link de política de privacidade**: forneça uma URL para a política de privacidade, hospedada no seu site. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

**Links úteis** (opcional): carregar documentos online complementares sobre sua oferta.

**Informações de contato**: forneça o nome, o endereço de email e o número de telefone de duas pessoas em sua empresa (você pode ser um deles): um contato de suporte e um contato de engenharia. Usaremos essas informações para se comunicar com você sobre sua oferta. Essas informações não são mostradas aos clientes, mas podem ser fornecidas aos parceiros do CSP (provedor de soluções na nuvem)

**URLs de suporte** (opcional): se você tiver sites de suporte para clientes globais do Azure e/ou clientes do Azure governamental, forneça essas URLs.

**Mídia do Marketplace – logotipos**: forneça um arquivo PNG para o logotipo de tamanho grande de sua oferta. O Partner Center vai usá-lo para criar logotipos médios e pequenos. Você pode, opcionalmente, substituir esses logotipos por uma imagem diferente mais tarde.

* O logotipo grande (de 216 x 216 a 350 x 350 px) aparece em sua listagem de oferta no Azure Marketplace.
* O logotipo médio (90 x 90 px) é mostrado quando um novo recurso é criado.
* O logotipo pequeno (48 x 48 px) é usado nos resultados da pesquisa do Azure Marketplace.

Siga estas diretrizes para seus logotipos:

* Verifique se a imagem não está ampliada.
* O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias em seu logotipo.
* As cores de portal do Azure são brancas e pretas. Não use-os como plano de fundo do seu logotipo. Recomendamos cores primárias simples que tornam seu logotipo proeminente.
* Se você usar um plano de fundo transparente, verifique se o logotipo e o texto não estão em branco, preto ou azul.
* A aparência do seu logotipo deve ser simples. Evite gradientes. Não coloque texto no logotipo, nem mesmo o nome ou marca da sua empresa.

**Mídia do Marketplace – capturas de tela** (opcional): Adicione até cinco imagens que demonstram como sua oferta funciona. Todas as imagens devem ter 1280 x 720 pixels de tamanho e em. Formato PNG.

**Mídia do Marketplace – vídeos** (opcional): Carregue até cinco vídeos que demonstram sua oferta. Os vídeos devem ser hospedados no YouTube ou no Vimeo e ter uma miniatura (arquivo PNG 1280 x 720).

## <a name="preview-audience"></a>Público-alvo de versão prévia

Um público de visualização pode acessar sua oferta antes de ser publicado no Azure Marketplace para testá-lo. Na página **público de visualização** do Partner Center, você pode definir um público-alvo limitado.

> [!NOTE]
> Um público de visualização difere de um plano privado. Um plano privado é aquele que você disponibiliza apenas para um público específico que escolher. Isso permite que você negocie um plano personalizado com clientes específicos.

Você pode enviar convites para endereços de email da MSA (conta da Microsoft) ou do Azure Active Directory (Azure AD). Adicione até 10 endereços de email manualmente ou importe até 20 com um arquivo. csv. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar quaisquer alterações ou atualizações na sua oferta.

## <a name="plans-and-pricing"></a>Planos e preços

As ofertas de serviço gerenciado exigem pelo menos um plano. Um plano define o escopo da solução, os limites e os preços associados, se aplicável. Você pode criar vários planos para sua oferta a fim de fornecer a seus clientes opções de preços e técnicas diferentes. Para obter diretrizes gerais sobre planos, incluindo planos privados, consulte [planos e preços para ofertas de Marketplace comercial](plans-pricing.md).

Os serviços gerenciados dão suporte a apenas um modelo de preços: **traga sua própria licença (BYOL)**. Isso significa que você faturará seus clientes diretamente, e a Microsoft não cobrará nenhuma taxa.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma oferta de serviço gerenciado](./create-managed-service-offer.md)
* [Práticas recomendadas de listagem de ofertas](./gtm-offer-listing-best-practices.md)