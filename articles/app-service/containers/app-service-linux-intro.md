---
title: "Introdução ao Aplicativo Web para Contêineres do Azure | Microsoft Docs"
description: "Saiba mais sobre o Aplicativo Web para Contêineres do Azure."
keywords: "serviço de aplicativo do azure, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: bdfaf0cd7d4bb44b877a9f14891e540fd789b6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-web-app-for-containers"></a>Introdução ao Aplicativo Web para Contêineres do Azure

O [Aplicativo Web](../app-service-web-overview.md) é uma plataforma de computação totalmente gerenciada que foi otimizada para hospedar sites e aplicativos Web. Os clientes podem usar o Aplicativo Web para Contêineres para hospedar aplicativos Web nativos no Linux para as pilhas de aplicativos com suporte. As seções a seguir listam as pilhas de aplicativos que atualmente são suportadas.

## <a name="languages"></a>Linguagens

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Implantações

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambientes de preparo
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e CI/CD do DockerHub CI

## <a name="console-publishing-and-debugging"></a>Console, Publicação e Depuração

* Ambientes
* Implantações
* Console básico
* SSH

## <a name="scaling"></a>Dimensionamento

* Os clientes podem escalar ou reduzir verticalmente aplicativos Web, alterando a camada dos respectivos [planos do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Locais

Verifique o [Painel de Status do Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitações

O portal do Azure mostra somente os recursos que funcionam atualmente para o Aplicativo Web para Contêineres. Conforme habilitarmos mais recursos, eles ficarão visíveis no portal.

Alguns recursos, como a integração de rede virtual, a autenticação do Azure Active Directory/de terceiros ou as extensões de site do Kudu, ainda não estão disponíveis. Quando esses recursos estiverem disponíveis, atualizaremos nossa documentação e nosso blog sobre as alterações.

O Aplicativo Web para Contêineres só tem suporte nos planos de serviço de aplicativo [Básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem uma camada [Gratuita nem Compartilhada](https://azure.microsoft.com/pricing/details/app-service/plans/). Veja a seguir as restrições que também são importantes para o Aplicativo Web para Contêineres:

* Não é possível criar o Aplicativo Web para Contêineres em um plano do Serviço de Aplicativo que já esteja hospedando Aplicativos Web não Linux.
* Ao criar o Aplicativo Web para Contêineres em um grupo de recursos contendo Aplicativos Web não Linux, é necessário criar um plano do Serviço de Aplicativo em uma região diferente do plano do Serviço de Aplicativo existente.

## <a name="troubleshooting"></a>Solucionar problemas

Quando seu aplicativo falhar ao iniciar ou você quiser verificar o log do aplicativo, verifique os logs do Docker no diretório LogFiles. Acesse esse diretório por meio de seu site SCM ou via FTP.
Para registrar `stdout` e `stderr` por meio do contêiner, você precisa habilitar o **Registro em log do Contêiner do Docker** em **Logs de Diagnóstico**.

![Habilitando o log][2]

![Como usar o Kudu para exibir os logs do Docker][1]

Acesse o site SCM nas **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

## <a name="next-steps"></a>Próximas etapas

Veja os links a seguir para começar a usar o Serviço de Aplicativo no Linux. Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres do Azure](quickstart-custom-docker-image.md)
* [Usando o .NET Core no Aplicativo Web do Serviço de Aplicativo do Azure para Contêineres](quickstart-dotnetcore.md)
* [Usando o Ruby no Aplicativo Web do Serviço de Aplicativo do Azure para Contêineres](quickstart-ruby.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)
* [Suporte de SSH para o Aplicativo Web para Contêineres do Azure](app-service-linux-ssh-support.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo do Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação Contínua do Hub do Docker com o Aplicativo Web do Azure para Contêineres](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
