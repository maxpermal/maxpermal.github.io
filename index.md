# Sources du projet

Document généré automatiquement.

---

## AcquisitionConnexion.wdc

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

## AcquisitionSignalClient.wdc

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

## AppConnexion.wdc

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

## ApplicationClient.wdc

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

## FabriqueClient.wdc

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

## FabriqueDeSignal.wdc

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

## GestionDesCalculs.wdc

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

## IAcquisitionSignal.wdc

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

## IClientSocket.wdc

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

## IConnexionClientSocket.wdc

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

## IGestionDesCalculs.wdc

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

## ISignalDessine.wdc

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

## ISignalEnregistrement.wdc

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

## ISignalTraitement.wdc

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

## ITreuilClient.wdc

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

## Logger.wdc

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

## LoggerErreur.wdc

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

## main.wdw

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
       type : 2
       content :
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
        internal_properties : CAAAAAgAAADhUVVuntPA5r/3nXCjJ74JhG+ZEBzvy83pW/PqLrmoQWyK87vnz0q9525TZjkjccmlSRLaKHDSRmXbHiZlgMsLPfI3ip5FUyf0slSgOHf2iJmQBbBsPaOHNn+a6H7JmcAtEToASCC1T1lr+TkwT7W2KVNQc0h/3QMfLvyVYQVBsXTX2bPzq/FhNEe6WMXDzSH+/1R8iTv/QQZsqasvAqqun9iUBR2E
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
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
       name : IP
       identifier : 0x2a4be702058c8a2f
       internal_properties : CAAAAAgAAAD0N9LT5dKE7/OFrLqti1LOk3DZ9qk98q7Z1nD++BciWohpa45phhcN6MuibhzwMSyMgHV2xQF5GPRMYbxOMrixAwn1YtZmDYxNFMIiir6cXmkQx0wr5sumZj8RjDe7gP9ETcgGxV8541wwO5sji2JZlLZT3VeDbXKUb+tb/Ezv6ilW5YI=
       type : 2
       content :
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
       name : FreqClac
       identifier : 0x2a4be69a04ecf372
       internal_properties : CAAAAAgAAAAEOh8z6ztQSxqisiFZHPj4yX6tFOupGX5N4ThVyn/JR3EP3IwKh9PIsnCuDlf3z3+EJlgqL6oCIII4AQmgTmZou5sTpn2t0zMIR/+L1KmOHDKirpevuRm1hWJTXQpJLp+sNhD1LSLwYoiBcNr7Ih0oTsBb9IUvkLiOWSfnmUldgVQKqgLunXrQBgoE
       type : 2
       content :
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
       name : Gain
       identifier : 0x2a4be69a04eef3a1
       internal_properties : CAAAAAgAAABH7QRV4yBdj8jUhaA2SRHThFAlI69pe5blvovqmsCMkhT+heKGzfBjympjygfPMVuoEr0lb3gRcu91EEmD2jVl0INQWIWWD/KEjYZNmx7+EDXYJMz+lHPSwdSRJtSPLxHV2keF7KnfxwOUEm7RQ4x8E7nJ+AJyIH96lxqVg3i2pq15QI5oEw==
       type : 2
       content :
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
       name : Energie
       identifier : 0x2a4be69a04f0f3d0
       internal_properties : CAAAAAgAAADHKueNZhSPcuRNJqdZVGOGTuQwCR+GQIi3As3alJrkRbe2ecADL8N67JPaEuo8/ZgbA/PMtLayfCmoBkxZITF3zF5N2yBJ2wiuEuv90jrxXiGtGqtXL0cMazEvpT7YzxFv/WzCJpJ28z3YVyudosWhRIIRQTntvLd0UIOa4ALs3VSlVmy/q34F7w==
       type : 2
       content :
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
       name : Vitesse
       identifier : 0x2a4be69a04f2f3ff
       internal_properties : CAAAAAgAAADsiJop4Qtyf4SPLgj+51pq0NqlvxyQvoHBF6MiP/4x/luti0GYiYOejbKr1FQkhRhk3xQJRp0EVEIHt/3jxcjPNJpvwaOF4lbtcpSbmE4HKQBGJIvJWBVIoqfNtXLQDg70c1IpVTWzDkgXgy+rnOK2dfyxFIf/ypR0vSN1j2/58XH7751gqqWk
       type : 2
       content :
        internal_properties : CAAAAAgAAACYncLzpE8tsa2w3LG34BDBbOlOvbzqOlvqbSSPAGVpQKVaI/fZs2T+LMa7G06ST55XhhNMltflmcgZpMsQXODvvbNv9sfOuEL8XEWOK6+UecFl/zQ1v/bZsiv9IR1yBTa68OiF84L/iHezlgsUXRLWIFiarkAJH/fxXVstS4iqbVIG+utidtRQa2Ls/uUvsn7rmtaIIps0bjonRcUFWM/hovaHPBt1GgtBnsfdRlTnXibGaLKIF4axVVOZROg4TKNxuVEo3LbWckUhrTtFqzjjlKRVJCS9KMjjUeUiA0GL5cDMN2WPFuIwaT3h3NE2CtsIecWfa5asZ6JDsiktBXkkMK+m+B78Cd0plScao/mI9yeD26q99hep5IA9niZMjy9lWwyTBjCT3PcHmiitmjozDv+FAVxMOh71IGapk8/sTeKYkngnUX7sfNms/l7REC/i2RtECzRbzraFm4OXOr8jAY0VucL9oms7bRnTtlZgceKODteWztpvSwrlNe0hTmyfhny4ixE9Ygd/kkxQ5U6qLvB5CU5NuW4ckWm1kEyNkg==
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
  internal_properties : CAAAAAgAAABNngmV3H0Lz2G7mVywvh3JxfjCvxMh+exnJCj0+NqqU+eXfUskM2GrL7ZnW2Js8em3SeTOWg==
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

## Mock_AcquisitionSignal.wdc

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

## Mock_ClientSocket.wdc

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

## Mock_SignalDessine.wdc

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

## Mock_SignalEnregistrement.wdc

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

## Mock_SignalTraitement.wdc

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

## Mock_TreuilClient.wdc

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

## MutexHelper.wdc

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

## ServeurConnexion.wdc

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

## SignalDessine.wdc

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
  internal_properties : CAAAAAgAAACmcEcPUOl8P88f4DqAteA6m/Mt111GGOClrNjaLJ9vO63bNEtdrqYoCCnjX1eG34p8AOi72Du5JSuppQ==
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

## SignalEnregistrement.wdc

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

## SignalTraitement.wdc

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

## TreuilClient.wdc

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
  internal_properties : CAAAAAgAAACmcEcPUOl8P88f4DqAteA6m/Mt111GGOClrNjaLJ9vO63bNEtdrqYoCCnjX1eG34p8AOi72Du5JSuppQ==
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

## TreuilConnexion.wdc

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

## WD_DualAxisChart.wdc

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

