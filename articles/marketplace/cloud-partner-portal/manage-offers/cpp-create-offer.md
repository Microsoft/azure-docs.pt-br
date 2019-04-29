---
title: Criar ofertas do Marketplace – Azure Marketplace | Microsoft Docs
description: Criar ofertas nos Marketplaces do Azure e do AppSource usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pbutlerm
ms.openlocfilehash: af9b34d90098409135020fa8a45ecd0253f25b22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825687"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Criar ofertas do Azure Marketplace e do AppSource

Um propósito essencial do Portal do Cloud Partner é habilitar os publicadores a criar (e, em seguida, publicar) ofertas nos Marketplaces do Microsoft Azure e do AppSource.  Esta operação sempre começa com a seleção do tipo de oferta desejada do [menu Nova oferta](../portal-tour/cpp-new-offer-menu.md).  Em resposta, a página **Nova Oferta** apropriada é exibida para esse tipo de oferta.  Por exemplo, a imagem a seguir mostra a página **Nova Oferta** padrão para um tipo de aplicativo do Azure.

![Página Nova Oferta padrão](./media/new-offer-page.png)

Há duas seleções de guia disponíveis na barra de menu horizontal exibida na parte superior desta página: 
- Guia **Editor** – permite a entrada de informações e o carregamento de ativos para a instância da nova oferta.  Essa guia é exibida por padrão.
- Guia **Status** – fornece o status de publicação e lista quaisquer problemas de validação e de análise. 

Quando cria uma oferta, você usa a guia **Editor** para inserir informações sobre essa oferta. 

## <a name="editing-operations"></a>Operações de edição

A barra de ferramentas horizontal, localizada acima da área de entrada de dados, exibe os botões a seguir:

|   Botão    |   Finalidade                                                          |
|   ------    |  --------                                                          |
| **Salvar**    | Salva alterações recentes de entrada de dados.  Você precisará salvar manualmente as alterações antes de navegar para fora da página, caso contrário as alterações serão perdidas. | 
| **Descartar** | Descarta as alterações recentes de entrada de dados (desde o último salvamento)             |
| **Comparar** | Compara o estado da oferta atual com o da oferta publicada.  Habilitado apenas depois de uma oferta ter sido publicada com êxito.  |
| **Publicar** | Inicia o processo de publicação para esta oferta                       |
| **Excluir**  | Exclui esta oferta depois que ela é criada, mas antes de ela ser publicada. |
|   |   |


## <a name="editing-tabs"></a>Guias de edição

Ao criar uma oferta, você fornece os dados necessários e opcionais em cada guia localizada na coluna vertical à esquerda da página **Nova Oferta**.  Controles de interface do usuário padrão, tais como caixas de texto, menus suspensos e caixas de seleção, são exibidos para coleta de dados.  Embora a coleção específica de guias de edição dependa do tipo de oferta, a tabela a seguir lista algumas das guias comuns.

|      Nome da guia       |   Finalidade                                                            |
|      --------       |   -------                                                            |
| **Configurações da oferta**  | Coleta informações de identidade do publicador e da oferta.                    |
| **SKUs**            | Define as características técnicas e empresariais para cada versão do SKU (unidade de manutenção de estoque) de sua oferta |
| **Test Drive**      | Para os tipos que dão suporte a esse recurso opcional, define uma demonstração de sua oferta.  Para saber mais, consulte [O que é um test drive?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** ou **Vitrine** | Coleta cadeias de caracteres de texto, documentos e imagens usados para listar a oferta no marketplace |
| **Suporte**         | Coleta informações de contato para suporte online, de engenharia e atendimento ao cliente  |
|  |  |

O conteúdo das guias nomeadas de forma similar pode diferir entre tipos diferentes de oferta.  Detalhes específicos a uma oferta dessas guias são fornecidos na seção "Criar oferta" para cada tipo de oferta.


## <a name="next-steps"></a>Próximas etapas

Depois de criar e salvar uma oferta e antes ou depois de publicá-la, você pode [ver seu status](./cpp-view-status-offer.md).
