---
title: Descrição do mercado para um módulo do Azure IoT Edge | Microsoft Docs
description: Crie a descrição do marketplace de um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 30f44e0e8bae4071403b5c28cd4133970fc4d468
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913257"
---
# <a name="iot-edge-module-marketplace-tab"></a>Guia de mercado de módulo do IoT Edge


A guia **Marketplace** da página **New Offer** permite que você forneça aos clientes em potencial informações e contratos de marketing, vendas e legais e gerencie leads gerados no mercado. Essa forma longa é dividida em quatro seções: **Visão geral**, **artefatos de Marketing**, **gerenciamento de clientes potenciais**, e **Legal**.

## <a name="overview"></a>Visão geral

Nesta seção, você insere as informações gerais sobre sua oferta do Azure Marketplace.  Um asterisco (*) anexado ao nome do campo indica que ele é necessário.

![Seção Visão geral da guia Marketplace no formulário Nova oferta para os módulos do IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

A tabela a seguir descreve o objetivo e o conteúdo desses campos.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título**                 | Título da oferta. Ele será exibido com destaque no marketplace.  Comprimento máximo de 50 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Resumo**               | Breve resumo da oferta. Comprimento máximo de 100 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Resumo longo**          | Mais Resumo da oferta (embora pudesse ser igual a **resumo**).  Comprimento máximo de 256 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Descrição**           | Descrição da oferta.  Comprimento máximo de 3000 caracteres, suporta formatação HTML simples.<br/> Ele deve incluir um *requisitos mínimos de hardware* parágrafo na parte inferior. Por exemplo: <br/> <p><u>Requisitos mínimos de hardware:</u> Linux x64 e OS arm32, 1 GB de RAM, 500 Mb de armazenamento</p>
| **Identificador de marketing**  | Um URL exclusivo para associar a essa oferta geralmente inclui sua organização e o nome da solução, com um comprimento máximo de 50 caracteres.  Por exemplo:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Visualizar códigos de inscrição** | Adicione de um a 100 identificadores de assinatura de pré-visualizadores. Essas assinaturas listadas na lista de permissões terão acesso à oferta assim que forem publicadas antes de serem publicadas. |
| **Links úteis**          | A seleção múltipla de categorias comerciais e técnicas que oferecem pode ser melhor associada.  Um máximo de 10 permitido. Certifique-se de adicionar pelo menos um link à sua documentação e um link para os dispositivos compatíveis do IoT Edge a partir do [catálogo de dispositivos IoT do Azure](https://catalog.azureiotsolutions.com/). |
| **Categorias sugeridas** | Selecione até cinco categorias. Eles serão exibidos na sua página de detalhes do produto. Nas páginas de navegação, todos os módulos IoT Edge são mostrados na categoria *Internet of Things \> IoT Edge*.|

### <a name="offer-example"></a>Por exemplo:

 Os exemplos a seguir mostram como os campos **Title**, **Summary**, **Description**, **Logos** e **Screenshots** da oferta aparecem em visões diferentes.
 
#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao navegar ofertas:

    ![Como as ofertas de mercado são exibidas no site do Azure Marketplace - navegue](./media/iot-edge-module-ampdotcom-card.png)

- Ao analisar os detalhes da oferta:

    ![Como o módulo IoT Edge é exibido ao procurar os detalhes do produto no site](./media/iot-edge-module-ampdotcom-pdp.png)

#### <a name="on-the-azure-portal-website"></a>No site do portal do Azure:

- Ao navegar ofertas:

    ![Como o módulo IoT Edge é exibido ao navegar no portal #1 do Azure](./media/iot-edge-module-portal-browse.png)

    ![Como o módulo IoT Edge é exibido durante a navegação no portal #2 do Azure](./media/iot-edge-module-portal-product-picker.png)

- Quando você está procurando uma oferta:

    ![Como o módulo IoT Edge é exibido ao pesquisar o portal do Azure](./media/iot-edge-module-portal-search.png)

- Ao analisar os detalhes da oferta:

    ![Como o módulo IoT Edge é exibido ao procurar os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefatos de Marketing

Esta seção tem as seguintes subseções: **Logotipos**, **captura de tela**, e **vídeos**. 

>[!Note]
>Os logotipos são os únicos artefatos de marketing necessários, no entanto, todos são altamente recomendados para o melhor apelo do cliente.

![Seção Artefatos de Marketing da guia Mercado no formulário Nova Oferta para máquinas virtuais](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  | Veja as capturas de tela anteriores para ver como e onde seus logotipos serão usados.  |
| **Pequeno**                 | formato PNG de 40 x 40 pixels                                                     |
| **Médio**                | formato PNG de 90 x 90 pixels                                                     |
| **Grande**                 | formato do pixel 115 x 115 PNG                                                  |
| **Ampla**                  | formato do pixel de 255 x 115 PNG                                                   |
| **Hero**                  | formato de pixel de 815 x 290 PNG.  Opcional, no entanto, uma vez carregado, o ícone do herói não pode ser excluído. |
| *Capturas de tela*  | Capturas de tela são exibidas na sua página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que o seu módulo IoT Edge faz e como funciona. Você pode mostrar diagramas de arquitetura ou usar ilustrações de casos, por exemplo. Opcional, mas no máximo cinco capturas de tela por SKU. |
| **Nome**                  | Nome ou o título. Comprimento máximo de 100 caracteres.                             |
| **Imagem**                 | Imagem de captura de tela, o formato do pixel 533, 324 PNG                                         |
| *Vídeos*  | Vídeos são exibidos na sua página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que o seu módulo IoT Edge faz e como funciona. |
| **Nome**                  | Nome ou o título. Comprimento máximo de 100 caracteres.                              |
| **Link**                  | URL do vídeo, hospedado no YouTube ou Vimeo                                        |
| **Miniatura**             | formato do pixel 533, 324 PNG                                                           |


### <a name="logo-guidelines"></a>Diretrizes de logotipo

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logotipos enviados para o Cloud Partner Portal devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.
*  As cores de tema do Portal do Azure são branco e preto. Evite usar essas cores como a cor de fundo para seus logotipos. Use uma cor que torne seus logotipos proeminentes no portal do Azure. É recomendável usar cores primárias simples. Se você estiver usando um plano de fundo transparente, verifique se os logotipos / texto não são branco, preto ou azul.
*  Não use um plano de fundo gradiente no seu logotipo.
*  Evite colocar o texto - até mesmo sua empresa ou nome de marca - no logotipo. A aparência do seu logotipo deve ser "plana" e deve evitar gradientes.
*  Não se estendem o logotipo.

#### <a name="hero-logo"></a>Logotipo Hero

O logotipo Hero é opcional.

>[!Important]
>Depois que o logotipo do herói é carregado, ele não pode ser excluído.

Use as seguintes diretrizes para um logotipo do Herói: 

*  Fundos pretos, brancos e transparentes não são permitidos.
*  Evite usar qualquer cor clara como plano de fundo para o logotipo.  O nome de exibição do editor, o título do plano e o resumo longo da oferta são exibidos em cor de fonte em branco e devem se destacar em relação ao plano de fundo.
*  Evite usar a maioria dos textos quando estiver criando o logotipo. O nome do editor, o título do plano, o resumo longo da oferta e um botão de criação são incorporados programaticamente dentro do logotipo quando a oferta é listada. 
* Inclua um espaço retangular não utilizado no lado direito do logotipo do seu herói. Esse espaço em branco é de 415x100 pixels e deslocado a partir da esquerda por 370 pixels.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Gerenciamento de clientes potenciais

Esta seção permite que você configure as opções para coletar leads de clientes gerados a partir de suas ofertas do Azure Marketplace. Você pode selecionar as seguintes opções de armazenamento em uma lista suspensa.

* **Nenhum** - as informações de lead padrão não são coletadas.
* Tabela do Azure - gravada na tabela do Azure especificada por uma cadeia de conexão.
* Dynamics CRM Online - escrito para o [Online do Microsoft Dynamics 365](https://dynamics.microsoft.com/) instância, especificada por uma URL e credenciais de autenticação.
* Ponto de extremidade HTTPS - gravado no ponto de extremidade HTTPS especificado como uma carga útil JSON.
* Marketo - gravado na instância [Marketo](https://www.marketo.com/) especificada, especificada por ID do servidor, ID munchkin e ID do formulário.
* SalesForce – gravado em um [Salesforce](https://www.salesforce.com/) banco de dados, especificado por um identificador de objeto.

Depois de publicar sua oferta, a conexão do lead é verificada e um lead de teste é enviado automaticamente para o destino configurado. 

>[!Note]
>As informações de lead devem ser gerenciadas continuamente e essas configurações devem ser prontamente atualizadas sempre que forem feitas alterações em sua arquitetura de gerenciamento de clientes.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->

## <a name="legal"></a>Legal

Esta seção permite que você fornecer os dois documentos legais que são necessários para cada oferta: Política de Privacidade e Termos de Uso.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **URL da política de privacidade**    | URL para sua política de privacidade postada                                            |
| **Termos de uso**          | Termos de uso como HTML simples in-line ou link para seus termos de uso publicados       |

<br/>

## <a name="next-steps"></a>Próximas etapas

Use o [suporte](./cpp-support-tab.md) guia para fornecer suportam técnico e usuário de recursos para sua oferta.
