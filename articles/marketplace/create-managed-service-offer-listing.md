---
title: Como configurar os detalhes de listagem da oferta de serviço gerenciado no Microsoft Partner Center
description: Saiba como configurar os detalhes de listagem da oferta de serviço gerenciado no Azure Marketplace usando o Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 5ab831ae6ab761804fc7a4665000a13ab61acadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918081"
---
# <a name="how-to-configure-your-managed-service-offer-listing-details"></a>Como configurar os detalhes de listagem da oferta de serviço gerenciado

As informações fornecidas na página de **listagem de ofertas** do Partner Center serão exibidas no Azure Marketplace. Isso inclui o nome, a descrição, a mídia e outros ativos de marketing de sua oferta.

> [!NOTE]
> Se sua oferta estiver em um idioma diferente do inglês, a listagem da oferta poderá estar nesse idioma, mas a descrição deve começar ou terminar com a frase em inglês "este serviço está disponível no &lt; idioma do seu conteúdo da oferta>". Você também pode fornecer documentos de suporte em um idioma diferente daquele usado nos detalhes da listagem de ofertas.

Na página de **listagem de ofertas** no Partner Center, forneça as informações descritas abaixo. Para saber mais sobre os detalhes de listagem para sua oferta de serviço gerenciado, examine [planejar uma oferta de serviço gerenciado](./plan-managed-service-offer.md).

## <a name="marketplace-details"></a>Detalhes do marketplace

1. A caixa **nome** é preenchida previamente com o nome que você inseriu anteriormente na caixa de diálogo nova oferta, mas você pode alterá-la a qualquer momento. Esse nome será exibido como o título da sua listagem de ofertas na loja online.
2. Na caixa **Resumo dos resultados da pesquisa** , descreva a finalidade ou o objetivo da sua oferta em 100 caracteres ou menos.
3. No campo **Descrição curta** , forneça uma breve descrição da sua oferta (até 256 caracteres). Ele será exibido na sua listagem de ofertas na portal do Azure.
4. No campo **Descrição** , descreva sua oferta de serviço gerenciado. Você pode inserir até 2.000 caracteres de texto nesta caixa, incluindo marcas HTML e espaços. Para obter informações sobre formatação HTML, consulte [marcas HTML com suporte nas descrições da oferta](./supported-html-tags.md).
5. Na caixa **link de política de privacidade** , insira um link (começando com HTTPS) para a política de privacidade da sua organização. Você é responsável por garantir que sua oferta esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida.

## <a name="useful-links"></a>Links úteis

Você tem a opção de fornecer documentos online complementares sobre sua solução:

1. Selecione **Adicionar um link**.
2. Forneça um nome e um endereço da Web (começando com HTTPS) para cada documento.

## <a name="contact-information"></a>Informações de contato

Insira o nome, o endereço de email e o número de telefone de duas pessoas em sua empresa (você pode ser um deles): um contato de suporte e um contato de engenharia. Usaremos essas informações para se comunicar com você sobre sua oferta. Essas informações não são mostradas aos clientes, mas podem ser fornecidas aos parceiros do CSP (provedor de soluções na nuvem).

## <a name="support-urls"></a>URLs de suporte

Se você tiver sites de suporte para clientes globais do Azure e/ou clientes do Azure governamental, insira sua URL, começando com HTTPS.

## <a name="marketplace-media"></a>Mídia do marketplace

> [!NOTE]
> Se você está enfrentando um problema ao carregar arquivos, verifique se sua rede local não bloqueia o serviço `https://upload.xboxlive.com` que é usado pelo Partner Center.

### <a name="add-logos"></a>Adicionar logotipos

Em **logotipos**, carregue um logotipo **grande** no. Formato PNG entre 216 x 216 e 350 x 350 pixels. O Partner Center criará automaticamente logotipos **médios** e **pequenos** , que você poderá substituir posteriormente.

* O logotipo grande (de 216 x 216 a 350 x 350 px) aparece em sua listagem de oferta no Azure Marketplace.
* O logotipo médio (90 x 90 px) é mostrado quando um novo recurso é criado.
* O logotipo pequeno (48 x 48 px) é usado nos resultados da pesquisa do Azure Marketplace.

### <a name="add-screenshots-optional"></a>Adicionar capturas de tela (opcional)

Adicione até cinco imagens que demonstram sua oferta. Todas as imagens devem ter 1280 x 720 pixels de tamanho e em. Formato PNG.

1. Em **capturas de tela**, arraste e solte o arquivo PNG na caixa **captura de tela** .
2. Selecione **Adicionar Legenda de imagem**.
3. Na caixa de diálogo que aparece, insira uma legenda.
4. Repita as etapas 1 a 3 para adicionar capturas de tela adicionais.

### <a name="add-videos-optional"></a>Adicionar vídeos (opcional)

Você pode adicionar links para vídeos do YouTube ou do Vimeo que demonstram sua oferta. Esses vídeos são mostrados aos clientes junto com sua oferta. Você deve inserir uma imagem em miniatura do vídeo, tamanho de 1280 x 720 pixels e em. Formato PNG. Adicione no máximo cinco vídeos por oferta.

1. Em **vídeos**, selecione o **link Adicionar vídeo**.
2. Nas caixas que aparecem, insira o nome e o link para o vídeo.
3. Arraste e solte um arquivo PNG (1280 x 720 pixels) na caixa **miniatura** cinza.
4. Para adicionar outro vídeo, repita as etapas de 1 a 3.

Selecione **salvar rascunho** antes de continuar para a próxima guia: **Visualizar público-alvo**.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um público-alvo de versão prévia](create-managed-service-offer-preview.md)
