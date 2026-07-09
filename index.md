# Sources du projet


---

# AcquisitionConnexion.wdc

**Nom :** AcquisitionConnexion

```wlanguage
      AcquisitionConnexion est une Classe
      	herite IConnexionClientSocket
      privee
      	clients est un tableau associatif de IClientSocket dynamique
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit AcquisitionConnexion, mutex=%1",:mutex))
      :nomThread="thAcquisitionConnexion"
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("dÃ©truit AcquisitionConnexion, mutex=%1",:mutex))
```

**Nom :** AjouteClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.AjouteClient
      PROCÃ‰DURE AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :clients.InsÃ¨re(client.id, client)
      Logger.Verbose(ChaÃ®neConstruit("AcquisitionConnexion[%1%] InsÃ¨re le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
```

**Nom :** SupprimeClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.SupprimeClient
      PROCÃ‰DURE SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI :clients[client.id]..Existe ALORS
      	Logger.Verbose(ChaÃ®neConstruit("AcquisitionConnexion[%1%] Supprime le client[%2]=%3,%4",:mutex, client.nomThread, client.ip, client.canalSocket))
      	:clients.Supprime(client.id)
      	pour tout c dans server.RecupereClients()
      		d est TreuilClient dynamique <- c
      		si d<>Null ALORS
      			si d.acquisition<>null _et_ d.acquisition.id = client.id ALORS
      				d.acquisition = Null
      				sortir
      			FIN
      		FIN
      	FIN
      	renvoyer vrai
      fin
      renvoyer faux
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE Demarre():boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("AcquisitionConnexion[%1] Demarre la socket %2, %3, %4",:mutex,:NomSocket,:portSocket,:AdresseSocketServer))
      
      SI SocketCrÃ©e(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketTailleDÃ©but)
      	Logger.Debug("CrÃ©ation de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      FIN
      
      LoggerErreur.EcritLigne("Impossible de crÃ©er la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE Arrete():boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("AcquisitionConnexion[%1] Arrete la socket %2",:mutex,:NomSocket))
      
      :ArreteEcouteSocket()
      
      POUR TOUT c DANS :clients
      	c.Arrete()
      FIN
      
      ArreteThreadSurTimeout(()=>:EstActif(),:nomThread)
      	
      si SocketExiste(:NomSocket) ALORS
      	SocketFerme(:NomSocket)
      FIN
      RENVOYER Vrai
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE EcouteSocket()
      
      SI PAS :arretDemandÃ© ALORS
      	SI SocketAttendConnexion(:NomSocket,:portSocket) ALORS
      		canal est chaine
      		si pas accepte_demande() ALORS
      			SocketRefuse(:NomSocket)
      			retour
      		FIN
      		canal = SocketAccepte(:NomSocket)
      		SI canal<>"" ALORS
      			Logger.Log("AcquisitionConnexion["+:mutex+"] accepte la connexion entrante " + canal)
      			t est AcquisitionSignalClient dynamique = FabriqueClient.CreeClient(:portSocket, canal)
      			si :_AjouteAcquisitionTreuil(t) ALORS
      				:AjouteClient(t)
      				t.Demarre()
      			sinon
      				t.Arrete()
      				SocketRefuse(:NomSocket)
      			FIN
      		FIN
      	FIN
      FIN
      
      procÃ©dure interne accepte_demande():boolÃ©en
      	t est tableau de IClientSocket dynamique = :server.RecupereClients()
      	POUR TOUT c DANS t
      		d est TreuilClient dynamique <- c
      		SI d<>Null ALORS
      			//SI d.type=ITreuilClient.eRecepteur ET d.acquisition=Null ALORS			
      			//SI d.acquisition=Null ALORS			
      				RENVOYER Vrai
      			//FIN
      		FIN
      	FIN	
      	renvoyer faux
      fin
```

**Nom :** _AjouteAcquisitionTreuil

```wlanguage
      procÃ©dure privÃ©e _AjouteAcquisitionTreuil(acq est AcquisitionSignalClient dynamique)
      
      t est tableau de IClientSocket dynamique = :server.RecupereClients()
      
      pour tout c dans t
      	si c.ip = acq.ip ALORS
      		d est TreuilClient dynamique <- c
      		si d<>Null ALORS
      			si d.type=ITreuilClient.eRecepteur et d.acquisition=null ALORS			
      				SI :server.AjouteClient(acq) ALORS				
      					d.acquisition = acq
      					RENVOYER Vrai
      				FIN
      				renvoyer faux
      			FIN
      		FIN
      	FIN
      FIN
      renvoyer faux
```

**Nom :** RecupereClients

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      POUR TOUT c DANS :clients
      	t.Ajoute(c)
      FIN
      
      RENVOYER t
```

**Nom :** server

```wlanguage
         procÃ©dure protÃ©gÃ©e server() : IConnexionClientSocket
         
         renvoyer ServeurConnexion.MonInstance
```

**Nom :** TEST_AcquisitionConnexion.wxt

---

# AcquisitionSignalClient.wdc

**Nom :** AcquisitionSignalClient

```wlanguage
      AcquisitionSignalClient est une Classe
      	herite IAcquisitionSignal
      	CONSTANTE TailleBufferMax = 100
      	bufferLecture est un buffer
      	acquisitionDemarrÃ©e est un boolÃ©en=faux
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit AcquisitionSignal")
      
      :nomThread = "AcquisitionSignal"+:id
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      Logger.Verbose("dÃ©truit AcquisitionSignal")
      
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      :acquisitionDemarrÃ©e = vrai
      Logger.Debug("AcquisitionSignal Demarre, thread="+:nomThread)
      RENVOYER Vrai
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE Arrete():boolÃ©en
      
      Logger.Debug("AcquisitionSignal Arrete, thread="+:nomThread)
      :acquisitionDemarrÃ©e = Faux
      :ArreteEcouteSocket()
      RENVOYER Vrai
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE EcouteSocket()
      
      tmp est un buffer = :Lit()
      SI tmp<>"" ALORS
      	:bufferLecture+=tmp
      	si :_synchroniseTrameDansBuffer() _et_ :acquisitionDemarrÃ©e alors
      		:_parseBuffer()
      	FIN
      FIN
```

**Nom :** _parseBuffer

```wlanguage
      procÃ©dure privÃ©e _parseBuffer()
      
      nbMsg est un entier = Taille(:bufferLecture)/CST_TAILLE_STRUCT
      stRDat est un IAcquisitionSignal.Str_DATA_Acquisition
      nPos est entier = 0
      bufTemp est un Buffer
      
      Logger.Verbose("Parse le buffer")
      
      i est un entier
      POUR i = 1 Ã€ nbMsg
      	bufTemp = :bufferLecture[[nPos+1 Ã€ CST_TAILLE_STRUCT+nPos ]]
      	nPos+=CST_TAILLE_STRUCT
      	Transfert(&stRDat,&bufTemp,CST_TAILLE_STRUCT)
      	:EnfileBuffer(stRDat)
      	Logger.Verbose("Buffer index :" +stRDat.Index)
      //	Trace(bufTemp)
      FIN
      SI Taille(:bufferLecture)>nPos ALORS
      	:bufferLecture = :bufferLecture[[nPos+1 Ã€]]
      SINON
      	:bufferLecture=""
      FIN
```

**Nom :** _synchroniseTrameDansBuffer

```wlanguage
      procÃ©dure privÃ©e _synchroniseTrameDansBuffer() : boolÃ©en
      
      pour i=1 _a_ bufferLecture..Taille
      	si :bufferLecture..Taille>=3 _et_ :bufferLecture[1 a 3]<>"DEB" ALORS
      		:bufferLecture = :bufferLecture[2 Ã€ ]
      	sinon
      		SI :bufferLecture..Taille>=CST_TAILLE_STRUCT ALORS
      			SI :bufferLecture[CST_TAILLE_STRUCT-3 Ã€ CST_TAILLE_STRUCT-1] = "FIN" ALORS
      				Logger.Verbose("buffer synchro")
      				RENVOYER Vrai
      			SINON
      				LoggerErreur.EcritLigne("buffer non synchro pas de fin trouvÃ©, reset buffer")
      				:bufferLecture = :bufferLecture[CST_TAILLE_STRUCT A]
      				renvoyer faux
      			FIN
      		FIN
      		sortir
      	FIN
      fin
      
      renvoyer faux
```

**Nom :** EnfileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.EnfileBuffer
      PROCÃ‰DURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
      Enfile(:fileBuffer,buf)
```

**Nom :** DefileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.DefileBuffer
      PROCÃ‰DURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      DÃ©file(:fileBuffer,buf)
      renvoyer buf
```

**Nom :** TaillefileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.TaillefileBuffer
      PROCÃ‰DURE  TaillefileBuffer() : entier
      
      renvoyer :fileBuffer..Occurrence
```


---

# AppConnexion.wdc

**Nom :** AppConnexion

```wlanguage
      AppConnexion est une Classe
      	hÃ©rite IConnexionClientSocket
      PRIVÃ‰E
      	client est un IClientSocket dynamique
      FIN
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit AppConnexion, mutex=%1",:mutex))
      :nomThread="thApplicationConnexion"
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** AjouteClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.AjouteClient
      PROCÃ‰DURE  AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :client = client
      Logger.Verbose(ChaÃ®neConstruit("AppConnexion[%1%] InsÃ¨re le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete() : boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("AppConnexion[%1] Arrete la socket %2",:mutex,:NomSocket))
      
      :ArreteEcouteSocket()
      
      si :client<>null
      	:client.Arrete()
      fin
      
      ArreteThreadSurTimeout(()=>:EstActif(),:nomThread)
      
      SI SocketExiste(:NomSocket) ALORS
      	SocketFerme(:NomSocket)
      FIN
      RENVOYER Vrai
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre() : boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("AppConnexion[%1] Demarre la socket %2, %3, %4",:mutex,:NomSocket,:portSocket,:AdresseSocketServer))
      
      SI SocketCrÃ©e(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketTailleDÃ©but)
      	Logger.Debug("CrÃ©ation de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      FIN
      
      LoggerErreur.EcritLigne("Impossible de crÃ©er la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      SI PAS :arretDemandÃ© ALORS
      	SI SocketAttendConnexion(:NomSocket,:portSocket) ALORS
      		canal est chaÃ®ne
      		SI PAS accepte_demande() ALORS
      			SocketRefuse(:NomSocket)
      			RETOUR
      		FIN
      		canal = SocketAccepte(:NomSocket)
      		SI canal<>"" ALORS
      			Logger.Log("AppConnexion["+:mutex+"] accepte la connexion entrante " + canal)
      			t est IClientSocket dynamique = FabriqueClient.CreeClient(:portSocket, canal)
      			:AjouteClient(t)
      			t.Demarre()
      		FIN
      	FIN
      FIN
      
      	PROCÃ‰DURE INTERNE accepte_demande():boolÃ©en
      		t est tableau de IClientSocket dynamique = :server.RecupereClients()
      		si t..Vide alors renvoyer Vrai
      		POUR TOUT c DANS t
      			d est TreuilClient dynamique <- c
      			SI d<>Null ALORS
      				//SI d.type=ITreuilClient.eRecepteur ET d.acquisition=Null ALORS
      				//SI d.acquisition=Null ALORS
      				RENVOYER Vrai
      				//FIN
      			FIN
      		FIN	
      		RENVOYER Faux
      	FIN
```

**Nom :** RecupereClients

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      t est tableau de IClientSocket dynamique
      
      t.Ajoute(:client)
      
      RENVOYER t
```

**Nom :** SupprimeClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.SupprimeClient
      PROCÃ‰DURE  SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI :client = client ALORS
      	Logger.Verbose(ChaÃ®neConstruit("AppConnexion[%1%] Supprime le client[%2]=%3,%4",:mutex, client.nomThread, client.ip, client.canalSocket))
      	:client.Arrete()
      	:client = null
      	RENVOYER Vrai
      FIN
      RENVOYER Faux
```

**Nom :** server

```wlanguage
         procÃ©dure privÃ©e  server() : IConnexionClientSocket
         
         RENVOYER ServeurConnexion.MonInstance
```


---

# ApplicationClient.wdc

**Nom :** ApplicationClient

```wlanguage
      ApplicationClient est une Classe
      	herite IClientSocket
      	bufferLecture est un Buffer
      
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit ApplicationClient")
      
      :nomThread = "ApplicationClient"+:id
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      Logger.Verbose("dÃ©truit ApplicationClient")
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre() : boolÃ©en
      
      :DemarreEcouteSocket()
      Logger.Debug("ApplicationClient Demarre, thread="+:nomThread)
      RENVOYER Vrai
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      tmp est un Buffer = :Lit()
      SI tmp<>"" ALORS
      	:bufferLecture+=tmp
      	_ParseDonnees(:bufferLecture)
      	//Trace(:bufferLecture)	
      	:bufferLecture=""
      FIN
```

**Nom :** _ParseDonnees

```wlanguage
      procÃ©dure protÃ©gÃ©e _ParseDonnees(_buffer est buffer)
      
      
      QUAND EXCEPTION DANS
      	nbMsg				est un entier	= Taille(_buffer)/1000
      	sVariable_Message	est une chaÃ®ne
      	sContenu_Message	est une chaÃ®ne
      	sVariable_AR		est une chaÃ®ne
      	sContenu_AR			est une chaÃ®ne
      	
      	POUR i = 1 Ã€ nbMsg
      		
      //		sVariable_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) Ã€ (i*::Size_MSG_Socket_RX)]],1,::CST_SOCKMSG_SEP)
      //		sContenu_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) Ã€ (i*::Size_MSG_Socket_RX)]],2,::CST_SOCKMSG_SEP)	
      		
      		SELON sVariable_Message
      			CAS "STATUS","TEMP"
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      			//				
      			CAS "DECO"
      				//				SELON sContenu_Message					
      				//					CAS "exit.From.PC"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de lâ€™application suite Ã  la demande provenant du PC Serveur")	
      				//					CAS "exit.From.PEGASE"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de l'application suite Ã  la demande provenant de lâ€™application Cliente Pegase")	
      				//					CAS "exit.From.REBOOT"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de l'application suite Ã  la demande provenant d'un Reboot de la plateforme Pegase")	
      				//					AUTRE CAS
      				//						LoggerErreur.EcritLigne(nIndiceListeClient+"Sortie de l'application suite Ã  la demande provenant de source inconnue")	
      				//				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      			//					
      			CAS "ERR"
      //				sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")	
      				SELON sVariable_AR
      					CAS "ARRT"
      						SI sContenu_AR = "PILE---WIFI" ALORS
      							//ERREUR WIFI - Goulot d'etranglement							
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+ERREUR de transmission WIFI")
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Erreur de transmission du buffer dâ€™acquisition par la socket, le treuil arrÃªte lâ€™acquisition pour Ã©viter un plantage")
      						FIN
      					//						
      					CAS "IDENT"
      						SI sContenu_AR = "NoCOMP" ALORS
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne voit aucun composant compatible")
      						FIN	
      						SI sContenu_AR = "COMPNonAdresse" ALORS
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne reconnaÃ®t pas le composant")
      						FIN	
      						//					CAS "COMM"
      						//						SI sContenu_AR = "----Arduino" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Communication sÃ©rie Pegase/Arduino inopÃ©rante")
      						//							LoggerErreur.EcritLigne("Gain _: Erreur"+sContenu_AR)
      						//						FIN	
      						//						SI sContenu_AR = "----OneWire" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Communication 1-Wire Arduino/Sonde inopÃ©rante")
      						//							LoggerErreur.EcritLigne("Gain _: Erreur"+sContenu_AR)
      						//						FIN
      						//					CAS "MESURE"
      						//						SI sContenu_AR = "SENS--COD" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Le moteur tourne dans le mauvais sens")
      						//						FIN	
      						//					CAS "VITESSE"
      						//						SI sContenu_AR = "NONSPECIF" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Pas de vitesse configurÃ©e")
      						//						FIN	
      						//					CAS "MOTEUR"
      						//						SI sContenu_AR = "BLOQ--MOT" ALORS							
      						//							Logger.Debug(nIndiceListeClient+"Blocage moteur, aucune rotation dÃ©tectÃ©e")
      						//							Gb_AnnuleMesureERR = Vrai
      						//							LoggerErreur.EcritLigne( "Blocage moteur, aucune rotation dÃ©tectÃ©e")
      						//						FIN	
      						//						SI sContenu_AR = "DATA--COD" ALORS							
      						//							Logger.Debug(nIndiceListeClient+"RÃ©ception donnÃ©es Codeur alors que le moteur est censÃ© Ãªtre Ã  l'arrÃªt")
      						//						FIN
      						//					CAS "GOTO"
      						//						SI sContenu_AR = "NON-ATTEINT" ALORS
      						//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"ArrÃªt moteur alors que la destination du GOTO nâ€™a pas Ã©tÃ© atteinte")
      						//							Gn_FinGOTO++	
      						//							SI Gn_FinGOTO > 1 ALORS
      						//								SI FenEtat(FEN_trptfixe)<>Inexistant ALORS 
      						//									FEN_trptfixe.BTNACTION..GrisÃ© = Faux
      						//									FEN_trptfixe.BTNENVOYER..GrisÃ© = Faux
      						//								FIN
      						//								SI FenEtat(FEN_trdepla)<>Inexistant ALORS
      						//									FEN_trdepla.BTNACTION..GrisÃ© = Faux
      						//									FEN_trdepla.BTN_RazTreuil..GrisÃ© = Faux
      						//									FEN_trdepla.FORCEPOS..GrisÃ© = Faux	
      						//								FIN
      						//							FIN
      						//						FIN
      						//					AUTRE CAS
      						//						Logger.Log(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      						//						Logger.Debug(nIndiceListeClient+sVariable_AR+"_:_"+sContenu_AR)
      				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      				//				
      			//						
      			CAS "TYPE"
      //				SI PAS Contient(sContenu_Message, "@") ALORS
      //					
      //					SI Contient(sContenu_Message, ::SOCKREP_TYPE_TREUIL_EMETEUR) ALORS
      //						:type = ITreuilClient.eEmetteur
      //						:Ecrit(::SOCKMSG_TYPE_CFG_EMETTEUR)				
      //					SINON SI Contient(sContenu_Message, ::SOCKREP_TYPE_TREUIL_RECEPTEUR) ALORS
      //						:type = ITreuilClient.eRecepteur
      //						:Ecrit(::SOCKMSG_TYPE_CFG_RECEPTEUR)
      //					SINON
      //						:type = ITreuilClient.eNonIdentifie
      //						:Ecrit(::SOCKMSG_REBOOT)
      //					FIN
      //				FIN
      			//			
      			CAS "RAVS"
      //				sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      				//				SELON sVariable_AR								
      				//					CAS "1"
      				//						si Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilIP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      				//							Param_Interne_Treuils.mTreuilType=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      				//							
      				//							Param_Interne_Treuils.mTreuilVersion=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      				//							GG_AVS.iVersionT1 = val(Param_Interne_Treuils.mTreuilVersion)*100
      				//							Param_Interne_Treuils.mTreuilFreq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      				//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 1 ALORS
      				//								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      				//							SINON
      				//								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      				//							FIN							
      				//							si Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 alors 
      				//								Param_Interne_Treuils.mTreuilSens="MontÃ©e"
      				//							sinon
      				//								Param_Interne_Treuils.mTreuilSens="Descente"
      				//							FIN
      				//						sinon
      				//							Param_Interne_Treuils.T2_IP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      				//							Param_Interne_Treuils.T2_Type=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      				//							
      				//							Param_Interne_Treuils.T2_Version=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      				//							GG_AVS.iVersionT2 = Val(Param_Interne_Treuils.T2_Version)*100
      				//							Param_Interne_Treuils.T2_Freq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      				//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 2 ALORS
      				//								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      				//							SINON
      				//								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      				//							FIN
      				//							SI Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 ALORS 
      				//								Param_Interne_Treuils.T2_Sens="MontÃ©e"
      				//							SINON
      				//								Param_Interne_Treuils.T2_Sens="Descente"
      				//							FIN
      				//						FIN
      				//						
      				//					CAS "2"
      				//						Gn_REP_QAVS++			
      				//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilProfondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      				//							Param_Interne_Treuils.mTreuilVitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      				//							Param_Interne_Treuils.mTreuilVitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      				//							Param_Interne_Treuils.mTreuilMesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))	
      				//						SINON
      				//							Param_Interne_Treuils.T2_Profondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      				//							Param_Interne_Treuils.T2_VitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      				//							Param_Interne_Treuils.T2_VitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      				//							Param_Interne_Treuils.T2_Mesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))				
      				//						FIN	
      				//						Param_Interne_Treuils.ValT0 = G_AVST0
      				//					CAS "3"
      				//						Gn_REP_QAVS++
      				//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_ACQ=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_BP=Droite(Gauche(sContenu_AR,9),3)
      				//						SINON
      				//							Param_Interne_Treuils.T2_Version_DRV_ACQ=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.T2_Version_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.T2_Version_DRV_BP=Droite(Gauche(sContenu_AR,9),3)				
      				//						FIN
      				//					CAS "4"
      				//						Gn_REP_QAVS++
      				//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_Capture=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)
      				//						SINON
      				//							Param_Interne_Treuils.T2_Version_DRV_Capture=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.T2_Version_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.T2_Version_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)				
      				//						FIN
      				//					CAS "5"
      				//						Gn_REP_QAVS++
      				//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_HeureABS=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.mTreuilVersion_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)
      				//						SINON
      				//							Param_Interne_Treuils.T2_Version_DRV_HeureABS=Gauche(sContenu_AR,3)
      				//							Param_Interne_Treuils.T2_Version_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      				//							Param_Interne_Treuils.T2_Version_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)				
      				//						FIN
      				//					FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      				Logger.Log("Client["+:canalSocket+","+:ip+"]+_RAVS:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				
      			CAS "RPAR"
      //				sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      //				SELON sVariable_AR
      //				//CAS "ARRT"
      //					//MAJ_AFF_TRAME_SOCKET(0,"Energie _:"+Gauche(sContenu_AR,2))				 											
      //					CAS "VPRO"
      //						:Param_Interne_Treuils.Energie=Val(Gauche(sContenu_AR,2))
      //						
      //					CAS "COMM"
      //						:Param_Interne_Treuils.Gain=Val(Gauche(sContenu_AR,2))
      //						
      //					CAS "QNUM"
      //						:Param_Interne_Treuils.numeroTreuil = Val(sContenu_AR)
      //						
      //						
      //					CAS "QPOS"
      //						:Param_Interne_Treuils.mTreuilProfondeur=Val(sContenu_AR)/2
      //						
      //					CAS "QVIT"
      //						:Param_Interne_Treuils.Vitesse_cm_s = Val(sContenu_AR)
      //						
      //					CAS "QDIF"
      //						:Param_Interne_Treuils.Decalage_Position_cm = Val(sContenu_AR)/2// reception en coup codeur ~2coups /cm
      //					AUTRE CAS
      //						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      //				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      				//				
      				//				
      				//			CAS "MSG"
      				//				sVariable_AR = ExtraitChaÃ®ne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],1,"=")
      				//				sContenu_AR  = ExtraitChaÃ®ne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],2,"=")
      				//				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				//				SELON sVariable_AR
      				//					CAS "MESURE"					
      				//						si sContenu_AR = "FIN--GOTO" ALORS
      				//							Gn_FinGOTO++
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"----==FIN GOTO MESURE==----")	
      				//							SI Gn_FinGOTO > 1 ALORS
      				//								 SI FenEtat(FEN_trptfixe)<>Inexistant ALORS 
      				//								 	FEN_trptfixe.BTNACTION..GrisÃ© = Faux
      				//								 	FEN_trptfixe.BTNENVOYER..GrisÃ© = Faux
      				//								 FIN
      				//								 SI FenEtat(FEN_trdepla)<>Inexistant ALORS
      				//									FEN_trdepla.BTNACTION..GrisÃ© = Faux
      				//									FEN_trdepla.BTN_RazTreuil..GrisÃ© = Faux
      				//									FEN_trdepla.FORCEPOS..GrisÃ© = Faux	
      				//								 FIN
      				//							FIN
      				//						FIN
      				//					cas "CALIB"
      				//						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				//						
      				//					AUTRE CAS
      				//						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				//				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      				//				
      				//			CAS "EVT"
      				//				sVariable_AR = ExtraitChaÃ®ne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],1,"=")
      				//				sContenu_AR  = ExtraitChaÃ®ne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],2,"=")
      				//				SELON sVariable_AR
      				//					CAS "STOP"
      				//						Gn_StopFROM = Val(Droite(sContenu_AR,2))
      				//						SELON Gn_StopFROM							
      				//							CAS CST_STOPFROM_ARRIVEAFTERGOTO
      				//								//Trace("Gn_StopFROM _:_CST_STOPFROM_ARRIVEAFTERGOTO")
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : ARRIVED_AFTER_GOTO")
      				//							CAS	CST_STOPFROM_FINCALIB
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : FIN_CALIBRATION")
      				//							CAS	CST_STOPFROM_PWMNULLE
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : PWM_NULLE")
      				//							CAS CST_STOPFROM_CONFIGSENS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : CONFIG_SENS")
      				//							CAS CST_STOPFROM_ERRSENS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Erreur SENS")
      				//							CAS CST_STOPFROM_BP
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Bouton Poussoir")
      				//							CAS CST_STOPFROM_CLOSEPWM
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Fin PWM")
      				//							CAS CST_STOPFROM_AU
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Arret d'Urgence")
      				//							CAS CST_STOPFROM_ERRCALIB
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Erreur CALIBRATION")	
      				//							CAS CST_STOPFROM_ERRGOTO
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Erreur GOTO")
      				//							CAS CST_STOPFROM_CMDSOCKET
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Depuis PC")
      				//							CAS CST_STOPFROM_BLOCAGEMOTEUR
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur : Blocage Moteur")		
      				//							AUTRE CAS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrÃªt moteur inconnu")
      				//								Gn_StopFROM = CST_STOPFROM_BLOCAGEMOTEUR+1
      				//						FIN
      				//						//[TEST]
      				//						enfile(fileDemandePostionTreuils,vrai)
      				//					CAS "BTNP"
      				//						SI sContenu_AR = "FROM-----UP" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton de montÃ©e")
      				//						FIN	
      				//						SI sContenu_AR = "FROM---DOWN" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton de descente")
      				//						FIN	
      				//						SI sContenu_AR = "FROM-----AU" ALORS							
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton dâ€™arrÃªt dâ€™urgence")
      				//							Gb_AnnuleMesureERR = Vrai
      				//							Enfile(fileErreurMessage, "Appui sur le bouton dâ€™arrÃªt dâ€™urgence")
      				//						FIN	
      				//					CAS "BTNR"
      				//						SI sContenu_AR = "FROM-----UP" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"RelÃ¢che du bouton de montÃ©e")
      				//						FIN	
      				//						SI sContenu_AR = "FROM---DOWN" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"RelÃ¢che du bouton de descente")
      				//						FIN	
      				//						SI sContenu_AR = "FROM-----AU" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"RelÃ¢che du bouton dâ€™arrÃªt dâ€™urgence")
      				//						FIN							
      				//					AUTRE CAS
      				//						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				//				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      				//					
      				////			CAS "COMM"
      				////				SI (Taille(sContenu_Message) = 16) ALORS
      				////					Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      				////				SINON
      				////					Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+"Erreur RÃ©ception Contenu_Message")
      				////				FIN
      			//				
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Variable :"+sVariable_Message)
      				LoggerErreur.EcritLigne("Contenu :"+sContenu_Message)
      				//LoggerErreur.EcritLigne(ChaÃ®neConstruit("[%1%][%2][%3]_:_Erreur RÃ©ception Contenu_Message",:ip,:id,:type))
      		FIN	
      		//			
      		//		TableauSupprime(GG_sendMessage,sVariable_Message)
      	FIN
      	
      FAIRE
      	ExceptionActive()
      	LoggerErreur.EcritLigne("exception dans _TreuilsSockParse")
      FIN
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete() : boolÃ©en
      
      Logger.Debug("Arrete ApplicationClient["+:mutex+"]")
      :ArreteEcouteSocket()
      RENVOYER Vrai
```


---

# FabriqueClient.wdc

**Nom :** FabriqueClient

```wlanguage
      FabriqueClient est une Classe
      
      	constante MaxClientsRecepteur = 10
      	constante SocketEcoute = "SOCKET_ECOUTE"
      	constante SocketAcquisition = "SOCKET_ACQUISITION"
      	globale
      		AdresseIP est chaine = "192.168.0.100"
      fin
      
      PortEnum est une Ã©numÃ©ration
      	portTreuil = 10
      //	portEmetteur = 12
      //	portRecepteur = 13
      	portAcquisition = 11
      	portApplication=100
      fin
      
      EcouteConnexionEnum est une Ã©numÃ©ration
      //	connexionTreuilRecepteur=1
      //	connexiontreuilEmetteur=2
      	connexionAcquisition=3
      	connexiontreuilNonIdent=4
      	connexionApp=5
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** CreeClient

```wlanguage
      procÃ©dure globale CreeClient(port est un entier, socket est chaine) : IClientSocket
      
      selon port
      	cas val(PortEnum.portAcquisition):
      		Logger.Verbose("Allocation AcquisitionSignal")
      		ac est AcquisitionSignalClient dynamique = allouer AcquisitionSignalClient()
      		ac.canalSocket = socket
      		ac.portSocket = port
      		ac.ip=SocketClientInfo(socket, SocketAdresse)
      		ac.nomThread="SignalAcquisition_"+ac.id+"_"+ac.ip
      		RENVOYER ac
      	cas val(PortEnum.portTreuil):
      		Logger.Verbose("Allocation Treuil R/E")
      		reem est TreuilClient dynamique = allouer TreuilClient()
      		reem.type=ITreuilClient.eNonIdentifie
      		reem.canalSocket = socket
      		reem.portSocket = port
      		reem.ip=SocketClientInfo(socket, SocketAdresse)
      		RENVOYER reem
      	cas val(PortEnum.portApplication):
      		Logger.Verbose("Allocation Application client")
      		app est ApplicationClient dynamique = allouer ApplicationClient()
      		app.canalSocket = socket
      		app.portSocket	= port
      		app.ip = SocketClientInfo(socket, SocketAdresse)
      		RENVOYER app
      		
      		
      	AUTRE CAS
      		
      FIN
      
      renvoyer null
```

**Nom :** CreeConnexionAuServeur

```wlanguage
      procÃ©dure GLOBALE CreeConnexionAuServeur(type est EcouteConnexionEnum) : IConnexionClientSocket
      
      conn est IConnexionClientSocket dynamique
      
      selon type
      	CAS EcouteConnexionEnum.connexionAcquisition
      		Logger.Verbose("Allocation connexiontreuilAcquisition")
      		conn = allouer un AcquisitionConnexion()
      		conn.portSocket = Val(FabriqueClient.PortEnum.portAcquisition)
      		conn.AdresseSocketServer = FabriqueClient.AdresseIP
      		conn.NomSocket = FabriqueClient.SocketAcquisition
      		conn.nomThread = "thConnexionAcquisition"+conn.id
      		conn.Build()
      	
      	cas EcouteConnexionEnum.connexiontreuilNonIdent
      		Logger.Verbose("Allocation connexiontreuilAcquisition")
      		conn = allouer un TreuilConnexion()
      		conn.portSocket = Val(FabriqueClient.PortEnum.portTreuil)
      		conn.AdresseSocketServer = FabriqueClient.AdresseIP
      		conn.NomSocket = FabriqueClient.SocketEcoute+"_ER"
      		conn.nomThread = "thConnexioTreuil"+conn.id
      		conn.Build()
      	
      	cas EcouteConnexionEnum.connexionApp
      		Logger.Verbose("Allocation appConnexion")
      		conn = allouer un AppConnexion()
      		conn.portSocket = Val(FabriqueClient.PortEnum.portApplication)
      		conn.AdresseSocketServer	= FabriqueClient.AdresseIP
      		conn.NomSocket = FabriqueClient.SocketEcoute+"_APP"
      		conn.nomThread = "thApplication"+conn.id
      		conn.Build()
      		
      	autre cas:
      		LoggerErreur.EcritLigne("Type de connexion inconnu")
      		ExceptionDÃ©clenche(1,"Type de connexion inconnu")
      FIN
      
      renvoyer conn
```


---

# FabriqueDeSignal.wdc

**Nom :** FabriqueDeSignal

```wlanguage
      FabriqueDeSignal est une Classe
      
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** CreeTraitementSignal

```wlanguage
      procÃ©dure publique globale CreeTraitementSignal() : ISignalTraitement 
      
      traitement est ISignalTraitement dynamique = allouer SignalTraitement()
      
      renvoyer traitement
```

**Nom :** CreeEnregistrementSignal

```wlanguage
      procÃ©dure publique globale CreeEnregistrementSignal() : ISignalEnregistrement
      
      record est SignalEnregistrement dynamique = allouer SignalEnregistrement()
      
      RENVOYER record
```

**Nom :** CreeDessineSignal

```wlanguage
      procÃ©dure publique globale CreeDessineSignal() : ISignalDessine
      
      dessin est SignalDessine dynamique = allouer SignalDessine()
      
      RENVOYER dessin
```


---

# IAcquisitionSignal.wdc

**Nom :** IAcquisitionSignal

```wlanguage
      IAcquisitionSignal est une Classe
      	hÃ©rite IClientSocket
      	fileBuffer est une file de Str_DATA_Acquisition
      	
      	CONSTANTE Size_MSG_Socket_RX_ACQU = 3000
      	CONSTANTE CST_TAILLE_STRUCT = (7*(4))+(Size_MSG_Socket_RX_ACQU*(2))
      fin
      
      Str_DATA_Acquisition est une structure
      	sEntete est une chaine sur 3
      	SizeStruct est un entier sur 4
      	Index est un entier sur 4
      	POS_EMET est un entier sur 4
      	POS_RECEPT est un entier sur 4
      	Buffer_size est un entier sur 4
      	tabBuffer est un tableau fixe de Size_MSG_Socket_RX_ACQU entier sur 2
      	sEnd est une chaine sur 3
      	//Dechet est une chaine sur 1
      FIN
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** EnfileBuffer

```wlanguage
      procÃ©dure abstraite EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
```

**Nom :** DefileBuffer

```wlanguage
      procÃ©dure abstraite DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
```

**Nom :** TaillefileBuffer

```wlanguage
      procÃ©dure abstraite TaillefileBuffer() : entier
      
```


---

# IClientSocket.wdc

**Nom :** IClientSocket

```wlanguage
      IClientSocket est une Classe
      	id est entier
      	canalSocket est une chaine
      	portSocket est un entier
      	ip est une chaÃ®ne
      	nomThread est une chaÃ®ne
      	dernierMsgEnvoye est une chaine
      	dernierMsgRecu est une chaine
      	
      	protegee
      		arretDemandÃ© est un boolÃ©en
      		threadPauseMs est un entier = 1
      		mutex est chaine
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      :id = &:id
      :nomThread = "ThreadIClientSocket"+:id
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      MutexDÃ©truit(:mutex)
      SignalDÃ©truit(:nomThread)
      Logger.Verbose("dÃ©truit IClientSocket, mutex=" + :mutex)
```

**Nom :** EstActif

```wlanguage
      procÃ©dure VIRTUELLE EstActif() : boolÃ©en
      
      renvoyer ThreadEtat(:nomThread)=threadEnCours
```

**Nom :** EstArretDemande

```wlanguage
      procÃ©dure VIRTUELLE EstArretDemande() : boolÃ©en
      
      renvoyer :arretDemandÃ©
```

**Nom :** DemarreEcouteSocket

```wlanguage
      procÃ©dure virtuelle DemarreEcouteSocket()
      
      :arretDemandÃ©=faux
      SignalModifie(:nomThread,signalFermÃ©)
      
      ThreadExÃ©cute(:nomThread,threadNormal,()=>{
      	QUAND EXCEPTION
      		LoggerErreur.EcritLigne(ExceptionInfo(errMessage))
      		ExceptionPropage(ExceptionInfo(errMessage))
      	FIN
      	
      	boucle
      		si SignalAttend(nomThread,1cs) ALORS
      			sortir
      		FIN
      		:EcouteSocket()
      		ThreadPause(:threadPauseMs)
      	fin
      	
      	SI :canalSocket<>"" _et_ SocketExiste(:canalSocket) ALORS
      		SocketFerme(:canalSocket)	
      	FIN
      })
```

**Nom :** ArreteEcouteSocket

```wlanguage
      procÃ©dure VIRTUELLE ArreteEcouteSocket()
      
      Logger.Verbose(ChaÃ®neConstruit("Arrete d'Ã©couter la socket %1",:canalSocket))
      :arretDemandÃ©=vrai
      SignalModifie(:nomThread,signalOuvert)
```

**Nom :** Demarre

```wlanguage
      procÃ©dure  abstraite Demarre() : boolÃ©en
      
```

**Nom :** Arrete

```wlanguage
      procÃ©dure  abstraite Arrete() : boolÃ©en
      
```

**Nom :** EcouteSocket

```wlanguage
      procÃ©dure  ABSTRAITE EcouteSocket()
      
```

**Nom :** Ecrit

```wlanguage
      procÃ©dure virtuelle Ecrit(msg est chaine) : boolÃ©en
      
      Logger.Debug("client["+:nomThread+"] envoie="+msg)
      :dernierMsgEnvoye=msg
      renvoyer SocketEcrit(:canalSocket,msg)
```

**Nom :** Lit

```wlanguage
      procÃ©dure VIRTUELLE lit() : buffer
      
      buf est un buffer = SocketLit(:canalSocket,faux,10ms)
      si ErreurDÃ©tectÃ©e ALORS
      	LoggerErreur.EcritLigne(ErreurInfo(errMessage))
      	:arretDemandÃ©=vrai
      	SignalModifie(:nomThread,signalOuvert)
      	renvoyer buf
      FIN
      si buf<>"" ALORS
      	Logger.Debug("client["+:nomThread+"] lit="+buf)
      	:dernierMsgRecu=buf
      FIN
      renvoyer buf
```

**Nom :** Build

```wlanguage
      procÃ©dure Build()
      
      :mutex = "mutex"+:id
      
      MutexCrÃ©e(:mutex,Faux,partageAucun)
      SignalCrÃ©e(:nomThread,signalManuel,signalFermÃ©,partageAucun)
      
      Logger.Verbose("Build IClientSocket, mutex=" + :mutex + ", thread=" + :nomThread)
```


---

# IConnexionClientSocket.wdc

**Nom :** IConnexionClientSocket

```wlanguage
      IConnexionClientSocket est une Classe
      	herite IClientSocket
      	NomSocket est une chaÃ®ne 
      	AdresseSocketServer est une chaÃ®ne
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** AjouteClient

```wlanguage
      procÃ©dure abstraite AjouteClient(client IClientSocket dynamique):boolÃ©en
      
```

**Nom :** SupprimeClient

```wlanguage
      procÃ©dure ABSTRAITE SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
```

**Nom :** RecupereClients

```wlanguage
      procÃ©dure abstraite RecupereClients() : tableau de IClientSocket dynamique
      
```


---

# IParser.wdc

**Nom :** IParser

```wlanguage
      IParser est une Classe
      	implemente IAcquisitionProc 
      	id est entier
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      :id = &objet
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure abstraite ArreteAcquisition() : boolÃ©en
```

**Nom :** DemarreAcquisition

```wlanguage
      procÃ©dure ABSTRAITE DemarreAcquisition() : boolÃ©en
```

**Nom :** Parametre

```wlanguage
      procÃ©dure ABSTRAITE Parametre(params)
```

**Nom :** ParseDonnees

```wlanguage
      procÃ©dure abstraite ParseDonnees(treuil ITreuilClient dynamique, buf est un Buffer)
```

**Nom :** id

```wlanguage
         procÃ©dure id() : entier
         
         renvoyer :id
```


```wlanguage
         procÃ©dure id(Valeur est un entier)
         
         :id=Valeur
```


---

# ISignalDessine.wdc

**Nom :** ISignalDessine

```wlanguage
      ISignalDessine est une Classe
      	implÃ©mente IAcquisitionProc
      	id est un entier = 0
      //	champImage est une wl.Image
      	arretTimerSysDessin	est un boolÃ©en
      	fileAffichage est une file de IAcquisitionSignal.Str_DATA_Acquisition
      fin
       
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** Parametre

```wlanguage
      procÃ©dure abstraite Parametre(champImage)
```

**Nom :** DemarreAcquisition

```wlanguage
      procÃ©dure abstraite DemarreAcquisition() : boolÃ©en
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure abstraite ArreteAcquisition() : boolÃ©en
```

**Nom :** DessineDansImage

```wlanguage
      procÃ©dure abstraite DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
```

**Nom :** DebutDessin

```wlanguage
      procÃ©dure abstraite DebutDessin()
```

**Nom :** SauveImageJpeg

```wlanguage
      procÃ©dure abstraite SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
```

**Nom :** PrepareDessin

```wlanguage
      procÃ©dure abstraite PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
```

**Nom :** Affichage

```wlanguage
      procÃ©dure abstraite Affichage()
      
```

**Nom :** EnfileSignal

```wlanguage
      procÃ©dure abstraite EnfileSignal(elem est IAcquisitionSignal.Str_DATA_Acquisition)
      
```

**Nom :** id

```wlanguage
         procÃ©dure id() : entier
         renvoyer :id
```


```wlanguage
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
```


---

# ISignalEnregistrement.wdc

**Nom :** ISignalEnregistrement

```wlanguage
      ISignalEnregistrement est une Classe
      	implÃ©mente IAcquisitionProc
      	id est un entier = 0
      	nomFichier est une chaÃ®ne
      	handleFic est entier
      	fileBuffer est une file de IAcquisitionSignal.Str_DATA_Acquisition
      fin
       
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** DemarreAcquisition

```wlanguage
      procÃ©dure abstraite DemarreAcquisition() : boolÃ©en
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure abstraite ArreteAcquisition() : boolÃ©en
```

**Nom :** Parametre

```wlanguage
      procÃ©dure abstraite Parametre(nomFichier)
```

**Nom :** EcritDansFichier

```wlanguage
      procÃ©dure abstraite EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
```

**Nom :** id

```wlanguage
         procÃ©dure id() : entier
         renvoyer :id
```


```wlanguage
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
```


---

# ISignalTraitement.wdc

**Nom :** ISignalTraitement

```wlanguage
      ISignalTraitement est une Classe
      	implemente IAcquisitionProc
      	id est un entier = 0
      fin
       
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** DemarreAcquisition

```wlanguage
      procÃ©dure abstraite DemarreAcquisition() :booleen
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure abstraite ArreteAcquisition() :boolÃ©en
```

**Nom :** Parametre

```wlanguage
      procÃ©dure abstraite Parametre(params)
```

**Nom :** Calcule

```wlanguage
      procÃ©dure abstraite Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
```

**Nom :** id

```wlanguage
         procÃ©dure id() : entier
          renvoyer :id
```


```wlanguage
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
```


---

# IThreadRunner.wdc

**Nom :** IThreadRunner

```wlanguage
      IThreadRunner est une Classe
      	implemente IAcquisitionProc
      	id est un entier = 0
      	callbackThread est une procÃ©dure
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** DemarreAcquisition

```wlanguage
      procÃ©dure abstraite DemarreAcquisition() :booleen
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure abstraite ArreteAcquisition() :boolÃ©en
```

**Nom :** Parametre

```wlanguage
      procÃ©dure abstraite Parametre(params)
```

**Nom :** id

```wlanguage
         procÃ©dure id() : entier
          renvoyer :id
```


```wlanguage
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
```


---

# ITreuilClient.wdc

**Nom :** ITreuilClient

```wlanguage
      ITreuilClient est une Classe
      	herite IClientSocket
      	type est EnumTreuilType
      	traitement est une ISignalTraitement dynamique
      	dessine est un ISignalDessine dynamique
      	enregistre est ISignalEnregistrement dynamique
      	acquisition est un IAcquisitionSignal dynamique
      	
      	Param_Interne_Treuils est Str_Param_Interne_Treuils
      	fileBufferEmet est une File de Str_DATA_Emet_t
      	
      privÃ©
      	bufferLecture est un buffer
      fin
      
      Str_Param_Interne_Treuils est une Structure
      	mTreuilVersion				est une chaÃ®ne
      	mTreuilVersion_DRV_ACQ		est une chaÃ®ne
      	mTreuilVersion_DRV_Arduino	est une chaÃ®ne
      	mTreuilVersion_DRV_BP		est une chaÃ®ne
      	mTreuilVersion_DRV_Capture	est une chaÃ®ne
      	mTreuilVersion_DRV_COD		est une chaÃ®ne
      	mTreuilVersion_DRV_Genere	est une chaÃ®ne
      	mTreuilVersion_DRV_HeureABS	est une chaÃ®ne
      	mTreuilVersion_DRV_Output	est une chaÃ®ne
      	mTreuilVersion_DRV_PWM		est une chaÃ®ne
      	mTreuilIP					est une chaÃ®ne
      	mTreuilType					est une chaÃ®ne
      	mTreuilFreq					est un entier
      	mTreuilProfondeur			est un rÃ©el
      	mTreuilSens					est une chaÃ®ne
      	mTreuilVitesseUp			est un entier
      	mTreuilVitesseDown			est un entier
      	mTreuilMesure				est un entier
      	Energie						est un entier
      	Gain						est un entier
      	VitesseBP					est un entier
      	ValT0						est un rÃ©el
      	numeroTreuil				est entier
      	Vitesse_cm_s				est entier
      	Decalage_Position_cm		est entier
      FIN
      
      Str_DATA_Emet_t est une Structure
      	Index		est entier sur 4
      	POS_EMET	est un entier sur 4
      FIN
      
      
      EnumTreuilType est une Ã©numÃ©ration
      	eEmetteur
      	eRecepteur
      	eNonIdentifie
      fin
      
      CONSTANTE
      	CST_SOCKMSG_SEP					= "_"
      	Size_MSG_Socket_RX				= 21
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_YYY)
      	SOCKMSG_PING					= "PING0000"
      	
      	SOCKMSG_REBOOT					= "RSTR0002"
      	//	SOCKMSG_QUIT				= "DECO0000"	
      	//	SOCKMSG_KILL				= "DECO0003"
      	//	SOCKMSG_KILL_SCRIPT			= "KLSH0000"
      	
      	SOCKMSG_TYPE_CFG_EMETTEUR		= "TYPE0001"
      	SOCKMSG_TYPE_CFG_RECEPTEUR		= "TYPE0002"
      	SOCKMSG_TYPE_CFG_NONDEF			= "TYPE0003"
      	SOCKMSG_TYPE_DEMANDE			= "TYPE0004"
      	SOCKREP_TYPE_TREUIL_EMETEUR		= "JESUISEMETTEUR"
      	SOCKREP_TYPE_TREUIL_RECEPTEUR	= "RECEPTEUR"
      	SOCKREP_TYPE_TREUIL_NDEF		= "NonDÃ©fini"
      	
      	SOCKMSG_MOTEUR_START			= "STRT0000"
      	SOCKMSG_MOTEUR_SET_STOPDIST		= "STRT0001"
      	SOCKMSG_MOTEUR_STOP				= "STOP0000"
      	
      	SOCKMSG_ACQUISITION				= "ACQU&&&&"
      	
      	SOCKMSG_CHANGE_SENS				= "CHSN&&&&"
      	SOCKMSG_IDID					= "IDID&&&&"
      	
      	SOCKMSG_MESURE_DEMARRE			= "DEMM0000"
      	SOCKMSG_MESURE_ARRETE			= "ARRT0000"		
      	
      	SOCKMSG_NUMERO_TREUIL			= "QNUM0000"
      	
      	SOCKMSG_QUESTIONNEAVS			= "QAVS0000"
      	
      	SOCKMSG_POSITION_ENCOURS		= "QPOS0000"
      	
      	SOCKMSG_VITESSE_ENCOURS			= "QVIT0000"
      	
      	SOCKMSG_RAZTREUIL				= "RAZT0000"
      	
      	SOCKMSG_SENS_UP					= "SENS0000"
      	SOCKMSG_SENS_DOWN				= "SENS0001"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_ENERGIE+<xxxx>) avec xxxx = 0000 Ã  0084 en V
      	SOCKMSG_FIXE_ENERGIE			= "VPRO"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GOTO+<xxxx>) avec xx = 0000 Ã  9999 en cm
      	SOCKMSG_DEPLACE_SONDE_VERS		= "GOTO"	
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_FREQ+<xxxx>) avec xxxx = 0000 Ã  0020 en Hz
      	SOCKMSG_FIXE_FREQ				= "CLAC"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GAIN+<xxx>) avec xxx = 001 Ã  016
      	SOCKMSG_FIXE_GAIN				= "COMM_"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_SENS_YYY)
      	// SocketEcrit(<SocketCible>,SOCKMSG_PWM_YYY+<xxxx>) avec xxxx = 0001 Ã  0020
      	SOCKMSG_PWM_UP					= "PWMU"
      	SOCKMSG_PWM_DWN					= "PWMD"
      	
      	// usage
      	// 0 : temporel
      	// 1 : spatial
      	// SocketEcrit(<SocketCible>,SOCKMSG_MODE+<xxxx>) avec xxxx = 0000 Ã  0001
      	SOCKMSG_MODE_TEMPOREL			= "MODE0000"
      	SOCKMSG_MODE_SPATIAL			= "MODE0001"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 Ã  4000
      	SOCKMSG_DEPLACEMENT_UP			= "DEPU"
      	SOCKMSG_DEPLACEMENT_DOWN		= "DEPD"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 Ã  4000
      	SOCKMSG_FIXE_POS_TREUIL			= "FPTR"
      	SOCKMSG_FIXE_POS_SYNCH			= "FPSY"
      	
      	CST_STOPFROM_ARRIVEAFTERGOTO	= 0
      	CST_STOPFROM_FINCALIB			= 1
      	CST_STOPFROM_PWMNULLE			= 2
      	CST_STOPFROM_CONFIGSENS			= 3
      	CST_STOPFROM_ERRSENS			= 4
      	CST_STOPFROM_BP					= 5
      	CST_STOPFROM_CLOSEPWM			= 6
      	CST_STOPFROM_AU					= 7
      	CST_STOPFROM_ERRCALIB			= 8
      	CST_STOPFROM_ERRGOTO			= 9
      	CST_STOPFROM_CMDSOCKET			= 10
      	CST_STOPFROM_BLOCAGEMOTEUR		= 11
      FIN	
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE EcouteSocket()
      
      :bufferLecture += :Lit()
      si :bufferLecture<>"" ALORS
      	si _synchroniseTrameDansBuffer() ALORS
      		ParseDonnees(:bufferLecture)
      		//Trace(:bufferLecture)	
      		:bufferLecture=""
      	FIN
      FIN
```

**Nom :** _synchroniseTrameDansBuffer

```wlanguage
      procÃ©dure privÃ©e _synchroniseTrameDansBuffer() : boolÃ©en
      
      pour i=1 _a_ bufferLecture..Taille
      //	si :bufferLecture..Taille>=3 _et_ :bufferLecture[1 a 3]<>"DEB" ALORS
      //		:bufferLecture = :bufferLecture[2 Ã€ ]
      //	sinon
      //		SI :bufferLecture..Taille>=CST_TAILLE_STRUCT ALORS
      //			SI :bufferLecture[CST_TAILLE_STRUCT-3 Ã€ CST_TAILLE_STRUCT-1] = "FIN" ALORS
      //				Logger.Verbose("buffer synchro")
      //				RENVOYER Vrai
      //			SINON
      //				LoggerErreur.EcritLigne("buffer non synchro pas de fin trouvÃ©, reset buffer")
      //				:bufferLecture = :bufferLecture[CST_TAILLE_STRUCT A]
      //				renvoyer faux
      //			FIN
      //		FIN
      //		sortir
      //	FIN
      fin
      
      renvoyer vrai
```

**Nom :** ParseDonnees

```wlanguage
      procÃ©dure abstraite ParseDonnees(buf est un buffer)
      
```


---

# Logger.wdc

**Nom :** Logger

```wlanguage
      Logger est une Classe
      	GLOBALE
      		niveau est NiveauEnum = NiveauEnum.ELog
      		affTrace est un boolÃ©en = faux
      	globale PRIVÃ‰E
      		nomFichier est une chaine
      		handleFic est un entier=-1
      		buffer est un buffer
      		id est entier
      		mutex est une chaÃ®ne
      		messageFile est une file de chaine
      
      	
      	constante BufferMax = 1 000 000
      fin
      
      NiveauEnum est une Ã©numÃ©ration
      	EErreur = 0
      	ELog = 1
      	EDebug
      	ETrace
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** EcritLigne

```wlanguage
      procÃ©dure globale EcritLigne(msg est chaine, niv est NiveauEnum, flush est un boolÃ©en=faux, enfileMsg est un boolÃ©en = faux) : entier
      
      si niv > niveau ALORS
      	renvoyer 0
      FIN
      
      si affTrace ALORS
      	wl.trace(HeureVersChaÃ®ne(Maintenant,"HH:MM:SS:CC") + " : " + V(niv) + ThreadCourant()+"=> " + msg)
      FIN   
      
      oc est un entier = -1
      dbMsg est chaÃ®ne = V(niv) + ThreadCourant()+"=> " + msg
      dbMsg = HeureVersChaÃ®ne(Maintenant,"HH:MM:SS:CC") + " : " + dbMsg + rc
      si enfileMsg alors enfile(messageFile, dbMsg)
      
      m est MutexHelper(mutex)
      SI handleFic<=0 ALORS
      	RENVOYER 0
      FIN
      buffer += dbMsg
      si buffer..Taille>=bufferMax ou flush ALORS
      	oc = fEcrit(handleFic,&buffer,buffer..Taille)
      	si oc>0 ALORS
      		buffer=""
      	FIN
      FIN
      
      renvoyer oc
```

**Nom :** Init

```wlanguage
      procÃ©dure publique globale Init()
      
      nomFichier = "trace.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCrÃ©ationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrÃ©e(mutex,Faux,partageAucun)
```

**Nom :** Quit

```wlanguage
      procÃ©dure publique globale Quit()
      
      SI buffer<>"" ALORS
      	SI EcritLigne("",niveau, vrai)=-1 ALORS
      		Trace("Erreur d'Ã©criture dans fichier "+nomFichier)
      	FIN
      FIN
      fferme(handleFic)
      handleFic=-1
      MutexDÃ©truit(mutex)
      mutex=""
      SupprimeTout(messageFile)
```

**Nom :** Log

```wlanguage
      procÃ©dure publique globale Log(msg est chaine)
      
      renvoyer EcritLigne(msg,NiveauEnum.ELog)
```

**Nom :** Debug

```wlanguage
      procÃ©dure publique globale Debug(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EDebug)
```

**Nom :** Verbose

```wlanguage
      procÃ©dure publique globale Verbose(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.ETrace)
```

**Nom :** V

```wlanguage
      procÃ©dure privÃ©e globale V(niv est NiveauEnum)
      
      selon niv
      	CAS NiveauEnum.EErreur
      		RENVOYER "[ERR!!]"
      	CAS NiveauEnum.ELog
      		renvoyer "[LOG--]"
      	CAS NiveauEnum.EDebug
      		RENVOYER "[DEBUG]"
      	CAS NiveauEnum.ETrace
      		RENVOYER "[TRACE]"
      FIN
      renvoyer ""
```

**Nom :** Erreur

```wlanguage
      procÃ©dure publique globale Erreur(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EErreur)
```

**Nom :** Message

```wlanguage
      procÃ©dure publique globale Message(msg est une chaine)
      
      EcritLigne(msg,ELog,Faux,Vrai)
```


---

# LoggerErreur.wdc

**Nom :** LoggerErreur

```wlanguage
      LoggerErreur est une Classe
      	globale
      		affTrace est un boolÃ©en = faux
      	globale privee
      		nomFichier est une chaine
      		handleFic est un entier=-1
      		buffer est un buffer
      		id est entier
      		mutex est une chaÃ®ne
      		messageFile est une file de chaine
      	
      	constante BufferMax = 1 000 000
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** EcritLigne

```wlanguage
      procÃ©dure globale EcritLigne(msg est chaine, flush est un boolÃ©en=faux) : entier
      
      SI affTrace _et_ logger.affTrace=faux ALORS
      	WL.Trace(HeureVersChaÃ®ne(Maintenant,"HH:MM:SS:CC") + " : " + "[ERREUR] " + ThreadCourant()+"=> " + msg)
      FIN
      
      logger.Erreur(msg)
      
      oc est un entier = -1
      dbMsg est chaÃ®ne = "[ERREUR] " + ThreadCourant()+"=> " + msg
      dbMsg = HeureVersChaÃ®ne(Maintenant,"HH:MM:SS:CC") + " : " + dbMsg + rc
      enfile(messageFile,dbMsg)
      
      m est MutexHelper(mutex)
      SI handleFic<=0 ALORS
      	RENVOYER 0
      FIN
      
      buffer += dbMsg
      si buffer..Taille>=bufferMax ou flush ALORS
      	oc = fEcrit(handleFic,&buffer,buffer..Taille)
      	si oc>0 ALORS
      		buffer=""
      	FIN
      FIN
      
      renvoyer oc
```

**Nom :** Init

```wlanguage
      procÃ©dure publique globale Init()
      
      nomFichier = "erreur.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCrÃ©ationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrÃ©e(mutex,Faux,partageAucun)
```

**Nom :** Quit

```wlanguage
      procÃ©dure publique globale Quit()
      
      SI buffer<>"" ALORS
      	SI EcritLigne("", vrai)=-1 ALORS
      		Trace("Erreur d'Ã©criture dans fichier "+nomFichier)
      	FIN
      FIN
      fFerme(handleFic)
      handleFic=-1
      MutexDÃ©truit(mutex)
      mutex=""
      SupprimeTout(messageFile)
```


---

# main.wdw

**Nom :** main
**Nom :** main
**Nom :** Client_acquisition
**Nom :** Colonne1
**Nom :** Colonne2
**Nom :** Colonne3

```wlanguage
         
         EnvoieSocketAuServerSurSelection(colonne1,"ACQ_"+ComplÃ¨te(Hasard(1,999),8," "),Client_acquisition)
```

**Nom :** Clients
**Nom :** Colonne1
**Nom :** Colonne2
**Nom :** Colonne3

```wlanguage
         
         EnvoieSocketAuServerSurSelection(Colonne1,"COMM"+ComplÃ¨te(Hasard(1,999),8," "),Clients)
         
```

**Nom :** Clients_socket
**Nom :** Colonne1
**Nom :** Colonne2
**Nom :** Colonne3
**Nom :** Colonne4
**Nom :** Colonne5

```wlanguage
         
         //EnvoieSocketAuClientSurSelection(Colonne1, "id__"+ComplÃ¨te(Hasard(1,999),8," "))
         EnvoieSocketAuClientSurSelection(Colonne1, ComplÃ¨te(Saisie1,8," "))
         
```

**Nom :** arrÃªte_acquisition

```wlanguage
         FermeSocket100("Acquisition",Client_acquisition)
```

**Nom :** demarre_server

```wlanguage
         
         server.Demarre()
```

**Nom :** arrete_server

```wlanguage
         
         server.Arrete()
         ExÃ©cuteTraitement(arrÃªte_acquisition,trtClic)
```

**Nom :** on1

```wlanguage
         
         //t est un tableau de IClientSocket dynamique <- server.recepteur.RecupereClients()
         //POUR TOUT cl in t
         //	tr est un TreuilDecorateur dynamique <- cl
         //	SI tr <> Null
         //		tr.ParametreNomFichier("./test.bin")
         //		tr.ParametreChampImage("300;300")
         //		tr.ParametreTraitement("T0=12;offset=100;suppressionMoyenne=1;lissage=1")
         //		tr.DemarreAcquisition()
         //	FIN
         //FIN
         server.DemarreAcquisition("data.bin"+tab+"300;300;main.graphe1;main.graphe2"+tab+"T0=12;offset=100;suppressionMoyenne=1;lissage=1")
         
         
         
         buf.sEntete="DEB"
         buf.SizeStruct=AcquisitionSignalClient.CST_TAILLE_STRUCT
         buf.Index=0
         buf.POS_EMET=0
         buf.POS_RECEPT=0
         buf.Buffer_size=AcquisitionSignalClient.Size_MSG_Socket_RX_ACQU*2
         buf.sEnd="FIN"
         arreteTimerSysAcq=faux
         TimerSys(()=>{
         	si arreteTimerSysAcq= vrai ALORS
         		FinTimerSys()
         		retour
         	FIN
         	POUR i=1 _a_ 10
         		buf.POS_EMET+=1
         		buf.POS_RECEPT+=1
         		buf.Index+=1
         		POUR j=1 _a_ AcquisitionSignalClient.Size_MSG_Socket_RX_ACQU
         			buf.tabBuffer[j] = PartieEntiÃ¨re((Sinus(20*j/AcquisitionSignalClient.Size_MSG_Socket_RX_ACQU * 180/3.14) + 0.5/10*i)*1000)
         		FIN
         		b est buffer sur AcquisitionSignalClient.CST_TAILLE_STRUCT
         		Transfert(&b,&buf,AcquisitionSignalClient.CST_TAILLE_STRUCT)
         		BufAEnvoyer+=b
         	FIN
         	pour i=1 _a_ Client_acquisition..Occurrence
         		t est chaine = Client_acquisition[i].Colonne1
         		Trace("->Envoi du buffer acquisition sur la socket acquisition = ", SocketEcrit(t,BufAEnvoyer))	
         	FIN
         	
         	BufAEnvoyer=""
         },50cs)
         
```

**Nom :** off

```wlanguage
         arreteTimerSysAcq = vrai
         server.ArreteAcquisition()
```

**Nom :** Bouton1

```wlanguage
         grSupprimeSÃ©rie(Graphe1,1,grDonnÃ©es)
         y est reel
         	rd est un entier = Hasard(5,30)
         pour x=1 _a_ 100
         	y = Sinus(2*180*x/rd)
         	grNuageAjouteDonnÃ©eXY(Graphe1,1,x,y)
         FIN
         grDessine(Graphe1)
```

**Nom :** Image1
**Nom :** Bouton2

```wlanguage
         grCrÃ©e(":nomGrapheVirtuel",grCourbe)
         //grDestinationImage(:nomGrapheVirtuel, :champimage)
         grDestinationChamp(":nomGrapheVirtuel",{"main.Graphe1", indChamp})
```


```wlanguage
         
         grSupprimeSÃ©rie(":nomGrapheVirtuel",1)
         x,y sont des rÃ©els
         rand est entier =random(3,20)
         POUR i=1 _a_ 100
         	y=Sinus(2*180*i/rand)
         	grAjouteDonnÃ©e(":nomGrapheVirtuel", 1, y)
         FIN
         
         grDessine(":nomGrapheVirtuel")
```

**Nom :** Graphe2
**Nom :** dÃ©marre_client_inconnu

```wlanguage
         
         tb est un tableau dynamique = server.RecupereClients()
         indice est entier = tb..Occurrence
         nomSock est chaine = "Client?_"+indice
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portTreuil),"inconnu", Clients)
```

**Nom :** Graphe1
**Nom :** DOTNET_Chart
**Nom :** Bouton4

```wlanguage
         chartt est un WD_DualAxisChart dynamique = new WD_DualAxisChart
         chartt.Init(main.DOTNET_Chart)
         chartt.ConfigurerGraphique("test","y","unitY",18000/10,20000/10,true)
         chartt.ConfigurerSerie1("serie1",0,10, "blue")
         chartt.ConfigurerSerie2("serie2",0,1500, "red")
         chartt.AjouterPointSerie1(20000/10, 0)
         chartt.AjouterPointSerie2(20000/10, 0)
         
         y, ang, xsin1, xsin2 sont des reels
         ech est entier = 20000/10
         pour i=1 _a_ ech
         	ang = i * 3.14 * 2 / (ech*2) * 180/3.14
         	xsin1 = 10 * sinus(ang)
         	xsin2 = 1500 *(1- Sinus(ang))
         	y = ech-i
         	chartt.AjouterPointSerie1(y, xsin1)
         	chartt.AjouterPointSerie2(y, xsin2)
         	si modulo(i,10)=0 alors
         		MultitÃ¢che(-10)
         	FIN
         FIN
         
```

**Nom :** emetteur

```wlanguage
         //SocketEcrit(Clients[Clients].Colonne1, "TYPE_&&JESUISEMETTEUR")
         EnvoieSocketAuServerSurSelection(Clients[Clients].Colonne1,"TYPE_&&JESUISEMETTEUR",Clients)
```

**Nom :** recepteur

```wlanguage
         //SocketEcrit(Clients[Clients].Colonne1, "TYPE_&JESUISRECEPTEUR")
         EnvoieSocketAuServerSurSelection(Clients[Clients].Colonne1,"TYPE_&JESUISRECEPTEUR",Clients)
```

**Nom :** Saisie1
**Nom :** send

```wlanguage
         EnvoieSocketAuServerSurSelection(Clients_socket[Clients_socket].Colonne1,Saisie1,Clients)
```

**Nom :** dÃ©marre_acquisition

```wlanguage
         
         nomSock est chaine = "Acquisition_"+Client_acquisition..Occurrence
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portAcquisition),"acquisition",Client_acquisition)
```

**Nom :** dÃ©marre_client_app

```wlanguage
         
         tb est un tableau dynamique = server.RecupereClients()
         indice est entier = tb..Occurrence
         nomSock est chaine = "App?_"+indice
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portApplication),"application", Clients)
```

**Nom :** position1

```wlanguage
         
         Saisie1 = "QPOS0000"
         EnvoieSocketAuxClients()
```

**Nom :** status

```wlanguage
         
         Saisie1 = "QAVS0000"
         EnvoieSocketAuxClients()
```

**Nom :** Arret_mes

```wlanguage
         
         Saisie1 = "ARRT0000"
         EnvoieSocketAuxClients()
         
         server.ArreteAcquisition()
```

**Nom :** Energie

```wlanguage
         Saisie1 = "VPRO0024"
         EnvoieSocketAuxClients()
         
```

**Nom :** gain

```wlanguage
         
         Saisie1 = "COMM_001"
         EnvoieSocketAuxClients()
```

**Nom :** Dem_mesure

```wlanguage
         
         server.DemarreAcquisition("data1.bin"+tab+"data2.bin"+TAB+"data3.bin"...
         +RC+"300;300;main.graphe1"+tab+"300;300;main.graphe2"+TAB+"300;300;main.graphe3"...
         +RC+"T0=12;offset=100;suppressionMoyenne=1;lissage=1")
         
         Saisie1 = "DEMM0000"
         EnvoieSocketAuxClients()
```

**Nom :** Clac

```wlanguage
         
         Saisie1 = "CLAC0010"
         EnvoieSocketAuxClients()
```

**Nom :** VitesseUP

```wlanguage
         
         Saisie1 = "PWMU0010"
         EnvoieSocketAuxClients()
```

**Nom :** VitDOWN

```wlanguage
         Saisie1 = "PWMD0010"
         EnvoieSocketAuxClients()
         
```

**Nom :** Mode

```wlanguage
         Saisie1 = "MODE0000"
         EnvoieSocketAuxClients()
         
```

**Nom :** DÃ©co

```wlanguage
         Saisie1="DECO0000"
         EnvoieSocketAuxClients()
         
```

**Nom :** GOTO0400

```wlanguage
         
         Saisie1="GOTO0400"
         EnvoieSocketAuxClients()
```

**Nom :** start_mot

```wlanguage
         
         Saisie1= "STRT0001"
         EnvoieSocketAuxClients()
```

**Nom :** Arret_mot

```wlanguage
         saisie1="STOP0000"
         EnvoieSocketAuxClients()
         
```

**Nom :** GOTO401

```wlanguage
         
         Saisie1="GOTO0000"
         EnvoieSocketAuxClients()
```

**Nom :** tout1

```wlanguage
         
         ExÃ©cuteTraitement(Energie,trtClic)
         ExÃ©cuteTraitement(gain,trtClic)
         ExÃ©cuteTraitement(Clac,trtClic)
         ExÃ©cuteTraitement(VitDOWN,trtClic)
         ExÃ©cuteTraitement(VitesseUP,trtClic)
         ExÃ©cuteTraitement(GOTO0400,trtClic)
```

**Nom :** Trace1

```wlanguage
         
         LoggerErreur.affTrace = Trace1=1 ? vrai sinon faux
         Logger.affTrace = Trace1=1 ? vrai sinon faux
```

**Nom :** Affichage

```wlanguage
         
```

**Nom :** raz_treuil

```wlanguage
         Saisie1 = "FPTR0000"
         EnvoieSocketAuxClients()
         Saisie1 = "FPSY0000"
         EnvoieSocketAuxClients()  
         Saisie1 = "FPSY0000"
         EnvoieSocketAuxClients()
         Saisie1 = "RAZT0000"
         EnvoieSocketAuxClients()
```

**Nom :** Type

```wlanguage
         Saisie1="TYPE0004"
         EnvoieSocketAuxClients()
         
```

**Nom :** DBG

```wlanguage
         
         selon dbg
         	CAS 1
         		Logger.niveau=Logger.EErreur
         	CAS 2
         		Logger.niveau=Logger.ELog
         	CAS 3
         		Logger.niveau=Logger.EDebug
         	CAS 4
         		Logger.niveau=Logger.ETrace
         		
         	AUTRE CAS
         		
         FIN
```

**Nom :** Table1
**Nom :** Socket
**Nom :** IP
**Nom :** Position
**Nom :** FreqClac
**Nom :** Gain
**Nom :** Energie
**Nom :** Vitesse
**Nom :** Graphe3
**Nom :** _Menu

```wlanguage
      PROCEDURE MaFenÃªtre()
      
      LoggerErreur.Init()
      LoggerErreur.affTrace = Vrai
      
      Logger.Init()
      Logger.affTrace = Vrai
      Logger.niveau = Logger.ETrace
      
      FabriqueClient.AdresseIP=NetAdresseIP()
      server est ServeurConnexion dynamique <- ServeurConnexion.MonInstance
      
      
      BufAEnvoyer est buffer
      buf est une iAcquisitionSignal.Str_DATA_Acquisition
      arreteTimerSysAcq est un boolÃ©en=faux
      
      
      GLOBAL
      	gclChart			est un objet  dynamique
      	gclChartArea		est un objet  dynamique
      	gclSeriesDensity	est un objet  dynamique
      	gclSeriesPressure	est un objet  dynamique
   -

```wlanguage
      server.Init()
      
      //TimerSys(()=>{
      //	TableSupprimeTout(Clients_socket)
      //	TableSupprimeTout(Table1)
      //	t est tableau dynamique = server.RecupereClients()
      //	POUR TOUT c DANS t
      //		si c=null alors continue
      //		TableAjouteLigne(Clients_socket,c.canalSocket,c.ip,c.nomThread,c.dernierMsgEnvoye,c.dernierMsgRecu[1 a 32])
      //		cc est TreuilClient dynamique <- c
      //		si cc=null alors continue
      //		TableAjouteLigne(Table1,cc.canalSocket, cc.ip, cc.Param_Interne_Treuils.mTreuilProfondeur, cc.Param_Interne_Treuils.mTreuilFreq, cc.Param_Interne_Treuils.Gain,
      //		cc.Param_Interne_Treuils.Energie, cc.Param_Interne_Treuils.Vitesse_cm_s)
      //	FIN
      //	TableSelectMoins(Clients_socket)
      //},100cs)
      //
      //TimerSys(()=>{	
      //SI Affichage=1 ALORS
      //	cs est tableau de IClientSocket dynamique = server.RecupereClients()
      //	POUR TOUT client dans cs
      //		tr est TreuilClient dynamique <- client
      //		SI tr<>Null _ET_ tr.type=ITreuilClient.eRecepteur ALORS
      //			tr.dessine.Affichage()
      //		fin
      //	FIN
      //FIN
      //
      //},10cs)
      
      TimerMajTabClients()
      TimerAfficheSignal()
      
```


```wlanguage
      
      server.Quit()
      
      LoggerErreur.Quit()
      Logger.Quit()
```

**Nom :** ConnecteSockets

```wlanguage
      procÃ©dure ConnecteSockets(nomSock_ est chaine, port est entier, commentaire est chaine, champTable_ est champ)
      
      nomSock est chaine = nomSock_
      champTable est un champ <- champTable_
      SI SocketConnecte(nomSock, port, FabriqueClient.AdresseIP) ALORS
      	SocketChangeModeTransmission(nomSock, SocketSansMarqueurFin)
      	TableAjoute(champTable,nomSock+tab+commentaire+TAB+"")
      	SocketEcrit(nomSock,commentaire)
      	SI ErreurDÃ©tectÃ©e ALORS
      		TableSupprime(champTable,TableCherche(champTable.Colonne1,nomSock))
      		SocketFerme(nomSock)
      		retour
      	FIN
      	TimerSys(()=>{
      		lit()
      	},10cs)
      FIN
      
      procÃ©dure interne lit()
      	quand Exception 
      		FinTimerSys()
      		retour
      	FIN
      	msg est chaine = SocketLit(nomSock,Faux,10cs,100)
      	SI ErreurDÃ©tectÃ©e ALORS
      		TableSupprime(champTable,TableCherche(champTable.Colonne1,nomSock))
      		SocketFerme(nomSock)
      		FinTimerSys()
      	FIN
      	SI msg<>"" ALORS
      		ind est entier = TableCherche(champTable.Colonne1,nomSock)
      		SI ind>0 ALORS
      			champTable[ind].Colonne3 = msg
      		FIN
      	FIN
      fin
```

**Nom :** EnvoieSocketAuClientSurSelection

```wlanguage
      procÃ©dure EnvoieSocketAuClientSurSelection(nomSock est chaine, msg est chaÃ®ne)
      
      t est tableau IClientSocket dynamique = server.RecupereClients()
      POUR TOUT cl in t
      	SI cl.canalSocket = nomSock ALORS
      		//msg est chaine = "ident"+Hasard(1,1000)
      		cl.Ecrit(msg)
      		SORTIR
      	FIN
      FIN
```

**Nom :** EnvoieSocketAuServerSurSelection

```wlanguage
      procÃ©dure EnvoieSocketAuServerSurSelection(nomSock est chaine, msg est chaine, table_ est champ)
      
      matable est champ <- table_
      SocketEcrit(nomSock,msg)
      
      ind est entier = TableCherche(matable.Colonne1,nomSock)
      SI ind>0 ALORS
      	matable[ind].Colonne2 = msg
      FIN
```

**Nom :** FermeSocket100

```wlanguage
      procÃ©dure FermeSocket100(sock,table_ est champ)
      monTable est champ <- table_
      POUR i=0 _A_ 100
      	n est chaine = sock+"_"+i
      	SI SocketExiste(n) ALORS
      		SocketFerme(n)
      		ind est entier = TableCherche(monTable.Colonne1,n)
      		si ind>0 ALORS
      			TableSupprime(monTable,ind)
      		FIN
      	FIN
      FIN
```

**Nom :** EnvoieSocketAuxClients

```wlanguage
      procÃ©dure EnvoieSocketAuxClients()
      
      indexTab est un tableau d'entiers
      pour i=1 _a_ Clients_socket..Occurrence
      	si chaineOccurrence(Clients_socket[i].Colonne3,"thtreuiler",SansCasse) ALORS
      		TableauAjoute(indexTab,i)	
      	FIN
      FIN
      
      pour i=1 _a_ indexTab..Occurrence
      	EnvoieSocketAuClientSurSelection(Clients_socket[indexTab[i]].Colonne1, ComplÃ¨te(Saisie1,8," "))	
      FIN
```

**Nom :** TimerMajTabClients

```wlanguage
      // ProcÃ©dure automatique :
      // La procÃ©dure est exÃ©cutÃ©e manuellement, lors d'un appel dans le code
      // Elle sera rÃ©pÃ©tÃ©e en boucle, en attendant 1 seconde entre chaque appel
      // Un nouveau timer est crÃ©Ã© pour chaque appel Ã  la procÃ©dure
      // 
      procÃ©dure TimerMajTabClients()
      TableSupprimeTout(Clients_socket)
      TableSupprimeTout(Table1)
      t est tableau dynamique = server.RecupereClients()
      POUR TOUT c DANS t
      	SI c=Null ALORS CONTINUE
      	TableAjouteLigne(Clients_socket,c.canalSocket,c.ip,c.nomThread,c.dernierMsgEnvoye,c.dernierMsgRecu[1 Ã€ 32])
      	cc est TreuilClient dynamique <- c
      	SI cc=Null ALORS CONTINUE
      	TableAjouteLigne(Table1,cc.canalSocket, cc.ip, cc.Param_Interne_Treuils.mTreuilProfondeur, cc.Param_Interne_Treuils.mTreuilFreq, cc.Param_Interne_Treuils.Gain,
      	cc.Param_Interne_Treuils.Energie, cc.Param_Interne_Treuils.Vitesse_cm_s)
      FIN
      TableSelectMoins(Clients_socket)
```

**Nom :** TimerAfficheSignal

```wlanguage
      // ProcÃ©dure automatique :
      // La procÃ©dure est exÃ©cutÃ©e manuellement, lors d'un appel dans le code
      // Elle sera rÃ©pÃ©tÃ©e en boucle, en attendant 1 seconde entre chaque appel
      // Un nouveau timer est crÃ©Ã© pour chaque appel Ã  la procÃ©dure
      // 
      
      procÃ©dure TimerAfficheSignal()
      SI Affichage=1 ALORS
      	cs est tableau de IClientSocket dynamique = server.RecupereClients()
      	POUR TOUT client DANS cs
      		tr est TreuilClient dynamique <- client
      		SI tr<>Null _ET_ tr.type=ITreuilClient.eRecepteur ALORS
      			tr.dessine.Affichage()
      		FIN
      	FIN
      fin
```


---

# MessageParser.wdc

**Nom :** MessageParser

```wlanguage
      MessageParser est une Classe
      	herite IParser
      privee
      	trTmp est ITreuilClient dynamique
      	sContenu_Message est chaine
      	sVariable_Message est une chaÃ®ne
      	sVariable_AR est une chaÃ®ne
      	sContenu_AR est une chaÃ®ne
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** ParseDonnees

```wlanguage
      // RedÃ©finition de la mÃ©thode IParser.ParseDonnees
      PROCÃ‰DURE  ParseDonnees(treuil ITreuilClient dynamique, _buffer est un Buffer)
      
      :trTmp <-treuil
      
      QUAND EXCEPTION DANS
      	nbMsg est un entier	= Taille(_buffer)/ITreuilClient::Size_MSG_Socket_RX
      
      	POUR i = 1 Ã€ nbMsg
      		
      		sVariable_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*ITreuilClient::Size_MSG_Socket_RX)) Ã€ (i*ITreuilClient::Size_MSG_Socket_RX)]],1,ITreuilClient::CST_SOCKMSG_SEP)
      		sContenu_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*ITreuilClient::Size_MSG_Socket_RX)) Ã€ (i*ITreuilClient::Size_MSG_Socket_RX)]],2,ITreuilClient::CST_SOCKMSG_SEP)	
      		
      		SELON sVariable_Message
      			CAS "STATUS","TEMP"
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      				
      			CAS "DECO"
      				gestion_deconnexion()
      				
      			CAS "ERR"
      				gestion_erreur()			
      				
      			CAS "TYPE"
      				gestion_type_treuil()
      				
      			CAS "RAVS"
      				gestion_ravs()
      				
      			CAS "RPAR"
      				gestion_rpar()		
      				
      			CAS "MSG"
      				gestion_msg()
      				
      			CAS "EVT"
      				sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],1,"=")
      				sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],2,"=")
      				SELON sVariable_AR
      					CAS "STOP"
      						gestion_event_stop()
      						
      					CAS "BTNP"
      						gestion_event_btnp()	
      						
      					CAS "BTNR"
      						gestion_event_btnr()
      						
      					AUTRE CAS
      						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				FIN
      				
      			CAS "COMM"
      				SI (Taille(sContenu_Message) = 16) ALORS
      					Logger.Log("Client["+trTmp:canalSocket+","+trTmp:ip+"]+_COMM_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      				SINON
      					Logger.Log("Client["+trTmp:canalSocket+","+trTmp:ip+"]+_COMM_:_"+"Erreur RÃ©ception Contenu_Message")
      				FIN
      				
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Variable :"+sVariable_Message)
      				LoggerErreur.EcritLigne("Contenu :"+sContenu_Message)
      				LoggerErreur.EcritLigne(ChaÃ®neConstruit("[%1%][%2]_:_Erreur RÃ©ception Contenu_Message",trTmp:ip,trTmp:id))
      		FIN	
      	FIN
      	
      FAIRE
      	ExceptionActive()
      	LoggerErreur.EcritLigne("exception dans ParseDonnees")
      FIN
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode IParser.Parametre
      PROCÃ‰DURE  Parametre(params)
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode IParser.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      RENVOYER Vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode IParser.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      renvoyer vrai
```

**Nom :** M

```wlanguage
      procÃ©dure privee M(var)
      RENVOYER ChaÃ®neConstruit("%1%2%3",var,trTmp:canalSocket,trTmp:ip)
```

**Nom :** gestion_deconnexion

```wlanguage
      procÃ©dure privÃ©e gestion_deconnexion()
      
      SELON sContenu_Message					
      	CAS "exit.From.PC"
      		Logger.Message(M(sContenu_Message)+"Sortie de lâ€™application suite Ã  la demande provenant du PC Serveur")	
      	CAS "exit.From.PEGASE"
      		Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant de lâ€™application Cliente ÂµC")	
      	CAS "exit.From.REBOOT"
      		Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant d'un Reboot du ÂµC")	
      	AUTRE CAS
      		Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant de source inconnue")	
      FIN
```

**Nom :** gestion_erreur

```wlanguage
      procÃ©dure privÃ©e gestion_erreur()
      
      sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],1,"=")
      sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],2,"=")	
      SELON sVariable_AR
      	CAS "ARRT"
      		SI sContenu_AR = "PILE---WIFI" ALORS						
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] Erreur de transmission WIFI")
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] Erreur de transmission du buffer dâ€™acquisition par la socket, le treuil arrÃªte lâ€™acquisition pour Ã©viter un plantage")
      		FIN
      	CAS "IDENT"
      		SI sContenu_AR = "NoCOMP" ALORS
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"]+Communication 1-Wire ne voit aucun composant compatible")
      		FIN	
      		SI sContenu_AR = "COMPNonAdresse" ALORS
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"]+Communication 1-Wire ne reconnaÃ®t pas le composant")
      		FIN	
      	CAS "MESURE"
      		SI sContenu_AR = "SENS--COD" ALORS
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] Le moteur tourne dans le mauvais sens")
      		FIN	
      	CAS "VITESSE"
      		SI sContenu_AR = "NONSPECIF" ALORS
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] Pas de vitesse configurÃ©e")
      		FIN	
      	CAS "MOTEUR"
      		SI sContenu_AR = "BLOQ--MOT" ALORS							
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] Blocage moteur, aucune rotation dÃ©tectÃ©e")
      		FIN
      	CAS "GOTO"
      		SI sContenu_AR = "NON-ATTEINT" ALORS
      			LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] ArrÃªt moteur alors que la destination du GOTO nâ€™a pas Ã©tÃ© atteinte")
      		FIN
      	AUTRE CAS
      		LoggerErreur.EcritLigne("Client["+trTmp:canalSocket+","+trTmp:ip+"] var:_ "+sVariable_AR+"_:_"+sContenu_AR)
      FIN
```

**Nom :** gestion_type_treuil

```wlanguage
      procÃ©dure privÃ©e gestion_type_treuil()
      
      SI PAS Contient(sContenu_Message, "@") ALORS
      	
      	SI Contient(sContenu_Message, ITreuilClient::SOCKREP_TYPE_TREUIL_EMETEUR) ALORS
      		trTmp:type = ITreuilClient.eEmetteur
      		trTmp:Ecrit(ITreuilClient::SOCKMSG_TYPE_CFG_EMETTEUR)		
      		trTmp:Param_Interne_Treuils.mTreuilType = 0
      	SINON SI Contient(sContenu_Message, ITreuilClient::SOCKREP_TYPE_TREUIL_RECEPTEUR) ALORS
      		trTmp:type = ITreuilClient.eRecepteur
      		trTmp:Ecrit(ITreuilClient::SOCKMSG_TYPE_CFG_RECEPTEUR)
      		trTmp:Param_Interne_Treuils.mTreuilType = 1
      	SINON
      		trTmp:type = ITreuilClient.eNonIdentifie
      		trTmp:Ecrit(ITreuilClient::SOCKMSG_REBOOT)
      		trTmp:Param_Interne_Treuils.mTreuilType = 2
      	FIN
      FIN
```

**Nom :** gestion_rpar

```wlanguage
      procÃ©dure privÃ©e gestion_rpar()
      
      sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],1,"=")
      sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],2,"=")
      SELON sVariable_AR
      //CAS "ARRT"
      	//MAJ_AFF_TRAME_SOCKET(0,"Energie _:"+Gauche(sContenu_AR,2))				 											
      	CAS "VPRO"
      		trTmp:Param_Interne_Treuils.Energie=Val(Gauche(sContenu_AR,2))
      		
      	CAS "COMM"
      		trTmp:Param_Interne_Treuils.Gain=Val(Gauche(sContenu_AR,2))
      		
      	CAS "QNUM"
      		trTmp:Param_Interne_Treuils.numeroTreuil = Val(sContenu_AR)
      		
      	CAS "QPOS"
      		trTmp:Param_Interne_Treuils.mTreuilProfondeur=Val(sContenu_AR)/2
      		
      	CAS "QVIT"
      		trTmp:Param_Interne_Treuils.Vitesse_cm_s = Val(sContenu_AR)
      		
      	CAS "QDIF"
      		trTmp:Param_Interne_Treuils.Decalage_Position_cm = Val(sContenu_AR)/2// reception en coup codeur ~2coups /cm
      		
      	CAS "EMT"
      		t est ITreuilClient:Str_DATA_Emet_t
      		t.Index		= Val(ExtraitChaÃ®ne(sContenu_AR,1,"-"))
      		t.POS_EMET	= Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      		trTmp:Param_Interne_Treuils.mTreuilProfondeur = t.POS_EMET
      		Enfile(trTmp:fileBufferEmet,t)
      		
      	AUTRE CAS
      		//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      FIN	
```

**Nom :** gestion_event_stop

```wlanguage
      procÃ©dure privÃ©e gestion_event_stop()
      
      StopFROM est entier = Val(Droite(sContenu_AR,2))
      SELON StopFROM							
      	CAS ITreuilClient::CST_STOPFROM_ARRIVEAFTERGOTO
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : ARRIVED_AFTER_GOTO")
      	CAS	ITreuilClient::CST_STOPFROM_FINCALIB
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : FIN_CALIBRATION")
      	CAS	ITreuilClient::CST_STOPFROM_PWMNULLE
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : PWM_NULLE")
      	CAS ITreuilClient::CST_STOPFROM_CONFIGSENS
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : CONFIG_SENS")
      	CAS ITreuilClient::CST_STOPFROM_ERRSENS
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur SENS")
      	CAS ITreuilClient::CST_STOPFROM_BP
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Bouton Poussoir")
      	CAS ITreuilClient::CST_STOPFROM_CLOSEPWM
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Fin PWM")
      	CAS ITreuilClient::CST_STOPFROM_AU
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Arret d'Urgence")
      	CAS ITreuilClient::CST_STOPFROM_ERRCALIB
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur CALIBRATION")	
      	CAS ITreuilClient::CST_STOPFROM_ERRGOTO
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur GOTO")
      	CAS ITreuilClient::CST_STOPFROM_CMDSOCKET
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Depuis PC")
      	CAS ITreuilClient::CST_STOPFROM_BLOCAGEMOTEUR
      		Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Blocage Moteur")		
      	AUTRE CAS
      		Logger.Message(M("EVT_STOP_")+"Source de l'arrÃªt moteur inconnu")
      FIN
```

**Nom :** gestion_msg

```wlanguage
      procÃ©dure privee gestion_msg()
      
      sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],1,"=")
      sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],2,"=")
      //Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      SELON sVariable_AR
      	CAS "MESURE"					
      		SI sContenu_AR = "FIN--GOTO" ALORS
      			Logger.Message(M("MSG_MESURE_"+sContenu_AR)+"----==FIN GOTO MESURE==----")	
      		FIN
      		//cas "CALIB"
      		//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		
      	AUTRE CAS
      		//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      FIN
```

**Nom :** gestion_ravs

```wlanguage
      procÃ©dure privÃ©e gestion_ravs()
      
      sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],1,"=")
      sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (ITreuilClient::Size_MSG_Socket_RX-5)]],2,"=")
      //				SELON sVariable_AR								
      //					CAS "1"
      //						si Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      //							Param_Interne_Treuils.mTreuilIP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      //							Param_Interne_Treuils.mTreuilType=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      //							
      //							Param_Interne_Treuils.mTreuilVersion=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      //							GG_AVS.iVersionT1 = val(Param_Interne_Treuils.mTreuilVersion)*100
      //							Param_Interne_Treuils.mTreuilFreq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      //							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 1 ALORS
      //								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      //							SINON
      //								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      //							FIN							
      //							si Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 alors 
      //								Param_Interne_Treuils.mTreuilSens="MontÃ©e"
      //							sinon
      //								Param_Interne_Treuils.mTreuilSens="Descente"
      //							FIN
      //						sinon
      //							Param_Interne_Treuils.T2_IP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      //							Param_Interne_Treuils.T2_Type=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      //							
      //							Param_Interne_Treuils.T2_Version=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      //							GG_AVS.iVersionT2 = Val(Param_Interne_Treuils.T2_Version)*100
      //							Param_Interne_Treuils.T2_Freq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      //							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 2 ALORS
      //								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      //							SINON
      //								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      //							FIN
      //							SI Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 ALORS 
      //								Param_Interne_Treuils.T2_Sens="MontÃ©e"
      //							SINON
      //								Param_Interne_Treuils.T2_Sens="Descente"
      //							FIN
      //						FIN
      //						
      //					CAS "2"
      //						Gn_REP_QAVS++			
      //						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      //							Param_Interne_Treuils.mTreuilProfondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      //							Param_Interne_Treuils.mTreuilVitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      //							Param_Interne_Treuils.mTreuilVitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      //							Param_Interne_Treuils.mTreuilMesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))	
      //						SINON
      //							Param_Interne_Treuils.T2_Profondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      //							Param_Interne_Treuils.T2_VitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      //							Param_Interne_Treuils.T2_VitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      //							Param_Interne_Treuils.T2_Mesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))				
      //						FIN	
      //						Param_Interne_Treuils.ValT0 = G_AVST0
      //					CAS "3"
      //						Gn_REP_QAVS++
      //						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      //							Param_Interne_Treuils.mTreuilVersion_DRV_ACQ=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_BP=Droite(Gauche(sContenu_AR,9),3)
      //						SINON
      //							Param_Interne_Treuils.T2_Version_DRV_ACQ=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.T2_Version_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.T2_Version_DRV_BP=Droite(Gauche(sContenu_AR,9),3)				
      //						FIN
      //					CAS "4"
      //						Gn_REP_QAVS++
      //						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      //							Param_Interne_Treuils.mTreuilVersion_DRV_Capture=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)
      //						SINON
      //							Param_Interne_Treuils.T2_Version_DRV_Capture=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.T2_Version_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.T2_Version_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)				
      //						FIN
      //					CAS "5"
      //						Gn_REP_QAVS++
      //						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      //							Param_Interne_Treuils.mTreuilVersion_DRV_HeureABS=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.mTreuilVersion_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)
      //						SINON
      //							Param_Interne_Treuils.T2_Version_DRV_HeureABS=Gauche(sContenu_AR,3)
      //							Param_Interne_Treuils.T2_Version_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      //							Param_Interne_Treuils.T2_Version_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)				
      //						FIN
      //					FIN			
      //				TableauSupprime(GG_sendMessage,sVariable_AR)
      Logger.Log("Client["+trTmp:canalSocket+","+trTmp:ip+"]+_RAVS:_ "+sVariable_AR+"_:_"+sContenu_AR)
```

**Nom :** gestion_event_btnr

```wlanguage
      procÃ©dure privÃ©e gestion_event_btnr()
      
      SI sContenu_AR = "FROM-----UP" ALORS
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de montÃ©e")
      FIN	
      SI sContenu_AR = "FROM---DOWN" ALORS
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de descente")
      FIN	
      SI sContenu_AR = "FROM-----AU" ALORS
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton dâ€™arrÃªt dâ€™urgence")
      FIN
```

**Nom :** gestion_event_btnp

```wlanguage
      procÃ©dure privÃ©e gestion_event_btnp()
      
      SI sContenu_AR = "FROM-----UP" ALORS
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de montÃ©e")
      FIN	
      SI sContenu_AR = "FROM---DOWN" ALORS
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de descente")
      FIN	
      SI sContenu_AR = "FROM-----AU" ALORS							
      	Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton dâ€™arrÃªt dâ€™urgence")
      FIN
```


---

# Mock_AcquisitionSignal.wdc

**Nom :** Mock_AcquisitionSignal

```wlanguage
      Mock_AcquisitionSignal est une Classe
      	herite IAcquisitionSignal
      	tEcoute est un tableau de chaÃ®nes
      	nb est entier
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      m est MutexHelper(:mutex)
      nb+=1
      tEcoute.Ajoute("chaine"+nb)
      
```

**Nom :** EnfileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.EnfileBuffer
      PROCÃ‰DURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
```

**Nom :** DefileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.DefileBuffer
      PROCÃ‰DURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      renvoyer buf
```

**Nom :** TaillefileBuffer

```wlanguage
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.TaillefileBuffer
      PROCÃ‰DURE  TaillefileBuffer() : entier
      
      renvoyer :fileBuffer..Occurrence
```


---

# Mock_ClientSocket.wdc

**Nom :** Mock_ClientSocket

```wlanguage
      Mock_ClientSocket est une Classe
      	herite IClientSocket
      	tEcoute est un tableau de chaÃ®nes
      	nb est entier
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      m est MutexHelper(:mutex)
      nb+=1
      tEcoute.Ajoute("chaine"+nb)
```


---

# Mock_SignalDessine.wdc

**Nom :** Mock_SignalDessine

```wlanguage
      Mock_SignalDessine est une Classe
      herite ISignalDessine
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.Parametre
      PROCÃ‰DURE  Parametre(champImage)
      
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      RENVOYER Vrai
```

**Nom :** DessineDansImage

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DessineDansImage
      PROCÃ‰DURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      RENVOYER Vrai
```

**Nom :** DebutDessin

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DebutDessin
      PROCÃ‰DURE  DebutDessin()
      
```

**Nom :** SauveImageJpeg

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.SauveImageJpeg
      PROCÃ‰DURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      renvoyer vrai
```

**Nom :** PrepareDessin

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.PrepareDessin
      PROCÃ‰DURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
```

**Nom :** Affichage

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.Affichage
      PROCÃ‰DURE  Affichage()
      
```


---

# Mock_SignalEnregistrement.wdc

**Nom :** Mock_SignalEnregistrement

```wlanguage
      Mock_SignalEnregistrement est une Classe
      herite ISignalEnregistrement
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      RENVOYER Vrai
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.Parametre
      PROCÃ‰DURE  Parametre(nomFichier)
      
```

**Nom :** EcritDansFichier

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.EcritDansFichier
      PROCÃ‰DURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      RENVOYER Vrai
```


---

# Mock_SignalTraitement.wdc

**Nom :** Mock_SignalTraitement

```wlanguage
      Mock_SignalTraitement est une Classe
      	herite ISignalTraitement
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      RENVOYER Vrai
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Parametre
      PROCÃ‰DURE  Parametre(params)
      
      TypeVar(params)
```

**Nom :** Calcule

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Calcule
      PROCÃ‰DURE  Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      TypeVar(el)
      RENVOYER Vrai
```


---

# Mock_TreuilClient.wdc

**Nom :** Mock_TreuilClient

```wlanguage
      Mock_TreuilClient est une Classe
      	herite ITreuilClient
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
      
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
```

**Nom :** ParseDonnees

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilClient.ParseDonnees
      PROCÃ‰DURE  ParseDonnees(buf est un Buffer)
      
```


---

# MutexHelper.wdc

**Nom :** MutexHelper

```wlanguage
      MutexHelper est une Classe
      	privÃ©Ã©
      		nomMutex est chaine
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur(nomMutex est chaine)
      
      :nomMutex = nomMutex
      MutexDÃ©but(:nomMutex)
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      MutexFin(:nomMutex)
```


---

# ServeurConnexion.wdc

**Nom :** ServeurConnexion

```wlanguage
      ServeurConnexion est une Classe
      	herite IConnexionClientSocket
      	acquisition est AcquisitionConnexion dynamique
      	treuils est TreuilConnexion dynamique
      	app est AppConnexion dynamique
      protegee
      	clients est un tableau associatif de IClientSocket dynamique
      globale privee
      	mInstance est ServeurConnexion dynamique
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure privÃ©e Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit ServeurConnectionTreuil, mutex=%1",:mutex))
      
      :acquisition = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexionAcquisition)
      :treuils = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexiontreuilNonIdent)
      :app = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexionApp)
      :nomThread = "thCheckClients"
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("dÃ©truit ServeurConnectionTreuil, mutex=%1",:mutex))
      
      liberer :acquisition
      libÃ©rer :treuils
      libÃ©rer :app
      SignalDÃ©truit(:nomThread)
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.Demarre
      procÃ©dure Demarre() : boolÃ©en
      
      Logger.Log("Demarre ServeurConnectionTreuil")
      si pas :treuils.Demarre() ALORS GOTO ERR
      si pas :acquisition.Demarre() ALORS GOTO ERR
      si pas :app.Demarre() ALORS GOTO ERR
      
      renvoyer vrai
      
      ERR:
      	ExceptionDÃ©clenche(1,"Impossible de dÃ©marrer les connecteurs de client")
      	RENVOYER faux
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.Arrete
      procÃ©dure Arrete() : boolÃ©en
      
      Logger.Log("Arrete ServeurConnectionTreuil")
      SI PAS :treuils.Arrete() ALORS GOTO ERR
      SI PAS :acquisition.Arrete() ALORS GOTO ERR
      SI PAS :app.Arrete() ALORS GOTO ERR
      
      RENVOYER Vrai
      
      ERR:
      	ExceptionDÃ©clenche(1,"Impossible d'arrÃªter les connecteurs de client")
      	RENVOYER Faux
```

**Nom :** AjouteClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.AjouteClient
      procÃ©dure AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      si pas :clients[client.id]..Existe ALORS
      	m est un MutexHelper(:mutex)
      	:clients.InsÃ¨re(client.id, client)
      	Logger.Log(ChaÃ®neConstruit("ServeurConnectionTreuil Ajoute le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	renvoyer vrai
      FIN
      LoggerErreur.EcritLigne(ChaÃ®neConstruit("ServeurConnectionTreuil Le client[%1]=%2,%3 existe dÃ©jÃ ",client.nomThread,client.ip,client.canalSocket))
      RENVOYER faux
```

**Nom :** SupprimeClient

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.SupprimeClient
      procÃ©dure SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI client<>null _et_ :clients[client.id]..Existe ALORS
      	Logger.Log(ChaÃ®neConstruit("ServeurConnectionTreuil Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	:clients.Supprime(client.id)
      	renvoyer vrai
      FIN
      RENVOYER faux
```

**Nom :** DemarreEcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.DemarreEcouteSocket
      procÃ©dure privee DemarreEcouteSocket()
      
      :arretDemandÃ©=Faux
      SignalModifie(:nomThread,signalFermÃ©)
      
      ThreadExÃ©cute(:nomThread,threadNormal,()=>{
      	QUAND EXCEPTION 
      		LoggerErreur.EcritLigne(ExceptionInfo(errMessage))
      		ExceptionPropage(ExceptionInfo(errMessage))
      	FIN
      	BOUCLE
      		:EcouteSocket()
      		SI SignalAttend(:nomThread, 1cs) ALORS
      			SORTIR
      		FIN
      		ThreadPause(1s)
      	FIN
      })
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.EcouteSocket
      procÃ©dure privÃ©e EcouteSocket()
      
      POUR TOUT c dans :clients
      	SI c<>null _et_ c.estarretDemandÃ©() et PAS c.EstActif()	 ALORS
      		Logger.Verbose(ChaÃ®neConstruit("ServeurConnectionTreuil libÃ¨re le client[%1]=%2,%3",c.nomThread,c.ip,c.canalSocket))
      		:treuils.SupprimeClient(c)
      		:acquisition.SupprimeClient(c)
      		:app.SupprimeClient(c)
      		:SupprimeClient(c)
      		libÃ©rer c
      	FIN
      FIN
```

**Nom :** ArreteEcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.ArreteEcouteSocket
      procÃ©dure ArreteEcouteSocket()
      
      :arretDemandÃ©=Vrai
      SignalModifie(:nomThread,signalOuvert)
      
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)<>threadInexistant, :nomThread)
      :EcouteSocket()
```

**Nom :** Init

```wlanguage
      procÃ©dure Init()
      
      Logger.Verbose("Init ServeurConnectionTreuil")
      
      :DemarreEcouteSocket()
```

**Nom :** Quit

```wlanguage
      procÃ©dure Quit()
      
      Logger.Verbose("Quit ServeurConnectionTreuil")
      
      :Arrete()
      :ArreteEcouteSocket()
```

**Nom :** RecupereClients

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      POUR TOUT c dans :clients
      	t.Ajoute(c)
      FIN
      
      RENVOYER t
```

**Nom :** DemarreAcquisition

```wlanguage
      // format params = <fichier1>+TAB+<fichier2>+RC+<300;300;main.graphe1>+TAB+<300;300;main.graphe2>+RC+<"T0=12;offset=100;suppressionMoyenne=1;lissage=1">)
      procÃ©dure DemarreAcquisition(params est chaine):boolÃ©en
      
      t est un tableau de IClientSocket dynamique <- :treuils.RecupereClients()
      
      tabChamp est un tableau de 0 chaÃ®nes
      tabFics est un tableau de 0 chaÃ®nes
      extrait_chaine_nomfics(ExtraitChaÃ®ne(params,1,RC))
      extrait_chaine_champs(ExtraitChaÃ®ne(params,2,RC))
      ind est entier = 1
      POUR TOUT cl IN t
      	tr est un TreuilClient dynamique <- cl
      	SI tr<>null _et_ tr.type=ITreuilClient.eRecepteur alors
      		tr.ParametreNomFichier(tabFics[ind])
      		tr.ParametreChampImage(tabChamp[ind])
      		tr.ParametreTraitement(ExtraitChaÃ®ne(params,3,RC))
      		tr.DemarreAcquisition()
      		ind++
      	FIN
      FIN
      
      renvoyer vrai
      
      procÃ©dure interne extrait_chaine_nomfics(paramsFics)
      	pour i=1 _a_ ChaÃ®neOccurrence(paramsFics,tab)+1
      		fic est chaine = ExtraitChaÃ®ne(paramsFics,i)
      		tabFics.ajoute(fic)
      	FIN
      fin
      
      procÃ©dure interne extrait_chaine_champs(paramsImage)
      	champsgraphe est chaine = paramsImage
      	POUR i=1 _a_ ChaÃ®neOccurrence(champsgraphe,tab)+1
      		champ est chaine = ExtraitChaÃ®ne(champsgraphe,i)
      		tabChamp.ajoute(champ)
      	FIN
      fin
```

**Nom :** ArreteAcquisition

```wlanguage
      procÃ©dure ArreteAcquisition()
      
      t est un tableau de IClientSocket dynamique <- :treuils.RecupereClients()
      POUR TOUT cl IN t
      	tr est un TreuilClient dynamique <- cl
      	SI tr<>Null _ET_ tr.type=ITreuilClient.eRecepteur ALORS
      		tr.ArreteAcquisition()
      	FIN
      FIN
      
      RENVOYER Vrai
```

**Nom :** MonInstance

```wlanguage
         procÃ©dure globale publique MonInstance() : ServeurConnexion
         
         si mInstance=Null ALORS
         	mInstance = allouer ServeurConnexion()
         	mInstance.Build()
         FIN
         
         renvoyer mInstance
```

**Nom :** TEST_ServeurConnexion.wxt

---

# SignalDessine.wdc

**Nom :** SignalDessine

```wlanguage
      SignalDessine est une Classe
      	herite de ISignalDessine	
      	nomGrapheVirtuel est une chaine
      	champGraphe est un chaine
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.Parametre
      PROCÃ‰DURE  Parametre(image)
      
      Logger.Debug("Parametre le champ image id=" + :id+", ")
      //:champImage.Hauteur = ExtraitChaÃ®ne(image,1,";")
      //:champImage.Largeur = ExtraitChaÃ®ne(image,2,";")
      :champGraphe = ExtraitChaÃ®ne(image,3,";")
      :nomGrapheVirtuel = "graphe_"+:id
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      Logger.Debug("DÃ©marre l'affichage pour l'acquisition id=" + :id+", ")
      arretTimerSysDessin = faux
      :DebutDessin()
      
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Debug("Arrete l'affichage pour l'acquisition id=" + :id+", ")
      //:champImage = ""
      :champGraphe = ""
      si :nomGrapheVirtuel<> "" alors grSupprimeTout(:nomGrapheVirtuel)
      :nomGrapheVirtuel = ""
      :arretTimerSysDessin = vrai
      
      RENVOYER Vrai
```

**Nom :** DessineDansImage

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DessineDansImage
      PROCÃ‰DURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      grSupprimeSÃ©rie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'Ã©lÃ©ment "+el.Index)
      pour i=1 _a_ el.tabBuffer..Occurrence
      	grAjouteDonnÃ©e(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
      
      grDessine(:nomGrapheVirtuel)
      
      renvoyer vrai
```

**Nom :** DebutDessin

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.DebutDessin
      PROCÃ‰DURE  DebutDessin()
      
      grCrÃ©e(:nomGrapheVirtuel,grCourbe)
      si ChampExiste(:champGraphe) ALORS
      	grDestinationChamp(:nomGrapheVirtuel, :champGraphe)	
      FIN
      
```

**Nom :** SauveImageJpeg

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.SauveImageJpeg
      PROCÃ‰DURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      fic est une chaÃ®ne = "tmp/"+SansEspace(id)+"_img"+el.Index+".jpg"
      renvoyer dSauveImageJPEG(:champGraphe,fic)
```

**Nom :** PrepareDessin

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.PrepareDessin
      PROCÃ‰DURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
      grSupprimeSÃ©rie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'Ã©lÃ©ment "+el.Index)
      POUR i=1 _Ã€_ el.tabBuffer..Occurrence
      	grAjouteDonnÃ©e(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
```

**Nom :** Affichage

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.Affichage
      procÃ©dure Affichage()
      
      //QUAND EXCEPTION
      //	//FinTimerSys()
      //	RETOUR
      //FIN
      SI :arretTimerSysDessin ALORS
      	//FinTimerSys()
      	SupprimeTout(:fileAffichage)
      	RETOUR
      FIN
      SI :fileAffichage..Vide=Faux ALORS
      	elem est IAcquisitionSignal.Str_DATA_Acquisition
      	POUR i=1 _Ã€_ :fileAffichage..Occurrence
      		DÃ©file(:fileAffichage,elem)		
      	FIN
      	:DessineDansImage(elem)
      	//:SauveImageJpeg(elem)
      FIN
```

**Nom :** EnfileSignal

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalDessine.EnfileSignal
      PROCÃ‰DURE  EnfileSignal(elem est IAcquisitionSignal.Str_DATA_Acquisition)
      
      enfile(fileAffichage, elem)
```

**Nom :** TEST_SignalDessine.wxt

---

# SignalEnregistrement.wdc

**Nom :** SignalEnregistrement

```wlanguage
      SignalEnregistrement est une Classe
      	herite ISignalEnregistrement
      
      	nomthreadEnregistrement est une chaine
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      nomthreadEnregistrement ="enregistrementDansFichier"+&fileBuffer 
      SignalCrÃ©e(nomthreadEnregistrement,signalManuel,signalFermÃ©,partageAucun)
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      SignalDÃ©truit(nomthreadEnregistrement)
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      :handleFic = fOuvre(:nomFichier,foCrÃ©ation+foLecture+foEcriture)
      SI :handleFic<0 ALORS
      	LoggerErreur.EcritLigne("Erreur de crÃ©ation du fichier d'acquisition id=" + :id+", " + :nomFichier)
      	RENVOYER Faux
      FIN
      
      Logger.Verbose("Demarre acquisition pour le fichier id=" + :id+", " + :nomFichier)
      
      SignalModifie(nomthreadEnregistrement,signalFermÃ©)
      ThreadExÃ©cute(nomthreadEnregistrement,threadNormal,()=>{
      BOUCLE
      	si SignalAttend(nomthreadEnregistrement,10ms) ALORS
      		SupprimeTout(fileBuffer)
      		sortir
      	FIN
      	si pas fileBuffer..Vide ALORS
      		el est IAcquisitionSignal.Str_DATA_Acquisition
      		mbuf est un Buffer 
      		counter est entier = 0
      		tantque DÃ©file(fileBuffer,el) _et_ counter < 10
      			temp est buffer
      			SÃ©rialise(el,temp,psdXML)
      			mbuf+=[RC]+temp
      			counter++
      		fin		
      		SI PAS fEcritLigne(:handleFic, mbuf) ALORS
      			LoggerErreur.EcritLigne("Impossible d'Ã©crire l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      			SORTIR
      		FIN
      		Logger.Verbose("Ecriture de l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      	FIN
      FIN
      })
      
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Verbose("ArrÃªte acquisition pour le fichier id=" + :id+", " + :nomFichier)
      
      counter est entier =0
      tantque fileBuffer..Occurrence>0 _et_ counter<200
      	counter++
      	ThreadPause(1cs)
      FIN
      
      SignalModifie(nomthreadEnregistrement,signalOuvert)
      ArreteThreadSurTimeout(()=>ThreadEtat(nomthreadEnregistrement)<>threadInexistant, nomthreadEnregistrement)
      
      SI :handleFic>0 ALORS
      	Logger.Debug("Fermeture du fichier d'acquisition id=" + :id+", " + :nomFichier)
      	fFerme(:handleFic)
      	RENVOYER Vrai
      FIN
      
      renvoyer faux
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.Parametre
      PROCÃ‰DURE  Parametre(nomFichier)
      
      :nomFichier = fabrique_nomfichier(nomFichier)
      :nomFichier = nomFichier
      Logger.Verbose("ParamÃ¨tre le nom de fichier pour l'acquisition id=" + :id+", " + :nomFichier)
      
      procÃ©dure interne fabrique_nomfichier(lecteur_dossiers_nom_extension)
      	lecteur_dossiers est une chaÃ®ne = fExtraitChemin(lecteur_dossiers_nom_extension,fDisque+fRÃ©pertoire)
      	fichier est une chaÃ®ne= SansEspace(id) + "_" + fExtraitChemin(lecteur_dossiers_nom_extension,fFichier)
      	extension est une chaÃ®ne = fExtraitChemin(lecteur_dossiers_nom_extension,fExtension)
      	renvoyer lecteur_dossiers + "tmp/" + fichier + extension
      fin
```

**Nom :** EcritDansFichier

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.EcritDansFichier
      PROCÃ‰DURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      enfile(fileBuffer,el)
      renvoyer vrai
      
      //mbuf est un Buffer 
      //SÃ©rialise(el,mbuf,psdXML)
      //si pas fEcritLigne(:handleFic, mbuf) alors
      ////hbuf est entier systÃ¨me = &el
      ////si fEcrit(:handleFic,hbuf,IAcquisitionSignal.CST_TAILLE_STRUCT)<0 ALORS
      //	LoggerErreur.EcritLigne("Impossible d'Ã©crire l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      //	renvoyer faux
      //FIN
      //
      //Logger.Verbose("Ecriture de l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      //renvoyer vrai
```


---

# SignalTraitement.wdc

**Nom :** SignalTraitement

```wlanguage
      SignalTraitement est une Classe
      	herite ISignalTraitement
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      
      Logger.Verbose("Demarre acquisition pour le traitement id=" + :id+", ")
      
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      
      Logger.Verbose("Arrete acquisition pour le traitement id=" + :id+", ")
      
      RENVOYER Vrai
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Parametre
      PROCÃ‰DURE  Parametre(params)
      
      Logger.Verbose("Parametre acquisition pour le traitement id=" + :id+", p=" + params)
```

**Nom :** Calcule

```wlanguage
      procÃ©dure Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
      
      dim est entier = el.tabBuffer..Occurrence
      pour i=1 _a_ dim
      	el.tabBuffer[i] += random(1,5)*100//PartieEntiÃ¨re(sinus(i * (20 / dim) * 2 * 180  ) * 1000) + random(1,5)*100
      FIN
      renvoyer vrai
```

**Nom :** TEST_SignalTraitement.wxt

---

# ThreadRunner.wdc

**Nom :** ThreadRunner

```wlanguage
      ThreadRunner est une Classe
      	herite IThreadRunner
      	nomThread est chaine
      	arretDemandÃ© est un boolÃ©en
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur(id est entier, callback est procÃ©dure)
      :id = id
      :callbackThread = callback
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      SignalDÃ©truit(:nomThread)
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode IThreadRunner.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      
      Logger.Verbose(ChaÃ®neConstruit("Demarre acquisition pour le ThreadRunner id=%1", :id))
      
      si :nomThread="" alors
      	:nomThread = ChaÃ®neConstruit("ThreadRunner%1",:id)
      	Logger.Verbose(ChaÃ®neConstruit("Construit ThreadRunner id=%1", :id))
      	SignalCrÃ©e(:nomThread,signalManuel,signalFermÃ©,partageAucun)
      fin
      :arretDemandÃ©=faux
      SignalModifie(:nomThread,signalFermÃ©)
      ThreadExÃ©cute(:nomThread,threadNormal,:callbackThread, :id, :nomThread, ()=>arretDemandÃ©)
      
      renvoyer vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode IThreadRunner.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      
      Logger.Verbose(ChaÃ®neConstruit("Arrete acquisition pour le ThreadRunner id=%1",:id))
      
      :arretDemandÃ©=vrai
      SignalModifie(:nomThread,signalOuvert)
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)=threadEnCours,:nomThread)
      
      RENVOYER Vrai
```

**Nom :** Parametre

```wlanguage
      // RedÃ©finition de la mÃ©thode IThreadRunner.Parametre
      PROCÃ‰DURE Parametre(params)
      
      Logger.Verbose(ChaÃ®neConstruit("Parametre acquisition pour le ThreadRunner id=%1, p=%2", id, params))
```

**Nom :** TEST_GestionDesCalculs.wxt

---

# TreuilClient.wdc

**Nom :** TreuilClient

```wlanguage
      TreuilClient est une Classe
      	herite ITreuilClient                    
      	
      	threadWorker			est un IThreadRunner dynamique
      	
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      Logger.Verbose("construit TreuilER["+:mutex+"]")
      
      :nomThread="thTreuilER"+:id
      
      :traitement = FabriqueDeSignal.CreeTraitementSignal()
      :enregistre = FabriqueDeSignal.CreeEnregistrementSignal()
      :dessine = FabriqueDeSignal.CreeDessineSignal()
      
      :threadWorker = allouer un ThreadRunner(:id, :threadTraitement)
      
      :traitement.ID = :id
      :enregistre.ID = :id
      :dessine.ID = :id
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      Logger.Verbose("dÃ©truit TreuilER["+:mutex+"]")
      
      libÃ©rer :traitement
      libÃ©rer :enregistre
      libÃ©rer :dessine
      
      libÃ©rer :threadWorker
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      
      Logger.Debug("Arrete TreuilER["+:mutex+"]")
      :ArreteEcouteSocket()
      RENVOYER Vrai
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      Logger.Debug("Demarre TreuilER["+:mutex+"], thread="+:nomThread)
      :DemarreEcouteSocket()
      :Ecrit(ITreuilClient::SOCKMSG_TYPE_DEMANDE)
      :Ecrit(ITreuilClient::SOCKMSG_NUMERO_TREUIL)
      RENVOYER Vrai
```

**Nom :** ParametreNomFichier

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreNomFichier
      PROCÃ‰DURE  ParametreNomFichier(nomFichier est chaÃ®ne)
      
      Logger.Log("Nom fichier acquisition Treuil["+ :mutex + "]"+"="+nomFichier)
      :enregistre.Parametre(nomFichier)
```

**Nom :** ParametreChampImage

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreChampImage
      PROCÃ‰DURE  ParametreChampImage(paramsImage)
      
      Logger.Log("DÃ©marre l'acquisition pour le Treuil["+ :mutex + "]")
      :dessine.Parametre(paramsImage)
```

**Nom :** ParametreTraitement

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreTraitement
      PROCÃ‰DURE  ParametreTraitement(params)
      
      Logger.Log("ParamÃ¨tre le traitement de l'acquisition pour le Treuil["+ :mutex + "]")
      :traitement.Parametre(params)
```

**Nom :** DemarreAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      Logger.Log("DÃ©marre l'acquisition pour le Treuil["+ :mutex + "]")
      
      SI PAS :threadWorker.DemarreAcquisition() RENVOYER Faux
      SI PAS :enregistre.DemarreAcquisition() RENVOYER Faux
      SI PAS :dessine.DemarreAcquisition() RENVOYER Faux
      SI PAS :traitement.DemarreAcquisition() RENVOYER Faux
      
      RENVOYER Vrai
```

**Nom :** ArreteAcquisition

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Log("ArrÃªte l'acquisition pour le treuil["+ :mutex + "]")
      
      :threadWorker.ArreteAcquisition()
      
      :enregistre.ArreteAcquisition()
      :dessine.ArreteAcquisition()
      :traitement.ArreteAcquisition()
      
      RENVOYER Vrai
```

**Nom :** ParseDonnees

```wlanguage
      // RedÃ©finition de la mÃ©thode ITreuilClient.ParseDonnees
      PROCÃ‰DURE ParseDonnees(_buffer est un Buffer)
      
      QUAND EXCEPTION DANS
      	nbMsg				est un entier	= Taille(_buffer)/::Size_MSG_Socket_RX
      	sVariable_Message	est une chaÃ®ne
      	sContenu_Message	est une chaÃ®ne
      	sVariable_AR		est une chaÃ®ne
      	sContenu_AR			est une chaÃ®ne
      	
      	POUR i = 1 A nbMsg
      		
      		sVariable_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) Ã€ (i*::Size_MSG_Socket_RX)]],1,::CST_SOCKMSG_SEP)
      		sContenu_Message	= ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) Ã€ (i*::Size_MSG_Socket_RX)]],2,::CST_SOCKMSG_SEP)	
      		
      		SELON sVariable_Message
      			CAS "STATUS","TEMP"
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      				
      			CAS "DECO"
      				gestion_deconnexion()
      				
      			CAS "ERR"
      				gestion_erreur()			
      				
      			CAS "TYPE"
      				gestion_type_treuil()
      				
      			CAS "RAVS"
      				gestion_ravs()
      				
      			CAS "RPAR"
      				gestion_rpar()		
      				
      			CAS "MSG"
      				gestion_msg()
      				
      			CAS "EVT"
      				sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      				sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      				SELON sVariable_AR
      					CAS "STOP"
      						gestion_event_stop()
      						
      					CAS "BTNP"
      						gestion_event_btnp()	
      						
      					CAS "BTNR"
      						gestion_event_btnr()
      						
      					AUTRE CAS
      						//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				FIN
      				
      			CAS "COMM"
      				SI (Taille(sContenu_Message) = 16) ALORS
      					Logger.Log("Client["+:canalSocket+","+:ip+"]+_COMM_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      				SINON
      					Logger.Log("Client["+:canalSocket+","+:ip+"]+_COMM_:_"+"Erreur RÃ©ception Contenu_Message")
      				FIN
      				
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Variable :"+sVariable_Message)
      				LoggerErreur.EcritLigne("Contenu :"+sContenu_Message)
      				LoggerErreur.EcritLigne(ChaÃ®neConstruit("[%1%][%2]_:_Erreur RÃ©ception Contenu_Message",:ip,:id))
      		FIN	
      	FIN
      	
      FAIRE
      	ExceptionActive()
      	LoggerErreur.EcritLigne("exception dans ParseDonnees")
      FIN
      
      	PROCÃ‰DURE INTERNE M(var)
      		RENVOYER ChaÃ®neConstruit("%1%2%3",var,:canalSocket,:ip)
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_deconnexion()
      		SELON sContenu_Message					
      			CAS "exit.From.PC"
      				Logger.Message(M(sContenu_Message)+"Sortie de lâ€™application suite Ã  la demande provenant du PC Serveur")	
      			CAS "exit.From.PEGASE"
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant de lâ€™application Cliente ÂµC")	
      			CAS "exit.From.REBOOT"
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant d'un Reboot du ÂµC")	
      			AUTRE CAS
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite Ã  la demande provenant de source inconnue")	
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_erreur()
      		
      		sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")	
      		SELON sVariable_AR
      			CAS "ARRT"
      				SI sContenu_AR = "PILE---WIFI" ALORS						
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Erreur de transmission WIFI")
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Erreur de transmission du buffer dâ€™acquisition par la socket, le treuil arrÃªte lâ€™acquisition pour Ã©viter un plantage")
      				FIN
      			CAS "IDENT"
      				SI sContenu_AR = "NoCOMP" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne voit aucun composant compatible")
      				FIN	
      				SI sContenu_AR = "COMPNonAdresse" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne reconnaÃ®t pas le composant")
      				FIN	
      			CAS "MESURE"
      				SI sContenu_AR = "SENS--COD" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Le moteur tourne dans le mauvais sens")
      				FIN	
      			CAS "VITESSE"
      				SI sContenu_AR = "NONSPECIF" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Pas de vitesse configurÃ©e")
      				FIN	
      			CAS "MOTEUR"
      				SI sContenu_AR = "BLOQ--MOT" ALORS							
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Blocage moteur, aucune rotation dÃ©tectÃ©e")
      				FIN
      			CAS "GOTO"
      				SI sContenu_AR = "NON-ATTEINT" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] ArrÃªt moteur alors que la destination du GOTO nâ€™a pas Ã©tÃ© atteinte")
      				FIN
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] var:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_type_treuil()
      		SI PAS Contient(sContenu_Message, "@") ALORS
      			
      			SI Contient(sContenu_Message, ::SOCKREP_TYPE_TREUIL_EMETEUR) ALORS
      				:type = ITreuilClient.eEmetteur
      				:Ecrit(::SOCKMSG_TYPE_CFG_EMETTEUR)		
      				:Param_Interne_Treuils.mTreuilType = 0
      			SINON SI Contient(sContenu_Message, ::SOCKREP_TYPE_TREUIL_RECEPTEUR) ALORS
      				:type = ITreuilClient.eRecepteur
      				:Ecrit(::SOCKMSG_TYPE_CFG_RECEPTEUR)
      				:Param_Interne_Treuils.mTreuilType = 1
      			SINON
      				:type = ITreuilClient.eNonIdentifie
      				:Ecrit(::SOCKMSG_REBOOT)
      				:Param_Interne_Treuils.mTreuilType = 2
      			FIN
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_ravs()
      		sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      		//				SELON sVariable_AR								
      		//					CAS "1"
      		//						si Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilIP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      		//							Param_Interne_Treuils.mTreuilType=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      		//							
      		//							Param_Interne_Treuils.mTreuilVersion=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      		//							GG_AVS.iVersionT1 = val(Param_Interne_Treuils.mTreuilVersion)*100
      		//							Param_Interne_Treuils.mTreuilFreq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      		//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 1 ALORS
      		//								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      		//							SINON
      		//								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      		//							FIN							
      		//							si Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 alors 
      		//								Param_Interne_Treuils.mTreuilSens="MontÃ©e"
      		//							sinon
      		//								Param_Interne_Treuils.mTreuilSens="Descente"
      		//							FIN
      		//						sinon
      		//							Param_Interne_Treuils.T2_IP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      		//							Param_Interne_Treuils.T2_Type=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      		//							
      		//							Param_Interne_Treuils.T2_Version=ExtraitChaÃ®ne(sContenu_AR,1,"-")
      		//							GG_AVS.iVersionT2 = Val(Param_Interne_Treuils.T2_Version)*100
      		//							Param_Interne_Treuils.T2_Freq=Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      		//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 2 ALORS
      		//								Param_Interne_Treuils.Energie=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      		//							SINON
      		//								Param_Interne_Treuils.Gain=Val(ExtraitChaÃ®ne(sContenu_AR,3,"-"))
      		//							FIN
      		//							SI Val(ExtraitChaÃ®ne(sContenu_AR,4,"-")) = 0 ALORS 
      		//								Param_Interne_Treuils.T2_Sens="MontÃ©e"
      		//							SINON
      		//								Param_Interne_Treuils.T2_Sens="Descente"
      		//							FIN
      		//						FIN
      		//						
      		//					CAS "2"
      		//						Gn_REP_QAVS++			
      		//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilProfondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      		//							Param_Interne_Treuils.mTreuilVitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      		//							Param_Interne_Treuils.mTreuilVitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      		//							Param_Interne_Treuils.mTreuilMesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))	
      		//						SINON
      		//							Param_Interne_Treuils.T2_Profondeur=Val(ExtraitChaÃ®ne(sContenu_AR,1,"*"))/2
      		//							Param_Interne_Treuils.T2_VitesseUp=Val(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),2))
      		//							Param_Interne_Treuils.T2_VitesseDown=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),4),2))
      		//							Param_Interne_Treuils.T2_Mesure=Val(Droite(Gauche(ExtraitChaÃ®ne(sContenu_AR,2,"*"),6),2))				
      		//						FIN	
      		//						Param_Interne_Treuils.ValT0 = G_AVST0
      		//					CAS "3"
      		//						Gn_REP_QAVS++
      		//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_ACQ=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_BP=Droite(Gauche(sContenu_AR,9),3)
      		//						SINON
      		//							Param_Interne_Treuils.T2_Version_DRV_ACQ=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.T2_Version_DRV_Arduino=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.T2_Version_DRV_BP=Droite(Gauche(sContenu_AR,9),3)				
      		//						FIN
      		//					CAS "4"
      		//						Gn_REP_QAVS++
      		//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_Capture=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)
      		//						SINON
      		//							Param_Interne_Treuils.T2_Version_DRV_Capture=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.T2_Version_DRV_COD=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.T2_Version_DRV_Genere=Droite(Gauche(sContenu_AR,9),3)				
      		//						FIN
      		//					CAS "5"
      		//						Gn_REP_QAVS++
      		//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_HeureABS=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.mTreuilVersion_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)
      		//						SINON
      		//							Param_Interne_Treuils.T2_Version_DRV_HeureABS=Gauche(sContenu_AR,3)
      		//							Param_Interne_Treuils.T2_Version_DRV_Output=Droite(Gauche(sContenu_AR,6),3)
      		//							Param_Interne_Treuils.T2_Version_DRV_PWM=Droite(Gauche(sContenu_AR,9),3)				
      		//						FIN
      		//					FIN			
      		//				TableauSupprime(GG_sendMessage,sVariable_AR)
      		Logger.Log("Client["+:canalSocket+","+:ip+"]+_RAVS:_ "+sVariable_AR+"_:_"+sContenu_AR)
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_rpar()
      		sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      		SELON sVariable_AR
      		//CAS "ARRT"
      			//MAJ_AFF_TRAME_SOCKET(0,"Energie _:"+Gauche(sContenu_AR,2))				 											
      			CAS "VPRO"
      				:Param_Interne_Treuils.Energie=Val(Gauche(sContenu_AR,2))
      				
      			CAS "COMM"
      				:Param_Interne_Treuils.Gain=Val(Gauche(sContenu_AR,2))
      				
      			CAS "QNUM"
      				:Param_Interne_Treuils.numeroTreuil = Val(sContenu_AR)
      				
      			CAS "QPOS"
      				:Param_Interne_Treuils.mTreuilProfondeur=Val(sContenu_AR)/2
      				
      			CAS "QVIT"
      				:Param_Interne_Treuils.Vitesse_cm_s = Val(sContenu_AR)
      				
      			CAS "QDIF"
      				:Param_Interne_Treuils.Decalage_Position_cm = Val(sContenu_AR)/2// reception en coup codeur ~2coups /cm
      				
      			CAS "EMT"
      				t est ITreuilClient:Str_DATA_Emet_t
      				t.Index		= Val(ExtraitChaÃ®ne(sContenu_AR,1,"-"))
      				t.POS_EMET	= Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      				:Param_Interne_Treuils.mTreuilProfondeur = t.POS_EMET
      				Enfile(fileBufferEmet,t)
      				
      			AUTRE CAS
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN	
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_event_stop()
      		StopFROM est entier = Val(Droite(sContenu_AR,2))
      		SELON StopFROM							
      			CAS ::CST_STOPFROM_ARRIVEAFTERGOTO
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : ARRIVED_AFTER_GOTO")
      			CAS	::CST_STOPFROM_FINCALIB
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : FIN_CALIBRATION")
      			CAS	::CST_STOPFROM_PWMNULLE
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : PWM_NULLE")
      			CAS ::CST_STOPFROM_CONFIGSENS
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : CONFIG_SENS")
      			CAS ::CST_STOPFROM_ERRSENS
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur SENS")
      			CAS ::CST_STOPFROM_BP
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Bouton Poussoir")
      			CAS ::CST_STOPFROM_CLOSEPWM
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Fin PWM")
      			CAS ::CST_STOPFROM_AU
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Arret d'Urgence")
      			CAS ::CST_STOPFROM_ERRCALIB
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur CALIBRATION")	
      			CAS ::CST_STOPFROM_ERRGOTO
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Erreur GOTO")
      			CAS ::CST_STOPFROM_CMDSOCKET
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Depuis PC")
      			CAS ::CST_STOPFROM_BLOCAGEMOTEUR
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrÃªt moteur : Blocage Moteur")		
      			AUTRE CAS
      				Logger.Message(M("EVT_STOP_")+"Source de l'arrÃªt moteur inconnu")
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_msg()
      		sVariable_AR	= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaÃ®ne(sContenu_Message[[1 Ã€ (::Size_MSG_Socket_RX-5)]],2,"=")
      		//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		SELON sVariable_AR
      			CAS "MESURE"					
      				SI sContenu_AR = "FIN--GOTO" ALORS
      					Logger.Message(M("MSG_MESURE_"+sContenu_AR)+"----==FIN GOTO MESURE==----")	
      				FIN
      				//cas "CALIB"
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				
      			AUTRE CAS
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_event_btnp()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de montÃ©e")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS							
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton dâ€™arrÃªt dâ€™urgence")
      		FIN
      	FIN
      
      	PROCÃ‰DURE INTERNE gestion_event_btnr()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de montÃ©e")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton dâ€™arrÃªt dâ€™urgence")
      		FIN
      	FIN
      
