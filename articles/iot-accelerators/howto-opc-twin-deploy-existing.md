---
title: Como implantar um módulo OPC Myem um projeto existente do Azure | Microsoft Docs
description: Como implantar OPC maquerer em um projeto existente.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: fc70d140479be100e6aa52cf8105d3e466342cd7
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302659"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implantar o OPC "an" em um projeto existente

O módulo OPC myé executado em IoT Edge e fornece vários serviços de borda para os serviços de registro e registros OPC.

O microserviço OPC maseriais facilita a comunicação entre os operadores de fábrica e os dispositivos de servidor OPC UA no chão de fábrica por meio de um módulo de IoT Edge do OPC. O Microservice expõe os serviços OPC UA (procurar, ler, gravar e executar) por meio de sua API REST. 

O microserviço de registro do dispositivo OPC UA fornece acesso a aplicativos do OPC UA registrados e seus pontos de extremidade. Operadores e administradores podem registrar e cancelar o registro de novos aplicativos OPC UA e procurar os existentes, incluindo seus pontos de extremidade. Além do gerenciamento de aplicativos e de pontos de extremidade, o serviço de registro também cataloga módulos de IoT Edge do OPC. A API do serviço oferece controle da funcionalidade do módulo do Edge, por exemplo, iniciar ou parar a descoberta do servidor (serviços de verificação) ou ativar o novo ponto de extremidade gêmeos que pode ser acessado usando o microserviço OPC.

O núcleo do módulo é a identidade do supervisor. O supervisor gerencia o ponto de extremidade de MyPoint, que corresponde aos pontos de extremidades do servidor do OPC UA que são ativados usando a API de registro do OPC UA correspondente. Esse ponto de extremidade gêmeos converte OPC UA JSON recebido do microserviço OPC MyService em execução na nuvem em mensagens binárias de OPC UA, que são enviadas por um canal seguro com estado para o ponto de extremidade gerenciado. O supervisor também fornece serviços de descoberta que enviam eventos de descoberta de dispositivo para o serviço de integração de dispositivos OPC UA para processamento, onde esses eventos resultam em atualizações no registro OPC UA.  Este artigo mostra como implantar o módulo OPC Myem um projeto existente.

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-twin-module)github.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem o PowerShell e as extensões do [PowerShell do AzureRM](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas. Se você ainda não tiver feito isso, clone este repositório GitHub. Execute os seguintes comandos no PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implantar serviços de IoT industrial no Azure

1. Na sua sessão do PowerShell, execute:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Siga os prompts para atribuir um nome ao grupo de recursos da implantação e um nome para o site.   O script implanta os microserviços e suas dependências de plataforma do Azure no grupo de recursos em sua assinatura do Azure.  O script também registra um aplicativo em seu locatário do Azure Active Directory (AAD) para oferecer suporte à autenticação baseada em OAUTH.  A implantação levará vários minutos.  Um exemplo do que você veria quando a solução for implantada com êxito:

   ![Industrial de IoT OPC para o projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui a URL do ponto de extremidade público. 

3. Quando o script for concluído com êxito, selecione se deseja salvar o `.env` arquivo.  Você precisará `.env` do arquivo de ambiente se quiser se conectar ao ponto de extremidade de nuvem usando ferramentas como o console ou implantar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de usar um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos, pois ele deve estar em conformidade com os requisitos de nomenclatura de recursos.  

### <a name="website-name-already-in-use"></a>O nome do site já está em uso

É possível que o nome do site já esteja em uso.  Se você encontrar esse erro, precisará usar um nome de aplicativo diferente.

### <a name="azure-active-directory-aad-registration"></a>Registro do Azure Active Directory (AAD)

O script de implantação tenta registrar dois aplicativos AAD no Azure Active Directory.  Dependendo de seus direitos para o locatário do AAD selecionado, a implantação poderá falhar. Há duas opções:

1. Se você escolher um locatário do AAD de uma lista de locatários, reinicie o script e escolha um diferente na lista.
2. Como alternativa, implante um locatário particular do AAD em outra assinatura, reinicie o script e selecione para usá-lo.

> [!WARNING]
> Nunca continue sem autenticação.  Se você optar por fazer isso, qualquer pessoa poderá acessar seus pontos de extremidade do OPC/Internet não autenticados.   Você sempre pode escolher a [opção de implantação "local"](howto-opc-twin-deploy-dependencies.md) para iniciar os aros.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implantar uma demonstração de serviços IoT todos-em-um industrial

Em vez de apenas os serviços e as dependências, você também pode implantar uma demonstração All-in-One.  O tudo em uma demonstração contém três servidores OPC UA, o módulo OPC My, todos os microserviços e um aplicativo Web de exemplo.  Ele destina-se a fins de demonstração.

1. Verifique se você tem um clone do repositório (veja acima). Abra um prompt do PowerShell na raiz do repositório e execute:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Siga os prompts para atribuir um novo nome ao grupo de recursos e um nome para o site.  Depois de implantado com êxito, o script exibirá a URL do ponto de extremidade do aplicativo Web.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script usa os seguintes parâmetros:

```powershell
-type
```

O tipo de implantação (VM, local, demonstração)

```powershell
-resourceGroupName
```

Pode ser o nome de um grupo de recursos existente ou um novo.

```powershell
-subscriptionId
```

Opcional, a ID da assinatura na qual os recursos serão implantados.

```powershell
-subscriptionName
```

Ou o nome da assinatura.

```powershell
-resourceGroupLocation
```

Opcional, um local do grupo de recursos. Se for especificado, tentará criar um novo grupo de recursos neste local.

```powershell
-aadApplicationName
```

Um nome para o aplicativo do AAD a ser registrado.

```powershell
-tenantId
```

Locatário do AAD a ser usado.

```powershell
-credentials
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a implantar o OPC "em um projeto existente, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Comunicação segura do cliente OPC e do OPC PLC](howto-opc-vault-deploy-existing-client-plc-communication.md)
