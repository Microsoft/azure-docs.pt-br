---
title: Suporte a compartilhamento de recursos entre origens (CORS) | Microsoft Docs
description: "Saiba como habilitar o suporte de CORS para os serviços de armazenamento do Microsoft Azure."
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Suporte para o compartilhamento de recursos entre origens (CORS) para os serviços de armazenamento do Azure
Os serviços de armazenamento do Azure, da versão 2013-08-15 em diante, dão suporte a CORS (compartilhamento de recursos entre origens) para os serviços de Blob, Tabela e Fila. O CORS é um recurso HTTP que permite que um aplicativo web em execução em um domínio acesse recursos em outro domínio. Navegadores da Web implementam uma restrição de segurança, conhecida como [política de mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy), que impede uma página da Web de chamar APIs em um domínio diferente. O CORS fornece uma maneira segura para permitir que um domínio (o domínio de origem) chame APIs em outro domínio. Confira a [Especificação CORS](http://www.w3.org/TR/cors/) para obter detalhes sobre o CORS.

Você pode definir regras de CORS individualmente para cada um dos serviços de armazenamento, chamando [Definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452232.aspx) e [Definir propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452240.aspx). Depois de definir as regras CORS para o serviço, uma solicitação autenticada corretamente feita no serviço de um domínio diferente será avaliada para determinar se é permitida de acordo com as regras que você especificou.

> [!NOTE]
> Observe que CORS não é um mecanismo de autenticação. Qualquer solicitação feita em um recurso de armazenamento quando CORS estiver habilitado deve ter uma assinatura de autenticação adequada ou deverá ser feita em um recurso público.
> 
> 

## <a name="understanding-cors-requests"></a>Noções básicas sobre solicitações CORS
Uma solicitação CORS de um domínio de origem pode consistir em duas solicitações separadas:

* Uma solicitação de simulação, que consulta as restrições de CORS impostas pelo serviço. A solicitação de simulação é necessária, a menos que o método de solicitação é um [método simples](http://www.w3.org/TR/cors/), significando GET, HEAD ou POST.
* A solicitação real, feita no recurso desejado.

### <a name="preflight-request"></a>Solicitação de simulação
A solicitação de simulação consulta as restrições de CORS que foram estabelecidas para o serviço de armazenamento pelo proprietário da conta. O navegador da web (ou outro agente do usuário) envia uma solicitação OPTIONS que inclui os cabeçalhos de solicitação, o domínio de origem e de método. O serviço de armazenamento avalia a operação pretendida com base em um conjunto pré-configurado de regras CORS que especificam quais domínios de origem, métodos de solicitação e cabeçalhos de solicitação podem ser especificados em uma solicitação real em relação a um recurso de armazenamento.

Se CORS estiver habilitado para o serviço e houver uma regra CORS que corresponda à solicitação de simulação, o serviço responderá com o código de status 200 (OK) e incluirá os cabeçalhos de controle de acesso necessários na resposta.

Se CORS não estiver habilitado para o serviço ou nenhuma regra CORS corresponder à solicitação de simulação, o serviço responderá com o código de status 403 (proibido).

Se a solicitação OPTIONS não contiver os cabeçalhos de CORS obrigatórios (os cabeçalhos Origin e Access-Control-Request-Method), o serviço responderá com o código de status 400 (solicitação incorreta).

Observe que uma solicitação de simulação é avaliada em relação ao serviço (Blob, fila e tabela) e não em relação ao recurso solicitado. O proprietário da conta deve ter CORS habilitado como parte das propriedades do serviço de conta para que a solicitação seja bem-sucedida.

### <a name="actual-request"></a>Solicitação real
Depois que a solicitação de simulação é aceita e a resposta é retornada, o navegador enviará a solicitação real em relação ao recurso de armazenamento. O navegador negará a solicitação real imediatamente se a solicitação de simulação for rejeitada.

A solicitação real é tratada como uma solicitação normal no serviço de armazenamento. A presença do cabeçalho Origin indica que a solicitação é uma solicitação de CORS e o serviço verificará as regras CORS correspondentes. Se uma correspondência for encontrada, os cabeçalhos de controle de acesso são adicionados à resposta e enviados de volta ao cliente. Se uma correspondência não for encontrada, os cabeçalhos de controle de acesso do CORS não serão retornados.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Habilitando CORS para os serviços de armazenamento do Azure
As regras de CORS são definidas no nível de serviço, por isso você precisa habilitar ou desabilitar o CORS para cada serviço (Blob, fila e tabela) separadamente. Por padrão, o CORS está desabilitado para cada serviço. Para habilitar o CORS, você precisa definir as propriedades de serviço apropriadas usando a versão 2013-08-15 ou posterior e adicionar regras de CORS às propriedades do serviço. Para obter detalhes sobre como habilitar ou desabilitar o CORS para um serviço e como definir regras de CORS, consulte [Definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452232.aspx) e [Definir propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452240.aspx).

Aqui está um exemplo de uma única regra CORS, especificada por meio de uma operação de definir propriedades do serviço:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Cada elemento incluído na regra de CORS é descrito abaixo:

* **AllowedOrigins**: os domínios de origem que têm permissão para fazer uma solicitação no serviço de armazenamento por meio de CORS. O domínio de origem é o domínio do qual se origina a solicitação. Observe que a origem deve ser uma correspondência exata de maiúsculas e minúsculas com a origem que a idade do usuário envia para o serviço. Você também pode usar o caractere curinga '*' para permitir que todos os domínios de origem façam solicitações por CORS. No exemplo acima, os domínios [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) podem fazer solicitações no serviço usando CORS.
* **AllowedMethods**: os métodos (verbos de solicitação HTTP) que podem usar o domínio de origem para uma solicitação CORS. No exemplo acima, somente as solicitações PUT e GET são permitidas.
* **AllowedHeaders**: os cabeçalhos de solicitação que o domínio de origem pode especificar na solicitação de CORS. No exemplo acima, todos os cabeçalhos de metadados, começando com x-ms-meta-datas, x-ms-meta-target e x-ms-meta-abc são permitidos. Observe que o caractere curinga '*' indica que todos os cabeçalhos que começam com o prefixo especificado são permitidos.
* **ExposedHeaders**: os cabeçalhos de resposta que podem ser enviados em resposta à solicitação de CORS e expostos pelo navegador para o emissor da solicitação. No exemplo acima, o navegador é instruído para expor quaisquer cabeçalhos que começam com x-ms-meta.
* **MaxAgeInSeconds**: a quantidade máxima de tempo pela qual um navegador deve armazenar em cache a solicitação OPTIONS de simulação.

Os serviços de armazenamento do Azure oferecem suporte, especificando cabeçalhos prefixados para ambos os elementos **AllowedHeaders** e **ExposedHeaders**. Para permitir uma categoria de cabeçalhos, você pode especificar um prefixo comum a essa categoria. Por exemplo, especificar *x-ms-meta** como um cabeçalho prefixado estabelece uma regra que corresponderá a todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações se aplicam a regras de CORS:

* Você pode especificar até cinco regras de CORS por serviço de armazenamento (Blob, tabela e fila).
* O tamanho máximo de todas as configurações das regras de CORS na solicitação, excluindo as marcas XML, não deve exceder 2 KB.
* O comprimento de um cabeçalho permitido, cabeçalho exposto ou origem permitida não deve exceder 256 caracteres.
* Cabeçalhos expostos e cabeçalhos permitidos podem ser:
  * Cabeçalhos literais, nos quais o nome exato do cabeçalho é fornecido, como **x-ms-meta-processed**. Um máximo de 64 cabeçalhos literais pode ser especificado na solicitação.
  * Cabeçalhos prefixados, nos quais um prefixo do cabeçalho, como **x-ms-meta-data***, é fornecido. Especificando um prefixo dessa maneira permite ou expõe qualquer cabeçalho que começa com o prefixo especificado. Um máximo de dois cabeçalhos prefixados pode ser especificado na solicitação.
* Os métodos (ou verbos HTTP) especificados no elemento **AllowedMethods** devem estar em conformidade com os métodos aos quais as APIs do serviço de armazenamento do Azure oferecem suporte. Métodos com suporte são DELETE, GET, HEAD, MERGE, POST, OPTIONS e PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Noções básicas sobre a lógica de avaliação da regra de CORS
Quando um serviço de armazenamento recebe uma solicitação de simulação ou real, ele avalia essa solicitação com base nas regras de CORS estabelecidas para o serviço por meio da operação Definir propriedades de serviço apropriada. As regras de CORS são avaliadas na ordem em que foram definidas no corpo da solicitação da operação de Definir propriedades de serviço.

As regras de CORS são avaliadas da seguinte maneira:

1. Primeiro, o domínio de origem da solicitação é verificado nos domínios listados para o elemento **AllowedOrigins** . Se o domínio de origem está incluído na lista ou todos os domínios são permitidos com o caractere curinga '*', então a avaliação de regras continua. Se o domínio de origem não for incluído, então a solicitação falhará.
2. Em seguida, o método (ou verbo HTTP) da solicitação é verificado em relação aos métodos listados no elemento **AllowedMethods**. Se o método estiver incluído na lista, então a avaliação de regras continua. Caso contrário, a solicitação falhará.
3. Se a solicitação corresponde a uma regra no seu domínio de origem e seu método, essa regra estará selecionada para processar a solicitação e nenhuma regra adicional é avaliada. Antes que a solicitação possa ser bem-sucedida, no entanto, todos os cabeçalhos especificados na solicitação são comparados aos cabeçalhos listados no elemento **AllowedHeaders** . Se os cabeçalhos enviados não corresponderem aos cabeçalhos permitidos, a solicitação falhará.

As regras são processadas na ordem em que estiverem presentes no corpo da solicitação, práticas recomendadas recomendam que você especifique as regras mais restritivas em relação às primeiras origens na lista, para que elas sejam avaliadas primeiro. Especifica regras que são menos restritivas – por exemplo, uma regra para permitir todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo – avaliação das regras de CORS
O exemplo a seguir mostra um corpo de solicitação parcial para a operação para definir regras de CORS para os serviços de armazenamento. Confira [Definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452232.aspx) e [Definir propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre como construir a solicitação.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Em seguida, considere as seguintes solicitações CORS:

| Solicitação |  |  | Resposta |  |
| --- | --- | --- | --- | --- |
| **Método** |**Origem** |**Cabeçalhos da solicitação** |**Correspondência de regra** |**Resultado** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Primeira regra |Sucesso |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Segunda regra |Sucesso |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Segunda regra |Failure |

A primeira solicitação corresponde à primeira regra – o domínio de origem corresponde às origens permitidas, o método corresponde aos métodos permitidos e o cabeçalho corresponde aos cabeçalhos permitidos – e então tem êxito.

A segunda solicitação não corresponde à primeira regra porque o método não corresponde aos métodos permitidos. No entanto, ele corresponde à segunda regra, portanto, tem êxito.

A terceira solicitação corresponde à segunda regra em seu domínio de origem e método, portanto nenhuma regra adicional será avaliada. No entanto, o *cabeçalho x-ms-client-request-id* não é permitido pela segunda regra; assim, embora a semântica da terceira não impeça seu êxito, a solicitação falha.

> [!NOTE]
> Embora esse exemplo mostra uma regra menos restritiva antes de uma mais restritiva, em geral a prática recomendada é listar as regras mais restritivas primeiro.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Noções básicas sobre como o cabeçalho Vary é definido
O cabeçalho *Vary* é um cabeçalho HTTP/1.1 padrão, que consiste em um conjunto de campos de cabeçalho de solicitação que fazem recomendações ao navegador ou ao agente do usuário sobre os critérios selecionados pelo servidor para processar a solicitação. O cabeçalho *Vary* é usado principalmente para armazenamento em cache por proxies, navegadores e CDNs, que utilizam-no para determinar como a resposta deve ser armazenada em cache. Para obter detalhes, consulte a especificação para o [cabeçalho Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o navegador ou outro agente de usuário armazena em cache a resposta de uma solicitação CORS, o domínio de origem é armazenado em cache como a origem permitida. Quando um segundo domínio emite a mesma solicitação para um recurso de armazenamento enquanto o cache está ativo, o agente do usuário recupera o domínio de origem armazenado em cache. O segundo domínio corresponde ao domínio armazenado em cache, portanto, a solicitação falhará quando ele, por outro lado, tiver êxito. Em alguns casos, o armazenamento do Azure define o cabeçalho Vary como **Origin** para instruir o agente do usuário a enviar a solicitação subsequente de CORS para o serviço quando o domínio de solicitação difere da origem armazenada em cache.

O Armazenamento do Azure define o cabeçalho *Vary* como **Origin** para solicitações GET/HEAD atuais nos seguintes casos:

* Quando a origem da solicitação corresponde exatamente à origem permitida definida por uma regra CORS. Para ser uma correspondência exata, a regra CORS pode não incluir um caractere curinga ' * '.
* Não há nenhuma regra correspondendo com a origem da solicitação, mas CORS está habilitado para o serviço de armazenamento.

No caso em que uma solicitação GET/HEAD corresponder a uma regra CORS que permite todas as origens, a resposta indica que todas as origens são permitidas, e o cache do agente do usuário permitirá solicitações subsequentes de qualquer domínio de origem quando o cache estiver ativo.

Observe que para solicitações que usam métodos diferentes de GET/HEAD, os serviços de armazenamento não definirão o cabeçalho Vary, desde que as respostas para esses métodos não sejam armazenadas em cache por agentes de usuário.

A tabela a seguir indica como o armazenamento do Azure responderá às solicitações GET/HEAD com base nos casos previamente mencionados:

| Solicitação | Configuração da conta e o resultado da avaliação da regra |  |  | Resposta |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Cabeçalho da origem presente na solicitação** |**Regra(s) de CORS especificada(s) para este serviço** |**Existe uma regra correspondente que permite todas as origens(*)** |**Regra de correspondência existe para correspondência exata da origem** |**Resposta inclui o cabeçalho Vary definido como origem** |**Resposta inclui Access-Control-Allowed-Origin: "*"** |**Resposta inclui Access-Control-Exposed-Headers** |
| Não |Não |Não |Não |Não |Não |Não |
| Não |Sim |Não |Não |Sim |Não |Não |
| Não |Sim |Sim |Não |Não |Sim |Sim |
| Sim |Não |Não |Não |Não |Não |Não |
| Sim |Sim |Não |Sim |Sim |Não |Sim |
| Sim |Sim |Não |Não |Sim |Não |Não |
| Sim |Sim |Sim |Não |Não |Sim |Sim |

## <a name="billing-for-cors-requests"></a>Cobrança para solicitações CORS
Solicitações de simulação com êxito são cobradas se você tiver CORS habilitado para qualquer um dos serviços de armazenamento para sua conta (chamando [Definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452232.aspx) ou [Definir propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar encargos, é recomendável configurar o elemento **MaxAgeInSeconds** em suas regras CORS para um valor grande para que o agente do usuário armazene a solicitação em cache.

Solicitações de simulação malsucedidas não serão cobradas.

## <a name="next-steps"></a>Próximas etapas
[Definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir propriedades do serviço Fila](https://msdn.microsoft.com/library/hh452232.aspx)

[Definir propriedades do serviço Tabela](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificação de compartilhamento de recursos entre origens W3C](http://www.w3.org/TR/cors/)

