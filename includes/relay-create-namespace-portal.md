1. Faça logon no [Portal do Azure][Azure portal].
2. No painel de navegação esquerdo do portal, clique em **Novo**, em **Integração Corporativa** e em **Retransmissão**.
3. Na caixa de diálogo **Criar um namespace** , digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. No campo **Assinatura** , escolha uma assinatura do Azure na qual criar o namespace.
5. No campo **[Grupo de recursos](../articles/azure-resource-manager/resource-group-portal.md)**, escolha um grupo de recursos existente no qual o namespace residirá, ou então crie um novo.      
6. Em **Localização**, escolha o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
7. Clique em **Criar**. Agora, o sistema cria o seu namespace e o habilita. Depois de alguns minutos, o sistema provisiona recursos para sua conta.

### <a name="obtain-the-management-credentials"></a>Obter as credenciais de gerenciamento
1. Na lista de namespaces, clique no nome do namespace recém-criado.
2. Na folha namespace, clique em **Políticas de acesso compartilhado**.
3. Na folha **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. Na folha **Política: RootManageSharedAccessKey**, clique no botão copiar ao lado da **Chave primária da cadeia de conexão** para copiar a cadeia de conexão na área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
   
    ![connection-string][connection-string]

5. Repita a etapa anterior, copiando e colando o valor de **chave primária** para um local temporário para uso posterior.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
