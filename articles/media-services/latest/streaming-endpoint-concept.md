---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais em aka.ms/skyeye/meta.
Título: pontos de extremidade de streaming (origem): descrição dos serviços de mídia do Azure: Saiba mais sobre os pontos de extremidade de streaming (origem), um serviço de empacotamento dinâmico e streaming que fornece conteúdo diretamente a um aplicativo de Player cliente ou a uma CDN (rede de distribuição de conteúdo). serviços: Media-Services documentationcenter: ' ' autor: IngridAtMicrosoft gerente: femila editor: ' '

MS. Service: Media-Services MS. Workload: MS. Topic: conceitual MS. Date: 02/13/2020 MS. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Pontos de extremidade de streaming (origem) nos serviços de mídia do Azure

No Serviços de Mídia do Microsoft Azure, um [ponto de extremidade de streaming](/rest/api/media/streamingendpoints) representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente usando um dos protocolos de mídia de streaming comuns (HLS ou Dash). Além disso, o **ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para DRMs líderes do setor. 

Quando você cria uma conta de Serviços de Mídia, um Ponto de Extremidade de Streaming **padrão** é criado em um estado parado. Mais pontos de extremidade de streaming podem ser criados na conta (consulte [cotas e limites](limits-quotas-constraints.md)).

> [!NOTE]
> Para começar a transmitir vídeos, é necessário iniciar o **Ponto de extremidade de streaming** do qual deseja transmitir o vídeo.
>
> Você será cobrado somente quando o ponto de extremidade de streaming estiver no estado executando.

Certifique-se também de examinar o tópico de [empacotamento dinâmico](dynamic-packaging-overview.md) . 

## <a name="naming-convention"></a>Convenção de nomenclatura

O formato de nome de host da URL de streaming é: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , onde `servicename` = o nome do ponto de extremidade de streaming ou o nome do evento ao vivo.

Ao usar o ponto de extremidade de streaming padrão, `servicename` é omitido para que a URL seja: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Limitações

* O nome do ponto de extremidade de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este padrão de [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Tipos

Há dois tipos de **ponto de extremidade de streaming** : **Standard** (visualização) e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) alocadas para o ponto de extremidade de streaming.

