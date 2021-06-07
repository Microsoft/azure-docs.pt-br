---
title: Usar a CLI dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como começar a usar a CLI do Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a24b8b18dd109f1d8ed5acaa7de55ce5a3cc1eb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201100"
---
# <a name="use-the-azure-digital-twins-cli"></a>Usar a CLI dos Gêmeos Digitais do Azure

Além de gerenciar sua instância do gêmeos digital do Azure no portal do Azure, o gêmeos digital do Azure tem um **conjunto de comandos para o [CLI do Azure](/cli/azure/what-is-azure-cli)** que você pode usar para executar a maioria das ações principais com o serviço, incluindo:
* Gerenciando uma instância de gêmeos digital do Azure
* Gerenciando modelos
* Gerenciando o digital gêmeos
* Gerenciando relações de entrelaçamento
* Configurando pontos de extremidade
* Gerenciando [rotas](concepts-route-events.md)
* Configurando a [segurança](concepts-security.md) por meio do Azure RBAC (controle de acesso baseado em função)

O conjunto de comandos é chamado **AZ DT** e faz parte da [extensão do Azure IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension). Você pode exibir a lista completa de comandos e seu uso como parte da documentação de referência para o `az iot` conjunto de comandos: [referência de comando *AZ DT*](/cli/azure/ext/azure-iot/dt).

## <a name="uses-deploy-and-validate"></a>Usos (implantar e validar)

Além de geralmente gerenciar sua instância, a CLI também é uma ferramenta útil para implantação e validação.
* Os comandos de plano de controle podem ser usados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores em sua instância e verificar se as operações foram concluídas conforme o esperado.

## <a name="get-the-command-set"></a>Obter o conjunto de comandos

Os comandos do gêmeos digital do Azure fazem parte da [extensão do Azure IOT para CLI do Azure (Azure-IOT)](https://github.com/Azure/azure-iot-cli-extension), portanto, siga estas etapas para verificar se você tem a extensão mais recente `azure-iot` com os comandos **AZ DT** .

### <a name="cli-version-requirements"></a>Requisitos de versão da CLI

Se você estiver usando o CLI do Azure com o PowerShell, o pacote de extensão exigirá que sua versão do CLI do Azure seja **2.3.1** ou superior.

Você pode verificar a versão do seu CLI do Azure com este comando da CLI:
```azurecli
az --version
```

Para obter instruções sobre como instalar ou atualizar o CLI do Azure para uma versão mais recente, consulte [*instalar o CLI do Azure*](/cli/azure/install-azure-cli).

### <a name="get-the-extension"></a>Obter a extensão

Você pode ter certeza de que tem a versão mais recente da `azure-iot` extensão com essas etapas. Você pode executar esses comandos no [Azure cloud Shell](../cloud-shell/overview.md) ou em um [CLI do Azure local](/cli/azure/install-azure-cli).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Próximas etapas

Explore a CLI e seu conjunto completo de comandos por meio dos documentos de referência:
* [referência de comando *AZ DT*](/cli/azure/ext/azure-iot/dt)