---
title: Reiniciar o servidor-CLI do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608367"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Reiniciar o banco de dados do Azure para PostgreSQL-servidor único usando o CLI do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para PostgreSQL. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.
 
> [!NOTE] 
> O tempo necessário para concluir uma reinicialização depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização. Talvez você também queira aumentar a frequência do ponto de verificação. Você também pode ajustar os valores de parâmetro relacionados ao ponto de verificação, incluindo `max_wal_size` . Também é recomendável executar `CHECKPOINT` o comando antes de reiniciar o servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções:
- Crie um [banco de dados do Azure para o servidor PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="restart-the-server"></a>Reinicie o servidor

Reinicie o servidor com o seguinte comando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para PostgreSQL](howto-configure-server-parameters-using-cli.md)
