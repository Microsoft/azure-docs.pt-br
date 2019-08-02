---
title: Encontrar salas disponíveis – Gêmeos Digitais do Azure | Microsoft Docs
description: Neste início rápido, você executará dois aplicativos de exemplo .NET Core para enviar telemetria simulada de movimento e de dióxido de carbono para um espaço nos Gêmeos Digitais do Azure. O objetivo é encontrar salas disponíveis com ar fresco nas APIs de Gerenciamento após o processamento computado na nuvem.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 06/26/2019
ms.author: alinast
ms.openlocfilehash: 9f88eccf1e488d52fbbbd064cc5d7f54c2eec32b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459189"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Início Rápido: Encontrar salas disponíveis usando os Gêmeos Digitais do Azure

O serviço Gêmeos Digitais do Azure permite que você recrie uma imagem digital de seu ambiente físico. Em seguida, você poderá ser notificado por eventos em seu ambiente e personalizar as respostas para eles.

Este início rápido usa [um par de exemplos do .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) para digitalizar um prédio de escritórios imaginário. Ele mostra como encontrar salas disponíveis nesse prédio. Com os Gêmeos Digitais, você pode associar vários sensores ao ambiente. Você também pode descobrir se a qualidade do ar da sala disponível é ideal com a ajuda de um sensor de dióxido de carbono simulado. Um dos aplicativos de exemplo gera dados de sensor aleatórios para ajudá-lo a visualizar o cenário.

O vídeo a seguir resume a instalação do início rápido:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Pré-requisitos

1. Se você ainda não tiver uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. Os dois consoles de aplicativo executados neste início rápido estão escritos em C#. Instale o [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento. Se você tiver o SDK do .NET Core instalado, verifique a versão atual do C# no computador de desenvolvimento. Execute `dotnet --version` em um prompt de comando.

