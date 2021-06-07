---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
title: Pontos de extremidade de streaming (origem): Serviços de Mídia do Microsoft Azure description: Saiba mais sobre os Pontos de extremidade de streaming (origem), um serviço de streaming e empacotamento dinâmico que fornece conteúdo diretamente a um aplicativo de reprodução do cliente ou a uma CDN (Rede de Distribuição de Conteúdo). services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 02/13/2020 ms.author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Ponto de extremidade de streaming (origem) nos Serviços de Mídia do Azure

Nos Serviços de Mídia do Microsoft Azure, um [Ponto de extremidade de streaming](/rest/api/media/streamingendpoints) representa um serviço de origem e empacotamento dinâmico (just-in-time) que pode fornecer seu conteúdo ao vivo e sob demanda diretamente para um aplicativo de reprodução de cliente usando um dos protocolos de mídia de transmissão comuns (HLS ou DASH). Além disso, o **Ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para DRMs líderes do setor. 

Quando você cria uma conta de Serviços de Mídia, um Ponto de Extremidade de Streaming **padrão** é criado em um estado parado. Mais Pontos de extremidade de streaming podem ser criados na conta (confira [Cotas e limites](limits-quotas-constraints-reference.md)).

> [!NOTE]
> Para começar a transmitir vídeos, é necessário iniciar o **Ponto de extremidade de streaming** do qual deseja transmitir o vídeo.
>
> Você só é cobrado quando o Ponto de extremidade de streaming está no estado de execução.

Confira também o tópico sobre [Empacotamento dinâmico](encode-dynamic-packaging-concept.md). 

## <a name="naming-convention"></a>Convenção de nomenclatura

O formato do nome de host da URL de streaming é: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`, em que `servicename` = o nome do ponto de extremidade de streaming ou o nome do evento ao vivo.

Ao usar o ponto de extremidade de streaming padrão, `servicename` é omitido, de modo que a URL é: `{accountname}-{regionname}.streaming.azure.net`.

### <a name="limitations"></a>Limitações

* O nome do ponto de extremidade de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este padrão de [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference): `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

## <a name="types"></a>Tipos

Há dois tipos de **Ponto de extremidade de streaming**: **Standard** (versão prévia) e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) alocadas para o ponto de extremidade de streaming.

