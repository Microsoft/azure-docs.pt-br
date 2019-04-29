---
title: Publicar uma oferta do Aplicativo do Power BI – Azure Marketplace | Microsoft Docs
description: Publica uma oferta de aplicativo do Power BI no Microsoft AppSource marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725697"
---
# <a name="publish-a-power-bi-app-offer"></a>Publicar uma oferta de aplicativo do Power BI

A última etapa, depois de ter definido uma oferta no Portal do Cloud Partner e criou os recursos técnicos associados, é enviar a oferta para publicação. Para iniciar esse processo, no painel esquerdo do **nova oferta** janela, selecione **publicar**. Para obter mais informações, consulte [Publicar ofertas no Azure Marketplace e no AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Etapas de publicação

Essas são as principais etapas no processo de publicação:

![Etapas de processo de publicação para o aplicativo do Power BI oferecem](./media/publishing-process-steps.png)

Esta tabela descreve cada etapa e fornece seu tempo de conclusão estimado:

|   Etapa de publicação            |   Hora     |   DESCRIÇÃO                                                                  |
| --------------------         |------------| ----------------                                                               |
| Validar os pré-requisitos       | 15 minutos     | Informações de oferta e configurações de oferta são validadas.                            |
| Certificação                | Um a sete dias   | A equipe de certificação do Power BI analisa sua oferta. A equipe executa seu aplicativo do Power BI por meio de um teste de verificação manual, instalando o aplicativo por meio da URL de instalação fornecido. Primárias validações são executadas como parte do processo de certificação de aplicativo (descrito mais adiante neste documento).         |
| Empacotamento                    | \< 1 hora  | Ativos de técnicos da oferta são empacotados para uso pelo cliente.                        |
| Registro de geração de leads | \< 1 hora  | Os sistemas de clientes potenciais são configurados e implantados.                                      |
| Aprovação do publicador            | \-         | Você pode concluir uma revisão final e a confirmação antes da oferta ficar ativa. Você agora também terá um link para visualizar sua oferta. Depois que você estiver satisfeito com a aparência de sua visualização, selecione **Go Live** sobre o **Status** guia. Isso envia uma solicitação para a equipe de integração para listar seu aplicativo no AppSource.    |
| Live                         | \< 3 horas | Sua oferta é agora listada publicamente ("dinâmico") no AppSource, e os clientes podem exibir o aplicativo e implantá-lo em suas assinaturas do Power BI. Você também receberá um email de confirmação. Na coluna à direita na **todas as ofertas** guia, você pode ver o status de todas as suas ofertas. Sobre o **Status** guia, você pode ver o status detalhado de fluxo de publicação para sua oferta. |
|   |   |

Permitir até oito dias para a conclusão desse processo. Depois que você percorrer essas etapas de publicação, sua oferta de aplicativo do Power BI será listada na [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) seção de aplicativos do Power BI.


### <a name="app-certification-process"></a>Processo de certificação de aplicativo

A equipe de integração da Microsoft usa esse processo para validar seu envio de oferta de aplicativo do Power BI:

1. Examine os links de Ajuda e documentos legais.
2. Valide informações de contato de suporte.
3. Use a URL do instalador para verificar a instalação correta.
4. Verificar o aplicativo de malware e outros conteúdos maliciosos.
5. Verifique se que o conteúdo exibido corresponde à descrição do aplicativo.
6. Verifique se que as operações relacionadas ao aplicativo funcionem conforme o esperado no Power BI. A equipe abre relatórios e painéis com dados de exemplo, se conecta a fontes de dados personalizadas, atualiza os dados e assim por diante.

A Equipe de Certificação faz comentários quando encontra problemas.  Para obter mais informações sobre requisitos de aplicativo do Power BI, consulte o [documentação do aplicativo do Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Próximas etapas

Recomendamos que você monitore regularmente seu aplicativo na [AppSource marketplace](https://appsource.microsoft.com).  Você também deve usar o [percepções do vendedor](../../cloud-partner-portal-orig/si-getting-started.md) recurso da [Portal do Cloud Partner](https://cloudpartner.azure.com/#insights) para obter informações sobre os clientes marketplace e o uso do aplicativo. Por fim, você pode [atualizar sua oferta](./cpp-update-existing-offer.md).
