---
title: Especifique artefatos obrigatórios para o Azure DevTest Labs | Microsoft Docs
description: Saiba como especificar artefatos obrigatórios que precisam ser instalados antes de instalar qualquer artefato selecionado pelo usuário em máquinas virtuais (VMs) no laboratório.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562102"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Especifique artefatos obrigatórios para o seu laboratório no Azure DevTest Labs
Como um proprietário de um laboratório, você pode especificar obrigatórios artefatos que são aplicados a cada máquina criada no laboratório. Imagine um cenário em que você deseja que cada máquina em seu laboratório seja conectada à sua rede corporativa. Nesse caso, cada usuário do laboratório teria que adicionar um artefato de junção de domínio durante a criação da máquina virtual para garantir que a máquina estivesse conectada ao domínio corporativo. Em outras palavras, os usuários do laboratório teriam essencialmente que recriar uma máquina caso esquecessem de aplicar artefatos obrigatórios em sua máquina. Como proprietário de um laboratório, você faz o domínio unir o artefato como um artefato obrigatório em seu laboratório. Esta etapa garante que cada máquina esteja conectada à rede corporativa e economizando tempo e esforço para os usuários do laboratório.
 
Outros artefatos obrigatórios podem incluir uma ferramenta comum que sua equipe usa ou um pacote de segurança relacionado à plataforma que cada máquina precisa ter por padrão etc. Em suma, qualquer software comum que cada máquina em seu laboratório deve ter se torna um artefato obrigatório. Se você criar uma imagem personalizada a partir de uma máquina que tenha artefatos obrigatórios aplicados a ela e depois criar uma nova máquina a partir dessa imagem, os artefatos obrigatórios serão reaplicados na máquina durante a criação. Esse comportamento também significa que, mesmo que a imagem personalizada seja antiga, toda vez que você criar uma máquina a partir dela, a versão mais atualizada dos artefatos obrigatórios será aplicada a ela durante o fluxo de criação. 
 
Somente artefatos que não possuem parâmetros são suportados como obrigatórios. O usuário do seu laboratório não precisa inserir parâmetros adicionais durante a criação do laboratório e, assim, simplificar o processo de criação da VM. 

## <a name="specify-mandatory-artifacts"></a>Especificar artefatos obrigatórios
Você pode selecionar artefatos obrigatórios para computadores Windows e Linux separadamente. Você também pode reordenar esses artefatos, dependendo da ordem na qual você deseja sejam aplicadas. 

1. Na home page do seu laboratório, selecione **configuração e políticas** sob **configurações**. 
3. Selecione **artefatos obrigatórios** sob **RECURSOS EXTERNOS**. 
4. Selecione **edite** na **Windows** seção ou o **Linux** seção. Este exemplo usa o **Windows** opção. 

    ![Página de artefatos obrigatório - botão Editar](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecione um artefato. Este exemplo usa a opção **7-Zip**. 
5. Sobre o **adicionar artefato** página, selecione **Add**. 

    ![Artefatos obrigatórios página - Adicionar 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Para adicionar outro artefato, selecione o artigo e selecione **adicionar**. Este exemplo adiciona **Chrome** como o segundo artefato obrigatório.

    ![Página de artefatos obrigatório - adicionar Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Sobre o **artefatos obrigatórios** página, você ver uma mensagem que especifica o número de artefatos selecionados. Se você clicar na mensagem, verá os artefatos selecionados. Selecione **salvar** para salvar. 

    ![Página de artefatos obrigatórios - Salvar artefatos](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Repita as etapas para especificar artefatos obrigatórios para VMs do Linux. 
    
    ![Página de artefatos obrigatórios - artefatos do Windows e do Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Para **exclua** um artefato na lista, selecione **... (reticências)** no final da linha e selecione **excluir**. 
10. Para **reorganizar os artefatos** na lista, passe o mouse sobre o artefato, selecione **... (reticências)** que aparece no início da linha e arraste o item para a nova posição . 
11. Para salvar os artefatos obrigatórios no laboratório, selecione **salvar**. 

    ![Página de artefatos obrigatório - salvar artefatos no laboratório](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Fechar o **configuração e políticas** página (marque **X** no canto superior direito) para voltar à home page do seu laboratório.  

## <a name="delete-a-mandatory-artifact"></a>Excluir um artefato obrigatório
Para excluir um artefato obrigatório de um laboratório, execute as seguintes ações: 

1. Selecione **configuração e políticas** sob **configurações**. 
2. Selecione **artefatos obrigatórios** sob **RECURSOS EXTERNOS**. 
3. Selecione **edite** na **Windows** seção ou o **Linux** seção. Este exemplo usa o **Windows** opção. 
4. Selecione a mensagem com o número de artefatos obrigatórios na parte superior. 

    ![Página de artefatos obrigatório - selecionar a mensagem](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na página **Artefatos selecionados**, selecione **... (reticências)** para que o artefato seja excluído e selecione **Remover**. 
    
    ![Página de artefatos obrigatório - Remover artefato](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecione **Ok** para fechar o **artefatos selecionados** página. 
7. Selecione **salvar** sobre o **artefatos obrigatórios** página.
8. Repita as etapas para **Linux** imagens se necessário. 
9. Selecione **salvar** para salvar todas as alterações ao laboratório. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Artefatos de exibição obrigatórios ao criar uma VM
Agora, como usuário de laboratório, você pode visualizar a lista de artefatos obrigatórios ao criar uma VM no laboratório. Você não pode editar ou excluir artefatos obrigatórios definidos no laboratório pelo proprietário do seu laboratório.

1. Na home page para seu laboratório, selecione no menu **visão geral**.
2. Para adicionar uma VM ao laboratório, selecione **+ adicionar**. 
3. Selecione uma **imagem base**. Este exemplo usa **Windows Server, versão 1709**.
4. Observe que você vê uma mensagem para **artefatos** com o número de artefatos obrigatórios selecionado. 
5. Selecione **Artefatos**. 
6. Confirme que você vê os **artefatos obrigatórios** especificados nas configurações e políticas do laboratório. 

    ![Criar uma VM – artefatos obrigatórios](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [adicionar um repositório de artefatos Git a um laboratório](devtest-lab-add-artifact-repo.md).

