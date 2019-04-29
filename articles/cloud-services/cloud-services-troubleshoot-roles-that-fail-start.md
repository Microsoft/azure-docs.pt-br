---
title: Como solucionar problemas de funções com falha na inicialização | Microsoft Docs
description: Veja algumas razões comuns pelas quais uma função do Serviço de Nuvem pode falhar ao ser iniciada. Soluções para esses problemas também são fornecidas.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d2daae2a3317d3b48748262d87ab8d7f7e13f2b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653331"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Solução de problemas de funções do serviço de nuvem com falha de inicialização
Veja alguns problemas comuns e soluções relacionadas às funções do serviço de nuvem do Azure com falha na inicialização.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs ou dependências ausentes
Funções sem resposta e funções que alternam entre os estados **Inicializando**, **Ocupado** e **Parando** podem ser causadas por DLLs ou assemblies ausentes.

Os sintomas de DLLs ou assemblies ausentes podem ser:

* A instância de função está alternando entre os estados **Inicializando**, **Ocupado** e **Parando**.
* A instância de função foi movida para **Pronto** , mas, se você navegar até seu aplicativo Web, a página não será mostrada.

Há vários métodos recomendados para investigar esses problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Como diagnosticar problemas de DLL ausente em uma função Web
Quando você navega para um site que é implantado em uma função Web e o navegador exibe um erro de servidor semelhante ao mostrado abaixo, isso pode indicar que uma DLL está ausente.

![Erro de servidor no aplicativo '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desativando erros personalizados
Informações sobre erros mais completas podem ser exibidas pela configuração de web.config da função Web para definir o modo de erro personalizado como Desativado e pela reimplantação do serviço.

Para exibir erros mais completos sem usar a Área de Trabalho Remota:

1. Abra a solução no Microsoft Visual Studio.
2. No **Gerenciador de Soluções**, localize o arquivo web.config e abri-lo.
3. No arquivo web.config, localize a seção system.web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```
4. Salve o arquivo.
5. Empacote e implante novamente o serviço.

Depois que o serviço for implantado novamente, você verá uma mensagem de erro com o nome do assembly ou DLL ausente.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas exibindo o erro remotamente
Você pode usar a Área de Trabalho Remota para acessar a função e exibir informações de erros mais completas remotamente. Use as seguintes etapas para exibir os erros usando a Área de Trabalho Remota:

1. Verifique se o Azure SDK 1.3 ou posterior está instalado.
2. Durante a implantação da solução usando o Visual Studio, habilite a Área de Trabalho Remota. Para saber mais, veja [Habilitar Conexão de Área de Trabalho Remota para uma função nos Serviços de Nuvem do Azure usando o Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. No portal do Microsoft Azure, depois que a instância mostrar um status de **Pronto**, acesse-a remotamente. Para obter mais informações sobre como usar a área de trabalho com Serviços de Nuvem, consulte [Remoto em instâncias de função](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Entre na máquina virtual usando as credenciais especificadas durante a configuração da Área de Trabalho Remota.
6. Abra uma janela de comando.
7. Digite `IPconfig`.
8. Observe o valor do Endereço IPV4.
9. Abra o Internet Explorer.
10. Digite o endereço e o nome do aplicativo Web. Por exemplo: `http://<IPV4 Address>/default.aspx`.

Navegar até o site agora retornará mensagens de erro mais explícitas:

* Erro de servidor no aplicativo '/'.
* Descrição: Ocorreu uma exceção sem tratamento durante a execução da solicitação da web atual. Examine o rastreamento de pilha para obter mais informações sobre o erro e em que ponto ele ocorreu no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: Não foi possível carregar arquivo ou assembly ' Microsoft.WindowsAzure.StorageClient, versão Version=1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35' ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado.

Por exemplo: 

![Erro de servidor explícito no aplicativo '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador de computação
Você pode usar o emulador de computação do Microsoft Azure para diagnosticar e solucionar problemas de dependências ausentes e erros de web.config.

Para obter melhores resultados ao usar esse método de diagnóstico, você deve usar um computador ou uma máquina virtual com uma instalação limpa do Windows. Para simular melhor o ambiente do Azure, use o Windows Server 2008 R2 x64.

1. Instalar a versão autônoma do [SDK do Azure](https://azure.microsoft.com/downloads/).
2. No computador de desenvolvimento, compile o projeto do serviço de nuvem.
3. No Windows Explorer, navegue até a pasta bin\debug do projeto do serviço de nuvem.
4. Copie a pasta .csx e o arquivo .cscfg para o computador que você está usando para depurar os problemas.
5. No computador limpo, abra uma janela de prompt de comando do SDK do Azure e digite `csrun.exe /devstore:start`.
6. No prompt de comando, digite `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Quando a função for iniciada, você verá as informações de erro detalhadas no Internet Explorer. Você também pode usar ferramentas de solução de problemas padrão do Windows para ajudar a diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando o IntelliTrace
Para as funções Web e de trabalho que usam o .NET Framework 4, você pode usar o [IntelliTrace](/visualstudio/debugger/intellitrace), que está disponível no Microsoft Visual Studio Enterprise.

Siga estas etapas para implantar o serviço com o IntelliTrace habilitado:

1. Confirme se o Azure SDK 1.3 ou posterior está instalado.
2. Implante a solução usando o Visual Studio. Durante a implantação, marque a caixa de seleção **Habilitar IntelliTrace para funções do .NET 4** .
3. Quando a instância for iniciada, abra o **Gerenciador de Servidores**.
4. Expanda o nó **Azure\\Serviços de Nuvem** e localize a implantação.
5. Expanda a implantação até ver as instâncias de função. Clique com o botão direito do mouse em uma das instâncias.
6. Escolha **Exibir logs do IntelliTrace**. O **Resumo do IntelliTrace** será aberto.
7. Localize a seção de exceções do resumo. Se houver exceções, a seção será rotulada como **Dados de Exceção**.
8. Expanda os **Dados de Exceção** e procure erros **System.IO.FileNotFoundException** semelhantes ao seguinte:

![Dados de exceção, arquivo ou assembly ausente](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Lidar com DLLs e assemblies ausentes
Para resolver erros de DLL e assembly ausente, siga estas etapas:

1. Abra a solução no Visual Studio.
2. No **Gerenciador de Soluções**, abra a pasta **Referências**.
3. Clique no assembly identificado no erro.
4. No painel **Propriedades**, localize a **propriedade Copy Local** e defina o valor como **True**.
5. Reimplante o serviço de nuvem.

Após verificar se todos os erros foram corrigidos, você poderá implantar o serviço sem marcar a caixa de seleção **Habilitar IntelliTrace para funções do .NET 4** .

## <a name="next-steps"></a>Próximas etapas
Confira mais [artigos sobre solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, confira a [série de blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
