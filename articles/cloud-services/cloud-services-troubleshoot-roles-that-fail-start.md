---
title: Como solucionar problemas de funções com falha na inicialização | Microsoft Docs
description: Veja algumas razões comuns pelas quais uma função do Serviço de Nuvem pode falhar ao ser iniciada. Soluções para esses problemas também são fornecidas.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2453fa2d9b4e78b60d4922e09347799266a84cff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743212"
---
# <a name="troubleshoot-azure-cloud-service-classic-roles-that-fail-to-start"></a>Solucionar problemas de funções do serviço de nuvem do Azure (clássico) que falham ao iniciar

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

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
2. No **Gerenciador de Soluções**, localize o arquivo web.config e abra-o.
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
10. Digite o endereço e o nome do aplicativo Web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar até o site agora retornará mensagens de erro mais explícitas:

* Erro de servidor no aplicativo '/'.
* Descrição: ocorreu uma exceção sem tratamento durante a execução da solicitação da Web atual. Examine o rastreamento de pilha para obter mais informações sobre o erro e em que ponto ele ocorreu no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: não foi possível carregar o arquivo ou assembly ‘Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’ ou uma de suas dependências. O sistema não pode encontrar o arquivo especificado.

Por exemplo:

![Erro de servidor explícito no aplicativo '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador de computação
Você pode usar o emulador de computação Microsoft Azure para diagnosticar e solucionar problemas de dependências ausentes e web.config erros.

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
3. Depois que a instância for iniciada, abra **Gerenciador de Servidores**.
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
Confira mais [artigos sobre solução de problemas](../index.yml?product=cloud-services&tag=top-support-issue) para serviços de nuvem.

Para saber como solucionar problemas das funções do serviço de nuvem usando os dados de diagnóstico do computador Azure PaaS, confira a [série de blogs de Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
