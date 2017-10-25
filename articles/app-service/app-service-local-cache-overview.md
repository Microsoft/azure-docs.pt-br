---
title: "Visão geral do cache local do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Este artigo descreve como habilitar, redimensionar e consultar o status do recurso de Cache Local do Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-local-cache-overview"></a>Visão geral do cache local do Serviço de Aplicativo do Azure
O conteúdo dos aplicativos Web do Azure é armazenado no Armazenamento do Azure e exibido de forma duradoura como um compartilhamento de conteúdo. Esse design tem como objetivo trabalhar com vários aplicativos e tem os seguintes atributos:  

* O conteúdo é compartilhado entre várias instâncias de VM (máquina virtual) do aplicativo Web.
* O conteúdo é duradouro e pode ser modificado pela execução dos aplicativos Web.
* Os arquivos de log e arquivos de dados de diagnóstico estão disponíveis na mesma pasta de conteúdo compartilhado.
* A publicação de novo conteúdo atualiza a pasta de conteúdo diretamente. Você pode exibir imediatamente o mesmo conteúdo por meio do site do SCM e do aplicativo Web em execução (normalmente, algumas tecnologias, como o ASP.NET, iniciam a reinicialização do aplicativo Web no caso de algumas alterações de arquivo para obter o conteúdo mais recente).

Embora muitos aplicativos Web usem um ou todos esses recursos, outros precisam somente de um repositório de conteúdo de alto desempenho somente leitura de onde eles possam ser executados com alta disponibilidade. Esses aplicativos podem se beneficiar de uma instância de máquina virtual de um cache local específico.

O recurso Cache Local do Serviço de Aplicativo do Azure fornece uma exibição de função web de seu conteúdo. Esse conteúdo é um cache de write-but-discard do seu conteúdo de armazenamento que é criado de forma assíncrona na inicialização do site. Quando o cache estiver pronto, o site será alternado para executar o conteúdo em cache. Aplicativos Web executados no Cache Local tem as seguintes vantagens:

* Eles estão imunes às latências que ocorrem quando eles acessam o conteúdo no Armazenamento do Azure.
* Ele está imune às atualizações planejadas ou tempos de inatividade não planejados, e a outras interrupções no Armazenamento do Azure que ocorram em servidores que fornecem o compartilhamento de conteúdo.
* Há uma quantidade menor de reinicializações do aplicativo devido a alterações no compartilhamento do armazenamento.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Como o Cache Local altera o comportamento do Serviço de Aplicativo
* O cache local é uma cópia das pastas /site e /siteextensions do aplicativo Web. Ele é criado na instância VM local na inicialização do aplicativo Web. O tamanho do cache local por aplicativo Web é limitado a 300 MB por padrão, mas você pode aumentá-lo para até 2 GB.
* O cache local é de leitura/gravação. No entanto, as modificações são descartadas quando o aplicativo Web mover as máquinas virtuais ou for reiniciado. Não utilize o Cache Local para aplicativos que armazenam dados de missão crítica no repositório de conteúdo.
* Os aplicativos Web podem continuar a gravar arquivos de log e dados de diagnóstico, como fazem atualmente. Arquivos de log e dados, no entanto, são armazenados localmente na máquina virtual. Em seguida, eles são copiados periodicamente para o repositório de conteúdo compartilhado. A cópia para o repositório de conteúdo compartilhado é a melhor opção. Podem ocorrer perdas de write-backs devido a uma falha repentina de uma instância de VM.
* Há uma alteração na estrutura da pastas das pastas LogFiles e Data para aplicativos Web que usam o Cache Local. Há agora subpastas nas pastas de armazenamento LogFiles e Data que seguem o padrão de nomenclatura de "identificador exclusivo" + carimbo de data/hora. Cada uma das subpastas corresponde a uma instância de VM na qual o aplicativo Web está em execução ou foi executado.  
* A publicação de alterações no aplicativo Web por meio de qualquer um dos mecanismos de publicação, publicará no repositório de conteúdo compartilhado. Isso ocorre por padrão, pois queremos que o conteúdo publicado seja duradouro. Para atualizar o cache local do aplicativo Web, é necessário reiniciá-lo. Isso parece uma etapa desnecessária? Para tornar o ciclo de vida contínuo, confira as informações neste artigo.
* D:\Home aponta para o cache local. D:\Local continua apontando para o armazenamento temporário específico da VM.
* A exibição de conteúdo padrão do site do SCM continua sendo aquela do repositório de conteúdo compartilhado.

## <a name="enable-local-cache-in-app-service"></a>Habilitar o Cache Local no Serviço de Aplicativo
Configure o Cache Local usando uma combinação de configurações de aplicativo reservadas. Você pode configurar essas configurações de aplicativo usando os seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurar o Cache Local usando o portal do Azure
<a name="Configure-Local-Cache-Portal"></a>

