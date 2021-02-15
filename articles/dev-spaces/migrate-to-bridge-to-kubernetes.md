---
title: Migração para o Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Descreve o processo de migração do Azure Dev Spaces para a ponte para o kubernetes
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, ponte para kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888514"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migração para o Bridge to Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces será desativado em 31 de outubro de 2023. Os clientes devem mudar para o uso do Bridge para o kubernetes, uma ferramenta de desenvolvedor de cliente.
>
> A finalidade da Azure Dev Spaces foi a de facilitar o desenvolvimento de usuários no kubernetes. Uma compensação significativa na abordagem da Azure Dev Spaces era colocar um fardo extra nos usuários para entender as configurações do Docker e do kubernetes, bem como os conceitos de implantação do kubernetes. Ao longo do tempo, também ficou claro que a abordagem de Azure Dev Spaces não reduziu efetivamente a velocidade do desenvolvimento de loop interno em kubernetes. A ponte para o kubernetes diminui efetivamente a velocidade do desenvolvimento de loops internos e evita a carga desnecessária dos usuários.
>
> A missão principal permanece inalterada: Crie as melhores experiências para desenvolver, testar e depurar o código de microserviço no contexto do aplicativo maior.

A ponte para o kubernetes fornece uma alternativa de peso mais leve a muitos dos cenários de desenvolvimento que funcionam com Azure Dev Spaces. A ponte para o kubernetes é uma experiência somente no lado do cliente usando extensões no [Visual Studio][vs]   e [Visual Studio Code][vsc].  

O Bridge para kubernetes ajuda sua experiência de desenvolvimento, permitindo que um aplicativo kubernetes estabelecido inclua um serviço em execução na estação de trabalho de desenvolvimento local. Ao contrário dos espaços de desenvolvimento, o Bridge to kubernetes reduz as complexidades de loop interno por meio da necessidade de criar configurações de Docker e kubernetes, permitindo que os desenvolvedores se concentrem puramente na lógica de negócios de seu código de microatendimento.

O Bridge para kubernetes ajuda você a iterar no código em execução no seu computador de desenvolvimento enquanto consome dependências e configuração existente do seu ambiente kubernetes. Por outro lado, Azure Dev Spaces implanta seu microserviço no ambiente kubernetes antes de você poder depurar remotamente seu serviço e iterar em seu código.

Este artigo fornece uma comparação entre Azure Dev Spaces e a ponte para kubernetes, bem como instruções para migrar do Azure Dev Spaces para o Bridge para o kubernetes.

## <a name="development-approaches"></a>Abordagens de desenvolvimento

