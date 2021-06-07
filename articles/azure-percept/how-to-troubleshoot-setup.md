---
title: Solucionar problemas durante a experiência de instalação do Azure Percept DK
description: Obtenha dicas de solução para alguns dos problemas mais comuns encontrados durante a experiência de instalação
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608486"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Guia de solução de problemas da experiência de instalação do Azure Percept DK

Consulte a tabela abaixo para obter soluções alternativas para problemas comuns encontrados durante a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md). Se o problema persistir, entre em contato com o suporte ao cliente do Azure.

|Problema|Motivo|Solução alternativa|
|:-----|:------|:----------|
|Ao conectar-se às páginas de inscrição da conta do Azure ou ao portal do Azure, você poderá entrar automaticamente com uma conta armazenada em cache. Se essa não for a conta que você pretende usar, ela poderá resultar em uma experiência inconsistente com a documentação.|Isso geralmente ocorre devido a uma configuração no navegador para "lembrar" uma conta que você usou anteriormente.|Na página do Azure, clique no nome da sua conta no canto superior direito e selecione **sair**. Você poderá entrar com a conta correta.|
|O ponto de acesso Wi-Fi do Azure Percept DK (scz-xxxx ou APD-XXXX) não aparece na lista de redes Wi-Fi disponíveis.|Esse geralmente é um problema temporário que resolve em 15 minutos.|Aguarde até que a rede seja exibida. Se não aparecer depois de mais de 15 minutos, reinicialize o dispositivo.|
|A conexão com o ponto de acesso Wi-Fi do Azure Percept DK frequentemente se desconecta.|Isso pode ser devido a uma conexão ruim entre o dispositivo e o computador host. Ela também pode ser causada por interferência de outras conexões de Wi-Fi no computador host.|Verifique se as antenas estão conectadas corretamente ao kit de desenvolvimento. Se o kit de desenvolvimento estiver longe do computador host, tente movê-lo mais perto. Desative qualquer outra conexão com a Internet, como LTE/5G, se elas estiverem em execução no computador host.|
|O computador host mostra um aviso de segurança sobre a conexão com o ponto de acesso do Azure Percept DK.|Esse é um problema conhecido que será corrigido em uma atualização posterior.|É seguro prosseguir com a experiência de instalação.|
|O ponto de acesso Wi-Fi do Azure Percept DK (scz-xxxx ou APD-XXXX) aparece na lista de redes, mas não se conecta.|Isso pode ser devido a uma corrupção temporária do ponto de acesso Wi-Fi do kit de desenvolvimento.|Reinicialize o kit de desenvolvimento e tente novamente.|
|Não é possível conectar-se a uma rede Wi-Fi durante a experiência de instalação.|A rede Wi-Fi deve ter conectividade com a Internet no momento para se comunicar com o Azure. O EAP [PEAP/MSCHAP], portais prisioneiros e conectividade Enterprise EAP-TLS não tem suporte no momento.|Verifique se o tipo de rede Wi-Fi tem suporte e tem conectividade com a Internet.|