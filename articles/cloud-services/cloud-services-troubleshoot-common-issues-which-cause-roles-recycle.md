---
title: Causas comuns da reciclagem de funções do Serviço de Nuvem | Microsoft Docs
description: Uma função de serviço de nuvem que é reciclada repentinamente pode causar um tempo de inatividade significativo. Veja alguns problemas comuns que causam a reciclagem de funções, que podem ajudar a reduzir o tempo de inatividade.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 2a9214b918883e493ebe5c93fc7f56e7ce9c77ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652206"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que causam a reciclagem de funções
Este artigo discute algumas das causas comuns dos problemas de implantação e fornece dicas de solução de problemas para ajudá-lo a resolvê-los. Uma indicação de que existe um problema com um aplicativo é quando há uma falha na inicialização da instância de função ou se ela alterna entre os estados inicializando, ocupado e parando.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo de execução ausentes
Se uma função em seu aplicativo depender de um assembly que não faça parte do .NET Framework ou da biblioteca gerenciada pelo Azure, você deverá incluir explicitamente esse assembly no pacote de aplicativos. Tenha em mente que outras estruturas da Microsoft não estão disponíveis no Azure por padrão. Se a sua função depender de uma estrutura desse tipo, você deverá adicionar esses assemblies ao pacote de aplicativos.

Antes de compilar e empacotar seu aplicativo, verifique o seguinte:

* Se estiver usando o Visual Studio, verifique se a propriedade de **Copy Local** está definida como **True** para cada assembly referenciado em seu projeto que não faça parte do SDK do Azure ou do .NET Framework.
* Verifique se o arquivo web.config não faz referência a nenhum assembly não usado no elemento compilation.
* A **Ação de Compilação** de cada arquivo .cshtml é definida como **Conteúdo**. Isso garante que os arquivos sejam exibidos corretamente no pacote e habilita outros arquivos referenciados a aparecerem nele.

## <a name="assembly-targets-wrong-platform"></a>Plataforma incorreta de destinos de assembly
O Azure é um ambiente de 64 bits. Portanto, os assemblies do .NET compilados para um destino de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>A função gera exceções sem tratamento durante a inicialização ou interrupção
Todas as exceções geradas pelos métodos da classe [RoleEntryPoint], que inclui os métodos [OnStart], [OnStop] e [Run], são exceções sem tratamento. Se ocorrer uma exceção sem tratamento em um desses métodos, a função será reciclada. Se a função estiver sendo reciclada repetidamente, ela poderá gerar uma exceção sem tratamento a cada tentativa de inicialização.

## <a name="role-returns-from-run-method"></a>A função é retornada do método Run
O método [Run] se destina a ser executado por tempo indeterminado. Se o seu código substituir o método [Run] , ele deverá ser suspenso por tempo indeterminado. Se o método [Run] for retornado, a função será reciclada.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Configuração incorreta de DiagnosticsConnectionString
Se o aplicativo usar o Diagnóstico do Azure, o arquivo de configuração de serviço deverá especificar a definição de configuração `DiagnosticsConnectionString` . Essa configuração deve especificar uma conexão HTTPS à sua conta de armazenamento no Azure.

Para garantir que a configuração `DiagnosticsConnectionString` esteja correta antes de implantar seu pacote de aplicativos no Azure, verifique o seguinte:  

* A configuração `DiagnosticsConnectionString` aponta para uma conta de armazenamento válida no Azure.  
  Por padrão, essa configuração aponta para a conta de armazenamento emulada, portanto você deve alterar explicitamente essa configuração antes de implantar o pacote de aplicativos. Se você não alterar essa configuração, uma exceção será gerada quando a instância de função tentar iniciar o monitor de diagnóstico. Isso poderá fazer com que a instância de função seja reciclada por tempo indeterminado.
* A cadeia de conexão é especificada no [formato](../storage/common/storage-configure-connection-string.md)a seguir. (O protocolo deve ser especificado como HTTPS). Substitua *MyAccountName* pelo nome da sua conta de armazenamento e *MyAccountKey* pela sua chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se estiver desenvolvendo seu aplicativo com as Ferramentas do Azure para o Microsoft Visual Studio, você poderá usar as páginas de propriedade para definir esse valor.

## <a name="exported-certificate-does-not-include-private-key"></a>O certificado exportado não inclui a chave privada
Para executar uma função web por meio do SSL, você deve garantir que seu certificado de gerenciamento exportado inclui a chave privada. Se você usar o *Gerenciador de Certificados do Windows* para exportar o certificado, selecione **Sim** para a opção **Exportar a chave privada**. O certificado deve ser exportado no formato PFX, que é o único formato atualmente com suporte.

## <a name="next-steps"></a>Próximas etapas
Confira mais [artigos sobre solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Veja mais cenários de reciclagem da função na [Série de blogs de Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
