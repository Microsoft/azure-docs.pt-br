---
title: Inserir o cliente do Analisador de Internet | Microsoft Docs
description: Neste artigo, saiba como inserir o cliente JavaScript do Analisador de Internet em seu aplicativo.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744044"
---
# <a name="embed-the-internet-analyzer-client"></a>Inserir o cliente do Analisador de Internet

Este artigo mostra como inserir o cliente JavaScript em seu aplicativo. A instalação desse cliente é necessária para executar testes e receber análises de scorecard. **O cliente JavaScript específico do perfil é fornecido após a configuração do primeiro teste.** Desse ponto em diante, você pode continuar a adicionar ou remover testes para esse perfil sem precisar inserir um novo script. Para obter mais informações sobre o Analisador de Internet, confira a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

O Analisador de Internet requer acesso ao Azure e a outros serviços Microsoft para funcionar corretamente. Permita o acesso à rede para `fpc.msedge.net` e quaisquer URLs de ponto de extremidade pré-configuradas (visíveis por meio da [CLI](internet-analyzer-cli.md)) antes de inserir o cliente.

## <a name="find-the-client-script-url"></a>Localizar a URL de script de cliente

A URL do script poderá ser encontrada por meio do portal do Azure ou do CLI do Azure depois que um teste tiver sido configurado. Para obter mais informações, confira [Criar um recurso do Analisador de Internet](internet-analyzer-create-test-portal.md).

Opção 1. No portal do Azure, use [este link](https://aka.ms/InternetAnalyzerPreviewPortal) para abrir a página do portal de versão prévia do Analisador de Internet do Azure. Acessando **Configurações > Configuração**, navegue até o perfil do Analisador de Internet para ver a URL do script.

Opção 2. Usando a CLI do Azure, verifique a propriedade `scriptFileUri`.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Detalhes do cliente

O script é gerado especificamente para seu perfil e testes. Depois de ser carregado, o script será executado em um atraso de dois segundos. Primeiro, ele entra em contato com o serviço do Analisador de Internet para buscar a lista de pontos de extremidade configurados em seus testes. Em seguida, ele executa as medidas e carrega os resultados cronometrados de volta para o serviço Analisador de Internet.

## <a name="client-examples"></a>Exemplos de cliente

Estes exemplos mostram alguns métodos básicos para inserir o JavaScript do cliente em sua página da Web ou aplicativo. Usamos `0bfcb32638b44927935b9df86dcfe397` como uma ID de perfil de exemplo para a URL do script.

### <a name="run-on-page-load"></a>Executar no carregamento da página
O método mais simples é usar a tag de script dentro do bloco de marca META. Essa tag executará o script uma vez a cada carregamento de página.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Próximas etapas

Leia as [Perguntas frequentes do Analisador de Internet](internet-analyzer-faq.md)