```

**Nom :** threadTraitement

```wlanguage
      procÃ©dure privÃ©e threadTraitement(id,nomThread est chaine, procArretDemandÃ© est une procÃ©dure)
      
      Logger.Debug(ChaÃ®neConstruit("Lance le thread de traitement id=%1",id))
      
      quand exception
      	LoggerErreur.EcritLigne("Exception dans le traitement " + ExceptionInfo(errMessage))
      	ExceptionPropage()
      FIN
      
      boucle
      	si SignalAttend(nomThread,1) ou :acquisition=Null
      		sortir
      	FIN
      	si :acquisition<>null _et_ :acquisition.TaillefileBuffer()>0 ALORS
      		elem	est IAcquisitionSignal.Str_DATA_Acquisition
      		counter	est entier	= 0
      		tantque defile(:acquisition.fileBuffer,elem) _et_ counter<10
      			:traitement.Calcule(elem)
      			:enregistre.EcritDansFichier(elem)
      			:dessine.EnfileSignal(elem)
      			counter++
      		fin
      	FIN
      	threadpause(1cs)
      FIN
```


---

# TreuilConnexion.wdc

**Nom :** TreuilConnexion

```wlanguage
      TreuilConnexion est une Classe
      	herite de IConnexionClientSocket
      privÃ©
      	clients est un tableau associatif de ITreuilClient dynamique
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit TreuilConnexion["+:mutex+"]")
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
      Logger.Verbose("dÃ©truit TreuilConnexion["+:mutex+"]")
