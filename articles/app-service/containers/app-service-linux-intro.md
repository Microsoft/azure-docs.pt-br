---
title: Introdução ao Serviço de Aplicativo no Linux – Azure | Microsoft Docs
description: Saiba mais sobre o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6bca1b067f5ec667e8b5da92a182a5618582b2f3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617438"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introdução ao Serviço de Aplicativo do Azure no Linux

[Serviço de Aplicativo do Azure](../overview.md) é uma plataforma de computação totalmente gerenciada otimizada para hospedagem de sites e aplicativos Web. Os clientes podem usar o Serviço de Aplicativo no Linux para hospedar aplicativos Web nativos no Linux para as pilhas de aplicativos com suporte. A seção [Idiomas](#languages) lista as pilhas de aplicativos que têm suporte no momento.

## <a name="languages"></a>Languages

O Serviço de Aplicativo no Linux dá suporte a inúmeras imagens internas a fim de aumentar a conectividade do desenvolvedor. Se não houver suporte para o tempo de execução que seu aplicativo requer nas imagens internas, haverá instruções sobre como [criar sua própria imagem do Docker](tutorial-custom-docker-image.md) a ser implantada no Aplicativo Web para Contêineres.

| Linguagem | Versões com suporte |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java * | O Tomcat 8.5, 9.0, Java SE, WildFly 14 (todos executando o JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

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

* Os clientes podem escalar ou reduzir verticalmente aplicativos Web, alterando a camada dos respectivos [planos do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Locais

Verifique o [Painel de Status do Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitações

O portal do Azure mostra somente os recursos que funcionam atualmente para o Aplicativo Web para Contêineres. Conforme habilitarmos mais recursos, eles ficarão visíveis no portal.

O Serviço de Aplicativo no Linux só tem suporte com os planos de serviço de aplicativo [Gratuito, Básico, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e não tem uma camada [Compartilhada](https://azure.microsoft.com/pricing/details/app-service/plans/). Não é possível criar o Aplicativo Web do Linux em um plano do Serviço de Aplicativo que já está hospedando Aplicativos Web não Linux.  

Com base em uma limitação atual, para o mesmo grupo de recursos, você não pode misturar aplicativos do Windows e do Linux na mesma região.

## <a name="troubleshooting"></a>solução de problemas

Quando seu aplicativo falhar ao iniciar ou você quiser verificar o log do aplicativo, verifique os logs do Docker no diretório LogFiles. Acesse esse diretório por meio de seu site SCM ou via FTP. Para registrar `stdout` e `stderr` por meio do contêiner, você precisa habilitar o **Registro em log do Contêiner do Docker** em **Logs de Serviço de Aplicativo**. A configuração entra em vigor imediatamente. O Serviço de Aplicativo detecta a alteração e reinicia o contêiner automaticamente.

Acesse o site SCM nas **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

![Como usar o Kudu para exibir os logs do Docker][1]

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir oferecem a você uma introdução ao Serviço de Aplicativo no Linux com aplicativos Web escritos em várias linguagens:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplicativos com vários contêineres](quickstart-multi-container.md)

Para obter mais informações sobre o Serviço de Aplicativo no Linux, confira:

* [Perguntas Frequentes do Serviço de Aplicativo para Linux](app-service-linux-faq.md)
* [Suporte de SSH para o Serviço de Aplicativo no Linux](app-service-linux-ssh-support.md)
* [Configurar ambientes de preparo no Serviço de Aplicativo](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implantação contínua do Hub do Docker](app-service-linux-ci-cd.md)

Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
