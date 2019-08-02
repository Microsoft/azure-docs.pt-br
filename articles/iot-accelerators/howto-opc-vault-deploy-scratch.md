---
title: Como implantar o módulo OPC cofre do zero - Azure | Microsoft Docs
description: Como implantar o cofre do OPC do zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 73588d8c84525cf70de9fac312b0a485055b0a2c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605700"
---
# <a name="deploy-opc-vault-from-scratch"></a>Implantar o cofre OPC do zero

O OPC Vault é um microsserviço que pode configurar, registrar e gerenciar o ciclo de vida do certificado para aplicativos servidor e cliente da UA de OPC na nuvem. Este artigo mostra como implantar o cofre do OPC do zero.

## <a name="configuration-and-environment-variables"></a>Configuração e variáveis de ambiente

A configuração do serviço é armazenada usar adaptadores de configuração do ASP.NET Core, em appsettings.ini. O formato INI permite armazenar valores em um formato legível, com comentários.
O aplicativo também dá suporte a variáveis de ambiente inserindo, como credenciais e detalhes de rede. (Esta seção será denominada originalmente tarefas de configuração e variáveis de ambiente).

O arquivo de configuração no repositório faz referência a algumas variáveis de ambiente que precisam criados pelo menos uma vez. Dependendo do seu sistema operacional e o IDE, há várias maneiras de gerenciar variáveis de ambiente:

- Para usuários do Windows, o script env-vars-Setup. cmd precisa ser preparada e executada apenas uma vez. Quando executado, as configurações serão mantidas em reinicializações e sessões de terminal.

- Para ambientes Linux e no OSX, o script de instalação do env-vars precisa para ser executado sempre que um novo console é aberto. Dependendo do sistema operacional e terminal, há maneiras de manter valores globalmente, para obter mais informações, que essas páginas devem ajudar:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Variáveis de ambiente podem ser definidas também no Visual Studio, nas propriedades do projeto, no painel esquerdo, selecione "Propriedades de configuração" e "Ambiente" para obter uma seção de onde você pode adicionar diversas variáveis.

- IntelliJ Rider: Variáveis de ambiente podem ser definidas em cada configuração de execução, da mesma forma que o IDEA do IntelliJ https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Executar e depurar com o Visual Studio

O Visual Studio permite abrir rapidamente o aplicativo sem o uso de um prompt de comando, sem configurar nada fora do IDE.

Para executar e depurar o aplicativo usando o Visual Studio:

1. Abra a solução usando o `iot-opc-gds-service.sln` arquivo.

1. Quando a solução for carregada, clique com botão direito no `WebService` do projeto, selecione e vá para o `Debug` seção.

1. Na mesma seção, defina as variáveis de ambiente necessárias.

1. Pressione **F5**, ou o **executar** ícone. Visual Studio deve abrir seu navegador mostrando o status do serviço no formato JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Executar e depurar com o IntelliJ Rider

1. Abra a solução usando o `iot-opc-gds-service.sln` arquivo.

1. Quando a solução for carregada, vá para `Run > Edit Configurations` e crie um novo `.NET Project` configuração.

1. Na configuração, selecione o projeto de serviço Web.

1. Salvar as configurações e execute a configuração que é criada a partir da barra de ferramentas do IDE.

1. Você deverá ver o serviço bootstrap mensagens na janela de execução do IntelliJ com detalhes como a URL onde o serviço web está sendo executado além do serviço de logs.

## <a name="build-and-run-from-the-command-line"></a>Compilar e executar da linha de comando

A pasta de scripts contém alguns scripts para as tarefas frequentes:

- `build`: Compilar todos os projetos e executar os testes.

- `compile`: Compile todos os projetos.

- `run`: Compilar os projetos e executar o serviço, que solicita privilégios elevados no Windows para executar o serviço web.

Verificam os scripts para a configuração de variáveis de ambiente. Você pode definir as variáveis de ambiente globalmente em seu sistema operacional, ou use o script de "env-vars-setup" na pasta scripts.

### <a name="sandbox"></a>Área restrita

Os scripts presumem que você configurou o ambiente de desenvolvimento com .NET Core e Docker. Você pode evitar a instalação do .NET Core e instalar o Docker apenas e use o parâmetro de linha de comando `--in-sandbox` (ou a forma abreviada `-s`), por exemplo:

- `build --in-sandbox`: Executa a tarefa de build dentro de um contêiner de Docker (forma abreviada `build -s`).

