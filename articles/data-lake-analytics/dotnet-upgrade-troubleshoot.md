---
title: Como solucionar problemas de falhas de trabalho do U-SQL do Azure Data Lake Analytics devido à atualização do .NET Framework 4.7.2
description: Solucionar falhas de trabalho do U-SQL devido à atualização para .NET Framework 4.7.2.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217670"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>O Azure Data Lake Analytics está sendo atualizado para o .NET Framework v4.7.2

O runtime padrão do Azure Data Lake Analytics está sendo atualizado de .NET Framework v 4.5.2 para .NET Framework v 4.7.2. Essa alteração apresenta um pequeno risco de alterações interruptivas se o código U-SQL usar assemblies personalizados e eles usarem bibliotecas .NET.

Essa atualização de .NET Framework 4.5.2 para a versão 4.7.2 significa que o .NET Framework implantado em um runtime do U-SQL (runtime padrão) sempre será 4.7.2. Não existe uma opção lado a lado para versões de .NET Framework.

Depois que a atualização para .NET Framework 4.7.2 for concluída, o código gerenciado do sistema será executado como a versão 4.7.2, as bibliotecas fornecidas pelo usuário, como os assemblies personalizados do U-SQL, serão executadas no modo compatível com versões anteriores para a versão para a qual o assembly foi gerado.

- Se as DLLs do assembly forem geradas para a versão 4.5.2, a estrutura implantada as tratará como bibliotecas 4.5.2, fornecendo (com algumas exceções) a semântica 4.5.2.
- Agora você pode usar assemblies personalizados do U-SQL que usam recursos da versão 4.7.2, se você direcionar para o .NET Framework 4.7.2.

Devido a essa atualização para .NET Framework 4.7.2, há um potencial para introduzir alterações interruptivas em seus trabalhos do U-SQL que usam assemblies personalizados do .NET. Sugerimos que você verifique se há problemas de compatibilidade com versões anteriores usando o procedimento abaixo.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Como verificar se houve problemas de compatibilidade com versões anteriores

Verifique o potencial de problemas de interrupção da compatibilidade anteriores executando as verificações de compatibilidade do .NET em seu código .NET em seus assemblies personalizados do U-SQL.

> [!Note]
> A ferramenta não detecta alterações interruptivas reais. Ele identifica apenas as APIs do .NET que podem (para determinadas entradas) causar problemas. Se você receber a notificação de um problema, seu código ainda deve estar ileso, mas você deve verificar de forma mais detalhada.

