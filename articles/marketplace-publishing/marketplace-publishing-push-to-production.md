---
title: Implantar nossa oferta no Azure Marketplace | Microsoft Docs
description: "Saiba mais sobre as instruções passo a passo para implantar sua oferta, imagem da máquina virtual, serviço do desenvolvedor, serviço de dados etc., para o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implantar a oferta no Azure Marketplace
Quando você estiver satisfeito com sua oferta (ou seja, os cenários de cliente, marketing, conteúdo etc. foram testados) e estiver pronto para começar, solicite **Enviar por push para produção** na guia **Publicar**.  

1. As quatro etapas na página PASSO A PASSO do publicação portal devem ser realizadas e estar verdes. Para ofertas de Máquina Virtual, siga diretrizes a seguir.
   
    ![desenho][img-pubportal-walkthru-checked]
2. Selecione a guia **Publicar** na lista no lado esquerdo.
   
    ![desenho][img-pubportal-menu-publish]
3. Clique no botão **Solicitar aprovação para enviar por push para a produção**. Depois que a solicitação for feita, a equipe de aprovação executará uma revisão final e sua oferta será disponibilizada no Azure Marketplace.
   
    ![desenho][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> No caso de Máquinas Virtuais, quando você clica no botão Solicitar aprovação para enviar por push para produção, as etapas a seguir são executadas em segundo plano. Você poderá exibir o andamento de cada etapa na guia PUBLICAR do portal de Publicação. Verifique essa página regularmente (até que o status mostre "Listada") para obter informações sobre falhas que precisem ser corrigidas por você.
> 
> * Inicialmente, sua solicitação é enviada à equipe de certificação, que valida o VHD. No entanto, se você estiver atualizando sua oferta já listada e a solicitação tiver apenas alterações de marketing, a etapa de certificação será ignorada.
> * Na próxima etapa, a solicitação será enviada para a equipe de validação de conteúdo, que verificará o conteúdo de marketing da oferta.
> * Se as etapas acima forem bem-sucedidas, a oferta será aprovada em produção. Nessa ocasião, o status da oferta se tornará "Listada" no portal de Publicação. No entanto, esse status significa que o processo tenha sido concluído. As etapas a seguir precisam ser realizadas para que a oferta seja disponibilizada no Azure Marketplace.
> * Depois que a oferta for aprovada em produção na etapa anterior, a replicação da oferta será iniciada em todos os data centers do Azure. Geralmente, uma replicação leva de 24 a 48 horas para ser concluída, mas pode levar até uma semana dependendo do tamanho do VHD. No entanto, se você estiver atualizando sua oferta já listada e ela tiver apenas alterações de marketing, a replicação será mais rápida.
> * Quando a replicação for concluída, sua oferta será disponibilizada no Azure Marketplace.
> 
> Você sempre pode excluir a oferta enquanto estiver no status **Rascunho** (ou seja, nunca **Enviar por push para preparo** ou **Enviar por push para produção**). Na guia **Histórico** clique no botão **Descartar rascunho** na parte inferior da página para excluir um rascunho.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de verificação de produção para todas as ofertas de Máquina Virtual
* Você deve ser um parceiro certificado do Microsoft Azure
* Na guia SKUs, a opção "Ocultar esta SKU do Marketplace porque ela sempre deverá ser comprada por meio de um modelo de solução" só deverá ser marcada como SIM somente se a SKU fizer parte de um Modelo de Solução. Em todos os demais casos, essa opção sempre deverá ser marcada como NÃO.
* Lembre-se: você não deve alterar a configuração de visibilidade da SKU depois que a SKU estiver listada. Não oferecemos suporte a essa funcionalidade.
* Verifique se os logotipos seguem as diretrizes de logotipo do Azure Marketplace fornecidas a seguir.
* As descrições da oferta e da SKU não devem ser iguais.
* O título da SKU e o resumo longo da oferta não devem ser iguais.
* O título da SKU e o resumo da oferta não devem ser iguais.
* Os títulos das SKUs não devem ser idênticos para uma oferta com várias SKUs.

**Diretrizes de logotipo do Azure Marketplace**

* O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.
* As cores do tema do portal do Azure são branco e preto. Portanto, evite usar essas cores como a cor de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal do Azure. É recomendável usar cores primárias simples. Se estiver usando um plano de fundo transparente, certifique-se de que o logotipo/texto não seja preto ou branco.
* Não use uma tela de fundo gradiente no logotipo.
* Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo.
* A aparência do seu logotipo deve ser “simples” e deve evitar usar gradientes.
* O logotipo não deve ser estendido.

**Diretrizes adicionais para o logotipo Hero:**

* O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. **No entanto, uma vez carregado, o ícone Hero não pode ser excluído do portal de Publicação. Nessa ocasião, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones Hero. Caso contrário, a oferta não será aprovada para produção.**
* O nome de exibição do editor, o título da SKU e o resumo longo da oferta são exibidos em uma fonte branca. Portanto, você deve evitar cores claras no plano de fundo do ícone Hero. Planos de fundo pretos, brancos e transparentes não são permitidos para ícones Hero.
* O nome de exibição do editor, o título da SKU, o resumo longo da oferta e o botão de criação são inseridos programaticamente no logotipo Hero depois que a oferta é listada. Portanto, você não deve inserir texto ao projetar o logotipo Hero. Apenas deixe o espaço vazio à direita, porque o texto (ou seja, nome de exibição do fornecedor, título do SKU, resumo longo da oferta) será incluído programaticamente nesse local. O espaço vazio para o texto deve ser de 415x100 à direita (e ele é deslocado 370px à esquerda).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de verificação adicional de produção para ofertas de VM já listadas
* Verifique se já há uma oferta com o mesmo nome de oferta da sua empresa. Se houver, você deverá adicionar uma nova versão da SKU à oferta existente em vez de criar uma nova oferta duplicada.
* Os discos de dados não devem ser alterados entre duas versões da mesma SKU.
* O Azure Marketplace não permite a alteração de preços das SKUs listadas, pois isso afeta a cobrança dos clientes existentes. Não altere o preço das SKUs listadas nas regiões onde a SKU está disponível. No entanto, você pode adicionar novas SKUs ou adicionar novas regiões a uma SKU existente.

## <a name="next-steps"></a>Próximas etapas
Depois que a oferta ficar ativa, teste os cenários de cliente para validar todos os contratos e a funcionalidade funciona corretamente no ambiente de produção conforme testado e validado no ambiente de preparo.

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
