---
title: Pool de hosts da área de trabalho virtual do Windows (clássico) Azure Resource Manager-Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows (clássico) com um modelo de Azure Resource Manager.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b9999ae92840d79bb19464216c0f28504011b3f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008314"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Criar um pool de hosts na área de trabalho virtual do Windows (clássico) com um modelo de Azure Resource Manager

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

Siga as instruções desta seção para criar um pool de hosts para um locatário de área de trabalho virtual do Windows com um modelo de Azure Resource Manager fornecido pela Microsoft. Este artigo informará como criar um pool de hosts na área de trabalho virtual do Windows, criar um grupo de recursos com VMs em uma assinatura do Azure, ingressar essas VMs no domínio do AD e registrar as VMs com a área de trabalho virtual do Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que é necessário para executar o modelo do Azure Resource Manager

Verifique se você conhece as ações a seguir antes de executar o modelo de Azure Resource Manager:

- O local em que a origem da imagem que você deseja usar está. Ele é da galeria do Azure ou é personalizado?
- Suas credenciais de ingresso no domínio.
- Suas credenciais de área de trabalho virtual do Windows.

Ao criar um pool de host da Área de Trabalho Virtual do Windows com o modelo do Azure Resource Manager, você pode criar uma máquina virtual na galeria do Azure, uma imagem gerenciada ou uma imagem não gerenciada. Para saber mais sobre como criar imagens da VM, confira [Preparar um VHD ou VHDX do Windows para carregar no Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerenciada de uma VM generalizada no Azure](../../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo de Azure Resource Manager para provisionar um novo pool de hosts

Para começar, vá para [esta URL do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implantar o modelo no Azure

Se você estiver implantando em uma assinatura do Enterprise, role para baixo e selecione **implantar no Azure**e, em seguida, ignorar antecipadamente os parâmetros com base na origem da imagem.

Se você estiver implantando em uma assinatura do provedor de soluções na nuvem, siga estas etapas para implantar no Azure:

1. Role para baixo e clique com o botão direito do mouse em **Implantar no Azure** e, em seguida, selecione **Copiar o link**.
2. Abra um editor de texto, como o Bloco de Notas, e cole o link nele.
3. Logo após " https://portal.azure.com/ " e antes da hashtag (#), insira um sinal de arroba (@) seguido pelo nome de domínio do locatário. Aqui está um exemplo do formato que você deve usar: `https://portal.azure.com/@Contoso.onmicrosoft.com#create/` .
4. Entre no portal do Azure como um usuário com permissões de Administrador/Colaborador para a assinatura do Provedor de Soluções de Nuvem.
5. Cole o link copiado para o editor de texto na barra de endereços.

Para obter diretrizes sobre quais parâmetros devem ser inseridos para seu cenário, consulte o [arquivo Leiame](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)da área de trabalho virtual do Windows. O Leiame sempre é atualizado com as alterações mais recentes.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir usuários ao grupo de aplicativos da área de trabalho

Após a conclusão do modelo de Azure Resource Manager do GitHub, atribua o acesso do usuário antes de começar a testar as áreas de trabalho de sessão completas em suas máquinas virtuais.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Para atribuir usuários ao grupo de aplicativos da área de trabalho, abra uma janela do PowerShell e execute este cmdlet para entrar no ambiente de área de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, adicione usuários ao grupo de aplicativos da área de trabalho com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

O UPN do usuário deve corresponder à identidade do usuário em Azure Active Directory (por exemplo, user1@contoso.com ). Se você desejar adicionar vários usuários, deverá executar este cmdlet para cada um.

Após concluir essas etapas, os usuários adicionados ao grupo de aplicativo de área de trabalho poderão entrar na Área de Trabalho Virtual do Windows com os clientes de Área de Trabalho Remota com suporte e verão um recurso para uma área de trabalho de sessão.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](../../security-center/security-center-just-in-time.md).