---
title: Controlar o comportamento de cache da CDN do Azure com regras de cache | Microsoft Docs
description: É possível utilizar as regras de cache da CDN para definir ou modificar o comportamento padrão de expiração do cache globalmente e com condições, como um caminho de URL e extensões de arquivo.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: a5f4f6a6e72b57638688069111071a6e0a035c49
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018657"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Controlar o comportamento de cache da CDN do Azure com regras de cache

> [!NOTE] 
> As regras de cache estão disponíveis apenas para perfis de **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**. Para os perfis da **CDN do Azure da Microsoft**, você deve usar o [mecanismo de regras Standard](cdn-standard-rules-engine-reference.md). Para os **perfis da CDN do Azure Premium da Verizon**, você deve usar o [mecanismo de regras Premium da Verizon](./cdn-verizon-premium-rules-engine.md) no portal **Gerenciar** a fim de obter uma funcionalidade semelhante.
 
A CDN (Rede de Distribuição de Conteúdo do Microsoft Azure) oferece duas maneiras de controlar como seus arquivos são armazenados em cache: 

- Regras de cache: esse artigo descreve como é possível utilizar as regras de cache da CDN (rede de distribuição de conteúdo) para definir ou modificar o comportamento de expiração do cache padrão globalmente e com condições personalizadas, como um caminho de URL e extensão de arquivo. CDN do Azure fornece dois tipos de regras de cache:

   - Regras de cache globais: Você pode definir uma regra de cache global para cada ponto de extremidade em seu perfil, o que afeta todas as solicitações para o ponto de extremidade. A regra de cache global substitui todos os cabeçalhos de diretiva de cache HTTP, se configurado.

   - Regras de cache personalizadas: Você pode definir uma ou mais regras de cache personalizadas para cada ponto de extremidade em seu perfil. As regras de cache personalizadas combinam caminhos específicos e extensões de arquivo, são processadas em ordem e substituem a regra de cache global, se definido. 

- Cache da cadeia de caracteres de consulta: é possível ajustar a forma como a CDN do Azure trata o armazenamento em cache para solicitações com cadeias de caracteres de consulta. Para obter mais informações, consulte [Controlar o comportamento do cache da CDN do Azure com cadeias de caracteres de consulta](cdn-query-string.md). Se o arquivo não for armazenável em cache, a configuração de cache da cadeia de caracteres de consulta não terá efeito, com base em regras de cache e comportamentos padrão da CDN.

Para obter informações sobre o comportamento de cache padrão e os cabeçalhos de diretivas de cache, consulte [Como funciona o cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Acessando regras de cache da CDN do Azure

1. Abra o Portal do Azure, selecione um perfil da CDN e selecione um ponto de extremidade.

2. No painel esquerdo em Configurações, selecione **Regras de cache**.

   ![Botão de regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   A página **Regras de cache** é exibida.

   ![Página Regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Configurações de comportamento do cache
Para regras de cache globais e personalizadas, é possível especificar as seguintes configurações de **Comportamento do cache**:

- **Ignorar cache**: não armazena em cache e ignora os cabeçalhos de diretrizes de cache fornecidos pela origem.

- **Substituir**: ignorar a duração do cache fornecida pela origem; em vez disso, use a duração de cache fornecida. Isso não substituirá o Cache-Control: no-cache.

- **Definir se ausente**: aceita os cabeçalhos de diretiva de cache fornecidos pela origem, se existirem; caso contrário, usa a duração do cache fornecida.

![Regras de cache globais](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Personalizar regras de cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duração da expiração do cache
Para regras de cache globais e personalizadas, é possível especificar a duração da expiração do cache em dias, horas, minutos e segundos:

- Para as configurações **Substituir** e **Definir se ausente, configurações** **Comportamento do cache**, as durações de cache válidas variam entre 0 segundos e 366 dias. Por um valor de 0 segundos, o CDN armazena em cache o conteúdo, mas deve revalidar cada solicitação com o servidor de origem.

- Para a configuração **Ignorar cache**, a duração do cache é definida automaticamente em 0 segundos e não pode ser alterada.

## <a name="custom-caching-rules-match-conditions"></a>As regras de cache personalizadas correspondem condições

Para regras de cache personalizadas, há duas condições de correspondência:
 
- **Caminho**: essa condição corresponde ao caminho da URL, excluindo o nome de domínio e fornece suporte ao símbolo curinga (\*). Por exemplo, _/myfile.html_, _/my/folder/*_ e _/my/images/*.jpg_. O comprimento máximo é de 260 caracteres.

- **Extensão**: essa condição corresponde à extensão do arquivo solicitado. Você pode fornecer uma lista de extensões de arquivo separadas por vírgulas para corresponder. Por exemplo, _.jpg_, _.mp3_ ou _.png_. O número máximo de extensões é 50 e o número máximo de caracteres por extensão é 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento global e personalizado de regras
As regras de cache personalizadas e globais são processadas na seguinte ordem:

- As regras de cache globais têm precedência sobre o comportamento de cache padrão da CDN (configurações de cabeçalho de diretiva de cache HTTP). 

- As regras de cache personalizadas têm precedência sobre as regras de cache globais, onde elas se aplicam. As regras de cache personalizadas são processadas de forma geral de cima para baixo. Ou seja, se uma solicitação corresponder às duas condições, as regras na parte inferior da lista prevalecem sobre as regras na parte superior da lista. Portanto, é necessário colocar mais regras específicas na lista.

**Exemplo**:
- Regra de cache global: 
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 1 dia

- Regra de cache individual Nº 1:
   - Condição de correspondência: **Caminho**
   - Valor de correspondência: _/home/*_
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 2 dias

- Regra de cache personalizada Nº 2:
   - Condição de correspondência: **Extensão**
   - Valor de correspondência: _.html_
   - Comportamento do cache: **Definir se ausente**
   - Duração da expiração do cache: 3 dias

Quando essas regras são definidas, uma solicitação para o _&lt; nome &gt; do host do ponto de extremidade_. azureedge.net/Home/index.html dispara a regra de cache Personalizada #2, que é definida como: **definir se ausente** e 3 dias. Portanto, se o arquivo *index.html* tiver `Cache-Control` ou `Expires` cabeçalhos HTTP, eles serão aceitos; caso contrário, se esses cabeçalhos não estiverem configurados, o arquivo será armazenado em cache por 3 dias.

> [!NOTE] 
> Os arquivos que são armazenados em cache antes de uma alteração de regra mantêm a configuração da duração do cache de origem. Para reiniciar as durações de cache, será necessário [limpar o arquivo](cdn-purge-endpoint.md). 
>
> As alterações na configuração da CDN do Azure podem demorar um pouco para serem propagadas pela rede: 
> - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
> - Para perfis da **CDN Standard do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos.  
>

## <a name="see-also"></a>Veja também

- [Como funciona o cache](cdn-how-caching-works.md)
- [Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)