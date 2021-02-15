---
title: Expandir o pool de hosts da área de trabalho virtual do Windows (clássico) existente com novos hosts de sessão-Azure
description: Como expandir um pool de hosts existente com novos hosts de sessão na área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009164"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Expandir um pool de hosts existente com novos hosts de sessão na área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../expand-existing-host-pool.md).

Ao aumentar o uso em seu pool de hosts, talvez seja necessário expandir seu pool de hosts existente com novos hosts de sessão para lidar com a nova carga.

Este artigo lhe dirá como você pode expandir um pool de hosts existente com novos hosts de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir o pool de hosts

Antes de começar, verifique se você criou um pool de hosts e VMs (máquinas virtuais) de host de sessão usando um dos seguintes métodos:

- [Oferta do Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Modelo de Azure Resource Manager do GitHub](create-host-pools-arm-template.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell-2019.md)

Você também precisará das seguintes informações ao criar pela primeira vez o pool de hosts e as VMs de host de sessão:

- Tamanho da VM, imagem e prefixo do nome
- Credenciais do administrador de locatários do ingresso no domínio e do Windows Virtual Desktop
- Nome da rede virtual e nome da sub-rede

As próximas três seções são três métodos que podem ser usados para expandir o pool de hosts. Você pode fazer com qualquer ferramenta de implantação com a qual esteja familiarizado.

>[!NOTE]
>Durante a fase de implantação, você verá mensagens de erro para os recursos de VM do host da sessão anterior se eles estiverem desligados no momento. Esses erros ocorrem porque o Azure não pode executar a extensão de DSC do PowerShell para validar que as VMs do host de sessão estão corretamente registradas em seu pool de hosts existente. Você pode ignorar esses erros com segurança ou pode evitar os erros iniciando todas as VMs de host de sessão no pool de hosts existente antes de iniciar o processo de implantação.

## <a name="redeploy-from-azure"></a>Reimplantar do Azure

Se você já tiver criado um pool de hosts e VMs de host de sessão usando a [oferta do Azure Marketplace](create-host-pools-azure-marketplace-2019.md) ou o [modelo de Azure Resource Manager do GitHub](create-host-pools-arm-template.md), poderá reimplantar o mesmo modelo do portal do Azure. Reimplantar o modelo reinsere automaticamente todas as informações inseridas no modelo original, com exceção das senhas.

Veja como reimplantar o modelo de Azure Resource Manager para expandir um pool de hosts:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Na barra de pesquisa na parte superior da portal do Azure, procure grupos de **recursos** e selecione o item em **Serviços**.
3. Localize e selecione o grupo de recursos que você criou quando fez o pool de hosts.
4. No painel no lado esquerdo do navegador, selecione **implantações**.
5. Selecione a implantação apropriada para o processo de criação do pool de hosts:
     - Se você criou o pool de hosts original com a oferta do Azure Marketplace, selecione a implantação começando com **RDS. wvd-Provision-host-pool**.
     - Se você criou o pool de hosts original com o modelo de Azure Resource Manager do GitHub, selecione a implantação chamada **Microsoft. Template**.
6. Selecione **reimplantar**.

     >[!NOTE]
     >Se o modelo não for reimplantado automaticamente quando você selecionar **reimplantar**, selecione **modelo** no painel no lado esquerdo do navegador e selecione **implantar**.

7. Selecione o grupo de recursos que contém as VMs de host de sessão atual no pool de hosts existente.

     >[!NOTE]
     >Se você vir um erro que informa para selecionar um grupo de recursos diferente, embora aquele que você inseriu esteja correto, selecione outro grupo de recursos e selecione o grupo de recursos original.

8. Insira a URL a seguir para o *_artifactsLocation*: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Insira o novo número total de hosts de sessão que você deseja no *número de RDSH de instâncias*. Por exemplo, se você estiver expandindo o pool de hosts de cinco hosts de sessão para oito, digite **8**.
10. Insira a mesma senha de domínio existente que você usou para o UPN de domínio existente. Não altere o nome de usuário, pois isso causará um erro quando você executar o modelo.
11. Insira a mesma senha de administrador de locatário que você usou para a ID de usuário ou aplicativo que você inseriu para o *UPN do administrador de locatário ou a ID do aplicativo*. Mais uma vez, não altere o nome de usuário.
12. Conclua o envio para expandir seu pool de hosts.

## <a name="run-the-azure-marketplace-offering"></a>Executar a oferta do Azure Marketplace

Siga as instruções em [criar um pool de hosts usando o Azure Marketplace](create-host-pools-azure-marketplace-2019.md) até que você atinja [executar a oferta do Azure Marketplace para provisionar um novo pool de hosts](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). Quando chegar a esse ponto, você precisará inserir as seguintes informações para cada guia:

### <a name="basics"></a>Noções básicas

Todos os valores nesta seção devem corresponder ao que você forneceu ao criar pela primeira vez o pool de hosts e VMs de host de sessão, exceto para *usuários de área de trabalho padrão*:

