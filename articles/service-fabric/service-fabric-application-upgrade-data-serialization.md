---
title: 'Atualização de aplicativos: serialização de dados | Microsoft Docs'
description: Práticas recomendadas para a serialização de dados e como ela afeta as atualizações de aplicativo sem interrupção.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614440"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta a atualização de um aplicativo
Em uma [atualização de aplicativo sem interrupção](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização de cada vez. Durante esse processo, alguns domínios de atualização têm a versão mais recente do aplicativo, enquanto outros têm a versão mais antiga do aplicativo. Durante a distribuição, a nova versão do seu aplicativo deve ser capaz de ler a versão antiga dos dados, e a versão antiga do seu aplicativo deve ser capaz de ler a nova versão dos dados. Se o formato de dados não for compatível com versões anteriores e posteriores, a atualização poderá falhar ou, pior ainda, os dados poderão ser perdidos ou corrompidos. Este artigo discute o que constitui o formato de dados e oferece as práticas recomendadas para garantir que seus dados sejam compatíveis de uma versão para outra.

## <a name="what-makes-up-your-data-format"></a>O que compõe o seu formato de dados?
No Service Fabric do Azure, os dados persistidos e replicados vêm de suas classes C#. Para aplicativos que usam [Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md), esses dados são os objetos nos dicionários e nas filas confiáveis. Para aplicativos que usam [Reliable Actors](service-fabric-reliable-actors-introduction.md), esse é o estado de backup do ator. Essas classes C# devem ser serializáveis para que possam ser persistidas e replicadas. Portanto, o formato de dados é definido por campos e propriedades que são serializados e por como eles são serializados. Por exemplo, em um `IReliableDictionary<int, MyClass>` os dados são `int` serializados e `MyClass` serializadas.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Alterações de código que resultam em uma alteração no formato dos dados
Uma vez que o formato de dados é determinado pelas classes C#, alterações nas classes podem causar uma alteração no formato de dados. É necessário ter cuidado para garantir que uma atualização sem interrupção possa lidar com a alteração do formato de dados. Exemplos que podem causar alterações no formato de dados:

* Adicionar ou remover campos ou propriedades
* Renomear campos ou propriedades
* Alterar os tipos de campo ou propriedades
* Alterar o nome de classe ou namespace

### <a name="data-contract-as-the-default-serializer"></a>Contrato de Dados como o serializador padrão
O serializador é geralmente responsável por ler os dados e desserializá-los para a versão atual mesmo se os dados estiverem em uma versão mais antiga ou *mais recente* . O serializador padrão é o [serializador de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx), que tem regras de controle de versão bem definidas. As Coleções confiáveis permitem que o serializador seja substituído, mas os Reliable Actors ainda não. O serializador de dados desempenha um papel importante na habilitação de atualizações sem interrupção. O serializador de Contrato de Dados é o serializador recomendado para os aplicativos do Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização sem interrupção
Durante uma atualização sem interrupção, há dois cenários principais em que o serializador pode encontrar versão mais antiga ou *mais recente* dos dados:

1. Depois que um nó é atualizado e inicia o backup, o novo serializador carrega os dados que foram mantidos em disco pela versão antiga.
2. Durante a atualização sem interrupção, o cluster conterá uma combinação das versões antiga e nova do seu código. Como as réplicas podem ser colocadas em diferentes domínios de atualização, e as réplicas enviam dados entre si, a versão nova e/ou antiga de seus dados pode ser encontrada pela versão nova e/ou antiga do seu serializador.

> [!NOTE]
> A "nova versão" e "versão antiga" aqui se referem à versão do seu código que está em execução. "Novo serializador" refere-se ao código do serializador que está sendo executado na nova versão de seu aplicativo. "Novos dados" refere-se à classe C# serializada da nova versão do seu aplicativo.
> 
> 

As duas versões do formato de dados e código devem ser compatíveis uma com a outra. Se não forem compatíveis, a atualização sem interrupção pode falhar ou dados podem ser perdidos. A atualização sem interrupção pode falhar porque o código ou o serializador pode acionar exceções ou falhas ao encontrar a outra versão. Dados podem ser perdidos se, por exemplo, uma nova propriedade foi adicionada, mas o serializador antigo a descartar durante a desserialização.

O Contrato de Dados é a solução recomendada para garantir que seus dados sejam compatíveis. Ele possui regras de versão bem definidas para adição, remoção e alteração de campos. Ele também tem suporte para lidar com campos desconhecidos, conectando-se ao processo de serialização e desserialização e lidando com a herança de classe. Para saber mais, confira [Usando o contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualização do aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [Parâmetros de Atualização](service-fabric-application-upgrade-parameters.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando os [Tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativo consultando as etapas em [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).

