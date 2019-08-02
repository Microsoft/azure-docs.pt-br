---
title: Como implantar as dependências de nuvem do gêmeo de OPC no Azure | Microsoft Docs
description: Como implantar as dependências do Azure do gêmeo de OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 95c2130727c0cef889771c181fec53557a2a4b0f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603706"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

Este artigo explica como implantar apenas a necessidade de serviços da plataforma Azure para fazer desenvolvimento local e depuração.   No final, você terá implantado um grupo de recursos que contém tudo o que você precisa para desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implantar os serviços de plataforma do Azure

1. Verifique se você tem o PowerShell e [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) extensões instaladas.  Abra um prompt de comando ou terminal e execute:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga os prompts para atribuir um nome para o grupo de recursos para sua implantação.  O script implanta apenas as dependências para esse grupo de recursos em sua assinatura do Azure, mas não os serviços micro.  O script também registra um aplicativo no Azure Active Directory.  Isso é necessário para dar suporte à autenticação com base em OAUTH.  A implantação pode levar vários minutos.

3. Depois que o script for concluído, você pode selecionar para salvar o arquivo. env.  O arquivo. env do ambiente é o arquivo de configuração de todos os serviços e ferramentas que você deseja executar no computador de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Verifique se que você usar um nome de grupo de recursos de curto e simples.  O nome é usado também para recursos de nome como tal, que ele deve estar em conformidade com requisitos de nomenclatura de recurso.  

### <a name="azure-active-directory-aad-registration"></a>Registro do Azure Active Directory (AAD)

O script de implantação tenta registrar aplicativos do AAD no Azure Active Directory.  Dependendo de seus direitos para o locatário do AAD selecionado, isso pode falhar.   Há 3 opções:

1. Se você escolheu um locatário do AAD de uma lista de locatários, reinicie o script e escolher um diferente na lista.
2. Como alternativa, implante um locatário do AAD privado, reiniciar o script e selecione a usá-lo.
3. Continue sem autenticação.  Uma vez que você estiver executando seus serviços micro localmente, isso é aceitável, mas não imita os ambientes de produção.  

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou serviços OPC gêmeo com êxito a um projeto existente, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Saiba mais sobre como implantar módulos Gêmeos de OPC](howto-opc-twin-deploy-modules.md)
