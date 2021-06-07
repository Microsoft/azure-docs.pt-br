---
title: Implantar a Plataforma de IoT Industrial do Azure
description: Neste tutorial, você aprenderá a implantar a Plataforma de IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787195"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Tutorial: Implantar a Plataforma de IoT Industrial do Azure

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> * Sobre os principais componentes da Plataforma de IIoT
> * Sobre os diferentes tipos de instalação
> * Como implantar a Plataforma de IoT Industrial

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure precisa ser criada
- Baixar o [Git](https://git-scm.com/downloads)
- Os Registros de aplicativo do AAD (Azure Active Directory) usados para autenticação exigem direitos de Administrador global, Administrador de aplicativos ou Administrador de aplicativos de nuvem para fornecer consentimento do administrador em todo o locatário (confira abaixo mais opções)
- Os sistemas operacionais com suporte para implantação são Windows, Linux e Mac
- O IoT Edge dá suporte ao Windows 10 IoT Enterprise LTSC e ao Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Componentes principais

- Dependências mínimas: Hub IoT, Cosmos DB, Barramento de Serviço, Hub de Eventos, Key Vault, Armazenamento
- Dependências padrão: Serviço do SignalR + Mínimo, Registros de aplicativo do AAD, Serviço de Provisionamento de Dispositivos, Time Series Insights, Pasta de Trabalho, Log Analytics, Application Insights
- Microsserviços: Plano do Serviço de Aplicativo, Serviço de Aplicativo
- Interface do usuário (aplicativo Web): Plano do Serviço de Aplicativo (compartilhado com os microsserviços), Serviço de Aplicativo
- Simulação: máquina virtual, rede virtual, IoT Edge
- Serviço de Kubernetes do Azure

## <a name="installation-types"></a>Tipos de instalação

- Mínimo: dependências mínimas
- Local: dependências mínimas e padrão
- Serviços: locais e os microsserviços
- Simulação: dependências mínimas e os componentes de simulação
- Aplicativo: serviços e a interface do usuário
- Tudo (padrão): aplicativo e a simulação

## <a name="deployment"></a>Implantação

1. Para começar a implantação da Plataforma de IIoT, clone o repositório do prompt de comando ou do terminal.

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Inicie a implantação guiada e o script coletará as informações necessárias, como a conta do Azure, a assinatura, o recurso de destino e o nome do aplicativo e do grupo.

No Windows:
    ```
    .\deploy
    ```

No Linux ou Mac:
    ```
    ./deploy.sh
    ```

3. Os microsserviços e a interface do usuário são aplicativos Web que exigem autenticação, o que requer três Registros de aplicativo no AAD. Quando os direitos necessários estão ausentes, há duas soluções possíveis:

- Pedir ao administrador do AAD para conceder consentimento do administrador em todo o locatário para o aplicativo
- Um administrador do AAD pode criar os aplicativos do AAD. A pasta de implantação/scripts contém o script aad- register.ps1 para executar o registro do AAD separadamente da implantação. A saída do script é um arquivo que contém as informações relevantes a serem usadas como parte da implantação e deve ser passada para o script deploy.ps1 na mesma pasta usando o argumento -aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

Para implantações de produção que exigem preparo, reversão, dimensionamento e resiliência, a plataforma pode ser implantada no [AKS (Serviço de Kubernetes do Azure)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Referências:
- [Implantando a Plataforma de IoT Industrial do Azure](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Como implantar tudo em um](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Como implantar a plataforma no AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Próximas etapas
Agora que implantou a Plataforma de IIoT, você pode aprender a personalizar a configuração dos componentes:

> [!div class="nextstepaction"]
> [Personalizar a configuração dos componentes](tutorial-configure-industrial-iot-components.md)