![Abordagens de desenvolvimento](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces ajudou os desenvolvedores kubernetes a trabalhar com código em execução diretamente em seu cluster AKS, evitando a necessidade de um ambiente local que não se assemelhasse ao ambiente implantado. Essa abordagem melhorou determinados aspectos do desenvolvimento, mas também introduziu um pré-requisito de aprendizado e manutenção de conceitos adicionais, como Docker, kubernetes e Helm, antes de começar a usar Azure Dev Spaces.

A ponte para o kubernetes permite que os desenvolvedores trabalhem diretamente em seus computadores de desenvolvimento ao mesmo tempo em que interagem com o restante do cluster. Essa abordagem aproveita a familiaridade e a velocidade de execução de código diretamente em seus computadores de desenvolvimento enquanto compartilha as dependências e o ambiente fornecido pelo cluster. Essa abordagem também aproveita a fidelidade e o dimensionamento que vem da execução no kubernetes.

## <a name="feature-comparison"></a>Comparação de recursos

Azure Dev Spaces e Bridge para kubernetes têm recursos semelhantes, eles também são diferentes em várias áreas:

| Requisito  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Serviço de Kubernetes do Azure | Em 15 regiões do Azure | Qualquer região de serviço do AKS    |
| **Segurança** |
| Acesso de segurança necessário no cluster  | Colaborador de cluster AKS  | Kubernetes RBAC – atualização de implantação   |
| Acesso de segurança necessário no seu computador de desenvolvimento  | N/D  | Administrador local/sudo   |
| **Usabilidade** |
| Independente dos artefatos do kubernetes e do Docker  | Não  | Sim   |
| Reversão automática de alterações, pós-depuração  | Não  | Sim   |
| **Ferramentas de cliente com suporte** |
| Funciona com o Visual Studio 2019  | Sim  | Sim   |
| Funciona com Visual Studio Code  | Sim  | Sim   |
| Funciona com uma CLI  | Sim  | Não   |
| **Compatibilidade do sistema operacional** |
| Funciona no Windows 10  | Sim  | Sim  |
| Funciona no Linux  | Sim  | Sim  |
| Funciona no macOS  | Sim  | Sim  |
| **Funcionalidades** |
| Isolamento de desenvolvedor ou desenvolvimento de equipe  | Sim  | Sim  |
| Substituir seletivamente variáveis de ambiente  | Não  | Sim  |
| Criação de gráfico Dockerfile e Helm  | Sim  | Não  |
| Implantação persistente de código para kubernetes  | Sim  | Não  |
| Depuração remota em um pod kubernetes  | Sim  | Não  |
| Depuração local, conectada ao kubernetes  | Não  | Sim  |
| Depurando vários serviços ao mesmo tempo, na mesma estação de trabalho  | Sim  | Sim  |

## <a name="kubernetes-inner-loop-development"></a>Desenvolvimento de loop interno do kubernetes

A maior diferença entre Azure Dev Spaces e ponte para kubernetes é onde seu código é executado. Azure Dev Spaces ajuda a desenvolver e depurar seu código de microserviço, mas requer que você execute esse código em seu cluster. O Bridge to kubernetes permite que você desenvolva e depure seu código de microserviço diretamente no seu computador de desenvolvimento enquanto ainda estiver no contexto do aplicativo maior em execução no kubernetes. A ponte para kubernetes estende o perímetro do cluster kubernetes e permite que os processos locais herdem a configuração do kubernetes.

![Desenvolvimento de loop interno](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Ao usar a ponte para kubernetes, é estabelecida uma conexão de rede entre o computador de desenvolvimento e o cluster.Durante o tempo de vida dessa conexão, um proxy é adicionado ao cluster no lugar da implantação do kubernetes que redireciona as solicitações para o serviço para o seu computador de desenvolvimento. Quando você se desconecta, a implantação do aplicativo será revertida para usar a versão original da implantação em execução no cluster. Essa abordagem é diferente de como Azure Dev Spaces funciona no qual o código é sincronizado com o cluster, criado e executado. Azure Dev Spaces também não reverte seu código.

A ponte para o kubernetes tem a flexibilidade de trabalhar com aplicativos em execução no kubernetes, independentemente de seu método de implantação. Se você usar CI/CD para compilar e executar seu aplicativo, independentemente de você usar ferramentas estabelecidas ou scripts personalizados, você ainda poderá usar o Bridge para kubernetes para desenvolver e depurar seu código.

> [!TIP]
> A [extensão Microsoft kubernetes][kubernetes-extension] permite que você desenvolva rapidamente manifestos do kubernetes com o IntelliSense e ajuda os gráficos do Scaffold Helm.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Transição para a ponte para o kubernetes da Azure Dev Spaces

1. Se você estiver usando o Visual Studio, atualize o IDE do Visual Studio para a versão 16,7 ou superior e instale a ponte para a extensão kubernetes do [Visual Studio Marketplace][vs-marketplace]. Se você estiver usando Visual Studio Code, instale a [ponte para a extensão kubernetes][vsc-marketplace].
1. Desabilite o controlador de Azure Dev Spaces usando o portal do Azure ou a [CLI do Azure dev Spaces][azds-delete].
1. Use [Azure cloud Shell](https://shell.azure.com). Ou no Mac, Linux ou Windows com o bash instalado, abra um prompt de shell bash. Verifique se as seguintes ferramentas estão disponíveis no seu ambiente de linha de comando: CLI do Azure, Docker, kubectl, ondulação, tar e gunzip.
1. Crie um registro de contêiner ou use um existente. Você pode criar um registro de contêiner no Azure usando o [registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/) ou usando o [Hub do Docker](https://hub.docker.com/). Ao usar Azure Cloud Shell, somente o registro de contêiner do Azure está disponível para hospedar imagens do Docker.
1. Execute o script de migração para converter os ativos de Azure Dev Spaces para ponte para ativos kubernetes. O script cria uma nova imagem compatível com a ponte para kubernetes, carrega-a no registro designado e, em seguida, usa [Helm](https://helm.sh) para atualizar o cluster com a imagem. Você deve fornecer o grupo de recursos, o nome do cluster AKS e um registro de contêiner. Há outras opções de linha de comando, conforme mostrado aqui:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   O script dá suporte aos seguintes sinalizadores:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Migre manualmente todas as personalizações, como as configurações de variável de ambiente, em *azds. YAML* para o arquivo *Values. yml* do seu projeto.
1. adicional Remova o `azds.yaml` arquivo do seu projeto.
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio, consulte [usar o Bridge para kubernetes no Visual Studio][use-btk-vs]. Para VS Code, consulte [usar o Bridge para kubernetes no vs Code][use-btk-vsc].
1. Inicie a depuração usando a ponte recém-criada para kubernetes o perfil de depuração/inicialização.
1. Você pode executar o script novamente conforme necessário para reimplantar o cluster.

## <a name="team-development-in-a-shared-cluster"></a>Desenvolvimento de equipe em um cluster compartilhado

Você também pode usar o roteamento específico do desenvolvedor com a ponte para kubernetes. O cenário de desenvolvimento de equipe Azure Dev Spaces usa vários namespaces kubernetes para isolar um serviço do restante do aplicativo usando o conceito de namespaces pai e filho. O Bridge to kubernetes oferece o mesmo recurso, mas com características de desempenho aprimoradas e dentro do mesmo namespace de aplicativo.

Ambas as pontes para kubernetes e Azure Dev Spaces exigem que os cabeçalhos HTTP estejam presentes e propagados em todo o aplicativo. Se você já tiver configurado seu aplicativo para manipular a propagação de cabeçalho para Azure Dev Spaces, o cabeçalho precisará ser atualizado. Para fazer a transição para a ponte para o kubernetes da Azure Dev Spaces, atualize o cabeçalho configurado de *azds-Route-as* para *kubernetes-Route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Avaliar ponte para kubernetes

Se você quiser experimentar a ponte para o kubernetes antes de desabilitar Azure Dev Spaces em seu cluster, a maneira mais fácil é usar um novo cluster. Se você tentar usar Azure Dev Spaces e ponte para kubernetes ao mesmo tempo no mesmo cluster, encontrará problemas ao usar os recursos de roteamento em ambos.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Usar o Visual Studio para avaliar a ponte para o kubernetes

1. Atualize o IDE do Visual Studio para a versão 16,7 ou superior e instale a ponte para a extensão kubernetes do [Visual Studio Marketplace][vs-marketplace].
1. Crie um novo cluster AKS e implante seu aplicativo. Você também pode usar um [aplicativo de exemplo][btk-sample-app].
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio, consulte [usar o Bridge para kubernetes][use-btk-vs].
1. Inicie a depuração no Visual Studio usando a ponte recém-criada para kubernetes o perfil de depuração.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Usar Visual Studio Code para avaliar a ponte para o kubernetes

1. Instale a [ponte para a extensão kubernetes][vsc-marketplace].
1. Crie um novo cluster AKS e implante seu aplicativo. Você também pode usar um [aplicativo de exemplo][btk-sample-app].
1. Configure a ponte para o kubernetes em seu aplicativo implantado. Para obter mais informações sobre como usar o Bridge para kubernetes no Visual Studio Code, consulte [usar o Bridge para kubernetes][use-btk-vsc].
1. Inicie a depuração no Visual Studio usando a ponte recém-criada para o perfil de inicialização do kubernetes.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como a ponte para kubernetes funciona.

> [!div class="nextstepaction"]
> [Como funciona a Ponte para Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
