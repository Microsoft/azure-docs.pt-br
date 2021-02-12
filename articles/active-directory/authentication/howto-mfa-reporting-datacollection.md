---
title: Coleta de dados de usuário do Azure AD MFA-Azure Active Directory
description: Quais informações são usadas para ajudar a autenticar usuários pela autenticação multifator do Azure AD?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2124ca9fae76d87a695a2c453f7b222e7a08794
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742786"
---
# <a name="azure-ad-multi-factor-authentication-user-data-collection"></a>Coleta de dados do usuário da autenticação multifator do Azure AD

Este documento explica como localizar as informações de usuário coletadas pelo Azure Servidor de Autenticação Multifator (servidor MFA) e o Azure AD MFA (baseado em nuvem) no evento que você gostaria de removê-la.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informações coletadas

O servidor MFA, a extensão NPS e o adaptador de AD FS MFA do Azure AD do Windows Server 2016 coletam e armazenam as seguintes informações por 90 dias.

Tentativas de autenticação (usadas para relatórios e solução de problemas):

- Timestamp
- Nome de Usuário
- Nome
- Sobrenome
- Endereço de Email
- Grupo de usuários
- Método de autenticação (chamada telefônica, mensagem de texto, aplicativo móvel, Token OATH)
- Modo de chamada telefônica (Standard, PIN)
- Direção da mensagem de texto (unidirecional, bidirecional)
- Modo de mensagem de texto (OTP, OTP + PIN)
- Modo de aplicativo móvel (Standard, PIN)
- Modo de Token OATH (Standard, PIN)
- Tipo de autenticação
- Nome do Aplicativo
- Código do país da chamada primária
- Número do telefone da chamada primária
- Extensão da chamada primária
- Chamada primária autenticada
- Resultado da chamada primária
- Código do país da chamada de backup
- Número do telefone da chamada de backup
- Extensão da chamada de backup
- Chamada de backup autenticada
- Resultado da chamada de backup
- Geral autenticado
- Resultado geral
- Resultados
- Autenticada
- Result
- Iniciar endereço IP
- Dispositivos
- Token de dispositivo
- Tipo de dispositivo
- Versão do aplicativo móvel
- Versão do SO
- Result
- Verificar notificação usada

Ativações (tentativas de ativar uma conta no aplicativo móvel Microsoft Authenticator):
- Nome de Usuário
- Nome da Conta
- Timestamp
- Obter resultado do código de ativação
- Ativar êxito
- Ativar erro
- Resultado do status de ativação
- Nome do dispositivo
- Tipo de dispositivo
- Versão de Aplicativo
- Token OATH habilitado

Blocos (usados para determinar o estado bloqueado e para relatório):

- Bloquear carimbo de data/hora
- Bloquear por nome de usuário
- Nome de Usuário
- Código do país
- Número do telefone
- Número de telefone formatado
- Extensão
- Limpar extensão
- Bloqueado
- Motivo para bloquear
- Conclusão do carimbo de data/hora
- Motivo da conclusão
- Bloqueio de conta
- Alerta de fraude
- Alerta de fraude não bloqueado
- Linguagem

Bypass (usado para relatórios):

- Carimbo de data/hora de bypass
- Segundos do bypass
- Bypass por nome de usuário
- Nome de Usuário
- Código do país
- Número do telefone
- Número de telefone formatado
- Extensão
- Limpar extensão
- Motivo do bypass
- Conclusão do carimbo de data/hora
- Motivo da conclusão
- Bypass usado

Alterações (usadas para sincronizar as alterações do usuário no servidor MFA ou no Azure AD):

- Alterar carimbo de data/hora
- Nome de Usuário
- Novo código do país
- Novo número do telefone
- Nova extensão
- Novo código do país de backup
- Novo número do telefone de backup
- Nova extensão de backup
- Novo PIN
- Alteração de PIN necessária
- Antigo token de dispositivo
- Novo token de dispositivo

## <a name="gather-data-from-mfa-server"></a>Coletar dados do Servidor MFA

