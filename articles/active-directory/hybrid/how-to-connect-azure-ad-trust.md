---
title: O Azure AD Connect - Gerenciar a confiança do AD FS com o Azure AD usando o Azure AD Connect | Microsoft Docs
description: Detalhes operacionais do gerenciamento de confiança do AD do Azure por conexão do Azure AD.
keywords: AD FS, ADFS, gerenciamento do AD FS, Conexão do AAD, Conectar, Azure AD, confiança, AAD, reivindicação, reivindicação, regras de declaração, emissão, transformação, regras, backup, restauração
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85360088"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Gerenciar a confiança do AD FS com o Azure AD usando o Azure AD Connect

## <a name="overview"></a>Visão geral

O Azure AD Connect pode gerenciar a federação entre o AD FS (Serviço de Federação do Active Directory) local e o Azure AD. Este artigo fornece uma visão geral de:

* As várias configurações definidas na confiança pelo Azure AD Connect
* As regras de transformação de emissão (regras de declaração) definidas pelo Azure AD Connect
* Como fazer backup e restaurar suas regras de declaração entre atualizações e atualizações de configuração. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Configurações controladas pelo Azure AD Connect

O Azure AD Connect gerencia **somente** configurações relacionadas à confiança do AD do Azure. O Azure AD Connect não modifica nenhuma configuração em outras relações de confiança da terceira parte confiável no AD FS. A tabela a seguir indica as configurações controladas pelo Azure AD Connect.

| Configuração | Descrição |
| :--- | :--- |
| Certificado de autenticação de token | O Azure AD Connect pode ser usado para redefinir e recriar a confiança com o Azure AD. O Azure AD Connect faz uma substituição imediata de certificados de assinatura de token para o AD FS e atualiza as configurações de federação de domínio do Azure AD.|
| Algoritmo de assinatura de token | A Microsoft recomenda o uso do SHA-256 como o algoritmo de assinatura de token. O Azure AD Connect pode detectar se o algoritmo de assinatura de token está definido como um valor menos seguro que o SHA-256. Ele atualizará a configuração para SHA-256 na próxima operação de configuração possível. Outro objeto de confiança de terceira parte confiável precisa ser atualizado para usar o novo certificado de autenticação de tokens. |
| Identificador de relação de confiança do AD do Azure | O Azure AD Connect define o valor do identificador correto para a confiança do AD do Azure. O AD FS identifica exclusivamente a confiança do AD do Azure usando o valor do identificador. |
| Pontos de extremidade do Azure AD | O Azure AD Connect garante que os pontos de extremidade configurados para a confiança do AD do Azure estejam sempre de acordo com os valores recomendados mais recentes para resiliência e desempenho. |
| Regras de transformação de emissão | Há um número de regras de declaração que são necessárias para o desempenho ideal dos recursos do Azure AD em uma configuração federada. O Azure AD Connect garante que a confiança do AD do Azure esteja sempre configurada com o conjunto correto de regras de declaração recomendadas. |
| Id alternativo | Se a sincronização estiver configurada para usar um ID alternativo, o Azure AD Connect configura o AD FS para executar a autenticação usando o id alternativo. |
| Atualização automática de metadados | A confiança com o Azure AD é configurada para atualização automática de metadados. O AD FS verifica periodicamente os metadados da confiança do AD do Azure e os mantém atualizados no caso de alterações no lado do AD do Azure. |
| Autenticação Integrada do Windows (AIW) | Durante a operação de associação do AD do Hybrid Azure, o IWA é habilitado para o registro de dispositivos para facilitar a junção do Hybrid Azure AD para dispositivos de nível inferior |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Fluxos de execução e configurações de federação configuradas pelo Azure AD Connect

A conexão do Azure AD não atualiza todas as configurações para a confiança do Azure AD durante os fluxos de configuração. As configurações modificadas dependem de qual tarefa ou fluxo de execução está sendo executado. A tabela a seguir lista as configurações impactadas em diferentes fluxos de execução.

| Fluxo de execução | Configurações de impacto |
| :--- | :--- |
| Primeira instalação de passagem (express) | Nenhum |
| Primeira instalação de passagem (novo farm do AD FS) | Um novo farm do AD FS é criado e uma relação de confiança com o Azure AD é criada do zero. |
| Instalação de primeira passagem (farm do AD FS existente, confiança do AD do Azure existente) | Identificador de confiança do Azure AD, regras de transformação de emissão, pontos de extremidade do Azure AD, ID alternativo (se necessário), atualização automática de metadados |
| Redefinir confiança do Azure AD | Certificado de assinatura de token, algoritmo de assinatura de token, identificador de confiança do Azure AD, regras de transformação de emissão, endpoints do Azure AD, id alternativo (se necessário), atualização automática de metadados |
| Adicionar servidor de Federação | Nenhum |
| Adicionar servidor WAP | Nenhum |
| Opções do dispositivo | Regras de transformação de emissão, IWA para registro de dispositivo |
| Adicionar domínio federado | Se o domínio estiver sendo adicionado pela primeira vez, ou seja, a configuração está mudando de federação de domínio único para federação de vários domínios - o Azure AD Connect recriará a confiança do zero. Se a confiança com o Azure AD já estiver configurada para vários domínios, somente as regras de transformação de emissão serão modificadas |
| Atualizar TLS | Nenhum |

Durante todas as operações, nas quais qualquer configuração é modificada, o Azure AD Connect faz um backup das configurações de confiança atuais em **% ProgramData% \ AADConnect \ ADFS**

