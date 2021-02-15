---
title: Como solucionar problemas de falhas no trabalho de U-SQL Azure Data Lake Analytics devido à atualização do .NET Framework 4.7.2
description: Solucionar falhas de trabalho do U-SQL devido à atualização para .NET Framework 4.7.2.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217670"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics está atualizando para o .NET Framework v 4.7.2

O tempo de execução padrão do Azure Data Lake Analytics está atualizando de .NET Framework v 4.5.2 para .NET Framework v 4.7.2. Essa alteração apresenta um pequeno risco de alterações significativas se o código U-SQL usa assemblies personalizados, e esses assemblies personalizados usam bibliotecas .NET.

Essa atualização de .NET Framework 4.5.2 para a versão 4.7.2 significa que o .NET Framework implantado em um tempo de execução do U-SQL (o tempo de execução padrão) agora será sempre 4.7.2. Não há uma opção lado a lado para versões de .NET Framework.

Depois que essa atualização para .NET Framework 4.7.2 for concluída, o código gerenciado do sistema será executado como a versão 4.7.2, as bibliotecas fornecidas pelo usuário, como os assemblies personalizados do U-SQL, serão executadas no modo compatível com versões anteriores para a versão para a qual o assembly foi gerado.

- Se as DLLs do assembly forem geradas para a versão 4.5.2, a estrutura implantada as tratará como bibliotecas 4.5.2, fornecendo (com algumas exceções) a semântica 4.5.2.
- Agora você pode usar assemblies personalizados do U-SQL que usam recursos da versão 4.7.2, se você direcionar para o .NET Framework 4.7.2.

Devido a essa atualização para .NET Framework 4.7.2, há um potencial para introduzir alterações significativas em seus trabalhos do U-SQL que usam assemblies personalizados do .NET. Sugerimos que você verifique se há problemas de compatibilidade com versões anteriores usando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar se há problemas de compatibilidade com versões anteriores

Verifique o potencial de problemas de interrupção da compatibilidade com versões anteriores executando as verificações de compatibilidade do .NET em seu código .NET em seus assemblies personalizados do U-SQL.

> [!Note]
> A ferramenta não detecta alterações significativas reais. Ele identifica apenas as APIs do .NET que podem (para determinadas entradas) causar problemas. Se você for notificado de um problema, seu código ainda poderá estar correto, mas você deve fazer o check-in de mais detalhes.

