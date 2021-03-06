<properties 
    pageTitle="Tutorial: integração do Active Directory do Azure ao Citrix GoToMeeting | Microsoft Azure" 
    description="Saiba como usar o Citrix GoToMeeting com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#Tutorial: integração do Active Directory do Azure ao Citrix GoToMeeting  
Aplica-se ao Azure

>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522412).

O objetivo deste tutorial é mostrar a integração do Azure ao Citrix GoToMeeting. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Um locatário no Citrix GoToMeeting

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Citrix GoToMeeting
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Configuração](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuração")



##Habilitando a integração de aplicativos para o Citrix GoToMeeting

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Citrix GoToMeeting.

###Para habilitar a integração de aplicativos para o Citrix GoToMeeting, execute as seguintes etapas:

1.  No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Citrix GoToMeeting**.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  No painel de resultados, selecione **Citrix GoToMeeting** e clique em **Concluir** para adicionar o aplicativo.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Citrix GoToMeeting com sua própria conta do Azure AD usando federação baseada no protocolo SAML. Como parte deste procedimento, será necessário carregar um certificado codificado em base-64 no locatário do Citrix GoToMeeting. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Para configurar o logon único, execute as seguintes etapas:

1.  Na página de integração do aplicativo **Citrix GoToMeeting**, clique em **Configurar logon único**, para abrir a caixa de diálogo **CONFIGURAR LOGON ÚNICO**.

    ![Habilitar logon único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Habilitar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Citrix GoToMeeting**, selecione **Logon Único do AD do Microsoft Azure**.

    ![Configurar o logon único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar o logon único")


3. Na página **Definir Configurações de Aplicativo** clique em **Próximo**.

	![Habilitar logon único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Habilitar logon único")

4.  Na página **Configurar logon único no Citrix GoToMeeting**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar o logon único")

5.  Em uma janela de navegador diferente, faça logon no seu Citrix Organization Center ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Clique na guia **Provedor de Identidade** e execute as seguintes etapas:

	![Configuração de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configuração de SAML")

	a. Selecione **Manual**

    
	b. No portal clássico do Azure, na página de diálogo **Configurar logon único no Citrix GoToMeeting**, copie o valor da **URL da Página de Entrada** e cole-o na caixa de texto **URL da página de entrada**.

    
	c. No portal clássico do Azure, na página de diálogo **Configurar logon único no Citrix GoToMeeting**, copie o valor da **URL da Página de Saída** e cole-o na caixa de texto **URL da página de saída**.

    
	d. No portal clássico do Azure, na página de diálogo **Configurar logon único no Citrix GoToMeeting**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do Provedor de Identidade**.

   
	e. Clique em **Carregar Certificado** para carregar o certificado que você baixou.

    
	f. Clique em **Salvar**.

6.  No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar o logon único")


7. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Configuração de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configuração de SAML")





##Configurando o provisionamento de usuários

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Citrix GoToMeeting.

###Para configurar o provisionamento de usuários, execute as seguintes etapas:

1.  No portal clássico do Azure, na página de integração do aplicativo **Citrix GoToMeeting**, clique em **Configurar provisionamento de usuários** para abrir o diálogo **Configurar Provisionamento de Usuários**.

    ![Configure o provisionamento do usuário](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure o provisionamento do usuário")

2.  Na página **Configurações e credenciais de administrador**, execute as seguintes etapas:

    ![Configure o provisionamento do usuário](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure o provisionamento do usuário")

	a. Na caixa de texto **Nome de Usuário do Administrador do Citrix GoToMeeting**, digite o nome de usuário de um administrador.

    
	b. Na caixa de texto **Senha do Administrador do Citrix GoToMeeting**, digite a senha do administrador.

    
	c. Clique em **Avançar**.

3.  Em seguida, na página **Confirmação**, clique na marca de seleção para salvar sua configuração.

4.  Clique no botão **Validar** para verificar sua configuração.


##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Citrix GoToMeeting, execute as seguintes etapas:

1.  No Portal clássico do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Citrix GoToMeeting**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sim")

Agora você deve aguardar 10 minutos e verificar se a conta foi sincronizada com o Dropbox for Business.

Como uma primeira etapa de verificação, é possível verificar o status de provisionamento clicando em Painel no D, na página de integração do aplicativo **Citrix GoToMeeting** no portal clássico do Azure.

![Painel](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Painel")

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado:

![Status da integração](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Status da integração")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso.

Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=AcomDC_0817_2016-->