O limite máximo de unidades de streaming é, em geral, 10. Entre em contato conosco [aqui](https://azure.microsoft.com/support/create-ticket/) para aumentar o limite da sua conta.

A tabela descreve os tipos:

|Tipo|Unidades de escala|Descrição|
|--------|--------|--------|  
|**Standard**|0|O ponto de extremidade de streaming padrão é um tipo **padrão** — ele pode ser alterado para o tipo Premium ajustando `scaleUnits` .|
|**Premium**|>0|**Premium** Os pontos de extremidade de streaming são adequados para cargas de trabalho avançadas e para fornecer capacidade de largura de banda dedicada e escalonável. Você passa para um tipo **Premium** ajustando `scaleUnits` (unidades de streaming). `scaleUnits` fornece capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Ao usar o tipo **Premium** , cada unidade habilitada fornece capacidade de largura de banda adicional para o aplicativo. |

> [!NOTE]
> Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos que você habilite a CDN no ponto de extremidade de streaming.

Para obter informações de SLA, consulte [preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

Recurso|Standard|Premium
---|---|---
Produtividade |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por UA (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|CDN do Azure, CDN de terceiros ou nenhuma CDN.|CDN do Azure, CDN de terceiros ou nenhuma CDN.
A cobrança é rateada| Diariamente|Diariamente
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|SUs adicional
Filtragem de IP/G20/host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> é usado somente diretamente no ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

## <a name="streaming-endpoint-properties"></a>Propriedades de ponto de extremidade de streaming

Esta seção fornece detalhes sobre algumas das propriedades do ponto de extremidade de streaming. Para exemplos de como criar um novo ponto de extremidade de streaming e descrições de todas as propriedades, consulte [Ponto de Extremidade de Streaming](/rest/api/media/streamingendpoints/create).

- `accessControl`: Usado para definir as seguintes configurações de segurança para este ponto de extremidade de streaming: chaves de autenticação de cabeçalho de assinatura Akamai e endereços IP que têm permissão para se conectar a esse ponto de extremidade. Essa propriedade só pode ser definida quando `cdnEnabled` é definida como false.

- `cdnEnabled`: Indica se a integração da CDN do Azure para este ponto de extremidade de streaming está habilitada (desabilitada por padrão). Se você definir `cdnEnabled` como verdadeiro, as seguintes configurações serão desabilitadas: `customHostNames` e `accessControl`.

    Nem todos os data centers dão suporte para integração da CDN do Azure. Para verificar se o data center tem a integração da CDN do Azure disponível, execute as seguintes etapas:

  - Tente definir a `cdnEnabled` como true.
  - Verifique o resultado retornado para um `HTTP Error Code 412` (PreconditionFailed) com uma mensagem de "a propriedade CdnEnabled do ponto de extremidade de streaming não pode ser definida como true, pois a funcionalidade CDN não está disponível na região atual".

    Se você receber esse erro, o data center não oferece suporte a ele. Tente outra data center.

- `cdnProfile`: Quando `cdnEnabled` é definido como true, você também pode passar `cdnProfile` valores. `cdnProfile` é o nome do perfil CDN no qual o ponto de extremidade CDN será criado. Você pode fornecer um cdnProfile existente ou usar um novo. Se o valor for NULL e `cdnEnabled` for verdadeiro, o valor padrão "AzureMediaStreamingPlatformCdnProfile" será usado. Se o perfil `cdnProfile` fornecido já existir, um ponto de extremidade será criado sob ele. Se o perfil não existir, um novo perfil será criado automaticamente.
- `cdnProvider`: Quando a CDN está habilitada, você também pode passar `cdnProvider` valores. `cdnProvider` controla qual provedor será usado. Atualmente, há suporte para três valores: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for fornecido e `cdnEnabled` for true, "StandardVerizon" será usado (esse é o valor padrão).
- `crossSiteAccessPolicies`: Usado para especificar políticas de acesso entre sites para vários clientes. Para obter mais informações, consulte [Especificação de arquivo de política entre domínios](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [Disponibilizando um serviço entre limites de domínios](/previous-versions/azure/azure-services/gg185950(v=azure.100)). As configurações se aplicam somente a Smooth Streaming.
- `customHostNames`: Usado para configurar um ponto de extremidade de streaming para aceitar o tráfego direcionado para um nome de host personalizado. Essa propriedade é válida para pontos de extremidade de streaming Standard e Premium e pode ser definida quando `cdnEnabled` : false.

    A propriedade do nome de domínio deve ser confirmada pelos serviços de mídia. Os serviços de mídia verificam a propriedade do nome de domínio exigindo um `CName` registro que contém a ID da conta dos serviços de mídia como um componente a ser adicionado ao domínio em uso. Por exemplo, para "sports.contoso.com" ser usado como um nome do host personalizado para o ponto de extremidade de streaming, um registro para `<accountId>.contoso.com` deve ser configurado para apontar para um dos nomes do host de verificação dos Serviços de Mídia. O nome do host de verificação é composto de verifydns. \<mediaservices-dns-zone> .

    A seguir estão as zonas DNS esperadas a serem usadas no registro de verificação para diferentes regiões do Azure.
  
  - América do Norte, Europa, Cingapura, RAE de Hong Kong, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, um `CName` registro que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.Media.Azure.net" comprova que a ID de serviços de mídia 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo assim que qualquer nome em contoso.com seja usado como um nome de host personalizado para um ponto de extremidade de streaming nessa conta. Para localizar o valor da ID do Serviço de Mídia, acesse o [portal do Azure](https://portal.azure.com/) e selecione a conta de Serviço de Mídia. A **ID da conta** aparece no canto superior direito da página.

    Se houver uma tentativa de definir um nome de host personalizado sem uma verificação adequada do `CName` registro, a resposta DNS falhará e será armazenada em cache por algum tempo. Depois que um registro adequado estiver em vigor, poderá demorar um pouco até que a resposta armazenada em cache seja revalidada. Dependendo do provedor DNS para o domínio personalizado, levará de alguns minutos a uma hora para revalidar o registro.

    Além do `CName` que mapeia para o `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>` , você deve criar outro `CName` que mapeie o nome de host personalizado (por exemplo, `sports.contoso.com` ) para o nome de host do ponto de extremidade de streaming dos serviços de mídia (por exemplo, `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Os pontos de extremidade de streaming localizados no mesmo data center não podem compartilhar o mesmo nome de host personalizado.

    Atualmente, os Serviços de Mídia não são compatíveis com o TLS com domínios personalizados.

- `maxCacheAge` -Substitui o cabeçalho de controle de cache HTTP Max-age padrão definido pelo ponto de extremidade de streaming em fragmentos de mídia e manifestos sob demanda. O valor é definido em segundos.
- `resourceState` -

    - Stopped: o estado inicial de um ponto de extremidade de streaming após a criação
    - Iniciando: está fazendo a transição para o estado de execução
    - Em execução: é capaz de transmitir conteúdo para clientes
    - Dimensionamento: as unidades de escala estão sendo aumentadas ou diminuídas
    - Parando: está fazendo a transição para o estado parado
    - Excluindo: está sendo excluído

- `scaleUnits`: Forneça a você uma capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps. Se você precisar mudar para um tipo **Premium**, ajuste `scaleUnits`.

## <a name="why-use-multiple-streaming-endpoints"></a>Por que usar vários pontos de extremidade de streaming?

Um único ponto de extremidade de streaming pode transmitir vídeos ao vivo e sob demanda e a maioria dos clientes usa apenas um ponto de extremidade de streaming. Esta seção fornece alguns exemplos de por que você pode precisar usar vários pontos de extremidade de streaming.

* Cada unidade reservada permite 200 Mbps de largura de banda. Se você precisar de mais de 2.000 Mbps (2 Gbps) de largura de banda, poderá usar o segundo ponto de extremidade de streaming e balancear a carga para fornecer largura de banda adicional.

    No entanto, a CDN é a melhor maneira de alcançar o dimensionamento para conteúdo de streaming, mas se você estiver entregando tanto conteúdo que a CDN está recebendo mais de 2 Gbps, você pode adicionar outros pontos de extremidade de streaming (origens). Nesse caso, você precisaria distribuir as URLs de conteúdo que são balanceadas entre os dois pontos de extremidade de streaming. Essa abordagem fornece um melhor cache do que tentar enviar solicitações para cada origem aleatoriamente (por exemplo, por meio de um Gerenciador de tráfego). 
    
    > [!TIP]
    > Geralmente, se a CDN estiver recebendo mais de 2 Gbps, algo poderá ser configurado incorretamente (por exemplo, sem blindagem de origem).
    
* Balanceamento de carga de diferentes provedores de CDN. Por exemplo, você pode configurar o ponto de extremidade de streaming padrão para usar a CDN da Verizon e criar uma segunda para usar a Akamai. Em seguida, adicione um balanceamento de carga entre os dois para alcançar o balanceamento de múltiplas CDN. 

    No entanto, o cliente geralmente faz o balanceamento de carga entre vários provedores de CDN usando uma única origem.
* Transmissão de conteúdo misto: ao vivo e ao vídeo sob demanda. 

    Os padrões de acesso para conteúdo ao vivo e sob demanda são muito diferentes. O conteúdo ao vivo tende a receber muita demanda pelo mesmo conteúdo de uma só vez. O conteúdo de vídeo por demanda (conteúdo de arquivo de cauda longa para instância) tem baixo uso no mesmo conteúdo. Portanto, o cache funciona muito bem no conteúdo ao vivo, mas não também no conteúdo de cauda longa.

    Considere um cenário no qual os clientes estão principalmente assistindo ao conteúdo ao vivo, mas estão apenas observando o conteúdo sob demanda e são servidos do mesmo ponto de extremidade de streaming. O baixo uso do conteúdo sob demanda ocuparia o espaço em cache que seria melhor salvo para o conteúdo ao vivo. Nesse cenário, recomendamos fornecer o conteúdo ao vivo de um ponto de extremidade de streaming e o conteúdo de cauda longa de outro ponto de extremidade de streaming. Isso melhorará o desempenho do conteúdo do evento ao vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Dimensionando o streaming com CDN

Veja os artigos a seguir:

- [Visão geral da CDN](../../cdn/cdn-overview.md)
- [Dimensionando o streaming com CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Faça perguntas e obtenha atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

[Empacotamento dinâmico](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar pontos de extremidade de streaming](manage-streaming-endpoints-howto.md)