1. Baixe o [projeto de exemplo em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extraia o arquivo digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Criar uma instância dos Gêmeos Digitais

Crie uma nova instância dos Gêmeos Digitais no [portal](https://portal.azure.com) seguindo as etapas desta seção.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para o aplicativo

Esta seção registra seu aplicativo de exemplo no Azure Active Directory (Azure AD) para que ele possa acessar a instância dos Gêmeos Digitais. Se você já tiver um registro de aplicativo do Azure AD, reutilize-o para o exemplo. Verifique se ele está configurado conforme descrito nesta seção.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilar aplicativo

Compile o aplicativo de ocupação usando as etapas a seguir.

1. Abra um prompt de comando. Vá para a pasta para a qual seus arquivos `digital-twins-samples-csharp-master.zip` foram extraídos.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) para atualizar as seguintes variáveis:
    - **ClientId**: insira a ID do aplicativo de registro de aplicativo do Azure AD anotada na seção anterior.
    - **Tenant**: insira a ID do diretório do locatário do Azure AD também anotada na seção anterior.
    - **BaseUrl**: a URL da API de Gerenciamento de sua instância dos Gêmeos Digitais está no formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Substitua os espaços reservados nessa URL pelos valores da instância da seção anterior.

## <a name="provision-graph"></a>Grafo de provisão

Esta etapa provisiona o grafo espacial dos Gêmeos Digitais com:

- Vários espaços.
- Um dispositivo.
- Dois sensores.
- Um função personalizada.
- Uma atribuição de função.

O grafo espacial é provisionado usando o arquivo [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Execute `dotnet run ProvisionSample`.
    >[!NOTE]
    >A ferramenta de Logon do Dispositivo da CLI do Azure é usada para autenticar o usuário no Azure AD. O usuário precisa inserir um código especificado para fazer a autenticação usando a página de [logon da Microsoft](https://microsoft.com/devicelogin). Depois que o código for inserido, siga as etapas para se autenticar. O usuário deve fazer a autenticação quando a ferramenta estiver em execução.

    >[!TIP]
    > Ao executar essa etapa, verifique se as variáveis foram copiadas corretamente caso apareça a seguinte mensagem de erro: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. A etapa de provisionamento pode levar alguns minutos. Ela também provisiona um Hub IoT em sua instância dos Gêmeos Digitais. Ela será repetida até que o Hub IoT exiba o Status=`Running`.

    ![Exemplo de provisionamento][4]

1. No final da execução, copie a `ConnectionString` do dispositivo para usar no exemplo do simulador de dispositivo. Copie apenas a cadeia de caracteres descrita nesta imagem.

    ![Exemplo de provisionamento][1]

    >[!TIP]
    > É possível exibir e modificar seu grafo espacial usando o [Visualizador de Grafos dos Gêmeos Digitais do Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="send-sensor-data"></a>Enviar dados de sensor

Compile e execute o aplicativo do simulador de sensor seguindo estas etapas.

1. Abra um novo prompt de comando. Vá para o projeto que você baixou na pasta digital-twins-samples-csharp-master.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Edite [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) para atualizar **DeviceConnectionString** com a `ConnectionString` anterior.
1. Execute `dotnet run` para começar a enviar dados de sensor. Você verá que eles são enviados para os Gêmeos Digitais, conforme mostrado na imagem a seguir.

     ![Conectividade do dispositivo][2]

1. Deixe que esse simulador seja executado para que você possa exibir os resultados lado a lado com a ação da próxima etapa. A janela mostra os dados de sensor simulado enviados para os Gêmeos Digitais. A próxima etapa faz consultas em tempo real para encontrar salas disponíveis com ar fresco.

    >[!TIP]
    > Ao executar essa etapa, verifique se `DeviceConnectionString` foi copiado corretamente caso a seguinte mensagem de erro aparecer: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

O exemplo de sensor simula os valores de dados aleatórios para dois sensores. Eles são os sensores de movimento e de dióxido de carbono. Os espaços disponíveis com ar fresco são definidos no exemplo pela ausência de dados na sala. Eles também são definidos por um nível de dióxido de carbono de, no máximo, 1.000 ppm. Se a condição não for atendida, o espaço não será considerado disponível ou a qualidade do ar será considerada ruim.

1. Abra o prompt de comando usado para executar a etapa de provisionamento anterior.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Examine esse prompt de comando e o prompt de comando dos dados do sensor lado a lado.

    Um prompt de comando envia os dados simulados de movimento e de dióxido de carbono para os Gêmeos Digitais a cada cinco segundos. O outro comando lê o grafo em tempo real para descobrir os ambientes disponíveis com ar fresco tendo como base dados simulados aleatórios. Ele exibe uma das seguintes condições quase em tempo real com base nos dados de sensor que foram enviados da última vez:
   - Salas disponíveis com ar fresco.
   - Sala ocupada ou ar de baixa qualidade.

     ![Obter espaços disponíveis com ar fresco][3]

Para entender o que aconteceu neste início rápido e quais APIs foram chamadas, abra o [Visual Studio Code](https://code.visualstudio.com/Download) com o projeto de espaço de trabalho do código encontrado em digital-twins-samples-csharp. Use o seguinte comando:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Os tutoriais se aprofundam no código. Eles ensinam como modificar dados de configuração e quais APIs são chamadas. Para obter mais informações sobre as APIs de Gerenciamento, vá para sua página do Swagger dos Gêmeos Digitais:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| NOME | Substitua por |
| --- | --- |
| NOME_DA_SUA_INSTÂNCIA | O nome da instância dos Gêmeos Digitais |
| SUA_LOCALIZAÇÃO | Em qual região do servidor de sua instância está hospedada |

Ou, para sua conveniência, navegue até o [Swagger dos Gêmeos Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais abordam detalhes sobre como:

- Criar um aplicativo para os gerentes das instalações aumentarem a produtividade dos ocupantes.
- Fazer o prédio funcionar com mais eficiência.

Para continuar nos tutoriais, não limpe os recursos criados neste início rápido. Se você não planeja continuar, exclua todos os recursos criados neste início rápido.

1. Exclua a pasta que foi criada quando você baixou o repositório de exemplo.
1. No menu à esquerda no [portal do Azure](https://portal.azure.com), marque **Todos os recursos**. Em seguida, escolha o recurso dos Gêmeos Digitais. Na parte superior do painel **Todos os recursos**, marque **Excluir**.

    > [!TIP]
    > Se você teve problemas para excluir sua instância de Gêmeos Digitais, lançamos uma atualização de serviço com a correção. Tente novamente excluir a instância.

## <a name="next-steps"></a>Próximas etapas

Este início rápido usou um cenário simples para mostrar como encontrar salas com boas condições de trabalho. Para obter uma análise detalhada do cenário, confira este tutorial:

>[!div class="nextstepaction"]
>[Tutorial: Implantar os Gêmeos Digitais do Azure e configurar um grafo espacial](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
