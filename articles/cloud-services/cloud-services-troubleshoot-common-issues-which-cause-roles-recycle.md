---
title: Causas comuns de reciclagem de funções de serviço de nuvem (clássico) | Microsoft Docs
description: Uma função de serviço de nuvem que é reciclada repentinamente pode causar um tempo de inatividade significativo. Veja alguns problemas comuns que causam a reciclagem de funções, que podem ajudar a reduzir o tempo de inatividade.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9610b32207f8367b9415c0881e49b54e24c49ad7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741155"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Problemas comuns que fazem com que as funções do serviço de nuvem do Azure (clássico) sejam recicladas

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Este artigo discute algumas das causas comuns dos problemas de implantação e fornece dicas de solução de problemas para ajudá-lo a resolvê-los. Uma indicação de que existe um problema com um aplicativo é quando há uma falha na inicialização da instância de função ou se ela alterna entre os estados inicializando, ocupado e parando.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de runtime ausentes
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
* A cadeia de conexão é especificada no [formato](../storage/common/storage-configure-connection-string.md)a seguir. (O protocolo deve ser especificado como HTTPS.) Substitua *AccountName* pelo nome da sua conta de armazenamento e *myAccountKey* pela sua chave de acesso:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Se estiver desenvolvendo seu aplicativo com as Ferramentas do Azure para o Microsoft Visual Studio, você poderá usar as páginas de propriedade para definir esse valor.

## <a name="exported-certificate-does-not-include-private-key"></a>O certificado exportado não inclui a chave privada
Para executar uma função Web em TLS, você deve garantir que o certificado de gerenciamento exportado inclua a chave privada. Se você usar o *Gerenciador de Certificados do Windows* para exportar o certificado, selecione **Sim** para a opção **Exportar a chave privada**. O certificado deve ser exportado no formato PFX, que é o único formato atualmente com suporte.

## <a name="next-steps"></a>Próximas etapas
Confira mais [artigos sobre solução de problemas](../index.yml?product=cloud-services&tag=top-support-issue) para serviços de nuvem.

Veja mais cenários de reciclagem da função na [Série de blogs de Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[Star]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Executar]: /previous-versions/azure/reference/ee772746(v=azure.100)