Geralmente, o limite máximo de unidades de streaming é 10. Entre em contato conosco [aqui](https://azure.microsoft.com/support/create-ticket/) para aumentar o limite de sua conta.

A tabela descreve os tipos:

|Tipo|Unidades de escala|Descrição|
|--------|--------|--------|  
|**Standard**|0|O Ponto de extremidade de streaming padrão é um tipo **Standard**. Ele pode ser alterado para o tipo Premium ajustando `scaleUnits`.|
|**Premium**|>0|Os Pontos de extremidade de streaming **Premium** são adequados para cargas de trabalho avançadas e fornecem uma capacidade de largura de banda dimensionável e dedicada. É possível mudar para um tipo **Premium** ajustando `scaleUnits` (unidades de streaming). `scaleUnits` fornece capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Ao usar o tipo **Premium**, cada unidade habilitada fornece capacidade adicional de largura de banda ao aplicativo. |

> [!NOTE]
> Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos a CDN seja habilitada no Ponto de extremidade de streaming.

Para obter informações sobre o SLA, confira [Preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

Recurso|Standard|Premium
---|---|---
Produtividade |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por UA (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|CDN do Azure, CDN de terceiros ou nenhuma CDN.|CDN do Azure, CDN de terceiros ou nenhuma CDN.
A cobrança é rateada| Diariamente|Diariamente
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|SUs adicionais
Filtragem de IP/G20/Host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> Usado apenas diretamente no Ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|ScaleUnits|CDN|Cobrança|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássico|1.0|0|NA|Gratuita|
|Ponto de extremidade de streaming Standard (versão prévia)|2,0|0|Sim|Pago|
|Unidades de Streaming Premium|1.0|>0|Yes|Pago|
|Unidades de Streaming Premium|2,0|>0|Yes|Pago|

### <a name="features"></a>Recursos

Recurso|Standard|Premium
---|---|---
Produtividade |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por UA (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|Azure CDN, CDN de terceiros ou sem CDN.|Azure CDN, CDN de terceiros ou sem CDN.
A cobrança é rateada| Diariamente|Diariamente
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|Unidades de streaming adicionais.
Filtragem de IP/G20/Host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> Usado apenas diretamente no Ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

Para obter informações sobre o SLA, confira [Preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Ação
---|---|---
Clássico|Standard|Necessidade de aceitação
Clássico|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássico|Não disponível (se a versão do ponto de extremidade de streaming for 1.0. É permitido alterar para o clássico configurando scaleunits para “0”)
Standard (com/sem CDN)|Premium com as mesmas configurações|Permitido no estado **iniciado**. (por meio do Portal do Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (por meio do Portal do Azure)
Standard (com/sem CDN)|Premium com configuração diferente|Permitido no estado **parado** (por meio do Portal do Azure). Não permitido no estado de execução.
Premium (com/sem CDN)|Standard com configuração diferente|Permitido no estado **parado** (por meio do Portal do Azure). Não permitido no estado de execução.
Versão 1.0 com UA > = 1 com CDN|Standard/Premium sem qualquer CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**.
Versão 1.0 com UA > = 1 com CDN|Standard com/sem CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**. A CDN da Versão 1.0 será excluída e uma nova será criada e iniciada.
Versão 1.0 com UA > = 1 com CDN|Premium com/sem CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**. A CDN Clássica será excluída e uma nova será criada e iniciada.







## <a name="streaming-endpoint-properties"></a>Propriedades de Ponto de extremidade de streaming

Esta seção fornece detalhes sobre algumas das propriedades do Ponto de extremidade de streaming. Para exemplos de como criar um novo ponto de extremidade de streaming e descrições de todas as propriedades, consulte [Ponto de Extremidade de Streaming](/rest/api/media/streamingendpoints/create).

- `accessControl`: usado para definir as seguintes configurações de segurança para este ponto de extremidade de streaming: chaves da Autenticação de cabeçalho de assinatura Akamai e endereços IP com permissão para conectar esse ponto de extremidade. Esta propriedade pode ser definida somente quando `cdnEnabled` está definido como false.

- `cdnEnabled`: indica se a integração da CDN do Azure para este ponto de extremidade de streaming está habilitada (desabilitada por padrão). Se você definir `cdnEnabled` como verdadeiro, as seguintes configurações serão desabilitadas: `customHostNames` e `accessControl`.

    Nem todos os data centers dão suporte para integração da CDN do Azure. Para verificar se o data center tem a integração da CDN do Azure disponível, execute as seguintes etapas:

  - Tente definir a `cdnEnabled` como true.
  - Verifique o resultado retornado para um `HTTP Error Code 412` (PreconditionFailed) com uma mensagem de "A propriedade CdnEnabled do ponto de extremidade de streaming não pode ser configurada como verdadeira, pois o recurso de CDN não está disponível na região atual".

    Se você recebe esse erro, o data center não dá suporte. Tente outro data center.

- `cdnProfile`: quando `cdnEnabled` é definido como verdadeiro, você também pode passar valores `cdnProfile`. `cdnProfile` é o nome do perfil CDN no qual o ponto de extremidade CDN será criado. Você pode fornecer um cdnProfile existente ou usar um novo. Se o valor for NULL e `cdnEnabled` for verdadeiro, o valor padrão "AzureMediaStreamingPlatformCdnProfile" será usado. Se o perfil `cdnProfile` fornecido já existir, um ponto de extremidade será criado sob ele. Se o perfil não existir, será criado um automaticamente.
- `cdnProvider`: quando a CDN for habilitada, você também poderá passar valores `cdnProvider`. `cdnProvider` controla qual provedor será usado. Atualmente, há suporte para três valores: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for fornecido e `cdnEnabled` for verdadeiro, "StandardVerizon" será usado (esse é o valor padrão).
- `crossSiteAccessPolicies`: usado para especificar políticas de acesso entre sites para vários clientes. Para obter mais informações, consulte [Especificação de arquivo de política entre domínios](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [Disponibilizando um serviço entre limites de domínios](/previous-versions/azure/azure-services/gg185950(v=azure.100)). As configurações se aplicam somente ao Smooth Streaming.
- `customHostNames`: usado para configurar um Ponto de extremidade de streaming para aceitar o tráfego direcionado para um nome do host personalizado. Essa propriedade é válida para pontos de extremidade de streaming Standard e Premium e pode ser definida quando `cdnEnabled`: false.

    A propriedade do nome de domínio deve ser confirmada pelos Serviços de Mídia. Os Serviços de Mídia verificam a propriedade do nome de domínio exigindo que um registro `CName` contendo a ID da conta de Serviços de Mídia do Azure como um componente seja adicionado ao domínio em uso. Por exemplo, para "sports.contoso.com" ser usado como um nome do host personalizado para o ponto de extremidade de streaming, um registro para `<accountId>.contoso.com` deve ser configurado para apontar para um dos nomes do host de verificação dos Serviços de Mídia. O nome do host de verificação é composto por verifydns.`\<mediaservices-dns-zone>`.

    Veja a seguir as zonas DNS que devem ser usadas no registro de verificação para diferentes regiões do Azure.
  
  - América do Norte, Europa, Singapura, RAE de Hong Kong, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, um registro `CName` que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" prova que a ID dos Serviços de Mídia 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo que qualquer nome sob contoso.com seja usado como um nome do host personalizado para um ponto de extremidade de streaming sob essa conta. Para localizar o valor da ID do Serviço de Mídia, acesse o [portal do Azure](https://portal.azure.com/) e selecione a conta de Serviço de Mídia. A **ID da conta** aparece no canto superior direito da página.

    Se houver uma tentativa de definir um nome do host personalizado sem verificação adequada do registro `CName`, a resposta DNS falhará e será armazenada em cache por algum tempo. Depois que um registro adequado estiver em vigor, poderá demorar um pouco até que a resposta armazenada em cache seja revalidada. Dependendo do provedor DNS do domínio personalizado, pode levar de alguns minutos a uma hora para revalidar o registro.

    Além do `CName` que mapeia `<accountId>.<parent domain>` para `verifydns.<mediaservices-dns-zone>`, você deve criar outro `CName` que mapeia o nome do host personalizado (por exemplo, `sports.contoso.com`) para o nome do host do Ponto de extremidade de streaming dos Serviços de Mídia (por exemplo, `amstest-usea.streaming.media.azure.net`).

    > [!NOTE]
    > Pontos de extremidade de streaming localizados no mesmo data center não podem compartilhar o mesmo nome do host personalizado.

    Atualmente, os Serviços de Mídia não são compatíveis com o TLS com domínios personalizados.

- `maxCacheAge` – substitui o cabeçalho de controle de cache HTTP max-age padrão definido pelo ponto de extremidade de streaming em fragmentos de mídia e manifestos sob demanda. O valor é definido em segundos.
- `resourceState` -

    - Parado: o estado inicial de um Ponto de extremidade de streaming após a criação
    - Iniciando: está em transição para o estado de execução
    - Executando: habilitado a transmitir conteúdo aos clientes
    - Escala: as unidades de escala estão sendo aumentadas ou diminuídas
    - Parando: está em transição para o estado parado
    - Excluindo: está sendo excluído

- `scaleUnits`: fornece capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Se você precisar mudar para um tipo **Premium**, ajuste `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Por que usar vários pontos de extremidade de streaming?

Um mesmo ponto de extremidade de streaming pode transmitir vídeos ao vivo e sob demanda e a maioria dos clientes usa apenas um ponto de extremidade de streaming. Esta seção fornece alguns exemplos de por que você pode precisar usar vários pontos de extremidade de streaming.

* Cada unidade reservada permite 200 Mbps de largura de banda. Se precisar de mais de 2.000 Mbps (2 Gbps) de largura de banda, você poderá usar o segundo ponto de extremidade de streaming e balancear a carga para obter largura de banda adicional.

    A CDN é a melhor maneira de escalar horizontalmente o conteúdo de streaming, mas se você estiver fornecendo conteúdo a ponto de a CDN efetuar pull de mais de 2 Gbps, você poderá adicionar outros pontos de extremidade de streaming (origens). Nesse caso, você precisaria distribuir URLs de conteúdo balanceadas entre os dois pontos de extremidade de streaming. Essa abordagem fornece um melhor cache do que tentar enviar solicitações para cada origem aleatoriamente (por exemplo, por meio de um gerenciador de tráfego). 
    
    > [!TIP]
    > Geralmente, se a CDN estiver efetuando pull de mais de 2 Gbps, algo poderá estar configurado incorretamente (por exemplo, sem blindagem de origem).
    
* Balanceamento de carga de diferentes provedores de CDN. Por exemplo, você pode configurar o ponto de extremidade de streaming padrão para usar a CDN da Verizon e criar um segundo para usar a Akamai. Em seguida, adicione balanceamento de carga entre os dois para alcançar o balanceamento de múltiplas CDNs. 

    No entanto, o cliente geralmente faz o balanceamento de carga entre vários provedores de CDN usando uma só origem.
* Streaming de conteúdo misto: vídeo por demanda e ao vivo. 

    Os padrões de acesso de conteúdos ao vivo e sob demanda são muito diferentes. O conteúdo ao vivo tende a receber muita demanda pelo mesmo conteúdo de uma só vez. O conteúdo de vídeo por demanda (conteúdo de arquivo de cauda longa, por exemplo) tem baixo uso do mesmo conteúdo. Portanto, o cache funciona muito bem para conteúdos ao vivo, mas nem tanto para conteúdo de cauda longa.

    Considere um cenário em que os clientes assistem principalmente ao conteúdo ao vivo e assistem ocasionalmente ao conteúdo sob demanda, que é servido do mesmo ponto de extremidade de streaming. O baixo uso do conteúdo sob demanda ocuparia o espaço em cache que deveria ser dedicado ao conteúdo ao vivo. Nesse cenário, recomendamos fornecer o conteúdo ao vivo de um Ponto de extremidade de streaming e o conteúdo de cauda longa de outro. Isso aprimora o desempenho do conteúdo do evento ao vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Dimensionando o streaming com CDN

Veja os artigos a seguir:

- [Visão geral da CDN](../../cdn/cdn-overview.md)
- [Dimensionando o streaming com CDN](stream-scale-streaming-cdn-concept.md)

## <a name="ask-questions-and--get-updates"></a>Fazer perguntas e receber atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

[Empacotamento dinâmico](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar pontos de extremidade de streaming](stream-manage-streaming-endpoints-how-to.md)
