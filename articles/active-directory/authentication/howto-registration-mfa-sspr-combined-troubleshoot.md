---
title: Solucionar problemas de registro combinado-Azure Active Directory
description: Solucionar problemas da autenticação multifator do Azure AD e registro combinado de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610770"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Solucionando problemas de registro de informações de segurança combinadas

As informações neste artigo destinam-se a orientar os administradores que estão Solucionando problemas relatados pelos usuários da experiência de registro combinada.

## <a name="audit-logs"></a>Logs de auditoria

Os eventos registrados para registro combinado estão no serviço de métodos de autenticação nos logs de auditoria do Azure AD.

![Interface de logs de auditoria do Azure AD mostrando eventos de registro](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A tabela a seguir lista todos os eventos de auditoria gerados pelo registro combinado:

| Atividade | Status | Motivo | Descrição |
| --- | --- | --- | --- |
| Usuário registrou todas as informações de segurança necessárias | Êxito | Usuário registrou todas as informações de segurança necessárias. | Esse evento ocorre quando um usuário concluiu o registro com êxito.|
| Usuário registrou todas as informações de segurança necessárias | Falha | O usuário cancelou o registro das informações de segurança. | Esse evento ocorre quando um usuário cancela o registro do modo de interrupção.|
| Informações de segurança registradas pelo usuário | Êxito | *Método* registrado pelo usuário. | Esse evento ocorre quando um usuário registra um método individual. O *método* pode ser o aplicativo autenticador, telefone, email, perguntas de segurança, senha de aplicativo, telefone alternativo e assim por diante.| 
| Informações de segurança revisadas pelo usuário | Êxito | O usuário analisou com êxito as informações de segurança. | Esse evento ocorre quando um usuário seleciona uma **boa aparência** na página de revisão informações de segurança.|
| Informações de segurança revisadas pelo usuário | Falha | O usuário não pôde examinar as informações de segurança. | Esse evento ocorre quando um usuário seleciona uma **boa aparência** na página de revisão informações de segurança, mas algo falha no back-end.|
| Informações de segurança excluídas pelo usuário | Êxito | *Método* excluído pelo usuário. | Esse evento ocorre quando um usuário exclui um método individual. O *método* pode ser o aplicativo autenticador, telefone, email, perguntas de segurança, senha de aplicativo, telefone alternativo e assim por diante.|
| Informações de segurança excluídas pelo usuário | Falha | O usuário não pôde excluir o *método*. | Esse evento ocorre quando um usuário tenta excluir um método, mas a tentativa falha por algum motivo. O *método* pode ser o aplicativo autenticador, telefone, email, perguntas de segurança, senha de aplicativo, telefone alternativo e assim por diante.|
| Informações de segurança padrão alteradas pelo usuário | Êxito | O usuário alterou as informações de segurança padrão para o *método*. | Esse evento ocorre quando um usuário altera o método padrão. O *método* pode ser a notificação do aplicativo autenticador, um código do meu aplicativo autenticador ou token, chamar + x xxxxxxxxxx, enviar um código para + x xxxxxxxxx e assim por diante.|
| Informações de segurança padrão alteradas pelo usuário | Falha | O usuário não pôde alterar as informações de segurança padrão para o *método*. | Esse evento ocorre quando um usuário tenta alterar o método padrão, mas a tentativa falha por algum motivo. O *método* pode ser a notificação do aplicativo autenticador, um código do meu aplicativo autenticador ou token, chamar + x xxxxxxxxxx, enviar um código para + x xxxxxxxxx e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Solucionando problemas no modo de interrupção

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Não estou vendo os métodos que esperava ver. | 1. Verifique se o usuário tem uma função de administrador do Azure AD. Em caso afirmativo, exiba as diferenças da política de administração do SSPR. <br> 2. Determine se o usuário está sendo interrompido devido à imposição de registro da autenticação multifator ou à imposição de registro SSPR. Consulte o [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "modos de registro combinados" para determinar quais métodos devem ser mostrados. <br> 3. Determine o quanto recentemente a política de autenticação multifator ou SSPR foi alterada. Se a alteração foi recente, pode levar algum tempo para que a política atualizada seja propagada.|

## <a name="troubleshooting-manage-mode"></a>Solucionando problemas no modo de gerenciamento

| Sintoma | Etapas para solucionar problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determine se o método está habilitado para autenticação multifator ou para SSPR. <br> 2. se o método estiver habilitado, salve as políticas novamente e aguarde 1-2 horas antes de testar novamente. <br> 3. se o método estiver habilitado, verifique se o usuário ainda não configurou o número máximo desse método que eles têm permissão para configurar.|

## <a name="disable-combined-registration"></a>Desabilitar registro combinado

Quando um usuário registra um número de telefone e/ou aplicativo móvel na nova experiência combinada, nosso serviço carimba um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos nesse usuário. Essa funcionalidade permite que o usuário execute a autenticação multifator com esses métodos sempre que a autenticação multifator for necessária.

Se um administrador habilita a visualização, os usuários se registram pela nova experiência e, em seguida, o administrador desabilita a visualização, os usuários podem ser registrados inadvertidamente para autenticação multifator também.

Se um usuário que concluiu o registro combinado ir para a página de registro SSPR (redefinição de senha de autoatendimento) atual em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , será solicitado que o usuário execute a autenticação multifator antes de acessar essa página. Esta etapa é esperada do ponto de vista técnico, mas é nova para os usuários que foram registrados anteriormente apenas para SSPR. Embora essa etapa extra aprimore a postura de segurança do usuário fornecendo outro nível de segurança, os administradores podem querer reverter seus usuários para que eles não possam mais executar a autenticação multifator.  

### <a name="how-to-roll-back-users"></a>Como reverter os usuários

Se você, como administrador, quiser redefinir as configurações de autenticação multifator de um usuário, poderá usar o script do PowerShell fornecido na próxima seção. O script limpará a propriedade StrongAuthenticationMethods para o aplicativo móvel e/ou número de telefone do usuário. Se você executar esse script para seus usuários, eles precisarão se registrar novamente para autenticação multifator, se necessário. É recomendável testar a reversão com um ou dois usuários antes de reverter todos os usuários afetados.

As etapas a seguir irão ajudá-lo a reverter um usuário ou grupo de usuários.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos apropriados do PowerShell do Azure AD. Em uma janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salve a lista de IDs de objeto de usuário afetada em seu computador como um arquivo de texto com uma ID por linha. Anote o local do arquivo.
1. Salve o seguinte script em seu computador e anote o local do script:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Reversão

Em uma janela do PowerShell, execute o comando a seguir, fornecendo o script e os locais de arquivo do usuário. Quando solicitado, forneça as credenciais de administrador global do seu locatário. O script produzirá o resultado da operação de atualização de cada usuário.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Desabilitar a experiência atualizada

Para desabilitar a experiência atualizada para seus usuários, conclua estas etapas:

1. Entre no portal do Azure como um administrador de usuário.
2. Vá para **Azure Active Directory**  >  **configurações**  >  **do usuário Gerenciar configurações para recursos de visualização do painel de acesso**.
3. Em **os usuários podem usar os recursos de visualização para registrar e gerenciar informações de segurança**, defina o seletor como **nenhum** e, em seguida, selecione **salvar**.

Os usuários não serão mais solicitados a se registrar usando a experiência atualizada.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o registro combinado para redefinição de senha de autoatendimento e autenticação multifator do Azure AD](concept-registration-mfa-sspr-combined.md)
