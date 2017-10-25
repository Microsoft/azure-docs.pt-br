---
title: "Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, perguntas frequentes, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: b783f7e8cfef991e7028ba4b4c7b1d0935397580
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure

Com a liberação do Aplicativo Web para Contêineres, estamos trabalhando para adicionar recursos e fazer melhorias em nossa plataforma. Este artigo fornece as respostas para as perguntas que nossos clientes têm feito recentemente.

Caso tenha uma pergunta, comente o artigo e responderemos assim que possível.

## <a name="built-in-images"></a>Imagens internas

**Quero dividir os contêineres de Docker internos que a plataforma oferece. Onde posso encontrar esses arquivos?**

É possível encontrar todos os arquivos do Docker no [GitHub](https://github.com/azure-app-service). É possível encontrar todos os contêineres do Docker no [Hub do Docker](https://hub.docker.com/u/appsvc/).

**Quais são os valores esperados para a seção Arquivo de Inicialização quando configuro a pilha de tempo de execução?**

Para o Node.js, é possível especificar o arquivo de configuração PM2 ou o arquivo de script. Para o .NET Core, especifique o nome da DLL compilada. Para o Ruby, é possível especificar o script Ruby com o qual você deseja inicializar o aplicativo.

## <a name="management"></a>Gerenciamento

**O que acontece quando eu pressiono o botão de reinicialização no portal do Azure?**

Esta ação é igual a um reinício do Docker.

**Posso usar o SSH (Secure Shell) para me conectar à VM (máquina virtual) do contêiner de aplicativo?**

Sim, você pode fazer isso por meio do site de gerenciamento do controle de origem (SCM) .

**Como criar um plano de Serviço de Aplicativo Linux por meio de um SDK ou um modelo do Azure Resource Manager?**

Você precisa definir o campo **reservado** do serviço de aplicativo como *true*.

## <a name="continuous-integration-and-deployment"></a>Integração e implantação contínuas

**Meu aplicativo Web ainda usa uma imagem de contêiner antiga do Docker depois que atualizei a imagem no Hub do Docker. Há suporte para implantação/integração contínua de contêineres personalizados?**

**R:** Para configurar a integração/implantação contínua para o Registro de Contêiner do Azure ou imagens do DockerHub, verifique o artigo [Implantação contínua com o Aplicativo Web para Contêineres do Azure](./app-service-linux-ci-cd.md). Para registros privados, é possível atualizar o contêiner parando e, em seguida, iniciando o Aplicativo Web. Se preferir, é possível alterar ou adicionar uma configuração de aplicativo fictício para forçar uma atualização do contêiner.

**Há suporte para ambientes de preparo?**

Sim.

**Posso usar a *implantação da Web* para implantar meu aplicativo Web?**

Sim, você precisa definir uma configuração de aplicativo chamada `WEBSITE_WEBDEPLOY_USE_SCM` como *false*.

## <a name="language-support"></a>Suporte ao idioma

**Há suporte para aplicativos .NET Core não compilados?**

Sim.

**Há suporte para o Criador como um gerenciador de dependências para aplicativos PHP?**

Sim. Durante a implantação de Git, o Kudu deve detectar que você está implantando um aplicativo PHP (graças à presença de um arquivo de composer.lock) e o Kudu então disparará uma instalação de criador para você.

## <a name="custom-containers"></a>Contêineres personalizados

**Estou usando meu próprio contêiner personalizado. Quero que a plataforma monte um compartilhamento SMB para o diretório `/home/`.**

Você pode definir a configuração de aplicativo `WEBSITES_ENABLE_APP_SERVICE_STORAGE` como *true* ou remova totalmente a configuração do aplicativo. Lembre-se de que isso causará reinicializações de contêiner quando o armazenamento de plataforma passar por uma alteração. 

>[!NOTE]
>Se a configuração de `WEBSITES_ENABLE_APP_SERVICE_STORAGE` for *false*, o diretório `/home/` não poderá mais ser compartilhado entre instâncias de escala e arquivos gravados ali não serão persistidos entre as reinicializações.

**Meu contêiner personalizado demora para iniciar e a plataforma o reinicia antes que ele termine a inicialização.**

Você pode configurar o tempo que a plataforma aguardará antes de reiniciar o contêiner. Para fazer isso, defina a configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` como o valor desejado. O valor padrão é 230 segundos e o valor máximo permitido é 600 segundos.

**Qual é o formato da URL do servidor do Registro privado?**

Forneça a URL completa do registro, incluindo `http://` ou `https://`.

**Qual é o formato do nome da imagem na opção de Registro privado?**

Adicione o nome de imagem completa, incluindo a URL de registro particular (por exemplo, myacr.azurecr.io/dotnet:latest). Os nomes de imagem que usam a porta personalizada [não podem ser inseridos por meio do portal](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Para definir `docker-custom-image-name`, use a [ferramenta de linha de comando `az`](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

**Posso expor mais de uma porta em minha imagem de contêiner personalizados?**

No momento, não damos suporte a mais de uma porta.

**Posso colocar meu próprio armazenamento?**

No momento, não damos suporte a trazer seu próprio armazenamento.

**Por que não é possível navegar os processos do meu contêiner personalizado em execução ou de sistema de arquivos do site do SCM?**

O site do SCM é executado em um contêiner separado. Não é possível verificar o sistema de arquivos ou os processos em execução do contêiner de aplicativo.

**Meu contêiner personalizado escuta uma porta diferente da porta 80. Como configurar meu aplicativo para rotear as solicitações para essa porta?**

Temos a detecção automática de porta. Também é possível especificar uma configuração de aplicativo chamada *WEBSITES_PORT* e fornecer a ela o valor do número da porta esperada. Anteriormente, a plataforma usava a configuração de aplicativo *PORTA*. Nós estamos planejando substituir essa configuração de aplicativo e usar *WEBSITES_PORT* exclusivamente.

**É necessário implementar o HTTPS no meu contêiner personalizado?**

Não, a plataforma manipula a terminação HTTPS nos front-ends compartilhados.

## <a name="pricing-and-sla"></a>Preço e SLA

**Qual é o preço agora que o serviço está disponível?**

Você será cobrado pela quantidade de horas em que o aplicativo é executado, com o preço normal do Serviço de Aplicativo do Azure.

## <a name="other-questions"></a>Outras perguntas

**Quais são os caracteres com suporte em nomes de configurações do aplicativo?**

Você pode usar apenas letras (A-Z, a-z), números (0-9) e o caractere de sublinhado (_) para as configurações do aplicativo.

**Onde posso solicitar os novos recursos?**

É possível enviar sua ideia para o [fórum de comentários dos Aplicativos Web](https://aka.ms/webapps-uservoice). Adicione “[Linux]” ao título de sua ideia.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Aplicativo Web do Azure para Contêineres?](app-service-linux-intro.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua com o Aplicativo Web do Azure para Contêineres](./app-service-linux-ci-cd.md)
