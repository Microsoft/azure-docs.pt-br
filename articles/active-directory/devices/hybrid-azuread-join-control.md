---
title: Validação controlada de ingresso no AD do Azure híbrido - Azure AD
description: Saiba como fazer uma validação controlada de ingresso no Azure AD híbrido antes de habilitá-la em toda a organização ao mesmo tempo
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59104bf9c7675fdac2c245fff89ab1483b96b67
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481714"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validação controlada de ingresso no Azure AD híbrido

Quando todos os pré-requisitos estão em vigor, os dispositivos do Windows serão registrados automaticamente como dispositivos em seu locatário do Azure AD. O estado dessas identidades de dispositivo no Azure AD é conhecido como ingresso no Azure AD híbrido. Para obter mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md) e [planejar sua implementação de junção do Azure Active Directory híbrido ](hybrid-azuread-join-plan.md).

As organizações talvez queira fazer uma validação controlada de ingresso no Azure AD híbrido antes de habilitá-la em toda a sua organização ao mesmo tempo. Este artigo explicará como realizar uma validação controlada de ingresso no Azure AD híbrido.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validação controlada de ingresso no Azure AD híbrido em dispositivos atuais do Windows

Para os dispositivos que executam o sistema operacional da área de trabalho do Windows, a versão com suporte é a Atualização de Aniversário do Windows 10 (versão 1607) ou posterior. Como melhor prática, atualize para a última versão do Windows 10.

Para fazer uma validação controlada de ingresso no Azure AD híbrido em dispositivos atuais do Windows, você precisa:

1. Limpar a entrada de ponto de Conexão de serviço (SCP) do Active Directory (AD), se ele existir
1. Definir configuração de registro do lado do cliente para o SCP em seus computadores ingressados no domínio usando um objeto de diretiva de grupo (GPO)
1. Se você estiver usando o AD FS, você também deve configurar a configuração do registro do lado do cliente para o SCP em seu servidor do AD FS usando um GPO  



### <a name="clear-the-scp-from-ad"></a>Limpar o SCP do AD

Use o Editor de Interfaces de serviços de Active Directory (ADSI Editar) para modificar os objetos SCP do AD.

1. Inicie o **ADSI Edit** do aplicativo de desktop e estação de trabalho administrativa ou um controlador de domínio como um administrador corporativo.
1. Conectar-se para o **contexto de nomenclatura de configuração** do seu domínio.
1. Navegue até **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. Clique com botão direito no objeto folha sob **CN = Device Registration Configuration** e selecione **propriedades**
   1. Selecione **palavras-chave** da **Editor de atributos** janela e clique em **editar**
   1. Selecione os valores de **azureADId** e **azureADName** (um por vez) e clique em **remover**
1. Fechar **ADSI Editar**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurar o registro do lado do cliente para o SCP

Use o exemplo a seguir para criar um grupo de política de GPO (objeto) para implantar uma configuração do registro Configurando uma entrada de SCP no registro de seus dispositivos.

1. Abra um console de gerenciamento de diretiva de grupo e criar um novo objeto de diretiva de grupo em seu domínio.
   1. Forneça um nome (por exemplo, ClientSideSCP) de seu GPO recém-criado.
1. Edite o GPO e localize o caminho a seguir: **Configuração do computador** > **preferências** > **configurações do Windows** > **registro**
1. Clique com botão direito no registro e selecione **New** > **Item do registro**
   1. Sobre o **geral** guia, configure o seguinte
      1. Ação: **Atualização**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantId**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: O GUID ou **ID de diretório** da sua instância do AD do Azure (esse valor pode ser encontrado na **portal do Azure** > **do Azure Active Directory**  >   **As propriedades** > **ID de diretório**)
   1. Clique em **OK**
1. Clique com botão direito no registro e selecione **New** > **Item do registro**
   1. Sobre o **geral** guia, configure o seguinte
      1. Ação: **Atualização**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Caminho da chave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome do valor: **TenantName**
      1. Tipo de valor: **REG_SZ**
      1. Dados de valor: Seu verificado **nome de domínio** no Azure AD (por exemplo, `contoso.onmicrosoft.com` ou qualquer outro nome de domínio verificado no seu diretório)
   1. Clique em **OK**
1. Feche o editor para o GPO recém-criado
1. Vincular o GPO recém-criado à UO desejado que contém computadores que ingressaram no domínio que pertencem à sua população de distribuição controlada

### <a name="configure-ad-fs-settings"></a>Definir as configurações do AD FS

Se você estiver usando o AD FS, primeiro você precisa configurar o SCP do lado do cliente usando as instruções mencionadas acima, mas vincular o GPO para servidores do AD FS. Essa configuração é necessária para o AD FS estabelecer a fonte para as identidades de dispositivo como o Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validação controlada de ingresso no Azure AD híbrido em dispositivos de nível inferior do Windows

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Microsoft Workplace Join para computadores não Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro quiet. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com Azure AD com as credenciais do usuário após a autenticação no Azure AD.

Para controlar o registro de dispositivo, você deve implantar o pacote do Windows Installer para o grupo selecionado de dispositivos de nível inferior do Windows.

> [!NOTE]
> Se um SCP não está configurado no AD e, em seguida, você deve seguir a mesma abordagem, conforme descrito em [configurar o registro do lado do cliente para o SCP](#configure-client-side-registry-setting-for-scp)) em seus computadores ingressados no domínio usando um objeto de diretiva de grupo (GPO).


Depois de verificar se tudo está funcionando conforme o esperado, você pode registrar automaticamente o restante de seus dispositivos atuais e de nível inferior do Windows com o Azure AD por [Configurando o SCP usando o Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Próximas etapas

[Planejar sua implementação de junção do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)
