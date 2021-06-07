---
title: Entender os conceitos do repositório de modelos de dispositivo | Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de ti, saiba mais sobre os conceitos básicos do repositório de modelos de dispositivo.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33ff96b4e51dbf80bfdb924bc37786a344cdfdc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582639"
---
# <a name="device-models-repository"></a>Repositório de modelos de dispositivo

O repositório de modelos de dispositivo (DMR) permite que os criadores de dispositivos gerenciem e compartilhem modelos de dispositivos de Plug and Play IoT. Os modelos de dispositivo são documentos JSON LD definidos usando a [linguagem de modelagem de gêmeos digital (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

O DMR define um padrão para armazenar as interfaces DTDL em uma estrutura de pastas com base no DTMI (identificador de modelo de dispositivo). Você pode localizar uma interface no DMR convertendo o DTMI em um caminho relativo. Por exemplo, o `dtmi:com:example:Thermostat;1` DTMI se traduz em `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-models-repository"></a>Repositório de modelos de dispositivo público

A Microsoft hospeda um DMR público com estas características:

- Modelos organizados. A Microsoft revisa e aprova todas as interfaces disponíveis usando um fluxo de trabalho de validação da PR (solicitação pull) do GitHub.
- Imutabilidade.  Depois de publicado, uma interface não pode ser atualizada.
- Hiperescala. A Microsoft fornece a infraestrutura necessária para criar um ponto de extremidade seguro e escalonável no qual você pode publicar e consumir modelos de dispositivo.

## <a name="custom-device-models-repository"></a>Repositório de modelos de dispositivo personalizado

Use o mesmo padrão DMR para criar um DMR personalizado em qualquer mídia de armazenamento, como sistema de arquivos local ou servidores Web HTTP personalizados. Você pode recuperar modelos de dispositivo do DMR personalizado da mesma maneira que a partir do DMR público, alterando a URL base usada para acessar o DMR.

> [!NOTE]
> A Microsoft fornece ferramentas para validar modelos de dispositivo no DMR público. Você pode reutilizar essas ferramentas em repositórios personalizados.

## <a name="public-models"></a>Modelos públicos

Os modelos de dispositivo público armazenados no repositório de modelos estão disponíveis para todos consumirem e integrarem em seus aplicativos. Os modelos de dispositivo público permitem que um sistema de eco aberto para criadores de dispositivos e desenvolvedores de soluções compartilhem e reutilizem seus modelos de dispositivos IoT Plug and Play.

Consulte a seção [publicar um modelo](#publish-a-model) para obter instruções sobre como publicar um modelo no repositório de modelos para torná-lo público.

Os usuários podem procurar, Pesquisar e exibir interfaces públicas do [repositório GitHub](https://github.com/Azure/iot-plugandplay-models)oficial.

Todas as interfaces nas `dtmi` pastas também estão disponíveis no ponto de extremidade público [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Resolver modelos

Para acessar essas interfaces programaticamente, você pode usar o `ModelsRepositoryClient` disponível no pacote NuGet [Azure. IOT. ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Esse cliente é configurado por padrão para consultar a DMR pública disponível em [DeviceModels.Azure.com](https://devicemodels.azure.com/) e pode ser configurado para qualquer repositório personalizado.

O cliente aceita uma `DTMI` entrada as e retorna um dicionário com todas as interfaces necessárias:

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

A saída esperada deve exibir a `DTMI` das três interfaces encontradas na cadeia de dependência:

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

O `ModelsRepositoryClient` pode ser configurado para consultar um repositório de modelos personalizados – disponível por meio de http (s) – e especificar a resolução de dependência usando qualquer um dos disponíveis `ModelDependencyResolution` :

- Desabilitado. Retorna apenas a interface especificada, sem nenhuma dependência.
- Habilitada. Retorna todas as interfaces na cadeia de dependências
- TryFromExpanded. Usar o `.expanded.json` arquivo para recuperar as dependências previamente calculadas 

> [!Tip] 
> Os repositórios personalizados podem não expor o `.expanded.json` arquivo, quando não disponível, o cliente fará fallback para processar cada dependência localmente.

O próximo código de exemplo mostra como inicializar o `ModelsRepositoryClient` usando uma URL de base de repositório personalizado, neste caso, usando as `raw` URLs da API do GitHub sem usar o `expanded` formulário – já que ela não está disponível no `raw` ponto de extremidade. O `AzureEventSourceListener` é inicializado para inspecionar a solicitação HTTP executada pelo cliente:

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Há mais exemplos disponíveis no código-fonte no repositório GitHub do SDK do Azure: [Azure. IOT. ModelsRepository/Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Publicar um modelo

> [!Important]
> Você deve ter uma conta do GitHub para poder enviar modelos para o DMR público.

1. Bifurcar o repositório GitHub público: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Clone o repositório bifurcado. Opcionalmente, crie uma nova ramificação para manter suas alterações isoladas da `main` ramificação.
1. Adicione as novas interfaces à `dtmi` pasta usando a Convenção pasta/nome de arquivo. Para saber mais, consulte [importar um modelo para a `dtmi/` pasta](#import-a-model-to-the-dtmi-folder).
1. Valide os modelos localmente usando a `dmr-client` ferramenta. Para saber mais, consulte [validar modelos](#validate-models).
1. Confirme as alterações localmente e envie por push para sua bifurcação.
1. Em sua bifurcação, crie uma solicitação de pull que direcione a `main` ramificação. Consulte [criando um problema ou documentos de solicitação de pull](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Examine os [requisitos de solicitação de pull](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

A solicitação pull dispara um conjunto de ações do GitHub que validam as interfaces enviadas e garante que sua solicitação de pull atenda a todos os requisitos.

A Microsoft responderá a uma solicitação de pull com todas as verificações em três dias úteis.

### <a name="dmr-client-tools"></a>`dmr-client` ferramentas

As ferramentas usadas para validar os modelos durante as verificações de PR também podem ser usadas para adicionar e validar as interfaces DTDL localmente.

> [!NOTE]
> Essa ferramenta requer o [SDK do .net](https://dotnet.microsoft.com/download) versão 3,1 ou superior.

### <a name="install-dmr-client"></a>Instalar `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importar um modelo para a `dtmi/` pasta

Se o modelo já estiver armazenado em arquivos JSON, você poderá usar o `dmr-client import` comando para adicioná-los à `dtmi/` pasta com os nomes de arquivo corretos:

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> Você pode usar o `--local-repo` argumento para especificar a pasta raiz do repositório local.

### <a name="validate-models"></a>Validar modelos

Você pode validar seus modelos com o `dmr-client validate` comando:

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> A validação usa a versão mais recente do analisador DTDL para garantir que todas as interfaces sejam compatíveis com a especificação de linguagem DTDL.

Para validar dependências externas, elas devem existir no repositório local. Para validar modelos, use a `--repo` opção para especificar uma `local` `remote` pasta ou para resolver dependências:

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Validação estrita

O DMR inclui [requisitos](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)adicionais, use o `stict` sinalizador para validar seu modelo em relação a eles:

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Verifique a saída do console para verificar se há mensagens de erro.

### <a name="export-models"></a>Exportar modelos

Os modelos podem ser exportados de um determinado repositório (local ou remoto) para um único arquivo usando uma matriz JSON:

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é examinar a [arquitetura de plug and Play de IOT](concepts-architecture.md).
