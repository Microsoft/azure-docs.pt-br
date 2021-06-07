---
title: Visão geral
description: Saiba como o Serviço de Aplicativo do Azure o ajuda a desenvolver e hospedar aplicativos Web
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 771c79f56a31c83f2152886ad6cf68367776f83f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767220"
---
# <a name="app-service-overview"></a>Visão geral do Serviço de Aplicativo

O *Serviço de Aplicativo do Azure* é um serviço com base em HTTP para hospedagem de aplicativos Web, APIs REST e back-ends móveis. Você pode desenvolver usando sua linguagem favorita, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Os aplicativos são executados e escalados com facilidade em ambientes baseados no Windows e no [Linux](#app-service-on-linux).

O Serviço de Aplicativo não agrega apenas o poder do Microsoft Azure ao seu aplicativo, como segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você pode também aproveitar seus recursos de DevOps, como implantação contínua desde o Azure DevOps, GitHub, Docker Hub e outras fontes, gerenciamento de pacotes, ambientes de preparo, domínio personalizado e certificados TLS/SSL. 

Com o Serviço de Aplicativo, você paga pelos recursos de computação do Azure que usar. Os recursos de computação usados são determinados pelo _Plano do Serviço de Aplicativo_ no qual os aplicativos são executados. Para obter mais informações, confira [Visão geral dos Planos do Serviço de Aplicativo do Azure](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Por que usar o Serviço de Aplicativo?

Veja alguns dos principais recursos do Serviço de Aplicativo:

* **Variedade de linguagens e estruturas** – O Serviço de Aplicativo têm suporte de primeira classe para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Você também pode executar o [PowerShell e outros scripts ou executáveis](webjobs-create.md) como serviços em segundo plano.
* **Ambiente de produção gerenciado** – o Serviço de Aplicativo [automaticamente corrige e mantém as estruturas do sistema operacional e de idiomas](overview-patch-os-runtime.md) para você. Dedique seu tempo a escrever aplicativos incríveis e deixe o Azure se preocupar com a plataforma.
* **Transporte em contêineres e Docker** – converta seu aplicativo para Docker e hospede um contêiner personalizado do Windows ou do Linux no Serviço de Aplicativo. Execute aplicativos de vários contêineres com o Docker Compose. Migre suas habilidades do Docker diretamente para o Serviço de Aplicativo.
* **Otimização de DevOps** – configure a [integração e implantação contínuas](deploy-continuous-deployment.md) com o Azure DevOps, o GitHub, o BitBucket, o Hub do Docker ou o Registro de Contêiner do Azure. Promova atualizações por meio de [ambientes de preparo e teste](deploy-staging-slots.md). Gerencie aplicativos no Serviço de Aplicativo usando o [Azure PowerShell](/powershell/azure/) ou a [CLI (interface de linha de comando de plataforma cruzada)](/cli/azure/install-azure-cli).
* **Escala global com alta disponibilidade** - escale [verticalmente](manage-scale-up.md) ou [horizontalmente](../azure-monitor/autoscale/autoscale-get-started.md) de forma manual ou automática. Hospede os aplicativos em qualquer lugar na infraestrutura de datacenter global da Microsoft, e o [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) do Serviço de Aplicativo promete alta disponibilidade.
* **Conexões com plataformas SaaS e dados locais** - escolha entre mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP), serviços de SaaS (como Salesforce) e serviços de Internet (como Facebook). Acesse dados locais usando [Conexões Híbridas](app-service-hybrid-connections.md) e [Redes Virtuais do Azure](web-sites-integrate-with-vnet.md).
* **Segurança e conformidade** - o Serviço de Aplicativo está em [conformidade com ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autentique os usuários com o [Azure Active Directory](configure-authentication-provider-aad.md), o [Google](configure-authentication-provider-google.md), o [Facebook](configure-authentication-provider-facebook.md), o [Twitter](configure-authentication-provider-twitter.md) ou a [conta Microsoft](configure-authentication-provider-microsoft.md). Crie [Restrições de endereço IP](app-service-ip-restrictions.md) e [gerencie identidades de serviço](overview-managed-identity.md).
* **Modelos de aplicativos** - escolha dentre uma lista abrangente de modelos de aplicativos no [Azure Marketplace](https://azure.microsoft.com/marketplace/), como WordPress, Joomla e Drupal.
* **Integração do Visual Studio e do Visual Studio Code** – Ferramentas dedicadas no Visual Studio e no Visual Studio Code simplificam o trabalho de criar, implantar e depurar.
* **Recursos móveis e de API** – O Serviço de Aplicativo fornece suporte pronto para uso ao CORS para cenários de API RESTful e simplifica os cenários de aplicativos móveis permitindo autenticação, sincronização de dados offline, notificações por push e muito mais.
* **Código sem servidor** - Execute um snippet de código ou um script sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura, e pague somente pelo tempo de computação usado pelo seu código (consulte [Azure Functions](../azure-functions/index.yml)).

Além do Serviço de Aplicativo, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos Web. Para a maioria dos cenários, o Serviço de Aplicativo é a melhor opção.  Para a arquitetura de microsserviço, considere o [Serviço do Azure Spring Cloud](../spring-cloud/index.yml) e o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Se você precisar de mais controle sobre as VMs nas quais o seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para saber mais sobre como escolher entre esses serviços do Azure, confira [Comparação entre o Serviço de Aplicativo do Azure, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>Serviço de Aplicativo no Linux

O Serviço de Aplicativo também pode hospedar aplicativos Web nativamente no Linux em pilhas de aplicativos compatíveis. Ele também pode executar contêineres personalizados do Linux (também conhecidos como Aplicativo Web para Contêineres).

### <a name="built-in-languages-and-frameworks"></a>Linguagens e estruturas internas

O Serviço de Aplicativo no Linux dá suporte a várias imagens internas específicas da linguagem. Basta implantar o código. As linguagens com suporte incluem: Node.js, Java (JRE 8 e JRE 11), PHP, Python, .NET Core e Ruby. Execute [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes) para exibir as linguagens mais recentes e as versões com suporte. Se não houver suporte para o runtime de que o seu aplicativo precisa nas imagens internas, implante-o com um contêiner personalizado.

Os runtimes desatualizados são removidos periodicamente das folhas de criação e configuração dos aplicativos Web no Portal. Esses runtimes ficam ocultos no portal quando eles são preteridos pela organização de manutenção ou apresentam vulnerabilidades significativas. Essas opções estão ocultas para orientar os clientes para os runtimes mais recentes, onde serão os mais bem-sucedidos. 

Quando um runtime desatualizado estiver oculto no Portal, qualquer um dos sites existentes que usam essa versão continuará a ser executado. Se um runtime for totalmente removido da plataforma do Serviço de Aplicativo, seus proprietários de assinatura do Azure receberão um aviso por email antes da remoção.

Se você precisar criar outro aplicativo Web com uma versão de runtime desatualizada que não é mais mostrada no Portal, confira os guias de configuração de idioma para obter instruções sobre como obter a versão de runtime do seu site. Você pode usar a CLI do Azure para criar outro site com o mesmo runtime. Como alternativa, use o botão **Exportar Modelo** na folha do aplicativo Web no Portal para exportar um modelo do ARM do site. Você pode reutilizar este modelo para implantar um novo site com o mesmo runtime e a mesma configuração.

### <a name="limitations"></a>Limitações

- Não há suporte para o Serviço de Aplicativo no Linux no tipo de preço [Compartilhado](https://azure.microsoft.com/pricing/details/app-service/plans/). 
- Não é possível combinar aplicativos do Windows e do Linux no mesmo Plano do Serviço de Aplicativo.  
- Historicamente, você não pode combinar aplicativos Windows e Linux no mesmo grupo de recursos. No entanto, todos os grupos de recursos criados em 21 de janeiro de 2021 ou após essa data dão suporte a esse cenário. Para os grupos de recursos criados antes de 21 de janeiro de 2021, a capacidade de adicionar implantações de plataforma mista será distribuída nas regiões do Azure (incluindo regiões de nuvem nacional) em breve.
- O portal do Azure só mostra os recursos que atualmente funcionam em aplicativos do Linux. À medida que os recursos são habilitados, eles são ativados no portal.
- Quando implantados nas imagens internas, o código e o conteúdo recebem um volume de armazenamento para o conteúdo da Web, com suporte do Armazenamento do Azure. A latência do disco desse volume é maior e mais variável do que a latência do sistema de arquivos do contêiner. Os aplicativos que exigem acesso intenso somente leitura a arquivos de conteúdo podem se beneficiar da opção de contêiner personalizado, que coloca os arquivos no sistema de arquivos do contêiner em vez de no volume de conteúdo.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo Web.

> [!div class="nextstepaction"]
> [ASP.NET Core (no Windows ou no Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (no Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (no Windows ou no Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (no Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (no Windows ou no Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (no Windows ou no Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (no Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (no Windows ou no Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Contêiner personalizado (Windows ou Linux)](tutorial-custom-container.md)
