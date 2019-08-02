---
title: Histórico de lançamento de versão do agente proteção por senha do AD do Azure – Azure Active Directory local
description: Lançamento de versão de documentos e o comportamento de histórico de alterações
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c024954053588537ac3363703876f716a38f41d9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702941"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico de versão do agente de Proteção de Senha do Azure AD

## <a name="121250"></a>1.2.125.0

Data de lançamento: 3/22/2019

* Corrigir erros de digitação secundárias nas mensagens de log de eventos
* Atualizar os termos de licença para a versão final da disponibilidade geral

> [!NOTE]
> Compilação 1.2.125.0 é a compilação de disponibilidade geral. Obrigado novamente para qualquer pessoa forneceu comentários sobre o produto!

## <a name="121160"></a>1.2.116.0

Data de lançamento: 3/13/2019

* Os Get-AzureADPasswordProtectionProxy e Get-AzureADPasswordProtectionDCAgent cmdlets agora a versão do software de relatório e do Azure atual locatário com as seguintes limitações:
  * Versão de software e dados de locatário do Azure só estão disponíveis para agentes do DC e proxies executando a versão 1.2.116.0 ou posterior.
  * Dados de locatário do Azure não podem ser reportados até que um novo registro (ou renovação) do proxy ou floresta ocorreu.
* O serviço de Proxy agora requer que o .NET 4.7 esteja instalado.
  * .NET 4.7 já deve estar instalado em um Windows Server totalmente atualizado. Se isso não for o caso, baixe e execute o instalador encontrado em [o .NET Framework 4.7 o instalador offline para o Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Em sistemas de núcleo do servidor pode ser necessário passar o sinalizador /q para o instalador do .NET 4.7 para que ele seja bem-sucedida.
* O serviço de Proxy agora dá suporte a atualização automática. A atualização automática usa o serviço de atualizador do Microsoft Azure AD Connect agente que é instalada lado a lado com o serviço de Proxy. A atualização automática está ativada por padrão.
* A atualização automática pode ser habilitada ou desabilitado usando o cmdlet Set-AzureADPasswordProtectionProxyConfiguration. A configuração atual pode ser consultada usando o cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* O binário do serviço para o serviço de agente do controlador de domínio foi renomeado para AzureADPasswordProtectionDCAgent.exe.
* O binário do serviço para o serviço de Proxy foi renomeado para AzureADPasswordProtectionProxy.exe. Regras de firewall talvez precise ser modificado conforme apropriado, se um firewall de terceiros estiver em uso.
  * Observação: se um arquivo de configuração de proxy http estava sendo usado em um Proxy anterior instalar, ele precisará ser renomeado (de *proxyservice.exe.config* à *AzureADPasswordProtectionProxy.exe.config*) depois de fazer isso atualização.
* Todas as verificações de tempo limitado de funcionalidades foram removidas do agente de controlador de domínio.
* Correções de bugs secundários e melhorias de registro em log.

## <a name="12650"></a>1.2.65.0

Data de lançamento: 1/2/2019

Alterações:

* Agora há compatibilidade com o serviço de agente e proxy do DC no Server Core. Os requisitos de sistema operacional mínimos são os mesmos de antes: Windows Server 2012 para agentes do DC e Windows Server 2012 R2 para proxies.
* Os cmdlets Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora são compatíveis com os modos de autenticação do Azure baseados em código.
* O cmdlet Get-AzureADPasswordProtectionDCAgent ignorará os pontos de conexão de serviço inválidos e/ou danificados. Isso corrige o bug em que os controladores de domínio, às vezes, apareciam várias vezes na saída.
* O cmdlet Get-AzureADPasswordProtectionSummaryReport ignorará os pontos de conexão de serviço inválidos e/ou danificados. Isso corrige o bug em que os controladores de domínio, às vezes, apareciam várias vezes na saída.
* O módulo do PowerShell Proxy agora é registrado de %ProgramFiles%\WindowsPowerShell\Modules. A variável de ambiente PSModulePath do computador não é mais modificada.
* Foi adicionado um novo cmdlet Get-AzureADPasswordProtectionProxy para auxiliar na descoberta de proxies registrados em um domínio ou floresta.
* O agente do DC usa uma nova pasta no compartilhamento sysvol para replicar políticas de senha e outros arquivos.

   Localização da pasta antiga:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nova localização da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Essa alteração foi feita para evitar avisos de “GPO órfão” falso-positivos.)

   > [!NOTE]
   > Nenhuma migração ou compartilhamento de dados será feita entre a pasta antiga e a nova pasta. As versões mais antigas do agente do DC continuarão a usar o local antigo até serem atualizadas para esta versão ou posterior. Depois que todos os agentes do DC estiverem executando a versão 1.2.65.0 ou posterior, a pasta sysvol antiga poderá ser excluída manualmente.

