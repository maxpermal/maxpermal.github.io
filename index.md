# Sources du projet

Document généré automatiquement.


---

## Mes Projets\repo\avsp_server\src\AcquisitionConnexion.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : AcquisitionConnexion
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1687bdbd004b6b9d
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      AcquisitionConnexion est une Classe
      	herite IConnexionClientSocket
      privee
      	clients est un tableau associatif de IClientSocket dynamique
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623474810141830045
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit AcquisitionConnexion, mutex=%1",:mutex))
      :nomThread="thAcquisitionConnexion"
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474810141895581
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("dÃ©truit AcquisitionConnexion, mutex=%1",:mutex))
     type : 655360
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1625068844508388771
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.AjouteClient
      PROCÃ‰DURE AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :clients.InsÃ¨re(client.id, client)
      Logger.Verbose(ChaÃ®neConstruit("AcquisitionConnexion[%1%] InsÃ¨re le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1625068853098391195
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Demarre
     procedure_id : 1625068865983361104
     type_code : 12
     code : |1+
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
      
      //SI pas SocketCrÃ©e(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      //	Logger.Log("AcquisitionConnexion a levÃ© une erreur dans la crÃ©ation du socket " + :NomSocket)
      //	renvoyer faux
      //FIN
      //
      //SocketChangeModeTransmission(:NomSocket, SocketSansMarqueurFin)
      //:DemarreEcouteSocket()
      //RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1625068874573363154
     type_code : 12
     code : |1-
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
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1625068891753302014
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : _AjouteAcquisitionTreuil
     procedure_id : 1643972503056403170
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1625399771586939404
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      POUR TOUT c DANS :clients
      	t.Ajoute(c)
      FIN
      
      RENVOYER t
     type : 458752
  properties :
   -
     name : server
     identifier : 0x16d07321087aea3c
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure protÃ©gÃ©e server() : IConnexionClientSocket
         
         renvoyer ServeurConnexion.MonInstance
        type : 1966080
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bdb900485b60
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_AcquisitionConnexion.wxt
```


---

## Mes Projets\repo\avsp_server\src\AcquisitionSignalClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : AcquisitionSignalClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1a0ed35f01958d1f
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      AcquisitionSignalClient est une Classe
      	herite IAcquisitionSignal
      	CONSTANTE TailleBufferMax = 100
      	bufferLecture est un buffer
      	acquisitionDemarrÃ©e est un boolÃ©en=faux
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877670499662073119
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit AcquisitionSignal")
      
      :nomThread = "AcquisitionSignal"+:id
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877670499662138655
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      Logger.Verbose("dÃ©truit AcquisitionSignal")
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1877670499662204191
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      :acquisitionDemarrÃ©e = vrai
      Logger.Debug("AcquisitionSignal Demarre, thread="+:nomThread)
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1877670499662269727
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE Arrete():boolÃ©en
      
      Logger.Debug("AcquisitionSignal Arrete, thread="+:nomThread)
      :acquisitionDemarrÃ©e = Faux
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877670499662335263
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE EcouteSocket()
      
      tmp est un buffer = :Lit()
      SI tmp<>"" ALORS
      	:bufferLecture+=tmp
      	si :_synchroniseTrameDansBuffer() _et_ :acquisitionDemarrÃ©e alors
      		:_parseBuffer()
      	FIN
      FIN
     type : 458752
   -
     name : _parseBuffer
     procedure_id : 1877670499662400799
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : _synchroniseTrameDansBuffer
     procedure_id : 1877670499662466335
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : EnfileBuffer
     procedure_id : 1877670499662531871
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.EnfileBuffer
      PROCÃ‰DURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
      Enfile(:fileBuffer,buf)
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1877670499662597407
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.DefileBuffer
      PROCÃ‰DURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      DÃ©file(:fileBuffer,buf)
      renvoyer buf
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1877670499662662943
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.TaillefileBuffer
      PROCÃ‰DURE  TaillefileBuffer() : entier
      
      renvoyer :fileBuffer..Occurrence
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : AcquisitionSignal
resources :
 string_res :
  identifier : 0x1687bdc1004f78ac
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\AppConnexion.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : AppConnexion
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1a0df36900fab2c8
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      AppConnexion est une Classe
      	hÃ©rite IConnexionClientSocket
      PRIVÃ‰E
      	client est un IClientSocket dynamique
      FIN
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877424251996975816
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit AppConnexion, mutex=%1",:mutex))
      :nomThread="thApplicationConnexion"
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877424251997041352
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : AjouteClient
     procedure_id : 1877424857587571525
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.AjouteClient
      PROCÃ‰DURE  AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :client = client
      Logger.Verbose(ChaÃ®neConstruit("AppConnexion[%1%] InsÃ¨re le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1877425106695732818
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Demarre
     procedure_id : 1877425884084863104
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877426223387358488
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1877427103856056182
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      t est tableau de IClientSocket dynamique
      
      t.Ajoute(:client)
      
      RENVOYER t
     type : 458752
   -
     name : SupprimeClient
     procedure_id : 1877427198345620150
     type_code : 12
     code : |1+
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
     type : 458752
  properties :
   -
     name : server
     identifier : 0x1a0df5cb010204c4
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure privÃ©e  server() : IConnexionClientSocket
         
         RENVOYER ServeurConnexion.MonInstance
        type : 1966080
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : Classe1
resources :
 string_res :
  identifier : 0x1a0df36000f78f61
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ApplicationClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ApplicationClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1a0e1cfc01121b32
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      ApplicationClient est une Classe
      	herite IClientSocket
      	bufferLecture est un Buffer
      
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877469963335441202
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit ApplicationClient")
      
      :nomThread = "ApplicationClient"+:id
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877469963335506738
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      Logger.Verbose("dÃ©truit ApplicationClient")
     type : 655360
   -
     name : Demarre
     procedure_id : 1877470354177621409
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre() : boolÃ©en
      
      :DemarreEcouteSocket()
      Logger.Debug("ApplicationClient Demarre, thread="+:nomThread)
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877470474436799623
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      tmp est un Buffer = :Lit()
      SI tmp<>"" ALORS
      	:bufferLecture+=tmp
      	_ParseDonnees(:bufferLecture)
      	//Trace(:bufferLecture)	
      	:bufferLecture=""
      FIN
     type : 458752
   -
     name : _ParseDonnees
     procedure_id : 1877471157336627428
     type_code : 12
     code : |1-
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
     type : 458752
   -
     name : Arrete
     procedure_id : 1877472012035186932
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete() : boolÃ©en
      
      Logger.Debug("Arrete ApplicationClient["+:mutex+"]")
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : Classe1
resources :
 string_res :
  identifier : 0x1a0e1cf2010ff413
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\AcquisitionConnexion.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 fYÒ¦8cwŠ1Çj]X³×ïÈ„	ÖRô¾âŒÄ¯     "      B      A c q u i s i t i o n C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          b                  9                €    ¼   01F280051n     v       :      >        
   
c                                                                ÿÎˆñ!   AcquisitionConnexion þí˜á                                /ÿ?a?t”ÌH²¥†qM(ä#Š@qT©¾+±÷­–Ía¸™â®q=	î]xÂâR    +  ðD    
dP«È@Ol˜ðå
Ñ…‘b 
)*8`1ÀÆˆ" ø,‘¼…‘cG
5uÔÀyC„æ3yf¾	a†Nvp† ÒF:füs—IœG¨Iš”Ç"¢‚4fæÄ4B‰Í78uÒ,ªÆÌ›:Tàac¦ÎÒ¦O£zœz’BÄ8[ï˜QÊÔé\º%O.¹J¦Y´tzþÊ³Ž˜1mÌˆ±â(µjÄÈ<¢L×¯‡Ï¦]Ûh=oÄ¸QÓ²o\ÀëÊ‹ñŒš7pÿÆ–ÊÛ÷oà?D	2xÄá•Z{ ÈØÐH§Òzì×=Ê¸ÊÜ;péß=V¬hAqôÆ§N@ü÷äÑ‹_ü½Eçë£kŸ›ýyTîRà>-
/ª‰8#"Áä± ¢#¬x‚ˆ*”€â	ÝÄa`•€[*éÐ`ª»JjŽ½æÈNÜ
 ‡uöH@:¢("—h8
H®C‹âûOª(¯µˆQ¥;æè
É:”DCÉ®NL1BØÁ+°ÄÚ©¬ÑÔbË­sÑ·ÙfˆÈÉ7î€C­B B«½šì2§^p‚&%ÞøIŒ7Èxm7ÞÎ,¾ùäCt9õÄËï@µ«;þ"
0€%Òà@Àè<ðÔ@öÌÔô·úõ.ÕP­4Ò!óO¿íº;õ=N ¾Ûu×Yû  |ýõcw…`),6"¿ÄvXßfåôÒLoÕ6 2ŒÛµ×Bð#˜1º
wÜr7vÖ0ð ×Ü Ú}WÝ_Ÿ
 ”:æ_}ÙÂÉ7Ý€ †wØeÿi–*{3œö×j_
 Ûm·å`Ä“4ˆ¬Ÿæ°ƒŒ:ÌhrE!\L¨	†º@º‹c£#62À£lcí¼æHrÉ&»â¸Ay,ËD³½Ãˆ¨	8Ð@C«®Ðj8’Jˆ4Ä æœ~ö8¦F ƒ¦Ž]j¥–^ŠéË›ð¤©…Ü°#ä<|0A†ZØAnºUPÁèBLi Þ€Ã*ÒpRŒ2|¡Ž4Ðv	&™Èj{¬7F <6C+N %"šÙ¢ŠÊˆÈt—#J ¢@ú¤YA’Nß¤[vÇD
ŽUE	|”·Ii
¾?‰)öü¸\¿ƒw ]³
Ã`67Œd>‘žÞ]œ+/LÉâàzy‹'ïÁß`Àây¢|ìJxá“Žöá]#N} ã?´ŒåÉ ¢"øh
ÙÈ¢Å {l$!NHÙC¤Ã¿Ô	Äûø‘G´G«Ù˜ g;c’“BÀ²	­IE‹–©ÌÔ 0,­iO›CÔ¦V53\-k[ëÙ¶”AI7<K&·6Ë…Åmo€ßrb7¼éÍˆyðÛæ Ó9ýEt¢O PgE‰­n ­{]Db§Ú/"·
 ït 3êwû‰Õð¢b- Ù*Šµ#ÐòüÅ«ˆ„€p¸Þ@‚° OôñŽÀŠH$hÀGxÅ"4±" r#1È#Û‡¾(ìQaP„%™÷«÷9Ë"ó£Ÿýhe‘üÍñ=³jÆ"„5Àanf ZVä‚Ñ‘Â{h€ Ðàˆ•y±RÀÈ€¤Åˆ“TD¢J"„`
fÐ¡*nó òä3!0JÜv˜8ló+—û’hÐ"&Ó¼ –²Y-ßRB45ˆ‰Â @b(¢V˜‚B°ÎYº3†APBSPtºÅ3©YMkì`ì€jph•rÏB!*ˆ¡„À	³ÌCÌÐ†8h%{#éß0Ê9ÁEd 
^Ð':#N¨9ëð5”n4E‹KaOªtB…NƒšÔæ@5«aMk\£¥
¿V‡°INm•«	9iÂ…»™À
^ñ“N=(™µ`ìFƒ˜ yc)Ý†êÓqŠ›ŒPÕ
Ô8´à¯d ”ÚÐ:4ñ¥OlÐ"ÒÏ'\
Z0ÂBp…9ˆA
Nœ
Uù¹þM@uBhCV- à¥òHƒr°41„àbØ
dÐÚ†µ¶+(p
Bð0q8JPdr˜œªÂ/ÈŸÃ*’‹¹"§ÉÅÞ}1VTèúVà0ªx¼'ïü¨BÆÐ`¼ò@_J^j;ääî=i,Ð<üJJb#TRùY)ØŽßŠHèÐ/÷k]	Ç0Þ È T£¤# `<^A Æ„‰¾CÃ	F‡)Œ0f‰Zq¡@Ly-9Ø;¬¤JË@;Ä!/jp-}º¢*h‘'àCñË`¤¿ÉÄÈ”Y(S;³™Â3£9Íj^3›žéJ7ÝðÍp‚æN˜s'b_€co¥ÇîälTf…ˆÔóžùÜg?å”ã¯Ù§=-(—|ŠØ…ª†5®‰èD+ªÜ‹5£'ÜhD<*ÜŽ”n&E©Jëš£
¦T¹” %µÒ

ª^ð#èä']B 0î.¶³
ÅU˜ÆÔ:ª1”*
«ê'fÕ‡[e›1 VˆõÎiæq—»¢V¶ºÕp•+]—x×‚æõp‰[œPÓŠW5öÛÙ&¬j[·(VÑ›>Éw4SŸÚÍyŽ!©Ëi¹Ž*8¸Jd9‡Ôiõ c%¨M“9ÖŠ
J ®›±T¦P†† 
NrIÜè†º&=pAZÛ•ÙŒÊâ0ñL~ð;”öŸ®†õÉC	H
`y^ †aSn'.CÌÛ0¸žùyÐ³	TŒû`Ü£úÌ_k‰Ïš*ÔùÎ}ô´	&!:ÌKksÙ4WÙ9Ïá0 ™¨HÖnnP‰,®€#(CsGyƒD«ó¼ï=ët§J1x´¤A6V ›Ü'€ß!³›åûÍO’Ÿk&ž™'qì@ÿ¦82yÐ| <+cÔ{G:~miO—Ô®¶7Pq-leK[ÍD¦Ë¹E+o}
\áW1sSr©ÄÜh9w:ÐÈèÐsÅêFåº®Ën¢¤Ã]æ…7"ß5/y·¿Þò²ø¼I/ø#âÞuÃwŒš¯|ë»_ý¶1þÂ›~åúßÏ#KFeŒSÿÿ‹¢ˆl	„ˆž
+ kŸ‰°A
È†ïi{	ƒ¤-
l¨ÀO:Ÿˆ]è—xÀ†l¤Pb˜Qb±Râ
þ‹ˆ>#Ã[¦Sò•ñ'p¦ä&ýËŸœ?Ô£±“€² 7 -'YÈ”(ˆ>h€C@2éxAb² ZP¦ÿ±)³™+“&jR-ã*.ã¦Øú2U€œ0ó3„B·Ÿ2B$”|c7yp· 3|Ò'~ò'8œž*48 (‹*½Óë;ªÀ„¥b¡ 1°4¡©ª!`»*a
»l³@¶±êÃ³Š-Ì 0€›i#Å¹R"’Ò›'8ÄÒƒ»VÜ!('A
3@¬9A­P7M{":‰œ»±OC,"˜*Y©UtƒX|EÒ E½™ÅZL
\¤“]´ºš«¼´£
ÂbÌš7xš=ùŒ38Ö@Š@FVtC¸A¬*Ð¬Ò2ƒ"P’FL,Bl °µ‚FtÄ°)F%ÙŒ(ÃnúDÓ`HFÄÂ7F;‰¸ŠàD30‚±™%ÄŠ¸^Ôº“`<È’,Ê²,ÌÒ¬z¤Šü@<ld-};‰/HD­0?T#ƒ­¨ƒ&P4À‰°Q7ð7sÔ½2”I*¸tCƒd—ä¸9è“3€ƒÐŠ9pŸ 1x^œÃ‰ã¼Ç2‚Èš¬Ê
#=BüEy¨+ó´á*.b4FåBGeTÇ`ÆX„Æ¨¼E7ÌÅÉ’V»º‘<	8°2¸5E¼µJü!®ÂÄ±‹-É¸%iŒ®pÊ¡)8?1Ã´RK€;&	‚T¨Ð#-QH7dH¤:	ðËÏƒ¼=; ½“Ü<yø» €&“<C<	2‚¦½”‡`„¦np³qô“r„	š`ËV”“º48' -×h’#À
ÐLIyàº×„ˆ4ƒ#|IŠd'ºEŒ´J” šÎŽäJœ¼°@ôœ£)j¾*‚¾,bêƒë‹ˆ° #ú‚÷ó Ì?ÿ´¿ÿ¬?Å? PâéAÿ
ÀäIOmP 
…Q± ¥‡y±¨ø9
O‹ BÓ#áÐt€è ø‘O}™ÐÐˆàÐBüŽÄŸ}Pþê»‚—™¹†Ñ™ðƒH)äÁ¬Bn ’ÜBj!øB,CÜØ²mò20§2sCuÊQ9lHyà¨;´§<¬3ÂQ'	,‘¨@<´³8ÏÐ” 7`ÉÚÇGì5ªò³Lb
"/ùª»+2u‹.óD¢EkKÅ„LGP¬JjSÍ!œ?
™ì´H7ìÎE…Nœ¡'(à'ËÔ* ¸"‚' P‹·ÛÌBL!ƒ,-ÓzN:,I½cSè•ÐRnë+›ƒ*h6vJ,ñØ#ø‰•à,Ñ½ªUÆaÕ«d¯îXMÓkæ250NPLµ—0ÓCmËDµF¬ËHÂ;	éT Ä:­è1âüÌÛtUÁóÖÙ”‡Î
€ñüÈË2Og¥ÑãYÏ p¾‹pOý›¾.R²ùˆú„• ÀÏý\•ƒ}ûš‹Ð %P‡]Ð ˆÑ‰™ÑÔ“X{ÍXåUÁsŠ³Pÿ²X{Byp¥ ˆÔA¬y pù $øƒh„!ØÔ©ÂT˜ˆ2ŽÙ@'
CkŠR2œÒ3¬R1û3,UÙí4Û¼Ô/3=´3¦½Heí,±Lˆ0Ë(QÝ8¬8|ƒjâÖÛ4L1KÄ‚:²5Ì78Wg|Ë·kFÓÀKšëVïüVyP¹+7ÃÊ82ÈÜØÒ6u¶c9á³2”80øŒ#$ÛU5[g
­í:Äš¦3°«ÉÖ…¼Í¹»2ý·ÛtÍ‹Mvµ<”`;À=:©[ÛÆ5ƒÇ!ÐE×Ëm`;³|]—ÍÌËk”MÖUŠ®L¨ÌÐ*±ë*cËÄƒ¼´<`+ sÜ‘Ì±yÞ±ÌB‹®ØLH«Âjƒ«åÛ¹; .æÍ&*°Isû8Ô4‡ê
©ûÊ1Ø
×p§(F¾(º…Eu$_s;ßvµ¶ã¹iÝ0 ¨i_®Ú8‘)¾H,Ë%Þl<‰Ýí:ÅX
¨=£ÇÜ¥Šðl;xCëŒCJmÓ é:˜0œD[Ý– Æk»ŸzÔU½M/5áÍ5ƒÎý\ÏLÔÛ´»¶SÝ½mWÔÅ»Wá“0<%^×#f]u…U:Ü
"%é ®À™ƒ¡¸^Ú…\É”Ï8SCD&–‡*õeŒÇÐ=Ê(Ãƒ
¢œß{(²Ó„k!…c8'q'ˆ3`Ö
«ˆ‚Lµ¬*àTOUQ}'V<Šc;Œ.	î8ùe¨AÃc’3¹4ö[qý´2p9³;:áÕ[.Å¾»ÓMP~:Ðƒ”ò%¨äâã_3P¸0~L#èÔ¯s:®òQæÖ¶»ÍŠk;Uf:°#Ìðe£æ»Íø0áxíÊ¤¼GfÍNe0b.MbmnSÔ
¼IcÆ+ÍÐË4Æ<Ü â]gvþ
|ÕWùà×­¾å˜:ºÏøb¿Eé3bŽ…µÏ‡è†…Xƒ&èˆMèƒ.è>Ðy®XePŒmgŠÖŸ— ˆ…ÐÀ ˆ G°žFš•pHŽ N i
Œ3àèð”þ@€@Ÿ8PŽŽHxé
KÁøYAmAJÑ¿ŒèBx£ˆH-ñh2ÊÁæ"Rj¢£–1“•Îþà æåå6XP¦qXP€$ˆ™ÂaÂ^%õÙ!ƒ+Îâž.½ ¤s`3€à¬æ8
»ŒÓkšxß›4¬K¦_BSçï”‚ˆhc xcÉˆc+UZP¹L¶_=¦F>6ƒ…K©?Ž·^´6U9k’€82€LÅpd†d‡¼
–CÕpËc°¬
^mÖæ‹Ë8­.ì;îŠM†ëNNÜi
å—[æR¾MÅvááfeW6XÞm¼ªeÍ¾eÒV¨]¶Ä^.:™+å`Nc~ã`³Ü=V'«v¸ÑH°ÃÛ«3áÛW¾nd;©»Ûôä¶‹æòÉ4æPîfè|æ¶#g€CMÕ¬æg%‘öo:äæ%6ðÙpb_Ö)NãwðÓ4g ¯è }çö<÷Ü¢ø$RÙ¡Ï|.Ø}öçöãgþÔ?dhWh„Æ?‡njLqÐ‰Öðß¸èˆˆ‡)ð‡/€—Pè†lŸY‰‡	€@x‘@
l${	f±-š‡!‡iô	ƒQè—ø†)ÏéóØ	eÁ
uA‰	êX1º¤Ž¥ÞA›}h<óÔ3ÙðìcÎ6ïÄX‘3 ’X†8  >¨„GH²‰=k
`Áh¶îYÖ– T†&0Ì2¢
H£õ¦4'+õªšìt¢ó†äÛ„3zS:ÛÃ ÃÍîtÏÎ’-qìÉ¨ŒÈnÃÉ¶ãÊFl¾Eà`ì€³­V—ã+uL®_ZoW¨5dMMd+èÔÓþÔPÕ‡»M”=aòÖÎäžðçÒµü¸`Çñö”.y¶.øü×A¿g‡sƒUq„ew…íÏ_h |j7'Y»qUÒñ€y‚_¤`8†„é`	Žai‚?–dqŸ.ßé•Ÿ–•â±÷ Õ÷ ˆw€ò ‚lø
›•À Œnàø‚g0xÀø è’Gøˆ„ àè ð†•W–…— ùóiYÀÐˆ†¨Øà2Uó(Q5ÑhAÑzïùŸ—xæ Øÿ¢QépM|g#àPŠ•¾¨–€         (àA„       2   
   …þE„+fÿ|'<f~&7	#XjÞ{ÑÊRº±÷OÉÝ(r¢¦=ŒL¥þsþ‡t                                     R   R   ÿÎˆñN   C:\Mes Projets\repo\avsp_server\src\TEST_AcquisitionConnexion.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\AcquisitionSignalClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 BP`@^À~YˆsØñ’«ä êmsÆŠÊ­0ä
 !}     "      \      A c q u i s i t i o n S i g n a l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             PCS          i                  9                €    ¼   01F280051n     y       ´      >        
                                                                       ÿÎˆñ$   AcquisitionSignalClient þí˜á                                /}õ@HÅëFä£ˆ6emÂÑgƒ—ŒpM†+µ1åZÂ´ç×ÓS{ë4=eHÏÀ
í    ¥  h6    
d áÆ@Ž,	 CíÁ†‘b 
r*@`1ÀÆˆ" ø,‘<…‘cG
5uÔÀyCE
š7bÚi£ÆÌ›:!ÌÐ	jç™<ÅÐ¡c&À?w™Äy¤:¤Iy("*HcfNL¤JV¶|sfÍ›9Û<:µªÇ«']l%å	•*BžT1¢Š5mÚ˜bçÌ¯NÄä	á#„Œ1ØJ}
·äI[ÇF<§‰2uìÌA:´è››{ì¶²ÕËòplÃÒ%L™4‹˜q#fÎbªJ
a458m‚ÿôqFŒ<­_Œ!â5o¤O@€ûwðá=~ˆ¤øä/®GOÕú@‘¯ã ˜_?ä}øÓà÷(ck{ ¹›/À·*ªÈ}ÊSð<úFÏ;ÑSï#œÐ¼#z¯Aùø{k¿ü¨òo 241ÄÊ¸.¢Ø,ˆè+ž ¢
%ìj{Â;c:2Î  úñG‚l²èQÈ!‹  "Šg„l”ÜèÉ(™üñˆÂ¸&É%x’K-‚ ¢@‚óÊ!Ïü ‚Ø²*©ž|’»" ŒhÄ J<ñÏ·8`+
""‚&¢æ°#43F»‘º„!‡6˜OPIÄ¨‡ðÈOþâ’ç„ˆâ˜2‚+c4¤}#ÑEëhtŽ2£Óµ× äNÔ"j4Ððê…+¼Ž¦RDëP4¦ÆºÍ,šlÂI§lmë;]§•ÏÞ€Ã*Ò MŒ2
aiËÊ
­kÛ…Ô(c;oT %"2À£ŠîH`=J Í~©Hæ“@ÀˆÎL¯B
-¤˜Á 7Ô“*ûPì¸à=Üó?}ÜøÄ0™2€P¶fŒ0[~9Ì'‹A‹™7º9ç2åÁe˜ušæ!É œ.x>:é ËtN¬älÍÎ!ñy ?Hînº“Å«.¢€ø¡µùò¬ŠOP»­ªë’«ê`Ð“2ˆh7XuôV8ˆÔL(}h¾·3
@ 
ª‰¯ &
56J=5UUV%-ºuÖZñÍ56|VØ9ˆ5Y3”-#8Ë¡m·³¬U+[Í+S[ö¶Åã×_€ ˜à“(„+€aþæ:âˆ.¤¸=åÙ‹Pãã=¾Ú#³EÔºöðØÆ~{î	|3N‹n¥úG«ÇZkŒ=Òþ»¯ 
{ì[Ëžíå×î¾2'±ª`nÞ|#íF! . 	É„ß,‘êE¤#ØÇF|§@õQ +Cˆ`‚)˜átªÀÎ¨…š2 ¥
bP—8„0Jà‰O€BT±ÁuxÁnzó§ÜŠ[±YAD¢0(Š(‚¦Ðú¯t*ØÁ±’³íèpWŠ
	"²ƒ$âÐÌ²ƒ¬`HÖ!sRôVl23Ô†,¬ÓMÿ~³uÝa€‰Ý[DµÏkX»Ù”µººN'”s#iZ‚:˜ËèòÁ¼Â5®r«Ú¢Ì®b‘ñ	W€‚Œ0…\aŽj¨cUfw?“õk ÿ²HÀ"Â»ª<x
C Å±H, Íó.v"èuHzÃ,ØÇBf¾ Ð•ÑCeÊÄ¤ˆDcþøB˜â¡€*H¥‚56¦@0 ›lú‘ÔHñ†on$Mk*“ÑÂ ¥ÅÀì|æž¾ˆˆo|åYD²¦©UÚ™^ÃÊ†ÞG¶Z"S™`e z¿ü„*Á¡¬nä&D”Ÿàx z¤#™¨„PÁ
fpƒü`T8Âž0…aiáOêðÅ0¾@Íá¶,‰•3à‡Ab-žÖª‰ÈQŽp¢èÓ|í+ªÀ¯ô:>ÚÁì²»Z—–Aî”4²:d"%ÙHz‰‹\ŠD%q%;3^QÓzW#uá9Ñ!ãæ°2‚+Šu†fØ"ºhœÎpŒPõëI0 MrÒ“ %ûJ»efèvm¥î^9=Yoa¶Œˆñ„ ]ò2}àQ-0§GRþ|L¶¯Mh? ²ÐÌ
´™çñE:_£¸”E¡·Î¤ÊÊRF…á®v E
S8˜Ü×È)LÁ Cr¿µðµe| áçÉþÙ'ÜqŒ¶ài_ ¿Úžl¡
…íC»Qyj …=lbëp£jdÔ›àhzEŠG@heéâ°‚”bPƒÁaé»\*BšP‰œé
kúÂÎðôõb†ï{Y;Æ†Tª‰hÄ¾p±^l%£úÓ“he upC„Bã¨¦3êÚAbÒâ·†+ˆ”€È÷à #A´š …)P¡”T•¶ÒDÎxå3¡a
|@ã8LÙ2X‰ÂV`‚€zxÌE“—˜¡
stƒŠ †Wf5´RAÀ0C0 y®^mã
ïŠÀat³ED5!( ÍqðMSôÜ Kõ$¦UÀ”ð„FKð$ò4¨AlÊÜro³ÍÝî@‹0Z.—´1meæ“Ú_ZŒ=ÊO0m[`s¶îÞü®—êö"»*Î„ne€«³T4û-ÆÝH0>!íª0w#a°¶©"ÝáÖ…ÚI¤¦Oªu·*ßÕ“½>î”÷¼mé¨m“©5w Ùð•o À@éE&Ës¸Ñ|—NôWÖÅ3< ¹Y¤À(Õ
‡*U±*¦RÃàøïJ
|Ï™ž¢¦åÁ„ˆ¼aN n|¡ Å'sP×_3˜`yÏ[
iT "àyQBš`0àe
V˜Q¨a3Áˆe
wŠiŠƒ±t•ðRË
¨à,0Ay ßåôîÄ&)²^s™xE]’95•cØ¨¦
¼±±ÕsiÖÔ.TÄŠÅ bUÐ‰nÇJÖ¢ò&5‡àMø“. jx»ql:s´‹jí$†‚CÀyÇÈ ÄDTÎr4„>6('‘ÞùÎck4=7¸ð†¶Óªg}R„Nt£#]éL¯
€ïv/xAöXi‰ž/|½
CBÑžô*,½é–µ<x°•9ã™ã	¤NõœÀö5nö¹ï}å‡Ÿùn5¹I´#xìÀ£•÷ƒ:à?“‡Ø
Ä–bY<ÒQ–<½7!ÆØ&c¤!%¸@3È,#¬˜
R€ô#ƒÒ™¿8ÁÆ’ Rû´é;	>12¿ Á(ëžcøës2(“2ò{º“è•ÿË=ÏØ=sŒÔ=.#
ß»¾á[½@ˆ¾”‡	(²O³‘"L;¬ +SÂ$F¬/•45ÆÂ,e{CTZ5ö¢WC¦ÐŠµbc[b&\Û5i-ñµ·¶bÒ7B<DCLDa¯<´7Ü‚CxƒCIt&t".ÈxÐ†jÚ6[¨D^
„:øƒMˆ`Oˆ
¨Š8¨…nh‚0	‚`8Eƒ‰ˆ €jÅ †_ðÄë2·ìª“}Òéa·ˆ˜(ñ"¶x+(°„Z8GÔct({C%øJˆP36s³Sñ ¦H¿:»³<¸Ù†‘JˆNP¸FÔ“¢„‡KE‰“FQI‚‹ƒÉA
Žó8¼ÂÆ6{3n”3:³3õ+ÀZA³ãx"§
=Ì“ (Ã¢95ðÕ142dBÒx°¹ÌÂ)03Î{Â%ŒB3ÀHŒðð3@C³ŠI.P=¢/P²(#‚&‚'‹²,´­´–ä=Ü¸2ì½°Â¾4ŒhT ˜—§r±<)Q É¢$I4Éì;¾îK>ð¿*Bž\J¬à‚Ÿ´Ê#ÃJ‹ì=ûC¾ï[¾q”,¾·üÊ¸Ô¿¹”/P—(µœÜI"lC!£Á´T€ d¼Ò%5ÛÇS©<Â4¥Ø€´HS€Èê¤O
¥QÊB©Jµ|/¬L»Ã
08ÌÄ4
#ƒ¯¨Ã‰Æ|L¨„Ìl|³[Š 
B¤S±|<$ÒhŠ PBÊ”J¸Â
#8L¶É¡Ü¹¶ä‚»Ä¿°d¾hQJÓ³“ÀÌH#7¸8Ð¹sèÈÂ¦Œ´RËÂ-¬Êõ4Ë“Í>qJE“	‚ƒOyà´÷ÜNG+¯BH“{,ðOò¤9óä+þtI\P‘ÃVû,;„5á!©µADž‰ñC]^
AÔ-D\D-Ä
QÅc¨Dågú‘•‰ƒBàN“ ‡Oð­zˆ"±ˆ•	…Iø†Ð&(Ñ­¸!%R£±P	ƒBHRr2“§(7y87í
Æ×H ‹ ÆˆÆ
ycFôrFõ‚FfJ5~‹ˆ @‘ëŒÑ„½ñtÔRŽÀˆ)Øˆ9œ¸‚•&H0•b°ìh©—Š0™R¡¬[£@«6Ø0Œ6
¼5ByèH"3ª¾5uTT2»¯Š¯;±#;P¥	tC<‰PÓFÅ¹`ÓlÕHU;jQú ª9t¥yµß­Új˜ÒÂ%ÔJž\@Ì=Æ5¦lKQðbPýfÓÑ·x6R¼6k­
jnó6ê‰ˆW·mý6qÝˆpˆrÃ§9¹ÒtFdâÒ^Ò· 	Sø¡7ÿ²MgÍ¬4ˆÝUqÄ›Ð¯O˜S‘Y nØ=ÕÓ’Š>
R°•
T Ô˜š02ÔSå:¨!3Ø=ËB)ð!K-*
BYLë³øT­KTQýº9»±‚²‹Ù³KÃ„lÀ¼3Œ·;Tº
Õ®£Y›5U=‹,¬¾ (@…U–õ
Z5[µLB‰ˆO¹S
 ÅUŽÑÕ íÕýU<¤Ð‚±PÝêCýÃc}F4QB<¦gTQIœÖ ¡Är½ÄLÄÅH Y|
PÅ0	ƒAEwôˆUlÅ0‰ DøÛˆx Z¼ÅÁ5^¤R+ÆwÍÒaÔ’F»­
{mÆt<ÎÑ7„/"ˆ’YNØ¶` 7ØP@X†³€ØˆÎ%0xŒ2“Ø”Z0²Ø³èM˜’0
ãØ¤­EÍÈ­d]t]å,Ly¨“%ª;ª¼9ç}T>#ÈÏƒÔèE5“›‹ÈÚE³¨
€2 ƒÑ OD;ìqÐÎªC{»Ã	µ @[fU[ÙÐÿUÖ·UDU¨c{ÃÏ¥
=…QÒ] ®Ó2)’ÓSªÀ’ˆXÒé y’{Â®|rW;Q7ªW~eÄdµe¼W©¨·2ÝWÓõZ–Ä^¬Ò_‡íaWÚû/‚ÈÐÓÑÕ×h
€~ÇÎ¬ùhÏiD“Ù2ÍàT‰äõØµ¢|+“€         (àA„       2   
   ©ê¡x‘¿úsø»ž(JZjÃ
/Ý­Ü12¢>–•óCÅÑ\ŽŽ’ÁžÀ1r’›`                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\AppConnexion.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Ù?Ï"í½¯F€R×9 ¥Fó&\¿"»Æ>æÎë#     "      à      A p p C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          a                  9                €    ¼   01F280051n     n       C      >        
                                                                       ÿÎˆñ   AppConnexion þí˜á                                /
@[Šéú[
"Ä^—÷¬œè&Ù|Š‰Cá¡Aè¼ŒŠÂ53¡ç%ãÒö­|    4  &8    
d€
ÇÀd³öPCÏÁ†‘b 
*8`1ÀÆˆ" ø,‘¼…Ä‰CÎ›7fò¨yÂ:!ìÄA¤:tÌøç.“8I ’4)¯EDiˆ©š£¦Ž™J\Â”IóO5fÞÔ¡‡3uˆEªÔ#Ó“"F™¢ä
1EFÖuû¶äIPÉ´	;Ö&No²‚KÖ,Úœeô¼ãF;C‹òí;ò¯¼(y²·mç P¯fÝÚã‡ˆ AºŽûâmÚJCÙ¹w€ßÁCïÜ€¸GPs/oý›¹ÇŠ-è‹]}6ð‘­U?_nûcõçÞes·¸»ïãnóVš| òñ-:W
à™ˆŸ-ˆ¶ò„H% (m3qÆ€ÖðKê74òkŠ‚ˆ„šã3æ0
¨˜€{²¨AÜ‚ÍQzÓÐ"ïØ[ê³!"Šc³¦*ÃŽ9°šQ;¦Â*	)œ#BØa±—bšé%ÆÆ*ë¬´Lcm»Õà’g†ˆ¦0â‰+ ÐÂˆ)B ¢B
qÜJI¯^pâ%%ÞÀIŒ7ÈÐŒ­)å‹O<ð˜Ã³DîÌ£oÁã„+.½ˆÜ
À>;ÉûÓ50¢ŽÏFçË®ÎDWÛó»ðò¼î9?Q´¯õÎCN¹J¹[Ô-2f "
Gc¾Hu Wc5€UW
5dlÝˆW_ Ê  "
Ã˜`86ÙapµWY7
 Ú[‡…€(>+6"‡vµPý…TLû:ÕµÏäÈ‰(X   ~0
¤pÛ#wSÎe®Þ¤E´T€Ýú-ˆ
VÈâVÚ‚áÀ`„•ºv  
AOÆµH,`øb·þÉ¶©m êÖÛUÁ%t qßËSàøÒˆ]wá•—@zOÀß••Òw9~GU9`€9pð$
"Ú
§9ì ã*/,d»„øaÈ¡
~ÚEå,øa£ˆÉÅÚÊd¤‘qÄêè:’^Ú‰”²Jí>Ë"¢&à@
_¸¢Â1à*"Òƒ˜WbB;i«RŒ—¶ÃªVj)É®^j!7ì¸*La„vÈ|sTx5+cˆh‡7àp£Š4r£F¨#
É	#CŒ:¼:³ò7F8½3*¦M %"êú¢2"jk‹ˆ(~ª·ß$Xí·‡¸”cK5µ³Sô°þtçž-ÂÙøã+m‹ 
µ<H¾YáÖ0”¿a‚"
eûÝ*­Øß¦~ƒKp#axE“‚¨0B	B±‰TyL[ÉL½ˆô\ÅÓYËšÒ=v™æ7é+Ô=Å>Ôt`hòÈ@DŠp“´)i"±‘„ôcjùA(
¢7$œÈq¬d±ÕH7ÊQnè¸µµÍt¢Ü`¨³äA:xm&½PÏz	H²·½ˆtï{Ü™£|Èg®@¡†…Yßá¸œ†© ~¸ÊU¨" AÞ*‘Æª•«C¼U(AÉ4+"Ñø& ŒJx’«ê^8!Êp“ ÙB²’éÑf"ü™¨XFžÏ 0
*d£Íú¸³±/TŸ)Ú@„°8hÎMRKo±"#pã¸ ÕVH(`$ÛAcD²)©5e—B03”a*gPÃ¬â•”+?ÁÜTn  y"‰+Kú
aó$È¼à˜É¼
3‡×+•f tðR+h)‘C¦2™™‚Á&'Zñ§““–xN¦2—ÉŒ
vÐ78´ÁGo8¨[HˆÐÏ˜n n8LNv'(3I0CâP¡ˆN§[¤S•>ãÕe´1!ðA”:––*ÅJ• ›Ýð6 ½ñÍof œàg¸Æå0q#ÜïøÉÏ™À
Yq1RQÅ6`å©u@ë¼Ð9´À5 P7w¹¹¾ u¯‹f×‚Àª!ˆíh^°;Ê´ i)]T“b%ˆDK\ò˜Bp…9ˆA
–}‹…<å±Œe\T7ÕX®6G{/ˆÅ—)Ü€Ï5w¼¥¨Î—G&e7óši{ëÇ†­ªU	ä@†Gê
5¡hÃs£K¬üäÊY9ÁxpŽd0ÀÖÐ¤°rÕ=ÊHD0„‘kakƒÜz%,?hË’kyÀåN._¶K]3¸Ä­å~Íg\áÎBÆœCŽ®r¡t$ Sø@ƒz±— Ì¦Æ±¢	xä›NüŒ"RÎs¦sí|gMòIO7Ø3
øÄ
G5:YÇ
¡Ái™gP÷Ìe

}hD0ÑÈíø*D2RPªRUˆ…´y©K?3”Ôíny»áì€†®®pË+âê ¸²Rî¬ž[«Ž<O1 é£91Áú::™„î	®Ãqe¹èE£6ExÙ›ylÆ)Çnû3êâÖ¤„j0Ò\{ðƒ7Ø¡
m˜²gšâì °†^2Q}L¼Ï˜‡,m4ñ} ¨DÉf€ìÊ@UA
n0AS@$ü` FÀ‰–¦†3)tƒ­µaË`êÓ¸zJŸyAD¨ 1ÍÙ3Á	Ww°g7ô9rM€Â¨ÐépžŠïÁñ*Äë ‚»ÏèNMªECzc6K[êÒ—ÂZÑæÁjÅh‘Š¬öŒÓ«lóôFÚÀ¶¹¥ãmãÃÛXã¾åïo7îqü&xÀ}$x¾JnÚä²
5P„ ÀpÝù)%]H„#3	I¥t-
Fx®»Ý¾Dá =¿îµXy’™––å€iy¨ˆDª´K1áItÉKùù’\R§ò¤J>Ì¦¨+ 7tƒv|¡=H8 Ž92\µ
ã·Ã5ˆX÷2â¯ÁèÄæD§:U±ê+æÅqŽ±*î™OÿsÎ/ »Ùs”oj
™ÈŠ€¨D'Úø3ùHO^©À§ÝHTuËXíò—ÃüU2»I¬g&+KÌê´Ê`­˜‡kœé0çÔùr&Èkîù:<çAÏ|žsèâ ‡98JuÊ£ ˜ÇcšP…ü\TGþ$

Àµ³½|"ÌÁGñ&þò|åC¦ùÏ¿‰P¦O&ë»Üçn5L›²¨à8ppÂÛšäY…ÐrË
¯ ¿p+¾ïƒŒ*­M3ƒ"˜
/«hÓ>y¨Ð²«b<3ð20ð›
Þ§Á“³å
`o+?È˜À@;‰€Š =3@4‚’¾9c4ìCµ¦¸šCÙ¬~ó,€­ú³²¦0ÑD¨W“‡/½«2‚Ã‘@´ª¨ƒ&P4ˆ	ÅQ78>ƒ¸Š'¹¿(›ƒ §Û+Á„ÂÁ‘698H
™'h?1À›ë;5û;‰Ìš0~ë¬091°ƒ<ð¼“;Dà£ Ø¯„sž…K£†»žr¸ò‘¸Ú°¸îÀÄ>±pâ¸ë8PüÄA¹â:Ä`BÄÎH9D‰ :p›K£P†h1¯Ui¢h@WDÅëZ® ˆ \|Å àÅ›“®ˆƒEÈÅ„0FZÄ®£“‡¤ë o¹¯Ey:Di¢N<ÅÎð¯u0¶°#0¨“ˆŒ+GÓ;¢‰T´9»X®°€¸Ë:Ãa°1“K7y@‚½K1¿¼Þq18«'Ã›1Äc&›ÂD»
ƒ¼¦H‰…2‡¢<#C²w´ÈdÁìkA”°6l‹! #q¼´h¼7ð;ù37Œ<‰"È?lÃ1!¨ƒ«ˆÉ×{	Ä±óK>qr£É!”ªÏ¨P Èƒ6¨)§òª1«Iy²÷P ïSI–è2ƒ8¸
0§²‹I®*£œ?«ÔÈCQ ­œ3t:;ø›<Àå#I<‰
hJÐ’*€‚ˆ²ÊËÊ#´JÕÉÊ§ŒÊ¦ÒÉ¯Ko“·9ÃË=” hJïKÌK˜IúKB"<	/àKÌ-C3×S3Ø;A¿’	µòJ9qÌ3Œ3ÆÙ'xjŒ ¼
p*8JÉüLyÍ¬T€œÀ¡Åh,
)Ê°ÌÀŠ¦Ä1¨Šçd¦?3X¦Æ
ªã#JLÌàTÊ¦`Êâ,´Š*¨í”@«äA·4N
,»Ì
ÏËúŒÁlËÂLÊùlŠÙ:”ûôLñ<	#Ä7üœ:“ä¨`¨Ž¬¼##* “'82ØK±$K8‘'"9)8H©ÎP*ûŒsËÊãDŒ:04,;Ãä¼1Ü“Œæ$)è4’ Ét";ø©a&:ÀA=N¬lËI
ÇÒ5È'¨D>:yƒ;€=¨Ï
-!ôÏü<‰ÊÌÊ( Ì0ùK+¨§²"!x–l&)õ‹¦ð·,©@¶Qƒ6`&æ©çp§²JòlK­Ä¶2¸´'Ð´¨LK¤Óm
)àK('ï+ƒ¨=ø)P5}œ6m¬0ƒ•P¶1C#àREõJÌPƒI[È7ð>Ý4Î´Êj+ÎC…D}O}œPõ
RõÓSåPO;	PkOÄ'
8[%È½TÕrêÏýOyX0<%Öh›LduË#„_­·¦0¢¶ÜU
A$D«ÜOPÖ.BEp
×ÓRDF4£`-¥p­HŒ;7ŠˆýÄ£ÚºDŠÃ­rÑ­ÖÀ8@EOÜWë×Pä×õW}XPÑºZBEoW……!UÜ$G`Æ…Šp˜ÅQˆ&
†G€Ø Z˜Ø^ŒˆxÈXaŒeðØcˆP	‡IÐØXˆ“Æb™Æj¬/jºrÔFkSŠP©2Ú Ç H¡ ‹»Eé£õˆ=Dv” jÑ­íÔ(:¸X‘;À¸Ç{Ð0~”;Œ`‡è¦»Hi=‰¢E±¾[1wU…1£1}B&QÎÅcZ}ÚÆˆÚhµ(¼ýÈ£ÑºÅQÍR«ADÑÄó¨dQ8Í«´ˆÑœÑ=Üˆ”ÛÅ)çtÜ¼]BïÈ±óô¶¹’Oý±¦À€}ã¬kÓb½¬…õrU-G0u]£­m×x×Þš¸z­¸y½8NXáÅr$9DLXù€°U®é©%à„^8ƒgM ‡30…C@…h•8¨ à†@‚F#xƒ8†Á9†{–IH‚VÈ½IØ†îú®ð¯ò
€DEÈq8ƒôZ¯öz¯ø’YW:Š¥ÃF¥ÀÙX±E^¥ðY G¡µ—Z‚ànDÚâA\c‰ªà5ƒê¡Jˆ˜[X ÈÚ} ê°'X‘˜Ã;Q±’)8È³ý;SÛ,¼Ã«±Ÿì']¾øàáÆ²Êq
 -2Ë3â¶Û±À¨ÆJQÅËÜ}N“â¼(c5Aõ:Ó=	™
 šBÎ›Ê©Íá)ŸªÕÌÒÔ“›ð>Q«â¦š«Zýb‚Ì„
´ª¼Ù›9è›¿Ù?Ô;œÇa½4³Í—ˆ=µ“=ây’«Æª«»â=½ú½6,Je¶Â:,§¢TÅÒdz¬ß”¬È|ã[• P«ãQ&µ³´Jj
5<nªLÛ´õ„
kB(µÊ EÂÖ%S>LÝÄA,ÄÎ}];1¸E”Ýs}Äa\×Û•­?²DÑDÚ¨#à°
ØáÝWõ0ØsW	6®”óE‹@”Ppë:Ù˜KŠÏZg˜Í®U:·ÈßøŠFD9àAº‡¶Ùl$—úD¸œY
Ç£ Ç¡h¥“k[$·È5ð‚AÊ€o¨‰N×A°è[ÉƒuÐhþñˆhÞˆÐ ‘Ÿƒ“ˆlH€ˆÎ }¦Æ~þgâr¥;»c—™q—
>
I\èƒ¸áê#žN
Ÿ¸ß¨ÏqNG‹»á²ˆ  Ÿ 
¡HŽ?[yˆ€         (àA„       2   
   øÒ	ˆ€÷J 01u?  Êà÷ð„ðêŽf¢ô1y™©:ƒ%¹6i› ij‘                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ApplicationClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ŸSí­n'©JEdDamç°œ.^˜ çLQöëSS8ÖPž     "      w*      A p p l i c a t i o n C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                  9                €    ¼   01F280051n     s       Õ(      >        
                                                                       ÿÎˆñ   ApplicationClient þí˜á                                /Úp;Õq7i{‹Ei=ÛG ì”9Bcõe9`Ú##™D}1T$¬_>îQ    Æ(  –    
dPÌÒÀ&è×áÁ†‘b 
ê<`1@E‹  ø,‘<…Ä‰ÓF
1uÔÀyCÄ¥™7uB˜¡£ÓÎ3!lŠ¡CÇL€î2‰óØtdÉ“#"*HcfŽš:A•ØTƒ³8dØ˜©ƒT)S§Iš”§bê;gÎXmb†L;s‚òôù&Ä¸sç˜]šV­aµPåEˆxFÍÂh  0ÙòeÌ?Dù1sÓÍEvÊx HÃ¨UG\}zrÖNeL%]3jÛ+V´ óïÎ®Gb®œ»vhÏ£ÿVnÜt ÜN[†-Üãì
Œoo½)‚Æ[ˆxÄÊ"U”@y™Á½Ìg¸Ï§_ßö?ŠXLºà ÿ³¬:èd£Í>-â@<¶4ˆˆšzšÃŽ»ÌÈË=OŠKˆ†Ú µ­Ë#,@Ï"íBŠh­“Nˆ(Ž9Â"F•2ò

Â7$¤0«¼Rp«?É+Î2åÙ!¢&à@
«^¸Âª1à0*…Èˆ°Ž	±
a¥–^Ši¦šnÊi„ÏºlÈÉŠÄ.€Þ€Ã*ÒÐKŒ2Bð!„¸t	&™hâÊ«XØA2";“¸@‰ˆÐ-€B Š´À¦ˆ(~êŒÀÎP“À2Ô ˆ¹Î‚#Tæ¸{Î;¦s56"Õ5SL”4Vo%-0V ƒS=J‡_(b
Ö²LzU¶¶
)vY¤òck¿ˆ€0Àaõ¨)›,×Ìu '¢ˆµY
q8ÄÈÕ5¢<)ƒˆŠàkÂ
/’
4ä ‚B­ÞX² ‘
ðh"o‹4áÅÉ˜±F½BÐWÇ~çðÑP"‰SL $•dr'¡”Ò*Ë˜K;´ÔÓË>ÃìjÌ2

y^PÈQ‹*štRV-SM#âÔÛOÇèÜ Pmî3©“SÕÛ´^µ©v›ª•çÌä{lÄrAcZÄ2¹íÌðÓ¿³¶°Û­ ÷]«³æÎÜˆÒ]Wi¬#úzêVÉ–Õ^y*È×7Ä˜C/÷p8 Qü°@ ,<0ðXÁ³€=:1bÅ†ˆB˜Â—Uqì
¬À¡Œ Ú#7^NÚCØJÌ¯Â«Žômüñ£€<S±"Šb
(ˆ F‘"¬˜Âˆ²7Þq½|a‡¢„£q9M‘Ù"!¢¶GÞ+íÈ
,±ÈúXy"[¢ä%›Ô.4Pif|ÓŸr¢1ÆqÏ-Aäd:ù PmzSœæT†œÉ0E‚È@®÷„+@A
F˜B®0 1¨á|B2\nÕ¨G
-p1Z fµ¥Y§iñzÚ¨RE­ËT
ˆŸY•Ó¬£5J%1-]»N¸Z(°¥b}a[ZÀñƒ4 	H®À¶ aË#ÿ bô€ÉÔA$Æ
@€ –‡9¬"n»Üü#7€ÐM‰¹[ l5¹ÂÕí3|C—º€Ä®`p ¤Š96)ÆAägú
Ï=Ç˜"ÚqÐAsâÜ=ˆâ0@t¤ŒbpºÔ­®u¯‹MfW»ÛåNäV¼ú¯’—Ì$YV¸AÅ¤ÀyÐ“õ¬‡½ì
s~Â³_µ<¶  "upCvÒ“üä/±Š¾×¬ÜÏLùc‹ "B%4°›=øÁF°¥&@a
T8fZŠ$‚©ì .r¡‹]ð¢ø€›qè§SŠÄ‚©€!
Ž3Jhž¤@ äœC]îb#k²³ESA]PH–m” 5(Hµƒ¿4)¨`<zP3HP*0’bžs%´‡§|"Ï^ø³F„hN©á
7•*Oª‡¢qª:Ÿ"òðˆÞBbW_“ÈE••bµLÒ46~¦2•ÙHeÔ¸µ óQ¥*EÇ“ˆ12xÌãWëÖG[.h‰$[ æ·C–’Vá,TÉ€H²¥ƒ(EÍ`Ñ7`”îÁÀÝÊ‘`ØÜ@˜8ÏIcùn‹š"b2¢‘B #8ÔaFh(_*;‡Š^ÔCMËÉ—ðq4¤è»¦Hå1× HÁ
BxBB`„,ÁQP{4F]~ÕŸŠáÁTÞ0'Ð
`¯+N¯te
¸SRØ†6°¬¸Ê…bðß†6¥H0˜
®à85ˆaùƒxB 10É½àüIPÈ1ã@	pbØÒ¼ìÐGYyƒüà›aÂ}±0†5Ìañ:T1
ñ¯¢`
a
ì}/PBpáo8yëŒ)[hìGÓ„Ä&Öq{{Üâ Ã˜È=e
Gc+º
ˆ¬5˜S! F BpÞô¢¡Ã)ÔØ»æ)&°d¯Œc3œ¸()¦ïuó€¥Ö.r
ö»R.p!)
R È@+ØÁ¨ †=äÙ	T@ŠY 0L!
*P™ÍühJ[Óšæ´§©	jQ«À
^hZPi"P¡
žŽ6Ýi*lw¹T>Éœ ‚º:'&vðž%|'#ü…Xt”õkÜ9$zÑ~t¤']éKgºÁ­þt¨G]ê2‡ Õà^õ¸ýjsËšÖ3¸õr½k*ôú×žöK§ŒLÃNÅß:+òH³£€`çó	!°ó¬i3	ÎOQŸÀ@!POºB¬@…´`U0Bºvúïñ²eµ goIOÊ2$ÔAÁ`@gOòüK.¼A	o(ÃKÌPó¬üÒ
/xÂ›ÀðKŒ ; žXÐð?\ÏfRÒ—†@%9'ËžzŠ‡
ð“p‘åÈnïÄ[)Y‹c\ã#(‚ˆ …“œØò˜Å@vö&Ü»Z_r×%ÜÞk0å'YÝñÎ^µãÉyÀÊ
Ž#7¼ 
Dˆ»àeÌ–A€go’ú‡²ÿÙ!€:ç¹Ïn¤ ,sIPz×†qBb`Éž¾dK:Ä,+dElwËÆñ<(¸½NÄpÀ…`ò! ‚UŠŸ2›'±Û{ø‹g\ñŒ'Þãß$y# !ãF¨üôåQ	Ís^ž?YñÌ À¾ç(=}êWßú[ú´ïq¯û  †Þû=Ú	¾Þ!>ãC¾Ú‰½Ù«=š‘_
Š(04 
3ˆ>³+’ê<ìK¼X¼Æó¾È»ž!€(¨‚òs®KH?Îc?ÿy?Ñ‹¿›¿ŸS›³?8`½®È¿6Ø? ô?¿È=¬À¼<¾Œ‘â{ƒãÓ‰2@qZñqB\º-ÛÉŠ3ÈA7@
´@Ä2;‰-»¾Í<!°‚*¸¡È¸”3<œ#@ž¼xø¹Š:@'4 
,=ú³Á ž¤»¿|=ì¿Û
B ä=ß3Â¾ÛRÂ¤B†³¤9@)0{+É,;¸@>çr'–»»2d/ z‚²C6S"xC°¯‡; ;`‰«ØÂ@‚N3
žk6ª[ ¨Ã30Ð±¯<yÀ ‹Ã¾6l´Ó¾ ¡)HÁ‚“ :¶RT€`”º&ƒ6i
´j{1–á;®óÅK´1K7R5qÛ·r5°y«5‘“ b¼¼“X:k/q,±aÜ±>‹6@£6BãÃ}d¶ˆ3s45uc·u$7xsGxœµ · GPœÆ)²¾¼3…c8‹º›‚e<	„ÑHÄsF›IyÀ»Ë»x*È~4§ˆ%h#pÄ% ª{‚|Ú'–‚—l9€F#ø2žªp@KjƒÜš 
z4È¯-Ì	õ»È¹cƒ¢\?“±Š9Ô‹:¼C¬ÐC>œ› ˜`Â6ø´¤9AQƒ8H:/`¤ü²Þ´q¤ÙYJ% –\†°ŒÃ²´”AË<Tƒ=d¶.pËãƒ¸|µ¹œ »ÄK½¤Ã9È?¿¤À,
ÙI>l›2ƒ˜Ìš¹-ÇiÄJ&Ê¯2K5hƒàbX…Ýƒ=ÙcM×´ÁZò‹8°D2S…;ÂÐ§–hÂ3LUŒ3¶;“4Å bÉÉâ<ìóJ°“ QÌNT‚¶{‚•¬NŠ;‰ºE˜d8eãG£É¢{;“Ã'}
/”³Gy@„ÄK9M;$<TKÉlKAyKÌ”ËãÌ»¼„7pƒŸx@[’À5È»8ÐY$q²’n’’&ü±7‰ƒ/y8êÜÏ,c
dtO8Eð4Æ#‘Qx‚O%ãºù¼¶„‚t)º7‚2Ð‹¢¸@üJõ<»“ „ÿü¼Å<KMKÈ\KÝÍÌ`ÚÌº|Ð}
… ÕP
½ØD&Ô0PQ:ð=
KQÅ–ŒÑìœÑ ªQþ,¢ì@g´O'Æ¹‚>½Æ÷”ÉÅÔI!˜ƒy8øÉ ÔO¹
OÈÑïËÏs?ø“?ÓDÔD¶Ó
S:˜‘«àÂ
4
hÔGu“L„ƒ8@Õ&ôD–”‚BíJÅPÇ4P*A‚ò‹¥».ÐÌºød²)¨Ç=‰™ÂTCÅSTdIBuVõËQD-!]T€ 0S$ÊüdI7ÈUõ‹CNAOýÃU	
S
õ‹2ÝÐ pÕ˜q 
H„ÔYU…«ÌŠ­TcÄUj-W ÒÆœÒÇŒÌ_
Vßƒ5Ö•ÏdeIäP¿ï<E–t‘m/t# ‚gÔðôr<k=Ö™ôÑ`{*ÐÉ·+I
×%-’I±ØgÕÔö
½Ñ›ÁO]×›©ƒ¤«
ÜÉ-Æl Kô±28¾7ÅÍÆ±ƒ;H¡7åE:øWË[VyhÖ;½Ø<eImÚØ“\»+P’£‚`Yc$Y¯=T”MÔxö ‚(p»žŒY%uÑ0” -(Y8ÐsÝY?¬AŸõŠ¤›¨7íæÌŠ#EÑ7p4È‹ò¡ZóÃZ›U?ÍX½Í¾µs‚LÊhdIÙJ[„‹ÉµÅV<‚|’Ô\LR*èJ–¬Æ¬½YsÕÙ>¤ÁúÕ!J˜`’¡å‘9` ­°ˆ™‹ÍÖÉŠ»x\e½[¸\öB‚1FNÌ¯f³h4§J!–çÅY²äU„õÕÒÕÝë]Æ^
^âMÄÙyƒR^–”\ï|ÖÊYÅøÜØ­VÑXd¥É"è8!pY(€YÖu]ûe‹EèÞÙ…A¿µ]PM½)˜”Šã½¨òyS¡¼èM©®pÍÚ	
7 Ú
F.”Ê‘!3àÌÔ?ßNÈu®Š_ÊÝÎ>	>M[D‚„;iuÞ“åß”Å·}è8’
ªÕ×ºXþ|„îu!X‚a<‚# ƒ4å5"`#¨‚ÚíÙ@üY‘kÔ9øMaßåà|S¦MÚé‰Ç`ßäãá*€‚3[MÙ|Ó—Iá˜ÈŠÇð×å]E¶p
ÐU $x08‚Ç°c(`ÐS«¥îÍÑD^äFîáø2e]–L‚î
Ý8œ0‚âM#0‚' ,¡`ÇÈ3PzÚ9ì‰ebcÉV<dSTeVžWVX~!¨‚'‚ôX'x@‚«8UU0§#Å<ðÜPžQ_nå:xe3 fcVå¡)`fg¶Á¾“i¶ƒj¾ayPÆXv>‚PÎQR~SŽ	TÎfG5ƒè”å eÆ³åÂe–4^ÆæUÆÚ‰No>ædfr~æsejfIy>èTNè}fèb^å)ƒ=0)
ÙÍˆ6çhFçŠfgƒÎß®Dè_^hÛq<}Ò®.£‚’†fi¦fI¾[wæex¶[BfÖîýi'¶Úæ@úµa ¶Î“ÈÈÉ5C4TCµcÉ2]sõ¼\”¹è=½££ÌtÜ1öŠ¡+:7èêŸµ:c:§ËÆ´:¦ƒØ­“ØAfjyØç]àNåYu
ëœxºFsk¬{ºÑëõ<F»£Ñ7cç]6ÉÃƒÅü’EZ´E5ÀE]^,rd’`ë<#Æ:uE–Ä—±{VîlÆµ«-ˆ[³åÏi
€ÃËÑ(Ð¾ûÙÉÍ^ <	2ñdvVkä<–¼‚|l¯­m¯¸m”Ím·^»ž(ÀâÕ6,¾+¸I0(¹/æÜR#hv®ân¯¨=ˆƒ ¸±ÕÍ6ø¥0#ØÂ:ð]–ìðf¯pÌtÌ·.Þ7ÕŽÛ£;‚NÓî* 9ëùÄT\êÃþ‚ú†§"f¾xBîœPn!fîï±´çÞ.éöï4LJì¾š&ðdõmgRyhÞàæ¼ñ.ïóö‘Ž™õžö^	–ov&™ ¼ûFØüÖ·Vëoê& 5·Û.7lŸ$Ø6Tg–ÌúV€7osJï‘~ñŸmo!UÚÉ	5pŒ!+ê»M‰oüvîýn54Á*0ò"±Sì#êª½[ì<<87?ò(í ó –‡îüZ<™!¸{Ýðöì~üÇnHk“ð¸6HgCH.8ÇScHV{·`2·wŒG[«ÈžFØ=¼ke/nHAÈp\îƒLHt¤twsµK‡Èx¤HzdÉZ!C“åÈ½ûkmôGõ‹_WÄ\<‘Ö¶Ú,øul³˜›9®Æ¹¯¾ÝŸk;ØÂiñÝ3×sº†]¤îÊ‰B¡²Þ¹¬˜ Àbõ¦ƒ€õV‚(ðdßjP5k¯È9½ëP}öPwxÏ	q5v.s1øö­÷<£ö60wt¯ò*Pqv×jš{wfŸwgëHxóÎ÷²`ç>7Jñ"	~ïö p·Šq'xƒ'ø'.0ñ€œ¶R_ôk•ÇpNŸ;.à÷Ët›Vƒ”Lš¨‚1»“§mƒ y€÷’/÷sGy«ðù7˜4ÿŠíýx?z’x¥?x<380¢oyo4uFï;›H‘£yRcÉbÅLµäNÃ’*/
.¸7.f‚êæð&Ènqh—v8 xk÷nŽW0à÷~÷v‘ø¤Ÿ{;Š9@ƒ®øzÌL±Wtp,{‚ ´'6—)~_r¡*|¶èÄ7úÅGz¬w|`}ÊûDùÌyÏŸyÐgÉvì›Uláfçöpû›LªüË§ýB‹ùø|R3§`c¶èÔ¯úÕ¿zrw|­ßÍæË	r%{\_þ®L7v~%ñçvêy¿~?yõÞ~: 9aeÉñÜö.}l?ì£&EõCþhK:
éó¨>ö×øÞßÄ›"oÝµ»†gs,ž¼û[/'|÷K<ñµ¾X ÅË)pÙy5ˆ÷À€ÇÐ!ï´qÀ<çöêƒìÅ²>÷·›Ì=oå¿o”üF—ÌS{,	
à¼N£ó^ Ïkz`Bb¯Êw­ŸÉc€=°	Ò¨ `Ø™‚«‰/ä%@¨« ×óz`ÏòÍ¾ H† ER{¡-´=Þ×•àÞ/›{t îéš»—÷®ÛÞpœëïY…À§ÞÌÉ¸v2®¾.è­^û£‚F òI¾œb Ù³}ÌÏ
µ.HúX
ŒƒêOñÁXðšY°Š}gÔ¥ÁS'ÄÎÞíkƒøßÕ9çü:!çÉQ’
RÂ!¸üp_ó»Ï—¹3•6ý>á …;°Ê¿î÷ýTÁ
³Ðd&ÇØ„á$†T "C¶Ãì3dgùÏÖí?–T’ôà…ÓÉÃ¹~ šÃîn2gø¼tR@çA²tè\à :|O&Ðá¡À
¨íÝˆv€PðÝ‰1Cøú‰ÃG]P
2Â¬Gð¢@ŒÈBÂ'/”ÂIó†à¦[ Ôv& ¨Þ0ÄT0ûµ+0„Ž”ˆ¥XQˆª`
]*Ô|
í€Ž¨&ÒÚcg~`$RÃ’Õ[JD\Á¼RXbÄÊE!*&fÄÚ·ENNl5@'Î žhúN‚ø‰‹PŠÁ“X½ôBR\ŠY¡)ÂD–… *ä|‰*Þ€«HjvÚžûßI²3ùà×_5Tz<p"ÂŠ¨Áæ F¼…‘-Ê¼œ¨>â¾Ó‹$q
öÅHŒ`‰.Ñ)¦ÅÂ£[$;ñÊ®Øâct|<P2)‹rÉâ‡2‹/ñÂ`ÆSˆ
7ãTìŒVÑ3²¤j&½b…<P,FÂÊ§Y#Ì¹Œ2qìÆF×nâ[Œ‹n±iEñdìêtüÆî!~EQöªÀó»IPÐmÇfÈ=
p‡{NcÅCD61œ=tŒí;KÆðNàÏ¢€,p]@¢S§ Dˆ…ÐüpB~h÷bPÄ~LOåÑ0P¦ÀFD@
°»âhû>cCDŠ°úAÄ9¯ "S’^A* ™¢kŒ‰1.v¾ÏX +äuäð¯Êù@Ù!ÁÀXw"²5FÈ!¨t"
øŒuñYÝEè¸¿á¤1FÆ
™ dƒ|$2&ÇUøóâŠ,’òHZÁÉ!—ä‡t!²,G´X‘U
8‘<’ vÅ
‰!–tzJ’AÒH/9Ïâk“ÊQG¢HÀ¥£R[ˆEB~i<Àãj@y¤kç1@^2zÈ©€{dgBP>Ã§$ÿ!}lvQâDÀw;™B4„‡ÍãéÆ4Ù"—ž†Ì’nòèÐœ!Ã¤„dIS2@ZHPh
d›”‘/+NŽHyËägÔvC²VI59*_d’”‘H 'X…œb#Á$ŒywÒL²3©¿ŠØ&D“±2²I0a*‰ ª´ª’YÁg	¹¨´–¥I–ÊZ9Ànå\•CpWn@*é­¤4ÄrIKÈ—,¿äœ,‘9rG:Ç¹H×Ä¡¯ÔNü¯S’¸?	 ¥3²„ò°J‚™…\tS”Œ2HÂÇxâ%àÃ
ˆ÷±üÁýXåß¤bIb ZòÅ9,Ñå’LŒÉ!@¼e¿<Œá²O*†^ù(¥¼–'FjI	äGërYÒL(ÉùÞ%^<™rúL{É £ pF“_>I³ÇùœeÄ˜Ó’y¦i$éå¹¤•,ÓeÂL™é$ëd”<“äe^ÉŸi*…f šuÀjªMù/[eØœ—½‘lfK95«¦²¼škSkþË<ÉÎ¦Î,˜ë×ù>]æŠ ™EµFT6˜³‹p‚f;HM×…¤Ï¨fFÑUSTâJe)E—…—b¡Fç©µ¬ó9?Ûlp-k~Æ’8¡cÆó@S`¶u.vöé®‘¡ÛF~&6"Ç¾“ÛTÝ¤SG•ÎÕ
L‘hÍ¦›uÁ³Ð.Qg<g¢lLŽÊ3Ò)¤üÖnØÑCÊ™N"Éºšž&Æ˜Ó;
‡xŠ$v†µêð@))=AÇO™äæ—£bBÀŠU7-FÝô›c1@ä8B« 	V À›kP.¶—ÐÒòá4B~
l=üžÜ`‘Åðñ%ÐÉgèfÌŠ7“-Ü9ýùû c5TP9LÇ6h ¥‚¯)ŠÐ\™4o#;C¡ßÉqŽ9`7¤À°Š%ÉP 
åh¨Ò+1™’??¢w­>c5m<ôÉýÐÊ±œ‘¨i0´H¨‹$ÊA¥¨<‰102¦R(E£M¢XÔ‚â	)¶î§	=	H4‹Š·%êøN£QøeíMÝ	3(EYíuÐ²¾y )PæKê#ßIŒòLQ©/`‚í`EJ§>
£haûˆ¨cxÙ~›pŠªE(J0†€?0âGxS0€Œ«nXßÎÐ†[C”ê`‰;ðä\k‚9’rcDûÈ×>&Ñ1:©§ÁN¿vgv]í$llñ3"9:—§ž#• 
NNÉf9oQæÄl›“¹½ÏÏh§Ðahã€áqHv,NãEXâ€ïÓœ¨E58áRÝ÷
)Ü8ÏvT>¥§Ì«žÐ‘:Ãk•=FÝöDžãhž&D{*>Ò«Ó§±n¥Ocá.E)YMÙMÊxW)=æX+¦¢ÊÝ Ó÷ÉL);ƒ‡è°}z$e’XRI%˜ëc…,JMËK
$~ÕG"äÈè–-5?“qÊCLö™Å¤¦&³
òÏ*vÅ²Ø _,ŒíµzçÒÞ¢ò>à=%Mæ±@Öõ¨ªÚê
AÇZ÷SEÙ%cdBU“q²„H2Ïâsp£¬”²ŒöË‚,óg 
Ý²I¡ãJ{•
 ‚ÆUmÆÍ2ë.Ë¬™I´“FÑÔYäL„UŸÍÕnÖÑÀ™!!7m¢¥³uf¦ò«åpî¶JÏÞ*>¬ÍvÔU ÐdÎ@S«Ð‘(%B—¦ÐøYLëh„U™YÖÄŠY­l-­0M4´PÒnÍi‹•½Ö
ÙË4šo•iS€¦A›vXMZqÍó°mÖ›ÕYùä-UÞP¬zÖð´8&9Ü®æÇ©L˜ð¦ØPRC@²>`ì(j/½¨¿”R¦ÀJLËš1ý5 Uvö#‘ZØXÒ9Œ˜,i1ÎY*ÕØ;[9èq¾ZTwg_3*~¦u¿zÔ´ÖtbgJÖ™ºB9¥! Ä¦•“²Y6ÍÙ‹:'8%§ÎÐœ>Jd®Àå®láŸ¢Cw:P£fìž uK$Ô†dé '¬“HÔ¢ò§ë+ê;ÍŒk±Ç>º„TO5Ê=muùÔ|Î›‡úYªÎ<©ï“%Ièt
ˆïõ$cwÉ¨@UG z8MšgÌÃŠŸ1¬ŽWã†ëŽše³qÀÍ©GMoT…L<voÌF?ëúÑPWÀøO’3Ù­B|vðÙ6ûfÖÑÁƒ‡6Ñ.ÚFûh@¤Õ©Ÿ±,$B¤ê?•* cª½Æ©jTa*rÀB^ÀD†è…¥1šÀGJ
µE ŠEZ0PjY%ë‚jçâtÚ;†hßì"
*§	´W–$QíKª ´Õv±WKa£jNµ–¨ÖÖŽýó8Ð˜ùÚ`àãö: ]a=²“Œß[C›l?­à$¢M '±¤$+V@ªýŸKU€ºZ0k·mè¶´Öõ€Û[Knu­ò¡š` Þâ¤vË¼Þ-mC¶döÓ¾' TÀÊâ©Îe\MÛþÙoYí¿Å¶WÛþ’‚{‰®
·ÀIá~¡À4.¿aYwÏµ'BKqñ¼]¶Ðˆã2ÆäÊoWm ½{Ù¶cÆZÕãmWnJ¸j¬h9,ÐÄ|TÈùŒb.çjœxkq—-Œ¶ˆOèZ[’K@Ÿ*½C¹I×àò p{Æ\®Ó-·Á’°Y¿,IQøPsÀæÒ5b{u…BÅU¶@êèä“`#8áe_ýºþ¶èšÜ£;pSî·e¹j×®1sûn ªºëòÝO{†º.È­¶†·©"Þ`ªxÍ®ÊE»M7×Â\3F¨’ˆ|8¥ÜÝÃ–w_e¡Å¼=w
8×tË’ì€×¥¶ªìÞ±k	îèe¼L·å>Þ§K¬ÌRhr¶íuÝ*³¯JMqîìÕ¹ž†çú]h4iÙÙÇ
º½WäÝÏ|-%·%¾K7íŽ[¶»p
V½c°—Ä·b[}=í²%N |^|Ë{CîÐ½¶bWà–ÝYKzmíñ}¹àC_M¤|(Ïgtrñ÷òî\­ëwMWôH äl.ëÁ’çõ½žàŠ_¹xÍ¯é…¼|ñ.ïç»¢u%qçU¬&Xì1ÕØ™èì¾û÷÷†ßÿ
<Dpé%Àé7©);à~‹hM„˜ÖÏjÚ;‘uû®¢ÀÇ«.ÎÅfƒâÜ“ÓüX<k”¸ÀØª`“½¶ ‰C~Icø²<"Ú!wåØÏ+w-àL›Ïfã¡ êhe–%¡-ôÄ’&MJ´ÚÊX2ë
àw…wäßLÆòKˆgƒá¦(!?lIïa9~ÅÒ8©)z8kòá7»¨Š ÀO`§²¤š1÷­÷å¿a×è†Þ²Ë%qî	MøíNâƒK ¶¡î®U˜ÇŒ©£öðM
ÅÚêíÔ@[cO(Ä¬øàD,U1vÄµ8(Db¸«š†.(/0	Md«Ø™ìUÅÐKz•êÅ¬×íÅ^Ó¦dò;Gh†Ëðnro ‰ÂMãYüˆ¯ñ-ö
Ýx;^m¬	¾±ëÇÐÑ»fahõ‹ƒÎ¨£OèÈ‰#¦'Þ| Hùa@|ŠÙÙ¸¬†üBã“«ƒGÀê £à5Î;>øqþ©Äºeòâvâ‹ïp0¶©x¢ë¤Q\Š52t$ÄGÕ{à’
‚‘.I&&¹* d•,‰ûq.ÞÅŸñ k­hÅÎ$òO¥ÈË";?|Œñ4ºTù'b’1Q6Ê®'%cãI¼š¸±7n½á&75_\H¡#Æ>0¸–²¤ ¬ÆÌ
0ãG²fñÐÚð¯À*·ÖL“¦SvE":ZS_Yb'ÛåD±ÝTÅ>:Ïi˜A;#mñðËj¼ãB”eghVøÅ¶x’ìËn"Ã¸í •„·òÜ ÙP™^
a#`_æ˜°xêWÁŒ˜5l2u8&·yØ'û‹¸æi¾F@’š2„}psEåË7 ˜Š±kaƒóÕq:Ï(êF0U0ã <}toYˆWÔM­°sÁN¾rÀZ»,ƒ¡#(
|¨*%™dç÷Ø°Åâ<ºÏxÑLÒ®Z¾
?+¬QÕèø³bNž¯™ªiÍ˜4•RX¨Gèˆt2¸€	&À
lèð¡M 
ðWgý‚gñlKP]y¾5v©E—[CÞÌ[	UÆò€M\£›õÁ;»•G—!É¬M1g.J±œ3ºÜ†;§x5FVÆ°¤	P„€¸á1Ú¤è!uÔ@ñQ'ÔÔgø‘-¥ôZZ<Xzv(-ßbò2I·3
æù§”×Ç§åôœ¦ÓuÚNßi<§õôžæÓ}ÚOÿi@¨õ &Ô…ÚPjD ŽJ  ¿A†Ô¦’)pˆc;$5L•¨ñC¬Š¦Æ*„…«|ê­ª“ˆWÕ¤zTƒêS]ªQµ©fÕ«ÚU«jXªeu«ŽÕ´zV¿ê[]«qµ­æÕ»ÚWëj`«…u¯ÖÄzXÿêc]¬‘µ±fÖËÚY+kh¬¥u³ŽÖÔzZ?ëk]­±µµæÖÛÚ[kkp­Åu·×äz\ës]®Ñµ¹^"`Å‰Äi±‘¨åõ¼¦×õÚ^ßk|¯õõ¾æ×ýÚ_ÿk€°öeˆñ1X
Ká C¢˜kdˆ¤„ ^‘ zE[à=Â* KD€ƒ!€„‡mI @,g‘ÔEÒ±#ÂÇ&d#²œ„=RoVpö 8Ôƒ0€6 -¤Ä´Ï
B‡Ex-tÍ4'9ÕÁ:f-1^UH—ð’Þñ;~Ö§y3ûËÕh«‹A›IõPCÙÃj{ð!>ÈÇ24/}œYþ‘³˜ A»g€$¢+E¡²BÀKæYï «-M0É«éÒ5z²|pFBLˆ;žY~zQ7ê‹ð¨÷
¹’ú©Œ†¨bD Ô¸*¶¡ro‡¬r©Ãu»FÙïú±lQEœd8µ!ìð,&·€ 1€l„@Ú€èN¹€Ðn
ZÀ´ Ø;n`l¿²¸íF¸h$¨:7Œì’½N¶ð^$Å[TÏi¶‘h ÛÈÓ[u§…\pp÷ØÈÚûz;…Px·µÈ+vDð•Œí~A¸`(M¡€„r¼ÙÂŽî@à 8€¼ÅÁãfÞžÛpœÁÁ¾ÂûfÜ-5DéGòXÅŽàMA1ìè¡p¤¦ð:‰ßâ@        (àA„       2   
   bÀÙw¢« ÓöÐÒÂòkÕ‡O¨^hÒøÿý[«m9ô&ö
YàF˜éºJ·¢#è                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\FabriqueClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 /½ñ«Û÷Ã%†ŽH<øš÷³;”àœ•¨ƒú‡§m5öžŸ     "      Ä
      F a b r i q u e C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                  9                €    ¼   01F280051n     p       %      >        
                                                                       ÿÎˆñ   FabriqueClient þí˜á                                /ž­*Z§Áñ}ö$nÆUÃ'ùg¾HºC¨øùè9Æ*œ†o5–zÓsÖÎ1éÇ£¸É      œ(    
dP$Å@ÿz(è$)Ú…‘b 
YÔ`1@E‹" ø,‘¼ˆ3G;fˆ´QcæMfèà´óÆLˆ™bèÐ1àŸ»Lâ<.H€)S’&åÁˆ¨€œ7;ÅÜüéDLž™5oÒ™b†Œ™8uÌØ™ÂGF‘*}j1êÉU¯f­³Um*pÈ°1SÇÈU;ßŽ …#UÀ!ÅJ##ä&­k·äÉ z±jå
X0á:BÈÀTCGM5XÝ†`ì²d!D¤XQBEI%Pžh>Ê¹sÓÏò,TEÓÎ1mŠ§{ü®<U)3G'Q%QrîA&5>g¡cÆ
7r¼ˆ?Fâs#—!â™ô6«®3§ö
à:"ŠŽ9{Ã7ÆãI=bTyÐ
ç6¬4ð:ªŠƒÁ:ªðÎ5Ú˜M†<Üï:"‚FlÐ7
S‹­gpÑºä4hÆI,ë¬´Öjë-â¢îÃ E$Q5Ö\ãðeø‘Àäb¨È…ˆ#šÈØ0¶7|`±Å'_LÎ¿ |cËº
tSª¼:ŽÄdÂÊ§<Ð4âB	Cèé§
/ÌðÌÛRª”†¤q/@Ñ4q­DkÇ9Ô¤ó©ëTqÒ?Í«:NLG•ôÁGG¹”ªÑÞ¨+*Q´MœÔïÎ“P¨õÖ›Tmã	¬”(Ã¦:|¨áS¨’ë`XSÑ3²‹µÎ7ÿ
p[P
w\rË5÷Üs?ˆ¤Ð
@Ý‹âuw 8©³{í(_~÷õw ~þW_
&øà€8á„Ÿj``‹´ür^ŠCªøbŒ3n·"
ôY÷cv
¶Ó:ƒ·]yÍ=™ÝÝ­7ß§fêaƒ#®ªdtafÊ€ÿ"Jn¹Ž°â	"€N@î÷ŒãÈYà9p¾8H¸ÉÄéÎ~¸"	Æý ‚ä°ˆ:€ÒV;íýh>Øæ‰­Ö˜Ÿ¥êh "FKÕ28EÚò!‡.È—îšP £AîYqò½î„ˆâ˜C°
Ë`ë§¼ùšƒoNS\¨Ä}\îÈ•ˆˆg‹**#¢×¨êéG vÝf7ß°Ë‚ˆVþ]áSvb¦>Îm ’rÔÇmþ\1z Hˆ yªåt ÆŒM¸ jÖÙ$òÍ?ŒsÔ )–_FàDüx^‘_üXŽ'|îÍ»ì³Íemkkòœw³è]lZIN"Rì­oJBZ÷î!ùZàÛ 
Âq™ÈÀ®c‚Ê]Ž™Û\ ¸“ÏM-¢ƒÖRN®.E ªë>»  qz´ËÞíP Ý
¬wøŠðR¼#ªlxQ<žì"·°å0 Ï[\÷3=sÝï}úãÆò‹ÀBÍð½í1¥
ß+c Â¡÷
äŽy:Ü÷?©˜-"h Û²hE.z‘bnK"BïÈ„&ÌBš€ Aƒ†‹ó<‚ Œp£‹^Š(-ÆŸ“ˆ!…˜S…æ¼‚æ<':œ‹dX˜•á$‚†Â³j2‡„€§)L¡ÌƒŸ¨ ;ly“À¦05ôÈ
£%G„(ÎÁR.©é©´èKf¢Cî,èFzA.sÕš× I;øæH’µ/) 	p@Ì0‡\áŸc€QR0!´Á9fºRÞ¹+¬PA
hxCtF0º_=ê$\ÐŽÈP¨‡Æ3¢­hž‰“2è¡¢nxILfçÄ¤-!½’D)“Òt6LåòéÑ˜©¤ÑDÍl†)Í:Ô•ò AGÉÐNµ˜oñæN«™œ8õjÀ–TëpR%¼ápHÁQQL¯rÇ;C1F£Qy„æ§O}ÜP…4xGeðcHš¢	#`PÕPŒ`°…}*W•z äl@;S0Â® -a
!ðèc¤¡˜êt„ Ú K¡¨
óôlrò¤8}òÓŸ èjP„*T0ŒÂRjS‚)À 3n·¤‚íD2B»ô­I'y“¤t¥-ýÉ[`Ê§† ¹9%.”¤ò¬|*÷uÐCÌàƒßë¤/0²Þ ,_h:•ÕI^3˜Á
AÝJt¼jTb&Õªà<	
ñ«ßÓÖá¿T%Qk¥òƒäæw¿\õWÁ*V²šµ0h
°Z¿ÓV Ÿ¤ƒ
P e-‹YÍ†@¹!–‡8GNÑ¶!$”iÝ9¦2ñ–µ¶çƒµ³Ï~þ3 -¨šÐ…òÖ¡8VCÑTžçÖá¢,ŽBGÇR&;+'®JÅÀR˜T—³º•é’ÉÔdÞr—ÅÂúé˜ø;Ô ©,¦U›ã`¯*€Á
b1¬|g®ÎÆÂQëXË`#ÕÃlí.°ä8Øšø²™Ý¬ÆÄb'ÂÓÚaq6'„ËP(B Â¦óÉâzA	Oð¬é"ï¼ápÐCv¤ÐÇ*R×»¶ÚWç×ÁN‹D´î:9EÞ}±‰Á›bÿÄEg—ëe¾Ã¢Á”gíj+ÛÛÖv·¯íílŒÇF$y­Ãs§[Ýë.W•qæÃ§ˆo)€<‰  BÈBÐŠ}07'˜ÒÁP,9™vB•©“/O.%‘ñ÷R ®nFJ¥©x«e©Nõ!Ø
ÿ¼©Ã V2!AØ$Â‘ J
€fl.å—ê†U®°•-DB 0!4Á@©¸­ª…+s\I) ¯y
µ§>uŽX5vƒ2™éL‹£é¤^mµwñJ
ê>¿øÔã,Ý/S·êŽöô6eSôù¾µ¾ò ç—ˆ@ê ]-Jÿù˜Ô¨ãj5ºéœx¼Ô#üX¶B®ímœÛ$7”RuO¦ÚX¬Lùï•
´“uë’9»=qèÞáy%º_\§ô5îIœpù?á9Îo¹:WÒê®—´ww§ç!ú†FÃu¨·Õ
M¯n|_R^ï’0¯÷÷f‹å
[J½ YnX°ñY‚ü˜¬7­¶ï»óyÿ†Ôõ®y5Ã^×S‡4„ÞZÜ¿a£XCšÄÑ‚jË0zµ—^oœØä¯ƒÏ
-¹ó®vZ¼‹k<Õr/øº	
¼|²Ù2Û*²#Ó--ÃDLÈ{É#=PIÉ'Ì³˜Ú¼ÞR¯¼{ü+®”Š$ø>ÕCªÙh=;¾Ø‹²˜=vÊ¥æÊ½µÛ=L½å
>¡+>L>\¾æó¿äÀž"ì½é«¾%Ì>ôªL¬É˜‚üÈ¿<‰¶ƒ¾Ô?¼Ò«28¿ôË»ær¿Ôƒ?(”
ù³ŠÔ«¿¹?!Ô?þƒœ8<‰Àr@õÃŠ»Û@\¹´>”‡¼ £-"Ã-$Û­†²´8DÌÃ0œŠï{)Ík
Î»K|A«“‡Ó›BðË¥êÃÁ¸BI‹:èAûÁ)É2c1"$ÃÞ;Â >âÓ3ëƒ=&tEßëŽ{B¼¦“¸ÅùK½*\½_Ä¾Vd–ÐBÄP
.‚(ˆ‚/„Ad” Ë3Åð³«3,¿4\ô»–k?x¿×IÄ9¤?ûEGË4c1 ú1HŒW"'zJD"`ÄÙ2‚98€:—¨ƒ& ©Ã«‚òú‰e)ô ;A0ìF)„-#È$¹’"¨2Ž70#“†4º‡4D,™È?©HLìÆT[µ±ƒ+ÉˆX›µZk
J©IvûI ä5_ë!`
"a³"b3¢ÜA¶ˆX¢ºøf“—hÛh»˜ic"n·pÛÊ¬äJpëJ°üJ±ÔJ+²ˆ„óˆ…ûÉq
J¶lËŠ‘„TPˆ6p1¦èO #ñ	‡oà#½ä£0(>zŠ0¸1ˆüÙyóz“ {B2¤®Ñ¢_0·¤ZŠL
¸yIŽ
ˆˆ‚s€è €0êXÊr‹› ¨L¸L9K¸·\ŠÄL¦ˆY(Ì 8Ì:"hH…VÈª„)H7§YÌÆÄ·Ò7ˆÉ4Í)hžoD"“ŠÍˆÎüÌÐÒLÎ-2·æ\Šçt›‹aÍˆHË×Ì—	Ð"s™žé„Î²¬›“‘ ¹>ê‚ ær"²ˆµ	        (àA„       2   
   DÁÅÜl“VÜõÅ«¹
dìÍ›¢Å5µR¢f0ý-íýVçA¥ÚÅG´½Æ5                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\FabriqueDeSignal.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 •Wû
¥j„m‰I‹øVƒp¶ÚËŒq¼ÏÝ=fˆ     "      
	      F a b r i q u e D e S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS                             9                €    ¼   01F280051n     r       i       >        
                                                                       ÿÎˆñ   FabriqueDeSignal þí˜á                                /¸­è.Ÿ_r.ð%ÍÀÀt9i Ë•Yõ^f®)ð¶š]Úa¸“©Áà+èŠ	Pé|¼ô    Z   ô    
d
Ã@<R6,µÁÒ…‘b 
j,`1@E‹  ø,‘¼ˆ3G;fŠ˜¡¢Í1mB˜¡S'„7fBi#†3þ¹Ë$ÎãÓ‘P¡’4)/BÄ3jÞ(eêTªE_ÅŽ%ëñCDËJ={ÑíÚ¯W ŠüJ×nDºRà…*#¢¸ È
·bE
úÐ.N;7bØ² On«öíàÊiO(—ðØ»b÷:~êw `Î©¡~Š kÄ’'-¨´ò„H%PžpmÊàÜ3cÉ4–¶iøÀ8 °\9>ÐEñæA"
Ó<–	bÕA“D½y(DÞAÆ™®}`´?â «¤>@ŒDîÃ)’OHü?±å9°¸´)@ ¬Ñ>+í/Õ„‹ØªÒ ""àx£§9ì £3ì˜ƒ7qð¤„8`È¡
èb4-D$ÉNóˆ.ªN:!¢8æ€ƒbT)CC¡"œ°Ž
/ÌpŽTà0²µZTp-”ˆÈ **#¢)Q´(ˆéG ´
L‹.	È¢
‚ˆ2[Œ33/KÍ3Ö^äËJ¨
lÓ´ P[Rµ6Éjn (8i®@‹"€ä›š‹¦„>÷ŒˆˆB™„Ðæâ1!ÑÍ|ïHAŠ€	JŸ²3ŒB2(Ný ?ýø‹È?qÀ7=¢ÓÎ;kµ¨ƒOÊ ¢™(´C
9d ¥è`Ä‡èÂN,„E&Š5FyL ÑFuä1„^üuÈ"$KÉ¯ÈµUª&ŸŒ2€*ÙõÊ,·´, /c
´ˆÈ(Íy1;³ß5ß]-Ö<#’³/Ï¬`±%AEŸJE‹‡›Ë'‹Š+µ(’-2(Š!Ö8¢4¸ð8€\:Y¬(º89“EFµ*U bÕÕ a…s YV¸@ýD€p3Ì¨b1ÔÀÐ
3Þ¨£¦›rö
Èò Ù
Ø5
è é \
é1ýª¸öÆw$:„8ì£—b
6à#§‰6i¥Í`Úi¨qj£ÈvA‰Às::é¥›öIÜ±Ìªìˆ€dÜoÇwêéðÄÛX¼ï¿}*CœÜˆ[(BÈ©î˜æ¡óÏw:\V‘ŒL¿""ú;àÐÃ×- ÝqÈÅ’Üg¸ÒJ‹¤¤Rà°HK.µ~
L1ó-óß‘Ëâ·ûÀØÄÅÐ,e˜^èèùÀÎçLQ²hNÀ³àgS Ê‚€ä IÀš%CøkN.2Ð¿ùèIÇ<’˜ ÿ
¾€z<Pûˆßpfv’šqgËÁä"öÝ	hU±Ö@ˆ 7#¼hhP…0 8›NXZ€Ìr‘‹ô‚Dt1aD¸ö0Ïˆn‰~¸p¶l©M(m{ÛéæV·»µ!oE‹ák¤>-‡9!œá7Æ6tÑ4´aèŠW®$é 
º3à0‡2hÎ'S#ÁºŒ®tVLÝêàÐº×¡±_$Zèh×ÛK?Ë
ÀŒð„+@A
F˜BˆfG<¾1…bQ^ ”x‘vµ«MÒ
 õæU/Ý‹”ÚÛ÷Ró½%‰o–ä#X	Ó·¾QfïNŠ‚ÿ¨ˆJTÅŠ± 0jT*H¢á„gVŠ.1ÀT5G…lz*" Zf,ñS}p?ýéÊ¢3KY˜ÁLÍ
OR™­WtÐ
MÐ(,@æÃÐÅ„ÀdY/žàÚ’6&€ZN€bÚ¶EE¸ÁD(t³Þ†¢·}ö³seäCQN%ŽUqÒ@ÊÀ~¾A‰ŒZN*AŠÁt
A!ÛÀ:àÑTp7}dS"¹IFÄ’˜Ô$'	S­¤”ž/j©)×•Êô±Ò•]úöÆ 
âM¶T.ëÒK^Šæ—	›êY‡Ù˜`ó+Ùˆ†È`±qÄšIˆ ×tC¯•êÈ@Rá‡¿Zì Â«TRÁ†Ä‚$Ð¬:Cx³ÂsNlmë`ì)|ê9!ƒ´Ø9a2DÈ.ð @#kÇÜFØð5ˆŽ­‰U‘BEµµ¶(XaMP‚¬`„ ¡	P‚š€Ü|ša´m(íiÑHR% 'uPƒ„z5ÝêÒAª9yõ3…ˆ´¦Êv“»Ýî¾á»ëmI:Ru?=dPµÛ“øÎ—º¦¥CQ½¹•žÄJ½d&7ÙIõX‹ãKg™TÕSª«;»`¼ªGÛëED–nëöÞ¾±ˆ5AfEZÍ—>‹Qžœµ°nÏ¥((ä}OQ¦7ßS‡CSšÌ¤fsÂ‰ k3"Ü\füdq„œ>À$’lYvþ'³¾äð<k¼–ÜYD¼¹#C à 7 PC Ú 1 9óVÆ€ w~ÁA l €?L¡P„ Ø! RG ’’»9ŸyÎq~ó£Ý•”;Çy+”€ Þ¤`yUíÜ²Z»Lã/·x €±`ÚT…º/zPP@         (àA„       2   
   ×°÷¯e”­"ÕìÎÔÀðmã
U›Å|BìÙ<3M¥c7ö$ôôoÞHžÓÌxåLU¦                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\GestionDesCalculs.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Ê2z®üw«–?öæ	Ú•Ñý9gd0w™b¦ ¼Fž     "      .
      G e s t i o n D e s C a l c u l s                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          a                  9                €    ¼   01F280051n     s       ,      >        
   
`                                                                ÿÎˆñ   GestionDesCalculs þí˜á                                /†¯1<R+CAdïWIK‘”¡:³ë`¼¸¿öÎ>ízÉvT¢où&¬•0ËÏ­(      '    
dÀNÃ@4%è‡Ô…‘b 
*8`1ÀÆˆ" ø,‘<‘˜¡SG
œ7EXÓ†Œ6tB°¬ÂÎ3!ˆ´C‡Ž™ ÿÜeçÑé@’&å¡ˆ¨ ™9jêU²²åË˜3kÞÌ™tiÓ§£žQõ
7UÒÌ1#¦ÌÎ–!È¤£¨Y¦iÕ–<É¢ª˜9tëÈt#æMbªðöüb8m"›y­`¨„åEˆxÆ¯gÐ#S¯fÝÚã‡ˆ AºŽûâmÚOI	ºw€ßÁC Ý€¸GUs/oý›¹ÇŠ-è‹]}6pÕ¬	<gnûcõçÞes·¸»ïãió~š| òñ-:Úqà™ˆ¢-D<bå	‘*”€â	Ï¸Ç5üÈë‚36!c£0~Y'“  ŠpÂ
Ç¯sj–8É0–#ÂqÐÃç²Éä,2œ'Nz  Í‹”b
 {äqµõÎCN9ùŠƒü¤:h "`ji;ÈØÊŽ9
c£„:aÈ¡
~C’=‹à³ Þ®ƒo¾‘D;!¢8æ€ƒŒÈÊ 2¨&ßx2Ê)çHAÔVÛÎÈÔP"" 
 ŒˆÓ£"
¤@
¤ß$Xí7jï»ð6Eq9óèsJ¸âÒ‹È½ Îô9QËp G,ñD#qx
‰TªéŒ$-9`Ö Â˜¦C
¥p£pÞé°Æs<ËG4u TU]uÛ’<)ƒˆdºSJ3¨,p,#ðdË‡~ëvQ‰0B¥·?#šè¸µä1M7áTENºB·(É¥rÏ>SË—ª¨P7j*"2‚íÐ*…ÞŠk®ºÊàSÇÏ@«w[ßEÈPE½6€F z4Òˆ&=ÎÒ@#Êt ñ6].gÜÈ
µæE[µ(ÈV³%™9¡S{5 d5Ä¥épzi:YbÝ(š[¬ˆD—&Š§ÜHe e$¦pä@
!¢¶ •
Çˆ@¦¶Gká=•È£
R4Â5£±ÄÌ‚9,¦C+°
Ã¾„>a·Ë˜ME #3

àæˆ.G34©¤ˆB˜ÂÈT1í
Æa
¡Œ ˆ
Á
ŠÓ€ö”pâšª˜£¯­Üà¬ŽCŒ®ÃWcq—`JXdÑl(Š)  ‚EŠ°b
#Bxðã
G\ñÖßØ3„.Ël³Î@Ö®µ|Çˆ¨	8Ð@«®Àê²£R ‚/b¼(uºÓÅF`¼Â…@ã[^ùB 8P)m
Á€7¸á…@
eð	d0‚ oƒ*øØYÞ§Ñ "‹CŸÆäB»ø`#P`à0 :<O0ùBU2—v,>
 hVÌ€uHƒï.(¼7ÔÁƒ ÜÁu˜–|‰î=ô³þô7 þ™ÁF “”X‡Œ ‰PÒJ,¨FQƒœb«è±ÆO¢iBUv±6a›Á˜
9f—,Žwmp‚cì`†6$òa€<Vh§ŠÄ1uÃý„¥Ÿä1d;yVwÅ§äëmÙAø¾ÇDÆ gƒò€J§ä‹ùcMœp;5˜FŒ?ÜË ÉEVr—%
¥’†ˆÑ.FÈCdn²•BñÇd¢1ŸpÃÆDr d¨IÆ°@6Hó˜"\'1ÕÂ=ùà ®4^,UÑL?ÁO4ˆ]Þp 8è+!¸ÃïÔ€Ë¾%”6‚"Êµ²–ù
Rœ
€ÌE3ô„s<£èÎ<U¤ŸeTH¤éqHzR“®lh+3šB›ãR˜Æ45aH…ÖÀ5bÉ 
˜B À6m¨‚> ävº
ÄnwHÞ„d‘–Êô9“Šw„P¸­$|ÍkßY` Í%“û
Ñ<r9
üâO÷ˆçð%š(Œ®t§KÝêˆ;Ù]’‰·ãJ/ÛàD8B‘xWUŒø”Ç¼Æ¹oRéÕôªw½ìm¯{! ¬²Ê@ÄÂä|éÃŒfTÁ„ZddÐ“JæW¿ûÍ!ûƒCÿþ'† –ÑNgLce·¢@Ì:‚¤ 
¼n°ƒlÁIÚì,ö$«@+±ZÏÔù  }A.èN²Ë†MÒ—Àf6‹Ù1d
v¸Vê€O…‰&
±­ÔI*UA
Â‹ ãùu“htç6UàƒnvÌoh•èP\C½"Ó§Tø9î=á
PÐ‚¦‚+´ºR]COf‘Š¤,e­ŠèË(jQ!aT0˜Òn>ÅŽ¾˜6!]qJƒfã’êÍ)fuJT9<ª/'­x 2”a9C “S‚† »æ p@g^ "€ÊmHŠæ&­1Õ©­òßìã’>§”é~à™²²ÔÌOI3‡©zˆL1bÁ2¸áO		Y+§7´ÎKgEsš¤’¹šu‘±+W_;1Ì®v|Í_
Ü(¾ 
{î3]6œ/>9ÖzØÓ÷B0êß•ZŒšŒ5¼ÃŸD§í¢j¿Æ1Âöˆk´G k>[6`
<¬n#8‡	Vð·qì‰p=Â8tp œ#¶Imë[c1Êt6Y 4WCÔQ•Ô¦*u)›µ˜¢2NMŒAªR£´T:€ÇýÔ \¦G•GR=ÃÔ¦¦†ÇCzÀ¹Ã†ÔDâ!Ð„×<bã$ Uw
<Bpƒ/õnc~J™>3àËYó@ÚüfÅ™ßQM9Žcú›	à{¡O©À¡Wž–ˆZÄG        (àA„       2   
   ã”Ë³€YA€"@dlñ_á4l=™æ('A÷£bˆˆ˜Ër4ªÇØtépqš                                     O   O   ÿÎˆñK   C:\Mes Projets\repo\avsp_server\src\TEST_GestionDesCalculs.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\IAcquisitionSignal.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Æñ&ùªj@2û>.’eT†¢6!2K;æÍÓ—««/     "      Œ       I A c q u i s i t i o n S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          n                  9                €    ¼   01F280051n     t       é      >        
                                                                       ÿÎˆñ   IAcquisitionSignal þí˜á                                /ì=ÖüÈòpûóÂiª£XþîÑúVšâŽÔ*²ˆÊM'Ò¦fé*õÑvOƒRÉ    Ú      
d€íÂ@h¡XeˆÓÁ‘b 
é¬`1@E‹" ø,‘¼•!#ÇŽ:jê¨ó†Š4oÄ´	a†NvÞ˜	A¤:tÌøç.“8Q ’4)DDiˆ©š#“¨£jÌ¼©C6fê0u
UªGª'}`=£¦™!vÎœ13§çÏ CCÔ½¢Q*uæ€)"¤Š0,]Â”Ió
Û§oá–<ù +f·š§r–w+(O¨<~RÅHˆ›{Ì€qB	³hÕ‚™’%2)VBøA#ÆqÐ¢G’–r:õj!­_Y
æ±’&MŒà®2Å
‘*ÃC¤À±"E
*X¤ˆ=»öíÜiëðö-¸ó3Ò'W ®¼"ªë²¦2ë€þF«Š†ˆ[¬±Ç"ké¥˜fªé/ „"ê§9ì £;æXªÀÐDûo¬è0‚,µˆò)CÁÈHC5£#ÄâøSî¿°jÏAz1°žÈË¯ýªAGIC+%Þ8,‰k¦¾BH2„%ILðD¬¢€‚
0ŒpÂˆð†ªH,‘Ä±Ë¶¾$­Ç“Ì)Œ Âˆ(ÒlÍ+ÔòM&5ûÏ¬òÚ«/0b’­J@ÌrK8
Ü‘4*°ªCŒ1åË/5ßaÓ1îÃŽÁÔÈƒ¨Ã`SC6ÚlÃí,ùèû-86Ýr†BßúïÉ:U”Òg¬qCôò@ÒXÀ
Š0CFµˆ%JFmÄQ†^¥ú/ÀŽPâ‰n£B0AtÓUw]v×ý ">jw w/²wÞˆÀ
@$ÑøÝ7"xà.˜àV8a†
nX¹¶ˆÛ:ñµ8*/Öxã·*ªÈ}à9Þ…Ï=ãë•÷^vUŽ—d|õÍX*‡ßŠxá‰?Cß™£:@ÀˆH³ ¢#¬x<%R{Ò=cg¨£ŽúHsÀ"Òzk­û»™áœ+–zg‚®Jƒˆˆ¨‰Ã Íqi?@J¨†ºÀß²q@Œá×d±þï„ˆâ˜ã¬­Ê‘(µß`ûC·çHA…ráBð±1V"¢,ª¨Œˆ@×{ "
¤âý:^%èÏ_"rYäygg9f‰iF¸g¾æâ½3W—wá‹7>Aª­Æº-®¹ö:÷€çûx‹;0û¤"’öñ:"_º¹Cq¥î‡üµ^o,äo&œ4
?œŒÄa{ÅÚqòÊ-ÂÜPê…dséœÈD€ò®t9]ê"²:„¹N9°“íVÖ.Û]Ðb2›àî ½ H/€ýa	M8äUåjÉZóºöÁÑ…Pg'L××Hƒ¶¬ˆ0xÑË§–6
ÈBÀ P Š»=0wÀ.Øg&†d9Uá‚ü§
Å‰(œâºÚ…‡‹’Üô‚•Lˆ2²	NtÒ†8ˆ1ŽŒd(T™šPnY;ºœ	0À ð" L T€^‚
“ 	 ;zYfêÊà$ÛÅAG>L4¾“Ši(ÅO†2s)<É
QÈ<äy1ô¤(a€jÏ;ôT_–&
ÀáE@
Ž˜ÄpŠ¹Å#Å÷Ue
V¤íÇEÅx‘(Ò¢eû2¹ì v\£en’“ÄQ1sŒ7+d™þ¤%ü#ç<ò¹Ðð‰T[÷ºˆ@2 •Ä?5†IÍ8ŒxJdåC9PW&´]¤”‡)A“JU¾òwUhTlXD¤
4jÃ]ªIÆ¥U$ D(à†AŒ˜æ11¢	c™%#Í\b¸+f‘(Ðœƒ4C°Q»xTŒµ”œ
²™+[îÑDëa;	øNÆ3†óD"í‘F4Ÿ¼—%ÑåÏ‹ô-•h')JC„VT­ýa¨C[èÂU‚-"­\«+³PW©´uyOh\y×‡fÕƒ#>Ðø«¥a
Þ@»°´BÍ_ !»x×‘`V6*‡´×        (àA„       2   
   dÎãa©&ÑðÒÐÔÜAqŒÞçàò9œ¯ñÁGSÒø[âDš×ÈD¹ !ê                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\IClientSocket.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ²µ'²¦±.ÝZØÍB>+ÀµqÜÖœÝýCç‚ß ~J     "      ñ      I C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          i                  9                €    ¼   01F280051n     o       S
      >        
                                                                       ÿÎˆñ   IClientSocket þí˜á                                /˜/ºGýÐOÿÁ›ÃÄËÒ!ú¬öZ|§{¡IÑ,÷~Š¹L¸ÝV?£€ìÃÜ­7l    D
  ¸6    
dÐçÆ@P«€(Õ®Ò…‘b 
)
„`1@E‹" ø,‘<•i£ÆÌ›:Tàac¦N3tnÚyc&K1tè˜	ðÏ]&q•$iR‡ˆ
Ô”Á©'L—sŠMºÔcÓ“)¢’óFL™4mVÝÙ3™4bÔôÜŠÔë×’'ODgNÌ™5oæl{µNÖº]ï2Í+/DT5qØ†àé®b¯èµ»˜±SQßÀqS%Í3b¨¦ìöræÍ\=>é"j3sÞduB ‘7wàèñÉºòÛ¸s‰rV¼¬<·sïÎÝÍ3dìL>~YyâÙž?ˆ
~ösˆÄ™§Ž4f–Ë>ß¸ETbæ ®SÄŒ›²Ê FîÞa8àhcÀ—Ìóì¹ð«ã´ÔÊˆB;†ê­@Ã²
Á‡dpÐ¹Æ@ÀÏ
;ÞËc2ïbëìÁÆ"ˆèŒ¹F¼‹€ðrÔqGwü ">ê1¢/2rH‹dH¤Å˜ÀI(#ŠrI)«¤òÊ'­ÌK¯ÐÒ"ÕCrL¯œ$óL+ªÈ}€t3H,qON4Ã+RÈ#{¼3H8ÇTrË»¦¼ËK.#
3 êDÓÌ¥˜1¢Æ,ˆè+ž ¢
% x"1îÑñECuÔ¥þ 1G#b V[eu6B³<RG…j Æ4ˆˆ8ÞÐi;ÈxÏ­˜c 9†!‡.pòVYÅO<J4¤‘Ûk öf°b}âÕ×:€ÖbSH‰¹9s|ƒˆv˜êÃN·½zŽ†xG+BÕèÁ4ÔT[©¥— S«ŽX¸7ß¥è¬uG”ˆ¨Z‹*Âw £(ˆéG  c
ÒI	fs²£ötI—óôóË¥µÙ³XèÐk'ÖQgŸƒºNSQµhUW[…•æ~¡5 –: ¢Èl¬‚ˆœˆÂz â‡ÄJf: ž‰”z$Qs^ªl¦<zè :€Ô©"òï×`‡-–+JIv•a˜}ÈI¹
È‚/@šÚ¢‰´|Î„ˆº%ãÛpC¸»Ü¼ÑC]ˆ•’˜D§FÈ:E3ò(b@ÌÕ¨#…PTq]ú`-"*Ô@Ã¬6NW%õÕwð—à
aô“Žˆ¨	8ÐˆoŽ®È
Á¡RAÀÝU=„ƒ]‚)-ÁZÁõÒ}!Bhô<„oîF¸y¬øb4Ž¨c AHd’#2YË”ÝŽˆå ÄOC`Ÿô§·iéfXJà@Ô¦¶µ/P¤`yT4§¤j HK@–V¨ôái=óŠyÔ j-1Nz G"HÂ¥˜n±ŠTDŒ !K
 2Ö8@’bÎYú£0‚&U-"FÄmäƒÈµgrª jBp%L¡
V0Bš`„Ü°9<ÌÔe¾%hAªh»Îã®Æì "¨	Îprà©ÆuCï~ °2¨À *X€Ã+bÑ¡s_±`H,6Œ¹©cô[Šý€¿îKýkÒÿÌv$Úée$ýÇÀ±Y¤…³S#=Ò@WÆ’T<‰ÓÁWáll”¥.qå”Â@ŒBØMü 7à†SÔ I'ô1€!
Ð¢‹ˆ‘l$Ñ#Lt\cÀÆ-)RÎŠXÔ"½Æa³?ÿ	Î¸ƒ4*ˆAoX¤E>w£Æ”`Ž/qæ`>ýðç˜P=ÑË÷A2~˜äØ,‰I±	e*ódËFÊÙ°N¦ì$*Aè‘Uêi²„e/Mz&ZÊÃ–LÚ =F¶‘žt)4tÊc2Ðbp ÷lÂ©S01!Å"
¯i0,®náfœEpz‹Š•»ƒþb 3´¡
>Á)jtÊÓ÷øtuêk×œcÓ ì  Ædg2 äƒ3`(u"¿R»ÛÅ	p(Ã]Âº?²t°îŽ7ˆ¬o4«St‘<2ÒÎ{ KšÈ¶€z‚_ Ô†¨Ë ?à]ÅÓDE
VÂŠÆ,ÁQÀ”¦Lû!à yÊËŠI¬ètªkBaÝi„<Áq8L¯”ð†3À!¹™ƒrB 1ÄG‹•ÝIä¨ W¹ÌUC¯¢Ðž8d÷¸É]nsßðÜèNw?ÖŠz·{ÛÆpRGPÂ¦ÜØ]yGLÿ%ž<°¦k«øuŠ]¥ Â† ¯¸BÞ“Ì¶B-è ‚¡	P˜|’ô<Â~9ÌÞ†‡O§L ?üõoì
|'(` _íi`lÂ9Ãø$+ÀOd/”!wî`³ª9²†>|byè—Fô²¾#`iU9bABT¨ ó‘wbíÁF0>0Øöq«$YÌg+ˆELbkÙsI	xÅzØ9¸AÎe¡s›U(=ãÅ)ú¥1”è î:G¦“®SBÉ‹4ô£Y&G9ÑS €…ÙE7šJ¦Ú£€Z «UíêU·ºÕ]ÚeL]YRJçZ×íKéJ¹‚Kúò¥~xš¤::¦EÄk`cŽQ?AcO°‘4=‰‰BLþ˜Çam3§¬!'ZÀ‚ˆÁ4«€ÑPÜ6_™-§$ªS¬¢OÈ¹Å.~1ÄêÔöN{¼0 Û˜}=IŽ—¼å5ïypˆâ3Œ
oª“¶mâ“2 €²`½cÄ:´9&ÁZ°ƒ9£ÅÐ‘²câµÖu"“z>¸Ãäe OX¯|õk’5¬šÁö,P°ÃrS –ëK×–Æ´4ýRNçÏš'¥-êO£ž!õ™8zuY£zê/å%Bw}vEõúh¿Æ¥K&ÂV@‰e:
³…ñÙO›{Í\ImÛÅäÔ#~¸
cUïÑR7%˜ô¹¦ê9P
€äÄé“ˆa¿’Ë{,7xÔ¼3žkl£À•^V
2}¡O¯_È:Ñ‚úØ£6u©Ayê6ÖƒªuÜIŠv]Ëx6jWÛ;èöµÙZ×€§@D,þN9âðÇPüØ¥îI<ÞÝºŽ¼+Î©Z1ó›/Wç}ò|w‚ž’'©lúˆ¹Ë‚ªÏCççP×KÝŸ®:E9’u>“®+%±Û=X
©Q¾ù>ô
âÛ ãk)aš²Û5ÀÓ•Ø63+NI„ÃC†êÛ¿BQ7GÐ>¦z7':Ë
?Ì‚! ‚*˜!P‚*@'ã6€KºøC=	¢?Iº¿MË?O£º°:ºû?‹º=ÛÓ:Ü{)ÝZÛ;ß3»¬BU9•Z;¤ 6äƒ ¤4À>a"ƒáªNáƒ¿Q†L7Œ„v;AîkŒ!ˆ"©ª·¨ª«Êª­
#2¤87ð
IŽžP¿Ñ›§Û‚#§ ‚ƒã-æñ1°42@˜à‚†ñÌ$/¸
áp	ña?DÜ³Ð‰ÜÐ
Þð
àè'3ðQÄ¹}ª#b³….ŠC9BS¹…iQ,EFÊ5Ì4 „:!„½“é¿P£=%$ Úóº»U‹B²ûÂÔ³Bl>,¬%- .œÀä›B
œ›“ø“â2CcAŒIˆeXÃ£j„ª ˆ<'yŽ Cz«œ{3'}k‰±B£G<ƒ3ÈC$8y˜ÃH	Èü'«sd”«ˆ+;È‹?F{Ò–ˆˆ0ßB
bÑ÷	±+1(ó¨Ø­å	IÍ	®>¬ƒâºãú­9€/é¢.úÂ.íâHx;‰¨QµR§r+U 9›S(«/º6Ø«¾¢ŸËDÁ"¬¼j¢3º¿ J'Ú€¨ #Wü§€„²?i´DëH§H­ˆ°€,³3CI<[É–D¸ÜxG„DI¤Ä:°D‡ù¹2ØÄNôÇPH¯<-§JDÙT¤Žêò
ìÐ(Ë´Jy8Ëþ‚2 ±´#²´‚?ÏÉFEF§+ÆÖ»Ÿ×[¼d,BÿÃº$dÂ%@4ùº#4ÀW“FÜœÆ™ê=i1)\Óµ „iœ;™| U8á§\ãÔ‘ä\Îæ ±€Î¬Û mT©nüÆÅ@@˜Çå#GyÃ!°5h|1–-¨š$ø‡;€_@‚Ø gc<Œ¨ 9!z´Lkû¾:¬œóLÏ2¸;½úHÌxŸ‘`ÐKœŠ‚LÐ“8'@"˜am#«H¯¿P/!ž8Ìç`ÚÊÝPLü—«„J'(
¬-¬4,ÄÕ>ŠÕŽ7@QD<‰/XÄ„sž9€wUOìa	íQ˜îùôò1½R“
%++-Ÿ,!%MÓ”ŽÁ?ÕÔ?M#ô;$ gÔ“8™ÜËM;5pôBñ\@ÒìÓ
ÚN_ÛÂ¶ËS€»ß ,CP$±»P¡f[<
|šDÝA?­ÔðxÀ[Ôï§‰;08›±[T§8° Ð®Y¶°Ôp<Ô.SŠÅ	€Æ©ðŒ ¥º‹­'Ãà5‹ Uð¬¤¥p•€         (àA„       2   
   N;x£>–Ä‰ÊßÙËÅjÖØzw>QL“o¥È˜p:ËÿPù¯7H[éVÃÌ?                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\IConnexionClientSocket.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 PÎ¸ö-/Z88ŸægC‰wÃ½¨“RŸ—Œ
¬‚     "      ›       I C o n n e x i o n C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          a                  9                €    ¼   01F280051n     x       ô      >        
                                                                       ÿÎˆñ#   IConnexionClientSocket þí˜á                                /ð…:2AŸz²,žœ:%Sà0\
imT
.ÞøùmŽï%ß._ð®Î+ÝU"µ À    å  Þ    
d0jÂÀ.2$@à®Ò…‘b 
*8`1ÀÆˆ" ø,‘¼•óæ™<jZi£ÆÌ›:Tàac¦N3t~Úy‚¦:tÌøç.“8Q ’4)oDDiÌÌQSÇLˆ•5oæÜÙ³S§P¥z¤z2Ö'pÜèäé¨ÐD¿’I#†Ø«¢hŸ®e[ò¤¬BÊÌ	ª”®Y*[ïl½;´(_¿€—6Lx¤ay"žQóF°ZÏ¤fÝÚµÇA‚|1öEÜµ¥Š(Ò³ï À…‡$î¹Aqˆ *ÐÝÜ5pç+V´ OöuÚÁGº^½ùí×£ƒŸíÝ"oí¿‘¯=Þ[ªò ÌÍÏ_/µãÀ3A[ˆxÄÊ"ªPŠ'Ncàž×òóŒÚ H¾ "AÃ•1(”ùÂÂ tp¢ 1ƒB‘AC=ˆˆB!ƒÄ©ÅTl/€8Ô€ñÂhHE ˜‚ ´ #âLUdíFè"‚Bú¢\‹ýªÒ "–Þj;ÈðÊŽ9c£„4`È¡
€£Ò=‹˜³ 
ß²ƒ2=Õ@;!¢8æØ‰UÊ ó«,·ìòË9RPá´Öº“ò7%"2@º ÊˆHR6=J ¢@ú¤ANÖ€ƒÀ6ñÂ¯Ôì¢C¯É¨†3®¾ž\Ô<V	ãðÇ #
a€c6Üh×^SôÄµB™a2„u°HÂBy Yez µj€¶FGýgÈª˜=-I—„5 Yg5w ª<)ƒˆŠª.½4LÅ Ó…
Î|¸t+•£3âôˆX:Û’Ç<õ$ƒO?
Á]AåÓPDYSô\ht G-ªˆÒHÇ½ôBMM
 Sä@MMTRsKõ5òJ5oÕ•ÇµˆÉ÷°Â¸¹Z×ºµÃ\ 
ã€`7ºh\?Œ(Œ€i§Udq Ppºê¨=¼1®îÚ×¤
r["-:\ Åõ×Iw^ôFÐ®Hˆ5à°Ã+šlÂÉÀWîå@ß4#ÂÙ"0¢Å·‹(Ü"àÆ"á=ûü31ÆØRŒ®¾¢Ûn¼ÅÂ)2<ÿ),½Éªë§2ôxC7ÔÃ3TØa8àhƒÏ›*N-qÂxw;¤èø:A®uäKæ´ÔOChÔbÎí;T£¤Y½J{|ÜroM{Ïv Â"}d©‚
|pæ`©¦lˆa¡TT &RQG	^*q¢˜#´xÄ,Ñ mTã0Cì¡
eŒTA7FA‰M`S(B²‡	(š°Ä1Â!_|b% &ø ŠW@Ë0 ÖàŠ<Ðð¸‡7 ‡d€
³P†¸@¶n™--hˆÚèd‘îyÏ<p«Êš@;Ä!Ojpƒò6Åâ^ p‘ÂYÀˆóÈâc0-@na’s˜†@…*LAJ¨‚B€E-r¥‹_ü\èJ BšÎ,!HÝêZ÷ºØÍ®v·SEîŽ”(îH±UÁÞEŠ'2L)o{¹i^Í½ HÏdÔs™õl†WæŒPl›&±§›Ÿ9hj„‰G8H‘…hÝh-qHÇ/£Å,Mk 	x„‘gøbÉÔO¶"
q¬DT¤-nÄ[gC›kE[6‡Š'éW ¦`2dq+^‹{y Œ£|¢â0R
6.cŸ©JäÈ°ÉUîr™
Á;ã‡y&’†
ÁBP‡ÊµÁb°#¿Bº±<Æ.dë`§;ÏøN**ýÆ©O®m™¡Ü?=•Ý•ª|™kxêJ™4{©™%Û–“Î[Ö&—+ª¦ƒn”
-ü˜ØrjSÅ‘\lAªÇ´ª2CÄ…­RUZVEeºÖ°³lFZb9‰Ê½Z"—
Z B0§Ý5kJ^Áö4¿Fk—á €Ó»W€Ü(0,cÿ
$!­u ä—9svT¹>'" 	*Ï¤R~‚Ï"È³ˆŠ        (àA„       2   
   ö½l}CÎ·ÄÿêäŽ¦.?ÛÀ„ÉšZÆW±å JÊÞuÄb„ý¦¦|                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\IGestionDesCalculs.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ºj	Y
y‹ÏKŒ%·IéAz
cL£     "      z       I G e s t i o n D e s C a l c u l s                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          a                  9                €    ¼   01F280051n     t       ×      >        
                                                                       ÿÎˆñ   IGestionDesCalculs þí˜á                                /`+·z¡T=S–væcR*õ˜Wø»Ò z-±ñéYó‘›(Q
ŠŽ)Þ`ošb³    È  ¼    
dÈÂÀèÜè´…×…‘b 
*8`1ÀÆˆ" ø,‘¼• 1C§Ž8oŠ´$"¦
;mè„hY'„7fBi#†3þ¹Ë$ÎãÓ$MÊSQ7qÚ˜qcæM¡J„‘cG
5/cF™‡ŒR¦N¡z”zr„U5exºüù†çW5fæ„ð"Ü¦sé–<	Ã*›mÆˆ!Ã¦Jš9fÄèíÙWhœ¶dˆ©*c'3b¹Š£2–!â5oP«IÛömÜ?D	2wÄÝ…ÿ†êz HÕÈ(gÒ¹êÏ=Ê°JÜ:nå×=V¬hAoð¾—×¾M@ûõàÁkOßû¼Eãã“KŸý8Tê¼ço1;ÔŽ:#"Öè+ž ¢
% x5j) ·ŸR
§‚ ¢¼ \Å¼ z‡!‡ ŠÃáÚÍ)8”«K"Í­ÑJË,„¼RP!„þzI°Ùl›qƒˆBÈì;àÐC0 ;\*1#ûCo=õØÃR<íâûÏBé¾ôÈ>ù¦«ÎJþÄT
Œ´pÑ#6ý‰µÃºQ4ÒLªÇ+l2Ã´½|JÈÀæ¨jÊÔT›ƒˆvÈË‡>·2­HÚ^D·ö°´.ÓÏó’¶æîƒŽ¾ˆò
`¿KÏS3UV[mõXsÀ"D²õV[m#SLSQuõR2¸œëÅhváæ‹`­
œc“U¬‚ˆ&
 Šv”ÃÙß¢iÁd…U*Ö©f¨V\oÕ•Ôzý5U2<IƒˆˆˆÉ¥9ì ¬Ie ‰‚‡Ä‡”kWÔê,h¹ÿ|%xÆl-G=‡š·Žzïýs)mU %"2`» ÊˆHd‚-J ¢@ú$2ARNÛ”ÛPÅ-Ïã4Kþ>oÔ’K=s]ìhÃ6 [ -<ä ÃY\Éœ+„+”fúVq
¡©7Âõ£aÈz \7„UVZã*×\ÚvÅïg ùëÀ]y2ˆh&zíÅwèx ¾@¹·{È&öHÚ…Y«ÑÎ‡óÜ‘nŠí¾8cD×åØcIÝ N(å•#jY:˜Ahæ nöö6Õ­ÔY±U Rû)uÛ¾-vÛs zìpËnêì\Óæ¼vÝµ#“µ8˜É
1æÈl¬²ÎJ
&ÙôåcoyüVnöˆÀ(äÄó^ÎÕ¦ÂaJŒ1è#-¡–oþy²ÌBK­7|r8àØÊ+)ÆR@»Ü@>f‘ŠhNsbò\ @‡³Ñ¬t;
 êZ§¥á¬î7¯›K¨Ã=‹¯xPÁE†önÉ®8øÆ
œ‹[ ph‰H(&ÑBg…‚2ä’rbp	n$×àáS|†Bq ! E Â,Þ¤j¨@ÎÕ³t±m„¿9ÞTÒ#ç™,Ð³ßôb¡ë!óÐžèHå=
€¯Sã“ÎŒÎÇ¸ôéh}í£ØûÀ0ffŒõ“þô·þùo4 ¬\•rWÀ <
ä\È2,½,f§ƒQmV³þp„œ³}¦Ÿ-v07‘øÒ$C	äT9–@Î%ƒ	äd9š@Î&Ã	ät9ž@Î'
ä„9¢@Î(C
ä”9¦@Î)ƒ
ä¤9ª@Î*EyL‘\g»b™B¨ÅTÞ¦‹'1Ñ´š'†®P,)ú‚ÃÞèÑFöŽuŸGè˜¸©Ð }xÒcØç>ø… 
ïŒgfR †ÒáPq)î )É‹P‹C™Ê ˜ÉˆLvœ¤™'1ˆ3
æF”`Úh)‡‡Ît–0"iøÒÃä´9n@Î äÄ9r@ÎC äðs v@Îƒ ää9z@ÎÃ äô9~@ÎäÈ1 ‚@Î ¾	.)úNU,'¯`šÎßí:M›+Õêj+®Y'lß"Û¸Ì¼¶®
•p5Ý@&@Jâˆ	Zù4§jh\        (àA„       2   
   ûœÓv‹¹øaî©¨ºøü¤I÷)J¬GÕ¹½yÉO[ªð  ³ŒÒ/pœhi’                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ISignalDessine.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ]ÈA7ÝM{8ž…%&a{AGì4á|”¾KÑRìUø     "      º
      I S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS                             9                €    ¼   01F280051n     p       	      >        
                                                                       ÿÎˆñ   ISignalDessine þí˜á                                /þ#Çš–Ò®R3îá*:öÑl0í¢ ô¯Òpä=°pút÷??…ò*btã¶ÜýNc    	  ¤$    
d¦Ä@g‚)Ø¯Ò…‘b 
Õ*H`1ÀÆˆ" ø,‘¼•PQƒæ˜6EÌÐ¡£æ™3ë„°s3‘6bhš	ðÏ]&q•$ieDjÜÄiCL•3oêàT"„Œ;jjÖQçM”9pÈ=št©Ç¦'G@US&'=sjUcfN!b°EúnÉ“* ’I#fª7bÐàÔÉÓ'ž6/ K&j´°á‘ˆåÉ€*fÎ3uªHõKE™4m*¨¬w8UUe%ìt€¸ò¨@=£¦!gŠ3æ|7¯ÏâÇC”áê¬X5dÍ²t	ó•:sÀREëaÇ–}Óû7SÑ"oïÙ7èà"†pÿÞÿ —ú "@
p /Rð@¥äH$Ð @B
C²ðÁ

k C‹HHC|OBß«¨"
ô!pE'é?Jd0ÁV‘ÆeŒÈA#äp©
1TÊÃ @ÔñÈ
c`¾ˆD³ ¢#¬x‚ˆ*”€â‰Þ ¥€ÿÎXJB4št
‚ˆêÊR•P	4àÆL Š(ÌÄ(
ÓŒˆF!Í„ˆâH‹«Ê°µêJA…vØ‹,¿úû-8
"Bí;àÐÃ¯FÍ´Ï?=‘GC$µÎy$ñËWU*È$;„JT]ý
Œ´ÈÓ#\-’0¸ÁT­BÅIÑ+`2ãPç,{´¯9ûô½à0ˆh‡º|Hö¸C%ÅÖ O­ñÆR[QÕß*ìó-Xm%ÒHpC´T‹à €Ì X@€ò]ð  Hœg7fÀ ‰÷áˆþ@4†#n
 Œ3Æø½v—zbCyä¢&vªâìÓXcŽèc’uä@Ì“*ˆ³ðšÃ2¶êÖ>Ø(!arè	g^·HŒš€pUxûî„@ -QœßÐ™gŸçXÔÛxP""<ª¨ŒˆÐ^z Ž
¤@‚$	%1"~×ÜQËE]AëxÈYcN÷·\}B ŒB œB ¬B ¼B ÌB ÜB ìB üB  B! B" ,B# <B ‰)¶Hå•7þmp`6\Äh–'ƒˆb
ëeçÈòˆ¡=Æè‡$L~i,`âi&Ê08@…µZCu~çž£ÿzZˆÅ&Ûì Ô®ßeŽ"z;îˆæÎÐîÃ
$o{+8ÃÌ«ø3ÞñX "(Ìns`ßÓ»“ýîbÁHËÖ€Zð?°œÓ8&5¨ÉtT@Ù[àÒ€‘<èË#7ôUhœ‚ƒª
}Æ
Æ 31d '(œƒ
Ãc†0ç1‘™Œ´ÚB¯˜Éo e³ÈÙÒ†¿¶5nã
€ÿúÀÀ`€~#×‚øÆ ¾E]b`áDØC ¹J`ÀP d  B€P d/‰@ˆj „
6 @Š „$ ¡	@ˆª „, !¤
Ê „4 ¡
ðÎd'AYo8ØÁáÕñCw	Ò« È$2§AÎWÔ“ödÉÉG4’¡ (Ìƒ†!Þ@nhAèÐ"<Ÿh„DB
QFD¢C KÓ &=ØÑÎÕ¨Ü&7Vá
ü¾•Å±m‘~ö³Ÿ­Â¸?2š1IhL ÞöÄFSTFq|`¬€$K¦Ñ
e(€ÎAMÐ Hd¥‘òJôevl( "	—[çzÌ’¥>l$üP†¶ ÌP2óGÏ„4#BM©‰&Ø¼N¸ž$n%"E
I}INöœÓQêÔM;¯H-Ãi±_óüâ ï9F¹•ªnwà?ÝX@¦j¡­Šå CxG*…*Æ&Ö: „ ­oåj -’‹?¼âÿ Ä@rp<–•Ìw»( 3ê.Žvô uŠ š7“šÜ¤bÐÚf&“¥Yl$ ÈÆ ’Œ8I¨™x&.lêLüJ4wà©6}zD z36‘5Ãd+KE(š¡
ÍêÊQMúî¼$3àyÌƒßÓ,ÓÑÃKÜ °˜QKÅÍqœú™IE5žSíâýRÏ¥\•2]ÐVíºÆ¯¶‘Œo<A•2Gv!4j´L«bí
ŠÊ£•À
Þa=–ØûÖ0 ¢aÞ@d2;Ôa¶6É6rŒu¬B|¸(bZÆ™b$§%mj}x´6}¯í¦Pœà›ÓŠØ}'É¤ÊÅÑŒúÃª€é~ÊÑŸvJo@{<Ð±ö©¾£Ýè,Z_Ðè•¯¨²`$ ¡ä¾ 
yÝk”sšXPyÉˆ!XQåüîwƒý-«FAà c˜±'QZ ¨ ;ÜÁ–5Ãâ`†0MQA9f°€nð‚í3oÁaœ&)8v1ÚDo¾9ÎsÆ­ñœÝò¶¤ÊnK„ûðŒ§<çÙt9™ë\èÚAºèd*;ëóÔìÂs~Ý•±Ui<^5“Ñ¼iDïzÁúc±~f¶‘Ïld†"¹I°LsCÃlQq`´ÌˆEv³É;`§l "h1CLS[È2h¦Ø.Ø0ìÖ§ðªž¹
D{>NQC£·	[H
•ÛÞFMŠ1­n¼„ ·×ùmp½C\Q·Ôì9µcR-]°½%T"{1U¿;c·ÕøÚ7HŽ
Úë+ûØ× r’†,ßŽ*ûa¡Øƒ²@W…	{G ŠÃ;öñºæc ­¨2Û~#>>	6ƒ„¶a§ýßj[Û–'1ar–Ó˜Ë
I ;¾ ¤c	©l÷1¢ˆ]MSÞ~rŠ
ìMâØ
Õéj`ÎdÞ ëÇÚ§uUÍ,Þ|jÇuy©fÖs•|Ž'7+š-¨rk7ûÙD—¶àð× ;ÖñoMÞœ…Þˆå2ç¼ÈBáªQ@ˆ*„L¡S@J„T!x„j„\¡W@Š„d¡Y@ˆª„l¡CÄ´É„t¡]¨r°)óÊü«È#›G×Qå#rùÌwpÚ—{#2’ÿÇVÿ{Âh        (àA„       2   
   xR‰ wÊ‡°‘±ÿõ¿  ŠÆµ{:Ì&ïêŽæ"t±ù)º¥9¶é€éê                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ISignalEnregistrement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ¦) ¿‹a»e‘ÄkŠÖ 
»Z„]RN®B˜–¤1¹¦á     "      º       I S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          a                  9                €    ¼   01F280051n     w             >        
                                                                       ÿÎˆñ"   ISignalEnregistrement þí˜á                                /¸ûMLÄÜS¬ãBÀŽ]“¶
¼¶a´ƒ?ÈwCD›p—ýÍõ(d ü¥¥½þ)­FÜ      _    
d@"Ã@
¢)g¯Ò…‘b 
á4`1@E‹  ø,‘<•PQƒæ˜6FÞÌ1ƒF
:4Ý˜yS'„œ!ì¼1‚H1tè˜	ðÏ]&q¥Ž,yrEDjÜÄiCLÕÎžE•!#ÇÎM5uÔÀye2MŸFê‘¤Iy#°ª)ó3èÐŸ=Õ˜™ÂGˆr¡Öµ[U^„ˆgÔ¼QL—1U¼"†¨|ÙógÐ?Dù1ôÔÑUŸ®
y HÆ°eGœ}¹í©) *`Ý›5lß+ZÐGÚxé×	„^<xjÓ«}C/Üy ×€O­Íøvr©ºð¾^Þãv©"Gtl!â+OˆTQåIe´
„>Ã="öð‚ "¾ðS¥93ˆ	@]ŒÁ¢ÿŒ
£(Dj."èÀ
à®“Lˆ(Ž·Èðª;h
¯Ta‡ÀÔ"¬³Ï8”Gƒˆhzã8ô lÅZ,Fóœ£NÂçˆ´Î¹ìÐ;7
ëúN»Ü°
rJ%/#-,ôèÊ”s,±@„kÄ‹Bñ
˜Ì(Ñ/Ÿ ãÉÅ9T€Ñ31ˆh ¾|@³
5ç˜ó²
§<mHéz#4:ó’ô¬»*#zRIñÈôºFY[2Žœ”±LÂ¸4ÓË9äS²SM=ËÅSTÿÀ1 ,òQ€jµµÖÏr·M{õõW`YsVYçºõÖ\™ô(Ò`'å À“hˆ¶pšÃ2êè?$@JH†º ¶g›£&DÚNR
e<áÃÉ4‘Ú7¬ÅVÛSü³W”ˆ¨#‹**#¢Í(ˆéG Òž,
6	>ƒmÀM4‹§Tô²JÐ5<)›
­ã 	m¢ 2ô,JT9¡äj˜Š/ÎaŠÉP]‘°P¤D2@¤DB@¤DR@¤¯X#šõX[“58 fE²hå9h "€ÊéÞmgeào˜&Ü‡`ãÚ\,`B]Pn×1ÃŒWMÛÞlûÔwÖ`ûý×#	VÖ"„ R˜aG‰„Xâˆ(NÙÈë2rãØ<ûxÙµ^TtÒK—úU¨‹…Šj\=_<¢¬M÷íIÇ¸ l7Ä˜ƒ&²ÌBK-¶ðó¤L¡F
îi;rsÕ³`ž=R¯KÌNÞ1ù.31Æ°VŒ´ŠÂ]wÞË:‹Ž´Öz#ÅC8ºR…§}ë
´YÃ9B<€‚ùýà„-QSVÄF7Ëi®H«¹k8W—î8éX
ì–dAŠìi'‰Ú@¦Æ:«A	d¼Êàih‡èawfÐVïÎ—¾à™­ ±6ª¡€(o\ÌaDœ'ˆèYdzÊ’Q°'"í™¨{ß
_TH“šïwêc‹Þ?¯Ðp@]þ 0ãô¯Jj\ 7@ÉEÄ€«†0'¤7&j‚Ôë¡¯»Ö1{ÒåÑÁÊ° !„TùhÇ 8Bˆ‚îÄ°“œ0Ålt É
Fa
tA‡°ùÜ@œ—‡ úð‚rŽ(¯¢,1'àÓV9‡G²&)xÜp5!
ƒ‰Sý¦r¿ÂùKóß@Æ8•2ž±aiÈ;‡@720sq,Ïýs5‹tv†Ì`Ç,âtXÄ¨È­ž
B°âe&cÌ­ yš[iÆ7·²\hüÈR»:×!mÓ5DÄd˜CZŠ †záR—¼Ä-@²ZøbHÆƒ8ù:çå"”ž¥ŒðpÊ$¦Ò{«lâ@
Z‡ƒ&4—»$L
ÌÐ6…`,RDðÞÀ—ÀäTÈ	Š „*.œ"[BP=¼Ä
j0‹TdEøÉO‹s
0åE0^DŒL¦ —¹šÉ0 	¤¦¡ÊŠ¤9*«cÚKèÍˆÄ8 Á,Ôˆ âëH8Ñ	€ÊŒ®`Eƒ8b‚ÝÔ0D sìã
‚M â@†ÃZ³GZ†ÓPÇAÕÑ
„®»#7ýyZÔNåž¡ä>GØÏÔöpiýÍT*ÀC¸ Œ¹U@         (àA„       2   
   h-b¹8 Ú—À¡ÏåïpÐÚ%Ýá'²4¾ ¾–r$á		Jóµ)FYëPÙÚ!                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ISignalTraitement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ÁáÎ`û\cmV¿õÏ¡~t«–µ\	¯|Œ_Ð®‚¼ç?#     "      Š       I S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          c                 9                €    ¼   01F280051n     s       è      >        
                                                                       ÿÎˆñ   ISignalTraitement þí˜á                                /8£Úà0zî€aê¥ŒÑÆúGwkY¸ö%&ß;ÌÄÊòÅ•D6›ª:ñG»<@    Ù  A    
d` ÃÀD¸|IÈ¦CÒ…‘b 
Ü4`1@E‹  ø,‘<•PQƒæ˜6UæˆQSÇŒ3oê„0Cg§7fBi#†3þ¹Ë$ÎãÓ‘%Oªˆ¨@›8mnæ´B‰2rì¨¡SS
œ7QæÀ!£”©S¨Iš”7¢ªš2<}† ÊS§3sBøÃmÓ¸r¥Ê‹ñŒš7‡á&ŽJ÷AÄ’)oæÜÙâ‡ˆ ?z†
úâiÒq™¸õëˆ°)7ˆ
UFÕÔ¹S·Ö}±¢}¡…‹f‘€çã½{›Z7sÑÄ• X€7TÙ‰iz{ ‚éáŸ^ŠÀqÄÅ"±ò„H%PžHfP«€ç3Ø#¢AOBD¼è[%¹0Æ¡'ƒŒÁ¢ý„û#)DJ."æ¸
`®“Lˆ(ŽµÈ F•2ì˜C(¼RP!„üª°94ãCy6ÀlÄ7î€CÀPp)Ä^O9è\NHé”«Ž<²CÒ¢í¬³
·\210ÒBBªÉ¸ÅˆC¶>qÄJ¼&3DÔë§7Td‘*'£F"Ú/ÎÔJD7›0JÒ‚t.7A›ïÈÍ”Ü¬I$½
 <@Ã›2µÀ £ÈHËƒ‰J/ÍÔ³L8µt€ XÌ‹âhUVWåŒÑ'¿ûtVZŸÊDŒN1­5ÊROMõ­V[}µ¶î Ü5Jú;Iƒˆˆ@Ë§9ì Ã&>ãd 	ú…!‡.h-Y
eµ 	‘®ƒ”Ø
;aÃÅQ(gß€VZjç0±ÏYP"¢Ž,ª¨Œˆ 7¢"
¤DkR´Ö$à¬µÿ*T×Î&ŽQÊ&
 Öbe=Ö3•ËfŒ\)Kƒ‘’H åQ ÊeO?æ¬WºPHÕ`]]]‹=Wæé:PVžƒ*¢§:¢6Í­="[\€áö¡Ö„X 
˜(×£‰Ð…QÃ/ÛñÝŽžwiïsW}ùõèß€y&Ø`„
]]‡(â ,’È‹åoç òè‡s«è€G=ò¢·ãŽëÈ¼È
a–V4‰9®È˜\e‘6'­sðh>ÉæpÎyØÂ8ñéš\LË ŽvCŒ9FK,²ÌB‹>>@*án˜„K¼ˆãˆÌ³ ‘¤3oËÊN‚Ý0Ç&SŒ1 ¥©+Ûq×=¬±ÊZ-Q8´Ê	ßÄþ<¶mŽÞ@àûW™î„—²áÄ
„o~j(–Œ¹FLÒßë`—=V¹@CÊâ’‰X°3¦“ ê$“3Í¦Æ’`jdGæ!wf°ÉîÊç»ÈX+xC7Â¡€U OyaÞ@œ ½AMÏk‹¹^Ø²7&¡pÏ{5Š
GÔBòõî|oHßÖ×¾½om>ý<g?üáIÈÁúÇÃÿE$oÛ… ÷7®uÔs’[çÀÒ1{LL6† Á§¤ ¹â  U,ÖÕÑ#xä#á° 
¸N’–kÁ$€Â*P†^,¨5=€óê=ÑB0¢ö<¤½$v/ißÊ$gbÉ¥ ”tàô–ÎÈmûªŸ¿ò71B…Œ0£ÝÒ85*P€nŒ#
u@Ò$‚
ôßIÈÇÆ%e©BÊ2P@z$€ØÅ&D‡¸|°Ó L:GUÈ_5„ Id£¾ÙHí­Í‚	ì ú¨$Ox 36 Ždˆ²›Šô!F^J‹¨R‘0rƒ+Ýµ=YÎ„‰C)èAÍ3´aM^q¡Ïò–¸&/ BÒÀP!TA`h©ù^‚2èá%nPƒXÌp¢]Ñ}1&eæÅ~	'Œú[f3öÌ Ds›Ó”Ø
xM9RT?sœ¨?=ÐpT­33UÍò)Ž}ö3V[+¥Z&ÁL!I„ R–‰¤,
~½k\òpB¶õto«#ÍJ×ºÒqÜÜ
TŠF²zd™iU@         (àA„       2   
   (m"ùøÐGšW€Áa%¯°Ú ¨Æ*Q·¢þÖ2d¡ÉIYŠ3õi†+™á                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ITreuilClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 èßéK 6iíw%‘óºcOÛ•“À8W@o¦L«¸ùŸ     "      E      I T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          	                  9                €    ¼   01F280051n     o       §      >        
                                                                       ÿÎˆñ   ITreuilClient þí˜á ILj                           /âP*ý¤ª±JÁŸƒ2­cM±±ìWîÑì*«T)(ÜkkWa
7ìº}õÏ]^ƒ    ˜  ¡6    
d`æÆ@âRÈâ
Ñ…‘b 
->”c1@E‹" ø,‘<•T™cÆŽš6DÚ¨1ó¦N3tnÚyc&„L1tè˜	ðÏ]&q•$iRÞˆˆ
Ò˜™£¦ŽO%2iÚ¤‡3uŠMºÔcÓ“&¢ÖÑÇgÎ›FÞØqÃÒ%Ì*m‰EjölÉ“¢Žíëw$`y"Æ©z‡˜*Âe
 @‹2ê;gÎPmb†L;-qêÁ3fÍT#OfŠ8BÄ3jÞ¬fM€uk§2"Ê¥k÷e›¼nGïìâñ\7çˆ©£Îl¾’
WÆÕŒ7a¯†¦=¹rëS<›‰³}N÷éˆ9Xò\I™šÍcï%{»òë±¡×¯}›2bˆ"Š'¨¨Bˆ'ª0=¿*#!*"ƒ
(ˆ`Â	*˜Ðˆ(Bð!„À€*:ûSk ¨Pc3ÀÀPÃ®¾

Œ)²ð0„t#±¿
0 `€¡4:Ä@ƒ¾Â¼C‡¨„!F°ê0‚ŒªêH¡ (Ã"B1Ú0ã‡(´ðE0´03¥•tj…¨Ä¼0C0¢Pâ	$°SG“ÎúŒaDþÔl°4Ósª…6+|SÃ)Œ
(ª¸O§0p
?g 4ICOr!"!ST”L)¬XiÒ<‹0b@?ÿT€A³*Ct PÝ$“	%šhUUeµO6•®AÄf QÇ„W]'$b
%¢ÔÎ<™h‚Š$Z¶ÇAQ„u©ÊjHTY
«Ð"
#À â
pÂˆ*´b
JG0]?eØ¶6ÄlwQ0îMw]FWåP^zó8Ó}•‡†ÉX]vÁx‚@U Ä…m¸SybˆÎ‰UuAU7¶÷\#ü¬ácbòáßF£˜å€5µ	0Ü…×ˆyU^Â*L¥ÂçxžæXÓfoF7g s¥Ñ`i•®·Q"Všip;E7k–:Ý*t¶ú‰ŒUnï"ÓO
°•ªL0o
ù$‘ÉN'"UzÂ§˜6ÏK«Ðö[»ÅYÃÀÞ°
Â¡ˆ¢%¬7ñ|ëþË©¾´pÁç­<Š;¯ÂòÅõnÚ)¼düs¾ C"JÍ|%%LDÜK=aø<·ènB9ôd&"	‘H÷èáy*ž ¾øÙGŒM¿ÉC	Ì‹àuòÉßÞøÃ`OžÓ½å16|€ß-ºQ0LbŠFU6Ù‰Ö)/~£™ì7¯tí¯Q
R™ç§Ø©{íë–ì\¶ÜØî	Vx× @µ©L
 'ˆ›
¾ê‚Ë`áT*¢U¡wOx‚„p*ˆð†T ü^—ÂþÇ)Ýß4(PwêÙ4¯æI
Hä¡	WH;yÄN…üáÃlw…•íhM|¢¥DÆ ö‰}(Ìâû†ÕÅØn
BØBÚª¶+•Í±Ž=|#xEï9åS.„‘
+ ÎzÔ«¢ƒX™8rC‡,°ð„E²‘úz$b´ØI5iHv(Ò‘@y¢ ÈIY’•¬r%Vn©K_
“Áp%d!]†4Â #°  y f~ ‚ˆáž	A1óp£V'!èSj€“7„à
§”Ç¬Š(>\ê²‰½ü¥Tv…(xpBü#ý(i”¥DRYx’.ˆòI^‰Ò”ªK~j‰K^Óè4„„HAA˜Î<f2—I†f>óCÑ$Æ4upQ`3dp7ç÷M€©*
MÀÝô0–®+ô2Š Bèê"ÈÍO’‚ðÔ')TOnÉƒúd¥?_‰¥€ÖA–­%8s™®#4J

-fC•ÉLgBÓOÒ¤ff&aÜHÉN¤*ÕzD )Ö‰¼¼¹óž4Uå<oÊÍ*ð4¨>µPe$ÔÒÒ `@‚æÄTc"ó©-¦Te@ÕÈà 2e#
l V¾ú“*œ "˜º1§ñt’MMÉXvÊ£ r½+]z×¡êÕ–Œ,Ó™¸ÉÒöÓ•ue^
jË(`á²fÒ`ëP¨v¢}:lE«zUn.D’¹½l"ÏÇ\+œ1)Y®nówÉçê¶Òu_Z}øNÎºõ³jÅ¢ ô‰Õ„à*nˆZÒnv@Ÿ‡M/âÀ5ˆ¾ ­LbÓÚ5J©½­ø§*ß:õ¡%¦Tû„XârS7’<0ÚÜ…ÄFá1OæcN™·×
O(
B¡4"—»íüîZ- JÏâ”_NÙi[÷YÚÙžvÀ¶-jH9TVŸ%¨µ½fS»à¨÷ÁÆ­Ÿ¸I¯ÂI¤@~—[/‘F—›0°Ý”KZeÊU»©âÐv3ë]{bñÅm17k¤UÎ5Çè,
ìã‘~Ù”ãm‚Ü+«Lv2ÃeIB–Š Ì•ÊŽ ­¥=ù±GS'¤'&ÁÑQ ‚Î Ä@ "NˆBd’à&¬Ž’,—T(\VJH©Ž  _&”R"‹N¨G]êèì Õª~'u!Ê¹:
°¾,.hb%áw:"‹¯ BêÈ;Õ]4¶}:fÇI·Ô•97f¬^;EÔ×6Y´Mìµ:EßVv¸ ô\"‘¿ƒXºO²î `;ØÃî¢rÝêÕ-Ûƒ—í¥¥4ù;qù[ ¸»	þÎ<Ù	÷ù;IœâíFÊ»¹Íê¿záêjB9¤Ûß-¶Úêþu¶/¾ÖŒ{á*õí¬0)J1Iä5ø¶‹rp¯¼áD€¶´7–ƒÈX;à$‡Éßip§\á=oxK¡ð»§
Ý67z¼‘^ï•Á	Ep¼¦Íë™ÿ›è/;¡œõ¤÷|-gºô"§5JÉ€ÚH‘zÅK~s,J "ÎÞ"ái>ö¢Ãû,þ¡|å-yÌg^ó›ç|ç=ÿyÐ‡^ô£'}éMo›ˆd òUÊÀ‘‰xDõ`} ‘¼~õK™}íc  Ý  à·ÙÈEŒùâ$" á<~0{³Ì^ú™~ê©}ëgÿùØß¾ö«ß}ð_üÜ¿÷É~ó§?üè_¿úËß~ø¿_þìŸ¿ûéûç?þøß¿þëß ü?d?³È=í³ˆ>Œˆ+¥ Ä¼Â£ºÙ3@ð³ˆ	“\
 ¬@Ñƒ>Óû@AA,A<A4½Š¨
Ð‡å{AæÓ>ÔãL>æ‹AË³Áì<çó@¥ø¾ä>
TÀÂ¥p½üˆÄX³ 8+p""È`€{ ¼3˜2ˆAÈ‚,4Œ|P‡-ìÂ H0äBôˆlX‡0‰PC34ŒP`‡5ˆxpC14‹<h‡9€dC3ü @ °ˆ^C<D€`
ôÁ ¸ÀW)BÿX@
ˆ"x˜;ø—8è` ? ‰„H‰†xˆÙ“D|
Õ›Ao	‰™:Å˜¯xŒ2
Ÿ¸Ä7ÈÄM4@ò:ÿpÅ %ˆðˆŠ(ƒˆ`Æ ˆÛˆ@è `>
d¾ÙS¼É˜=€ÓÁã»<oT>4Â$À¥XÄ¥pDH$GLÆg€¨ jH‚ p>³@ÄoWà ‹H 	F4‹G´ˆWÀbˆ¹@DB¬6D<DEdGu\GÖ¨=ÄØÀ"€
MäDîøD1Eq`“R¼€Þ3B°€3P=Œ=4‘Y¬EU¸EÑØH^ôÈ9øÅG*ÆcDe gJvŒÆ ˜ÆjÄ½?m4nŒˆp<CÊ{JÏëÁmdG‹@G¥¨ÈuÈÛ(„74½|à ¯Ã
LAXU0–)hH§Äˆ(ÄˆLÄÉÀJÐJ‹ô

¤ÄÝ 8°ƒ«`¥)t wÄ„Aø„ 0z0E¤|@Àd@=
ˆÆ„E6B‚c	ñ(¹‘
«pŽl‚h1äP…:H8 Í¬˜‰ø`¥˜’¾üË â&YˆtªbP„"@ €M¿4VÒIþF˜ˆ@Í˜ƒÎø\øh‚WÆÃ"ƒ£5ãÌŒä\NÐh‰ø01(°nÁÎ :Ðƒ7 ƒ4 Å7Pƒ¡`	1ÈŽ"ƒ]î¤Š_,Ï–COCs
yˆ1˜ƒ¡(‚çè‰œHíL
åôŒï4êL³@:	ˆ
yÂO20	ENÎ°P\TÆÓÐ™’GQ€	íNm	OnZJp6nr>Î»=cDÆŸÊ dD¢4Jq€kä¾¥ô‹¦©Ì¼'m>«ä¿ô¿¹¤H"„D®ìÑÎ+€É`Ñ# G{ÄG³ðÒ ØÇ~ôˆ# 5¨¼‚ ±˜x 5™`Ë“pË€ËˆœÈwLÀW´½¥ÀFÎ‹ÀÈ˜@,
T¢´BEÁ¼t
ƒsOø”Oú´O3ÀOýäO:ðO
ÂV˜L S  r h0ÉÅäÀÆ´ Y@Â‘LÙ›){ÆIšô	ÆPÇPŸÔøœÏç°TL5ƒýìÏÿÌIdJ¯1€8hƒÇ¨‰Ì)D
€ö
P8,008cõN\|¨1€	/«cQ ö|ÑeÎ–Wr5×/ñhÏÑN­Pw5.8¬Ñ¤/ÏP•! Oó$Ð`œ)&¡dqÑ|WÑ€Î‡Q~ã„ðnbÂPÁÎ7xn"‚tM%À×o¥Xx-×6 (ø¶É•&˜ž´±"¨=O*à&eØ;Q"“¥PˆåW—õ«r#œ) Ù*pz%ŒZ˜•Ù£­Y/¸‘p6ƒÐœ-Ð“ ¢P¹“&€48’9x+ ŠeŠ|
÷üUZÍ)
€¯µ“Fy‚+€-è%mZŽ6åZy¨žµ%º˜'à&1Y;	Û±¥
#˜ƒ–×œ-éDƒžXÛ¶ýØlz[J…ƒ¸/:ÐÔõ¢E;ØÕ–Š›ÈW¹å&Ë,\;aW}½Ð±Ý å§-ÚKAÚòÔXÁ­Û»U€–xƒ;€= Š81°ƒ<ØXÆÝÑ„e£
Yê­Î
Ü‘¥÷jO¬ÞÐRžÍ^e#çÓh±“Ø€ˆ8Þä]Þ9; ÜõåRüeõIFÊÒ¥(Rj<Ò$Õ¾%¾ˆèÆ¤„JÿˆÒÍ£J¦¤R	¶R
À)ºÂ@ÕÒüíàÐcÑ`Ó{œ}äG4GÃpÉ  )€˜#ÀSyÐÓ¨‹K¹4ŒŽ»ô`$UB§¨DÛ\Ð}P3È‰)Ì ƒ 0 8UDeLŒð„VôˆÉä¾Ê‚˜$[lN1ƒL,×«ePb%âEá(lÊ×¹¥Aï8Áý
 w|A!Êˆ0RkLÊlÔßvÊÖ< Ö<fRöˆˆNÁ-A3õ‹&Ó¥0S4=áˆ-ËsÉCDb‚ƒaæSDôSTä,åá; øJ³ÈxÐ†/ÃHƒWne¥„:øƒZ‰Xxƒ\‰W]ŒZè†&Ãh€ƒaV
¢	¨†_¦U5fR~HÂ³aTfD@ÅÀ †¥ø¨:Î<CŽ(NåF$BpV
q.ÁÙ›€
¦<F¼^ŽçˆXT%<Ä€         (àA„       2   
   ~Å•«æü§’¼æþ¦·‰ã‰L
O‡Æ6ž™÷ÏI]¨ò"&½šÌ%~Žó~ ô                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Logger.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 `
£%k¯ `ï–%ÄTåò%4^$ž¸7t
**     "            L o g g e r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                  9                €    ¼   01F280051n     h       ø
      >        
                                                                       ÿÎˆñ   Logger þí˜á                                /“m)R¢y?íéP‘,è—Žœs•»<0øæ,:¢½¨ÙVƒ¼¾GäCÍ4ˆ    é
  î5    
d0ËÆ@îz 6bÓ…‘b 
™Ä`1@E‹" ø,‘¼›ÀAƒÆÌœfèÔ	aç™DÚˆ¡CÇL€î2‰óXt I“ò,DT€¤	”!Bš*”¨QHOÞ`ªàš;fÄØ‰93Ä°bíycÇMgÓŽeëö…‘•hªÅšµäÉ]Åœ9SeŽ28eÒ´b8mˆ©2ónˆ3cóì½Ú÷è_y˜¢igŒ˜68£LQr…˜"ªAùzþœ4E×7pÜQC&š—eßá[Œš›œi×>©¢k1oÊ¤æMFxMË•ë ŸãÃ…åËµÊ#ÑuŒÂÁcwŒþÌËð´Çwèª¦ÌuíÜã{_¢«;ê0#ëlÂÉ8b^IN¶Îø
¼PÀ
™èÃ%ÞR+¸3ÔÐ°Œ¡Cî'—
€ Oí¦öëk<˜"Ž7fŠNÀ†pï%'Ä 0.BˆaH!‡t«ñ"ˆ¨Ã7Ž4
EaŒ­°èjë­õa²+';l;,j<
˜2Âˆ9æ0Ã˜âŠAL¿’‚(B¼Xº¼Å­3‹0ã<½ò”oO3
CŒD«¢MÉ˜„Ó"(S”tRJ+µôÒ>ˆ¤0Õô"P1ˆÑ DòÌÔR#BuUU[ˆÕW]M5VZgµÖ[eÅ«dµÂ uØˆ5öØK+ªÈ}6u–ÓZ#•Ùå>í4ÔJ­åZLIE«]êµÖ_[¤öÒoÅ]2"Ð–è+ž ¢
% x‚3î‘ôÚÈ€v0ü=w`‚‹ú Ð°ˆA€v¸áåÆµµÜ
¦–v“*s "hœi;ÈM|ý )!_rèT1&7XŒ1UZ˜‹eNž"Šc8È˜¬6qê¸Æ:@yÍ9RpNPþR¤Ùâ”ˆh#‹**#¢«_(ˆéG N%æU	–C‚ˆ´uöRµ±íÖW£pM·(‰ç Xa¡NÑn½ÏÍ¤
€î+‘9 )9(‹Âæ–¶ƒ^Øª‡Žîðî›Xú-ƒˆüü8ä‘çÀ·“ÝùEå‡Pí|â ²@™=šHÖñLÐ™gŸUZÍD/št¤•~t$§7÷H © ¢ÚÙ¬ˆÞn® òìˆÄ–µlÚÎN{ík-m;|Q½í^WÚê6JsåOmÿ}bóXCñ¾RqÃðÈ“R8"†+‡8}ÌÝÍ\ð£”Ä@Ó‘sPCš 4,È*XAÞÖ±,{pC FÊÑÁ ˆÆ" Äh,±»žý,h! iPƒ2Ð¼I
Ü@ 4\Ç8#jA¾r‡ëPI-uqCÏÐ;Ð!
rd$C™7ø 3vÈC+Ó¡Ô8‡L|Ld&S™ËL1*Ábò†íÀ‡i/rhR2:¨Á‡`	ÁæX%²H
S Âñnº©é
w€ƒ‚ó¦5"iQ9‚î•È'µ1)ÖŠcS˜Ã$&zä£U”90mxAÑ•‚$ ÍWx	ˆ Õ°GÈÐnÔ‚$!	;p‚v@*ì€DY‚”™!8æRðCc† 
iP“ÊÐ16ÝHi,ì˜ÌcÞ
KK”ž’BF:2ëôäÓ™”7 gLÌOpâòv‚†*)Ã´˜Ãõ HA8‰‹3¡ÉLiRS,×„C6Ñ¸Ín~¡âÌgRª€5n1.©+çàJX4³Dcen™Ë]öò—Áf1šL3‚3ýü'Bç@†Šžd ©ä=d†š¢=#Í`Ã
]È2CiŠCrÎfZŠJŠî‚%³8!@JeâðnuŒ9EID¨ „@G:ÔéM|ƒM>¥“Óç˜2#<á
PÐ‚¦ $²’ª‘,ç ’òºó¤ &,ˆËSUX %% <•cbß3‡¼ 
ÇiCj~àËòÈG!h(f¨Ä¸î±tÓIxÀ”yÆå1l`
Ô:¥ö¦'íZÐÛÌnÖC©‰jƒ^—«’=û\9¹ZÈ7)€–zëƒ¤„µ
÷‚5ØÀ¦“°RmUOB]AÒ0éY;	ø^øRŠySóˆÕ°ÀêàzáÓ^­¼Ûïe
|“Ôø\)ó¹¯VrCß‚œ+ GÂf°„+Üõ…}ñÕ‡=<0úa%z _Hì¸¤âÄ‚û°ˆ€?„éorCñßÿ<“aôa€ð°@ƒ¶8!
œAŽ)V³ ð\ð5`R¼«„¯Ô_¾( BÜŒ™ƒ”öF
40@aï~‡“8ØamPƒì€“žFCP®mq£$É“X5 ¹ÙMo~cOdš21/(
´Ýwžd¹-[­ã<!úI„ˆ}àrûÕZ ÐFÀé€™Þ.òÌ[Öœ5­ÑÁmu Ú¹Jº®IùSÛGc
v¸ƒšž©›êüÙ·g€æ01€é
TP” ¤¡‘ÄÂZ‡iÃ¡	fYÐh[‡ ÙÙœ'¬#Ý{ê7Y¬ÆþbX;Ý‚#h¦qÃê T!„Ì&âžj‹	6_çÕWz÷íp~÷6ð‘Ílr ì©+Á=¶ðƒ/|q vxÃæ›¿=><"ÄíKA `,9þQ®Æ—Óø ?î‘&ŸÄ»R°Cñu ª%DYÞàË:h`@Ib	³­ÆCæì,…¾[aš×Üæ7³°4rÆ	Íë|îqË#fEko{ðƒìÂºä•îI!Uû<jv[ðCµô°×ÇQÁ«ŸKV"tÅP‰IÁÐ¤&6Í“Y`WX†èÐ3~æ2YÀç`s‡ß‘•9SÀ[ê›•{¼ÆM´<¬¥X
Ù–ÕŽž|Ùå±± ÜÚÕøÄü'O"‚ˆhµÓE˜Ìðj
ê±¶~<î
 î±{h²w>g©¶Cv¦
UáÚHí‰R™Zú—[
àxÞE¢7=£üküMxDþ»ä4üál‹8±&~
[\ýæU 7ßŽs˜Å’ÊAF®¼,ØÅ}ÉÄè—ÿÛ?ƒ‰±“ø2Î¨1€`9Ø«‚8Qù±Ø2Ñ²–sÀ ` $ ˜“ RÉ
|‡Ôž#
€ŸC S©²Zž‘ (3§c37ƒ³©{¡È
Ú¢€‘›€¾H:µ³
€ô*¤àP;:*qj!²»¨“qÂº÷š¾êë”ëÃ¯®Ù>„
…Û@ñ+?ñÃò	 ›I¿s?—ƒ¿a™¿Éƒ2¸¿ÍÉ„8@£È  Ÿtx9ˆü)À T@»y¿è
€œ€Ðù;x¬¡`€sHb(ADÜ2
`‡ôˆ¢C•ñød:T3Œº8ÓA?”(³Ø¡ ¤BÊ“ ˆ¼Ò+¾ò«PB¶KE'œ‹µ¸’»8ÅFÂ1á¸æ¡>ËÂ‚ÛBì1Áîˆï‹†ûžP90¿a9?h¬¸4¼@$ã8½I SqCÙˆ pôC@ˆD˜…VÐq´r„™sLÇ ˆGÚ˜GØûCy0ÀþñB\Ÿ5<Ää“ð¨Çð	|I‡Ô)J<ÁŸs‡LlÁ3 øÄ3
„:t!
AH8ð‰TÔ! 4aÌ
<ƒEYÌ«½ê«¿ÂÅjÂ'ìÅ(Ü;“tEø±Âc$¸DGeä>/ô¾…
?ij¬2|›;Ã
k?nÌ‹?c‰„0…ø‡u<DÏ æq¬œ~ôG•ÈŒëICŒ¾d‘ƒÄ€`Á„0†‡´Ä`Aµ3¼ÀÈ¦c ;ø"©óHœ0(:Z™$¢“„””œ“ ð‰Ò°Œ"+ÿˆ"*
<"¢»ø;¤!+Ø‹˜Å–´Åà¿š/ >Î›´¤èÌ`™ÌÊ,L_”BÎìŠ#\/dâ‚§@pÔ$«òLÊ´Ì!šM`tÄÕœµ×ë
Ð¬Å¿Í"0‚!°$øMÝà|Íá”ÍšÌ$D	>Ö\NÏlN—ÌÍ*˜! #°NYk-/²B¯ÊP¯àÈ.œÔJbÑÉª8~ÒBëáÂž
Ÿ/T° Ãi„¸5?3\?4lPŒ[Ê -Ä¬Bü\Ž>Œˆl(=$úI
µÃ¢È#¸P¨É…uœ°ÄA,K	Å@üì@tJ 4ÙLa †iÈxH€T Ö‰PMÄ‰´ËŠ
 ÐËP|ºüKª»Åe“‘üÁãhEÝKÉÉò´E˜dBÚÅËœMÍd“Ã45øÒOèáIêùÉ.P¡Ã¢Ü5J‰[PmìI‹8ÁÌ™P’ÊË‡# Ñ È Ÿ(ÔýKDÅGbIÑ”£±•cQtQ­,ÈØK*—À€pø4 $¸€$Ø 
„Ÿ3‡‰4ÒJŠ"PR4EŽtRtLõRUŽ©Ò›¼ÒScˆèR]”Bx“·+85(Ó£ FúÚOdìIí[ÆJJgÊSðÉ–¤¼l,Š Å05¤Ô3mÃÑ—Ë…@Ø…M`ÔaqT?ËÊ	È¢h@$;J<•	2
´ŠRmÑyÝÀ—‹?õ
ÔÀxP×I™„€í‹5Fv=ÀHÝÆž”W¨ÌÒ¾(ÒK¹× 2"R
 ‹Å
ŒíTQD»1Èh­WÈ/‹x˜€         (àA„       2   
   ’ç¤gzJÅÕ[ë—*„˜ÊÄ:¯{QTl¬þ[»ÏµsëŸ­ k                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\LoggerErreur.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Lÿi]úõÐ;«i°¿¹
rv1¸pÙÄöAhÒ\¡¨     "      ¨
      L o g g e r E r r e u r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          	                  9                €    ¼   01F280051n     n       
	      >        
                                                                       ÿÎˆñ   LoggerErreur þí˜á                                /@¡ÈÊ‹YˆƒÜnüñlN§4±GÝG~ÝBñéñ ö€mivAÿÜs‡„v»’W    ü      
d@ÄÀsAî%(cbÓ…‘b 
è|`1@E‹" ø,‘¼›ÀAƒÆÌ#sæ˜±3'„:uBØyc&‘6bèÐ1àŸ»Lâ<.HÒ¤<
 igŒ˜6E&eºÔéÉRˆ9s¦Ê1d|âÔÉ3Ä8pÚSeæM!Îˆ±“Ç(R¥]-~•×A*U«X}Æ™£æŽ­€.y2å@
ÞÀqsD
™4j^ÞÌ¹³gÐbÔôüËurÓÊòTˆM#æM™¬žÉn{×nÑs|¸Ñ:ðkÂ$ÄŽ±Sv4[Óo›Ÿyiü5ì§†1+PS†÷Í7À«o=>™p	±nìÔ1“ç;OŸ©‰½bMþz ÂÅ·»ÁIGŒKÏ²zï´3Ô°Œøj[-2×‹­#Ì¬»Ž0¤"Ž7r²½†˜î%'Äp//BˆáDO”ÐÁ§"ˆ¨À7V4ï>k´ñÆë>ˆ¤pŒHÇ‹‚ôÑ" i²#HrÉˆ˜4²I(Ÿ”RÉ(©œ¬*-Ò/ †ô²«$¿óµŠ*²@ŸÓäqÊîksÌû€ìQHåäqM/‹´R0'ÃrJ-¥‚sÌ0™:àÅˆb‹j #¬x‚ˆ*”€â	ã¸§Æ3õ21V ƒ<5Õ¢ ˆÍRhUVW½îÏ=pTZ9Hô)
""Ãœæ°ƒöjªÔÂ‡!‡.HÒV@¹Äh#ßœµJÂNˆˆ18È «Œš|Ú5Ã:|–¦9R˜Í¾	i”–V0•ˆÈ **#¢y›(ˆéG aå1I	®K‚Ó¤óF;
SÏB—ê³a`…xË.Ù½b=ÕâÉò`BãP9Ž(“? ÔSSMªÕV_Í2"ŠET» bË ¢"Ø7Ø9*í£Ø òAö¡$i.T 
‰Ö£‰¨Í„kçÈvÛnCÈ¹×_y6WFÁÖíZf¦pw x-’—Þ—ïÍwß9ð·Ê€_¸`!C¦1áƒ‡dXî*1ŽHb¦b[àÁ
5!@¶å§Pˆ<–YuÙÞ 7ÜGXcÃ "#Èh¬Ž&Ô@£>Àðß Ògheÿ~Œð€= š šÝé§ÀˆzjU¸)Ä®°óÏCÝŒÜ ïR[°½Ú°ƒŽ4|+®¹êzÃ‡½úR!„À¯gt‘S7¶"¢B‰È2
-µB ÃŒ:À¡ª–^zÁý³Ò2ƒ{¾¸GM€Â¨Àµ®æRÁB^Ð?µ¤ 	ä2Ã^B "Ôf>=IAˆVÃž7l¨#HBvà'ì€
TØˆ0‚ï± # ßC ÃpÁS˜‚¬0/ØP†UHÃLÄP†]ÕdCæbÁ|ð!†@yh8—d&=A	”*ßŒÜ›ƒ.™Ã
b2“š$oyUl¯ø”D$[ÖûMpðâ )„Y½Æà„å5OƒôñI^r¸Ãþ0ˆ4bÍpÄ$¢%<Lt")ÅåéÑ+±©‚É¼¼L°[œ ÉA†Ç."$¡	Q¨Bº†7´aø.Y†?R†s ƒ&=R­ˆØ¥@YI^þc† ™¡¸ü$Ëóµ/>jqÃwœ°žöL°
qxIò°™ gFó$qÈúBP›Ûäæ3=ðAB0À°‹]³™Tzø„+@A
;4‘/)Ó¢ˆh‘‹V4Ÿ8
æ8Ç&,È
339Ï¢/"tPƒtú‚ ª¦
YùJ9„È=p°Cô¦W=xÐŸ#‰
¤2Ç¼œxj¨C
Ð‰d~¦'øèKZÐÓ2fÔ@Y	çOÂ,.Q4Ùú;S*O‚š§žÛªFGˆî16qã’@WŠ“dq‹_ÅÏùžr³Ìä
w€ƒF“-²zírs¥ë}Äö®x ^zM[ P }ñËuBáB°à­muR˜Ýn´7$õM²SzØd÷TYÊZ³—Õl_-¸¥X®®žmi¿´±ˆ(Î´¯É@TÛ•¬@µ9pj301î$Žä" ÉÅ*"}Ô@Å'1Å±pBŒ“$ÐzÄrÇÓh3÷®*á
4­Ô/
€ ìƒuÐ}FˆÑ&ÞÚÎ#I"v§­ÞU-vCÔ  ;ø$xŠ	viºµ¬zÅ¬'±Ö@6Ó™Ï„f4†|	Ëˆ?¬F•žOÙ@DnªÎÝ§8þel*‘îàåÝ‰klä¤öŒh(nÏX@bfÀò g Ìyáœ’AÃï$ JcÌÒ§|A*8ÖÍÏ ;Üa&)8°n<‡œD˜]Ä QA
Úm4HYk iÂ‡&˜XÝšiºUT…5 cýqSfƒˆ\3ÅT¦
òXœ‡!€-ˆƒæP‡c
áW<³Wk±»’-¯{Ý+ÄþØ¶½mJ\EîaéÖ6Çæ¨±p‚l§9›YTŸZÕ}êJteå¬GûíÑ³öH=VPˆ ÂH…L‘‚ b2±° 
L†¤@à)”È­©§2qü¸¯q5ÌŠKëVßê$\•‚¸K ä»©
?Æ;XáÂÎºh¬Ô ŸÄ½Á|}·úÚ¿ú­Šð|Òíÿ6šF•GÔÇ> öà Wåq<Kü”°`Æœm6 ¯
=%«F;~êÃOB±@y#8#¹BPÐÅs4­ZlC ½$ØŽ.n²Ì_"ðˆbW*^­3~°8ç¶qÂ'‘ÓŽðþØÔ68Õ
ÎµŠ+
/Ãö¸<Dço.áòvMÿìôM>å¸¶j„…žs«Ïq`ŒøS…2s(ÇL&Ø±£H  li¢t_/Í¶~)¬°|ótbG=$Å†ºF¥®nª7ûxËf›r¢-­¬±ýZÃeÃ˜­nå‘^ßF.¸#î´@x/)7ÏÎ%y'íÓGöî‰„å_Ó¥.…eAí˜jÿ%Ü  ,ž ½ª~;z¦”Ö¨£}¤ú4÷¹èžØµ›?ðÁ%i¤µÄ><j
æ¯iU@         (àA„       2   
   þ‹È®f‘TZO);uúFh%éLíÑ‘‹qx8 ª{¯osài?§ØËyæs|                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\main.wdw

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 â=i¶§;ˆ‡¬Z:Õ¿½ò5+i¨™Œ¯_¹‘eï‡Á/     "      Ùâ      m a i n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          b            Ñ      9                €    Ì    01F280051n     f   Y  4g   V        >             a   
$                                                                                                       ÿÎˆñ   main þí˜á e¼Lj                           /®fhä¯xsìÎÆù°3ê80ªy‘£c
b^Ýf{Nð>¤^{$óúØËÆéêªO€a       Y        e     õX  Ýd    3@ Àg‰äIèFŒš7þ¹Ë$Ä‚j:HrvbàGö2rdÉO¦( äK˜ E
Ãa`™’.AÎ(o =*ÀÓKÎ\Jó#@¨€¾:eàR'TBPIc¦M›EÚ y"X¤¢N[XªV`< nå‚TwîÝ—P¡ÅKu`¼
ü®% mß¼š¾„Ë8.Ìƒâh ±@Ÿä
ò0HÁ&.
ú1SÅ0ƒ\ý2raµð9ÓI€iÂ…
FœXñeiÉ¦cfèûÙ8Þ ÿl èÔ±¢n…
L·¼Þá êHdG'fè„ˆ2 Î3uèt™c&œ.bîÐ‰†Ž™9wðw¡3‡L‡ z2ðˆ"q:` NsðAÕZÐ­2
Í©ÜÒNž Š«·aºî â¤+ñ%bJÚp‚Àï
;¬È—Flð “4¬8!ç$Ã«„PŠ0|äÒ
,$kâªª#
8*ë¬é<c
¯Öú*¦ºqjHÃHÔ0ª$
[r´&ŸŒRŒ)ÑBîÊ»²ÄkË—ºt*(7…D*µ1BRI&rJ)_2+Ï¹\êÑ)9ï¢“.»bS§ÅÔ:²Ì0:Î ]³Í*?RËÇ{ô#;%Ý³R§.MAÕ,¤CCõkÑ˜U‹Ò˜R)Hôbµ¯
åBežzh1eÂzb	ˆŽ’¸‘A 8Jªé ÜLHbØJ:ê0Ãªbˆ&Œ C‰7Ìã
3d‡²LŸˆ'Í‚êLT·Ž*LÔú”J2«`
 «­ºú*¬±@}WÕJí«ž^#…i9…ý÷MR‰N 1¤
 ’,H•‰+LŠ…‰e\æfd~‰æ la œÐ$žmöV  /´`æ– Zè˜ñRYOT16xªÂæ”a®¼K,²¥rbR#¬Sã—Èž˜²‹
ë|µÆJ«®[â»FÓâ»a›K´AbQ |§.8 ~ØL aƒi[Â÷Ô³ŸüÙ)AR›OKßF8Ð…évøëˆÅFT.½çÒB_Í†Éc ƒì.`Â7­@îé©rÎ.ï
ßwÚ—wÆ}·½¤à?J<ñÅ…2þ%à_Zžxç»«žä(Ã´U„êN¦<Ït®G‡8lZÇÎ›S¹VwËê³½Tõ €ÒŸ~?á®Š|Ñ½>ïD±Ï-îS
ü %¿ç}dD Y`¾ð5!å
¯_½³žZ2”Í±
8ø;ØøV>ÿÝÍt¶Z
Åã ¢*páØƒ`¡ìåÒ“ â,WA§0D …Pô$w¸ÌØ‚VRËí.€ 6¯qùš‹"´e“Èi¯RI¼!1'¹I|qã_ÃBXºôÎD&d
ßÌÖ:×K/KDÝ_ðb³©1X»›`ñp“
…4>2[S†õ3®î5L„IA·Á4ú±b'„ó²Åý}°vãGjE¶½=¨YwŒÉda5Å 3\ ä89$Ro‰Uº`RœãI5æïs’ô"%ÑgIõŒ!àiÐ˜Æðí%vn¼ËpJ ª2"H ê‘Å«Írk“$Ý- rÉõír ½DÌ/_BÅ¼ $È\[3ítæk•nfçdéÁiÖ²š l£6ù Ïxó4†T"]§HFòÒŒd•pÇ6^9hQY¨ÖM~(&Î¤Š ³è4à#îÀì|eÛ‚ÙA.Ò²nó!&U7PÖ­ðpäL%åêX=+‹&<ç&Ïÿ•4›ef ô™È5:(zÂó&êÈ˜`,7H
ú{F((¥Ä×RÈVÓŽ1…Iæ>ÒÎVAr rmIÏ1ò˜?ýgPO#;¼Ô€‰‚+1× è.Š„Œ(SŽª=´R `]Ô‘ô7Ö.Ž4§a$a˜¢ÊÍ¾üt&s+„î* è¤ª/± 6ÐÑ, ùÚd¾êP;˜Îô "á lá›ékÒx*V„Œ5iûPª¡Œ!ð%-tÐ
»9ËæÕ¡
cÊ;&Á7n)ë´`mQÖwâö¦º­ä5sYOµö–?%îiŒYäâU¯¦”:Þ‘h¦½âÀhêNF2·æÛ-.óªÔù¶/Á °S$ÛÇÊöe²É]ï)Ý
_:
q«ô•©}m‹ßÃ
„¬1+IyËWë=ö·0!p0¬RÑPV˜§Y°z—›¯ ÇW™œ|‰`©vÝNŠUÃ‰åow‚M 
P-H‰O,\™ xÅˆi±@,9ÏfuÆ\­±u‡Ýü"V¤=¶æ¿›ÖLâÅÈÁuJys^ 1øÅ‡™â”)ÜU+CËÀ†C‡ÓþzWŒ»2£FÜ*2Çd².Lpš]¼W6GX‰ËˆÁšãìêw»\F+ŸE\Ü@ÃdÐÅ-ôiÀŠHÃJsÒ_ìr °©Ëðâe¼(f¯@€«6Å”€†@( ÒJæ´[¥G¯
`Çv¶ÕµðuA­ã:ó˜Ô•ß¥Qu… <èÀBó]ð¥CTÊ—ºõÍ×§%j›–•š‹õoc<À?w“Õfo§çrí#j›Ñqô}óå¶Hky¿ËÖ)ªÃ|æLWÉ a£[ÔŒèh ­ñK·]ánÛÒYÞñ–ùýaŸÓ€C2
 ^ð }#®àÇÁ[ l'Óáó@½/|ïÒÙÎ&·žÍÝç\¥ûu 	»Uín¹b9ÉÍì^_ë ÌºÖ¸u®
³k˜öšµÀŽ†°ûIÛb_ùØù®ø¾ÏÚoðþ»™Ð–6«7m^ »Eè19KÛ¼r 6Îœƒ9 e®ì¯c<ì7/Ï{ª˜´»eèxÇšãíæ§6zîü6™^Å)OÍªÜ½E÷ÆãWæ›<ˆ‚×ö’l` HWº­qýWÄå×-,²g>î<{yÏÍNµ[Jï÷œýÌ7ðàüâÄ
ŽP†@ u²|õWÞ£ÝE-n<ûØÔ_¶tíÝyd§è¾ÝKîË |€¢ŸœÍ ¿K¨wF/¿¶l¯Nìˆ¿|âw·xÞ'¿S±·Q1ù4{æ#DtÍ*‰à¶ÉLà”óˆô
&ö«:÷ãVZ<-âºd«?»¿Œ0‘(ŽË>ÿ;.š€"ñ;<¸£2¹ëù{×Ã»
´9Ê2Ã
¶úÀ	€Ù™
Ô	rÊEC½Æúˆ¡‚5¬j8	›¯ÄÛ«äAº¢7ó!Ì½!\Â	;BÊ=)’B#,Õ“ÀÛ’4èã®R;µ½ÓÀ3ã¿ŽË=4´ßC¸
“·¸ëÂ¬“³ù{>ƒ=é#C0ã»3ì@A[C`ZŠZ"ŽI(ºJ¯6<9È‹Ûk:ÄºøÃ1¤À×‹¾1¤>ÚË¿»Xµ4L1Bã>DDð
@WƒBY£5Ós:ÓB­Îº´:„D²·­£¸
ôº”½›s¶µê?jã½^ù¾ðÃ—£ÂF<½H@X@Íð5ªƒÅæã¶Y|¹Z¤?\¬9]|A?û»iS1‚sµs
,ä«·+Â‡ƒ³9¤»:·;´Dfó(^/4ôÀ_ÅÓÇDs)sT>/Ä0;¼31|ÇèˆGU›Ç?¬ÇoDÈÀGÜÇ8T¼td•Ö£DÄÆé›=x´>#Á=`ª¶»ð¹ 8‡Ø˜†ÀÀÝYŠSúbÐòû óÓ5VdÀg|Àhô¨Âp;-<Ç2³‚à… îh50ƒx1 9°5 5¨5€Þ@q„G W k8|ð ¨˜˜ €HC ½@ ìP0
Á
¢"0J¤¬ƒXÊ¦|Ê¨œÊªô0‚	hB¨êXÂ€ÃìÈ¨€´øŒ8è‘éƒ¢Âãˆ8ÁÄ7XúD(	¼Ÿ|Á¶Ê¬;ÌS˜
Y¸„ƒ6 Jz¹iÁ
Ô "PMÖ4×´JA€­äÊh„ÛP
˜8 6H{¡¯äùK.\Š¥ X•¡ª‡â84A9Ô‰›D[¬Ä€»=4Ã4êFDÒ¾`JH¹A¹€ pÓ®0¤4ð¬>Mœ‹zàI}\´‡¬êÌÂû|Døûªí|$Š´@‹ÔCùäÃñdµ¶²Ç õt‹Â‡âÁþdB*Œ‰ ¬!£²P.|1+DœÂOìôP**ÄëP²ÂÏ(ƒé°Ï–rÈˆAH¹`(àÃ»°JáÀ)DQÎYŠ‹‚ÙÜ(µx´ÆÐkŒ½‹ÜE,»NAõ+M½+s‹ ¤Že˜ È^h‚§Q2@8’Ô4|j<ÝÌ8L55ÌÏÜ=?aLìÌÓˆˆ˜øÌ1õÑ—ˆ–L¨ýÓ;…Ó—˜„z	 Œ"8;DÍ˜ÉŸûžó<H¦]`…J]ŠÓìÛ|z™{M¡”‡Ù¬ÍÕäT3ðT¹J=ÝÊ®Ü„à” ›q:@ƒ@Ê;€= UHNÉXN¤2
çœ–’ÎÌÑl;Q*¥·1RíÎŠTRÍDuò”óÄô| •˜ûGšƒVLÌÈù”‹ú¬ÎrQ>¥‹zØOrìI~DAIlVk5ûsAü›Ö3RP@lPÄ‹5ÖfZ¯ëL×"BžÑ$Ø“ûÐéIX*ÌPXUØÀdÑ‚åÏEW*œÑÊ¨Ñ†rCTÚQÿ4…	R&RŽJÁÂhO0lÇïÔ»ð¬¼žú)%Ù)­4‰+EÌe¨€-íÒ/­Ç0Š@ýˆkå³–ý#a™‹5-L¨í©#T¤’SÄ Ó1‘·B=ÍÏ?
Ö=¥Ì¿ÓZ T8TÂHTbÎd
 GÝÄõ‹Ô¦í©J½ÔWÕM¥—¹zÍØT¼-U½µ—«”„ÞìJ[xÕ
hˆY
ö TˆQÎàX&°
 è b
 vÍÇ-YˆP–%g%PpÅÈlRpôEÒLZì‹mu
ö\AÒÍÃp=Ýq}Ø
õ©uÁÜEÖ„×*åÎy}Ï‹ÃÀ2¤Ù^|ÒÍVéWÚ9Ç¡ÐÎÍY¡2X¢BØ‰}Xì ‰åX…XíÝÐmÛŠUQÛiQsuW¯õØ 0œ˜°Ñ ÀQ‘=œ-_µ(QÙ"
Ð|qY}£×\\RmÌ9ndµ›•Rü®»èÙtX†
Z/]Ð¢õ“£UÃaZÚ3uZ6ÚÄª7-SÁ´Ú¾ÀZSZÄÓ®ýS…­\Û±Í0[CETE\¯}Û¹˜[l•ÔEŒºe…4XËÂ
ûØ–ÅW£‚(qƒz‘ÉÊÙÔT7ˆs±ƒ9°—(~ÕÎp\EJzÑâoÃ.†5p;p1Nb…èb1Èƒ3Nã5f¢-Æ`:ö‘:öD˜à‡òúŒvèƒ­u\‰€À$[ŠU‹œš (DÉÈh	 “±Ü’0†k	€l)VÅè–o	—q)—sI—ui—w‰ücqQòÝÂ¶]>:$^oÅÃK4ÝC]§0 zÒ6íç]Ÿ¹œ’a;]™¦y™¨Q‹¢¤q‹cˆŸAšbvŠe€£	€dˆÙüˆhÚjž‹©QgšÀÑMRÚ•e+!È|}ÒÍùà®à­ÝY¾]SçrŠW+œ#JeŸ‰ÍmHð]åeõß§]qŽe&•g_ÒW„9Ð|Ý˜ø×úõ)Þë}ÂìÕg–ß†ÝÞðí^
¥húDß—È(Ð•TÐdç[tçÒ=h|¥OŽ¼1¨°”ÔpVéqfiñÄ
[ˆŸšŽˆ’¡’€ vXeeº«ÆWvÇø”Öœ¾ƒÌÚ…6›z1¢–XŒöZlã!'l×
]àŒõêêë»p"L†Òc5j€ŽHp.^˜…O™=Ð§Žœê@†Þ¹€#€…(ÝõQ~ŠE<U5u¼§2Š‰‚r‹¿ŽÆ
NOjA¢B”öN¸FÞ™…AMÚÓž
Pš£Q:ëßUëàÄá•×¥ŽYÊ–kåMPæÝWýÃk¹0¦Ïæhóêfz&îVvÈÉ¶×LmjUhù§ˆ€¿ñt†äóZf]·Öíãåíäµì®g×IlN¤Á ÇF©ÃFasÊ”Ì†‰…
Y´¶_Våü=YŒ"ýÿÅ·š6ÞzÍÆ{ë”zÒJa© êëµíV&íÜþÖ›.àEªY…öF†ÆPÆj&× Xª¦¢Â¨¢Zvµª(üjô†Ä’mæ.íÝ¦ïÞ–îåáóLðËqh˜+º²«C+oˆºß‡¿id=WpXÈxnéû¦G7 Ü«é¬Ï
€Ð­ PÆ `Æ P-Ör-ØÂI*DêŽìgðúöíM|é\v§]ž
Çàs.è’.ë•qW
ÝFR›6h(ñÒrÖV;Q|²Âˆ1öýS)oknú2sV‹1óC2µk&›scµsßçìœqOóø~k>'`ó=7Ÿ‹@¿¼ƒìq3t‘Eô±Žqi<óxípgêósZ‰K§îA×ôBg»?¥(#ÂmFÿoRwOHŸoIñm³ƒsB†|Ã‡¸[Ïî\YçæõhWg`LoõOìò¾(ö´–m[õFÇm]oö~vÛöŠô—HÚ	´v¼ð¶¥Usùwx.çTß9s×Á’(ÆS\:GLFšä5gl¿a
õÁ¢Å#}tpgr‚²KÝ'Ew]6ñ®N9¾>vtôï¤.øæp6¿òJÿm\Žó°¦xë´ø*cëë¦òÙíx_7à¾«wv¼Àv…c¸;WØ<ÿÂ ~w„Çi,ßaawu`9’G’?WÏ½ødg¼v7xŽÇqyGèN
új—xºFR |O:Td:d$WrW¼É€Gvn—3¥.uÓ†îÊþužnø„~x‘ï=XOÄØ®uY<ûŒoú¿ñ¦†vû–vVáÖ•‹ï+¼áFú2øÓº‚Ç” Ø”¥ˆ<6ùûqürŸöz Ù¼Îû¼X=Säz}ÿzP7û¹‰tz¿k§þy§¸=˜·ãª Çï£s!>â3>äKú“Çx‰Ôø=wöxWøygÀ—†x.·úð¾ºÅÄÉ™|ºšÄŸW„ò£îÇ9~xOø[9~ÂàÑÁO2™¿G¼F<¢<À¥ûê7i²?Æ_nGï{S?í×ÿx­ÿ[æñÚ÷¥ÐÏŒhÓpzoÊ´5 õŒŸÔ‹, îÿ¥0b‚ƒ¤!¡^ü-P!%”è2Z÷Ê€ŸNÑ‘(Ð-àõi Û©m—÷Rß¨«Ü¯çµ¹Âf^¢Ú¹›{ráV<hæV`·s>jïÃõºèæöäÑìÓch‡A	²0ˆ¶
#£;Q€Ó)"FÔõöYC}×µ@éÄ]ŽÃ|&Kó‘?Î×7Dú+E!ÐIAÓçþüÝÔxò¯
²»ÿ… yÞJƒå€É½òçå°žÑ}ÇH¡Ÿ÷7öÚ `›ï¹@:èñ„ ÈóœæªtK^,„hÎÛ1»§gù¸ ìÓ3>@bú4ÖÔVPÙaÁo—	]àã„–Â"­¨Ë’(k¤°Ò8 —
[þc…úèA¦îZI$é%˜$€’T§hI/) H?ú¢aÊú‰„p¦	µQhòâPê[ò 3Ä¥£/è€½ ƒ€.¯– ÂK:áô	´Ô’×ú„¯ƒŒ¥p¨xÕ@ðKTˆô)µð´S “Z‹I„!•Ç”Ÿ$ÙšL8ä2)¤_©Ü‚ÕÚrV$(ˆ¦ËÁ¶b§ÈT©	pá¦OÕ
ÿÖmÊM¡vt˜•BÕpt 8§:°ó…¯,À
t\®Ì% ³ÞûOPÏeAâçýJøórª«<E<³â^Bìrw»®ûù<_H®îžïã½k¯MÂØwà¼„;Ï)¾@EèòF‰óaÌO2(E Ñ\\Ô†rA£…(ª(¡,š÷Ò^<q2,¸ñÕ_‚ÅLë.fCJÜü‚	ò‹~¹¸¾rââá Rû‹½­¬Q ßìßÚqAP,ª¶¿(Ö`Ü äŠ<k)<0
 Á†–Š©`€`± å·¶ÂÇ“
Q *¦6Ý 
	Ã
&¬ç²çuVØ!óQ.Œ7¶­ÓXƒfX¡B[™BmYÄˆ¨tXºKs~$nQ*KÅ
0Õóˆ·	U!P•Éü–¦úˆ×Ñ$O€/Á«²n`q™;ðp•®’‰A&‚›¨0pâtò]ëKz-¯âu^;›ƒV®å¸±ØêŽâòKŠx)²¾ûÇöP[TÄ]öÑ†-g¢Q ]ü\úÑÅ§X ·IêÐ
Ã†æ¯& î˜h/¼Åïµ‘ÐÁÊj
EÖEY….–úŒYÑ)Fu£®âŽ:oœñG
”Uø—{
]’úGË 1£‚ÓX›‘å„7‚ð— LL™Ôˆé kt
®‘ ÂÆÑrgã„¶q#Î4á8vãîê~ê76D²h‚hXqÔ	Ç‘¾°­*±e¡ÜzŽi$ˆMÇíXMÕÞÂŽ"‘;Þ&Bù?:ÌˆKqÑªÆõ¸Úã@xŒ
 rŠùèÙ¤-¬[ñvEþøátsŒ0Ó
Hì"åZü r2þÀ-õÈ]-›Š‹/6XÅÄwó¢\@³„=P&ÈÊØö.£©$q……±Ê wP‰´‘¿/n4
9\´•Ò’EêÅ5#Ãçâ”0GºÃ¸#íWŒ¬QH=ÆþU$AeJë,/I–Ês–9—“ôZPÒ-P%ÁÔ•ŒYòN®ÆX’Ù¼dÔ“TkLÎ©Ò$Ãr£š[m’¹¼IÜ'AÌœ´aª±&º=Éåš£û;AlˆYÌ|qÄÞ×SK¬‰=1‘Ç:"mŠVÌ`±9&ÅÚXYùb¦*fr±²bÆÐ˜ƒb$Ó–•7ÇxfÊÄcE6ä1Ôh4‹;ì®EˆAVÈ¼›Œ	‰L20Á™¨¢…°Ô ’]2ÍE6Ä4â‚

s.Ô»pðB^˜2Ê°eËÑ~ëèWRF (,•¤N£zÒ,‘C~ÙÉf š…m¢ÙRHœL£f„fFÍž™âfÒjäŒpÍB6»œBCjœ½o¶Ù¥¨D’z^Ò5V“ÎfšÊ+h
ðû1@º@Ïú_L¸g%0VŠ©°$$l–oÃuFšö*µ`ñ{³RÝ¬¶<ØÚ¥@P–ŠYFKkó,©%´–mn¾ÈÜÉ&ù"R!i¢®OZÖ™ 5¡¬ì‚Iå¥‰˜vÒä ét—¦3"†ÖLXO;AÍª]Kùû–Þ¿ô•˜p*Èü·¡0¬ˆFUº…ª¶W®Z^ÌŸ a«õ{W#¡g­…s$˜µ°).ï#ž«›5înÂJâ9ag‡Dža_-ÏžQ8k%õ|	Ö­ˆI­ì¶Ýr¡¤éŸÄÐ@ÂÝ Ûú<x£ò]¾OÄPZ(”T
›M_t¶š+7(Îë ¹ÐË¼¹ (ê,¡ ðy¡PØ¶)-(mã]ôo]J¶á)
Q:Àz7ÄXÜÎI:‰‘^´zÅZ:¥è°\˜ò³Š"6É Ø`hcË¡3ÔGðö Æ›ŽT¢…\ê/ØQÚÛüˆŒ_´ÊµÏ9º7ãåÔnûmnæ¼
ùAÃ(Tœ¢;Ž,ËÌ8-Sdµ¬eŽ© §â0%æT©
C^R^ÉéÊs"Ôx6À;j(A¡*. Ô•ç‡ûæe|WÀïR¢Â'Š7cå¼¥2HuYQó‡Y²^›uEî* ÷ÇäZËkF»åeÒd
BÅ(¥ë¤‚ovJ5tžàf	b. D…á#1éöÃ`‘TQÁF@caX§íå½Ðº9Oíf8Ý¤xŠœÒQ@ì€¡¿<S3iÓC÷Oueöû¦ôT“E€êFh Xuç”&O·Ðd”K§“¨KÔ›SÑ	F1ªAÅ9’4¡ŠÌ/
AªZ©Ât Ìº‰ÚßögJ}¤µtŒnÔŽÊÓÖÍ>G´[ÂÒŠ:Pá¨*ä…—¯zÁñ'SÓéÌ3ªþ, 6Ñe·Tÿg°Œ¤§s#-T¢ÊHß!¬§>Ô}ÚÁ—S_ÌÖóƒG®ßEç€ÊžÒ³¤)|¶KŸzP÷&Ã‹6¯êR‡
o`ïÔŸu5í]Ô]ª'E§ÐôÝ”„£—S·§dzq°)ÆQH@êWU«¡€N¢]j«ª@¨y •”ž ”
Nµ*]¦Å³|Ò»Î >kC[D¯äÐÂÈjR)jjµ«<”©Ð^XN3_T³5`:ÔpôãN[Eƒ×tÂ¸ÚMwëN}¡o4TbÖ¼ÚRK%_uRÕM…VTqû4èƒÄÚï°Ô•ªX½ª9ª¹ô»:TÙúübT|,µR×Þê' ß@|i‚PT¾UèTäpõ¨·q¦ÎÏ <DÝ+¢Çæ»TtU+êêžéõž¢Õ» ûb+C=®¶Ážœà3|ŠO 8>ÉƒNXáWVå¨f]¬ûd:Ç*œßÉ8Yoü”ŸhøVÕÏ÷¨†9‰®RÖŸˆXíéÅ°
ŠÿµWïº}À+LP®ì•¬?¤cmÒ ô±…ÈòTZ:>™ékGÖŽ&ÙGµd›I l¥´f§ßù)­ëèÄ®ZÖµ>Õã'AéLC¡Ísz=ÏÉ$ÁÏè“¬‚^8òê"ÿ¬”9³{±ÎB»§E
­²Jª½&Ö
»!‹Ë+¤}~ÓÇÖ(ay«R½®¿•«²Øõê
Áê¤IuÙà?,¾XDÁ¢­žÖEWW9-›õ´QÔâÓ,7j§jlƒY¹–>çšä +”«nðÇšÂÊj$Ulf
®›µ»ÂXKëKöàríƒÍÕ­R¿5xýæª•E¶ü3xIf+`…kÜ³pëÆ†Hˆi'¤¦Å¯³V¥BZ"+ie ®±T5ÑJPv+kíjU¦!ô§6Ó`Ai‹à{ÅLýßÂZˆÄ[),¸Í®Ž³WØ(f=+1tTÝS  Ã  W’±rI0	ÇJi¸c›B…aDÑÖm®Ô†Aàp.Ñûð‘ˆxƒ€á°ªÐ,Ä¾Ð	ãÑÄC€0_X¥Í
¸¡åÊ%q\¢qš¹$ôCe8>ÓÀˆgj‰I“cÔBDap’¾<Do”™$¢Z ˆÓí"&G
¦ U‡ÒT•DqW&©"‰ö¢æºtX!V"qbº11rõªëÆ§*W¦L¢ôµÏjÅn‹e[§›µ¥\–„ò8Ti@ák\O³tñØM8`iåÉ uW^ÝÚßÒì¼¬µ¶µ6^8‹Kå,0:q"²«‘ÈÃ‹°çõ”ž£Ô¾ÊÅ=k=­Œì‹4ÒÌ¢ÜæW8B¸,¤ôòO•K éÂ"…	ËMÙ
Y³ÚUq-•—GÄø*,{)%C€¾$ZüBpØÞÊ%"A| ,LÊFCï‚™,®“_éÆ†Ù¶|#ÕôºsbÇŠ©%á#ÆT²“‡ùÉèÈvG¢©òŽq×P
ÊNew Á+È»¯ªÕ 5 &å\Ä“1að+ú8^»åï½AöÑÙ³*oäG]]H1B PWÉpï¿u¼
d…ž>óy_°<¥<ðÛ.Û†KFÕŽ‡¬Ó­ÎŽHº€gnX¥0r/,ü	|A ]Ìž- lOÀâõìQÝ½ßR-_ÐkH0òAò#I±Šëžï Ž¾·¶ÈË×k}‰i½Ä
÷rp_Òè}ø=6â×)PÝFÁfú%“ï4a¢É:êÖ¤–¿n’þº_²ø(fÚºaÕõOeÌTé¿§Ùø“wËí.¾ª0&F54à½»ŒÒ@{âï†JI¹n"Ö” ÷ö&Ü¬xÅ'æ%Ÿ©×™âÁL O°´¼³¸©fÞq›Tf0„”ÁœWÓÆbÕÚi·ª­m¶-Ö¯~RÖËK]/…½¡÷Ö^#üŠW¤îUÂÛ˜	oÑ'|ho‚ï¥ÂÝ8 XááKÞ˜bÜÂˆ4e¡K"ÙH×ì»ÁÒ÷OR ¥Ø±6V³á^– 8ü‰Jãi¤Ãf*`r0ö%Íïþêw.°ß‰;5y^Ä³M•âŠ›#‘ã¤"Æ¿ŒdZÊ— ‰
¨$žT ØniâKlœJ&ÖŽm«d
@qK.çñ
Üª\µ«NñL¼OåG§\H70jEÆ•¯ØfjÉí©´V&8=ñâyÜSW°^UÊÂØáÚ¶;±bç…ÅúÖ‰ò[qÊIí3.–ÑØŠüà4Œ*„0?Ö³E¸	·­Ù»{ã²9>_Ú²úV®,£„oüRÇòúB6w,$áqVvc,Tõ:}kZYkøø¶a)y²#ÈXò º
;ð°qÅ2&8d¹ ‘ã«Fnmð—J8CLˆëï*…	‹Ø86â´ë£D2h%ÉDT.Tb”ü’m@HÌÍ,ó#îfš;dÁLÞ‰K ˜GHiärº€×
ÞU”[ñïÍËtSxÏ»Ê>sðF-£¸8ò‚ÖÉë  ò
†¾HYÜŽåÍëŠap¯(Æê™_ç~œÛ,-Þ²¶øñBãs"£cX±×YÎÅ¢fŽçruÐy¨‰cÂAŽ­êFÇ|™fá¿ì£¸p ðÂ™s07ß÷Ö‹ñêTÖ®ŠùÙæãÆ¼×s¯ôÇR2Lf#X™ûåe˜³5’_…¬‡±nCîÃ¢ÙN¡_¹P‘3²S˜¿ª“8Ž|¶ò¯·É a6‹ß¦õ]‡Ç$b¤Id*²We2Ý€»™½¹6U±+–Åz&û™¦fÂÍŠ 2ï!‡Ñ™rlKÿ¯1½0‚æÎ¬Ò)¦( M8­4cÓ4AN"A˜@ÈJ3ÕÌÓ…s‘]7p0!&Ëå¢âdÔ€j¨FÄ4|Ô«v7§ð×Õ•¤íJ†Â«34ÌÊ˜µú[ªì¡
ˆÀ=9
Z¶QChYd¿§úÓ'RÏ45ª¦F)¶<‡[`Œž“J¬jIxS§êNMk—ñéeÁöYjú’	"“û-ÒÍZÕà†°ÉÌ&ÚüdC 
<0@ÎXh(“/©…<@¶g_åÑ‚JNÌiÌ*§6›œ'²WÏê#Ì©5´væÐW'yYŸÂƒ£d¨
AÌ9@V¹PJLÉ)A%©D•ìE3¸ æƒ 8@ Nóç¦%‚|§ë0H$#sü¾„ò+£ÑïgxÀÇTÏÍ
Ñ¯IÀØæ¢.l¼”°÷R‚(
Ÿá”*Ì¿ƒH$ æš`¨ŒC]ôhºÎC»9jÅ©ñÈucÔ} i¿ê  ´3âý„×üX^Gå,KŸß¬æÝÕÀ0SãÝ{µÉóQÆÕµ˜k‹^ÄÀ¶9ÛòÙô‚jW-‚/FÚ ¦zzŽè¢|^³…·„
~"kÉP–µFvÖq§±lˆhÝy8ÙÙôdèÂZckm]ÅºµLŒ	á:B|í ý«k5Y½ÕÛà‚7ÀýV®Ñ½	çâ_· û€ôƒ–Fx€˜$œ{!.„ªM¡[fì±±éLå%dƒ)ÏüJvjÆõ’tÓ
°Í€ÒUÝø!9Õl˜p³qÎ–€g·Ä’ €6¢–Š:ödjƒC5
eÚ|¸<m¹ý¤6ÈÄÜ.lëy=Ÿ±ÙÆhÛk¿ëµ­¹­&Ûž»W,ºMOu¼†ßXûòÎïú|¶÷"Ý¶Û¶oVþ½·ámöÛsÒÁMšÑ®Ø5ÜÏZ!$n»¸©µã¾ÖÙz[Oî\¹ÍFûf l»€m	^ÍpúÞ¡Û3†*  Nwz0©;?8oq
1@‡"Úm3ÎÆ¶Ì¹Û ’¦Ó”ïŽÑÀ{FÃ„áý0…2ÊÆ
9|‡»ìûðÃY·o€Þ/AzÇß»öÀõ–dlo¡}Ýµù®(è»‚«oòíˆ›6À±ãÚM‡^ê„¿37­Þßœ»×kü„¸¯&äcUUWØ	ni8¬¾ßj{ðÍýÈU°ÖF½
œ,òªýÀ¯¬#vÛÃnÛëQ}Á÷àÆÙû·ƒŸæPÂËf'K›/àq›pÉí­C¸^á‚Ü}»ðL¬éqNÊ¢º‚àëºvÃ[ò
¢JpWä  Á6¸' è,0&víÆ.@Wè~_ýÄ t·wƒì*N´‚7HÈâ}¦x_Üð€vä*ã áŒÃr³;ø ¿\o\ í¢-Ç¿÷sî#â;içqÒ´Çß\·ÔÀSûò®¿¹0—Êœ¼X{òc]Éë6#Çä…\“ã`DÎZ çèVû…—òOÝªQy-åù»‘£½î©YµXvÆ,]•†ÞÊ9¸	*Ü;ZKkZ^­KxäæÖº\.XnÁÂ ¸Iæñûm«t¢H)lx‰UÌüœ{8Á ò``k®à€6J|ysÜ
2t7ådš™ŠÌ…|Å…·xÑëv
ã«ŽÕé¹`ºçC7Ÿ
BÀÏK9€ãÝÏqB¼‘úo[’|wƒµ×·þUêwÛG¶mßjÊ¡ºHWä½gcQžxIyfOé4½*ÛïØ*«]zGŸèN]³›vcÞµS;D_ê¡³—Þ×®!ïø ­œ•OoWÎÓã/âêŒ»–ßr¢ŽÂ›3/¿Ü´Ý²³œÛ¾ªÃ²n÷ã'™ëëÑ=3Â€v 4s{‘^€‰Q ¿ ¨€v±‚ÈLâð‰¯è¿x^ÀãûœWÝ‚Ÿ&ˆiç‹âcß4}¸»wŸçN·ž„¾þÓø@p*#£\.0á
žH¨’š³Ü¸2%à ¨s@¼úð¯ç’½øï'µ(»ÛŠŽÀ·vý¾Å«7?Ï´*àËL¹^
ÑÌfl³ÿË9Ñî
Ù]°Ä   ‰ â @M  àÁ88ïM@/(Á @
"pï·»‰KÌ¦UÝ::‡ë"{¿†÷h@›Ö…ˆýzRw¤È>É£6_Õ) ” ˜°Ï õiÌÃ…·ÄïÖ¿oeútOÌPûC>³L_Ð25ÂþÙ]h‰5”qoVôK¸†4úl©à!0¹Z€Ò÷…Àžb.aÿçÜ›zï8NÐÃ÷b÷£½|'öó]©;œÄ†=”_ö“NÚg:u'2ŸÜ³—tÛëq{i§õ2£ÏvKþË%:L7ä1œ˜Ÿçš.Û!n©Æõ°¾©p_\‘ýi_à7=_ätà¾ÓµýoK¼¸p[>ÔO¸Qw
HýATv îì=:EÏÚ*¾“³øð7Õ[D7ìþ»¿, n ¯€ˆô®ÞË/ëU>è¦õ; _B÷œö}Šwy+>²±8]çÏ¼³:oˆzºû½Þ|ýG»D’ÐVä¼\,`&¼ƒå á§s9®Îùq”ecožsu² îÉ¼ÞK 15Pú¬ïójÞÇü²Àá}VÝóˆ9Òîvs+Ý~ß®î”’ËðbîçÝB*e
ñ ”|K ’ÁÖ‡>¾3  ºBæ›@, (°	 ~Ð	ÊZ |C4qä°ƒ`]ì‹±Oî÷²Ù`äü¿uü^0å:Hó uÌôØÙI}#AõÆÍÃù—°ÏZ€§§óåE+××ÐîòE;Ì/ú|›‚7ý<¶ælëÝÏ„^-úè‰èµ¤GÂÙ‹õ¿HÖ_—%½¸ô‰€\z©1ÀLX
%( ¬
,ª@Tj! Ø€Â~W Ü4y è è ¿þÐß^ll3ý½ˆØ6cúWê?ý›ÿ†¸ADÀü_ÿx>¨ @ÿ
áOéö/nPHi þ?à% -¸?nÀ6NÀÿ;øÛ@!€Wi”‚Ð¡³¿ì¯3O§[ »¾Àí
 $À¥ "j «¬5/"³Èª¾¦`ëKLš¬/Í±«Øpj Œƒ$ ‘I,`: ïŽ¸[M•S#[‰Ž
3H@v€5¨p¿˜ >u €_  (9^@kªl«k‚ ª„\ÆÒû’€ 
xÀ˜ ¨(  ¸®`À X °R ðþ  (8 ‚< 8I Àhš   {FöÀ`*ˆ¥ª=NH–ö´ƒ,;õºÇNXè&±GXpK²0( ;Í °Ó#ºP/ñs!Ò<¤/4õô86oêYü\ƒ’«ÕûìÞ²*!º{úŽ½™Ú³õ€=WoµcêÖ=×Ž×ëù:Á÷¤{õ ;]Oéóü$9\0•ƒOÁ¹IÜäÜ½‹nèà¬½  Ûc9zºm#î·i­q
÷ ·q¯ù{	Ì½Š&¼äfÁÖÚÛÐ,ºPMê;æä=ÀÇƒüL	]Å!PÞÀ 2 éªÀ ˆ€`M˜³Š`€	àPÌ à
Ð
ŠÀŠ›[â¬<øN­ç`ƒå¾{ø¿¸Nâ›ë^ÿî*ãÇ6„u°h w¸ Ì‡B€zðìøŠ/äW‚‘™éÙL> åT>;Ï"Ìüj0ìLÖãóŽ>Ð/á+°¬®@ï,ÊùjAž'¬õˆ>aÍ
}=d
èóßŒµ¤°ó‹äf¸`0+
ƒB¦o(ü¨>^Âê‹%8*…J1ð »>zo ¸ûò>¸ËH ¾  @Ö àB c@5ëH˜û6KÁî[þô¾`ï;ÈÚ¹‡ð!¬óÔ¹Áï#(üÎ”Ã/Tè›Qccóœ€Ç/ÎK &?Q1¨ó.?Ô(óÊˆÂaNæ£ßt5Õ
?“¸àŠÒ÷ªÆ-IÏ{ôh/Õ/qxý°±Øï@óëÚ$°ý€@nF÷ãýÊ9É øÃ
„?âoy;þ’¿»püÃ
¼¿èïûÿö?… ûƒþ¶?¡û{þŠCè;Há–¹îàüCå lý»ÿ‚¡©" ÿ²Àúo;ò¿ °¬øÿÊC€ , ÿ®0\Í@ê-2,
Ç–ÃP- )Àök"C$0ÀAkê
â­ð+\?B@ÔàM@³!V@"Ç„­àBa¡Üï@AÂár@e $}@Úð%‰@½àL—À&s€§¶Íg
†ä+&Ð€1 àa/É@3
TÙ@7”ÑÀ:ðÌa>°êxFüÀ%©Aæ‰tÑ€´C0D*AF0¢xA"M2ðJ) g+ &¼-n÷ÒüÜ(šf¿Ò@ Êg)Äèº3ï3¤¼¬”ne) €kÍ¨À " 
ø×ü.ÁXn àî  Àß
  0!äB£åÊë$vÃî@O¬bÂ8 xÒƒà8âR8•aK>´¨ÎûÝ C‰$ &„
ìºðÐ, "(à <€xŽ 8`0^€"@¨Ä€+à)±Ä £dÐõ’ OÜ!²à:ÄÔ?p¯U|^ÅXqV¬oÅ\qWìé€_1X©’ ø
â¿WÃXœøÄ0m Èôª6Ö°µË04	U¯Ô«;ù°Q‹?ÐäRš=^ðÙƒáœB¨oÚ‹íP»eÝíÔ½pð_œ
oÂª)é4:î¹SnzÁUP3ú8Ãjoä'Fqçc??«ðaìíV9
îbzå„»Mä§£ƒ:Ìå´A€<
FT2J=Æ¥dôõ¤:kªkÉê½*YÉ9`à   ÐT€WÃÐ	Ú;Ûíàs”µîÇøH‡p I£=,&èïê:‹\JÓ¼€ãÛëHÂÏl@$J0 ( ÖÅî­]4ì4A{^jèæÅ(‘R³W=†ˆj“s½ŽÑ&ÌÁœP§ié¾AÀ‘ é5r0n‹
•½ðÅˆÓ½~QqT3Ã oÅÃ_Á‚Ñ¹£9FËgü¡ÂÂ‘·[ö|»’1¸‹UÇnOåD8k¹Ë§Ãm°—ÛwAÐñfGÇ¨Ïç®;÷ÊÇ›MÎ…ô K³%`ˆ w@hô›jìæÞ;pÎ‰Ó`@¢æÕØòî»¿kÄº¼FŠÏôË´îº@)épbê ãñl,JÂÆïy	
¶Ñ€?=¹ñ¬¶{‘ôkòF4/1¬ïòESpcüSŒA®q\é G$KÏ"ß·Àq×£
uFBüFbï£ëÜBºÏmÔqÇ’b<¥½Vp`ÌOG’Ñ4QFÒHPC¸jð¸÷^FÚ1f´½Aaï¥Û{,ÈCÎ€ŒêD´ÈQ-ðñŒ³%Ç ÜƒôAhÞ 6@ ` ˜G`Ð
¸ƒCÄb›ÂêQ!¼û’ ÈÛýBÎlâè'>ŠlÜÊÄF… ˆl†H—	fRAöq\ð‚s`BxNJ‚} (›YH–ñ „ % —+êH<™ð=Û´ô¼…qp$CÆ',ù	M6lÅ¡
ç‚¬€DËAßÑ)¸ T»Ã¡f:Ú3êì»›ÂB’wÍÁ8Ë3Ï\ld‹v¯¶L€TX
´#–dË\ÃÞk[ZÐø±­0êËš$Çû
Ì¾°

>ƒ%Ø½„Üi¾½è¥YÇöv¬	
*à!€JŠ<ø Òà
@â¶àø€‚/jüææ°ÖÑ¸&ŒÅI±XÞè iò(¡Ü(¿MnTeúB­1óq´Ï¬FûÃÐ8óMGë)z´
ð=4 •Žì#°“? m æG£
±s#S½½Q^Ä£(Ä‡²ŽËC@N_¤!#:2Ù
?FÂ‘üi¾,Ò•¤BÈ
R‡äìFÊ|íIÁ$kHÖ®‚$&£½O«w|	Æ’Ùã ”2‡œ$ÇÒ‘’qºß.€¹½ºÐÛ[WÅkÐTî¾5§@f”½<Çƒ±rÔHòÜyÈ2
U°ûàY´ 
ÀH º€mR(ô‚æm1ë´njÔ?ªF¦˜tøêIˆTœÁH "tÏž$þoZ@JL1ÏãK¥
-±mŒ JLpn¬ÿñBDè¤Äþñ¢üY=¿q¤ oÈ™rü)Hœ²«ìjH`.tÜŸB›2¨$W$HÍÒª”)?JC2µ)G˜|-GùÍ˜¼‘IÎ1§DMHÖñ„\E¥d*eGr¹‹*gHÒrØã,aKQ2„$%)
à1ðñ"'Pp Q7»ä`ËK
¼Š œ
bƒ
D‚ð­Œ%âÊ„—¡+“Ä.	ðë²uî#ø+¾1’|” °ËPy{Ùî„M/	×Ç´±,8¶`,;Äï±Tì,KÉr¤(ƒ#Ô ²$(JÁÌ²¶L¯J_µ%+I’¤œ€šËŽÒÛ=LéÄ-EÌ®fr¬*sÇÛòrLñtKÍ‘·üõ:ÇÛq‚t-ŸKÜò©ƒí¦ËÒ·,!³=¤2¸42‰K×ñÛ‹_È?2†ü¤Êÿl£¬í6K²ÇÌínAÕò^C ÷µ–Lec µË” •’8@ŒQà( ¦€hàXB¨`Ð
ÒKƒoœ¤ûÂ¹+2& "·H¼"ôòtBþn‘<Ùv¯”í&è/‹?p.©KÊÌ“,0©– àˆLÒÄ¡¤0I
ÓÄ'L½×ºûFÊ¤ôQzJ¥ƒüßDFSÊ”;È²ÃSÌ›2È-)(S“ŒÇ8ÌŠ1s|÷6Ç3§¤‹AR’¤1wMSƒ$!‰ÊÔñ¨.©¦;ÒÂ÷Ë&S¹Táš»³µ„1yL³Ý6“A^“g*•¹âl xLðŠQØ¾`øV€
kÁ5¤ÈƒPjl/©Æ…0÷^À¿Ëó3#¾½r"ì+ÅHÌÏGZ™ÆÍïrfc"l¶G“E¶q 4	K³Ó¼Ç>Mµ€^¤ñÇˆ_Ä,CÍò¤6SÊÏ2‘c)mªÒÖ$ÌªÌl³˜¬)?ÌX³·œ5q¯ZØl5sM5QD5}Í’Sæ4-±ÊØ’åÌ2…ÁcóÚ+*OÆÕ±HC2_ÇR¨s£M¨rÚLêrNrçt5YÁcÒÝzÆy/³\vÅ:À‡@BÀÈ€5@Ý\
?,Š4Ï:ê‘œ´>vî#P/éÉ„Œ'Ôï Mð1ŒÄ/NÀR8™¸N¯³ÑD8KHsÊ›4;=w±0ˆ8+ËŒSüÁöQ„0/ÌŒ2€ì8GÍÓ§Ü6)É–³×|93LŽ2¦Ä6OÌW³ê!]N“Ö5ÛR`¤:wK«ÓØÄ9«M
“Ê41OËÒö<==ÏÔÆ:SÈ"Óè´‡ŠKgsÉ\:ŸÊ]n¹¤6UÍÏ‘Õ”:iNSÓXó6ƒNŸ1ÜL•·yÐ‰ù:„3À ˆ %€X7ÑNFÀ›\;áÊ¶óÎ|;13Áá#@YøÌº3¿;¿úÍ¸Sïíº‹7 "… ì3Ð>Ïè-áŒïÎÅAm°{8%ƒÅóh›<5Í‰ò¬(Ls²ó¶~Í¨×ü WNXóç´é6HóÚD9IÏ×³ÆŒ=oÎÏs¯ˆ9+ÐµEÐÝSö,AuÊÔù´@…MÔô2QÏ–’_q½eÓÈ<ÜVÈâÓ…<>ñB§óÜƒ:KËäÜ<Ê¡pú2E·g2 0è…9 
p'ASà  J@üd7 ‚UÀüœ;ƒ	ö²Št/ëÍáƒ	DE{’¾üùJF2
cý0¾`
Å¬P£îÀÚÈKE JM¯$X(OÇm0ßÅ9",ÁJ­<AÏ)sÇü@qÏ”ØA;;ÔÚ”ASPJtµD[P´÷”WÏT0ó,5UÊ2ÍAKÊË“õ4E]OO”Û,6UÑd®ü=‹N£r¸BaG!Ô©$Ba(óÐc>_ÌMtôìDuMYÔêtB_Ð˜ÀÇÃ6Ð€ÝÀ^À´€v@  ¼P´3U;ÇPü¡u;­H¸Ó®Dø¿›/)³/ÒßŒCï¼¶û Qˆ  5ãÐ72 PÚÆsà U<ÑÈsçpDOÊRm<1J ]5yÊW4”D1mPSÖE9Î!9/PfÌçœ-!FRÔfäDyÎè’!í<]Ð‡ÕD%Òç“"%Ö8O
4Ä\EÉ–PÉE‰NfsM:[F_f|2™ËŒ4]*IiÐŠ4mB¹Ê´í†ðÍ"à\ ´O,F—ƒ.`  ÚuóB(?·Ñ:á£7óL å ÊÑ­Ñ
Å'ÕQâ-ì;å¢ô\ð?Åƒ¤TµçèÑ„/( DÑÅËe
  :û±ÒäGPÉs ½,Ð€´]@%Ðë†EB7Ò©3EI/ÒL´ö”DoOŽt!Õ=oPÞs%m1sÒ[“0…>QÑSM5ILÑsmLsO2I%SÐ¤%UXzÐôèT2{Ñäî­—OÎÔ¶4F?ÓJ4Ý=—Ñ Tåê†°¸d)±h~À ¨t(Ž>@ªTœ´JçÊzS‰3'ïÊøSðûJëÏsï¬Ÿ‚À.i|Ó5rð.
Ì|~Dù‘­K!Î»´(<M¾±âäRöT¢ìºÒæ“$MB5Ïô1mHqP’“ö=eSÏ´0EFS‡Q3==¶ˆÔãœHyÒ“”	-PGNæu.ÔË4ä\)m8[4Ù|IOiP&Í#¡Mäó¨ÃI[ÑRtAUBóÓÐt?½:¿ÍêSÿzEs¸¬ 'D  ¾ !€ P ŠÓ
áC«R¹Rk¡+ÉQ‘+mCÑÑ7,ÕâäP.®Œ|Q£ñ`F­Q
K´‘ðT8 ´gãôæRO¯¡LO%NŽâŒÔÞS¿T/0
Rˆt$UPwÒµ'õHSÒ“4Å½bÓ
Ó25IO9‘“µTL=PÆT@­9yÍÑ´?FMNÍDÅOÝTýô#¥Eœ¸DM7T’øäE•ÎšÔÉ‚ÑÔoPÕ9ïÓSÔBýÜpS9NU:uNÄT Š  (NÑN59"åM3ô*
G„à-É7¹Ç.’;IG©ÓuT,5Þ´»  T…NUµÁcKk·T¡ÔGó…´LO5N¾”-èþÑS$ÝS‹Q
õNÝìRQõL'Ô>µB]BAK‡4T5A)Ó“³\õVL5PPýÓH”OMyŽÑ?u
WsSdÓQ…TƒO.É\*ŸM&3D-÷FÔó4T;Õsu‡d&YÔ¬³%ëÿà uÒ^˜ˆ ïÃp ÆÏ 9ý¥ÂÉYÕÎ¤
ÁQöÓýü¼L#5¯¼'éOûRÐÍ1wÔK«X»Ó ÔJï ¶QxVA½B´…ÍTÊ µVG½¿ÃLS·UûÔNePñÔ
õÖ#Q5ÒÙÔO>×ÄEÕ¥´ØÕB•M=TTt•?W5U4j5WQÔOT4­ZuÐ[`…I;TIu&m*[S›ôREXÕT…UkeXÉVÛ2Um¾ˆuEŠ ]Å´ˆ &  ( Z€w *†€§d7EÖW‡d'UBÈ)¾Ô7ÏÑíWmYíOÑð–íüV pUWšuµYëQ¶q<uwÖú‘]d<§UÇsÓt@ÕSNPµL±qµÄ¼WiSÃÔA Ö„Õ]XaÑÕw=
 T«5/¬W¹UwuiýV3Sä53¢SmÏÝUjÅL™Â³µµ1MK.\tmm<Ôã²`uMeHØk&›×6•kuX«Ëï 
  :
ˆj€`SD@/`( vx@qõ¬Wx“í¤"¿Ñ3+
 žS „KY÷M½ï„CÃÒ¿rXuÉÞ×øu~­_“Õ=tYu
OÕµ<åROhõK}]'ÎötLå4GØ0µ„Öÿ´N[ßÕ^ïwML[Xêu´ã]‹×®52TOÐÁô…u^áUEu_UWµUv1­^ÇVD5E
b£Wí5m]6ŸØ#søXK‚u[Lõ±¢WÒ5•S%^óUe(b…ÒPˆ,öÄ£´R´À KÀ( ’þõ
M`Øós½L?GÖöVtÕ à‚NÍÑ^µYÑ\ ‚ûòþõK3vWýO5¶t]KO×¶”ƒ…KK–.u]¾ž/õG‹V	svSw;1u…%b—×¤Õ‡M_™Vp•‰1ÍWídxUHAÓ²5^Í^' –\=baX,1ÝLYÙ¼-¦{ekSU†
:U"Š}TSÆ¶õC_¯Ø  ‹u‹$TÝÕ†µ^=ÕloÅîº¡ýûLcÉEqàØ`ˆcÑÎ€ŽåF%oTý”\·$ e] XÌÕM™?X%ÕVá9'õúlè7 ‘¥R	LH–Y
¶qKµdãF¢í<EDõlµZ¥(¥UV>M,ÏàµDµ[¿Ø©ÕæbÕéÕˆ•f‘Xõµi…kÙh6¿šfV‘v—}ZuR/ö—í]sXyµ„"fŸÁÕ˜
X‘NeÖŠµT±Ø¹iíb•ÖSöy•>ÅX¾5ŒBá×* 
 _á.[` ˆ €	€@ÎÎÜÃœRçMæÔ€-R§x6]Y í\ñ22r‚Ejå×úÕ~%ãËï´ðþ€œ•uMh/Yˆ6…•h—¶>h©Õù”…%eZSvkEe¡WÚ²ˆµW?Z\ö(fT’¶3E_[¡–jÝh¿ÖŽö°5iAÚÁ{leÚ&Á‰õAkÚ
°ÙœTiR¸u§mf{ZÃ–y½eØvÙ2}¼Wã»óîà® È€ÀP¨Z`°j•Ó Ui¡+¹ZàíJ‘Ôéts­N
Ùü2ÿLƒøÐL2à TârÉ—0™
Á¥b­A*ÀhÀ !€è
î `Ôâ*ÖÂ§hù°*PscAÑDêÃ:¬»‹ ØQÂjp&ÈD‡9_-$'ª5ê¸1±JYÒëO0ÐDYÀ¢f5[£¨
 €ÀhõZådóÅï&îë
©ùe¼ŠÐÚÑ_7\¾ ÖaºtL•X85™†*Èëð–j²"àÆ@°:Ýö=  ¸jkÕ¢«dj<¼!R&Ú’ ¸ë^Ý| ©i{¤º¤S–5ªK7„ 
@U„BÖ4Cm…ïP €Ž€#À^èr‡ƒ¶–4)1o+ÛsÍdª1`BÈnK‚BÀŽÄ
¼Û ô“	oÇÛòö¼MoO™ôçÂho÷¶ÑúÊ0ÚëU²Ÿ†Øû¶9r$ÅMO èòCÃ©rz5§¡rò0]¢a]ëœ††˜`ØoïYÞÈ¿µmH´ w¾5[ëÛ¡R¾0p‡›—rl
31 è¯b…ÿJÕ•e

ÃaõZ»–¯U˜ü†@J¡Ð%´eRü8K\cH£@qW'|5£T¹¨Ø
ÆÅf !àªSÌ[1€È³Ú~@P Š B`	X~¹QÈ
r‡\VŠÝ?µ]n×ÛÝÉ–Üoa¢+ OÑ¾›r#Â*W<Ë±Ü@Ë5sÃG/·­
s€1·ÌÎ\˜àËÕÿV6|*Þ\æU]óQóô’Íp‰V •uOóÚº”UaýÐÜuÖElYÛ\öA-lÿÚºµ¥•o¥]}U•LWÚÊ4°½[“ØXv‰]y¹Ø–¨…lØ×kålÏö˜åP¿×d6|Õi¥M`µey…×‹Ö¥p7*k6xJ o ê€öÀ)iì…) ˆ€> `äØ À·•U×åTH­7ßÙs­«µ;çÙ4)ï\nÇÚñÑ¹%K±^1`¸Þ—I
øzYe5 Ý`‚a m,µ.µ±Th}ÖDtÃ•]ûR
·ã½]I“’@}loÞ6•ÝyëSÀ¶[EyÛãUOMmÜÍÑpÝÏ—E}WÛ èÕeŸÐ|A,f?[ïõ Mz«ØJ•é}MA“q
t0×>]eÆÓÝñ0Ù(³²UmOÞØ·µåeØW¯Rbmô€8@—ÓÈ€$@ è8³íd\×^–Vmw³Z=6 P`G£5T¾ôj°6 k M˜U‰™~«ßáŸu4
ß‡W °äÜž-°s<w´Õ#Å[òÖ¼EþÜ¸)ÐE%ÝBw<t¥^]w‰}iÝgº‘À'“aÒÕ £™K·]Ët¥ÈM×ît¥HvÇs
D]þöz¨m Üh7ÑÕhÝºÆÕ=pµ‡X¹)i'ŽZ÷Öí"Ü8è5AzÝBâ×
yKÇPÖx v=\¼ Ä=Œ’ÝCJ½I¤‚ÉVVK9iJœ7oÝua`VÃÚ­êþÖ! 	°°€YÀ
( rTàìM UàmÉæ€[¬6XÀë¸`/xÞÅ
˜\{·ÞÔòX ËUY½Ò~¯	
&‹ƒ× Ø^û³à½}·`MH¹\‚Í=„[²W£(ì€E¸"`x_‡7R• ^7É]xá†7ÍE$ÖÜ‰,K$Í(^Ñ€
Àê ™ž­øšÀÄ±i5ýdÖ&QÆm*et9¹IL„oU\XÖ
–€õ)Rërœ¬ˆÁ	ePÛ'âW]“"'›©œ"'N×–"§™mFsfHàd˜›ÝøvÆÄ[ƒY qd'_öåu§]bªòBÜin¢­±èåCñ´ÍoØx
ýìN7Ñß•€*¾U’.«ž*Z?+5äyá2lŒP­e´ ÀœCŸJ2ð©^}_àóÉ{i.‡Tæ½D±à'…~!)ZºU§‚àŠ=Õ¥õ|á]k.H FÙ¸JÀ6®|ïÅ?C»/ðÀD&U6tE—ÂÍu-búöÅ*Ë^<Ò/¯Q¡HÝE¡vEÞ»6ai×†¿È‰†4‘'^u !êB±Íb#
5p –ƒw˜4<©Ú-îä)†<x`©x×õ‰= ‘Á
²¨vè2uY`ýêñ˜|ÈâsX_=‹cùb˜ùŠxÁ
}Á±x.Þ‰ëâK´ÂÕ×ðÚL–)–|q£#x²ÀŽÝ Àq{IÀÌ	f"©g—"v<\¨˜.~ˆ§ÝØ 
¾Š,)›ØÆ‰åbÿêS(‹ƒØ»¬
bpL©8¥¨¾*~^*>
F¼àb 7_Ð£bÂ¸'&pÃ*Ÿð.ð¥ çÞÒŒ(ãéª®Z¸Fß†ñ*·v˜žð%H.€iŠ³¨¦D‹`KÉÑ¦œéÊ#–‡ÃbÔ×UƒãU—Ù'{0 H¾>òw
žâI°¸Y	ŒScÉC$ÎLÕã:…8³V-
ÂÐo!¬">u¢c` ¦ZöÀ¯
xdÅv<ÖO "$ÍáûC£úÜ‚ª”An$ÕÙ ¹¤òŠ›ãñø¦í
¹Ã‡X
¹,â	c§ß Û†ƒ¸Íãü¸ÎaOdòÂ?­Þø·®OcÎx,Œ?c}•?¦v¢-Ë‚E~Y}äOò£˜‘wà!Y?Öldìæ›
¢»'8&’/Q*™‚úµ",~ Û‚«ˆ­ðx²Úüô¶ØFN5¸+²¬JF’£ÌÜêCÏäÇ§<†’Ïã-Y8&‰­d=+NŽÍ«:9HŒñd)Y²í’Ï¤7ÙÞÒnŒ•Òê:M¸~dG'KöŒ
eöP¦tùdÄÀ¶J.æ+Á*ï	+ªZDRŽ’ÕdÎÎRÖ 1å:aÚ"Fª-`ëÚZE²-’
D¦“Abx5.Ùä¥ìHf»‰Ã§GÎ·^¾PÙNÖ’)eÐØT†à«^yIþ•geUMT&”IepÕXdG?µ Á² È¹q$‡#=¬­#lžzŽ›ß”·/Ð°d9¹R’A¬¼@Äâ}übY™°”}cøY®‚Áas«5&º®Ó`Æj²Ìhä’²¬ŸXyNæ1ÛÈvè«z§ãtç”Ü—×[$ 0@¢,1YäÒ¶Œ-nk`Nläç—TK‘©eº€Ìò•»eš0>ÛŒáåÎxTž—íá%é'Î‡QIµÀÎ¢€Ö²Ð®•|°L+~´VÃ¹è•b%iI¤bSþ˜ù1	Ù>–'ehy?Ö˜udDkQV–Aæ›8RšMæÎ×h¦·å@ÄÔZ‚P-óÓgÞ+²8€àÕšwaùi–—£æ)Y‘†ù©ç ;æƒff¯™*þ—‡è
û˜{Ag¹F>™aÚC•¯ž´Ù×b•Ád°çm„Š-kèØÂ’Åf»™l–líåKÉB®Ò-yiþš›f'q–p‰å"ùh>›‹ªÈ¹&6Ç‚f ™d’ÇæÌÖPÖœKbæHá”‰âvÙÞX}êæË¹h.›±Î]1 IŠ¡„¸$9I‚”dãY:®gèã*B®)‹pÆ 'PÖyÒi¬úVÜ@ÆÆ€n
îÊæt  ØÎ³—X€(	Ðv7±«ûŒÜXz&B;E*dÏ¬ŠõM.rîmµ.‚…ëšh™µ ìªvv:%-Ÿ<•S3o
#úÈRIÓ¤yVÿd.nVî ¡IÚ^ EÀ  Ð³WïjÉ€<&¦ïšÀìÍ
,|œ#‚I (;9Î¾³¼:_i]Ë¹ F$gtñmY&‰
TÏ\A?Žïf‘3&V;8I–Ï“‘CçÈ'^NœKg…n^]}X.p™GÃÖ°«qËPC{³#®dÉ˜™g,àÂ)Ìsõ‘rÉ—@~‘Œ‹/æ˜ÄµŒ³—¥Ã*çšFž¡ç Z™ôËºèôf.¸—¸ Mž
#×H–}õ]Í}§<}²?6|U˜ø«6æŸw´G˜Òjà |h©#­ìaòç7kÉ0%`J­h÷o ¨¤í`hhz ^`“ÞÉÜ£Ç.>Â”J‚z:¡eF+
.Gæºd&
fÐ…ž,Th—UEÞÅî‚	v6¡TúVêŠÿäÚ).¡måz8Þœ½iÌ%–h`éPŠ—uô|ÚŽF }0(Ä ¢Nj‹	´èÉXD[v¿hq”ùJ¦â¸¸Œ¾“h\Ú.V£yIsúIŠ~}/ ŽÞ>T4"7ÿ•ïi<::åwM
aò£ÓÜ×0 ð'Ka
ºæpÀÖ¬ ü[a³Ú)h¤ïè$­J|$é•)
€ÈÍ™
“ž`=jzp¥ Öë{^T¢–l6!©
Î
:‹C~²  •Ž	‹ úÄ’¥kÂvZt”áiÙ·@Õ¥ ^šsmŸ2`šŒö©eèa…(¦c°c©Ú©Eæžº–jç7eF‹WæAOšî‡«3™ &Â¬iÓ›¶ÈºéÏà›¶N_doiïË¢K‚-Bc£¥sé
£kÞ7lv§‡eÙyqž§—ãzš
s&µÆe Ð§~züõ§bŽéE‹?ãÙ}º¾ä£
êåŸ“ôê÷«¡¤5Ø7V¤P…å¢»8&HBà¨©£ àhä€Þ”àÍZê@µ6XëíÔµæ z H©#hKL ­ikîT¹D¥#CUzƒ®©=h|á•f«9°&Y¯^ªÍè¦:æqŠê»÷¨ÖV^hZš¿ò¡Ïh 4.WÚâC&B*³rh¦yªnp…ifZ¨v¦Qç ØˆV%af)¬v¢â&Ú¬~¢aã93AÔjæ6§VXÄé€œæ¢ëÇ¶ÐîêvU3®ªµk[«—¥ë*Z{X?×{úë¤oŽfKâ;Æº´æUê?3M*¨¯–õ8¡Fõ0k'ñRÂÈ"^È·î­©%êÐ•¢.2?Ñº‡á“ëH: {U°
$ eÉbÖ÷ÕÇFDzàØ­Eé[,²¡Êá:ñ+®{6›š]Q®åäÐ&Ïš÷j¢™†æì†ê485c¡OIL©¶ªjî:ž¾ˆŸjö,ª.­lšÁ¾ªËì Z:6%}°hz¯ùª
4%š¬nýÄêjšæ}bþZšnnC; û$«Ëé	ûœæ*4k
f³áô=6‹ÿêaŠÀö% k,…À°©c?AÃÆ

2ÖÃnŸõ`ùS²@3·ÀFÓ`i|N^RJÎŸô¬el(–Æî¿nì«X-8­‰U;´
½B
¥Qµ¥BñP{!  â#{”NÓzmbû”¦”RéÇX>b¥‘ë’`ÊF¦ÍkÀx™–´Yc³Ùmñ²Á³)©v®Çl¦:½vª“g[A¼þ™?el›„Æ²¡æîZž¦ñå„¯«¶³ák~²¬>}¹mÚõå·ëë}››´4²–As«ÇéC{ÀN´íi»ÑF°³V ÒÖ¶óä©˜ÒVÃ,íÊØ–’Ì€Mœñ´ïP»üµûÌ}7Ä^˜Fì¦ÄžYm¿ÎÕÆ9é|û‹œµÿçæÖ~¤5j a×îÒŠU(UFÕåDjRµç¾®?z`H¶q& FRñÂ&»¢^në(»Ì²¶¥j6Ûi~·oé8Ânu¨ë8”c®š°ëûLl°áì¬Z§A³«"5ûs™Ïë‰s.Œ×ë½š
ïmjZ¾¶¯éë%Z.Ã¯ýaýZ0	´×í+úà°n¹ã–Ð˜Ý  ƒ¸ÇèqÛÍ&³£ë{ÑVvn›°F:nƒáãž
Bî’u´ÞUKn~“ ÖŸIìƒÚ¡¾¬7kµ  „¹-kµ1¢ÒiZ;Ší²Ã¬Ú¬cŠŽ>&ýËˆñÓ¢´zJÓåÄ´òñJK#Ç8q øÞ¹½= Œù½}¦2æ
šÚ4:”ÃXÓÌ4¢éhRšÞ´ë[ªšÓú˜ÀNs±Oƒ¨ÉI\l^µ‹ÒF   %g  x•   d`ÂÒ€  T
ÐA’³ 
˜@âE
NÌpÑãD‰ >K$„Ä(S 1RÄÊ#!œˆ9bæ
±Uuæ˜I¡"À?w™Ä}$J€hÑ‘%CHl 
3sŒÌÙigÎ
%oÔÔéù3èÐ£E’”çs`Ó§Q§V½*æÌ™*sÄ1ÂGˆ+rÕxV¬_±Iåm`êêÕ¬[»í
8ÀØ’'ÏŽúÂ-\¹tíâÕË,`ÈòVN{Uë3bìlF{ø…‘¸sÍxvü¸öcÁ5$3fŽ9vÌi£ÆfBÊì¤CÇŒ’(>ž˜©£œ¹sè‹¿Ö=E¢ó9¨ç„0C§N*QQ[%çÍ3yÔ¸QFÏ1n€
ÑÃzõÌ`Ï=øäsï'ÜËÊ<1Þ ‹6ÇŒâî¶Ð$
`;ÎÂˆ7î€C¨È3/„12<#*A,‰J<CÄóì€/5„ #85èØj¾7¨P
üÚx
À(Bˆ*„ ÃFu¬ƒÇý
‰Ä Š:3ªPÃ¨¨Ðƒ&aAFá€£
bT±É‡3TË#Ê°$ŒBÁ(‰& Bˆ&à<*´$R 
2Ú "
+ëP`Ñòb|#8ÆXƒºê»/¿ýfcì3)£r A
=4Q!vƒÑÉŒtÒó,ÅO¿à4Ý.BÁn”Ð6Òû­¼"lrRóÑU)uÓXýDŠ,n-TWãèˆâ:«Ì6Ub%5Ö¾W3Uö#9ow\rË5×1„@¨¢0(J¢ˆ¢¨!‰&è‚w z`Ü$J7€Ëíw >7€xÕ
k^†%jXá‡ˆxb‡+†Øb‰1¦Xá£Àø"=xäæ%ùd”OA"
dQ` $Z¹ä#l ðÄÃJ+®|ÚtBîã@"-¹œÃK:RèÉ‡øøÖ£Ð`´ŠÞÚ0ƒ
;âˆã7.«€Ã®ˆ3î:è ƒ8È`ƒ:ŸgU‘,¦®úê¬·Þ2Ë¯¹¢z«eh»±·KbÔó„­cnÕŽ…•¿»r®l
3ÈÈ:ªáŠ;.é¿9S°¥@Š—B¨ô*B #„#Ÿ "„:žnAÐÑtÞ°£6B£
8æ Ãt÷ž´]VÀ;%kØ÷¶Zˆ5ò6£‰áKAìËÏN{í:Z ã2ÜŠë©Óþ5âÿ
8Ü¨"
SË¿Œ¨´ŠÊ	:ÐàÐCßß¸9èGrÉqAº

fà…Ì,!•Èp:aÅE@j0”åÈ¦¸Lõç?dðŒ`  'DÁ3\èEÖð'Á¤ v³{`ín—»Ýõîwdž†×A²|p !ôLJxÂe•äÉ›óœ = ñDyfö¶×½ Omâ3ÝöÊ×‚*¾ 
VÊ²R‡ø™´éðõU…‚Ó‚ÃÜ?«`ñZä¢¼H¶0ŽQm8c'Ø†#ìDrÜÞåbÇ/æ‘Œf|Ä`Ã¸®fdÁBìæhÈ.&rðÑãÏÈ¡¨ Á8„¤ã!ïÆMŠq‘||Á¶Rç€v¤CiE	êJx‚$ šÈoW3ƒÕÈP‡Ù0iÔ#›ÙÐ6Å:Ø2pò°€DüÐÄ d¨¥/m£À’°k EëÒ—”¦¦ñáe?»eID *(!Bx‹È€(¨ø`€|‚Â¨àÍîŠ›b:Ü0 ¿(™u"ö0«Í8î* œÜNŠ9€
&"žÝDG:ÓYð<ehÐéQ²Øª:Ó*úPþø'¤ck;§y„ØÅ“usèÁžðÂ€6`Øé
ê ‡8˜Á Jp©H_` š!`´Ê<óäO”–d£X”tòø5Ç†fHN=ï)¨h§x›#‹½xfU¹Õ.{iSã•$a°+@éD–
X›Ûì&^ãD¡”%V±‹eì@
€1q`#q@¾$+/‰DÖ­¸Ôü…ÙuI$°Ñ€e'ë‘yE_±¬Á 30€}ö\°-Êöj2Ðfìb»Õmo7æ[—·¿%®pË1¢x¹éƒÈÒÙ£à¶\¡75/åödÎå,`¤›Øï6V¼ã=Ù:X¦ÙÀ¼4û%bÉ{Ú¦W`éu­¹nëÝ%7¿a Wß{›ðJÄçMï€ÙûÍ9ýw ñ%ØÁÛ“Ý×/ëØ~Ðß—)¿~‘<8\RÒp \ßÛœØ¶¾…õ»Ü
s·Ä¹=J-<á Ø˜Ä@S°ŠgKßÃ¸Å,–Hv¿‹á/0ç¸ñ¿˜¼ã÷È?þ0ŽG&a%
¹ÈFr’iL”Y€1€|16b¶–¤³}ÊØ3¿‚S
@kÔr%«´ùÍjÕ
ÉÒY;_ÅÍŠ‘¦—=âcs!úÊD®q+ã.zÈ#
æ@Þ9Á¬6 )YIK^bÏ0 *¬Í¥=rÚ}aš,‘mÏ{" Æ†Rç¤›bÀ 0l#8!¬qy¥Z»@@ ,ÐƒÄ)ö¤qC–ædkiKSª‚VÃç˜W
_ÙR€>74”¨Â:ëX±‡ÞnDÇáŸÓÇ%²=r'b*7|äˆ(7Äa‰ãÑYó]èÃÊy4wCé¨±n¦à×ƒ\? ë{nb"SÄ5h:|K|˜
7¬ŸKâxÊ3ÖuÈö«y²pµ;ÝXäo2µÐä, –Ï‰‚
¬ÚÏv<ˆòX‚ F>ª7@%Að‹Íè ±ºçÛék(GN…’Îd±²ÊlpË£Â\^óÀÆ“ˆ/±(g

ÊºÞ‡SÃNX¤!°`#ÀúÎ+ÍÕ‘¿á
Ùµ÷îãÈÛu —`â*®E@s(BšÀxÌ4Õ…ŸXÕyáÉâ.kZãØ3.ö%**÷ô|~4nµ´‡w®n"ÙU@x¨	ÆB‹yMæÀ¥¥‡ûz°]IªÙÀÔáks+]¹Ú×ÊÇ~ÕS»ÛÑÌ¹´¦Éu´\U@qàœüÞSSP×Ì&7y?I½î†—qn§<–-í9P[Öž·
Q9#ëóYÎ°U@pT†!t™*–‚}ð“‡ÈZ«{rr>ŒÀ_Œß£¾à¾âÓ¥ñSÀg+	 7¨Ÿƒ{¤¹‹‘ƒž¾cºëiÞ°ƒ<,n¬ø¾„Ä“'Å³ŠÆS…Ç«ƒÈ»9Ês›¼’ Ì£>Í«›ÎK°››½4½#ÂÏ››Ô;a¢±Aµy=¹š=¨½¨À=×“«T€<@¤‘¿ò3>/œ@L3y ?Ê@4è©¹ÃÁœ;Ã¬S‚Ø±!üv
‘»0B$,½|[¢&\=øh=)d¬@yð€œ’';ü€C¹º»®Âaâ;ô‚@|BØ6”«Í"@3\«¬#Cä“ÃÛ+¸zò“4VlÅ :ˆ2»„(‰°E³ˆ„~H¶É®™ãË² Ð!›²DCÆEã-g+®F{ÆãŠFg”FãšFk¬FF
 ##ŠHkÅ sEpGo€- „HN ƒt‘“sô‹| vtÇx¸‹ Š €¸ˆ€ Á¨,‰Ø~,È‚tŒmüˆhó¯Ø:¿@1q¡®²®¯À..ã.‡
ˆ|/aˆûã9!°ƒ…Âš£)¦WƒZ£‡Ç
 A@ I dð5`“aÃ­b³ W[»ˆf;3Áð‰˜62¨¶k
É‘ì¶’<I«Hcz¥#»
4·`Ã
$†W€CD³¬¹P·ó((«I…©‘ƒ©ºhœ ™¨È©¨Ê¡©4EË+	ØúœÐ+(B¡‘G©)ºT¾’š)îñž\é6…+DH¤ÀÐÈØÀJrC¸Pø!›`$:°’2H&Ê&mêJÁèÂBy½S6DM²h@Úë§*PïpLÁ ÅPì³S¿»
Ì¿G’E‰˜ÅÏÂÅ @ÎïÒÅ àE_Ü²*FÇ˜cT4r±ÎsÁ²ïºFhü­G6þ’1VüFáˆTh†
Ð{$69ðmlÎ` ƒC@x€¨ƒú¼ODÇ H_Ð€0XO¯<Šp€nø‚õü‹Hü
ƒ4H„´°…Ì° FŒÙÈÚÈ à¡ë‚NðŒ1P‘U
¯Ž$‹¥ä6h*I]”„Ê•tP;‚—Œu˜š´É`Ë¯d	±ÐJ…	
UD?õc¿ºhQ’´ƒí’§TÉcâ‘Ü3¸Êä@3À"Êì7ËÜRÖ™~;ˆëÊ¿*	R
 7²¬‹,Í·‹kSŽÎ <¿ È»ÖAÝK¹×\ÅÌH;“<=œÑ›ÒK5mÓLd½(ÜSC”«¡jÄGœ¼8Å3
®ZÔ¹±Ä7(LT½'œN¬¹Ú«RÔÔò,™X ãl0äTN¢`NçÄ1`Ä˜=°êTÆëôÕìt4lìNaýˆ„ôˆnÏV5OwHÏ%
âk¥P!F(iàè‚æ C(ÇûÌ‚@ðVÿÌ@×Û˜
âË†A0×Mûv
?×%
‚ŒP~œPHÏe
€-‰Iå«,tQì1ÍP#€Y„‡v „ HÅI 5¶xÉz-RÔ
@øTÒ£\¿k³=.9ÓLgbƒÓ
ü7¹âT7p%:Ù8ˆS1•+E¼ô(5ðG\¢2XÜtA®z”,M¸Æ¹¹ƒÎÜ‹žÕ*Ÿ¹nÓ¿£o³9L•Ùœê6=9Ø,	!¨ÃPT²áŸÍàCDMÂ•5@&$UG}ƒ¬MÄR¿,GlL:mÁ’`ØQ€ä¼»QT–%B;\ÛO,ÃßdUÁœ+	\Þ¬KyPÕÝä±~=â”E¨Å[´0[íE\M/éÜ°b”ì—Ï-íÌ-î¤Fbõ‹cÝ®GòGþ$GsôÏ Èv
…'‚vü—rÝŠØÝû´]Ü]PÚåÝæÄv
‡;¨ÝÛÍÝ*+—y-‰Š½W|Õ×MÖ~ýW“ ÉI‚¥Ž’Ìƒ‹
µf˜E
p>¹†g@‚
øQäÚÉ" Xf»Xº•‡@ÑX¤œ7'eJ¼(13•‡»³ÃøØŠûKÄY2@Aµ¹EDÝ¶á£}
*1 ª‹²hz€‚ ²ŠpâIÜÂÝAa¡/å`É¹ž‘r¦n³ÄQuBW£:Hø£¨€:£#Ðt©½ªLáìãÔEY"Õ0¢¯KÎƒ½i5PujàÐÐMÄ•3ÆÍâ(›¦»C·ªºÙƒ
~Ûî

Fa¶‰ IZy€AÉß5I´œÒ¨´Ò
Yê UøâÞ€áD(Ä¶ô‰4I¨…º1Ž&ÊÆÌ×«bÂÍ¾-öÓÇ=˜È…ÕÉMÎÊÍE‰¸Õ_Ô\N‰èÕ*;Ñ°]rÝ+]WÎÆÔ•ë5´ÖuÅg|”}Ä´~lU`×?Hÿüˆç•‡è…P|íeÔµ°_à®é£E3›.Àª. X€ ‚ðŒPÞ×Õ
€dûˆ`T¬XÏnþfbL2ì
Ø¤'¸:+¨5h¶<€ƒP R „%X_ˆm_cC‡Yl@‹u¶ÐH‚¢L¿eRÑi>v¶'|â	–2i*šRK3•³È’+‚'(‚üË–ˆ‚Ú„.ž¦üÂæ
C›“>A	½¼M+ŒJvÌÿ}à;•§¯
+Ë¸’: Ã/é±z”©uã€õBòé¹œä ¤é+Â»ñ°I›†¥±z¸Z³jèµ>`éŽJUß´ä¬Ó+*œ®ê«6ƒ…vg²j%ÍTªÌ¹UêùÍfPQI7%`’2rñ Ù!)’#I’%¹‘¾ÞuãÁáªš•à1&†@ ª©j²Jëµnè4î`nã—f–Ø™Á´–ê«8kÆì¬F«-Åk*–d#@,–+ÂäªÊÎi²jÛ.‚’R‚5M+Ö–«å*ÉÜÓ&+¨³ÔàmÕ^‚¤Bƒøm7Žé¾dZ~ÕX­YµÜOÆÜmÆ±Í°Rö\`ÝñÝb5]ô~åÓMoX¾ÈnGê¯×½
ue×(pWâ…Wvü¾ÏÀƒàM	•ˆˆWâ…>`×ÿðˆ„'ÞhàyHz}P¡Hfe‹q€
•°¬Èª.Ï°HnfÈ øpp		IsB¸PÏ|ƒAp… pñÈNˆñõ„.‰ ñ!rqk8lèŸñA€…÷µH…F8r¿ ñXXò!O‡f8òb>æ
Ofê=2~ÇyÙ¬Z>]ŽÍÝ°‚' ‚F\¸ q!s¿xVÈ‚õŒÀ‚+XP˜óõ‚H†1XÏX0bôt	I ôõDPôt‰ƒ?7ôˆ†?pô†J°ô ˆDÐôpPO'GÏr
 ïrnüòVµPÁˆ™X(2FìE–JU¤5:h–  Ax*ß*o@a³ ôª2ah÷’²Sv°ò^ålt1cuo‡ï°°aO/
	fÇ}/UvŒm,‹®ö>q"UV¿0Æj¯2c4öl'¯n÷‹v¯o']ÀØðYö2òÜ«sÇ±½R÷.Ööeçö÷öf÷êUõ$#O;ˆ|ÿ—„—_ó_
{÷£xÀˆ÷VžwhwI#Ïé[xÑÊ­÷¬ˆxw'yxx-z7øWŒôx… ùcÿ/Šÿˆš
‹wxò\ù÷&÷°Ð4˜ MÃö~gw“Ÿø£¯x”Ñ‹àùh÷ù£è…v“ú€+úñºyøJz¿ÈùfÄxqGòt—©O/²¿zˆGvCe¤Oö[úÏx± ŒÒ*û*«{´‡2šçúCãû£ðúónz¹g]À05
 ŠSã÷iŠíõ¥œã(=;®Ñ7BÄ"£eöÈ·C†ÚÏÍ9Í&ÒÕWpä€ä€õ¹hÅV[±Þ{·OÆÜ_F¦V
|W¬åx2c£z'K{ä÷÷ÝÿÕåÖ”ÆïôòžßxÀ‡&“ë×{í7úæ'ïî7ïçwvpz§eÀ ‡ëô±™WþµWöïgöð'xézê÷‹zHÿ Èö_÷¬÷{ñÿÆàcF/¦àM?óç Ez
pû=Ü÷þ¨Œ$úÀúWþì`8 ÐøI„ø -K¬-°V@¸ ö ýK€aáè¿(WÜÈ€¯ ~
(þ_Ø#|~è¿(EžÚ;ó¥Þ†HÿRÜ€~!è¿'(ƒŒfÞò>ò¤ATœ{~Á8™šLƒTKp	0!Ô$ á·øˆ‚œ£sîÎå¹±çÚà@ðsšnÐºõ„è4£ÓtNÓQ:M‡é4§ót NÒ€p ê©HÈlz©@.Ø¯Â\6p. 0 3—æÖ\¨qs·ÁÆ·~Åí•#ŒP¨î#Ô»ò$'0á«Ë|L$Ôp€o¦ë A¯»)(»¨/džÿ/Z/ ^Áƒ‘Ÿùë‚aÁÜåÂÎÕÿ°^/¼Çèž¼ù7Ÿ´û{úoß™öÇý€!¿h†Aö>•7
¡Q¨ 0½”C)Èâhà6´‚E0¦ÀeÆƒŸË3‡UÆ|£7I`L1Ý^ÀÃß'©!Q¸IÅ/½D˜‹/\0þp |Ã¸'ÇáG0>AoWÍ‹|¨©`ób~ìüÃ
øì$âýÓH’MÃiÈà§9ƒi0<«0 ÃBð  `@¥‹|°bâ-ói ØÄõ”ÀNÜH	à Åt‘ 
 QäeüÈÕ+A7
Ú@…„ËAÂ%È‹¡˜«
–pò4§æ”€Hã„o0p	ð2
<hfDáî¤B|à ÄG¨)¼8NÊs	 Úª¿à@¾rHÌ èl  lµ àAU”^¥+žB– 
%ã{aŒ¤0#™BdÃ‘*4s¬PÍ€W
Á®r¾Îî3ƒHìvá2l,ÑzDù µ`8‰-ð(Ãô’î°!/lÍÐ5zC€ý²¢<xJÏîé»Õ˜ü´¡;ä†ÏÐ1@ÄÇ˜™`qô
è05âë˜óÞrä‡µá7þÃh‰a
÷ð8†|˜
ÿŸs$
ßñ!G8‹áQ(ˆA!Ž@…˜2\£{DðQ$®@âØòüBED†Q#*Çõ{ìzþqü‰C’xþÅI,	)qz3HkÐàmPƒaÆ‹°ÛâGx‹(§ØÅ¼ *	#> 2 tö=B¼À6Ä€ä R†€) 	  * *ã–{„ñ@>®Iøü*a™ó=™P,’EZÓ	»âd…z•ºSU—V¤’]²±ISW.£–”ešqÆ&é5Qhºn&•ÆZ(n¡þ+v»‘52–ýèûcxŒˆG’<ÞFý§”zd†²ïÊjÈ'É$9ã’çYÃä˜QFžD”D:ÉH8«c=ÄŽé‘7âIß¨'/e@ô“š²<rJô˜3âB••0*JL)ÿ$AD•÷ÂSÞÉÅB)¤Àƒ•¤2SDŠ¨ÿd{ù”¹²UîJ$è w`©–›ÌÒ0Iy`!;M5Ò%VG@c™	H ±h(y¢<2Ñ@á'‰ð“j˜Ì’±2NGSÉƒWìŒ`QŽE6'%ç%`˜o`×S !öJ8P»à3€Ì%Qˆ? l‚õ4àœà
X2±ü‰Ž7 ãL à†À>vyÝ¥tl”^Òd¢ŒÉÆäÈÜ’R=+Å/B(=@c€PPb¦k‘a`ØÌ>·v¦_: h¦‹H 	†æ%,HÆ(‚o°ò !Ø™)Ó1²L‰ÐáÂã¹àPª"mAY)%
«›f¯®……B€ë€À à´» ;:)·c‚dŽí°;@e	6ßeÉÔ0ä‰Ú!Ãk‡çf¡D–©LTæMÚÈ,'¢GÀèNnjGºy7m «œ‘ˆ3>ÕÆ&ø(Ï#xtx«òá¹¿ºÙ)'pì•ÂqoºÌ¾	^ÐkxŒORž]:ccT‚§s>’Cý÷Ìì$‚$œ²sAÎçh9Ç£©<
/´Ýù:IFì”œÉ²tÆGÛ9+?ÐC†COpBÎÞÙ<
çèäœ´SbNê¬^Ð³zØsnD+Ó}'¯›ßSqFÈv¡ÿÎžù‚û#6GàiÛ§x„¶‘(à½ ‡÷è§>Ú³íåÏù<ÿã¯\œÁðQŠð’@‡ì-ž34Ìµ½"ö€QmiFQ)Z7=ZaD 
8'Ð™BŸ!#ˆŒ'”>ÑDšZ€êÓ¬ÏõÁ¾‹RÑp¥ýLŸøs}:Ïýé 'ÉD
Føyâôˆß =ô~ÚÍ'ú½'ÿŸŽò(d¿ëø/°(%–è“íµÇÃ©@d-1äiýeQ‰pF]çàì”“?ŽJÓIDoçGàht ÔÑ5ÊEõã±ü¡Ð0ˆ.JÅ7(`p€v”ØÜJÞÙFè¡\¤‰òbMFYDÿ
y
†´’êQÏ9)ù¨ÕŸS´O2Pøùb !¥˜´Ä0Ï(:;9×'
›Íò"AC
KMé:ì£ ´–vÒU
HQ¡%
QÐúÒYêEá_#µ”Â³fÎ£ð¡¥´\‰ò+0€
YjúUýbEñ ”Ëõ	€3 ŠVÒ˜aI‘é+[iyJ’ qIªš4i/Ÿd¾4‹“q~špö‚ VSbŠÓèIÝÐ¤öQ“¬‡M€µÙ	ÜfC’“¨1èÕÉAY,
*í¡öÅ˜’ÑÔYîåã¼žÙ3•þÎ[š@)>•£Ò³rnÎ÷¨)¨t£at£ÆÑ–9G=‚uzÙ±¢žOúEmžIõ¤ºÔLNRzR
"Už#ƒ¡ÊTÍyR‡hJõ¨¡>VOžÊFI*1mˆÜ3¤
Õ:NC©G éeX~N¥j,KªSÝ“PuŒ¾Oÿ	ÎLâ2ƒÓ’%ZKYc"ø˜0LØâ¼‚ d š‚l°	<æ}2FáàºuÕ0  4ÀÍŒ`
@LcO’M…—ÒN^z™N@XÜ„Q²†…×Õ
B·D¨(c;IŽA$À ë)% îS2à@–¤
á1,‚õô
Š5_1VAz2u«óò¦Z.LªLq1</Ù>m@h
>€FZ
 ÂÚôõ0T]¨S;+F$”Š4£2ÒëêHk*$
¤’ô½§Æ‰ce­®K5»þÂóú*½ê²¥aõ£Òk˜Pw(«ÜªLõ½nWŽJT‰'9¬•Á¥ÂÔž:LÓëk°¤s½êÍŽº_?‚y4¤6˜ÆTËI5*~E©e²±FÔŸ×_…^RÕ£š´¾Øî™K¹ë.-£€¡ªV™«jAOé&½¨ì“ÂU
›[¿+`˜d•f]HjÙÓj¹˜q0Ð±šÝ`Ì­:xSÀœ#Ûšá*l$E’õ‚€ÈÊOÕie}¦ú²’
#Û//B.¬ œ.0Nêb ˜
v  ì…6 ˆ@ H³{a6¨¢ÖLN³eöÌ²Ù pRþ	ã †Q„ÜEâsRfCÃù'kv äŠ`DÁ-ˆ» 
$YÌ¨_wë¤=Vseæ×ë]Yq•ÆÕP‰äúÍÖæ'p®§1ºÒéÚD³êBe±
UŠŠXLËd‹jxÓ©ã•º*Túj_ßèC«È´˜êÔøJ^qíý°6¨¾Ø¨šO]©hÑ°ÿµ~Xhl·W…£0VÖ*XÀ`ƒžƒ}©Ï–;¶Ø’Gmym{õµÓSÃÞG »jsí€Ýµb”½JU÷zN,ŽI±åU«êZš
k—lwU©ÐìY¢ÓhYV›iµ|¦ÑÔ1PÐþ¤aÀ7Š•`´Ut‰-›jmÈO€­Þ§ iq«¦u”Õ/DÙ4Yï%”¬²—U±p¤fºˆ
`ÈÄòAìmN®`|ƒQ4îˆ½©”ÖçbÖÞ
Nëm®?÷½ÄÓyºžB !7q Pf 0
E3 5ÑYÇÅÎµŽ»LQ®9â&R‰C°úìÎ²	°:m
Ã`Ø#ÔP> h\+p®
nJWŠšn#'¸ÝžöÕÀ"Û¯JnÃçQø›Ön¤Ðoëj½c¾oîSðòÒ^ÿ…­Uµv¶Z ª]õ­µå·³ö©½`{kç+±Å·âÖÝ–ÝLÛo%ÂêD†­3yª[ÒËn/1b¥Ê¶~<ª;åëáeµÅ–ïÏÓ›8o‰õ
Æ3½ ÏÝ
mu_éõ»!¶ñRÑ^;x	¢þ£¹ˆ×ò.OÌd¬ð…·åÖ#ŒOdX>»­õµ¨Š£JÛ_»}/gô…¼áì½wªwÇoïe¾ ö©Þwk{§êE  ÈP€Îß.
a£íïÅ¥Ï·ö&Øeûo‹™«"u …‰ ™ä ¥c4åƒÞ1¥ãi›fÒL`àó­ÐÑ÷BASM}«¯õ=2º³¶¤Àãã^OÊ)¥$|ù40Á-4‘=…nh
¾}ò×ÐüTí‹_…h²EÀ·÷6$Qd¸D0VÝ»æwr6ßû»y‰°¤5Â ‹½-º„3éåõ½Nˆâ_Ô{mp 8£AO¾ÞëëS³ïÛ
¾ëwøbØ£PGƒEÃKØÃÖ_ô«^Á0÷Õ¿ñVNjØBú`¨î»ö·«îa7Ü}¥ïG¨¤Aï’jáÛ…3oúEÄEwÿJ„QôJ)$¦¥y˜Àzâ¿+…¯"n¿¯òÞ <ˆ'ñÄ5ÄÕv
§^Ï‹¡ *fÂåwb<aEqþ-Â—8 MP»ipìY-¸×²UWÀdb°¸÷¿¤°..ÈºH^Í®Kb}žÇ

 ×IRY¡ÐæLnbI 7 Ëú§e³l¨D/Q¤`
„b˜Æ!4ãû”ð±Šôâfƒ}ì@Ø MàY›yÐ ¸î¾ÕÆgZ×úOiœƒ“ÁÔú«¸é€aï–½¦WýZb?<$/­íœö–þvd(|ˆwqî¼ØÖÀ‚Þ¼ëm›ð-n·ù˜.â•Úl9¬æÂx8Û•Ì‡{qHÞ©:•Ûú`™l‹#ìOÖ¼xãªÞpTâNFÊ÷%·âqKŠ‰oX˜·ÿ¢Þ[Žì‰kr%¾É¥8½PÈ€{cÍª3
Ç·ð‘
Kð	—“9>²ÃL—%³^bÎQX³pì\ÜEºh è2’­Æ£ðßS†ÜsÃ'7>
Þx ˆÜuZ=¡¹0H1`€›É1÷ª² (ô£—!5fÛËøÁÀðƒs!5^Æb/ãÏÒ
°9oC6ºµÙf«8tkHnUœ¶ŸW‰<‚“‹“¿¦—T›Š7rO¾Ê ØùÒÞ§‹G²HŽÉä×ºŠåÙ
î¯V†Ãé&kä4¼”	±-uÎQØ)óÜ
;H7eî¥ÊIù:3eôZžUòyÎÆŠy¼'m{<Û³u6¯ØÙ#“eˆªž3¬NE·îÙ@[eÿœ’]ñ(îÃÞWÞ
Ëê\y•ò Ï:£gK^ÈS¸ÌCÜ¶lŒ‚Ì<¾áìÝÖ(P3sÇ„¸y œ–|$w@ë¨W¤Ù4£fsQY’
k–Í¯X“S'«$¡ìCþÆøR2OÉ‘Ñ m ¸‹¡á”æ‘f™¦’%´dVF°$£[‡: %@°à 
^Õ«w/4˜Í ‡²mfÓÄ77ÆK›˜Ó³+òÍj¶‚Úgª\ån’+µÐ#ã]ðÌ“±¯$ÂA97hœ|¦s2,É`™9'h¬Ü†ys¢æœß¹QÞ°ŸY±|^Ð¼˜
ûâ ÀR‘¡³åÏ¥ú$CjN•µYVxö(h	ýž+ôLýÏ†zR³ê
K ÷s¬îÏšÏj LŸåt†¾Ï&B êª|ª}õX¶Õey+KÈ|	Q¢ÀýÐ:Všú…Ù6¢Jói>tH±æ0h %>!Í„`âÆu €¢ ø ;ð2Èôfö ½ÌÉ]TØ@7uPÖø·bãaÍq·2c&
ŽyÊiË:™ã\À¸¯u ¤ÙÙpwÎ€8¨> ” ‰°Â$¸Où@lì~©eC,`»`(‰`fU=ð `f„t 
özŠÙ3»ž¾iRˆ˜g³}nÓ”ÖÒWg½‰ôo6º!ÔœA0å*2A5ÎùRÓâL
¬§­‚VÕkšRSçZ¡•óLÏ·8
ë=§g¬FµÔyTP«aBÍ†mr€Æ©ëÙ’j^mª×-ªöÕ_ûB×g±­¡sjƒÝÕ[{B/ßH½¶Ùï³¦•ºZUFbŸ,µ)q³fÛc›+ë§­|`âöÚÏY;Cß7, ¡µƒjyàZëƒË/í2È¶Ë!nØ©Û,É^Oób)À1–í²·,+°Ù‡–v;l<±%Â. 1À ¬~Ô
é›t§iO
‹—U9sCû`“Üple?.¹`ÝïH"ì…Ö»_ö‹\µ›{{oˆ îN  ŸÀ Ðª;¼9¶2Þëû#¤ƒQ ÆkXPÝÕy³dÚÜ³îÏ&ºAÛ!\ˆ,È>ÜÑÞÓfi?×¦
¨#÷rÔ”»PƒmÝ’Çð¢¦¼~;YÏíeå†ØåÞAJd
«1¸âÞÔuÛrop¼M¬Å
ÇÓÉ‡»gðZ
ÁÕô§&ÊùùøöíZ,«i²¿x*çjCZ åvïÕ$üWWnó|·öïÊ£,„ñ¨½Ãc¸Û*ÜƒÓØhÝ¹…ñZÑâ šŠh¢ðºˆ·GˆYú6ÄGP½	‚D€cfV{_„T°
ÄÀÍŒãs|=éne# †8H>@¹
oí¾@û&ÌýÚ0ÿkÁó÷b~²2ƒc/.½»±è¾´À nŒ!X¹qÌ€›^ (_OñÀòC÷Éof,H¨œf¡ W Â€
ˆå1 x‚ÌLr,Àåë)ðrw¿…2
×ß=›ïfÿÝªÎñ.r¸ÎØ5Ü‡{Ÿ˜ÓQ€«D
óCí´fC•»kR$¡`âJe}éæ’F‚rŽ*Ap‰u´Ö…š"0YšÁ ÀÎU‚Ê‰ã¬ŸxÜVÖFœYËðäý£Aõf± ú¬·l&2–Àº=Ý«„¬À×fàa²OÖù5÷pˆºöFWÛgW‘óì+þDþé.\˜Rh$>Ÿ©øGÏÛÅú!eñií¹«5Z½ÖaDW™Í²YnkÙ€žJíßˆŒÖeO€x9P6€0xÌ@`[ãèr!
¸€° <gfp½Ä%a6§C€ièÉÇñtª d‚h 	fˆ	Pú`Ì àÌAVŸÒuý"°
áxA10€dPÐÒ€øc:¯ÒkoF6¿ÕâƒV‰ ðÔÏ cYS™Ùí-3<Âzb^ À'y}Ø!®/
3¨1T~]áüu»Lá<Éœs²°àÇšºg£-…š-0Yð !`ê üžTd˜œ‰8Ô0œjKêdî¸A‰«›mÅ5³òl[z½‚¶ï„þ%¹9:àNÕ½ò ó«íJº!ï‚8ñJñã.¸3wÛ.‰œ;¦oñÏMÓC·_pÑS ÄcàÓTPçÌCý3?ƒ^îZ$;ÉÈêQ ø£ÀØ“;#ÒŽœ²"ì’«°[5•4Ö›"ÍmšÚ¯äj§ZºÉÌ7
¤»Gè ]  \ò„!Úï“Pì™Å´£ö:7
Öb}é¡fÜe~~âŠÞÌ
¾q³"Ø®fÒyð#%ÔðƒÉr
1°ç1Ÿãá®b_¸?çá:/:»d®nA}îuXqE6S*ˆ|vGC= $å4!9ÀHlx%k'X‰9°ÎÅÈìKl´®dÍ’SÈ€•4@¸£ÃÚÙ˜ÍÁ;®>
ã=è•wN|Ò¹»	Oâ—û»+ï1Ì¡¥u…¤Ö9V¾¿eÆ "€9øé%!Ä
ä+œp€"°ÄÇÇîmÜÈ÷#‡ðÑ{¬{„€E
c`·F]»ðAN0XË,£pRÁ(6'å‚rRÁ¸5•e û àøÒ!¸5[‡Pû ð‘´!(6Á$pû p À
·FÜ§€@Pl>Ò6 Fá$ÌfÏ1ÂUGät¥[ñ$“_0g!,AÐÖS  ¦“¡ ,Â§þ»øî¢¡þ?áše³5Ì•·Ä7çÐr¯¸‡Akb[Q ¹@  d°
®¯ûí§ö jíáÞÏÑ{àÐ¨^s‚Î5Eè^¾pAà ™ÄÒ°SÕ…³d·@1å?°™À¨Š[’IÅ¢´02ŸuèŒdzlz‰/åÂ:äÊˆÀ0À«Œ ˆö(VI¯ !raê«¥‹S(¨VÐ7û:…¥ô”ðSp‡\Qq@2-Š¯‚I¸D{éÃêÜþ‘FÀ
€`Ä<¼€1`CèŽ\tð_ÁO,JÆQnieÐ mÂ@¿e^[$ÌòŸýR’4–Ð
`ÄÍ€*à»eÀàmä8‡: OB? BTÍ=$ájô	(ÍAµé“ÉïÆÒÔßGýÖÎ«µ‹^û²þÎixU½¶ùµY$Óõ—3ÿrº‡‡Êàü¯„DÇè—o”Ãù%¿Ùé
rgô‹þÐ¿¦lÈ
@r!!Àäÿ ÿöÿÍ€Îäì€ýd?ÚÏöÃýt¿€ àý8ßï
 þlø`ø+þB“|BHþ¬“Ì‚+\`
á 80ŒðF€"ÀV7Ô:@a¸t;à˜ðÈ¿Ú °	`"€
¨Æ!@	hš # Ð§ +@Í‘+¬ž0+ècpI¥EÈ´(€
*œ Ø€	tc¤»ðsa‚¦b
Ü˜^b|1„· *8Ô pŒþhpw€9Z@1Â	  !°Û ¦€,`Iè|¬€O®	Ï C(F@»ül"ƒ!K@j˜6ÁMðÇµ“æù@'¢7F™ž¿5/n„»àÐ
æìéBL£¥‘+dà±
"º¢q)?¤;à±c(˜å
<ÓØ!
Œ§@Ø€Ð'ä
:cQÐ·@# 
ôC¾[°¨Q ’ÀP%>8å
& vxlÖ€	†Œ!*h@60zp
8éÀ‘ãÌ9ŒF®`î–°  O„9€
X¼€ÍÀxÌ P
h¡°»ÀG°’† XÖ  ük´Á
‚¦ÐÀ8p¬¡	qPÄhB€ r€  ^  °9X€@”³`€lb˜bV ÓdñDb î¯üxU„A1ÁE` C Þo<Ýƒ6° <R ô@!°
 @7&Ì<ä£ƒä
N€1 @
3„`&ìB0'LvÂ p,«€µT ÈÀ
a{æ _p`Wpš k@0f<˜­AlC¤hçê<a³Ia>Ï†®ADÌ¼•¢*¹L,:wŽG€a•AD®ðË|Ì€'\€@)©V—0D ó&:ú &Ú 1 &TLÃòo3àaÐƒ¦%O‘jZ ®0$ù
#™±Pp‘+„
×Æ¢BC¯0*
A»04˜>®	€°àó´»b.7Tô&7šÐó}Ôöf½‡Ãß°8ü
¤c,&>Ý°9=dÑ»àb¾næëîÔ;îìæ«Ü;JO¦³ôŠ±«
s‘v•t à p7h
!ø þ/ÞÊËÍ ¶*Îµcï¶ÀÁ¹Ø)	ï( ¨ò"Ø:
2 ±;
 w Ëj•ÁŒs£Dn†ƒôœC’aÔ+õê"GàÖð–Æ€BÀN€cÄB`hP×xxÏ0zB]£õnâèõ€b`>ÃÈá#
¶¹Ææ"˜>¼9±«CçÄq.Sªq/#Ždò6î-5Ü€É"àî€@°–ÏÉsÚ ;.ö¢Û‚Ä®‡kí~8‚Žhñ‚žïHìL0KÐÃ„N°3pcïOgó°§púÓá¶;,ˆq³ùØ»B¤£Å¶°®9côT:GïÊ‹ô`ºî¾›éÀÃšn\È‡Rg `^ì;	ªØ 0-›Çê± àÃõ1L±Ã2L‘Ø ì ˆÇ2€  [O<E¤ØW5ÇJÕûê³ /¬ÇòÃÚÀ¸™Œ‘!È;Èd;¤G_X3¬¹ÄŠÞcí<<*‰ù«È…Ó¢[æV?I™¹hÎ?!ê6&àU´Ú„“!qúò/Ô š|à¸Š_ àT€–ïOCµ`>>msàþ<€.9léV8À€ CÒ
%L¬ <‰ þÛ<ì†%MØR $ÀÿƒBšˆú¯ "Ââ<{ô 1a'(&€Aáô)`Èƒ¯E	`¨Ó'°Z Ð/+,J"˜< ,cx–[pöÀ=Âà IBŠpÈ`€Ógmy8…9à§Pm˜0Bb|",@
`ÆÒ¤8Aô€µEº‰
[‚‘ñ j¤‡†Q¸ÿ˜ˆÛp2òóP ïÎ8‰DŽ(S, ÃôB:¬ëE:Q}ºk¾õN¹;¹Ã´ÌPüC´ðpDÛHžEfS|Æ
’hSœæˆä¶ Ü´1g³÷ÈF	HeÅIOÊbõlEI®1£äÓŠP˜Öhâ?‚jî#`ôÀÌ+6Š
SÌ¸”Å/>Ô­z,q¡¾“U:™…ÈzE\€`Žu,’¶Äø†9©$‚ÎÏ¢5<€ã €4  ÜÅ‹€9A5¦DŸ¸ë¯Ä”®SÓñ½Å‚<!è!Qô‚ÚŠ;*oŠ
}¸X¬‰¾2L
|´ÿDëQã+4‰`ÐäÇ}NDÐŒ»Þ0_tGP?Ô©üÇ.<CéÚFç ;„»³€!Ð©È òl# ¡(,±ÑCá˜Å*LÒÓâT¢CoLkƒi1IfÒ”ËMÒH¨Gñ¦
 Ò€| 
¸@ oqÐ‹¼U¯VÄ‘4‘(` ¦¡sÞÖ%Ý>¼÷äÃ#»¦Å@ ™»OŒ”/£\¨s˜€ wë¥¹·êÚ ’²AèøÇe%\”ÆE°Á=@®ž©M Ã n>` x( 1à]Œƒw€Ë;µ:² ò
 ê?" ÎS¿ ÜÈ·FWÏÚ †Ž>Æ€ÁC®ØZè¾ˆ±Q°!Ø!xLòÈôa<BúÈ:àÄ"v€ˆcKPmÖ( jj„ FÒ=ò
<Rt)É?’‡áF€ý¯‘`7C;d”Œü€®1ÉøëDú,ÐkìÈÚ€ ã|’./ùBChƒÑ©IU…q€$Ý IÒ@%‹	Æo %‚§Ñ2¼ ÂŠÀŽ,&˜©‘,8ê¢ûÃk™ 	¢°HR ¦Ti–Ž²¡%Bm¢
ô @´[9œÑTxÆûcm	¹Šúh4ŒÆ›àŽT&MI­q’l;*Ih‹ØÀ F°æ1+
U%ÁÀnIË¦”Œ$OÉ…2•¬%©¤yA0Ø, ! w°6	ê¨‚ƒ²	¨ï +A
˜8x‡îÁjP€< Þ fDˆ@“1áI(þ"`¯/nÉw(l<¡
Tû/ÐÆ*CmâØÆ²ª
Èo‘pÑ2ínôâÜ2¿ áª!y}À€Oª.i±.kŠz™òAN˜7ò+šgÈ}‰¼XP0Í1]x"ý“Ïq¥ú…ƒÔâj!’¶ò},Ož•3 ›‰QÇ|@W\t8l€Æ³ïþ…,`p,Ó…àˆ,#¦‚DNÈðËr tÍRÀ)HÇç«0HÊŒÂ‹lU€kúh&¿ÈpŒ˜,8M@X  €M ¼`KãÉû¢¼ÐJÄßF;1nÔÛü‚í1½èù9ûQæ{ _-ýQÐs+‰²Ï èáM¼GÜ‚\#H@q‚tc1
ò½Ã ½Ê/.oãì²j ¸O¢  ½\­` ùÒ?I èK÷Ò?ÉâËü²"ˆêË ø/ûË  	0þ2;0ý“L  0–³t£"G¬î¶"—7®®y›Ÿ7ºòÊò
¦H¯$²jGÌeÆ  òS ›©,P.d‘#ø?¦Ör‘GJÈáŠQ‘ô=  Pøì1ì>Ñ!.Nâòÿ±€l³Kr@U„­ EÄ´‚EÔÊ0®Š\¯HÓÐ1¶ H®;1¢P )À ‚†xÌ,Zƒh6æ X` î ”ÖHèQ˜ ½ð1½ƒ³C<çcÎ€tá´x.£ËùÅÐšÑÆ€±M9(.…Åa-	©;ŠÂê‹K. 9±p™mì{ÂMY7BFB…uäÂ5„q8~Æ$h¹LJ(‘ø|(Íº0
€>;ÍÕØ6FŽ¤Áï¸v‡ åø
b˜#
H78;À
xÐ
<o•`}2 @°6Ãª kEÊRó}šJ³Q\€*q©º£¸éR•ª.wIíÐYÆ:´JÓ ç;ÌŠ½$0áË 3À/ï“ûòÁÜ/Ì`Á0åÍ“Þ"Ì 
 7ÌtßŒ2)ÌzÏÂä1ÌÇ†Ü0çÊ#­®
NÁ0([Ì° 
Æ<GÎÅÇò
‰ŠobLåpÆx]ø‘ Ä ÉU>ƒ¤¯Ô+‚k‘Zd2 `0z€€[*"³Å­[”2Î}k›S—æL€ËñšÂ¹ëÑBÙ9H”¸dK¦1Ô°- G ø  XÞEàŸ‹3…­ôqz<.ÙÇªÍ…ä%/‚æ§£7?¼l­KÃWË.‘¡íÒÚ¤*éÌï²zì:¯Kø±Êà*ÃM¶ì«ãÀ
m Ó3FÖ“bâú'ý-ˆ#¥Ÿ^Ìûdðœ÷ Îeq¼$8 úÐ#°#³„†Lð$LÂÄL,üC¢ @I @„s8Î}‰D·
åì q<Òá992( hLÒ{,1ä h	ï) €*à	*F</Ô@(–àP Þ@àxqO…A¸D¨DéÑ^,;åNäîÇséÚ»o3rì*ÅM½S½$f0ÏÎZœc€	 X˜žŸ´ãjš ƒÄU¼9•,ÇÓ—É0ãÊz	á¬<NI#ý?oãCÿÄ¹s¡ö"5ŒþLœ’)¢ o@Ç
ƒ„Ð , à×LÎÊE …´È€‹>Þ £í™r ”
9`à   ÐÔH)Qƒ>­MlÓªÄ%W:Æ±Ê?—;–!˜,Îž?‰» DÏúÈ, 0ì€ÝAè às¤@43ˆ» >ÀB€ t-BÐ$T®°(yÐD9H¸‚Vr÷ì=Ï4š+z³Â mBÞ ¸
¨æ I2SPü¿:€‡ô 9TFØ
´€h’"Ô&@Â è µ‰Š0›¸ó„ƒµ… °æÂPä ÕHC×Ph‰PüÀF†‡éCQÌ„o.Àn²$À
±P3TýÑ¨À8˜ÑPÔ  [€I´6ìC1“ ÀDUM‚q (Cë ä@ œtM÷£•Z9@4]EMB7æá˜EkQ¯•ÀEÉ†FtýC÷PJÅ åjJÀ"4 ‹Èrš
ìµ!L´áš,²I3`›”+¶ŠÈÁ'ÂC0ÍÍ`õEç¾h/‚Y
Ë˜r”ï¨ÑÍÀm‰/ÂŒ"L xnî‰OÎæˆ'ÂŠí@3QóCPÐFß€â.È&ôÑœæž¤F×–+TXHM‡T…I“ÿÀžL&ÒµåìQ fFòàžp–[ÃF“ƒb4*8FŠRt•FC€…TštcTRD”%unQ‘-EGÑ™ô"][ìÙÁmYY¬
ÞÁ‰,Ð‰G$†ðä
W‡úpn‡'Á ›X[b›è&¬‘iµÒu¹N¥284K‰Ã¥ràÃšÃió_¨6ÍkS}Ì6ñ¦êóN”·Ê`ì¼ä>ÓË
òûô Ï¾±Èüù‘À‘âô
l
t«+8»ºÉ’»-O¢ 
#"Èýs9ÜVˆ‚½2õ
BÇÂ3Á{?#-º“ºÉŽ§ÎL&2ðL<çÆ €G1ã
ÕÔ?Q<ù®Ör›S)8’3Gü9yÄ S"¸MyÎ‘aHl@Ì€Áoê
5|€fÃPÒ `8Þ´‹r>ßÈè‘•KÅÒf3¹Ä3ïR€ Kœ»ÅŽ,	Â@¿ÀSøF.á.Š@æ	ÙÀ`?- 
O€`	0¨ +@	 Œ *°
¨Œ€—à•3Ò lƒò^ ªò©R2°f=maÚSG>•OÁ úÔ>ÅOõSþ6øOÔØ§…ù(U±¹ÔÅò
/ÌµT"hKMÔ=‘ì¤8”Êì8‹
P¬õÒ¼“/%7
ÿ3 `X–¡O8´€Q kN.EˆëÇÌÇŠÌ!ó>Ð‹ Pœ ™ÈB(äT»C®ñü!›¬ÅTÃŒC.2áô0mº$èÊÜÍŠ”‚À8ø€a€(xš¢&R3kÑü;’0(ÍS“	v´W*SÙ»¡¿
ÛñY«M¿.ñRš)	|Î±G›>+!­±5¨Oª0Ni
ät ;Ø €xN£Óàòêä.qÓ5Ü²3µÓ²Û;Y†ï´‹ÊACòT29Oƒ‹ÂA•K TÍ 3©âÓùÔBÍO© .Ð i Ô)@@-?Ô
òqè:(ðòÁ,ŸôôrU/ Z
‘PsÕûtWíUU5ÙüJmÉ5ìªt6IÔ*ì"8Q ‚·tX|;yÃ_lQ{SòRFÝ>kT°2,+‡85]¯bÓ`6/o|5 P<ñTù3K¥?%«.õþüRÏ…ÀRýÄ9Å…ÀÔo|/ ±ŒSA¤RßC3u”A“†Ó0

Î‹ \#
$Î†óù²8ÃŒ3dU2Õn†Qíêü‚oŽì²X	U-1ÉØ§¦E& CÃ1=
ø Òà
@âwQNÀ×æÅú1.Õ:ëÌüq]}YKOËT¢ÊÚŠÖsu,EZ‘ËmsÁ»>©²­:…K[P¸“EÕÒ*LT;‡p¸¨Õ‡JW½UR5«ü:YWlUQ·N³sîL;µJ@*«2„8·ómZÉR¬Òë4U8C
}üÖ¸Ók-TcÐ¼ÀâèÆwµ‹³Q1=rc¥¸"VÍôrƒ-LÈ’Ucµ\yHÆ³u¼RSÈr”+
Ö2uVÏµÒ ü3å<
V 0à XÈÏTP|\x")x2E¶¦Uü„2}¦h@²d]ÄiQ(œ0'VqÊdW©›!ÑøpB•Pc h6 
ÀH º€Ÿ•Sµ:UP­Õh•>ÏÖôk…6ëÎÐUáq¾Äòµs,¡	ì@|.0f5yaäWh"Zm"jVF¦C•K>TÏ
Z)KÊ6à¤ÔAš€Ã£uA1˜M‡ª.U.¯V¢`\
 ÊU)o7”Z©Ï¾54ÅÕÊËB‘Fu\ãÕ£`^µÒ.‚b`ø>y¬ÇôOŒTÿ$È¼O’TÿdIå]
Ì@ÉÄÚ<ÝU	€2¾Ú\}+çO…st½½Ôé­¶ªLgŒÌ4âÚLÿJÀ aõOVÇÎuw_Ë“F•rTàUxÍFrÔŠ5 r " BYQ
G<TIœŒílÉÂØŽà25´LU€Ë|
!ÃZç™Ò šV`°š<éÔSÍ[§¼ÂU]_ãÏ*Œ ƒ ÜÈôu_:I‚#ÔôR´ÍÐ)üÀÜBÌ°öá4;›FÎØ‹ZMmÖè6bÍB¤˜5Û€Z³7¸5…ƒ\s×´|M`³:Xy„a3’¥u*:Hv•Íè”M	Ölå[¿UÌm;].É ‚-.¹Vtõ–M[ýÖ</7½Ã½”ƒ%
<ØÀXãT’¡_ÕW
ÏãuÃ«R=WÄ•iE×ú“tuõpÅ šå}Ï“I5¦È¹ìXõ7²ˆ¥@ý+$Ö/Pb×ÊöyUÖÏ•y…–è6Öõ22Fk5ÄfÃ`
ˆ€o^LA‰Ör:f'XÃ5fd :–¡
è3ƒI¬sš4ë B3í+úÊf	…(Ø’dõh4) þem™4C•JSœ[îv’¥
+Ùòƒš¢JDME3¢ÓLÍ£¦M5åŒ3
“59`Í+eùdCY—×t*tM<×DeÓ
!@kÐóBIµ¥†Ñ
{«E6=TZ¶ÏëVÃWnyb	=iSO4é°®Õ=\]TmVŠ­ñ²Ï
rƒW÷N¿Ò
J@Ó‡p¤\Ø£@r­­rX°u‡X{Ønö‡5XƒXqaZlˆ 
 ¶	]„Hõ1‹L–3a VÂ|êòI@‰Î¨û¤†½amÎ&¶õ„kmOq¤O¥\²L
#PE ïUÄSbµf)VåuÔ9 ×ÜtŒ»ÊØáiè”h‹Gue,ÿnV/.˜,
   €uà hháLîUÎ$:ÛZì–¬µhóÙ1Œ £ÙUõ15ðW&B3”´À^6„EÉ;¥}­ö¬­e½ËŠÖÊ+€¸ÛpµUka¯²•¾ÅNØ
vn][íÎ¼Ô®Ý`ñÚ„K"@ìê»)Ò rÔsaúÈQgNsv5lÓW-Õ‡¥<KWü³6àÖ,×•õÌb™ÃÅBÿ´p—•–š¾jƒr6Š=¾X
åY$<Ã.ßVGtYÍØHCÆ•ÏÌ…!±áa*In2¹,O  Ö€èvº}h_>¨UoícÑÖªÕúä™K–á»u,8ÚO5ëoq0FÆ¼®€ÉUoåŒöVƒ:Vá[¹$ÒŒ>ïÇ½5n-K=÷oõ
zÙ•
Uý¨;³T-f½Í»™½k½ÏnqˆU\UÏßõ^ÅY Â}q	Û³ö…dLcHÅ–Ã=X×KqÄ"'.À²ÈdbíÙ
ÓÁ]m ÐŸËÒ•¸^£LWuÌmÿMN7àôtY²mNÇ5áZVàøqu÷Õr¡¸ÞÄ: à‚\ h6Î  €EØé6DZrƒ»±ÓÎýo©K¥ÇM\¾~‘e€(?“^¶ÜE¦¼½
"º)`
ˆýÄ\¹¢Ì5VŸð%¾
kç61Ìãi ¿¾PéoÅÚ´
Ê½ÛÌÚ‹6Ú¼
èÜµD]vµÍf—ÛÜs	Ï>×nLf\ˆS¼à<?‚WÐÒÙºìÄÝŠ¸Y‚5ÒýfMW#S¯ýP7e€XMÖÀöºæÝz·UÁ]%Ó6ãí_	ÝÒÖ	8t3/EW„ B ×3›ki\y Q½q‡'ÍóOäq¡¥é”˜D‰†Pn¦€ (bWÉÍc«ÛŽ¶»îEy÷Åo1Z…ÇÊuhÃS¡„,ÀvmVÍð
à4´‰ sËq·Ó_ÊÝ4÷ÝÝZÿ¶`ÖÍÝ¨tI«uÊåe÷[óŽkÛwéÒ ×µï0ØÕÀÝ ×qA°MbÿÚw¶·¸\7’ÌUã$FFVÄ1ÕµR³YÖöúŒ</‚Æ´ÕëÀYrÁ
àR€G1PpÇ<èó½™‚+ ôMÂ€G`Èå.Þ
 xO  ÈJ_ @ H €
ø^€FÀá
KÝrÁšp)ÓJ
äàZÝÅõ`€u¿¦¥Å™uUÜq¡y-9Pcè
 0- hN“^cwé…h
YïõÎ%Ï„Ù(×.ÝeiÐª¼µvµ^x‚ËÕv1
…´	
0Ü-PÏ\´ÒT{Ï;ñ×BûZá^)wMku*_,ý^áV
6ÏU[å\#àÕ`ÿÞ°’dpm9ØvÈ*1!/Ú¶Ç3~!_PWî|%‚Ê—r|õ´‹`2¥9W €Ü£@Ô=b'.ÎÓ÷½dPWûÎ 9
÷2/øõh€€X!c^Éeæ¥ÌtÓDuy}~ÓØÈñì±îtÝò Z5–f@º€ h ŠÝ7óØEÎ’ÝùöéMZ¥^˜Kd¬€ëµn g îÄ¿ N<ÿØ1@PÿÞ€ °ý{ƒ‡`îL¦ "Àý
ýÞ¿V²þK(™ˆ:xðƒ@@˜éÿa àƒåŽØOŸ( ß‚Ð4Q {¿¤Ãü @¥"ÿ› ?
pÿ

¶Þ.wÛ-Q¨ôÖþ-VÍÞ9&ÿm&Ô\ëÔÉíu±\òÙyÃ»qßåoeá»WD-X¹ws£kÍK¿÷Ò‹€Ç…iQ ^!bÁ0Ž 3¯²Ýl3EÌI†;Û ±I6€=àH,Â¦ä?e`G7ÃMl7Ü‰·Ãu„Ý€1F@,àEªmóUÖt/¸d
i–5=)ŠÚÛ,µÍbýÝVäY‘²Ö²=a«áNñækeÑt! ºáõäU‡‹_øÔÝp¶¹œK\~Îæ×BÉˆ¡_qAú5›æ‚êw² úg` x®`¥·Sez³Üë¶í}rµ[û¶°%Ê¼[õj½vÛßl·!…› Î‡þµÌTá À½ý^ZÚs—ÿí…¿°5—Õs¹SÇ@®{Ù^ŠÖ­ÝnÃàv•qÅ;\@÷"xVœYoó¿È‡ŸY$ŽNõÎ-¬ë0Ûì1ò"
Æâ°ø½€‹í•
øÓÕßtà‹ 8pøÄRœ†QØ|Óþb @ dXy‘.®ˆñYÆù‚
Õš—Œe~Ýnæ¶2ƒ1WÂñs¬€Qè,è/@  Ô–¸ûu‰¿ßîµ¿õ‚Á×¤2ï½XÛb*7ý½rÍà¦
!…ßß 
Àœ +@({§‰²×@}o]aocÿµ{³ÛÖ—…sõ^´V¤£{=¶V&®o¿–(ã{WX^½Á¼X\0F†ß¸ñ½m“WÆØ.]?às*-¦%ÞË—âMp…áÿâàõ^ù¦.^^w«yÖ¾L÷Z‡•¼ØxaLûX·å.,cå—¾yeÝä÷µãy%‚Ìx÷ÄØë Š  (³`ï—Éh«Ä‰Ö–u{`b*Îò¬^	jUÝ‰)÷×'Îwã"À7&Šb4WÿMŠUãY»¢ZçÝÚiþwŸÃçx:¤‘Ï/x:F;âî:ÞŠ³ãv8fP^	óñ^IÎS÷\0qdÊ8òõ€!^ÉÑ›mÕáÚ@O­
&àP½€U* fE2VÅ·ãÅt _Ž7·`|‰‚Ä—¯Ä/€`Mæ1&6bÞ”H‚±Oà2>†Ž ×úˆ€A€ 0Ü,x6>5dð÷G~Â¦O!ù¾ÅŠ±¼ÅÔ ‘±Ü ä6V‘ ( L…Ý˜àÿEŠ·UÞ–Í‰“ã¦x9~åÚ¹— ®s»àÅK||kïã
Ò]=’aíxaó
rG ÏQºHÒr†ý-_9]`9,Hˆå55H’eö0 HÞ ¥+H–ÅãoŠÃsboáx	KæRÙãÇ”KÀä?•(mµÃ@^ŒW]=™º)@÷ã‹—0/þã4™p\<_löŠtŒùä!¸ÆÝMÕ|Š@î“#ÇÍb¡!Bs`² ˜È x å«wQFv›\e9ÎNa]–ý:9 'V_É‚¨”¼•FÀÐ„ú”oÁP™Ü•ŸÑ¬³ˆ;•/fy;{ŠÏß˜¼{•£ãª8ê•ÛåAQ+ös¹â¾”\hXÍYÆ‚^–“ÓxÕïYv¹Jî€ÃOôØ«S9L v’ã±~!i 0Èå
˜Äì÷ú¦G w\—AÓ8×A“æ5i>
@VÍÕMâäÊ³ “æ~92þm'ãá‰n.1„Ó;÷„b€ ž¤5æ øö€`€  fê5>ƒ9äëÔbpAd
–n=UžŽYQoåŒ9E‘ ¿pÖ&~ã$e>{WæØÐA`™Ýÿ7G‘iæ{·G^yaÉ™uÞ™æ"ÙVþ™‘äeö½(|]`jNmþX5ÝÅñã›m×
7j×b«¹Ü¼¸€(ˆÿb„XÈTˆûctÉ"HØ (u=	1 W 
à&b’Á,v]Þ“×w-m€•—[dˆ™d¿ 
°ø†áaÄ e 0„¹9A®›mÞXWÒ›5[7Ï:ð<] LÂ1t`² ,?  Hç–x{uœkcàÔC~™'ça¶rpÓW
`ð˜etEî‰·Ý!ðÛê_PùþU™Í]Rù kQåZXfÖ‘Eä9Wvž*igéØ*®‰ÝÚHÞqå$¹\|åYÂ·â,xáä$Zdå\ñåmyêsxØ/UÃuL+Ç‚m;~/ÚYâ°Å&®6?Þt—æN—lfŽ_¦€ÆpÚ$2ÉèŽ v P~à2™BýgÈ€Æ›d;YÁ( qÝzöâçpo
€m€‚6-hF95æ¡sfš˜:¶‰ÉWŽy„ÎzK‚¨449¡G 
ˆNf„Ôtn…QgUó—¥Š5èHÙ,¢à<ÑUÎw•âÚ™ßƒÍcJ¹.p¹8Þù#ð`SÜC>¾èã/©Ž€4åÒE‡·äI7œ‰
,r6@$šÉ¹ ÐšŸæd^æDóY™ƒ231€8î“k@h>iSŒOHÁ˜ÈÄž×“T€
èž¿a7 ÈglÙ·r’YÝ;ºqüå"¸] ýèy  G‡À¹e i[` ˆ €	€@*hàn
¦˜ae ùCî Ëf„Îœ#æÍ¹üèœ7éúow[èUX8>Šah–Þ…z#i±q•Þ‘shWz®˜{hù*¦¥{æa8ƒ-†Åµ6 Ì<8&˜ˆÙ@ Æp¨´"„¤t†GˆLt±iaÚüí¸¼eµ¸j¶‹7: @È _ /R©çM1VRåe‰ 0 cÝ(›ˆWÑ?¡§·•ü4?y"ò$Guy«Ùlùš¢ã|š_Î£%cŽwÞ“áãcHÜ,„°Á Ft‘ È€ÀP j—obÞ×Ü¥¸»‘giZ™ê56ft9é¨§	jJÿHó¾6Ïõ;R Ÿè½óò<{bm	nLkU@Ícý	ÕšlHÐ äµö<Ž‘EAójkÔºõ#NWkâ`.ð­õ¼µ¥´®mëÔú¸Ö­d(àA^®ckÊïÌ;­×<ãÚÍËŒgÎøº®oRíš¸æ®qkéš+©Eç>T'€­Çë”:¾Yeé²VlT•ŸÝôY¼Š¡c}7–Æ{)gŒvæsoéøŽ§|—jî8Ä-Ö
õÙ?¡pE6µšÁ{tN-9\N¦¡]Æé-ž‡ýUÖÑ=æâ{™ŒVuÍèUYÒ@/††~!]^M…E±xÃžf—d¥9¯ÆmËc|º›ÉˆIÜ|Ä@ K¥[Ò8-~Çq¢04¨ýfƒ  Àè&ë‹à´ ¦ÓÄØ‘ž©!iºß{`·øi½¬cá¸ÍM€/ê3úƒÆw¡Ðk¥¨åÍúv6“5fªŠeXÀÓ
eýµ@HƒÃ i
Þ‚Ø@3ØC„ì	<ÁG&´!èƒ6Á‘+nà@Ã2À³
Ã°…Ï ül@ÛmÉimG1•kä¦,fž¤qá³ŒÀÎ »Ï ù"€jD°˜ð„ ØhØÔÅfMs 
€ðuêYûžæ™Gw`Í’Áe,º>Ô¢il‹×#ÀxÑç“ÁhŽ›?Ä4ª†Û´¬Ú1pSÎ ØºŽgîj›ãø’4{–‡¦m/ãÇ~ï2Õ&Ô šv Ð€" ŸihìˆZŸÛ²×^ÂÓ^Ü0æ™™ÍVÔ&ª‰gF¥}hIÚ¥Î¨Çìô¢Ì†KeQRÆ·‡dÁº–®ˆF©ïüu·K8‹úùÒ·l!ZàýsCm‰ u¸°75ë„dÒ€w@F{ìJ’ wØ
ô€` À4À
ø #@ Ø!óÕVû›–“}i¢ 	8Ö_À ¶4 
øê#Àåþ'öäA\9rc;cØ¨êõø˜¶°Ùb¢@	3ŸiÅ¢ i»èÂ—3…°“îx»_££d¢`h6yƒm¿vk-G$
Ëô«ïä²) ¸øª™Ý´»9¶Q†áIí&¬÷fhéì‘
ö 0 ‡)#Ò,JU™
8@ è &€²–éÇ³ö¯Ñlò×Ê¦aã°À;1åùESÖ f
 ¥×Û’@”¦Àˆã¤ô– 4ïm—ó¦´€Ï[Î`adÒ»'¨ä
Ô3Q½Yï‘ev¡Og”!­½G€)@ØTè¡˜¤.ŠÍÜ:íMŠ[•ùú¿N•íkäÙÞEökù‘¾·kêš[¶œ;ícVâš{jÇ 0óÜá­[!^Àv…\fÄ…ú†x¹oÞç>áoÞ€ÁÑ	œøÛ¶•±Éã¦yÍ^ºÑb+Úò}º›çËó"`WÂ 4Àþþ¢’>ÀþfiåN	 „ C€ÈÌ€¬üN¿Ã•óÛw‡¹îZ8Á “B?¿9ðoÅ/Úï÷²¿‘]òoiÃ¿tÖ ûÛ ©€x@(p&h»ëÚæœjû¬óä‚‹ñn?™h@*‚^BZ ’  àïE:Ý†ò¸`{Û¿Å¾ómíûƒ®¤5g‡j4¡mÖðV•OQ±÷EF¾yoå;†U¥ËÌzÅUŽ£oòµ•¶™_éã˜¦f©%å›ºVö™½ï\Ú#Ø¥í2ûFxWà û;0Ä!œáå>™]q \ÇO$ñ  }q Ûx!^ŠVm;ë)ú°­¢Ïá+z†± ‚ýF(€\ÛÁœ ‡?aÅE ‡]‰p
q`f_Oq[°—óÄYÉ¼xD@ Èftƒ@`>ÙOøJýd}a3©|€ÈÎO–3ÄÞ¿™f3z›C"‡[ŠÏn¶qwìLi‡œ
m»â;Œ† o‰Ö 2,`ð
€\  ÂeÞˆÊ&.
à…ûˆK¥`©:&
(8,¼¿ÖÃ«lÎzÖÕøí*Ãß6W­o-|ûpP­"ú$aCpÝA4
ç`õ¨Æ ºÈix "@X[9£"U íÓ 5°|€DÛ°³åŠ<$_hKÓ;MYPr5 5P
XrÊà*0Ð˜\PÉ
¹â›¼É{ò %ßÉ[ò^¡DèÈoÁ˜\$RÓÛ(?É‘r¥|%oÉólœvš0F¹B$ö˜ ÛÉåŠ¯<5,?¤ÆPðçj¾}aM-U¥»pVÚ‰¸`"ºwb
pâeþ&^€  çGT‘Ùà÷1cña&0§ÅóIGÂq¿qþD1ßÅ!sÿ$ðÅAð×yªÀIq<×–<µh\<¿qÊ|ÒÁÄé Ó0Í__I< ÄWsá…]	4ñÒ6y‰]~qCãu9À œÀQÂjƒ`|õõ"
 Šà¼êy
â2F‚­½"hÚ€Ü…N®XÚèqO˜¬éÜ}Ö0Ò.y áY"ê2 á ò€y ( 6s`ïfî®
&æÆ»ì
Úv¶©Aqòµðª2§ê{Ê¾¾r~|î¦¼%-»QfÈWãÈ» /¸Ç0„9ª·r*f6Ðýo,Û#`½Ò
×«ñæ²óñNòîÇ#t1w
ÈaéÜAW³¯ìÞµxÒB=÷|‡Ïóq»íA'Ñÿóé‹@»™ôýœ Ñ'oV9êÑÄ¯üüÌ¸ÓìùÜ 'ÊÞ/d(þšõó†ÜFÒô*¬ÿJ/¢åI'Ðn
Ý?ÏÑõrCŠ‚JÑï=IÑ—t½DºõŸßüH÷Ñ5ëýFßÐÅôà
C†²°=
ŸÒ¿lx;—·Ó(ýç³ÐÙí=÷Õt+}Ð

ä0dˆÛÓÃ_?VUëôÌÜÿ
©Èm•ÒcåE½JWº9tHÃzÄâtÝKç ušÎÄ¡MŒS ÑôOÝRïº1õ –
zd)þúT¯Ñ¿ôì[Š&_}©  ˜ŠÕópT]>¯Õ'å1ÌBq€wêqóÀvØçxìïZÂqÌßËÒZÖE^œWRóp`Z_PZs7Ç°Ši
×6Å÷ÔÛÀ aÁý“÷;¿Á£=U<ð óÇ0?aó—÷]_Ìmñg]ÿ¶§óeú<‚–ž‘ãŸ¢EÀ  Ðõ#6¬ðáòCçÕgõTÝEÇÑu ÝªàòI½¢¦ÓUu;ÝQ¯²ÖCàFÓ½ì}ÜuÕ_êP­…“©Ïôñ—J—Ø×t}Áj[ñðSÚS÷Õ¹p[=¼³[q¼õßÐ)õ/P¿ÔÙô6[§²ÈßðÇûGOÓÁôýJ7fµÏ[ÙÀ>†åOv@Yšà‘Ê4
hê×"—Á4ö‘Ç['¦™nªy±%ÀéT‰ Èfìü[AÁÇI|\8×iU\¿éËUñ`À €×
y]l'b=PCY
*‰>á.REè"ÀŒÀ	 È¾¡A„ð–>MžÞ4ÖµÎZ
.wÉ]„ ³Èš`"Pè¬È}hˆ16T K»Öµ¥ pçdè,¤¥‡ŒÓèr:
Z2F ƒ
Xƒaoh/2{Üa¯Ùçô’Ý†nÔYuF-è¹Ø#X9h Òuˆœ(KÐÿ…yïÔ÷›ï×q5±µÊ€Û|öŒòvÙ…öUhÇfö•=y×Øƒw§¸c'Þwö¡gÇØYv0JVfÚ'vûq3Ú}÷91QÖ8öá]|ç¾¡ö!ZjÏ•çÚ uØ
æövþNÅk—0 ^ 5.÷u=Y'^Bþ^wÖ;ñX›_ÒÞÍ<âuº=sÊ4'^"ÀOÍgóãÅ6¿OHøÞü6H*  œd  ›™üváDä,Jø t xœ;…X]+³sé1H„€
ˆtîý30HŒ«ç ñ¦ø À
ðŽ9¿~/ê4]³üû§ˆ z`8ÏÓóÝÝOëÝtYÝf§¯çw˜ÝiÏ°t)~WÔƒv6>P¯ß±V2w™Yô,‘y¿Þ·oÿJeŸŠó÷¤Ï³Kì;ž(°áì!I]Ž·‘—v?~ƒ×ÙÏ­FÞ|÷Þ7ö—ÝŽÏ¯‰ó1ÎFãv5ÞùÕì;Àí¾ñr¾ˆ&àQñü&5r€Hœ]qÍXþ—Íis·Z…çÌjùõ!øH<wq‡ßeÁõ©Ùþ”t¡nû(PG¸örA˜€
Ä'HÆéÏ±Ù>ðüá–4 €€¡8¸yFL"x€Z  ç'oþœ7Hê•D  ¾ùtÌyÓ’6²€eà?çS€`žWç×O˜-Ÿ‡ç ‚D€ðço¶ÔöuÏÙ…“:-0ä€äšƒ àæ z ãvÞ]åÞù©Žä™tP
 ÿäwå’ÇÙõ*ìyçœôî]iÿÞëø¡=fÏÉØ3šïÒov‚;L—éÇw£¬’ŸÞKú—þ£×éÝx£Š’¯é÷xý=ÓÎäcz¢pµˆôøK;©·ù÷“=£¾Ë‰uPü&–ù×÷3Kf¨@@  ‚¥`š_Æ+k^Â¼O¸z”ž%
²u¤«o/„yÉ—l7æÓá
û=†]VxÀ„¿åSxÙ¼¯+·àÜÃ– €AÀ€O ºþ¿8ç ‚tÞµ8-Æùrþœ·Ì"û!^"Hæ}>]PGúµRØÈîÎ¾"è þ z~­œßžmO\¡Géû•}Êøp$â
N  Þ‹¾ŒOàÚs3Û’wé1yð]}÷ßgXãª‡Ã
y9ü×â•d²-émú^}ê›vŠ¿¢éË÷Ÿþ|¯Þò?þƒ^äqŒW-Jçèã3“>§§îysËpÃîÓxê}
7ïé÷M>XºßŸ{¤¹ÿÓaúð}§ÿßëZ\:/×¥µh‰6ŸÆy"¯ýÛ¡XÌ¼ª—°Ía¾Ç|õúÐü>	X Ò¼¿ÖÙ•	?–'^fyâ%Ã·åGx\~ àÄoóŸ”74û  ³?5Äyr>µß4H §&F`  ÎCÀÆ?â)3€>˜wÝ6ø~‹¨ïPÂ8ø§¢z Èí‹³…½²–¨×mDÝ¾Ÿã—÷“¾y'_Ez(_doÙãsé¾Êïî`•þ©béÇ{†¸Çï…{ý>¿î ùúþ¦ïòßû6¾º—Ù{ú£>ª—ò÷÷¥>¿oê£2£~½ åÛ{þÜd7ñ‰÷NþhÏ CùK}¿¯ãZ9À>å±úÇuj Òªö ÅéB0ÂhJ`iº
ü]†iê‡=ñø¼¿’;ø[ûƒ×B|„—üz°Wõ{óþ6HšáÜ1€ 
{ÀÝû÷ÅÏñQü jü}Þøõ7’L`Ø·ñ àØÿ$¨4 
 öbÀÙ
r ,`Ú0a€c‹§ÃýõØ’A~%šƒ WOèy`É·ÈÌxþ}÷íGv3ºò™{cˆÞÎòvíÞ½·Þ%y\-ÌïªÆüwËWô¿}ì]ßÕIú|?ÐÞüR¾(ãÛêüãÞÍå©úA¸ âì#Ÿž½ êã}/Ÿß ä=ùÀÕ¸¯ùx	òÌïßÓ|[úÑïïSy,}ÏM fþ?©"™( „qY4 0Œ ð @~j»j[1¿Ýöô;nGmA}‹XÔ‡šíú
„ ExG<5pÖ\–—Ä§~V…ñ!]^´¤ú{ù  Žù'úÔ$à
px}òâ‡¯…2€žÉ.¢€B`hÔþMg(Øþz ¨Wâ€ ` Îù@¸ûÛ~€ ‰÷ Z€îhÁ?ï7F´	ÄŸîì~¼ßÛ¯Ž[Ýü1Çð³€
ßZoå7
_š“ÄƒÎÿÃ×ú]ý)5ƒß«­üò¤
0ø
%ˆägÀõ·Çð yÀ°ýí;s&à PâÝ? À€g`÷—ñEüß¿ö§‚ U€ø7þ
a#è €Š hþ‹°Ý)²Æ÷õÕŸ%C»:×‚Mì9¨í’`ú!ª™‹O›Y^Í€ðÿNg€ŸêëB'F6Èå‹6csì’Àê9:þ*˜!„¾’A"  K    ¬    / ÐÁ rªAx ¡„|–H^ „XÔ¼) EÌÐaS Nœ ÿÜeç eÎ¤Y“ ‘6‹˜zäìˆL5ÃØDˆ  Žš’%
›1NJiÚ»÷$ IÎ%(5¨L€aº@&²;
 1ÂÔmM \²vFÀ	¦A!L« ¡Y´jÙ&H b/Â¯D ÒÏ…&± Å	j`®µC({6íZ#„
'–)5@ƒšœ	º!“ØÁ€Ëv
,â™‘M¿åp1Á8Æ FžìÂ2ÝÌ›{/%ˆš¦êÓ©¾Ž-`öLÓ·	’ {ù÷ß—
;ùÅqÌš	BÿÞ¡ó™ÐáË„N®lÚ³ã+vw{¦Â
e +OÐKn= õ{Oºæ €=Ê'‚Âà ¡2hËx ’I„è1M"šþ£É‹üâ¢ÅîÞ
/$h
„òs‚Š¨HÃŒ6Ú©
4j:!˜HT$J)™Š ˆ‰
dÊ(y‹ÅöL‹`¹h (
	ªÄË îá6E$HÍîNdŽ(Ý#èÅÜdj²»°h´1 	Ò‘G²"DRI›˜\îÉ*kš2¢ØkðÊF“  ¤êÐÏ©°AGD,Ð $ Ó*“3€:¸ÂrTè	 H¼	`‹÷´)NR]„‘M¢f¬ñÔ>ø³Çƒ²HšŽLr9E{c@^ez”(î 
Ëï “L3	ÚTXš zg’0ß˜€S‚°µµ&\M£,óü5×k%hGb=ÖÐe{k–·gÛ‹¡imªVàkÔÖRnÏ<wò±D7St«]]ëtË×=ƒ6Pc	Ev&e¥B´¶;m
˜·`Z–’J›$µ6,u{ƒîÒ2¦7 ˆ%FˆD‚LŒèÍ[ÑÌÞåíØÞ ðyÐB“=ôÔ%ÁuÊåZæå…Ÿ«”R onØ[£	êyâ +Æîâ²xw¢xÒX¦.ê‘e*™êD­ëÞ´æ:Ò¯!œ»
à[Ó<åè „N—hvÛ~{Í¸çÞÙc„ìÖWd~O¶8åšV~Kp›
®éàïÀ®2qÓü™â¡-f
ã9wÍ|^Ó8¿Ð»÷•º_Þþ}ËôÞ&Hu¯u=JØÉæ;F‚ —<€šw.úÔË©gŠcºÑmøÏ£&yjý>ðÖS‡YÂ(Ÿo°{^M3
üÍë&?dóõF¿xõ¹yVr_×fò­sD"2% A
@ag¦ÚYª&4z¸*œÓD Ü¹Mw{ºŸÒÂç7Ïñ/oÙ[úHGÓâ
AˆóâS8š5è[aÒX ¬§¶ÚÜÎr#<U	åÆ;„øn|ùRaè˜@'±¯R°!Ppì½e‡áš]Új·6!rˆ;3¢æz§?&âÍ‰.\”“W@j]1‹n‰ÞâÚ¦
€´m‘âºV4ÄöÊ„ù_
Õ8¼¢¬“‚£Áä8Å™|#®àG·ØÃ~1ˆp$ÒŠÈ—#.­û;äùˆ÷ã	ÐtÔûhÂºåÈ¯7ø
Î2%½ $0 
@€NpOÄ^‰2€
F£ƒâû`é·É2"±^£L£ðL™H ½p&1œa
]yCç®=´ÜAÇ8.Vv#'ÅHûÒŒIDãÓ¬é¿Sº%•Qdd+
IPb1Jµ$S<b—N®³˜¶sçÎžIByfaOM`ZÄ8¸Ak 6e2@5rulx@< œ9Ã¥zv¢^:‚ìãôfâÌ1Þ	g¤¦=A‡È'z! µ‰/ 3’Ž“ æÔY“ âG(ƒ PÂÎ²×ÌNÂÍƒò”&Ÿê¼Ÿ^3¨‹ŒeXŽ
¿I‰ó;ƒXj niÇéäsm'MCL,‰™¤3ÙöNO
2”'üXûÇÂÿ¡ŠD!*s ¢¤**'œ9l”-5å!„Ú# 9À„ßìŠ¼‚r¯Êì«ß°
X­~o<E!)ï‰Ø|2eŸŒ]eÕÚäOÂMV  Z˜†ŠI…^õ¯3y¨j KÈÖV3¬øÄ¦{„JÆòö› ]“ZŸH-õ†+ lÛò—Ivnò;ßR©aÚE>jÒÜÅBx‰Þë	—)äuïyÕéÅð®·&õ(ïz-Ù]ÌÝ7½ùµ©LpH›ì”ž=5ì
	ÒÂŽ’U`®d«$Ëöü7®ð­©ö*‡Ú wžÓL®OûàÄês±*«íÆ(üO
ÇÏº ÍO-¾˜©ð*
ˆ\p°˜4UoràÀ&xµ
.qƒ9ªØç º	ió.<cÞ,Ì²¸Ô%/…ÌR
SUÆÔ _·F‡Ž1žÕ=.k
[>   ÂP–0$#Ë ÝÎ‘)äd¦.K.ÃôËD&Èg	Z–™´gn¨»Ô¼'“øÍM*¯F]*VÏW¦¬R™¢a{¼
mçöÒŒàšDÚ«†ó“Uå)§úÅ¹åôn_×ct¿¤ï‡OäT/YÒsíraÛÜ\ÁºÅß›5Ró|E™€ZvŽ/åþbï-VÕJ4äkQÛª)»Ÿ4¹3$1ÌXà¿I@M„‰ 80S~r€OÃLsý@]ÅÌN½&s™¦Uñ©ê#—ûÜ%/?¨_˜Ï¦0@@ºï A‰”Ë¢Ê¸`B3ñÄäÇØÁ‚2§ƒ,¸üTó±0k\›„_g“Ú©xnüƒiß $˜Ââñ(È¥2»nt¬LóMÛ§.ÖÑ~¶ãó° <2y@Ñ;>(=ÆYzÐÓ%$uü´“çÜñÐžà£@_ßØ7
 Ó@}Be çÞR¡ \(CÂå‡ì›Ð]Ë·¡2)®`G¼ê&·ºÒ¾4uË=Ås< ÙåM—¾ÄCi#”M-r¨ošÕk[ØŸt)™;ÖÊ´ÜLo–¿³e7ÉŽ
@¨FæS!: ¬r¬dUÚüœö×©u|W¹]ìË9Å²]qé–Ý«*ÏÄâq³u¶xÌÔÂ‡k\åJ`~ojG£:\«¾r£?çW×9–Ù·â³yû}–úØ4gãpé•Ÿ~`[¿6c²Öó6éë—Dš-ûÔÁ-g«5=£µÐ3›ˆÙ£jc(ô;æS²¶ßq¿8k¡¬>º¾™h@rÓ4¿ƒÀðÓ?„à?jë°ÿÓÀÔãÀÇk?
AôAqË—¡¿±›™î[Ì¿{ã½þ#0Ó30ÔË6t¾¯’<±²´¿aÀ tÀ‘²¿[{Á{K­Q;?_À
¶d=bAW£¾!¼»´ê4èÁ5 µà²¶ÓÃ6åÁºœBJ«BÊ»BhyÃ”19œŸG;•móC×;6ØD7ÔBš@ úb ™2a
¾1K´Ñ¸ä.2L½5Ã“3ÔA'›<:‹½åé&…QAÚ+«CìKò¤0ÄC'ÔChâ*QŠ¼?|=+\L+·íKšXäYÃ;ÄÀ1´ÁlÅEdµ^tÄ_T¥q›	aLªÛ£ÃSÁ#=ZB^Ó¯#{Â=ìRdD„?6”¿7’DíÃFH’$J5¢Åd,µeÌx‚4ö3Gcû6dS@ë«FiQÁa4®"œ¥¶z+J4/Kü%0CCC•MŒœc8äG‚@2æsÆ}„ÆFôÇGFiaEé‚18$ÂL¶:§:d“ZTÆk[>QÔÇrôÈsœ¾pSGVJA“47Û{‹¶2(\š6Ä°o4²ŒÇ\d3@À8Ó (ñ±(ŒÒ¨5ÌIUœ?v¬?,©“
€”Z)–$—ò²K|H&¬A|,êH^üÈüGÜ&”	!ËÊ!D˜b|
>5§‚*©
 ª²*[G\d´glË›·¾iC+1+B|ÅäùI¦`«•RÈ–ä7ÓD€3+‹d´LË2À>´É~|Ë¤Æë"=ž¤5C<È#ËŸÔÒ,Îò¬#µ€33¿B³P„B3MÄ,Mt´JH ÛZÍ lÍ”t„ð­¡D¨—´Ç˜d¼1ŠÂ],@áÄÉÅÔÉ*¹<wLìZŽí’‰/¤¢ïªÇ^“EÚè¯÷šAÿs
úbH
¤Aø\ÏJœÏ÷ä
þ’Î>CÂ–ôF1ŒÉÞG]$¬4ÜÁªÜÎ«LžfC+”¬”	¤ÇÄÌ¤,L“BÒ|?í\
ÆŒ¥µ2åt;¿±1vÂ±ÔÛ1°ÔŠ[“AL˜ÌC™ôMÑÄÐàÔPÅäPîÜ˜ÇüÎ,Ã¿ØÌ¥ûlQà« ‰Ì EóDŒ!¥„(¦¤Qì´Q¸ìÐJ™=ÕË„ü3 •O!…ÈÛÄ#íÄ‹DJ%µP2*PäzR¼ÑÑÉÑVìIÈlÌ¼t
>³‰EÆ	}Ñ[ŒQmÒë<ÐSÄT,NµË“ÄK×ÌF/üO÷<KPdÆ=%EàDÓÅÑµ’í»Õ­hÓÆPsIôLRL½µ¬ÉMÓ(eSì#TŸŒS·H·u#ˆv£‰w#Œ›·zû-|Ó· ÀÌMŠHáËÍEÛMÛÚ™ƒ«¼ˆ# ‹‡ëŒÀ 
Ñ 8‹
 ˆ Ž‹7„Ÿ¹¥cÖˆ;¹Ñx½ƒÖ0€éÀ¹©Ó¹=Y;¬SM 
`à‡¹³ÖA@»+D²‹³³2´£:µã€Òºåó:yM‚z}SêÃ×¾
×r­t=u%GvÅ´ð‡ Ø°¸ÖªÒÖ»O§[Ø³3×´Û9e³‰¥®ðˆ€wˆ×Œ„%8Ø*É;	aØ©Y~%Ù«“X€íðÀX€¸V&€Ù(‘Ù|í;
±	 çÌ½ûP
SâªÎßtR?¥B_D‘”=Ä<ÉVÎ{
Ï
 0IÑçì¡Ñ£OÞtT;©Ú>ý@EÅø«ÔîtÅ*]ÜûÝã=ßËD"e'â{•XAR1
UåC«…Û?ÍÚ@ÝÚöQÕ‚¬C•Sü
K(r	 sŸ …ÑÝ·]¢¸Ô¹ÔuDÎ-ÜÊqª\\•Á¢¼@þÌÓÏ%ÆH½ÚhÉ&T$ºµ	´F»åÂï8BËÝ¿
„Z<%L=]U\¬•FDÕÌBÔÑ8œ\¦ÞÖ-ÞE=ÊÜ­Ðä¥ÝÄÝÅuÞÆEÍÀ‘^ßuÓ»½^Ö=Û PÂíí\Ùe[ð
ÝnËN5])eŠÞÈžŒ\´^Þ¨Ó‹Îôô\ú½Pû…>S=M~DUEXbà· `;¼ÓØEÞÙUàåßæÅ]­5ßÇ^L­ÞådŠÆãHR•T¹MÇÝýB\È—jÈ™òÛ»*ÒŠÔM‚[[ÐœIEdK&]T¢¡6eMÖ!Ú˜G;5JùÕà.S>íàû…R ¦­`$Èõ%Šc,àO-Ü%ý¤)>SÛuËáTPÓÝÉô#àÅ[N5
nÄOFåaR?Ä]`5laât\¬$a­ÌTê‚ÇJâT&öb>àùíáf¤ÉÚeÞÛ5Íi|àHôã’MbÞºR.
Ò²$´ ðU>Ò0í^©Ý`)†Ôð­âŽd,I#.I$~ÐK6[Ô5'ŽZÃ­F>ežÔ5}áT¥d€Ê¼V])¡œe	­åãõ^RVá¦œ¨Šº¨ŒÚ(=Fc>>Ý5v¤6“B©òµ±´MvÑVæ(ÕFöàG>cJMc¢¨K`¶WXö´þ´Ü¾Œª©ª*µm´rÎe<]Æ-Ýj6®³¢Þx^+L6æ‰àÕ=ñäÍ‘ÎÖÏ¬ã ¼c*Þåi^ç€¶­“]žßUÝ9ô³ðë!Úì¬ü`hÜäLþD:n<ë¬è<â=aÅ:ÎkvPC=aklNÂChCFæq–Zê´cr<gTæeý…ÞléÚ-î›ðÔ®š(Ï@8ÏCNR›ˆÏ–cîý «nÏ×Íçª¶Où4ÞŸFˆý<`
öÔ©c2mi2vd3ÞÐ^fg‹à¥¦£7†Î/å[VÞ¶Fç·Î_Ý•kf£ël†¤»1Ñ±öõ±íŽ-c}VäG=Ì þç!Îh%l^-ƒM,
4ÆÄ!½avúÒ‡ÞáÈ–èäg—ögiaI¶³Žäl9h&K‡äd“öÒVÔŽèfí¾.ê‹ŽkÌ>âä´dy¦Ó»¦eØEd(–l¾v³2NÌSõå»Tú¨ëOkîc~nP
ãæëçñáòíA}g	6HœàMMÔ
‹_[osÖå—¶ì˜Fokö_5ÙöþcšxÕ ˆÕ™˜Õ‹C[õ›{Ë·}£«^íRÑ:m•VÓ(Ö„f#ˆe-9Ð°ˆ„}€n‚0H€70º<'€Yg
 q%1Z¤µ°¸¨o¸¿ð“{ñOÒðˆ?NˆŒf	âXiq#àñç»íÞ4
Ÿº7Ù
×ñÿðS
!< -`q+wq,or…}òŒr»˜rRÔðoV2 q;„ˆ†<èr±8Bà1‡ó%/sp•è88dØ†kXsšÈr}¥QÖ7ïVŸóŸ<
uÈ©*ôkX½Sr&t+ôé yl@ô™Pô‘Êð*÷óH÷q„ˆ*¨t€ N(€(ulàtÎðt@g(šMóQW€_ 50ó H«cõGßq,—ô:ZÏ‡‚  v0P„mõu õ:€‡q0vdWöž³ ¸1o‹‰ƒv‚ˆ+ õ€p qÏöm_÷7dvè pÓˆ‹W  18ö71÷ `vuwõg‡õwßZ€[ðx1Ðv’[÷O vA§ñ `‚M(x%Ù.ž  :P‚Ð•ß¥“€¥
 Àë¹ðh»vEˆÐ 
h‚ÈP†D‡ ù‹ßÖ¢ur
õ©€{P30øD¯‰Eï9…çVg—s‡'[C õ9€…~x€¥7ŒOrÿõvòw‡ TX§oGšˆù™Ššÿ9·
8°‡ày±p„t †@û5{xn:³º x‡@¸ƒµGóAH‚(Æ Bx|+Â‚Qwƒrø‚Èp€X0ü; üõöv4GzÀ ¨]HüUOwª¿r«×ò:GZƒIð„ H}]ý×ó}è÷Ž ÷q8„w`}ñ‰z+šznÏrQØE uGPx€á Þ_~àu v ~ã÷äÇ
åûØo~„‡lp÷€7˜î·þ|gønçxí ÷+@„xø~+Z9*ovÐX€vwõ"Á (.ü' î_<¸~ë ò;Òwô;8] ÿ}?UÇèÆŸüc~„Ä!õ€¸	À2Àø éÈÞ&|+(.Lhð Ö€’È€íhªÀòç›S)bÁŒ
P `àX)°êý¾å¶ßX ?`Ö„˜ü\ßòswLKôÀ°PR€? Lð ÁŒÇ³ß·s`
4Á'øôBø[vSüu@·P±”„À  3ØÁ ØƒIPØ;%Àœ 
DlPê¹AXÃA; ƒgÀ ø ¿ „}Hþ‘ÁƒC	aLu…°õõ¿…‡á`€hNÿ€š‚ð€&œ„aPŽÁÒ·ýÆÁ&$„kð¶¹V'
«`ˆth;  fa+äƒ¯Ðš¾ƒÚÁ „‚žP"B]xõÂÁ8 ƒa  ‰a§{ƒ°P‚»G°9aÛ²…Ïö¾?—ã`s" '.2Ð‚~0@8Ü†½®ÃoxúÔ8¬…À&<hxwá& ƒôà
ƒ°ë!:Üxe/ø•A|À›a9´±*B~@ A%0 ".Dì‡•`Ò3î€ªAx
](ˆÒPs¡¸9@ (‰ ñ^ÂXîZÀ;8‰"JÁsH¥!5 ƒD€	Ø°mbÄ‰ùðàìxÐÉaJ4‡¸Ðÿù¾]X4â°  *.EcØGbLQq2ØèúÂUL‡£P1œ?Q@ Á€%¸  ,‚Å#è
ÇâÁÙf±¦ÅØeA §€Þm€@_Ä‹•P/þÁ28È8‹§"êÄ øwá: ƒ¶` ‚ ‘Àä†LÑ!Ö¿2x ¨ŒàO%®E–x¥a1 ƒö€ • i´‡¦‘>Äƒc	:k # %¦E
`ÙB€ƒ‘l €âZÁ8	@8îFJ¸'ÎcWd@å€ë8#AÕq†=GgY¾:wø ˆÓ‘XÇáÈc–Ñ‹+'<
€ñØN.ãºÚY¼¡g}õØÇA#€Ø±c§•!ãÁ)„`	Ì@ãè©b;JŽ™ÑÏ1GÃPƒ /ˆŽ‘Áô  äJCB îˆXÃw‘â‘<RD¶#÷np0R,|È "¤|,zg.®œ‰_ä„Œ‘Y =„  ldXø$@G’H¢× dœ‚D†£®@&Ð£ øSŽmQ:8
> W’	”È›x1á¨ëœ KþÅ×r¡`4‚ÐAm@¸A h“g2;¶ÅY@.@o2Bj%C_P¨V16jÇ) sA‡
*`p€@(ý¤+49'2P ÖP€¦t‘2ê‡œUùæBˆ\`IKy2e¡ä‘R’>BÆ•C*…¤©D¨Ò<ªÊ "Ax•–ÌÊMY¨<V
\9Ø P–Ê©x*+™Í£X2¼Jð\@p–É2ð-Ë‡Ø,Ÿå®Œ–½rZÊÈjIb@À–/àpKZ%ñÎ­¬’‚’œƒ7y¬6Éxyz¡K>+ G0N@ Õ*,L‚ °À½<“#§šœg•r^Nih92ÿé‡˜ó­kØ.¯a  ƒ7ù°Ø°L— (  E	#Àu ³VÎK¹i‚Ç™3Pd8j‰²†:`Y ,GÐ2½åè‹+hf€M€/m&é(’
’¸pÐ3‰ =x iÍù3fÈºšK“‘L	]ÊAÏœô XM|ù2ïU‚´0G³kÞ¬Ôe²úã[ {aÀdÈ 
èÁ4@›X³G²M™É5•&ÜdtçqUb—Gp7#C4  Õ oªÍï0%Û¦à™L“?É 
&âŒôà<Ny¹6ƒ]
ìK«,­¦¥·ž–|Kf@-Šá §„ ’ßJÑÌ¨¶ãæÊú›0[\6ï–ZÒV~Jm§¾‘·É`Û*ãZê«°!ª‡¥öÖ§è=¢Â†–‰¸ŒáÒkâ
v:³¨Í¨Rq;jØ-Õ" f†(s"fMEüd®Íå7ž9“n´+v„Ù™jçø„iÔL¦í7‚¡Åž'å’e÷Æu°ð¶ÖÜVk+oìy™O„Ð¿¨óÜl6c±PíÕÕˆ'p£Z3
y¦³tÞ˜'Ë@_ü-¹4#tAÛÆÍkhIµÝ :Ü,Zz ‚&HPŽFAi[
mŸî
~iPÈÆA…ZDŸ´S|J³þ‰Ñþ'ÿ*¡ôkîP 3ŠYyº`>mÎí5ûYDõç½Ù©!”šFÐn„*(¸ª‰7µö½DÌý ù!ìO$Šßü§~Sc&Ô¦ÕVÅ‚Û“lÔ8ÚEËµcåæ¶³Üv=Í'Sžé½ÐEÆG‰Ú•£J½1o¢C•ÛxYbx-­uO2ÅÌüè3›Jqôµ‰Ðdyh5FÔ¹yµGºGÕbÕ¢o”‹’OÜ¹DC
äân3AŠâª8&Ö®¨÷üM£4|–RAšléD»F4J²_ŠK
YE£ T–²aL(2Å¥^‚ÒU
E³Œm£
 -¦$ÒÑ†=K[oãž.T–F“ú×®#Iãd¶mRÎ”ÌhO«¦î4ÍÒfv*þh ¤Ýt~SfªÔœéÂéOSTQÑ¬Ö¨ê§ƒTÚt‹rÓ$jÜtif{géS ­O¦°Í€=àÞL“¡Ó
KsJ¥nnÍº]±TÚÜY-a(!õP§•üÒ=gÃ”LM¸lZKé-Í¨åÓ†bVSÇ(€ LýéG¥	…v*v[„Ki	ôš²µ–ê×^ª*‹©ÆÉiR©…ZÁ<[Hó$#Í6i¦“æÐªj%eX• U7ü5Os'ùª÷Ô¦ö¨Á³O©),N¯³˜
U‚JT+©F¥£u«'y­<ÊÔ6xzj &ª	SªÖXÛKXc?m«*¦.ëãh¡½¡¬ù—2ê¾Î(_E`ÕñXT[ŠQ «Q«²F³‰SèéPãDŸ£Ì¯Š&Ô:TUë)hu°©·^ú[‰Jl2E5T.[D]i”åÖÀº[Í[o-¬•¦¢9ØJ@ûSøl'µ†¥ÓEºNA™#PQL”Ö‚ZTsiuEnwTkÔV¸k&ÓRá¼J$ñÚH!šJÍG’º¦×Õº^Gè«¬ÔJu7è¢WÑšµªmõ¯
TRj;
ª2E¨A»ÈÈ”XíZ‚­ü”´&2H:ÙòÄ¬¼õ²ùÖ^qa•å{ýoõ½I
YÙê>Ã­èµÄN×Ë^Ùu¥.¼ó‰8vãnà
‚£7
®m0¸]åà„c3úõ·½‰
 ? ÅXØd,˜²0áLE 1 !ÐœÀHQ`À5`ê èsÀÄ8ÐÄÀ q Ð30 î@žít`”°ÄA 	Ó”(,€X'ÜÏ	 †Ö& Z‚P-£u´5Ò
Ë`°heB¥•X@*`gp(‚E 2N`Ç¨–0ÁËá€-Ë¸Ùæææ:$! P 0Ê@ J € &Àë²&à\A¬Í!?½#°DØr
N&Pø°]”Dd Qm.g _8i µgÀ&      2   
   ]&m¼Mó>'ªøÞªÈàAç x~Ngn¿Hú–Wƒ‘‘ ŠÒm6ÖKÖß,      U              D   D   ÿÎˆñ0   C:\Mes Projets\repo\avsp_server\src þí˜á                       
        H0Ð Á‚    #     $$0ÐàA„	
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_AcquisitionSignal.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ±57l'A¿Ó©ÚÍ¯Í1:®o©Qw¹‘Ÿp
:ù™(Ï     "      >
      M o c k _ A c q u i s i t i o n S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                  9                €    ¼   01F280051n     x       —      >        
                                                                       ÿÎˆñ#   Mock_AcquisitionSignal þí˜á                                /Æa<&®Žjóÿ£§Ã6låÙ»øùÒñôÜt3Yäë$)8FÇƒó‡yñq0Öb’Â    ˆ  «     
d 'Ä@N’^AàÀbÓ…‘b 
*8`1ÀÆˆ" ø,‘¼À!ÃŒ2rì¨¡£¦Ž8o¨¨AóFL›fèÔ	aç™DÚˆ¡CÇL€î2‰óXu I“òPDTÆÌ›H•À”IÓ&N<}¶*•ªUXOÆàZÇ8vê JÔhˆ:bÆ´1#ÆNˆ2HÉ¤CìÕQ:m§Â[ò$ ®oÆôÍÍ¯’ßR¾jY^„ˆgÔ¼Mz¤kØ±e{ü$ÈÙk_äÛ*ê"I
@ÜxHä¤$÷(ƒ«oè²‰G÷X±¢}¶µã.þ:6êÑwÔN}üíðwÎîòàVTÞ¾ÅéVë<Ñ´…ˆŽ°â	"ªPŠ'DcàžÙú»ïA£ËÄ‡À ƒ;R°£Â
ÿÀ4 ,Šj*€J4±DØàc¯¹ç"tÑ"Ì2MƒˆˆÈi¨9ì c/;æPÐ‚…!‡. .ÆéÃháÀ³h?æä’ç„ˆâ˜ƒ%bT)£G¤l|Gyœ#ZƒÍÉ3_ŒM %"2 º ÊˆHÎøªJ ¢@ú$A"NØˆƒ@·òÈ3¯P£[/¿ªŽ«“2
`¾  T37I/ÕtÓl†à0ÑˆRáSAÑ­OLÑ½ˆ(µtSû:ð/«"*¢¯w4£GûR’Xˆ|ˆ8Y•#Ašôh¢(M3¡Ê+ÉÈrË9ºL]{$ÓL×ÐìV¸ØtN:ãdu ; ÊsÏˆúdP×%´7Pc;¯Ðô—¹LÛ=wÒÁ
ôÁŽÞ4Fã™¥M.„†~cë¨£¨Ø£7í+5«#QœTM\ÕØV8<M«ÀV3ÜcŽjŒÂÉxÆÀ ‚IŠ$.RÀÈŠ:²ÈgüF2mˆˆ`€!„)Ì(#KÕÞ8+'ÄZ*7²¬#
8
A	¥Ô0ã3*XbÃŒ:^¸Õe˜Ÿúø;ÓZˆ(Š)  ‚EŠ°b
#Bð{í—«%s‡1à€£,Çæ–4o7„ˆv ¼í»òÚ«ì–ÐÞöí3MÛ "¾Ÿ¸
-Œ˜"„+æCÅ)küä@Û(cíÊ-WÒtX×Ð Ü}Þöt {{ƒ®xÞóý·èG]1âÞÿuö}©·>¢lÙe‡)KD‹O Ê|
ë¥,í¹`ã“:èãQtçª¦¿º”³¢ "!ÚÞKA'8	jÈ@(¡3‘Ìg˜BÐzæâHII[ZÓž¦Š¨Mí
U
ÁÕ²¦Š­uM,a[0w¶´õ¯Zÿã\·LÃ‚¹ÕínyÛ[ßü¦B´™ap…;\âXÓBÇee‘Ãá^*§3œPse¢ë< :#ˆŽt¦CêX×D¸¼.‹÷‹Ý¸,RÛý+w»ãS¡þ40áÍ‹wæs
òØ(}ïyüZ^“T2%q‚'
Ÿý˜‰4êC;‰â7²;Ä~~ŒMþNB£ñrfC›‚¤p±QÀA° B¿ˆ,ð
NÊÝ@ˆÖ¬¬ ‚LsÔV£AzPk\óØÚ 6²Q2m’L¢.[—EÓ¤ †vÃ›Þøæ·ðR‰uØœ[¾#)Qi nàQœ€Ä<$ÁmˆÃWR°7`“‰Îì\V&Í°À ÎÁ¢U¤$ÌØ/H|Ö`9Ž`1«;ÊX ™qN&MÏddõ`šs¢KmhÃ>v± \Â;1M"Ò1„à/Sƒ`ÓA–ü)(p
Bðô%q`JQnrK:$î‹“” áÅÚÍIŒxÒï|·"*Ìê$ˆÈ
Dc¢Ä¨ðËJR°TTeex˜
ŽÚÔˆÐˆò"¢ÂãÆ Å‘2Î#+IVPZd‘Œ„^AÝJ=öÉÃ}G=äü¤g²·ZÅ‘ò€d ŒðÕfv8ÃBó1èÂI€˜Â0&0ŒˆÂHvÌÏ±ˆšr¥ÉJViAWJí&Tóš,AHK±e&5mZzò“ü5°flaKNfe
Ãœ¡1m([5–°†Óû5Õš¥µ;ym^@…:ÌEB„ð’˜¬Vƒ
ã y?â 4"	XhCEQ‰žõ YÙÁE3ºQ°xÔj!ý(
Œ{“¢T¥,¥ƒKƒK™Š¦Vêµ)N/FõŽ±§eì
q‚*#¬*õªC-ê„åáT¨’Hª©ª…ªUÜ®hª`=½ÂZÇæ™RwÍcAÛjŸHðÁåH¢PçõEq+üêªWJÇ+†ð#Y6ÛÚWA˜pÒ%¤5dDv²ŸdÕ;áÀ¡m6 Ròì@”ÆÊ
f°µ±Ìè CXËä²-Ì]‹ÚÌ€äáÞV ¹=‰x[ÌþíÍÁ¥mœÇ¤‚ì ¹ÙUîšÕ[èJ—ºÖÅnYÔœ“_ºš¦éADî[ÍA;ZƒlFtt§[Ýë¦Yƒ‘®Š”úZ­7ÜzøJîKê¸98²
 í¬Óó”]TnpD€7ÖˆŒØ«%^ã‰ÕË¼9¦øØ”•^õøbYGÛ7<.$È~LäD¬ÅÒæõzOâˆTauÝ³pmë#ÄŠÀI&@9PŠAŒbÊÑS FN€Ù "[JQøl+1øÊ1—¶Ì³a¦µ»ÜC;WÜÁ%œÏ
k‹H©w¦á1C°pr;œ¸d
´g@ƒî»nÒYqADP­jVÏ!ÐÿÊ
^ 2¡G¨&ƒÛBn=qÍŽ\¸Î¶‚wMoÞÑ Â61±Ñs±†KÅÐK+…ìVhû1ƒØ^÷à’.ô1YÏq2¸nõ²}ÕŽ@)©µr»*}´^&„Àv«$Â}ÌD ú˜…¹ç.y8‚ Må1T]û=A~•Ž‰“ÐlCGR+ë6´Ké        (àA„       2   
   
B™0gúw !o…OÐpzUISOºN‡‚Þ6ÒÄAi©¹*“I¦ù
pùú                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_ClientSocket.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ÏÌ¨³»ëW,QÌzÙK‰¥S•5£‘5’ú¸d’”Ñ     "      ¨       M o c k _ C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                  9                €    ¼   01F280051n     s             >        
                                                                       ÿÎˆñ   Mock_ClientSocket þí˜á                                /¼ <~‘-^L¡Œ™þ{´±Cƒâßýâï´X¼\‰4ä¶õ
—àyõ 2ù    ÷  œ    
dÆÂ@0ð%Pâ®Ò…‘b 

*8`1ÀÆˆ" ø,‘<À!Ã‘6jÌ¼©C…%3uB˜¡£ÓÎ3!`Š¡CÇL€î2‰óØt I“òFDTÆÌ5u‚*)“¦Í–9‘*eêÔ#Ô“1¨Ö1BŽ­;{†ø¢Ž˜1mÌˆ±¢LP2iÄ{”ÎØ¥fÏ–<Éê›1ruÎ¬#sâ²ŠŸ2–!â5o0kIÚôiÔ?D	2uÄÕe¿vêy HÍ¸èæÒ·æ¿=Ê JÛ8jÝÇ=V¬hAkè®w—>M@ùñØ¡+ÏÞúºEÛÓs
7ü¶Sâ|go1¹ÓŽÏDä|pà+OˆTQå	fîIm¾ö
40¢,ØX2¤ó(ƒ8lp \àp£t,dp€ àÌ‹’Z
 K$Ñ4óÄ®¸[´È±8Ó ""àx£§9ì C+;.QüØ(¡|rèÝ`TQ=ŒÁÍ:‹Öso$ÎNˆ(Ž9X"F•2zªÆëÈqG3zLA…ÑLƒRMO@‰ˆX.€2"ªó¼¦ˆ(~)Et“À4Ý €m;í¸CÔÁãÂ{¯©ÞðT,ÅGH/ )Û¤­Òö"á5uŒH ·	È–V4IÕ¬PG-õÔ;ü0D²L4Eò"º4SM
ì€¾¨2ˆ¨žÄÔ‘GÉb !aÐÇÈ‡tVR‰0äI&-yL¸2K2¶ìrŽ ÅqÙ2ç83MÒÖŒ7ØÜàHN‹*º“Î^ Òs >ýŒPá%­ÐCgcµîýÎÑƒ…ût JÑc‘^ä
ÔSÏ<z5µµ‹½cµtbÓB®ÈÖ¨@ŒèG]Kä[_/Æ˜½9«àX3ÜctŒÊ„ö¡É{
Õ+6y P§Ü,ª!"‚†¦0£Œ-A{#+5lô+¨6ÄÁ-ëHŽ¿Bà*&ÊÀÂ©Žõè£~¬Ž³"Šb
(ˆ F‘"¬˜Âˆ·ûgtÏÜa8àhcË™àÕLÞË9#!¢Ç»­·´’;§wõV“³
":ü‰+ ÐÂˆ)B¸b1Ô°\1ÌoFÎÞ ð…nß}+ý7€€
€`I
o C jx6ãž7þá~=ŠTÅëQ®¸©_uwÓ{Ô²ãBÍÒ`„|SÏßp Ôÿü¦X>Éå`Ž ™—¬™Iø•Ë9*
DDxÓ
€f@4ðãhºÙ^D–†ƒøjÖ£RT„`5¬ikªðØÄÖ¶²-mkkÛÛ¼R“›ääDWM/Î°Ào€á‡¸Ä±0'fpä$G9Ñ¼0sQ™Ê@v C­€.f]JG–½E%uY]ë^»ÙÕîvfÉ]ÿÜÃ;	j xÕ^ñþ„(A*"Í
€ôv7bËÃžÄHÓ@‹tÏ‹NAÙw*Ò`À
Ç±Žuä*Zy$³hÕûÎ CŠÊT¨z#î ‰)¤x¨e¸ÑýòW)=îñ5ÿ;ÉŒ¢DÑ¥° ºAƒ~,0i4[ 5 Rpj'A³¶µ®…Æƒo[Bˆ6U¨m[éJÜTùT2Q•[t
¸R0ÃÀ
®p‡K\¢ÙÄ'&†MQ<‰•âÉ„À	KÌCÌÐ†8\%;p;Ñ$DÜq†T
|@|r‘3×[Ü²Ä®!t<A`j ”° 2÷„â8E©Ýè3"	BÚ §¹r — ŽG8“ƒˆ4Álwø™ð¢c²D0EP‚7  Y!ˆQèR™˜Ðrb¥Àˆ8ÍiŒ4ó×žúd<ä©ˆ
Ä:É"²ÌpÆªÁ*Ì¢²Õ t5WQaãhÕ¯FD-E]cÂŒWIÒÄ±=sÄ¨%•½;V/”e*_ý
¾\xáBò“ ý²êÉ¼öu¯¥MpÁ ²qäk<€Ž…ß9B&oõ²\!8zµc¥€}êO9•ÚÅªç”áYÄD        (àA„       2   
   ë¬ã&»	ˆñ~9ªÈìÔYç¹É}•»|SY¼Ïé¹_KÚ CŠB¿àlÑXY¢                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_SignalDessine.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Ü>_5Üa
ºs›dÐÅšªUVn°¡À°ù«ýyð(     "      
      M o c k _ S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          	                  9                €    ¼   01F280051n     t       _
      >        
                                                                       ÿÎˆñ   Mock_SignalDessine þí˜á                                /3%~5§ƒÇ*ë0šM×ai6ýì‘HuW“”£?r™ ³Ž>!#úôË Ž=    P
  W)    
dÀAÅÀ$†ÖH`óbÓ…‘b 
,`1@E‹  ø,‘¼À!Ã5hÞˆiSÄ:jÞ˜	q³N;;Ci#§™ ÿÜeçÑéÈ’'GDLcfŽš:<•À”IÓ&NH•2}ê”¤Iy"žÑ™tiÓ²	Ä¥[×®ÇA~¼[6ïEÀ}ãª(2®aÄ
—m ø©Œˆ
O¦`±àŠ-èÓÛyoáˆsïŠ®\ú/ßÀ•Oïý\z aËuÓmÚ)ä’]ï~zÙ)ƒµ£Ê³ñˆ•'Dª(òÄ-S÷Ÿá7Ï’`È´ö˜Jöís|×ÎÝn&ìå«Çý a¸‹cÅ¤_Ÿ¾ÝÚ±oG^ß¿. áÐÒ ""àxƒŽ:æ°ƒŒ¬ì˜ã9qP¤„Ø`È¡
Ð6þ,D$ÒróÈ°³N:!¢8æ`‰UÊp§LpÁçHá ùFë+Dÿì@‰ˆð¨¢2"*’C‹ˆ(~Ú+¿½“ÀÇˆ ˆhµÎ\Ã2µÝ`ómDÇ|*¿/q
@·]û²2ð@JEŠ6 Ê Š8ãÊ]6	/Í»òÁSOó"J…ÎòÚ{/¾·ì³¿0=òƒ?1ZS°á`ëˆ 4ŒÌÇ Õï0>ë À“2ˆè«4ÃAH†BCÚ¸ð!ÃFS 
Ñ£‰-QPT‘]œƒ'TTÕÁ!ÔñÙPéú1È!8ÒÚF“\²IÔ€²Ñ)e«òÊ,»U­Ü@)óR\'ˆÓýDŒ–2wãÚs b (ˆò½WÚ»|ËåWNø ñæ}jÑ¤ÜC
¾ˆäS´>F#
àQy?-«^»*èÒLåÛ4ÛˆÌD³^ŽçÍo8ˆŠBŒ9ÄpÃŒÅz‹qD3E˜:$àÃ‘ÅD £M@êÈ¢¡C
m8#"‚†¦0£Ùz+5¡žŠ
Á
ëH®CØ*¦™jº)§^x9æ™kvÖ®_Sry
(ˆ F‘"¬˜ÂˆBx[fšMŒ4dŽC	7Ä@Ãß‚¶.Vz á&ˆ(!ÚhCIùŽà’1GKˆˆš#„;`VCŒ1Úèš%šxBqÅwÜBxŸBˆÃ( êP£
5è`ÑŒ”ä+Îb¡Ö"",= % bÒÉˆ¾å
Råy ¢ nüÊ—-ôP?Q´¬hÌg?¢ææPþ ¶4—2ÿ¥k2ëX»®gxyÄd–LÊø@–ÅPCS&vŸº$Ð";Óõf³
dšêÞõ½>pehÁ©ÌÐ¸9Kdƒ’‡5µª
¢áC2ì ? ­„BÃˆŒæ‘¤ùj8ÎÈÓ¢6µªé‡oØZ×V 6UˆlZáJÚ¾Â6æÙ†2¤¡
sb¼æˆnÃÉAD¢€7½ñÍo€
aFÆÞo¸Qv‚1ÀmXÞôG—_m "ÇzÃà  «°.fj8d\*ÁÂ i B¢¶r­/e/ Ûë–÷>®€Ü
+×C@PÐb4¡§0Ù›[æR—ç ƒ&ê§€	žb‘Ø+¦±ˆpMæù kB€ŠlˆÈÈ	ÿ'K]®ð$§Bif†æñŒY3¤|°ÑˆwÀ„ø.’
Í8"ÒÀ´8ÁiP“ÕTaµ)V1^Ã¢Ëv¶®¨
,mëæ±²N3N±’eùÜÇ½õíokè7!ªGröñd!'ú”DFäoO¸´`„)„à
“<©YtM€“¹(Ÿ"JR>©\R¢Ò@V‰®V"õ•)Äf3ÇTËŒÝ”©w‰„o&!Jˆ¤"±„H.!Lˆ$"Ñ„H6!Nˆ¤"ñ„H>!Pˆ$"…HF!Rˆ¤"1…HN!Tˆ$"Q…HV!€`Êc˜æ3f¯§Ìr]Žš”yf4ß´ÉZ“™SU˜6åá.ÔEÃt÷¸Véàhµ¸$ˆwz+ž™G=#’Dýª	û|¢?ºG4lc;h½²¶°¼à‹a!­i‡Z5Rn8†¼[Þ2:ÇÀ-w'Í¥ÃiÍ3´¡'2ÛGÇi ã¶áTHŠ „*æ
hô07¨¡†ó# IHU˜¡œ’£ÛŽ†£È¬´¥/éLÅ@ÉèFÐ0™Û\ç> !Ñ‘ÎbÃÑAêV×º«ÀNv¥Ýˆ×†Þý.xÃ£CñŽ—¼å5ï-¶šlô¦ÇSëY¨Üj`>Ó¤}ï³àýÒg?!“Éòàý–Ü¾üEØ0G
Œ ëâÊêÀrcrªS4X2[Ju²R
Å„…=Í`ª°±KÞ±/ì)h…Á†½ºôê4Ät˜0+8ŸÆÒæ±žÚì,)…KaŠ„ð´˜fqihzÖT±ÉìP í¶j¢YÖ!‚hÜ@šæeäI‘ˆn ¸ýÔ¯ÀÛ~FñjÀ-›p³H\.¢í¸¢U®,iä˜,kDË‰ªGÒ±Ò—Ön³"lÉ‰Ù29ÝéEzz½sÑÝ"*»TI®*'5ÜK…ô§>X¯/W3ÌRu`\Zà›ˆ"‰Hd ’ˆ„"©Hl ’ˆ "ÉHt ’ˆ„ "éH| ’ˆ"	‚H„ ’!ˆ„")‚HŒ ’# ¶ÏŠýórÌ/A…åî¢?¦hin»LžúR—#èÙ
€
b°ÃÌàÝ%ÄÁhh•
Žf:taCè)H-s$bä¶]õ=ð«%ÀŠÿ”"­­øµánÑléÕ.óvÞóŸ?×AzM=ò«6\ÔºrÜhÎîs 

àoð”@ß=¦w½í}o|ç[FjÍ¾øÕ¯ø;ÒÿšôÙ50Z F:’säƒÝ>¯	£Esáœç@÷–_ÃfëD<;2Ô.'N1ðÈËb#Oy–1S._sém²Z×Öñ¶´-[GÈ8Ÿ’×—ä#?_|Î‡ßIš\?éCùSw·¬L,óFË6÷TÓ kJ»ÝeÁ ´ÝÏž‘/¶˜CùST~Íòó¦²0¿l©;•±B3
ŸË‹‚cžcÉ4sjÑ €° xX‚
À,SÃˆC º `5Žœr¢XãºY#§àº¢°+.]S(0r›„™Ñ
¶Ï[´¹C6ìœLÀ`ã»ñú‰¿C¼ózÁc¯9p/ø’/À#ÁÅ“™Æƒœ‰hË¥Ð;‰ÑÃ
;=¦H½
C‹Õi½‹Ø“=ÚóÛû	Ü»´Û=æj¾›¬l+¥¡ŠTŠ¥oˆð ¥Ê²©:7óƒªz¿õ{Š4ð5	6‰6	7‰7	8‰8	9‰9	:‰:	;‰;	<‰<	=‰=	>‰>	?‰?	@‰@	]ˆI,ùK”@‹‹óëš#¿CtŠáà'ˆ‚EC¾ÌFIá,•	Ÿ–	 !8¶Àèº
0ª’@
´À¨³ pHµ¨“¬_iš&â§­û-%üº‚Âµ±KÁ²{j¼ÆÄÉÆa“.´(¼®º»G5ÀÆïŠœ}D¤(d·8ì$â£Cã³Ãäˆ<Ü2£ê>Ö7ïëñ{Æ¦r¬F[·›*Æ÷É¾àÅ’ûEÆ¸aÌ˜šƒFº8F•PÆ˜cÆ™cIb$ÉPÉ%¨“²È‡&¨“d` üIõˆ‹,ØÉ¢,Éø;IúóÈÉúO™HÒ‹àÐ?´¨€˜t€°,¦è±fÀ§¸È9?kâ‹8 TÃ¨B‘äHÀJ¨»
Q²û        (àA„       2   
   Ý¦í<Ís¾§4ïúô¾¶þoá
B»³éÒß61O§a5ð:ÚîeÔR”í¶VËV_¬                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_SignalEnregistrement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 M¯»ÎQˆ4?©û\Ð‡ÛÉ5KìÐ2*LS­R(•     "      ¦      M o c k _ S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                  9                €    ¼   01F280051n     {       ü      >        
                                                                       ÿÎˆñ&   Mock_SignalEnregistrement þí˜á                                /»ZS‰?¼åÔRT_\Ôt>×'Âh²Œ¬„Xàþö÷Ô©a†úévWuòÒÉ    í  e    
d "ÃÀ\‰zH`ñbÓ…‘b 
›,`1@E‹  ø,‘<À!Ã5hÞˆicäÍ3hÔÐ©ƒÓ™7uB˜áÂÎ3!ˆ´C‡Ž™ ÿÜeçÑêÈ’'UDLcfŽš:I•À”IÓ&N<}­ujÕ«Iš”!â5oÜR+·ï_À=~ˆò£à¸„/.FÜ×î@‘}#OŽ9nƒÊWeDTÐØóç –W¬hA_aÔ†!G$€¸5hØfz¶aÕ±<
˜ò_Ì«­nØY÷q¢­"¸1«<
XyB¤Š(OöŠcp¯ñÀdr
ÎQdñÈag1~¼ÅL#Î§F¾û¸ÿ <w`Q*U€0ÀÀ‚ëm8ÎÔS0œ£Kƒˆˆ€ã
µì #,;æØŽ?@JÈ †º ²…KÐ‚ADz-ÁX{î„ˆâ˜ƒ%bT)#Ã¤$¤°'
1œ#…­þƒ
°‰\Ð3”ˆÈ **#¢'M´(ˆéG Ã4,2	‹‚ˆnC-61kÓ7å’ËLÊ«L“¸ Œ;2¶4çŒ- %æ{/¢|šÐs dXø3€düÄ¯¯,ò<1ýøóï­ ,pMà”ÓÎ9;pð¤ƒ*‚(/4#Ã
ûð0‘B|(2MÙÈATôh"Jç:É„g$£Æq
áÓ
EÍÈí+ò¯c1½*É%› Êg)²Ê+i
@KJ»ô-"0 *ÓZÛÆ÷Li—¥´ÎˆÜÔŒEe‘„¾mw^;¥«¿ˆ„ŒTÀIÙÈRz4ð9"úÔ
1æÀI2ä°c'°Ô˜pC^#@1$hBÄÈÔµˆ9
: ©£ÕÄŠ.+"‚†¦0£Œñz#â	C(#)¦Bp£Æ:Ò€ãæÆŠi¦šnÊi§žÌø)¨Nxá†¦ƒf½„4ö¹"Šb
(ˆ F‘"¬˜Âˆ~Uúi3vâ:$~Èva8àh£F Š
,Ù¸l•gƒˆpzã8ôð*„;æC
½Ž‹Ù˜´ÈI(ËˆÊ¬Ä2]q¹ô’Û0ÅÝS°oG÷ÍmMü­/+e×ñ¿Ðms ‚·±ÖZÛ¨µ:dß; ÝAA(žI{OÂw }÷¨ß]/vä¦k6!6#,µ¥¦zC
^ë2$p‚cÎÙyƒ‘=b®Å“OªBe–]†Y™©¶g1tæÙg 	­,EC
ÒÖÂ´ìá„{Qc›Û‡¬çà@k\óØÄF¶$p{icàÔÚ6!¸ÉnvÃ[ÕÞr5º n c{Â  #L!WPã¬ö;ê-KI‘s´ •&Ì@sÖÂ–‰´;ÐyKtº)‚P‡DÕYÎ#­³Àr(™+F$K×UÒ …Aå Œ‹jL&¸HÆ9%OËÛŽóž *FÄŠYüŒõN‘D!abøIÒ6Ô‰×ÔÃe Ð78‚Lí+ü€÷œ&Ð¯e/‹Y^ô ´œíL=û™XÈB´³M-JcË
ô¨¸>âä}ñÛ
&Øµ¯…mle;%·‡“¼n8‚È5x%H'Äa1s™çL "	BÚ@%!‘, —âs„‘&ôï	SƒÆÐœ±„&IA.yéK`z!x
@‚84Å(mkÃNðæJ!EÇ_¡aD³&­üð*Ab–:*lJˆÈ
ÚH†Æë¡'‰h ª/ºt+ 3 ÁD‘<ðK¡cŒé ÓDõ<‹QÜ§Eâø¯×ÑQŠžI	,ÒE«€ã i ð+j@lÌ þ2 ŠÆ¥w¶É€4—Žt¤"jdcóö½7ÅT¦Ð{Ž "b2|¥E…zùË`jHH¨Çk0	Há×*ÈôÁÈˆ¼¯VÏIè@V6ÉûåïƒoØ_2ù¿Ní“f1ZZ’¶´:¼ ¬b%«YÑ™ÖUöí9zÉ#cyÁ²M,•¥ÃYÓù#3´a(EQB÷8¡Å¶áTè	Š „*®õ Ûl¦‡™¸A
3ƒ
â6·ºÝMy»!‘ž£Â °Ð…0”!
—Ù«ð-`È¤‹2/×ÌgnGšÔÜçst€MmrÓ›à4¬8ÙëÎ6¬³ïŒ'æ©†zÒážÛiUôBÒOþÓ‡R$hµÊ˜Èô5 Ï©èE!Ñ†z´ÁÕ(G%R‘&Q6K$‡‘£ÒÇk¦éó¯µ*Sß©')þKñŽ€þ2uA“`q_\ äíç^ó°º®émUÈÇÉDpê‘DÌaP™Ã ² d4†ª<‡Œ  9Äñn‰ét•
¸ÆA´È€        (àA„       2   
   (m"ùøÐGšW€Áa%¯°tsÓãfÂ;"~Ö2d¡ÉIYŠ3õi†+™á                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_SignalTraitement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ÖùdlArÕsµ«“\vØ
µõé£%ÉÎžeuW.í·     "      º       M o c k _ S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          	                  9                €    ¼   01F280051n     w             >        
                                                                       ÿÎˆñ"   Mock_SignalTraitement þí˜á                                /‡·è.…¼‘iÜ@TçR¡¸¶.@~Ìþ1A#¢ú×:/æá`ðÓdÍæo,˜&H¼           
dPîÂ@fÅVHÐçbÓÁ‘b 
”,`1@E‹  ø,‘<À!Ã5hÞˆiSeŽ5uÌ¸1ó¦N3t~Úyc&‘6bèÐ1àŸ»Lâ<NYòä‰ˆ
Ò˜™“Ó¨˜2iÚÄ©“§O§P¥RõHÒ¤¼Ï¨y“6*Û¶xõîåëñCDû²ý{Ñð`¼q ŠÄËØqDÆl@¦šr Ä™5 ˆŒ¸bE
ú ¼8"Á¨7¯PXðáÍ®—f@1ç½õN6=Õr Ìµ…{ì<ÜˆVåYˆxÄÊ"U”@ybWƒ{ˆÏçÞÝ{ß\^V€!3àå,>
Ðý{÷|yãöõû}’¿Õ‘œ7„šÃ2t²cëð¤„X`È¡
Ó¯7û,D$Õì
é4å°(Ž9X"F•24ê¿ ë°@3LAëúÊp¯ñkL‰ˆð¨¢2"ê‘B‹ˆ(~
l¾À“€/Æ ˆH¶ÑXƒ¶Ún+Ž8Ê€¤j¾+
ÎFÍ®sÌ òd¼òÈÄ+…@Ð4½·Ô‹ˆ=qàƒO¾,=òRMï:Øï¤"*"(	4A:èƒÁbZxð!ÆüÔR 
ÁÐ£‰òtë$"QDMaP
mqŽcä«F¼Xå“3 ÒÑ"}Ì3È!‹|- $ó\2·ˆœhÊ]c‹²Ø*o¥J7úðâ²2
_–;7 JEŠjÈ ŠlÙÊ]6ISZ‹òW\Ú<J…[òÞLo=µì|O- Ú“ÜÚæSAwcŽ9Ì‚9ìPƒŽœÔ 0Á	TûÅŸ$@àH#zÖ¢ã,À ¤Ž0Æ²ª·¤ˆB˜ÂŒ2Fœë„¡£’
ÁëH˜C +¦™jºÉ«³êxaP7þ
xà‚n¹.:eT‡ˆ¢˜
"ˆQ¤+¦0"„QýX`‚
F¸…ßx1„Æ€Ž6FìIU•Û ¢€ß¸=¸
áŽŸáÖËU|ˆ5€YGû1€Ã¯r "´øX%™öÉcÓEŒXË7³rÚf™•LY{£ÅwÙÑK7}ªóÞ3^y¢·Y‹î=1}ßrMˆ¯uBZì¥Œ@5+
°A(Žðq-3¶€cŽÛä“¢¹ä“SVeå¥_ŽYŒ™k¾9çÅò¹¬z÷€u[i²nzUånˆzêª¯ÎzëÐ7Cý¤Ç.ûì´­­mªxÛûâö–¹
DkO¸´`„)„à
}3àßf·˜Áî"‡KU€ÆíªWú•^šD¹Ãd®/˜ëÎæL˜'Ïm	tÝagÃÒe
Ý¢Jâ­\À![éb»R'xµ§u¯ë’èpè¬?É"ˆÂ¿ÄÀ5%QPÍ'p	,ÀxŒ¹XD2öh0 ÎÓ”r’0=“¡LetÉ^ÎdF3UØg_	KÏÈ4ó½ Š7ÁbÀüÖ*å¬`~T³Ö´ÆµA^qKA¬è:À¨‚­Jr$€*è!f¸Â¿*yÉL’-€3àr´#ÄÙª^ø`¹ˆ”°1“VåjÃBî¸p—b–˜wÃÐ]æ‰ÈÄO ü¤šk<€xä€Îœ§“Èi tjëöRÆØ9q™S©ÝI*àšÁmÐ¢ZÐÕ`8@ÆxÌÎdL
ÌóØó°)yxs $‹£õ°×¾7h/wôÞuÖÇ±üÌ,$B<çYÏ»Àï-MˆÔi¿GÅ£ôLÚ ¡èlwì+FÛð*¨EB„ †™úÏeeÐÃLÜ †‚™Ah
ÚØæ6¦©Åio	Ô@B9ÊR¢Ê¥š”ªHO’À ,°Œàq¢ÊÀe–œa-uuËÃDŽsÂj/¥d×á“-Åœ¡EÐ‘^Ð˜ƒ©f x¸N‹ä"á*ì}ŽÎ$Öi‰çœa`«—Ãz$zÈV8Û.k}¶±—
@6€à¦ÇÊCœÖ)'¡¥LÒRh}
UÞ9¿âåƒO@         (àA„       2   
   tVõ<cæk,%5{i;4^ý»©ZFæßÞ‚R¶à-}­&—Uªõ„íö                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\Mock_TreuilClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 òŒKeu·D¢ëf£a¾ã2®ZÌèQBy7æ¸- ñl     "             M o c k _ T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                  9                €    ¼   01F280051n     s       í      >        
                                                                       ÿÎˆñ   Mock_TreuilClient þí˜á                                /Åª!¯ÀÖæÁs^¿?}ÿœL$ë_…¢þ®E £²äÒîbô‡¯    Þ  ·    
dÀ§ÂÀj–\ !Ò…‘b 
Œ,`1@E‹  ø,‘<À!ÃL•9fì¨iC¤3oê„0Cg§7fBØC‡Ž™ ÿÜeçÑéÈ’'GDTÆÌ5u„*)“¦Mœ:“.múÔ#I“ò"D<£æÍX¦fÏÊ¥[×®ÇA~¼k6ïEÀ}å®(R®aÄ
›m ø©ª‚%O°XpÅŠôéå¼·pD}CS&à/ßÀ”Oïõ\: áÊuÓmüÙ)ä
\ïvjÙé¶£Ê³ñˆ•'Dª(ò®8÷Ÿ©K¦µÇ$VÎl²>0Ò‰`ºËM€gûøWÂ—¬[w‰ ) ¤ðéú !$…0ÜE¥˜Ê/?»j‹í¶ÈxSÐ.„KKƒˆˆ€ã
Ÿæ°ƒ­ì˜ã9ü )¡WrèÃ´-2
i´ƒ*­"Šc–ˆQ¥
…’Â:,ÄP¦9RPá¹»X¬ËÈï@‰ˆð¨¢2"ŠòD‹ˆ(~ÚËÀ½“À.Ãöh5ÎJ#35×`óÍ£Ù”ËÀ5q
@·$w[³Nò c½ë,J‡> ÊPñú¬+“=
¥,ƒB}êŸþÒú/¢ Å°¾]ôHN:ñô4€<)ƒˆŠè©Ç
3Ü°RúøÐXD|È°P©@ 
YÑ£‰4Eë$d¤‘qŒ)S+LH!‰´
I¹ ý´7& rÒ"(¥Ô´Ê+³D- .5ýR¶ˆÄ4­ÌoUC×ÐÉÔ$÷Ä;#‚ó±§,ÞÝ"Á×.èwŒH@¤ñr ¥MØ{ªßOHý¬KéËÔÖ8µO‡;h SÝcŽ˜8dc4|Ž‰…€pF4l^‹úµ ú™MÐ†""`a
3Ê°±­7²RcÂÊª
1BpÃÆ:Ò€Cé” +§:¨`‰
3êx!ä‘K®´ÈáZˆ(Š)  ‚EŠ°b
#Bl’Ír‡1à€£
sröHÑ†#!¢èŽÉ2à°C+­[êºÙ±Ÿnƒˆà~â
(´0bŠ®˜C5þ¦KÚ§Hßx²%›|2€)Yß6"+ ÂRKyÑõÌr#:3ÝÉvgW2wéjsxM÷…yÓzOWy‹5+›M‡GS‰^Q¹21Âyæ#ÊeûB#–tb+¾ØM‹4î~·ŽÓ¢ "!êÖŠC'FC¡YÇåÚm•y
 çf!ÉYZ„À3ŸMhª šÑF5¦9
jR£šÕn‚µÆqÍkñ‹Éü"w¤á°àli[[ÛÞ7¹q°kvSÞôÆ7Uøíƒ£ÎT²jqŠcÜÖ7¤Fkr•3Âå2·¹Î}.tA4‹éæ²¾{U+ ×âLëZ·¦Ø`vß
×‰Æ%¼Üi]®ñÝ‚‚w˜â¥c9^ú’ Eœ1{ëËDæè‘DÂQYÐ#ö$“
;†/R'™Ô@*U1€œ/No„c_Úw
$
#;J&”žph£‘„>0a Iì $êúü…P€¤#ôÕp€ÀŸmhnqà’¶´¦=MQ›ÚVº2“š\P'/˜ä*yI3d’‰¥NF¨6¶¹
nr+æ1ß€I:¤`v8O| „`Ü<ÃU€H–"‰¦‘nÀ’ !´¡
VªÔ*p‰×
d8;ˆHšv‡‘©AchÃÒX"†‚† Û<
Bðœ*q(
8ë@5Ð¡o/x©Ö( F¨[®ÈŸ’Å-nÉv¡‚¨äqŸ¬à9Ãi dzÈ´Ôô¦K‹¹f@ƒ™æ4"1]˜t7F3!•7g4
ñÖh‘6FD}ëŒ#Uû’#ÜÑ"y@£2ÐUì%¬
ƒØ åQÈ™š¯.QÍ#­êš‡€¬wq’“t‰ä€H"f°¹Z¤®s
 ^ãØºVü‘¥(¦VÚä3 S}kS#âNS–fM—dOú”ü        (àA„       2   
   ô‘ÖuŒ¼ãfë¬¥µûé»´œžZÚË\\uV¿¾‚Ò6`­ý-¦‘Õ*u—mv…                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\MutexHelper.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Ì ±Y¢ÉƒdË©Ö+´¢œžTr¹n}ðd)˜P–"     "      ä      M u t e x H e l p e r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     PCS          r                  9                €    ¼   01F280051n     m       H      >        
                                                                       ÿÎˆñ   MutexHelper þí˜á                                /4öŒH£Ur´0MÍ+v`±Îìó¿ÝE šs`Õcu<ÂîfLÅÿk™i>íZ    9  ¬    
d§ÁÀL+¦Ð!Ñ…‘b 
¶4`1@E‹  ø,‘<Ø©c&O3mâ˜™Â:!ì¼1‚H1tè˜	ðÏ]&q•Ž,yâ@qæ¨¹CL•Õ¢G“.õHÒ¤¼(xÇÍÊ–ylâA&5<³"åÚµ©¼ÏÄ»µîÈ¿öø!"È„—¾ØX1×¼EÖ\9¢å¿ %/•!öñçÇ“A_¬hAßaÔˆ7L`ôkÆ‰ƒŽXõë ‘ˆ^Š¹®æÝœ=ã&î‘·R×ÏD¼k!â+OˆTQåI_÷//ÞÝ;ñ<kV€!sÛc*7ãËÿƒp×E£HÎ§?_0ðã:CýÞ) æ¾Ò ""àx§9ì £%;æÀÎ’ä¢†!‡.˜ÀÕ<³À:²Hã"òê$"’
2¬*£Áž
<°Ž4£ÁÊ:‹%—ÖÊÉ-¸xR¡¯ÖQ"ÚÁF´rô!$qÌHÁ†$A%'‹°j–R8Ò¬$óø1>¿þJÎ¿×P""Q+#¢53´(ˆéG Ä€Cl2	›‚ˆjC
¶?e+N·üBºìÐ
?áø#s4E»«è€ @´È‹Ñ\s­#Hà¸fiE“òÄ`Ó :ýt R?Ó´Ò¢Úûê½ˆÀ¬¯¾ûõè—áN
,ÒÁîª ¢€øÁœˆ¢¯:uµh?XŒQ¥ µÔ×¿€•v <)ƒˆŠ¸	FtLˆˆp…j(|h²nÝ@ 
x© &ÒuHLœEYA\ËqŽ¾Ô*ÈÖî
Ê´ŽˆKË&ÓR˜®(·%ÌL4=ª¨Í @ÎÎä¤sÑ@ñÔ3"> òs¶Ï^”8BÕÕ7®¨õÈZC#N8_ð¨ÕÇ€NŒÒîŒî´S´ýËèŠØs>­n¥/Wy âÕÑ™{+NXbEVYfQÎZ¿^Í
±?Ÿíµg¸#qº.£ ŒíÑID"Z±ºcºÖª­ëà”Ú:Ú ÒäJíÇÀ¨Øc“]ÌfÏæf¥æ&¬íˆ.Ç–2¸×`gqËoØ8§{©ú        (àA„       2   
   /XÊçm¬UÝôÆ¬¸eÛJdJcÔ(#E{/þ,ìüWæ@¦ÛÄ@½¤-Þ                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\ServeurConnexion.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ëcv™¥w>rÎIÐkxÅçÄè»ËÅUÑg¹’Ý3!9Â     "            S e r v e u r C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          	                  9                €    ¼   01F280051n     r             >        
   
_                                                                ÿÎˆñ   ServeurConnexion þí˜á                                /…ýÍAc@\²5+‹gÌQsãMÿüÔçtÖyÝmt¦_ØW»ýÃ¤•m¼”>ç±O
      |^    
dðáË@òd1ÍÁ†‘b 
•´ ¢E	 |–H^‹ˆTÌÌ¹cÆÎ"pÞ¼1“GÌfèÔ	a‡f"mÄÐ¡c&À?w™Äud:pdIy,"*H³RM3!”ÄœYóæ› jÌ¼©C6fêMº´iÇ§&{LCFŽ5t®~Í¹3„»xõÖùÊ•¦Mœeô¼ãFÍ]£H•¾…KÒ$©uæ¸TÓ†N_žU6ãmcØkâÅÛ‰Ü–²Å¸'éÆ‰ú/mÓˆß„PÌØ1d¶“_;µ,ÏBÄ8sà`Ec¦µðá±—L%ÓF,ÙÏ:yúQGÌ˜6fÄØ	1”ÎY5bŸáu«õ±eÏ¦åÙ[5pÉn£çM8Æ#¼’S£¥çô{-¶¦rC‰7vã
2²Ò.•Xr	&™ãË¾ßX
NAÊb‹ ¢3ÔxcÄáœjñEc”±£"úÈ£-ªñ"sìèÄBzMÈ ˆ42¢#ƒDrI%›¤¬&;Zh |´rÈ+³´¨¢Š,ÐÇF0otr£ÉÔÆqì1Ç4oóJ ‹.É'£´hÊ ª<óL"ß‚ ÅˆŠ;n #¬x‚ˆ*”€â‰Xå ÏhŠH4…*ƒˆœ	B	)L@Hžr"ÿÀÔ£B>J€ÆŽˆŒM	ä”#ƒUÊx)«ÿ0«8ì oµ¬4}ƒSï<MA…vÀp¥–^Êí+[4s?¨JˆH¯Œ0Ù
}xÂŽ6Úø«	(¦ ¢ZlMªÁAd'¬0Ê+ÖæxVCi;<íeÙ]°8àýV^3^‚´2Î¯Ìââ<B	F¶ÆØ
h³7î€C•º×S÷4¹©6Uõ1å5á´sÒ(é”ùe¦ tòÎ©NÎ²O;*ØK• Ò8Êkìù5–Õ\Yå7­Œ“g¦è|Ëf9™ÂSÏ£g„:k®»öñŠsÀ¢ü 2ûl³[¤zë_ræèµ¦g,®‚ˆz â  p"Šo\»©«½~ío«Ý€áâ| 2Ä4q¦°Å9Èh| ´©~+„+*¿< ´ÇÆüì †C»ôÑÍþ3u€°FûO°Å&»-´ÑV›æ Ú¦2"È)S<Æ¹ë¾;ï½ûŽˆp)
_¼#äqÞýmß—oª?Ì¡Ò "®všÃ2°z©Q/DeHÌzˆHê«ÎÙkÈÄúy¢‹{¡Ö³pÕu³ç8×¬²×Œ{ïÍÁa®¹Ä B†ˆ4hhÎ^p…•ŒEIÒ b¼‚&þÛÞUR02ÈD{xáI†¢Å¡®tï+¢áLZÐ-;`%>0FÐ‚¸!†5Q
Jö–¢•¨8’Èêr—¼ì'õ:Âwô ™°ÄçDØŒ¦%!ØÁ„JAÇðDÖD‘,/aWt$æ+>´X»äa”"j¦…Ÿa¢‰%Æ:L±ŠW|C·ø’.ÚŠðc=$9’æ	›*C|ØH@7^!"F¤
½BàE0Z‘y¤¢søèGhq“wÜ$ýuFÚ<:ƒÊ
&ù8¸¡
iØŒÊ`ÉÔ!
4ZðH‡ü°)A”ÞÎh5ñ©YæÍ"òª¢ñ›Êˆ$iuHÚïZÔÍ==mN1#§“f68Ü®˜”ÙÚâ>7[dáMÉ†¼ ÏdàµÀçk0ˆ{~.ëàç<™’64Ÿú(¨7S  …$	ˆgãb Ñ
¤lµKÛpšÝå)"(h
"©,o {Ë›Ý~`¼|!†KŸE
‘›žL¦1œæD´uvÀR&ÁÔ@Š íuoCJDNò.óm€HBu¦@Žã¾ŽL$~P1ýn•«]…à¨#T* UI" b¬8cHàøÀNÐ¼`7(ÂÿypeÀU^KÊ~¥02a!i^¨CÒÐ†8D,ÏZ&µBE±ÎV2#&‰ƒ]QYi’4UW›Ñ×Ù:“)±AU D«
Ò6K´Qme BÒPA
h`LŠÐ×Ö!;€¥,i)ž2<¶€ëäÓ1Ì‹43 ÒÝ4 MjÏšÁæ8 ²MpZ)¼Z'–ÌYNõ³NÎœ)ü˜«÷`žèg |:@â+ø\”A_ƒ."¡˜Ä~?‚V ¸iDŠÁ%ü‘0¼BÁ‘‹H
ñà¡
ŠØ„EÃ†ÑÙ)…£E'{sg84¥ÏÕ’JÀÒ¸¦öM§á&Ð”œ€˜WÚ©:Ì7Å—ÆÕ3	ÝŒj7ˆa›i”#Ä—˜ê|Úu¦I-`€œft &‰HŠƒ’$§~`ÅßQ“¼ä¹2ËY„Cø×YHÍ€&ÙA[¸’(
4³’ñ÷GÒD…„MgF \7Fy ÜŠÃPšuZÏ¼ Ïh^Ö¹ÒE… 
U€BŒ0…)Ô6ÅÑÁ¶ÔP Gg6J|ƒ¤‘ìç4[Z]™Þt§?jÏ
*xbtªg WŸy3•ºf­iN{Ô¢þéœåÑZŽydúºÃÄ f»ÈÙÝ
À­ëœk '
S1B* ÎþVÕ
ª’´`JpC&¨ðd¥‘¼Á•°CžÏ²—üÌ¾«s::Û:æÏT¦`„'\
ZøtÎ0ž<,|ÇB>š œ
]I—ºM±.v•Fµ]3›/Ó®4Þ,•—èUoÍiîÑ»—ãíä8Œˆ„
¬ ¾2
 „`‹l3™!†'†£HXàèÞdÊEM¢årôv&)ÖÀÜ‘ƒ<G.†±ŒóSMû¸¦8m¶Ézl8no	¨Å¤Zq(! +:Ä“åÑ ¨é£Vþ ™´ .oÕ$R	³­ìÖ¼o+•^3ÚüfGÖ$¥ˆžßªçF^-Y´iý†ÂÑŸ•Ç»r«„DA˜6m¡#-úÉ3«Ø—¦u²o­ñâä %°—=í(V¿ ÷±Þý±k­l\ÇY×&‰Áðc?{ÙÒFùzg¾±1l[/ÜÃÔü³#òðˆO¼âW°6¶Ç¿ÚòsÛÛÀ‡Š$©Tîs§Wì¦Ð»ã=ïz»75È7Þ@?#†UÀ
}¸¸²šƒ‚ËŠƒ‹…{?Û2‰ôQ ô“8Š›‚KÂ¸Ìë9Áñ¸ˆ ¹Š9Ü)¹i:¹íˆ¸{
mŠ˜›‘tÜé²óÊÁôº9ä¹˜Ê9‘AõÂ·˜'¨¯Ì‰ˆÐ‡#üœ ˆ„aƒÏ	ˆIˆÂÏ	DÀÂ8ˆˆ†?àÂ†JCÐ9›m
 E0Ãp .´:yÀ:Ú±­B2œ¯³ˆ°k1¨0²‹‚–º›{;øY»È±óBÂ xÁ¡º®©;ëÁ»5À¬À£Fi”J2©¢2;´2ŸÞÙ2ø"¨p¯z<ü‚JÜ!<J	$
­À#³@
µàÔ3P`37S…±Ak)E
€^ûŒ˜Å: .`ÆPƒ2ð‚xrË
¬µu±ÀQk% ±
îp‚Êƒ$0ƒ6ˆƒ•®ÆÊƒÕ›>…˜ŠeÜ$:xN!†T6h”ÆbÆv7¨x„©ð<<=
Â 
²"¼
®*=9½Ô3W´Ä¬ fä‚òš0¸!h,®Yª¥2h|Œ’,%2˜¹Eúè!úÃÀ©ˆ¶	™j»¶—” ‰¡˜›;k #@³—°F2ÐŸ:h‚Ý¢	º2È»ú…†¬²½h	Ü$‹”Œ4äÈœ°‰Ð7\Yb,%<.[:I„‹F“„Æ”ì-–T
—äF/ƒŠÖÚ@õóÀ‹³ƒŒ£Km#B!’â¸±H !(—WÉ»„¶³ˆàK 1;P²ô @ IIA7@x¸FûŒêŒ¼àŸùÉ©AF(ÁŽ8AfJÁˆ09´S"Y"“ Ô	 W*¨ÈÍÝ¤ÞŒˆß1¨Ø¦ÀÞ4‰Ü¬>ÏŠÁ¨ÁÎ‘9äÁìAôš |ŸLùr€è$”(-ø$<›º;›ŒÊ…-0Ï)TÏ@!Øä÷ìBùdmÊ‚.¨Oü<Š»º‡ˆ:Ü?0îŒ>4‰81»¶¼íT˜‰/I„ÉÜ²ÚÐ7Ð$„k”~ð;á
<Pä+«ƒœÒ*SÄA†ƒ
(XE2#=5
Ef¬ÅMŠËúØEbñE`Ä<¿Ô±òó¡ppÜ
q”¡r<ÇtÌ¡qìÇa*"°>fìxr10P
0hftÆ|œÆj¼Æ¯ÔÆ›Œ„€t«\ ¤´+„dJZHÀ
´‹ÊÂÕÐ‰¬J„»Ê¬ÜÊŽTK):K‘Ô1Rƒ¶DI•lKéóG“p%*‰G„›G*¸S5ØPY,%itÒÕj8*‘Éi“Ìö»Éœ¬˜F}R»<?ˆãÀŠÛË¾<UøóNŸ*y Ì 0LÄ‘Åœ;Ð
áë¼ÈœLýù¹Ì_	ÍäLÏMGMë Ó˜­€ÕD¦Öœ®×41Ì®ì‘Ú¤Uß‘â×äÄMá×âŒˆãDWåŒæŒ3çä&—¯y%¯´N›ÃÎ³ÃöbÄžóÁå™§ÇŽx R8O³¹¯Žˆƒt8Ø÷4›ŒòÂÂ|8„p_‰=CÕùB˜eXV„xÿ”ˆÈºÚ)Ð¦è:%Lœ• ?t)âá›³QÀ1ëªNŠÐÂô®÷ªPyˆ˜´Í0‚Ú!m>ØDy(5íÖPÄˆŸ»³ˆÄ“âp½ ³¯º`Ñ«WC3¤•H U*‘ò#Ú"‚µ:è³7à+Uð&âË›ÄŒÜ:ÊÑ”2Pƒ™+âŠ¥D®Ð
Þ#X	7ÀN…‹òã¼ (\[*·ýÛ¡áTÃÕ%‘\¤9H2Zø>¸É4Q )°!x‚"ð´, #ˆ‚DY¢ýÑØ‚©P œƒŸ
 BZ¢4JÞš+ü3tû
98H•˜'Ð	:ƒæ`ÔµuTy¨3*Q€â=^™ˆåh´æHíå¬ä]Þæ}^¢Þ^|Ün4	nS€‰1ÕêEÕ"›Š!€+ ‚&0ÓÍÝ8[¥µP[´¢_y8FP *€=ÝJÜ6‚:ÀŠ¹Í\ÂEË‘ :Ð=ï»É
È]N—D‰¾ùíT¨°ÕŽßþÍ^Ê-ƒ(
’
¾É÷UaÛ-ŽmòömLYL´V¹CAmÍœÍ”ã®•û.¬W6ab—11Í×ëœb)®â{•Ú­ñ±íá£Ù(}…* 
ðgø€…jhl hƒTPjÈ¸ HuP^¨'h0‡l …G˜dèm¨†( 3( {Ðe è†Q „M€9˜‚"èƒlÀƒ	PdÐK8†p‡_ø„i(À>P…W0 MXs€ƒ5p…< 8€‡{ð€ƒd( . Y3à8”C£Cœ3±…·=E¥±’ˆž—BMœ-ežÐf^'¡½ž `¬XÚü`€rð»:‹Ú!›Z
è‚,ëˆ¬UŸØ˜ŸÆ3°ÍŸýFOKÚn`áÚá¿t£ˆˆ(À_ä4+¨‚ä	QÆ.½IU¤’n@*µÒr	,­ƒ-%ƒÐ¸•[º­´\,¾´Æ ŒõX Ó†
CÈÝÝ…«9 
*H6å 7uÊ85=ÎzÈ­{ÌÓ´Ê‹ÌÈüS‹ÖÜ´´hC%I2JÔEÕç5	!Í¨½9zJÍÐKåÐøE¦Ž
ðßãÛ¬¯ jKÑMÒê›ìªì¥$`«jÅj²0ë“6‰ÉJk¶ÆSÝêâÐf¨~­ØB ¸–‡ÖÉ¿.Ubäâ=ùa
âlå×[%bn-ç#vÁ$€–{Ä—sb„b|Ýl}bíL;	2€åâ;ôô*4m³ÉM1ÙaÐeY¦pÙh¦ØÃ”êÃLÄÄ›Åâœ…Ÿ<d»CÔ™EÄmÁ ¥Õ¡Y>n6on‹‚p*yàO„Ð*ÃlpufæØø:¯eÅ¬pn{†n|&`Èr¶e6"½Óh\ñö³É¿ÖÛ hàÞò[À

6®AEÜÞ‚;h‰9¨ƒ­.?/À;îkáJƒ*¸jKLÑý6ï®ÁåoäR*å\ä‚¯è”:x¡
oa¼†
ª
€ÿ-ï´-pÃŽš»V!fìmeAoEb ­l¥DÊÎ¡Nk¶bülÆÖâî”
Ïá<ðûLØ$ïËir€8<¦`rÖaí¦ÀrŽØùÏ8„mcÞW5œävg U
ìr4·æ5?¡å6¹ŠFy2Iˆ¹ çÁÃøˆKQÅ“‡4	ïÕŠ7ð /F¨°îÎCS ‚ ––«Êó¿â9uHÛ«@X…\g#I2Z3xî/l~qÅˆ‘cŠ7â(qDUâçÄìèÄõé¼b*¶N!ÏâÐŽ¯Ñö‘yú†/ø…Í/
€X¸c°Þ!°ebxösW ã¯ˆk°v

[ö … CYšC­;fÆNfFLDÆnm¨hPß~ÐiNø÷aÏš;) ¡Fñ„>— ¸î{7E
¸‚Xçu–•âHtÇ[ôwtUwGI€•¶t—ž«˜øM?!›¦SÒ õuH7‰.¢åCï“—‡`RyîCõó~tÃFlÄVW‡Íh*bPŒìFœl2zµì3ññZïõÎþõàö×"ïšyJƒŽÈ‚Bà†cÿˆÙ 1Ie°‡ª·ˆ¡ˆ1`®Ÿ§±ïzy})‹HCà‡&øœyyÞ@ „·xí”U÷•e÷3‡ŸãÙ+ió xsqˆs` | e.¡=ñ)è%½¬_j”Y î—ô—±26 “RD<ï.Û €x|ºM‘Fç¬öÈéÈl\UÐ%8Ø·­8¥Â¸Q|~Ç'ƒÈßŒÉÿëˆ(˜( bP„"°‚‡
Ò‡|tä}y¤¼î Vñ Ù·E|ÖEß –Š·Þcä‰
ñŽ|Õ„ÃQì¿±Ýòëgà èh‚&ƒŽ.•Ç›ÔkÈ£ˆÄ\2(qê`CùÁ ª*ýt î°ýZÜœ`uÊd±UÇFãh“3/”m‰½¥c¯rHÏ*=jÆôDÛ$V"h,eèF €z/Ý³ugæš‚»Ã….;¡¶y ÍFâ[z5ðÇ	¡54LA>œ¥Q¤›‘€€ÁnX
#„ ÈÁþ"BÃ«K&áD„ÑgqàÀçR¨ÆÚ¸:z@Šp7baÈ€Àª€,`ª2¸f@œDÀ
Ò€x ÔƒIFE¼ 4`mâ@U@ƒj
ÞÁ<¸ ¡l5p¶T…9Èê U ø 0x 8g (ÔL˜éÀ‚^_AS€Œ®€	yÀ³Š^ÍŽ€àÿäÁpQñlj5"R@£ù‚ÙÁ/¸›kCx”å	FÃHý9û ¼Ÿ_àà/<ˆ¿êgûpQŽÊ~Aº€Gc{æQîÛ}n­±¼èP~JÅ@ ©Þ©èþ¨_Vˆ
¨®¤Ôò×ðø;pÐn‡Ýà§ßêk‡ÀMÅCXåAˆ5A3\ŠU„ Å…Òñ• âU€i i*™Æ@ Z@¼©ƒÔ«€™0“° ‚Al?	ñ‚,D0 ë
D,7±TD¤Ä
3b
ÐHQ
qK.D|0ƒ–d!üµ4ñÏßûÃ_ñÏ\PŒø×¸VþÓ‚;<$<rþxÑ5ìi7‰14Ø£Ú•’hZª;8:  Ð‘PP±”˜Üg¼ˆ½Ø‚ ±äµ/ò‘»4Ã
xa_P-l†Eâêa ôÐrœèdáRdŠt#n(9bã ½Q_) ^zç–`¼IC@.:ºØî¢
Édµ–Xaâ\‘‰`°Ð€`o’L`sñ®ÎYJ`9‘H…¿Øä×G¼@8	 
¶€¸ÏX´
Ò†&ªcØò,ã;ûZaEE( Ÿ@bJ<‰4¡!Æ’‡x{á^œaMíå	{óB€À„Ml©Ñ,ÇKàB;fDîØÀŸ
`?1865y0²´
ñ'Ô‹ÐˆGã{ƒñQ
ÈB¹×¿w|bàM2(
u#©B*B‚Dy°»VÄ‹gÏq%DG‰X©c0dˆ*1@ÄÒH ãôÊ[Ô2žHG8-Ä/¬ŽY¡^¤È™ œ
A¤BÑ‘G`Ñã;¤	êQ÷¡ea"é@Œ¬
òñ;ÐGûø"„j&²?ÖHK )"‘œ+J’Išp Ïš\üŠ±A>È.ù"%äT€Rý¿€ú.d	¤“\ÌæÁ¸ Hr" ¬s´Ž™=^…&t]ã€œÍv@Di(eg[”ëEÎéœX'%å¤¤”-PÌ3Uæ%Û0¿ÏäÁ»Avx›á›M‰yÊ(µÄq£“B+M,"ÖdB£@~‡ b¾ìf€™ð)TðMF…Zy¸O¬Ì… ƒæ±žd¸ña:|†ëÐF•¿ë "ˆU”j¤a2?É÷üdaPtð¯§i…'ïclXŠššE„#§eÀ†ÕÁ*N…†¦À¸ˆh`ÀT-5ÃâX,‹¶ \´´ˆn6„I—Å­Aµ¹+q!«‘îhB¶ß¸Ó[Ë»K«*/qä8.+å‹¸“­çÉ¸=ÙófÐ»€9î²iÀ'ïåÊrMU
¡}·1e¦¥DwâJS‚ÀŽ@Û€!R Òtè¶•2*ëR(•=0"ðÌá3cÄË$GkDGà
¤ÐGˆÑ  =´¡U‚Á#š ¦\Í‡…äB<àšS3Ì(°)å¨F8˜]ó#Ä‚X6YÇØp63nÊ–e8~Ô`< úä¶ú4Q4k
E3b
 5RÌ­ëY 
~T«Ž›lÕò ¶*fV@‹à‡vŠc‚
¤.·Ô4ÚGhZHµØYñÉl        (àA„       2   
   ƒô«~“±àyö¡ ‚àÌQÿAå6€%²à(œŸá±WCÂè(8«”Ê'x”	‘z                                     N   N   ÿÎˆñJ   C:\Mes Projets\repo\avsp_server\src\TEST_ServeurConnexion.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\SignalDessine.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Â¯}hÆÓnž¢ÿùöõ®Á'HÍ•¸†kàâsc$1Þ’     "      ×      S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          s                  9                €    ¼   01F280051n     o       Ý      >        
   
\                                                                ÿÎˆñ   SignalDessine þí˜á                                /SïJ}Å
ªDŠufcÖ“c‚
)¹j²çÕ5n˜!*F³å¸ø(
—Xnà‰2d    Î  Ð4    
dP©Æ@	:%HRÁÑ…‘b 
*8`1ÀÆˆ" ø,‘<©¨AóFL›"fèÐQóÆL™uBØ±‚H13Íøç.“8I ’4)EDiÌÌQSçf™›JV¶|sfM3
ˆEªÔ#Ó“. ¾ãÉ1q¤^Q3§Ž3mpÒÑÉó&™4bÀŽ=jölÉ“O * ,ÆMœ·q¥îíû&DãÁE
‰X^„ˆgj.Ë9 Ó©U¯öø!"H¬#º¾X[¶RÐErÞ ÷ïÁ97îQÔÛÉW÷Vî±bE
ú^Oí{äjÔÍ“Óþ8½9wØÚ-æ¶Î»¸Yâº•_,Þ½EæJ„ŽèÙBÄ#Vž©¢ÊÒ¸‡µ3Þ3ðÀæ2ña0È¨Î£ì`ÐÁ ðÌ‹4  ;ä0µôÊ39K´ˆƒúšÒ ""àxƒ¯9ì Ã*;æÐò„!‡.è
Eõ,Ë‚AvËŽÄâÐ’ç„ˆâ˜2ˆQ¥ŒojñÅ:bœÑŒSHICÕŽ4ML
@‰ˆp.€2"j3Hˆ(~)Dz“ µÞ ˜Í»î¾ôAåÈ‹/)ààDï¼ˆØ
@¬2“;ÔÀHø°¡ÁBˆ¢L#õô¶
/Ì,=‘Ñ…ôS;Hñ¤"ò*Ki´QCúÈ1Ox|¨7W
@ 
1Ò£‰’ôÌ„&ŸŒrÊ*C˜UK[½$-LìX5ëÌ4×|“MTs :íŒÏâö4­Ï?m#t5ð ÏÐu‹›Ô¢'UUÛå"äÞÕÄë… fðÈÁÿOà µ)#Ò°ÔO6U$ùÕ.DÏ (
1àrÃŒ,‡ÂÕšj„dÀFžJ|<—Q0‚$»U ª¾Îš2""`a
3ÊR´7ªRÃÅ°
¨Ü²Ž4àpZ+–\‚I&šlzd‘IžÃd²Âô,¥§€‚b)ÂŠ)ŒAÓÛŒÔpC4Ìø²ìÔÈ4LÉ""j4úžã…˜Æ°F Ûî’Ÿþ+°ÇBÐ›ï›Ô(Ã‡B`!„<ga„BáïÍÆô, aØ¡±Ç”Ø»ï’Ã[Cð!#òÈR°:ˆŒ˜WlÊûv3Z¡…xXýÚÀ_}vÌã°óš¹ï} ^x¸ª2^ä•ß¼ïf€^zÖKãLI"ÂÞ1Èà’ë¦ßƒÞüã%oëk
Þ7=0U¯)ÈØâýÍ¥.wÉ‹øF€†š#`AéÚ”À1iÌDÜˆš,R‘o}kRã
@¹ tÁI]æˆŸ/ÛlgP¢
E”¨Ú+\ø
×¾DèÃ#*¥#jòSY´B»
 ¦šŽt$e)óˆ	Åó°“Dl £@,&"‹ôI\<ËYãÀ	Q Moòµž5*åHWdÇšY™aos›È  ;¨&uXÚT .
a ËPÀæ3;Ò#/À™Gv¦¬¦ h AZÑŽV¥1ÍiP“š*¨fµ¬lEk^éš'Èò‰\d#©çº¦ä "QPÛÜ7¹I
—„4ƒ!©ÈT¾ÁK¤
Áà ‡6HÉŽáü°ã*qŠ»¥ã 7‚"Hic{
ÄpÑ4¦o!ˆÂ×wîšŒdšç@':Ò™uª‹ŸÙš¢˜ ä’dUÐÛT¨  :Ð²&â;ÃîòðËùyHØAãx'Qi4pßlÊ"2¶7Üz˜JîP>Œ.‰ "a ¼x‘†‹….¼ ôÄ§ˆÄ‡/Ô¡»xèF8Q©B¼˜Gî˜#ÎQR¥ªT3 „	½Ë,`”‡ISF3šæ©Qc{J¨ 6 Ž™cgÖÔ¨Þ”©RõãI¸#fZÅ™¼Œf€¢`B
pÃ%e6±ê#
àbˆË³¥xÆ	±+¥ÑT´h6í&¬œZÕ®&Ë®p
,/ÀëØôªÏ^º(¦f¡ß0‹Ù¶·Åmn¥%Y3QÍiî š×Ì¦*¶¹Z=’ô$Rgâ¦RÎÇEŽ¶Viç;ã˜yÖóžù|fj-ãÏÐÎƒ§K Ì“xÀ~´Óó,8à&EIÜ¹ßc"³¿ó¦÷0¡ŒMH÷À÷JÐ.xiC~ ºÐ½s C0H;ÄÁI
­‚=ë ¶%¿f Ë~ó"RðÊƒöÃo+LA½ünèíæà<“û)´ÙQÑZæw/Œ|-"8ÖzÆ¤‰Û® -a
!¸LKÌÚª²ª¦rE¡›t:§:½0†"š¡™‚Ú®j55CÕ2kzhå¤Îu©a&³aÛ¨Œ%™IöHVÙ\ÕL¤!«\õ*)vÆI•õQa!dÑ¨œµ¤­Ê$\‘Ôç¹Î´®òï@@j†"ˆáEÛë[€`Âdì@h20qXjÒMh¬'=Ò%5²D³,fùi™UŠ!j…e°Æ•­}ÅkŽô¤™gãžÄ‘i[l‘97^Kš”Æ[sbë½î³‘ mÃ
¨%0AU¢mÝ¦éÁ%nP"ýFMkbS›Üü®7
zDäÀ	^ðÈ¨ %ªà-Â”…'ÈßÔÉ Ãm
ŠKÎ+LÅštÀ:c|ekîsÙ
h¸ÛN)õvdoÐ	 óò%¼+y¸‰›b°HwšóB 1€'Õ®ƒÆ0;Àó¸/€ÈP#”’l­#øI
·
a
†´‹Þ Mw¸ßxž7óÔüæ9?ÃT¸ /”<ÇM!Ï”  # ÷Ý(‘÷u	{øßöR„+  ¥*eéÊi¬†º¿YRh:ë	Á5*e§Qî©m~Z¯b9<‚2ê{¾¼-1g¾Ìc6sÓ\Õ5
> nži&°Ú)Ã$Â¤ÏD HŸ…Õ£Þ,y8Âœ-±Q¬xÆ£YEßùx&VŽ6ƒãê Ò y‹
àA3D ‰ˆÔg®™0ÐI‹|NJBÂªMyYT¾Z³O“u+_ùÙ¬…V×¶ôèñcx%1‰ØÆŒm2ÛRk¥]½wAÛ‰0 ¨Û·	£º¼hj‘¿?Ï€úRŸÈžàQ$«€0òš0!h(˜‚pÒ¿ù:‰!€
š•šƒW;ŸÇ€;~ÛlƒÔi¯üé7
¼1Ï(»³ƒ¿lù=ÖX2oq²¦Z<sA,ŸŠˆ*Ã¼È»¡¢¼,;ª¦ò3Î›B¤
5Có=6
½ßË†1 =³HF ½CÔË2¤"6£³ÜÛ2â½¤Ð³U‘«2S«¦ˆ£A#‹Bû3Œñ=9"ªZ´¢‚Ý¹3`¶%ˆ3@ƒ y.ú@‚x ^ …éµsØˆŽÈ¾S
6yXïkµðk¤ñã,Wò¬XJ¿\«%kÄBdžCLDlŠ6p­b;&Ù
A´B4DD„gã
h»­W£6kÃ6mã6oFQ,qsŒrÃ‹Ô­të­ßºÁëX;ƒˆx¢Œè‰ÆHþQ:xà A|# ƒ8¡Ë›2PÓ½A«¶¹Óy5ˆzŒÅ“¨ŸØ»•j©2ÈÅ]tÅ(0$°Àì™°ˆ'±S;ô
Â³© D¼'#Æ{«z<ª&T.ƒB=ÜÄÍK4¥8³=Ü3ÁÓÂ‰|ÉäPC‰!•6«pù$Á±´´¦¨€ˆè ø X€;<ŠÆ+É¨R4Ïó=¬ÂOY´¼‹‚±YÇ±A>\1.ú†*¸ @œÐ€<Œê[ì‹í%©HRbµSJñ‹µY+ÅZ»µYz¸¯™Ê´÷³ÆÈjŠ0 Eú;6MÁËªŒ±øEP‚o‹&b¼¶9È¶më¶Å|Ëf$7s‹Èý[»xˆyS0ª°7|S}“:œ;´:ÌAyÈÆ 8ãZœ„›ƒ…k¸GkšˆÃ.€Ú®Â§ŒS§mê8ø¸{4’óGyØÌ ˆ(€–s90 †@¹Ó:œÓ¹Åá9Ÿºm:ã<ºGIº¥ã¦{ƒ§ó-@Ó¸Ô™N›«Î®›ƒ¯
;ãÔA´k7™‚Éu©H&;<þ\!(+B+ìH$ª%€‘tÂÉsËC+ÍóC å#•€¤T3ÑóÂ#Ê&°PÊ„Å™#š¼3›¼B–ÄOŽì˜ˆxRy:q` 'Ø~à .€S@‡%Ø€°45
0ºMô3%ù™Q¶?·\ÆÍ*?ZC?\{4ÒZÑ5Î
K9g‘*a'}.ÎÉ?ûD²µ
– +‚'(àÉ"0‚(èÿ0ÎF{” 9‚š`1‡‚(/%:“;	Ÿ\Œ¸©‚åœ‚ùÄäËÓðŒP	% ³Sƒ(Ò@ô@8…Š9­Ó†zT:ÈSùÙSyHÔÅ@0Ïd0 ƒ0Ñhƒf’RèZ@CU’>}”?
ÔA³«O=»“pÀ ‚EÝSMU.Å 
¬ð#°(IíÀüR=òŒ€Š¼$nTLet‘Æ4ÆÈLÆêê«VõŒ€
ådN5h¹—ƒN™óU5@Õ-eÑU}ìü9fzîìÖ¦P‹ÅP€t?T5ÕtVvåœˆV7¨AdVõòŒR€Z5NfYŒŽz¸^[6æ9LTX†µTÙ1ÈVäœWôÅŠMM$;BµÕNS5Q 4¼œÂÿ”²#ˆ$dÐ=P‘Éy©B*<I}ÐÍÙ’$"Í
=ÙOAÃÑ“=üÌ€$ÐPÈ€ ½pÒË€°=Q™IÝ«Éá¸I$ùÖ¤ø$“
ÏxU J¢¤#
9J}A ]“(lŽ*P
/ê²O±æÀ[ÎÃrÀ¾
€D¨½
\³È<øÛÀM†(Ü+J€APÜÝHƒp\ÎH:ÜH…
pÜý*­¾’ÄIßÛ¥H–LÔŽ±e«(Ê²%
µí½mØ8‰ˆÔ5¸e[¥È“ÙÑ~ˆ	pJÕ˜”WPÅS
	        (àA„       2   
   ?HÚ÷Ý\…JÄ–˜•«ÅžEÂ@Â‡Ü0;í+ŽÜ<L‡6ðv‹0­´=Î                                     K   K   ÿÎˆñG   C:\Mes Projets\repo\avsp_server\src\TEST_SignalDessine.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\SignalEnregistrement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 pR‰ù *=:098Vpm"È_"EÜ
DÊ„ýgû
     "      K      S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          t                  9                €    ¼   01F280051n     v       ¦      >        
                                                                       ÿÎˆñ!   SignalEnregistrement þí˜á                                /ßDáó§²›ˆXÎñõ!Áïnî<­z•¶~}Aü´7Ž« +:ï•ZOF$    —  ž2    
d0fÆ@ð,%RÁÑ…‘b 
H´ ¢E	 |–H^ŒˆTÔ y#¦‘7sÌ QC§ŽL7fÞÔ	aÆf;oÌ„ ÒF:füs—I\G¨G–”·"¢‚4fæ¨©3T‰J–.aÊ¤i§Î:K›>ÚqªÉ WÕ:më–¤ÉWßÀqsD
™4j´öüt(`1Ä^	Ë¶®Ô»òF\M#æM™6fü’!Ì­à9‹Œw Ñ£¼•åê5™‡Ø9sf°Ož†CÀÎ¢ŒW!däØ©ÉU
œ7`[¶yAå&˜"Bª|xñ:Çß¤Íº†^¾uÒÈSfìÌš7ÍäÜÙ¨ÐˆÕ0fJW5ëao¯ïøñFÕp@?ˆè#ìÈÀ‹Tªü
é±	¨ðÂˆ0”0C7|¬;’A® K­BM¬¨"
ô9ðE=\M@ S,ðE d0ÁSŒÐÂCl
D-ñ4“i4ô‹(2
":ÂŠ'ˆ¨B	(žhŒ{<CI0¬g…Bn`ƒ IE
SRø Çb±‡0Èˆ*¹3JV¨s€ ˆÌ‹úèPDUH ¡:2 ÔÂ”*ž¤Jƒˆˆ@Î,;ÈèÊŽÐúc  º„!‡.¨°Ò"!Å¨‹è(ÒY;!¢8æ€ƒbT)Ô¡4}ƒSOÍ 5¸ûÆÈ´h/7Ä#Ï¼˜Ð3k=´BðaÈJï¬Š°ŒŽÍÓjNàÍŒÙjLÙºXË"¥•–#bŽ^ÍHZiÍ(ï¼²Ôc¯Žè¨×%',³ÃŒ6
>¸#´r£×âcŽ:Ä@Ã!:
*Ùþ¬qÒP""Y-ª¨ŒˆXnu "
¤dÁ
%P­B"ÚÁ;ot0èD1*
Uúh¨e:€Gi%YÁ§§¶úêÑ²ÂO;K!®”*B#24QD2¢_Hœ‘Â$#« ¢€øÁœp­?›×(j¬Gs:*À¥
2ðº:°Ô¤"*·9:ý4ÔµÀ¢Tx0Aõ¡
oU 
 ™°€Ž&ª52pÕ•W_
áqb'GÞ¶Fþ/²ïRÎ¥"z½‰¸:ö
o<©õöÚA^KdÄO<y ”_t9€éŸŽy ™kŽèæu><€ž úÇÇïÑD£¿Oºêíý†ÚíæážT{F êZÁ6
i£:Øg^ÿ ˆ L e¨Aj-hK›àÜ×6$¯#þ
Üèf7¼é­1}{Ùû"¿ŽÎQn#Ú’@ÂÅÉŽ[ÏÅdbâ,;ÈÙ’ ˆ
]hCË@€%TÕ¾V5É< ` PÐ‘&™p5‘±BD` ƒLÁeèÕ~ŒƒßÅ(!˜˜*Ä‡ß„à+Ø·°µ“<Î
.ä˜pbxEþ,ï>‘ÉAD¢0(Š(‚¦`„¼®…sx¡±£d…` !àÐ†|ÅÑ>ÝaUœ€Tæ2™ÙŒ¶v;ÜA&)Ø´Êšœ÷ê•fø¨²	fð°b	 #a+u –ò&y;ªLFwJP¤&1£™¿ô !B 0*Ð.*¬	Íiš  4llF ¤±æð‚\î²	õÒ×¸)Pe1>øR…+µã;ìæ/æšƒoP CÊðj(Ã¶BÀEò“#hAFðOEšrž ñ¥âVAêO¸´`„)„àb°C¤	üHI	Z
Ù}*i0DäšÙÔÊ
® •F&%#P#C€Ïëè‹q€C:{À€&üô'@w P‚¡¥äË)µÒPæ‰,2A WXÚà0ª6úâñþU­€‹`³jÄæÐE¨Î‘*GˆH¼jž<ôŠvèŠð¢õÖ2"--èWyž ‡³º¤ÈòÁøðQ»P…¬@„&±a®b°ì®
B¨CW.c×¾ÏZÛ«bà:¨€™Î„ædIC•#žFT°Cr¥†œT¡§Á[W»l3‡´v JWQ  ›Êýò¡Tñ¤Ž0R×B†*,¸¬B`1:ÈS6´ùí
\ÊÏ¡4ó™Ñ,©p©ââ2Ì=J€á!‘£Ùæ<':Ó©N>áÝ%“¸nmÜ”øö]å¦IR'[2ôt'·ùÉgãƒ „ÀË5	ˆ›±g(½SÅºRÐ[îj¥S-ÌP 0ÄGÁ]©gB`Zü²f…²U@WÜ ÷ô×]Éuhí"cS¡W[qIMôÅ0¾®'£C²à„&·À*$®øKø€
S ‚X`ã8Ä82˜¢q‚ƒÂbPwÂ>¦ÊÜd» fÍÓŒŒˆKbFATØM8¹2N–è+“–9æf
zå3¨V¼­3H#£‡*¯T›çô&8uùgrÎT	7†RÔ0†Þ”rÝŠLBÐ†Q«’ª˜AYÀ°(á2fÈBZ†qz(@¥§RªÐ >UÌTñ@•'ZÑ‹ft£¶I&@Üç’TŽæ5É—d+é–¾t1-§Ÿyiê0¢ºW«n5º`-ëßÔ ·ÎõO:˜ƒþº©ïn³åÑ\DÛÞ?Òw£k’ðÀRŽ×Iå¦Èä
w€ƒ#J1¨!Æ'¤xÅ-®$“¡¬#+k™û®—&šñ( Ü“QsëÂ3Ÿ‘¯„2ŸÈm”>ù--Dë›yÍeDó›Û\æ9çùÎ}ŽóŸë<iCr_á.®Ä£']é`::,¶$ª~JTÙ”(Ó\QÉ¦@§0PQì`Ü&@<¹@‘	¸ûS!ŠèìHS{ó™	¡›]i¯N%)ˆãzpÆ6ÐŒþp qˆµÄˆ6~‚¯5IœE)R±Ãó£:·ØÅ/†qŒVÍ+iÑ¨w™ðÝ¾¯´7ìˆG=òÑ€dæÞÛ˜S8&r‘|d$'.Uª¤t Øþ¦¶¹=SÔc]¹éêßÉSŸîšÞCíg¼Z†ÔúLí
½	^;ƒ“àòn–âªŠ¹Ö•°†ý½ZM"Ì ˜yÁõÄMOv"THxÚSþAD0\ 
Ó.v	±oz( ƒ¹ê¦7 3øe21S±3ÓŠ˜Ð?åb3“À€«?4S³ý£0yØ ¿â‰Ž’) ƒÔÚ7‘’¶œ&ƒ£*Ýy¬*­ÐÂ+°2£q«¥;ÐŠ:ø>:£
<ˆÔ;130Áò ­9¨Ü2ƒÝJõsBóÈ˜àéªòëA½Ú	è-Y£µô°‚	.,žÒ+#)ƒƒ?<+&Bã¤¿pÀÕ/{ó‚« ¾42ƒþBƒ™2«œø¶¡øy
¶z5œz£W¢¾¢º¾ìóµ„š7E|Ã“‡x
B4c²C2ÀDÊ‚¨«86‹Â¨¸‚9ˆ8ˆ~
A
”‡¬“3X<B“h®'8{óœ x"…c8‡«'ˆ“8£,ƒC¸ øÅ†Œ3à(*F‘X:i¼¸Œƒž£žŽë Ë‘#9¥1¹¶@9ñQ¹i¹•#˜;9¡[Çž:wdÇ kG#º2:¥£ iÌÇŽ¸É"€ºŠË‚ü“ÇÈ„ È48H‚Œ
¯3	«k±µé r•ØD¨8 ò1
ˆº¹›¼Ù›µà ¥1ø¡8Æ‹£ÃÈŽÐH	ŠŸ»£
ˆ\ÅB”‰-Ù —¬ƒdàx†Äƒ»µ¢ZH™¬“<%b&¸¼(š¢*â¼G71à"ßI0ò
ÍjÃ3ªƒ¨I1¸I¥€Fé2‰x½<Ú£>ú£@òJ°´+§ª7±|÷“‡ˆî{KOjÆ1Ø

há5ÐpKï³·Ã$`ë5;LÐ°76P)lÒ&—‚©N+'¶$†TP]SC¾P'Àè«'TkD÷zHÜ>¤Ò¾¥RLa‹KÛÁhˆ\Ù•^ùS›ƒ½äË…ñK¾èÌ9HÌ(Pƒ2è4ƒÑ
:àM70±<ð,ƒAQ¤<yH‹ÓÎÉ!Nãìª?Ý@Å€;ó»‰ˆ«"È
7˜à¤¥ë,NOCNådNçÔŽT*‚šÐ°Xš‚^‰"„5‰èKy°ƒ×˜>î„ï\3€0*# ‹aÀà'ÕZ70°>ñlÎUäŠó\õ´NoÂÎ÷œƒä„ùÔ‰ç|ƒTzË Í/“ ƒ« µùÄ¢=ŒÊøÎ¡ÏñÜPóDÏeÏuÏã$Ñø”Q¥ÏÑ7hQÖ`«ÓPÆ`Ô5áœÒìZ·:¸1ÀÐé(#íhÅ ØöÓÇ«©Æ ˆž‹˜žê‰Šm9’Ä™ˆ Gºˆ€3Çr$G%IÇp„Çw”G@­‹”ü›“<:|4SD\@€l‹L¸F
†”‡‡<šÈ’Œ³û 7»Æ—¤:ªh;‘t
¡¬G·éT•ÙH”L¡bo—ayË-I‚$R (†
(dÀÅ¹¡ÄˆpÉg³ˆ$:ªxÍ^Ä<§Ü¼7è<©¤J/²JÑËJ/4=®|Õq‘UÖŒŒí IØCËÙ
$lUzKõª?ör¾÷z˜ø¢Òù¢Ž¾sV=h‰ôÔ0ÕÚ=GZ5(Ó)ƒ?ð0Û±k&d„Ò‡ÛP~A–D´ÿ"M«'{‹­ 2U ²602smDc2'ƒ²‚¥
×ÌÂ®>»´: 4¡ ¥OD&284S-— ,:°7¸¼4Ð1÷p°z¢=°	ËÌ	O:†±·+¸<ÌO”eÙ:tÙÈYÚhtÅ½W‚/" ‚* ƒéP‚&ˆ,0ÀÚ)x¬*ÃeZ4òªEGCÂ>|L­ 4P±4qÊ´Ø4žò4P;ÄT»4S7Uë•r{5æ@7Z³5ßh7éSDJ¼ËÁÜVªˆ(@ØzjFfc\“à7èbMƒ›?4¾Éœ)ospK5rËs
ÜYS7¥ÂµaÉLßL\Õ„K´ÍÄƒÕ	`LXVÄÜD]:4UÓaS“8"‘Ë™‰ˆ;ÕÓòAÞ—«»@ýÓx|^ç¥ Aõ ‹ÔÝÜšH…Ší Îˆˆ…=°…„ €‡ ƒ„phƒì%™\ ƒõµˆTh°™ÔJ] ±ÃÔ§ÙÔê-"·`Þ

Õˆp»µ(ÕL%Ôâ_cì øIñ‹ˆ÷pø4 $˜É¸€¬:†„“ºÀºÑèûùˆDáº¨ Ò©ú;qˆHüŠŠ¤•nE¡âÈ6
à(À›Ü á}£‹a‹`©Q’éµG3­a-à’‰ŠN^7ŠD	        (àA„       2   
   vP‹&.iüq"' as-2ÐûV¾V "W­¸`¸â#w§»(‘O ~ëô                          
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\SignalTraitement.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 xéâ¯‹áVÕ •²»ÖšÂpzçWMà«• Ê®‘     "      w
      S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          	                  9                €    ¼   01F280051n     r       w      >        
   
_                                                                ÿÎˆñ   SignalTraitement þí˜á                                /{¢æ±çJþ .=¶TNéóWý"óG‘LÎ¸F"?¿Èéñ¤ŽÀ­Ä}ÚŸq'    h  ð    
dPKÃ@<Ã´ æ
Ñ…‘b 
,`1@E‹  ø,‘¼©¨AóFL›*sÄ¨©cÆ™7uB˜¡£ÓÎ3!ˆ´C‡Ž™ ÿÜeçÑéÈ’'ODTÆÌšA•¬lù2æÌš7s&]Úô©G’&åEˆxFÍ²LÏ¢[×î]"‚üˆ÷¬Þ‹ýÎe;PäÜÃ‰#>Û`ñST O¦€ñàŠ-èÛÛ™¯áˆüŠ®\: à¾‚+£æûÙt€Â–í*®ë´ÓÈ¼æíô²Sm#F•g!â+OˆTQåI\qî
>c—Ìg€ wÌžÝv€Ž}¹Î½ûÄ Ý:ˆjÆ2åÓwgo>ûƒöàËWß>@"
¡†ý@R/À  ®¾”bJ=õîúî· r
`»Þ2´‹ƒáÔ:h "àx£§9ì £&;æ€Ž?@Jh
†ºà0o“Ì‚AD"M²B#nªâ˜2ˆQ¥Œƒ
qÄ:J<ÑŒSP:¼v´ËJ
ñ@‰ˆð¨¢2"
ÓF‹ˆ(~âë;¾“à®ÃˆµÎL£Sµ×b£°G÷ŒhBÈxÌò5?ë’Ï<Ã8`<ùeÔÀó"
#G š´Òù²c =0
¥Sò õ®=<-5ÔÿØKpA‹ Âî$|7A µu :<)ƒˆŠà©IQTÑUúpq.b|è°\É@ 
ÑÑ#ôlLë$"’H#‘œ#(_IÊ9¤¤ò.,çJ÷Ö§¶ìòË ÆŒwVÌM5ÿ¬S¶Þœ-"9OÛ×µÉîL­7=ÿíÓ.@iÕÝÒ
ëÐù6(<ÍÕLú.0SõEOQæ¸¿ˆÂÉ S—IîAÕb:X³“ÕÙ,Äâ,¿#„^mcŽo… C;Ô ƒ&5D\‘Ò¸q…JiÀ’õu68
Bé ‚ã3€kå‘""`a
3Ê0Ò­7 1„2‚"*7Œ¬#
8òa+–\‚I¦¬Äªã_Ý8:é¥›~ºŽ¨árµJâpˆ(Š)  ‚EŠ°b
#B¡ñÇÍPši§í~CÊv8Ú0§s¯¸*"j4Ð¸ê…+®šý¨F@é Äˆ¼uÊïŽŽCh#¨&Á²	'Ô(Ã‡B`!vïY¡…F˜òrtwWkƒˆ¾}ã8ô¸*„;ÏèÊÜÈK‹€ILô*Ó™Òt06ÑË_u‰SDF°Á<PC	c ½hÃ/6†€ñÿ&#1ÓPLSv	äQyŒ„m8!è3œîø' 'Ø Î‘ØÀ,ûOÀ80D(#ÁF"””šäf®Ê@v–AÖÊƒ¼šZP#æÕdu’sÝŠ2ANØ¢
8ÅÖº6†Elž[Ù<r˜´Emnƒ›ÜTA7×áMobà›ß '8ÂuåpÚSÜ
°ø-->or•ÛŸºˆsƒÍuîs¡]éBpH3$’u‹ìdG;Û©wìÓæÔBßOxs žñà€¼`²&!pÞ&ó8½ê]/ÙK÷Bà=ð‰|e0úÔ×È³¬ë)iƒß@H÷„+@A
F˜B® ?RNq0þ
  ;#/yQÈ^À÷å&8Ì{3DAÄXPž<óH-ÒAm¦“]pgÅÎTƒ%p¢gxF04AŽ3˜â¨øÏwâPpãHh„Žð†CÄ8Æ=J‘4( 	­	&±
Ú‡:äa 1‚EäB‡Á~Ä!ñˆ«²Y«ÊÒÄ'RŸù|MO‘Dáhb¸I“B¬/ ÁpDÑ
Lœ1 ö ØäãÍ˜^iKÂß·¹½%‚Û[ßTñ·Ài…+†ûJ/sò¥Ê¤©ßBæ2‰³‚Hzt¢#éòÊÔL~+qXªè°¾²TÉ}'C*ƒ7¼âÍáxfHÞa÷Ú<EÞ’zs°ö_3|ãÛAùÎ‚8s|ŒÕëcûêe5$\ú¼À¹ÁŒ³œk:gDÏÌ¥Y.oày–
F—aÀ­uûÆA‰ ›K ! 0¸pTu‰E1ñ®ç)ÞŒ‚^ÁƒôÊð,QøÁ{ÓK3VE„‰9
j vÝÒUˆ^B;\oEèH ¬Š^l ÈÈêa¸
¶¦Ÿ]Å.TÔBm
©HGJ’Pœ`Îš¡
;éÉà¶½ÊÍõ%/ B“ÀP!TA`x1'ß€7=¸Ä
j`šT»Ì®vfedÛgÊ“@r ePƒX¬î©á~>ØI^P 1Œav8Ã†÷(Ä'Eò#ÃS|%âx@[£ý¥d0ˆx«òmÑBœ>PEÅ_ó˜Ë|.¨Á
âã²LÞP8¸!2h
T°Ä mË+åÌ`„œ¨©PìÓÞ` :¤@
!XAR0ƒ„ÀmTvÃ‘[=V‡@9ˆul­kMÇàÈãs4¤%MiKcZÓ~¶HÚbs%<'›Ž|_üpB?û‘63avh L(ÞvÓ·¤§pH\Á SaP.;íän„Q÷lÒ]X½çÂÕžY÷ÛPô v1:¸½ 	D
”±²H
$ˆø¡@pðôr7ø½T ñ:t
'ø0ž*$æ·A?åïtéèÿŠ›ž ´
qæ9ºÊÂÔª7)Ärˆù;"‘@Çï¥ƒw=0ƒÏÉ)ƒ «‡»¡ ƒÑQƒ¤wÇ¡jpú	¢žU%QK$9¬úë”_è]+ïË#sèÌœž5§.Î³t˜	È›2"0å^¯§¨'         (àA„       2   
   q6ÕìÜC†KŒÅ•	›”¼þÚj½,-½ ¢ò€Ý=M†7ñuŠ7¤å                                     N   N   ÿÎˆñJ   C:\Mes Projets\repo\avsp_server\src\TEST_SignalTraitement.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\TreuilClient.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 WZ¨]kP@;%/.‹(Ñ³È½%eŒ©qÖí     "      }=      T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          	                  9                €    ¼   01F280051n     n       à;      >        
                                                                       ÿÎˆñ   TreuilClient þí˜á é%Lj                           /Èæn[!“Oš® æ§óë tªŽbð3Ï³w"t6ÁéÕ	IÄ,~L&»4ˆR%    Ñ;  m×    
d€Û@?
8 DíÁ†‘b 
ÕŒa1@E‹" ø,‘¼«Ì1cGM"mÔ˜yS'„:6í¼1"¦:tÌøç.“8I ’4)oDDiÌÌQS§§’•-_ÆœY“¨Q¤J=2=Ùj9bªšqCÓ&N<C(¡¢Í1mVªµÚ¶fˆ2zðºQ#ÇÎÐ¢GÅŽ-yÒÔ28é¨‘
7ÄÎ¹uïæ-"™rÏÀƒ
þªxñÈÆò€DÑúæ–hÔädy3gf»xÛmF6m¶n
CØ0b°§Q7õUÃ³«ªóÆvÜ¹B »œ\‡úÍº‡‹>^:¬r²ò@5¯|ijPÑÀý~ý²u%HèWïL‡H^2ìhƒŽñŠ
9ÓÐKm½hÏ½ôŠ€êŒ—p‚ƒ6Ì¨Ã2¨ªCˆ)ìÛ©§:Ä£
3Ä°# è¸P
1¼;°žÈHC-žT°)! ŠBŒ´Ü C‰š¦â	Œ¬\Ð>*ÐãÇ ‡,rŽ#“|‰OKÁ Lì<-S3«A
QÂŽ3Î˜Êˆ¶ÞºmÄŽ¨ÆšœŒ"„¨B0ØÔŒ:²\,½"¢ð
AÅ"À½÷šÊ!";Ÿ²8)­¢Ò$YR’@Ëà¤Í2ê°ƒ¥D•J¨Ü°²+¦š¬:bU S¹lƒ˜Wrü’ÑôkPUM_juŽWßÀsŠ+À‚)b¥µÆqÕ59S{ô×U‡-öØd…˜£—Þ€cÖ7k6WÁ4µRÍÖÕï¸cˆ(œ-Ú[Ñ-5©ô®íX­X…·:yÿ‹CT–È
_iÓå55_ÿ}—Øx‹@" (ÂÞ…C°µá}k
 wƒ˜b‚-NV?ž~Öãs§M0Ì¦âÃÖdm+¾8‰–Xb*¾a}w­vä’Î9å‹¡°£Ž8žÎ‹¦vGÇ’VricU~
'8æ|eVWÑÔXÐú%%ê•ujsËvøè“\X[/=â0ãm¸‰6ûá¦Ô¾9à#X’ƒoZ_ónª-Jï…»£˜Ž3ª‹lTYŸƒUÌh£ñåê¾›
šè@ü^²A6ky |ëµ‚2ÃŠ82/Wñ™æ ½Ñ“:z]ØØ…*<Þ°;·rß=€ôZ¸Û	œFÝûøûpg|ušOB*Þ¦’mzê1Kþú«³—§¨ÀñtäkRûu›J©Á+‚7ƒÞ·_wøÑnŠ¨\!/UˆûªÇÏ-/=&€ÊìÐ–É­Ê>ûS`jH?ûBâÔm(Ø?S¥Æn
êò"†ù<	Ý‰×'è>òÍ,~')Ô@Ž „'(QÌKë©<í©Om”ˆäò©PI¯‚M¹`—ˆ™<ˆm! Ã¨BÐ.ò+5+ð¨Ð''¡
p³Þ¨hE,–o†ò¨a n˜Ã,Že‡s¤cíxG<æQ{ä#ó""’t@)áWD&â@@@DT`ªˆ4@)‰\d  :.’tü@D@ò‘<vò"£Üc)–D¦ò«ˆ*[ÉÊ ¸2–°”e-iyËWær–º´%/q¹K`ö2˜¿¦0Å"I]ZDìéãOÙLhFSšÓ¤&+R
èÃ“Ûü¤.Å«j*E” $¥ÇùÉnæÑ”Ê‘å1a©Lf†s‡ÏL
!p†ˆ¤Æ9‚ž@„*(
p
î1G|ž†ÝÓé‘T4 
dXè@p††Vt1ùÈÇ(
’D@!£µH8áQ¦ 
#=M6<R®a¤ÿ€@jìi€äT§9u2	ÏÉ³™ÈgS4"T ‰V•y ‡ d`ˆC6H¢&³K‘GÒÆ †$tò8ADâ092p\µIê–Ú³<2ŽŽKMsÒ8 a>sxÁ°Æ°B3¤`dP*Z\b“Ua
\
và†§™!,F W5*j‡é™_ v .U¥%b(ƒFP‡4$v
ÝÊ CÀdÙZøœ¿ø N&¢Êq:ž¼¼€ÜËZüR à¶!®K<ÉhK“ØÌ-=Ñíx{ ßæ&¸Ã5ƒx]à7¹ËëXÇäÙÈå3!¨îu}k†ä
ý™OÆ‹Yú¯,´ONêÓÞ1ôö0ÙÝLäÛÝÿ´!@(º/lé&ÐÖ¶ÆuË
”Pö† µ¯-/ï üÜÞüfºÆ°n7Ì\yŒs é­¯Hœáw˜‡MQq½S›Øµ(ö×lü˜°
mÃsØPÑ¦¨0FíhËPÜ¾¸ÅÁB¥2 „ˆÀ#yí@ºüÓˆ$ "è‡@>éÓO&RîId# rÎmâÎåTç;'IL<ûRÏÃÜ3==âS?/3¬U>Ÿ	ÝL„¥‹I„0¼ñ&‹K2vñh&"
”VJ.
i¤cµ=M\Q…+h4e˜iM›rÓ/ít§=µó@~Ï(‡¤tLM"Ò üÀ
pBÌsæZ @Ð[ˆOQM@+eÙj6;í‘J¦&×éaCÕ3§Á«Hˆ ªúJÚY ðHÄl‘CzÓ‚fE«Z¥‚nÏTLy3Â¦œ¯zJ_§òW¡¶œ3lUB ZÆ:²V™le/;eóEx 2çXbÆÚ¦…Â^‘qz4 Ž{|*†®o¤Kª’§fœ)WÅÇ5ÌâÏ xæjèxÍWîãý ªä×Öã•³¼å |ùË~~w ÈlæM’RÍlŽˆ›0grÞë»®ã:
g>‡ÝZÈ´‰þlB': ‹Þ©O#:ÑHOšÔØ©=9
Óhší:å’Iñ€tÜaï9µºKÿž#´Ú¦‰µ¬yªœh'åÖƒv¶R¸¾Ã^ÿ:ØÃ.ö±£æ²Ç3ª_‡fä=böŒ~1†¦²OSCˆx‹%Vq*;¾™œûªŸ&0R
øÞ"Â‡ezz4À S0ÃÂUq¨éT‡NmCÜÀð4À!2sáŠ[¨p¡Õá²×ÐÜXwÉ(L
D †"Š`…)Á5ä·J\wðW8´s4Áøý5ð½zÎ;@Á¢¿ž€8Öš8É¢,ËÂ¯³¸“xŠØä8ˆ¬ïÃ
ñ·ü’@y€ˆˆ¿'¸(ÐÅ
+¹þ;»BÃ²Ð2‹à²ˆX:¥hº§Ë:4ƒ¥5³6FŠˆ­
¥8Ë:=òºÕ;cR
Ó*fsA±’'µã´¤°4L‰ˆ)ôO«Â­,´SCµHûƒ/Œˆ|  ¸‰4PÅ{5Æ
Ç{¼ÓXÂˆðƒxjºB<J6¶0D¢ÃÐ4<ÂË“'×k
_ã6¶ ˜+(u·„ Ý
D;ó=
ƒo¢¶ˆ >ykŠÙB>åc>çƒ¾³Ží£>ëÃ>í»Šî«‰
?lÄ9xÄÔ¢¦pžH¿õk¿÷‹?×è7pÄÀRûƒƒüÛ?¡û·BŠ-yp‚ˆ À‚À,a$Æ‡3ÅŠ¸ÇŠ¬Šó‚&S2Ór,ÐB2'ƒ@g 
¼ÆZô.ÂÒ@ðë@u|°ãI¬0™W¬ƒØ*X?&p*@$Ñ‚(0<1'‚'(#¨GÖ±ÏÂÇªÐÇUéÇH"È‚ƒ'°‚0R?$‰?+P‚&HóÁ  ADÁYAµhA'œ#£‹A¤SºdÃÁ2ÓÁ!œ: «
B93Ä>2B´
»bú¥±=e‹§štJ*‹Â¹k»ˆh4.€+<€°'*µ.®ä’-KÀS®´º0L55dC‡¢©Å‹ˆÆs<Z#» ˜¼&œ HZŠ=Ê¼(¶aóCÏ«6»D½ P½'”J<Ä§¤<ËÓÃkKÄ“ˆ€’âÐ–x8p9±‘÷)(ÄJä=ÀÄb 	Ä O¦ô‚ˆH¾åk>Î1ÅúHÅê»>U@­Vœ‹Ua>20ƒƒé™°Ì6ÉÌÍìÌ5Œ¡ÄÅ“]T?ös?ø“¿0NÌ4ÍäL5ðÌ©HÐRÎÜ±ªá	•”ÍJ
,ˆÆ¼
À¬í
ñN3zŽè`!é[•ˆÓ0Ìƒ`°8Ê*ÇðìÎåœƒóÔ¯Ö1ð1ìœ.ðLNÍÝË¨LŠ› Ü&´Kž„:ÂÌ: $=¡B£,JR:ÄBÊ;ë3¦TÂd3Ì¨d==¢JµKŠ4à†¬L…:(C‹‡¬”4ÕJvÈÊ‹ÒDÀ‡Õ‚6<	XƒÃ8¬K§´ÃA[ÍíË¦èÃ`DÓL
Ã´Ò¬SÑj’Ly Ì]œ”ã4"¸7ˆ%Æùp*M(Íí=Œ€Õ<MD+ØÅÙ,EÊˆ>Tì	UÔMÞÜ¾}àN~+N4ÍÎ5-7…Só3ŠäÅéüEë|P–˜Ô6}S
¬8@S:Õù¨Ðf|0(`O‚ã«÷"àœa|Çh°Oî Ôˆƒ4#ÉÆ€ÙÏn¤¸ÿÐ D1]D/Ïà	H
’ì¼BSEU3PU9ºP…A
ÍÉÜÉ1ëId“ºˆèAÊÕºs-ÂdKÌ¥´K‹`L­IÍ#½<¬4ËÐƒ!ýJòÂ¹‹²Ô¨$p‚!]KÝƒ&•‡'=Š8)õ3¼äD0E',=‰E ?ä<cû’puJÃü¦>u×ÅŒÑxYvuB25Ó ðT3€2Ü
”/a€Í°Ó  CLä…=¾±“øÓ MR¬ÍA=ÅéËMVTÔß4ƒàÎQyÖËœ.—=.k—èìEêÆëŒT©¹(«	i
:°VMrÆ1pU÷Ì+Á2NbàÑÚÖ	ûZ›HÅ\Ýé0Z_µ¢`ÕÏÆâOo<V‹#Û±â¢
ŒÛ—…Ú4
Ûâ[šœQmÝÐ‹èP§üPŸ× ×#üÁ7K×:"ÊRZW$|Ñ¤€W¨”Wjª×ÓH  5ËT8„!M"5Ë$URÖ‚!MxÈÊt@Ë·t5'}C†RÈK6*mÂ‰…L;âCÍûÃ/±ÄÂ½Ç¤³C#Sˆwô»ÅÏe<
 L°YœÅ·ÊkÍDJcÔ¡}¾¢½MCEÚÝÌ>¥5™FuZ¾Ê^3ÐŽû$T«LéôÅêl
þõß]=Å¸Ò°À?ýëœîmÎkeU´-¸XUU¨Õ·­ÛÿÅÛ_½Õ4bíOÀMÖ¡sÆÕ*P‚ë*Ð°Ã±Þ^B]`—<Á´.;È
Åbá(pa–
±—s±îàï°a#(Aæá˜kŠþá~ákV#¦EíEâêPb&†ÉÝÚá† â»­ãšE
î_V`X¾%~ÉV(fÆ	f–¼a0–I5h\lÍ£…\P’\¦ûV½ÙŸWªÓÜ«ã\Nrä;bQ
]­dÅ¼Si+]'œW'
‹ ƒ €Æ¡À,ƒ @ƒ 
.€UÊìÊL‰ð¬
€>ÞƒT P†F* $ÐåZ†ƒ  ƒ `ƒ\Pž‚ È‚ °›Çx$/€hvž çyŠæxŠGr¸tC¹„Rº,^»ŒØˆØ6P=“½#¿ÄØÍ#¶‹Ž
´xÂ\HÎ#Ò´Öˆq»dD,ª“'Dà»ý§Jƒð]€ ßKÄˆpø¦ôíÙôhU÷¥Mø}^ÅÍU¬ßÞ\T%ÉßGè66è(>	’¹ÚM-ài.~ƒÞFÆ æ¿~°º«ö´`µÙ#†U°Š[åà®¼Vá¾-VÉ*á‹«iÖy¤
Œá7¿w´
‚æ^”`ºÊEu9à êÙcã–Îjg„jfU/geé¡îbÁõ°e=ã‘?µ.h¶öã­>‰<Žã&VAtê?î£@æV/óÖÈÁŽM3EJs½R
Ó£]KnW¥Ý?#ÙA›Ñ¿öa¶ˆ¡@; æQfÊlÊ¤À hUh4¶fDé‘×ÒQ ,Š§xå h‚ÓnÉZîæ­rßîí­ÂmaFeT
Ý1‚ ÐØ&
àUXá‡†}X¥0ç¸ ¥èçu^^El^LDŽÞA³º<¤^=Êç&ÜgEY€–‡£:Sb1ƒáyžÀ	§ê…Û[ `h­ÂÄ 	AVß±úåŠTŒ6ZFTû½ŠŒ”‰
ƒá©ŽùÆ’’–‡“^ÙÎZùspø†ðî¢ƒ ÀIœ3I“©hk?rÆ²ˆL	:ˆE
 * ‚GÏîM~\ÁN:Âv:pý<Ä¾ÜE.ÑÍelt%ruÙÈ†ìïöXMvANž#b¶ˆ@‘eZîåV%‚ `‚P®e×mäž­VmÕ*€Gâíß>ó­šec3eÝ1e7 f,C”@1î @”¡ å1_îÏîãÏÁÈùæà
çáç9L6ê€Bäçˆèç=ÜîÈXxönóæDF¿ç‘ï&¬t#Ìõö€ˆGÒšZ)‹Ù4 ¶„8ý3L á«¤ pÛì 8«
±·Ú8+5¸ƒýÇ$
u¯}Ùp­8Ç^_2Ë°•ÏGZ'ú»Æ7p>#ê‰e·õjµë±²‚
Þ_3 @œÔ†i«O¹Èh1xƒàa^w2:1Üã
×òh×ROÏ¦¨$Ã(wcÏƒ¦=E;Û‘Æ90‚w|Z‹¬ƒ&ÈÀ#ðwG½Ír'¸ýõvc}øÿ"©œ˜Š9€ž U{G1•U øê8(Õù ë k£7²vƒ½ø+PA#Ü.™Œ:Ñ®6‚:°ŠgÐÐr²×'¨P ™ïà;¾ky W˜ù	7k§W
ÓUº~ƒø 2i8 £ÈXñùðzî}*P‹!3ƒ2¹4Q“~Sˆ•n(˜‚^žð húYA7%¸jB/ Ò>Q|€Ÿðï’X‰Ç)šýQ Ýêˆ	Çí‰•©‰ã ‘ºO¶i«ûÇûð¶xc0øÉÀ)éàü©\ÂW ¸–[á	,×wƒ¥o
Øýh9c‰Hø"˜xñüÎá/þ“°ZÊÞ(ý+Fk«{!HÕÖ7|êÁÝ·|Kaµ™p›¿þCA1½ü|¬Ï}³ú÷ŸðqR P/ƒRµƒ„“2À¸&œÌÃ!4/³ÀfØò!»A†¬r¥7 §ØLÔ¦{d0’)9É–)ÙdKrú³I÷ÇžD€WÀ#ô1Sv¶>ÇÛr±
K„â (Sl
”é³Ù98Ì@ ¦ìÔ¹\fæd[ Àr¬- Ì9 øÙvY.#e=ð”å²8stÐxºrO S:	K\,H×y8V—:=ñäbYÕ3wHäºF½
„ÔprVÜ¦pq@B(À8,³Æ#*)ÀfY<e¶À"\à”À:Ÿ%|˜¦2œ*ÌÁ+Q ßL` Äá=#ÒÀî^‰3€&'"H+à0ŒÈDÀD€2PðC
|ô/j¹ñÆ€ h ÜŽÌ§[âÞK@•ð–8°‘ÔÀÀÉ Å¢?1˜Ù„C
‚ª`"Pyt`TõÔa5n‘È8	>Äéµ¢g ¨¡d¸†/£<]ª5rIœ4„'S€±A5b:TPêÐ?T‡p¨,¦@øˆªÃn	ö0áN^€ðÇÝe°ÒB€,¤…¨lÂYXbAÓ{†@¢NÃj8ª2ÌÃ‹[Þ]øÂM8 ¸ H)@°€`ç”Þ
†Ä~$dX À€2´w
1Ø±D€4‹!LœG2‘&zp$ZÀ?"T 
À8daÜ)„@©ÁêˆA°L@X
j"~¾{X$È8yóa#æŽ8A"‰‰$Ñ$¢D5 U N¬
.Q)ÆÄ™˜j¢7±%îÄcØã¢
 Š-`ÅP"GòHÇ)z	8Ôžb<;
ç`*VEã6¤z“/VP#à÷ž a‡Ò°’Ã85áØÑõ#BlE=a¨À¼U€!á.ƒË>_¬H>{!8¥ $P"Æ Xx9Sô#.ðžHw2½Ñ*ô#/ð¶Æ`p5²€ 
¨ˆÑÐrEk8žctl,Y1fÄT…b¬ž¬À)Š1"@Å£ðJ UTP,IV”Æ&«Œ@Æx
±Æ<þ%ÆˆH {A–QVH50‹|“,I¬)ÞÇP–À~„ŒÎ©êí>ÓÞH£iT,«¥<ž„Åh¼ ƒTPèc6*  	òàŠ
 öCžÇü88$qÂ¹ÇXT@H‰ ÅøÇX ë@ÞÀÓEId5§ˆ"ÍÃ%X‘²ENFè† Ñ£8P ë±ArCó7eä˜Bà
¬Fs½>$€L
wÀÃÈ‰ÍxŒ
êH¢© ¦äbrÂœ·Á%KUÞs‘‰1L&HóÐŠ$”<“&RdÈ/
ö$Õ“ïÑ4ÃúøeÎîn I%™Ÿ€“4“rÂÊI!¯@H””q¬$”ë·-¢gÔ‡_1,Êài‚8f`$&Dºhúr¢[ä‰È'f`øbPl#À´€É?)$¿8(“ È“…JU¹A„©ôZ‘'¤Jp¸*±ã¨z•6ñ.¾Ä¼h+ãb®Ü•~±WþÊ
,Åä— ÅÒAŽÆ·5#g•×D ˜JÂ7¦¤Q„°qÂm™#ƒNì»nä„– )ïä’ô ”µ?¾$ƒä¸
’ ðG /ßÆ”Uà	ØKE)?vŸ¾ô/ÃôËš`	ëÀˆqEaFJó`¦ƒD˜|#\Êä0îž$Õ“˜†2>VÌ'à&3¦öØ˜ r_~L9FfÞ{™Â,ð‚•	%[æÛ(šó( Mˆ	%³J¨T BÀ
T`QL È—
 6¦…Û˜Mo”.¿±9Öà8ƒ“qTSg39.Ç~ôADtœŽÕñ"¾Ã)°Á@wŒ–Êr
”¿‹õ6æŸ§¬–ÒNŠK<ù%¬AÒ¤zirJf'@°šfâ
GÀ\BIµ'+VX]Ü…sZâCiÙ*ß˜n‘7à	¿ ø&ç’d—“ê½@ªIð^@¬Ž¬Å„è†˜HYz¥cQqÀ± Œ"'t”ŽÝ±3^ÇV©7ù&èÜŠ¢suæÇcà:ÇŠuû+ì	”çi,gÔ¤zo._ÊNÚy6¹ÑíÌ
¹“5òNßYY€'ätÄ³±8¼ƒgF¦ çØxÒ§o²JÐhšÚá„™GaHO8eÅàL’SAÐDØ'ù§Ñ<
Ó€qŽLY5¯fÖta(&	ì>‚gð4_Ix
ï.4¼ã¬v€ñl—1–?çò!3hül~¢*xÐg9î§†YžZvÅj7á`çÛX¡´…zXœ¥š*,úFÜqxg¼ ^€xjÐž±|è§Ê³÷sZÎ‡y×ZâÀP¬wå¯e¾$éŒ¬uF\"Œ>˜øçF„€ˆ?Y’{@¼ƒñ´ƒwè—0uh+ÐC‰S ex‚E²ÔOT4ñáªhqàGÃíOÂ™	 AaX(óKlƒÀ=»àÂ¤œG’JRƒ/»Ä´Sk¥'PK!œ@
¸@XöOq"éÌ+Ðèó±„7p à€X9CÜ5œ: Xø@¼ó»tôR@üÀ¼+¥s`(’Ï6¹h8¥Tè€I(T  ”tIN€A9I)X  ÙÔÃÜ ¨ÀI=©»›r@
H— döü4À›&Bžw3£*L–74;‚:ÙH
¨y¸ ï4=˜6› 
¤ø)›©8NY@ç’<þ45/šÓøJ«L£´ž^ÑjEY¾Ô›	ÓgM â@	&ÄhU‘µˆA(¸UÕ'2£g'í& È,wskED"P'¦B¿—ådîÓ/ÑP¥izÀ‘Sr ¾€‚êŠ“@.ÌFúIT*TY©‡iT¢¾ ŠjQ­¨èÔ¨goqLŸ\5RKjŒ¨(¨­Ô–J#`êö¡u4õ/¤¢RÅjw"«öólö„a`
*‰d§`Aª*¨¢jjR}ñg@(À)ûêQø« (XÕW…UógEýœ6£ª€®ÊQAƒ¡
«$•®žÔÃÒÐª*z©üN¦Â·:·P f`>øÇ¦†€¼ºWêBÍÕAFP«‰5åÖ„ªÎÔ¶BÉÇªB¯jV­¬ßñ„fÖ¯J·Dªg5©e5´ªT–JZQLíU3•&ÔT:á"FÅ¹£ÒèO¾Ú[¦8ø­T/ÆŠjºM«Cm
ö2,Vq° ü)ÝQ¯ñ•½ž„i#Nï
(5=!DÆOI[Íƒ¸¯ˆ3Ÿ*TTª
ì±\{!oj¾À›¥X¼¨­²Zƒ@P¯å[Ô‹Ê[öE¡è+)‚u¦q À‚ÊµgEá!„•°|aˆ×Ð™?/l†••m/Â¸‹+ue_ü‹"6¼.Ðñjb‡jlzê’ž:X}_…ìTM„ŽÓ4‚ˆ) XÅk% KVAñ€Ý·9/ê²Ô-# 
 $#àÄ,P7ã¡Î¿4\æ”]’Võ±ï)Q¡(Âs¡
¯ÊPñi;Ë]ùì‰º“µôN aãçºK
n…0¨éCfÍl3
²6lL=Jgè­Ž™šÏÝ©Eíœ5#ÛÇ¶ÚÙP, ÃŠø…6®º=^5'Óª‹°•á¨âQ‹|Pk§õY‡¢¦RUÁX
f3Ôš:ªF¬5ÍnÖA
PôZ Ùl±-°S3 Ž€
³Ä¤,¥²ÆJúŽP©eUlˆè²š	râ®¾—f•l5VÎ²P>úAK¥Ý—¶ÏžOM«>“Š€,­¹Jà°€¨
rA–: ¬aiS+»ÜRåÜá°­¯Ö–êí“	FÇíuY–…mYn ¾­fzB °ÙqfSg+Ô§öK8Ü±"Ìí«â´?ô…RÚ<ki÷l2}·™ÐÊÛÍTo?k}È·û¶ßö–à]ó…M(U¯Fl&_e
kÃª`åzˆ*%î²5O7ÖÌ)F¨@uJ¨Ä¶êØãW=/î¶Í0§q‰Q1›1Š€È·T÷äØQY.º­³AtÝÆÜv;s1íŸMŸ^àaö7  ø8úUr¡Ò`5Œì@)
T¤t0]“ët
lÔœ8d¶¢R«›<”4W +*€»nÃ»—ŠÝn;P¨@B"e–íªN·+y)îIàqvîúÐtCÙ­»µ•~}ÚA ¶¯µ
²cö½
FÅþ¼˜wòî¾©KmÛlÅº‡2±ø3z›îQÈ¼©a–‰ÝÓË<Å!—
PCÀïI1ëëëU³kVú2ÛÙ+x˜íÝ+-7÷6¼Ý«wk.ß
´CÀì™¯xùM8
¸pbr”Çp˜V`¾Œ¶ÚXd{õ”íûýºñ×ÉÆG$XEÊÄe²½ÂôbÜ²{=Ÿ€
Ð®‡pÊp
{)ðÛ¿–`åÖ_º;iÕ-ží
hSÏâÎýû{+ËJ“µ?ÍúÞ(šÎ}ÒÕ¿à·"2p€
ñÔö*  k°*`CÏð×æ[b!0ôÀ%×ùÆ_Ü:Awk½Ü·Àêáz{÷hÝ…¹5x|êßÞ
oo®ß-¦t“„ZÇi"”'uÜ¶xúÆßó*)‰m´ñ×½F0¯û%>Á|MÄ`aÇ_pÚ ôkmà¯—"èH	(LF€E¥¨ ÀbÙÏ·9ƒØ
‡5Æ6ÏüŽF@¸,)˜äb*

l)‘+,¬‚«Ìfãæ,ÅnáÛSñ§a ’@Š?Qà %¤’„}Q#Ø÷oºbøŠ‹ã/¶}µ…Gf¶Îæ
ð.m‚ßDã‰CåHx56)~$‰Œ‰À0HIŒ¸FøC"/¯3•Æ85T¸jlœ*E…„â %Q®ñæÇñw
>ë©
y1>4Å6
cÂXŒ±BÒ­'©	ÌÄùx
áñ3F³*Ø
Óã;md…5ÖØxÁÕ`nÒÀM õXAEŒj\Ž7Ò96HéX!­cƒL!*tÆ~8?ºd„G²>&¡SÒ¼*rI&Ù7Ê{qödÉ@ÙAZ¿j¼#™²oâGÛX;U %Ü …Hø(Xe(©õF²LÆI)VÄZ|²y@ËTO(ÇŠ@”ËéQ6t )›Œ¥L]B@5«Êvi¦×íÛ’
l FÌ‘÷,ØC_ ]fY< Êœšá&­7t¿îåK¼/»ä—„¯ 37+*ñðƒÕ--¶*¼X‡ŒCg¬†•7.zØË+},| §ù$¤fŒ+Z3Xœ°¨26ßÓÙ\cu"¶Ä±Ú7wË
.Y2ðÍò`3ÓF.çéf´›¤rÅ&LøJãïæ¥|ÉG^šFyž«³	ü|æ™oø<±¹Ù&stÃÂñÙG¶‰•ã™
p@‚Nh<«3*kÏÙ9aêe£Üý2@fQÀ Äg²éé³[H›÷ym–MýÜ6&4É±ÅGA<?å“`Ì
tÂT š#e7ôÆƒÖÚ*lh´iŸË ~ÆÐnîeâ~¤žã/:ÃÎ$ZGƒhyðšž{æ&úR(è¿¬4Æð®¹Àæm›ôz¥
ÈÂãõC+(-0¤
4ß@ÉJR ¸FHˆº¥”*#]%úr’ÞKz-Þ>Ü÷‘­tuZšD§i¾ÌâØôK(f@ø ¢\œ4”¿úÐ[Ž *]òtüeÏåyK§KÖ¥51‹£\@0"ÅŽ”Œ-23ÞÈo“?G“t‡ WE³}§ÑÔ>>ÒH¹{Ìï¨óB
Ô6JÓ€)}Yæò—0Õ’<÷è„™ ±eîÕPò §ê½ª‹ò‰^Óêƒ2ÈjAMœ]¬­.Ô ¢àjD­«´‚¨^óQPË
ž5ö|ŽšDû¼@M«¥õk¦Ö–ÕZ×€\]7_ÙÂ”i\+¨-¬“u”XÖ­zM§hÒáVL‹À‡Ìw[ƒ _í­Ï5e¨ ÛA"7d]¤•õ^FÒ »t˜–þœŠ„öuÌ¥º:;[…03*-Ö4ÓN¿g†ˆëkÉ+QÎ\ïkJÑ¯Õ´ž®3`H8h=Ÿ£Œ¦Ñ¾1Cã}¯A~Ù›_Sl¤\³‘dÑcSg‹Ož}¡}¶Ì0ÚaCI²Ý¯œ¦²,ÛÃë¢ý6ð4’VÚ\£I¯ë'Ý®ó¡µ±‰újS=l±e…¤þÒazLÏ€“®ÇvÒ¶Ù\ãMÇ€8
‹—¤×N„]àdí˜´×´ÒîÓ:]×jã¥{l¢ÞÕ`p§‡F­¯!uÔ>òJTêKM5õ2vÇz?Cÿªu
ÞîÈ¯˜XWgT¶ÅöÄNÐ®ºeÄêÇ¶	µ»ž×:^Wn]6‘†Ô"øm°­¹g÷Ñ®ÝÍG@ku}*Æ/6ÔØšro²‰7¸Ö+»:—kå-•UÐ¬†ÜÓã¶ x­åuà{­Û^“êËŸÜ–{{qÛì€M vÁî›d
ïÃ]¼©2ü®ÎÇGv×oÚÍ¬i6þ¾Ø²
'hl«à¿iün
Úû['Ûî˜)8^
à{'BÐÁçñœÏ|JÎ 	NÂ´&
ßHÀXHd)ð¥Õ_-æÚƒ{=‹ðÍ™³Ë&Œ®Ð3ZjGý>uê†µ ºuðôP{x‰NàþZO§h–W9bê¨€Þåû#žï»t5 Ì<þÊºðÍÄ™·ß{šUØÚ½Õ ¨
Ñ»8›oÉÝ+µ¸
ð‹ŠÚ‹i> ÂÅxânÞO<[ØáQ< w˜k7Ü6nÅŸåùî±s}Óñ`Çkx~Üàò èqûíÇM†È“ƒ¼×Ÿ5n#‹",Œ\Ž_–`:ëøäæ¡’KÌí­ „5O
_&Œgò2®´£¸åèU\w«miÉ¶O¹½ãâ´¼)ˆè%ŽË¯ÜëÛh<¨qD>Êyä®Ö½»‘£òH¼=vü½äô{
	™§sy3/¾N§kB y(OäÓŽWóCíÈk $—ä‚›’Wç;€É›øÌç\±“£óOXØ¹4—ÞW<Ž G®Ê³9ÓÚÒ‚‹lËkÌ÷
ýVæþ8Pã¥¯$‘èò`xë)IRx×®Îò€»pŒlŒcø•T=skmÍ©[ôÓ®Ïj³8Öèì©Q÷°rävôä
Óøwâe<Es
y±,¤ÀƒŽæÔÛZÓ m^É•ø-ßçâ|™õ%Ô¿E¸€> ¯ó¦~¾U9Tïè.›ªu~~Õ'Æ¶ðôÂ« ôCÎÆKy”Ötœ¬7t(	Ë/ø,ÇëTï¥ÇòoÎªÓº?.ÛYÝk(u¦.×zï¾ëw\AÙr¡+*º_.ì(£k\o.ö[Wç <¬Gé±Õ«³Yì=œ“í¾mØ/†[ÿêž]±Ÿïº~­åñ}ÝX„ì	Àåö”¬#Ý†Ç_“ÞÂ_ø)+
´ô¾>Vþú^—éN{‡ïl›žŸƒøçé>½í:v$¤½ùd7ãHÝk„a»(_ì²½£CvÀÎÝ±ºe/ƒ³ƒõ×Úíºh¿Dû¬óq2®Öq†j_4a*ˆ÷Ö.Ð?;<í½•óêê¬×ƒõTÎà»’ õô^ÕQ;ðî¼wò^Þ
¼=wåtZŸ£u«NØS;{ÿîî=ÀÇuò.Öç{G·ï¥»Wv¶~1XÆï®=ÅÓõùÎÐ±»ƒ„‘±·«põÑÏs|´¾ýžwŽÒa¸_;J.÷Ï¹gús‡ÚÑ§»ÞŸ«û¨>ð–»:ƒ²í.áQôZ×É‚gHŸAÆ{?ß¾¶éáA|Å&óL#Y8
¨ñ4j¼€ïð<´Ÿ÷ß‹º~WÒ$þbD°±çQ<›Çñæ=¹{Ï7øº“§z­,™‹ùûMáù;8ó>h¬ù¹ÏÝ¼÷Ž¿¤]Î
úÁNÙG|Œ¿óO#jÔEŸáU¼£§ôc½_pÓ®¸¸¦7ôÉÑ;X?àyw
(ð³}„“tÛN¿<² Ò‘ž{Ópàã¯×ÜqÏª*` F*Uh
¸Ñ 	…g×*[@wv—³£§fð¹Óò/ŽÃ6·ïÏ*y%í0uD²z¸¡pG>\Íé;ŽÌ,¹7Uúš˜¯A½Î“ø“‚f9	$8±3Ý
–"Öû}8œ
ú"ßª2–
$DK›m,s¾Í:¶[†XéLª³ÃÇŠÛV8GXwnÐ
µ…½ø*•ÆnØZ~¶en>Ô!¿Ë…nP`á¤,ßÎìr5'Y–œóã¯Ÿ’PvÚÞ|q ô´ážÉ§!-eqŽ@IaS31’b@¯|ÕßãŽP ë%·½{!€Dëx¤¯ôÛ#á›’W€yó öAÐ@~Ü½ëÏhÞç‡}6‡¯rßHÎ´_`/°Æž’3èX7Ž¹»³fÒéZìÃs¾ø
1àö`ÁïCÉÁ?¤@H2ü¤úòS½ÄŸ1½žv ;]uoå‚qµùç+
4ð'û	¥ AÄ’’Ÿ\×ýÑoÔWÅ.Ÿâs z«þbnû+å‡ìû‚Ÿí›F)ày~åÇùˆ÷Ïy¤\?Ê9¦Ø ÿ2oÏjÕ_ü'?ñþ (óK"àzµ?ž¿Š?÷/órBRHX!„Jƒ¸ëŸúû0—MA—Š¾†Ù§ígßå0#`Ä£8€@€6ˆñoø
•
@$‚š’’åÏ2cý<Iÿaœ„ ‚Oðd"˜Åó+°|˜·Aüx"$‹“/w›øB½a¯mÓÖº? )_“ÄÚSƒ°DÀ7NåÛû~7ÐAJ¡Ì?èï_Ûý& )ŽÿË0X@/‚S«Ó²Ý'û©R Õ'$º [€:`õƒÌ(? °À
Ãt+Ÿ€Ó ã/4,TúšžnÊ“Ð ;
í¦›Úr:åh3Ñä¦)`Þ#÷ò°¼éÞÛ›ò=ÞÍ	Œ¿¾¨ˆä“&„÷Š%(¾Ä2#øÀD&3øÈ)£,4ë˜Œ )d˜J&m Óƒ)æ2ªZž#@ýp^ˆÅÁŒÐ- ºÎŽ\Û¬¹zÎ2s‘¼P¬çk(AKÐGcÉb€G05¨f8/+&02Š`&,
¨…'}h
P® #@8\£)`	kUVÁV°)€³B%Ù‰¸rx6%Z°^zOÁ8àt0«ÓèW9w”«Ì&À¢(˜ÊÚ¡¨„Á*Êbã/u(S
ˆ¢Y^²-A0à(š %`¶ ã/KVH'«QjŒjÁgpŒ-AjðûØ®6« Æ™µ~°Á
¦y#0Ô©¬’R¡' \’ÎA -T“\ÞGü ë%iðkpB’& 	@nîA ­NKÃeÐ`ÝÁ€ž
Bk!ÄUBl´"`š_$±JÂÄC›Ã”"öÆB·Z0$Ìž€	ÀŒž ×­¾
A ®øAÁÂ%ü±Æ”iÂƒ¬Â	µA l'ô	Š¦p)‚
-Âüh*tâ)uð(l “Bx°Bz
£ÂÄÌ-„’ªB¹‹%Ä
ýÁf+d¼B‚°«

±ÁÍ!',
AAkƒªÎ.¶Ð<Ð
©ž&" b ·Â^€¯&ÃO	²ò
q(Ê
0„	»Bð+4ç€k³
Bn02IÐ:`ÿüßŠ¿ÎÐÃÐe¤Ë00T
ýÌè×Û‹Á“@»³
#«–ð%IC™p<
SÃ„0d
ÍB9A6nC° 7L„*Ã¸°7t CB«i8Ä =Ã‚#9Ü
cÂp&|mÂ\E:Ô	Cú0@¬ X&N°Ã£@;LvC3Æ#ô
ëÂ)`
˜ ëAñHkúÂòð/Ô
CôÐ4¬CÕÐ=\N«
¤ ‡€‰P ¨Y9æOÐ·ˆã/D
AB 1ÔÀ
D…/Ô ¯BÐ°\GÃÁ°4,DÔ=\
ßC
qÐ" …BâPXV,Cÿð;„ ‰ '  ŒÄQ	ÃÏðž

_Äå0FlÁÂQ,”B|C8á®“½*It¨±î0H¤
qAóký †äú‚?¼D(©Å‹öÁ1+TAæ0=tiDèð&¼[CÿëBÐˆ6~Ã§:ö.Âq
¤ ðÂŽíCl
z™•0AŒEÃåJÌÛÃÄ,¼º+ÇÀ=Ä‘1ëÂ:D3 Gì÷Hµ+àø:¶R1þò†ÁJÌœÚ—%D›ëzDàâc±R>Šoå³ø0¾—¯6ãør¬K7³ù:Ä,€V¤ˆL¾[q¤ÞX>^Qã[Î8,çÌãã±¾¥Mñ
èå -Í6ê»/°Ê
¥;2n:›ž#cH
„øö°ì%³ã¯ÏÈâÎ >øj]Òv¾0>Òº¸®­­C””°+Û»ª¹²ë³ÈA.O_ìc7¾NtûA¥è^Œ?Þ@Õ²³Ä,À x#87
,p€ŠqbLÔÅüh_A"°eoI¤üGVH>LA†Ši—x±GŒ»°3m7ò_40Ð‡¸pÑŒ›Šr±x¢÷J(¼	]ìÃbÆíï T
EÂ'·:‘F -
|¶À°éf4›jºoñÊ«	Þ&Ÿq\l(æ&¡q
,ÝÀêÔæNF(ÉSlðP)´‘ê‘>GÀT´ÌQìÌÆ©VÑ¿ú™@&™Hh"™¾ñêèûEñ
ë²¤AÑ°À­#Ž%‹zDKoEDDfg#Fžf¤r	Î£&¦y¤í#ðª	0ø*èéÀ' d4LÇø‹p”åþEÞ-`´'Æ" 
À*Â‚Ñ2c—>ÂGaUžÄÊÑbºû0ÊBÐ¹ž*¢sÄybC:!½€àÀ«Òñtlz¢/¤owTP¾/ÊGÛÇÙÑc´šØ/UT³‡¶Þ‘E\åÄ£Èr„‰ÇÌ1j@õ–ž`yO-é°ˆ«F…ùÀÜY²ñÑAR£Ç¤Q¸H5M@n¬¯6º1œº?³B0DâE&Ø p¤²NÁ±È‚?AÙQxJGö
¾êzÄ .2G1x¼˜ÖG}²`>ôQ@”µ<Gæ1täßöGR-åŸ:°†¬ C±
2˜Ë¸F ÄQÌªoÇ’%³!ã¯ÈñM!_ž2}QÈ8P…ôˆR*x!åÇB…F žÇMñˆÒPGÕñKð"ùE
2üŠ¹G
Ò{´ŠHr<œ|G&}œžÈ²Ñ‡¤WH*Ò…`Èù¤Z
íG
´iH¤oŒAþG#RTÄU¶Äìƒ”¦H H2’”$'IJ²’´$/IL2“Ô$7IN²“ô$?IP2”%GIl%n&\ˆ`
 '@°–¨$>	¦laMZE©à',”Dç”Œ'8YLI7.a~›`4]r €>›U…¤ø% { "8(VAF—¨$º¹ @°P”Éˆ ’˜šPI¾'™d‰	( Ð’&˜h²˜ NÓF–,'—I“®_T'iÉJâ¼˜bÞÉo²–¼+"‚õdU	áêÉJ"KC,”}²g©$"ì‘ž(Ï™ˆ@B2(“J"vë,br™D  ‰`•<:øÉˆà $` ‹Ò›<( ‚KbYY(]ÉŒ26±PÖÉl2"è,Ìš‘RšŒdl1™„'Ï£ˆ Q“%#Ê”r "‚rK¦Ä( ‚C "P®É™r È‘Ir²¨ d£  ƒ&•Jq¬Â‘%±Ipr @æ¼§	†ª´'€ÃB±q–É| "Ð&žÊŸ2 à "‚~à¤T*ÿ|B­,+G @‚è(·•²² $L€¹¥¬*€ @òJ¥2 $T À²¬\ 	 °ü(­@ÂP,Êˆ WæÊ€²æˆ ˜+qÊ½’¯R©Œº¨§¤²¬tÙŽÁ–Òœ¤ ‰!€²T*Û—  hI-—É4ªý™-sJÝF…q+K@ "ˆ J¥ò¦ÉmÈÊÅÒ8¬
oË½ò $0€Ç’¤ìrn¢²¬¤  	Àµ,+- @"Ø,•J# @æJ­²’È  	
 ¹t) ‚Ý# à ÌKs² $< ör™ü  	 ¾Ä-C @B°/÷J àÀK¥òË •¦J¥² $ž ër±® H º\,'ˆÀ(>"‚ÊÒ¹ ° @B
 0ËJ)  6ÌÅò‚è>}R©  ‰`¿Ü*… ¦,-Ë|n °&‰KËò	PË.ˆ¼Y&“ @b	 1kÌ	«D.·JMÊÌ1 Îr«¬ ‰+àÇ´0s ÿRÅ¬$
 @Â 2U‚ˆÀ”2+	& pœÌóre$TÉ³¬Ä 	!`»œ'pœ¨[ñP Ã† 	rÇ¦±¡XøI¦gÑ€< =óê3ÿÌ
Ðä3MBsÐ44ýÌBÑ<4ME³Ñd4ÍDÒ\4#MJsÒ´4ÍJÓ¼4%MM³Óä4?ÍLÔÜ4CMRsÔ45=ÍRÕ<5EMU³Õd5_ÍTÖ\53Í¦ÄÏx( 
ÉR—XÕ ±À2ƒ^˜\–Ø5Ã	^“”46Md3ÙT6—Mf³Ùt6ŸMh3Ú”6§Mj³Ú%§ …Å
"tÆäÐÑ‰‹… 6ZIíÆbán"1“ñRâ‰\  YÆKòŸ±lša`" WXwƒK :¬€S¢x–L8H§¨<0â¦˜#v
}óàÔ)ÄFÛ|n¢rÆ)9t@ áb™ ¨
#³Üü‚ÎÍ%ižÉd
dá(«hcÞäDz 
 ˆwƒ<lÈ  '*6“‹àbaL,‚Ô`jJ,¼±•ŒZÈ%wŠ€         (àA„       2   
   ¢×”·Jú5Åþëû§):4ÝWBC+íì¤|œKËßtÅcût/ÝB/°[                         
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\TreuilConnexion.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 ¿ª
Û Û(4{‘až;«hSÚV±íW_Ð×NÁ{y     "      Þ      T r e u i l C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             PCS          r                  9                €    ¼   01F280051n     q       à      >        
   
^                                                                ÿÎˆñ   TreuilConnexion þí˜á                                /Ìžjèf½gÞQƒ–NƒÐ`{¾¯ $¼2õ¶©ôfQÅuïje³Râ    Ñ  Ü9    
dJÇÀP@`ˆã
Ñ…‘b 
'*8`1ÀÆˆ" ø,‘<«Ì1cGM"pÞ¼1“GÌfèÔ	a‡f"mÄÐ¡c&À?w™Äyd:¤Iy/"*HcfŽš:fB”Ñª$æÌš7ßUcæM*pÈ°1SçhÒ¥M=>=I!bœ«wˆ©z«TîÜ’'—L%Ó¦ìY:9wö|¢Ž˜1mÌˆ±b(µjÄÔQsfk×•-_’5Ë³Œž7bÜ¨‘cÇ(R¿G–!â5oúÆ•€@oàÁ…{ü$Èá‹_dž¼©í"eK@ÝzHì²d÷(cªsðÂ©‡÷X±¢}ÆÕ#¯>Røoòà—TO~þñø¡·ŸÎ]îöèšòn ò3Ð¢ñšš(€3"¢Í‚ˆŽ°â	"ªPŠ'vc „k)êÐp*»*jŽ;¬Òød!‘f¡#ˆ"±¹Š³€é
 Î#êè’§ˆ»æPk¯2ì`)¼à¨c/4öÒêÄçHA…vÁ+™h²I&ÒÎJk­¶v
>à„œ!"–Þ¸=¬

«RTò+/ÅzÁ	™”xc'1Þ ã5¸Î<ÐÀûê
/QãÛ/A¹»N;ÿ"0€5Òá@À(½F=EÐ=C5ŽQúìS”=òímÒ¿ ä¯»ïLS¹È@ €2
 „	ŽùB×€–Ø xåuÁ0‚v£fÝHY€ˆ(
¤[m“UVÖp:è6Ün©uà(h;7"ØÄ¡–Zàdå´ZSý
×áhƒ "'¢Ø:y¬—U m5øà¿²d—M•+-V Ù,"ž˜àÞòY¸áþIªu j÷]eã­t z	TT.|…Ó—_Ûøä .õcšfŠƒOÒ ¢¯všÃ2²RRCn6J(†º€:ž´¨@
ÂéÑ#_¥ö¶Š<R•$—ºŽ¡‹nÉJÌLó=Úˆ¨	8Ð@Ãª®°j8ŠJa2dÚ%žBs	¦.Ã’‰‹XØÁ
;²Êƒ…¼!ívKÕ8”ˆ¨C‹**#¢Ïµ¶(ˆéG d‰:	0hßWUœ“sµ?­aýf›/O®eß‡Ëe†n*.$F6ä/(™æ·Õcu-™d€LÝÒzƒÏ¯ƒžåÉ ¢"t"›h£çÐP¥ð®¡‡¨{,øæj‹ÔZH¼&I%i¥|3›’®¤¶Þ )´!BDà&7ºÙmxÓÛÊ°—óa%‚Má¾ôÄ)ŽqŽƒœä(W¨µyï^šçÔº ÀS¤ˆéPÕq§u¯zrd§ªæÔn8·»—¤r†CÞuO…,3˜ò„ˆ°l˜Á‰K”
õ@f=¸`/{½	SzGEòÈŠ6?ˆÖ ‡Æ™!Lni|á£ ¨ÃË@$ˆ§!q{ÀÈ0àÃG‹ ’;Br‚ÿ ¸$3¢1+kLAaÃ¯¦4cb‹iP£Ö¸F;ÀÚ %ÝT.…i¢MÚâÅðÄ'!pBó3´!VIÁâ\yÂØ,*LÈY:¼ÀOt F*XÒHIžåj(C
BðË³à’7²Ò%·¹Í¡nwË›R@„4ˆ¯ ÉØ††•½qp`ù`mIÂÈméOÅ\’dž™Ì:pÁ2ð‚L0ƒ˜€#h4ëð‚7ÀÁ
UHKÄÐLƒ.3Åç
È(1´’mQ4ÏD›
°É,oŠÓBp‡9ˆA
¤&Å¾	D$BhCH×.8Àšmrà@1œTWŒ=Û –Œjn@AÞ€ó1i(=áŒaˆ©
3HEf4 #6Wž‚Žf4€
í•C­Q!|ˆÈ
vC¶Ä­#ƒJ\0×,Bv˜
ÞZ×ˆ0M”ZÛ+íf÷C‘‰¹3"c)µ½|Ñ¥‘ãX6™uÞ_rqË‚1 í¬\RP'Zñ$!{+ö´7+‹Hv²Ã#T¢ *Ø!xQƒÔˆO
™s·°3‰;æq ]Œ-°8ê/ ‚Üm `H°pNºå­oiÐ-­q£–LÍjZc†Mvò“X
%
È6¨˜2 ¨Œê*[é8XÊ’–ï¬‰GI	•KQA	Y2(¸Qfzá/0‚Mv¢!4
SPë`©I›LLåÙÜæ»ùÍpŽ³œ~Ë`ÒÉ’ÁáÉp LÏÜîö*¾
=
ªO~ú %hDºÐ†N¢UÃD#zÑÔhT-•ì(Kÿ"$·È—øfŒ»ëM—Á¿º<Ém0#<á
PÐ‚¦‚+¤t¥~2möƒ›öæòÂP	é@À,f2›9g Lœ,Î6<Ô¡ML 2ÓšÞ.9Ý©½,âS 
•¨‘ÑÊQ/jÏ¥6õ©QÃT÷5\5«ÿâªW[VÊ°)d5kêEµö°rýëIìŠW¥Àµ­µ–Ç^ûÊk»
Ö½³*,båclGÑ±³‚U³«$ªLÐ£švµ›’‰6„¶)‰˜ƒfå`Ù,t»´ÁQ­<X{=’½–S²µ6´e}’
Ïn*K4ô‡Ž`
ìÇ èÀ€F(7 ÌˆssÀ¹½¼`N¡Í"„`
f¸ *àŒ±|¦ÆAuÓ  ®„Ÿã}Aùè= {³Ð´‰r ¢0(Š(‚À“×Û›é…ƒ'Aùç¦ºÍP©Ä5!¨ÍòÁh ±8É©FŸÓÄ#P§ŠÛÙâÖ¤„[ÙËÉëTÌ‰'ûtæ?Ë>Ðìà	
oÚã ‡9 eÉmI»ÊÀ¢˜a¼tB‘Uš¬ò Ã÷$2‘æ4÷:aRªåÚÝÐö¼=î·;Þ‹²÷:ù=žp…‡=ô“’@ãg æÆ'®¬$PtX
QÄÒx¶#Þ™{_½†z^%!7[žóI‚ alÒ-éKß›â÷²™Œ‡\(!{%CÐbÇ—÷ —G	¦²ƒœ£ÜFè[Ç{@À3EH^¾ó˜Ë|æ4«èâwómr³þ¹^_0z6 }%1˜W©CÔ€šØ%pƒ·s=JÓ¸ý““Z¾æk‹çcûC±9ð“3€ƒ°Š9p 1˜›¿û¼•£³ˆ(¿<;³#è3ø³¡k7ïÉœ¯êœ°Uc
V;³zµˆØ!Ü	€b
¢ÃòÁQ¬¦p¶e#Bf{6#ÜÈª%4àaÁ¦0mƒÂÉ*·sË"×â"šIL‰hŠœZÁäx™èR[®hëÂøB¦CA«­ºˆ!Ð¾¬ÐÈ7=`o €~(h8‚›.ŒX Î)‘æZ¸­
!x¸ˆ›¸Š»8ÎÀ	åã8¹®/Ä{9d‰:?À€
¿ˆ—‹¹™«¹›»%˜‚*°#h‚&0‚Í£Ãã¤ž[/³HARù¯“X©·	>¤3¥cºÒ1¨û“Û›Ml
­¨:
wÊºó‚c>Ä
}"(µ‹=±û@9{2ùˆØdÌ
îS$½C<ðÁÀû(¨èšx;%qEz¦­X=£2\d8¨!M”ÅtìÆ<‰ý8%ÈOTAù9”E†r¨2È­9¨‚Þ2EJ+ñØ#Ø	!(šÎ¸’´û±‡²é4ù“Š°Ã»
µ¬€½ÇC<,á<
ËÇD<‰þ=Ä;«ð-™”>œ’‚<È¡”¿:
€6!)9A)õKÈ\¤B©tÁSƒÁT«Ò±AWkÖqèA{y¢¿0¬ T¶#,B$LK´\Ë³t7Øâi³¶'”ÊCákˆà)ôj‘•LPƒ½|4ea­|Q»ÊÀ7¦ –}±B,Êµ,|¬·Œ¬&¤Ë
2
€rô>ÄÓ` ¡p €0‚?œ™=Âˆ= ¤ÌT8jËIy@F”8ŠÛ
HL¾J£DU¨ƒ
¹Hz$’ÛÌ¬¯¡¤
^r9˜“9š³9XDEUdEW„Eá<G±
ÈiòFv<‰ˆ:0¼NKŒÐ¹:È¹7¨8Ë8ª9 ƒâ„
·Á–/!¨ƒ¬(O«{½lœI±s;¸“»mÜ<	»I¨ÔÇ“/P€î0 Î(
òäŠ+9O¸SÏ …Í˜ŠE²ŒJ¥g
§ÜÐ
hÉJúÈA‰ƒ˜¼O¡œP!G)ÐÅÐ6è€Ðô\Ï“
-Ð
£› :1ÑX§<9¬s¥­2Ñ¬˜Æ¾a§Œ+”:
­˜Qx&Œjƒ“:Ú ¿-Pžˆªßœ¤j<òÊ$
ƒ«8¯5zÅ3ƒßz¤Z’<þ¬$grÑ+e? ËQ`É ºI¤4b¤:¨Éÿô<ÄR¨PQL)ÐÇ3
T|JAµQy`ÃEeÔƒÊ¾cºÓO<‰¹Ô==H¡ÔùU8¨qÜ9Jm9F-Ðñš¸3°½1Qš¤ÔxÛR€3J5DFÕU¥[]ÔîL¨8»Î” ,ÈÑÕ¦¥:ë &R"Òxb‰X-
%mFœpÒ@IÒÉ¡ÔíÜÒW5ƒkõ&ZÝFJ5´EõUuÄÓ“ÐÁ\}¿_…Š7“×v?ËÌWò Ê p¡‹¸Ê%¬ÁBC®ÌA¯äAÂEA¶V1K#|XµlKˆeË‰mK%œL&ŒËj›K}…ÂHàX¹°kéRé‰I Ù¦Ù~E—êa,L7-\B.ô•O}M•m
2 3Ü*‚ÅXÉºÙgs)8”‡RMP«XP3hPBQ
Îa„7 `°ÓÔ£·t.~€»rÍA¢
}G#ùŸìZÞÒ
¬BPÅ¥·@qP,Ñõú9•¿ù ‚VÄ,‘Sâœ×“ØÂ˜ª;È¨8Ó4u5‚ý4‚7°7x9U§M¢T)°P>-°ƒ‚#U’6”øPÃ%Ó5µ½<X#:˜8$=ÿì<JµTF%Áó3Sô[yP×5H„¼W¤
˜
!°‚*¸9"C5VD=‰R-PØÕ³´?£Ý½*PJ-ÊÜ=TñCJ‘r8iJ5;ÊŽµ~õWú XŒØ,ØÐÁ"úÊ„
Ëtß²\BœØŠ­ß™ÚÊœ¬œýÞÉ%ÌˆÈ‡&°¬d``Åôˆ,øß®¢Y­Çt—ÈtËy©ù¨íY¸@MüË
–©¦¢¢-UÔÕ-«xS`Ò3€£x`€i8„G ÄJq.6€»<Ä×’X
 ˆ£ÍGÌ
Œ“ÄÜŽÛÍÞ¼Dm‹‘ËÄž%–0]J½.QLÎRdÎ M2@á)¦² Õ’Ç 4ÊÐ¸/“0½$ór
ïÕEy°ˆèÒÅcÉ(cßÄD±Û
5&Ó6æ2ypG†@#“ªƒy$ƒzü“{|$	ÕÝÙ€ŠÌ<UM|Ô˜$NÝ]z£¬Ûì”‡Þˆ¥ÜÞAîcþ}jY««€ÍÊ½Lô
 õ],„…=¶YN6ù¥_\fË‹]·üu©ý½ã±Ëkcx–m!fcö^‘•@†bF–f~æiÌˆcæ°flVf€°«ppædþæmfL—½"˜…L™•L^ÖX}=YàÈƒ2 `ßÉ„xnàˆÈ °¬tx vÌs–àt¦à¦¨ÙˆÐR²"
Ž™ÆÚuVÃ 0h‹@h0¢uýeÊòˆ`
Ä¿Hh Q–€         (àA„       2   
   Œù¾m„„ûN4-saSÌt¦O`þYÒùA‹žúJÎØU…••Î)½2mŸŽ}                                     M   M   ÿÎˆñI   C:\Mes Projets\repo\avsp_server\src\TEST_TreuilConnexion.wxt þí˜á
```


---

## Mes Projets\repo\avsp_server\src\avsp_server.cpl\MP\WD_DualAxisChart.wdc

```wlanguage
PCS                     B      9       j        €    Ê    01F280051n                 Ù‹éfÖ'«z±lwZ¨NDÞ+©Dðàh^·ŸßU¼¬Ô     "      þ       W D _ D u a l A x i s C h a r t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          L                  9                €    ¼   01F280051n     r       ]      >        
                                                                       ÿÎˆñ   WD_DualAxisChart þí˜á                                /ÈetóÕ>ú{ ë_ˆûÒAJüá@ªãÊ  X¨>bT¥‚Ð!½˜’/•Rc    N  †    
d0ËÐÀA‚xs—†‘b 
*&¦° ¢E	 |–H^‹ˆXŠ€)bGL!yÔÐ!’FÌœ:!ÌÐÉiç™DÚˆ¡CÇL€î2‰ëØtàÈ’òÀDT@Æ&NYììù&
*zxšqó‹š7eàà¡óâœ9nÚSGÌ•™.Û¨ÙSW
œ7/jÞ¬ƒ
ÁWs–ÑóFŒ5rìMºÔiS¨&ÏPµJXÈ3b´r
áókØ±eÏ¦]ÛömÜ¹uïæ}É×/àÄ„
#ŒósèŒC–LY)ÓË3Ë{@éñäÊIšCÕè5fdhÝÊ³´W°bë5‹V-[·på¾ k/½¶
ãî]øÍáTÌ`ß¼ñãÈ&{Î²èžšNžêRàºìfàŽ4ÓÂK­<ÖÐ{m½öfƒ¯¶¾æ
¬¾ÝòÛ/;:ü.@ã,p¹æ¹l1 ^,ÐÀ¨\ 
7à(Ã"ì˜Ã(!»ë*'|RH"ç°ñÆådàŒI3„‚ŒÂÎ0ò»Æ€Ž6B{CB8â%£žŒ1*¨:ÃŒŸê M‹¬ ô
´—²*S†ÔŒn9¨ÃŽ:à ‚Œ9Àl£NïLóL1É¡Š9¬Æ?etîÒXª`€!2^jc1È`#„ˆQåWrÎ:ÚäÊ} Êˆ;h
rÈ*ça¦¢H”3ÊÒŒ¨¨‚0~m’WZ?Cƒ[¡Ã5ª":-?“# SO|!¢$”6Xý)„XípÃÕ9n{#ÜË–Ã€*!ì Ã'Iû¼uM“öU°	5ÐøIR) Î4ª
¨Ú03$_§–ëv oïuX\r—»x g¡]WHjß¨ÃÚí
9ý­cYŒ1;°ªèÜÒ4<Û¨¹£å>ÈÙ	´v¾34Ÿ?Î÷@¡ÔÂ	1ò0:„ž–.ªkT:ã= *‹í¦®zk›£òZÁ,i«EÒÔE²>0„¯e ƒè1][[‚å¡m»¡–Zo¶ã¾Ú¤¿iLûî¢	©mÿz†»£^ûqÃÝÆzS¾6	ª’ÔìË“î\ÜAWPô´K‡¼\n#2â‰"^—\Üs×}wÞ{÷ýwà;jÚ£ˆ>â}xãÿ}ã BŠÎùæ#‚~zé«ˆúë­>{î·÷ûïµ |ñËïžüóÍ?º´·HJƒzùë·ÿ~üå¯¨"
ô)þåyïvPÊ_r÷¿Þð"Ácýœ¾Ë´¯{ïsNuçÀ¦DŒc9"r+<UPžð"Üw›±`
ñ	m Â¨@ A®@à@°È¥ òC þðFôãçÂàq€ƒQÈ@ˆ žT
KÁBa=>’!0Ä!€ž'H#Œ¼Ây<b÷–s‚ˆÄáX®Rh‚Å˜MQH)È–Š<'
„GJ:™°Ê”®%KH/è×¿<vº|!ñJˆˆ:R‘2D$’_H"ˆ~ÄxD4ô$p#èA "
$^XJà5°@èK1 øQ‘ œe-mY î°‡Ç	b‡è¾ˆô¡‚¨¼Ì+sw QÄ	Qxô\é”XÖhÉ1¦"q6e "EàŠÇ#$öáŠA‚½k1 ±€ ÆØ‘‰ho9&HãUÑF p3Š2
m²[^‘q¤E )I`Vò’™D%'µ÷ÉUF$™ e sWQù©òyê‹`Ba9Ì€v¤š!%isFÄ‡½bžÙ”h–4wD<D „7¨¡(äÅD’¡
"À[t&0€;Hs9*H4gÔd`ã²B€S¦ 3nˆC1…º¨h!"ÁŠ¤¶‡ˆê'x8xÈ³šó¸F=±qmöÒ¡¿|H1!ªí‡±*æ7¢)ÓZÛ:!¸¶&=°alÞ¼Òg¯÷á`ñ”‚@
-ƒ
Îú‚<nðj§–ƒ’µ;¨Ï`é°!”¡ZµlhJK@Óh*av¸SX3°UBãQy{!ºj²öÊ
NB¤þÐ³ÐÍŽFpÛ¾¡!"½•kS=Ä¡beî[k¡¹6¶®º+sAtE·>à/
v°„ýÄ¯Q!ÃvG¤d!&3¡ïxK\
Åu±r¬]å“×æÚ 
„”	
lßK
n¸‰ök’þòö¿ÝÕÂ€-^—·¸’+c3ôX¹w²6ñ…ï«aÏÁÃ™{JÈtë_þ¨MO!0¬Š|Üô¾ØÁ‘Õ«n(_M÷¿3¸î‡å¡] Ù»„)°¤ŒœXôº¸Áì}°d<aêîd¾(Æp~SôÕ¥Eå(YnP€kŒb/£Á`n1ƒ×»\EØ0&0›ï«2 ¦¶rˆÐÛì Äî2yõl^/8¹0no ßKcJ:ÃØÉñŽ™1G…ž©UÌÒüÚØÎ–»Ø½ãª ZWÿÖ²M¤i
2Ó¦Ú$ˆ¨^F„)@¡	MPÂ1ôŠÐ¦²ÞÐ$+ã °žÁl+¬W#JQC¸ÉŒà˜1„©Z)¥,uê²™ÛöM·MîC%ª
m ÷v :DtÐi^“…S‚ÿ¨Ý•zsn£’z¯ÌÞß÷KÌ€—àî`b£Cº+6dw7|[&ÑAÄ ëí™à»TaÂ8\Í—ùMqš“•gÈjn‡åtP¹Å[žá0ìÀà
4™&¤h 
ú¿I ê¯´ä@0©Éˆ8´{6¥¦DGyJ‹â£ô¨SÓwöõ¥•kWÛÑA§¼´#1Uº:ë~wÜu£OŽ¼!ÞIzR“¤t +eé/) aQu¨|Ø•‰.³™Y÷(Ý°xã5¾¤4Jâžø†o¡aYs@B½â¡Ð HP»Q•ò_<ªöQP
F¤©ò<PP‹=ª)¸
>óREv‚˜ E{Ô@* 'gƒNC0”¨qªUÍ¬ÜÄ ;,*‹Pv`e4þþ OØèK/†Ó£ˆU¿Ž³I&**+Ÿ-ü£ŠÂMCŒT“þ8?ƒ“ð ßS€Bò	+ˆ0¶+°8!	  B™@<ºÃ‘ ƒÙ¿P@ñ	‘ë›û
”\ð36#È²ˆƒ01¾l{ÀQéœ*ƒåk¾ç3
3`A®zÁ2@BQƒ½ {ñ¾D&´²+*ªº'«ú¼Ðs?ÓC½âHÂÀ ²ÑÒ‚,ô	2.Èxƒ0ä°<C´è`ÑúC5‡3‰ˆ¸¨
“]›­+6Äa“‡…À¹zÓ¹{›Ã:ü¸ÂÚÂ`VaDEôÂñÐ‚D\DpÀ#Œ
ÔºµÚƒ7¼Ù0y²1«K4‰LT­“DÁE7Å¨!E
|*yMÌ9ND€›3PC£X·2IÃý0
X´#`Ã-¿;ÆNaºFz$©C¨Ã«:b¨M:%O¢¥˜¨±;°Ë¨²k;o|»oT»pt»Vª<’©‘BFu\Gü<y¼a©–bs,#vä<“Ð?(l?Ð˜ƒ4k˜ã` T(¨]°# [½»¨=1(€€Ç Ø=3:<`BîŠ}½~üÇxƒÑÊÂ1Ì‘Œ’$8°ƒ0™@Òè´0aq´¨ƒ,Dƒv9’šÌÂ1ÈI.©É=Dº7”  ²êÚ'xŒ˜“tƒJb«Å@¼Å
ËB¬ƒ0aÊÑ²20¹‰›	«üD7Å2I®ôJA¬ÊXÅ±lÅ²4É§9©«6˜Iï0²"¨<´'j¸@i³4·*‹¼ƒ½„¯¾üËû:‚Dqƒ¤œ­JÉ•œƒ ÜÀÅã.Ö¸2!ƒº´2@KªË«“× Îœ¢M')EyÐ6@”¸´4Í&è83h˜Ñƒ0ÙÔ\LÖüÌÐ<Av¬e$(fŒº©s
h¼º†¢ÆˆàºbòºÈÆÝ±NßÑ¨®GîÇîì¨Ã³¼¤KÇâ´?°†!’qé»ò<B©]Z
yÃ³;Ä&æ	;ÇÃ$T&fº”¢
Oü<%ƒÚ¼%ÊÇˆØÈÑK³‹¹`Ö
€CÀ„#€cPÈ `È‰Är€È ˜¨†)YtŒtB7RÐŽœ²I1D‹“4Ã“L4•d—–´‰—¼L‘èC ¤33ˆ´È\J­tJâJ6 ÍKsC7uË³Â<²0ó3åŠ1O›1B£ƒsë8uÃ°³²0RCR:ÄJCÔB!ÅÈ9	/­ÊHK²lÑ7È+;ÅM<Ò5V”Ë¾Ù­[«KŸÄK½äËÕLáLÏ<0ò8ÌÄœ0àÄ0Ç­Ì=S[ƒ´ÍôKÖäŽ!sM+«4ËH
SÔ„
ÕÔÔÖN35@y€¸ÙLE5Å/ÜÔÍ:àM3ðMSN³RUbL¤`kÏü9N§KNg´Ïæ”ÆÙC¥jŒ¨küºiÚÏÁÎÞÑÎéìNr¼VŽŠ»z”%¥#O`ýÖ[rGx$¼^ªÏWú…aŠ§jõ©¡è ø X É»”iP{´%
ËS× 2PlŠŠÅ‚5HÉñ˜«ÉD!5¸4F’ ÕÐÚ#xº5;Võ¾Ð¾&Ä§ XƒÝ„UŒƒ ó0¼	œƒ,¨TOéCPåÑî*Ù:¸Ã,Ð‚`ÙÊ<I”%8•…Ùå(J’·}3ŒøY¡#C¡Å;aÅXâQN:V¬[Hèéü×f­Îm4¥gåÆÃÓXrOûOtW´½qOq˜OsuŠÄãV2ÒZv…<tùÏãÐ²¦¹m
o- |”‡-XB!Y8PØ)cØÛ¡€EX‚ ( t8§du Š½X¦Ñ3åØ ðØŒYÂ=X›eÐ¤MÙ¼ZÙ–}Y=½£™}´)ºšÄYåÙ`ñYÒ6´2¢
€|S¤UÚ M]tl:¨=¨XÎ¦ Úçœ¦eÝ¨­¥¨®ÕÆ¯%»°Çn´Z|åÖñLÛºû¸ ˆCöÄ¥÷<¶u[—ò¨¸•¦˜eÞºµ¿»<ëÝÛ#ê[Ì
©Àõ¢ 0‚3 >d™ƒ" ŒŸØ	JÛ‚^`‚ €Èå¢É½\
 çñPÝ«ßåˆJÎ5Q Ð_þÝÿ}ƒ –®¦Êe]þ¨ÙÃU™×ŠÐ˜‚ÍÆT›Má0¹‰^U
zZb-Þ©]¨ªÍÐ«M`küPgE%h-iåj¥[V"ÛW2Û’ú[íÅ»µU)^’Ç·m
?à[fm_y`x5Â¼m`˜Òbö
¨À]ªXA”Å‰ýPCƒ7c€9¸^ ‡& X`½¥\Œ`ËµŠ É£Íµ'Uc¸8X78¦-²@‹–K§œMW‹63XäF¶ŠG~ƒ,ü²óê³M[2;³Ãd3¸ºÀ°(p¶H^§ÞnÆgäáäUÖèbl|ÞëÔåì¬^ïL;&†¦s|â(.æ)¼*.¼ó=¼,>¢"†âË8/ŽWøõáëUßgØ 8ÜÛÝ£½’†Íz€ÓVÈ]`àøåc
`„‚êÀŒå½¨ˆÃŽ5d«òæ½¸ÐƒV¾£®ä­ç{(¨h#C1Ú6€6M-
LÉ×\Uúåš¨ðÞCŸä¤9TTüJž[K´`E+£è 0Ã‹>š—Ðè9UE;mK+›¨¬8K€vÓ(=@¾·Ì…†‹™#¨(°‚)°²¬±24 ,H%h#šþ1Œ+°h(“˜93Àa„3‰6	©&éª6>¬Öj+cžÙ©!•Ë¢>ê¤^êšî7jª°h°ê±®9Øüê¨	ºk8Éj¼†hi–ÚÞú“‡.}UŽöÄnK<ØLl©¤ÍÒìh¶lS34l7,fdÄá•ÚY¶:dßNºå-Îåè…^"[û[êµÖ×þelÞuæ|]:ÎÎmÎ>æx´âe¶Ïôˆ¾Œ"¾‘þ€z
ãÚã#n§(n

\Dn1`3‚)@‚rrP`¸{ ÿÙnHç=v`¸¢ËÕØå«zÞ>F’ê¾îì‚1(éaQ™,´KÔïÀIšôïžðZ±²¨7¸8Ðƒýè*WY93@‚AÑäúƒ,œPIƒ p‹£pIf™ƒ,”àpEôð:€pUp·p4hPñ.Ñl>Þeüìb¥:Z¾W[ÆZ\âäñ#Þ$öÛpfæÆÞ³Õíòämrõ¥ß~¥a&	&nàÉ¦ˆˆ" (8‚* ƒyMî¥ ò&æZÚ×:¢,nm>ü½’ÂPÃ>B¡U¸(‚v( ; ^PgkV§Úû€?ž`õ>’kïµ*:W;§’9J•,ñÉG™á7èg°2‰ÜýAšƒG?ƒC¥t5ØØ\]Õº	“¸ 7€³‚ë¸RóEhÃ’RÇñ€òl¨ëqæüñd-m!?m"'PÝ9rÝIòú[m½æ»ƒf(O:)Oær­r§¸òùÝâÞáòðr0s2§W¼=s1ž;5Ÿ%6·¼ˆöåºßˆÐ: ¤;wPpðÐh w(€T tA§XŒ ‚Ûa€ô–g“èYw‡wIgáùŸDøO×ôÃNœ=R—„?Ô…Á?éCB&81T’Cuø&y‰y——Ði+ÓIõncõ9pu:€õýÐ0±uÀuu;Ô-…M›2‚& ‚ƒ„MÍ­Š9ey—‡yÌM3 ù7°ùd9Ô3iƒ4M³.ì´vågoXæqíhìa­óž¬Uòæ5öhååiõåÙ–ílmŠs/÷\¿úüÙ†l P˜ð
©h—Oß¦ÇÃ³vn
d±çbmÚö/ó1/óp 4·m'××mU_À¯_3>Pyü €‚7H;0Š$é¢-‚ax‡È€ôòdŒ@‚øŠœè‚·*Ì×|Î‡ƒ^Ùç(PLhÒ •ì°L\ß@)Ø®€hRIøP(˜‚Ž§øOG>§ù`y„Q (ù!	žöi ~Y¬Ð¨iÒ ÜDiÂPõÊ·Üß}Ú¼ª8ˆÀ6˜Ü×ýÜ„ø®–‡žlôg-&iu ãþ Ã¡25½j³õÅ5Ö@©~?aŸá¿÷ìtÝE mÆÊ×‘¶‡2ä¸VjÛe°—Ñ6µ Û¾Ó²“_·Á=`¬f "Ý½ä÷ÚÖÞ+G}o˜@­À—í¢‚åãv†ïÛ™9Å7îŒˆ	4woÇm'ûEù,_ç
$ØÆAÅu&<( Oàôí;£‚J@ëS}‰.*Ð¢
²˜`I?¢
Ø…aÁÐ”Qå=šçsñúôC|$áQ¿„Aòšöëi?-¨U½;Â"BM#=Ãüá„þGôàÒšDfÐ.x¿3µÿTÞªû-oz	YU
0á5@fÁÏ4aºÓ^0jñºãu °bÂn®6¯
S	Ú‹mi/µ½ÛRœ
Ô^2Ð|ñ=ûä÷Ô”|¼CÛ€èíß‹X|MN}M>f‡œÊØ-	\éD2À<ÇdAS  9pSß³ & BŸxöúL‚ïñ@ÁGWa|ò)ÌG
8è¤õ±>Ø'.
0f ã<HA£büØƒ\˜‚ožðÓÂùÓÒ’lÒ?Æ¦ÿÔ‹y€@C‰È@±
 ‚Ibˆò]0¼††¬L 26"QIÄßó}â
jA/¨#rµ(›<
J
7èæ ¯°ƒj"PøAAh!˜¸¬´gnä~Âl¸ÏÒ ÕXƒ£ð@´‰p®ÅT,Ñ"á¥‰s-¬bTè@ZñµÐ	š§"‚Y0Û¡Là”ƒ…²Â
ã.YHÂ¦LÄ[Ç[TûA.î‡^3eì"Ü@’ãÅ9ØœÚPI	
ûÑ+“8 bLxõm\
¡5ØÒÕƒh …v¶¬·ë¶žm^ï‡…½ú…Úda \°µÕBØ(Càl\b$0ÍåÂ·÷ucqò…4Éua ÑºÒ& >eî˜¡ŒNlÍA…2
_À2	øë)~º‘ÇÔ@S›@ I ™cíñeº¥CA4×?tŠ°dÇ}&¦bÏ°‹3`>VE@¸éäV f‘+>
¯øéÀ"Ë‹ph-^Æ³ˆ7$Þˆõ13Î³y-$àXQ£A2µ@¿_„|-`ƒqŒ‰2²Ì¨
±„e!¶ø4ÐŠD2jd¡ó Màm±rH9"íB0‘Õ…EJHÉÉ M‹|/ÒL 	˜T åøë-rq(ÈY H$„4?2A®È!	$ Œd82RÐÈ¬è$) …Œ’S²Gö(+9!±dŠÜ’Gê%ÉüxØÊI˜”¬Ã-† ùîœp€u1K^I!É&£†—œ“M²N.¸½ˆ'³šèà“Ì¢JþI5(‰¤àÐgÊ0>Êøüc`Œû&§
‰È¸+z…Ê ƒfÑKN=´ÖÕnl!¦xÉ2ÒØõj+DK5¹Xˆ+g!´…±‘6úÊß8Ÿá“c•ÅÒ…ôFe­Ü0‘H9ÐîÀ.‡Ÿr´WÌ‘_¹=$‚
¹U³,†Óq›‘Åˆ ý"¤xhKªãÀ<( °@<.7rh
"”þAÐc9JöÄåø‹tq*Â1°’ÞbøØÅûø*%«J?a*Ö¤CõZbØfò	;6ñGŽçàš}|ŒT1ÁíIaÑ)M‡GT}Tt,Ðâ€}¤€!' LI0ODqk­'e2µíÐ2UUÌM1¯E„<0êã‚p™<ê)Iýs–ß°8TòÑ§ H¦,j°I2Æ;)1OØÍâj jzšI#V¦¤Ðš¯«k‚MØT5[ç¡
zàNbMI#ÿ&à1 m®MÉ¡ ô€ƒ(+3n¾„¹Y7ïæÔ<Sj“p²*þ¸ žOS£iò1«15›†!?¥ÏÄ›ò@ÿd…<ð6»K›O?^N4%…ßäœ€Ós6—¦5*çÌ
NÁ18a‘9'‘Tt8“Ã­ V4ùZÈœnTì3ûX;Ý¦—´)“‘3NÁ9§fÌ_Ç'
%µ\'Èä”qË´å*a¥ñêÈ
ÈÑJ
è¼<àE1{HŒÊÆ^‰>}eúf¶‘ñ
Kb†=eJ3 $À@¼C¬A2÷³“Œ”`Á Ah$d9í”eµ&ðŒnÇÊ-»Ý´‚ÍÜáÆl	€u–Þ²]E„<9%Q&_ Ðƒ@Þå cgÀ€º¾ôh*Aìs#!tGöš©èÉ2
rQ/QªÓ@˜Oƒ^M`2˜vo$Ì6`D÷Z!DYh&QbqéÄÄÂ\ô, |‹!Q @ý˜NRä¼Ê	~xVV`¢˜CUÊ¥†VHÌ|œ…Š8¼›åÒI@˜:3'¿£'óŸdSšÌïù”‰0) @(›(ŒbªŒ¨)Ô”çóÜ@—3M×…ÂÎfØT z`tþÍù·§"…•“)NÈùhÚ}œom”îµÇ˜_lÂ_˜óPÅ Ó9U©êŒ£JA€R,êR)l²|ZaŒJŠ‹D©BVÈ¥Z´L<½¨W9)À0åy>ÏÊD“¬Àñž(ZÐ	9§Á5ž59C@6-Á´rÞM‹A›¦AÑš† §–M;&7åVf0Sç9”R§•)žg“‚©GØR%òìCóùSm:=agõÜ72q ÔÇ³®§üüŒÚ³ú¸Ò8+3 +Ÿ­‘|ŽOóÉ+×çFUŸõWzÔ`Ù±¥-Ù…Õ¤ºd  ‚¶ÁøÓ)$P*·@›Bpø‹8>Kã(A}à2¬–ðRXj³ÛÈ­lê¥†”¯åIFéÁâ˜h*	 ÔƒðV(¿³ ®  X¾z)‹¨ 

HuO*UJ,Ø(– ¢÷ÐˆRÓ2šD™šà@¢T´kl—P´EÕ`ž ¤ùg¬¤$•!Wó›1-:V&ªt,Fñ2f-˜© 6ÍQ»H4w&¥œüt(ñÑ¥ùG‹Ž %¤‘ôfÒ:HÓæ"JŽ’Æ°Iú3õæ%Õ“nÚÍ{JK©,ú¤q®UjZJ#§`«»´rJµ!/QßŒ¥¦ÓÊäž¬ £T
èJ:oWÄS¹d8Wë™B•Jrã=áš i@…¯³Á‰L%™»ª°>i%UÌ…zRg‰CE…ÝS~½å…o%±»¨5£¾FÊ>å+G­%~ž@ñš_ó L"W8“w*uÇ1L†4ñYÐŸJR´¥ú
°HP¿òÆñõŽÊ—otŸM¡ 8ÈÁ"ó@‡8ÕrÞ­yU`¿Ý{dÁ
;˜  ý'VÁjPbX HjDh°%µƒÒkÙ>¨
EA;<‰áÈA€H@         (àA„       2   
   L9~­DÄ;ÃôMí³¡4f šRµ þºŠŽEÕÕ¿iýr-ß\ÅÎ=                         
```


---

## Mes Projets\repo\avsp_server\src\FabriqueClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : FabriqueClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16d0919c093cfe1f
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      FabriqueClient est une Classe
      
      	constante MaxClientsRecepteur = 3
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1643973963346214431
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1643973963346279967
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : CreeClient
     procedure_id : 1643973963346345503
     type_code : 12
     code : |1-
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
     type : 458752
   -
     name : CreeConnexionAuServeur
     procedure_id : 1643973963346411039
     type_code : 12
     code : |1+
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
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : FabriqueDeTreuil
resources :
 string_res :
  identifier : 0x1687bde4005d0481
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\FabriqueDeSignal.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : FabriqueDeSignal
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16951aa10a1a5177
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      FabriqueDeSignal est une Classe
      
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627236119325921655
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627236119325987191
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : CreeTraitementSignal
     procedure_id : 1627236119326052727
     type_code : 12
     code : |1+
      procÃ©dure publique globale CreeTraitementSignal() : ISignalTraitement 
      
      traitement est ISignalTraitement dynamique = allouer SignalTraitement()
      
      renvoyer traitement
     type : 458752
   -
     name : CreeEnregistrementSignal
     procedure_id : 1627236235300354158
     type_code : 12
     code : |1-
      procÃ©dure publique globale CreeEnregistrementSignal() : ISignalEnregistrement
      
      record est SignalEnregistrement dynamique = allouer SignalEnregistrement()
      
      RENVOYER record
     type : 458752
   -
     name : CreeDessineSignal
     procedure_id : 1627236806531072694
     type_code : 12
     code : |1-
      procÃ©dure publique globale CreeDessineSignal() : ISignalDessine
      
      dessin est SignalDessine dynamique = allouer SignalDessine()
      
      RENVOYER dessin
     type : 458752
   -
     name : CreeCalculeSignal
     procedure_id : 1643567015085553731
     type_code : 12
     code : |1+
      procÃ©dure PUBLIQUE GLOBALE CreeCalculeSignal() : IGestionDesCalculs
      
      calcule est GestionDesCalculs dynamique = allouer GestionDesCalculs()
      
      RENVOYER calcule
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x16950bc909e55375
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Gabarits\WD\110 System\System_0.wdw

```wlanguage
PCS         
   !           l       9                € 
       01A110053g 
    ƒ   è  ¼   Ï   ó  \            ’   
$            System_0  žhUF                          #¶8F3Ûi¿~|ÀDªž9¼Ší<ÇNYËc†É•ðT·“bF:\WX9\Gabarit\WD\Source\US\110 System\System.WDP       k   k        v     P  Ó    _@E:fÜ€‰ÀW#:¥±r‚àÅ‹õ¤±1€F‚-<†xB$AU®\Yà"»òâ¼‹ò.ÒKé’àL”µÐ¼¥J{ •ÅÉæJ'TBPIc¦M›EÚ aÚÕë×®J•ú¬y1ËS®U
Ö­Q£+ã‘K—å‚¶]0°2n y^$›– Y¢lÝ~…ËrnÝ£xƒ~
„OG]—ì6ÃbÎ*Ÿvþš²G	£; —iºÌG§<²£
–,:º 3FÌ5unéBŽ9dÌt±B¥‹1¦Lè¦Ë˜6bÞ°Á£æ
ÃxÊà¹xàb‡Ö‹…ÑãÄÌ;ÙHñD0¢¢Ó‹@`èd|¾Jj)¯œêª
1Ò€Ã
1Òó*@¡ò
ÀÅúÊ@¦TA ™
„B× 
£Y
      v     P  Ó    _@E:fÜ€‰ÀW#:¥±r‚àÅ‹õ¤±1€F‚-<†xB$AU®\Yà"»òâ¼‹ò.ÒKé’àL”µÐ¼¥J{ •ÅÉæJ'TBPIc¦M›EÚ aÚÕë×®J•ú¬y1ËS®U
Ö­Q£+ã‘K—å‚¶]0°2n y^$›– Y¢lÝ~…ËrnÝ£xƒ~
„OG]—ì6ÃbÎ*Ÿvþš²G	£; —iºÌG§<²£
–,:º 3FÌ5unéBŽ9dÌt±B¥‹1¦Lè¦Ë˜6bÞ°Á£æ
ÃxÊà¹xàb‡Ö‹…ÑãÄÌ;ÙHñD0¢¢Ó‹@`èd|¾Jj)¯œêª
1Ò€Ã
1Òó*@¡ò
ÀÅúÊ@¦TA ™
„B× 
£Y
   ­  ü    DÁ@_hxè”ÆÊ‰ŒXñ©ˆ*n”È‘#!/x|xR$I”$?dd™2åÊcº¤90BÄ‰(sæ¬8"âÅ–|räIÒäH£!‘Ö|T&Ó0<}
ÕãÍŽ)wz|
tæ×E=EIÖªJ§g+J¥ªö*N—[92ø‰,]¢4ÍŽUê–#Û jƒõk®ÖÃÔ
º8¯Ë½ûŽ8xjaËU
c
 6lâŠƒý8eä¨ýfÆœ–²á¬:AG”0úòÀÚ¦ËNæ{òu Ö«]¿æì¹¢ÜOEDT1Ê(DŒ±2Å‚PP—ÿæÞÝ;Ó*$_ž<Í

DãÄÌ;’LLhƒ¡CˆÓÇaûiÝHòä
ß<°@Ê‚sKAÊŠ‹k¶ï"”»ðÆ3¯¼	3Ô²
#ºÃ×<« "Ç:séŒ÷îPÃŒ9Ð0Œ"J ¦Hä"` F¨BŒ4àpCŒ à¡	5Þ€£=â0#€2Ô°Ã HÈI3€L¨
™Äªr y"¢'§"Ò‚Êã« Ä”jäèÆwìñÇ ‡,òÈ$—lòÉ(§Ü¨Êœ°ŒÍÃˆæñL1ÉL,§3ÏsIÍ¬ØÔ‘G’H#‘T’I'¡”2° øŒÈO+Ç
ÔËŠÂhÌ=ËŒ-Ñ-ÓËQ7#“R:/½SS=Eí3KŽZír /#25 TE=4"Vib´"X!…sÒ9-µ3Ó<9õÔ´(D,À?!UÕšÈÀ9psÝ9Í$4Î£!ªx"ß

5Üˆ£%Áõ+…õvØ"ò ¢q ºÃ€HöÑfX#
x dx!†– ²
S‡M`"sÁ-Ñ\ä~«²ÕŠâ‚#“IZÀÛŠ>*ä×Fö(åhæhån¹
 T±årPa
MõØìA¥e=r‚ŠyÓ0£6B(¢
4^«g×Èç ~
 Ø¬…66¶œŒVÔÕ”–núé¨§®š²«?•‰ Ž¸öì†O5”ìˆÌ&™¤¤9R›
§¡–šj«±Î»"»[ï¡ù.úhÀ×
`ðÂÛFnÅµ q`	…|ìPË¦Ü£À7ÂœíÃß.,î+e: îˆîØ½KïûtŽR¯huÃÝN\îÇ¯ÝñÅ‹”è üVÖrà5wÝ/Ø?(ÛmeÓÙ3î=²<¹‚˜ùtÕM¬üï¾¯¨Ýw‡€C:Ì€ÂŽ:ÔhÃúÂPè÷_#q ðaa
c˜Æ"±‰Uìb“Ç^–—f”‘ÊNV³
ÞŒe$:óDÖ©™e0"6ÛÎ¾å½Ä€jvá×ð6,å
„dÈqÞ¢ Ç4Â±Nxœ#ÞâFµ5äÁ0w4äÝF|‘èµnx±È
 ÄÐmtË“\óŠX‘#$‰;|]çh Å¯Ý.† d^
Ñ†’-n®‹=ì×?D±QQw“;Òn¸¶à©‘zŠsâßÅâq†‰1£Ó†ÃÌ)‘‡Lô!](Æ!òŠÉâå©C=º¥zàÂžö<©/ouOg$—ÿÊÅ©	¡kC'L	ûÀ1Ìp°ÃÔ0 üùÝp‘2˜r x`76âË ä& ¤˜Å ‹ˆ Ó2á z¨¥ãrq<‹£iRÈÊÒ[b é—Ò,.'3òN”00ÐË^øJÎkRÀËhÒ˜Â$V1©OˆI¬™@‚æ@¤IMk22]­¢Ç@ñÂkv“Œ)'êî˜Ã<NO“êƒJ:‡µÎï¸žFÈCüæð†|I±0÷,2•Ìü/ [ð'³ ºL*ð™ùDB{¸PRà¡·ãæ½é’Šöî¢‰äâQy&†Ý)„@§ÝA÷+Õk^€OƒÞ4
‹HjÊÓd0§Î,¨9#2ÍT³‡%ò£íTZTÜ±ð›“H%Ó¨QµTïœn‰jl¦Êª*á
&EiÜG :¤”0nqXÝúËœ!"ˆHÎVÌâ4lE+\ W…~ñ¡à^ÇÈ5”$ÕˆKÅ$`Ï"Ø×6T‡ýMbË¢Æo€éS­ÂÒÅ¹tŸ1H>{Ù´t´-­OS¹†
¢ŒÌ‰ ÷ZÇp¢ñ’-ç`Õ¢[¶ó`P,c%{›Â°À²ÈÌ“"‚ˆpá¹ôUëtw
Ú ˜µ;ãÕžÂq`¡"¡jmóJ:’Ðƒ¯ðëxÉ"´ú…3íŸzáéÈŠÁEîËC.Ýò
™ë)é4Y ²€ˆ d¨ÊŠû;Ð 4A&O0^©h×â¹6w$‘-iK^¸uu ?xjùÐÛáØN¸ÂãTdaîª6Rñ‘*uh=zìàÃ‘‘ñ.R¥|ä
¯*æñ\ x;,G´"¸G6ÃÈe€ê_üãƒ,“=ëŒÛ%ð@íÆ-Y}ä„ „xÖë^ïåhJ²qÅúÐÙˆ˜c0Ó†þÉuZ LúË9ó•˜
ª2SôÌâÄhš©§ä 0y²»À“ òæ™’h®2õ°Ü+?ºË«fe«µÙ`ŽÄ:Êàµ(­™šI5»„Ívœ¹»:ÛYÙy–Ÿ½Ír
ºÏ„V¡¹]½èÈ¿§Mè«±)Ôz Bßî!™‡,Ij+ÕÚµMŸw8ük3 |¶G²•}|Ô®½ßÌ¾wÄŸÝo‰þÍ#Âî+±›šmm—§ÍÝ&w·Âý³‹§¤Ýê>7´Àè>·¼‡0o¡ÄPF3¨(üs´ÿÍŽSØãØ9Mîf®t3õø®Æ.^*\-u–¨=šþ<†ÛZ-°zDÄóí93å~^Å‘=vv§KÚDž5Ôk]ì¢£dÛ†õˆ·í
nª[œìm®ˆžÝòWÚ2g¤àisœ£Äï,GüFˆ '
‘ž'¥_C€ý1Çë±‹}
j[0¬9ùºhqü	S> b°îÝ#Gx×?XŽlW¸‘³÷ÀÚ”2¾F:GžÃ¥_Ý†·—:eÆØuÓ}Êo/>T¸^‘	°ñìë¾Ù# dtû<rÓvúÂÝ~mÛÆ}O"—jÝK~Â“ë}ÜY<¢]ÝþÁ¿Ðß;GïoÖÞtµ6ñ‰n±òH‡šP¾¡
?¨@VªˆMˆêá#úK»Ø6"â¿Žó?d
ÜÚ=<#µJ7÷<e‚ó«8ìs9|7@”¨!‚&0öú­“z/ó:
È¼P½Òë¼â²ƒS½ŠàASs&ÔÛÁˆ`½¸ª·‡›(Øüƒ°k;’ø+«Ñ=î8:`û½ª¾„ë>Ûû>‚óŽã#‰ýÃþ#Ã†s‹ç‹ˆèc$okØ³¾±9AŒ3ª‰â>¬cC­¿^!?ºã»{¸ôÃC•›?Æ<ø;<FTÄ¬¿¸?±¹¢4ìÃ+´0Ü»­)À´B¥ÑÄæK@PšD±xÀ™‹@¢r0
85¼@?ÜD Ì‰´
Ý²pY¹H$´,ÁéCÄØ
3À
€t	„A¤1x: ’1x 1.¨ Ëj£8 +ˆˆPPÃž°Æ#˜ˆ6Ò¹éË®¡º+Vü¹`EÁÅÿ#Eid
Þ¶ €'t6IdÀ.[»*´@¡ÃÀ,äìK	%x‚*°*¬2­âª
bŠQ¨F8Œ*ˆˆˆèÆoœHéÂ±q„³ürB0ƒ8`²í£½WÈXE
ÔBtR©ô*!|É‰FdéGv¬È$#Hä1H„¤¼I31µ Æâq"
ˆˆ7ˆˆ¬¤Ç øÈ—
ÉŸ:G‡’@XËÉÝÈå¿ä5[„I(«ˆ€|·Û¹I|óG sGÕÇ¬	TËš #h…Ìª­"Ê³ˆÈ£Œˆ¤‡¼ˆ8€§<LÁ”Jªt";Æ}4I*|K¯<À°„®—,ž˜4K´¤I}\?±[:·lÇÊäÉ×ñˆ‚D	¼ÔKyÊR<‹ð¥a5™*±°Ms5j¨
Ú”Ê^‰Q{L’”¿	äJ:JÉ¯,ÃÓÌ0Â"K„ó=¥«ËDNË¤Œ¼“½¢¾3ÊÄ¨‹ÇšxCÒ“¾×s5á¬IâÜˆÑÔI¸ü¹Ä”˜»Ý2?öÉC´¹hÄ˜;OÃËÏGôÀ]\K „¹Šp'*¨‚d”4zzM«0ÙÄH~ÂMÜ¼ƒ 4…Ý¬ˆÞìHúMŠhB9»ÃóœÂÙ›NLÅîœKšPÀZt
.L:£CY+Í•ôÎš¸Î3Ã}Ç¥ð€Ò;¶ñ¼òüÌUÔ¾É$Mê4M÷DòXQBôÅ9Ë;›<¿ý¬¹þ€)M¶!µRUì@Jô5ÐxZÆfTƒ1`´'Ëš*
X.Ïû5Ë†PS©l«©0‘d5»ì9ô4N+‚Qîd>MRu*K¿P'@0¨‚9Ó8ˆ¥÷¨¿´ŠMY9Í¬ˆÐ,‰ 58­ˆJ5=¢Ó·j=PÙ9Óp9…8>¾5Ñ$eRÊ0TDmÍ}ÈšP4ˆ5%ÌˆpÈÐÔ8ÅÕ9…2;í!ÕƒL­<Õ=DÉËÔ”pÕÂ€U0(Z|	"˜3°ÜÌ™³È„‹â¬(®C› ±%¨Šq•Ji˜
Ò<Í¾­<IW\VölÖåT´æÔLB… h#PñÐÙÇš£ô‹
Â 
RZ=Xä¨´šXØ’¡ !³¤Õ\Uï0š¡9´âò$‰-Œ	Ò%+@‚dSg¬'ÊÀ„p}±ÍŠˆ© ˆt…€uÍØ WOu¦w•Y;­ÒîbÇKTÕ?Ëžä×órÎÞÛÎØ˜u#`„XŠ™d½X‹
 Œ×‡ež’UØ­õÚ®ýZ¡MØ®<RŽí5©¨ÙìY¿Û	9Ù”=ÐÊ‹T¨@®aÁ‹ ˜7-ˆkü‡œ5¦Š \Áe&’[Àe6óRu$Ò¥L¶]ZåtVÄøäˆoWà‡º
ª›Û*ˆ‚ƒÊYõV«ª-‘ð)®úò‡œ½ž€]©…‰(yE»,EÖ”ÜXÌmCMúøÜLª"»U
Š¡³íÚ´ý¬• ïh^®mX0¡ïI—zX>+É¹ùÞw«¯[—Ýˆ(€‚„”UË£¨Útò´ÀˆÐˆè‡œMžˆ_©”…‰H§ÞU¿ßÍ8í4¤¶ÝWî8Þ
€ƒ}Šå=
ë5[ìÕ ÖMÞPi‹
fÞ²ýZ´
 íu	îõ^u_ Ýa„Z”Hß„œ‚#ƒú9ÕmßÂÐ†ªåŒý€dˆˆ~ÈÙG¨
–Ê7	Î`ÞS{µ\|E¢öÔ\
YÒòãˆ/Ø± hÅØ^TVM	´âPÁbÕâËÔÅø3Ú-uZØª‰Z°`¡U«ˆ`†^ÛØp`7^³
~^‡ÍàQ‚‚'ˆU}/ôŠhÐLàˆˆKPd/ÉYb¨
D–
Æu¦V	D6âó<Ú$6Ò%Ö¢&Fà¥G¨6d*Y0fV1îª[ºSÎ×Tþ
ðì:¥ ­Ã;m¶y5U¶Q¥Íb¦uâüƒâAÜˆB¤O(µÏ¾ƒÄ2ŽLfvDgöÏfM!˜‚!°Ž>_¿° C€HfäXd tdH®©¬äC¶J¢5ÖÖÃà]O&Ö×[cNÊ€V´2eyödz~U®‡VžçW~Xî2/©eƒ´êËå¬Rà-àðÞ?tV~NcvR[NæžOi†f­¼R…žÜÁýO.­æ¸æl^YftF3ý'÷ÕÁ ¨7Ãgq : f`™–ÊZ0•z‹ÍÐPzëPwé‡öåíü,lÚÁg° >éTâ_ã`®g=D¾TMjªÎÜ­Ãê•‰9ÜevÆátè^~ÑËf®ÖÜŠŽÚŠ8f“Óè}$	ü,Øß*i.éš™ˆ´ô]‘ÖdÒ1—€æçÞ(ïÐ­¿Þk=l9"ì}¶¤~,º}OÎ]ì#vlT…l?Ukâ=
®«l”¸E|KhE¾ ¬Ë& HSÆ•&Ó–®ˆAfŠ
€é¦iÜ€:ÀiŽˆ™þY ééøi_jL6cx†èjÓ—¥Fàß¨GÅNÍj–hY”Ç«Vî§³n–¤‰ýj¼>òdè_üÐ³¦îˆöì‰Ô¶&9ŽF?¹^¿û|æ>;b½¶o¼žë íëv.ëh®×ÁŠÂ–ìÃ¦ìÄìòFnìÜCÎNë­þìÛm’€OÌÎäónðN&ðªîj	÷ÒC@ÔfËˆXíÖdnv‹¨Z´ßØaŽ; wà‰—Jg˜<îÿ>j´Îðææööˆ-‰˜^ðîKZ­Få
Ï½ó— jÐòâí!ó²ë,¥9,étq¡¬P¾±»õ‚ÉcßW
(°,ŸåFç` P+ žhs©t‡™
jQÎïñ(Ñ©vòµ¾gƒ‹âù.a0:âÈkv¹æm–í×h ˜&ˆ ‚ˆh‰€ñ µ_`JKÇPgBSùôPaÂ¡f$˜“ÜÆþs©nrW~rÊåš€nèlÑ¨æä@õA/ŒM¾È¶rÐÆêkDhÿ&4?VW×uXhYgoÎEÞð>tøftÅ£o-éûÖÏü¦f$î€ìS t÷u™ðDíö>îT«øQ
2Nt
Oî2
öO&Y{†àh/µl¢n¿^|ï†þïW¶,+Æ„=ÆL‚GÏw«8Ù&%™p%4‚:xx)6˜w¥*€8hƒ1€ƒs¥!˜8ÀƒÈ
€%‚mny0 )ÆêK˜‡‚;`‘6ƒ8`ù: ƒœù¨™1PƒÈ
&*èùŸ ®­r,2`4˜£¯—o/¥¿˜ 1:13 ± ˜ŽõEñÇ‹¼3Gñ±Ö¡ú“êK…—Á'€"Ðùú®  ÉË0°yœ×ù¹¯ûmž!(%˜<¼Ÿƒœßy¿ ü±ßÛ/ÝæÅoû·ƒ¸_JiUTFuÔÅ
 iEs%p4˜Öj½V*°ƒ7`ƒ÷€ç€$Gw|¸W¹?ýÔwt4?ÝÔý&(‚—¿}0`á:pá"é|4?Æm®ëÐÛ¡$~#`{·o}¹_í+ Ö‡|×_Jçy%(ã§§*Pý)þ\øm6iŽ`‚X † h È Ð ˆƒ „¨Òµ:m€m€c d°¯%9‡ €À `  d QÀò@ 1B ¤ ý³ €X€À0à€åàÐß;(  à  <÷ ;HðÞ¿ü'
À, ´  ¬1e X	  Æ] 
€4 œ¿˜ƒ   •@ ø ì”O Là  „  ”ƒ €S` ˆ‚&P<Ü˜6 * ©  @‰¥duêÌ3 Ð  T‚ `	€
„ \P
€  ~Ó" Œ  HÀu€€s/ 88ÇèBÔÀ L òAÐ,U€D°  H  $¨é‚
` ¢«   Â* ¬  œŒ<è
À7 #ÉØ¯@a€$xƒeh‚ °	rÍ+t+Á ˜Àìp
 dí  ¸€ ð
` @Al àÔ+°€
c=Ü‚ €
†áÉPÀ
D€
h˜!
,1€ d‚  [Ìþ[c¶  ØCpH‚
!ôƒá  ˆƒx/G xÀÍ	3„˜âBdˆ
Ñ!>Dˆ%âD¤ˆÑ"^DŒ˜5âFäˆÑ#~DEâH$‰%Ñ$žD”˜UâJd‰-Ñ%¾D˜eâL¤‰5Ñ&ÞDœ˜uâNä‰=Ñ'þD € à ÄƒRP R HBÈ_Y4Ä²^Ðœ‚ê[óå¦™¢öËM™ddà¿@—au]Æ:ÒJš­l1»ê¦Þ®›êpÁÖQ•õ"ó€Ë™¢~
­¼~BSâº‹GÈ+¶•ƒ2´^
©"‹YÊ,Æ³ÎöàÖ[êýjPAE” Ä   ©      @‚		T¸°!A
NÈðáEŒ/ZP@a   $´8’ É‚	Qt¨ñÈŽ.X²èè‚DÌ1sÔÔ‰Y¤
8væ1ÓÅ
•.2dÄAE:fÜHøP‚Æ†
FæŽ„Â«Xj%X¤ë×„aÅ  R0¯`×RÈ(!!/x|ð”ÆÊ*MlI¨  /hŒˆÄòbD…t9®¥\!a:Ay
Ð-Ó€Š’ ˜rCFa‚‹Hbl€5 è  â×    CœŒÃÀàÁ! S¼t°ËAB p0TÁƒ5n Ð ÇŽ ›± š‡ÍÌh1¹²%K•0]Æ|Y“æÍ™9eî´©³'Oœ@}ýY”èÑ¡I….5ª´)S¤PF}Z•êÕ©Y¥nµªµ+W¬`½†ýZ–ìÙ±NŸáÀ™™--ã>Sã%@±1µÝ!€Ûµi¶‰'8Ãˆ·NÜx±bÆG¦ÙòäË’5WÆÜysfÎŸ=‡&
ÚôèÓ¢U—FÝzujÖ¯]Ç¦
ÛöìÛ²u×ÆÝ{wnÞ¿} 'Ž˜[¸p–={ ›¶l´uã [³dnëZ§F:rëîäYg÷Ž¼ylåÏ¯O^}{öïå»§¿>|üóíïÏ_þþÐ?,@ 4pÁT°A”ÐA
#¬B'´pÃ/Ô°C?Ñ¼lØ'€kÔq'€gÈ9'€nÜ9qêL:#.lº“îoÈ;1uÚq.È!…ÒH",rI%›LòI$£dÊ)¥tÒJ*¯¬rK-»ÌòK,ÃäÌ1ÅôÒL2Ï,sM5ÛLóM4ãdÎ9åtÓN:ï¬sO=ûÌ“Ìi\dQÐç¢kæFî¼Ó†GùüOHÔÏI•ôRJ1µ4SN7õ´RP5
µÓQ?õTR·¼f—kî4pÔqÑÓûÑÔTsÅu×R{E•×_}ÕUX`‡
öXc“-vÙQMô
<šrT”Q[‘eÖZb³Åv[eµí–Ûk¿7\r½-wÍj·ùæ›š¤Ý±VgÇ5w^pé•·^|ïÕ÷Ü}íå÷_ÅLwÝvg¥6^€óM¸_…nøa†#^xb‡%u`v£5Þ[+¦â-Ùc’G6Yd”C¶ã‚§åød•KNYæ˜a¦ùf›sžùX–5v¹Ñƒ®Yhœ‰Öyh¤‹Núáž]r—V —6zj©«Vúê£±¦Zk2›fééƒ;Îzì­É¶Úl®ÓF{ío½NôÝ¨Õ.{î³é–»n¼ïÖ]gÕÍØéãf;ïÁ÷¶»pÄW<a·Á~yqÃ	‡<qÉ+üržû&Øg¸«üsË)ÇôÑC'½Q¿[îáÒ[ýõÓc7}v¬ÜsÚaÏ]v×y×½÷smÿwà‹ÿýxß“ß}ùH…_]låyæ©—¾zç¡&Þzî§÷¾úï¯W;û°» ÿ|ñÍO?üµÉ}õãg~øé×Ö}Áí—¿~þ×ï_áo{ú#àÿ
è?pIdè@F0úc ô$Á	^ðÌ]1øAfP„ ÄžæþöµÛ50„$á
]¨Áyð…-„!
mÈÂö™PuÚSa
q8ÃúˆÆ’¡xÃ!1ˆô*"•˜D'Fq‰™›Ñæ 7¼JŠSÜb}ØD.>QŒZcåF/’Qfdã
ÑXÆ4¶Žsá×G<ÒQŽÆ³ãý˜Ç;êñt}äyÈ@bŽˆ4d"ùÈ²-Ò‘…¤$#+é0IBÒ’›œ$'M–ÉKj²“£%¾@éÉP¦•«<£9ÇC
’R•²de)ÛæÊ+>Ï–µœå.i@\¢‹±ôe1{yÌZžò—Æäe3—©&e2ó™Î”f)£‰Ìif³š…¼&5±¹Mo*±›Úüf9Ã)Åq‚“œçT'ÓiÎuÆ³|';á9O{–°Š'|,ñ)Ï{TœÁìgù
Ð|"Ô‘õTè?zPÄ1ô¡	hC'Qˆf”¢-F7úQ‹‚4‡ûÜ¡AEÊÑŠ¦ô¢u\þNúÒÆ”{•)Jm
ÓmÑT¥8½iM¯¥ÓžîÔ§CÍ)KSHÌ &•§K½ŸQ‡)T¨*•¨;*S£jU©.Ð©ºÌjW±úÕ‹mÕŸ^*X¯ÊD±š´¬k=k[EVU¶’Õ­V…ë\ãjÖ ÖU®{Å+5õÚ×»¶­,_
+RÂÚU±†-fbûXÀú±qs ƒsÎ¡Ù£(š‡>8ëÙktö³£½=@kZ¡¶´§emj[»Z×Æ¶³Umm_k[Ùâ–¶·åmn{»[ß¸ÃÕmqk\á"—¸Çenr›»\çFºÓUnuŸk]éb—º×å.iEZÐ~W¼¯¯wÃ{ÞÔî#»¯½Çfµ‘Ìª$
(êÃàFØW°ï8$‡ £ 0'Aàÿ@ùU0ƒ¬_ GÂn0…\a_XÃÎ0‡7la‡Ä#î0‰?\bŸXÅ"N1‹WlbÇÆ3n1_\cßXÇ2Î1wlc ¹¿C Êa	=¸yPr’Éa‰=4YÁŒhC Æ‰9ÉJf²€å S  "Ö u8Â
`Ç# ÌG¼ÙöeG# œDTƒ°¯3â ç Àè@… °ŽW" íE" Z," î"Ðì
E¯ƒ„@ó*­OwúÓ¡õ¨M-jT—:Õ¤fõ©UýêV¯ÚÕ±†õ¬m-k\×:×´æµ«Uñé_£9Øêv±}la#›ØÊ6v²½ìg7ÚÓ–vµ™}íhc›ÚÚ¶v¶½½íowÜãw¹¹}îpk{ƒ@:L¡hp¢-àKÂAˆù’ãqñF â²ŽYL¢Ý¡ ™å€pâ
V.D\Èˆ)Àh
ŸÇ-$ ULÂ`ÄR I ‚h„^€@Ì¡ÈÄ T€xBÑÑ œó‡A—"í&E’é‘Š([ÃbPð& |Wp:§Ø9:LÑô§ ÀéP§ºÔ£>u«Wë[¿z×µîõ¬‡ë_'»ØÁ>v³—ík?{ÛÕîö´Çío§»Üá>w»×ï{¿ûÝ™.õ¿£#ðƒo:á_xÄ^ñ‚O<ãoxÇGò“o<åŸøs: ï €ç!
v÷c×@>Œ1rxôVVÃ¤>
Ml€¦G) {Ô"öö˜ÅÅña‹‹
¹‡/D€EèøE-¬€GèAt,€
ZTG
]@E èq
E r ô5ø`ßx€¢hCø`,p: @L ¾P‰ùÿÅŒÌ	?0²NÀr@$@,@\Àl@tÀŒ@|@
”@œ@
¬@ÜÀ
ì@
ôÀÁ $  °$Á<ALA4ÁDAŒAœÁ”Á¤Á¼AÌA´ÁÄAB $‡MX?"ÁM@B%°$dÂ%<B'ŒB(œÂ&¤Â'¬B,¼B-”Â,äÂ-‡l‡Lˆ²v „: y€…LØ<Pð?vØƒ#@¸6ø Kük‡IÈ²y˜Oh·=ˆ¸t°ƒ"ð¾Vh)@ÃZè„ Ð„A¨–óëƒ v ‡C3‡9„<È… Àƒ2è¾wè„Ûs7Hƒ °†<¨²vˆ„,NX¹
¸„U€h„ãË‚>È2,ø„QØ¼M³pÀ„(
ÆaF„3Fb<Æb\FelÆd|FdŒFf„Æi”Fg´Fj¼ÆjÜFmìÆlüFlGnÇqGo4Gr<Çr\GulÇt|Gt4Fp¸„(›Çz¤G2ÃG{ÌÇ{ìG~üÇ}H}HÈ‚$H€DHƒLÈƒôÇo¸„$c Oà4u03º>ˆ
tÀƒ(@3;`‚ûb#@¸R¸8vØ²yÀR`3I°6[@«‡[øéX4Wˆ=QP[€J¸ƒ‘S sˆ 4Ê øƒ4€…£LS@³Kà´dPƒ3 j ƒ*{CX3D€ƒ‡ÐA(ƒ ˆ…@è‹F¸„Û»E ´qØ„z”G0[H¸TH¹lH†¬Ë¸¤Ë»´Ë¹ÔK¼ÜË¼üË-‡·Ìz$L23LÁ,LÅ<ÌÅLLÆ|LÇŒLÄœÌÆ¤LÈ´LÉ¬LÍ¼ÌÍÌLÎüLÏMÌÍÎ$MÐ4MÑÜLoX26…ðS VD3‹”:>h‹¬Iƒ8ü†Q€6ëC“‡^˜Å~˜‡á‡w`‡ Ð‡lpé`³VXÃTxHœcˆŽ‰ìÉF˜ƒS†_è† Èƒ0©£vC©<E;¨2n€„1;‡1€s …÷K R@u…(#Á÷‡LH²‡d²
€=ÐK Ð5ÐEP}P…P P
P
mP•Ð­Ð
½P
ýPQ½/K`²Õ4Ñ%Q]QmQoHÑeÑuQ­QµQ½QÍQQÅÑÝÑ íQ MÑn¨„1;Ò$ERaR%mÒ%R(Ò'­R'½R)µÒ,ÅR*åR-íÒ-•Rn˜—\‡V°„CÓ„÷S þB3F°¹t(°ü;¸éKc W@SuÈC©#ï@ @xtØ,ð
;8ÔrI†RÐ…  …D€Ä–ƒÄKHD9˜¾F J„C„([50Å^Xƒ¸( O(¸oÐ„þ,… 4‡T04XÝ9r0…‚+‡R(8rÿÛÕ^åÕ V_
Ö_-Vb=ÖaMVa]VcUÖfeVd…VgÖg­Vj½ÖiÍVi…Öq…ôÖ WrWrýVs
×s×t-WtmWuuWv}WyWz]W{…×{×|­×`
Ànõ¿q X+#Ø€X„-Ø„=X…mX†}XƒØ…•X‡¥XˆXŒ­ØŒ QØ9spßüØu…H sx…‹ûØ’‡OèOR`7‘5Y’•Ù’=Ù”mß‡NH²s…‚[ T¸½u0¡-Ec‡Rà´sØ„‚»²÷»…0ˆ
 ƒ†=°‹t…õ3‡—¥OU4t`CÛ±[ú\FðZI; UH[¶uÛ¶õZ¸[¹­Û·µÛ¸½[½Í[¾¥Û½õÛ¾Å[À\Á-Ü¿5ÜÀ=\ÅM\Æ%ÜÅuÜÆE\È\É­ÜÇµÜÈ½Ü½]ÛTà\Ï¥ÏÎÝÏ=‡Ð%ÝÑ-]Ô=]Õ]Ö5ÝÖMÝ×]]×]Ø¥]Î
¿Ìs´Ü¥OTÐÝÞå]ÝE]ÜýÝÝ-ÞT^ÜýZÞ
?®½½æ52Qà´EU:Ÿ½‹=ðŽ©ˆ€       %    %      |…pG{†Ã§?2€i;q=þñZ° Ü£.a!á£Œ   ß&:†tZ_j!†|•=r<ùìa©)ð™Ô¾D@/      @    @               /   /   F:\WX9\Gabarit\WD\Source\US\110 System                 /   /   F:\WX9\Gabarit\WD\Source\US\110 System               
        H0Ð Á‚ 
```


---

## Mes Projets\repo\avsp_server\src\GestionDesCalculs.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : GestionDesCalculs
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16a36f9c08991005
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      GestionDesCalculs est une Classe
      	herite IGestionDesCalculs
      	nomThread est chaine
      	arretDemandÃ© est un boolÃ©en
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1631270205988278277
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1631270205988343813
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      SignalDÃ©truit(:nomThread)
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1631270205988409349
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalTraitement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      
      Logger.Verbose(ChaÃ®neConstruit("Demarre acquisition pour le traitement id=%1", :id))
      
      si :nomThread="" alors
      	:nomThread = ChaÃ®neConstruit("thTraitement%1",:id)
      	Logger.Verbose(ChaÃ®neConstruit("Construit traitement id=%1", :id))
      	SignalCrÃ©e(:nomThread,signalManuel,signalFermÃ©,partageAucun)
      fin
      :arretDemandÃ©=faux
      SignalModifie(:nomThread,signalFermÃ©)
      ThreadExÃ©cute(:nomThread,threadNormal,:callbackThread, :id, :nomThread, ()=>arretDemandÃ©)
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1631270205988474885
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalTraitement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      
      Logger.Verbose(ChaÃ®neConstruit("Arrete acquisition pour le traitement id=%1",:id))
      
      :arretDemandÃ©=vrai
      SignalModifie(:nomThread,signalOuvert)
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)=threadEnCours,:nomThread)
      
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1631270205988540421
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Parametre
      PROCÃ‰DURE Parametre(params)
      
      Logger.Verbose(ChaÃ®neConstruit("Parametre acquisition pour le traitement id=%1, p=%2", id, params))
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x16a36d9108161470
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_GestionDesCalculs.wxt
```


---

## Mes Projets\repo\avsp_server\src\IAcquisitionSignal.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : IAcquisitionSignal
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1697c05018aba0cf
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627981240561606863
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627981240561672399
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : EnfileBuffer
     procedure_id : 1630627429913819569
     type_code : 12
     code : |1+
      procÃ©dure abstraite EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1630627593126678127
     type_code : 12
     code : |1+
      procÃ©dure abstraite DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1631009419835772927
     type_code : 12
     code : |1+
      procÃ©dure abstraite TaillefileBuffer() : entier
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1697c04b18a88cf9
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\IClientSocket.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : IClientSocket
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1694eca406bfaa9f
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627185554619738783
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      :id = &:id
      :nomThread = "ThreadIClientSocket"+:id
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627185554619804319
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      MutexDÃ©truit(:mutex)
      SignalDÃ©truit(:nomThread)
      Logger.Verbose("dÃ©truit IClientSocket, mutex=" + :mutex)
     type : 655360
   -
     name : EstActif
     procedure_id : 1627185554619869855
     type_code : 12
     code : |1+
      procÃ©dure VIRTUELLE EstActif() : boolÃ©en
      
      renvoyer ThreadEtat(:nomThread)=threadEnCours
     type : 458752
   -
     name : EstArretDemande
     procedure_id : 1643629433866877340
     type_code : 12
     code : |1+
      procÃ©dure VIRTUELLE EstArretDemande() : boolÃ©en
      
      renvoyer :arretDemandÃ©
     type : 458752
   -
     name : DemarreEcouteSocket
     procedure_id : 1627185554619935391
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : ArreteEcouteSocket
     procedure_id : 1643629352262021043
     type_code : 12
     code : |1+
      procÃ©dure VIRTUELLE ArreteEcouteSocket()
      
      Logger.Verbose(ChaÃ®neConstruit("Arrete d'Ã©couter la socket %1",:canalSocket))
      :arretDemandÃ©=vrai
      SignalModifie(:nomThread,signalOuvert)
     type : 458752
   -
     name : Demarre
     procedure_id : 1627185554620000927
     type_code : 12
     code : |1+
      procÃ©dure  abstraite Demarre() : boolÃ©en
      
     type : 458752
   -
     name : Arrete
     procedure_id : 1627185554620066463
     type_code : 12
     code : |1+
      procÃ©dure  abstraite Arrete() : boolÃ©en
      
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1627185554620131999
     type_code : 12
     code : |1+
      procÃ©dure  ABSTRAITE EcouteSocket()
      
     type : 458752
   -
     name : Ecrit
     procedure_id : 1627185554620263071
     type_code : 12
     code : |1+
      procÃ©dure virtuelle Ecrit(msg est chaine) : boolÃ©en
      
      Logger.Debug("client["+:nomThread+"] envoie="+msg)
      :dernierMsgEnvoye=msg
      renvoyer SocketEcrit(:canalSocket,msg)
     type : 458752
   -
     name : Lit
     procedure_id : 1627185554620328607
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Build
     procedure_id : 1881457625711181384
     type_code : 12
     code : |1+
      procÃ©dure Build()
      
      :mutex = "mutex"+:id
      
      MutexCrÃ©e(:mutex,Faux,partageAucun)
      SignalCrÃ©e(:nomThread,signalManuel,signalFermÃ©,partageAucun)
      
      Logger.Verbose("Build IClientSocket, mutex=" + :mutex + ", thread=" + :nomThread)
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bce300111696
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\IConnexionClientSocket.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : IConnexionClientSocket
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1694ed0708182d38
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      IConnexionClientSocket est une Classe
      	herite IClientSocket
      	NomSocket est une chaÃ®ne 
      	AdresseSocketServer est une chaÃ®ne
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627185979844013368
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627185979844078904
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
     type : 655360
   -
     name : AjouteClient
     procedure_id : 1627185979844144440
     type_code : 12
     code : |1+
      procÃ©dure abstraite AjouteClient(client IClientSocket dynamique):boolÃ©en
      
     type : 458752
   -
     name : SupprimeClient
     procedure_id : 1627185979844209976
     type_code : 12
     code : |1+
      procÃ©dure ABSTRAITE SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1627185979844275512
     type_code : 12
     code : |1+
      procÃ©dure abstraite RecupereClients() : tableau de IClientSocket dynamique
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bcf300185499
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\IGestionDesCalculs.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : IGestionDesCalculs
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16bb5a9c00ede72c
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      IGestionDesCalculs est une Classe
      	implemente IAcquisitionProc
      	id est un entier = 0
      	callbackThread est une procÃ©dure
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1638002515556427564
     type_code : 27
     code : |1-
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1638002515556493100
     type_code : 28
     code : |1-
      procÃ©dure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1638002515556558636
     type_code : 12
     code : |1+
      procÃ©dure abstraite DemarreAcquisition() :booleen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1638002515556624172
     type_code : 12
     code : |1+
      procÃ©dure abstraite ArreteAcquisition() :boolÃ©en
     type : 458752
   -
     name : Parametre
     procedure_id : 1638002515556689708
     type_code : 12
     code : |1+
      procÃ©dure abstraite Parametre(params)
     type : 458752
  properties :
   -
     name : id
     identifier : 0x16bb5a9c00eee72c
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure id() : entier
          renvoyer :id
        type : 1966080
      -
        code : |1+
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
        type : 2031616
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x169139d311baa392
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ISignalDessine.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ISignalDessine
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1694f0da09c01dcd
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1-
      ISignalDessine est une Classe
      	implÃ©mente IAcquisitionProc
      	id est un entier = 0
      	champImage est une wl.Image
      	arretTimerSysDessin	est un boolÃ©en
      	fileAffichage est une file de IAcquisitionSignal.Str_DATA_Acquisition
      fin
       
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627190184644779469
     type_code : 27
     code : |1-
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627190184644845005
     type_code : 28
     code : |1-
      procÃ©dure Destructeur()
     type : 655360
   -
     name : Parametre
     procedure_id : 1627190433753202688
     type_code : 12
     code : |1+
      procÃ©dure abstraite Parametre(champImage)
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1627191786668085496
     type_code : 12
     code : |1+
      procÃ©dure abstraite DemarreAcquisition() : boolÃ©en
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627191833912802288
     type_code : 12
     code : |1+
      procÃ©dure abstraite ArreteAcquisition() : boolÃ©en
     type : 458752
   -
     name : DessineDansImage
     procedure_id : 1627990788310338616
     type_code : 12
     code : |1+
      procÃ©dure abstraite DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644442558556870
     type_code : 12
     code : |1+
      procÃ©dure abstraite DebutDessin()
     type : 458752
   -
     name : SauveImageJpeg
     procedure_id : 1631354043776558121
     type_code : 12
     code : |1+
      procÃ©dure abstraite SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
     type : 458752
   -
     name : PrepareDessin
     procedure_id : 1829904471857195959
     type_code : 12
     code : |1+
      procÃ©dure abstraite PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
     type : 458752
   -
     name : Affichage
     procedure_id : 1894581804520631272
     type_code : 12
     code : |1+
      procÃ©dure abstraite Affichage()
      
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c5db19074789
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure id() : entier
         renvoyer :id
        type : 1966080
      -
        code : |1+
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
        type : 2031616
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1694f0d209bd015c
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ISignalEnregistrement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ISignalEnregistrement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1694f5f109d00013
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1-
      ISignalEnregistrement est une Classe
      	implÃ©mente IAcquisitionProc
      	id est un entier = 0
      fin
       
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627195780988207123
     type_code : 27
     code : |1-
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627195780988272659
     type_code : 28
     code : |1-
      procÃ©dure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627195927017260696
     type_code : 12
     code : |1+
      procÃ©dure abstraite DemarreAcquisition() : boolÃ©en
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627196017211660245
     type_code : 12
     code : |1+
      procÃ©dure abstraite ArreteAcquisition() : boolÃ©en
     type : 458752
   -
     name : Parametre
     procedure_id : 1627196240550011434
     type_code : 12
     code : |1+
      procÃ©dure abstraite Parametre(nomFichier)
     type : 458752
   -
     name : EcritDansFichier
     procedure_id : 1627989705976820618
     type_code : 12
     code : |1+
      procÃ©dure abstraite EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c5b91900c365
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure id() : entier
         renvoyer :id
        type : 1966080
      -
        code : |1+
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
        type : 2031616
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1694f5ef09cdf872
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ISignalTraitement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ISignalTraitement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169139d811bdb788
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1-
      ISignalTraitement est une Classe
      	implemente IAcquisitionProc
      	id est un entier = 0
      fin
       
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1626144540630890376
     type_code : 27
     code : |1-
      procÃ©dure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1626144540630955912
     type_code : 28
     code : |1-
      procÃ©dure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627219562222878244
     type_code : 12
     code : |1+
      procÃ©dure abstraite DemarreAcquisition() :booleen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627219665302379617
     type_code : 12
     code : |1+
      procÃ©dure abstraite ArreteAcquisition() :boolÃ©en
     type : 458752
   -
     name : Parametre
     procedure_id : 1627976928344035087
     type_code : 12
     code : |1+
      procÃ©dure abstraite Parametre(params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1627990650870500942
     type_code : 12
     code : |1+
      procÃ©dure abstraite Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c53e18f3e260
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure id() : entier
          renvoyer :id
        type : 1966080
      -
        code : |1+
         procÃ©dure id(Valeur est un entier)
         :id=Valeur
        type : 2031616
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x16cc3fd200aae0b3
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ITreuilClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ITreuilClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1687bd580028e105
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      ITreuilClient est une Classe
      	herite IClientSocket
      	type est EnumTreuilType
      	
      privÃ©
      	bufferLecture est un buffer
      fin
      
      EnumTreuilType est une Ã©numÃ©ration
      	eEmetteur
      	eRecepteur
      	eNonIdentifie
      fin
      
      CONSTANTE
      	CST_SOCKMSG_SEP = "_"
      	Size_MSG_Socket_RX = 21
      
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_YYY)
      	SOCKMSG_PING				= "PING0000"
      	
      	SOCKMSG_REBOOT				= "RSTR0002"
      //	SOCKMSG_QUIT				= "DECO0000"	
      //	SOCKMSG_KILL				= "DECO0003"
      //	SOCKMSG_KILL_SCRIPT			= "KLSH0000"
      	
      	SOCKMSG_TYPE_CFG_EMETTEUR	= "TYPE0001"
      	SOCKMSG_TYPE_CFG_RECEPTEUR	= "TYPE0002"
      	SOCKMSG_TYPE_CFG_NONDEF		= "TYPE0003"
      	SOCKMSG_TYPE_DEMANDE		= "TYPE0004"
      	SOCKREP_TYPE_TREUIL_EMETEUR		= "JESUISEMETTEUR"
      	SOCKREP_TYPE_TREUIL_RECEPTEUR	= "RECEPTEUR"
      	SOCKREP_TYPE_TREUIL_NDEF		= "NonDÃ©fini"
      	
      	SOCKMSG_MOTEUR_START		= "STRT0000"
      	SOCKMSG_MOTEUR_SET_STOPDIST	= "STRT0001"
      	SOCKMSG_MOTEUR_STOP			= "STOP0000"
      	
      	SOCKMSG_ACQUISITION			= "ACQU&&&&"
      	
      	SOCKMSG_CHANGE_SENS			= "CHSN&&&&"
      	SOCKMSG_IDID				= "IDID&&&&"
      	
      	SOCKMSG_MESURE_DEMARRE		= "DEMM0000"
      	SOCKMSG_MESURE_ARRETE		= "ARRT0000"		
      	
      	SOCKMSG_NUMERO_TREUIL		= "QNUM0000"
      	
      	SOCKMSG_QUESTIONNEAVS		= "QAVS0000"
      	
      	SOCKMSG_POSITION_ENCOURS	= "QPOS0000"
      	
      	SOCKMSG_VITESSE_ENCOURS		= "QVIT0000"
      	
      	SOCKMSG_RAZTREUIL			= "RAZT0000"
      	
      	SOCKMSG_SENS_UP				= "SENS0000"
      	SOCKMSG_SENS_DOWN			= "SENS0001"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_ENERGIE+<xxxx>) avec xxxx = 0000 Ã  0084 en V
      	SOCKMSG_FIXE_ENERGIE		= "VPRO"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GOTO+<xxxx>) avec xx = 0000 Ã  9999 en cm
      	SOCKMSG_DEPLACE_SONDE_VERS	= "GOTO"	
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_FREQ+<xxxx>) avec xxxx = 0000 Ã  0020 en Hz
      	SOCKMSG_FIXE_FREQ			= "CLAC"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GAIN+<xxx>) avec xxx = 001 Ã  016
      	SOCKMSG_FIXE_GAIN			= "COMM_"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_SENS_YYY)
      	// SocketEcrit(<SocketCible>,SOCKMSG_PWM_YYY+<xxxx>) avec xxxx = 0001 Ã  0020
      	SOCKMSG_PWM_UP				= "PWMU"
      	SOCKMSG_PWM_DWN				= "PWMD"
      	
      	// usage
      	// 0 : temporel
      	// 1 : spatial
      	// SocketEcrit(<SocketCible>,SOCKMSG_MODE+<xxxx>) avec xxxx = 0000 Ã  0001
      	SOCKMSG_MODE_TEMPOREL		= "MODE0000"
      	SOCKMSG_MODE_SPATIAL		= "MODE0001"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 Ã  4000
      	SOCKMSG_DEPLACEMENT_UP		= "DEPU"
      	SOCKMSG_DEPLACEMENT_DOWN	= "DEPD"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 Ã  4000
      	SOCKMSG_FIXE_POS_TREUIL		= "FPTR"
      	SOCKMSG_FIXE_POS_SYNCH		= "FPSY"
      
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623474376347869445
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474376347934981
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
     type : 655360
   -
     name : EcouteSocket
     procedure_id : 1623533668379034263
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : _synchroniseTrameDansBuffer
     procedure_id : 1643602633264841279
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : ParseDonnees
     procedure_id : 1643598492914224755
     type_code : 12
     code : |1+
      procÃ©dure abstraite ParseDonnees(buf est un buffer)
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bd2b0022318b
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Logger.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Logger
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a22bf06bced39
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628652419095063865
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628652419095129401
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : EcritLigne
     procedure_id : 1628652419095194937
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Init
     procedure_id : 1628652419095260473
     type_code : 12
     code : |1+
      procÃ©dure publique globale Init()
      
      nomFichier = "trace.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCrÃ©ationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrÃ©e(mutex,Faux,partageAucun)
     type : 458752
   -
     name : Quit
     procedure_id : 1628652419095326009
     type_code : 12
     code : |1-
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
     type : 458752
   -
     name : Log
     procedure_id : 1628652419095391545
     type_code : 12
     code : |1+
      procÃ©dure publique globale Log(msg est chaine)
      
      renvoyer EcritLigne(msg,NiveauEnum.ELog)
     type : 458752
   -
     name : Debug
     procedure_id : 1628652419095457081
     type_code : 12
     code : |1+
      procÃ©dure publique globale Debug(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EDebug)
     type : 458752
   -
     name : Verbose
     procedure_id : 1628652419095522617
     type_code : 12
     code : |1+
      procÃ©dure publique globale Verbose(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.ETrace)
     type : 458752
   -
     name : V
     procedure_id : 1628652419095588153
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Erreur
     procedure_id : 1628653419859245265
     type_code : 12
     code : |1+
      procÃ©dure publique globale Erreur(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EErreur)
     type : 458752
   -
     name : Message
     procedure_id : 1893788137531391711
     type_code : 12
     code : |1+
      procÃ©dure publique globale Message(msg est une chaine)
      
      EcritLigne(msg,ELog,Faux,Vrai)
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x168d080307cc1cde
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\LoggerErreur.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : LoggerErreur
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a256408f640e6
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628655326825234662
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628655326825300198
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : EcritLigne
     procedure_id : 1628655326825365734
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Init
     procedure_id : 1628655326825431270
     type_code : 12
     code : |1+
      procÃ©dure publique globale Init()
      
      nomFichier = "erreur.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCrÃ©ationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrÃ©e(mutex,Faux,partageAucun)
     type : 458752
   -
     name : Quit
     procedure_id : 1628655326825496806
     type_code : 12
     code : |1-
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
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x168c710e00a5cf7a
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\main.wdw

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : main
 major_version : 28
 minor_version : 0
 type : 2
 description : ""
 subtype : 0
window :
 name : main
 identifier : 0x26cd911c00d40a1f
 internal_properties : CAAAAAgAAABEuD+b0L1yQlgBBSD79Zh51WxJRUI1n7x//NA6N2YNZzLFMPW4AFzq1aa7oSRjMSxkeU06La3O8BrbpRDa8ibbeiJa13GXokwQMjXgTYlMhC/cK1eGZUGYB1wwUA6PSOBItizHGvb5a3Zsi5y9xbUxBLlf957dNJdjV64xbdrAV3WH8Rq0/LI/H6YwtdXNPmsHVsxnLhCQegCYC6xZowFZ51qbzPLjeYHiMoNtSvVFYAg85OuvVHOW28A251caZb0naq7fh7gZ9c1jRmr1ClXCztKJatq5hYmv3l1OhKslj6iLICmPj/Km18eG0pJqFV+nx3bw8GvtVtcsKqWI2xZGRrN4r0KZv0fZ19Ev9o41IvK1MU/a3v4OLrFlAlsDNkfNpo5aYEM/ZgpcrJcb7VXZbrz7ktbLelCaJA2460z1PlfZ6ydAQc76gpY9An0jrDzKg1EquROnqS+sgDEcEWzK1goBLWg8zNipYj922ETBaKi9OoSgiZ5MAwQKX7M6Dv8jqbH8kr15R+/R/L084WCTKU6vyqMmFnnN5xpj07nGmirEFbx9V2E6dpIXA3N5XXTlJ1Q7xeNJPDvRElSpM0iP0h4bDUIoVgGbIPF0bwul2C/zh7VOs0tMPADUPKcDgsd08GpU81aDrhzo48qf3baPevslLpU9Igbs
 properties :
  width : 1024
  height : 700
  widthClient : 1022
  heightClient : 668
 controls :
  -
    name : Client_acquisition
    identifier : 0x26d583ad008e81e1
    internal_properties : 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
    properties :
     x : -520
     y : 466
     width : 498
     height : 94
    columns :
     -
       name : Colonne1
       identifier : 0x26d583ad008f81e1
       internal_properties : CAAAAAgAAACylI3lOe/gG9qLt4vupmgAW2qn1nr48e5aXJPMihpzqVRJWzC4t8q/4CL4gaiSy+TrI1HvonUeixW2dd1C0kmbGLPFqnHE7w5CwV5YqYa6rNdF0bjkA99rIISI8YXH0EVaDGaOHYOpx7KYpfaeQ/eqxMhaxxZPoXnhmBzcJNDbOynA+LA8SywCdLiy
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne2
       identifier : 0x26d583ad009081e1
       internal_properties : CAAAAAgAAACAsETNW0dEolJq7FXOBkCigNFYhyxO9C1kuBrciMSYVIQU8lWy7rhRBC7kgYoR9BbRjKnQBT8p9VSU2YReXmPcrqQjy/ickj/qrygNGz2X7qibke83g5JOMhRouEnoQz/JCv6JIS2KSfRqHF9MbAkUjZzutmaQ1WDb0MavUWcCZMZlO32vTC6c0et0VpJfr2w=
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne3
       identifier : 0x26d583ad009181e1
       internal_properties : CAAAAAgAAAALxz5m/je6RKsuOn8DB2evCKdpZZMXp8s7XEEyondlGL9540kMpWfY8oRq47Udm3oyApGeDuoJqfmJYj50giGx8SgEvc+J8D0aY7Q94FuaHG6NUiAbanHS1CLqiceFX5aqb9SXi5QXdxlBH9RJriWOs64nyYN9LwjaC/pvgxRLJMENMoj1jiqre8/xEIA3
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
    type : 9
    controls : []
    code_elements :
     type_code : 9
     p_codes :
      -
        type : 35
        enabled : false
      -
        type : 22
        enabled : false
      -
        type : 24
        enabled : false
      -
        type : 23
        enabled : false
      -
        type : 25
        enabled : false
      -
        code : |1+
         
         EnvoieSocketAuServerSurSelection(colonne1,"ACQ_"+ComplÃ¨te(Hasard(1,999),8," "),Client_acquisition)
        type : 33
      -
        type : 230
        enabled : false
      -
        type : 241
        enabled : false
      -
        type : 263
        enabled : false
      -
        type : 262
        enabled : false
      -
        type : 222
        enabled : false
      -
        type : 223
        enabled : false
      -
        type : 224
        enabled : false
  -
    name : Clients
    identifier : 0x26d583ad009b823f
    internal_properties : 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
    properties :
     x : -520
     y : 334
     width : 498
     height : 126
    columns :
     -
       name : Colonne1
       identifier : 0x26d583ad009c823f
       internal_properties : CAAAAAgAAABfczgMrEJTTAdEasgrYavLzJkqGQ0jxPP34T5t67mSCDXoimPDGi3Q7deLsvm17tfEXrxm2cSFAA5rKxdi/OMTlmthcD/TAKPfKiWXGCHhxV5UwElVmmjyu+9j3qqi/3ZtH3Hzui5eemH3BFc/ohZLZ2v1vCN61ixUC4+h4RMe+GT7owXp5sGZ31Pd
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne2
       identifier : 0x26d583ad009d823f
       internal_properties : CAAAAAgAAADF9R8AHgAzLc3BC2Q33ykJ/f6nHlPfR6zjO+VfDVEN5xGBjeAtewfMq1VzbJmi5SEi9WC3FORgtBOP0+b4XB9+amhbe0byOF/B/vlcoKD2vbeqoh6mTNkL20V1q8x9aC7aG4t+SsQj4F8Fp9T535ypIC95O+MTUt9aX1XYug7lSS3MkpaAs5cHTJavE1ea6ik=
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne3
       identifier : 0x26d583ad009e823f
       internal_properties : CAAAAAgAAABwvJYn/qwawzTRfDxCdiV+Hhxx7/RCjnCXZ+DjXeQlRJrVQmc4c4WaUsJfBpe+ktlbSHTJAxDCSE2aHeq8PpSLu49ChZmZINWTLHQDrw4mLlhgM9OHVfN0iYUjmqCvbJrd2Q6+oKeHdEIHSZVsqWucQFbmEWj1HxOfI6oGjh6i3d6OafeMhXTIyDCZK18=
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
    type : 9
    controls : []
    code_elements :
     type_code : 9
     p_codes :
      -
        type : 35
        enabled : false
      -
        type : 22
        enabled : false
      -
        type : 24
        enabled : false
      -
        type : 23
        enabled : false
      -
        type : 25
        enabled : false
      -
        code : |1+
         
         EnvoieSocketAuServerSurSelection(Colonne1,"COMM"+ComplÃ¨te(Hasard(1,999),8," "),Clients)
         
        type : 33
      -
        type : 230
        enabled : false
      -
        type : 241
        enabled : false
      -
        type : 263
        enabled : false
      -
        type : 262
        enabled : false
      -
        type : 222
        enabled : false
      -
        type : 223
        enabled : false
      -
        type : 224
        enabled : false
  -
    name : Clients_socket
    identifier : 0x26d583ad00ac826e
    internal_properties : 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
    properties :
     x : 252
     y : 12
     width : 498
     height : 150
    columns :
     -
       name : Colonne1
       identifier : 0x26d583ad00ad826e
       internal_properties : CAAAAAgAAAD43vMHX01OuXRRVSnMzBYmAdxtWMhuDzQcsmFi2MCpR3rT4Uo2PUDFarh+By4YQWph1TdBAbtureeUVIbWaPPHGvsFXNPp+pWdhPPttod7Q2hY/sGVcEHNRnr+ew9NSsPYguwIlwkzUQguy4hoiSlYEp6gAei9E+sXTkL2Prb92UfmlhLe6c6kKu54
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne2
       identifier : 0x26d583ad00ae826e
       internal_properties : CAAAAAgAAADpD26zreiNg3j+MshW3WZp5/R2NYJ5SI26JqTFizPdsTbSVW5Rc32fR0kkN4F44mDlsDt88oWb6weix8h77ugI5qywmLEnpbsql4NQtrkYiqoFRWO9dHgH+yUIermVHbTNUWJwGHLG/u7Zo3N5tPhbzLuJrRNK2irNKj1NzXKwVPfDGqRGfQ==
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne3
       identifier : 0x26d583ad00af826e
       internal_properties : CAAAAAgAAACugf60vW5ffoMqsp7VqbN2jKReQymaY1mqsv1reZRg1jbUeCB0Q+gxcfJ9WeGyLAy5BJG5FOW3CFYldCj/vkKZUg66mkfrbG6baTWJdvYkx5nt1r3Vphcy+pHE6dkSxRUZYTwk6OUJaL+Rns6DWY68kWNoNUz0GR6VjVzSoNWf/Xmyq42YkmdsBg==
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne4
       identifier : 0x26d583ad00b0826e
       internal_properties : CAAAAAgAAAB83LCpd2soNiZegOmiUrQG5PWsC5AagOkw9DZIdCiUUOCw3qFuskzlmKIYBX79SHqlMK1kaVsFIbCvQGvM8KOOFiQfN3p25CtHNPNaJiJoLwWIhCS8ti7qnoBE5D0kj4u1fmodnakOzUg+YBM4kG34kZjSUtpkoawXnBIbxRPeQCI5ByELKALwZX+Yau7zE9A=
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Colonne5
       identifier : 0x26d583ad00b1826e
       internal_properties : CAAAAAgAAAD/KwIiqqMmwCeqtuP/Y8PL1LMVeWd7Cz/fcJV+FsvpnDP9Z8WIEaPkPvCml8HJJ9beXg3yUj6NqBFb/Tlq0lkZBRREtWXN7C7pgunAd4L/B3WcUW3KffVWqHa+/XPp8yreGwjjP9hTM8W9m1DNKqEKDwLz/V9ZA1xunw4TbziviKVplhR5AqYn53st3FRD
       type : 2
       content :
        internal_properties : CAAAAAgAAABM6fmkKMlVPeQbCfcsBDYyTcTTUIm5Re5/5V1ZOOsLrRw0i1veFC5iew46DoY6Ldj4prYQVsteEm2CItj12JTSHG6e1pYufqpWZvOqWNse08v4MWsahqrZAu+1E6cVC842IgmUYVk8XFKD6jaGtTcuLSnEZEyKy7sEhjSIMb/nja4mvGbu9uAqidRMFB42y6kjOv4vVnC7yPXW8in4Ispc/AhC69qXsPRX7FQNBa/WxkOrPxwdZOMoUW7SAXTvAr5S5b+ulHdWxL6z2tOm/IhCswihvBwHE44KVew558EgHs0D+zAm+661Z/FsKVw6m0/sXhKWvlnpGfuI+Jy/ddyEXvk9iLgxU7V5ZXEwUdOzNScvI0c6idKwy/LtVIZevLZQ7aAlztoqVqDuOKANwHSvajXHfxvBTLOP3UXKXxXgc51suHR1ByMkay7s/aftwZBpL5xshQ+/enTMj4dgzephlF/R5+rheK+STRGgvoLsEXukd79efJm20ESxkGRANzjAT2Y2kUMMMTwoA3uQXe+54NLRfMYewVanNOzxTiASMeAKOUx9gaDwwEOLMX3yq7g=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAD2Rkbx+OjeaV6cz3eLdX7jB0Jgs6WDDXWz15HdSBZm9oeZQ2BKnjRAOa1wH4EQ9d5x
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
    type : 9
    controls : []
    code_elements :
     type_code : 9
     p_codes :
      -
        type : 35
        enabled : false
      -
        type : 22
        enabled : false
      -
        type : 24
        enabled : false
      -
        type : 23
        enabled : false
      -
        type : 25
        enabled : false
      -
        code : |1+
         
         //EnvoieSocketAuClientSurSelection(Colonne1, "id__"+ComplÃ¨te(Hasard(1,999),8," "))
         EnvoieSocketAuClientSurSelection(Colonne1, ComplÃ¨te(Saisie1,8," "))
         
        type : 33
      -
        type : 230
        enabled : false
      -
        type : 241
        enabled : false
      -
        type : 263
        enabled : false
      -
        type : 262
        enabled : false
      -
        type : 222
        enabled : false
      -
        type : 223
        enabled : false
      -
        type : 224
        enabled : false
  -
    name : arrÃªte_acquisition
    identifier : 0x26d583ad00b782cc
    internal_properties : CAAAAAgAAAB+UxcrbXOqv5EgTzzC6eEDxdywAUGkkJIhYA4PK1XyUifTkW+Gtw5xrBWl4Nrt/3J4/TEVCT0+F4cW5wg8FYgKMGqHhoza5XpChxa1OoI8JTLXwZefDjAyqTzPQvUE9Kj9Bqgd5Tt6cK1bF0u5ze34Z8Mh6jQvRhsa+PEKZK8+zkMhz9SUjOxQhEacWHvcXkr2aG3P2AYpEYIT0XqFXaHR1ljZ3V/740assL1Yrr8y9+oqr+WS6/+M6nO/4myeml74p/34bE4cm98WMvDh9k/6kLYQJp98/UJ914mmFDAI3kafWgKh0reNPvFx3MJODSSaFSvdQNKUAxc94j1Vlz/hUfnT96nzBJ/DtA2dWq5HZTsUrU6+AyUsMj02p50Qvk/sNaxSFqqqY69cI8pqmgrXOj83yEVjqo91Qt8Tb5t1xzPTJRsAtt8sMcWeeOFPkgjiUtUfU7EoljPcJ5uVWF1p4ZFavUjqQcAilnsy9Xw577aOsEEKq0WkRwWYJ0/pjLgju1P12/P7hEIIR9CAU0KYW60Jr9QBXCDy+AFKcUGoTgNqKGHFN2NDYRPCH0lYmyKJoAKFqw1ZX49ZzdxRz9HJRwjYZ+otpjZxZPo3T/3k9Vi3I1lIAy7Ji6byrjCh6XkYfUE74RNjrhkTRim7uBgMXVr3/N5Hmwxs1c1dncrx9rHsgoFHBVxe5i/AGQR9V8kGkJo0IW6085oabdzlofxXCCWL/mVjp/fzavGgcPpJmYKrmU3xt5lntf8CXUodvVPy
    properties :
     x : -264
     y : 59
     width : 140
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1-
         FermeSocket100("Acquisition",Client_acquisition)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : demarre_server
    identifier : 0x26d583ad00c18377
    internal_properties : CAAAAAgAAAAY5bjtYFVuq6EGwqA8C50Bb87Oym4bPMAJcUxYPT5n/E3itHbUZGDha6MIr8BCCQ0d3IEwlX73z2rKO5VBSx6WvRyNgecl3iYfyh/gXz9Lf9qiVW5pEXe125OCBIPNj7OrzFdPIY2s3HFPFpad6rRGat7RYY90Apq8EPQYzG9/01zOglAPygBgb7kFV4icRpey3KYF+0tQM9lEFE8T1dn8hOoYQCBvlVSnCwHq7Bl+76q5Bs6ZB7U7ZxLK7VXFaTAd0606G6d9toGdbxD+E7aOSsSpg3noBNCYktVIv3CsOFIOVQfMUYOyN2Q0flbfGhYi3F5+1NRbDNyoNrWTCTLwDZ0IbjzgGMnLO4UGF/c04IB76x8TUVLdJTxHIGB6wIxSpfloez1EZGOYOkBhfIQZTJVa9mxEUhSX7ua5evnTrTXzQsE7ozFfryWD0K5zgmTuDC7jGqo21612PGnKcocf4fdfJ5sPVfU+XixwpEytUy5czRNbumFbHTnFkx6RUOi59xLfAOatr6pgdOATh8I0L/3KmX9eHkvaKvJVDMDjat7sYU69nzL7mdg3Hj8JV35Hpr5RjBPKP8+P6daztJAejlJUvTjJQz8KZCuJC/ll4fO31V0198P8mcaJLeTCxUeF9XnQ4UffQWoHuor/+Z091E8I+PvJyiCmNBMc8GAZKB02VNjrDyfdaOM3HtPdiO+Q7q6Rn7Gbk7pS
    properties :
     x : 12
     y : 12
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         server.Demarre()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : arrete_server
    identifier : 0x26d583ad00c383a6
    internal_properties : CAAAAAgAAABYWMTJvUF9VyP7WkTm3sXMYhLaskzCFbGyYAwDzVfG1dMr3OS/kfQYcEBce2cAzRlFXfJs2lD1S6vAkcAAnh/HwJG4ddcpYISGQe7+j7CsuEepPhRP1GUG9XaVDNCL1vqll6OBUD8SCSYhTS/qInCQdxAa5WHux258njw1dTRRAOqeyMp+qkqZn+ToFwQ1BwSP0KdSVFl9K/VrvSUe/IFdWb2B9ubidNki0E6eBt2LJZk/4ziI7RvLjrtTUoc6FXMc6myp0/6cHwOayB2rjUGmeAMk6nZU8++MyFJ+MjBpnR9Sg0EdmRf71+07K+pf2BNSvLqTSSDylQshAB9V+ibhHnGrFD5T7Mv95NN/UOVDy/jgKvd9CxD3cmB2HFxiy394qvT0HVc4xCaIFa7DhmMkNNpNFL7O4ggitjGFAPfuzLk4d0NbFyk7rgWUYhIrmc8WLRNbpuJswbxM2XkeqkLetX7aDYl9r+v3StFmTua4Jqwv3LH9FDnvWAXa+76VsdG9rtld5oS127myCyR3V6GzE6D5W8cCmLGbQjeddlpYi2Jz8FhjbeCdAJgqHGUf7uG/wX9LhjUKGxhrlMxXPDXnZKXhvYbnS1NNUhKDKz1+Agl86D+dYzG+07RJMJFccWk/cFDMF7VJsoPthjhi4nIyZj1FXguMl9qGdYHwmz2fVAws+NGBQ0Q+SmK1eTqycILCvxQI1mz4k8jzhB8=
    properties :
     x : 12
     y : 48
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         server.Arrete()
         ExÃ©cuteTraitement(arrÃªte_acquisition,trtClic)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : on1
    identifier : 0x26d6dac70139b8e9
    internal_properties : CAAAAAgAAAAfEej50VZ4yR/NaK2ljagnP9MJ0mt0hVypaZ85HkMAniSf6wjjqscw2Cj7+9OYSY84lnFsIU00W5qB205gHVMvHbzl2w3XEYVBniPjWNkxPdGORjtUEUPn4mcbyldBlV6CT1VeFpYA+v2pqWDFnm5vttFfUsDlI+lt3RYeI/OBW+wns9XSmPuF5b/+DKzh3srnQKad8AFwY/3XmkcBOOIX8sMb2JUbErOAIeeRlDGnl1M6q5g4zfBt5jk+eVJ36JqoMh8On8bCUUKitRUZvHsNzkAwSgJz6E4S/Z8avnNHJ5l+UDFdOlScF7gSheKGv1VQtYHnC2RGJ7BSUULCtisy77E3xSjUly3AoK9NlnH5ruqeZty9Ho/m5z2rZahGDV3ROOj0xUnv0l2AUfKOP8JhzO/xRAA7lGtyu3wP54rSe+iDLUSTaGK+5CRQAkOXzFYjRm+ACc+Zb8V0E5dLQKRd3A5fHvEscNNb8zP2S/3SBZr0T779QnuMQpNe2cf6eXfMvFrZ52Bsu+1A4ZYzxwPU9SFjw6zduoagPEpyptuYOzN4pH0tHAjrly0817GuRygMiGUc9iOphpDGhcfwHJfBCgzww2rWt++B+ZtCPnWwfGBSKdkkFd+4aWUcxfY3ko8s/FLai7f9btFQhTDbsgOxPG+dcKGYTbEowPSeJKxEHT1a+xDB9PIy6lEFl5vRm/4iIAxFj0vRNS8=
    properties :
     x : -89
     y : 87
     width : 59
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
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
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : off
    identifier : 0x26d742f102769be0
    internal_properties : CAAAAAgAAADP6Vev3z6AHppCTctWLdDadmokWEkFHx4oC4g6hquJ7kcWK3USUkQtt6lt+80ryWKOAF4i46vpt9X2jpwy22pEX0GUGY283Y9tpp0r+wo2eqUsqSgspyDkY22p5vXPbvz/31W42ms/5r0DJ1RZP5g4doeHmM7+cfaNYIVy8RVo0EVh9scQMWueYzyZRC/dI1X1WlHh2mrYsUDjQjLgHdgXWocthEplxaSH5Yevgbr9ab1Lxyqr/t/Cb7XbOLJ2uKq/RVwDY910q7VXYu+hqNdf15Y22kJ6YPBhtgHFXmMpoTrSnHhtYpqTFHOtwDDr5UX1CcS7IESLK8muspl2fB+We4b2/kc/cwAXuTxDvi7oZo3KUaFXZasQxTR0bCZYaAlqtL1f/+gHUPO9/C+SOzd9L10ouzoooosOwA+M7RSfFRMNTlUp6ZaUJDmbv3fDI/gwQsLdQTazqw3lYqYR+hfJZXDAB7/FFLlxDPLVWf/vl6XA2oWJXSdaHqBNxN+iMLmdwseQtzHIXbmVkCXRafZSmE9YIsaaBiD224cTtJ0nAzY2gyALHazdM3xV2ElWwtbLItbXmlg1QJwBoiCk+GL/zXz3CoCXbfsFgEDXRI+9IDXEpIQo6HNm0cSBjhbnSx2rpBqqWuhckWu4SIO42G310bOU9niQwVNRwL7OfQc6reBnCskwWNKBkWlmXHgE/Fd39MkLW/WTBxeCE4g=
    properties :
     x : -89
     y : 111
     width : 59
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1-
         arreteTimerSysAcq = vrai
         server.ArreteAcquisition()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Bouton1
    identifier : 0x295fbe0900c02ef8
    internal_properties : CAAAAAgAAACHsR9fsCqOl9J6wQQmHaaEFMZutrKW/0I7DBQUtau/nnZVw1AeKdnvPDJi62zDxyVQF8hBSX+Y+4IvG17KpTiYyLOtRhVQ3NSkYwAA4kgl6UkCOwNRrw3yr7k3aplyff0mqLNswAHI4VCdcz6rzk/CuPS2insfYNDrIa/noNazaN0356eH/A3xZ+QEFtWRoyMNvsYLVYGd+6D+3tDZsSyLpoj/+g/tuxZosB+H8TuRT6OUFC4DfoBWFNN+5pd+tgNHrQM5u6WKmOteszLqZq+VbQb3oOd1Yk+7PdPiYHM0SjX5GCnbu146OXhZE38bSkK75eKZW1WcJ6RCkOVpqZBUtxnzRU5PED5tZ9yeg5pPsXtvKf2tCzif/bDoY6jAcqpXr/L6Eyo/Z9ct8Jf8MTh+TnG6pxXhmJG1NzY/K9GY6B3nrzdLIM9VANs2WsToSBrjSjvQN/aH068uOdsNjKlfBCGA0UD6VVdbLA9yEDp0knyEwNM2QnlmdnOPwF6zjRPzzbgLK1mK745gK+BOnsaW/MKsvfMVCKFO538bMEycCKUfpyw7FMn0jT2V6K0i1+p2P6P7VmwVbc+cDR2HNdXsr1kN2QJPdOBF0X/9M51R5Q02zGqqz71Llh0mihYtE2ksInvIpAavZ3ymOuxBTOcb4CEPBC7i51TMhA6OFIMnLWx8CPZfqTB6Jt6h37nT0aIjZDk7irI8p386W0A=
    properties :
     x : -276
     y : 192
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         grSupprimeSÃ©rie(Graphe1,1,grDonnÃ©es)
         y est reel
         	rd est un entier = Hasard(5,30)
         pour x=1 _a_ 100
         	y = Sinus(2*180*x/rd)
         	grNuageAjouteDonnÃ©eXY(Graphe1,1,x,y)
         FIN
         grDessine(Graphe1)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Image1
    identifier : 0x295fe2ef01994f06
    internal_properties : CAAAAAgAAAAi5Lvhj5hJzZcuBb+IS6B/a+fm+WFKWW8qP+tVDEhZsH1/Hm+owMCZZHhMYOp4mEoVt36G5kqyQwN4L/8BAoAPb2QG9fHzssGgIJS0vWGznszMHigEtPCggiHmCTU5AsGXjYYahpnVVWA4V+ZV1GpMmhUCUNeWENlzQDRjbtgN7PM596HTuy5vQB14JHD3kU0rqqTsc1PJMQUNk6FCta50xyFpUNXvQjBGdnkqC/3F6aXTXkR/GNp1OIa57CFsJr5EqVGyWAb+aQ3Cx1Qbx1WWYqiR1XPBTebE++UOiUUU0o6qbY5CqWDDm33sYTlsXpdbfjmFcAnbNULg+hNuzh6s3QwXxs1mX1Ut+pE+/G2Ocf2gVAbzoYn4emk8DBNBhvMAfNcF7QfUwL2N2TnmKeAU1EGVeWBgnX3fPmx7kMNKE/qguZlodit9N9PbrV6kgvATbCRw23ToOG117i7juKIlAabnwULGRCH88MeJPCJ6/kJWJ2KlDzggoJqh72UrwLxDuH439zTRtfY06F/PuW773ROe4wT9M4BtibtQiLUJH6Pphvi0YNGo0xS1Z82RzUc0AG1SV0ca4QfDka+U3bvDPXZ9OXg/BXY6+uv0hQUwtGaOT3RcbLc=
    properties :
     x : -180
     y : 180
     width : 163
     height : 135
    type : 8
    code_elements :
     type_code : 8
     p_codes : []
  -
    name : Bouton2
    identifier : 0x296113ae027bb601
    internal_properties : CAAAAAgAAABzDDzyYQoA/Z3MBB4WU/f0gJlp3VqXeHKef6AXWqBvlVcwHOf+thXsVbKtKpoDPQpTe6JttZJCRGBwjdJcYjghr9JSmnqBggbHFzSyoTC0mXTUK6zVk3xyX/FIg9Kdfgg/W5mFNYs3h50YdjiMN4GriOjOYHDtvTZR8QgATtMqZPJ08sPVUbUXZaY4WRHRuziDHN3ZtT1fZ2cOyY1NJk0XsMKpoG5hmQI2uGYJoY8rF7ePgrp2aUffBHjBWGczYIzrQWeyL2P/Rz+rwU8C9Mn6qEiGywbC3g33OGOFvo7YxM2p18HztsQpayNLDWcaiIAcgAT3UE1nS/QJBYwrAb/tAaA7uQOw6521MZN8J+lqAooNeYkhoyLt3S1sy8GRqMt2e2EmpK5tE5wf0Gy7rhqZuIHqJiWXqZBT28yAgxIsSFDXCLX1ujWE3CK90Kzyjeg1JKiIKdvbFBz/N/aFqaZEhkl6fDBKeGjTsIpNv+Ezbi5LZJi076YpiQe+SmS5gB+kNAlh8PC77Ff9g5GwZsIsgRAbfFauJJN2QpPGn1vTGWmXRz8imhWsd5G8m2L+4yF6WvSL86HbOS1ZBpBvOIXGRRzi7eXp5i+5jKCIRmfRwWjWTBZnPYhw9HFmYNl7Q1sSPSYeykc+9uj3+V+m7tFFNmQtQ3vLVG2IxSB+80Y6IR7Y7Dirb+kz+tceYR6KhT7rr3Xyr5OxvpF5
    properties :
     x : -276
     y : 228
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         grCrÃ©e(":nomGrapheVirtuel",grCourbe)
         //grDestinationImage(:nomGrapheVirtuel, :champimage)
         grDestinationChamp(":nomGrapheVirtuel",{"main.Graphe1", indChamp})
        type : 14
      -
        internal_properties : CAAAAAgAAAAUeUSjYmoUVqFbJA/Do3wVzpDC8bnedWgwaB+s7TNH/rK+IdE4eJMnw0FVrzoCYnGa0/jJGmMQiCYXOwU7vhEMll4/mya/+8LCQZF7jwKE6g8ePT4yDzlAZEbHsQHPrhv4tlEBDp6FPQeutmP3GrdNHhie50IW1phL1IJ+UA4yUDdvFQdflbawB+jlMMMT5CjfXiVbncQRhuDnfvTTaxR6VbBzlRnLU8hdGsu/8t/b8FCch+152Lqvif+eyLYaiBtepB1U9f2dnA==
        code : |1+
         
         grSupprimeSÃ©rie(":nomGrapheVirtuel",1)
         x,y sont des rÃ©els
         rand est entier =random(3,20)
         POUR i=1 _a_ 100
         	y=Sinus(2*180*i/rand)
         	grAjouteDonnÃ©e(":nomGrapheVirtuel", 1, y)
         FIN
         
         grDessine(":nomGrapheVirtuel")
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Graphe2
    identifier : 0x29619a4f0a219a9b
    internal_properties : CAAAAAgAAAAGLTPEqaUYOQg78Taiaf1RX4y+oHJrY+JGV/NRRXvibSN5MdRmaqhOWWkn+x2LY9YCWcEkXert8heahE871cC54zhK3mCHkIGHgKfBABPQPhLOhNUTegnnD1rYX7tbrrzgKkghXark2+FDnL0gobcNDLINMiE8gN/z82dnvLISBw/3u1QW1FtERju2M7QHetFsOQqcBDTMmZcaZQ2w121DUgGrGm1M2mE2KUZvpeCXK1KzUTpAvSNt1Xthkww0RgaTakD6req6fCUtJ3WNhNKwXMxLV7L92MxPHEw3uxIqbV0YWfpdsDAGTiZcMrDWj6GR1rhr7W16BMvUIqIyU1p2YuBEBT+B6tewWC2jWlrhFkBd25RLXOo4wiYwj7IWRKq9VkBZqvW5whzH9vPqwzhMA0/ETOCbEdE3sFi9wil0Ju1EaE99v/GJ1W93dE8Tc+oSfFK//utdFNn0p9jB6QKKKZ301oFa/65ebWe4HcdURjv6UQscVnSf22FbCPd1GRszbOQjAhatrdUYBpMJEbfEDP5uKjkiG/kNXbfcEme3cSX6GTFwa4nBMWz3ibbeB5MmmFn0C7rUR+UqkSgk31DD1Jx/+iZ7Pm/munbtawGsYkNkScgV9oq8s+lnz76RBNWzulVBu6MyjQAnWjYKKiaSjGZCjuctEfDXyy94QjjwGpOTHuqvBmgN5iP5ARCZMQN2pQGig/WL2v5UJZHeU4ib6mycwZB67eGmh9HtA28ign3hiJ4EME4sCRv3ZnlkeuAWgrLBT62RSU5eg7U7t+M49rMEPfLiOikkGwfwvG23LYJp9HCT3k6997UFdq+xYqqFf1hr7m5349QqxqT8WUT2Y2x7J46MKUIJD0VfbczHJ6gaOMiUaU9m6WkqsoxLYKMEueaz4qLLRCVuXJeMfru1z04rSXyEOFBm4J5B7InC5SLe+89+XI0pU++dMgOCcxfBBuHT6FaubhfiLuAAe2V1ltmMAIKiZkJK8Jbgv5tdz7zLij6KON2lWtbw0umtEGJQqcTBHzfsS5rAeEL9FqK0K5T37RR1yMaugSFGxukqDKyVpmrTRXjLAGKCpLU3LzVUBIXujgGSrr+hZfJcNo95WoOzuZ37AFZRmori7Oi9SCF3rD39VvF6Y40dtLqQVbnW8+0nedjXzK0T59MkSdye832rXD1F0Y3oCbkk10McFrypmaR97am1vmo=
    properties :
     x : 372
     y : 408
     width : 350
     height : 255
    type : 33
    code_elements :
     type_code : 72
     p_codes : []
  -
    name : dÃ©marre_client_inconnu
    identifier : 0x296529f61182af72
    internal_properties : CAAAAAgAAABg3oivgDW7h7+Cbwkh02AaHjt9ca3UO9N53j/jjMxj9H36gA1Wq9bKS/9qEQ1Wd6HBfFj/y15F4iYbwLhkY6lTXmnFSrJFiD2xbWv/1wB/eX59QvNn5t1vRLYxAvVIxNXBX71JqtRZiyjm6AILtCRvZ5Qf25WkpuQIIGaNAG9Kqo+ShZChf4PrI9wX9iA6IqPPGjcUWHPViu90+v5vL/6d2GGSl/aB6rY0BUbYc2kRjtH2nqQ80OYuJOs8zJifxWkM9RvB8Fr6NNM/elxoLNfHsP/Sl8bO6ITKOFNrLs+Zc1rX6bCYTzks079zlvmeT5ChiqbkoJm8NoKrg69gxJRi9ibrUpNnrVe8IBQIpmTnP2uWp5d8or9/tvKlsshWomeaf/px68R4ZUyTfzf259f9XTIbC9hCGap5fm68pRzXhflAOZfPAxuZCTv460MnDZ6Qc0sR6Q4OhjjBibJNTofRt3GF978Fs2ys2tH9o3W5jQm/dSsdAeBvexA27l+CnJ33rZAUL/E3XFbDhVOj58bCLtY1cmz8B/1juZCflsutJXb59Ip941WuekqKbrEOSs5lGQeLBD6imavv09JKQmYb5y9+wtRD7pjo1BcbVM89yBs/qei6Xpr1MHyQ/CyxQ1Xm5MN6VoPlRY6fy0mDWtoZ4+zvr9PVxB6vMeYTGtZ3GXpvM+nf7RZ2U55rhlw8AWOlF3fcLQYYZKFZ5vi6eRU6vIb50m0Wxt9zqBrZCyafuV3p
    properties :
     x : -170
     y : -3
     width : 140
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         tb est un tableau dynamique = server.RecupereClients()
         indice est entier = tb..Occurrence
         nomSock est chaine = "Client?_"+indice
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portTreuil),"inconnu", Clients)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Graphe1
    identifier : 0x296aa70f035ee88d
    internal_properties : CAAAAAgAAAA/YWxO6EyTNOOCjg5TyuL7BB7yJYLhtw24PsxvnS6OFkAZ8wHhhgHTggvXSrPp93A20W15lRNhxKQId7af9Rk/iNiIToVgdz4sqaC5Es7j80ICvLRLGwZbsnakC8NbEcgfTHsDkynpgAsM8wKegqsGcGbifBFvZOY7J4afe4SPuwdIEWO+1PPdzadfJ2MevBQf6cjQ1fcbSeEM/HtQPysDkJ6Mz80BiHIpNFxFQW2CRktmvZsGaspAwZRotjMmAoXq6Lax3/jmGOosZzWK9Jdta/BYeArqso40Wt7ngG4C8zW+7tIQ77cD/2lxEenjoD5rt2uOROv0C+sIOtYnb7HjnGdI7e7ZTdAXapTp7hIE6alUMmMJaYQLqP3CBFDahR2/wC2OooSm1aCMGG2JbjNw26TvHG1pGiTveb6oI3Gt0labWKtsbrayHjoyPTzlINjhRcBz683bSGmuweXAMPZeP0uyQgLK7ohQXPklefE9yiRRS3b0cLFzSbv935bqkWG/cue10sy2gdOIN4gmULhi5tRltzHVfrPGJzvoT5DTGgetAxbCJt25WE/YfwsryBSQPuqOLD2bVrRXopfl2GYYYuL6IZxFWONYKsI8gYJ4yqa4IJzdqGWl4LPCxySZmbj/n1xt1UblBRCY0T9tar7ZeT1EtDtHjZAyuKIS0R5z6sgNzu0sGzgv2+lERn8O4Yn5q/Ni9kcROTpmVxEGsCjI8LigY3ogKeTzUAkvHOpeGXaUJ/nmXXUq7tJAmrYdTgeDnwAmKJfnR/izb3d2dQ4r+pCSRlF8GsNCMOWBCX9kr/HLds4W34PAHn2TcBXX8xw8/IxZ90Wl8IQSZF0HVwMR54Es0bN3w47DRRbeDgqXtsjinanaTsIW7nJgz9fWYH2s6MJVQyrZ5F2XpoFrhYSR5U5go7v9NpRW5k0ZqJY7qt/TZg/9RG30+Abr0W7oTtDSQfQw6fP6FxcEVGrYnAC/IBAi7Asud20QNoFLYrmj0fcWJINg7uUWbrWGFzhmlJt3wbuP27FFmhEXk3ktDY5mY0kr8iDthLHQ0FHLfND51CgXy+R73KM0eixGbAJAPO61r+sQtj5F8ZI6UlPykh1MhwM8wa4rHGm1lCd+tALNrlB8gE1lgZeQyUeMz86VwpsSFX73b6h61tOXNgkY+2lRFIuM20FLnA==
    properties :
     x : 12
     y : 408
     width : 350
     height : 255
    type : 33
    code_elements :
     type_code : 72
     p_codes : []
  -
    name : DOTNET_Chart
    identifier : 0x297680ee09362a0d
    internal_properties : CAAAAAgAAADSOFJ6EPP5yZUEUZG6875tPtZ/CT8bXl78sK+7DvX1d1GFlBYGOzuI/w47hGUroQr/HvXZNB6sIsV/gQgSci8hAeLk9S3pdbJ+gvTB/s03SVcWfOYobE5KnGquFdHyJ1pUAO/8Ovi8OY69nasqP/IkQDWiQH+zPmJ9CWgx8Pg1KKPM43q3wMSszLo8Gsshg6D1rMrR8AKtWcoK3AgCkSl6s/KXrZlzAnJ89VVLDneeoahVvSz2exIY+lGMMrvxn6ZyApvH0zvBain9weo9HEYkk+/CuTEnaYhMKtuwymxSOaVfpJve7QlUturrmNiicBxCcSMKJUkVa+QRn3WVh+8PxZ73UEOrVzuv9YzWgO4pODG3dZwH/39kM0+edqPQzwYsSpqSxwcqzpXhxOw1yXJbXfAiqjVLaJcsrxdcgVxF/aMMJ0Ur8J/7tHhRcyv/xjPnIw==
    properties :
     x : 756
     y : 6
     width : 260
     height : 656
    type : 81
    code_elements :
     type_code : 113
     p_codes : []
  -
    name : Bouton4
    identifier : 0x29b9d439011b9271
    internal_properties : CAAAAAgAAADtRNhZJilTpeLoliAi37nG1uBwoG7lf6JHfU7I4Xlt2rGKuzvUthh/ZB9bAZFhCAPfgElfx/cseliPeJmi0rsjFLIcVpFaqJL3XIRsgyKIpqAJFKq5RBgnpfufZZcMbzMLosxpjNA8QNCHeWdQK5mL7+fHbE90KsVyLMxlY6WfqMCmduGZTQrVgJzAHJrVZEsk0rvbPJF/SkJ1dztcvEpDJ9n3jLhAgvpY8KQh2u4uOG0oT0AGbEQhZWxs1fwdHj58I49QFiHw8Zd3/hitKP83PMXmVWZKDTkILrvgGugnIcIoc1L0SWtfXW0Bsht0Hq5ZtxD7Lw0tF4Pd/ty0VeWeu4ddb+kbzCxQOxkxdf4YYpJwu6DiwgOcGqppEQuvtBquXh+fjPxFLyjA3i0sgUw/YubMbVmX7RmuwG5MAQqDq4V2kopEOpSIrnOdOclA2ERAPd4kwyFddPwGykTHba+NX3LdKg5MzEJlHsxHb5HT73jJadz+f+Aw7Ncjth1aTlgfTpjOjfH6kreKIuUoUt66PaSuJjIzo5YbxLROz005huqRpWcejm8GqqRDL3KGR5xNnYROv2DTdnipaQXIR4CosmlM8lk46MIzimEGXNATQdtIEzpw/FCaZWkHlBt7HaVtpPvvWJhcbJLgQHPYCKNVXCIOfV5utz/LgOtu38UBVl/0PYrtgci9cp7c9lSqVMsFDeiP
    properties :
     x : 18
     y : 389
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
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
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : emetteur
    identifier : 0x29ee86e20168498d
    internal_properties : CAAAAAgAAAAkYFDpO+ezKdvYfnpVO9LvzGHoXCQPlcmT7g/y0kg1xp/XWaAqRDyYvNMGZPnGsf1FY8g+joLsvxg6tRg6JKVUkK84a4qCsGqp+pNLwEdboWM/bOc3XQ2sTw8VgjRbeAfRxlkVwCnq+Obq0quS2gsLZFnreWBoBPoI9vTYfqjlzSavp4J3zxeVLmWHgq97tPYs/iLDvKh1XaU77h69dT8exG08hkoO8b9/kLANgxV5HuSciGs3QB6HM8a65lxcjAO4HCGT+mAGAyEWIMyGMu0yWY7d4c0Fsad16hgIfldVXvzo2wHMdxajTwE2V1aWmqiPCZsM2RZxEU5VATt8jjV10ZwTFMbi5WeSgEA6PQjWLd59x2PZCDWNGzN1qDAlVtQ7P22Zo9n37xl8cYfWsHlajm42dUCVij6ggwCsg9Wjhg6ihmX7iLp7AP7Wn1POaSmHv1KFbU+pTKem6dATIS+wBgfeAbeefjQbhRnzqRqZkvrL2EsJeiOFPo+WnNljsEySVWE2GaDUpgtm2/v5PPb9KZq+5vrh/ud0UAKnHBxnYos1Qzjv+Fz6Q1OGsDk7qmceV+IjVh0/ewRaldp000i4iAkPXGwwfcwBc2GXDqDxfdSVbo4aY3GnRb9avEAi53IAO7rm1lIKTLKLdB5C1t0tLTWt7oC3Vr5oUB1HzcAhm0NPfaxIZxtJArGbLrbZJI7SQnEAnl87yVqGN1W9
    properties :
     x : -116
     y : 24
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         //SocketEcrit(Clients[Clients].Colonne1, "TYPE_&&JESUISEMETTEUR")
         EnvoieSocketAuServerSurSelection(Clients[Clients].Colonne1,"TYPE_&&JESUISEMETTEUR",Clients)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : recepteur
    identifier : 0x29ee86eb016b6e2c
    internal_properties : CAAAAAgAAAAvgKsK3OEQBTxA6+aQfrBmDYVty+HqfQGi3ujK5iUpVqag0oj8pr9WbThdaFHKNGR5FOONAAnW8AB3hzFEQP8XFmamzBkvx2Y+4T+HxKZ0uLJGwJVnuKsEjEexUdVxLLciPtRCm7wet4BxmLVxm0igKz8my3Z6Ioc2+6G9hbOh/1eiOxj5XRcuHxKbGqpx0UQhfBIV2VLnzZ18p9Db20EBltjFFahKaoX43bPnfhKwohtOtOwaZp9GsB/MFOfbQiIOX9l0UKCApAiIeNp5/LbFoG5F/Oa37Oj4zsPpW67U/rKO5WAP1+2ngux8rnrralSB5dpDzCRdgMwHLOvKe9tWCfIDrcQrtQ4MTPI4gwahjryqxsAFG4jSKccR2fDz3Le5Okb4vS6gjEOYhXpyIC1BBKNBpobgMfWSPxwfk2sJdZL1IwALo2lCqKbm+mdg+2YDmp+csQLsMdNOkVt7ZY9zmnZrzk2TA0WLoYUWV365T27qAc4B0RogDrfG/f+FkqGUPL65K+HpuetATVCRrCLwxYt5Czj8qQbSKJJuVs5hs5voIJ3B8/eVXUN+MQ8bnmDU0IFC4Fa0cpysj7Oo/3ADHN7mORz3dO3Zp+3mzpKT8JZOwf8mliDePU+Sn/wMsQkO0TQHghT952ej0fau33cFkvXR4wgkMxZxh58bjYgiiKohlrXqJEVQApngrpccn+UXlTyzt639tdQmuZ10y6Q6Kw==
    properties :
     x : -110
     y : 57
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         //SocketEcrit(Clients[Clients].Colonne1, "TYPE_&JESUISRECEPTEUR")
         EnvoieSocketAuServerSurSelection(Clients[Clients].Colonne1,"TYPE_&JESUISRECEPTEUR",Clients)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Saisie1
    identifier : 0x29f0875608e8feaf
    internal_properties : CAAAAAgAAAAA0NZYeGreoT3U6k26Vzmft0gccW6DqYh54PZENZV4kuneaqFGmCCufEcsslyQFtNFlWXEadocpYtaZqlA+ZpcBpyBUjt7jl63bRji2T9xX0aeCVaKM/XkO/6r4dnX1eTYGAZIRuVPUiZrTczIl2zAqmrUlFrLcXGjtFXwDDilWJfowdEoAEZBBZdxtvkfj9/KGY1YIhLXBVOL2zYe29VucezM+H1KSgiurS7WZ9r2I+cidKz1ZbxyA6i4w2MdVPEquqRSWFGj/cB3VA1mrpP9X0ekIxNMuhmfz9OEoqlb29e8NZwnrsX32V9iUZ7bR5H2RhF725OwpZvLZlrcV689/J9n/+X3rOEqFl8nIk4WFZW8F5PQzx5cfgYFUd+IiA3Rz2z2lh+thzaVUniOshLT66atgYBKNnylJAd4mPaiP/7WbGPUejVxlJoh0SUK0LF0LZjb+XTHesn2IXACF7E7514e9XWZ0qo0yRdSxAxZxp9yiqwhs5ssGGRvnhdafP/GyFol8YMyU087mKAecVZ52YsuzbaDso5Ng1NlERjq+VslCIX4RH2h3jAb69mWu+fSVtU0Aa/e29VvWXg9oCL6g2Zmz3wQAsksGn56T84aZXFYl7yW4dNRQuV7NZlnzsfznRBj6njDM5HQuBnHK4OZzcPbOhn8L4hX33e5Zi+rX3zOveDE2INSPyAt7M0iMhULmZuC3wxegdn02LtF5B6XM6rhX/fno3snJystngdbJwypvLm/Fs1DCwoIHKfwbDsHbg8s5vx7
    properties :
     x : 12
     y : 80
     width : 123
     height : 24
    type : 2
    code_elements :
     type_code : 3
     p_codes :
      -
        type : 276
        enabled : false
      -
        type : 277
        enabled : false
      -
        type : 278
        enabled : false
  -
    name : send
    identifier : 0x29f0875c08ed1506
    internal_properties : CAAAAAgAAABxDjjwtpRiwt6x7mehtOOCa5a0nGxGgD5VLgVEgcAOl9rf48he/jgfH3nk4S6UGZt4/EUP1FgJSsoDSsXm7A09ZVeNIhispVQ/bjqpjKR4ZFglJsBsJ3khJsofFMIndkI7sVUFEME9c8p25gFshvC73WVbu3J6G4QRzENf+nzJZMWIX3GvucCh+XPYK0VECPWzvFRQ0K46wb/YxKbOhPzARN986nC9zYjoq3D7BxoQ4Bym8SvTk1kIBO96IUl6t2W4xWTIq+bRuQ+/+b8qHjnQ18kDveP9BHmZcUG630u5tm5dsScZ4bZHoAD1bBnqB5idKm/HtzUh5IqshwhR5qETpcSGI7GtiDdD7ckpT8QS/n4uc37c/EALa2MMhycDQNu5JnWa8MP2FdHMOJrd9B2hLHeYuu9P4YyoZw35MHXDl1ARatrOQEldhIBTRDSalYC1KDKWNsVK/KGUuB+0+zVF5NxUBPyc134vPbYEwEzPAdqeVNAYKvzNKGs/yRC1tnUStuV79v6fXg1GMBTM+iCuEQXODREbFUUEFBlywYwHpSUWQRGqegE0O5V/1RSHlqj4lOYIP9eW39q6JCWpH8QPDi5D+YeskgLJ++LEHwCRvs4YFkf/PbaHL0PVQ90MrRXumEjX8xtpOOcHfDBDCfHfrquqJzlLWecVhyb/KsI0UmZ/IY+R3fc/mvlVu6hwf03+unAwVoO1vJN7
    properties :
     x : -92
     y : 144
     width : 62
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        internal_properties : CAAAAAgAAAAGRigO8h9SvWn+9ZyKmWqHuZwNfy6nUHRzT5Ghxe1cP2k4NBdfpzPpSH6J+zmcxUQJkG99jT4Eg66Pwy4YZbxiqepbRM83GjdEaKOhNoXixHe4wd4umvhC3szI3w28oySv/JjrnEMPCrNf9w==
        code : |1+
         EnvoieSocketAuServerSurSelection(Clients_socket[Clients_socket].Colonne1,Saisie1,Clients)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : dÃ©marre_acquisition
    identifier : 0x26d583ad00b582ac
    internal_properties : CAAAAAgAAADGP1fQmDwH7Zf1JAz0LIYOcZqEjA1JFrW3a0MgWjxIhWFEK/30HkCn1+TpTfS9bIN5Q+bOmH2iuwdTDU0MS8ufoEtA75n+zCetjWtj6eC48iQuKKQpz9QkohIp3ppC4OcQCtK6W0sNJfR4sZFditDmcjoKZA9XtCwLRH2nIxsfpqaj81upRUofSqa7A04n3y1vFAz9pBrBkiTlHcsWWsH5luScfj6fcTl3qBBsLmgQeB9HvTEIOWPZHEvDU49g+LTUHMEeDH0GDqKhlyS0YmROilwvt8afwfc87+/fzrDVZJV451Ax4Mu2/hGvnVCwgEoBKg9yzO+515HCyw4Y0RmwvF+/bQ5/jnbP6tvNH/CwV7VI0NTZJDn+OVd7as4jmgii5siJY+DpJw0v+sa7T9Jd7JyBH6KZE2sPMySdPpq3G/lGFcb53hj4K0rfwnYEjPzk5WW8hlvVSMEBcO92Kz3bISh+mKfmehR4i2SKyM0RvbZL8r71Vf7yk4A3h0KsXH6Hut8qGhEC9sipRMZJqB4uLXBfPBtrs4p+JVghGEUH/OADchWPvGdSBc3hsv3QXZKtsX1K0GC3BvXA8073+8Z+aBtfIvCmNHpotefvZQolc+3OxC6BIj69mFn6vUPzJJ4j4sh9RTKIQ9Y4gwicZ5vRGKkxV+cZwNVH/ryU85Tjw1NnK/FfIJzQkkLFKqwDCJZoIcfQnykWcKzVbrhr8+HBM0ncYDmi
    properties :
     x : -264
     y : 27
     width : 140
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         nomSock est chaine = "Acquisition_"+Client_acquisition..Occurrence
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portAcquisition),"acquisition",Client_acquisition)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : dÃ©marre_client_app
    identifier : 0x2a0ed052048ea2c5
    internal_properties : CAAAAAgAAACTJ8nHa0h4+Eej8cLLsETWbyC/O1haeL8Jgk5JDDoWJjhuV4mLWclFzZziFDXDOv7BDiXEdDQMRh384xP+jOHsT29zrXfvyiSq4JOW3FZQnfELGT9lrufq8Bq5g7nzGSVWHEcowZj6r68WzdE4I12ZUUXFsrxEjUafW2A86XMA1PgQxtsn2q972tSsyynMWh5GiWDzfSP2pFnzKCpbOFOa6l0X7G6Y7b+H7BP5SuI4GvOO3pBLt780E59FevIDDSG4CJH/QNs98bs5j2f8n0TYIV8fIkRyNdJHay9Tx5QCWIXLQV94SwDa4Cn9liR+b6fL4179HAX4slIQSkQlNGaFyMO3JqmwPjzTKz05M0XZmUQHk4HfeS6V3iVJly0xHUTn4VLAlBNaFQy2Jdobkl4uHmwnvgEZtawXyRxvage2dhJ0zcJk/qGxAUaM3KiyPJ/ZL8nQ+H96gN4uO2P0l4S0zs2vfgqy94qGwSOQsmYG6NSjVwowNozRD2nGs2IvqfAyE/D/QOgTFu5SxZhMnjsV7QyFc42TIz9TwOiy1fy6kHU5FLNG2tGAFtdwh57hQw2gOwHe70smkzluDxGRa/NW4kVokyH2epb6xcF2B0xyW06xn6URP0abDGf0/eU4iEpGLx6joWGGep65Af3+j+uaIGfCof2toTZXQAea1D78DbkTLjmekl2x0Gh5EfMdRCAU43GrGd5PHzJBFnHR8ZTC15I8nVuZERkBekVS
    properties :
     x : -170
     y : -38
     width : 140
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         tb est un tableau dynamique = server.RecupereClients()
         indice est entier = tb..Occurrence
         nomSock est chaine = "App?_"+indice
         ConnecteSockets(nomSock,Val(FabriqueClient.PortEnum.portApplication),"application", Clients)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : position1
    identifier : 0x2a3eea0a01742904
    internal_properties : CAAAAAgAAADjxO/OYKTb1JF7OpnVe7Ht1ICFRPP0dQ3MgmiVXRGdoMx60mpyqvEWQ4DpLF/eOH6T8sMDYvkkjPYPq5Xa1pnU9m6+tJF3P17s0LXtGsSY0E52qE2LIAeg2CtluVn5oJ8WPuxKR3TyQ3RdbH0lQ9wwfydug5pybntyz4UFAQvtj3vaG0A1rUvaExYPoh6J9XzdXEK99ZqzKdnY24gvKxVpWqAVJcT6LnG06a8Hqlrsyo/WJOQedlu6TCvInKtDLjJ6L0EkBLisiGREXCot9JK9HHYVHAIPkEykQsdB14443l5W1Uh7l0ELnngANvZz5rRF5fJD6JThzHDr4Cse47++9VpbTZgzedo4gK6gZ8Zlxsb4pO7Tddqs+yXnb3792kXLMDz+++hibgXmG/jA6qt39rlv5ESyGzfUPdpV6QW361RzoY4x9bOM+hRUmMWC+VTNyM3Sj7xuK90E36Ghzw2xfLSpFAtNlXfNr6scXXhH7eiAr5RHy8xSUGUMq61v2B8OUsS/0fvLc0U28/5XdpB2H2nfqZ72i+j09iichDD3yb1uPvN3taE/b5H0z71N3Ga2Ng8gBhhuFBaynTE+ySZJxgQcZ+JZzkd7BUtMZOw5HrAMB8kopIK6NXdKp8TUaUEuuJG63XFQngzikNVdjuY6g+TQ4g/d4nGKeoJW4NvxqafEQQhtOUzfY1wh/T7NL0EvS+oLF03RA1zo81koB+D2Zw==
    properties :
     x : 12
     y : 110
     width : 79
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "QPOS0000"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : status
    identifier : 0x2a3eea6a01789d83
    internal_properties : CAAAAAgAAAAoyw352/VUXnmwVopWkoC17uU3ZIExx+xeYi8q3/1Cre/4hoHsrtY64wB2IaxlvPVWvjrdhMeTnEsWXYY9vpXFFZc3lV0AGVkWjKXK7MUVJQ4GRUfjm3VL2d3QMDsbMfwZejmBWkx5p7scFpdF2Vg/BaxdllkR118PtAh6WYg53RwWMFyNlpehei+v101FbEqvcnwQwqOFwe2lewssDtkqsPcZFvDf12lIgi3TH+B1l7e0fsFFLy7Ez6C7I+PkTyw0EgFNoI3969e/CwF5oRwU5xPSZs9/SImZSsULxYOVsywxzvosSLxkYuikuo1SAqplUpnj8NyngParSbH+7K6vkIHad0pc//MzvTvrUf7dWSFOCKF/VbDDdAkMki1/cMhWQdinKBp3EP9Ip802o69wHVZz1qwQYIhbarh3BQNbaxv6Gyo69Szcwn5y0RHHmn/uh1eBwGP2ycj8iKQI9gN2xXaH8ejmmbnk9+7QvtlbxYKZf1susWoC61w9ZZXPGjVWlvFy5Nrj2kCn5a93tufzJrHayjIy1pgYrDtl9bwLdyDMzRmnO8WnvHacRzrAgTVieKZaQsF2Az/LVFOytBEDUoqeGWplhrZVEDZk/DPB7dBE1Dw9rYxcqXAJvgFCXbW2Ug2bV65FxGCnx+GyBTKvfP6N+37XX+P5m5ebOgJfHJWiJym3L7Z8wz7ovrBYJ+JP86TxH2yKMcoi
    properties :
     x : 101
     y : 110
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "QAVS0000"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Arret_mes
    identifier : 0x2a3eeb4901b00566
    internal_properties : CAAAAAgAAAAihaDx8/YU0ushLourZQtB2yBlMmQWCJI3OwM7X6nWXKuPL07EU+ridXTbiOOd+VLPL5qHiiUx26nFAMyGlihuPjo69Pqc7L3do+iasYpy33eZczAwLAAvZLcLRFzkN1uPwKGJ/IPcjOVT1VcnH24nW2v41+xGC//UYEifqB13oq78Zya7Xg5JK0t1NCYK+jXAfJYVAHm0xGeEQfDeRLlZ6w0mGg9MhukgMHBMfxWCy/DBpBBFDh9zeTrn1hBz4w24kmdbR8h5dH0Exat8yrSF4dynOgTCDR+am6AaPZuSBvLKam9C60XNmPSjVEozxFI2GELIuVeLW1zZjlRP5Sqp/BIoq90IexVlo5p48kn6m3ESp5fQZK6uCP/nyOQR8rasxQtZvf2f++dQwGkJUe3+DWIc6EcNYCPbFCFNsFEpsVf8QKiLRIDBJOAZm6j/PpcgVR4fg9AqZEConXyhNPmwUdo4O3NDL/Q4t6ccQ73TBdwRkBFZHrwvMfgqRyL5nGM69jwEkUWh8XqftJm6lIUJfC0rwL3vZvGsKgxLYgaV8dh+qpcGJG9eEkCFT6dSMyu4Pnvul9RsThdxUV01UBFzQPxLv6n2Vj/LtLHGjWu92bBa3fZ5C9fhYdwoyoroi7nAgnqDuD1136pnMClxFh+ef2ivslld9tPkqj8+emH6tY5FR6bVV6AtV07VOvsqGPpVt1aLr8VfsW46hn590p83Jg==
    properties :
     x : 101
     y : 139
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "ARRT0000"
         EnvoieSocketAuxClients()
         
         server.ArreteAcquisition()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Energie
    identifier : 0x2a3eeb9301b326a5
    internal_properties : CAAAAAgAAAC8tGBsuH325VHG0TjCjguOU8g/rYGK21qaClFgpeUrMAivmWYV1UUuA5WGZqm9l5d9d7nP6wEsFNddipuRBivIx2FglSaRfoxAr9arM66x11Twt9SN9THWFS05OgeYFZbD4kNd17NCyxCsr+tH9l4quRgEZoIRL+risX1QWnRTzgt8VTXaEhEq1HKvJ8gPNq5R10kKdm5aYi1KBYN1zIzTkEpvqyAHtIqIsBM6nnP7/vefip6+rZoij+fi8V4oIFTbfHXc/8HsFk8ZGsO4sRRGf4r/rNM4ggieONWTWcQdtQulrJr1AHUXz2tD2+082hgxafBoLszWaNHFWTRmz6nDOhTBInL7pnH2aOnWjBejfQa2yP6sDIFWpPh6T4p2dLZrEfxWGa2OnWsfwdYV2BNiYF6Le4V7UV/1hVRgJMiP4YRSg8XZQvuBVp/KSpbm6rm1L2Vsj7itgurJ+Nm/A22isrYvzhnhfhBUDEZ5DVZVBdGvqRzr8C3bXZ+HXJR0wsdNhkEfJc3EudoGlUqSp9cJ0YeABceh0xv8q8c89mfHGWHObUCaNEr+uI/SLsmtQ7klvH7P46TLcjEkCrbxgV+/GwCy7OWWveN1mSDdA2CxmyXQbnePYU4bGQ9N4X5hyz2TZyOS3uwNg5GUfAtOX5nMslzqVRfZtrDywiSgJgNrgMFKfWF7UsS0mrr26s3fm8vHcU3VRwguSFRPYHs=
    properties :
     x : 12
     y : 170
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1 = "VPRO0024"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : gain
    identifier : 0x2a3eebe201ec5997
    internal_properties : CAAAAAgAAAB/GCZg2F0GdgTYDMHXcF3tRzC+NWgqYu/X3w2pjDrxisIJnyKS9hvsbbP3Gqwm1/6hsVFsf/qkxYhx6pt0RfQymG7fxA5eNGgwhhFidB2/Rww6ZTcTu5l7d21QWJv7v6Lj4qllBjgp23WAdmd9gfLJPwxxZvVtX7clpHyu1dwTFxrCNJihiv9VVNsLZyWtlkRVfigsLpfdaQPNSzNYMqts7pu1opgnk90S/s0XixTjBcl8ahHx81b4GWwLFz+wfToeoi0t2CVZf81n+4Ht3ZKaJcdaJnPjgNEnKt1fcfdP7dr9AjboDHQwkMiYluE2eJh7tlGrHKiH4GQrva1+3HRtvk1mD3o04+OJpZdDyemIqubhWZh0qIPEDbidMbI6v4ETjIEOhTUG8cxzXBBHsm55GrF83SmTa41YlTVyDhI6uED9dAE30PvF5zGH1BLOjURbYrBgVZaZEsXHzY8B7Rqz9j2cLBG97HJRcjObd0i6evsYnL5fsPVPYJ/6MKam7TKrWdJRyT3Ox31iIpoIBZYYqxyb8T/9mYU1lwhQjks6eKVHVsBsXmQcLTXdlk/bfMoDp1stg5C9EDzuKQZj4xAs+xFjBOP8nX9yyxPHbaLgmmHDX6GFR7B5znkuUZCTMjiTO0Ie1t907eU4VHwDzRqnln9f1e2hI9clGVMnkKT95lXk48XdDxJA4+zHOdw0E7130XXbRoOzup11
    properties :
     x : 101
     y : 170
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "COMM_001"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Dem_mesure
    identifier : 0x2a3eec3f025cc76f
    internal_properties : CAAAAAgAAADpwtXE1toa2v3Ao/Msz4XOghppHp00uDO8Mn4TyTHecgkR+yTi74yW+yBzpBlpUQhZOYq10HHzj/dpwIxAjowo9BqaGrxQsjHWmYAic2pyxT1TI0bSRGaLct+LpHaU4+15JpG/2m9G/HPD0aOFG1oBRUnUQW6O+YMmUDQfaj2XYOga/0yx+rAxRfO9YCQmHs+O1lKnHiXWZCHcufzUqMXzNWMmdEH8DHXGCDz86UVOJUYrPJYnDs0Tu5LD9npDq0OuQAfxbcxDQMuIwRfKHmxLr6aXZM5KW/MsW8Duo//y9ODYEoWUk9tR9sRvZDwDgJSowh6qzzPlI15tPoSR9TrXDjiEAYcAyeV3SzbQlDkSsd7F4vp7gdVRlR5Gz/Pg3ZOFHGKAROZ2ZhBDcyQsBBCn9rHjj+5WI2owo7DsN1hAjpQXa/vmwW/QxRN0toMeATw5jMXmchGR08nDeIeks8C9nm1bBm64vo/tvn7DOrTCZrF0Y9yE+4NyEhs1cnUAp2QHea+vJKrImNNit5yFJ6jSmWDGs8hk3ehVrfc2JUXERkHFUW49ndJla8sowjLRWhSdGW7hYtE/JTwIoCwk+9R8Zf/GwmxLmQqcZyx5WL44XikxND0g/Jb0eHH9Fz9dtLINtbU2MzpwvkPiu9TUNV5fQI9WCbheORo1PYLxNdhNbov4coHwHEC3vwZctuGUrv4YU+ITfJ73zn0/m2ti/er8bQ==
    properties :
     x : 12
     y : 139
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         server.DemarreAcquisition("data1.bin"+tab+"data2.bin"+RC+"300;300;main.graphe1"+tab+"300;300;main.graphe2"+RC+"T0=12;offset=100;suppressionMoyenne=1;lissage=1")
         
         Saisie1 = "DEMM0000"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Clac
    identifier : 0x2a3eec8e02cdfcb3
    internal_properties : CAAAAAgAAAC4uGRw1DCERtgMvSFeJe5gd6yKVCc+9H6DY6XbLzG7Nl8MLcbgKzw9k4M96b27NyjEhuw2YS3NW4mwdoZOR+h64aPmZbfaOVtw0eY++MckoCeWq+hmHbB8mZc9wJn7qMYRuScgXAVrMpMt22YNi8L6tJ3ZwAiEuXJnDjnQVcnKOtvPtHsqU3c6fdJRBi/doQO/zCd1TJgoUapJcgxkkSIhCE33VHyX6YyxGyf1QXq3Y7uJcRo1BEc2BV+/8tbqzgClkyJfnet8w++d2tWFxIV9GTjU/uDgyJSXcMXXij9bE4g4SkQ/sJqXbtkpSqS3E+OXowb7Wvavl0dAWgeaUL2UCXQMsnHxt5AhD3zN+qo9eZ77FABcGGoBSL+bN+PryTBjSbAGBCPmoRqEL97bQihsPF4XVruv3VTBQ14/Gsd0qMTyrYiisFHRZ7zeprCq1HdxP0Foao0uBCio4cEOfZSEfr2/GsAEpzrIw4m4vppMavadWWDCXMKHcXOYg/i5Nbz+Q6JXxGx/4jg8D4BOHEchqfhbAdtNVYER9l4WcSCkvrNrzneAWOOS7r9wJ3INj9msJ/Xqc49gvReMYSOBm9NuCl2KkUXSXOgwI+cKcXJQddgX992jdXR5phkay2m0CmYipwPJ81cvwto/8+rBL46U7rQ7Y+fxdzmaSYfZNGwTBfSG/s1/O+Ul0ys4ayHr+UpIwRRKEACaDGhTZH8=
    properties :
     x : 11
     y : 200
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "CLAC0010"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : VitesseUP
    identifier : 0x2a3eece903415ef3
    internal_properties : CAAAAAgAAABiRWAxiygJHCOjzomjSwXbsRb/ztKE2XYTf8m9GcnJwOAqqnJW6qWKl5ytxPu+pNb3UutTViFIqMrDdw22k+mO6OiCFnfXGXbK00V5eHi08nSgnIdJtHnoui8piQNZnDFMcAQsMQyY/74VkHUnAzQWJZlaybBWELfgnxE9CwP1raXkr+LXNTnK7cYjfpQFzarvrnJX71qpnbMkP6CJYzXnvCaB25JugO0GEcvvNLr0+P3oYKYkEn1qnufQFDXb1shE7QV+cgTu3DLkSIpXxGqTqlxFGtQnRnySTn913YIkiIToUbrJI+MnaIAIKvyHGsZvy27NbtAj2M5DVFPos2/kdxyzt873M/LqVGLEPWqJaOPzH2kWejUHJBgqMgnAyWY0/3Mlysk9TRqF8kc3aVJgMxxan9c3wv7NGG3YpECCNDF2zNH8onIvf6uz1xRZ+FkS6/g1YhG9fm6XYqSsSogu/SuQO9IGTAJeKhYHUPvaRqX7Ij9S8Ot9VURTEtTSV5DTnS8ybIA66vplYjWugS+XAGhuCDUvCnOpnU/b61uKEhbFBbq+7tTGhDBrBtQg4zs/fcb/5akp8zeVuLSZ6HkWh6Oj/kHamfL0aOZ/t49shw3HuvTzoU0PQIAD6AWVdrwhdcaMz4eulDIckl8HdCjkof56vClraE+wBFww6qmz600Om6K/j3471UCS296VZzHy6I/ghJjimMstsOa9kl935g==
    properties :
     x : 12
     y : 230
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1 = "PWMU0010"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : VitDOWN
    identifier : 0x2a3eecf70344939f
    internal_properties : CAAAAAgAAAAMc2icEcm8M9qDY7nvtNWAvLx8EjK/nH+cuM67kLZMaVadjGOglfzUtTMX2YzZ2IG2UZbQwBQ6eKWPPC92POwRFpQwN0oNlhtRJyVywWVpLKyLHTDkJD+riRftZGw+VmnQxjQR56HoB/ydJSzT64QYh9X/fxkuMksuncKp4vkiTCssGavy2CPhE/4m4gcJuDEtKXD00KKxjHvIoA4lIBVTj/8yybh4KC1QSuMUbZnMPkGfWxay6qLQpF4VQXwO4AVz78qiDPVFb53gS+InDc8SQNA/UOJ0FgB8NgDJJw2VzpOsVz4RzBcZ18rk0FfYy63MdweNWGMTpUT8AprACPOjjgLXE8BFUnPZBdse4C3maiD7l5hs53lSn+rb/LUMJudLwz+i+KxVelvZr3eFF++h7iLwTF2yMsfsikiyjOi9416WPAoufWLEbkd+9bRz93jfSxheTGofKyOXSYIIJmU/JmtAorK7WkN5eE25iK93wy7TCtSQ09GoIvi+pt5y5MGwD3lZEm1UNcX9kTdm1wTIzeqooeXIkJ7YmSSrFQllEXlfjwUycywRdfJ0vCASRKi8oSLabx63Do5/y4I1JpTJBrOzK6fdn2EZCCf7eQnTjmIHuoGx6NEYCRCyCoG/D412lUZc7u8dFEpbMIC+H4KcNUEV3toZ4fdQ45zF1Z4l733hBgQSBWn53ystcww5WZaiRl2i7pI9FMMRQAiHMA==
    properties :
     x : 101
     y : 230
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1 = "PWMD0010"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Mode
    identifier : 0x2a3eed6503824372
    internal_properties : CAAAAAgAAAAQ7QfTUT8/JyNNwQeG21ew+i0b3r9hoet4FTdPkQnRaFmgwOVbo+MuSHpHKJNRGPgbuQ6QYW1HeztOO3oFyzTSgDb0pV/b4rHBZSLv9rbyPtJVfrh016W1rKLrwLoXQOQN0XUJ3F0dx1iejoUsRqKFIyGfz8oCt8yHRKvzrrivMsOEb0VbNXBlV0O4lx3MDntxYBSQUC725cG8pJLuXNYKLn8sjrTxFTh6/1R/025C4k52hY+ry11c0pci2RESBUuizQTQN3Jhzfkj/U+i3nNygUmTETMtsGGTfVWKBzN3nJzZsV954QJvRph59LlCGdq3uv+rK4lx2Ci8t9D1Egt96+B68/0BGCeBTb1FF/dxZ+u59P2Jbz8geEZJQuhK21iWAbaZXa7ZFtKjCckyh4BqkeoJMcSiNFFhjgbA/bhmKNmQ160HDySGjbd8A+mN+El+Hz+BP8zNEZ6PpXrVGgSGJfPBnz2j0gtoVB8T1V/iRn85Xd+dXTfW8WqQqC2ArbJLdeomV11+f6JpFW9lETt3WuxruNqChMqdm9K5DifyLs5dtqwjIaQZGMhsoC3eaamZK3PjiO411AeVCbg6ds/0d5X49JhTOWtSkisjsCcoF0H9z17SYlngMIjWUtjJHjBbAV2HLQFRALN1iNzZKxetGhEi50tpfVtNsR53rAj+4LoxKU9XB/T+zRwXemE3yT/ZnVPdu2aSm7Ja
    properties :
     x : 101
     y : 200
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1 = "MODE0000"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : DÃ©co
    identifier : 0x2a3eee2c04384b43
    internal_properties : CAAAAAgAAAC329UcnllNHecWUCaxR0qJVtwOC+/xNyKsw6BimYChu7NvcaXZQvPxPU0nfXCAtxWPgmjYak9IBXSNHNaHH2IXk9YxZs2NK6GiQtkFWnkjf/+5zFX3qyWQz71nhjjDWGEpZMGzJDcObOr8KFdegn9J2Is/C/ySwOr8cBao9jC1637thyDfGd/JWmepChfzbLj8NLpZ1K4d9Y3hQFa9AXcojIMEjFK4ebfHLroNgxHlmGSKmPkX1u5/44xcfoToDJlU3h19jnoSq/HUAtwuCv2AqZRpsyWnqW9tOPqketeVZAjq0wdwNbaPp4+sjw7Cmj4D61vStTDBldaLg0e4ui2f6YJrsrJMDX/O8jpCjdy7IpXUjkLedaDEclgSzx1WN8Uy1kCwgOb6lsDRPvpnLYoDwTl1NJ1yZbHXwD1ndKLMn2lV1YjkgfFONzvzulSrisJ+BtmAhnDQi4bPSmskXviBcUoxaFTzCd/mWmaiZlte17mKH3bSs1rgpTgvB2LIVfHhFLD5WsEz9apHDFZKqXNqvFXp6eMSLaoLuev6VbFIS5IsWj+0xfcVrkC7t9ZUEzi5up16A+rCegdTXt09WE1Vb7DyQ3HVIB+0sDaC52UqImPEUTVBeNSEZmgzx5ffoF8hI1JsRXb1WX76KKaTMs454UGHCWGIxVbSKkppHmnvgE3lvM2zZgFgnXGPJa20M60L2zIlfZZ2MP35usoq
    properties :
     x : 12
     y : 322
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1="DECO0000"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : GOTO0400
    identifier : 0x2a3eefba047a5e9f
    internal_properties : CAAAAAgAAAAZ0kVUvorxrhfNaK+7KeO72d7XAhEb2bZknoSrxYcTsDRK9CrqwI24cwIDekcs/qZvNjUfPhvE6qYBXeste1Pk/u4Y9DXVVzAPNp85NAjQEhBIzKfF5L0sfvv9sechyCE48Nwk1WTE02Jp9FU7U4jWgaGK+WReDLMUy91tl1NJFSnM/xLzbcXGgWLXhjjluVLjBnL3o7LFMa9AuwjlMyn/sDbBM06+dNEyJedvQPqoCCGw2CZgspmOmuOsNIlTOiBwLV2+pizC+BZ4RKLzZA6rLiQt+rAfSiAWArvFmYKosGhA8ZL9Q/+DFFTs4ngvLuZTA+bd8rg/FDJ3sIP0C/PkC1wLn1KPJ37m2L7EMfoF4LWBLUd0FIex9lbgmLe6r1wOnZErcNN/94R7uOX1I4BWxeIQVS35HFg/tov2ulrgmpucardaTFSp+QFZbZJbvteswTZz6EsvaDjNEKKy1Pac5zkmmXg4plz4cJSF9qW4VH854P2oOk1Xd+IxyIIEDQ5FC51oeqJEFDTbEJsks901uq6UZtNpLA0Lq10hAS240IDrswhU8H5wdi5pDCLqSeE5UywVKzObRbnzHtL/iodkQRk5VL/ka1wWdvT1/cnSrT+VCDqx43u7Pm5xmpdjwAr/SywkWlxZoy//te6Gf/fxyoHlwyhkV0JZc8OLDYhiBBJVUn3ybKV+zNKjRP1w2LBdeZjdQRujVeJuCUPSbXoMHQ==
    properties :
     x : 12
     y : 259
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1="GOTO0400"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : start_mot
    identifier : 0x2a3eefc8047d955f
    internal_properties : CAAAAAgAAADsx+rLHaqLkqU5QBMd9TsFNbidLOoquKbhU8+ksK4qoceLM+k9gUp9Dj/AX65zUWuq98D67ZDR00FO/oT97bpy1/VvTRruAuXs/9vF/oQ0cPIeuF0XUAu8LP/BkT0BNKe6DrTCU8xW94iBAM0BM+g4G/d+Q9YSQg+2kwEtHcM5/48y+ziRjV9+V7KTUurpQayhtCp9uRpnlf3EV7AT2zmB3hgVRfBagkUAbTsPLvoQegtWfJQKrt8+IPfcxI+L2kIWT7k02OBIpGB4EHKpZPYdMNbN5JbvvBD4diPZsz7cXhruFQDXp2X3qgwkdiojOvwBbcJrDFydCBz/jBsimwOWwbIT7Yw7fd7kPGrww26BFkgestT5P5zO3SPlfUyPYIP1/vJMUaI0qFck2T7eTHF90Lf9egr0dRHWO8Ajj/etob6Z7zTfF/0uXMJaTqMsTzJfvktQRfaAnWdqdT8XceOPVko38lGnVxGv/XmaIwrNuzp+vYIVZZ6s8sMq0eshhr1AIJpdTzU95c9MgdQ9CGYMoU/Fr9xQTdKWhGaigqo1T2fcrBFVZ1tRcZ+CJds3MoywFG1WlMpojvB401cUa5RvSCKi7RB76NnN05G6mi7XbBLCvff2pjj2eXVwf5zul7duP5i9mP4nD7mTGdCw20OB3LGlgVYGDfIXu4PJQ0AaTCxtekMYdoECIm/0ybrpQw08ssE+OiJoHk2vMmQzDOX5aA==
    properties :
     x : 12
     y : 290
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1= "STRT0001"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Arret_mot
    identifier : 0x2a3eefda0480da76
    internal_properties : CAAAAAgAAAApCZONXucnhlt/abEJ3oaqldzIEcu31RkBVkuya/Iz2SfCfx4lhNr4OkXMV1eMyBZLKSLBG4hwllTEJzi62f8CkM3wPhoPsMfno4XOmUHUlVMmLtqkEjQLSc6r4nXNiA1QSQ7qOp5JX59/BnqtAAHpZBJlfPsudvlrb+Vo0aQXlF2FqPXOkEnTnk8vJL226lSHhHzk5j8FAkkCZb8/SmEDfFWh9pNKqjzcedifcSjR1b0OOSAnVUrbkyNkk1Q5pxU2iau0oVkTM8QN2bbncr+XlTWTnGsvGLcD3yhizvYOdwXwbdzGryHcP2lptjZOQH9USaHQI5SXF9VCeHuc/loJD9xv1WXpgUWm4SPcgJH2jCHzoJWBM75HJMDzfIyC/isq76ZFC+wQJTRhaQ2yW1tZ+aJN6f6sP/C7Mr5k6dibwbGY53ntbYFTHw3Eb1878ZLA0S1PuXYG0vIjzbb/zAeB84lJsw2vaabynNW57RudQbWtZznxbSTDr+y+LssWiKm736CEyd8ZkhIFc6Wd7boeiqihFt5CAZ/7+7B3CHmFXfYdWiTnMccIBt7q1rcI0BDb1aFRMMoGJQGTVVhQfCqnH3HYeDDvElAwcqHRIvEdCIsD5azgKLDfcDYOtmSZ7XuTMnKgUPK1EpKkfYMsFGNfTfQ/ttrGg1vqwiXcmTccZJM+IGyAOtn3mdZcl3OVohMpsRlpYsxXv4/970myHpLwLjY=
    properties :
     x : 101
     y : 292
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         saisie1="STOP0000"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : GOTO401
    identifier : 0x2a47e31205103ab5
    internal_properties : CAAAAAgAAADW3nXvGeRe/6hc+ZqWbcBmi7SCdC1njoCbwR4SjDySuMvSZPOC8CPcjS/xYidoPJqVI2s8gMYXamOYeCj/wgkBSTIi8f7dqx3WJ+S8sgh5BTEan6stv8FiD3kfIslCKWFGwFc09Al0saA1k+7jvssu0KwSCu//DIAboVfn+FbfHBWvs4+LDEk571wUnl0Rhw+tbgLjMXHBq4gWLohhkVh/frgzeuNF3z5YsA+3MTudi+OM8Ib/zrQulOPGPod+yj+3fUexTzVmiAsOw5rKDhNBtTZL2HtVJi9z9WMyGMPwzjW5PDm/AzLiiUgBk9drfvbrPU5xnzUYvzzqgO0BedwIHymfraJXDNxNR9zegxqfAYsfOQ3/8S6Fl3rmOXoOoFThPRjQHSANhbGXHp3m15bYeAdodZ/ffoc3qQxdzS8uOg8tbYXdTh0zoiVEgO5O1mTtGAmOyawlARkQL/ln0t8JTtdOj5qogwW9nvWYEoiqTKaatukgoN/M4N193pjp05UVy95xlT+ghZCy4QawPLyI/ji6uxUzjsc40R1xQiqiGqclFaqdKmciZ29bjvcY7fzopYG5qNIbU3mu0+tpU8PeMfdzTzxVqqYf8xVjXVMfN89kSoRi1yNRlh0miCq5nW+ftQ5dGYEkOpsR3sYhfLmblnHfhL71ioR24Do2o54QeQg6MtdpYQguykyHGDjU7Ef2D7JIWStBSjFpCpE=
    properties :
     x : 101
     y : 260
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         Saisie1="GOTO0000"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : tout1
    identifier : 0x2a49850607b8dc04
    internal_properties : CAAAAAgAAACrTUuDI+PHuQ05VjxPtl2XDhe5GwR9K/3YpKvN3YdPCl8U9RBkr1Lr+9uDocOpW8w8RuzYGeFjabWAv0YWfzCSSTseLa8yITMoae85KzI0yOuSi4yOYRCgLSOhxF1/GKYV/Sek3IFz9s8RV9rZvxo66Bnh7LhocVarGiUcQfUSwjdzNA/iR0eeicaVAuuZUfs7wGfxTJyAlWZ9/kAQVYpB7Hl3mLRLOdCd7zNxzfavYz/NUWalCH8yKQvLZlputiixv0pr1a8cx2MZXllJmNVVfaSkirDMyJjrjEnjtkuzY1z0cqCX1KJDQsXd5ogjo/N793YnmrLXezuMTtM2TM2UDXAMpgEVD7zt06jhNpaPz0Q93jLi0iyzpkUVuWllU35hq8pYCuWIw2AqabgZhHLCAgCRyCkxNw4PCejh4LEOxgIM7xIoKqOjNcrQkAZEpr0H8Wta2F9g1uYOG+NUN2aaxMfJDKbeeQwGQasKlATmiCQfMwIAHoTJt+G6Va7zZ2osXWRx0tZhxNZa4VpMntFv92p9a4Hn7zvf7GhQjzL+TBEVeDn2ao3sNL0iGYiXAZeacScEadEi/92600XnNb1cuG+Ug2vsxgIWsTmsPxQ0kQT/b7UbPTxB/mESk3E8gh4Cdmx+Tjz8Iftw5IuMxH2Fcc+44LrujCwL6siSr3Em2sv9ZVYgGkYiTIQX/LJ6aDtb3D+fzUPn2ztmd2w=
    properties :
     x : 101
     y : 354
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         
         ExÃ©cuteTraitement(Energie,trtClic)
         ExÃ©cuteTraitement(gain,trtClic)
         ExÃ©cuteTraitement(Clac,trtClic)
         ExÃ©cuteTraitement(VitDOWN,trtClic)
         ExÃ©cuteTraitement(VitesseUP,trtClic)
         ExÃ©cuteTraitement(GOTO0400,trtClic)
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Trace1
    identifier : 0x2a499c950901e2db
    internal_properties : CAAAAAgAAABCk/o8OCoMIrlC8M2Tykgv4TZyndmf11gcGJhgc5d9bPl14O7H6axwj97/JcuDzqXxbMAf3yyCZ2Pku+jADmPBFx1zyn/etoX5ZAThVOYysNmi9v45Nu/MrHm8BqWnxZ7pzzNr6EDl7drEHGEwkGn/SGoXusvumaY8D7IqDfZMS2NeVhZfF+7Frw0YjVyUjzVi6Weasuyri9XkeiidW0dULUpIohN39/T6eNZQFvlSJRGyZLLaiIKYaA58QnKRFlpxrNJmrrlhIFsAr85n+OrKS+cosdF0tFf2U5v0xjkpbQ9+RQTfztrT3NTD/L+e3htGCtc/Oss7ShPfz1L/3dX/7tVuM8S+NJGZhKTCC97iZTxrCmlLfRmq+OgPukYQytJ0nGZOJv9SGIAFMUr0Rz7GNB7036yhMt6k46GHhBQFmcP3Ypl+ybqoaY6ubNKII/yvhW2mSpt3f2xlYR/kJxW+PDbIUajKQ3rbRPoGkxaa9Cap78vcfuFZJiCCh7GmOPq2aMoTIapS55ceBoc520HP7veO5p93k9MTMJYfPlOcw9rg0Lsv/SiQEd/xfBDkHjf69nQLQY9ZGzTUuhHsjV1QzaDBunsoPCYzKC4BnGhFKrq4TTQhJJj78CIv5nixwYOXnUJB5lKSPbxRlj0Fn6Mtq85fBenOBnFgiVVZYq3EC52ImnQvjHpdjGuj
    properties :
     x : 134
     y : 12
     width : 50
     height : 56
    options :
     -
       internal_properties : CAAAAAgAAAA+DF6M2adakX+WVoC5P0m2w1tSRFWeHM3Hn2oM3icw8Y3mhrRSyCAs/cnosMK1RWDLdoPIB6GcM3GsHu4ZVzm4smky0dRkO4OgQCzmk2l3EXObSUZLmM1dqzL26whcJXDdRE803SVyUAo=
     -
       internal_properties : CAAAAAgAAADaj9r1jUSY6sU43rzrCMBpkYUxzxQCwfUwLbUNI2/k7E/SZt3e5K90q9ZKqw8vM9RnPqb/121pHGusxUoakjJGnK2/c/tSxCy+Nu2Q1jLKOWX3eP0312cZ9IKHHNkfL88JwiLChL27EfAo
    type : 6
    controls : []
    code_elements :
     type_code : 6
     p_codes :
      -
        code : |1-
         
         LoggerErreur.affTrace = Trace1=1 ? vrai sinon faux
         Logger.affTrace = Trace1=1 ? vrai sinon faux
        type : 17
  -
    name : Affichage
    identifier : 0x2a4a83440999fcec
    internal_properties : CAAAAAgAAABH0krUxT13aQGrR5nJPELgpRm8h8Wj5DDRODPsTOIqNVF34c+9mITvfUop21unsyhV3lpawTSPAVrWh9hOLQx8lCGh2CW4gePIMl9XlNdR3QdFfB26g7Pw1x/piui3ls8O7x2oJNdl4gl1iy5iDtui18zTiGCqHKnLzbnsNv03bdVKvcUuYcuZT95X06kTaR/O0l0XM0qpzeGvHWECJpRzEe6fhLKQ3WlgT6hmmywQKDvul8X2gbwgOawkRbZ4zL3SSXcXYd1QLVCwT1O1A+Wf4vPafOTbf6ByGyV/wrVA/WGB1Z2MTxN07tw7CFT6W3at0Jp3aD+ZM0mRXT13c6kw2l+vYyPd9K9SY863HhhxZ1Cs3GdHCNJ2k1LcHXl4GczKcmgEr3JFcTMD2fzFVsSqTrmN2J/Yi+UW9FC68Ei2PjGUq4n+oDj6BpOmNRF8rUG/jUWUWV/hVLBwUHv2/4SJ/zVdld8dVWZsjU4hcXRPgALADuCafY8CGOjzcjrnMK6vaWYtj2dlPtr5WWrRV0ihJ4b4rEme2o354GR/I//a41Iy3EgbHYPSARSMRU9FvhCxzKJzUGl65G9YnN0yozj63pXpvBo471/5SorIE+yHn6n2UUel3O++0zdGCqsT2EnVU9iXxSx31AUuksCiBD5xYR2txJq7lW++RxC05E8Ql4k5gQoS462JpgCr4RpZc43J8ULeN1ykMlJn9n6JUBtL1Ll9Yd5PaZql57+GnzrPdiPPa+i8T8ETjUjE
    properties :
     x : 190
     y : 12
     width : 56
     height : 56
    options :
     -
       internal_properties : CAAAAAgAAADeLH7sOcf6MZ82tiDZnynWI7sy5HX+fK0XougqNAW22wOYhLrIdgqyQ29e/rMLL4ZPgL+k0J5PVBQvSW3LlUsuCCdwgzZ6Rb0+7kqwpTfR0VP7KWYr+C09S5JWS6i8RRB95O+UfUWScGo=
     -
       internal_properties : CAAAAAgAAABm+yZR2eB0TqEUiggXBIzdtXmFMyimpcG0hOuz4a3qAvHI8BuU+k12qeygEY1SdQb/Oj7z1y6u4yZ7fvn06FQUqrO1zY2QBp6cJBuyABi8fyHLRKGTszttGGb74OXj4wPdHoZu6BEX7ZyE
    type : 6
    controls : []
    code_elements :
     type_code : 6
     p_codes :
      -
        code : |1-
         
        type : 17
  -
    name : raz_treuil
    identifier : 0x2a4b8f7c01708452
    internal_properties : CAAAAAgAAACwlPR2AkhTWLZwc2fjQWHeAM4ebWnhRkoKzQ+p9YZbNCfK1GhisC8uOz0u9/GfUw/cToopODr6vzeJM3/kPMlkMqIQ8Xunq2PIm85s+rK2nCPcK2MivpE3v05V1e8OsdDHIo7HpwgcUcL3dWs5u7uPUEblUl3C59LgyfYtlzf1/etaUKWIttGenJHPBbzYsj6YxT37aMu5vY81Dpq9ZxPidSwxUQEgq+2q2I93ND74K/e96EqKnd55Yy1vApjzuoma8LaFmqs9WxpKm1aadwp1dlent8qLsYL7c3LgcHbd5McsesXHl2DTk0+LMsFnNsh0/ZQsd7wO9AWgzHK5lwJlyf3QvjyHwja6pmc2jdcEKB6/QJ5PwrM4MB5XhUgZmmbtEwq4wYdZgnITs2w4kt8OMiBxgBTt2vPvuB591Ym3zKyuqcLR4BKgJ9vW6Gnu+fm/2RMiG6kXn2D/d3+7Ud+onmW/JlnM2E2KLlvC184Nysqtc6Lp5FGER5KU+d+lVjTao4Bph/42SxpfI4TZzpRNUTipdS6XqIK9YTS67iB7NmfDGpujIkLD9oBWnX+/zsEYT+Xm+OnLtskF+aeYcVqYCAeu53YyO5mQ0skA/Mhpq3qpe70QXbfaambQOWqyp6CgNBDeRVjLXi2hRqsfIBhDs71eQgMyo6WLgWBIlus9BEm3r+R9tRJSHYTD2u/mEH3U0W1NTU1qBLo64b6ydAC6PJer
    properties :
     x : 12
     y : 354
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1 = "FPTR0000"
         EnvoieSocketAuxClients()
         Saisie1 = "FPSY0000"
         EnvoieSocketAuxClients()  
         Saisie1 = "FPSY0000"
         EnvoieSocketAuxClients()
         Saisie1 = "RAZT0000"
         EnvoieSocketAuxClients()
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Type
    identifier : 0x2a4b9bdc014863d9
    internal_properties : CAAAAAgAAACZYN13un+BUVw2Y2IC9mxXPXDJGRvt5auOSwINuYcVpBN8y3z5n4UqZTaV+ovw1wvAMNH9YuCvizeK7ALkoC+JXhnpLKdF5qF7eIHqUOluwOt+++IRwo2uosrj8GQLvlfC4FbLrSdV3vWVdBxmSWPzJg+1JzcQs9U/3C4PsIQnLXC54KVbcHckFoKPX9GZ24beivhLoZ+SnZ2gGq1JhwKJAIhommzwkSA6AeZQ4F2tQdxaM+EekvaPcE3gmAyjeg1H610e+oX3PonLICJGmjO1HNxktZTQ1NMHj9VKtSDdfb08V6umaHBn0DXXDga0qEu7CgnK/U7q5u/U/Hmc2OCMc5G36P/iSuMLUx2fXgvwx/YsqFkI5WTJEi4YeBS5Zpe2Z8wemw0Pl7XJ+wK6siP1pSIJYtLBmkYc5IYPc9GDBWUEf5hjWpTZbpl0IL1Mx1h+2QMaj0+NOActK3ubg2mjsWeLykHm4qhr8J+6ax2ogqworEVYuVjQmfkXOEIOM5KagMFgcr8Z0ygiEpeIkXcNS/Ec+IWOtSoXFlc7e3deOAM5BCdOpioNZNJEfj12YzF0ASlvlmW+8ZrLqqE2o7GYoQY0wBcgPpztuyjf/hCSPlJ2kg80g2nd/zpRvWCmRyDjpZSAcYohgBkg8Y3RwEGIT7JudXo1zRNG7RjXf+el97VAkLfTAYzVNJzu+Bgpm3wj9OD8If7sDY+RuPvlp/319OgNeObHA8Qq7+XebnoV3I4ovkAX2vsiX5qW
    properties :
     x : 101
     y : 322
     width : 80
     height : 24
    type : 4
    code_elements :
     type_code : 4
     p_codes :
      -
        code : |1+
         Saisie1="TYPE0004"
         EnvoieSocketAuxClients()
         
        type : 18
      -
        type : 161
        enabled : false
  -
    name : DBG
    identifier : 0x2a4ba7b8045db257
    internal_properties : CAAAAAgAAABPEwmxKlvI9r4HomsI35I2Alx3XQ+QkhvXgyEQ7ywk5F0aJwpcpcr7CtlKE06nPkxBSCmMn9ZZvEhRFvxYTVhv8JV7t87vzaShFqDNJGRxGRS+Vu6HncQW7wRa8aoDJDukA6V+AYY0AawOH5NJM8RAcdQCIL2EUNDdZs1zMdr1kRshROHMYkgFco+oJ/sVcrRAoyeX+zWuqsTgJgBI+0+gD0wZPIlYXm94IzYgawvxroRc2UAkHTvKfg0eGL+3/OpKfdso9cZCgHUWrNzVZ5pKWtvg5skJHXGas2whJyWoEjR9AxXNXYHFnCnTVxTOg9xrFvfp3oJAq3c2e1DB9Pq2L9aSTmvowsxMLJIWg4gLnQvO/huxJMbVyW9O+e2hEowQ3vBul38iICwKmuvGd3H9h7l3eBQySJNvXyLm72Z4fssD1NkRsn6Z4z5gCuWLo4DZ1pahcQiJ5gKVQRAnr7iPv0aHZLz8yRv+OWIiTKBszGJRv+unuza0Qd4MS68+xo0O9No8zxqH/LZba96uW9hmuvtqAXZeeiBz4WSHFfgNzVWFfmm7oTf1Vw5pkNo4pr57eS/Mf5SzhjWHLrJrEWuAw1m/NoMQWYX8UVofpSvJ6mu4qGk8iHdQd5w/guYDYL9q/b6W21da7q7AXQ9py8fr41ruAYf1JoW3YhMqsQ+j+O6mrpZ/Oky8W8LcppBKAyYCB9eFCkF0ysW48gTtuNIqaaN1gCVvZ1l/o38UABJcjUl1u22dgzgpY3yLAA==
    properties :
     x : 184
     y : 78
     width : 62
     height : 87
    options :
     -
       internal_properties : CAAAAAgAAAD9TecWuzvu7NuREND5GOKEG1hO46YgswaCti2kctygB/qO5LMwfInSUExKdOP7SjmbswsFl3s/H5YCCtpiWLqz5zon7Jxp3JLXBNOAuTIDdAtb/wbcpox+eN9PgvJ7FEP2Tyi/HFgrze/ucALuyQ==
     -
       internal_properties : CAAAAAgAAADPXwkoCd0Uth3bYqYr8kSmfQLlTZvaZQwEMKc2yFZK+ZRYGpGCLvtgygYMptklNLQL4xvV+V/PBpM/SXvD+T43d0LPsNDReO5vbMuw9ZKPgAtbX/LYpmCKuM48GEgV8p1QIXrt4pJhg1GAgjDc+w==
     -
       internal_properties : CAAAAAgAAAB9HV44t0+X9mWN5JHL0seYJbVrKaEqADp4QS1+z/MZwkUDSNofK4CbfLkBjsmT3mpeh7wD+CUk3/VWZ/AixSGOHBqHI3pS4G8yUgKedejY3aZhxcvgNVTPO0HDhzzJCiCcpIRhCME4pctEZgJkmY9i9w==
     -
       internal_properties : CAAAAAgAAAAHt6BqoVmB7E/79rNtRK3y/5+F7400/Tx+6SXm5fsxzKMpIBRRf94X1vEhOhvvptzo2dwxRjVO72zzXEVPzuY1AyMIlAN5BSzhry8r+vEJnDWSBhyRtIUgqgC3hx7zYPr2fu73ZqMmk+UuUBhya70MAQ==
    type : 6
    controls : []
    code_elements :
     type_code : 6
     p_codes :
      -
        code : |1-
         
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
        type : 17
  -
    name : Table1
    identifier : 0x2a4be69a04e7f278
    internal_properties : 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
    properties :
     x : 187
     y : 171
     width : 563
     height : 231
    columns :
     -
       name : Socket
       identifier : 0x2a4be702058a8993
       internal_properties : CAAAAAgAAADI9IZVzW5lwoFkd5VDeo3ltfPIippqFC9NpkTKdx5zvtdN6TfUNQuoPxTnHlqXXWkBpGYEhmSrxl64T6z8i/KhZVE/JddBzUXD4GqLFL4YX/TTqdsCk51RFkX+ehlR2YVTyr+sVmzcI7MC9LZSpga0M+qnLvsLEhqiG4vNV4sHqav907lEjoGA
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAA2/NAN8e0i1nx9CL+UKDdKp0Wj8Hs3F8Uq+4nJi+Vlrhs8L5hr2bVP32EnBW6z+NbZQlN5MBPGig8ViSdfVhZsQGtgEMDD1bdMu7MeAi3Mz5RU1KkMSSSTgilVTXjn74MSjTOuyMJEpPVP6Gp5Ar2OzWyav4kS
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : IP
       identifier : 0x2a4be702058c8a2f
       internal_properties : CAAAAAgAAAD0N9LT5dKE7/OFrLqti1LOk3DZ9qk98q7Z1nD++BciWohpa45phhcN6MuibhzwMSyMgHV2xQF5GPRMYbxOMrixAwn1YtZmDYxNFMIiir6cXmkQx0wr5sumZj8RjDe7gP9ETcgGxV8541wwO5sji2JZlLZT3VeDbXKUb+tb/Ezv6ilW5YI=
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAAAmVURQE6UDnjsE5z87huUD9AYtud6iknI+0Mw7OlwoCTBzIeIBjVWXdEbISJt070O4ixE29lwlO9yKscgkvdLKo0hX9e0jKV2x+AjjG7FjMvPMpWamkyx7jWJ6YQ==
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Position
       identifier : 0x2a4be69a04eaf343
       internal_properties : CAAAAAgAAAB7X3SY2MjFPoU5pzRMJZEhYEfGXbySaoncaEHMaVz+jGw4uflnrPDHKFhpJ8DuqIka9RudQhsfKA2ogYkQbqY4W7uDNo0VKwvA/3cTzNHmZOr6Uww84ELULBHNOqmuh06JiIUAEAfbT6VubSUOP36P10nsYxRYx3F3csAOwDJqjEF/F5VZak0lq2/5
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAACXIzn0V4JgTnctC+2R9w8enOMIkV/FcjLUTpbj0jpeP7XIp5EdTlp+CQy2eoIOKHHiIq1y+nvEhnP9aUpT1AAD4yc7JFwgNL/mnNfQ7lIHn/DNorK7CzQKamTeCkgYnqJXUtqt
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : FreqClac
       identifier : 0x2a4be69a04ecf372
       internal_properties : CAAAAAgAAAAEOh8z6ztQSxqisiFZHPj4yX6tFOupGX5N4ThVyn/JR3EP3IwKh9PIsnCuDlf3z3+EJlgqL6oCIII4AQmgTmZou5sTpn2t0zMIR/+L1KmOHDKirpevuRm1hWJTXQpJLp+sNhD1LSLwYoiBcNr7Ih0oTsBb9IUvkLiOWSfnmUldgVQKqgLunXrQBgoE
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAADlBmfx/y1MA3ez/tUgsCgosggqA4sMFzcjdAMBzN/7//X4DSZ3mJ8LPoUyAIKAhUSS9wNJxG8gdAjT/TNB3w1g0yoptYR+f2fQdtYB9YIqknKpVa0LZTXhfpvG13ZZzl0=
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Gain
       identifier : 0x2a4be69a04eef3a1
       internal_properties : CAAAAAgAAABH7QRV4yBdj8jUhaA2SRHThFAlI69pe5blvovqmsCMkhT+heKGzfBjympjygfPMVuoEr0lb3gRcu91EEmD2jVl0INQWIWWD/KEjYZNmx7+EDXYJMz+lHPSwdSRJtSPLxHV2keF7KnfxwOUEm7RQ4x8E7nJ+AJyIH96lxqVg3i2pq15QI5oEw==
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAACSoOK+exUtIUXxy4bTADu65kfQt+Pp1nO10Uw0AVgjT53Q/FCjdavKlSLwdaIln0N4UnH8C8jnF3iwzSNs8/aKiXMN+JsP3+T0khlUPcoDIWsNMZjmqv0=
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Energie
       identifier : 0x2a4be69a04f0f3d0
       internal_properties : CAAAAAgAAADHKueNZhSPcuRNJqdZVGOGTuQwCR+GQIi3As3alJrkRbe2ecADL8N67JPaEuo8/ZgbA/PMtLayfCmoBkxZITF3zF5N2yBJ2wiuEuv90jrxXiGtGqtXL0cMazEvpT7YzxFv/WzCJpJ28z3YVyudosWhRIIRQTntvLd0UIOa4ALs3VSlVmy/q34F7w==
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAADp3pv4rUJAVWptZtnApHgiND2YQPUnykIbUdIBuNzhahQRE94QZ6R6XSfH/l7DLyJtX1H29N409QteJuVgLZ1ZlQIBGPy0Tu8qz1deiyR8w341+d3ZqOSGr6Co
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
     -
       name : Vitesse
       identifier : 0x2a4be69a04f2f3ff
       internal_properties : CAAAAAgAAADsiJop4Qtyf4SPLgj+51pq0NqlvxyQvoHBF6MiP/4x/luti0GYiYOejbKr1FQkhRhk3xQJRp0EVEIHt/3jxcjPNJpvwaOF4lbtcpSbmE4HKQBGJIvJWBVIoqfNtXLQDg70c1IpVTWzDkgXgy+rnOK2dfyxFIf/ypR0vSN1j2/58XH7751gqqWk
       type : 2
       content :
        internal_properties : CAAAAAgAAAAXdKh+OIQKV9abrh/yVEmKnrFHnsjp3TVWOJGoav1yPAlu9pOtY6p9XbWgsWRiX9GI+S1vBZZuPkHG+PLYQfeqofp6WBdHskVvv/MbtLCM9B1/ZFsRl/GpcRf5lePDxEmiUCgvnwzkWXFlBjuRPiVOUheEmMa6r9Da+T0gFBRr1el3idbNbi/wVSK6xEBk2uRM1jw/Ob7k+GTUOU+6fVTGY7HD2n0ZH3TxYpKxM2BeMS9rscldkfa0hqYPeVxndgzEMa5lmEF11oo7ob9i67PQy7sTjKYPfGQBY+yA5HYrG+XqeA0GI4yoLhs+8/+6QaKx3JGfEiRiJhT5ju4V84XS2KgpymVzfas3wbfddTFj0Hd3jvmll6PxJGtPU8sON7Mv7wgydIx3Hd6+yJuiFIKtoHXq8+PhS+eILWEECJn3R9iojt6IRth/6pl6ZXMroZWAOJLsfPhEdrkCDAeOpOKM94nkqQT9s19O0U4Zhy8cHhfbjIYH4zh5hfwHWFPiW2hFcp6pNK/awi8cdsju40xU5WqEbRCwhIMLmIK5YhReVjBtGu0=
        type : 2
       code_elements :
        internal_properties : CAAAAAgAAABxykFuGFGToL/GkpztlxQscsKzpOPfvMmYiwbER6o2FYwjeh8v3lujx91Wtd4k0ky/um86e70+24VihGA0iLGhHLKSrO43XuEgzdvj8iZXJstXVu7lY5y3s7p1L7ZP8gzgK1uE11Z0uKuT
        type_code : 3
        p_codes :
         -
           type : 276
           enabled : false
         -
           type : 277
           enabled : false
         -
           type : 278
           enabled : false
    type : 9
    controls : []
    code_elements :
     type_code : 9
     p_codes :
      -
        type : 35
        enabled : false
      -
        type : 22
        enabled : false
      -
        type : 24
        enabled : false
      -
        type : 23
        enabled : false
      -
        type : 25
        enabled : false
      -
        type : 230
        enabled : false
      -
        type : 241
        enabled : false
      -
        type : 263
        enabled : false
      -
        type : 262
        enabled : false
      -
        type : 222
        enabled : false
      -
        type : 223
        enabled : false
      -
        type : 224
        enabled : false
 menu :
  name : _Menu
  identifier : 0x2655689c1d358cbd
  internal_properties : CAAAAAgAAAALslDswD3CYsfGr25fRYFKsHvHy/rDpKo+gVr870LHWGysjfk3JZQ/EvfyJHSY5vfTzMwm178q+7ELfGRxvW9/IijS9i44qyWCuRFmekZrQbT1q9ofkePiYeXMUDXIsCDEk4bog5HYw1VQIFayeehk39yD0eKN7xnMinwbDnTBCWd4xbVG48rUGVDwacydQYxymm6QOaTaX6fv4rIkHgXN4N/2FI7x4dzW33RAm5xe3SWQ9uxQ37vUDBsxZDeiuiqCwgzeq+ZqWaWA5Hca6sMTqUwktuuOzolj
  options : []
 languages :
  - fr-FR
 popup_menus : []
 message_bar :
  internal_properties : CAAAAAgAAABoWSB/s5NvAgwSUMcloJvFSnBVxJ0NOzFl2ZcFjgav8dfRAQp1xqb0CxkQ2od5tmn+phQMbDacXUb67K0sLovRR/GdF7DHuCdFjzzIYV2X
 code_elements :
  internal_properties : CAAAAAgAAACKqIjoI4kFzhryPpdidS1cmmpxExR75XWtRhASxHROp+l6I3pyLfDXMoEAKIh2YeJQ67tzmsqBFyr7GpeuXm2SLen2fz+z+7q+0xXrGFRvS+Ojegr7JkKKcelotOMN3p1pGR80B6sO7JY=
  type_code : 1
  p_codes :
   -
     code : |1+
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
     code : |1+
      server.Init()
      
      TimerSys(()=>{
      	TableSupprimeTout(Clients_socket)
      	TableSupprimeTout(Table1)
      	t est tableau dynamique = server.RecupereClients()
      	POUR TOUT c DANS t
      		si c=null alors continue
      		TableAjouteLigne(Clients_socket,c.canalSocket,c.ip,c.nomThread,c.dernierMsgEnvoye,c.dernierMsgRecu[1 a 32])
      		cc est TreuilClient dynamique <- c
      		si cc=null alors continue
      		TableAjouteLigne(Table1,cc.canalSocket, cc.ip, cc.Param_Interne_Treuils.mTreuilProfondeur, cc.Param_Interne_Treuils.mTreuilFreq, cc.Param_Interne_Treuils.Gain,
      		cc.Param_Interne_Treuils.Energie, cc.Param_Interne_Treuils.Vitesse_cm_s)
      	FIN
      	TableSelectMoins(Clients_socket)
      },100cs)
      
      TimerSys(()=>{	
      SI Affichage=1 ALORS
      	cs est tableau de IClientSocket dynamique = server.RecupereClients()
      	POUR TOUT client dans cs
      		tr est TreuilClient dynamique <- client
      		SI tr<>Null _ET_ tr.type=ITreuilClient.eRecepteur ALORS
      			tr.dessine.Affichage()
      		fin
      	FIN
      FIN
      
      },10cs)
      
     type : 34
   -
     type : 180
     enabled : false
   -
     type : 230
     enabled : false
   -
     code : |1-
      
      server.Quit()
      
      LoggerErreur.Quit()
      Logger.Quit()
     type : 2
  procedures :
   -
     name : ConnecteSockets
     procedure_id : 1645366125310043095
     type_code : 14
     code : |1+
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
     type : 458752
   -
     name : EnvoieSocketAuClientSurSelection
     procedure_id : 1645366129605076865
     type_code : 14
     code : |1+
      procÃ©dure EnvoieSocketAuClientSurSelection(nomSock est chaine, msg est chaÃ®ne)
      
      t est tableau IClientSocket dynamique = server.RecupereClients()
      POUR TOUT cl in t
      	SI cl.canalSocket = nomSock ALORS
      		//msg est chaine = "ident"+Hasard(1,1000)
      		cl.Ecrit(msg)
      		SORTIR
      	FIN
      FIN
     type : 458752
   -
     name : EnvoieSocketAuServerSurSelection
     procedure_id : 1645366129605143088
     type_code : 14
     code : |1-
      procÃ©dure EnvoieSocketAuServerSurSelection(nomSock est chaine, msg est chaine, table_ est champ)
      
      matable est champ <- table_
      SocketEcrit(nomSock,msg)
      
      ind est entier = TableCherche(matable.Colonne1,nomSock)
      SI ind>0 ALORS
      	matable[ind].Colonne2 = msg
      FIN
     type : 458752
   -
     name : FermeSocket100
     procedure_id : 1645366133900176623
     type_code : 14
     code : |1+
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
     type : 458752
   -
     name : EnvoieSocketAuxClients
     procedure_id : 1893709586855898393
     type_code : 14
     code : |1+
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
     type : 458752
   -
     name : TimerAffichage
     procedure_id : 1894471329520054136
     type_code : 14
     code : |1-
      procÃ©dure TimerAffichage(tr est un TreuilClient dynamique)
      
      QUAND EXCEPTION
      	FinTimerSys() 
      	RETOUR
      FIN
      
      SI tr.dessine.arretTimerSysDessin ALORS
      	FinTimerSys()
      	SupprimeTout(tr.dessine.fileAffichage)
      	RETOUR
      FIN
      
      SI tr.dessine.fileAffichage..Vide=Faux ALORS
      	elem est IAcquisitionSignal.Str_DATA_Acquisition
      	POUR i=1 _Ã€_ tr.dessine.fileAffichage..Occurrence
      		DÃ©file(tr.dessine.fileAffichage,elem)		
      	FIN
      	tr.dessine.DessineDansImage(elem)
      	//:dessine.SauveImageJpeg(elem)
      FIN
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : fenvierge_0
resources :
 string_res :
  identifier : 0x16cd91170004f6f5
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAAC68/W0/adbG0nFCRhrE42B5HRnbDzVHaz86sSQyxlBk7I=
rad :
 internal_properties : CAAAAAgAAABoAN7PLpwJW3EiA7WVZdCA5EwrCA1rvutcDHooMWKe
```


---

## Mes Projets\repo\avsp_server\src\Mock_AcquisitionSignal.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_AcquisitionSignal
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a2b1b0fae919b
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_AcquisitionSignal est une Classe
      	herite IAcquisitionSignal
      	tEcoute est un tableau de chaÃ®nes
      	nb est entier
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628661610475131291
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628661610475196827
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1628662121583043312
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1628662125878077863
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : EcouteSocket
     internal_properties : CAAAAAgAAABLoxn892W5mHHhms9bbaHUibGC+psvlT5AtIg6/3RBeF3LmWq0q9orS6K+Tov7ThQU1/a8cHpW9Aa70c4hiQMWcB3Xd8xbezoF+zx0ELVX3Uco4Zbb9FwG3TUtiyLqfp+guClfHNfToPe9s0IRfgZVaqkzdjW/StWhhxH7EJit3qG0TqqmIpZPy4t+69VnHyklT9OMcsbZf76R+Gc5czumRSOl/acNRolwrD0yvJHQ/lPc099lnwaAO1f4I0TrRyMeubdQQtTG0Iag
     procedure_id : 1628662134468079522
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      m est MutexHelper(:mutex)
      nb+=1
      tEcoute.Ajoute("chaine"+nb)
      
     type : 458752
   -
     name : EnfileBuffer
     internal_properties : CAAAAAgAAACx+UZQaYf+/SsXqJ955bh4IDEXWh3QQPvpX1XeLx++4wZgVOfkk132HxI7CFk7O1pRsa7YpEkfk6iqD5tkGYxMy9Z+KxPy3p9CCkZr9A9rb1na7sFcxRzOg9KfaR/zltPzbwLkw+1SXZfCc51MZ6LP+dXJsS5SyQ4Bfu9Eh/WtQIZIGPVTSUVQ14wodqyMYVMSo9b7Z6pWOswXM+qwjJ0WNsM22bHTdDxdBm+n5hJuzqXZaxCzXUHFE+Vlw6WlzwtLmDvzy7Kz3w7GYWo=
     procedure_id : 1630629354088321663
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.EnfileBuffer
      PROCÃ‰DURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1630629375563228054
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.DefileBuffer
      PROCÃ‰DURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      renvoyer buf
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1631009656063354661
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode IAcquisitionSignal.TaillefileBuffer
      PROCÃ‰DURE  TaillefileBuffer() : entier
      
      renvoyer :fileBuffer..Occurrence
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x169a2b120fab6f2f
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Mock_ClientSocket.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_ClientSocket
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1694ed4908f72f17
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_ClientSocket est une Classe
      	herite IClientSocket
      	tEcoute est un tableau de chaÃ®nes
      	nb est entier
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627186263326469911
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627186263326535447
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1627186263326600983
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1627186263326666519
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     internal_properties : CAAAAAgAAABLoxn892W5mHHhms9bbaHUibGC+psvlT5AtIg6/3RBeF3LmWq0q9orS6K+Tov7ThQU1/a8cHpW9Aa70c4hiQMWcB3Xd8xbezoF+zx0ELVX3Uco4Zbb9FwG3TUtiyLqfp+guClfHNfToPe9s0IRfgZVaqkzdjW/StWhhxH7EJit3qG0TqqmIpZPy4t+69VnHyklT9OMcsbZf76R+Gc5czumRSOl/acNRolwrD0yvJHQ/lPc099lnwaAO1f4I0TrRyMeubdQQtTG0Iag
     procedure_id : 1627186263326732055
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.EcouteSocket
      PROCÃ‰DURE   EcouteSocket()
      
      m est MutexHelper(:mutex)
      nb+=1
      tEcoute.Ajoute("chaine"+nb)
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x168ffad002b380d4
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Mock_SignalDessine.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_SignalDessine
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a2e6b116a8548
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_SignalDessine est une Classe
      herite ISignalDessine
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628665252636493128
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628665252636558664
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Parametre
     internal_properties : CAAAAAgAAAADBTi1kFxz4gXhpQHhLflPeR/+hUHry48aM9g4kUhpKHg1I6Y0JZo2Be75ynKmDtWbBpqnIfW1FDonFyNKOfL/emoA5pKqw6TFVt/VNCTuSWtzl+dW79WREl2GSf+iiuaD1H2BcbkmKU+37qShFiuqL/lkMOc9Aj1XyjwrFGSOu262kwZGstqjAQBHZVVfZAwgd4q3Z9ZKwsH+R6gKhts8AXS6QTvlQBQQi0FUUVl3Jadj9pR5d9uhEu8ozsj8RogzXkMh+DmlEv/UtSj7e6NWhEr89w==
     procedure_id : 1628665321356575141
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.Parametre
      PROCÃ‰DURE  Parametre(champImage)
      
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1628665329946577019
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628665334241611617
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      RENVOYER Vrai
     type : 458752
   -
     name : DessineDansImage
     internal_properties : CAAAAAgAAAAMeMKRJSYnv+N7MFbI09Aa2jX0KCjnw9ay7jTGw5AXGTVwrJj2FXJARTq6jgMPDZrPo9wPLzqParxpV9LxiQsWNNUPo/z7q+bZawRgiDXXXZvgYVJ7pLwa0SUtZ3JqZgec4FHLFNFJ7tWPQUxvUAQrIONZqIMJmMNXMXddtgrTpP8GXFxkfOxdic0cFStxpa1zG2HreaTSynMrCrxIcARxpJRroZM96hZEoIjva8rJpZifrPRo9cKqwfpNNRY4bw/e/XbPmhV1Bctl
     procedure_id : 1628665342831613620
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalDessine.DessineDansImage
      PROCÃ‰DURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      RENVOYER Vrai
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644807634925418
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.DebutDessin
      PROCÃ‰DURE  DebutDessin()
      
     type : 458752
   -
     name : SauveImageJpeg
     internal_properties : CAAAAAgAAAAMeMKRJeYnP+N7sNbI09CaWrX0qKhnQ9YybjTGQxCXGTVwLJj2FXLARTo6DgMPDRpPI9yPr7oPajzpV9JxCYsWNNWPo/z7q2bZa4TgiDXX3RtgYdL7JDwaUaUtZ/Lq5gec4NHLFBEJLhVPgYyvEETr4CMZaENJWAOX8bed9soTZL9GHBwkvKydyY1cVWsx5W0zWyGrOeSSCrPrSvyIMEQxZNQrYVN9qlaEYMgvq4oJZVjfbLQoNYLqAbqNdVb4r8+ePTaPmhV1Bctl
     procedure_id : 1631354481865945909
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalDessine.SauveImageJpeg
      PROCÃ‰DURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      renvoyer vrai
     type : 458752
   -
     name : PrepareDessin
     internal_properties : CAAAAAgAAACyAh9Ocv3onOXLS4XRdrCvKrEpZ/gulcIby8mVXAfC7xdy9XNUnDMMagaRwonnHvXF7R+6CothFOhyAIcT6JtfFKRv2uN4EqpsQB8OLLH9w3Jy7vuNd1YXJLUC+Zq185GfHInzMgMnqGYw26/vEBc11mG0Xkk+WP0NOK6R8aw/cgjozqYARZcXzu0TXwZmk+mlODBGP+WVB9nrVe9hKGwOI0N81ExV
     procedure_id : 1894589466762224543
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.PrepareDessin
      PROCÃ‰DURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
     type : 458752
   -
     name : Affichage
     procedure_id : 1894589466762290485
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.Affichage
      PROCÃ‰DURE  Affichage()
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x169a2e6911677d49
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Mock_SignalEnregistrement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_SignalEnregistrement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a2e2b113c88b8
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_SignalEnregistrement est une Classe
      herite ISignalEnregistrement
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628664977755572408
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628664977755637944
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1628665119491885315
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628665123786919804
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     internal_properties : CAAAAAgAAACht87jPjIBcGu3/3c7mweNv9k8u4chic3Y6YL+W5Yn+QnVK37slwXepwGhSmo+XoWzPvofSS11BGofB1v6iboXmgoItkLKq7yNZldVHIz+WdNLV09uh/Vx4lWm4b9ygs5zVCWx8UkuEdcXwpCdwrfGw92IbFsB3uHrdoCHwBAq96Kap1KqltbXJSz7ofG7APi0K97Di2rmZqXqq3QGsifoVfgOza/5bDjEB2YHPDczyevX6iHDLZXbWKVekIo6gEZxHIFjun/v3LUaC5Jt6T3sOoCqWQ==
     procedure_id : 1628665132376921588
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.Parametre
      PROCÃ‰DURE  Parametre(nomFichier)
      
     type : 458752
   -
     name : EcritDansFichier
     internal_properties : CAAAAAgAAAAMeMKRJSYnv+N7MFbI09Aa2jX0KCjnw9ay7jTGw5AXGTVwrJj2FXJARTq6jgMPDZrPo9wPLzqParxpV9LxiQsWNNUPo/z7q+bZawRgiDXXXZvgYVJ7pLwa0SUtZ3JqZgec4FHLFNFJ7tWPQUxvUAQrIONZqIMJmMNXMXddtgrTpP8GXFxkfOxdic0cFStxpa1zG2HreaTSynMrCrxIcARxpJRroZM96hZEoIjva8rJpZifrPRo9cKqwfpNNRY4bw/e/XbPmhV1Bctl
     procedure_id : 1628665140966924294
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.EcritDansFichier
      PROCÃ‰DURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      RENVOYER Vrai
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x169a2e2911398136
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Mock_SignalTraitement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_SignalTraitement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x169a2df9112ac4cb
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_SignalTraitement est une Classe
      	herite ISignalTraitement
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628664763006043339
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628664763006108875
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1628664823136189886
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalTraitement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628664831726126165
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalTraitement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1628664836021160606
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Parametre
      PROCÃ‰DURE  Parametre(params)
      
      TypeVar(params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1628664844611162625
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Calcule
      PROCÃ‰DURE  Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      TypeVar(el)
      RENVOYER Vrai
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x169a2df01127a4ff
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\Mock_TreuilClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : Mock_TreuilClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16900fd303ad95d4
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      Mock_TreuilClient est une Classe
      	herite ITreuilClient
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1625816864455038420
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1625816864455103956
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1625816920289756919
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : Arrete
     procedure_id : 1625816924584791079
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : ParseDonnees
     internal_properties : CAAAAAgAAACx+UZQaYf+/SsXqJ955bh4IDEXWh3QQPvpX1XeLx++4wZgVOfkk132HxI7CFk7O1pRsa7YpEkfk6iqD5tkGYxMy9Z+KxPy3p9CCkZr9A9rb1na7sFcxRzOg9KfaR/zltPzbwLkw+1SXZfCc51MZ6LP+dXJsS5SyQ4Bfu9Eh/WtQIZIGPVTSUVQ14wodqyMYVMSo9b7Z6pWOswXM+qwjJ0WNsM22bHTdDxdBm+n5hJuzqXZaxCzXUHFE+Vlw6WlzwtLmDvzy7Kz3w7GYWo=
     procedure_id : 1643878258528745873
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilClient.ParseDonnees
      PROCÃ‰DURE  ParseDonnees(buf est un Buffer)
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x16900fca03aa701c
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\MutexHelper.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : MutexHelper
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x16880f9900d22a98
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      MutexHelper est une Classe
      	privÃ©Ã©
      		nomMutex est chaine
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623564815485315736
     type_code : 27
     code : |1+
      procÃ©dure Constructeur(nomMutex est chaine)
      
      :nomMutex = nomMutex
      MutexDÃ©but(:nomMutex)
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623564815485381272
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      MutexFin(:nomMutex)
     type : 655360
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x16880f9500cf1961
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\ServeurConnexion.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : ServeurConnexion
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1a0dd2310031f131
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877387727581999409
     type_code : 27
     code : |1+
      procÃ©dure privÃ©e Constructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("construit ServeurConnectionTreuil, mutex=%1",:mutex))
      
      :acquisition = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexionAcquisition)
      :treuils = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexiontreuilNonIdent)
      :app = FabriqueClient.CreeConnexionAuServeur(FabriqueClient.connexionApp)
      :nomThread = "thCheckClients"
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877387727582064945
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      Logger.Verbose(ChaÃ®neConstruit("dÃ©truit ServeurConnectionTreuil, mutex=%1",:mutex))
      
      liberer :acquisition
      libÃ©rer :treuils
      libÃ©rer :app
      SignalDÃ©truit(:nomThread)
     type : 655360
   -
     name : Demarre
     procedure_id : 1877387727582130481
     type_code : 12
     code : |1+
      procÃ©dure Demarre() : boolÃ©en
      
      Logger.Log("Demarre ServeurConnectionTreuil")
      si pas :treuils.Demarre() ALORS GOTO ERR
      si pas :acquisition.Demarre() ALORS GOTO ERR
      si pas :app.Demarre() ALORS GOTO ERR
      
      renvoyer vrai
      
      ERR:
      	ExceptionDÃ©clenche(1,"Impossible de dÃ©marrer les connecteurs de client")
      	RENVOYER faux
     type : 458752
   -
     name : Arrete
     procedure_id : 1877387727582196017
     type_code : 12
     code : |1+
      procÃ©dure Arrete() : boolÃ©en
      
      Logger.Log("Arrete ServeurConnectionTreuil")
      SI PAS :treuils.Arrete() ALORS GOTO ERR
      SI PAS :acquisition.Arrete() ALORS GOTO ERR
      SI PAS :app.Arrete() ALORS GOTO ERR
      
      RENVOYER Vrai
      
      ERR:
      	ExceptionDÃ©clenche(1,"Impossible d'arrÃªter les connecteurs de client")
      	RENVOYER Faux
     type : 458752
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1877387727582261553
     type_code : 12
     code : |1+
      procÃ©dure AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      si pas :clients[client.id]..Existe ALORS
      	m est un MutexHelper(:mutex)
      	:clients.InsÃ¨re(client.id, client)
      	Logger.Log(ChaÃ®neConstruit("ServeurConnectionTreuil Ajoute le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	renvoyer vrai
      FIN
      LoggerErreur.EcritLigne(ChaÃ®neConstruit("ServeurConnectionTreuil Le client[%1]=%2,%3 existe dÃ©jÃ ",client.nomThread,client.ip,client.canalSocket))
      RENVOYER faux
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAACs8cybCqLcnhnjHHfbSyStlngaGbHGLUC44IdkNV2L1vraVW0kfE+z1yWFsz4O9tkiV3wFTicsTFrT+wGXOk189nJbV0qDj37+pRH/I9bAwg+iCZJW033McFrf5S0jGqsY4rUt9Mhn82108DF1qDnXRH6Q5UDIsLaxypwk3nxMcpXN/31trwg2XT63XtFBFoLFRA9Bu9YbnPb9AGpJ8WbMQ2bNGw8VVe5ToEMHOmdTaCj0b/VhMBIH0bfG4I4y8IMm7NUsLYUlJA==
     procedure_id : 1877387727582327089
     type_code : 12
     code : |1+
      procÃ©dure SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI client<>null _et_ :clients[client.id]..Existe ALORS
      	Logger.Log(ChaÃ®neConstruit("ServeurConnectionTreuil Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	:clients.Supprime(client.id)
      	renvoyer vrai
      FIN
      RENVOYER faux
     type : 458752
   -
     name : DemarreEcouteSocket
     procedure_id : 1877387727582392625
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877387727582458161
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : ArreteEcouteSocket
     procedure_id : 1877387727582523697
     type_code : 12
     code : |1+
      procÃ©dure ArreteEcouteSocket()
      
      :arretDemandÃ©=Vrai
      SignalModifie(:nomThread,signalOuvert)
      
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)<>threadInexistant, :nomThread)
      :EcouteSocket()
     type : 458752
   -
     name : Init
     procedure_id : 1877387727582589233
     type_code : 12
     code : |1+
      procÃ©dure Init()
      
      Logger.Verbose("Init ServeurConnectionTreuil")
      
      :DemarreEcouteSocket()
     type : 458752
   -
     name : Quit
     procedure_id : 1877387727582654769
     type_code : 12
     code : |1+
      procÃ©dure Quit()
      
      Logger.Verbose("Quit ServeurConnectionTreuil")
      
      :Arrete()
      :ArreteEcouteSocket()
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1877387727582720305
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      POUR TOUT c dans :clients
      	t.Ajoute(c)
      FIN
      
      RENVOYER t
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1877387727582785841
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1877387727582851377
     type_code : 12
     code : |1+
      procÃ©dure ArreteAcquisition()
      
      t est un tableau de IClientSocket dynamique <- :treuils.RecupereClients()
      POUR TOUT cl IN t
      	tr est un TreuilClient dynamique <- cl
      	SI tr<>Null _ET_ tr.type=ITreuilClient.eRecepteur ALORS
      		tr.ArreteAcquisition()
      	FIN
      FIN
      
      RENVOYER Vrai
     type : 458752
  properties :
   -
     name : MonInstance
     identifier : 0x1a0dd2310032f131
     type_code : 103
     p_codes :
      -
        code : |1-
         procÃ©dure globale publique MonInstance() : ServeurConnexion
         
         si mInstance=Null ALORS
         	mInstance = allouer ServeurConnexion()
         	mInstance.Build()
         FIN
         
         renvoyer mInstance
        type : 1966080
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : ServeurConnexionTreuil
resources :
 string_res :
  identifier : 0x1687bcc8000aaea4
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_ServeurConnexion.wxt
```


---

## Mes Projets\repo\avsp_server\src\SignalDessine.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : SignalDessine
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x168d1448089c0833
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  internal_properties : CAAAAAgAAACOgrDDft/MUuYxYkHcZgLPyy8c0CNQuk5BHAdEh9T8KHpNfbtVFsEmrsX4O+r4RwxK+GA/PPu9df8abhnAjC81j1ps1XmM+fqdiMudW0PhRTEccwui7G/7SLBB68h/PUc4g8O0
  type_code : 10
  p_codes :
   -
     code : |1+
      SignalDessine est une Classe
      	herite de ISignalDessine	
      	nomGrapheVirtuel est une chaine
      	champGraphe est un chaine
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1624977340165326899
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1624977340165392435
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Parametre
     procedure_id : 1627191271271955340
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.Parametre
      PROCÃ‰DURE  Parametre(image)
      
      Logger.Debug("Parametre le champ image id=" + :id+", ")
      //:champImage.Hauteur = ExtraitChaÃ®ne(image,1,";")
      //:champImage.Largeur = ExtraitChaÃ®ne(image,2,";")
      :champGraphe = ExtraitChaÃ®ne(image,3,";")
      :nomGrapheVirtuel = "graphe_"+:id
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1627191902632294977
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      Logger.Debug("DÃ©marre l'affichage pour l'acquisition id=" + :id+", ")
      arretTimerSysDessin = faux
      :DebutDessin()
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627191915517264901
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Debug("Arrete l'affichage pour l'acquisition id=" + :id+", ")
      :champImage = ""
      :champGraphe = ""
      si :nomGrapheVirtuel<> "" alors grSupprimeTout(:nomGrapheVirtuel)
      :nomGrapheVirtuel = ""
      :arretTimerSysDessin = vrai
      
      RENVOYER Vrai
     type : 458752
   -
     name : DessineDansImage
     procedure_id : 1627990981583911624
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalDessine.DessineDansImage
      PROCÃ‰DURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      grSupprimeSÃ©rie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'Ã©lÃ©ment "+el.Index)
      pour i=1 _a_ el.tabBuffer..Occurrence
      	grAjouteDonnÃ©e(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
      
      grDessine(:nomGrapheVirtuel)
      
      renvoyer vrai
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644528460086025
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.DebutDessin
      PROCÃ‰DURE  DebutDessin()
      
      grCrÃ©e(:nomGrapheVirtuel,grCourbe)
      si ChampExiste(:champGraphe) ALORS
      	grDestinationChamp(:nomGrapheVirtuel, :champGraphe)	
      FIN
      
     type : 458752
   -
     name : SauveImageJpeg
     procedure_id : 1631354121087298622
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.SauveImageJpeg
      PROCÃ‰DURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      fic est une chaÃ®ne = "tmp/"+SansEspace(id)+"_img"+el.Index+".jpg"
      renvoyer dSauveImageJPEG(:champGraphe,fic)
     type : 458752
   -
     name : PrepareDessin
     procedure_id : 1829904725260522718
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.PrepareDessin
      PROCÃ‰DURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
      grSupprimeSÃ©rie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'Ã©lÃ©ment "+el.Index)
      POUR i=1 _Ã€_ el.tabBuffer..Occurrence
      	grAjouteDonnÃ©e(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
     type : 458752
   -
     name : Affichage
     procedure_id : 1894581293419273211
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalDessine.Affichage
      procÃ©dure Affichage()
      
      QUAND EXCEPTION
      	//FinTimerSys()
      	RETOUR
      FIN
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
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x168d14440899f92f
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_SignalDessine.wxt
```


---

## Mes Projets\repo\avsp_server\src\SignalEnregistrement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : SignalEnregistrement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x168d14420895ef1d
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      SignalEnregistrement est une Classe
      	herite ISignalEnregistrement
      	
      	nomFichier est une chaÃ®ne
      	handleFic est entier
      	
      	fileBuffer est une file de IAcquisitionSignal.Str_DATA_Acquisition
      	nomthreadEnregistrement est une chaine
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1624977314395123485
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      nomthreadEnregistrement ="enregistrementDansFichier"+&fileBuffer 
      SignalCrÃ©e(nomthreadEnregistrement,signalManuel,signalFermÃ©,partageAucun)
     type : 589824
   -
     name : Destructeur
     procedure_id : 1624977314395189021
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      SignalDÃ©truit(nomthreadEnregistrement)
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627215404694354351
     type_code : 12
     code : |1+
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
      			RENVOYER Faux
      		FIN
      		Logger.Verbose("Ecriture de l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      	FIN
      FIN
      })
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627215413284356463
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Verbose("ArrÃªte acquisition pour le fichier id=" + :id+", " + :nomFichier)
      
      //ThreadExÃ©cute(()=>{
      
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
      	
      //})
      //renvoyer vrai
      
      renvoyer faux
     type : 458752
   -
     name : Parametre
     procedure_id : 1627215421874358388
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : EcritDansFichier
     procedure_id : 1627989847711167977
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalEnregistrement.EcritDansFichier
      PROCÃ‰DURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : boolÃ©en
      
      enfile(fileBuffer,el)
      renvoyer vrai
      
      mbuf est un Buffer 
      SÃ©rialise(el,mbuf,psdXML)
      si pas fEcritLigne(:handleFic, mbuf) alors
      //hbuf est entier systÃ¨me = &el
      //si fEcrit(:handleFic,hbuf,IAcquisitionSignal.CST_TAILLE_STRUCT)<0 ALORS
      	LoggerErreur.EcritLigne("Impossible d'Ã©crire l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      	renvoyer faux
      FIN
      
      Logger.Verbose("Ecriture de l'Ã©lÃ©ment "+el.Index + " dans le fichier " + :nomFichier)
      renvoyer vrai
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x168d143c0892d9b1
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\SignalTraitement.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : SignalTraitement
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1687bdd30059c277
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      SignalTraitement est une Classe
      	herite ISignalTraitement
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623474904632050295
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474904632115831
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627220739045436891
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalTraitement.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() :boolÃ©en
      
      Logger.Verbose("Demarre acquisition pour le traitement id=" + :id+", ")
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627220747635439003
     type_code : 12
     code : |1-
      // RedÃ©finition de la mÃ©thode ISignalTraitement.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() :boolÃ©en
      
      Logger.Verbose("Arrete acquisition pour le traitement id=" + :id+", ")
      
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1627977052899360823
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ISignalTraitement.Parametre
      PROCÃ‰DURE  Parametre(params)
      
      Logger.Verbose("Parametre acquisition pour le traitement id=" + :id+", p=" + params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1627989401034072149
     type_code : 12
     code : |1+
      procÃ©dure Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
      
      dim est entier = el.tabBuffer..Occurrence
      pour i=1 _a_ dim
      	el.tabBuffer[i] += random(1,5)*100//PartieEntiÃ¨re(sinus(i * (20 / dim) * 2 * 180  ) * 1000) + random(1,5)*100
      FIN
      renvoyer vrai
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bdca00569bf4
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_SignalTraitement.wxt
```


---

## Mes Projets\repo\avsp_server\src\TreuilClient.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : TreuilClient
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1a0ed37f021b0a7d
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  internal_properties : CAAAAAgAAADY3WC9mLJm1IaVPK2cV7pjWtzL/FzEJ2QKxe6iDAZJSFhsHY6oezEVYE718hbQNcvsrIdxGjsHTYpokYLCrWQqB8Q/U2AKRxKDWH6Ulc0n
  type_code : 10
  p_codes :
   -
     code : |1+
      TreuilClient est une Classe
      	herite ITreuilClient
      	traitement est une ISignalTraitement dynamique
      	dessine est un ISignalDessine dynamique
          enregistre est ISignalEnregistrement dynamique
      	acquisition est un IAcquisitionSignal dynamique
      	
      	gestion est un IGestionDesCalculs dynamique
      	
      	filesocketEcritAR est une tableau associatif de chaine
      	Param_Interne_Treuils est Str_Param_Interne_Treuils
      	
      	fileBufferEmet est une File de Str_DATA_Emet_t
      fin
      
      Str_Param_Interne_Treuils est une structure
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
      	Energie					est un entier
      	Gain					est un entier
      	VitesseBP				est un entier
      	ValT0					est un reel
      	numeroTreuil est entier
      	Vitesse_cm_s est entier
      	Decalage_Position_cm est entier
      FIN
      
      Str_DATA_Emet_t est une structure
      	Index		est entier sur 4
      	POS_EMET	est un entier sur 4
      FIN
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877670637109774973
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      Logger.Verbose("construit TreuilER["+:mutex+"]")
      
      :nomThread="thTreuilER"+:id
      
      :traitement = FabriqueDeSignal.CreeTraitementSignal()
      :enregistre = FabriqueDeSignal.CreeEnregistrementSignal()
      :dessine = FabriqueDeSignal.CreeDessineSignal()
      :gestion = FabriqueDeSignal.CreeCalculeSignal()
      
      :traitement.ID = :id
      :enregistre.ID = :id
      :dessine.ID = :id
      :gestion.ID = :id
      :gestion.callbackThread = :threadTraitement
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877670637109840509
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      Logger.Verbose("dÃ©truit TreuilER["+:mutex+"]")
      
      libÃ©rer :traitement
      libÃ©rer :enregistre
      libÃ©rer :dessine
      libÃ©rer :gestion
     type : 655360
   -
     name : Arrete
     procedure_id : 1877670637109906045
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Arrete
      PROCÃ‰DURE   Arrete():boolÃ©en
      
      Logger.Debug("Arrete TreuilER["+:mutex+"]")
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Demarre
     procedure_id : 1877670637109971581
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IClientSocket.Demarre
      PROCÃ‰DURE   Demarre():boolÃ©en
      
      Logger.Debug("Demarre TreuilER["+:mutex+"], thread="+:nomThread)
      :DemarreEcouteSocket()
      :Ecrit(ITreuilClient::SOCKMSG_TYPE_DEMANDE)
      :Ecrit(ITreuilClient::SOCKMSG_NUMERO_TREUIL)
      RENVOYER Vrai
     type : 458752
   -
     name : ParametreNomFichier
     procedure_id : 1877670637110037117
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreNomFichier
      PROCÃ‰DURE  ParametreNomFichier(nomFichier est chaÃ®ne)
      
      Logger.Log("Nom fichier acquisition Treuil["+ :mutex + "]"+"="+nomFichier)
      :enregistre.Parametre(nomFichier)
     type : 458752
   -
     name : ParametreChampImage
     procedure_id : 1877670637110102653
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreChampImage
      PROCÃ‰DURE  ParametreChampImage(paramsImage)
      
      Logger.Log("DÃ©marre l'acquisition pour le Treuil["+ :mutex + "]")
      :dessine.Parametre(paramsImage)
     type : 458752
   -
     name : ParametreTraitement
     procedure_id : 1877670637110168189
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ParametreTraitement
      PROCÃ‰DURE  ParametreTraitement(params)
      
      Logger.Log("ParamÃ¨tre le traitement de l'acquisition pour le Treuil["+ :mutex + "]")
      :traitement.Parametre(params)
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1877670637110233725
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.DemarreAcquisition
      PROCÃ‰DURE  DemarreAcquisition() : boolÃ©en
      
      Logger.Log("DÃ©marre l'acquisition pour le Treuil["+ :mutex + "]")
      
      SI PAS :gestion.DemarreAcquisition() RENVOYER Faux
      SI PAS :enregistre.DemarreAcquisition() RENVOYER Faux
      SI PAS :dessine.DemarreAcquisition() RENVOYER Faux
      SI PAS :traitement.DemarreAcquisition() RENVOYER Faux
      
      RENVOYER Vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1877670637110299261
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilRecepteur.ArreteAcquisition
      PROCÃ‰DURE  ArreteAcquisition() : boolÃ©en
      
      Logger.Log("ArrÃªte l'acquisition pour le treuil["+ :mutex + "]")
      
      :gestion.ArreteAcquisition()
      :enregistre.ArreteAcquisition()
      :dessine.ArreteAcquisition()
      :traitement.ArreteAcquisition()
      
      RENVOYER Vrai
     type : 458752
   -
     name : ParseDonnees
     procedure_id : 1877670637110364797
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode ITreuilClient.ParseDonnees
      PROCÃ‰DURE ParseDonnees(buf est un Buffer)
      
      _TreuilsSockParse_SC(buf)
     type : 458752
   -
     name : threadTraitement
     procedure_id : 1877670637110430333
     type_code : 12
     code : |1+
      procÃ©dure privÃ©e threadTraitement(id,nomThread est chaine, procArretDemandÃ© est une procÃ©dure)
      
      Logger.Debug(ChaÃ®neConstruit("Lance le thread de traitement id=%1",id))
      
      quand exception
      	LoggerErreur.EcritLigne("Exception dans le traitement " + ExceptionInfo(errMessage))
      	ExceptionPropage()
      FIN
      
      boucle
      	si SignalAttend(nomThread,1)
      		sortir
      	FIN
      	si :acquisition<>null alors
      		si :acquisition.TaillefileBuffer()>0 ALORS
      			elem	est IAcquisitionSignal.Str_DATA_Acquisition
      			counter	est entier	= 0
      			tantque defile(:acquisition.fileBuffer,elem) _et_ counter<10
      				:traitement.Calcule(elem)
      				:enregistre.EcritDansFichier(elem)
      				Enfile(:dessine.fileAffichage,elem)
      				counter++
      			fin
      		FIN
      	fin
      	threadpause(1cs)
      FIN
     type : 458752
   -
     name : _TreuilsSockParse_SC
     internal_properties : CAAAAAgAAADYuPST6CP+T+K2ZU37o7g3n8Kew/nVjSZD5G6OyRuT2Yo67DV/AZmlWI7RE7NW48hb
     procedure_id : 1877670637110561405
     type_code : 12
     code : |1-
      PROCEDURE _TreuilsSockParse_SC(_buffer est un buffer)
      
      QUAND EXCEPTION DANS
      	nbMsg				est un entier	= Taille(_buffer)/::Size_MSG_Socket_RX
      	sVariable_Message	est une chaÃ®ne
      	sContenu_Message	est une chaÃ®ne
      	sVariable_AR		est une chaÃ®ne
      	sContenu_AR			est une chaÃ®ne
      	
      	POUR i = 1 A nbMsg
      		
      		sVariable_Message = ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) A (i*::Size_MSG_Socket_RX)]],1,::CST_SOCKMSG_SEP)
      		sContenu_Message = ExtraitChaÃ®ne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) A (i*::Size_MSG_Socket_RX)]],2,::CST_SOCKMSG_SEP)	
      		
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
      				sVariable_AR = ExtraitChaÃ®ne(sContenu_Message[[1 A (::Size_MSG_Socket_RX-5)]],1,"=")
      				sContenu_AR = ExtraitChaÃ®ne(sContenu_Message[[1 A (::Size_MSG_Socket_RX-5)]],2,"=")
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
      	LoggerErreur.EcritLigne("exception dans _TreuilsSockParse")
      FIN
      
      	procÃ©dure interne M(var)
      		renvoyer ChaÃ®neConstruit("{%1}{%2}{%3}",var,:canalSocket,:ip)
      	FIN
      
      	procÃ©dure interne gestion_deconnexion()
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
      	fin
      
      	procÃ©dure interne gestion_erreur()
      		
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
      	fin
      
      	procÃ©dure interne gestion_type_treuil()
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
      	fin
      
      	procÃ©dure interne gestion_ravs()
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
      	fin
      
      	procÃ©dure interne gestion_rpar()
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
      				t est Str_DATA_Emet_t
      				t.Index		= Val(ExtraitChaÃ®ne(sContenu_AR,1,"-"))
      				t.POS_EMET	= Val(ExtraitChaÃ®ne(sContenu_AR,2,"-"))
      				:Param_Interne_Treuils.mTreuilProfondeur = t.POS_EMET
      				Enfile(fileBufferEmet,t)
      				
      			AUTRE CAS
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN	
      	fin
      
      	procÃ©dure interne gestion_event_stop()
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
      	fin
      
      	procÃ©dure interne gestion_msg()
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
      	fin
      
      	procÃ©dure interne gestion_event_btnp()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de montÃ©e")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS							
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton dâ€™arrÃªt dâ€™urgence")
      		FIN
      	fin
      
      	procÃ©dure interne gestion_event_btnr()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de montÃ©e")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"RelÃ¢che du bouton dâ€™arrÃªt dâ€™urgence")
      		FIN
      	fin
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : TreuilEmetteur
resources :
 string_res :
  identifier : 0x16cf1baf021b5809
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```


---

## Mes Projets\repo\avsp_server\src\TreuilConnexion.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : TreuilConnexion
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x1687bd70002f3fa0
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
      TreuilConnexion est une Classe
      	herite de IConnexionClientSocket
      privÃ©
      	clients est un tableau associatif de ITreuilClient dynamique
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623474479427501984
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      
      Logger.Verbose("construit TreuilConnexion["+:mutex+"]")
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474479427567520
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
      Logger.Verbose("dÃ©truit TreuilConnexion["+:mutex+"]")
     type : 655360
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAABYA/3DxBAKpoXP9q0xkayH6OpSAYdI58ywoAG2N89/pNxQ9bVi1jmHX/X/YcT0JuNMNVx19Jk2NJobtTfVIAV+9IyvS0xZ9cIaqfMJXaj4jCHA7cb0FV9gTBbRsyMJNjkOuEE5D4VqwjIZI/56f8q4zU9HTutlU9nqs+OVz2ONy6raovroPE/NehuCi5a4I4eGhd4C+lfeF7OKk9e+KDMhHL80RO5uwCm8Kdrwk0ZwDY0R4gJcVXWm1ow5bUMpgWTXZfxFeDPRUA==
     procedure_id : 1623562577806767593
     type_code : 12
     code : |1+
      procÃ©dure AjouteClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      :clients.InsÃ¨re(client.id, client)
      Logger.Verbose(ChaÃ®neConstruit("TreuilConnexion["+:mutex+"] InsÃ¨re le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      renvoyer vrai
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAABYA/3DxBAKpoXP9q0xkayH6OpSAYdI58ywoAG2N89/pNxQ9bVi1jmHX/X/YcT0JuNMNVx19Jk2NJobtTfVIAV+9IyvS0xZ9cIaqfMJXaj4jCHA7cb0FV9gTBbRsyMJNjkOuEE5D4VqwjIZI/56f8q4zU9HTutlU9nqs+OVz2ONy6raovroPE/NehuCi5a4I4eGhd4C+lfeF7OKk9e+KDMhHL80RO5uwCm8Kdrwk0ZwDY0R4gJcVXWm1ow5bUMpgWTXZfxFeDPRUA==
     procedure_id : 1623562612166579638
     type_code : 12
     code : |1+
      procÃ©dure SupprimeClient(client IClientSocket dynamique):boolÃ©en
      
      m est un MutexHelper(:mutex)
      SI :clients[client.id]..Existe ALORS
      	Logger.Verbose(ChaÃ®neConstruit("TreuilConnexion["+:mutex+"] Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	:clients.Supprime(client.id)
      	RENVOYER Vrai
      fin
      RENVOYER faux
     type : 458752
   -
     name : Demarre
     procedure_id : 1624686154171345770
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : Arrete
     procedure_id : 1643608238200826745
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1643608401409688092
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : _VerifieDemande
     procedure_id : 1643608452949438091
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1625384752065244144
     type_code : 12
     code : |1+
      // RedÃ©finition de la mÃ©thode IConnexionClientSocket.RecupereClients
      PROCÃ‰DURE  RecupereClients() : tableau de IClientSocket dynamique
      
      t est tableau de IClientSocket dynamique
      
      pour tout c dans :clients
      	t.Ajoute(c)
      FIN
      
      renvoyer t
     type : 458752
  properties :
   -
     name : server
     identifier : 0x16d073b2088723b1
     type_code : 103
     p_codes :
      -
        code : |1+
         procÃ©dure protÃ©gÃ©e server() : IConnexionClientSocket
         
         renvoyer ServeurConnexion.MonInstance
        type : 1966080
     template_refs : []
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
resources :
 string_res :
  identifier : 0x1687bd6a002c268a
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
associated_test :
 name : TEST_TreuilConnexion.wxt
```


---

## Mes Projets\repo\avsp_server\src\WD_DualAxisChart.wdc

```wlanguage
#To edit and compare internal_properties, use WINDEV integrated tools.
#Internal properties refer to the properties of controls in windows, reports, etc.
info :
 name : WD_DualAxisChart
 major_version : 28
 minor_version : 0
 type : 4
 description : ""
 subtype : 0
class :
 identifier : 0x19b7e66e00404004
 internal_properties : CAAAAAgAAAB2/vstTMCJbS/hlxjFxirSqKvCUuv8YxgpWyl7S3iA
 code_elements :
  type_code : 10
  p_codes :
   -
     code : |1+
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
      END
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1853203131811315716
     type_code : 27
     code : |1+
      procÃ©dure Constructeur()
      _modeCurseur = ModeCurseur.Aucun
     type : 589824
   -
     name : Destructeur
     procedure_id : 1853203131811381252
     type_code : 28
     code : |1+
      procÃ©dure Destructeur()
      
     type : 655360
   -
     name : Init
     procedure_id : 1853203264955464136
     type_code : 12
     code : |1+
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
     type : 458752
   -
     name : ConfigurerGraphique
     procedure_id : 1853203355149798008
     type_code : 12
     code : |1+
      // RÃ©sumÃ© : <indiquez ici ce que fait la procÃ©dure>
      // Syntaxe :
      // ConfigurerGraphique ()
      //
      // ParamÃ¨tres :
      //	Aucun
      // Valeur de retour :
      // 	Aucune
      //
      // Exemple :
      // <Indiquez ici un exemple d'utilisation>
      //
      procÃ©dure ConfigurerGraphique(titre, nomY, uniteY, minY, maxY, inverseY)
      
      chart:Titles:Add(titre)
      
      chartArea:AxisY:Title		= nomY + " (" + uniteY + ")"
      chartArea:AxisY:Minimum		= minY
      chartArea:AxisY:Maximum		= maxY
      chartArea:AxisY:IsReversed	= inverseY
      
     type : 458752
   -
     name : ConfigurerSerie1
     procedure_id : 1853203436754261434
     type_code : 12
     code : |1+
      procÃ©dure ConfigurerSerie1(nom, minX, maxX, couleur est chaine)//r est un int, g est un int, b est un int)
      
      serie1:Name				= nom
      
      chartArea:AxisX:Title	= nom
      chartArea:AxisX:Minimum	= minX
      chartArea:AxisX:Maximum	= maxX
      
      clr est 'System.Drawing.Color' dynamic = 'System.Drawing.Color'::FromName(couleur)
      serie1:Color = clr
      chartArea:AxisX:TitleForeColor			= clr
      chartArea:AxisX:LabelStyle:ForeColor	= clr
     type : 458752
   -
     name : ConfigurerSerie2
     procedure_id : 1853203509768787846
     type_code : 12
     code : |1+
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
      
     type : 458752
   -
     name : AjouterPointSerie1
     procedure_id : 1853203694452555880
     type_code : 12
     code : |1+
      procÃ©dure AjouterPointSerie1(positionY, valeurX)
      
      serie1:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
     type : 458752
   -
     name : AjouterPointSerie2
     procedure_id : 1853203776057018899
     type_code : 12
     code : |1+
      procÃ©dure AjouterPointSerie2(positionY, valeurX)
      
      serie2:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
     type : 458752
   -
     name : EffacerDonnees
     procedure_id : 1853203831891672143
     type_code : 12
     code : |1-
      procÃ©dure EffacerDonnees()
      
      serie1:Points:Clear()
      serie2:Points:Clear()
     type : 458752
   -
     name : ExporterImage
     procedure_id : 1853203874841420732
     type_code : 12
     code : |1+
      procÃ©dure ExporterImage(chemin)
      
      chart:SaveImage(chemin, 'System.Windows.Forms.DataVisualization.Charting.ChartImageFormat'::PNG)
      
     type : 458752
   -
     name : Scrolling
     procedure_id : 1853750954891473848
     type_code : 12
     code : |1+
      procÃ©dure Scrolling(y)
      
      si NOT autoScroll alors retour
      
      min est un real = chartArea:AxisY:Minimum
      max est un real = chartArea:AxisY:Maximum
      		
      si min<= y <= max alors RETOUR
      	
      WHILE y > max
      	min += :fenetreY
      	max += :fenetreY
      END
      
      WHILE y < min
      	min -= :fenetreY
      	max -= :fenetreY
      END
      
      chartArea:AxisY:Minimum	= min
      chartArea:AxisY:Maximum	= max
      
     type : 458752
   -
     name : MakeARGB
     procedure_id : 1853754451005366261
     type_code : 12
     code : |1+
      procÃ©dure MakeARGB(aa est un int, r est un int, g est un int, b est un int)
      renvoyer bitDÃ©caleGauche(aa, 24) + bitDÃ©caleGauche(r, 16) + bitDÃ©caleGauche(g,8) + b
      
     type : 458752
   -
     name : ActiverCurseur
     procedure_id : 1854077273698485285
     type_code : 12
     code : |1+
      procÃ©dure ActiverCurseur(actif est boolean)
      
      curseurActif = actif
      chartarea:CursorY:IsUserEnabled	= actif
     type : 458752
   -
     name : SetModeCurseur
     procedure_id : 1854077492741868568
     type_code : 12
     code : |1+
      procÃ©dure SetModeCurseur(mode est ModeCurseur)
      
      _modeCurseur = mode
      
      si mode = ModeCurseur.Zone alors
      	chartarea:CursorY:IsUserSelectionEnabled = True
      ELSE
      	chartArea:CursorY:IsUserSelectionEnabled = False
      END
      
     type : 458752
   -
     name : OnMouseMove
     procedure_id : 1854077973778317892
     type_code : 12
     code : |1+
      procÃ©dure OnMouseMove(yPixel est entier)
      
      si NOT curseurActif OR modeCurseur <> ModeCurseur.Ligne alors RETOUR
      
      y est reel = chartarea:AxisY:PixelPositionToValue(yPixel)
      
      chartarea:CursorY:Position = y
      
      MajCurseurLigne(y)
      
     type : 458752
   -
     name : MoveCurseur
     procedure_id : 1854078205706605126
     type_code : 12
     code : |1+
      procÃ©dure MoveCurseur(delta is real)
      
      si modeCurseur <> ModeCurseur.Ligne alors RETOUR
      
      y is real = chartarea:CursorY:Position + delta
      
      chartarea:CursorY:Position = y
      
      MajCurseurLigne(y)
      
     type : 458752
   -
     name : MajCurseurLigne
     procedure_id : 1854078356030496033
     type_code : 12
     code : |1+
      // RÃ©sumÃ© : <indiquez ici ce que fait la procÃ©dure>
      // Syntaxe :
      // MajCurseurLigne ()
      //
      // ParamÃ¨tres :
      //	Aucun
      // Valeur de retour :
      // 	Aucune
      //
      // Exemple :
      // <Indiquez ici un exemple d'utilisation>
      //
      procÃ©dure MajCurseurLigne(y is real)
      
      args is STC_CurseurEventArgs
      
      args:Y	= y
      args:X1	= Interpoler(serie1, y)
      args:X2	= Interpoler(serie2, y)
      
      si EventCurseur <> Null alors
      	EventCurseur(args)
      END
      
     type : 458752
   -
     name : MajCurseurZone
     procedure_id : 1854078493469547135
     type_code : 12
     code : |1+
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
      END
      
     type : 458752
   -
     name : Interpoler
     procedure_id : 1854078622318595405
     type_code : 12
     code : |1+
      procÃ©dure Interpoler(s is object dynamic, y is real)
      
      n is int = s:Points:Count
      si n < 2 alors renvoyer Null
      
      p1,p2 sont objet dynamique
      x1, y1	sont reels
      x2, y2	sont reels
      
      FOR i = 1 TO n-1
      	
      	p1	= s:Points[i-1]
      	p2	= s:Points[i]
      	
      	y1	= p1:YValues[0]
      	y2	= p2:YValues[0]
      	
      	si y >= y1 et y <= y2 alors
      		x1	= p1:XValue
      		x2	= p2:XValue
      		
      		renvoyer x1 + (y - y1) * (x2 - x1) / (y2 - y1)
      	END
      	
      END
      
      renvoyer Null
      
     type : 458752
   -
     name : GetStat
     procedure_id : 1854078845657012318
     type_code : 12
     code : |1+
      procÃ©dure GetStat(s is 'System.Windows.Forms.DataVisualization.Charting'.Series dynamic, yMin is real, yMax is real, isMin is boolean)
      
      first	is boolean	= True
      val		is real
      
      p est objet dynamique
      x, y sont reels
      
      FOR i = 0 TO s:Points:Count-1
      	
      	p	= s:Points[i]
      	y	= p:YValues[0]
      	
      	si y >= yMin et y <= yMax alors
      		
      		x = p:XValue
      		
      		si first alors
      			val		= x
      			first	= False
      		ELSE
      			si isMin et x < val alors val = x
      			si NOT isMin et x > val alors val = x
      		END
      		
      	END
      	
      END
      
      si first alors renvoyer Null
      renvoyer val
      
     type : 458752
   -
     name : GetMoyenne
     procedure_id : 1854078961621156872
     type_code : 12
     code : |1+
      procÃ©dure GetMoyenne(s is object dynamic, yMin is real, yMax is real)
      
      sum		is real	= 0
      count	is int	= 0
      p		est objet dynamique
      x, y	sont reels
      
      FOR i = 0 TO s:Points:Count-1
      	
      	p	= s:Points[i]
      	y	= p:YValues[0]
      	
      	si y >= yMin et y <= yMax alors
      		sum += p:XValue
      		count++
      	END
      	
      END
      
      si count = 0 alors renvoyer Null
      
      renvoyer sum / count
      
     type : 458752
  procedure_templates : []
  property_templates : []
 code_parameters :
  internal_properties : CAAAAAgAAAA6ih3UbgNXHwTtiPSFUEj+2fi/m7v4QV2rqidAupM=
  original_name : Classe1
resources :
 string_res :
  identifier : 0x19b7e668003d27e8
  internal_properties : CAAAAAgAAAAnMYFQ1bL/vz9ehh7L22SNNSlIzGTOI8h5F/WtgDNP
custom_note :
 internal_properties : CAAAAAgAAABtB9HWVzrXO2+4NDRVK0vmzaNKrCKqH1DBX30lMmGZ
```

