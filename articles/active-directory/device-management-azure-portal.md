---
title: "Gerenciar dispositivos usando o portal do Azure – versão prévia | Microsoft Docs"
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4b46e1627a229b0649d9ccd2550cd28fda9849f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-devices-using-the-azure-portal---preview"></a>Gerenciar dispositivos usando o portal do Azure – versão prévia

>[!NOTE]
>Atualmente, essa capacidade está em visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. O recurso está disponível em qualquer assinatura do Azure AD (Azure Active Directory) durante a visualização pública. No entanto, quando o recurso for disponibilizado para todos, alguns aspectos dele poderão exigir uma assinatura do Azure Active Directory Premium.


Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. 

Este tópico:

- Assuma que você está familiarizado com a [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)

- Fornece informações sobre como gerenciar dispositivos usando o portal do Azure


Para gerenciar dispositivos no portal do Azure, é necessário clicar em **Dispositivos** na seção **Gerenciar** da folha **Azure Active Directory**.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/11.png)




## <a name="configure-device-settings"></a>Definir configurações do dispositivo

Para gerenciar seus dispositivos usando o portal do Azure, eles precisam ser registrados ou associados ao Azure AD. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo.

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/22.png)


A folha de configurações do dispositivo permite que você configure:

- **Os usuários podem associar seus dispositivos ao Azure AD** – essas configurações permitem que você selecione os usuários que podem associar dispositivos ao Azure AD. O padrão é **Todos**.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite). 

- **Os usuários podem registrar seus dispositivos com o Azure AD** – você precisa definir essa configuração para permitir que dispositivos sejam registrados com o Azure AD. Se você selecionar **Nenhum**, os dispositivos não terão permissão para registro quando eles não forem associados ao Azure AD ou não forem associados ao Azure AD híbrido. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.

- **Exigir autenticação multifator para associar dispositivos** – é possível definir se os usuários devem precisar fornecer um segundo fator de autenticação para associar seu dispositivo ao Azure AD. O padrão é **Não**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Número máximo de dispositivos** – essa configuração permite selecionar o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. A cotação de dispositivo é contada para todos os dispositivos que estão associados ao Azure AD ou registrados no Azure AD atualmente. O valor padrão é **20**.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).
 
    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)




## <a name="locate-devices"></a>Localizar dispositivos

Como administrador, no portal do Azure, você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da folha **Dispositivos**  

    ![Todos os dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** na seção **Gerenciar** de uma folha **Usuário**
 
    ![Todos os dispositivos](./media/device-management-azure-portal/43.png)



Com as duas opções, você pode obter uma exibição que:


- Permite pesquisar dispositivos usando o nome de exibição como filtro.

- Fornece visão geral detalhada de dispositivos registrados e associados

- Permite que você execute tarefas comuns de gerenciamento de dispositivo
   

![Todos os dispositivos](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Tarefas de gerenciamento de dispositivos

Como administrador, você pode gerenciar os dispositivos registrados ou associados. Esta seção fornece informações sobre tarefas comuns de gerenciamento de dispositivo.


**Gerenciar um dispositivo do Intune** – se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. Um administrador pode ver dispositivos adicionais 

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)


**Habilitar/desabilitar um dispositivo do Azure AD**

Para habilitar ou desabilitar um dispositivo, você precisa ser um administrador global no Azure AD. Desabilitar um dispositivo impede que um dispositivo acesse seus recursos do Azure AD.  Para desabilitar o dispositivo, você pode clicar em *...* clique no dispositivo para detalhes adicionais.

 
![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/33.png)

Desabilitar um dispositivo altera o estado na coluna **HABILITADO** para **Não**.

![Desabilitar um dispositivo](./media/device-management-azure-portal/32.png)


**Excluir um dispositivo Azure AD** – para excluir um dispositivo, você precisa ser um administrador global no Azure AD.  
Excluindo um dispositivo:
 
- Impede que um dispositivo acesse seus recursos do Azure AD 

- Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows  

- Representa uma atividade não recuperável e não é recomendável a menos que necessário

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, o Microsoft Intune), certifique-se de que o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure AD.

Você pode selecionar “…” para excluir o dispositivo ou clicar no dispositivo para obter detalhes adicionais
 
![Excluir um dispositivo](./media/device-management-azure-portal/34.png)


**Exibir ou copiar a ID do dispositivo** – você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  

**Exibir ou copiar as chaves do BitLocker** – se você for um administrador, será possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.
 
![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Logs de auditoria


As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Isso inclui atividades acionadas pelo serviço de registro do dispositivo ou pelo usuário:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo

- Alterações nas configurações do dispositivo

- Operações de dispositivo como excluir ou atualizar um dispositivo
 
O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da folha **Dispositivos*.

![Logs de auditoria](./media/device-management-azure-portal/61.png)


Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- a data e a hora da ocorrência

- os destinos

- o iniciador/ator (quem) de uma atividade

- a atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.
 
![Logs de auditoria](./media/device-management-azure-portal/64.png)


Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md)