* O serviço de proxy e do agente do DC agora detectarão e excluirão cópias danificadas de seus respectivos pontos de conexão de serviço.
* Cada agente do DC excluirá periodicamente pontos de conexão de serviço danificado e obsoletos em seu domínio, para pontos de conexão do serviço de proxy e do agente do DC. Os pontos de conexão do serviço de proxy e do agente do DC são considerados obsoletos caso seu carimbo de data/hora de pulsação tenha mais de sete dias.
* O agente do DC agora renovará o certificado da floresta conforme necessário.
* O serviço de proxy agora renovará o certificado de proxy conforme necessário.
* Atualizações para o algoritmo de validação de senha: a lista de senhas banidas global e a lista de senhas banidas específicas do cliente (se configuradas) são combinadas antes de validações de senha. Uma determinada senha agora pode ser rejeitada (falha ou somente auditoria) se ele contiver tokens na lista global e na específica do cliente. A documentação do log de eventos foi atualizada para refletir isso. Consulte [Monitorar Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Correções de desempenho e robustez
* Log aprimorado

> [!WARNING]
> Funcionalidade de tempo limitado: o serviço de agente do DC nesta versão (1.2.65.0) parará o processamento de solicitações de validação de senha a partir de 1º de setembro de 2019.  Serviços de agente do DC em versões anteriores (consulte a lista abaixo) parará de processar a partir de 1º de julho de 2019. O serviço de agente do DC em todas as versões registrará 10021 eventos para o log de eventos do administrador nos dois meses anteriores a esses prazos. Todas as restrições de limite de tempo serão removidas na próxima versão com disponibilidade geral. O serviço de agente de Proxy não tem limitação de tempo em nenhuma versão, mas ainda deve ser atualizada para a versão mais recente para aproveitar todas as correções de bug subsequentes e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data de lançamento: 01/11/2018

Correções:

* Serviço de agente e o proxy do controlador de domínio não deve falhar devido a falhas de relação de confiança de certificado.
* Serviço de agente e o proxy do controlador de domínio têm correções adicionais para computadores em conformidade com FIPS.
* Serviço de proxy agora funcionará corretamente em um ambiente de chamadas rede somente 1.2 TLS.
* Menor de desempenho e correções de robustez
* Log aprimorado

Alterações:

* O nível de sistema operacional mínimo exigido para o serviço Proxy é agora o Windows Server 2012 R2. O nível de sistema operacional mínimo exigido para o serviço do agente DC permanece no Windows Server 2012.
* Agora, o serviço de Proxy requer o .NET versão 4.6.2.
* O algoritmo de validação de senha usa uma tabela de normalização de caracteres expandida. Isso pode resultar na rejeição de senhas aceitas em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data de lançamento: 17/8/2018

Correções:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora dão suporte a autenticação multifator
* Register-AzureADPasswordProtectionProxy requer um WS2012 ou domínio posterior no domínio para evitar erros de criptografia.
* O serviço de agente do controlador de domínio é mais confiável sobre como solicitar uma nova política de senha do Azure na inicialização.
* O serviço de agente do DC solicitará uma nova política de senha do Azure a cada hora se necessário, mas agora vai fazer isso em uma hora de início selecionada aleatoriamente.
* O serviço de agente do controlador de domínio não fará com que um atraso indefinido no novo anúncio do controlador de domínio quando instalado em um servidor antes da sua promoção como uma réplica.
* O serviço de agente do controlador de domínio agora respeitará a definição de configuração "Habilitar a proteção por senha no Active Directory do Windows Server"
* Ambos os instaladores de agente e o proxy do controlador de domínio agora serão compatível com a atualização in-loco ao atualizar para versões futuras.

> [!WARNING]
> Atualização in-loco de versão 1.1.10.3 não tem suporte e resultará em um erro de instalação. Para atualizar para a versão 1.2.10 ou posterior, você deve primeiro completamente desinstalar o software de serviço de agente e o proxy de controlador de domínio, e instalar a nova versão a partir do zero. Instalar e configurar o serviço proxy de proteção por senha do Azure Active Directory.  Não é necessário registrar novamente a floresta.

> [!NOTE]
> Atualizações in-loco do software agente DC exigirá uma reinicialização.

* Serviço de agente e o proxy do controlador de domínio agora oferecem suporte em execução em um servidor configurado para usar somente os algoritmos compatíveis com FIPS.
* Menor de desempenho e correções de robustez
* Log aprimorado

## <a name="11103"></a>1.1.10.3

Data de lançamento: 15/6/2018

Versão prévia pública inicial

## <a name="next-steps"></a>Próximas etapas

[Implantar Proteção de Senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
