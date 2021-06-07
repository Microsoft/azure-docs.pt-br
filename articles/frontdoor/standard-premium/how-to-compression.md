---
title: Melhorar o desempenho compactando arquivos no Azure front door Standard/Premium (visualização)
description: Saiba como melhorar a velocidade de transferência de arquivos e aumentar o desempenho de carregamento de página compactando os arquivos na porta frontal do Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098366"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Melhorar o desempenho compactando arquivos no Azure front door Standard/Premium (visualização)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

A compactação de arquivo é um método eficaz para melhorar a velocidade de transferência de arquivos e aumentar o desempenho de carregamento de página. A compactação reduz o tamanho do arquivo antes de ser enviado pelo servidor. A compactação de arquivos pode reduzir os custos de largura de banda e proporcionar uma experiência melhor para seus usuários.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Há duas maneiras de habilitar a compactação de arquivo:

- Habilitando a compactação no servidor de origem. A porta frontal do Azure passa os arquivos compactados e os entrega aos clientes que os solicitam.
- Habilitando a compactação diretamente nos servidores POP da porta frontal do Azure (*compactação em tempo real*). Nesse caso, a porta frontal do Azure compacta os arquivos e os envia para os usuários finais.

> [!IMPORTANT]
> As alterações de configuração de porta frontal do Azure levam até 10 minutos para serem propagadas por toda a rede. Se você estiver configurando a compactação pela primeira vez para o ponto de extremidade da CDN, considere aguardar 1-2 horas antes de solucionar o problema para garantir que as configurações de compactação sejam propagadas para todos os POPs.

## <a name="enabling-compression"></a>Habilitando a compactação

> [!Note]
> Na porta frontal do Azure, a compactação faz parte da **habilitação do cache** na rota. Somente quando você **habilita o Caching**, você pode tirar proveito da compactação na porta frontal do Azure.

Você pode habilitar a compactação das seguintes maneiras:
* Durante a criação rápida-quando você habilita o Caching, você pode habilitar a compactação.
* Durante a criação personalizada, habilite o cache e a compactação quando você estiver adicionando uma rota. 
* Na rota do Gerenciador de pontos de extremidade.
* Na página otimização.

### <a name="enable-compression-in-endpoint-manager"></a>Habilitar a compactação no Gerenciador de pontos de extremidade

1. Na página de perfil padrão/Premium da porta do Azure, vá para o **Gerenciador de pontos de extremidade** e selecione o ponto de extremidade que você deseja habilitar a compactação.

1. Selecione **Editar ponto de extremidade** e, em seguida, selecione a **rota** que você deseja habilitar a compactação. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Captura de tela da página de aterrissagem do Endpoint Manager." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Verifique se a opção **Habilitar cache** está marcada e marque a caixa de seleção **Habilitar compactação**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Habilite a compactação no Gerenciador de pontos de extremidade.":::   

1. Selecione **Atualizar** para salvar a configuração.

### <a name="enable-compression-in-optimization"></a>Habilitar a compactação na otimização

1. Na página de perfil padrão/Premium da porta frontal do Azure, vá para **otimizações** em configurações. Expanda o ponto de extremidade para ver a lista de rotas. 

1. Selecione os três pontos ao lado da **rota** que tem a compactação *desabilitada*. Em seguida, selecione **Configurar rota**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Tela de Habilitar compactação na página otimização." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Verifique se a opção **Habilitar cache** está marcada e marque a caixa de seleção **Habilitar compactação**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Captura de tela de habilitação da compactação no Gerenciador de pontos de extremidade."::: 

1. Clique em **Atualizar**.

## <a name="modify-compression-content-type"></a>Modificar tipo de conteúdo de compactação

Você pode modificar a lista padrão de tipos de MIME na página otimizações.

1. Na página de perfil padrão/Premium da porta frontal do Azure, vá para **otimizações** em configurações. Em seguida, selecione a **rota** que tem a compactação *habilitada*.

1. Selecione os três pontos ao lado da **rota** que tem a compactação *habilitada*. Em seguida, selecione **Exibir tipos de arquivo compactados**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Captura de tela da página otimização." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Exclua os formatos padrão ou selecione **Adicionar** para adicionar novos tipos de conteúdo.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Captura de tela da página personalizar compactação de arquivo."::: 

1. Selecione **salvar** para atualizar a compactação configurar.

## <a name="disabling-compression"></a>Desabilitando compactação

Você pode desabilitar a compactação das seguintes maneiras:
* Desabilitar a compactação na rota do Gerenciador de pontos de extremidade.
* Desabilitar a compactação na página otimização.

### <a name="disable-compression-in-endpoint-manager"></a>Desabilitar a compactação no Gerenciador de pontos de extremidade

1. Na página de perfil padrão/Premium da porta do Azure, vá para o **Gerenciador de pontos de extremidade** em configurações. Selecione o ponto de extremidade que você deseja desabilitar a compactação.

1. Selecione **Editar ponto de extremidade** e, em seguida, selecione a **rota** que você deseja desabilitar a compactação. Desmarque a caixa **Habilitar compactação** .

1. Selecione **Atualizar** para salvar a configuração.

### <a name="disable-compression-in-optimizations"></a>Desabilitar a compactação em otimizações

1. Na página de perfil padrão/Premium da porta frontal do Azure, vá para **otimizações** em configurações. Em seguida, selecione a **rota** que tem a compactação *habilitada*.

1. Selecione os três pontos ao lado da **rota** que tem a compactação *habilitada* e, em seguida, selecione *Configurar rota*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Captura de tela de desabilitar compactação na página otimização."::: 

1. Desmarque a caixa **Habilitar compactação** .

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Captura de tela da página atualizar rota para desabilitar a compactação."::: 

1. Selecione **Atualizar** para salvar a configuração.

## <a name="compression-rules"></a>Regras de compactação

Na porta frontal do Azure, somente os arquivos qualificados são compactados. Para se qualificar para a compactação, um arquivo deve:
* Ser de um tipo MIME 
* Ser maior que 1 KB
* Ser menor que 8 MB

Esses perfis dão suporte às seguintes codificações de compactação:
* gzip (GNU zip)
* brotli 

Se a solicitação dá suporte a mais de um tipo de compactação, a compactação brotli terá precedência.

Quando uma solicitação de um ativo especifica a compactação Gzip e a solicitação resulta em um erro de cache, a porta frontal do Azure faz a compactação Gzip do ativo diretamente no servidor POP. Depois disso, o arquivo compactado será servido do cache.

Se a origem usar a CTE (codificação de transferência em bloco) para enviar dados compactados para o POP de porta frontal do Azure, os tamanhos de resposta maiores que 8 MB não serão suportados. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar seu primeiro [conjunto de regras](how-to-configure-rule-set.md)
- Saiba mais sobre [condições de correspondência do conjunto de regras](concept-rule-set-match-conditions.md)
- Saiba mais sobre o [conjunto de regras de porta frontal do Azure](concept-rule-set.md)