1. Execute o verificador de compatibilidade com versões anteriores em suas DLLs do .NET por
   1. Usando a extensão do Visual Studio na [extensão do .net portabilidade Analyzer Visual Studio](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Baixando e usando a ferramenta autônoma do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). As instruções para executar a ferramenta autônoma estão no [GitHub dotnetapiport alterações significativas](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Para 4.7.2. compatibilidade, `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar se o código pode ser afetado por qualquer uma das possíveis incompatibilidades com versões anteriores (alguns exemplos comuns de incompatibilidades estão listados abaixo), você pode verificar novamente por
   1. Analisando seu código e identificando se seu código está passando valores para as APIs afetadas
   1. Execute uma verificação de tempo de execução. A implantação de tempo de execução não é feita lado a lado no ADLA. Você pode executar uma verificação de tempo de execução antes da atualização, usando a execução local do VisualStudio com um .NET Framework local 4.7.2 em relação a um conjunto de dados representativo.
3. Se você realmente for afetado por uma incompatibilidade com versões anteriores, execute as etapas necessárias para corrigi-lo (como corrigir sua lógica de dados ou de código).

Na maioria dos casos, você não deve ser afetado pela incompatibilidade com versões anteriores.

## <a name="timeline"></a>Linha do tempo

Você pode verificar a implantação do novo tempo de execução aqui, [solucionar problemas de tempo de execução](runtime-troubleshoot.md)e examinando qualquer trabalho bem-sucedido anterior.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não puder fazer com que meu código seja revisado no tempo

Você pode enviar seu trabalho contra a versão de tempo de execução antiga (que é construída de destino 4.5.2), no entanto, devido à falta de .NET Framework recursos lado a lado, ele ainda será executado apenas no modo de compatibilidade 4.5.2. Você ainda pode encontrar alguns dos problemas de compatibilidade com versões anteriores devido a esse comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são os problemas mais comuns de compatibilidade com versões anteriores que você pode encontrar

As incompatibilidades mais comuns que o verificador é provável de identificar são (geramos essa lista executando o verificador em nossos próprios trabalhos ADLA internos), quais bibliotecas são impactadas (Observação: você pode chamar as bibliotecas somente indiretamente, portanto, é importante tomar as medidas necessárias #1 para verificar se os trabalhos são afetados) e as ações possíveis para corrigir. Observação: em quase todos os casos para nossos próprios trabalhos, os avisos desativados como falsos positivos devido à natureza estreita da maioria das alterações mais recentes.

- A propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída
  - Ao chamar TaskFactory.FromAsync, a implementação da propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída. Isto é, a propriedade deverá retornar true se, e apenas se, a implementação for concluída de modo síncrono. Anteriormente, a propriedade não era verificada.
  - Bibliotecas afetadas: mscorlib, System. Threading. Tasks
  - Ação sugerida: Verifique se TaskFactory. FromAsync retorna true corretamente

- DataObject.GetData agora recupera dados como UTF-8
  - Para aplicativos direcionados ao .NET Framework 4 ou que são executados no .NET Framework 4.5.1 ou versões anteriores, o DataObject.GetData recupera dados formatados em HTML como uma cadeia de caracteres ASCII. Como resultado, caracteres não ASCII (caracteres cujos códigos ASCII são maiores que 0x7F) são representados por dois caracteres aleatórios. #N # #N # para aplicativos direcionados ao .NET Framework 4,5 ou posterior e executados no .NET Framework 4.5.2, o `DataObject.GetData` recupera dados formatados em HTML como UTF-8, que representa caracteres maiores que 0x7F corretamente.
  - Bibliotecas afetadas: Glo
  - Ação sugerida: Verifique se os dados recuperados são do formato que você deseja

- XmlWriter é gerado com pares alternativos inválidos
  - Em aplicativos direcionados ao NET Framework 4.5.2 ou versões anteriores, escrever um par alternativo inválido usando o tratamento de fallback de exceção nem sempre gera uma exceção. Em aplicativos destinados ao .NET Framework 4.6, tentar escrever um par alternativo inválido gera `ArgumentException`.
  - Bibliotecas afetadas: System.Xml, System.Xml. ReaderWriter
  - Ação sugerida: Verifique se você não está gravando um par substituto inválido que causará exceção de argumento

- HtmlTextWriter não renderiza o elemento `<br/>` corretamente
  - A partir do .NET Framework 4.6, chamar `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` com um elemento `<BR />` vai inserir corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas afetadas: System. Web
  - Ação sugerida: Verifique se você está inserindo a quantidade de `<BR />` espera para ver, portanto, nenhum comportamento aleatório é visto no trabalho de produção

- Chamada para CreateDefaultAuthorizationContext com um argumento nulo foi alterada
  - A implementação de AuthorizationContext retornado por uma chamada a `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com um argumento authorizationPolicies nulo mudou sua implementação no .NET Framework 4.6.
  - Bibliotecas afetadas: System. IdentityModel
  - Ação sugerida: Verifique se você está lidando com o novo comportamento esperado quando há uma política de autorização nula
  
- Agora, RSACng carrega corretamente as chaves RSA de tamanho não padrão
  - Nas versões do .NET Framework anteriores a 4.6.2, os clientes com tamanhos de chave não padrão para certificados RSA não conseguiam acessá-las por meio dos métodos de extensão `GetRSAPublicKey()` e `GetRSAPrivateKey()`. R `CryptographicException` com a mensagem "não há suporte para o tamanho da chave solicitada" é lançada. Com o .NET Framework 4.6.2, esse problema foi corrigido. Da mesma forma, `RSA.ImportParameters()` e `RSACng.ImportParameters()` agora funcionam com tamanhos de chave não padrão sem lançar `CryptographicException` .
  - Bibliotecas afetadas: mscorlib, System. Core
  - Ação sugerida: Verifique se as chaves RSA estão funcionando conforme o esperado

- Verificações de dois-pontos em caminhos estão mais rigorosas
  - No .NET Framework 4.6.2, uma série de alterações foram feitas para dar suporte aos caminhos incompatíveis anteriormente (em termos de comprimento e formato). As verificações de sintaxe do separador de unidades (dois-pontos) correto foram aperfeiçoadas, o que teve como efeito colateral o bloqueio de alguns caminhos de URI em algumas APIs de Caminho selecionadas em que eles costumavam ser aceitos.
  - Bibliotecas afetadas: mscorlib, System. Runtime. Extensions
  - Ação sugerida:

- Chamadas para construtores ClaimsIdentity
  - A partir do .NET Framework 4.6.2, haverá uma alteração na maneira como os construtores `T:System.Security.Claims.ClaimsIdentity` com um parâmetro `T:System.Security.Principal.IIdentity` configuram a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor`. Se o argumento `T:System.Security.Principal.IIdentity` for um objeto `T:System.Security.Claims.ClaimsIdentity` e a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` desse objeto `T:System.Security.Claims.ClaimsIdentity` não for `null`, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` será anexada usando o método `M:System.Security.Claims.ClaimsIdentity.Clone`. No Framework 4.6.1 e versões anteriores, a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade é anexada como uma referência existente. Por causa dessa alteração, começando com o .NET Framework 4.6.2, a `P:System.Security.Claims.ClaimsIdentify.Actor` Propriedade do novo `T:System.Security.Claims.ClaimsIdentity` objeto não é igual à `P:System.Security.Claims.ClaimsIdentify.Actor` Propriedade do argumento do construtor `T:System.Security.Principal.IIdentity` . No .NET Framework 4.6.1 e nas versões anteriores, ele é igual.
  - Bibliotecas afetadas: mscorlib
  - Ação sugerida: Verifique se ClaimsIdentity está funcionando conforme o esperado no novo tempo de execução

- Serialização de caracteres de controle com DataContractJsonSerializer agora é compatível com ECMAScript V6 e V8
  - No .NET Framework 4.6.2 e versões anteriores, o DataContractJsonSerializer não serializava alguns caracteres de controle especiais, como \b, \f e \t, de forma que fosse compatível com os padrões ECMAScript V6 e V8. A partir do .NET Framework 4.7, a serialização desses caracteres de controle é compatível com o ECMAScript V6 e V8.
  - Bibliotecas afetadas: System.Runtime.Serialization.Jsem
  - Ação sugerida: Verifique o mesmo comportamento com DataContractJsonSerializer
