---
title: Como implantar um módulo gêmeo OPC em um projeto existente do Azure | Microsoft Docs
description: Como implantar o gêmeo de OPC para um projeto existente.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6bdfeefc366734aa10dbaccec69bac8e0b41103f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451287"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implantar o gêmeo de OPC para um projeto existente

O módulo gêmeo de OPC é executado no IoT Edge e fornece vários serviços de borda para o gêmeo de OPC e os serviços de registro. 

O serviço de micro gêmeos OPC facilita a comunicação entre dispositivos de servidor OPC UA no chão de fábrica, por meio de um módulo do IoT Edge OPC gêmeo e operadores de fábrica. O serviço micro expõe os serviços de OPC UA (Procurar, leitura, gravação e execução) por meio de sua API REST. 

O microsserviço de registro de dispositivo OPC UA fornece acesso a aplicativos de OPC UA registrados e seus pontos de extremidade. Operadores e administradores podem registrar e cancelar o registro de novos aplicativos de OPC UA e procurar os existentes, incluindo seus pontos de extremidade. Além de aplicativo e gerenciamento de ponto de extremidade, o serviço de registro também cataloga módulos do IoT Edge do OPC gêmeo registrados. A API do serviço lhe dá controle da funcionalidade de módulo de borda, por exemplo, iniciar ou parar de descoberta do servidor (serviços de verificação) ou ativar novo Gêmeos de ponto de extremidade que podem ser acessados usando o serviço de micro Gêmeos de OPC.

O núcleo do módulo é a identidade do Supervisor. O supervisor gerencia Gêmeos de ponto de extremidade, que corresponde aos pontos de extremidade de servidor de OPC UA que são ativados usando a API de registro de OPC UA correspondente. Gêmeos esse ponto de extremidade traduzem OPC UA JSON recebido do serviço micro gêmeos OPC em execução na nuvem em mensagens binárias OPC UA, que são enviadas por um canal seguro com monitoração de estado para o ponto de extremidade gerenciado. O supervisor também fornece serviços de descoberta que enviam eventos de descoberta do dispositivo para o serviço de integração do dispositivo OPC UA para processamento, em que esses eventos resultam em atualizações para o registro de OPC UA.  Este artigo mostra como implantar o módulo gêmeo OPC em um projeto existente. 

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o GitHub [repositório](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem o PowerShell e [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) extensões instaladas.   Se você não o fez ainda, clone este repositório GitHub.  Abra um prompt de comando ou terminal e execute:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implantar serviços de IoT industriais no Azure

1. No prompt de comando aberta ou terminal, execute:

   ```bash
   deploy
   ```

2. Siga os prompts para atribuir um nome para o grupo de recursos da implantação e um nome para o site.   O script implanta os microsserviços e suas dependências de plataforma do Azure no grupo de recursos em sua assinatura do Azure.  O script também registra um aplicativo em seu locatário do Azure Active Directory (AAD) para dar suporte à autenticação com base em OAUTH.  Implantação levará vários minutos.  Um exemplo de que você veria depois que a solução é implantada com êxito:

   ![Implantar o gêmeo de OPC IoT industrial ao projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui a URL do ponto de extremidade público. 

3. Depois que o script for concluído com êxito, selecione se deseja salvar o arquivo. env.  Você precisa do arquivo de ambiente. env se você quiser se conectar ao ponto de extremidade de nuvem usando ferramentas como o Console ou implantar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Verifique se que você usar um nome de grupo de recursos de curto e simples.  O nome é usado também para recursos de nome como tal, que ele deve estar em conformidade com requisitos de nomenclatura de recurso.  

### <a name="website-name-already-in-use"></a>Nome do site já está em uso

É possível que o nome do site da Web já está em uso.  Se você encontrar esse erro, você precisa usar um nome de aplicativo diferente.

### <a name="azure-active-directory-aad-registration"></a>Registro do Azure Active Directory (AAD)

O script de implantação tenta registrar dois aplicativos AAD no Azure Active Directory.  Dependendo de seus direitos para o locatário do AAD selecionado, a implantação poderá falhar. Há duas opções:

1. Se você escolheu um locatário do AAD de uma lista de locatários, reinicie o script e escolher um diferente na lista.
2. Como alternativa, implante um locatário do AAD privado em outra assinatura, reinicie o script e selecione a usá-lo.

> [!WARNING]
> NUNCA continue sem autenticação.  Se você optar por fazer isso, qualquer pessoa pode acessar seus pontos de extremidade de Gêmeos de OPC da Internet não autenticada.   Você sempre pode escolher o [opção de implantação "local"](howto-opc-twin-deploy-dependencies.md) antes de se comprometer.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implantar uma demonstração de serviços de IoT industrial do all-in-one

Em vez de apenas a serviços e dependências, você também pode implantar uma demonstração do all-in-one.  Todos em uma demonstração contém três servidores OPC UA, o módulo gêmeo OPC, todos os microsserviços e um aplicativo Web de exemplo.  Ele é apenas para fins de demonstração.

1. Verifique se que você tem um clone do repositório (veja acima). Abra um prompt de comando ou terminal na raiz do repositório e execute:

   ```bash
   deploy -type demo
   ```

2. Siga os prompts para atribuir um novo nome para o grupo de recursos e um nome para o site.  Uma vez implantado com êxito, o script exibirá a URL do ponto de extremidade de aplicativo web.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script usa os seguintes parâmetros:

```bash
-type
```

O tipo de implantação (vm, local, demonstração)

```bash
-resourceGroupName
```

Pode ser o nome de um novo grupo de recursos ou um existente.

```bash
-subscriptionId
```

Opcional, a ID da assinatura em que os recursos serão implantados.

```bash
-subscriptionName
```

Ou o nome da assinatura.

```bash
-resourceGroupLocation
```

Opcional, um local do grupo de recursos. Se especificado, tentará criar um novo grupo de recursos neste local.

```bash
-aadApplicationName
```

Um nome para o aplicativo do AAD registrar com. 

```bash
-tenantId
```

Locatário do AAD para usar.

```bash
-credentials
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como implantar o gêmeo de OPC em um projeto existente, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Proteger a comunicação do cliente de OPC e OPC PLC](howto-opc-vault-deploy-existing-client-plc-communication.md)
