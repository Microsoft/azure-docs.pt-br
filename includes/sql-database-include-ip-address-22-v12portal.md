
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Faça logon no [portal do Azure](https://portal.azure.com/) em http://portal.azure.com/.
2. Na faixa à esquerda, clique em **PROCURAR TUDO**. A folha **Procurar** é exibida.
3. Role e clique em **servidores SQL**. A folha **servidores SQL** é exibida.
   
    ![Encontre o seu servidor do Banco de Dados SQL no portal][b21-FindServerInPortal]
4. Para sua conveniência, clique no controle de minimização na folha **Procurar** anterior.
5. Na caixa de texto de filtro, comece digitando o nome do seu servidor. A linha é exibida.
6. Clique na linha do servidor. Uma folha do servidor é exibida.
7. Na folha do seu servidor, clique em **Configurações**. A folha **Configurações** é exibida.
8. Clique em **Firewall**. A folha **Configurações de Firewall** é exibida.
   
    ![Clique em Configurações > Firewall][b31-SettingsFirewallNavig]
9. Clique em **Adicionar Cliente IP**. Digite um nome para a nova regra na primeira caixa de texto.
10. Digite os valores baixos e altos de endereços IP para o intervalo que deseja habilitar.
    
    * Pode ser útil ter a extremidade de valor baixo com **0,0** e a de valor alto com **0,255**.
    
    ![Adicionar um intervalo de endereços IP para permitir][b41-AddRange]
11. Clique em **Salvar**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