![Página do Azure AD Connect mostrando a mensagem sobre o backup de confiança existente do Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Antes da versão 1.1.873.0, o backup consistia apenas em regras de transformação de emissão e era feito backup no arquivo de log de rastreio do assistente.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regras de transformação de emissão definidas pelo Azure AD Connect

O Azure AD Connect garante que a confiança do AD do Azure esteja sempre configurada com o conjunto correto de regras de declaração recomendadas. A Microsoft recomenda o uso do Azure AD connect para gerenciar sua confiança do AD do Azure. Esta seção lista o conjunto de regras de transformação de emissão e sua descrição.

| Nome da regra | Descrição |
| --- | --- |
| Problema de UPN | Essa regra consulta o valor de userprincipalname a partir do atributo configurado nas configurações de sincronização para userprincipalname.|
| Consultar objectguid e msdsconsistencyguid para declaração de ImmutableId personalizada | Esta regra adiciona um valor temporário no pipeline para o valor de objectguid e msdsconsistencyguid, se existir |
| Verifique a existência do msdsconsistencyguid | Com base em se o valor para msdsconsistencyguid existe ou não, definimos um sinalizador temporário para direcionar o que usar como ImmutableId |
| Emita msdsconsistencyguid como ID Imutável, se existir | Emita msdsconsistencyguid como ImmutableId se o valor existir |
| Emitir objectGuidRule se a regra msdsConsistencyGuid não existir | Se o valor de msdsconsistencyguid não existir, o valor de objectguid será emitido como ImmutableId |
| Problema nameidentifier | Essa regra emite valor para a declaração nameidentifier.|
| Tipo de conta do problema para computadores ingressados no domínio | Se a entidade que está sendo autenticada for um dispositivo de domínio associado, essa regra emitirá o tipo de conta como DJ, significando um dispositivo associado ao domínio |
| Emitir AccountType com o valor USER quando não for uma conta de computador | Se a entidade que está sendo autenticada for um usuário, essa regra emitirá o tipo de conta como Usuário |
| Emitir issuerid quando não é uma conta de computador | Essa regra emite o valor issuerId quando a entidade de autenticação não é um dispositivo. O valor é criado por meio de uma regex, que é configurada pelo Azure AD Connect. A regex é criada depois de levar em consideração todos os domínios federados usando o Azure AD Connect. |
| Emita o issuerid de autenticação de computador DJ | Esta regra emite o valor issuerId quando a entidade de autenticação é um dispositivo |
| Problema onpremobjectguid para computadores associados ao domínio | Se a entidade que está sendo autenticada for um dispositivo de domínio associado, essa regra emite o objectguid local para o dispositivo |
| Passe pelo SID primário | Esta regra emite o SID principal da entidade de autenticação |
| Passar por reivindicação - insideCorporateNetwork | Esta regra emite uma declaração que ajuda o Azure AD a saber se a autenticação está vindo de dentro da rede corporativa ou externamente |
| Passar declaração – Psso |   |
| Emitir declarações de expiração de senha | Essa regra emite três declarações para o tempo de expiração da senha, o número de dias para a senha expirar da entidade que está sendo autenticada e o URL para onde encaminhar para alterar a senha.|
| Passar por reivindicação - authnmethodsreferences | O valor na reivindicação emitida sob esta regra indica que tipo de autenticação foi executada para a entidade |
| Pass through claim - multifactorauthenticationinstant | O valor dessa declaração especifica o tempo, em UTC, quando o usuário realizou pela última vez a autenticação de vários fatores. |
| Passar declaração - AlternateLoginID | Esta regra emite a declaração AlternateLoginID se a autenticação foi realizada usando ID de login alternativo. |

> [!NOTE]
> As regras de declaração para o problema UPN e ImmutableId serão diferentes se você usar uma opção não padrão durante a configuração do Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Restaurar regras de transformação de emissão

O Azure AD Connect versão 1.1.873.0 ou posterior faz um backup das configurações de confiança do Azure AD sempre que uma atualização é feita nas configurações de confiança do Azure AD. As configurações de confiança do AD do Azure são armazenadas em **% ProgramData% \ AADConnect \ ADFS**. O nome do arquivo está no seguinte formato AadTrust- &lt;date&gt;-&lt;time&gt; .txt, por exemplo - AadTrust-20180710-150216.txt

![Uma captura de tela de exemplo de backup da relação de confiança do Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Você pode restaurar as regras de transformação de emissão usando as etapas sugeridas abaixo

1. Abra a interface de usuário de gerenciamento do AD FS no Gerenciador de Servidores
2. Abra as propriedades de confiança do Azure AD indo até Trusts de Terceiros de Confiança do **AD FS &gt; Plataforma de Identidade do &gt; Microsoft Office 365 &gt;Editando a Diretiva de Emissão de Reivindicações**
3. Clique em **Adicionar regra**
4. No modelo de regra de declaração, selecione Enviar declarações usando uma regra personalizada e clique em **Próximo**
5. Copie o nome da regra de declaração do arquivo de backup e cole-o no campo **Nome da regra de reivindicação**
6. Copie a regra de declaração do arquivo de backup para o campo de texto da **regra personalizada** e clique em **Concluir**

> [!NOTE]
> Verifique se suas regras adicionais não estão em conflito com as regras configuradas pelo Azure AD Connect.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar e personalizar os serviços de Federação do Active Directory usando o Azure AD Connect](how-to-connect-fed-management.md)