Habilitar o Cache Local com base em cada aplicativo Web usando esta configuração de aplicativo: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Configurações de aplicativo do portal do Azure: cache local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurar o Cache Local usando o Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

"properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Alterar a configuração de tamanho no Cache Local
Por padrão, o tamanho do cache local é de **300 MB**. Isso inclui as pastas /site e /siteextensions que são copiadas do repositório de conteúdo, bem como as pastas de dados e de logs criadas localmente. Para aumentar esse limite, use a configuração do aplicativo `WEBSITE_LOCAL_CACHE_SIZEINMB`. Você pode aumentar o tamanho de até **2 GB** (2000 MB) por aplicativo Web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Práticas recomendadas para usar o Cache Local do Serviço de Aplicativo
Recomendamos que você use o Cache Local em conjunto com o recurso [Ambientes de Preparo](../app-service/web-sites-staged-publishing.md) .

* Adicione a configuração de aplicativo *temporária* `WEBSITE_LOCAL_CACHE_OPTION` com o valor `Always` ao seu slot de **Produção**. Se estiver usando `WEBSITE_LOCAL_CACHE_SIZEINMB`, adicione-o também como uma configuração temporária ao slot de produção.
* Crie um slot de **preparo** e publique em seu slot de preparo. Normalmente, você não deve definir o slot de preparo para usar o cache local para habilitar um ciclo de vida contínuo de compilação-implantação-teste para preparo se tem os benefícios do Cache Local para o slot de produção.
* Teste seu site no Slot de preparo.  
* Quando estiver pronto, execute uma [operação de permuta](../app-service/web-sites-staged-publishing.md#Swap) entre seus slots de Preparo e de Produção.  
* As configurações temporárias incluem o nome e a parte temporária em um slot. Assim, quando o Slot de preparo for trocado pelo de Produção, ele herda as configurações do aplicativo do Cache Local. O Slot de produção recém-trocado será executado no cache local após alguns minutos e será aquecido como parte do aquecimento de slot após a troca. Assim, quando a permuta do slot é concluída, o slot de produção é executado no cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Como saber se o Cache Local se aplica ao meu aplicativo Web?
Se o seu aplicativo Web precisa de um repositório de conteúdo confiável e de alto desempenho, não usa o repositório de conteúdo para gravar dados críticos em tempo de execução e tem menos de 2 GB de tamanho total, a resposta é sim! Para obter o tamanho total das pastas /siteextensions e /site, você pode usar a extensão de site "Uso de Disco de Aplicativos Web do Azure."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como saber se meu site passou a usar o cache local?
Se estiver usando o recurso de Cache Local com os Ambientes de Preparo, a operação de permuta não é concluída até que o Cache Local seja aquecido. Para verificar se o seu site está em execução no cache local, verifique a variável de ambiente do processo de trabalho `WEBSITE_LOCALCACHE_READY`. Use as instruções descritas na página [variável de ambiente do processo de trabalho](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) para acessar as variáveis de ambiente do processo de trabalho em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Acabei de publicar novas alterações, mas meu aplicativo Web parece não tê-las. Por quê?
Se o seu aplicativo Web usar o Cache Local, será necessário reiniciar o site para obter as últimas alterações. Não quer publicar alterações em um site de produção? Confira as opções de slot na seção anterior de práticas recomendadas.

### <a name="where-are-my-logs"></a>Onde estão meus logs?
Com o cache local, as pastas de logs e de dados são um pouco diferentes. No entanto, a estrutura de suas subpastas permanece a mesma, tirando o fato de as subpastas ficarem aninhadas em uma subpasta com o formato "identificador da VM exclusivo" + carimbo de data/hora.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Meu Cache Local está habilitado, mas meu aplicativo Web ainda é reiniciado. Por que isso acontece? Pensei que o Cache Local ajudasse com reinicializações frequentes do aplicativo.
O Cache Local ajuda a evitar reinicializações de aplicativo Web relacionadas ao armazenamento. No entanto, seu aplicativo Web ainda pode sofrer reinicializações durante atualizações de infraestrutura planejadas da VM. A quantidade de reinicializações de aplicativo gerais que você enfrenta com o Cache Local habilitado deve ser menor.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>O Cache Local exclui os diretórios de serem copiados para a unidade local mais rápida?
Como parte da etapa que copia o conteúdo do armazenamento, qualquer pasta nomeada repositório é excluída. Isso ajuda com cenários em que o conteúdo do site pode conter um repositório de controle do código-fonte que pode não ser necessário na operação diária do aplicativo Web. 
