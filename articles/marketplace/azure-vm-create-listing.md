---
title: Configurar detalhes de listagem da oferta de máquina virtual no Azure Marketplace
description: Saiba como configurar detalhes de listagem da oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 7030ffeb49c0a3919894f6dcf81e7252777267c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629590"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Como configurar detalhes de listagem de oferta de máquina virtual

Na página de **listagem da oferta** (selecione no menu do nav esquerdo no Partner Center), você define os detalhes da oferta, como nome da oferta, descrição, links e contatos.

> [!NOTE]
> O conteúdo de listagem de sua oferta, como descrição, documentos, capturas de tela e termos de uso, não precisa estar em inglês, desde que a descrição da oferta comece com a frase "este aplicativo está disponível apenas no \<non-English language> ." Você também pode fornecer um endereço da Web para vincular a um site que oferece conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

## <a name="marketplace-details"></a>Detalhes do Marketplace

### <a name="name"></a>Nome

O nome que você digitar aqui será mostrado aos clientes como o título da sua listagem de ofertas. Esse campo é preenchido automaticamente com o nome que você inseriu na caixa **alias de oferta** quando criou a oferta. O nome:

- Pode incluir a marca registrada e os símbolos de direitos autorais.
- Deve ter de 50 caracteres ou menos.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta para exibir nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 100 caracteres.

### <a name="short-description"></a>Descrição breve

Forneça uma descrição mais longa da sua oferta para exibir nos resultados da pesquisa do Azure Marketplace. Ela pode conter até 256 caracteres.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Use marcas HTML para formatar sua descrição para que ela seja mais atraente. Para obter uma lista de marcas permitidas, consulte [marcas HTML com suporte](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Link da política de privacidade

Insira o endereço web (URL) da política de privacidade da sua organização. Garanta que sua oferta esteja em conformidade com as leis e regulamentos de privacidade. Você também precisa postar uma política de privacidade válida em seu site.

## <a name="useful-links"></a>Links úteis

Forneça documentos online complementares sobre sua oferta. Para adicionar um link, selecione **Adicionar um link** e preencha os seguintes campos:

- **Name**: os clientes verão o nome na página de detalhes.
- **Link**: Insira um endereço Web que permita aos clientes exibir seu documento online.

## <a name="customer-support-links"></a>Links de atendimento ao cliente

Forneça o site de suporte em que os clientes podem entrar em contato com sua equipe de suporte.

- Site de suporte do Azure Global
- Site de suporte do Azure Government

## <a name="partner-support-contact"></a>Contato de suporte para parceiros

Forneça o **nome**, o **email** e o **telefone** para que os parceiros da Microsoft usem quando os clientes abrirem um tíquete de suporte. Essas informações não estão listadas no Azure Marketplace.

## <a name="engineering-contact"></a>Contato de engenharia

Forneça o **nome**, o **email** e o **telefone** para que a Microsoft Use quando houver problemas com sua oferta, incluindo problemas com a certificação. Essas informações não estão listadas no Azure Marketplace.

## <a name="cloud-solution-provider-program-contact"></a>Contato do programa do provedor de soluções na nuvem

Forneça o **nome**, o **email** e o **telefone** da pessoa que lida com seu programa CSP (se aplicável). Essas informações não estão listadas no Azure Marketplace. Para **materiais de marketing do programa CSP**, insira o endereço da Web em que os parceiros neste programa podem exibir seus materiais de marketing.

## <a name="marketplace-media"></a>Mídia do marketplace

Forneça logotipos e imagens para usar com sua oferta. O logotipo precisa estar no formato PNG. As imagens borradas farão com que seu envio seja rejeitado.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Se você estiver enfrentando um problema ao carregar arquivos, verifique se a sua rede local não bloqueia o serviço https://upload.xboxlive.com que é usado pelo Partner Center.

### <a name="logos"></a>Logotipos

Forneça um arquivo PNG para o logotipo de tamanho **grande** . O Partner Center usará isso para criar um logotipo **pequeno** e **médio** . Opcionalmente, você pode substituí-los por imagens diferentes posteriormente.

- **Grande** (de 216 x 216 a 350 x 350 px, required)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Esses logotipos são usados em locais diferentes na listagem:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Capturas de tela

Adicione até cinco capturas de tela que mostram como sua oferta funciona. Cada captura de tela precisa ter 1280 x 720 pixels de tamanho e estar no formato PNG. Cada captura de tela precisa incluir uma legenda.

### <a name="videos"></a>vídeos

Adicione até cinco vídeos que demonstrem sua oferta. Os vídeos devem ser hospedados em um serviço de vídeo externo. Insira o nome de cada vídeo, o endereço Web e uma imagem PNG em miniatura do vídeo em 1280 x 720 pixels.

Para obter recursos adicionais de listagem do Marketplace, confira [Práticas recomendadas para listagens de ofertas do Marketplace](gtm-offer-listing-best-practices.md).

Selecione **salvar rascunho** antes de continuar para a próxima guia no menu do nav esquerdo, **Visualizar audiência**.

## <a name="next-steps"></a>Próximas etapas

- [Criar um público de visualização](azure-vm-create-preview.md)