```

**Nom :** AjouteClient

```wlanguage
      procÃ©dure AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :clients.InsÃ¨re(client.id, client)
      Logger.Verbose(ChaÃ®neConstruit("TreuilConnexion["+:mutex+"] InsÃ¨re le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      renvoyer vrai
```

**Nom :** SupprimeClient

```wlanguage
      procÃ©dure SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI :clients[client.id]..Existe ALORS
      	Logger.Verbose(ChaÃ®neConstruit("TreuilConnexion["+:mutex+"] Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	:clients.Supprime(client.id)
      	RENVOYER Vrai
      fin
      RENVOYER faux
```

**Nom :** Demarre

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE Demarre():boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("TreuilConnexion["+:mutex+"] dÃ©marre la socket %1, %2, %3",:NomSocket,:portSocket,:AdresseSocketServer))
      
      si SocketCrÃ©e(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketSansMarqueurFin)
      	Logger.Debug("CrÃ©ation de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      fin
      
      LoggerErreur.EcritLigne("Impossible de crÃ©er la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
```

**Nom :** Arrete

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE VIRTUELLE Arrete():boolÃ©en
      
      Logger.Debug(ChaÃ®neConstruit("Arrete TreuilConnexion["+:mutex+"] la socket %1",:NomSocket))
      
      :ArreteEcouteSocket()
      
      pour tout c dans :clients
      	c.Arrete()
      FIN
      
      ArreteThreadSurTimeout(()=>:EstActif(),:nomThread)
      
      SI SocketExiste(:NomSocket) ALORS
      	SocketFerme(:NomSocket)
      FIN
      
      renvoyer vrai
```

**Nom :** EcouteSocket

```wlanguage
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE VIRTUELLE EcouteSocket()
      
      si pas :arretDemandÃ© alors
      	SI SocketAttendConnexion(:NomSocket,:portSocket) ALORS
      		si _VerifieDemande() alors
      			canal est chaine = SocketAccepte(:NomSocket)
      			si canal<>"" alors
      				Logger.Log("TreuilConnexion["+:mutex+"] accepte la connexion entrante " + canal)
      				t est IClientSocket dynamique = FabriqueClient.CreeClient(:portSocket, canal)
      				SI :server.AjouteClient(t) ALORS
      					:AjouteClient(t)
      					t.Demarre()
      				SINON
      					t.Arrete()
      					SocketRefuse(:NomSocket)
      				fin
      			fin
      		sinon
      			Logger.Log("TreuilConnexion["+:mutex+"] refuse la connexion entrante ")
      			SocketRefuse(:NomSocket)
      		fin
      	fin
      fin
```

**Nom :** _VerifieDemande

```wlanguage
      procÃ©dure privÃ©e _VerifieDemande() : boolÃ©en
      
      SELON :portSocket
      	cas val(FabriqueClient.PortEnum.portTreuil):
      		SI :clients..Occurrence = FabriqueClient.MaxClientsRecepteur ALORS
      			RENVOYER Faux
      		FIN
      	AUTRE CAS
      		RENVOYER Faux
      		
      FIN
      
      renvoyer vrai
```

**Nom :** RecupereClients

```wlanguage
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      pour tout c dans :clients
      	t.Ajoute(c)
      FIN
      
      renvoyer t
```

**Nom :** server

```wlanguage
         procÃ©dure protÃ©gÃ©e server() : IConnexionClientSocket
         
         renvoyer ServeurConnexion.MonInstance
```

**Nom :** TEST_TreuilConnexion.wxt

---

# WD_DualAxisChart.wdc

**Nom :** WD_DualAxisChart

```wlanguage
      WD_DualAxisChart est une Classe
      	chart				est un 'System.Windows.Forms.DataVisualization.Charting'.Chart dynamique
      	chartArea			est un 'System.Windows.Forms.DataVisualization.Charting'.ChartArea dynamique
      	
      	serie1				est un 'System.Windows.Forms.DataVisualization.Charting'.Series dynamique
      	serie2				est un 'System.Windows.Forms.DataVisualization.Charting'.Series dynamique
      	
      	
      	_modeCurseur	est un ModeCurseur
      	curseurActif	est un boolean	= False
      	fenetreY		est un real		= 10
      	autoScroll		est un boolean	= True
      	
      	// callback (Ã©vÃ©nement)
      	EventCurseur is Procedure(STC_CurseurEventArgs)
      fin
      
      ModeCurseur est une Ã©numÃ©ration
      	Aucun	= 0
      	Ligne	= 1
      	Zone	= 2
      fin
      
      STC_CurseurEventArgs est un Structure
      	Y		est un real
      	YMin	est un real
      	YMax	est un real
      	
      	X1		est un real
      	X2		est un real
      	
      	X1_Min	est un real
      	X1_Max	est un real
      	X2_Min	est un real
      	X2_Max	est un real
      	
      	MoyX1	est un real
      	MoyX2	est un real
      fin
```

**Nom :** Constructeur

```wlanguage
      procÃ©dure Constructeur()
      _modeCurseur = ModeCurseur.Aucun
```

**Nom :** Destructeur

```wlanguage
      procÃ©dure Destructeur()
      
```

**Nom :** Init

```wlanguage
      procÃ©dure Init(champ)
      chart		= champ //new 'System.Windows.Forms.DataVisualization.Charting'.Chart
      chartArea	= new 'System.Windows.Forms.DataVisualization.Charting'.ChartArea("MainArea"+champ..Nom)
      chart:ChartAreas:Add(chartArea)
      
      serie1				= new 'System.Windows.Forms.DataVisualization.Charting'.Series("Serie1")
      serie1:ChartType	= 'System.Windows.Forms.DataVisualization.Charting.SeriesChartType'::Line
      serie1:XAxisType	= 'System.Windows.Forms.DataVisualization.Charting.AxisType'::Primary
      serie1:YAxisType	= 'System.Windows.Forms.DataVisualization.Charting.AxisType'::Primary
      
      serie2				= new 'System.Windows.Forms.DataVisualization.Charting'.Series("Serie2")
      serie2:ChartType	= 'System.Windows.Forms.DataVisualization.Charting.SeriesChartType'::Line
      serie2:XAxisType	= 'System.Windows.Forms.DataVisualization.Charting.AxisType'::Secondary
      serie2:YAxisType	= 'System.Windows.Forms.DataVisualization.Charting.AxisType'::Primary
      
      chart:Series:Add(serie1)
      chart:Series:Add(serie2)
      
      
      // SCROLLING avec ascenseur
      chartArea:AxisY:ScrollBar:Enabled			= True
      chartArea:AxisY:ScrollBar:IsPositionedInside= True
      chartArea:AxisY:ScaleView:Zoomable			= True
      chartArea:AxisY:ScaleView:Size				= fenetreY
      chartArea:AxisY:ScaleView:Position			= 0
```

**Nom :** ConfigurerGraphique

```wlanguage
      procÃ©dure ConfigurerGraphique(titre, nomY, uniteY, minY, maxY, inverseY)
      
      chart:Titles:Add(titre)
      
      chartArea:AxisY:Title		= nomY + " (" + uniteY + ")"
      chartArea:AxisY:Minimum		= minY
      chartArea:AxisY:Maximum		= maxY
      chartArea:AxisY:IsReversed	= inverseY
      
```

**Nom :** ConfigurerSerie1

```wlanguage
      procÃ©dure ConfigurerSerie1(nom, minX, maxX, couleur est chaine)//r est un int, g est un int, b est un int)
      
      serie1:Name				= nom
      
      chartArea:AxisX:Title	= nom
      chartArea:AxisX:Minimum	= minX
      chartArea:AxisX:Maximum	= maxX
      
      clr est 'System.Drawing.Color' dynamic = 'System.Drawing.Color'::FromName(couleur)
      serie1:Color = clr
      chartArea:AxisX:TitleForeColor			= clr
      chartArea:AxisX:LabelStyle:ForeColor	= clr
```

**Nom :** ConfigurerSerie2

```wlanguage
      procÃ©dure ConfigurerSerie2(nom, minX, maxX, couleur est chaine)
      
      serie2:Name					= nom
      
      chartArea:AxisX2:Enabled	= 'System.Windows.Forms.DataVisualization.Charting.AxisEnabled'::True
      chartArea:AxisX2:Title		= nom
      chartArea:AxisX2:Minimum	= minX
      chartArea:AxisX2:Maximum	= maxX
      
      clr est 'System.Drawing.Color' dynamic = 'System.Drawing.Color'::FromName(couleur)
      serie2:Color							= clr
      chartArea:AxisX2:TitleForeColor			= clr
      chartArea:AxisX2:LabelStyle:ForeColor	= clr
      
```

**Nom :** AjouterPointSerie1

```wlanguage
      procÃ©dure AjouterPointSerie1(positionY, valeurX)
      
      serie1:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
```

**Nom :** AjouterPointSerie2

```wlanguage
      procÃ©dure AjouterPointSerie2(positionY, valeurX)
      
      serie2:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
```

**Nom :** EffacerDonnees

```wlanguage
      procÃ©dure EffacerDonnees()
      
      serie1:Points:Clear()
      serie2:Points:Clear()
```

**Nom :** ExporterImage

```wlanguage
      procÃ©dure ExporterImage(chemin)
      
      chart:SaveImage(chemin, 'System.Windows.Forms.DataVisualization.Charting.ChartImageFormat'::PNG)
      
```

**Nom :** Scrolling

```wlanguage
      procÃ©dure Scrolling(y)
      
      si pas autoScroll alors retour
      
      min est un real = chartArea:AxisY:Minimum
      max est un real = chartArea:AxisY:Maximum
      		
      si min<= y <= max alors RETOUR
      	
      tantque y > max
      	min += :fenetreY
      	max += :fenetreY
      fin
      
      tantque y < min
      	min -= :fenetreY
      	max -= :fenetreY
      fin
      
      chartArea:AxisY:Minimum	= min
      chartArea:AxisY:Maximum	= max
      
```

**Nom :** MakeARGB

```wlanguage
      procÃ©dure MakeARGB(aa est un int, r est un int, g est un int, b est un int)
      renvoyer bitDÃ©caleGauche(aa, 24) + bitDÃ©caleGauche(r, 16) + bitDÃ©caleGauche(g,8) + b
      
```

**Nom :** ActiverCurseur

```wlanguage
      procÃ©dure ActiverCurseur(actif est boolean)
      
      curseurActif = actif
      chartarea:CursorY:IsUserEnabled	= actif
```

**Nom :** SetModeCurseur

```wlanguage
      procÃ©dure SetModeCurseur(mode est ModeCurseur)
      
      _modeCurseur = mode
      
      si mode = ModeCurseur.Zone alors
      	chartarea:CursorY:IsUserSelectionEnabled = True
      ELSE
      	chartArea:CursorY:IsUserSelectionEnabled = False
      fin
      
```

**Nom :** OnMouseMove

```wlanguage
      procÃ©dure OnMouseMove(yPixel est entier)
      
      si pas curseurActif OR modeCurseur <> ModeCurseur.Ligne alors RETOUR
      
      y est reel = chartarea:AxisY:PixelPositionToValue(yPixel)
      
      chartarea:CursorY:Position = y
      
      MajCurseurLigne(y)
      
```

**Nom :** MoveCurseur

```wlanguage
      procÃ©dure MoveCurseur(delta is real)
      
      si modeCurseur <> ModeCurseur.Ligne alors RETOUR
      
      y is real = chartarea:CursorY:Position + delta
      
      chartarea:CursorY:Position = y
      
      MajCurseurLigne(y)
      
```

**Nom :** MajCurseurLigne

```wlanguage
      procÃ©dure MajCurseurLigne(y is real)
      
      args is STC_CurseurEventArgs
      
      args:Y	= y
      args:X1	= Interpoler(serie1, y)
      args:X2	= Interpoler(serie2, y)
      
      si EventCurseur <> Null alors
      	EventCurseur(args)
      fin
      
```

**Nom :** MajCurseurZone

```wlanguage
      procÃ©dure MajCurseurZone(y1 is real, y2 is real)
      
      args is STC_CurseurEventArgs
      
      args:YMin	= Min(y1, y2)
      args:YMax	= Max(y1, y2)
      
      args:X1_Min	= GetStat(serie1, args:YMin, args:YMax, True)
      args:X1_Max	= GetStat(serie1, args:YMin, args:YMax, False)
      
      args:X2_Min	= GetStat(serie2, args:YMin, args:YMax, True)
      args:X2_Max	= GetStat(serie2, args:YMin, args:YMax, False)
      
      args:MoyX1	= GetMoyenne(serie1, args:YMin, args:YMax)
      args:MoyX2	= GetMoyenne(serie2, args:YMin, args:YMax)
      
      si EventCurseur <> Null alors
      	EventCurseur(args)
      fin
      
```

**Nom :** Interpoler

```wlanguage
      procÃ©dure Interpoler(s is object dynamic, y is real)
      
      n is int = s:Points:Count
      si n < 2 alors renvoyer Null
      
      p1,p2 sont objet dynamique
      x1, y1	sont reels
      x2, y2	sont reels
      
      pour i = 1 _a_ n-1
      	
      	p1	= s:Points[i-1]
      	p2	= s:Points[i]
      	
      	y1	= p1:YValues[0]
      	y2	= p2:YValues[0]
      	
      	si y >= y1 et y <= y2 alors
      		x1	= p1:XValue
      		x2	= p2:XValue
      		
      		renvoyer x1 + (y - y1) * (x2 - x1) / (y2 - y1)
      	fin
      	
      fin
      
      renvoyer Null
      
```

**Nom :** GetStat

```wlanguage
      procÃ©dure GetStat(s is 'System.Windows.Forms.DataVisualization.Charting'.Series dynamic, yMin is real, yMax is real, isMin is boolean)
      
      first	is boolean	= True
      val		is real
      
      p est objet dynamique
      x, y sont reels
      
      pour i = 0 _a_ s:Points:Count-1
      	
      	p	= s:Points[i]
      	y	= p:YValues[0]
      	
      	si y >= yMin et y <= yMax alors
      		
      		x = p:XValue
      		
      		si first alors
      			val		= x
      			first	= False
      		ELSE
      			si isMin et x < val alors val = x
      			si pas isMin et x > val alors val = x
      		fin
      		
      	fin
      	
      fin
      
      si first alors renvoyer Null
      renvoyer val
      
```

**Nom :** GetMoyenne

```wlanguage
      procÃ©dure GetMoyenne(s is object dynamic, yMin is real, yMax is real)
      
      sum		is real	= 0
      count	is int	= 0
      p		est objet dynamique
      x, y	sont reels
      
      pour i = 0 _a_ s:Points:Count-1
      	
      	p	= s:Points[i]
      	y	= p:YValues[0]
      	
      	si y >= yMin et y <= yMax alors
      		sum += p:XValue
      		count++
      	fin
      	
      fin
      
      si count = 0 alors renvoyer Null
      
      renvoyer sum / count
      
```