1.    Para *assinatura*, selecione a assinatura na qual você criou o pool de hosts pela primeira vez.
2.    Para *grupo de recursos*, selecione o mesmo grupo de recursos em que as VMs de host de sessão do pool de hosts existentes estão localizadas.
3.    Para *região*, selecione a mesma região em que as VMs de host de sessão do pool de hosts existentes estão localizadas.
4.    Para *nome do Hostpool*, insira o nome do pool de hosts existente.
5.    Para *tipo de área de trabalho*, selecione o tipo de área de trabalho que corresponde ao pool de hosts existente.
6.    Para *usuários da área de trabalho padrão*, insira uma lista separada por vírgulas de todos os usuários adicionais que você deseja entrar nos clientes da área de trabalho virtual do Windows e acesse uma área de trabalho após a conclusão da oferta do Azure Marketplace. Por exemplo, se você quiser atribuir user3@contoso.com e user4@contoso.com acessar, insira user3@contoso.com , user4@contoso.com .
7.    Selecione **Avançar: configurar máquina virtual**.

>[!NOTE]
>Exceto para *usuários da área de trabalho padrão*, todos os campos devem corresponder exatamente ao que foi configurado no pool de hosts existente. Se houver uma incompatibilidade que resultará em um novo pool de hosts.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu quando criou pela primeira vez o pool de hosts e as VMs de host de sessão, exceto o número total de VMs. O número de VMs que você inserir será o número de VMs em seu pool de hosts expandido:

1. Selecione o tamanho da VM que corresponde às VMs do host de sessão existente.

    >[!NOTE]
    >Se o tamanho específico da VM que você procura não aparece no seletor de tamanho da VM, é porque ainda não o integramos à ferramenta Azure Marketplace. Para solicitar um tamanho de VM, crie uma solicitação ou atualize uma solicitação existente no [fórum UserVoice do Windows Virtual Desktop](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Personalize o *perfil de uso*, o *total de usuários*e o *número de parâmetros de máquinas virtuais* para selecionar o número total de hosts de sessão que você gostaria de ter em seu pool de hosts. Por exemplo, se você estiver expandindo o pool de hosts de cinco hosts de sessão para oito, configure essas opções para chegar a 8 máquinas virtuais.
3. Digite um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o nome “prefixo”, as máquinas virtuais serão chamadas de "prefixo-0," "prefixo-1" e assim por diante.
4. Selecione **Avançar: configurações da máquina virtual**.

### <a name="virtual-machine-settings"></a>Configurações da máquina virtual

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu ao criar pela primeira vez o pool de hosts e as VMs de host de sessão:

1. Para a *origem da imagem* e a versão do so da *imagem*, insira as mesmas informações que você forneceu ao criar o pool de hosts pela primeira vez.
2. Para o *UPN de ingresso no domínio do AD* e as senhas associadas, insira as mesmas informações que você forneceu ao criar o pool de hosts pela primeira vez para ingressar as VMs no domínio Active Directory. Essas credenciais serão usadas para criar uma conta local em suas máquinas virtuais. Você pode redefinir essas contas locais para alterar suas credenciais posteriormente.
3. Para as informações de rede virtual, selecione a mesma rede virtual e sub-rede para onde estão localizadas suas VMs de host de sessão de pool de hosts existentes.
4. Selecione **Avançar: configurar informações da área de trabalho virtual do Windows**.

### <a name="windows-virtual-desktop-information"></a>Informações da área de trabalho virtual do Windows

Todos os valores de parâmetro nesta seção devem corresponder ao que você forneceu ao criar pela primeira vez o pool de hosts e as VMs de host de sessão:

1. Para *Nome do grupo de locatário da Área de Trabalho Virtual do Windows*, digite o nome do grupo de locatário que contém seu locatário. Deixe-o como o padrão, a menos que você tenha recebido um nome de grupo de locatário específico.
2. Para *Nome de locatário da Área de Trabalho Virtual do Windows*, digite o nome do locatário no qual você criará esse pool de hosts.
3. Especifique as mesmas credenciais que você usou ao criar pela primeira vez o pool de hosts e as VMs de host de sessão. Se você estiver usando uma entidade de serviço, insira a ID da instância de Azure Active Directory onde a entidade de serviço está localizada.
4. Selecione **Avançar: revisar + criar**.

## <a name="run-the-github-azure-resource-manager-template"></a>Executar o modelo de Azure Resource Manager do GitHub

Siga as instruções em [executar o modelo de Azure Resource Manager para provisionar um novo pool de hosts](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) e fornecer todos os mesmos valores de parâmetro, exceto o *número de RDSH de instâncias*. Insira o número de VMs de host de sessão que você deseja no pool de hosts depois de executar o modelo. Por exemplo, se você estiver expandindo o pool de hosts de cinco hosts de sessão para oito, digite **8**.

## <a name="next-steps"></a>Próximas etapas

Agora que você expandiu o pool de hosts existente, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Você pode se conectar a uma sessão com qualquer um dos seguintes clientes:

- [Conectar-se ao cliente da Área de Trabalho do Windows](connect-windows-7-10-2019.md)
- [Conectar-se ao cliente Web](connect-web-2019.md)
- [Conectar-se ao cliente Android](connect-android-2019.md)
- [Conectar-se ao cliente macOS](connect-macos-2019.md)
- [Conectar-se ao cliente iOS](connect-ios-2019.md)
