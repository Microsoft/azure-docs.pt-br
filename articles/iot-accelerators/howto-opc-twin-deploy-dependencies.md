---
title: Como implantar as dependências de nuvem do OPC entrelaçadas no Azure | Microsoft Docs
description: Este artigo descreve como implantar as dependências do Azure de OPC/desenvolvedor necessárias para fazer o desenvolvimento e a depuração locais.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646002"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantando dependências para desenvolvimento local

> [!IMPORTANT]
> Enquanto atualizamos este artigo, confira [IoT Industrial do Azure](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como implantar apenas os serviços da plataforma Azure necessários para o desenvolvimento e a depuração locais.   No final, você terá um grupo de recursos implantado que contém tudo o que precisa para desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implantar serviços da plataforma Azure

1. Verifique se você tem o PowerShell e as extensões do [PowerShell do AzureRM](/powershell/azure/azurerm/install-azurerm-ps) instaladas.  Abra um prompt de comando ou terminal e execute:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga os prompts para atribuir um nome ao grupo de recursos para sua implantação.  O script implanta apenas as dependências para esse grupo de recursos em sua assinatura do Azure, mas não os micro serviços.  O script também registra um aplicativo no Azure AD.  Isso é necessário para dar suporte à autenticação baseada em OAUTH.  A implantação pode levar vários minutos.

3. Quando o script for concluído, você poderá optar por salvar o arquivo. env.  O arquivo de ambiente. env é o arquivo de configuração de todos os serviços e ferramentas que você deseja executar em seu computador de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de usar um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos, pois ele deve estar em conformidade com os requisitos de nomenclatura de recursos.  

### <a name="azure-active-directory-ad-registration"></a>Registro do Azure Active Directory (AD)

O script de implantação tenta registrar os aplicativos do Azure AD no Azure AD.  Dependendo de seus direitos para o locatário do Azure AD selecionado, isso pode falhar. Há três opções:

1. Se você escolher um locatário do Azure AD de uma lista de locatários, reinicie o script e escolha um diferente na lista.
2. Como alternativa, implante um locatário privado do Azure AD, reinicie o script e selecione para usá-lo.
3. Continuar sem autenticação.  Como você está executando seus micro Services localmente, isso é aceitável, mas não imita ambientes de produção.  

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou com êxito os serviços do OPC filepara um projeto existente, esta é a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Saiba mais sobre como implantar módulos do OPC entrelaçar](howto-opc-twin-deploy-modules.md)