1. Execute o verificador de compatibilidade com versões anteriores em suas DLLs do .NET:
   1. Usando a extensão do Visual Studio na [Extensão do Visual Studio Analyzer portabilidade .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Baixando e usando a ferramenta autônoma do [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). As instruções para executar a ferramenta autônoma estão no [Alterações interruptivas do GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Para 4.7.2. compatibilidade, `read isRetargeting == True` identifica possíveis problemas.
2. Se a ferramenta indicar que o código pode estar afetado por qualquer uma das possíveis incompatibilidades com versões anteriores (alguns exemplos comuns de incompatibilidades estão listados abaixo), você pode verificar novamente:
   1. Analise seu código e identifique se seu código está passando valores para as APIs afetadas
   1. Execute uma verificação runtime. A implantação de runtime não é feita lado a lado no ADLA. Você pode executar uma verificação de runtime antes da atualização, usando a execução local do VisualStudio com um .NET Framework 4.7.2 local em relação a um conjunto de dados representativo.
3. Se você realmente for afetado por uma incompatibilidade com versões anteriores, execute as etapas necessárias para corrigir isso (como corrigir sua lógica de dados ou de código).

Na maioria dos casos, você não deve ser afetado pela incompatibilidade com versões anteriores.

## <a name="timeline"></a>Linha do tempo

Você pode verificar a implantação do novo runtime aqui, [Solucionar problemas de runtime](runtime-troubleshoot.md)e examinando qualquer trabalho anterior bem-sucedido.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>E se eu não conseguir revisar meu código a tempo

Você pode enviar seu trabalho com a versão de runtime antiga (que é construída direcionada a 4.5.2), no entanto, devido à falta de recursos lado a lado para .NET Framework, ele ainda será executado apenas no modo de compatibilidade 4.5.2. Você ainda pode encontrar alguns dos problemas de compatibilidade com versões anteriores devido a esse comportamento.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Quais são os problemas mais comuns de compatibilidade com versões anteriores que você pode encontrar

As incompatibilidades mais comuns que o verificador pode identificar são (geramos essa lista executando o verificador em nossos próprios trabalhos ADLA internos), quais bibliotecas são impactadas (observação: você pode chamar as bibliotecas somente indiretamente, portanto, é importante tomar as medidas necessárias n.º 1 verificar se os trabalhos foram afetados) e as ações que podem ser corrigidas. Observação: em quase todos os casos para nossos próprios trabalhos, os avisos acabam sendo falsos positivos devido à natureza limitada da maioria das alterações interruptivas.

- A propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída
  - Ao chamar TaskFactory.FromAsync, a implementação da propriedade IAsyncResult.CompletedSynchronously deve estar correta para a tarefa resultante a ser concluída. Isto é, a propriedade deverá retornar true se, e apenas se, a implementação for concluída de modo síncrono. Anteriormente, a propriedade não era verificada.
  - Bibliotecas afetadas: mscorlib, System.Threading.Tasks
  - Ação sugerida: verificar se TaskFactory. FromAsync retorna true corretamente

- DataObject.GetData agora recupera dados como UTF-8
  - Para aplicativos direcionados ao .NET Framework 4 ou que são executados no .NET Framework 4.5.1 ou versões anteriores, o DataObject.GetData recupera dados formatados em HTML como uma cadeia de caracteres ASCII. Como resultado, caracteres não ASCII (caracteres cujos códigos ASCII são maiores do que 0x7F) são representados por dois caracteres aleatórios .#N##N# Para aplicativos destinados ao .NET Framework 4.5 ou versão posterior e que são executados no .NET Framework 4.5.2, `DataObject.GetData` recupera dados formatados em HTML como UTF-8, que representa corretamente caracteres maiores que do que 0x7F.
  - Bibliotecas Afetadas: Glo
  - Ação sugerida: verificar se os dados recuperados estão do formato que você deseja

- XmlWriter é gerado com pares alternativos inválidos
  - Em aplicativos direcionados ao NET Framework 4.5.2 ou versões anteriores, escrever um par alternativo inválido usando o tratamento de fallback de exceção nem sempre gera uma exceção. Em aplicativos destinados ao .NET Framework 4.6, tentar escrever um par alternativo inválido gera `ArgumentException`.
  - Bibliotecas Afetadas: System.Xml, System.Xml.ReaderWriter
  - Ação sugerida: verificar se você não está gravando um par substituto inválido que causará exceção de argumento

- HtmlTextWriter não renderiza o elemento `<br/>` corretamente
  - A partir do .NET Framework 4.6, chamar `HtmlTextWriter.RenderBeginTag()` e `HtmlTextWriter.RenderEndTag()` com um elemento `<BR />` vai inserir corretamente apenas um `<BR />` (em vez de dois)
  - Bibliotecas Afetadas: System.Web
  - Ação sugerida: verificar se você está inserindo a quantidade de `<BR />` que espera ver, para que nenhum comportamento aleatório seja visto no trabalho de produção

- Chamada para CreateDefaultAuthorizationContext com um argumento nulo foi alterada
  - A implementação de AuthorizationContext retornado por uma chamada a `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` com um argumento authorizationPolicies nulo mudou sua implementação no .NET Framework 4.6.
  - Bibliotecas Afetadas: System.IdentityModel
  - Ação sugerida: verificar se você está lidando com o novo comportamento esperado quando não existe uma política de autorização
  
- Agora, RSACng carrega corretamente as chaves RSA de tamanho não padrão
  - Nas versões do .NET Framework anteriores a 4.6.2, os clientes com tamanhos de chave não padrão para certificados RSA não conseguiam acessá-las por meio dos métodos de extensão `GetRSAPublicKey()` e `GetRSAPrivateKey()`. Uma `CryptographicException` com a mensagem “O tamanho de chave solicitado não é compatível” é gerada. Esse problema foi corrigido com o .NET Framework 4.6.2. Da mesma forma, `RSA.ImportParameters()` e `RSACng.ImportParameters()` agora funcionam com tamanhos de chave não padrão sem gerar `CryptographicException`.
  - Bibliotecas Afetadas: mscorlib, System.Core
  - Ação Sugerida: verificar se as chaves RSA estão funcionando conforme o esperado

- Verificações de dois-pontos em caminhos estão mais rigorosas
  - No .NET Framework 4.6.2, uma série de alterações foram feitas para dar suporte aos caminhos incompatíveis anteriormente (em termos de comprimento e formato). As verificações de sintaxe do separador de unidades (dois-pontos) correto foram aperfeiçoadas, o que teve como efeito colateral o bloqueio de alguns caminhos de URI em algumas APIs de Caminho selecionadas em que eles costumavam ser aceitos.
  - Bibliotecas Afetadas: mscorlib, System.Runtime.Extensions
  - Ação Sugerida:

- Chamadas para construtores ClaimsIdentity
  - A partir do .NET Framework 4.6.2, haverá uma alteração na maneira como os construtores `T:System.Security.Claims.ClaimsIdentity` com um parâmetro `T:System.Security.Principal.IIdentity` configuram a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor`. Se o argumento `T:System.Security.Principal.IIdentity` for um objeto `T:System.Security.Claims.ClaimsIdentity` e a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` desse objeto `T:System.Security.Claims.ClaimsIdentity` não for `null`, a propriedade `P:System.Security.Claims.ClaimsIdentify.Actor` será anexada usando o método `M:System.Security.Claims.ClaimsIdentity.Clone`. No Framework 4.6.1 e versões anteriores, a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade é anexada como uma referência existente. Por causa dessa alteração, começando com o .NET Framework 4.6.2, a `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade do novo `T:System.Security.Claims.ClaimsIdentity` objeto não é igual à `P:System.Security.Claims.ClaimsIdentify.Actor` propriedade do argumento do construtor `T:System.Security.Principal.IIdentity`. No .NET Framework 4.6.1 e nas versões anteriores, ele é igual.
  - Bibliotecas Afetadas: mscorlib
  - Ação Sugerida: verificar se ClaimsIdentity está funcionando conforme o esperado no novo runtime

- Serialização de caracteres de controle com DataContractJsonSerializer agora é compatível com ECMAScript V6 e V8
  - No .NET framework 4.6.2 e nas versões anteriores, o DataContractJsonSerializer não serializava alguns caracteres de controle especiais, como \b, \f, e \t de forma compatível com os padrões ECMAScript V6 e V8. A partir do .NET Framework 4.7, a serialização desses caracteres de controle é compatível com o ECMAScript V6 e V8.
  - Bibliotecas Afetadas: System.Runtime.Serialization.Json
  - Ação Sugerida: verificar o mesmo comportamento com o DataContractJsonSerializer