Para Servidor MFA versão 8.0 ou superior, o processo a seguir permite que os administradores exportem todos os dados para usuários:

- Faça logon no Servidor MFA, navegue até a guia **Usuários** selecione o usuário em questão e clique no botão **Editar**. Faça capturas de tela (Alt-PrtScn) de cada guia para fornecer ao usuário as configurações do MFA atuais.
- Na linha de comando do Servidor MFA, execute o comando a seguir alterando o caminho de acordo com a instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` para produzir um arquivo no formato JSON.
- Os administradores também podem usar a operação GetUserGdpr do SDK de Serviço Web como uma opção para exportar todas as informações de serviço de nuvem do MFA coletadas para um determinado usuário ou incorporar em uma solução de relatório maior.
- Pesquise `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` e todos os backups de " \<username> " (inclua as aspas na pesquisa) para localizar todas as instâncias do registro de usuário que estão sendo adicionadas ou alteradas.
   - Esses registros podem ser limitados (mas não eliminados) ao desmarcar **"registrar alterações de usuário"** no UX do servidor MFA, na seção log, na guia arquivos de log.
   - Se o syslog estiver configurado e **"registrar alterações de usuário"** estiver marcada na UX do servidor MFA, na seção log, na guia syslog, as entradas de log poderão ser coletadas do syslog.
- Outras ocorrências do nome de usuário no MultiFactorAuthSvc.log e outros arquivos de log do Servidor MFA referentes a tentativas de autenticação são consideradas operacionais e duplicadas para as informações fornecidas usando a exportação de MultiFactorAuthGdpr.exe ou GetUserGdpr do SDK de Serviço Web.

## <a name="delete-data-from-mfa-server"></a>Excluir dados do Servidor MFA

Na linha de comando do Servidor MFA, execute o comando a seguir, alterando o caminho de acordo com a instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` para excluir todas as informações de serviço de nuvem do MFA coletadas para esse usuário.

- Os dados incluídos na exportação são excluídos em tempo real, mas pode demorar até 30 dias para que dados operacionais ou duplicados sejam totalmente removidos.
- Os administradores também podem usar a operação DeleteUserGdpr do SDK de Serviço Web como uma opção para excluir todas as informações de serviço de nuvem do MFA coletadas para um determinado usuário ou incorporar em uma solução de relatório maior.

## <a name="gather-data-from-nps-extension"></a>Coletar dados da Extensão NPS

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação.

- As informações do MFA estão incluídas na exportação, o que pode demorar horas ou dias para ser concluído.
- As ocorrências do nome de usuário nos logs de eventos do AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh e AzureMfa/AuthZ/AuthZOptCh são consideradas operacionais e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-nps-extension"></a>Excluir dados da Extensão NPS

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de Encerrar Conta para excluir todas as informações de serviço de nuvem do MFA coletadas para esse usuário.

- Pode demorar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Coletar dados do Windows Server 2016 Azure AD MFA AD FS adaptador

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação. 

- As informações do MFA estão incluídas na exportação, o que pode demorar horas ou dias para ser concluído.
- Ocorrências do nome de usuário nos logs de eventos de Rastreamento/Depuração do AD FS (se habilitado) são consideradas operacionais e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Excluir dados do Windows Server 2016 Azure AD MFA AD FS adaptador

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de Encerrar Conta para excluir todas as informações de serviço de nuvem do MFA coletadas para esse usuário.

- Pode demorar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-for-azure-ad-mfa"></a>Coletar dados para o Azure AD MFA

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação.

- As informações do MFA estão incluídas na exportação, o que pode demorar horas ou dias para ser concluído.

## <a name="delete-data-for-azure-ad-mfa"></a>Excluir dados para o Azure AD MFA

Use o [portal de Privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de Encerrar Conta para excluir todas as informações de serviço de nuvem do MFA coletadas para esse usuário.

- Pode demorar até 30 dias para que os dados sejam totalmente removidos.

## <a name="next-steps"></a>Próximas etapas

[Relatórios do Servidor MFA](howto-mfa-reporting.md)