- `compile --in-sandbox`: Executa a tarefa de compilação dentro de um contêiner de Docker (forma abreviada `compile -s`).

- `run --in-sandbox`: Inicia o serviço dentro de um contêiner de Docker (forma abreviada `run -s`).

As imagens do Docker usadas para a área restrita são hospedadas no Docker Hub [aqui](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Empacotar o aplicativo para uma imagem do Docker

O `scripts` pasta inclui uma subpasta do docker com os arquivos necessários para empacotar o serviço em uma imagem do Docker:

- `Dockerfile`: Especificações de imagens do docker.
- `build`: Crie um contêiner do Docker e armazenar a imagem no registro local.
- `run`: Execute o contêiner do Docker da imagem armazenada no registro local.
- `content`: Uma pasta com arquivos copiados na imagem, incluindo o script de ponto de entrada.

## <a name="azure-iot-hub-setup"></a>Configuração do IoT Hub do Azure

Para usar o microsserviço, configure o IoT Hub do Azure para testes de integração e desenvolvimento.

O projeto inclui alguns scripts de Bash para ajudá-lo com essa configuração:

- Crie um novo IoT Hub: `./scripts/iothub/create-hub.sh`

- Liste hubs existentes: `./scripts/iothub/list-hubs.sh`

- Mostra detalhes (por exemplo, chaves) de IoT Hub: `./scripts/iothub/show-hub.sh`

E caso você tivesse várias assinaturas do Azure:

- Mostra lista de assinaturas: `./scripts/iothub/list-subscriptions.sh`

- Alterar a assinatura atual: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Configuração de desenvolvimento

### <a name="net-setup"></a>Instalação do .NET

O fluxo de trabalho do projeto é gerenciado por meio [.NET Core](https://dotnet.github.io) 1.x, que você precisa instalar no seu ambiente, para que você possa executar todos os scripts e certifique-se de que seu IDE funciona conforme o esperado.

Nós também fornecemos uma [versão Java](https://github.com/Azure/iot-opc-gds-service-dotnet) deste projeto e outros componentes de PCS de IoT do Azure.

### <a name="ide"></a>IDE

Aqui estão alguns dos IDEs que você pode usar para trabalhar em PCS de IoT do Azure:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Instalação do Git

O projeto inclui um gancho do Git, para automatizar algumas verificações antes de aceitar uma alteração de código. Você pode executar os testes manualmente ou permitir que a plataforma de CI para executar os testes. Usamos o gancho do Git a seguir para executar todos os testes antes de enviar as alterações de código para o GitHub automaticamente e acelerar o fluxo de trabalho de desenvolvimento.

Se você quiser remover o gancho a qualquer momento, basta excluir o arquivo instalado em `.git/hooks`. Você também pode ignorar o gancho de pré-confirmação usando o `--no-verify` opção.

#### <a name="pre-commit-hook-with-sandbox"></a>Gancho de pré-confirmação com a área restrita

Para configurar os ganchos incluídos, abra o console do Windows/Linux/MacOS e execute:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Com essa configuração, ao fazer check-in de arquivos, o Git verifica se o aplicativo passa todos os testes, executados a compilação e os testes dentro de um contêiner do Docker que está configurado com todos os requisitos de desenvolvimento.

#### <a name="pre-commit-hook-without-sandbox"></a>Gancho de pré-confirmação sem área restrita

> [!NOTE] 
> Requer o gancho sem área restrita [.NET Core](https://dotnet.github.io) no caminho do sistema.

Para configurar os ganchos incluídos, abra o console do Windows/Linux/MacOS e execute:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Com essa configuração, ao fazer check-in de arquivos, o Git verifica se o aplicativo passa todos os testes, executados a compilação e os testes em sua estação de trabalho usando as ferramentas instaladas no seu sistema operacional.

Orientação sobre o estilo de código do projeto:

- Onde razoável, o comprimento de linhas é limitado a 80 caracteres max para ajudar a revisões de código e editores de linha de comando.

- Recuo de blocos de código com quatro espaços. O caractere de guia deve ser evitado.

- Arquivos de texto usam final do Unix do formato de linha (LF).

- Injeção de dependência é gerenciada com o [Autofac](https://autofac.org).

- Campos APIs de serviço da Web sejam CamelCased exceto metadados.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o cofre do OPC do zero, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Implantar o gêmeo do OPC do zero](howto-opc-twin-deploy-modules.md)