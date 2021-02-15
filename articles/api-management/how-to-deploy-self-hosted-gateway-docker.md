---
title: Implantar o gateway auto-hospedado no Docker | Microsoft Docs
description: Saiba como implantar um componente de gateway auto-hospedado do gerenciamento de API do Azure para o Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82205086"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implantar um gateway auto-hospedado do gerenciamento de API do Azure para o Docker

Este artigo fornece as etapas para implantar o componente de gateway auto-hospedado do gerenciamento de API do Azure em um ambiente do Docker.

> [!NOTE]
> Hospedar o gateway auto-hospedado no Docker é mais adequado para casos de uso de avaliação e desenvolvimento. O kubernetes é recomendado para uso em produção. Consulte [este](how-to-deploy-self-hosted-gateway-kubernetes.md) documento para saber como implantar o gateway auto-hospedado no kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
- Crie um ambiente do Docker. O [Docker for Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte a [documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, seus recursos e a documentação abrangente do Docker em si.
- [Provisionar um recurso de gateway em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> O gateway auto-hospedado é empacotado como um contêiner do Docker baseado em Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Implantar o gateway auto-hospedado no Docker

1. Selecione **gateways** em **implantação e infraestrutura**.
2. Selecione o recurso de gateway que você pretende implantar.
3. Selecione **implantação**.
4. Observe que um token de acesso na caixa de texto de **token** foi gerado automaticamente para você usando os valores padrão de **expiração** e **chave secreta** . Se necessário, escolha os valores desejados em um ou ambos os controles para gerar um novo token.
4. Verifique se o **Docker** está selecionado em **scripts de implantação**.
5. Selecione o link **env. conf** do arquivo ao lado do **ambiente** para baixar o arquivo.
6. Selecione o ícone de **cópia** localizado na extremidade direita da caixa de texto **executar** para copiar o comando Docker para a área de transferência.
7. Cole o comando na janela de terminal (ou comando). Ajuste os mapeamentos de porta e o nome do contêiner conforme necessário. Observe que o comando supõe que o arquivo de ambiente baixado está presente no diretório atual.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Execute o comando. O comando instrui o ambiente do Docker a executar o contêiner usando a [imagem de contêiner](https://aka.ms/apim/sputnik/dhub) baixada do registro de contêiner da Microsoft e mapear as portas HTTP (8080) e HTTPS (8081) do contêiner para as portas 80 e 443 no host.
9. Execute o comando abaixo para verificar se o contêiner de gateway está em execução:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Volte para portal do Azure, clique em **visão geral** e confirme se o contêiner de gateway auto-hospedado que você acabou de implantar está relatando um status Íntegro.

![status do gateway](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Use <code>console docker container logs <gateway-name></code> o comando para exibir um instantâneo do log de gateway auto-hospedado.
>
> Use o <code>docker container logs --help</code> comando para ver todas as opções de exibição de log.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md).
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
