AcquisitionConnexion.wdc
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
      procédure Constructeur()
      
      Logger.Verbose(ChaîneConstruit("construit AcquisitionConnexion, mutex=%1",:mutex))
      :nomThread="thAcquisitionConnexion"
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474810141895581
     type_code : 28
     code : |1+
      procédure Destructeur()
      
      Logger.Verbose(ChaîneConstruit("détruit AcquisitionConnexion, mutex=%1",:mutex))
     type : 655360
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1625068844508388771
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IConnexionClientSocket.AjouteClient
      PROCÉDURE AjouteClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      :clients.Insère(client.id, client)
      Logger.Verbose(ChaîneConstruit("AcquisitionConnexion[%1%] Insère le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1625068853098391195
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IConnexionClientSocket.SupprimeClient
      PROCÉDURE SupprimeClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      SI :clients[client.id]..Existe ALORS
      	Logger.Verbose(ChaîneConstruit("AcquisitionConnexion[%1%] Supprime le client[%2]=%3,%4",:mutex, client.nomThread, client.ip, client.canalSocket))
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
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE Demarre():booléen
      
      Logger.Debug(ChaîneConstruit("AcquisitionConnexion[%1] Demarre la socket %2, %3, %4",:mutex,:NomSocket,:portSocket,:AdresseSocketServer))
      
      SI SocketCrée(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketTailleDébut)
      	Logger.Debug("Création de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      FIN
      
      LoggerErreur.EcritLigne("Impossible de créer la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
      
      //SI pas SocketCrée(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      //	Logger.Log("AcquisitionConnexion a levé une erreur dans la création du socket " + :NomSocket)
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
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE Arrete():booléen
      
      Logger.Debug(ChaîneConstruit("AcquisitionConnexion[%1] Arrete la socket %2",:mutex,:NomSocket))
      
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
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE EcouteSocket()
      
      SI PAS :arretDemandé ALORS
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
      
      procédure interne accepte_demande():booléen
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
      procédure privée _AjouteAcquisitionTreuil(acq est AcquisitionSignalClient dynamique)
      
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
      // Redéfinition de la méthode IConnexionClientSocket.RecupereClients
      PROCÉDURE  RecupereClients() : tableau de IClientSocket dynamique
      
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
         procédure protégée server() : IConnexionClientSocket
         
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

AcquisitionSignalClient.wdc
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
      	acquisitionDemarrée est un booléen=faux
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877670499662073119
     type_code : 27
     code : |1+
      procédure Constructeur()
      
      Logger.Verbose("construit AcquisitionSignal")
      
      :nomThread = "AcquisitionSignal"+:id
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877670499662138655
     type_code : 28
     code : |1+
      procédure Destructeur()
      Logger.Verbose("détruit AcquisitionSignal")
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1877670499662204191
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE Demarre():booléen
      
      :DemarreEcouteSocket()
      :acquisitionDemarrée = vrai
      Logger.Debug("AcquisitionSignal Demarre, thread="+:nomThread)
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1877670499662269727
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE Arrete():booléen
      
      Logger.Debug("AcquisitionSignal Arrete, thread="+:nomThread)
      :acquisitionDemarrée = Faux
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877670499662335263
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE EcouteSocket()
      
      tmp est un buffer = :Lit()
      SI tmp<>"" ALORS
      	:bufferLecture+=tmp
      	si :_synchroniseTrameDansBuffer() _et_ :acquisitionDemarrée alors
      		:_parseBuffer()
      	FIN
      FIN
     type : 458752
   -
     name : _parseBuffer
     procedure_id : 1877670499662400799
     type_code : 12
     code : |1+
      procédure privée _parseBuffer()
      
      nbMsg est un entier = Taille(:bufferLecture)/CST_TAILLE_STRUCT
      stRDat est un IAcquisitionSignal.Str_DATA_Acquisition
      nPos est entier = 0
      bufTemp est un Buffer
      
      Logger.Verbose("Parse le buffer")
      
      i est un entier
      POUR i = 1 À nbMsg
      	bufTemp = :bufferLecture[[nPos+1 À CST_TAILLE_STRUCT+nPos ]]
      	nPos+=CST_TAILLE_STRUCT
      	Transfert(&stRDat,&bufTemp,CST_TAILLE_STRUCT)
      	:EnfileBuffer(stRDat)
      	Logger.Verbose("Buffer index :" +stRDat.Index)
      //	Trace(bufTemp)
      FIN
      SI Taille(:bufferLecture)>nPos ALORS
      	:bufferLecture = :bufferLecture[[nPos+1 À]]
      SINON
      	:bufferLecture=""
      FIN
     type : 458752
   -
     name : _synchroniseTrameDansBuffer
     procedure_id : 1877670499662466335
     type_code : 12
     code : |1+
      procédure privée _synchroniseTrameDansBuffer() : booléen
      
      pour i=1 _a_ bufferLecture..Taille
      	si :bufferLecture..Taille>=3 _et_ :bufferLecture[1 a 3]<>"DEB" ALORS
      		:bufferLecture = :bufferLecture[2 À ]
      	sinon
      		SI :bufferLecture..Taille>=CST_TAILLE_STRUCT ALORS
      			SI :bufferLecture[CST_TAILLE_STRUCT-3 À CST_TAILLE_STRUCT-1] = "FIN" ALORS
      				Logger.Verbose("buffer synchro")
      				RENVOYER Vrai
      			SINON
      				LoggerErreur.EcritLigne("buffer non synchro pas de fin trouvé, reset buffer")
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
      // Redéfinition de la méthode IAcquisitionSignal.EnfileBuffer
      PROCÉDURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
      Enfile(:fileBuffer,buf)
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1877670499662597407
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IAcquisitionSignal.DefileBuffer
      PROCÉDURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      Défile(:fileBuffer,buf)
      renvoyer buf
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1877670499662662943
     type_code : 12
     code : |1-
      // Redéfinition de la méthode IAcquisitionSignal.TaillefileBuffer
      PROCÉDURE  TaillefileBuffer() : entier
      
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

AppConnexion.wdc
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
      	hérite IConnexionClientSocket
      PRIVÉE
      	client est un IClientSocket dynamique
      FIN
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1877424251996975816
     type_code : 27
     code : |1+
      procédure Constructeur()
      
      Logger.Verbose(ChaîneConstruit("construit AppConnexion, mutex=%1",:mutex))
      :nomThread="thApplicationConnexion"
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877424251997041352
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : AjouteClient
     procedure_id : 1877424857587571525
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IConnexionClientSocket.AjouteClient
      PROCÉDURE  AjouteClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      :client = client
      Logger.Verbose(ChaîneConstruit("AppConnexion[%1%] Insère le client[%2]=%3,%4",:mutex, client.nomThread,client.ip,client.canalSocket))
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1877425106695732818
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete() : booléen
      
      Logger.Debug(ChaîneConstruit("AppConnexion[%1] Arrete la socket %2",:mutex,:NomSocket))
      
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
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre() : booléen
      
      Logger.Debug(ChaîneConstruit("AppConnexion[%1] Demarre la socket %2, %3, %4",:mutex,:NomSocket,:portSocket,:AdresseSocketServer))
      
      SI SocketCrée(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketTailleDébut)
      	Logger.Debug("Création de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      FIN
      
      LoggerErreur.EcritLigne("Impossible de créer la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877426223387358488
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE   EcouteSocket()
      
      SI PAS :arretDemandé ALORS
      	SI SocketAttendConnexion(:NomSocket,:portSocket) ALORS
      		canal est chaîne
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
      
      	PROCÉDURE INTERNE accepte_demande():booléen
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
      // Redéfinition de la méthode IConnexionClientSocket.RecupereClients
      PROCÉDURE  RecupereClients() : tableau de IClientSocket dynamique
      t est tableau de IClientSocket dynamique
      
      t.Ajoute(:client)
      
      RENVOYER t
     type : 458752
   -
     name : SupprimeClient
     procedure_id : 1877427198345620150
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IConnexionClientSocket.SupprimeClient
      PROCÉDURE  SupprimeClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      SI :client = client ALORS
      	Logger.Verbose(ChaîneConstruit("AppConnexion[%1%] Supprime le client[%2]=%3,%4",:mutex, client.nomThread, client.ip, client.canalSocket))
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
         procédure privée  server() : IConnexionClientSocket
         
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

ApplicationClient.wdc
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
      procédure Constructeur()
      
      Logger.Verbose("construit ApplicationClient")
      
      :nomThread = "ApplicationClient"+:id
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1877469963335506738
     type_code : 28
     code : |1+
      procédure Destructeur()
      
      Logger.Verbose("détruit ApplicationClient")
     type : 655360
   -
     name : Demarre
     procedure_id : 1877470354177621409
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre() : booléen
      
      :DemarreEcouteSocket()
      Logger.Debug("ApplicationClient Demarre, thread="+:nomThread)
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1877470474436799623
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE   EcouteSocket()
      
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
      procédure protégée _ParseDonnees(_buffer est buffer)
      
      
      QUAND EXCEPTION DANS
      	nbMsg				est un entier	= Taille(_buffer)/1000
      	sVariable_Message	est une chaîne
      	sContenu_Message	est une chaîne
      	sVariable_AR		est une chaîne
      	sContenu_AR			est une chaîne
      	
      	POUR i = 1 À nbMsg
      		
      //		sVariable_Message	= ExtraitChaîne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) À (i*::Size_MSG_Socket_RX)]],1,::CST_SOCKMSG_SEP)
      //		sContenu_Message	= ExtraitChaîne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) À (i*::Size_MSG_Socket_RX)]],2,::CST_SOCKMSG_SEP)	
      		
      		SELON sVariable_Message
      			CAS "STATUS","TEMP"
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+sVariable_Message+"_:_"+sContenu_Message)
      			//				
      			CAS "DECO"
      				//				SELON sContenu_Message					
      				//					CAS "exit.From.PC"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de l’application suite à la demande provenant du PC Serveur")	
      				//					CAS "exit.From.PEGASE"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de l'application suite à la demande provenant de l’application Cliente Pegase")	
      				//					CAS "exit.From.REBOOT"
      				//						Logger.Debug(nIndiceListeClient+"Sortie de l'application suite à la demande provenant d'un Reboot de la plateforme Pegase")	
      				//					AUTRE CAS
      				//						LoggerErreur.EcritLigne(nIndiceListeClient+"Sortie de l'application suite à la demande provenant de source inconnue")	
      				//				FIN			
      				//				TableauSupprime(GG_sendMessage,sVariable_AR)
      			//					
      			CAS "ERR"
      //				sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")	
      				SELON sVariable_AR
      					CAS "ARRT"
      						SI sContenu_AR = "PILE---WIFI" ALORS
      							//ERREUR WIFI - Goulot d'etranglement							
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+ERREUR de transmission WIFI")
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Erreur de transmission du buffer d’acquisition par la socket, le treuil arrête l’acquisition pour éviter un plantage")
      						FIN
      					//						
      					CAS "IDENT"
      						SI sContenu_AR = "NoCOMP" ALORS
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne voit aucun composant compatible")
      						FIN	
      						SI sContenu_AR = "COMPNonAdresse" ALORS
      							LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne reconnaît pas le composant")
      						FIN	
      						//					CAS "COMM"
      						//						SI sContenu_AR = "----Arduino" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Communication série Pegase/Arduino inopérante")
      						//							LoggerErreur.EcritLigne("Gain _: Erreur"+sContenu_AR)
      						//						FIN	
      						//						SI sContenu_AR = "----OneWire" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Communication 1-Wire Arduino/Sonde inopérante")
      						//							LoggerErreur.EcritLigne("Gain _: Erreur"+sContenu_AR)
      						//						FIN
      						//					CAS "MESURE"
      						//						SI sContenu_AR = "SENS--COD" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Le moteur tourne dans le mauvais sens")
      						//						FIN	
      						//					CAS "VITESSE"
      						//						SI sContenu_AR = "NONSPECIF" ALORS
      						//							Logger.Debug(nIndiceListeClient+"Pas de vitesse configurée")
      						//						FIN	
      						//					CAS "MOTEUR"
      						//						SI sContenu_AR = "BLOQ--MOT" ALORS							
      						//							Logger.Debug(nIndiceListeClient+"Blocage moteur, aucune rotation détectée")
      						//							Gb_AnnuleMesureERR = Vrai
      						//							LoggerErreur.EcritLigne( "Blocage moteur, aucune rotation détectée")
      						//						FIN	
      						//						SI sContenu_AR = "DATA--COD" ALORS							
      						//							Logger.Debug(nIndiceListeClient+"Réception données Codeur alors que le moteur est censé être à l'arrêt")
      						//						FIN
      						//					CAS "GOTO"
      						//						SI sContenu_AR = "NON-ATTEINT" ALORS
      						//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Arrêt moteur alors que la destination du GOTO n’a pas été atteinte")
      						//							Gn_FinGOTO++	
      						//							SI Gn_FinGOTO > 1 ALORS
      						//								SI FenEtat(FEN_trptfixe)<>Inexistant ALORS 
      						//									FEN_trptfixe.BTNACTION..Grisé = Faux
      						//									FEN_trptfixe.BTNENVOYER..Grisé = Faux
      						//								FIN
      						//								SI FenEtat(FEN_trdepla)<>Inexistant ALORS
      						//									FEN_trdepla.BTNACTION..Grisé = Faux
      						//									FEN_trdepla.BTN_RazTreuil..Grisé = Faux
      						//									FEN_trdepla.FORCEPOS..Grisé = Faux	
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
      //				sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")
      				//				SELON sVariable_AR								
      				//					CAS "1"
      				//						si Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilIP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      				//							Param_Interne_Treuils.mTreuilType=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      				//							
      				//							Param_Interne_Treuils.mTreuilVersion=ExtraitChaîne(sContenu_AR,1,"-")
      				//							GG_AVS.iVersionT1 = val(Param_Interne_Treuils.mTreuilVersion)*100
      				//							Param_Interne_Treuils.mTreuilFreq=Val(ExtraitChaîne(sContenu_AR,2,"-"))
      				//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 1 ALORS
      				//								Param_Interne_Treuils.Energie=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      				//							SINON
      				//								Param_Interne_Treuils.Gain=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      				//							FIN							
      				//							si Val(ExtraitChaîne(sContenu_AR,4,"-")) = 0 alors 
      				//								Param_Interne_Treuils.mTreuilSens="Montée"
      				//							sinon
      				//								Param_Interne_Treuils.mTreuilSens="Descente"
      				//							FIN
      				//						sinon
      				//							Param_Interne_Treuils.T2_IP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      				//							Param_Interne_Treuils.T2_Type=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      				//							
      				//							Param_Interne_Treuils.T2_Version=ExtraitChaîne(sContenu_AR,1,"-")
      				//							GG_AVS.iVersionT2 = Val(Param_Interne_Treuils.T2_Version)*100
      				//							Param_Interne_Treuils.T2_Freq=Val(ExtraitChaîne(sContenu_AR,2,"-"))
      				//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 2 ALORS
      				//								Param_Interne_Treuils.Energie=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      				//							SINON
      				//								Param_Interne_Treuils.Gain=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      				//							FIN
      				//							SI Val(ExtraitChaîne(sContenu_AR,4,"-")) = 0 ALORS 
      				//								Param_Interne_Treuils.T2_Sens="Montée"
      				//							SINON
      				//								Param_Interne_Treuils.T2_Sens="Descente"
      				//							FIN
      				//						FIN
      				//						
      				//					CAS "2"
      				//						Gn_REP_QAVS++			
      				//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      				//							Param_Interne_Treuils.mTreuilProfondeur=Val(ExtraitChaîne(sContenu_AR,1,"*"))/2
      				//							Param_Interne_Treuils.mTreuilVitesseUp=Val(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),2))
      				//							Param_Interne_Treuils.mTreuilVitesseDown=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),4),2))
      				//							Param_Interne_Treuils.mTreuilMesure=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),6),2))	
      				//						SINON
      				//							Param_Interne_Treuils.T2_Profondeur=Val(ExtraitChaîne(sContenu_AR,1,"*"))/2
      				//							Param_Interne_Treuils.T2_VitesseUp=Val(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),2))
      				//							Param_Interne_Treuils.T2_VitesseDown=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),4),2))
      				//							Param_Interne_Treuils.T2_Mesure=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),6),2))				
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
      //				sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      //				sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")
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
      				//				sVariable_AR = ExtraitChaîne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],1,"=")
      				//				sContenu_AR  = ExtraitChaîne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],2,"=")
      				//				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_MSG:_ "+sVariable_AR+"_:_"+sContenu_AR)
      				//				SELON sVariable_AR
      				//					CAS "MESURE"					
      				//						si sContenu_AR = "FIN--GOTO" ALORS
      				//							Gn_FinGOTO++
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"----==FIN GOTO MESURE==----")	
      				//							SI Gn_FinGOTO > 1 ALORS
      				//								 SI FenEtat(FEN_trptfixe)<>Inexistant ALORS 
      				//								 	FEN_trptfixe.BTNACTION..Grisé = Faux
      				//								 	FEN_trptfixe.BTNENVOYER..Grisé = Faux
      				//								 FIN
      				//								 SI FenEtat(FEN_trdepla)<>Inexistant ALORS
      				//									FEN_trdepla.BTNACTION..Grisé = Faux
      				//									FEN_trdepla.BTN_RazTreuil..Grisé = Faux
      				//									FEN_trdepla.FORCEPOS..Grisé = Faux	
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
      				//				sVariable_AR = ExtraitChaîne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],1,"=")
      				//				sContenu_AR  = ExtraitChaîne(sContenu_Message[[1 A (:Size_MSG_Socket_RX-5)]],2,"=")
      				//				SELON sVariable_AR
      				//					CAS "STOP"
      				//						Gn_StopFROM = Val(Droite(sContenu_AR,2))
      				//						SELON Gn_StopFROM							
      				//							CAS CST_STOPFROM_ARRIVEAFTERGOTO
      				//								//Trace("Gn_StopFROM _:_CST_STOPFROM_ARRIVEAFTERGOTO")
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : ARRIVED_AFTER_GOTO")
      				//							CAS	CST_STOPFROM_FINCALIB
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : FIN_CALIBRATION")
      				//							CAS	CST_STOPFROM_PWMNULLE
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : PWM_NULLE")
      				//							CAS CST_STOPFROM_CONFIGSENS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : CONFIG_SENS")
      				//							CAS CST_STOPFROM_ERRSENS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Erreur SENS")
      				//							CAS CST_STOPFROM_BP
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Bouton Poussoir")
      				//							CAS CST_STOPFROM_CLOSEPWM
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Fin PWM")
      				//							CAS CST_STOPFROM_AU
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Arret d'Urgence")
      				//							CAS CST_STOPFROM_ERRCALIB
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Erreur CALIBRATION")	
      				//							CAS CST_STOPFROM_ERRGOTO
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Erreur GOTO")
      				//							CAS CST_STOPFROM_CMDSOCKET
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Depuis PC")
      				//							CAS CST_STOPFROM_BLOCAGEMOTEUR
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur : Blocage Moteur")		
      				//							AUTRE CAS
      				//								MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Source de l'arrêt moteur inconnu")
      				//								Gn_StopFROM = CST_STOPFROM_BLOCAGEMOTEUR+1
      				//						FIN
      				//						//[TEST]
      				//						enfile(fileDemandePostionTreuils,vrai)
      				//					CAS "BTNP"
      				//						SI sContenu_AR = "FROM-----UP" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton de montée")
      				//						FIN	
      				//						SI sContenu_AR = "FROM---DOWN" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton de descente")
      				//						FIN	
      				//						SI sContenu_AR = "FROM-----AU" ALORS							
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Appui sur le bouton d’arrêt d’urgence")
      				//							Gb_AnnuleMesureERR = Vrai
      				//							Enfile(fileErreurMessage, "Appui sur le bouton d’arrêt d’urgence")
      				//						FIN	
      				//					CAS "BTNR"
      				//						SI sContenu_AR = "FROM-----UP" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Relâche du bouton de montée")
      				//						FIN	
      				//						SI sContenu_AR = "FROM---DOWN" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Relâche du bouton de descente")
      				//						FIN	
      				//						SI sContenu_AR = "FROM-----AU" ALORS
      				//							MAJ_AFF_TRAME_SOCKET(nIndiceListeClient,"Relâche du bouton d’arrêt d’urgence")
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
      				////					Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_:_ "+"Erreur Réception Contenu_Message")
      				////				FIN
      			//				
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Variable :"+sVariable_Message)
      				LoggerErreur.EcritLigne("Contenu :"+sContenu_Message)
      				//LoggerErreur.EcritLigne(ChaîneConstruit("[%1%][%2][%3]_:_Erreur Réception Contenu_Message",:ip,:id,:type))
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
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete() : booléen
      
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

AcquisitionConnexion.wdc
PCS                     B      9       j                  01F280051n                 fYҦ8cw 1 j]X   Ȅ	 R  į     "      B      A c q u i s i t i o n C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          b                   9                         01F280051n     v       :      >        
   
c                                                                 Έ !   AcquisitionConnexion                                    / ?a?t  H   qM( # @qT  +     a   q=	 ]x  R    +   D    
dP  @Ol   
х  b 
)*8`1 ƈ"  ,     cG
5u  yC  3yf 	a N vp   F:f s I G I   "  4f  4B  78u ,  ̛:T ac  ҦO z z B 8[ Q   \ %O. J  Y tz ʳ  1m̈  (  j  < Lׯ Ϧ] h=oĸQӲo\  ʋ 7p Ɩ   o ?D	2x    Z{    H  z  =ʸ  ;p  =V hAq Ƨ N@   ы _  E  k   yT R >-
/  8#"    # x  *   	  a`  [*  `   Jj   Ȑ N 
  u H@:  (" h8
H  C   O (   Q ;  
 : DCɮNL1B  +  ک   b˭sѷ f   7 C B B    2  ^p &%  I 7 xm7  ,    Ct9    @  ; "
0 %  @  <  @      . P 4 ! O  ; =N    u Y    |  cw `),6"  vX f   Lo 6 2 ۵ׁB # 1 
w r7v 0      }W _ 
  : _}ف  7݀  w e i *{3   j_
  m  `ē4   氃 : hrE!\L 	  @  c #62  lc    Hr &   Ay, D  È 	8 @C   j8 J  4    ~ 8 F    ]j  ^  ˛𤩅ܰ# <|0A Z An UP  BLi ހÍ* pR 2|  4 v	&  j{ 7F <6C+N %" ٢ ʈ t #J  @  YA Nߤ[v D
 UE	|  Ii
 ? )   \  w ] 
 `6 7 d>   ] +/L   zy '   `  y | Jx    ]#N}  ?      " h
 Ȣ  {l$!NH C ÿ 	    G G ٘ g;c  B  	 IE      0, iO CԦV53\-k[    AI7<K& 6˅ mo  rb7  ͈y     9 Et O PgE  n  {]Db  /" 
  t 3 w    b-  *  #   ū   p  @   O    H$h Gx " 4 "  r#1 #ۇ ( Q aP %    9 "  he    = j  " 5 anf ZV ё {h     y R    ň TD J" `
fС*n    3!0J v 8l +   h "&Ӽ   Y- RB45     @b( V  B  Y 3 APBSPt  3 YMk ` jph r B! *    	  C І8h%{#  0 9 Ed 
^ ':#N 9  5 n4E KaO tB N    @5 aMk\  
 V  INm  	9i   
^ N=(  `   F   yc)݆  q   P 
 8  d  ڐ :4 Ol "  '\
Z0 Bp 9 A
N 
U   M@uBhCV-    H r 41  b 
d چ  +(p
B 0q8JPdr    /Ȑ  *   "     }1VT  V 0 x '   B  `  @_J^j;   =i, < JJb #TR Y)؎ߊH  / k]	 0    T  #  `<^A Ƅ  C 	F ) 0f Zq @Ly-9 ;  J @; !/jp-}  *h ' C  `      Y (S;   3 9 j^3   J7   p  N s'b_ co    lTf      g?   ٧=-( | ؅  5   D+ ܋5 ' hD<*ܐ  n&E J  
 T   % ҁ

 ^ #  ']B 0 .  
 U   : 1 *
  'fՇ[e 1 V   i q   V   p +] xׂ  p [ PӊW5   & j[ (Vћ> w4S   y ! ːi  *8 Jd9 ԁ i  c% M 9֊ 
J    T P    
NrI 膺&=pAZ  ٌ  0 L~ ;       C	H
`y^  aSn'.C  0   yг	T  `ܣ   _k Ϛ*   } 	 &! : Kks 4W 9  0   H nnP ,  #(CsGy D   = t J1x   A6V   '  !     O  k&  'q @  8  2y | <+c {G:~miO Ԯ 7Pq-leK[ D ˹E+o}
\ W1sSr   h9w:    s  F庮 n   ] 7" 5/y      I/ #  u w   | _  1 ~    #KFe S     l	   
+ k   A
Ȇ i{	   -
l  O:  ] x  l Pb Qb R 
   > # [ S  'p  & ˟ ?ԣ      7 -'YȔ(  >h C@2 xAb  ZP   )  + &jR- *.   2U  0 3 B  2B$ |c7yp   3| '~ '8  *48 ( *   ;    b  1 4    !` *a
 l  @   ó -  0   i#ŹR" қ'8    V !('A
3@ 9A P7M{" :    OC," *Y Ut X|E  E   ZL
\  ]       
 b̚7x =   38 @  @FVtC A *Ь 2 "P FL,Bl     Ftİ)F%ٌ ( n D  ` HF  7F;    D30   %Ċ ^Ժ `<Ȓ,ʲ, Ҭz   @<ld-}; /HD 0?T#    &P4   Q7 7sԽ2 I*  tC d  9 3  Њ9p  1x^ É  2 Ț  
 #=B Ey +  *.b4F BGeT ` X ƨ E7  ɒV   <	8 2 5E  J !  ı -ɸ%i  pʡ)8?1ôRK ;&	 T  #-QH7dH :	  σ =;    <y    & <C<	2     `  np q r 	 ` V   48' - h # 
 LIy ׄ  4 #|I d' E  J    Ύ J    @  )j *  ,b 니   #      ?     ? ? P  A 
  IOmP  
 Q     y   9
O  B #   t      O}   Ј  B  ğ}P        љ H)   Bn   Bj! B,C زm 20 2sCu Q9lHy ;  < 3 Q'	,  @<  8 Д 7`   G 5   Lb
"/   +2u . D EkKńLGP JjS ! ?
  H7  E N  '( '   *  " ' P    BL! ,- zN:,I cS   Rn +  *h6vJ,  #    ,   U aիd  XM k 250NPL  0 Cm D F  H ;	 T  :  1    tU   ٔ  
     2Og   Y  p  pO   .R          \  }     %P ]   щ  ԓX{ X  U s  P  X{Byp   ԐA y p  $  h ! ԩ T  2    @'
Ck R2  3 R1 3,U  4 ۼ / 3= 3  He , L  0 (Q݁8 8| j   4L1K  : 5 78Wg|˷kF  K  V  VyP  +7  82    6u c9 2 80  #$ U5[g 
  :Ě 3   օ    2   t͝ Mv < `; =: [  5 Ǎ! E  m`; |]     k M U  L   *  *c ă  <` + sܐ ̱yޱ  B   LH  j     ; .  &* Is 8 4  
   1 
 p (F (  Eu$_s; v   i 0   i_  8 ) H, % l<   :  X
 =  ܥ  l;xC CJm   : 0 D[    k  z U M/5  5   \ L ۴  SݽmW ŻW 0<%^ #f]u U:܁
"%頮       ^څ\   8SCD&  *  e   = (Ã
  ߐ{(   k! c8'q' 3` 
    L  * TOUQ}'V< c; .	 8 e A c 3 4 [q  2p9 ;:  [.ž  MP~ :Ѓ  %    _3P 0~L# ԯs:  Q ֶ ͊k;Uf: #̝ e 杻  0 x ʐ  Gf Ne0b.MbmnS 
 Ic +   4 <ܠ ]gv 
| W      :   b E 3b   χ膅X & M . > y XeP mg ֟         G  F  pH  N i
 3     @ @ 8P  Hx 
K  YA mAJѿ  Bx  H- h2   "Rj   1       坐 6XP qXP $   a ^%  ! +  .   s`3   8
   k xߛ4 K _BS  hc xcɈc+UZP L _= F>6  K ?  ^ 6U9k  82 L pd d  
 C p c  
^m   8 . ; M  NN i
 [ R M v  feW6X m  e;e V ]  ^.: + `Nc~ ` ܁=V' v  H  ۫3  W nd;    䶋   4 P f | #g CMլ g%  o:  %6  pb_ )N w  4g    }  <  ܢ $R١ |. }    g  ?dhWh  ? njLqЉ  ߸興 ) /  P l Y  	 @x @
l${	f -  ! i 	 Q   )    	e 
uA 	 X1     A }h<  3   c 6  X 3  X 8  >  GH  =k
` h  Y֖ T &0 2 
H   4'+     t   ۄ3zS:      t Β-q ɨ  n ɶ  Fl E `쀳 V  + uL _ZoW 5dMMd+     PՇ M =a  ΁    ҵ  `    .y .   A g s Uq ew   _h |j7'Y qU  y _ `8   `	 ai ? dq . 鏕           w    l 
     n   g0x     G          W      iY  Ј    2 U (Q5 hA z    x    Q pM|g#  P               (  A        2   
     E +f |'<f~&7	#Xj {  R   O  (r  = L  s  t                                     R   R    Έ N   C:\Mes Projets\repo\avsp_server\src\TEST_AcquisitionConnexion.wxt    

AcquisitionSignalClient.wdc
PCS                     B      9       j                  01F280051n                 BP`@^  ~Y s     msƊʭ0 
 !}     "      \      A c q u i s i t i o n S i g n a l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             PCS          i                   9                         01F280051n     y              >        
                                                                        Έ $   AcquisitionSignalClient                                    /} @H  F  6em  g    pM + 1 Z´   S{ 4=eH  
        h6    
d   @ ,	 C     b 
r*@`1 ƈ"  , <   cG
5u  yCE
 7b i  ̛:!  	j獙< Сc& ?w  y :  Iy("*HcfNL JV |sf͛9 < :  ǫ']l% 	 *B T1  5mژb   N  	 #  1 J}
  I[ F<  2u  A:   { 춲   pl  %L 4  q#f b J
a458m   qF  < _  ! 5o O@  w  =~     / GO  @     _? }    (ck{   /  *  } S < F ; S # м#z A  {k    o 241   .  ,  +   
% j  { ;c:2     G l  Q !   " g l    (   ¸& %x K-   @   !    ز*  |  "  h  J< Ϸ8`+
"" &  #43F    ! 6 OPIĨ    O  焈 2 +c4 }# E ht 2 ӵ   N "j4   +   R D P4 ƺ , l I lm ;]   ހÍ*  M 2
ai  
 k    (c;oT %"2    H`=J  ~ H  @   L B
-    7ԓ* P  =  ?}   0 2 P f 0[~9 ' A  7 9 2  e u  !   .x>:  tN  l  ! y ?H n  ū.       OP   뒫 `Г2 h7Xu V8  L(}h  3
@ 
     &
56J=5UUV%-   u Z  56|V 9 5Y3 -#8ˡm   U+[ +S[     _    (  + a  : .  = ًP  =  # EԺ    ~{ 	|3N n  G  Zk =     
{ [˞    2'  `n |# F! . 	Ʉ ,  E #  F| @ Q +C ` )  t      2  
b P 8 0J O BT  ux nz  ܊[ YAD 0(  (     t*       pW 
	"  $  ̲  `H !sR Vl23 Ԇ,  M ~ u a   [D  kX      N' s#iZ :       5 r  ڢ̮b   	W   0 \a j cUfw?  k   H "» <x
C    H,   .v" uHz ,  Bf     Ce    Dc  B ⡀* H   56 @0  l   H on$Mk*        |揞   o| YD   U  ^ ʆ G Z"S `e z    *   n &D   x z #    P 
fp  `T8  0 ai O   0 @  ,  3 Ab - ֪  Q p   | +    :>   첍 Z  A 4 :d"% Hz  \ D%q%;3^Q  zW #u 9 !  2 + u f " h  p P  I0 Mrғ % J ef vm  ^9=Yoa     ] 2} Q-0 GR |L  Mh?    
    E:_   E  Τ  RF  v E
S8     )L  Cr   e|      ' q   i_  ڞl 
  C Qyj  =lb p jd   hzE G@he Ⰲ bP  a \*B P   
k      b  {Y;ƆT  hľp ^l%  ӓhe upC B㨦3  Ab ⷆ+       #A    )P  T   D x 3 a
|@ 8L 2X  V`   zx E    
st    Wf5 RA 0C0 y ^m 
   at ED5!(  q MS   K $ U   FK $ 4 Al  ro    @ 0Z.  1me  _Z = O0m[`s        " *΄ne   T4 -  H0>! 0w#a   "ݍ օ I  O u *    >   m m  5w   o  @ E& s  | N W  3<  Y  ( 
 *U * R    J
|ϙ        aN n|   'sP _3 `y [
iT " yQB `0 e
V Q a3  e
w i   t   R 
  ,0Ay      &)  ^s xE] 95 c؁  
   Սsi  .TĊ  bUЉn J   &5   M  . jx ql:s  j $  C y    DT r4 >6('    ck4=7   Ӂ g}R Nt #] L 
  v/xA Xi  /| 
CBя  *,   <x  9   	 N    5n    }凟 n5 I #x      : ?   
  bY< Q < 7! ؁&c  !% @3 ,#  
R  # ҙ 8 ƒ R   ;	>12   (   c   s2( 2 {     =  = s  =.#
߻  [ @    	( O  "L;   +S $ F / 45  ,e{CTZ5  WC Њ bc [b&\ 5i-  b 7B<DCLDa < 7܂Cx CIt&t".  xІj 6[ D^
 :  M `O 
  8  nh 0	 `8E      j   _   2 쪓}ҏ a   ( " x+(  Z8G ct({C% J  P36s S H :  < ن J NP Fԓ   KE  FQI    A
  8   6{3n 3: 3 + ZA  x" 
=̓ (â95  142dB x     )03 { % B3 H    3@C  I.P= /P (# & '  ,       =  2     4  hT    r <)Q ɢ$I4  ;  K>  *B \J     # J  = C  [ q  ,   ʸԿ  /P (   I"lC!   T   d  % 5  S < 4 ؀ HS   O
 Q B J |/ L  
08  4 
#   É |L   l|  [  
 B S |<$ h  PBʔJ  
#8L ɡܹ 䂻Ŀ d hQJ     H# 7 8  s  ¦  R  -   4˓ >qJE 	  Oy   NG+ BH {, O 9  + tI\P  V ,; 5 !  AD   C]^
A -D\D- 
Q c  D g     B N   O z "   	 I   &(ѭ !%R  P	 BHRr2  (7y87 
  H   Ɓ  
 ycF rF  FfJ5~   @  ф  t R   )؈9    &H0 b  h   0 R  [ @ 6 0 6
 5By H"3   5uT T 2    ; #;P 	tC< P FŹ` l HU;jQ   9t  y    j   % J \@̐= 5 lKQ bP f ѷx6R 6k 
jn 6ꉈW m 6q݈p rç9  tFd  ^ҷ 	S  7  Mgͬ4 ݐUqěЯO S Y n = Ӓ  >
R  
T Ԙ 02 S :  !3 = B) !K-*
BYL  T KTQ  9     ٳKÄl  3  ;T 
ծ Y 5U = ,   (@ U  
Z5[ LB  O S
  U       U< Ђ P  C  c}F4QB< gTQI     r  L  H Y|
P 0	 A Ew Ul 0  D ۈx Z   5^ R+ w  a  F  
{m t<  7 /"  YNض` 7 P@X   ؈ %0x  2 ؔZ0 س M  0
 ؤ E ȭd]t] ,Ly  % ;  9 }T># σ  E5      E  
  2    OD; q ΪC{  	   @[fU[   UַUDU c{ ϥ
= Q ]   2)  S    X   y {®|rW;Q7  W~e d e W   2 W  Z  ^  _  a  W  / Ȑ     h
 ~ ά h iD   2  T   ص  |+           (  A        2   
     x   s   (JZj 
/ݭ 12 >   C  \      1r  `                         

AppConnexion.wdc
PCS                     B      9       j                  01F280051n                  ? "  F R 9  F &\ "  > Ν #     "             A p p C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          a                   9                         01F280051n     n       C      >        
                                                                        Έ    AppConnexion                                    /
@[   [
" ^     & |  C A     53  %    |    4  &8    
d 
  d  PC     b 
*8`1 ƈ"  ,   ĉCΛ7f y  :!  A  :t   . 8 I  4) EDi       J\I O5f ԡ 3u E  #ӓ"F   
1EF u   IPɴ	; &N o  K ,ڜe  F ;C   ;  (y  m  P f  ㇈ A    m JCٹw   C ܀ GPs/o   Ǌ- ]}6 U?_n c   es     n  V |   -:W
   -   H% (m3qƀ  K 74 k      3 0 
   {  A   Qz  "  [ !" c  *Î9  Q ;  *	) #B a  b  %  *무Lcm   g   0 +  )B  B
q JI ^p %%  I 7 Ќ ) O< óD ̣o  +.   
 >;   50   F ˮ DW     9?Q    CN J [ -2f "
Gc Hu Wc 5 UW
5dl݈W_    "
Ø`86 ap WY7
  [   (>+6" v P  TL :յ  ȉ(X   ~0
 p #wS  e ޤE T   - 
V  Vڂ  `   v  
AO  H,` b  ɶ m    U %t q  S  ҍ ]wᕗ@zO ߕ  w9~GU9` 9p $
" 
 9   */,d   aȡ
~ E , a      d  q   : ^    J > " & @ 
_   1 *"   WbB;i R     êVj)ɮ^j!7 *L a v |sTx5+c h 7 p  4r F #
 	#C : :  7F8 3*  M %"   2"jk  ( ~   $X    cK5  S  t -    +m  
 <H Y  0   a "
e  *  ߦ~ Kp#axE   0B 	B  TyL[ L   \  Y˚ =v  7 + = > t`h  @D p  ) i"     cj  A(
 7$  q d  H7 Qn踵  t  `   A:xm&  P z	H    t {ܙ | g @   Y Ḝ   ~  U " A *      C  U(Aɍ4+"  &  Jx    ^8! p   B    f"   XF Ϡ0
*d       /T ) @  8h MRKo "#p    VH(`$ AcD ) 5e  B03 a*gP    +?  Tn  y" +K 
a $ȼ ɼ
3  + f t  R+h) C 2    &'Z   xN 2 Ɍ
v 78  Go8 [H  Ϙn n8LNv'(3I0C P  N [ S >  e 1! A :  * J     6    of   g   0q#     ϙ 
Yq1RQ 6` u@   9  5  P7w    u   fׂ  !  h^ ;ʴ i)]T b% DK\  Bp 9 A
 }  <危 e\T7 X 6G{/ ŗ)܀ 5w   ΗG&e 7 i{ ǆ  U 	 @ G 
5 h s K    Y9 xp d0  Ф r = HD0  kak  z%,?h  ky  N._ K]3 ĭ ~ g\  BƜC  r t$ S @ z   ̦Ʊ 	x N  "R s s  |gM IO7 3
  
G5:Y 
  i gP  e

}hD 0    *D2RP RU   y K?3    ny  쀆  p + ꠸ R[  <O1  91  ::   	  qe  E 6Exف yl ) n 3     j0 \{ 7ء
m  g      ^2Q}L Ϙ  ,m4 }  D f    @UA
n0AS@$ ` F     3)t   a ` ӸzJ yAD  1  3 	Ww g7 9rM     p     * 렂   NM ECzc6K[ җ Z    j h     ӫl  F      m   X   o7 q &x }$x Jn  
5 P   p  )%]H #3	I t-
Fx  ݾD  =  Xy      iy  D  K1 It K   \R  J>̦ + 7t v| =H8   92\ 
  5 X 2     D :U  +  q  * O s /   s oj
 Ȋ  D'  3 HO^    HTu X    U2 I g&+K    `   k  0   r& k  :< A | s    98Ju ʐ    c P  \TG $

    |"  G &  | C  Ͽ P O&돻  n5L    8pp ۚ Y  r 
   p+ * M3 " 
/ h >y в b<3 20  
ށ     
 ` o+?Ș @;    =3@4   9c4 C    C٬~ ,      0   D W  /  2 Ñ@    &P4 	 Q78>   '  (     +     698H 
 'h?1   ;5 ; ̚0~ 091  < ;D   د s  K    r   ڰ    > p⸏ 8P  A  : `B  H9D  :p K P h1 Ui h@WD   Z    \|   ś     E ō 0FZĮ      o  Ey:Di N<   u0   #0    +G ;  T 9 X     : a 1 K7y@  K1   q18 'Û1 c&  D 
   H  2  <#C w  d  kA  6l ! #q  h 7 ; 37 < " ?l 1!      {	ı K>qr  !    P   6 )  1 Iy  P  SI  2 8 
0    I *  ?   CQ   3t:;  <  #I< 
hJВ*       # J  ʧ ʦ ɯKo  9  =  hJ K K  I KB"<	/ K -C3 S3 ;A  	   J9q 3 3  'xj   
p*8 J  LyͬT     h,
)ʰ     1   d ?3X  
  #JL  Tʦ`  ,  *  @  A 4N
,  
     l  L  l  :   L <	# 7  :   `    ## *  '82 K $K8 '"9)8H  P*  s   D :04,;  1ܓ  $) 4   t";  a&: A=N l I
  5 ' D>: y ; =   
-!   <    (  0 K+   "!x l&)     , @ Q 6`&    p  J lK Ķ2  'д LK  m
) K(' +  = )P5} 6m 0  P 1C# RE J P I[ 7 > 4δ j+ C  D}O} P 
R  S PO;	PkO '
8[%ȽT r   OyX0<% h Ldu #  _   0   U
A$D  OP .BEp
  RDF4  `- p H ;7   ģںD írѭ  8@EO W   P   W}XPѺZBEoW  !U $G`ƅ p  Q &
 G    Z  ^  x Xa e  c P	 I  X    b  j /j r FkS P 2    H    E    =Dv  j     (: X ;   { 0~ ; `  覻Hi=  E  [1wU 1 1}B&Q  cZ} ƈ h (     Ѻ Q R  AD   dQ8     ќ =܈   ) tܼ]B ȱ    O     } k b    rU-G0u]  mׁx ޚ z  y 8NX  r$9DLX   U 驁% ^8 gM  30 C@ h 8   @ F #x 8  9 {  IH VȽI؆      
 D E q8  Z  z   YW:   F  فX E^  Y G   Z  nD  A\c   5  J  [X    }  'X   ;Q  )8ȳ ;S , ë   ']    Ʋ q
 -2 3  ۱   JQ   }N  (c5A : =	 
  BΛʩ  )     ԓ   >Q ⦚ Z b ̄
   ٛ9蛿 ? ;  a 4 ͗ =  = y  ƪ   =   6,Je  :,  T  d z ߔ  | [  P  Q&   Jj
5<n L۴  
kB(   E  %S>L  A,  }];1 E  s} a\ ە ? D Dڨ#  
   W 0 sW	6   E @ Pp :٘K  Zg ͮU:     FD9 A    l$  D  Y
ǣ ǡh   k[$  5 Aʀo  N A  [Ƀu h  h ވ        lH  Π}  ~ g r ; c  q 
>
I\    # N
  ߨ qNG  ᲈ    
 H ?[y           (  A        2   
      	   J 01u?  ʁ      f  1y  : % 6i  ij                          

ApplicationClient.wdc
PCS                     B      9       j                  01F280051n                  S n' JEdDam簜.^   LQ  SS8 P      "      w*      A p p l i c a t i o n C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                   9                         01F280051n     s        (      >        
                                                                        Έ    ApplicationClient                                    / p; q7i{  Ei=ۍG  9Bc   e 9` ## D}1T$ _> Q     (       
dP   &       b 
 <`1@E    , < ĉ F
1u  yCĥ 7uB     3!l  C L  2   tdɓ#"*Hcf  :A  T  8dؘ  T)S I   b ;g Xmb L;s    &Đ s ] V a P E xF  h  0  e ?D 1s  Ev x HèUG\}zr NeL%]3j +V     ήGb   vhϣ  Vn t  N[ -   
 oo )  [ x  "U @y    g ϧ_  ? XL      : d  >- @< 4   z Î    =O K       #,@ " B h  N ( 9 "F 2 

 7$ 0  Rp ? + 2  ! & @
 ^ ª1 0* Ȉ  	 
a  ^ i  n i Ϻl Ɋ . ހÍ*  K 2B !  t	& h ʫX A 2";  @   - B      ( ~   P  2    ΂# T { ;  s56 "  5SL 4Vo%-0V  S=J _ (b
ֲLzU  
)vY  ck   0 a   ) ,  u '   Y
q8   5 <)    k 
/  
4䠂B  X   
 h"o 4  ɘ F B W ~   P" SL $ dr'   *˘K;    >  j 2

y^P Q * tRV-SM#   OǍ   Pm 3  S ۴^   v      {l rAcZ 2    ӏ    ۭ  ]    ܈ ]Wi # z Vɖ ^y*  7ĘC/ p8 Q  @ ,<0 X   =:1bņ B   Uq 
      #7^N C J̯    m  <S " b
(  F "   7 q |a     q9M  "!  G +  
,   Xy"[  %  .4Pif|ӟr 1 q -A d:  PmzS  T   0E  @   +@A
F B 0 1  |B2\nըG
-p1Z  f  Y i zڨRE  T
  Y Ӭ 5J%1-] N Z(  b}a[Z  4 	H    a #  b   A$ 
@    9 "n   #7  M  [ l5    3|C   Į`p   96) A  g 
 =ǘ" q As  =  0@t   b p ԭ u  MfW   N V      $YV AŤ yГ     
s~³_ <   "upCvғ  /         L c  "B%4  =  F  &@a
T8fZ $    .r  ]    q S Ă  !
 3Jh  @  C] b#k  ESA]PH m  5(H   4) `<zP3HP*0 b s%   |" ^  F hN  
7 *O    q : "   BbW_  E  b L 46~ 2  HeԸ    Q *EǓ 12x  W  G[ .h $[  C  V ,T  H    (E ` 7`     ʑ`  @ 8 Ic n  " b2   B #8 aFh(_*;  ^ CM ɗ q4 軦H 1  H 
BxBB` , QP{4F]~՟   T 0' 
`  +N te
 SR؆6   ʅ b ߆6 H0 
  85 a  xB 10ɽ  IPȐ1 @	pb Ҽ  GYy    a } 0 5 a :T1
񁯢`
a
 }/PBp o8y )[h Gӄ & q{{   Ø =e
Gc+ 
  5 S! F Bp    ) ػ )& d   c3  ()  u ց.r
  R.p!) 
R  @+     =  	T@ Y 0L!
*P   hJ[Ӛ洧 	jQ  
^h ZPi"P 
  6 i*lw T>ɜ   : '&v %|'#  Xt  k 9$zэ~t '] Kg    t G] 2    ^    js˚ 3  r k*  מ K  L N  :+ H   `  	!   i3	 OQ  @!PO B @  `U0B v   e  goIO 2$ A `@gO  K. A	o( K P   
/x  K  ;  X  ?\ fRҗ @%9'˞z  
 p   n  [)Y c\ #(         @v &ܻZ_r % ށk0 'Y    ^   y  
 #7  
D   e̖A go      ! : ρn   ,s IPz׆qBb`ɞ dK: ,+dElw   <(  N p  ` !  U  2 '  {  g\ '   $y# ! F    Q	 s^ ?Y       (=} W  [   q       = 	  !> C ډ ٫=   _
 (04 
3 > +  < K X   Ȼ ! (   s KH? c? y?ы     S  ?8`  ȿ6 ?  ?  =    <    {  Ӊ2@qZ qB\ - Ɋ3 A7 @
 @ 2; -   <!  *  ȸ 3< #@  x   :@'4 
,=        |=  
B  = 3   R  B   9@)0{+ ,; @ > r'   2d/ z  C6S"xC   ; ;`    @ N3
 k6 [   30б <y   þ6l  Ӿ  )H    : RT `  & 6i
 j{1  ;   K 1K7 R5q۷r5 y 5   b   X:k/q, aܱ> 6@ 6B  }d  3s45uc u$7xsGx     GP  )   3 c8    e<	  H sF Iy  ˻x* ~4  %h#p  %  { | '   l9 F# 2  p @Kj ܚ 
z4ȯ- 	  ȹc  \?   9ԋ: C  C>    ` 6   9AQ 8H:/`     q  YJ%  \   ò  A <T =d .p    |      K   9 ?   ,
 I>l 2   ̚ - i J&ʯ2K5h  bX ݃= cM״ Z 8 D2S ;    h 3LU 3 ; 4Šb   <  J   Q NT  {   N ;  E d8e G ɢ{;  '}
/  Gy@  K9M;$<TK lKAyK̔ː ̻   7p  x@[  5  8 Y$q  n  &  7  /y8   ,c
dtO8E 4 # Qx O%      t) 7 2Ћ  @ J <        <KMK \K   ` ̺| }
   P
  D& 0 PQ: =
KQ     Ѡ Q ,   @g O' ƹ >        I!  y8 ɠ O 
OȐ    s?  ? D D  
S:     
4
h Gu L  8@ & D   B J P 4P*A 򋥻. ̺ d )  =   TC STdIBuV  QD-!]T  0S$  dI7 U  CN AO  U	
S
  2 Рp՘q 
H  YU  ̊ Tc Uj-W   Ɯ ǌ _
V  5   deI P  <E t  m/t#  g   r<k=֙  `{* ɷ+I
 %- I  g   
 ћ O]כ    
  - l K 28 7  Ʊ ;H 7 E: W [Vyh ;  <eIm ؓ\ +P   `Yc$Y =T M x   (p   Y%u 0  -(Y8 s Y? A      7  ̊#E 7p4ȋ Z  Z U? X ; s L hdI J[  ɵ V< |   \LR* J  Ƭ Ys  >    ! J `   9`        Ɋ x\e [ \ B 1FN̯f h4 J!   Y  U       ] ^
^ M  y R^  \ | ʝY   حVэXd  " 8!pY( Y u] e E  مA  ]PM )   㽨 yS    M  p  	
7  
F. ʑ!3   ? N u   _   >	>M[D  ;iuޓ ߔ  } 8 
  ׺X |  u!X a< #  4  5"`#     @ Y k 9 M a   |S M   `    *  3[M |ӗI Ȋ    ]E p
 U $x08 ǰc(` S      D^ F   2e] L  
 8 0  M #0 ' , `  3Pz 9 eb c V<dSTeV WVX~ !  '  X 'x @  8UU0 #  <  P Q_n :xe3 fcV  )`fg    i  j ay P Xv> P QR~S 	T fG5    eƳ  e 4^  U ډNo> dfr~ sejfIy> TN }f b^ ) =0)
 ͈6 hF fg  ߮D _^h q<}Ү.    fi fI [w ex [Bf   i'   @  a  Γ   5C4TC c 2]s  \   =    t 1   +:7 Ꟶ:c:  Ɛ :  ح  Afjy  ] N Yu
 x  Fsk { ѝ  <F   7c ]6 Ã   EZ E5 E]^,rd ` <# :uE ė {V lƵ - [   i
    (о     ^ <	2 dvVk <   |l  m  m  m ^  (   6, + I0( /  R#hv   n  =       6  0#  : ]   f p t̷. 7Վۣ; N  * 9   T\       "f xB Pn!f ﱴ  .   4LJ  & d mgRyh    .         ^ 	 ov&    F  ַV o & 5  .7l $ 6Tg ́ V 7osJ ~ mo!U  	5p !+ M o v  n54 *0 " S #ꪽ[ <<87? (  󠖇  Z< !  {    ~  nHk  6HgCH.8 ScHV{ `2 w G[   F = ke/nHA p\ LHt tws K  x Hzd Z!C  Ƚ km G  _W \<  ֶ , ul   9 ƹ  ݟk;  i  3 s  ]  ʉB  ޹    b     V ( d jP5k  9  P} Pwx 	q5v.s1    <  60wt  *Pqv j {wf wg Hx    ` >7J "	~   p  q'x ' '.0    R_ k  p N ;.   t V  L   1   m  y   / sGy   7 4     x?z x ?x<3 80 oyo4uF ; H  yRc b L  NÒ*/
. 7.f    & n qh v8 xk n W0  ~ v    {; 9@   z L Wtp,{     '6 )~_r *|   7  Gz w|`}  D ̏yϟy g v Ul f  p  L  ˧ B   |R3 `c  ԯ տzrw|     	r%{\_  L7 v~%  v y  ~?y  ~:   9ae    .}l? &E C hK:
  >     ě"oݵ  gs,   [/'| K <  X   )p y5      !  q <  ꃁ   >   ̝=o  o  F  S{,	
 N  ^  kz`Bb  w   c = 	   `ؙ    / %@     z`  ; H   ER{  - = ו  / {t  隻     p   Y       v2   .  ^   F  I  b  ٳ}  
 .H X
   O  X Y  }g ԥ S'    k     9  :!  Q 
R !  p_    3 6 >   ; ʿ   T 
  d& ؄ $ T  "C   3dg    ? T     ɍ  ~     n2g  tR@ A t \  :|O&   
    v P ݉1C    G]P
2¬G @  B '/  I  [  v&   0āT0   +0     XQ  `
]* |
퍀  &  cg~`$RÒ [JD\   RXb  E!*&f ڷENNl5@'   h N    P   X  BR\ Y ) D   * | *ހ Hjvڞ  I 3   _5Tz<p"     F   -ʼ  > Ӌ$q
  H ` . )    [$;      ct|<P2) r  2 /  ` S 
7 T V 3  j& b <P,F ʧY#̹ 2q  F׏n [  n iE d  t   !~EQ    IP m f =
p {Nc CD61 =t   ;K  N Ϣ ,p]@ S   D    pB~h bP ~LO  0P  FD@
   h >cCD    A 9  "S  ^A*   k  1.v  X + u    @ !  Xw" 5F ! t"
  u Y E     1F 
  d | $2& U   ,  HZ  !  t! ,G X U
8 <  v 
  ! tzJ A H/9  k  QG H   R[ EB~i<  j@y k 1@^2z   {dgBP>ç$ !}lvQ D w; B4      4 "   ̒n    !ä dIS2@ZHPh
d   /+N Hy  g vC VI59*_d   H 'X  b# $  yw L 3    &D  2 I0a*      Y g	     I  Z9 n \ CpWn@*   4  rIKȗ,  , 9rG:ǹH ġ  N  S  ?	  3   J   \tS  2H  x %  
      X ߤbIb Z  9,  L   !@ e <  O* ^ (   'FjI	 G  rY L(   %^< r L{    pF _>I    e ĘӒ y  i$ 幤 , e L  $ d <  e^ɟi* f  u j M /[e؜   lfK95     kSk  <    ,    >]   E FT6   p f;H Mׅ ϨfF UST Je)E   b F穵  9? lp-k~  8 c  @S` u.v 鮑  F~&6"Ǿ  TݤSG   
L hͦ u  Ѝ.Qg<g lL  3 )   n  C  N"ɺ  &Ƙ ;
 x $v    @))=A O    bB  U7-F  c1@ 8B  	V   kP.      4B~
l=   `    %  g f̊7 - 9    c5TP9L 6h    )  \ 4o#;C   q 9`7     % P 
 h  +1  ?? w >c5m<    ʱ   i0 H  $ A   < 102 R(E M XԂ 	)  	=	H4   %  N Q e M 	3(EY uв y )P K # I  LQ /`  `EJ >
 ha   cx ~ p  E(J0  ?0 GxS0   nX  І[C  ` ;  \k 9 rcD   >& 1:   N vgv] $ll 3"9:   #   
NN f9oQ  l      h  ah  qHv,N EX  Ӝ E58 R  
) 8 vT>  ̫ Б : k =F  D  h &D{*>ҫӧ n Oc  .E)YM M xW)= X+        L);   }z$e XRI%  c ,JM K
$~ G "    -5? q CL  Ť & 
  *vŲ  _,  z  ޢ > =%M @      
A Z SE %cdBU q  H2  sp       ˂, g 
ݲI  J{ 
   Um  2 .ˬ I  F  Y L U   n    !!7m   uf   p  J  *>  v U  d @S Б(%B    YL h U Y ĊY l- 0M4 P n i     
  4 o iS  A vXMZq  m֛ Y  -U P z  8&9ܮ ǩL   PRC@  >` (j/    R   JL˚1 5 Uv # Z X 9  ,i1 Y*  ;[9 q ZTwg_3*~ u zԴ tbgJ  ֙ B9 ! Ħ   Y6 ً:'8%  М>Jd   l២Cw:P  f  uK$Ԇd '  H    + ;͌k  >  TO5 =mu  |Λ  Y  <  %I t
   $cwɨ@ UG z8M g Ê 1  W    e q    GMoT L<vo F ?   PW  O 3٭B|v  6 f     6 . F h @ թ  ,$B  ? * c  ƩjTa*r B^ D 腥1  GJ
 E  EZ0PjY% j  t ; h  "
* 	 W $Q K    v WKa jN     ֎  8И  `   : ]a=    [C l?  $ M '  $+V@   KU  Z0k m趴    [Knu  `   v˼ -mC d Ӿ ' T     e\M   oY ŶW    {  
  I ~  4. aYwϵ'BKq ] Ј 2   oWm  {ٶc Z  mWnJ j h9,  |T   b. j xkq -    O Z[ K@ * C I   p{ \  -    Y ,IQ Ps   5b{u B U @   `#8 e_     ܣ;pS e j׮1s n      O{  .ȭ    " ` xͮ E M7  \3F   |8   Öw_e ż=w
8 t˒ ץ   ޱk	  e L  >ާK  Rhr  u *  JMq  չ    ]h4i   
  W   |-% % K7 [  p
V c  ā b[}= %N |^| {C н bW  YKzm  }  C_M |( gtr    \  wMW H  l.        _  xͯ酼 | . 绢 u%q U &X 1 ؙ        
<Dp %  7 ); ~ hM    j ; u       .  f  ܓ  X<k   ت`      C~Ic  <" !w   +w- L  f   he % - Ē&MJ   X 2  
 w w  L  K g  (!?lI a9~  8 ) z8k  7     O`    1    a  ޲ %q 	M   N K    U       M
     @[cO(Ĭ  D,U1vĵ8(Db    .(/0	Md ؙ U  Kz       ^Ӧd ;Gh   nro   M Y    - 
 x;^m 	     ѻfah    Ψ O ȉ# ' | H a@|  ٸ    B㓫 G     5 ;> q  ĺe  v  p0  x  Q\ 52t$ G { 
  .I&& * d ,  q. ş  k h  $ O   ";?|  4 T 'b 1Q6ʮ'%c I    7n  &75_\H # >0        
0 G f     *   L  SvE":ZS_Yb'  D  T >: i A;#m   j  B  eghV Ŷx   n"ø         P ^
a#`_昰x W   5l2u8& y '    i F@  2 }psE  7     ka   q: ( F0U0   <}toY W M  s N r Z ,  #( 
| *% d  ذ  <  x LҮZ 
?+ Q    bN    i͘4 RX G t2  	 & 
l  M 
 Wg  g lKP]y 5v E  [C  [	U  M\     ;  G !ɬM1g.J  3 ܆; x5FVƏ  	P    1ڤ !u @ Q'  g  -  ZZ<Xzv(- b 2I 3
           u N i<      } O i@   &ԅ PjD   J   A ԍ  )p c;$5L   C    *   |    WդzT  S] Q  fի U jX  eu  մzV  [] q   ջ W j`   u   zX  c]    f   Y+kh   u    zZ? k]        [kkp   u   z\ s] ѵ ^"`ŉ i          ^ k|         _ k   e  1X
K   C  kd    ^  zE[ = * KD  !   mI @,g ԍEұ#  &d#   =RoVp  8ԃ0  6 - Ĵ 
B Ex-t 4'9  :f-1^UH    ;~֧y3   h  A I PC  j{ !>  24/} Y     A g $ +E  B K Y   -M0ɫ  5z | pFBL ; Y~zQ7   
       bD Ը*  ro  r  u F    lQE d8 !  ,&   1 l @ڀ N   n  
Z    ;n`l    F h$ :7 쒽N  ^$ [T i  h    [u  \pp     z; Px   +vD  ~A `(M   r   @  8      fޞ p       f -5D G XŎ MA1  p   :   @        (  A        2   
   b  w         kՇO ^h     [ m9 & 
Y F  J  #                          

FabriqueClient.wdc
PCS                     B      9       j                  01F280051n                 /     %  H<    ;        m5        "       
      F a b r i q u e C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                   9                         01F280051n     p       %      >        
                                                                        Έ    FabriqueClient                                    /  *Z   } $n U ' g H C    9 *  o 5 z s  1 ǣ         (    
dP$ @ z( $)څ  b 
Y `1@E "  ,    3G ;f  Qc M f    L  b  1  L <.H )S &      7;    DL  5oҙb   8u ؙ GF *}j1  U f  Um*pȰ1S  U;ߎ  #U ! J## & k    z j 
X0 :B  TCGM5X݆`  d!D XQBEI%P h>ʹs   ,TE  1m  {  <U)3G'Q%Qr A& 5>g   c 
7r  ?F s # !  6  3  
 :"  9{ 7  I=bTyЍ
 6 4 :    :   5ژM <  :" Fl 7
S  gpѺ 4h I,무 j -    E$Q5 \  e    b    #   0 7|`  '_Lο  |c˺
tS  :  d ʧ< 4 B	C  
/    R    q/@ 4q  Dk 9Ԥ  T q ?  :NLG   GG    ި+*Q M   ΓP  ֛Tm 	  (æ:|  S   `XS 3    7 
p[P
w\r 5  s?    
@݋ uw 8  { (_~  w ~ W_ 
&  8ᄟj``   r^ C  b 3n "
 Y cv
  :    ]y = ݐݭ7ߧf a #  dtafʀ "Jn     	" N@     Y 9 p 8H     ~ "	       :  V;  h> 扭֘   h "FK 28E   ! .ȗ P  A Yq   C 
 `맼   oNS\  }\ ȕ  g **#     G v f7߰    V ] Svb > m   r  m \1z H  y  t ƌM  j  $  ? s  ) _F D x^ _ X '|     emkk w  ]lZIN"R  oJBZ   ! Z   
 q    c  ]   \    M -   RN .E   >   qz    P  
 w   R # lxQ<  "   0  [\ 3=s  }      B     1 
 +c ¡ 
 y:  ?  -"h ۲hE.z bnK"B Ȅ& B    A    <   p  ^ (-Ɵ  !  S    <':  dX   $    j2    )L ̃   ; ly   05  
 %G (  R.     Kf C  , FzA.s՚נI;  H  /) 	p@ 0 \ c QR0!  9f R޹+ PA
hxCtF0 _= $\Ў P   3  h   2衢nxILf Ĥ-!  D)  Ґt6L        D l ) :    AG  N  o  N   8 j  T pR%  pH QQL r ;C1F  Qy  O} P 4xGe cH  	#`P P `  }*W z  l@;S0   -a
!  c     t  ڠK  
  lr 8} ӟ  jP *T0  RjS )  3n    D2B  I'y  t -  [`ʧ   9%.   |* u C    /0 ޠ,_h:  I^3  
A Jt jTb&ժ <	
     T%Qk    w \  W *V   0h
 Z  V    
P e- Y͆@ !  8GNѶ! $ i 9 2  烵  ~ 3 -  Ѕ ֡8VC T    , BG R&;+'  J  R T       d r      ;   , U  ` *  
b1 |g    Q X `#  l .  8ؚ   ݬ  b'   aq6'  P(B ¦   zA	O  "  p Cv   *R׻  W   N D  :9E }    b  Eg  e â  g j+   v    l  F$y  s [  .W q ç o) <   B BЊ}07'   P,9 vB   /O.%   R  nFJ  x e N !؁
     V2!A $ J
  fl.   U   -DB 0!4 @     + s\I)  y
  >u X5v 2  L   ^m w J
 >    , /S    6eS      痈@  ]-J   Ԩ j5  x  # X B  m   $7 RuO  X L 
  u 9 =q   y% _\  5 I p ? 9 o :W ꮗ ww  !  F u   
M n|_R^ 0   f  
[J  YnX  Y    7    y     y5 ^ S 4  Zܿa XC    j 0z  ^o  䯃 
-   vZ  k< r/  	
 |  2 * # -- DL { #=PI '   ڼ R  { +   $ > C  h=; ؋  =vʥ ʽ  =L  
> +>L>\      " 髾% > L ɘ  ȿ<     ? ҫ28  ˻ r ԃ?( 
   ԫ  ?! ?   8<  r@ Ê  @\  >     -" -$ۭ   8D  0   {) k
λK|A   ӛB ˥    BI : A  ) 2c1"$  ;  >  3 =&tE  {B      K *\ _ľVd  B P
. (  / Ad   3  3, 4\  k?x  I 9 ? EG 4c1  1H W"'zJD"`  2 98 :   &  ë    e)   ;A0 F) -# $  " 2  70#   4  4D,  ? HL  T[   +  X  Zk
J Iv I  5_ !`
"a "b3  A  X   f  h h  ic"n p ʬ Jp J  J  J+      q
J lˊ  TP 6p1  O # 	 o #  0(>z 0 1   y z  { B2  Ѣ_0  Z L
 yI 
   s    0 X r    L  L 9K   \  L  Y(  8 :"hH VȪ  )H7 Y  ķ 7  4 )h oD"          L -2  \  t  a͈H  ̗	 "s   β      >ꂠ r"   	        (  A        2   
   D   l V  ū 
d ͛  5 R f0 -  V A   G   5                         

FabriqueDeSignal.wdc
PCS                     B      9       j                  01F280051n                  W 
 j m I  V p    q   =f       "      
	      F a b r i q u e D e S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS                              9                         01F280051n     r       i       >        
                                                                        Έ    FabriqueDeSignal                                    /   . _r. %   t9i ˕Y ^f ) ] a     +  	P |      Z        
d 
 @<R6,  ҅  b 
j,`1@E    ,    3G ;f     1mB  S'  7fBi# 3   $  ӑP  4)/B 3j (e T E_Ŏ%  CD  J={  گW   J nD R *#     
 bE
  .N;7bز  On     iO(  ػb :~ w `Ω ~  kĒ'-   H%P pm   3c 4  i  8  \9> E  A"
 < 	b A D y(D Aƙ }` ?    >@ D  ) OH ?  9   )@    >+ /   ت  "" x  9   3옃7q   8`ȡ
 b 4-D$ N . N:! 8怃bT)CC "   
/ p T 0  ZTp-     **# )Q (   G  
L .	Ȣ
  2[ 33/K 3 ^  J 
lӴ P[R 6 jn (8i @ "      >     B     1!  | HA  	J  3 B2(N  ?    ?q  7=   ;k   O    ( C
9d   `ć  N , E & 5FyL  Fu 1 ^  u " $KɯȵU &  2 *ٍ  ,  , /c
   ( y1;  5 ]- <#  /ϝ ` %AE JE    '  + ( -2( ! 8 4  8 \:Y ( 89 EF *U b   a s YV @ D p3̨b1   
3ި   r 
    
 5
     \
 1     w$: 8    b
 6 #  6i  ` i qj  vA  s::饛 Iܱ̝ 숀d o w     X  }*C ܈[(Bȩ     w:\V  L ""  ;    -  q Œ g  J   R  HK. ~
L1 - ߑ        ,e ^      LQ hN   gS ʂ   I  %C kN.2п  I <     
    z<P   pfv  qg   "  	hU  @  7#  hhP 0 8 NXZ  r   Dt1aD   0ψn ~ p l M(m{   V   !oE  k >- 9!  7 6t 4 a W $  
 3 0 2h ' S#    tVL   кס  _$Z h ۍK? 
   +@A
F B fG< 1 bQ^  x v  M 
   U/݋    R % o  #X	ӷ Qf N     JT    0jT *H  gV .1 T5G lz*" Zf, S}p?    3KY  L 
OR  Wt 
M (,@       dY/    6& ZN bڶEE  D(t ކ  }  se CQN% Uq @  ~ A   ZN*A  t
A!  :  Tp7}dS" IFĒ  $'	S    /j )ו  ҕ]    
 M T .  K^  	  Y ٘` +و  ` q  I   tC    @RᇿZ   TR  Ă$  :Cx  sNlm ` )| 9!   9a 2Dȁ.  @#k  F  5    U BE   (XaMP  `   	P    | a m( i HR%  'uP  z5   A 9y 3       v      mI: Ru?=dP ۓ Η   CQ     J d&7 I X  Kg T S  ; `  G  ED n      5AfEZ͗> Q    nϥ(( }OQ 7 S CS ̤fs   k3" \f  dq  > $ lYv '    <k   YD  #C   7 PC   1 9 V   w~ A l  ?L P   ! RG    9 y q~ ݕ ; y+   ޤ`yU ܲZ L / x   ` T  /zPP@         (  A        2   
   װ  e  "      m 
U  |B  <3M c7 $  o H   x LU                          

GestionDesCalculs.wdc
PCS                     B      9       j                  01F280051n                  2z  w  ?  	ڕ  9gd0w b   F      "      .
      G e s t i o n D e s C a l c u l s                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          a                   9                         01F280051n     s       ,      >        
   
`                                                                 Έ    GestionDesCalculs                                    /  1<R+CAd WIK   :  `     >  z vT o &  0 ϭ(      '    
d N @4%  ԅ  b 
*8`1 ƈ"  , <   SG
 7EXӆ  6tB    3!  C      e   @ &塈    9j U   ˘3k ̙tiӧ  Q 
7U  1#  Ζ!Ȥ  Y iՖ<ɢ  9t  t# Mb    b8m" y `   E xƯg #S f  ㇈ A    m OI	 w   C ݀ GUs/o   Ǌ- ]}6pլ	<gn c   es     i  ~ |   -: q   -D<b 	 *   	   5   36!c 0~Y'    p 
  sj 8 0 # q     ,2 'Nz      b
 { q   CN9      :h "`ji;  ʎ9
c  :aȡ
~C =    ޮ o  D;! 8怃  ʠ2 & x2 ) HA V    P"" 
   ӣ"
  @
  $X 7j  6Eq9  sJ  ҋȽ   9Q p G, D#qx
 T 遌$-9`    C
  p p   s< G 4u TU]u  <)  d SJ3 ,p,# dˇ~ vQ 0B  ?# 踵 1 M7 TEN B (ɥr >S˗   P 7j*"2   * ފk    S  @ w[ E PE 6 F z4҈&=  @# t  6].g  
  E[ ( V % 9 S{5 d5ĥ pzi:Yb ( [  D &    He e$ p @
!    
ǈ@  Gk = ȣ
R4 5     9, C+ 
þ >a ˘ME #3

  .G34   B   T1 
 a
    
 
 Ӏ  p⚪     ଎C   Wcq `JXd l( )   E  b
#Bx  
G\    3 . l  @֮ |ǈ 	8 @   겣R  /b (u   F` @ [^ B 8P)m
  7  @
e 	d0  o *  Yާѐ " C   B  `#P` 0 :<O0 BU2 v,>
 hV̀uH  .( 7      u  |  =   7    F   X    P J, FQ  b   O iBUv 6a   
9f , wmp c ` 6$ a <Vh   1u      1d; yVwŧ  m A   D  g  J   cM p;5 F ?    EVr %
     .F Cdn  B  d 1 p  Dr d Iư@6H "\'1  =    4^,U L? O4 ] p 8 +!   Ԁ˾% 6 "     
R 
  E3  s<   <U  eTH  qHzR  lh+3 B  R  45aH   5b  
 B  6 m  >  v 
 nwHބd    9  w P  $ | k Y`  %  
 <r9
  O    % (  t K   ; ]    J/  D8B xWU   Ǽƹo R   w  m {!    @   | ÌfT  ZddГJ W   !  C  '    NgLce  @ :   
 n  l I  , $ @+ Z     }A. N ˆMҗ f6  1d
v V O  &
   I*UA
   u  ht 6U nv oh  P\C "ӧT 9 = 
PЂ  +  R]COf   ,e    (jQ!aT0  n>    6!]qJ f   )fuJT9< /' x 2 a9C  S     p@g^ "  mH   & 1թ      >   ~     OI3  z L1b  2  O		Y+ 7  KgEs     u  +W_;1̮v|͝_
 (  
{ 3]6 />9 z   B0 ߕZ   5   D  j  1   k G k>[6`
< n#8 	V q p= 8tp  # Im [c1 t6Y 4WC Q Ԧ*u)    2NM A R  T:      \ G GR= Ԧ   Cz   Æ D !Є <b $ Uw
<Bp / nc~J > 3  Y @  f ř QM9 c  	 { O   W   Z G        (  A        2   
       YA "@dl _ 4l=  ('A  b    r4   t pq                                      O   O    Έ K   C:\Mes Projets\repo\avsp_server\src\TEST_GestionDesCalculs.wxt    

IAcquisitionSignal.wdc
PCS                     B      9       j                  01F280051n                   &  j@2 >. eT  6!2K;  ӗ  /     "              I A c q u i s i t i o n S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          n                   9                         01F280051n     t              >        
                                                                        Έ    IAcquisitionSignal                                    / =    p   i  X    V   *   M'Ҧf *  vO R            
d   @h Xe     b 
  `1@E "  ,   !#ǎ:jꨁ 4oĴ	a N vޘ	A  :t   . 8 Q  4) DDi   #   j̼ C6f 0u
U G '}`=    !vΜ13  ϠCCԽ Q*u )"  0,]I 
ۧo <  +f   r w+(O <~R H  {̀qB	 hՂ  %2)VB A# qТG  r: j! _Y
汒&M  2 
 * C   "E
*X  =    i   -  3 'W   " 벦2돀 F    [   "k饘f  /  " 9   ; X   D o  0 ,   )C  HC5 #   S  j A z1   ˯  AGIC+% 8, k  BH2 %IL D    
0 p  H, ı˶ $ ǁ  )  ( l +   M&5    ګ/0b  J@  rK8
ܑ4*  C 1  /5 a 1 Î  ȃ  `SC6 l  ,   -86 r B    :U  g qC  @ X 
 0CF  %JFm Q ^  /   P n B0At Uw]v   " >jw w/ wވ 
@$   7"x .  V8a 
nX    : 8*/ x *  } 9ޅ =    ^vU  d|  X* ߊx ?Cߙ :@  H   # x<%R  { =cg    Hs " zk     + zg  J        qi?@J    ߲q@   d   㬭  (  ` C  HA r B 1V"  ,    @ { "
    :^%  _"rY ygg9f iF g    3W w 7>A  ƺ-   :     x ;0  "   :"_  Cq    ^o , o & 4
?   a{  q  -  P ds  D  t9] " :  N9   V . ] b2      H/  a	M8  U j Z   хPg'L  H     0x ˧ 6
  B   P   =0w . g& d9U   
ŉ(    څ       L 2 	Nt҆8 1   d(T  PnY;  	0   " L T ^ 
  	 ;zYf   $  AG>L4   i( O 2s)< 
Q < y1 (a j ; T_ &
   E @
  āp   #  Ue
V   E x (Ңe 2    v\ en    Q1s 7+d     % # < Ё T [   @2   ?5 I 8 xJd C9PW& ]   )A JU  wUhTlXD 
4j ] IƥU$  D( A   11 	c %# \b +f (М 4C Q xT    
  +[  D a;	 N 3  D "  F4   %  ϋ - h')JC VT  a C[  U -" \ + PW  uyO h\yׇfՃ# >     a
 @   B _ ! xב`V6*           (  A        2   
   d  a  &      A q     9    GS  [ D   D  !                          

IClientSocket.wdc
PCS                     B      9       j                  01F280051n                   '   . Z  B>+  q ֜  C   ~J     "             I C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          i                   9                         01F280051n     o       S
      >        
                                                                        Έ    IClientSocket                                    / / G  O   Á   !    Z| { I ,  ~  L  V?    ܭ7l    D
   6    
d   @P  (ծ҅  b 
)
 `1@E "  , < i  ̛:T ac N3tn yc&K1t 	  ]&q $iR  
Ԕ  'L s M  cӓ)   FL 4mV  3 4b  ܊  ג'OD gN̙5o l{ Nֺ] 2 +/DT5q؆   b      SQ  qS% 3b    r  \=> "j3s duB   7w   ɺ ۸s rV  < s    3d L>~Yy   ? 
~ s ę  4f  >߸ETb栮SČ    F  a8 hc        ʈB;  @ò
  dpй @  
;  c2 b    " 茹F    r qGw  " > 1 /2rH dH Ř I(# rI)    '  K   " CrL  $ L+  } t3H,qON4 +R #{ 3H8 Tr˻   K.#
3  D ̥ 1  ,  +   
% x"1   ECuԥ   1G#b V[eu6B <RG j  4  8  i; x   c 9 ! .p VY O<J4   k  f b}   :  bSH  9s|  v   N   z  xG+ B   4 T[    S  X 7ߥ uG   Z * w   (   G  c
 I	fs    tI    ˥ ٳX  k' Qg   NSQ hUW[   ~ 5  :    l     z   Jf:    z$Qs^ l <z  : ԩ"   ` - +JIv a } I 
Ȃ/@ ڢ  |΄  %  pC  ܼѝC]    D F :E3 (b@ ը# PTq] `-"* @ì6NW%  w  
a    	8Јo   
  RA ݁U=  ] )- Z   }!Bh  < o F y  b 4  c AHd #2Y˔ݎ    OC`    i fXJ @Ԧ  /P `yT4  j HK@ V    i= y  j-1Nz G"H¥ n  TD  !K
 2 8@ b Y  0 &U-"F m䁃ȵgr  jBp%L 
V0B ` ܰ9<  e %hA h      " 	 pr  uC ~  2   *X  +bѡs_ `H,6   c [     K k   v$  e$    Y   S#= @WƒT<   W ll  .q  @ B؏M  7 S  I' 1 !
     l$ #Lt\c  -)RΊX "  a ? 	 θ 4* AoX E>w Ɣ` /q `>   P=   A2~   , I 	e* d F   N  $*A U i  e/Mz&Z ÖL  =F   t)4t c2 b p  l©S01! "
 i0, n f Epz      b 3  
> )jt    tu kלc      dg2  3`(u" R ۝ 	p( ]º? t 7  o4 St <2  { K ȶ  z _ Ԇ   ? ]  DE
V   , Q   L !  y ˍ I  t kBa i <  q8L   3 !   rB 1 G   I  W  UC  О8d   ]ns    Nw?  z {  pRGP¦  ]y GL % <  k  u ]     Bޓ  B- 蠂 	P |  < ~9 ކ O L ?  o 
|'(` _ i`l 9  $+ Od/ !w `  9  >|by F   #`iU9b ABT   wb  F0>0  q   $Y g+ ELbk s I	x z 9 A e s U (=  )  1    :G   SBɋ4 Y&G9 S    E7 J ڣ Z  U  U   ] eL]YRJ Z  K J  K  ~x  :: E k`c Q?AcO  4=  BL    am3  !'Z    4     P 6_ - $ S  Oȹ .~1    N{ 0 ۘ} =I    5 yp  3 
o   m 2   ` c : 9&  Z  9  Б c  u" z>   e OX | k 5    ,P  rS   Kזƴ 4 RN Ϛ ' - O  !  8zuY z / %Bw}vE  h ƥK & V@ e:
    O { \Imۍ    #~ 
cU  R7%    9P
     a   {,7xԼ3 kl   ^V
2} O _ :   أ6u Ay 6փ u I v] x6jW ;    Z׀ @D, N9   P    I< ݺ  +ΩZ1 /W } |w   ' l   ˂  C  P Kݟ :E9 u>  +%  =X
  Q  > 
    k)a   5 ӕ  63+NI  C  ۿBQ7G > z7': 
?  !  * !P *@' 6 K  C=	 ?I  M ?O    :  ?  =  : {) Z ; 3  BU9 Z;  6   4 >a"  NჿQ L7  v;A k ! "      ʪ 
#2 87 
I  P ћ ۂ#     -  1 4 2@     $/ 
 p	 a ?DܳЉ  
  
  '3 QĹ} #b  . C9BS  i Q,EF 5 4  :!    P =%$    U B   ԳBl>, %- .   B
      2CcA I eXãj     <'y  Cz  {3'}k  B G< 3ȍC$8y ÁH	ȁ ' sd    +;   ?F{Җ  0 B
bѝ 	 +1( ح 	I 	 >  ⺝   9 / .  .  Hx;  Q R  r+U 9 S( / 6ث    D "  j 3   J'ڀ  #W     ?i D H H     , 3CI<[ɖD  xG DI  : D   2  N  PH <- JDفT    
  (˴Jy8   2   #   ?  FEF + ֻ  [ d,B ú$d %@4  #4 W Fܜƙ =i1)\ӵ  i ;  | U8   \ ԑ \     ά۠mT n   @@   #Gy ! 5h|1 -  $  ; _@   gc<   9 !z Lk  :   L 2 ;  H x  ` K   LГ8'@" am #  H  P/! 8  `ځ  PL    J'(
 - 4,   >  Վ7@QD< /XĄs 9 w UO a	 Q     1 R 
 %++-  ,!%MӔ  ?  ?M# ;$ gԓ8    M; 5p B \@   
 N_ ¶ S     ,CP$   P f[<
| D A?   x [    ;08  [T 8    Y    p< .S  	 Ʃ      '  5  U𬤥p           (  A        2   
   N;x > ĉ     j  zw>QL o  Șp:  P  7H[ V  ?                         

IConnexionClientSocket.wdc
PCS                     B      9       j                  01F280051n                 Pθ -/Z88  gC wý  R   
       "              I C o n n e x i o n C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          a                   9                         01F280051n     x              >        
                                                                        Έ #   IConnexionClientSocket                                    / :2A z ,  :%S 0\
imT
.   m  % ._    + U"               
d0j  .2$@ ҅  b 
*8`1 ƈ"  ,     捙<jZi  ̛:T ac N3t~ y  :t   . 8 Q  4)oDDi  QS L  5o  ٳS P z z2 'p      D  I# ث h  e[  B  	   Y*[ l ; (_   6Lx ay" Q F Z  f ڵ A |1 Eܵ  (ҳ     $ Aq   *   5p +V  O u  G ^   ף    "o   = [      _/   3A[ x  " P 'Nc      H  "AÕ1(     tp  1 B AC=   B! č ōTl/ 8Ԁ  hHE      # LUd F " B  \     "  j;  ʎ9c  4`ȡ
   =    
߲ 2= @;! 8 ؉U   ,    9RP ֺ  7%"2@  ʈHR6=J  @  ANր  6     C ɨ 3    \ <V	    #
a c6 h ^S ĵB a2 u H By Yez  j  FG gȪ =-I  5 Yg5w  <)    . 4L  Ӆ
 | t+  3  X:ے < $ O?
 ]A  PDYS \ ht G-   Hǽ BMM
 S @MMTRsK 5 J5oՕǵ    ¸ Z׺  \ 
 `7 h\? (   i Udq Pp  = 1   פ
r["-:\    I w^ FЮH 5  + l   W  @ 4#  "0 ŷ ( "  " =  31  R     n   )2< ), ɪ 2 xC7Ԑ 3T a8 h ϛ*N-q xw;    : A u K  OC hԁb  ;T  Y J{| roM{ v  "}d  
|p `  l a  TT &  RQG	^*q  # x ,   mT 0C 
e  T A7FA M`S(B   	(   1 ! _|b% &   W@ 0   <  7  d 
 P  @ n --h   d  y <p ʚ@; !Ojp  6  ^ p  Y     c0-@na s  @ *LAJ  B E-r  _ \ J B  ,!H  Z   ͮv SE( H U  E '2L)o{ i^   H d s  l W Pl &    9hj  G8H  h h-qH /  ,Mk 	x  g b  O "
q DT -n  [gC kE[6  ' W  `2dq+^ {y   |  0R
6.c  J Ȱ U r 
 ;  y&  
 BP ʵ b # B  < . d  ` ;  N**    O m   ?=    | kx J 4{  %ۖ  [ & +   n 
-   rjS  \lA Ǵ 2Cą RUZVEe ְ  lFZb9 ʽZ" 
Z B0  5kJ^  4 Fk ᠀  W  (0,c 
$! u   9svT >'" 	*ϤR~  "ȳ          (  A        2   
      l}Cη   䎦.?   ɚZ W   J  u b     |                         

IGestionDesCalculs.wdc
PCS                     B      9       j                  01F280051n                   j	Y
y   K % I Az 
cL      "      z       I G e s t i o n D e s C a l c u l s                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          a                   9                         01F280051n     t              >        
                                                                        Έ    IGestionDesCalculs                                    /`+ z T=S v cR*  W    z -   Y   (Q
  ) `o b             
d     贅ׅ  b 
*8`1 ƈ"  ,    1C  8o  $" 
;m hY'  7fBi# 3   $  Ӂ$M SQ 7qژqc M J   cG
5/cF   R N z zr U5ex     W5f  "ܦs <	 * mƈ!æJ 9f    Wh  d  *c'3b   2 ! 5oP I  m ?D	2w      z H  (gҹ  =ʰJ :n  =V hAo ׾M@    kO   E  K  8T   o1;Ԏ:#"  +   
% x5j)   R
      \ż z !       ) 8   K"ͭ J ,  RP!  zI  l q  B  ;  C0 ;\*1# Co=   R<    B   >    J  T
  p #6     Q4 L  +l2ô |J   j  T   v ˇ> 2 H ^D    .       
` K S3UV[m Xs "D  V[m#SLSQu R2    hv  ` 
 c U   &
  v   ߢi d U*֩f  V\oՕԁz 5U2<I    ɥ9   Ie     ć kW  ,h  |%x l-G=    z  s)mU %"2`  ʈHd -J  @ $2ARN۔ P -  4K > oԒK=s] h 6 [ -<   Y\ɜ+ + f Vq
  7    a z \7 UVZ * \ v  g    ]y2 h&z  w   x  @  { & HڅY  · ܑn  8cD   c I  N( #jY: A h  n  6խ Y U R )u۾-v s z p n  \  vݵ#  8  
1  l   J
&   coy Vn   (   ^ զ aJ 1 #-  o y  BK 7|r8   + ) R@  @>f  hNsb \ @    t;
  Z    7  K  =  xP  E   nɮ8   
  [ ph H(& Bg  2 rbp	n$   S| Bq ! E  ,ޝ j @ ճt m  9 T # ,г  b  ! О H =
  S Ό Ǹ  h}    0ff       o4  \ rW  <
 \ 2, ,f  QmV  p    }  -v07   $ C	 T9 @ %  	 d9 @ &  	 t9 @ ' 
 9 @ ( C
 9 @ )  
 9 @ *EyL \g b B  Tަ '1Ѵ '  P,)      F  u G蘸   }x c  >   
 gfR    Pq)   )ɋP  C    ɈLv   '1 3
 F ` h)   t 0"i     9n@     9r@  C   s v@      9z@      9~@    1  @   	.) NU,' `    :M +  j+ Y'l "۸    
 p5 @&@J 	Z 4 jh\        (  A        2   
      v   a    I )J G   y O[       /p hi                          

ISignalDessine.wdc
PCS                     B      9       j                  01F280051n                 ] A7 M{8  %&a{AG 4 |  K R U      "       
      I S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS                              9                         01F280051n     p       	      >        
                                                                        Έ    ISignalDessine                                    / #ǚ ҮR3  *:  l0    p = p t ??  *bt   Nc    	   $    
d   @g )  ҅  b 
 *H`1 ƈ"  ,   PQ 捘6E С 捙3넰s3 6bh 	  ]&q $ieDj  iCL 3o  T"  ;jj Q M 9p = t Ǧ'G@US&'  =sjUcfN!b E nɓ*  I#f 7b     ' 6/ K&j  ᑈ ɀ*f 3u H KE 4m*  w8UUe% t   @=   !g 3 |7    C    X5dͲt	  :s RE aǖ}  7S "o  7  " p       " @
p /R @  H$  @B
C   

k C HHC|OB߫ "
 !pE' ?Jd0  V  e  A# p 
1T   @   
c`  D   # x  *         XJB4 t
    R P	4  L  (  (
ӌ F! ̈́  H  ʰ  JA v؋,   -8
"B ;  ïFʹ ?= GC$  y$  WU* $; JT] 
    #\- 0  T B I +`2 P ,{  9   0 h  |H  C%ŏ  O   R[Q  *  -Xm% HpC T      X@  ]   H g7f        @4 #n
  3   v zb Cy &v    Xc  c u @̓*    2   >  (!ar 	g^ H   pUx  @ -Q  Йg  X  xP""<     ^z  
  @ $	%1"~  Q E]A x YcN  \}B  B  B  B  B  B  B  B  B  B! B" ,B# <B  ) H 7 mp `6\ h '  b
  e   =  $L~i ,` i & 08@  ZCu~瞣 zZ  &   Ԯ e "z;      
$o{+ 8 ̫ 3  X "( ns` ӻ   b H   Z ?    8&5  tT@ [    <  #7 Uh    
} 
 Ơ31d '(  
 c 0 1     B   o e   ҆  5n 
     ` ~#ׂ    E]b` D C  J`  P d  B P d/ @ j  
6  @   $  	@    , ! 
   4  
  d'AYo8     Cw	 ҫ  $2 A Wԓ d  G4   (̃ ! @nh A  "< h DB
QFD C KӠ&=   ը &7V 
   űm ~    ¸?2 1IhL    FSTFq|`  $K  
e(  AMРHd   J evl( "	  [ z̒ >l$ P    P2 G  4#BM  &ؼN  $n%"E
I}IN   Q  M; H- i _     9F   nw ? X@  j     CxG* * & :     o j -  ?     @rp<   w ( 3 . v u   7  ܤb  f&  Yl$       8I  x&.l L  J4w 6}zD z36 5 d+KE(  
   QM  $3 ỹ  ,   Kܠ   QK  q   IE5 S   Rϥ\ 2] V Ư   o<A 2Gv!4j L b 
 ʣ  
 a=    0  a @d2; a 6 6r u B|  (bZƙb$ %mj}x 6}  P   ӊ }'ɤ     ê  ~ џvJo@{<б      ,Z_ 蕯  `$    
y k s XPy  !XQ   w  - FA  c  'QZ   ;   5  ` 0MQA9f  n  3o a &)8v1 Do 9 sƭ     nK     <  t9  \  A  d*;     s~ݕ Ui<^5 ѼiD z  c ~ f   ld " I LsC lQq` ̈Ev  ;` l "h1CLS[ 2h  . 0   𪞹
D{>NQC  	[H
   FM 1 n      mp C\Q   9 cR-]  %T"{1U ;c    7H 
  +    r  ,ߎ* a ؃ @W 	{G   ;    c   2 ~#>>	6    a   j[ۖ'1ar Ә 
I ;   c	 l 1  ]MS ~r 
 M  
  j` d    ځ uU , |j uy f s | '7+ - rk7  D      ;  oMޜ ވ 2  B Q@ * L S@J T!x j \ W@  d Y@   l CĴ  t ] r )     # G Q #r  w pڗ{#2    V { h         (  A        2   
   xR  wʇ           {: &   "t  )  9                              

ISignalEnregistrement.wdc
PCS                     B      9       j                  01F280051n                  )   a e  k ֠
 Z ]RN B   1        "              I S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          a                   9                         01F280051n     w             >        
                                                                        Έ "   ISignalEnregistrement                                    /  ML  S  B  ]  
  a  ? wCD p    (d      ) F       _    
d@" @
 ) g ҅  b 
 4`1@E    , < PQ 捘6F  1 F
 :4ݘyS'  ! 1 H1t 	  ]&q  ,yrEDj  iCL ΞE !#  M5u  ye2M F ꑤIy#  ) 3 П=՘  G r ֵ[U^  gԼQL 1U "  |  g ?D 1   U  
y HưeG }  ) *`ݛ5l +Z G x  	 ^<xjӫ}C/  y   O   vr   ^  v "Gtl! +O TQ Ie 
 > ="   "  S 93 	@]     
 (Dj."  
ஓL (    ;h
  Ta   "   8 G  hz 8  l  Z,F N 爴ι  ;7
  N ܰ
rJ%/#-,  ʐ s,  @ k  B 
  ( /     9T  31 h  |@ 
5  
 <mH z#4:   *#zRI   FY[2    L¸4  9 S  SM=  ST   1 , Q j     r M{  W`YsVY   \  ( `'    h  p  2  ?$@JH    g  &D NR
e<   4  7  V S  W   # **#   (   G Ҟ,
6	> m  M4  T J 5<) 
   	m  2 ,JT9  j  /ΐa   P]  P D2@ DB@ DR@  X#  X[ 58 fE h 9h "    mge o &܇`  \ ,`B] Pn 1ÌWM  l  w `   # 	V "  R aG  X (N   2r  < xِ ^Tt K  U    j\=_<  M  IǸ l7Ę &  BK-    L F
 i;rsճ`  =R K N 1 .31ưV    ]w  :    z# C8 R  } 
 Y 9B<     -QSV F7 i H  k8W  8 X
  dA  i'  @  : A	d   ih  awf V Η     6   (o\ aD '  YdzʒQ '"홨{ 
_TH   w c  ? Џp@]  0  Jj\ 7@ EĀ  0' 7&j      1 {    ʰ ! T h  8B   İ  0 lt  
Fa
tA    @      r (  ,1'  V9 G  &)x p5 !
  S  r   K  @ 8 2  ai ; @720sq,  s5 tv  ` , tX  ȭ 
B  e&c̭ y [i 7  \h  R   : !m 5D d CZ   z R   -@ Z bHƃ8 :  "     p $  { l @
Z  &4  $L
  6 `,RD      T 	   *. "[BP=  
j0 TdE  O s
0  E0^D L      0 	   ʊ 9* c K ͈ 8  ,     H8 	 ʌ `E 8b   0D s  
 M  @  Z GZ  P A  
   #7 yZ N垡 >G    pi  T* C    U@         (  A        2   
   h-b 8  ڗ     p  %  ' 4    r$ 		J )FY P  !                         

ISignalTraitement.wdc
PCS                     B      9       j                  01F280051n                    ` \cmV  ϡ~t   \	 | _Ю   ?#     "              I S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          c                  9                         01F280051n     s              >        
                                                                        Έ    ISignalTraitement                                    /8   0z aꥌ   GwkY  %& ;    ŕD6  : G <@       A    
d`   D |IȦC҅  b 
 4`1@E    , < PQ 捘6U QSǌ3o 0Cg  7fBi# 3   $  ӑ%O   @  8mn B 2r쨡SS
 7Q  !   S I  7   2<}   S 3sB  mӸr ʋ 7  & J A  )o   ⇈ ?z 
  i q   눰)7 
UF ԹS  }  }   f    {  Z7s ĕ X 7Tىiz{    ^  q  "  H%P HfP   3 # AOBD  [% 0ơ'       #)DJ." 
`  L (    F 2 C( RP!    94 Cy6 l 7 C  Pp) ^O9 \NH锫 <  CҢ  
 \210 BB  ɸ  C >q J &3D  7Td *' F" /  JD7 0J҂t.7A   ͔ܬI$ 
 <@Û2     H˃ J/ ԳL8 t  X   hUVW  '  tVZ  D N1 5 ROM  V[}    5J ;I   @˧9   &> d 	   ! .h-Y
e  	     
;a  Q(g߀VZj 0  YP"  ,     7 "
  DkR  $ଵ *T  & Q &
  be= 3   f \)K   H  Q ʁeO? W P H `]]] =W  :PV  *  :  6  ="[\    քX 
 (ף ЅQ /     wi sW}   ߀y& ` 
]]  (  , ȋ o    s  G=   ȼ 
a V4 9   \e 6' s h>  p y  8  \L   vC 9FK,  B >>@* n  K    ̳   3o  N  0 &S 1   + q =   Z-Q8  	   < m  @  W    
 o~j(   FL   `  = V @C ⒉X 3    $ 3 ͦ ƒ`jdG !wf      X+xC7¡ U Oya @   AM k  ^ز7& p {5 
G B   |oH  ׾ om> <g?  I      E$o    7 u s [   1 {LL6         U,   #x #   
 N  k $  *P ^, 5=   =  B0  <  $v/i  $gb    t   ȏm     71B  0   8 5*P n #
u@ $ 
  I   %e  B 2P@z$   &D  | ӝ L:GU _5  Id   H  ͂	   $Ox 36  d     !F^ J  R 0r +ݵ=Y΄ C) A͐3 aM^q     &/ B  P!TA`h  ^ 2  %nP X p ] }1&e  ~	'  [f3   Ds Ӕ 
xM9RT?s  ?= pT 33U  ) } 3V [+ Z& L! I  R   ,
~ k\ pB  to  # J׺ q  
T F zd iU@         (  A        2   
   (m"   G W  a%       *Q    2d  IY 3 i +                            

ITreuilClient.wdc
PCS                     B      9       j                  01F280051n                    K 6i w %  cOە  8W@o L         "      E      I T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          	                   9                         01F280051n     o              >        
                                                                        Έ    ITreuilClient     ILj                           / P*    J   2 cM   W   * T)( kkWa
7  }  ] ^         6    
d`  @ R  
х  b 
-> c1@E "  , < T cƎ 6Dڨ1 N3tn yc& L1t 	  ]&q $iRވ 
Ҙ    O%2iڤ 3u M  cӓ&    gΛF  q  % *m Ej lɓ    w$`y"Ʃz  * e
 @ 2 ;g Pmb L;-q  3f T#Of 8B 3jެfM uk 2"ʥk e  nG    \7 爩  l  
W Ռ7a   = r S<   }N  9X \I   c %{  끱 ׯ} 2b " '  B ' 0= *#!*" 
( ` 	*  Ј(B !   *: Sk  Pc3  Pî 

 )  0 t#   
0 `  4: @  ¼C   !F  0    H  ( "B 1 0 (  E0 03  tj  ļ0C0 P 	$ S G     aD  l 4 s  6+|S ) 
(  O 0p
?g 4ICOr!"!ST L) Xi < 0b@? T A *Ct P $ 	% hUUe O6  A f QǄW]'$b
%    < h  $Z  AQ u  jHTY
  "
#   
 p* b
JG0]?eض6 lwQ0 Mw]FW P^z 8 }    X]v x @U  ą m Syb ΉUuAU7  \#   cb    F   5 	0܅׈yU^ *L   x   XӁfoF7g s  `i   Q"V ip;E7k : *t    Un " O
   L0o
  $  N'"Uz   6 K   [  Y  ް
¡  % 7 |  ˩   p  < ;     n )  d s  C"J |%%LD K=a <  nB9 d&"	 H   y*    ُG M  C	̋ u      `O ӽ 16|  - Q0Lb FU6ى )/~   7 t Q
R  ة{   \    	Vx  @   L
 '  
 ꂍ ` T* U wOx  p*  T  ^    )  4(P w  4  I
H 	WH;y N    lw   hM|  D    }(       n
B Bڪ + ͱ =|# xE 9 S.  
+  zԫ  X 8rC ,  E   z$b  I5iHv(ґ@y   IY   r%Vn K_
  p%d!] 4   #   y f~     	A1 p V'! Sj  7  
  Ǭ (>\겉   Tv (xp B # ( i  DRYx .  I^ Ҕ K~j K^  4  HAA  <f2 I f> C $ 4upQ`3dp7  M  *
M   0  + 2  B  "  O    ') TOnɃ d ?_    A  %8s  #4J

-fC  LgB OҤff &a H N * zD )։    4U <o  * 4 > Pe$ԁ Ҡ`@   Tc" - Te@    2e#
l V   *   "  1  t MM Xvʣ r +]zס Ֆ ,ә    ӕu e^
j (` f ` P v }:lE zUn.D   l"  \+ 1)Y n w    u_Z} Nκ  jŢ     *n Z nv@  M/  5    Lb  5J     * :  % T  X rS7 <0 ܅ F 1O cN   
O(
B  4"     Z- J  _N i[ Y ٞv  -jH9TV %   fS    ƭ   I  I @~   [/ F  0 ݔKZe U    v3 ]{b  m 17k U 5  ,
  ~ِ  m   + Lv2 eIB   ̕ʎ   =  G S' '&  Q     @ "N Bd  &     , T(\V JH    _& R" N G]    ժ~'u !ʹ:
  ,. hb% w:"   B  ; ]4  }:f I ԕ97f ^;E  6Y M :E Vv   \"    X O   `;   r    -ۃ   4 ;q [   	  < 	  ;I   Fʻ  ꍿz  jB9   -    u / ֌{ *   0)J1I 5    rp   D   7   X; $   ip  \ =oxK  
  67z  ^  	Ep       /;     |-g  " 5Jɀ H z K~s,J "  " i>    ,  | -y g^  | = yЇ^ '} Mo   d  U    xD `}   ~ K } c        E    $"  <~0{  ^  ~ } g   ߾   } _     ~ ?  _    ~  _ 쟿    ?  ߿     ?d?  =  >   +   ļ£  3@ 	  \
  @у>  @AA,A<A4   
Ї {A  >  L> A˳  <  @    >
T ¥p    X  8+p""  ` {  3 2 AȂ,4 |P -   H0 B lX 0 PC34 P` 5 xpC14 <h 9 d C3  @   ^C<D `
     W)B X  @
 "x  ;  8  ` ?   H  x ٓD|
՛Ao	  : Ř x 2
   7  M4 @ : p  %   (  `     @   `>
d  S ɘ=    <oT>4 $  XĥpDH$GL g     jH  p> @ oW   H 	F4 G  W b   @DB 6D<DEdGu\G֨=   " 
M D  D1Eq` R   3B   3P= =4 Y EU E  H^  9  G* cDe gJv     jĽ?m4n  p<C {J   mdG @G   u  ( 74 |    
LAXU0 )hH Ĉ(ĈL   J  J  

    8   ` )t wĄA   0z0E |@ d@=
 ƄE6B c	 (  
 p l  h1  P :H8 ͬ   `     ˠ &Y t bP "@  M 4V I F  @ ͘   \  h W  "  5  ̌ \N h    01( n   :Ѓ7  4  7P  `	1Ȏ" ]_,ϖCOCs
y 1   (  艜H  L
   4 L @:	 
y O20	ENΰP\T   Й GQ 	 Nm	OnZJp6nr>λ=cDƟʠdD 4Jq k侥  ̼'m>     H" D    +  ` # G{ G      ~ # 5      x 5 `˓pˁ ˈ  wL W    F΋ Ș@,
T  BE  t
 sO  O  O3 O  O: O
  V L S  r h0    ƴ Y@  Lٛ){ƐI  	 P P       TL5      IdJ 1 8h Ǩ   )D
  
 P8,008 c N\|  1 	/  cQ  | eΖWr5 / h  N Pw5.8 Ѥ/ P ! O $ ` )& dq |Wр· Q~    nb P  7xn" tM%  o Xx- 6 (  ɕ&    " =O* & e ;Q"  P  W   r# )  *p z% Z  ٣ Y /  p6 М-Г  P  & 48 9x +  e  |
   UZ  )
    Fy + - %mZ 6 Zy   %  ' &1Y;	۱ 
#    ל- D  X۶  lz[J   /:    E;       W  & ,\;aW}   ݠ  - KA   X  ۻU  x ; =  81  < X  фe 
Y  
ܑ  jO   R  ^e# ӏh   ؀ 8  ] 9 ;    R e  IFʁҥ(Rj< $վ%    Ƥ J   ͣJ  R	 R
 )  @      c ` { } G4G pɏ  )  # Sy Ө K 4    `$UB  D \ }P3ȉ)     0  8UDeL  V      $[lN1 L,׫ePb% E  ( l ׹ A 8  
 w|A!ʈ0RkL l  v  <  <fR   N -A3  &ӥ0S4= - s CDb   a SD ST ,  ;  J  xІ/ H Wne  :  Z Xx \ W] Z & h  aV
 	  _ U5fR~H³aTfD@       : <C  (N F$BpV
q. ٛ 
 <F ^  XT%<Ā         (  A        2   
   ~              L
O  6    I]  "&   %~  ~                           

Logger.wdc
PCS                     B      9       j                  01F280051n                 `
 %k  ` % T  %4^$  7t
**     "             L o g g e r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                   9                         01F280051n     h        
      >        
                                                                        Έ    Logger                                    / m)R y?  P ,    s  <0  ,:    V    G C 4      
   5    
d0  @ z 6bӅ  b 
  `1@E "  ,    A  ̜f  	a獙Dڈ C L  2  Xt I  ,DT  	 !B *  Q HO `   ;f ؉93  b yc Mgӎe     h Ś   ]Ŝ9Se 28eҴb8m  2 n 3c     _y  ig  68 LQr  " A z  4E 7p QC&   e  [    i >  k1oʤ MFxM˕    Å ˵ # u   cw      w誦 u   {_  ; 0#  l  8b^IN   
  P 
   % R+  3 а  C ' 
  O    k< " 7f N  p %'  0.B aH! t  "   7 4
Ea    j  a +';l; ,j<
 29 0   AL   (B X    3 0 <  oO3
C D  MɁ   "(S tRJ+  ҁ>   0  "P1   D   R#BuUU[  W]M5VZg  [e  d   uؐ 5  K+  }6u  Z#   > 4 J  ZLIE ]   _[   o ]2"Ж +   
% x 3   Ȁv0 =w`       A v   Ƶ  
  v *s "h i;  M|  )!_r T1&7X 1UZ  eN " c8Ș 6q  :@y 9RpNP R  ␔ h# **#  _(   G N% U	 C   u R    W pM (   Xa N n  ͤ
  + 9 )9(  斶 ^ت     X -    8        E P |   @  = H  LЙg UZ D/ t  ~t$ 7 H     ٬  n    Ė l  N{ k-m;|Q  ^W  6Js Om }b XC Rq  ȓR8" + 8}   \  @ӑ  sPC  4, *XA ֱ,{pC F      "  h,    ,h!  iP 2Ё I
 @ 4\ 8#jA r  PI-uqC  ; !
rd$C 7  3v C+ӡ 8 L|Ld&S  L1* b    i/rhR2:   `	  X% H
S   n   
w    5"iQ9   ' 1)֊cS  $&z U 90mxA   $  Wx	   հG  nԂ$!	;p v@* DY   !8 R Cc  
iP   16 Hi,   c 
KK   BF:2      7 gL Op  v  *)      HA8  3  LiRS,ׄC6Ѹ n~   gR  5n1. +  JX4 Dcen  ]   f1 L3 3  'B @   d   =d   =# ` 
] 2Ci Cr fZ J   % 8!@Je  nu 9EID   @G:  M| M>     2#< 
PЂ  $     ,砒   &,  SUX %% < cb 3   
 iCj~    G!h(f  ĸt  Ix  y  1l`
 :    ' Z   n C  j ^   = \9 Z 7)  z     
  5     RmUOB]AҐ0 Y;	 ^ R yS հ   z  ^    e
|    \) VrC߂ + G f  +   }   =<0 a%z _H츁  Ă     ?  orC   < a  a  @   8!
 A )V   \ 5`R     _ ( B      F
40@a ~  8 amP 쀓 FCP mq $ɓX5   Mo~cOd 21/(
  w d -[  <! I  } r   Z ЏF      .  [ց 5   mu  ڹJ  I S  Gc
v        ٷg  01  
TP       Z iá	fY h[   ٜ' # { 7Y   bX;݂#h q  T!  & j 	6_  Wz  p~ 6  lr  + =  /|q vx 替=> <"  KA `,9 Q Ɨ   ? & ĻR C u  %DY   :h `@Ib	   C  ,  [a    7  4r 	  | q #fEko{      I!U  <jv[ C    Q   KV"t P I Ф&6  Y`WX   3~ 2Y  `s ߑ 9S [ꛕ{  M <  X
ٖՎ | 山       'O"  h  E   j
걶~< 
  {h w>g  Cv 
U  H R Z  [
 x E 7=  k MxD   4  l 8 &~
[\  U 7ߎs Œ AF  ,  }     ?     2Ψ1 `9   8Q   2Ѳ s  ` $    R 
|   #
  C S  Z    (3 c37   {  
ڢ     H:  
  *  P;:*qj!    qº      ï  > 
  @ +?   	  I s?   a  Ƀ2   Ʉ8@     tx9  )  T@ y  
     ;x  ` sHb(AD 2
`  C   d:T3  8 A? ( ء  Bʓ    +  PB KE'      8 F 1  >   B 1       P90 a9?h  4 @$ 8 I SqCو  p C@ D  V q r  sL   GژG  Cy0   B\ 5< 䓁   	|I  )J<  s Ll 3    3
  :t!
AH8 T ! 4a 
< EY̫ ꫿  j '  ( ; tE   c$ DGe >/  
?ij 2| ; 
k?n  ?c  0   u<D   q  ~ G Ȍ IC  d    `  0    `A 3  Ȧc ; "  H 0(:Z  $         Ұ "+  "* 
<"   ; !+؋ Ŗ     / >Λ    `   ,L_ B  #\/d₧@p  $  Lʴ ! M`t ՜   
Ьſ "0 ! $ M   |  ͚ $D	> \N lN   * ! # NYk-/ B  P   .  Jb ɪ8~ B  
 /T   i  5?3\?4lP [  -ā B \ >  l(=$ I
 â # P Ʌu   A,K	 @  @tJ 4 La  i xH T  ։PM   ˊ
   P|  K   e     hE K   E dB  ˜M d  45  O  I   .P â 5J [Pm I 8 ̙P  ˇ#      (  KD GbIє   cQ tQ ,  K*   p  4 $  $  
   3  4 J "PR4E tRtL RU  қ  Sc   R] Bx  +85(ӣ F  Od I [ JJg  S ɖ  l,   05  3m ї˅@؅M` aqT?  	Ȣh@$;J< 	2
  Rm y    ? 
   x  P I   틁5Fv= H ƞ W  Ҿ( K   2"R
   
  TQD  1 h W  / x           (  A        2   
     gzJ  [띗*    : {QTl  [   s    k                         

LoggerErreur.wdc
PCS                     B      9       j                  01F280051n                 L i]   ; i   
rv1 p   A h \       "       
      L o g g e r E r r e u r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          	                   9                         01F280051n     n       
	      >        
                                                                        Έ    LoggerErreur                                    /@  ʋY   n  lN 4 G G~ B     mivA  s   v  W           
d@  sA %(cbӅ  b 
 |`1@E "  ,    A   #s昱3' :uB yc& 6b  1  L <.HҤ<
 ig  6E &e    R 9s  1d|   3 8p Se M!Έ   (R ]-~  A*U X}ƙ    .y2 @
  qsD
 4j^ ̹ g b    ur   T M# M   ɐn{ n s|   : k $Ď Sv4[ o  yi 5짆1+PS   7  o=> p	 n  1  ;O    bM z  ŷ  IG Kϲz 3    j[-2א  #̬  0 " 7r      %' p//B  DO    "   7V4 >k    >   p Hǋ   " i #HrɈ 4 I(  R (    *- /    $  * @   qʍ ks    QH  qM/  R0' rJ-  s 0 : ňb j # x  *   	    3 21V  <5բ   RhUVW   = pTZ9H )
""Ü氃 j   ! .H V@  h #ߜ J N  18Ƞ   | 5 :|  9R ;	i  V0     **# y (   G a 1I	 K Ӥ F;
S B  a ` x .ٽb=    `B P9 (  ?  SSM  V_ 2" ET  b   " 7 9*    A  $i.T 
 ֣   ̈́k  v nCȹ _y6WF   Zf pw x-  ޗ  w 9 ʀ_ `!C 1ჇdX *1 Hb b[  
5!@  P  < Yu   7 GXc  "# h  & @ >     ghe ~  =        zjU  )Į   C݌ܠ  R[  ڰ  4|+   zÇ  R!   gt S7 " B  2
- B Ì:    ^z    2 {  GM       R B^ ?   	 2 ^B " f>=IA VÞ7l #HBv ' 
T  0   #  C  p S   0/ P UH L P ] dC b | ! @yh8 d&=A	 *ߌ   .  
b2  $oyUl    D$[  Mp    ) Y    5O   I^r   0 4b p $ %<Lt")    +   ɼ L [   A  ."$ 	Q B  7 a .Y ?R s  &=R  إ@YI^ c      $  />jq w    L 
qxI  gF $q  BP    3= AB0   ]  Tz  +@A
;4 /)Ӣ h  V4 8
 8 &, 
339  /"tP t     
Y  J 9  =p C W=xП# 
 2Ǽ  xj C
Љd~ '  KZ  2f @Y	 O ,.Q4   ;S*O      FG  16q @W   dq _    r    
w  F - z rs  }   x ^zM[ P }  uB B   muR  n 7$ M Sz d TY Z   l_-  X   mi    (δ  @Tە @ 9p j30 1 $  "    *"} @ '1  pB  $ z r  h3  * 
4  /
    u }F  &   #I"v   U-vC   ; $x 	 vi   zŬ'  @6әτf4 |	ˈ? F  O @Dn    8 el*    ݉kl   h(n X@bf   g  yᜒA  $ Jc ҧ|A*8    ; a&)8 n<  D ]  QA
 m 4HYk i  & Xݚi UT 5 c qSf  \3 T 
 X  ! -   P c
 W<  Wk   - { +  ض mJ\E a  6 樱p l 9 YT Z } Jte G  ѳ H=VP  H L   b2   
L  @ ) ȭ  2q   q5̊K V  $\   K 仩
? ;X    h    Ľ |}        |   6 F G  >    W q<K   `Ɯm6  
=% F;~  OB @y#8# BP  s4 ZlC  $؎.n  _" bW*^ 3~ 8灶q '       68 
ε +
/   <D  o .  vM   M>  j   s  q`  S 2s( L&ر  H  li t_/Ͷ~)  | tbG=$ņ F  n 7 x f r -    Z e   n ^ F. # @x/)7  %y'  G     _ӥ. eA j %   ,    ~;z      }   4   ص ?  %i   >< j
 iU@         (  A        2   
       f TZO);u Fh% L ё qx8  { os i?   y s|                          

main.wdw
PCS                     B      9       j                  01F280051n                     \ ^
 ˥L   5w ;   t3        "              m a i n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          	                   9                          01F280051n     f   Y  
j   `        >             a   
$                                                                                                        Έ    main                                    / fh xs     3 80 y  c
b^ f{N > ^{$       O a       Y        e      X   d    3@  g  I F  7   $Ăj:Hrvb G  2rd O (  K  E
 a`  .A (o = *  K΁\J #@   :e R'TBPIc M Eڠ y"X  N[X V`< n Tw ݗP  Ku` 
  % m߼    8.    h  @   
 0H &.
 1S 0 \ 2ra   9 I i
F X eiɦcf   8ޠ l     n 
L     HdG'f脈2  3u t c& .b Љ   9w w 3 L  z 2 "q:` Ns A ZЭ2
ͩ  N     a   + %bJ p    
; ȗFl 4 8! $ë P 0|  
 ,$k⪁ #
8*  <c
   *  qjH H 0 $
[r &  R ) B ʻ  k˗ t*(7 D* 1BRI&rJ)_2+Ϲ\  )9. bS   :  0:  ]  *?R  { #;%ݳR .MA , CC kјU ҘR )H  b  
 Be zh1e zb	      A 8J    LHb J: 0Í b &  C 7̝ 
3d  L  '͂ LT  *L   J2 `
     *  @}W J  ^# i9   M R N 1 
   ,H  +L    e \ f d~   la   $ m V  / `  Z  RYOT16x   a  K,  rbR# S Ȟ   
 |  J  [ F   a K AbQ | .8 ~ L  a i[       ) AR OK F8Ѕ v   FT.   B_͆ c   .` 7 @  r . 
 wڗw }    ?J< Ņ2 % _Z x     (ôU  N < t G 8lZ ΛS Vw 곽T     ~?ᮊ|ѽ> D  - S
  %  }dD Y`  5! 
 _   Z2 ͱ
8 ;  V>   t Z
   *p ؃`   ғ  ,WA 0D  P $w  ؐ VR  .  6 q   " e  i RI !1' I|q _ BX   D&d
   : K/KD _ b  1X  ` p 
 4>2[S  3  5L IA  4  b'    } v GjE  = Yw  d a5  3 \  89$Ro U `R  I5  s  "% gI   ! iИ   %vn  pJ  2 "H  ū rk $ - r   r  D /_BŁ  $ \[3 t k n f d  iֲ  l 6   x 4 T"] HF Ҍd p 6^9hQY  M~(&      4 #   |eۂ A.Ҳn !&U7P֭ p L%  X=+  &< &   4 ef   5:(z  & Ș`,7H
  {F((   R V ӎ1 I >  VAr rmI 1  ? gPO#; Ԁ  +1   .   (S  =  R `]ԑ 7 . 4 a$a   ; t&s+  *  褪/  6  ,   d  P;    "  l   k x*V  5i P   ! %-t 
 9   ա
c ;&  7n) `mQ w      5sYO   ?% i Y  U   :ޑ  h    h N F2   -.     /   S$    e  ] ) 
_:
q   }m   
  1+Iy W =  0!p0 R PV  Y z     W  | ` v N UÉ ow M 
P- H O,\  xňi @,9 fu \  u   "V = 揿  L    uJys^ 1 Ň  ) U+C   C   zW  2 F *2 d .Lp ] W6GX         w \F+ E\ @ d  - i  H Js _ r      e (f @  6    @(  J [ G 
` v յ uA  : ԕߥQu  <  B ] CTʗ   ק%j      oc< ?w  fo  r #j  q }  Hky   )  | LW  a [Ԍ h    K ] n  Y    a ӀC2
 ^  }#    [ l'   @ /|    &     \  u 	 U n b9   ^_  ̺  u 
 k         I b_        o    Ж6 7m^  E 19Kۼr 6Μ 9 e  c< 7/ {    e x ǚ   6z  6 ^ )OͪܽE   W換 <     l` HW  q W   -, g> <{y  N [J     ́7     
 P @ u | Wޣ E-n<   _ t  yd   K   |       K wF/  l N숿| w x ' S  Q1 4{ #Dt *    L    
&  :  VZ<- d ?   0  (  > ;.  " ;<  2   { û
 9 2  
   	 ٙ
 	r EC      5 j8	   ۫ A  7 !  !\ 	;B =) B#,Փ ے4  R;    3㿎 =4  C 
    ¬   {> = #C0 3 @A[C`Z Z" I( J 6<9ȋ k:ĺ  1  ׋ 1 > ˿ X 4L1B >DD 
@W BY 5 s: B ΐ  : D     
    s   ?j ^   ×  F<  H@ X@  5     Y| Z ?\ 9]|A?  iS1 s  s
,䫷+  9  : ; Df (^/4  _   Ds)sT>/ 0; 31|  GU  ?  oDȏ G  8T td ֣D   =x ># =`     8        Y S b      5Vd g| h  p;-< 2       h50 x1 9 5 5 5  @q G W k8 |         HC  @  P0
 
 "0J   Xʦ|ʨ ʪ 0 	hB   X    Ȩ     8       8  7X D(	   |  ʬ; S 
Y   6 Jz i 
  "PM 4״JA    h  P
 8 6H{    K.\    X     84A9   D[ Ā =4 4 FDҾ`JH A   pӮ0 4 >M  z I}\       |D    |$  @  C    d      t    dB*    !  P.|1+D  O  P**  P   (  ϖr  AH `(  û J  )DQ Y      ( x   k    E , NA +M +s   0X h  Q2@8  4|j< ̏8L55   =?aL  ӈ    1 ї  L    ; ӗ  z	  "8;D͘ɟ   <H ]` J]    |z { M   ٬   T3 T J= ʮ܄   q:@ @ ; = UHN XN 2
眖     l;Q*  1R ΊTR Du    |      G  VL        rQ>  z Or I~DAIlVk5 sA   3RP@lPċ5 fZ  L "B  $ؓ   IX* P XU؁ dт  EW*  ʨцrCT Q 4 	R&R J  hO0l  Ի𬼞 )% ) 4 +U@ -  /  0  @  k峖 #a  5-L  #T  SĠӍ1  B=  ?
 = ̿ Z  T 8T HTb d
 G    Ԧ J  W M   z  T -U        J[x 
h Y
   T Q  X& 
   b
 v Ǎ-Y P  %g%Pp ȁlRp E LZ  mu
 \A   p= q} 
  u  E   *  y}ϋ  2  ^|  V  W 9ǡ   Y 2X B؉}X    X X   mۊUQ iQsuW    0      Q = -_  (Q "
 |qY}  \\Rm 9nd   R    Yz@5Z/]Т   U aZ 3uZ6    7-S  ھ Z SZ Ӯ S  \۱͏0[CETE \ }۹ [l ԝE  e 4X  
 ؖōW  (q z     T7 s  9  (~  p\EJz  oÍ. 5p;p1Nb  b1ȃ3N 5f - `:  : D     v胭u\   $[ U    (D  h	   ܒ0 k	 l)V  o	 q) sI ui w  cqQ  ¶]>:$^o  K4 C] 0 z 6  ]     a;]  y  Q    q c  A bv e  	 d    h j   Qg   MRڕe+! |}       Y ]S r W+ #Je   mH ] e ߧ ]q e& g_ W 9 |ݘ    )   }   g ߆    ^
 h Dߗ (ЕT d [t  =h| O  1     pV qfi  
[           vXee   Wv   ֜   څ6 z1  X  Zl !'l 
]     p"L  c5j  Hp.^  O =Ч    @ ޹ #  (  Q~ E<U5u  2   r    
NOjA B  N Fޙ AM   
P  Q:  U    ץ Yʖk MP  W  k 0   h  fz&  Vv ɶ LmjUh      t   Zf]         g IlN    F  Fasʔ̆  
Y  _V  =Y "  ŷ 6 z  {  z Ja     V&   ֛. E Y  F  P  j &  X   ¨ Zv  ( j Ēm . ݦ ޖ    L  qh  +   C+o     id=WpX xn   G7 ܫ  
    P  `  P- r-  I*D   g    M| \v ] 
  s. . qW
݁FR 6h(  r V;Q| 1  S)okn 2sV 1 C2 k& sc s   qO  ~k>'` =7  @    q3t E  qi< x pg  sZ K  A  Bg ? (# mF oRwOH oI m  sB |   [  \Y   hWg`Lo O  (   m[ F m]o ~vۍ   H 	 v  Us wx. T 9s   ( S\:GLF  5gl a
    #}tpgr  K 'Ew]6 N9 >vt  .  p6  J m\  x  *c     x_7ྫw v  v c ;W <   ~w  i, aawu`9 G ?WϽ dg v7x  qyG N
 j x FR |O:Td:d$WrW ɀGvn 3 .uӆ   u n  ~x  =XO خuY<  o    v  vV  ֕  +  F 2 Ӻ ǔ ؔ  <6  q r  z ټ   X=S z} zP7    tz  k  y  =      s!> 3> K   x   =w xW yg   x.     ďə|  ğW    9~xO [9~    O2  G F< <    7i ? _nG {S?   x  [       όh pzo  5    ԋ,    0b   ! ^ -P!%  2Z ʀ Nё(  -  i ۩m  Rߨ ܯ絹 f^ ڹ {r V<h V` s>j           ch A	  0  
# ;Q  )"F   YC} ׵@  ]  |&K ?  7D +E! IA      x 
     y J   ɽ  尞 } H   7   `   @:         tK^, h  1  g     3>@b 4 ԁVP a o 	] ㄖ "    (k   8  
[ c   A   ZI$ % $   T hI/) H?  a    p 	 Qh  P [ 3ĥ /耽   .    K:  	 Ԓ       p x @ KT  )  S  Z I ! ǔ $ٚL8 2) _ ܂  rV$(     b  T 	p O 
  m M vt  B pt 8 :  , 
t\  %    OP eA   J  r  <E<   ^B rw    <_H     k M  w   ; ) @E  F  a O2(E   \\ԆrA  ( ( ,   ^<q2,   _  L .fCJ   	 ~   r    R     Q     qAP,   ( `   <k)< 6      ` `  巶   
Q * 6  
	 
&    uV ! Q. 7   X fX B[ BmYĈ tX Ks~$nQ*K 
0  	U!P         $O /   n`q  ; p    A &   0p t ] Kz-  u^;  V        K x )     P[T ]   -g Q ] \  ŧX  I  
Æ &  h /       j
E EY .   Y )F u   :o  G
 U  {
]  G  1   X   7  Q  LL  Ԉ  kt
      rg  q# 4 8v   ~ 76D h hXq 	Ǒ   * e  z i$ M  XM  " ; &B ?:  KqѪ     @x  
 r     -  [ vE   ts 0 
H " Z  r2  -  ]-   /6X  w \@  =P&    .  $q   ʁ wP    /n4
9\  ҒE  5#   0G ø# W   QH=  U$AeJ ,/I  s 9   ZPҁ  8P% ԕ Y N  X ټd  TkLΩ $ r  [m   I 'A̜ a  & = 嚣 ;Al Y |q   SK  =1  :"m V ` 9&  XY b *fr  b И b$   7 xf  cE6 1 h4  ; E AVȼ  	 L20      Ԡ ]2 E6 4 
 
s .  p B^ 2 ʰe  ~  WRF (,  N z ,  C~  f   m  RH L f fF͞  f j䌁p B6  BCj  o ٥ D z^ 5V  f  +h
  1@ @  _L g% 0V   $$l o uF  * ` {  Rݬ < ڐ @P  YFKk , %  mn    & "R!i  OZ֙ 5   I奁  v    t  3"   LX O;Aͪ]K   ޿  p*    0   FU    W Z^̟ a  {W# g  s$   ). #   5 n J 9ag D a _-ϞQ8k% |	֭ I   r     @     <x  ] O PZ( T
 M_t  +7( 령 ˼  ( ,   y Pض)-(m ] o]J  )
Q:  z7 X  I:  ^ z Z:  \  "6ɠ `hcˡ3 G   ƛ T  \ /ؐQ     _ ʵ 9 7   n mn 
 A (T  ; ,  8-Sd  e     0% T  
C^R^   s " x6 ;j(A *. ԕ   e |W  R  ' 7c   2HuYQ Y ^  uE *    Z k F  e d
B ( 뤂ovJ5t  f	b. D  #1  Ð` TQ F@ca X   к9O f8ݤx   Q@쀡 <S3i C Oue    T E   Fh Xu &O  d K   KԛS 	F1 A 9 4   /
A Z  t ̺    gJ}  t nԎ    >G  [ Ҋ:P *䅗 z  'S   3  , 6 e T g    s#-T  H !  > }   S_   G  E  ʞҳ  )| K zP &Ë6  R 
o` ԟu5 ] ] 'E   ݔ   S   dzq ) QH@ WU   N ]j  @ y      
N *] ų|һ  >kC[D     jR)jj  <   ^XN3_T 5`: p  N[E  t¸ Mw N} o4Tbּ RK%_uR M VTq 4 ď  ԕ X  9   :T   bT|, R   '  @ |i PT U T p   q    <D +   TtU+       ջ  b+C=       3| O 8>ɍ NX WV f]   d: *   8Yo   h V     9  R֟ X  Ű
   W } +LP    ? cm      TZ:>  kG֎& G d I l   f   )   ĮZֵ>  'A LC  sz=  $  蓬 ^8  "   9 {  B  E
  J  & 
 !  + }~ Ǎ֝(ay R         
   I uُ ?, XD     EWW9-   Q   ,7j jl Y   >  +  n ǚ  j$Ulf
     XK K  r  խR 5x 檕E  3xIf+` k  p ƆH i'  ů V BZ"+ie    T5 JPv+k jU ! 6 `Ai  { L   Z  [), ͮ  W (f=+1tT S     W  rI0	 Ji c B  aD  m ԆA p.     x  ᰪ ,ľ 	 ѝ C 0_X  
    %q\ q  $ Ce8>   gj I c BDap  <Do  $ Z    "&G
  U  T Dq W& "    tX!V"qb 11r   Ƨ*W L   j n e[    \   8Ti@ k\O t  M8`i ɝ uW^          6^8 K ,0:q"    Ë      Ծ  =k=   4 ̢  W8 B ,   O K   " 	 M 
Y  Uq-  G  *,{)%逾$Z Bp   %"A|  ,L FC,  _ Ɔٶ|#  sbǊ % # T       vG   q P
 New  +Ȼ    5 & \ē1a + 8^    A  ٳ*o G]]H1B PW p u 
d  > y_ <  <  .ۆKFՎ  ӭΎH  gnX 0r/, 	|A ]̞ - lO    Qݽ R-_ kH 0 A #I            k} i  
  p_  } =6  )P F f %  4a  : ֤  n   _  (fںa  Oe T      w  .  0&F54ཻ  @{   JI n"֔   &  x ' %  י  L O       f q Tf0    W  b  i   m -֯~R  K]/     ^#  W  U ۘ	o '|ho    8 X  K  b 4e K" H     OR  ر6V  ^ 8  J i  f*`r0 %    w. ߉;5y^ĳM ⊛#  "ƿ dZʗ  
 $ T  ni Kl J&֎m d
@qK.   
ܪ\  N L O G \H70jE    fj   V&8=  y SW ^U    ڶ; b   ։ [q I  3.  ؊  4 * 0?ֳE 	  ٻ{ 9>_ڲ V ,  o R   B6w,$ qVvc,T :}kZYk   a)   # X   
; q 2&8d    Fnm J8CL   * 	  86  D2h% DT.Tb   m@H  , # f ;d L  K  GHi r   
 U [    tSxϻ >s F-  8   렠 
  HY܎   ap (  _ ~   ,-޲   B s" cX  Y Ţf  ru y  c A   F |  f 죸p  s07  ֋  T֮     Ƽ  s   R Lf#X   e  5 _    nC â N _ P 3 S    8 |    a6 ߦ ]  $b Id* We2݀    6U +  z&   f ͊ 2 ! љrlK  1 0  ά ) ( M8 4c 4AN"A @ J3  Ӆs ]7p0!&   dԀj F 4|ԫv7        J  34 ʘ  [  
  =9
Z QChYd    'R  45  F) < [`   J jIxS  NMk   e  Y  j  	"  -  Z     &  dC 
<0@ Xh( /  <@ g_   JN i * 6  ' W  #̩5 v  W'yY  d 
A́9@V  PJL )A% D  E3    8 @ N   % |  0H$#s    +   gx  T  
ѯI   .l    R (
  *̿ H$   `  C] h  C 9j    uc } i     3     X^G ,K ߬    0S  {   Q յ k ^    9    jW- /F   zz  |^     
~"k P  Fv q  l h y8   d  Zckm]ź L 	 :B|    k5Y    7  V ѽ	  _     Fx  $ {!.  M [f     L %d )  Jvj   t 
 ̀ U  !9 l p qΖ  g Ē  6   :  dj C5
e | <m   6   .l y=    h k 뵭    &۞ W, MOu   X     |  "ݶ۶oV    m  s  M Ѯ 5  Z!$n       z[O \  F f l   m	^  p    3 *  Nwz0 ;?8oq
1@ " m3 ƶ̹    Ӕ   {FÄ  0 2  
9|      Y o  /Az ߻    dlo }    (軂 o 툛6    M ^Ꝅ 37  ߜ  k    & cUUW 	n i8   j{     U  F 
  ,     #v  n  Q}          P  f'K / q p  C ^  }  L  qNʢ    v [ 
 JpW     6 '  ,0&v  .@W ~_    t w  *N  7H  } x_  v *    r ;   \o\  -ǿ s # ;i qҴ  \   S     0 ʜ X{ c]  6#  \  `DΝ Z   V    OݪQy-       Y Xv ,]    9 	* ;ZKkZ^ Kx  ֺ\.Xn    I   m t H)lx U   {8   ``k  6J|ys 
2t7 d   ̅|Ņ x  v
     `  C7 
B  K9    qB   o[ |w  ׷ U w G m jʡ HW   gcQ xIyfO 4 *   * ]zG  N]  vc޵S;D_    ׮!       OoW   / ꌻ  r  3/ ܴݲ  ۾ òn  '    =3  v 4s{ ^  Q     v   L 𐉯 x^    W݂ & i  c 4}  w  N       @p*# \.0 
 H    ܸ2%    s@   璽  ' ( ۊ   v  ū7?ϴ*  L ^
  fl   9   
 ]         @M    88 M@/(  @
"pﷻ K̦U ::  "{   h@ օ  zRw  >ɣ6_ )        i Å     oe tO P C> L_ 25   ]h 5 qoV K  4 l  !0 Z      b.a  ܛz 8N   b    |'  ] ; Ć= _  N g:u'2 ܳ t  q{i  2  vK  %:L7 1    . !n      p_\  i_ 7=_ t ӵ oK   p[> O Qw
H ATv   =:E  *     7 [D7     , n       / U>  ; _B   } wy+>  8] ϼ :o z    | G D  V \,`&     s9   q eco su   ɼ K 15P    j       }V  9  vs+ݍ~߮    b   B*e
   | K   և> 3   B  @, ( 	 ~ 	 Z |C4q䰃`]  O    `   u ^0 :H u    I}#A        Z     E+     E; / |  7 <  l  τ^-    G ً  H _ %   \z 1  LX
%(  
, @Tj! ؁  ~W  4y          ^ll3    6c W ?     AD  _ x>  @ 
 O  /nPHi  ? % - ?n 6N  ;   @! Wi  С   3O [     
 $   "j   5/" Ȫ  ` KL  /ͱ  pj   $  I,`:   [M S#[   
3H@v 5 p   >u  _  (9^@k l k     \       
x    (     `  X  R     (8  < 8I  h     {F  `*   =NH    ,;   NX & GXpK 0( ;͠  # P/ s! < /4  86o Y \      ޲*! {     ڳ  =Wo c  =׎   :    {  ;]O   $9\0  O  I  ܽ n ଽ   c9z m #  i q
   q  {	̽ &  f     , PM ;  =  ǃ L	] !P   2      `M   ` 	 P   
 
     [ < N  `  {   N  ^  *  6 u h w  ̇B z     / W      L>   T>; "  j0 L   > / +   @ ,  jA '   >a 
}=d
  ߌ     f `0+
 B o(  >^  %8* J1   >zo    >  H    @   B  c@5 H  6K  [  `  ; ڹ  !  Թ  #( Δ /T  Q cc  / K &?Q1  .?ԏ( ʈ aN  t5 
?       -I { h/ /qx     @   $   @nF    9    
 ? oy;    p  
       ?      ?  {  C ;Hᖹ   C  l      "     o;       C   ,   0\ @ -2,
ǖ P-  )  k"C$0 Ak 
   +\?B@  M@ !V@"    Ba   @A  r@e $}@  % @  L  & s    g
  +&  1  a/ @3
T @7   :  a>  xF  %  A tр C0D*AF0 xA"M2 J) g+ & -n    ( f  @  g)  3 3    ne)  k    " 
   . Xn       
   0! B    $v  @O b 8 x҃ 8 R8 a K>      C $ & 
   , "(  < x  8`0 ^ "@ Ā+ )    d    O !  :  ?p U|^ XqV o \qW  _1X    
 W X   0m    6ְ   04	U ԫ;  Q ?  R =^ ك B oڋ P e  Խp _ 
oª) 4: Snz UP3 8 jo 'Fq c??  a  V9
 bz儻M䧣 :  A  <
FT2J=ƥd   :k  k 꽍 *Y 9`     T W  	 ;   s     H p I =,&   :  \J      H l@$J0 (    ]4 4A{^j   ( R W=  j s   &    P i A    5r0n 
    ňӽ~QqT3ào  _  ѹ 9F g    [ |  1  U nO  D8k    m   wA  fGǨ   ;  ǛM΅  K %`   w@h  j   ;pΉ `@      kĺ F   ˴ @) pb    l,J   y	
    ?=     {  k F4/1   ESpc S A  q\  G$K "߷ qף
uFB Fb   B  m q  b<  Vp` OG  4QF HPC j  ^F 1f  Aa  {, C΀  D  Q-  %  ܃ Ah  6@ `  G` 
  C b   Q!        B l  '> l   F   l H 	fRA q\  s`BxNJ } ( YH     %  + H<  =۴  qp$C ', 	M6lš
   D A  )  T áf: 3     B w  8 3 \ld v  L TX
 # d \  k[Z    0 ˚$  
̾ 

> %ؽ  i   Y   v  	
* ! J <  ҁ 
@⁶     /j      &  I X   i (  ( MnTe B 1  q ϬF   8 MG )z 
 =4    #  ? m  G 
 s#S  Q^ģ(ć   C@N_ !#:2 
?F   i ,ҕ B 
R   F | I $kH֮ $&  O w|	      2  $ ґ q  .       [W kЏT 5 @f  <ǃ r H   y 2
U    Y  
 H   mR(  m1  nj ? F  t  I  T  H  "tϞ$ oZ@JL1  K 
- m  JLpn    BD     Y= q  oșr )H    jH`.t  B 2 $W$H Ҫ )?JC2 )G |-G ͘  I 1 DMH  \E  d*eGr  *gH r  ,aKQ2 $%)
 1  "' Pp Q7  ` K
    
b 
D   % ʄ   +  .	   u # + 1 |   ˍPy{   M/	 Ǵ ,8  `,;  T ,K r ( #   $(J ̲ L J_ %+I     ˎ  =L  -E̮fr *s   rL tK͑   :  q t- K    ҷ,! = 2 42 K  ۋ_ ?2     l   6K     nA  ^C    Lec       8@  Q (   h XB ` 
 K o   ¹+2& " H "  tB n < v   & / ? p. K    ,0     L ġ 0I
  'L ׺ Fʤ QzJ    DFSʔ;   S̛2 -)(S   8̊1s| 6 3   AR  1wMS $!    .  ;     &S Tᚻ   1yL  6 A^ g*   l xL  Qؾ` V 
k 5 ȃPjl/ ƅ0 ^     3#  r" + H  GZ    rfc"l G E  q 4	K Ӽ >M  ^  ǈ_ ,C͝ 6S  2 c)m   $̪ l   )? X   5q Z l5sM5QD5}͒S 4-  ؒ  2  c  +*O ձHC2_ R s M r L rNr t5Y c  z y/ \v :  @B   5@ \
?, 4 :ꑜ >v #P/ Ʉ '   M 1  /N R8  N   D8KHsʛ4;=w 0 8+ˌS   Q 0/̌2  8G͏ӧ 6)ɖ  |93L 2  6O W  !]N  5 R` :wK    9 M
  41O   <==   :S " 贇 Kgs \:  ]n  6U ϑՔ:iNS X 6 N 1 L  yЉ : 3    % X7 NF  \; ʶ  |;13  #@Y ̺3 ;  ͸S   7 "   3 >  -    Am {8%   h <5͉ ( Ls  ~ͨ   WNX   6H  D9I ׳ƌ=o  s  9+  E  S ,Au    @ M  2Qϖ _q e  < V  Ӆ<> B  ܃:K   < ʡp 2E g2 0 9 
p'AS   J@ d7  U   ; 	   t/    	DE{    JF2
c 0  `
  P     KE JM $X(O m0  9" , J <A )s  @q   A;; ڔASPJt D[P   W T0 ,5U 2 AK ˓ 4E]OO  ,6U d  = N r BaG!ԩ$Ba(  c>_ Mt  DuMY  tB_И   6Ѐ  ^   v@   P 3U; P  u; H ӮD   /) / ߌCＶ  Q   5  72 P  s  U<  s pDO Rm<1J ]5y W4 D1mPS E9 ! 9/Pf  -!FR f Dy  ! <]Ї D%  "% 8O
4 \EɖP E NfsM:[F_f|2 ˌ4]*IiЊ4mB ʐ    " \  O,F    .`  ځu B(?  : 7 L     ѭ 
 ' Q -  ;   \ ?Ń T   ф/( D   e
  :    GP s  ,Ѐ ]@%  EB7ҩ3EI/ L   DoO t! =oP s%m1s [ 0 >Q SM5IL smLsO2I%SФ%UXz   T2{     O Զ4F? J4 = ѠT ꆰ  d) h ~   t( >@ T  J  zS 3'   S  J  s    .i| 5r .
 |~D   K!λ (<M    R T    $MB5  1mHqP   =eSϴ0EFS Q3 ==    Hyғ 	-PGN u.  4 \)m8[4 |I OiP& # M   I[ RtAUB   t? :   S zEs    'D    !  P   
 C R Rk + Q +mC  7,   P.  |Q  `F Q
K   T8  g   RO  LO%N    S T/ 0
R t$UPw  ' HS  4Žb 
 25IO9   TL=P T@ 9y Ѵ?FMN D O T  # E  DM7T   E Κ     oP 9  S B  pS9NU:uN T    (N N59 " M3 *
G   - 7  . ;IG  uT,5޴   T NU  cKk T  G  L O5N  -  эS$ S Q
 N  RQ L' > B]BAK 4T5A)ӓ \ VL5P P  H OMy  ?u
WsSd Q T O. \* M&3D- F  4T; su d&YԬ %    u ^     p  ρ 9    Y Τ
 Q     L#5  ' O R  1w K X    J     QxVA B   T   VG   LS U  NeP  
  #Q5   O >  Eե    B M=TTt ? W5U4j5WQ OT4 Zu [` I;TIu&m*[S  REX T UkeX V 2Um  uE  ]    &  ( Z w *   d 7E W d 'UB )  7   WmY O    V  pUW u Y Q q<uw   ]d< U s t@ SNP L q ļWiS  A   ք ]Xa  w=
 T 5/ W Uwui V3S 53 Sm  Uj L ³  1MK.\tmm<  `uMeH k&  6 kuX    
   :
 j `SD@ /`( vx@q  Wx  "  3+
  S   KY M  C ҿrXu    u~ _  =tYu
Oյ< ROh K}]'  tL 4G 0       N[  ^ wML[X u  ] ׮52TO   u^ UEu_UW Uv1 ^ VD5E
b W 5m]6  #s X K u [L   W 5 S%^ Ue(b  P , ģ R   K (     
M`  s L?G  Vt    N  ^ Y \      K3vW O5 t]KO׶   KK .u]  / G V	svSw;1u %b פՇM_ Vp  1 W dxUHAӲ5^ ^'  \=baX,1 LYټ- {ekSU 
:U" }TSƶ C_     u $T Ն ^= lo   Lc Eq   ` c     F%oT  \ $  e] X  M ?X%՝V 9'  l 7   R	LH Y
  qK d F  <ED l Z ( UV>M,  D [ ة  b  Ո f X  i k h6  fV v }ZuR/   ]sXy  "f  ՘
X Ne֊ T عi b  S y > X 5 B  * 
 _ .[`    	 @   ÜR M Ԁ-R x6 ]Y    \ 22r Ej    ~%        uMh/Y 6  h  > h     %eZSvkEe Wڲ  W?Z\ (fT  3E_[  j h ֎  5iA  { le &   Ak 
 ٜTiR u mf{ZÖy e v 2} W        P Z ` j   Ui + Z   J   ts N
  2 L    ЍL2  T r   H    b A* h  !  
  `   * §h  *PscA D  :     Q jp& D 9_-$' 5 1 JY  O0 DY  f5[  
   h Z d   &  
  e     _7\   a tL X85  *   j "  @ :    =    jkբ dj< !R&     ^ |  i{   S 5 K7  
@U B 4Cm  P    # ^ r    4)1o+ s d 1`B nK B  U    	o     MoO    ho      0  U       9r$ MO   Cérz5   r 0] a]뜆  ` o Y ȿ mH  w 5[ ۡR 0p    rl
31  b  JՕe

 a Z   U   @J  % eR 8K\cH @qW'|5 T   
  f ! S [1 ȳځ~@P   B`	X~ Q 
r \V  ? ]n   ɖ oa +  OѾ r# *W<  ܁@ 5s G/  
s 1   \     V6|* \ U] Q   p V  uO ں Ua   u ElY \ A-l ں   o ]}U  LW  4  [  Xv ]y ؖ  l   k l    P  d6| i M` ey ׋֥p7*k6xJ o    )i )   > `      U  TH 7  s   ;  4) \n   ѹ%K ^1` ޗI
 zYe5  ` a m, .  Th} DtÕ] R
  ]I  @}loސ6  y S  [Ey  UOMm  ѝp ϗE}W۠  e  |A,f?[   Mz  J  }MA q
t0 >]e    0 (  UmO ط  e W Rbm 8@  Ȁ$@  8   d\ ^ Vmw Z=6 P`G 5T  j 6 k M U  ~   u4
߇W   ܞ- s <w  # [ ּE  ܸ) E% Bw <t ^]w }i g   ' a     K ] t  M  t Hv  s
D]  z m  h7  hݺ  =p  X )i' Z   " 8 5Az B  
yK P x v=\   =   CJ I   VVK9iJ 7o ua`V ڭ   ! 	   Y 
( rT  M U  m  [ 6X  `/x  
 \{    X   UY  ~ 	
&     ^   } `MH \  = [ W ( E "`x_ 7R  ^ 7 ]x 7 E$ ܉,K$ (^р
         ıi5 d &Q m*et9 IL oU\X 
   )R r    	eP ' W] "'   "'N  "  mFsfH d     v  [ Y  qd'_  u  ]b   B in     C  o x
  N7 ߕ * U .  *Z?+5 y 2l P e    C J2 ^}_   { i. T D  ' ~!)Z U   =   |  ]k.H Fف J 6  |  ?C /  D&U6tE   u-b   * ^< / Q H E vE޻6ai׆ ȉ 4 '^u ! B  b#
5p   w 4<  -  ) <x` x   =   
  v 2uY`   |  sX_= c b    x 
}  x.މ  K      L ) |q #x      q{I  	f" g "v<\  .~     
  ,)  Ɖ b  S(  ػ 
bpL 8   * ~^*>
F  b 7_Ё b¸'&p *  .    Ҍ( 骮Z F߆ * v   %H. i    D `K Ѧ   #   b  U   U  '{0 H > w
  I  Y	 Sc C$ L  : 8 V-
 Ѝo! ">u c`  Z   
xd v< O "$   C  ܂   An$            
 Ð X
 , 	c  ߠۆ        aOd  ?      Oc x, ?c} ? v -˂E~Y} O򣘑w !Y? l d  
  '8& /Q*    ",~ ۂ    x      FN5 +  JF     C   ǧ<    -Y8&  d=+N  ͫ:9H  d)Y  Ϥ7   n    :M ~dG'K  
e  P t d   J. + * 	+ ZDR   d  R  1 :a "F -`  ZE - 
D  Abx5.   Hf   çGη^ P N֒)e  T  ^yI  geUMT& Iep XdG?     ȹq$ #=  #l z  ߔ /аd9 R A  @  } bY   }c  Y   as 5&   ` j  h䒲  XyN 1  v z  t ܗ [$ 0@ ,1Y Ҷ -nk`Nl  TK  e    e 0>ی   xT    % '·QI  ΢ ֲЮ | L+~ Vù b%iI bS   1	 > 'ehy?֘udDkQV A 8R M   h   @  Z P-  g + 8    wa i    )Y      ; ff  *    
  {Ag F> a C      b  d  m  -k   f  l l  K B  -yi   f'q p  " h>   ȹ&6ǂf  d     P֜Kb Hᔉ v  X}  ˹h.   ]1 I    $9I  d Y: g  *B ) p  'P y i  V @ ƀn
   t   ΁  X   (	 v7     Xz&B;E*dϬ  M.r m .   h     vv:%- < S3o
#  RIӤyV d.nV   I ^  E   Ё W jɀ<&     
,| # I (;9    :_i]˹ F$gt mY& 
T \A?   f 3&V;8I ϓ C  '^N Kg  n^]}X.p G ְ q PC{ # dɘ g,  ) s  rɗ@~   / ĵ     *  F    Z  ˺  f.  ^M  
# H  } ] } <} ?6|U   6 w G  j  |h  #  a  7k 0%`J h o    `hhz  ^ `   ܣ .>J z: eF+
. G  d& 
f   ,Th UE   	v6 T V    ). m z8ޜ i % h` P  u |ڎF } 0(Ġ Nj 	   XD[v hq  J ⸸   h\ .V yIs I ~} X   >T4"7   i<:: wM
a    0  'Ka
   p ֬  [a  )h   $ J|$ )
  ͙
  `=j zp    {^T  l6! 
 
: C~     	   Ē k vZt  iٷ@ե ^ sm  2`    e a ( c c کE枺   j 7eF W AO 3  &¬i   Ⱥ    N_doi ˢK -Bc  s 
 k 7lv  e yq    z 
s&`Ч~z   b  E ?  }   
          5 7V P   8&HB਩   h ޔ  Z @ 6X  Ե  z H #hKL   ik T D #CUz   =h| f 9 &Y ^   : q     V^hZ    h 4.W  C&B* rh y np ifZ v Q ؈V%af) v  &ڬ~ a 93A j 6 VX 鏀         vU3   k[    *Z{X? {    o fK ;ƺ  U ?3M*    8 F 0k' R  "^ȷ% Е .2?Ѻ   H: {U 
$ e b    FDz حE [,    : + {6  ]Q    &   j      485c OIL   j :    j , .  l      Z:6%} hz   
4%  n   j  }b Z nnC;  $    	   *4k
f   =6   a   % k,    c?A  

2  n  ` S @3  F `i|N ^RJ   el(   n X-8  U; 
 B
 Q  B P{!   #{ N zmb    R  X>b   ` F  k x   Yc  m   ) v  l : v  g[A   ?el  Ʋ   Z    儯    k~  >} m     }   4  As   C{ N  i  F  V  ֶ 䩘 V ,  ؍  r M   P     }7 ^ F  ĞYm    9 |        ~ 5j a  ҊU(UF  DjR   ?z`H q&  F R  &  ^n  ( ̲  j6 i~ o 8 nu  8 c     Ll   Z A  "5 s   s.    
 mjZ     %Z.ï a Z0	   +   n  И       q  &   { Vvn    :n   
B u  UKn~  ֟I ڡ  7k     -k 1  iZ;  ìڬc  >& ˈ Ӣ zJ  Ĵ  JK# 8q  ޹ =    } 2 
  4:  X  4  hR ޴ [      Ns O   I\l^   ҍF    i      dЋҀ  T
 A   
 @ E
 p  D  >K$   (S 1R  #!  9b 
 Uu I " ?w  }$J hё%CHl 
3s   ig 
%o    3 УE   s`ӧQ V * ̙*sĐ1 G +r xV _ I m`  լ[  
8 ؒ'    -\ t    ,`  VN{U 3b lF{    s xv   c 5$3f 9v i  f B  Cǌ (>      s苿 =E  9  0C N*QQ[%  3yԸQF 1n 
  z  ` =  s '   <1ޠ 6ǌ   $
`; 7 C   3/ 12<#*A, J<C   /5  #85  j 7 P
  x 
  (B *   Fu    
  Ġ :3 PÍ  Ѓ&aAFဣ
bT ɇ3T #ʰ$  B (  & B & <* $R 
2  "
+ P`  b|#8 X   /  fc 3) r A
=4Q!v  Ɍt  , O  4 .B n  6    "lrR  U)u X D ,n-TW   :  6Ub%5־W3U #9ow\r 5 \ >    5W v E  $ ( { w_  
@_  ߁&X` 	>  % A y#  ^ +  $    7  ? 74 O<   ʧM' N0$Ғ 9  #  |   o=

A  
3  # 8~ 
8  8 ި 0考6 cyV B!衋>: - l +   Z   $A=O : V XX    ʦ0       nn  8[
 ( x) * "2B8 	*B     T)  
; h#1ڀc:(w I eu S   <m  X l3    . ꫳ  2^   ԩ( : _P#  ߀Í* 0   ˈJ    
=  C|4  
P   8 e U2*6.  P   2՟   s! +n6   w@ 	    %oyAr֠G  M  |A  0d  3 h   ث    3  {Va JxB7 Pj,ta
  C 
G؉z < % *$ 
c $ A
oh  ,|·QD!   "*Q  
   JQ +<c
 x \a+ q D:܏Vd      'x7dy  V43 uH o&< Q j    &
H - Ab@ @*2\ +I dV  }	g*  J  @"TPB  5 QP A   )P d  P)  = n 
 0Y π  ߮   䚆[fI" 8 Mr   S   唇   1      ͵l %9  x  9   O(    0  u C  % ӝ/0    ZŗyJ&=  E)@'/ ^s h   S b  vf 8 L sg "=
	 C&R    @  J:   $ )S9T)Q bS jU      ] ʪ $VԂ  `c5XYÚ0    d]kZ J1 0 _ J J.  U { :4    "
 T :    \ 
 V ּzD`
k @ z  & 1 _ Y ~Ѱ e c UڊAV    \ JW e /   ,<    6  qikZq     q%   ^V  L-n X 87   p    ׬    b \  v  x   ; 9eֺ\  p  [   DQ.x 
wY 1@` +Z   d  
  Õ!5  X\ Z  +Y   )  $ ? * V '훮 ^  - j            ȁ r    JT    3hE
n#c ld   `\9    o+ &u  A0`   G$pBX  "rw 
 `1 G     A"I Z  P $> ,   !  L_: 8 nn#:  zx=.IM ;    CD !  f*i ) % @  F#꥛ t  @ O ғ  ȴjR.Ҭ~䩡 Ӓ8a  lr d  k  y L]39   s    "S   0Y   ^  
PI  b : ǥ     9 ^S! 3 C  r -f  %) #s ;  d l     h   >΢   $
 #   U   F  o  o \ j؉  %    k    N2Mu9fI  py4 
  ѐ 4}Ϛ <TT    Кh/h ꔌA   >    ́K  s ޹  r; C   SC"  GF ̺$˜팞K _q  E (e)Qyu  G   6- 2  dp3  H    
   Ӡ2  y 
Hy   c]d_  ` X 3 y    u U   `  (7 / Z  ]z    x-   TT65xo  D K [  HyVm s  1 #mN   ;l- y|  h  D? . o cM  t    '}  < m     (  q-  ;  C b8 6  7yP  ٢  B
    :I   3: @  c [      %   8@z   ?    4?، ' ڟ  ) ? A
 9  7P +B#/`  诋@ 2  'L  	 @ 1s r { ?    " 0<{  :Ck+5T+6D 5t 6L     `1#  # C=<9 23 3 -?  @L  D (ğ   p     DI DǠC P3P  z 
  p ( { D  ā İ Ľ C p (80 ^; &   HZ2(   A@ I dȲ- D  /  W )cF "=y 5  6S 7c4Y E; E\    '!7  ?
  =bx   U ?] A; h"   &w 5~   	 o  p     i ǉ+  B  G	        k' a \ 3R @d=     u 3Q  5  H1 +)   R2%x421      ; B *P     A\  [   =  $ &  (    )  +<   BǸP c ˮ C LC      " é ed-ED e43q C  `D pD   I  Jd-L    B a   D ER c1  ȼ+W$
X n 3N E] \G^t ; ` X I0Fd䲆iF& ̒E B  F  F8
 Z    q  I  5  =3      N 	 J; U ǿ p1 ǺxNI 5񴵨Լ 
   8  8 $ ORE   	     
   <: :      * l9 A 3
 O t 7(,: zzJ JϺ %      J  J+    B    * 1  |, r 8|  S .T<B  C L  Դ!    Ki|D ,	  <L H       , 
   ^  # &  v   H ټK/ h ' i,   k  m  .M    $6h  =L 'u   A   Nz  PHuR8 % (0UJ  Gy Qۛ Y80 ɽ T 8)  5;  Y!< 6mT   <mUz  P    
 O 3  P 7    QЏQ9    
  ù <  s@c      ?  J  Hy P  R   % ' B RѯlѰ      = {5 2   ׶  ,+ Ks  !  "  #%ҾTS  &  7  äR  Ks R    E= [̃qڱf`B
p >  g@ 
  5  "pS D3  ߴ   XoM '    8    q     (  '#Ւ 
  :)  1  q"Nz       p  Aq A	G3  +  q'M 3   S2h   4 	%  hu7" I pU jP e:   E  Ո |K  ̓ i5P [rZ 
 
W -	rUʠ]3     ڈ ܴ ڵ[ xͅA! o  }LNrdά S P  ޘ[     4IVU 1  ]I  \f
 Э x, sAWUW dW)  dQ    K  ״\ |-  W C ظ 
R U CDRA _CTR݄ F|į RJ _    b; ŭsE X    ,\     © _  . /  R  %  )  18 <  P R  %hY   fD & ;8 Y H :
 ; XB `&  'a!  ~  #;) !x "  ,` (8J(    <Ϋ?X ?h[;A 	 LJ    ܿX0 e ^Z      +    ^q GyU   ܢ=6H7  8v   9 q  c>z  ܑ)$ VV 4N  
p   Z0 5  cI d30b  <jɋ <X Cn 
 ]t1Q& !'    "9 $Y   QM͍  ԩ=]b   d? )TVe$fݯm[ ]cf   C G 
S  k d   [ \   V/^0 D)j c  c{. xR u   D  ܁r )u    t^ B  g mcP      mW ]  - \-/    uߵ _  Q  _5|    _ >  %Ć]؇MR ] . 6`     M( PF  QܔR  -jp9Ꙟj 	̞ ҟ Ҡ /   { V < "#J   ' . b  qk  R L
 R       k  cn rB_  f	 q G WȲ bL0sX
 ,  C4  i (ir)ã   rjȾC    ,
 _ U/  Ͼ  ̋ j$  <m * ˦  zm n ۈm     k  
  m   6  ^_# ͬ
  9  $m    `nw nz ^ fm FK VK  Q ~i _ `` ~     C n j     N   n  -    >
     a ^     l p#Mp j  pǸn  
	      J 
   No  o2|  .   o    G/Wp   7  ~  n 
  Xr _p  q q#W1  n?
   B 92   8 X Y !M <M    
 [! )UhIK^V I     th  m    &I b-W    *  / ri 6q    37 oq  t t *      
y`  hu(W P' H /  S Q$     W   " ~tZu[ q\  #  $  zp gv̞ crQ uSWvTwoU t @  (    X/o  v| Φ d q-  8 p t   r s/v Vw f lw f op   w   j 
*Wn nf u   W x c w }Gw}m    n m  W   jj  t x"   u   2   h  0 K  sqiM !    /z  y      @  ? z` #z  k  k   : p    
     #0k V . 1    멌k' kq   zL K9){   
 1  Ć v P @ Cq l   q}O    d  m  w_   mzii  (   }      gx - n i} 'i)W l |]G  ? 
       z Ox '} 6 vw  '

 } Z  G  Gyk     G -' d    ~Y uا   ~7        w ׿   1 q 'q   p '

O   1  tޏ O"l k@  5P  4  @
 `  PL h0  | L f0 A {   =  0  j m  = W  ZZc{m
0(   
 B/ 
  (   
 B. 
  (  `
  t   (  `[J   j+       T  'P       {d- + 0   V  Y ǆ $[  _      ' ȟ  r   q      
,    ><8  \<
}P ݾTA^XXnt   
 g  2vү    	  c~        _            ~ O dB    fc D ھ?  F  +     O 
 /T    ?        / « 
EZ3      ,([  k%     c  + ye m  g E(  p  {  A> ۏ   G  "E(xq x | 
 !7  ]0 ս g  !
 a 1 @3x f  SkmO Ï  
 @ ~8 $RU   @>  V ā ̶ 끉    @ jbȇ< !F,*     Q p
 y   P` (
  7  ` A2~ J   7[1z q    ZH &|ٰ"   ~   o :CH Y A~  
 b 놫  8\ N' xːz     G8   bCd8  K  1.;Ř <-w 2>>Ɉ. Z   JB
 â1  eѪ   h͟ ˌ P  :k(Q!]\  2J  
 0 `a  C  cn^ c ~   0m   L 0e  L 0] l  @    D  q XD   " B{4 yċ /       J  TM M5  D+S	_M\l5   F  O j    5$L q9E)7䈔J BFDX b   {`d ,Q& O# \.{M  5  c a  >8xr  Q|tp,    Bc   o6>   aisu   %C hY o  6fH x n     F.  $ \   / F X&  kL  1N> 9# bXo N    H'  6l U 4BB0 = |      , 
G_ t   K  0  \{ XRPRJ= % _ c  R3 GW  > &\    ;< "ۣ <
,Nܱ Uy
# d     o( df /=Nc +u 2  [rP  C)#  Q<,     p-   ԅ rR J'I*       Q   x \ ? 4鬏U Ӛ ]  
  AF  N 	 $<      	| &ݤ t  +t n @1H)[  $  « H+ Ŧw n[&Ɋ11} /  sVN£ 밥    N  5 ' %j  ~ a˛	( e | S6 K ) E vزh  + *;!aDv@ R  L     -  5K  3  d c Z{ -æռxLs a p  &       H67 d\ Z K   1Q&Q  N  M YѦ    \ B    ?   l*<  7ޞ<
1 ʃs    :$ ~    W@^a ye  W   +c *  W@^ yE  I &QĂ4S  G  A n    =   $B 	 ـ"x  I    &z g5 g  "ړ"  q  <۞6 zF    @  r)B  (+g   ó*   9X  @ + "X     g g LhY81  ܛaL    $ $U  prM  ; c Ė P P
z@-h  OsfnЋ ek S  N: L   ʯj1
(Ŵ wS   ZCC 
    YzKg @9 ڜ E w~M @
Kn ;Sb P$:* h  Pt ˙9  : 
`u: u   z m   U 
  & S( |  `  ^hg0  ѽ0   h$  
    G  %0 )  .  (   ̆S LV b   @" ī7 <$  4 J  d /  + k > @   B ,,W   bdA  
 
 6pf JLh    @
   <   e@   p hpJ QȝUpwrQjI" )7u '2 H )4
V  ;h  A 9z '  +  &'uh塑 "JuZ./ u   S*Ba ܢԉ Э) < A  ;T  P5)9c  d D4 <Ƥ(- 
 
 Wb t 6O%   % VT~zQ )J- U z  ' #  1   ř PSj  A  * $A U Y4mZJp D  8 3  % t   i / ~ T  D   1  #   A! b)dO A
 E      #@7 B7e  ԡB p & gX  t#  2  #`  	 H F        G    4   PH e     >eI $   ԳfR? I=) *  1E q [ _  MQ\L   N  e4+  	 &   n     8 jIu T  T *U J|!  K  Ԅ O fF   Ҕ M A     U    j8*  -   T  T  O%   d2   8# !ՊF, 8a%M  *U  C  _{jx   5f  Rϗ}ݰ m RX  - F՛  Ƙ0 U s  B J     L	  ~0]  kD  J  o  ֦ Ҋ @a%   #   ^ @X
Ҁ(<  P  p  `|;z^  D  6X  [լ_0   nԺ <   DA ~    GHD
  'ܥG JA | 1@*i   5  H  "K\j_   > 	^l  Ik C  g `  (" \ 넽	V  W @ +G   Z  z`煫 tP Ѷ      -  6U[ b-Ͱ6 J U kE# } 	t T K_=B   c ]  c _M*    6      ͯ     [o*Fؔ     d -ǭ -   ȢW  * mŷ  , X  7O        { )C  } [  b 4 a  w 2 F :m 5  V ^Kq -?.O=  6 N\  k lv  (   Zl O % v p  r q d% $0`   + 3 `:AG W  VR  y  tQ r"  r ,݁     8?
  ::i[pEn M B ҡ f m Ǥ s Bב o).̵ 2  O W  w,  .K  $ n   p  :  p3 ]   v׾ RwqK.͝  h   YyY  ൙  ی
y        J\  Q/    ( Mq :!  ͻ     o9oQ o  M׻Tݭ   5 bܸ n%Bߤ 3  L  r   ݼ     I        \  _gm ]R 8bN 9 js C   ~W 
   =
     ;Ёp       	,\^ A/,0Z :s   B  N Z    b
k
 ( p  h J @ea    T n Q ud X ]C 	)ү3Rp   5 C Fp	v0 4 x \PBp" 7CX @   k       O      p 6  A    -      [) E *n  c \ +{I ­ o     ^+, % $W  [ {_9 G  i Uj_ʈ~  N r  b K  n6  r    pl>
9   I    ĳ  _ {T WR   x   
\.9    Q   9k W   ; 	V) qXt 
X	5  
 .L  E|
 hG  $ B:   # 
   B@
 ; G      L , l 
 ^   - p >`i8 N  z    Oy  7 M 0t*cb (&CiH>W 'm 	<  ; 0i l  ag b;`z,/p   &      8   v   5K"-:  S U 3   i 4   tE{
@U-  k  a*o  w U  ދ  &6Ũ7  gk	 0 M     \4, k/'&
*  b׫   "N =Y
oW 0`K$~ qY  b     M      eI  0] j '
^    +6   F  ,|' T - W fU  1 y  Db + @P͕  dZ31 < g و'      X T    @F 	# >T   ; {{Jv  G'{<? _  Ъo 0  sJ  
  L !RG  #I <  p   ]"#@;+  ԝ;Oj  ! 9K圜  T  X< of   iq   u
 /   fʣW./f < 71 d Uy,_  ] El   p  Z ; s  _   k     G  +Sb  { g+ ) - l
     , ݺ   2 v #^  c4u] '5 b +  ՅF Y9Ce=.m4 >  7  b IF;0W 
   n  |  c c$    ^P < 00 vso   Y i7m @p iZsgc       0S $ J  *m Y: G  .Bq & $  4  H  'yx 0T  
   90BT بn   r    ǆ Rp b  c   yC  7[b    JF7 SR I 2sUʃ  Vi {5  
 @ 1  |W knІxi^ } (*ָ 0 Lԫ̣ 2 . zK{QR8 [t_N $ ^wk    r   ͣ  lͯ   ւ9K  F\ qb    07lo   5  Ҹ J h  1v   B 1 k  sg  ZuL ߣ 4.B x'u 
  @ 	   (  IP kR  - 
G i@    VxL z   0    ~Zhэk  S \  : -  Ζ     1   #  j> Y @ X\* = @T  *~܅; :R w #UĈ  ;  Y  P+ ' f m    F   6@is%  ^Q  [ضL T \  ǖ׊Wd7ir  Q4 V  \  l  5,>ј   wM -v   ZYs | - u & !{vciu  5n &_;^     I  Vސ 0 #
    F  w n}    . ~y ^  DU  ش  ]U 
 u1 
&   G ( T     ;6Q H  MX 
H)  s烂 	 桅t 7  @0  jpD ͮ Rkd[       V   6   ^  b  ll  3n +     |& f  Rf  #+ 9 + ^x   yHS i k  K#j h#l@"  ̀] ց`Z  J쬂4 RG 9 Ωi _    SÀ@ l :C  `̪  E     z (, ! ټ?:  ii / .    < Q% NX! m 
 S L3Ft   7   .  v?  
  ¶  6, l+6 v   |j 2  # V 
l  =|   ʑ n  T  g^ [9 6Ѱ w k  W $    e أ B. h3`z   < 1  N   
   
   (         (0<d;П  {  З` I    	7 g{:  6} @hǑ#
    PW   8  ՆVks   R     Ot(` a  q   K p  U Ԥ
  +1 8  .  d p   !  ̀ s4Z  #s Nmv R pU;   z   PF K 0P  6rPE 婄     JT
  c     @x T  $+/   {[s:, ?- U  ^ @z ®  7 ؘX n +  oC4ǖ ݈  s
oKv ؐW  on ] 80 0 (     n +  o  (v   ܀#p     |    wr  9 sn}uGl  Mx+5  (l `  	b(4   p: 19\˝   -   I |O )p   ) ºs "$    K     .Г    l    ޝ 9 #  <
 ݷ 8~    `gP    yH 
M uZ 7 1 @ȜB@  Qçb    d. =    Gݪ Zn e       R  3  በ) 	  Bb I4\! s    `Ts   l  bJ/f~   u  H  3 ䷰ x N 5g&_ 9  i N Â    T" 
 F^ g &  (  @  F{  j 3Ї 6f 
 < > Շ;j~ڙ _s͗ = B  z   q6yNpհ:   a H p[    Q   D  1  kt"   X	L 6 t pP:M  )   O   8  = a W   ӽ]  z ~ 3:Q ٗ   қ      t   s p >     dQ`c  ]  ֏       ,܁c 
R  @  d 
  c
 
z   ~ ٣w O s  r@   cg       Ʉx &uA~ - E? f'    E'pk + S  CrH c hz z. Vb r ^1p䂫  y .  B P> >( D    b: 	T  C| |g     7 D}8   8r[    Y    "i8  VSF 
0%<  ǀ        W  ) B    1     C΀s Ť ӁE  C  J     / H ެ$@O  R,{POeŜ  | #  s  $۪$    '{x   J sc~/G < /   p ? < <m   B >2  Ӟ    8 a T ˟     1     #    	   ~f  [ ;S  8$}  8 b Pό ! c \ĸ  +t    C  _&HP6v= .IP  &    +   'X  P  & E J( 
x(     \$   qpa- 
 ]?y  
 @ - 
 5  &   L 
 p[  1;   )f  ? A<@  ǂ
   h?)o   6 MH  Bf  %R  E
@ , n M/ e&    d>; W E  
R  Y0$; , xf   %(%  C [ 6` 	z  X.   7   
Kp4  R  `> gf
 ZP_  K %p h 4(    @Ls    Tp
 &  a   T . " 3  T& . H  a V 5 @}\p  (  "0{ 1xV I0  `   j h b   J  rq L    ?  cD  !@   t @q 8 :x=
   ':P \^   ۠ A  Z  0Ch !  )B  "  C * [a  pC X@ 9`L 4x  e@ @C 0] A ŝ  <A a[n
$
   # K[6b    e 'd  03( \   V  0D  9  !  1`! H   o3 [ЃTE8{dZ  P 	 D ӣ'  v  I@   & 
 0 ),	 Q p* <    08  J X ~ S ̐3 W  uK &C p      ,  < M  4;/~   /p ( . 6 
 (.
	 7  <   8 G   B  <  n  
 C  j    M  $<+ ޻ B= 3 ̶m;
 =b>  5D ;   0   N 1 @   <r  [ 6 c38AR աuxȬC PI!. {N #  3: 3س"    `   1:      k cf - 푋䄂Kk/    \8 ƕ^ - 1  !#  @    [ʊ<sMp: ҵ   Ӹh9" "pH>$I0XO  : f <P  ' '$ D	  MvK    X
׮  bͣ  <-5L D/33 D ۭ   b  -4  2  A"    p+x  ` xt8n [| A t 9 =d  .FQ| 䈬 @   "Q "   3 S

 4 WK)6  #  x M>  tf s < > ـ:  %  @ ="
 ¶   < 65  <=D
 . CEC 
  	   ȳ$n    0p CHў[ B  0  W<  ˉ   oǐ Ό| P _  PP9qt  6Ͱ   D nrZ`r$ 7!
R
 oZ)  4aW  Ŀ      a x*x@cIv H    < U$  ` sR   
g@ E  f H R    " l qs W <  s  TcP  trY!   `     qg    C V$   z# 	`  k   
 3 ؀ElN 
2 ^      [ J  0 Q `; 'N ߺ    ꂁ 
 )    W   9 B,  n Ms 6# %p   0)=u .  <A
p\ ñ
  Ā R .E S  ' ` L   qX2   d2FcF8s΃Y  8^     8$
    G  	 o=    6   JiZ8flQx  hLO  Ȭ>%   PE  3&Ʊrl<  @xL   1Aq  E	  0 '`!<Ɛ    B ?   {|  8   M@    w " B   ] ִ7RI G o      _tu  m [
 F{-@ǷA 2C    [A=  / \ r^, @HeH n  6 pudH l  B {1  GÁ ! |H |  з4Q   l mD dCo.ϳ >  l  .2  {, x   
 - E       슸 r  p3  GNo6 }</ s  ޷   O    
  ƽ`s 
  K  S5  q     C 2  3n	FҮh$     $튂 I$n	Eґ $  - 2 F     h    S\   h   ( XM S% z;l     qT!F    8@ `l     Q\$߀+   0
   W  ŊO y  < &    >   go  3 F   # < 
 [ I c0   $  4 ` 	 xq z ' ?s     # 5  T    yvp ܃QA ' I   '#     7    B       о   +4 ! ڀ   "iW☂q 9F   '#J2  [ Fͥ h      H i  '  W @>F $ ,   n eB  
iA Q1 X@=   i `  A5@g <   T   F3 h 	    t I C  )G  i d 
 5   `  glʹ D')ʐ  m%L  `S y # G0H+  0p 
Z U-u 
P < ހ eD f 1 IH V F  ,Fʦ      K      (W ml! FoML 7%   7 P4 
   `  $5IH  $W"[E X
2] Q&H0!d 2 Ir  . KMR `@  'c%   8O2( "0Q z  C8 ~* P8 Q     .  U   ;P& K   d ?          F Q   <  
 ,?.  126n r 6 .  |V >aҶ/ <;      6 =kY
 CR  #7 Cr6@$    /;I򲑬$ 8   $/KL ; bj1  | v@  6` 6 i "X2  aq) Fr  3 .1   & /p |r  pB  /  @  A(   G -E  2_41 
   /?   S. 6 `6       
 9γ6    r0
 r d 4  Ꮤ2 E ɨ0   r)(sdBҏ4$  J Z
 (  T -/1 l#   l C ( /- b d/WM    J 6yL 2 $ 8S :5t
_ ɨ  ^     s)P       0 ̙&ļ|   Y 1 X a 	F AY l ?N P&n, J            ' 	  uq)87 v3 P       7!9G  t dp  Yw0 $M  Z B      t 1  3c 	 P ɋ    L R   I  5,! Z ` 
r{kV6 7n Y   R   	      K <     U A Ds B ElA5$ s SQI62  s ΦP0$
N  Lɠ3z    玍  VP6  A C ܀     A %q;`.|   #  |P   Ըƅܒ| -+ 7M    X  z   2 t"kC( # $}LM    ͯ  t 8ͯ#  #)L  O 0  
3   *    <5   @ |0    YiZ4   C -e  Ѝ Դ J
   FG 
0 r6O t    M 0   Ť  Qd%  L   <o	 3  J /̳J  ?+  _D L /  !  ,2{L    $ J* R4 JJ R& 2  A=   Ǹ  > c8  E.qЎ  "     х-   F(S pҁ/    P 5 ] 2-    HΎ  > ,k    4 L1  l3 Ù ( 3 9    O Ё. GȌ). &   <=  
 . O r 8/) RŴ1 gӿ<  H: 4 L03  E     `@ ̌a) /  ӑ >    { jM  ;> K( #0  P[% \8     y)c 㾗 @  
TOkӳ 0)- { .S+  4o0?   $MDʯ C
-  t> K 2 \    ]  / i B   >      <   r        EGQseӬŮ " j{  Q   1P 7e  w   Ec4  2 I N |9 LԈ yH7 cB <'t <
     (( P  -Ca+$# xQ
S \D  FԳ D s  @ ."c DKMGԏ D   E  Q L  O ;$
    # M R   ́   	 k  x
ҜȻL$q    $   r
 4O
[ ;X
  0  HC-   +V"e␂    K BC0]  KC D     
 +t  Ӯ 5l k &5J  ,) = ,  \)&% q ip  2  + I@   ]          P |#! P Sc  c 

N    S , 0  A  1D   GH
3  Ŵ1}L Q D ,
 :  S  /  Q  3 Vd 9H2  ja +3go  [F = 6e   -` t M }0(   w    l 'ZS Co bSh p    oN ӕDm ?  $ 
    9 	t ]a N  k S P8]Iv 2 ;]0R
攭    #:  J 48!NF > LC   ߠ# S !:5O A  ?  K  | d mc     B "4 ! , J3  4)-a   U 8 QƨE(d ab 4`3   xp\ C 
   4 
 6 A    A T \O 
 
  $   c J5[, T; X   u &  ʛ J V % f$ 	`T /   @ 
Tl= N'  I ) TA M   Y$A S S: I5  %5t0T  Ari*h  Pu`  kQpJ '      VzƓ g> ޲4T
 R b  FT  9B H>ST Owa  9CUt +B ȳ    ĸ 4 İ 礲   tm B Qd$ C S     ]V R    Jɫ4  J Pwt ¼PAt'  $     /-   " H J*!5@]̌ M5O
    BLET  G5Q
3 
  J i$ Wt \  թd  V9 LR  V] I V!  U g)8E MΓ* E;Iv  d@ 
{   \  D JD0)d @υq 0 :Xm   G@	H q x  Ҫg XN V !  T D"; ! sZ  G0 K  ('  qbx  v U  N `	0  +@	  :ؠ
H   4  ޥ3'^Rs    ^  j 
 U     V l[  * lU BU3 &   !%   V dUzZ     =% % Λ  Q` ((E7V    % K?  BN  " t= U ԏ `    
-    % >C Ia z$  S 55ţ y      ,   ,5 H?2uU1  Q$  5vLj   ] ˋ bu    uYMV]MP K  ϵ  X)   .MWcus ]ʜ 	c8    ` ^ R  ( _ R/ Y%E  V)(>  _Q    2 A73VR *VD
   (bV_df 
6@ ` tV   \9   E\) W_%6ZNiu  # u Z  n Z  $k [ ְ 
 
8 m V V^*N   U貗ޖ  j a V}Ɇ ZqX u  b  a p<C.R W q=
   sT K;7 hm` 4sTs  F  " ~ F  B= C' 
 ON M ^5 k ( y S   D˜>  j$Z C
  B    b P w%>	2    = # c   z    ìD ـ` G {u/k( L   $ >	  0}   _ '  r h   U@g QDvHWQӓ 47 '    Ej%u.[  ӏ I PD  En 1   lX
0H` 3   f    ~b`   d  +X H  +    E9!  `K  r   RH    z%  b  RU/ 9 ғ*DZgU 3  -c 2  m X4 ̴   [ r}dYթ" ` \*0v%h X# -̌     -sP~v  U <)  ZhiUV .K    P?s  U-OΕ 

>WW  p> UsY 5 W   -j 
   bҁt& _  [֜_uY r f U= " 5  ;  ]8Yϊ   ,  UV C Y    u 
  ؔt)((   @  ]   5sidCI V{  M3Z=V g Y $!   m$    t5JUWŶu]
^  Ev0   Uw CV   9 Pv   8 , w      TP   i <3  h/ Ocp D ƞ +͙  V6g [ O w ] 6 Ag µ 1\+  2  K 2JhB<  <?! 6  A[     nPp  d3
ڈ M"{Z   CG <. 0  ZFꏫI9O   ]   ջ| ٌua%/=ֶͬ  ^ :  + &Y[3  d 6 `X< X  j     \8sO,t L5 Ѕ uf ه  -  ^t l- ۭv &   t  .G ? Ep ?4	 H02 Ye8 Q'   # P c lE  ! -s,͜  mp   C  R yᛕ t 2  T  	 (,N  (D
	 2J83Z A@	: TNy  UioH 6q%cyP i n lҫ iC  4 N  %T  B  ѡ:   e  5    H;; `  
 N   ;I    lA 
oit  *V <u +6 =   Τ<"%Z p \ RW miUU VW-G L5vn,   y&  $y ?kx E `    Q"L G N3 XA+w =^ ]Vs R ^ EJ  w$  r; d  M #    ' ! 4 , v  =) ֍{2 ׿v  c  KR  l  
1 -l
 c xf Y   Em  
 ,C' Ejs D1LϪ  v    U  ]   0Qq Y 3W 8 Fi 1ā3  `
   	^ Y  
T m_݂v 5Zi]   kn  6  $x :  \b i"    n      g V N P3X*     }t  H7 0 ] :It  ST z  ObϢt K 9 =4]d   ( N/O u
! Vl 9S  ](   ˊ]   v  -U   ׁ    v^]7  <_ vWMc     ,4=J  . C2! G P {Y/     v9 R3 d d    
 6M=   ?  5 ,5C^   ̈  ߕ <
 (  DIRD  Ee;ц  4 W?  U^UMw)h  ( j \1    =  F ~  L q     G P}   G ݀  
}  lw $9^j  $ ߯  c (B  f+ md  JVּ  ,Po b `I  O M   @  ` j^: .h)C v  yO 7 ȳ  @W0 ;      
   8M S   Bs@    ns  V U\    ϭ
   @  (  x     =   r X`   \ [   +_] lu V Rx\ G B -C SV  W      D _   xm ( /%w P  \kA_  
 D ߪv X ̂ ^   l  l8E SC = a 
   6  Ba  m1   ¬
d A  E YE ;  l cV k$ XY   ksҽ6  wo     J `IK  `  ) > Q a I     B   5    tЕ\ \ u u ۲/ nX`   3 x r^f  &Tu`   .H Π ^ i X F  X5O = 5jX> g]# 	 \  5v  
 	!I }.3  cuWW ́>5  ?  1 `Ft
 =+M8l~ H U^  b   BQQ  {-V U U   I =V  f  mU    88    Oق  D
R@! ~   ! ~ m7    Hڳ 4  @ 0k 	|   $ [8] i  U  ]_  %&  $ :  ʃP  8 X 	a >  m   5 !Z>e 
h 
h  9I \b  e`ɑQ *?  ^0֓  Y| V  )  x  `1 2 y a w  O{  iy^ քՌ   Ӹ i P, #Ђ5O x %K Z# l]\ & Dݸ.  d    &vd : . j' r  4W PtuZ:
 Usn$Q>  l  0    yb   脉 O  (s6 U    |  YD5  C       Mw Ѝ   S<n4   a% 6 ͊ ĥjq  l    ^5     d 9 l Ŋ`    _ؾKߥ6 8 1    8h X  ᛷ΅   V   `  (  a W mZɂp y W wN%0%f  0 ?c! vY  aʘ E *_Xw          i   | YW *   +  - 
b &   
o=r  2 m   }T  v  a _=   K '~H 98Ɖ  ؾ C    # X  
 6 ? K́1 Ph?  
  H@@    ev!  /@QV  L`
   $0 S       W W V    { _N 
e@  /  rVWWi5         n *z  \x   b\x  m?O cC20-  YG䶸D kH#ϕ     #G_  /   b Iݗ % Δ`  a       p i      Q z  E
  wX ڇ Q~9  
+O XKX  WT  O    E  )4 mq Xd m$     	ۛ ` C5E< #F    7 M   ~u t  ` jf@x\ J  Z `     X< c q  f V \ v6v_ ^ T G R  x+̉   .%bY          .7+  LV SښS raE4 ,   ![ e ̀  .`    X і a  ]i èh   ދ       [I   L 5 v +q  Od    + ƀ   f   c  "`   ? 2 +'Y   s  
  Y +' ?     d  O c r  / 7   [ P ! +& V  \FP bu    sXs    dHN: d  2    s=v|k   _  Ac 6\fg  W o  'tK v ;n \D DD*  CLd   R E   >= M N  ) y  ׏ڏ fh  
篶w
O > #PX X  T)  (v [a W   3  s  = ߥ  1  ؾx e 7 }r  7c8 | 2 ue)ۥ N    B   /w  s k  /   8 
 偀       Id DΛ \ 5v  ^  #  az 틇 tY.$  9 ]F     y   ÷I|f   m-r6	~ -Z 6E K  . iaC Y 
  <  M f5  wX    oUr `? LF  U58!  k QņV    5O6w c    N   N    D !   ` _ v+bK "~ mf Rf6 a    $Kh b ` G br    \ p QDW    
 l  V % $  (7  r c" ~6    kO ,  &a    # 8+ de 8dz 
 u Ζ  m  m K {   9  ^  e 94   Y 0 s  3 G $ gy gq 3	v [  b W՗  ~0@  ` woƧ        ) `> ;  ٯ d   
 "    UI \ 9:6J-I x*   Kҕ >=i   Ew% vS"P8 y XU    # 7a 4  I-ѓԻ S~eW =>q      S|jق}N ,a  މe wŝ !蛚 d k t n ˅   o  +f2% &.         fzo g#` z   E   ݢcd  fO  Z +j4:1   ;zΣ  o 2V  F 9 
6j EΧ   oΨ5 *aF  Q  
m d h  j P    Bw a!Cq4QC  6ԙf  P  Ɠ U  M  U ڦގ  F ;    7a6`k 24 C00nW^   j n^ ` X*  
qS" 7 |ݬ_ M  L8=\ysu 77RϪ  B Mӏ8 k Sĵe  xs  꼚     pz  ,  PH |y  p  f.: ~   {   k ր֢5 穬   :  G 9y        x     Z n y[s  i e Z     ? m o)ϓ |ƭ?   <4 _K    M 7M5RL ] }  F  4qEM9  X
N  z  6        _ 9 6~u     5  nW     ԪY  ٳ # XjSЎD lKs  c c ? roh wa?ia 2 |.    l O     W  ?>@  { 5(ld       ܲ r V   c 8  " 	 
  @ cd v 
     e ګ     z  k  t   |l%n  l e =   )٥    &{f Z  I6  gv  v M  y  o _Z" U Ee   {   ɥlOiwἤqiZz"  ai X  b)E    /\ N ̮ ڑ؏ T =     re]+f X Ι f s 嘅  S  g W֘ n   m7    g ~   CJ
B]G   c    >F_ U ơ ];b   f )   6Z
 j SB 2@ /`( vx`  k   D ]쾹 f  m?   Sl8j䶝 |, ޺M	 z    a ڐ1O {  e Ԛ֨ h r   'Wy ƨ  3   ]B  PM Ѯ %  -5[ _٩ݩ L" ߭^ b Nf  c=  p  ? #@1  l  WaL S! kSM <$ij Ԏf   3 n$ 콂 T         X vq
n  ? Dim+ Aօ eY ְ 	 d   < U 1 !    (   f; z  gy{z |    ڞ m/ ,h0b`@r 	  ]V   5 H    #    o~  >
    f   }z  A  `7  \] 2   ' {O6 H y @D N@Q ,"   b .B i        م  W k ! ]  p [ PXW [BYM$Vo   E     Xib  AN   /W    o    /  3      `      ƻ ] ǻG+p ۟  )k ,  h t9
  m     r{	>   t _9 -p          _ ;  . i QjI \ x9m     )\| d EUY ~h+Q\& #kP|
  9 0   
y
    ^ +.dד   Y4<  v      $  V  o{Z ޣ         f      "  n  ? p     o \>    |^ o! ~  lɻ Ά o˻ U? Z \H [U 8. h?{ r _ g^|   / \{/    Zl[ : u 
 n @  C@	  ;g  i m}
   Z ;p fva
    X  S  ,  {Y N  纼//  V    7"  \jH  ڃ( Ȁ <'Wʥ< | 3    ̣'̧ |'_ʳr       (   r + ƚr   k6 鴜* 'ꊰ 'g '?   Nu   .' ) 7  ^|Y G< $q  o  p      mN W <  \\ȥ   r { #H  T 7  | 0   6,   h ܁ *   
 
 Y    @ :  "z|  .5   k  z  ۘ *Ö  Y   ! +z݆  nJ e  q        |2    E`i i  &r0 =  2 
4 	 6 Z       M =|B  5pϽB^E>  s \ X  GA?@w ;߾N  \  +s 656 Rss5  ;s  3  Qf  j iq
	   \QP | hXtE   مD`F    h F  = Rt  G   e  k   i   *  
   fҙ 83ځ~@P     Χq9\%    Z   #  zQ~ G E     nٺ9  X4 -   D ؾ   Qɓ  f  
  1 };D/? l@ 4 } KFf:    E  ^   <DC7  
   l(  ;f`  	  h   յpg r2  =  3"d֎a
(         K O x{ힷ qL  Ja Eo   a   b  s GFǍ  ; I  yp֩ - Z $ ' :d zW3 [W   n 0 t * u p
    ^    [\w   - 0    ϐLwө
 Jƿ u1ݥرs!  ̃Z X\  a1 F l4   X  .B ( م  Se kv G8	 3 A zS 4u  )R Y~   M  XF X   Hǰ j )To k  C7 (  @     xu8 K7  tJa   t@]1   d Yǉ |̆{ u] n   \d  qw|   p	  ǣs
<   s a   ,[ ΂e ۀf JZ  e  ko } jԋ  "ܻ E.ai k1ewy,w \bh AMd  |y   f/   P } I  s  A  t ݏυ  
Xw̽s  v o  I;_ CX =  / ,:   	   ?	
 90: 1   ś Q ' ԫ- {N     6# " '   - \0  Ek U   {_   ]PH 4 zj߽  ]|' {  z *   .I  ?  ]
@
 w  =g{   D" \ T! ! 	⌈Ns  C  SU$ ) v A   &  <DP    <r  ' >   #u   C   b   X &^ Sst        1  `c :v  _ )    Ʌ      M ro,u    y ( 6 s uG  v/=  yw+   p  \  1\
   +q$ wo  vbK܁ #׃n֑  ]su 5 = W #o=^2 * 
n+   n  O ю :      'v    < XOޗ zk     - 7  Ɇ  x	<    <[ z  xE g E U M  K  ç 5 ǆy%      S~   )p   r 4  guyK  Z mwd /  
  `   g~n   Q2   f w Mx8>' m _Ǔ3 y'mʎ   ϋ
  w 9/ޭ+} n/ W/:  b wwl O   t 
   t/f  7 z  k Ѝ   Z _y = '     
,     ݕ F~  ï b J  tw>n  Gy +7q +t پ     ?  c ]O΃h ݅/L zj 
  w	;c
(E`0  t  ?  4  h|   yy   h x\  ' w       58ݔ         xBL_Ki/wX   o ^^b h I\v ۥ ޠد g   {r     v }J       , Qk  W wB}p/    
  G   p   U\R  k  @   P + ~[ m5È>a  E  aU F 
 $   a8 nR? ? F F"P   N P    f c ʤ x7 r     ,    mh     U `w  G    o z̞	  Ez        ( W ! }  ݻ   - -r D~ g    ޭ/  { ͕  zĞ   {  §    ^Ɏ     ?  ަ 5  ^ w՘4|     ȸ ĝ W _{  1~ 2    Y^C D\ 8 @ p ' ] R     D  H@  D   
    
 I ,aqo ̗   -n >    
 L  {" X   2޽  i ] w %{h  ' 7  ]   C X~ / 
 A          {?xs   |   {w
   g    _ }  7  | <,` Y#_N    ~ͯ c   yW   =_ů Y    ϷC  /8  =, Q    W ˀb? O }E ` Y x   
XAc_     > Dŏ  \ W yx     q =
 *      0   0 u"@   I II    g J@  w %?$
X  x?X }z_)Kҁ _       {YCś     v     { ךg 	|P  o I}& -  N|U   2}}<҇  wJ    ]~m> w s    }Zo  Q M߆Ĝ _ O  zC oO |    W}     +     =jK   c   B   Z   ˅l        R    p V Q  " w   C"h   x    H II   ]HX     %  ( + ~    H$ ч| ̾    ;!Z  Dz     j
  s    W  { 
 g   Z   } 
 w  ~   1     {~w  _   ?į  i0  O a >  } b  ~~  w  y _ ϱ%Y     /L   [ + 4  =       Q|    ={  a{     e
 ?    }  ݟ ')  O       _* `  ] xh
  
 + G c   0d 
 &@     2\= 5. w # 	  ~D R  $      S     / g ˼ {>     G K Q  7@`    
 mF   _ O     I ~E Ɯ   Gߋ  R       M  yE    쇙{    
9  ^
o   +  6. 8=  \  c   eR `  P dEd x D    y   y@vA: 0
       @
       6@s  9    @j
 W'< 1 .z >      p J@   O    nl S  _ f          K  o ً Q  ?@\` / 7  i p* _     Kf    =   O    
  e:]  
  y }  ₴  8fq[R S[ j g
   >  9 \  ;|d ~  B 0
h ]  " ?  xR    @ / 
H X> " `  * 
   )A#h     *  o $T(   D  p    < < ` D    '  (  < z D  	R      Rp 'x  )    Q " k    K  ܄   <a.  sÂ  Ȣ1Ƃw  pEU 
 W / x    E@0  8t  P0@ @ f  U         /    r Ax   | H^  XԼ)  E  aS N    e琠eΤY   6  z  L5  D      %NڷMJiڻ $ I %(5 L a @&  ;
 1  mM \ vF 	 A!L   Y j &H b/¯D  H X   0 ڡ

 = v ˔ A  ݐI ` e   FH Lȥ  F  `$pP!KvQ .fͼ <M3 i ]  {fi G ȭܻ    	+   ˙	>  a    |> n    +Vw{   g  [@ ދ   `O  	  08@    #  d  !zJ    h "?  `     	     "* 0  6Bj  N&  R E   A     ;   "P  B * 2 {6 - 	J ; #jE r7   . k F rܱ  ,b " LҦ% s ʚ  (      $  8 s*l 	
4 	 4      z@#o G=m s _\ (i4      G  $ &# T.Q pW %j gźһG  L 4
 & ޙ 7&ؔ kk     +F<}  FZ	 q @ -TYޘ     EHZ  
 Z  t[3ME |D$ M b7W:  UO`   A  )Y   N   -   ҦH 
+]ޞ  ̆ 
    #r  31~W x9 7 {? Pd
5U o }R9  qYa ( @  h x h      ܝ^   u馉}Zd H Ѫ        k  F fn Nw 9   z]  Vn u    
y_ +F &  
 & k2ػ  D<g4} X芙 XN]1       n}  w7 * 7 	J kF[         # f  6    x s   s G  ߾   u _ J  ~  J ^s       '>}  J   L e. |(Df 4B( T:C  F VEC  ZZ p   NO K  ֹ  
!zC  hR @\!PZ^|73y
L
@  F UN  "a v  މ_)ݲ ؤ Q* 5 
    \ C    
QgE   Ļ5    " 8-+b - S۴ V 
r?T    C^  Ca     QRo,X 8 o    y C/ M< bD R 4   R   q  rMV    &_    @ `  A	ꩂ #ó Y σ   & xDz   +%"  P 4l 
 W   R[I  H  Y  #    2" N f L TBq  , #?yE( rL  9{ Nb֎ :k  =5aiUC  1  ؔ  c H 9 Q Ad q    % x   F  қ	3 h   Q    !  Q x &  DN  3gL   8@	:  29    { ' zZ͟* a)  $N "  e    u , 0 $ cF   \[;;HP  c^  
 w 'E  H    o Q  T j    } +B  ɼ oV +V 'H  p  4 =  OŪ jl   "
P ),C $B  י4   j  U{Z =@% bs M 	 N$  x  My ä:5 o 4 .  3   ^
! ,  ~˔𮗼  w [ z       k^  T&6 Mr*ϝV a F 0 B T e{     4{ +   h   e a ؔ Vc   7] ^L Tv Éͭ8X  K  W ! _
 Z X E,s \  y  nF [ r ?&    bf Y#C  N 7  %߂  &?   ] m  q. R %  ]       Ŭ d   :  b63ѧk Zt ( :SY Heʅ7ܽ &t  3s k h &  L>   |j VӸu         .  O dHsn  m rq j{/ F  e    ׽  c   XT'   - k   }҄Ώ  n^ s &5B J L  u >A 2  s  : AD y :  \ s     ~!`<3   8    	r @ @ eQ  8\0  xb   `A ҹNVn  Lx5 y      L7  to plB  A X3 Bg 1ϴ} R  G;;      	=   b,9h r:~֙  t   O   e ; z T KH   ;B( 
aHC   |   .T& , S  UO    X WwKc RJ    B tM  al  &er Jy 鬫 w  c&٣q    l B `U zU    ڴ ߪ  M 7   (&] y% Pn  V  Jxp  \ O I hS K  ?.   j9  Ulvn     l&}   k S $c=n >~A$ J1 8  b Y 3Y 6  ?i 0 3  ;26 i?7c!     `@qô {@	  ˿	 = 6 :?D=
t<  < 7 i  ;  > `A      + ; k     +J       7 
5     _   6d     	B ; M{[ 5Cj3=kK  A) 4* <+|7L  C i4Sɶ>l b{ @l ,     ) 
& 3C
  /"sB q  [ | 5     A  +C 
K $; A1  kC ;1 \2ɋ3؛3 > y ݈ / ;l <t& 
%  C׫B 	  7a." 
	$ ; #% 5O$ "#   CU  F  T  h	ƣ H $O !YDFZ 6 C=\T sd n3  >vw BlY     D_ 2  S D 1& - [4[ D ~,E  6TܦUl 7BD   <    H q'Q ER$ t i H 9 s  " [   0p2qEfL3@  Ґ  (  (5 _    ?  
   a - D \ 
|ɿ ) |F4@  l"A    !Ʒ 3Oc*     * J O\F  G  Ho 6   VD  s
 J   C} L 7   N$ 2 a4   I   u   ;A $D8 F
    ,  ~ 3 * 1  2 LMS  G  2MY+DլK ح 4 Y5^[<1 Bg M  HÌ?J  wL:  
 	/ " G    / bI t  Z  @ <OJ|ϖ܍ *O=;B:  0   | ݌ M \ Ä e3+   m G  ˢ  Ϭ  4P쬒 2 <I 1   ? T8     G< G -G
   P  Џ ˒   t +cM>å D ߣ  ĠC  p L	 f,4    Ω NV NYB  3  )LRu"RN8$   ǳ  '  (e٣R+; =  UQedQ=  P
] E 
 e 9\ɠ A      %-.3 E K  S `S KL \N\  \ C     &  ?   J \ 4 Q d t# u  v#   y -{÷  LM   ̀ ́+            А8       7   Cև#9 p  {  0 騹  9=A; +͞ X    iE  CM     p r5s St 4 ehWi $    <  ;{ *q5;   :      
 i *k K􁐱        S X   @    i]  z X 
 |ՙ} XS h(   V&xY( Y   	  
	  Ľ  OE   Ne tRF=EG Ӽ Ɯ>  
. zS=  ͌ T ԩ hě  )5H =oɽ  - + >W #  q ʹ Z%  Pm Qu# 9  %]    (   TC=  | O ¶շe ŽF B (y\K dZ9 K:  -\ M  Q 0 j ۫l#,Q K] J&d M    6=@ M   RUP  )kP
Բ]    } -  է ŔT  ]ZM ߵ\  \ %\  S      _m O C  5 T_   4 Z  \R   <  ^ [P  #
  C ][ MG U^ m^MM 	 # ;   }  H AA l ,     . H T4 S} I  \t Iu _   *` %
c   _M _ a	         ] 
z SLmZ  H  E=\ M U    a h n     J? _  \ ` c  SK GJ  )b\  $  `؝` ` v<M  @ EIȼ  @
 ]R U ^# a@n fb 'ܥcN O e = m]  c6  A  & D9   L   I TB% ˕ [ a D      `(    k   ) /O  ?  -   ]1  b7 b8f
 tfy}ބII ō      0M   W %& YF :]Sh~    ּ_\ՓI   L0 Env]   3 ѫe,H XW\ 9 Q  ! ܬ (hڼ  H4f< l      ` N.N
N   ]v *^ U N5 S$d%  eg*  ~F  |    R]m / tO   AO  #mO6c U  $b  8]j    5 6 `Y d[\F  4 J   $-iE   i4e Sk1 P  1u 1 ñJ 1 S  OL^h %kX  H 4   
  
h,}   d  /Eh_   gǶg  h I ^ ܲ ,{   [Ϣ " LU L  o k  S em $ t .    > N .P 꽦 6n Ը  +f     Q  S
  fLJ5e  㯆 Uko iN~    [\  ˷8 U
 V  W 8  վ  { 7   ټ+  d   	 
 H " c ϰ  }`o C 80 `: h Ap   p  p
O   L
փ     9  # Eq h A   ]7 g s    S     q   N q  BЂ  r# X {% 
& E
  d r
  
+ S .  4'r/ V }p@ m 2    WW  @ l q6 pGxs P \ *> ѻ!/r< 21   !  ?  @ 	w :G *4` p  NǆIߌJ s Z $ t 0P /   Q7t  D' 8 Fχ    u0P k    0 sx ]  _ 9
   򶈸b 8 F? (     g vp o  u[  .  x
^w m `  R' So.C`t _ |h9p  Z  
 & }O  9p@    x]   8   ;  KW   
 N eH pH  g k
 #  L *  0 }  t  wl 5 ' F X   z3hx!xZ_ ypC 5 0 	 G  Py [;I   GH b  z ? MX$    w ; {$   B   ¯", Y q +  X e@      lj {=ׁj  uoOz(_ ) p=`t1 O  ׅ }5  y    q8 w   1 *Bzi    1`tGPx        t=g         } '   J`tx   ~ w     w   +@ x  *B &  X qg 0  h  p H xX~p'  ]  y   .   _ t o ?"      W $  ' ]  $[  M h   @ ` a n@ = `t `X   "ЀJO     : # &    ~߸ k ѕ    4  =  W    >         nA@ FG< T 	   w˟ }@	  HB<z[   x 0 > 
 A'x    u  A+    { v  10.A)xb      |  0
j  7 Ă  ̑ G8  &   h;  D!'   ⽂ A      T   h b! r  t!- t\    va"D[  ;`h ]  0,Ђ~0 4d    Bh   :     Bc b 6d  Ѓ/P ;,   Α? G 
>h     F M] CO  J` A   @ g /   ! By ! bj  "   F   p
  6  GT   f 4 
q =$L      KL g  X
 ! 

 P
CS   =  GQ( B   `2@  pi,  "AH X {@   U   P+ m  !Xĉ P {x  >  q - A X    H + 
  
 dz ڂ@

 aD  ŀh   0J 0N?xX  X ~A C{ 
   J 2 C       3
   "  F  ̿ V "qa   @  j kD l 7    V  	  qF   
  cy!@ % F 8`  6 F   "ʱ  ;   x L n Q  G p yd m ٽ   B K` ƚ  o\   9J \Z q     @   zr20z+2@5 u̎61휽 H\~
rd  u   (gD    Od  Y 	 Ȱ`!I  ܐ9 A    @ L 3   bi$J  B   d %fF6Xp 'p s146Ő8%#$Ah P n  d l " C
H 
PA D V    $H   A  P ( XP   =I';   1     "% L F 8<3  B @ 4  )   <  Q0  M #;%B  ۱d H H  l (@UJ U <`  { S*EZI   ||
 !TS	^ 
(  X =   c     +&KPy+_s     j *   q  O  sP&  & $O/  e5 j  	   Z  I   ]    L !
*G& ?  rd Y
  5 tP&k  \y  K bP%P   eLV .i䄱u3c    ,;ft  L   #8   EG @v &  0st  ~E3 9  b Ѓg@    L +3Ӵ̡Y4EV# W  
  e 4     @j ˔v f
S h  +@ b  z0
 &՜ ڒm
M  5 P T   	 /  = {3mΫv9V   rY  P  'd    q K  7  f G   U  Vr iN
  =4 B1  *)  &݄   e| k톯 V  [3(dK      [|Ý M I)V v  H = ny
 *h  [  o    ۇ  og Td& ID o ,    V  .S  A L. s< <#G 3  ϧ  P O{ _  qM( v qs  z O  ? ;m=  E t  u  Hz9  6 $  i ̡M   >K :  kR O$ A{ kۭ - J R j 	 됵6tf8R
 d 끞  6BR*  ! N RZ!s
>  !   Jz[`뭁-  Dh l  M     A/Zs ^   P0   hSB  s     ZhsmT    ? h U ׄ E  u¾ l[a   ݶ Y Ƨ re      }    . F9 
 c (  
'J h!]. [<1  X( e_4I 9j  h5ko, j A >     =5[  ȧ ե    O H g 
v fQ  J h- kʔ   \Z;w)+=6 Ԡ Ѱ @ T# m ) 	 f  h۟ )·*E	h!Ӥ    RvM (2 b
 2Sq L   *3 I  @=]T  j  vPQ     6T= P g, ,   W d   g 4y.z <*C go   S$GR  ]g`   wA%   d$ > q L
 -iT  F $Ӂ#h 4   K <sZ Z3  xa h
D f	  O  `4   2
G{HV5a  Uag	 g AS Y;  ) :9	  JCG)  ir "U :S hXU ( SSQ   Q  ≠ 6 `    j I 85 Z՚ U  ZM   : ,jK    UU T bͫf ˊ&Q       U  ?7  %    ReC  *  Q MD  r a ﴳ 4  ZklM  T !   S  >  S  T vzլ g   -  T Hak X J  2UY2 ^ kK) u l   VC[HK U mW *[ q  t   R6 x jUo    0 ` q]  7 Vli   ne  u   _FT; |ݰ
   VX \ jE# O& rV     {c 
ֻNњ ī?  &  & [  7 m [EUM{   5  	` > X d ,  pe EE  n      HQ`  5`   s  8    q  30  @  t`     A 	  (, Q) A 	   &@Z P$-   5  H& L   H    E = 	   fB"`9    Yid/ ,y  
d P P% 0 XW5E'BX
H    p%$+SH m a $      ܗ TR  	      2   
   ]&m M >'  ު  A  x~Ngn H  W      m6 K  ,      U              D   D    Έ 0   C:\Mes Projets\repo\avsp_server\src                           
        H0Р      #     $$0  A 	

Mock_AcquisitionSignal.wdc
PCS                     B      9       j                  01F280051n                  57l'A ө ͯ 1: o Qw   p
:  (      "      >
      M o c k _ A c q u i s i t i o n S i g n a l                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               PCS          	                   9                         01F280051n     x              >        
                                                                        Έ #   Mock_AcquisitionSignal                                    / a<&  j    Ï6l ٻ      t3Y  $)8Fǃ y q0 b               
d ' @N ^A  bӅ  b 
*8`1 ƈ"  ,    !  2r쨡   8o  A FL f  	a獙Dڈ C L  2  Xu I  PDT    H   I &N <}  *  U XO  Z 8v  J h :bƴ1# N 2HɤC  Q:m [ $  o   ͯ  R jY^  gԼMz kرe{ $  k_  * "I
@ xH $ (  o貉G X  }   . :6  w N}    w    V T ޾  V <Ѵ     	" P 'Dc     A  ć   ; R   
   4 , j* J4 D  c   "t " 2M    i 9  c/; PЏ   ! . .   h   h?  焈☃%bT) G l|Gy #Z   3_ M %"2   ʈH   J  @ $A"N؈ @   3 P [/     2
`   T37I/ t l  0шR SAѭOLѽ ( tS : / "*  w4 G R X | 8Y #A  h (M3  +  r 9  L]{$ L   V  tN: du ;  sψ dP % 7Pc;      L =w  
    4F㙥M.  ~c먣 أ7 +5 #Q TM\  V8<M  V3 c j   x    I $.R  Ȋ :  g F2m  ` ! ) (#K  8+'ĐZ*7  #
8
A	  0 3*XbÌ:^  e    ; Z ( )   E  b
#B {헫%s 1   ,  4o7   v    ګ     3M  "   
-  " + C  )k  @ (c  -W tX    }  t {{  x     G]1   u }  > l e )KD O  |
 , ` :  Qt 窦      "!  KA'8	j @( 3  g B z  HII[ZӞ   M 
U
 ղ   uM,a[0w    Z  \ LÂ   ny [   B  ap ;\ X B ee   ^* 3 Pse  < :#  t C  X D  .   ݸ,R  +w  S  40 ͋w s
  (} y Z^ T2%q '
    4  C;  7 ; ~~ M NB   rfC   p Q A  B  , 
N  @ ֬    Ls V AzPk\    6 Q2m L .[ EӤ  vÛ    R u؜[ #)Qi n Q   <$ m  WR 7`    \V&Ͱ     U $́ /H|  `9 `1 ; X  qN&M dd ` s KmhÝ>v  \ ;1M" 1  /S ` A  )(p
B  %q`JQnrK:$         I x  | "*  $  
Dc Ĩ  JR TTeex 
  Ԉ   "     ő2 #+IVPZd   ^A J=   }G=   g  Ző d    fv8 B 1  I   0&0   Hv     r   JViAWJ &T ,AHK  e&5mZz  5 flaKNfe
Ü 1m([5     5՚  ;ym^@ : EB 𒘬V 
 y?  4"	XhCEQ    Y  E3 Q x j! (
 {  T ,  K K   V )N/F    e 
q *# *  C-   T  H      Uܮh `=  Z  Rw cA j H   H P  Eq +  WJ +  #Y6  WA p % 5dDv  d ;   m6 R  @   
f      CX  - ]  ̀   V  = x[      m Ǥ     U  [ J    nYԜ _    AD [ A;Z lFtt [  Y      Z 7 z J K 98 
     ]TnpD 7ֈ ث%^  ˼9  ؔ ^  bYG 7<.$ ~L D     zO⁈Tauݳpm #Ċ I&@9P A b  S FN   "[JQ l+1  1  ̳a    C;W  %  
k H w  1C pr;  d
 g@   n YqADP jV !   
^ 2  G &  Bn=q͎\ ζ wMoޝѠ 61  s  K  K+  Vh 1  ^  . 1Y q2 n  }Վ@)  r *} ^&  v $ } D      .y8  M 1T] =A~     lCGR+ 6 K         (  A        2   
   
B 0g w !o O pzUISO  N   6  Ai  * I  
p                           

Mock_ClientSocket.wdc
PCS                     B      9       j                  01F280051n                  ̨   W,Q z K  S 5  5   d        "              M o c k _ C l i e n t S o c k e t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                   9                         01F280051n     s             >        
                                                                        Έ    Mock_ClientSocket                                    /  <~  -^L    {  C       X \ 4  
  y   2             
d  @0 %P ҅  b 

*8`1 ƈ"  , <  !  6j̼ C %3uB     3!`  C L  2   t I  FDT   5u * )  ͖9 *e  #ԓ1  1B  ;{     1m̈  LP2i {  إfϖ<Ɂ 1ruά#sⲊ 2 ! 5o0kI  i ?D	2u  e v y H͸  ҷ  =ʠJ 8j  =V hAk w >M@    +    E  s
7  S |go1 ӎ D |p +O TQ 	f Im  
40 , X2  ( 8lp \ p t,dp      Z
 K$ 4    [ ȱ 8  "" x  9  C+;.Q  ( |r  `TQ=   :  so$ N ( 9X"F 2z    qG3zLA  L RMO@  X. 2"   ( ~)E t  4   m; C    {    T, GH/ )ۤ   " 5u H  	ȁ V4IլPG-  ; 0D L4E " 4SM
쀾 2    ԑG b  !a  ȇtVR 0 I &-yL 2K2  r    q 2 83M ֌7   HN *   ^  s >  P %  Cgc    у  t J c ^ 
 S <z5       c tb B  ֨@  G]K [_/Ƙ 9  X3 ct ʄ  Ɂ{
 +6y P  , !"   0  -A{#+5l + 6   - H  B *&  ©    ~   " b
(  F "    gt  a8 hc˙  L  9#! ǻ    ; w V  
":  +  )B b1԰\1 oF    n }+ 7  
 `I
o C jx6 7  ~= T  Q   _uw {Բ B  ` |S  p     X>  `      I   9*
DDx 
 f@4  h  ^D     j֣RT `5 i k    ֶ  -mkk ۼR    DWM/ΰ o    ı0'fp $G9Ѽ0sQ  @v C  .f]JG  E%uY] ^    vf ]   ;	j x ^   (A*" 
  v7b Þ H @ tϋNA w* ` 
Ǳ u *Zy$ h    C  T z#   ) x e    W)=  5 ;Ɍ Dѥ   A ~,0i4[ 5 Rpj'A     ƃo[B 6U  m[ J T T2Q [t
 R0  
 p K\   '& MQ<   Ʉ 	K C І8\%;p; $D q T 
|@|r 3 [ܲ  !t<A`j    2   8E   3"	Bڠ  r    G8   4 lw   c D0E P 7  Y! Q R  Ёrb   8 i 4 מ d<䩈
 : "  pƪ *̢   t5WQa h կFD-E]cWI ı=sĨ%  ;V/ e*_ 
 \x B     ɼ u  Mp   q k<    9B&o  \!8z c  } O9  Ū  Y D        (  A        2   
     & 	  ~9    Y  }  |SY   _K  C B  l XY                          

Mock_SignalDessine.wdc
PCS                     B      9       j                  01F280051n                  >_5 a
 s d Ś UVn       y (     "      
      M o c k _ S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       PCS          	                   9                         01F280051n     t       _
      >        
                                                                        Έ    Mock_SignalDessine                                    /3%~5   * 0 M ai6  HuW   ?r    >!#     =    P
  W)    
d A  $  H` bӅ  b 
 ,`1@E    ,    ! 5hވiS :jޘ	q N;;Ci#     e   Ȓ'GDLcf  :<   I &N H 2}ꔤIy" љtiӲ	ĥ[׮ A~ [6 E } (2 a 
 m     
O `  -   yo s\ /   O  \z a u m )䁒] ~z )   ʳ 'D (   -S   7ϒ`ȴ  J  s|   n&     a  c  _   ڱoG^߿.     "" x  :氃    9qP   `ȡ
 6 ,D$ r Ȱ N:! 8 ` U p  Lp  H   F +D  @   2"* C  ( ~ +    ǈ  h  \ 2  ` mDǐ|* /q
@ ]  2 @JE 6 ʠ 8   ]6	/ͻ  SO "J    {/    0= ?1 ZS  `   4      0>    2  4 AH BCڸ ! FS 
ѣ -QPT ]  'TT  !   P  1 !8  F \ I    )e   , U  @) R\'   D  2w  s b (  Wڻ|  WN    }jѤ C
   S >F#
 Qy?- ^ *   L  4ۈ D ^   o8  B 9 pÌ z qD3E :$ Ñ D  M@ Ȣ C
m8#"   0  z+5   
 
 H C *  j )  ^x9 kv֮_Sry
(  F "  Bx[f  M  4d C	7 @ ߂ .Vz  & (! hCI    1GK   # ;`VC 1  % xBq w Bx B  (  P 
5 `ь  + b  "" ,= % b Ɉ  
R y   n  ʗ- P?Q  h g?   P   4 2  k2 X  gxy d L  @  PCS&v  $ ";  f  
d     >peh   и9Kd    5  
  C2  ?   BÈ 摤 j8  Ӣ6    o Z V 6U  lZ Jھ 6   2  
sb  n  AD  7   o 
 aF   o Qv 1 mX  G _m " z     .fj8d\*   i B  r /e/    >   
+ C@P b4  0ٛ[ R    &꧀	 b  +   pM   kB  l   	 'K]  $ Bif   Y3 | шw    . 
 8"   8 iP  Ta )V1^â v   
,m 汲N3N  e  ǽ  o k 7! Gr  d!'  DF oO  ` )  
 < YtM    ( "JR> \R  @V  V"  ) f3 Tˌݔ w  o&!J  "  H.!L $"фH6!N  " H>!P $" HF!R  "1 HN!T $"Q HV! ` c  3f   r]   yf4   Z  SU 6  . EÁt  V  h  $ wz+  G=# D  	 | ? G 4lc;h      a! i Z5Rn8  [ 2:  -w'ͥ i͐3  '2 G i   TH   * 
h 07     # IHU     ێ  ȁ   /  L @  F 0  \ > !ё b  A V׺  Nv ݈ ׆  .xãC񎗼 5 -  l  S Y  j`>ӝ }    g?!      ܾ E 0G
       rc r S4X2[Ju R
    = `   Kޱ / )h       4 t 0+8   汞  ,) Ka   fqihz T   P  j Y ! h @  e I  n   ԯ   ~F j - p H\.   U , i  ,kDˉ G ұҗ n "l   29  Ezz s   "* TI *'5 K  >X /W3 Ru`\Z  "  Hd    "  Hl    "ɁHt     " H|   "	 H   !  ") H   #  ϊ  r /A    ? hin L  R #  
 
b     %  hh 
 f:taC )H-s$b  ] = %    "     n l  . v  ? AzM= 6\Ժr h  s 

 o @ = w  }o| [F j; կ ;     50Z F: s   > 	 Es  @  _  f  D<;2 .'N1   b#Oy 1S._s m Z   -[G 8  ח #?_|· I \? C  Sw  L, Fˏ6 T  kJ  e    Ϟ /  C ST~   0 l ;  B3
 ˋ c c 4sj     xX 
 ,SÈC   `5  r X Y# ຢ +.]S(0r    
  [  C6  L `     C  z c 9p/  / # œ ƃ  h˥ ;   
;= H 
C  i  ؓ=    	ܻ  =恐 j   l+   T  o    ʲ :7 z  { 4 5	6 6	7 7	8 8	9 9	: :	; ;	< <	= =	> >	? ?	@ @	] I, K @     # Ct   '  EC  FI , 	  	 !8   
0   @
     pH    _i &⧭ -%   µ K  { j     a . (     G5  }D (d 8 $ C    < 2  > 7   {Ʀr F[  *  ɾ Œ EƸa̘  F 8F PƘcƙcIb$ P %   ȇ&  d`  I   , ɢ,  ;I     O Hҋ  ?    t  ,  f    9?k  8  TèB  H  J  
Q          (  A        2   
   ݦ < s  4     o 
B   ҏ 61O a5 :  e R  V V_                          

Mock_SignalEnregistrement.wdc
PCS                     B      9       j                  01F280051n                 M   Q 4?  \Ї ɍ5K  2*LS R(      "             M o c k _ S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                   9                         01F280051n     {              >        
                                                                        Έ &   Mock_SignalEnregistrement                                    / ZS ?    RT_\ t> ' h    X    ԩa   vWu           e    
d "  \ zH` bӅ  b 
 ,`1@E    , <  ! 5hވic  3h Щ Ӎ 7uB    3!  C      e   Ȓ'UDLcf  :I   I &N <} ujիI  ! 5o R +  _  =~  ค/.F   @ }#O 9n  WeDT      W hA_aԆ!G$  5h fz aձ<
  _̫ n Y q   " 1 <
 XyB  (O  cp   dr
 Qd  ag1 ~  L#Χ F     <w`Q*U 0    m8  S0  K     
   #,; ؎?@J       KЂADz-  X{☃%bT)#ä$  '
1 #    
  \ 3     **# 'M (   G  4,2	   nC-61kӍ7  LʫL    ;2 4 - % {/ |  s dX 3 d į , <1     ,pM    9;p * (/4# 
  0 B|(2M  AT h"J :Ʉg$  q
  
E   + c1 * %   g)   +i
@KJ  -"0 * Z   Li   Έ ԌEe     mw^;      T I  Rz4 9"  
1  I2 c' ԘpC^#@ 1$hB  Ե 9
:     Ċ.+"   0   z# 	C(#) Bp  :Ҁ  Ɗi  n i    ) Nx   f  4  " b
(  F "  ~U i3v :$~ va8 h F  
,ٸl g  pz 8  * ; C
   ف   I(  ʁ  2]q   0  S oG  mM  / +e  Ѝms     Zۨ :d ;  AA( I{O w }   ]/v  k6!6#,   zC
^ 2$p c  y  =b œO Be ] Y   g1t  g 	 ,EC
  ´  {Qc      @k\   F $p{ic   6! ɍnv [  r5  n c{   #L!WP  ; -KI s   & @s   ; yKt )  P D Y #   r( +F$K  U   A    jL& H 9%O ێ  *FĊY   N  D!ab I 6ԉ   e   7 8 L +    &Яe/ Y^     L=  X B  M-Jc 
  >  }  
&ص  mle;%    n8 Ȑ5x%H' a1s  L "	B @%! ,    s  &  	S      &IA .y K`z!x
@ 84 (mk N  J!E _ aD &    *Ab :*lJ  
 H   ' h  / t+ 3  D < K c    D < QܧE     Q  I	, E    i   +j@l   2  ƥw ɀ4  t "jdc   7 T  {  "b2| E z  `jHH  k0	H  *   Ȉ  V I @V6    o _2  N f1ZZ   :   b% Yљ U  9z #cy  M,   Y  #3 a(EQB 8 Ŷ T 	   *    l    A
3 
 6   My !      Ѕ0 !
 ٫ -`Ȥ 2/  gnG    st Mmrӛ 4 8   6    '橆z   iU B O ӇR$h ʘ  5 ϩ E!цz   (G%R &Q6K$    ǝk    *Sߩ') K   2uA `q_\    ^  󰺮 mU   Dp D aP  à  d4  <    9  n  t 
   A Ȁ        (  A        2   
   (m"   G W  a%   ts  f ;"~ 2d  IY 3 i +                            

Mock_SignalTraitement.wdc
PCS                     B      9       j                  01F280051n                   dlAr s    \v 
   % ΞeuW.      "              M o c k _ S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          	                   9                         01F280051n     w             >        
                                                                        Έ "   Mock_SignalTraitement                                    /   .   i @T R   .@~  1A#   :/  `  d  o, &H            
dP  @f VH  b    b 
 ,`1@E    , <  ! 5hވiSe 5u̸1 N3t~ yc& 6b  1  L <NY 䉈
Ҙ  Ө 2i ĩ  O P R HҤ Ϩy 6*۶x     CD    {  ` q     qD l@  r ę5    bE
    8"   7 PX  ͮ f@1罏 N6= r ̵ { < ܈V Y x  "U @ybW {     { \^V !3  ,> 
  { |y     }      7   2t c    X`ȡ
ӯ7 ,D$  
 4 ( 9X"F 24    @3LA   p  kL   2" B  ( ~
l    /   H  X   n+ 8ʀ j +
 Fͮs   d    + @ 4  ԋ =q O ,= RM :  "*"(	4A:  bZx !   R 
 У  t $"QDMaP
mq c F X 3   "} 3 ! |- $ \2   h ]c   *o J7   2
_ ;7 JE jȠ l   ]6ISZ  W\ <J [  Lo=  |O- ړ   SAwc 9  9 P     0 	T ş$@ H#z֢ ,    0Ʋ    B 2F 덄   
   H C +  j ɫ  xaP7 
x n .:eT    
" Q + 0" Q X` 
F   x1 ƀ 6F IU     ߸= 
᎟   U| 5 YG 1 ïr "   X%   c E X 7 r f  LY{  w  K7}  ޝ3^y  Y  =1} rM  uBZ  @5+
 A(  q-3  c   䓢  SVe _ Y  k 9  򹬝z  u[i nzU n zꪯ z  7C   . 촭 m x      
DkO  ` )  
}3  f    " KU   W  ^ D  d /    L ' m	t  ag  e 
ݢJ␭\ ![ b R'x  u   p ? " ¿  5%QP 'p	, x  XD2  h0  Ӕr 0=  Let ^ dF3U g_	K  4󽠊7 b   * `~T ִƵA^qKA  :    J r$ * !f ¿*y L - 3  r # ٪^ `     1 V j B p b   w  ]   O    k< x       i tj   R  9q S  I*    mТZ  `8 @ x  dL
     )yxs $    ׾7h/w  u Ǳ  , $B< Yϻ  -M  i Gţ L     lw +F  * EB      ee  Lܠ   Ah
    6   io	 @B9 R ʥ   HO   ,    q   e  a-uu  D s j / d   -Ŝ E  ^И  f x N  " * }  $ i  a`   z$z V8 .k}   
@6    C  )'  L Rh}
U 9     O@         (  A        2   
   tV <c k,%5{i;4^   ZF  ނR  -}  & U                              

Mock_TreuilClient.wdc
PCS                     B      9       j                  01F280051n                  Keu D  f a  2 Z  QBy7 -  l     "              M o c k _ T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         PCS          	                   9                         01F280051n     s              >        
                                                                        Έ    Mock_TreuilClient                                    /Ū!     s^ ?}  L$ _    E      b                
d    j \ !҅  b 
 ,`1@E    , <  ! L 9f iC  3o 0Cg  7fB C      e   Ȓ'GDT   5u * )  M : .m  #I  "D<   X f ʥ[׮ A~ k6 E } (R a 
 m     %O XpŊ  強pD}CS& /   O  \:   u m  ) 
\ vj    ʳ 'D (   8   K   $V l >0҉`  M g  W  [w  )      !$ 0܁E   /? j   xS . KK     
 氃   9  ) Wr   -2
i  * " c  Q 
    :, P 9RPṻX    @   2"  D  ( ~      .  h5 J#35 ` ͣ    5q
@ $w[ N   c  ,J >  P   + =
 , B}    /     ] HN:  4 <)     
3ܰR   XD|ȰP @ 
Yѣ 4E $d  q )S+LH!  
I    7& r "( Դ + D- .5 R   4  oUC    $  ;#   ,  "  . w H@  r  M {  OH   K   ց8 O ;h S c  8dc4|    pF4l^        MІ""`a
3ʰ  7 Rc   
1Bp  :ҀC   + : ` 
3 x! K    Z ( )   E  b
#B l  r 1  
sr Hц#!   2 C+ [ ٱ n   ~ 
( 0b   C5  KڧH x % |2 )Y 6"+  RKy   r#:3  vgW2w jsxM  y zOWy 5+ M GS ^Q 21 y # e B# tb+  M 4 ~  Ӣ "! ֊C'FC Y   m y
   f! YZ  3 Mh   ѐF5 9
jR   n   q k   "w   li[[  7 q kvS   7U 탣 T jq c  7 Fkr 3  2   }.tA4  沾{U+   L Z   `v 
׉ % ܍i]  ݂ w  c9^   E 1 {  D  D QY # $ 
; /R'  @*U1  /No c_ w
$
#;J&  ph   >0a I  $    P  #  p    mhnq     =MQ  V 2  \P'/  *yI3d   NF 6  
nr+ 1߀I: `v8O|  ` < U H "   n   !  
V  *p  
d8; H v   Ach  X"     <
B  *q(
8 @5Сo/x  ( F [     -n v    q    9Ái dzȴ  K  f@   4"1] t7F3! 7g4
  h 6FD} #U   #  "y@  2 U % 
    Qș  .Q # ꚇ  wq  t    H"f   Z  s
 ^ غV   ( V  3 S}kS# NS fM dO           (  A        2   
     u   f묥  黴  Z  \\uV    6`  -   *u mv                          

MutexHelper.wdc
PCS                     B      9       j                  01F280051n                    Y Ƀd˩ +    Tr n} d) P "      "             M u t e x H e l p e r                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     PCS          r                   9                         01F280051n     m       H      >        
                                                                        Έ    MutexHelper                                    /4  H Ur 0M +v`     E  s` cu<  fL  k  i> Z    9       
d   L+  !х  b 
 4`1@E    , < ةc&O3m☙  :! 1 H1t 	  ]&q  ,y @q樹CL բG . HҤ (x  ʖyl A& 5< " ڵ   ĝ   ȿ   !"ȏ    X1׼E֝\9   %/ !   ǓA_ hA aԈ7L` kƉ   X     ^    ݜ= &R  D k! +O TQ I_ //  ; <kV !s c*7    pׁE HΧ?_0  :C  )    "" x 9   %;  Β   ! .   <   : H "  $" 
2 *   
<  4   : %    - xR   Q"  F r !$q H  $A%'  j R8Ҭ$  1>  Jο P""Q+# 53 (   G ĀCl2	   jC
 ?e+N  B   
 ?  #s4E    @ ȋ \s #H fiE   `  : t R?ӴҢ  꽈        N
,             :u h?X Q    ׿  v <)    	FtL  p j(|h n @ 
x   & uHL EYA\˝q   *   
 ʴ  K & R  ( % L4=    @ ΁ s @  3">  s  ^ 8B  7    Z C#N8_  ǀN    S     s> n /Wy   љ{+NXb EVYfQ Z ^ 
 ?   g #q .     ID"Z  c  ֪    :    J     c ] f  f  &  .ǖ2  `gq o؁8 {          (  A        2   
   /X  m U  Ƭ e JdJc (#E {/ ,  W @   @  -                          

ServeurConnexion.wdc
PCS                     B      9       j                  01F280051n                  cv  w>r I kx      U g   3!9      "            S e r v e u r C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          	                   9                         01F280051n     r             >        
   
_                                                                 Έ    ServeurConnexion                                    /   Ac@\ 5+ g Qs M  ԏ t y mt _ W   ä m  > O
       |^    
d   @ d 1     b 
    E	 | H^  T ̹c  "p޼1 G  f  	a f"m Сc& ?w  ud:pdIy,"*H RM3! ĜY 曠j̼ C6f M  iǧ&{LCF 5t ~͹3  x   ʕ M e  F ] H   K $  u TӆN_ U6 mc k ōۉܖ Ÿ' Ɖ /mӈ߄P  1d  _; , B 8s `Ec   ᱗L% F,  :y QG̘6f  	1  Y5b  u   eϦ  [5p n  M8 #  S    {-  rC 7v 
2  . Xr	&  ˾ X
NA b   3 xc  j Ec   " ȣ-  "s  āBzM   42 # DrI%   &;Zh | r +     ,  F0otr    q 1 4o J  . '  h   < L"߂ ň ;n # x  *   X   h H4 *    	B	)L  @H r"    B>J Ǝ  M	 # U x)  0 8  o  4} S <MA v p  ^  +[4s? J H  0 
}x6   	(   ZlM  Ad' 0 +  xVCi;< e ] 8  V^3^  2ί   <B	F   
h 7 C     S  4  6U 1 5 s (  e  t ΩNβO ;* K   8 k  5  \Y 7   g  | f9  Sϣg :k     s    2 l [ z _r 赦g,   z    p" o\    ~ o      | 2 4q   9 h|   ~+ +* <        C     3u  F O  & -  V   ڦ2" )S<ƹ ;    p)
_ # q  mߗo ?̡  " v  2 z Q/DeH z H   k   y  { ֳp u  8׬ ׌{   a  ĠB  4hh ^p   EI  b  &   UR02 D{x I  š t +  LZ -;`%>0 FЂ ! 5Q 
J     8   r   ' : w      D، %!   JA  D D ,/aWt$ +> X  a "j   a  % :L  W|C   .   c=$9  	 *C| H@7^!"F 
 B E0Z  y  s  Ghq w $ uF <:  
& 8  
i، `  !
4Z H   )A   h5 Y  "   ʈ$ iuH  Z  ==mN1#  f68       >7[d MɆ  Ϗd    k0 {~.   <   64  ( 7S   $	 g b  
 l K p   )"(h
" ,o {˛ ~`  |! K E
   L 1  D uv R&  @    uoC JDN . m HBu @ 㾎L$~P1 n  ]  #T* UI" b 8cH   N  `7(  ype U^K ~  02a!i^ C І8D, Z& BE  V2#&  ]QYi 4UW    : ) AU D 
 6K Qme BҁPA
h`L    !;  ,i) 2<     1 ̋43   4 MjϚ  8  MpZ) Z'  YN   NΜ)    `   g |: @ + \ A_ ."   ~? V  iD  %  0 B   H
   
 ؄EÆ  )  E'{sg84  ՒJ     M  &Д   Wک: 7ŗ  3	݌j7 a i #ė  | u I-`  ft & H    $ ~`  Q   2 Y C  YH̀& A[  (
4    G D  MgF \7Fy ܊ P uZϼ  h^ֹ E  
U B 0 ) 6     P Gg6 J|     4[Z]  t ?j 
*xbt g W y3  f iN{Ԣ    Z yd   Ġf    
   k '
S1B*   V 
   `JpC&  d      C     ̾ s :: :  T ` '\
Z t 0 <,| B>   
]I  M .v F ]3 /Ӯ4 ,    Uo i       8   
   2
  ` l3 !  '  HX   d EM  r v&)  ܑ <G.    SM   8m  zl8no	 ŤZq(! + :ē Ѡ  V     .o $R	   ּo+ ^3  f G $   ߪ F^-Y i   џ ǻr  DA 6m #-  3 ؗ u o     %  = (V       k l\ Y &   c?{  F zg  1 l[/     #  O  W 6 ǿ  s     $ T s W л = z 75 7 @?# U 
}      ˊ   {? 2  Q  8   K¸  9     9 ) i:    {
m    t      9乘 9 A · '  ̉ Ї#     a  	  I   	D 8    ?  JC 9 m
 E0 p . :y :   B 2     k1 0      {; Y  ȱ  B  x     ;   5    Fi J 2  2; 2   2 " p z<  J !<J 	$
  # @
   3P `37S  Ak)E
 ^    : .` P 2 x r 
   u  Qk%  
 p ʃ$0 6     ʃ՛>   e $:xN! T6h  b v7 x   <<=
  
 " 
 *=9  3W Ĭ f    0 !h, Y  2h |  ,%2   E  !       	 j         ;k #@   F2П:h ݢ	 2Ȼ      h	 $   4  Ȝ    7\Yb ,% <.[:I  F  Ɣ - T
  F/    @        Km#B! ⸱ H !( Wɏ      K 1 ;P   @ IIA7@ x  F  ꌼ    AF(  8AfJ  09 S"Y"   	 W*   ݤ ތ  1 ئ  4 ܬ>ϊ       9   A  |  L r  $ (- $<  ;  ʅ-0 )T @!     B d mʂ. O <     : ?0 >4 81    T  /I  ܲ  7 $ k ~ ; 
<P +    *S A  
(XE2#=5 
 Ef  M    Eb E` < Ա  pp 
q  r< t̡q  a*" >f xr10P
0hft |  j Ư ƛ   t  \   + dJZH 
     Љ J  ʬ ʎTK):K  1R  DI l K  G p%* G  G* S5 PY,%it  j8*  i    ɜ  F}R <?     ˾<U  N *y   0L  Ŝ;Ё
  ȜL    _	  L MGM      D ֜  41̮ ڤU      M  ⌈ DW  3  &  y%  N      bĞ  噧  x R8O      t8  4     |8 p_ =C  B eXV x    Ⱥ )Ц :%L   ?t) ᛳQ 1 N       Py     0  !m> Dy(5  PĈ       p     `  WC3  H U*  # "  : 7 +U & ˛Č : є2P  +⊥ D  
  #X	7 N     (\[*  ۡ T  % \ 9H2Z >  4Q ) !x " , #  DY      P     
 BZ 4Jޚ+ 3t 
98H   ' 	:  `ԵuTy 3*Q  =^   h  H    ]  }^   ^| n4	nS  1  E "  ! +  &0   8[   P[  _y8FP * = J 6 :    \ Eˑ : =  
 ]N D    T       ^ - (
  
   Ua - m  mLYL V CAm   ͔㮕 . W6ab 11   b)  { ڭ   ف( }  * 
 g   jhl h TPjȸ HuP^ 'h0 l  G d m  ( 3( { e  Q  M 9  " l  	Pd K8 p  _  i( >P W0 MXs  5p < 8  {   d( . Y3 8 C C 3   =E      BM -e  f^'     ` X  ` r :  ! Z
 ,눬U ؘ  3 ͟ FOK n`   t   ( _ 4+   	Q . IU  n@*  r	 ,  -%    [   \,  Ɲ   X   
C  ݅ 9  
*H6 7u 85= z  {   ʋ ȍ S  ܴ hC%I2J E  5	!   9z J  K    E  
   ۬  jK  M    $` j j 0 6  Jk  S    f ~  B     ɿ.Ub  = a
 l  [%bn- #v $  {ėsb b| l} b L;	2   ;  *4m  M1 a eY pفh  Ô  L ě ✅ <d CԙE m   աY>n6on  p*y O  * lpuf   : eŬpn{ n|&` r e6"  h\   ɿ   h   [ 

6 AE ނ;h 9   .?/ ; k J  * jKL   6    o R* \   :x 
oa  
 
  - -pÎ   V!f meAoEb  l  Dʐ  Nk b l     
  <  L $  ir 8<  `r a  r    8 mc W5  v g U
 r4  5?  6  Fy2I        KQœ 4	 Պ7  /F    CS         9uH۫@X \g# I2Z3x /l~q   c 7 (qDU        b* N!  Ў    y  /   /
 X c  ! ebx sW 㯏  k v

[    CY  C ;f NfFLD nm hP ~ iN   aϚ; )  F >    {7E
   X u   Ht [ wtUwGI   t     M?!  S   uH7 .  C `Ry C  ~t Fl VW  h*bP  F l2z  3  Z        " yJ  ȂB c    1Ie       1`     zy}) HC &  yy @    x U  e 3    +i  xsq s` | e. = ) %  _j Y    26  RD< .    x| M F     l\U %8ط 8 ¸Q|~  '  ߌ    ( ( bP "   
 ҇|t }y    V  ٷE| E      c 
 |    Q     g   h &  . Ǜ kȣ  \2(q  `C    * t   Zܜ`u d U F h  3/ m    c rH *=j  D $ V"h,e F  z/  ug暂   .;  y  F [z5  	 54LA>  Q      nX
#     "BëK& D  gq   R  ڸ:z@ p7baȀ   ,`  2 f@ D 
Ҁx ԃIFE  4`m @U@ j 
  <   l5 p T 9  U   0x 8g ( L     ^_AS    	y   ^       pQ lj5"R@     /  kCx  	F H 9    _  /<   g pQ  ~ A  Gc{ Q  }n    P~J @       _V 
        ;p n      k   M CX A 5A3\ U  Ņ    U i i*  @ Z@   ԫ  0    Al?	 ,D0  
D,7 TD  
3b
 HQ
qK.D|0  d!  4 ϐ   _  \P  ׸V   ;<$<r x 5 i7 14أڕ hZ ;8 :  БPP     g   ؂   / 4 
xa_P-l E  a   r   d Rd t#n(9b   Q_) ^ z ` IC@.:    
 d  Xa \  ` Ѐ`o L`s   Y J`9 H     G @8	 
    X 
҆&  c  , ; ZaEE(  @bJ< 4 !ƒ x{ ^ aM  	{ B   Ml  , K B;fD    
`?1865y0  
 'ԋЈG {  Q
 B   w|b M2(
u# B*B Dy  Vċg q%DG X c0d *1@  H    [ 2 HG8- /  Y ^     
A B  G`  ; 	 Q  ea" @  
  ; G  "  j& ? HK )"  +J I  p Ϛ\   A> . "% T R     .d	  \     Hr"  s   =^ &t] 〜 v@Di(eg[  E  X'%夤 -P 3U % 0     Avx  M  y (  q  B+M," dB @~  b  f   )T MF Zy O ̅  汞d  a:|   F    " U j a2?   daPt i '  clX   E # e    *N      h` T -5  X,    \   n6 I   A  +q!    hB ߸ [˻K */q 8.+勸   ɸ=  fл 9 i '   rM U
 } 1e  Dw JS   @  !R  t趕2* R( =0"   3c  $ GkDG 
  G    =  U  #    \͇  B< S3 ( ) F8 ] #ĂX 6Y  p 63nʖe8~ `<    4Q4k
E3b
 5R̭ Y 
~T    l    *fV@  v c 
 .  4 GhZH  Y  l        (  A        2   
     ~   y      Q A 6 %  (   WC  (8   'x 	 z                                     N   N    Έ J   C:\Mes Projets\repo\avsp_server\src\TEST_ServeurConnexion.wxt    

SignalDessine.wdc
PCS                     B      9       j                  01F280051n                 ¯}h  n        'H͕  k  sc$1ޒ     "             S i g n a l D e s s i n e                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 PCS          s                   9                         01F280051n     o              >        
   
\                                                                 Έ    SignalDessine                                    /S J} 
 D ufc֓c 
) j   5n !*F   (
 Xn  2d        4    
dP  @	:%HR х  b 
*8`1 ƈ"  , <  A FL "f  Q  L uBر H13   . 8 I  4)EDi  QS f  JV |sfM3
 E  #ӓ.     1q ^Q3  3mp    & 4b  =j lɓO * , M  q    &D  E
 X^  gj. 9 өU   !"H  #  X[ R Er     97 Q   W V bE
 ^O  { j ͓  8 9w  - λ Y⺕_,޽E J    B #V        3 3   2 a0ȨΣ `      4  ; 0   39K       "" x  9   *; Џ  ! . 
E ,˂Avˎ  В焈 2 Q  oj  :b ьSHICՎ4ML
@  p. 2"j3H  ( ~)D z      ͻ  A ȋ/)  D（ 
@ 2 ;  H     B  L#  
/̐,= с  S;H " *Ki QC  1 Ox| 7W
@ 
1ң   ̄&  r *C UK[ $-L X5  4 | MT s :    4  ?m#t5    u  Ԣ'UU  "       f    O   )#Ұ O6U$  .D  (
1 rÌ,  ՚j d F J|< Q0 $ U   Κ2""`a
3ʐR 7 R   
 ܐ  4 pZ+ \ I& lzd I  d   ,     b)) AӐی pC4      4L ""j4  ㅘư F   +  BЛ  (ÇB`! <ga B     , aء ǔػ   [C !#  R :   Wl  v3Z   xX   _ }v 㰝    } ^x  2^ ߼ f ^z K LI"  1   ߃   %o k
 7=0U )    ͥ.wɋ F   #`A ڔ 1i D   ,R o}kR 
@  t I]   / lgP 
E   +\ 
׾D  #* #j S Y B 
    t$e) 	  Dl  @,&"  I\< Y  	Q Mo 5* HWdǚY aos    ;  &uX T .
a  P  3; #/  Gv    h AZюV 1 iP  * f  lEk^ 'Ȑ \d# 约  "QP  7 I
  4 !  T  K 
    6H      *q     7 "Hi c{
 p 4 o!   w   d  @':ҙu   ٚ    dU  T   :в& ;      yH A x'Qi4p l "2 7 z J P> .  "a  x    .   ħ   /ԡ x F8Q B  G # QR  T3  	  ,`  ISF3  Qc{J  6   cg Ԩޔ R  I #fZř  f  `B 
p %e6   #
 b ˳ x 	 +  T  h6 &  Zծ&ˮp
,/     ^ ( f  0 ٶ  mn %Y3Q i ̦*  Z=  $Rg R  E  Vi ;  y   |fj-  Ѝ΃ K ̓x ~   ,8 &EIܹ c"    0  MH   J .xiC~  нs C0H;  I
  =   % f  ~ "R ʃ  o+LA  n    <  )  Q Z w/ |-"8 zƤ    -a
! LK ڪ   rE  t: : 0 "    ڮj55C 2kzh  u a& aۨ %  I HV \ L ! \ *)v I  Qa!dѨ     $\   δ   @@j "  E  [  ` d @h 20qXj Mh '= %5  D ,f i U !j  e ƕ } k    g㞝đi[l 97^K   [sb  m 
  %0AU mݦ  %nP" FMkbS    7
zD  	^ Ȩ %  -   '      m
 K +LŚt :c|ek s 
h  N) vdo 	   % +y   b  Hw  B 1 'ծ  0;  /  P#   l # I
 
a
   ޠMw  x 7    9? T  / < M!    #   (  u	{   R +    *e  i    YRh: 	 5*e Q m~Z b9< 2 {  -1g  c6s \ 5
> n i&  ) $   D H  գ ,y8- Q xƣYE   x&V 6      y  
 A3D    g  0 I |NJBªMyYT Z O u+_ ٬ V׶   cx%1  ƌm2 Rk ] w Aۉ0  ۷	   hj   ?π  R Ȟ Q$  0 0!h(  pҿ : ! 
    W; ǀ;~ l  i   7
 1 (    l = X2oq  Z<sA,   *üȻ   ,;   3ΛB 
5C =6
  ˆ1 = HF  C  2 "6   ې2⽤гU  2S    A# B 3  =9" Z    ݹ3` % 3@  y.  @ x ^    s؈ ȾS
6yX k  k   ,W XJ \ %k Bd CLDl 6p b;& 
A B4DD g 
h  W 6k 6m 6oFQ,qs rËԭt ߺ  X;  x     H Q :x  A|#  8 ˛2P   A      y 5 z œ   ػ j 2  ]t (0$  왰 ' S; 
³  D '# { z< &T. B=   K4 8 = 3  |  PC ! 6 p $            X ;<  +ɨR4  =   OY     YǱA>\1.  *  @ Ѐ<   [  % HRb SJ Y+ Z  Yz   ʐ      j 0 E ;6M ˪   E P o &b  9ȶm  | f$7s   [ x yS0  7|S} : ; : Ay   8 Z     k Gk   . ڮ § S m 8  {4  Gy    (  s90  @    : ӹ  9  m: < GI  㝦{   -@  ԙN  ή   
;  A k7   u H&;< \!(+B+ H$ %  t  s  C+  C  #   T3   # & Pʄ  #  3  B  O 예xRy:q` ' ~  . S@ %؀ 45
0 M 3%  Q ? \  *?ZC?\{4 Z 5 
K9g *a'}.  ? D  
   + '(  "0 (  0 F{  9  `1  (/%: ;	 \    圂 ď    P	%  S (  @ @8  9 ӆzT: S  SyH  @0 d0  0 h f R Z@CU >} ?
 A  O=  p    E݁SMU.ŝ 
  # (I   R=    $nTLet  4  L   V   
 dN5h   N  U5@ -e U}  9fz  ֦P  P t?T5 tVv  V7 AdV  R Z5NfY  z ^[6 9LTX  T 1 V W ŊMM$;B  NS5Q 4      # $d =P  y B*<I }  ْ$" 
= OA ѓ= ̀$ P Ȁ  p ˀ =Q Iݫ  I$ ֤ $ 
 xU J  #
9J}A ] (l  *P
/ O   [  r  
 D  
\  <   M ( +J AP  H p\ H: ܁H 
p  *    I   H LԎ e (ʲ%
  m 8   5  e[ ȓ  ~ 	pJ՘ W P S
	        (  A        2   
   ?H   \ J Ė   ŞE @ 0; +  <L 6 v 0  =                                      K   K    Έ G   C:\Mes Projets\repo\avsp_server\src\TEST_SignalDessine.wxt    

SignalEnregistrement.wdc
PCS                     B      9       j                  01F280051n                 pR   *=:098Vpm" _"E 
Dʄ g   
     "      K      S i g n a l E n r e g i s t r e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          t                   9                         01F280051n     v              >        
                                                                        Έ !   SignalEnregistrement                                    / D 󧲛 X   !  n < z  ~}A  7   +:  ZOF$        2    
d0f @ ,%R х  b 
H   E	 | H^  TԠy#   7s̠QC  L7f  	a f;ō  F:f s I\G G   "  4f権3T J .aʤi  :K >  q   W :m떤 W  qsD
 4j   t(`1 ^	 ˶ Ի F\M# M 6f  !   9   w ѣ    5   9sf O  C    W!d ة U
 7`[ yA & "B |x : ߤͺ ^ u  Sf ̚7        0fJW5 ao    F p@?  #     T  
 	  0 0C7|  ; A  K BM  "
 9 E=\M@ S, E d0 S   Cl
D- 4 i4 (2
":'  B	( h  {<CI0 g Bn`  IE
SR    b  0Ȉ* 3JV s       PDU H  :2  *  J   @ ,;  ʎ  c    ! .   "!Ũ   ( Y;! 8怃bT)ԡ4} SO  5    ȴh/7 #ϼ  3k= B a  Jיִ      jN ͌ jLٺX "   #b ^ HZi (Ｒ c    %', Ì6
>  # r   c : @ !:
*   q P""Y-    Xnu "
  d 
%P B"  ;ot0 D1*
U h e: Gi%Y      Ѳ O;K!  *B#24QD2 _H   $#        p ?  (j Gs:*  
2 : Ԥ"* 9: 4Ե  Tx0A  
oU 
     & 52pՕW_ 
 qb'G޶F /  RΥ"z   : 
o<   ځA^Kd O<y  _t9 韎y  k   u><        D  O         T{F  Z  6
i : g^    L e Aj-hK    6$  # 
   f7  1}{  "   Qn#ڒ@    [  db ,; ْ  
]hC @ %TվV5 < ` PБ& p5  BD`  L e  ~    (!  *  ߄ +ط   < 
. pbxE , >  AD 0(  (  `    sx   d ` ! І|  > aU  T 2 ٌ v; A&)       f   	f  b	 # a+u   &y; LFwJP &1     !B 0* .* 	 i   4llF     \ 	    )Pe1> R +  ;  /暃oP C  j(öB E #hAF OE r    VA O  ` )  b C 	 HI	Z
 }*i0D    
   F&%#P#C     q C:{  &  '@w P     )  P ,2A WX  0 6    U    ` j   E Α*G H j < v    2"-- Wy           Q P  @ & a  b  
B CW.c׾ Z۫b :   ΄ dIC # FT Cr   T   [W l3  v JWQ      T   0R B *,  B`1: S6   
\ ϡ4  , p   2 =J  !    <':өN>  %  nm    ]  IR'[2 t'   g     5	    g( SźR [ j S- P 0 G ] gB`Z  f  U@W     ] uh "cS W[qIM  0  ' C  &  *$  K  
S  X` 8 82  q   bPw >   d  f ӌ  KbFAT M8 2N  +  9 f
z 3 V   3H#  * T   &8u gr T	7 R 0 ޔr݊LBІQ    AY  ( 2f BZ q z(@  R  Р>U T @ 'Zыft  I&@  T  5ɗd+閾t1-  yi 0  W n5 `-       O:      n   \D  ? w k   R  I偦   
w  #J1 ! ' x - $   #+k    &  ( ܓQs  3    2  m > --D y eD  \ 9   }   <iCr_ . ģ'] `::,  $ ~JT  ( \Qɦ@ 0PQ ` &@<  @ 	  S!   HS{  	  ]i N%)  zp 6Ќ p q   Ĉ6~  5I E)R   :   / q V +iѨw  ݾ  7 G= я d  ۘS8&r  |d$'.U  t      =S c]     S   C g Z    L 
 	^;    n  ⪊ ֕    ZM"   y   MOv"THx S AD0\ 
 .v	 oz(    7 3  e21S 3ӊ   ? b3     ?4S   0y      )    7    &  * y *   + 2  q  ;Њ: >: 
<  ;130  9  2  J  sB Ș    A  	 -Y    	.,  +#)  ?<+&B㤿p  /{   42  B  2      y
 z5 z W      󵄚7E|   x
B4c C2 Dʂ  86 ¨  9 8 ~
A
    3X<B h '8{  x" c8  '  8 , C   ņ 3 ( *F X:i         렏  #9 1  @9 Q i  # ;9 [Ǟ:wdǠkG # 2:   i ǎ Ɂ"   ˂   Ȅ  48H  
 3	 k    r   D 8  1
     ٛ    1  8Ƌ  Ȏ H	    
 \ B  -     d x ă   ZH   <%b &  (  * G71 " I0 
 j 3   I1 I  F 2 x <ڣ>  @ J  +  7 |     {KOj 1 

h 5 pKﳷ $` 5;Lа76P)l &   N+' $ TP]SC P'  'TkD zH > Ҿ RLa K  h \ٕ^ S    ˅ K   9H  (P 2 4  
: M70 < , AQ <yH    !N   ? @ŀ;󻉈 " 
7 थ ,NOCN dN ԎT*  аX  ^ " 5   Ky  ט>  \3 0*#  a  ' Z70 > l U  \  No       ԉ | Tz   /       Ģ=   Ρ   P D e u  $   Q    7hQ `  P ` 5   Z : 1   (# h  ؏  ǫ        ꉊm9 ę  G   3 r$G%I p  w G@      <:|4SD \@ l L  F
     < Ȓ    7 Ɨ : h; t
  G  T  H L bo ay -I $R ( 
( d    Ĉp g  $: x ^ < ܼ7 <  J/ J  J/4= | q U֌  I C  
$l UzK  ? r  z        sV=h   0  =GZ5 ( ) ?  0۱k& d ҇ P~A D  "M '{   2U  602smDc2'    
  ®>  : 4   OD&284S-  ,: 7  4 1 p z = 	  	O:   + < O e :t  Y h tŽW /"  *   P & ,0  )x * eZ4 EGC >|L  4P 4qʴ 4  4P; T 4S7U r{5 @7Z 5 h7 SDJ    V  (@ zjFfc\  7 bM  ?4 ɜ)os pK5r s
 YS7 µa L L\ՄK  ă 	`LXV  D]:4U aS  8" ˙   ;   Aޗ  @  x|^祠A     ܝ H   Έ  =           ph  % \     Th    J]   ԧ   -" ` 

Ոp  ( L%  _c  I   p  4 $     :           DẨ ҩ ;q H      nE   6
 (      }  a ` Q  G3  a-   N ^7  D	        (  A        2   
   vP &.i q"' as-2  V V "W   `  #w  ( O ~                            

SignalTraitement.wdc
PCS                     B      9       j                  01F280051n                 x ⯋ V     ֚ pz WM૕  ʮ      "      w
      S i g n a l T r a i t e m e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          	                   9                         01F280051n     r       w      >        
   
_                                                                 Έ    SignalTraitement                                    /{   J   .= TN  W " G LθF"?        }ڟq'    h       
dPK @<ô  
х  b 
 ,`1@E    ,    A FL *sĨ cƍ 7uB     3!  C      e   Ȓ'ODT    A  l 2 ̚7s&]  G & E xF  LϢ [  ] "     ދ   e;P  É#> ` ST O    -  ۙ     \: ྂ+    t  *         Sm#F g! +O TQ I\q 
>c  g  w̞ v  } ν    : j 2  wgo>     W  >@"
   @R/       bJ=     r
`  2     :h " x  9   &;怎?@Jh
   0o ̂AD"M  B#n   2 Q   
q :J<ьSP: v  J
 @   2"
 F  ( ~  ;       L S  b  G  hB x  5?  < 8`< e   "
# G      c =0
 S    =<-5   KpA    $|7A  u :<)    IQT U pq.b| \ @ 
  # lL $" H#  #(_I 9   ., J ֧     ƌwV  M5  S  ޜ-"9O ׵  L 7=   .@iՍ  
    6(<  L .0S EOQ渿    S I A b:X   ف,  , # ^mc o  C;Ԡ &5D\ Ҹq Ji   u68
B     3 k ""`a
3 0ҭ7 1 2 "*7  #
8 a+ \ I  Ī _ 8:饛~    r J p ( )   E  b
#B    P i  ~C v8 0 s   *"j4и +    F@ Ĉ u   Ch# &  	'  (ÇB`!v Y  F  rtwWk   } 8 * ;     K  IL *ә t06  _u SDF  <PC	c  h /6    &#1 PLSv	 Qy  m8! 3   ' '  Α  , O 80D(# F"     f  @v A ʃ  ZP#  du s݊2ANآ
8 ֺ6  El [ <r  E mn   TA7  Mob   '8 u p S 
  -->or ۟  s  u s ] BpH3$ u  dG;۩w    B Oxs     ` &!p & 8  ]/ K B =  |e0   ȳ  )i  @H  +@A
F B  ?RNq0 
  ;#/yQ ^   &8́{ 3DA XP < H- Am  ]pg  T %p gxF04A 3     w Pp Hh   C 8 =J 4( 	  	& 
  : a 1 E B  ~ !񈫲Y    'R  |MO  D hb I  B /   pD 
L 1      ́  ^iK     %   [ T  i +  J/s  ʤ  B 2   Hzt #     L~+qX 谾 T }'C* 7    xfH a  <Eޒzs  _3|  A   8s|   c  e5$\         k:gDρ̝ Y.o y 
F a   u  A   K ! 0 pTu E1  )ތ ^     ,Q  { K3VE  9
j v  U ^B;\oE  H   ^l    a 
    ] .T Bm
 HGJ P `Κ 
;       %/ B  P!TA`x1'߀7=  
j` T ̮vfed gʓ@r eP X   ~> I^P 1 av8   ( 'E # S |% x@[   d0 x  m B >PE _  |.  
  L P8 !2h 
T   m +  `     P   ` : @
!XAR0   mTvÑ[=V @9 ul kM    s4 %MiKcZ ~ H bs%<'  |_ pB?  63avh L( vӷ  pH\  SaP.;  n Q l ]X   ՞Y  P v1:   	D
   H
$   @p  r7  T  :t
' 0 *$ A?  t        
q 9     7) r  ;" @ 弄 w= 0   )         Q  wǡjp 	  U%QK$9    _ ]+  #s ̜ 5 .γt 	ț2"0 ^   '         (  A        2   
   q6   C K ŕ	     j ,-       =M 7 u 7                                        N   N    Έ J   C:\Mes Projets\repo\avsp_server\src\TEST_SignalTraitement.wxt    

TreuilClient.wdc
PCS                     B      9       j                  01F280051n                 WZ ]kP@;%/. (ѳ  %e  q       "      }=      T r e u i l C l i e n t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   PCS          	                   9                         01F280051n     n        ;      >        
                                                                        Έ    TreuilClient      %Lj                           /  n[! O       t  b 3  w"t6   	I ,~L& 4 R%     ;  m     
d  @?
8 D     b 
  a1@E "  ,    1cGM"mԘyS' :6 1" :t   . 8 I  4)oDDi  QS    -_ƜY  Q J=2= j 9b  qC &N <C(   1mV  ڶf 2z Q#  ТGŎ-y  28騑
7 ιu  -" r   
  x    D    h  dy3gf x  mF6m n
C 0b  Q7 Uó     vܹB   \  ͺ  >^: r  @5 |ijP   ~  u%H W L H^2 h   
9  Km  hϽ ꌗp  6̨ 2  C ) ۩ :  
3İ#  P
1 ;   HC- T )!  B    C    	  \ >*     ,r # | OK   L <-S3 A
Q3Θʈ ޺m        "  B0   : \, "  
A "     !";  8)   $YR @   2갃 D J ܰ  +   :bU S l  Wr    kPUM_ju W  s +  )b   q 59S{  U -  d    ހc 7k 6W 4 R    c ( - [ -5   X X  :y  CT ȍ
_i  55_ }  x @" ( ޅC   }k
 w  b -NV? ~  s M0̦   dm+ 8  Xb*  a}w v  9勡   8 Ώ  vGǒVricU~
'8 |eVW  X  %% ujs v  \X[/= 0 m  6  Ծ9 #X  oZ_ n  -J   3  lTY  U h   꾛
  @ ^ A6ky |뵂2Ê82/W    ѓ:z] ؅*<ް; r =  Z  	 F    pg|u OB*ަ mz 1K          t kR u J  + 7 ޷_w  n  \!/U     -/=&   Жɭ > S`jH?   B  m( ?S  n
  "  <	݉ ' >  ,~') @   '( Q K   < Om    PI  M `   < m! èB . +5+   '' 
p   hE, o  a n  , e s c xG< Q { # ""  t@) WD& @@  @DT`  4@) \d  :. t @D@ <v "  c) D   *[    2   e-iy W r   %/q K` 2   0 "I]ZD   O LhFS Ӥ&+R
 Ó   .ūj*E  $    n єʑ 1a Lf s  L
!p    9  @ *(
p
 1G|      T4 
dX @p  Vt1   (
 D@!  H8 Q   
#=M6< R  a   @j i  T 9u2 	 ɳ  gS4"T  V y   d` C6 H & K G Ơ $t 8AD 092p\ I  ڳ<2  KMs 8 a>sx  ưB3 `dP*Z\b Ua
\
v   !, F W5*j  _ v .U  %b( FP 4$v
 ݁ʠC  d Z     N&  q:      Z R  ! K< hK   -=  x{   &  5 x] 7 ˝ X     3!  u}k  
   OƋY  ,  ON   1  0  L     !@( /l & ֶ u 
 P     -/      f ưn7 \y s 魯H  w  MQq S  ص(  l    
m s PѦ 0F h Pܾ   B 2    #y @  ӈ$ "  @>  O&R Id# r m   T ;'IL< R   3== S?/3 U>  	 L    I 0   & K2v h &"
 VJ.
i  c =M\Q +h4e iM r / t =  @~ (  tLM"    
pB s Z @ [ OQM@+e j6; J &  aC 3   H    J Y  H l CzӂfE Z  n TL y3¦  z J_  W   3lUB Z : V le/;e Ex 2 Xb ڦ  ^ qz4  {|*  o K   f )W  5  Ϡx j x W        㕳   |  ~~w  l M R l   0gr 뻮 :
 g> ݝZȴ  lB':  ީO#: HO  ة=9
Ӂh  : I  t a 9  K  # ڦ   y  h' փv R   ^ :  .     3 _ f =b   ~1   OSC  x %Vq*;       &0R
   "ezz4  S0  Uq  T NmC   4 !2s [ p       Xw (L
D  " ` ) 5 J\w W8  s4   5 z  ;@     8֚8ɢ, ¯   x    8    
   @y     ' (  
 +  ; Bò  2 ಈX: h   :4  5 6F   
 8 :=  ;cR
 *fsA  ' 㴤 4L  ) O  , SC H  /  |    4P {5 
 {  X xj B<J 6 0 D   4< ˓' k
_ 6   +(u    
D; =
 o    >yk  B> c>烾   >  >  
?l 9x Ԣ p  H  k   ?  7p  R      ?   B -yp      ,a$Ƈ3Ŋ Ǌ    &S2 r, B2' @g 
  Z .  @  @u|  I 0 W   *X?&p*@$т(0<1' '(# Gֱ  Ǫ  U  H"Ȃ '  0R?$ ?+P & H    AD YA hA' #  A S d  2  ! :  
B93 >2B 
 b   =e   tJ* ¹k  h4. +<  '* .  -K S   0L55dC   ŋ  s<Z#    &  HZ =ʼ ( a Cϫ6 D  P ' J<ħ <   kKē       x8p9   )(   J =   b 	  O    H  k> 1  H  >U@ V  Ua>20     6     5   œ  ]T? s?   0N 4  L5 ̩H R ܱ   	   J
, Ƽ
    
 N3z  `! [   0̃`  8 *    圃 ԯ 1 1 . LN͝ ˨L    &  K  :  : $= B ,JR: B ; 3 T d3̨d== J K 4  L :(C    4 Jv ʋ D  Ղ6<	X  8 K   A[  ˦  `D L
ôҬS j Ly  ]   4" 7 %  p*M(  =   <MD+   ,Eʈ>T 	U M ܾ} N~+N4  5-7 S 3     E |P   6}S
 8@S:    f|0(`O    " a| h O     4# ƀ  n     D1]D/  	H
  BSEU3PU9 P A
    1 Id    A պs- dK̥ K `L I # < 4 Ѓ! J  ¹  Ԩ$p !]K ݃&  '= 8) 3  D0E',= E ? <c  puJ   >u Ō xYvuB25   T3 2 
 /a      CL =         MR  A=   MVT  4   Qy ˜. =.k   E   T  ( 	i
: VMr 1pU  + 2Nb    	 Z H \  0Z_  `    Oo<V #۱ 
 ۗ  4
  [  Qm Ћ P  P ׁ  #  7K :" RZW$|Ѥ W  Wj   H  5 T8 !M "5 $UR֍ !Mx  t@˷t5'}C  R K6*m L; C   /    Ǥ C#S w    e<
 L Y    k DJcԡ}   MCE   > 5 FuZ  ^3Ў $T L    l
   ]=ŸҰ ?   m keU - X UU շ     _  4b O M֡s  *P  *аñ ^B]` <  .; 
 b (pa 
  s    a#(A  k   ~ kV# E E  Pb&       ␻  E
 _V`X %~ V(f 	f  a0 I5h\lͣ \P \  V ٟW  ܫ \Nr ;bQ
] dżSi+]' W'
     ơ ,  @  
. U   L  
 >ރT P F* $  Z      ` \P   Ȃ    x$/ hv   y  x Gr   tC  R ,^  ؈ 6 P=  #    #    
 x \H #    q dD,  'D   J  ]   KĈp      hU  M }^  U   \T%  G 66 (>	   M- i.~ ށF   ~     `  # U  [     V -V *ያi y 
  7 w 
  ^ `  Eu9   c  jg jfU/ge  b   e= ? .h   > <  &VAt ? @ V/ ց   M3EJs R
ӣ]KnW  ?# A ѿ a   @;  Qf lʤ  hUh4 fD  ҝQ ,  x  h  n Z  r    maFeT
 1    &
 UX   }X 0灸    u^^El^ LD  A  < ^=  & gEY    :Sb1  y  	   [ `h    	AV߱  T 6Z FT      
 ᩎ ƒ    ^  Z sp     I 3I  hk?rƲ L	: E
 *  G  M~\ N: v:p <ľ E.  elt%ru Ȇ   XMvAN #b  @ eZ  V%  ` P e m䞭Vm * G   > ec3e 1e7 f,C @1  @    1_          
   9L6  B    =   Xx n  DF   & t#      GҚZ)  4   8 3L ᫤  p   8 
   8+5    $
u } p 8 ^_2˰  GZ'   7p># e  j 뱲 
 _3 @ Ԇi O  h1x  a^w2:1  
    h R OϦ $ (wcσ =E;   90 w|Z   &ȍ # wG  r'   v c}  "    9   U{G1 U     8(     k 7 v    +PA# .  :Ѯ6 :  g   r    ' P    ; ky W  	7k W
 U ~   2i8   X   z } *P !3 2 4Q ~S    n(  ^   h YA7% jB /  >Q|    X  )  Q   	      㠑 O  i      xc0    )    \ W   [ 	, w  o
  h9c H " x    /   Zʏ ( +Fk {!H  7|  ݷ|Ka  p   CA1  |  }     qR P/ R    2  &   !4/  f  ! A  r 7   LԦ{d0 )9ɖ) dKr   I ǞD W # 1Sv >ǝ r 
K   (Sl
   98 @   Թ\f d[  r -  9   vY.#e=  8 stЍx rO S:	K\,H y8V :=  bY 3wH  F 
  prVܦpq@B( 8,  # *) fY<e  "\  : %|  2 *  +Q  L`   =#   ^ 3 &'"H+ 0  D D  2P C
| /j  ƀ h ܎̧[  K@   8       Ţ?1 لC
  `"Pyt`T   a5n  8	>   g   d  / <] 5rI 4 'S  A5b:TP  ?T p , @    n	 0 N^    e  B ,   l Y X bA { @ N j8 2 Ë[ ]  M8   H)@  `   
 Đ~$dX   2 w
1رD 4 !L G2 &zp$Z ?"T 
 8 da ) @   A  L@X
j"~ {X$ 8y a#恎8A"  $ $ D5 U N 
.Q) ę j 7 %  c  
  -` P "G Hǐ)z	8  b<;
 `*VE 6 z /VP#    a Ұ  85    #BlE=a   U ! .  >_ H>{!8  $P"  Xx9S #.  Hw2  * #/   `p5     
    rEk8 ctl,Y1f T b    ) 1"@ţ J UTP,IV  &  @ x
  < %ƈH {A QVH50 | ,I )  P   ~  Ω  >  H iT,  <   h   TP c6*  	  
   C   88$ q¹ XT@H     X  @   EId5  "  %X  ENF  ѣ8P  ArC 7e  B 
 Fs >$ L
w     x 
 H     br   %KU s  1L&H   $ < &Rd /
 $Փ   4   e   n I%    4 r  I! @H  q $  - gԇ_1,ʍ i 8 f`$&D h r [  'f` bPl#    ?)$ 8(  ȓ JU A   Z ' Jp *  z 6 . ļh+ b ܕ~ W  
,     A Ʒ 5#g  D  J 7  Q  q m  # N n䄖 )      ?  $  
   G / ƔU 	 KE)?v   /  ˚`	   qEaFJ `  D |#\  0 $Փ  2>V ' &3  ؘ r_~L9Ff {  ,  	%[  (  ( M 	% J T B 
T`QL ȗ
 6  ۘMo .  9ց 8  qTSg39. ~ ADt    "  )  @w   r
    6柧   N K< % AҤzirJf'@  f 
G \BI '+VX]܅sZ Ci *ߘn 7 	   & d   @ I ^@   ń膘HYz cQq    "'t  ݱ3^ V 7 & ܊ su  c :Ǌu  + 	  i,gԤzo._ N y6    
  5 N YY ' tĳ 8  gF    xҧo J h  ᄙGaHO8e  L SA D '   <
Ӏq LY5 f ta(&	 > g 4_Ix
 .4   v  l 1 ?  !3h l~ *x g9Y Zv j7 `  X   zX   *, F q xg   ^ xjО | ʳ sZ·y Z  P w e $錬uF\" >   F   ?Y {@     w 0 uh+ C S ex E  OT4  hq G  O	 AaX( Kl  =  ¤ G JR / ĴSk 'PK! @
 @X Oq"  +   7p  X9C 5 : X @   t R@   + s`(  6 h8 T  I(T   tIN A9I)X         I=   r@
H  d  4  &B w3 *L 74;  : H
 y   4= 6  
  )  8 NY@ < 45/   J L   ^ jEY ԛ	 gM  @ 	& hU   A( U '2 g' &  ,wskED"P' B   d  / P iz  Sr       @. F IT*TY  iT    jQ   ԨgoqL \5RKj  (  ԖJ#`   u4 /   R jw"   l  a`
* d `A *  j jR} g@( )  Q   (X W U gE  6     QA  
 $      Ъ*z  N   : P f`> Ǧ    W B͏  AFP  5 ք  ԶB ǪB jV    f֯J D g5 e5  T JZQL U3 & T: "FŹ ҁ O  [  8  T/ Ɗj M Cm
  2,Vq   ) Q 񕽞 i#N 
(5=!D O I[̓   3 *TT 
 \{!oj    X    Z @P  [ԋʐ[ E  +) u q   ʵgE !   |a  Й?/l   m/¸ +ue_  "6 .  jb jlz꒞:X}_  TM   4  ) X k% KVA ݷ9/  -# 
 $#  ,P7 ο4\ ] V   )Q ( s 
   P i; ] 쉺   N a  K
n 0  Cf l3
 6lL=Jg 蝭     ݩE 5# Ƕ  P, Ê   6  =^5'Ӫ     Q |Pk  Y   RU X
f3 Ԛ: F 5͏n A
P Z  l - S3   
 Ĥ,    J  P eUl 貚	r⮾ f l 5 VβP> AK    ϞOM >   ,  J    
rA :  aiS+  R  ᰭ ֖  	F  uY  mYn   fzB   qfSg+ԧ K8ܱ"   ? R <ki l2}      To?k}ȷ      ] M(U Fl&_e
k  ` z *% 5O7  )F @uJ Ķ   W=/  0 q Q1 1    T   QY.   At   v;s1 M ^ a 7    8 Ur  `5  @) 
T t0]  t
l  8d  R  <  4 W +*  n      n;P @B"e  N +y) I qv   tC٭   ~} A    
 c  
 F     w Km l ź 2  3z  Qȼ a     < ! 
PC  I1   U kV 2  +x   +-7 6 ݫwk. 
 C   x M8
 pbr  p V`    Xd{          G$ XE  e    bܲ{=  
   p p
{) ۍ  `  _ ;i -  
hS     {+ J  ?   (   } տ  "2p 
   *  k *`C    [b!0  %   _ :Awk  ܷ   z{ h݅ 5x|   
oo  - t  Z i" 'uܶx    * ) m  ׽F0  %> |M `a _p   kmௗ" H	(LF E    b Ϸ9  
 5 6   F@ ,)  b*

l) +,    f  , n  S a  @ ?Q  %   }Q#  o b    / }  Gf    
 .m  D  C Hx56)~$    0HI  F C"/ 3  85T jl *E    %Q     w
> 
y1>4 6
c X   Bҭ' 	   x
  3F * 
  ;md 5  x  `n  M  XAE j\ 7 96H X! c L!*t ~8? d  G >& S  *rI&  7 {q d @ AZ j #  o G X;U %   H  (Xe(  F L I)V Z| y@ TO(Ǌ@   Q6t )   L]B@5  vi   ے
l F̑ , C_ ]fY< ʜ  & 7 t   K / 䗄  37+*   -- * X  Cg    7.z  +},|    $ f +Z3X   26   \cu" ı 7w 
.Y2   `3 F .  f   r &L J    | G^ Fy   	 | o <   &st    G     
p@  Nh<  3*k  9a e ܏ 2@fQ   g   [H  ym M  6&4ɱ GA<? ` 
t T  #e 7 ƃ֍ *lh i   ~  n e ~   /:  $ZG hy   { & R(迬4      m  z 
    C+(-0 
4 @ JR   FH    *#]% r  Kz- >    tuZ D i     K(f@   \ 4    [  *] t e  yK K֥51  \@0"Ŏ  -23  o ?G  t  WE }   >> H {́   B
 6JӀ)}Y  0  < 脙   e  P        ^  2 jAM ]  .    jD     ^ QP 
 5  |  D  @M   k ֖ Z׀\]7_ i\+ -  u X֭zM h  VL    w[  _  5e   A"7d]   ^F   t       u̥ :;[  03*- 4 N g    k +Q \ kJѯմ  3`H8h=     Ѿ1C } A ~  _Sl \   d cSg O } }   0 aCI ݯ   ,    6 4 V \ I  'ݮ    jS=l e    azLπ   vҶ \ Mǀ8
    N ] d혍 ״   :] j  {l   ` p  F  !u  > JT KM5 2vǝz?C  u
  ȯ XWgT    NЮ e  Ǎ 	    :^Wn] 6   " m    g Ѯ  G@ku}* /6 ؚro  7  + : k - UЬ     x   u {  ^  ˝ ܖ{{q  M v   d
  ]  2    Gv o ͬi6  ز
'hl ࿝i n
  [' )8^
 {'B     |J  	N &
 H XHd)  _- ڃ{=  ͙  &   3ZjG >uꆵ  u  P{x N  ZO h W9bꨀ   #   t5  < ʺ  ę  { U       
ѻ8 o  +  
  ڋi>   x n O<[؏ Q< w k7 6nş   s}  ` kx~     q   M ȓ   ן5n# ", \ _ `:     桒K    5O
_& g 2      U\w miɶO   ⴼ)  % ˯   h< qD> y ֽ    H =v    { 
	   sy3/ N  kB y(O   W C  k $ 䂛 W ; ɛ   \    OXع4  W<   G ʳ9  ҂ l k  
 V  8P 㥯$   `x )IRx׮  p l c  T=skm  [ Ӯ j 8   Q  r v  
  w e<Es
y ,       Z  m^ɕ -   |  % ԿE  >    ~ U9T  .  u~~ 'ƶ  «  C  Ky  t  7t(	 / ,  T   oΪӺ?. Y k(u .םz  w\A r +* _. ( k\o. [W < G ի Y  =   m / [  ]   ~   } X   	     #݆ _   _ )+ 
  >V  ^  N{  l       >  :v$   d7 H k a (_첽 Cv  ݱ e/       h  D   q2  q j_4a*   . ?;< 퍽    ׃ T       ^ Q;   w ^ 
 =w tZ  u N S;{   =  u .  {G 諾 Wv ~1X  =     б       p   s|    w  a _;J. Ϲg s      ޟ   > :   . Q Z ɂgH  A {?     A| & L#Y8
  4j     <    ߋ ~W $ bD   Q<    = { 7    z ,    M  ;8 >h    ݼ    ] 
  N G|   O#j E  U    c _pӮ   7   ; X? yw
( }  t N <  ґ { p     q  *` F*Uh
   	 g *[@wv    f   /  6   *y% 0uD z  pG>\  ;   , 7U    A  Γ   f9	$8 3 
 "  }8 
 "ߪ2 
$DK m,s  : [ X L    Ǌ V8GXwn 
    *  n Z~ en> !   nP` ,   r5'Y      Pv  |q  ឍɧ!-eq @IaS31 b@ |    P  %  {! D x    #ᛒW y  A @~ܽ  h  }6  r Hδ_`/    3 X7    f  Z  s  
1  `  C  ? @H2    S ğ 1  v ;]u o q   +
4 ' 	  AĒ  \   o W .  s z  bn +      F) y ~      y \? 9    2o j _ '?   ( K " z ?    ? / rBRHX! J      0 MA    ٧ g  0#`ģ8 @ 6  o 
 
@$      2c <I a    O d"   + |  A x"$  /w  B a m ֺ? )_  ڏS  D 7N   ~7 AJ  ?  _  & )   0X@/ S Ӳ '  R  '$  [ :`   (?   
 t+     /4,T    nʓР;
   r: h3   )` #    ۛ =  	      &   %(  2#  D&3  ) ,4똌 )d J&m Ӄ) 2 Z #@ p^     -  Ύ\۬ z 2s  P  k(AK Gc b G05 f8/+&02 `&,
  '}h
P  #@8\ )`	kUV V )  B%ى rx6%Z ^zO 8 t0   W9w   &  (       *  b /u(S
  Y ^ -A0 (   %`   /KVH' Qj j gp -Aj  خ6     ~  
 y#0ԩ  R ' \  A -T \ G   %i kpB & 	@n A  NK e `    
Bk! UBl "` _$ J  C   "  B Z0$   	    ׭ 
A    A  %     i  	 A l' 	  p) 
-  h*t  )u (l  Bx Bz
    -   B  % 
  f +d B   

   !',
AAk   .  < 
  &" b   ^  & O	  
q( 
0 	 B  +4 k  
Bn02 I :`   ߊ     e  00T
        @  
#   %IC p<
SÄ0d
 B9A6nC  7L *ø 7t CB i8  =Â#9 
cp&|m \E: 	C 0@  X &N ã@;LvC3 # 
  )`
   A Hk     / 
C  4 C  =\N 
     P  Y9 O    /D
AB 1  
D /   Bа\G   4,DԐ=\
 C
q "  B PXV,C  ;    '    Q	    

_  0Fl  Q, B|C8    *It    0H 
qA k       ? D( ŋ  1+T A 0=tiD  & [C  BЈ6~ç: . q
    Cl
z  0A E  J   Đ,  +  =  1  :D3 G  H +  : R1   J̜ڗ%D  zD  c R> o  0   6  r K7  : , V  L [q  X>^Q [ 8,   㱾 M 
   - 6   /  
 ;2n:  #cH
     % 㯝     > j] v  0>Һ    C   +        A.O_ c7 Nt A  ^ ? @Տ   ,  x#87
,p   qbL   h_A" eoI  GVH>LA  i x G   3m7 _40Ї  p    r x  J( 	]  b    T
E ' : F -
|    f4 j o ʫ	 & q\l( & q
,     NF( Sl P)    >G T   Q  ƩVѿ  @& Hh"      E 
벤A     # % zDKoEDDfg#F f r	  & y   # 	0 *   ' d4L   p   E -` ' " 
 * 2c > GaU    b  0 B   * s ybC:!       tlz / owTP / G    c   /U T   ޑE\ ģ r    1j@   `yO-   F   ܁Y   AR ǤQ H5M@n  6  1  ? B0D E&  p  N   Ȃ?A QxJG 
  zĠ. 2G1x   G}  `> Q@  <G 1t   GR-  :    C 
2 ˸F  Q̪o  % !   M!_ 2}Q 8P    R*x!  B F   M  PG  K " E
2   G
 {  Hr<  |G&}  Ȳч WH*҅`   Z
 G 
 iH o A G#RT U  샔 H H2  $'IJ   $/IL2  $7IN   $?IP2 %GIl%n&\  `
 '@   $>	 laMZE  ', D甌'8YLI7.a~ `4]r   > U   % { "8(VAF  $   @ P Ɉ    PI ' d 	( В& h   N F ,' I  _T'i J⼘b  o   +"  dU	   J"KC, } g $"쑞(ϙ @B2( J"v ,br D   ` <: Ɉ  $`  қ<(  KbYY(]Ɍ26 P  l2" ,̚ R  dl1  'ϣ  Q %#ʔr " rK  (  C "P ər   Ir   d     & Jq % Ipr @漧	   '  B q  | " & ʟ2   " ~ T*  |B ,+G @  (     $L    *   @ J 2 $T    \ 	   ( @ P,ʈ W ʀ 恈  +qʽ  R        tَ  Ҝ   !  T*ۗ  hI-  4   -sJ F q+K@ "  J   m    8 
o˽  $0 ǒ  rn      	  ,+- @" , J#  @ J      	
  t)   #    Ks  $<  r    	   -C @B / J    K      J   $   r   H  \,'   (>"  ҹ   @B
 0 J)   6    >}R    `  *    ,- |n  & K  	P .  Y&  @b	 1k 	 D. JMʝ 1  r    + Ǵ0s  RŬ$
 @   2U    2+	&  p   re$T     	!`  'p  [ P Æ 	rǦ  X I gр< =  3  
  3MBs 44  B <4ME  d4 D \4#MJsҴ4 JӼ4%MM   4? L  4CMRs 45= R <5EMU  d5_ T \53ͦ  x( 
 R X    2 ^ \  5 	^  46 Md3 T6 Mf  t6 Mh3ڔ6 Mj  %    
"t         6ZI  b n" 1  R \   Y K   l a`" WX w K :  S x L8H  <0 ⦘#v
}   ) F |n r )9t@  b   
#      %i  d
 d ( hc  Dz 
   w <lȠ ' *6   baL,  `jJ,    Z %w           (  A        2   
    ה J 5      ):4 WBC+  | K  t c t/ B/ [                         

TreuilConnexion.wdc
PCS                     B      9       j                  01F280051n                   
   (4{ a ; hS V  W_  N {y     "             T r e u i l C o n n e x i o n                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             PCS          r                   9                         01F280051n     q              >        
   
^                                                                 Έ    TreuilConnexion                                    /̞j f g Q  N   `{   $ 2     fQ u je R         9    
dJ  P@`  
х  b 
'*8`1 ƈ"  , <  1cGM"p޼1 G  f  	a f"m Сc& ?w  yd:  Iy/"*Hcf  :fB Ѫ$ ̚7 Uc M*pȰ1S hҥM=>=I!b  w  z T ܒ' L%Ӧ Y:9w |   1m̈ b( j  Qsfkו-_ 5˳  7bܨ c (R G ! 5o ƕ @o   { $   _d    "eK@ zH d (c s ©  X  }  # >R o  TO~       ]    n  3Т ( 3" ͂    	" P 'vc  k )  p* *j ;   d! f # "       
  # 蒧   Pk 2 `)  c/4     HA v + h I&  Jk  v
>  !" ޸= 
 
 RT +/ z 	  xc'1   5  <    
/Q  /A  N; "0 5  @ ( F=E =C5 Q  S =  mҿ 䯻 L  S  @  2
  	  Bׁ    x u 0 v f  HY  (
 [m UV p: 6 n u (h;7" ġ Z d崏ZS 
  h  "' ؍:y  U m5 ࿲d M  +-V  ,"     Y   I u j ]e t z	TT.| ӗ_    . c f  O    v  2 RRCn6J(   :   @
   #_     <R $     n J L =ڈ 	8 @ê  j8 Ja2d % Bs	 .Ò  X  
; ʃ  ! vK 8   C **# ϵ (   G  d :	0 h W U  s ? a  f /O e߇ e n*.$F6  /( 捷 cu- d L  z ϯ      "t" h   P  𮡇 { ,  j  ZH &I%i |3     ޠ)  !BD &7  mx  ʰ  a% M   ) q    (W  y ^       S   P q u z rd    n8    r C uO ,3 򄈰l   K 
 @f= `/{ 	SzGE Ȋ6?    ƙ!Lni|     @$   !q{  0  G   ;Br    $3 1+kLAa   4cb iP ָF;   % T. i M     '!pB 3 !VI  \y  ,*L Y:  Ot F*X HI  j(C
B ˳ 7  %  ͡nw˛R@ 4     ؆   q p` `mI  m O \ d   :p 2 L0   #h4  7  
UHK  L .3  
 (1   mQ 4 D 
  ,o  Bp 9 A
 &  	D$BhCH .8   mr @1 TW  =۠  jn@Aހ 1i(= a  
3HEf4 #6W   f4 
 C Q!|  
vC ĭ# J\0 ,Bv 
 Z׈0M Z + f C   3"c)   |ѥ   X6 u _rq˂1  \RP'Z $!{+  7+ Hv  #T  * !xQ ԈO
  s  3  ; q ] - 8 /   m `H pN  oi - q  L jZc Mv X
%
 6  2    *[ 8Xʒ ﬉GI	 KQA	Y2( Qfz /0 Mv !4
SP ` I LL       p   ~ ` ɒ   p L    * 
 =
 O~  %hD ІN U D#z  hT-  (K "$ ȗ f   M    < m0#< 
PЂ  + t ~2m       P	 @ ,f2 9g L , 6<ԡML 2Ӛ .9ݩ , S 
     Q/jϥ6  Q  T 5\5   W[Vʰ)d5k E   r  I W       ^  k 
ֽ *,b clGѱ  U  $ LУ v    6  )   f ` ,t   Q <X{=   S  6 e} 
  n*K4 `
      F(7   ss    `N  " `
f  *  |  Au       }A  = { д r  0(  (    ۛ酃'A    P  5!    h  8ɩF   #P     ֤ [    T̐ ' t ? >   	
o    9 e mI     a tB U     $2  4 :aR       =;ދ  : = p  =  @ g   '  $PtX
Q  x #ޙ{_  z^%!7[  I  al - Kߛ      \(!{%C bǏ    G	      F [ {@ 3EH^   | 4   w mr   ^_0z6  }%1 W CԀ  %p  s=JӸ   Z  k  c  C 9 3    9p  1         ( <; # 3   k7 ɜ ꜰUc
V;  z   ! 	 b
    Q  p e#Bf{6# Ȫ%4 a  0m   * s "  " IL h  Z  x   R[ h  B CA    !о   7=`o  ~(h8  . X  )  Z   
!x      8  	  8   / { 9d :?  
          %  * #h &0 ͣÝ㤞[/ HAR   X  	> 3 c  1   ۛMl
  :
wʺ c> 
 }"(  =  @9{2   ؁d 
 S$ C<    ( 蚁x;%qEz  X= 2\d8 !M  t  <  8% OTA 9 E r 2ȭ9   2EJ +  # 	!( θ       4       
     C<, <
  D<  = ;  -  >   <ȡ  :
 6!)9A) K \ B t S  T ұAWk q   A{y  0  T #,B$LK \˳t7  i  '  C k  ) j  LP  |4ea |Q   7   } B,ʵ,|    & ˁ
2
 r > Ӑ`  p  0 ?  ==   T8j Iy@F 8  
HL J DU   
 Hz$  ̬   
^r9  9  9XDEUdEW E <G 
 i Fv<  :0 NK й:ȹ7 8 8 9   
   /!   (O { l I s;   m <	 I  Ǔ /P  0   (
  +9O S      E  J g
   
h J  A    O  P!G)   6     \ϓ
- 
   :1 X <9 s  2Ѭ ƾa  +  :
  Qx& j  :ڠ -P   ߜ j<   $
   8 5z 3  z Z <  $gr +e?  Q`   I 4b :    <  R PQL)  3
T|J A Qy` Eeԃʾc  O<   ==H   U8 q 9Jm9F-  3  1Q   x R 3J5DF U  []  L 8 ΁  ,  զ :  &R" xb X-
%mF p @I ɡ    W5 k &Z FJ5 E Uu ӓ  \} _  7  v ?  W   p    %  BC  A  A EA V1K#|X lK eˉmK% L&  j K}  H X  k R鉏I ٦ ~E  a,L7-\B. O}M m
2 3 *  Xɺ gs)8  RMP XP3hPBQ
 a 7 `  ԣ t.~  r A  
}G#   Z  
 BPō   @qP,   9     VĐ, S ד  ;Ȩ8 4u 5   4 7 7x9U M T) P>-   #U 6  P % 5  <X#: 8$=  <J TF%  3S [yP 5H  W 
 
!  * 9"C5VD= R-P ճ ? ݽ*PJ-  =T CJ r8iJ5;ʎ ~ W  X  ,؁  " ʄ
 t߲\B  ؊ ߙ ʜ     %̈ȇ&  d``  ,     Y  t  t y       Y @M   
     -U  - xS`Ґ3  x` i8 G  Jq.6   <   X
    G 
        ޼Dm     % 0]J .QL Rd  M2@ )   ՒǠ4 и/ 0 $ r
  Ey      c (c  D  
5& 6 2ypG @#   y$ z  {|$	  ـ  <UM|Ԙ$N ]z    씇     A c }jY    ʁ L 
  ],   = YN6  _\fˋ]  u     kcx m!fc ^  @ bF f~ i̈c 恰flVf   pp d  mfL  "  L  L^ X}=Y ȃ2 ` Ʉxn     tx v s  t নو R  " 
    uV  0h @h0 u e  `
ĿHh Q           (  A        2   
      m   N4 -saS t O` Y  A   J  U    ) 2m  }                                     M   M    Έ I   C:\Mes Projets\repo\avsp_server\src\TEST_TreuilConnexion.wxt    

WD_DualAxisChart.wdc
PCS                     B      9       j                  01F280051n                 ً f ' z lwZ ND + D  h^   U        "              W D _ D u a l A x i s C h a r t                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           PCS          L                   9                         01F280051n     r       ]      >        
                                                                        Έ    WD_DualAxisChart                                    / et  > {  _    AJ  @     X >bT   !   / Rc    N       
d0   A xs    b 
*&    E	 | H^  X  )bGL!y  ! F̜:!   i獙Dڈ C L  2   t Ȓ  DT@ &NY   &
*zx q   7e     9n SG̕ .ۨ SW
 7/jެ 
 Ws   F 5r M  iS & P JX 3b r
  kرeϦ]  mܹu  }  / Ą
#  s  C LY)  3 {@    I C  5fdh ʳ W b 5 V-[ p徠k/  
  ]   T `     &{β螚N  R  f 4  K <  {m  f     
     /;: .@ ,p   l1 ^,   \ 
7 ( "  (!  *'|RH"    d I3     0 ƀ 6B{CB8 %   1* :Ì   M    
   *S Ԍn9 Î:ࠂ 9 l N L L1   9  ?et  X ` !2^jc1 `#  Q Wr :   } ʈ;h
r * a  H 3 Ҍ   0~m WZ?C [  5 ":-? # SO|! $ 6X  ) X p  9n{# ˖À*!   'I  uM  U 	5  IR)  4 
  03$ _   v o uX\r  x g ]WHjߨ    
9  cY 1;     4<ۨ   >  	 v 34 ?  @   	1 0:   . kT: = *   zk   Z ,i E  E  >0  e   1][[  m   Zo  ڤ iL  	 m z   ^ q   zS 6	    ˓ \ AWP K  \n#2 "^ \ s }w {  w ;jڣ > }x  }  B    # ~z   뭏>{     |      ?   HJ z   ~ 寨"
 )  y vP _r     " c   ˴ { sNu   D c9 "r+< UP  " w  `
 	m   @ A @ @ ȥ  C   F     q  Q  @   T
K Ba=> !0 !  'H#   y<b  s    X Rh ŘMQH)Ȗ <'
 GJ:  ʔ %KH/ ׿<v |! J  :R 2D$ _H" ~ xD4 $p# A "
$^XJ 5 @ K1   Q   e-mY  	b 辈     +sw Q 	Qx \ X h  1 "q6 e "E  #$  A  k1     ؑ ho9&H U F p3 2
m [^  q E )I`V D%'   UF$  e sWQ   y `Ba9̀v  !%isFć b  ٔh 4wD<D  7  (  D  
" [t&0 ;Hs9*H4g d` B S   3n C1   h!"        'x8 xȳ  F= qm ҡ |H1!     * 7 ) Z :!  &= a l   g   ` @
- 
   <n j       ;  ` !  Z lhJK@ h*a v SX3 UB Q y{! j   
NB     ͎Fp۾ !"  kS=ġbe [ k  6   +sAtE > /
v   įQ! vG  d!&3  xK\
 u r ]     
  	
l K
n   k       -^    +c3 X w 6  a  Ù{J t _  MO!0  |     իn(_M  3   ] ٻ )    X    } d <a  d ( p~S եE (YnP k b/  `n1 ׻\E 0&0  2   r      2y l^/8 0no  KcJ:     1G   U     Ζ ؽ  ZW ֲM i
2Ӧ $  ^F )@ 	MP  1 Ц   $+     l+ W#JQC    1  Z) ,u겙  M M C% 
m  v :Dt i^  S   ݕzsn  z     K̀   `b C +6dw7|[& A     Ta 8\͗  Mq   g jn  tP  [  0    
4 &  h 
  I ꯴ @0 Ɉ8 {6  DGyJ    S w     kW  A   #1U : ~w u O  ! IzR  t +e /) aQu |؏  .  Y (  x 5  4 J   o aYs@B     HP Q  _<  QP
F   <PP = ) 
> REv   E{ @* 'g NC0  q U     ;,* Pv`e4    O  K/ ӣ U   I&**+ -    MC T   8?    S B 	+  0 + 8!	   B @< Ñ  ٿP@ 	   
 \ 36#    01 l{ Q  *  k  3
3`A z 2@BQ   { D&  + *  '    s? C  H      ҂, 	 2. x 0 <C  `  C5 3     
 ]  + 6 a     zӹ{  :     ` VaDE   ЂD\Dp # 
Ժ    7  0y 1 K4 LT  D E7Ũ!E
|*yM 9ND  3PC X 2I  0
X #` - ; Na Fz$ C ë:b M:%O      ; ˨ k;o| oT pt V <    BFu\G <y a  bs,#v <  ?(l?И 4k  ` T( ] # [       =1(      =3: <`B  } ~  x    1    $8  0 @   0aq   ,D v9    1 I.  =D 7     ځ'x    t Jb  @  
 B  0a Ѳ20   	  D7 2I  JA  XűlŲ4ɧ9  6 I 0 " < 'j @ i 4 *          : Dq    Jɕ       .   2!   2@K ˫   Μ M')Ey 6@   4 & 83h у0ف \L    <Av e$(f   s
h    ƈ b   ݱN Ѩ G    ó  K  ?  !  q  <B ]Z
yó; & 	;  $T&f   
O <% ڼ% ǈ   K   ` 
 C  # cP  `ȉ r      )Yt tB7RЎ   I1D  4ÓL4 d      L  C  33   \J tJ J6 ͝KsC7u˳ < 0 3 1O 1B  s 8uð   0RCR: JC B!   9	/  HK l 7 +; M< 5 V  ˾٭[ K  K    L L <0 8 Ĝ0  0    =S[    K  !sM+  4 H
SԄ
    N35@y   LE5 /   : M3 MSN RUbL `k  9N KNg     C j  k  i         Nr V   z % #O`  [rGx$ ^  W  a  j         X ɻ iP{ %
 S  2Pl    5H     D!5  4F     #  x 5;V   о&ħ X ݏ U     0 	  , TO CP   * :  ,Ђ`  <I %8    (J  }3  Y #C  ;a X QN :V [H    f  m4 g    XrO OtW  q Oq Osu   V2 Zv <t   в  m
o- |   -XB!Y8P )c ۡ EX  ( t8 du   X  3    ،Y =X eФMټZٖ}Y=   } )   Y   ` Y 6 2 
 |S UڠM]tl: =  XΦ  眦eݨ     Ư%   n Z|   Lۺ     C ĥ < u[ 򨸕  e޺    <   # [ 
     0 3 >d  "    	J ہ ^`      ɽ\
   Pݫ  J 5Q  _ ݏ }      e]    U   И   T M 0  ^U
zZb-ީ]   ЫM`k PgE%h-i j [V" W2ے [ Ż U)^ Ƿm
? [fm_y`x5¼m`  b 
  ]  XA ŉ PC 7c 9  ^  & X`  \ `˵  ɣ͵' Uc 8X78 - @  K  MW 63X F  G~ ,    M[2;  d3     (p H^  n g   U  bl|     ^ L;&  s| (. ) *.  = ,> "   8/ W    U g  8   ݣ    z  V ]`   c
`     彨 Î5d  潸 ЃV     {( h#C1 6 6M-
L  \U 嚨 ށC   9TT J [K `E+   0Ë>    9UE;mK+   8K v (=@       # (  )    24 ,H%h#   1 + h(  93 a 3 6	 & 6>  j+c ٩ ! ˢ> ^  7 j  h 걮9   	  k8 j  hi      .}U  ďnK< Ll     h lS34l7,fd    Y :d  N  -   ^"[ [    el  u |]:  m > x  e     "    z
   #n (n

\Dn1`3 )@ rrP` {   nH =v`       z >F     1( aQ , K    I    Z    7 8Ѓ   *WY93@ A    ,  P I   p  pIf  ,   pE  : pU p p4h P . l> e  b :Z W[ Z\   #ޝ$  pf  ޳    mr   ~ a&	&n ɦ  " (8 *  yM   & Z ׏: ,nm>    P >B U ( v( ; ^PgkV    ? ` >  k*:W;  9J ,  G  7 g 2   A  G? C t5  \]պ	    7    R  EhÒR   l  q   d-m!?m"'P 9r I  [m 滃f(O:)O r r          r0s2 W =s1 ;5 %6      ߈Џ:  ;wPp  h w( T tA X    a  g  Yw wIg   D O   N =R  ?ԅ ? CB&81T Cu &y  y   i+ I nc 9pu:   Џ0 u  uu; - M 2 &    Mͭ 9ey  y M3  7  d9 3i 4 M . v goX q h a  U  5 h  i  ٖ lm s/ \   نl P   
 h Oߦ óvn
d  bm  / 1/ p 4 m'  mU_  _3>Py    7H;0 $  - ax Ȁ  d @    肷*  |· ^  (PLhҠ  L\ @)خ hRI P(     OG>  `y Q ( !	  i ~Y Ё i    Di P     } ڼ 8  6    ܄     l g-&iu    á25 j    5 @ ~?a    t E m  ב  2 Vj e   6  ۾Ӳ _   =`  f "ݽ     +G}o @       v  ۙ9 7	4w o m' E ,_ 
$  A u&<( O   ;  J@ S} .*Т
  `I? 
؅a А Q =  s   C|$ Q  A    i?- U ; "BM#=    G  ҚDf .x 3  Tު -oz	YU
0 5@f  4a  ^0j  u  b n 6 
S	ڋmi/   R 
 ^2 | =     | C    ߋX|MN}M>f    -	\ D2 < dAS  9pS   & B  x  L   @ GWa|   ) G
8    > '.
0f  <HA b   \  o  Ӂ   Ғl ?Ʀ ԋy @C  @ 
  Ib  ]0     L 26"QI   } 
jA/ #r  ( <
J
7      j"P AAh!    gn ~ l     X   @   p  T, "  s- bT @Z  	  " Y0ۡL     
 .YH¦L [ [T A. ^3e " @    9   PI	
  + 8 bLx m\
 5  Ճh  v   붞m^    da \    B (C l\b$0  · ucq 4  ua Ѻҁ&  >e Nl A 2
_ 2	  )~    @S @ I  c  e  CA4 ?t    d }& bϰ 3`>VE@   V f +>
    "  ph-^Ƴ 7$ވ  13γy-$  X Q A2 @ _ |-` q  2  ̨
   e!  4  D 2jd    M m rH 9" B0 ՅEJH ɠM |/ L 	 T    -rq( Y H$  4?2A  !	$  d82R Ȭ $)    S G (+9! d ܒG %  x  I     -     p u1K^I! &     M N.   '    ̢J I5(   Аg 0>   c`  & 
 ȸ+z    f KN=   nl!  x 2   j+D K5 X +g!    6   8  c  ҅ Fe  0 H9   .  r W̑_ =$ 
 U ,  q  ň  " xhK     <(  @<.7rh
"  A  c9J     tq*1   b       *% J?a*֤C Zb f 	;6 G   }| T1  Ia )M GT}Tt,  }  !' L I0ODq k 'e2   2UU M 1 E <0  p < )I s ߰8T ѧ  H ,j I2Ɓ;)1O   j jz  I#V  К  k M T5[ 
z NbMI# & 1 m Mɡ  (+3n   Y7   <Sj p *    OS i 1 15  !?  ě @ d < 6 K  O?^N4%  䜀 s6  5*  
N 18a 9' Tt8 í V4 ZȜnT 3 X;ݦ  )  3N 9  f _ '
% \' 䔍q˴ *a    
  J
 < E1{H   ^ >}e f   
Kb =eJ3  $ @ C A2      `  Ah$d9 e & n  - ݴ ͐   l	 u ޲]E <9%Q &_ Ѓ@  cg     h*A s#!tG     2
rQ/Q  @ O ^M`2 vo$ 6`D Z!DYh&Qbq    \ , | !Q @  NR  	~xVV`  CUʥ VH |   8    I@ :3'   ' dS      0) @( ( b   )Ԕ   @ 3 Mׅ  f T z`t     "    )N  h } om  ǘ_l _  PŁ  9U ꌣJA R, R)l |Za J  D BVȥZ L<  W9) 0 y>  D    (Z 	9  5 59C@6-  r M A  Aњ    M;&7 Vf0S 9 R  ) g    G R%  C  Sm:=ag  72q  ǳ     ڳ   8+3 +   | O  +  FU  Wz `   -مդ  d       )$P* @ Bp  8>K (A} 2   RXj  ȭl      IF   h*	 ԃ V(      X z)   

HuO*UJ, (    ЈR 2 D   @ T kl P E `    g  $ !W 1-:V& t,F 2f-   6 Q H4w&   t( ѥ G   %   f : H  "J  ưI 3  %   n  {JK ,   q UjZJ# `   rJ !/Qߌ    䞬  T
 J:oW S d8W B Jr =  i@     L%    >i%U̅zRg CE  S~  o%   5  F  > +G  %~ @ _  L"W8 w*u 1L 4 YПJR   
 HP      ʗot M  8  " @ 8 rޭyU`  {d 
;    'V jPbX HjDh %   k > 
EA;<   A H@         (  A        2   
   L9~ D ;  M  4f  R      E   i r- \  =                         

FabriqueClient.wdc
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
      
      PortEnum est une énumération
      	portTreuil = 10
      //	portEmetteur = 12
      //	portRecepteur = 13
      	portAcquisition = 11
      	portApplication=100
      fin
      
      EcouteConnexionEnum est une énumération
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1643973963346279967
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : CreeClient
     procedure_id : 1643973963346345503
     type_code : 12
     code : |1-
      procédure globale CreeClient(port est un entier, socket est chaine) : IClientSocket
      
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
      procédure GLOBALE CreeConnexionAuServeur(type est EcouteConnexionEnum) : IConnexionClientSocket
      
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
      		ExceptionDéclenche(1,"Type de connexion inconnu")
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

FabriqueDeSignal.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627236119325987191
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : CreeTraitementSignal
     procedure_id : 1627236119326052727
     type_code : 12
     code : |1+
      procédure publique globale CreeTraitementSignal() : ISignalTraitement 
      
      traitement est ISignalTraitement dynamique = allouer SignalTraitement()
      
      renvoyer traitement
     type : 458752
   -
     name : CreeEnregistrementSignal
     procedure_id : 1627236235300354158
     type_code : 12
     code : |1-
      procédure publique globale CreeEnregistrementSignal() : ISignalEnregistrement
      
      record est SignalEnregistrement dynamique = allouer SignalEnregistrement()
      
      RENVOYER record
     type : 458752
   -
     name : CreeDessineSignal
     procedure_id : 1627236806531072694
     type_code : 12
     code : |1-
      procédure publique globale CreeDessineSignal() : ISignalDessine
      
      dessin est SignalDessine dynamique = allouer SignalDessine()
      
      RENVOYER dessin
     type : 458752
   -
     name : CreeCalculeSignal
     procedure_id : 1643567015085553731
     type_code : 12
     code : |1+
      procédure PUBLIQUE GLOBALE CreeCalculeSignal() : IGestionDesCalculs
      
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

System_0.wdw
PCS         
   !           l       9                  
       01A110053g 
                      \                
$            System_0   hUF                          # 8F3 i ~| D  9   < NY c ɕ T  bF:\WX9\Gabarit\WD\Source\US\110 System\System.WDP       k   k        v     P       _@E :f܀  W#:  r  ŋ   1 F -< xB $A U \Y "   ⼋ . K  L  м  J{     J'TBPIc M Eڠa   ׮J   y1 S U
֭Q +  K 傶] 0 2n  y^$   Y l ~  rnݣx ~
 O G]   6 b * v    G	 ;   i  G <  
 ,:  3F 5un B  9d t B  1 L ˘6bް   
 x  x b ֋     ;ِH D0  Ӌ@` d| Jj)   
1Ҁ 
1  *@  
    @ T A  
 B  
 Y
      v     P       _@E :f܀  W#:  r  ŋ   1 F -< xB $A U \Y "   ⼋ . K  L  м  J{     J'TBPIc M Eڠa   ׮J   y1 S U
֭Q +  K 傶] 0 2n  y^$   Y l ~  rnݣx ~
 O G]   6 b * v    G	 ;   i  G <  
 ,:  3F 5un B  9d t B  1 L ˘6bް   
 x  x b ֋     ;ِH D0  Ӌ@` d| Jj)   
1Ҁ 
1  *@  
    @ T A  
 B  
 Y
           D @_ hx  ʉ  X   *n ȑ#!/x|xR$I $?dd 2 ʁc  90Bĉ(s 8" Ŗ|r I  H !  |T&Ӎ0<}
  ͎)wz|
t ׍E=EI֪J g+J   *N [92  ,] 4͎U #۠j  k    
 8 ˽  8xja U
c
 6l   8e䍨 fƜ   :AG 0   ڦ N { u ֫]  칢܍OEDT1 (D  2  PP     ; *$_ < 

D   ; LLh  C   a i H  
 < @ʂsKAʊ k  "     3  	3Ԑ 
#   <  " :s   PÌ9 0 "J  H "` F B 4 pC    	5ހ = 0# 2԰Í H I3 L 
 Īr y" ' "҂    Ĕj   w     ,  $ l  ( ܨʜ   È  L1 L, 3 sIͬ ԑG  H# T I'  2     O+ 
 ˊ h =ˌ- -  Q7#  R:/ SS=E 3K Z r /#25 TE=4"Vib "X! s 9- 3 <9 Դ(D, ?!U՚  9ps 9 $4Σ! x" 
 
5܈  %  +  v "   q  ÀH  fX#
x dx!    
S M`"s - \ ~  Պ  # IZ ۊ>*  F ( h h n 
 T  rPa
M ؁ A e=r  y 0  6B( 
4^ gׁ   ~
 ج 66   V Ք n 騧    ?       O5   &   9R 
    j  λ"  [  . h  
`   Fnŵ q`	 | P˦ܣ 7   ., +e:    ؽK  t R hu  N\     ŋ    V r 5w / ?( me  3 = <    t M  ﾯ  w  C :̀: h   P  _#q  aa
c  "  U b  ^   f   NV 
ތe$: D֩ e0"6 ξ Ājv   6, 
 d qޢ  4±Nx #  F 5  0w4  F|  nx  
    mt˓\ X #$ ;|] h ů .  d^
ц -n  =  ?D QQw ; n  ੑz s    q  1 ӆ  )  L !]( !     C=  z  < /ouOg$   ũ	 kC'L	  1́p   0    p 2 r x`76    &         2  z   rq<  iR    [b    ,.'3 N 00  ^ J kR  h   $V1  O I  @  @ IMk22]   @  kv  )'   <NO  J:     F C   |I 0 ,2 ́ / [ '   L *  DB{ PRࡷ   钊   Qy &   ) @  A + k^ O  4
 Hj  d0  , 9#2́T  %  TZTܱ H%ӨQ T n jl ʝ * 
&Ei G :  0nqX  ˁ !" H V  4 lE+\ W ~  ^  5 $ՈK $` "  6T  Mbˢ o  S   Źt 1 H>{ٴt - OS  
  ̉  Z p  - `բ[   `P,c%{ °     "   p  U tw
    ; ՞ q` " jm  J: Ѓ   x "   3 z  Ȋ E  C.  
  ) 4Y     d ʊ ;  4A&O0^ h  6w$ -iK^ uu ?xj     N   Tda  6R *uh=z  Ñ  .R | 
 *  \ x; ,G "  G6  e   _ 㝃, =   % @  -Y}    x  ^  hJ q       c0ӆ ɍuZ L  9 
 2S    h     0y         h 2   +? ˫fe   `  :  (   I5   v   : Y y    r
 τV  ]     M諱) z B  !  ,Ij+ ڵM w8 k3 | G  }|Ԯ  ̾wğ o   #  +   mm    &w         >7   >   0o  PF3 ( s    S   9M f t3    .^*\-u  =  <  Z-  zD   93 ~^ő=vv K D 5 k]좣dۆ    
n [  m   ݝ Wڏ2g  is    ,G F  '
  ' _C  1    }
j[0 9  hq 	S > b   #Gx ?X lW     ڔ2 F:G å_݆  :e  u } o/>T ^ 	   뾝 # dt <r v   ~m  }O" j K~ } Y< ]      ;G o  t 6 n  H  P  
? @V  M   # K ؐ6"⿎ ?d
  =<# J7 <e  8 s9|7@  ! &0    z/ :
ȼ P   ⲃS   ASs&    `      (     k;  +  = 8:`      >  >   #    #Æs 狈 c$okس  9A 3   > cC  ^!?   {   C  ? < ;<FT     ?   4  + 0ܻ )  B    K@P D x   @ r0
85 @? D ̉ 
ݲpY H$ ,  C  
3 
 t	 A 1x:  1x 1.   j 8 +  PP    #  6ҹ ˮ  +V  `E   #Eid
    't6Id .[ * @   ,  K	%x * * 2  
b Q F8 *     o H ±q   rB0 8`   W XE
 BtR  *!|ɉFd Gv  $#H 1H   I31    q"
  7        ȗ
ɟ:G  @X        5[ I(   | ۹I| G sGՁ  	T˚ #h ̪ "ʳ ȣ       8  <L  J t"; }4I*|K <     ,  4K  I}\? [: l      D	  Ky R<  a5 *   Ms5j 
ڔ ^  Q{L   	 J:Jɯ,   0 "K  =   DNˤ      3 Ĩ ǚxCғ  s5 I ܈  I   Ĕ   2?  C   hĘ;O   G  ]\K    p'*  d 4zzM 0  H~ Mܼ  4 ݬ   H  M hB9    ٛNL  K P Zt
.L: CY+͕ Κ  3 }     ;      UԾ $M 4M D XQB  9 ; <      )M ! RU @J 5 xZ fT 1` '˚*
X.  5ˆ PS l  0 d5  9 4N+ Q d>MRu*K P'@0  9 8       MY9ͬ  ,  58  J5=  ӷj=P 9 p9 8> 5 $eR 0TDm }ȚP 4  5%̈p   8  9 2; !ՃL < =D   Ԕp U0(Z|	" 3  ̙ Ȅ ⬁( C    %  q Ji 
 <; <IW\V l  T   LB   h#P       
  
RZ=X䨴 Xؒ   !  ՝\U 0  9   $ - 	Ґ%+@ dSg '   p} ͊    t  u ؁ WOu w Y;   b KT ?˞   r    ؘu#` X  d X 
  ׇe  Uح ڮ Z Mخ<R   5    Y  	9ٔ= ʋT @ a    7- k   5   \ e&  [ e6 Ru$  L ]Z tV   oW  
   *    Y V   -  )  򇜽  ]   (yE ,E֔ X mCM   L " U
    ڴ     h^ mX 0  I zX>+ɹ  w   [ ݈(    Uˣ  t    Ё 臜M  _    H  U   8 4   W 8 
  }  =
 5[    M Pi 
f޲ Z 
  u	  ^u_  a Z H߄  #  9 m  І    d  ~  G 
  7 	 ` S{ \|E   \
Y   /ر h  ^TVM	  P b      3 -uZت Z `  U  ` ^  p`7^ 
~^   Q  ' U}/ hЁL  KPd/ Yb 
D 
 u V 	D6  < $6 %֢&F  G 6d*Y0fV1  [ S  T 
  :     ;m y5U Q  b u    A܈B O( Ͼ  2 LfvDg  fM !  !  >_   C Hf Xd tdH    C J 5    ]O&  [cNʀV 2ey dz~ U  V  W~ X 2/ e      R -   ?tV~NcvR[N  Oi f  R     O. 恸 l^YftF3 '     7 gq : f`   Z0 z ́ Pz Pw     ,l  g  > T _ ` g=D TMj  ܭ    9 ev  t ^~  f  ܊ ڊ8f   }$	 ,؏ *i.隙   ]  d 1     ( Э  k=l9" }  ~, }O ] #vlT l?Uk =
  l  E|KhE    & HSƕ&Ӗ  Af 
 靦i  : i     Y    i_jL6cx  jӗ F ߨG N j hY ǫVn    j  > d _ г    Զ&9 F? ^  | >;b  o      v. h     æ    Fn  C N    m  O    n N&  j	  C@ fˈX  dnv  Z ߁ a ; w  Jg <  >j        -   ^  KZ F 
Ͻ   j    !  , 9, tq  P      c W
( ,  F ` P+  hs t  
jQ   (ѩv g    .a0:  kv  m   h  &     h      _`JK PgBS  Pa¡f$     s nrW~r 嚀n lѨ  @  A/ M ȶr   kDh &4?VW uXhYgo E  >t ftţo-      f$   S t u  D  > T  Q
2Nt
O 2
 O&Y{  h/ l n  ^|   W ,+Ƅ= L G w 8  &% p%4 :xx)6 w * 8h 1  s ! 8   
 % mny0 )  K   ;` 6 8` :      1P  
&*      r,2`4    o/    1:13      E ǋ 3G       K   ' "        0 y      m !(% <     y      /   o     _JiUTFu  
 iEs%p4  j V*  7`    $Gw| W ?  wt4? ԝ &(   }0` :p " |4? m   ۡ$~#`{ o} _ + և| _J y%(㧧*P ) \ m6i ` X   h           ҵ:m m c d  %9     `  d Q  @ 1B       X   0      ;(     <  ;H ޿ '
 ,     1e  X	   ]  
 4         @    O L          S`   &P<  6 *    @  du  3    T  `	 
  \P
   ~ "    H u  s/ 88  B   L  A ,U D   H  $   
`       *      < 
 7 #  د@a $x eh   	r +t+     p
 d        
` @Al   +  
c=܂  
   P 
D 
h !
,1   d   [  [c    CpH 
!      x/G x  	3   Bd 
 !>D % D   "^D  5 F  #~D E H$ % $ D  U Jd - % D e L  5 & D  u N = ' D     ăRP R HB _Y4Ĳ^М  [      M dd @ au] : J  l1  ޮ  p  Q  " ˙ ~
  ~BS⺋G +   2 ^
 " Y ,Ƴ    [  jPAE             @ 		T  !A
N   E /ZP@a   $ 8  ɂ	Qt  Ȏ.X   D 1s ԉY 
8v 1  
 .2d AE :f H P Ɔ
F掄«Xj%X  ׄa   R0 ` R (!!/x|   *MlI   /h    bD t9  \!a:Ay
 -Ӏ    rCFa  Hbl 5          C      ! S t  AB p0T  5n Рǎ        h1  %K 0] |Y  ͙9e 'O @} Y  ѡI .5  )S P F}Z  թY n   +W `   Z  ٱN      -- >S %@ 1  ! ۵i  '8È N x bƏG    ˒5W  ysfΟ= &
   ӢU F zuj֯]Ǧ
   ۲u   {wn޿} '  [ p ={   l u  [ dn Z F:r   Yg   yl ϯO ^}{  廧 >|     _   ?,@ 4p T A  A
# B' p /԰C?Ѽl؁' k q' g 9' n 9q L:#.l   o ;1u q. !  H" ,rI% L I$ d ) t J*  rK-   K,   1   L2 ,sM5 L M4 d 9 t N: sO= ̓ i\dQ  k F ӆG  OH   I  RJ1 4SN7  RP5
  Q? TR  f  k 4p q     Ts u R{E  _} UX` 
 Xc -v QM 
< rT Q[ e Zb  v[e   k 7\r -w j  曚 ݱVg 5w^p長^|    }   _ Lw vg 6^  M _ n a #^xb %u`v 5 [+  - c G6Yd C ェ  d KNY a  f s  X 5v ѝ  Yh   yh  N  ]r V  6zj  V ꣱ Zk2 f  ; z ɶ l  F{ o N ݨ .{ 閻n   ]g     f;     p W<a  ~yq 	 <q +  r  & g    s )   C' Q [   [   c7}v  s a ]v y׽ sm w   xߓ } H _]l协y橗 z & z     W;     |  O?   }  g~    } 헿~   _ o{ #  
 ?pId  @F0  c  $ 	^  ]1 AfP  Ğ     50 $ 
]   y - !
m    Pu Sa
q8   ƒ  x !1  *"  D'Fq      7 J S b} D.>Q Zc F/ Q fd 
 X 4  s  G< Q Ƴ    ;  t} y @b   4d" Ȳ-ґ  $#+ 0IBҒ  $'M  Kj   % @  P   < 9 C
 R  de)   +>ϖ   .i@\    e1{y Z    e3  &e2 Δf)   if    &5  Mo*    f9 ) q    T' i uƳ |'; 9O{   '|, ) {T   g 
 |"ԑ T ?zP 1 	 hC'Q f  -F7 Q  4  ܡAE ъ  u\ N ҐƔ{ )Jm
 m T 8 iM  Ӟ ԧC )KSH̠&  K  Q )T *  ;*S jU .Щ  jW  Ջm՟^ *X  D    k=k[EVU  խV  \ j֠ U { +5  ׻  ,_
+R  U  -fb X   qs  sΡ٣(  >8  kt    =@kZ    emj[ Z    Umm_k[ ▶  mn{ [    mqk\ "   enr  \ F  Unu k] b    .iEZ ~W   w {   #    f  ̪$
(   F W   8$     0 'A  @ U0  _ G n0 \a_X  0 7la  # 0 ?\b X "N1 Wlb  3n1 _\c X 2 1 wlc     C  a	= yPr  a =4Y  hC   9 Jf     S  "  u8 
` #  G   eG#  DT   3     @    W"  E" Z,"  "  
E   @ * Ow ӡ  M-jT :դf  U  V  ձ   m-k\ :״浫U  _ 9  v  }la#   6v    g7 Ӗv  } hc  ڶv    ow  w  } pk{ @:L hp - K A    q F ⲎYL ݡ    p 
V.D\  ) h
  -$ UL ` R I  h ^ @̡   T x B Ѡ  A " &E 鑊([ bP & | Wp:  9:L     P  ԣ>u W [ z׵      _'   >v   k?{     ǝ o    >w   {  ݙ.   # o: _x ^ O< ox G o<   s:    !
v c @> 1rx VV  >
Ml  G) { "      a  
  /D E  E-  G At, 
ZTG
]@E  q
E r  5 ` x  hC `,p:  @L  P        	?0 N r@$@,@\ l@t  @|@
 @ @
 @  
 @
    $   $ <ALA4 DA A       A A   AB $ MX?" M@B% $d %<B' B(  &  ' B, B-  ,  - l L  v  : y  L <P ?v؃#@ 6   K k IȲy Oh =  t  " Vh)@ Z  ЄA     v  C3 9 <ȅ   2 w  s7H    <  v  ,NX 
  U h  ˂> 2,  QؼM p  (
 aF 3Fb< b\Fel d|Fd Ff  i Fg Fj  j Fm  l FlGn qGo4Gr< r\Gul t|Gt4Fp  (  z G2 G{  { G~  }H}HȂ$H DH Lȃ  o  $c O 4u03  > 
t  (@3;`  b#@ R 8vزy R`3I 6[@  [  X4W =QP[ J   S s  4    4   LS@ K dP 3 j  *{CX3D    A(    @ F  ۻE  q؄z G0[H TH lH  ˸ ˻ ˹ K  ˼  -   z$L23L ,L <  LL |LǌLĜ ƤLȴLɬLͼ   L  L M   $M 4M  LoX26  S VD3  :>h   I 8  Q 6 C  ^  ~   w`  Їlp ` VX TxH c     F   S _  ȃ0  vC <E; 2n  1; 1 s   K R@u (#   LH  d 
 = K  5 EP}P P  P
 P
mP    
 P
 PQ /K`  4 %Q]QmQoH e uQ Q Q Q Q Q      Q M n  1; $ERaR%m % R(  ' R' R)  , R* R-  - Rn  \ V  Cӄ S  B3F  t(  ;  Kc W@Su C # @ @xt , 
;8 rI RЅ   D Ė  KHD9  F J C ([50 ^X  ( O( oЄ ,  4 T04X 9r0  + R(8r   ^   V_
 _-Vb= aMVa]VcU feVd Vg  g Vj  i Vi  q    WrWr Vs
 s t-WtmWuuWv}Wy Wz]W{  {  |  `
 n  q X+#؀X -؄=X mX }X  ؅ X  X  X  ، Q 9sp  ؐu H sx   ؒ O OR`7 5Y  ْ=ٔm  NH s  [ T  u0 -Ec R s؄      0 
   =  t  3   OU 4t`C۱[ \F ZI; UH[ u۶ Z  [  ۷ ۸ [  [  ۽ ۾ [ \ -ܿ5  =\ M\ %  u  E\ȝ\ɭ ǵ Ƚܽ] T \ϥ    =  %  -] =] ] 5  M  ]]ם]إ] 
  s ܥOT    ] E]    - T^  Z 
?    52Q EU:   =            %    %      | pG{ ç?2  i;q=  Z  ܣ.a!ᣌ    & : tZ_j! | =r<  a ) ԾD@/      @    @               /   /   F:\WX9\Gabarit\WD\Source\US\110 System                 /   /   F:\WX9\Gabarit\WD\Source\US\110 System               
        H0Р   

GestionDesCalculs.wdc
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
      	arretDemandé est un booléen
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1631270205988278277
     type_code : 27
     code : |1+
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1631270205988343813
     type_code : 28
     code : |1+
      procédure Destructeur()
      SignalDétruit(:nomThread)
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1631270205988409349
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalTraitement.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() :booléen
      
      Logger.Verbose(ChaîneConstruit("Demarre acquisition pour le traitement id=%1", :id))
      
      si :nomThread="" alors
      	:nomThread = ChaîneConstruit("thTraitement%1",:id)
      	Logger.Verbose(ChaîneConstruit("Construit traitement id=%1", :id))
      	SignalCrée(:nomThread,signalManuel,signalFermé,partageAucun)
      fin
      :arretDemandé=faux
      SignalModifie(:nomThread,signalFermé)
      ThreadExécute(:nomThread,threadNormal,:callbackThread, :id, :nomThread, ()=>arretDemandé)
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1631270205988474885
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalTraitement.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() :booléen
      
      Logger.Verbose(ChaîneConstruit("Arrete acquisition pour le traitement id=%1",:id))
      
      :arretDemandé=vrai
      SignalModifie(:nomThread,signalOuvert)
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)=threadEnCours,:nomThread)
      
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1631270205988540421
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalTraitement.Parametre
      PROCÉDURE Parametre(params)
      
      Logger.Verbose(ChaîneConstruit("Parametre acquisition pour le traitement id=%1, p=%2", id, params))
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

IAcquisitionSignal.wdc
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
      	hérite IClientSocket
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627981240561672399
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : EnfileBuffer
     procedure_id : 1630627429913819569
     type_code : 12
     code : |1+
      procédure abstraite EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1630627593126678127
     type_code : 12
     code : |1+
      procédure abstraite DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1631009419835772927
     type_code : 12
     code : |1+
      procédure abstraite TaillefileBuffer() : entier
      
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

IClientSocket.wdc
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
      	ip est une chaîne
      	nomThread est une chaîne
      	dernierMsgEnvoye est une chaine
      	dernierMsgRecu est une chaine
      	
      	protegee
      		arretDemandé est un booléen
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
      procédure Constructeur()
      
      :id = &:id
      :nomThread = "ThreadIClientSocket"+:id
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627185554619804319
     type_code : 28
     code : |1+
      procédure Destructeur()
      
      MutexDétruit(:mutex)
      SignalDétruit(:nomThread)
      Logger.Verbose("détruit IClientSocket, mutex=" + :mutex)
     type : 655360
   -
     name : EstActif
     procedure_id : 1627185554619869855
     type_code : 12
     code : |1+
      procédure VIRTUELLE EstActif() : booléen
      
      renvoyer ThreadEtat(:nomThread)=threadEnCours
     type : 458752
   -
     name : EstArretDemande
     procedure_id : 1643629433866877340
     type_code : 12
     code : |1+
      procédure VIRTUELLE EstArretDemande() : booléen
      
      renvoyer :arretDemandé
     type : 458752
   -
     name : DemarreEcouteSocket
     procedure_id : 1627185554619935391
     type_code : 12
     code : |1+
      procédure virtuelle DemarreEcouteSocket()
      
      :arretDemandé=faux
      SignalModifie(:nomThread,signalFermé)
      
      ThreadExécute(:nomThread,threadNormal,()=>{
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
      procédure VIRTUELLE ArreteEcouteSocket()
      
      Logger.Verbose(ChaîneConstruit("Arrete d'écouter la socket %1",:canalSocket))
      :arretDemandé=vrai
      SignalModifie(:nomThread,signalOuvert)
     type : 458752
   -
     name : Demarre
     procedure_id : 1627185554620000927
     type_code : 12
     code : |1+
      procédure  abstraite Demarre() : booléen
      
     type : 458752
   -
     name : Arrete
     procedure_id : 1627185554620066463
     type_code : 12
     code : |1+
      procédure  abstraite Arrete() : booléen
      
     type : 458752
   -
     name : EcouteSocket
     procedure_id : 1627185554620131999
     type_code : 12
     code : |1+
      procédure  ABSTRAITE EcouteSocket()
      
     type : 458752
   -
     name : Ecrit
     procedure_id : 1627185554620263071
     type_code : 12
     code : |1+
      procédure virtuelle Ecrit(msg est chaine) : booléen
      
      Logger.Debug("client["+:nomThread+"] envoie="+msg)
      :dernierMsgEnvoye=msg
      renvoyer SocketEcrit(:canalSocket,msg)
     type : 458752
   -
     name : Lit
     procedure_id : 1627185554620328607
     type_code : 12
     code : |1+
      procédure VIRTUELLE lit() : buffer
      
      buf est un buffer = SocketLit(:canalSocket,faux,10ms)
      si ErreurDétectée ALORS
      	LoggerErreur.EcritLigne(ErreurInfo(errMessage))
      	:arretDemandé=vrai
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
      procédure Build()
      
      :mutex = "mutex"+:id
      
      MutexCrée(:mutex,Faux,partageAucun)
      SignalCrée(:nomThread,signalManuel,signalFermé,partageAucun)
      
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

IConnexionClientSocket.wdc
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
      	NomSocket est une chaîne 
      	AdresseSocketServer est une chaîne
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627185979844013368
     type_code : 27
     code : |1+
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627185979844078904
     type_code : 28
     code : |1+
      procédure Destructeur()
     type : 655360
   -
     name : AjouteClient
     procedure_id : 1627185979844144440
     type_code : 12
     code : |1+
      procédure abstraite AjouteClient(client IClientSocket dynamique):booléen
      
     type : 458752
   -
     name : SupprimeClient
     procedure_id : 1627185979844209976
     type_code : 12
     code : |1+
      procédure ABSTRAITE SupprimeClient(client IClientSocket dynamique):booléen
      
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1627185979844275512
     type_code : 12
     code : |1+
      procédure abstraite RecupereClients() : tableau de IClientSocket dynamique
      
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

IGestionDesCalculs.wdc
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
      	callbackThread est une procédure
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1638002515556427564
     type_code : 27
     code : |1-
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1638002515556493100
     type_code : 28
     code : |1-
      procédure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1638002515556558636
     type_code : 12
     code : |1+
      procédure abstraite DemarreAcquisition() :booleen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1638002515556624172
     type_code : 12
     code : |1+
      procédure abstraite ArreteAcquisition() :booléen
     type : 458752
   -
     name : Parametre
     procedure_id : 1638002515556689708
     type_code : 12
     code : |1+
      procédure abstraite Parametre(params)
     type : 458752
  properties :
   -
     name : id
     identifier : 0x16bb5a9c00eee72c
     type_code : 103
     p_codes :
      -
        code : |1+
         procédure id() : entier
          renvoyer :id
        type : 1966080
      -
        code : |1+
         procédure id(Valeur est un entier)
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

ISignalDessine.wdc
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
      	implémente IAcquisitionProc
      	id est un entier = 0
      	champImage est une wl.Image
      	arretTimerSysDessin	est un booléen
      	fileAffichage est une file de IAcquisitionSignal.Str_DATA_Acquisition
      fin
       
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627190184644779469
     type_code : 27
     code : |1-
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627190184644845005
     type_code : 28
     code : |1-
      procédure Destructeur()
     type : 655360
   -
     name : Parametre
     procedure_id : 1627190433753202688
     type_code : 12
     code : |1+
      procédure abstraite Parametre(champImage)
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1627191786668085496
     type_code : 12
     code : |1+
      procédure abstraite DemarreAcquisition() : booléen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627191833912802288
     type_code : 12
     code : |1+
      procédure abstraite ArreteAcquisition() : booléen
     type : 458752
   -
     name : DessineDansImage
     procedure_id : 1627990788310338616
     type_code : 12
     code : |1+
      procédure abstraite DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644442558556870
     type_code : 12
     code : |1+
      procédure abstraite DebutDessin()
     type : 458752
   -
     name : SauveImageJpeg
     procedure_id : 1631354043776558121
     type_code : 12
     code : |1+
      procédure abstraite SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
     type : 458752
   -
     name : PrepareDessin
     procedure_id : 1829904471857195959
     type_code : 12
     code : |1+
      procédure abstraite PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
     type : 458752
   -
     name : Affichage
     procedure_id : 1894581804520631272
     type_code : 12
     code : |1+
      procédure abstraite Affichage()
      
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c5db19074789
     type_code : 103
     p_codes :
      -
        code : |1+
         procédure id() : entier
         renvoyer :id
        type : 1966080
      -
        code : |1+
         procédure id(Valeur est un entier)
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

ISignalEnregistrement.wdc
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
      	implémente IAcquisitionProc
      	id est un entier = 0
      fin
       
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627195780988207123
     type_code : 27
     code : |1-
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627195780988272659
     type_code : 28
     code : |1-
      procédure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627195927017260696
     type_code : 12
     code : |1+
      procédure abstraite DemarreAcquisition() : booléen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627196017211660245
     type_code : 12
     code : |1+
      procédure abstraite ArreteAcquisition() : booléen
     type : 458752
   -
     name : Parametre
     procedure_id : 1627196240550011434
     type_code : 12
     code : |1+
      procédure abstraite Parametre(nomFichier)
     type : 458752
   -
     name : EcritDansFichier
     procedure_id : 1627989705976820618
     type_code : 12
     code : |1+
      procédure abstraite EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c5b91900c365
     type_code : 103
     p_codes :
      -
        code : |1+
         procédure id() : entier
         renvoyer :id
        type : 1966080
      -
        code : |1+
         procédure id(Valeur est un entier)
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

ISignalTraitement.wdc
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
      procédure Constructeur()
     type : 589824
   -
     name : Destructeur
     procedure_id : 1626144540630955912
     type_code : 28
     code : |1-
      procédure Destructeur()
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627219562222878244
     type_code : 12
     code : |1+
      procédure abstraite DemarreAcquisition() :booleen
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627219665302379617
     type_code : 12
     code : |1+
      procédure abstraite ArreteAcquisition() :booléen
     type : 458752
   -
     name : Parametre
     procedure_id : 1627976928344035087
     type_code : 12
     code : |1+
      procédure abstraite Parametre(params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1627990650870500942
     type_code : 12
     code : |1+
      procédure abstraite Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
     type : 458752
  properties :
   -
     name : id
     identifier : 0x1697c53e18f3e260
     type_code : 103
     p_codes :
      -
        code : |1+
         procédure id() : entier
          renvoyer :id
        type : 1966080
      -
        code : |1+
         procédure id(Valeur est un entier)
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

ITreuilClient.wdc
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
      	
      privé
      	bufferLecture est un buffer
      fin
      
      EnumTreuilType est une énumération
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
      	SOCKREP_TYPE_TREUIL_NDEF		= "NonDéfini"
      	
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
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_ENERGIE+<xxxx>) avec xxxx = 0000 à 0084 en V
      	SOCKMSG_FIXE_ENERGIE		= "VPRO"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GOTO+<xxxx>) avec xx = 0000 à 9999 en cm
      	SOCKMSG_DEPLACE_SONDE_VERS	= "GOTO"	
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_FIXE_FREQ+<xxxx>) avec xxxx = 0000 à 0020 en Hz
      	SOCKMSG_FIXE_FREQ			= "CLAC"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_GAIN+<xxx>) avec xxx = 001 à 016
      	SOCKMSG_FIXE_GAIN			= "COMM_"
      	
      	// usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_SENS_YYY)
      	// SocketEcrit(<SocketCible>,SOCKMSG_PWM_YYY+<xxxx>) avec xxxx = 0001 à 0020
      	SOCKMSG_PWM_UP				= "PWMU"
      	SOCKMSG_PWM_DWN				= "PWMD"
      	
      	// usage
      	// 0 : temporel
      	// 1 : spatial
      	// SocketEcrit(<SocketCible>,SOCKMSG_MODE+<xxxx>) avec xxxx = 0000 à 0001
      	SOCKMSG_MODE_TEMPOREL		= "MODE0000"
      	SOCKMSG_MODE_SPATIAL		= "MODE0001"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 à 4000
      	SOCKMSG_DEPLACEMENT_UP		= "DEPU"
      	SOCKMSG_DEPLACEMENT_DOWN	= "DEPD"
      	
      	//usage
      	// SocketEcrit(<SocketCible>,SOCKMSG_DEPLACEMENT_YYY+<xxxx>) avec xxxx = 0000 à 4000
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474376347934981
     type_code : 28
     code : |1+
      procédure Destructeur()
     type : 655360
   -
     name : EcouteSocket
     procedure_id : 1623533668379034263
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE EcouteSocket()
      
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
      procédure privée _synchroniseTrameDansBuffer() : booléen
      
      pour i=1 _a_ bufferLecture..Taille
      //	si :bufferLecture..Taille>=3 _et_ :bufferLecture[1 a 3]<>"DEB" ALORS
      //		:bufferLecture = :bufferLecture[2 À ]
      //	sinon
      //		SI :bufferLecture..Taille>=CST_TAILLE_STRUCT ALORS
      //			SI :bufferLecture[CST_TAILLE_STRUCT-3 À CST_TAILLE_STRUCT-1] = "FIN" ALORS
      //				Logger.Verbose("buffer synchro")
      //				RENVOYER Vrai
      //			SINON
      //				LoggerErreur.EcritLigne("buffer non synchro pas de fin trouvé, reset buffer")
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
      procédure abstraite ParseDonnees(buf est un buffer)
      
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

Logger.wdc
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
      		affTrace est un booléen = faux
      	globale PRIVÉE
      		nomFichier est une chaine
      		handleFic est un entier=-1
      		buffer est un buffer
      		id est entier
      		mutex est une chaîne
      		messageFile est une file de chaine
      
      	
      	constante BufferMax = 1 000 000
      fin
      
      NiveauEnum est une énumération
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628652419095129401
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : EcritLigne
     procedure_id : 1628652419095194937
     type_code : 12
     code : |1+
      procédure globale EcritLigne(msg est chaine, niv est NiveauEnum, flush est un booléen=faux, enfileMsg est un booléen = faux) : entier
      
      si niv > niveau ALORS
      	renvoyer 0
      FIN
      
      si affTrace ALORS
      	wl.trace(HeureVersChaîne(Maintenant,"HH:MM:SS:CC") + " : " + V(niv) + ThreadCourant()+"=> " + msg)
      FIN   
      
      oc est un entier = -1
      dbMsg est chaîne = V(niv) + ThreadCourant()+"=> " + msg
      dbMsg = HeureVersChaîne(Maintenant,"HH:MM:SS:CC") + " : " + dbMsg + rc
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
      procédure publique globale Init()
      
      nomFichier = "trace.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCréationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrée(mutex,Faux,partageAucun)
     type : 458752
   -
     name : Quit
     procedure_id : 1628652419095326009
     type_code : 12
     code : |1-
      procédure publique globale Quit()
      
      SI buffer<>"" ALORS
      	SI EcritLigne("",niveau, vrai)=-1 ALORS
      		Trace("Erreur d'écriture dans fichier "+nomFichier)
      	FIN
      FIN
      fferme(handleFic)
      handleFic=-1
      MutexDétruit(mutex)
      mutex=""
      SupprimeTout(messageFile)
     type : 458752
   -
     name : Log
     procedure_id : 1628652419095391545
     type_code : 12
     code : |1+
      procédure publique globale Log(msg est chaine)
      
      renvoyer EcritLigne(msg,NiveauEnum.ELog)
     type : 458752
   -
     name : Debug
     procedure_id : 1628652419095457081
     type_code : 12
     code : |1+
      procédure publique globale Debug(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EDebug)
     type : 458752
   -
     name : Verbose
     procedure_id : 1628652419095522617
     type_code : 12
     code : |1+
      procédure publique globale Verbose(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.ETrace)
     type : 458752
   -
     name : V
     procedure_id : 1628652419095588153
     type_code : 12
     code : |1+
      procédure privée globale V(niv est NiveauEnum)
      
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
      procédure publique globale Erreur(msg est chaine)
      
      RENVOYER EcritLigne(msg,NiveauEnum.EErreur)
     type : 458752
   -
     name : Message
     procedure_id : 1893788137531391711
     type_code : 12
     code : |1+
      procédure publique globale Message(msg est une chaine)
      
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

LoggerErreur.wdc
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
      		affTrace est un booléen = faux
      	globale privee
      		nomFichier est une chaine
      		handleFic est un entier=-1
      		buffer est un buffer
      		id est entier
      		mutex est une chaîne
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628655326825300198
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : EcritLigne
     procedure_id : 1628655326825365734
     type_code : 12
     code : |1+
      procédure globale EcritLigne(msg est chaine, flush est un booléen=faux) : entier
      
      SI affTrace _et_ logger.affTrace=faux ALORS
      	WL.Trace(HeureVersChaîne(Maintenant,"HH:MM:SS:CC") + " : " + "[ERREUR] " + ThreadCourant()+"=> " + msg)
      FIN
      
      logger.Erreur(msg)
      
      oc est un entier = -1
      dbMsg est chaîne = "[ERREUR] " + ThreadCourant()+"=> " + msg
      dbMsg = HeureVersChaîne(Maintenant,"HH:MM:SS:CC") + " : " + dbMsg + rc
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
      procédure publique globale Init()
      
      nomFichier = "erreur.log"
      handleFic =-1
      id =&id
      mutex ="mutex"+id
      
      SI handleFic=-1 ALORS
      	handleFic = fOuvre(nomFichier,foCréationSiInexistant+foAjout+foLectureEcriture)
      FIN
      
      MutexCrée(mutex,Faux,partageAucun)
     type : 458752
   -
     name : Quit
     procedure_id : 1628655326825496806
     type_code : 12
     code : |1-
      procédure publique globale Quit()
      
      SI buffer<>"" ALORS
      	SI EcritLigne("", vrai)=-1 ALORS
      		Trace("Erreur d'écriture dans fichier "+nomFichier)
      	FIN
      FIN
      fFerme(handleFic)
      handleFic=-1
      MutexDétruit(mutex)
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

main.wdw
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
         
         EnvoieSocketAuServerSurSelection(colonne1,"ACQ_"+Complète(Hasard(1,999),8," "),Client_acquisition)
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
         
         EnvoieSocketAuServerSurSelection(Colonne1,"COMM"+Complète(Hasard(1,999),8," "),Clients)
         
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
         
         //EnvoieSocketAuClientSurSelection(Colonne1, "id__"+Complète(Hasard(1,999),8," "))
         EnvoieSocketAuClientSurSelection(Colonne1, Complète(Saisie1,8," "))
         
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
    name : arrête_acquisition
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
         ExécuteTraitement(arrête_acquisition,trtClic)
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
         			buf.tabBuffer[j] = PartieEntière((Sinus(20*j/AcquisitionSignalClient.Size_MSG_Socket_RX_ACQU * 180/3.14) + 0.5/10*i)*1000)
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
         grSupprimeSérie(Graphe1,1,grDonnées)
         y est reel
         	rd est un entier = Hasard(5,30)
         pour x=1 _a_ 100
         	y = Sinus(2*180*x/rd)
         	grNuageAjouteDonnéeXY(Graphe1,1,x,y)
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
         grCrée(":nomGrapheVirtuel",grCourbe)
         //grDestinationImage(:nomGrapheVirtuel, :champimage)
         grDestinationChamp(":nomGrapheVirtuel",{"main.Graphe1", indChamp})
        type : 14
      -
        internal_properties : CAAAAAgAAAAUeUSjYmoUVqFbJA/Do3wVzpDC8bnedWgwaB+s7TNH/rK+IdE4eJMnw0FVrzoCYnGa0/jJGmMQiCYXOwU7vhEMll4/mya/+8LCQZF7jwKE6g8ePT4yDzlAZEbHsQHPrhv4tlEBDp6FPQeutmP3GrdNHhie50IW1phL1IJ+UA4yUDdvFQdflbawB+jlMMMT5CjfXiVbncQRhuDnfvTTaxR6VbBzlRnLU8hdGsu/8t/b8FCch+152Lqvif+eyLYaiBtepB1U9f2dnA==
        code : |1+
         
         grSupprimeSérie(":nomGrapheVirtuel",1)
         x,y sont des réels
         rand est entier =random(3,20)
         POUR i=1 _a_ 100
         	y=Sinus(2*180*i/rand)
         	grAjouteDonnée(":nomGrapheVirtuel", 1, y)
         FIN
         
         grDessine(":nomGrapheVirtuel")
        type : 18
      -
        type : 161
        enabled : false
  -
    name : Graphe2
    identifier : 0x29619a4f0a219a9b
    internal_properties : CAAAAAgAAAATCpClykb7GGukUNUDyh7wBKXXbXcYrpVtpGLgNnT7lBiCVulLj5/JXHaiZi7kVGHr8u7BqteYJULv2/bwIvVU3GVjd/loQzAuIUagPTi1rY8rZTY8G+hG7rs/fhAAS9t91+Xyvo37ehjoFywlbryAjT+yy/4Jj1YSKEY6Vdsb5iAw1B2rkTZTIbAxeslER5S1vv8RfX1PYjqniKhZ4EYCw2pkQ849RTDZSr2OolVs3gcgQouJ9hLdAPqSSBOzyx00yfMdOF3jAdwaiBLorzmDtU+GGuF0FbX6gwH84jkW4CY0wo0s3yU/FXkFS3/FkNAaGRPCKBjjAcKjZ8PZYtsJ+x/b0Npsm4yn4/JkGZkKCeWQhmP+77uNheWB+JHFGYlijROYAfTqwxM6ayQhgtV7cLY7Z7cq2i5q693+SVhFKeJbexZI+g5SSBLIMQomGBH/F0UulXKm7eBjUFU0bEdR/iyvzQxUjRIP5pO6Z/BZmZe/oJvBj09y4RktYetDiL+zobpy8ofyoVurRlXxAZC5ySWgZ/gz4IbGopSLUr9vfZAc/pmq6S5heeAjePIrlq9p7PLj8En0tAEs8YWld+YZZv+9q7Jk78YKGKqWDn88VE+MJoTl/7o/ZnjQDwWltCiJuwNe2RXjTwHqwYcbGVYIgj4KVRehPHLgb3qFiN3oh8rhR0oPq8X+rKFSQlSFXdK0sxyvRNVr2rIIlxdKx3yPHBzEKcw2L2s4G1vrOye6VoedLqBg7AxiL/M1sI24TthwBugbA9GzY1RegYfPO9cIhNsuQ5Ii8H9Sy7UCiKmTFejJbjYLRijnbS3LOBvtzq4Hg25depohbWbeKDpM6VS6xcgRUQoYv9DvByMp2SjjL1quDjrwBVkw+z08oLxzeHt+PTzl3n6lmIJ7iTNujNVzL8yXnf4q5JR6muIXlLkyccosb5uoerVZVzGRHiXYdeHpLLZG+3tRm0jTH3cPRIKYA1ZpK0Xnma+flasLkmDmDlVUZdNrD748ecnPs4bAt8eXFJs6NMoBlu23C/deta1NKu8WDlEgBVV1xNgPuxz5WZPwBYsyJue4PS/fh1CM0FT1pSZNrSIxz5zOEo03S3BYO+wkyAxU57/OB2dNF93GV66Eocjgq8xXDObmY2nBGvwbuCp6lMU6sUZ8UCpzGg1NgOJAd5AiNB4pmtLDtmR99Z0KekPcTggS23c=
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
    name : démarre_client_inconnu
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
    internal_properties : CAAAAAgAAABcB/xxXBE0XVvZMlceXdPEnh+iyJWaONspASsy24BD8qkcDN8b9Th4N1vK8DYtK9XcTl+OcHQynkvbuWK27iIKm1zacpPcnDsLfmxk9MfZivXrQkCYFLm1y9IM5qPR1LPxY1i+ImW3G6uGAlQTfuRpr7rmSJRQQyC1XqjvGywSNKBez8+HJzp2BN+BmkjJ10rC9NTed4woJAlsVrEEofXwEVFi/uNIBtob2ibQ8kbM/CWHrE6Tw4heFBoY7UhoJlE+KLxV3ob7xyzdH3M2SfJD//WERFT1KzjvjEyVGcFBdIeudpaoTj44g78KxJFgaidK4y6LpJUI/1I3snhVIvTq81lyvBvuW4cFf78eXvBjO1C6lVn/8hNOiFsXgyLVAQEnQKZseDNCW3Mb9ycem2HR9MDu/iOoac5h3mTH+Ur8ehDHImET9ReDUI6KFXxdd/E4g2pryIx9qbSaqDUW8RVXC/yXVBApT2HUtHZ3riCPzQ5N4YMuAZ9H5ps+3cd6BoMmW5ZEPM4O1w1BKLdjNu83xzj5ls+RY4uhnMh6oAQ0dqBn09h3iFaP0VtmQeiB/jghEAbgeVKzsxQmnszX+H1LKoD0PhH0GplJOBWKYZpvK2WZvWCBCsKtoDS3kdcmheHfwSCpr2MBJhDm8cL+w2+mncnWQgvi0VBa5AQkHHF9rDZ/HEr8QKKB9Qb/dnjjby1hLbmNUA731bIhDNnhz+YqvhzoABI1Q5jb9xI7P8Gat4Ae1ebC3HXiQmNLkuHE/gvz72TtiEwvCcLcYmdXFl4TnrIY49XXQvMeayrXWpzcsT+Xfp80/rX4shRcqqLwsZKmZvqI87ILXIQvn28IaPn3gbEIrMlwdz6vEKMOhUFLzPq0dx+Ri1hfWp354cJbakFEFQktuoQOT6Tr98tIGvT5QS7siKskXrZgnxx/1wY2nxYCsR3mgBQX41LNOoKq+90lsokY3tFrgKig3jV7o2o1skyr710MKlXF4tGljsUN1zkIvaUPUJ+UX5yt+UnK6M03xcsFHJENPfiiNSj9tOje+LIht6pdK7eEzyrJc2asWgZhogYcuafW7HtvgTLdigR56LB20oC483a1uiBypyAH1DZ9RRY7nfRquRQDL69Plptyr6yB4ZOjlBpYQVfyk0/c5tR+7tiuO+lrKoNrNBXn4RtLz3s=
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
         		Multitâche(-10)
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
    name : démarre_acquisition
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
    name : démarre_client_app
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
    name : Déco
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
         
         ExécuteTraitement(Energie,trtClic)
         ExécuteTraitement(gain,trtClic)
         ExécuteTraitement(Clac,trtClic)
         ExécuteTraitement(VitDOWN,trtClic)
         ExécuteTraitement(VitesseUP,trtClic)
         ExécuteTraitement(GOTO0400,trtClic)
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
       internal_properties : CAAAAAgAAAC0lqQ1r1PXou6iBuTZYyFao8NmmH2aJHV/W4JYxvt4Fd0ucgBSnHAURVWgTGJZ1exDBj8kEd+OGdeGiEDj/ceigLOEE4Yy3SlmmubYxfe1u6GtW0g19g/j0azw9cpaS0rqohZTzJsM6pA=
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
       internal_properties : CAAAAAgAAABUtsSVD/P3Qg5CZgT5QwF6gyNGuJ36BNXPnnD+FKlW12vQACbgWjoCO4tOqoNvH4pHCAPQEtSBCtph+4/1WzEoJvmChSDYt3PMEPzS/92/OwENO2gV1u/D8YzQ1eo6a2qKwjazbDusCvA=
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
       internal_properties : CAAAAAgAAABl28KPSOIQxZhJaI1nR+NxUUNRq78hZaF+c4aVha/9vkcwjmSOeFghK2k4f3aBFCCgT2lkLFhgKJLhanzBoZ5i50bFu21kWOsvmfFYTDWp019Tfe0/N4D1psrZJkl+xjC2u0IUkTofPKFSSFq+
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
        properties : {}
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
      PROCEDURE MaFenêtre()
      
      LoggerErreur.Init()
      LoggerErreur.affTrace = Vrai
      
      Logger.Init()
      Logger.affTrace = Vrai
      Logger.niveau = Logger.ETrace
      
      FabriqueClient.AdresseIP=NetAdresseIP()
      server est ServeurConnexion dynamique <- ServeurConnexion.MonInstance
      
      
      BufAEnvoyer est buffer
      buf est une iAcquisitionSignal.Str_DATA_Acquisition
      arreteTimerSysAcq est un booléen=faux
      
      
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
      procédure ConnecteSockets(nomSock_ est chaine, port est entier, commentaire est chaine, champTable_ est champ)
      
      nomSock est chaine = nomSock_
      champTable est un champ <- champTable_
      SI SocketConnecte(nomSock, port, FabriqueClient.AdresseIP) ALORS
      	SocketChangeModeTransmission(nomSock, SocketSansMarqueurFin)
      	TableAjoute(champTable,nomSock+tab+commentaire+TAB+"")
      	SocketEcrit(nomSock,commentaire)
      	SI ErreurDétectée ALORS
      		TableSupprime(champTable,TableCherche(champTable.Colonne1,nomSock))
      		SocketFerme(nomSock)
      		retour
      	FIN
      	TimerSys(()=>{
      		lit()
      	},10cs)
      FIN
      
      procédure interne lit()
      	quand Exception 
      		FinTimerSys()
      		retour
      	FIN
      	msg est chaine = SocketLit(nomSock,Faux,10cs,100)
      	SI ErreurDétectée ALORS
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
      procédure EnvoieSocketAuClientSurSelection(nomSock est chaine, msg est chaîne)
      
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
      procédure EnvoieSocketAuServerSurSelection(nomSock est chaine, msg est chaine, table_ est champ)
      
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
      procédure FermeSocket100(sock,table_ est champ)
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
      procédure EnvoieSocketAuxClients()
      
      indexTab est un tableau d'entiers
      pour i=1 _a_ Clients_socket..Occurrence
      	si chaineOccurrence(Clients_socket[i].Colonne3,"thtreuiler",SansCasse) ALORS
      		TableauAjoute(indexTab,i)	
      	FIN
      FIN
      
      pour i=1 _a_ indexTab..Occurrence
      	EnvoieSocketAuClientSurSelection(Clients_socket[indexTab[i]].Colonne1, Complète(Saisie1,8," "))	
      FIN
     type : 458752
   -
     name : TimerAffichage
     procedure_id : 1894471329520054136
     type_code : 14
     code : |1-
      procédure TimerAffichage(tr est un TreuilClient dynamique)
      
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
      	POUR i=1 _À_ tr.dessine.fileAffichage..Occurrence
      		Défile(tr.dessine.fileAffichage,elem)		
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

Mock_AcquisitionSignal.wdc
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
      	tEcoute est un tableau de chaînes
      	nb est entier
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1628661610475131291
     type_code : 27
     code : |1+
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628661610475196827
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1628662121583043312
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre():booléen
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1628662125878077863
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete():booléen
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : EcouteSocket
     internal_properties : CAAAAAgAAABLoxn892W5mHHhms9bbaHUibGC+psvlT5AtIg6/3RBeF3LmWq0q9orS6K+Tov7ThQU1/a8cHpW9Aa70c4hiQMWcB3Xd8xbezoF+zx0ELVX3Uco4Zbb9FwG3TUtiyLqfp+guClfHNfToPe9s0IRfgZVaqkzdjW/StWhhxH7EJit3qG0TqqmIpZPy4t+69VnHyklT9OMcsbZf76R+Gc5czumRSOl/acNRolwrD0yvJHQ/lPc099lnwaAO1f4I0TrRyMeubdQQtTG0Iag
     procedure_id : 1628662134468079522
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE   EcouteSocket()
      
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
      // Redéfinition de la méthode IAcquisitionSignal.EnfileBuffer
      PROCÉDURE  EnfileBuffer(buf IAcquisitionSignal.Str_DATA_Acquisition)
      
     type : 458752
   -
     name : DefileBuffer
     procedure_id : 1630629375563228054
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IAcquisitionSignal.DefileBuffer
      PROCÉDURE  DefileBuffer() : IAcquisitionSignal.Str_DATA_Acquisition
      
      buf est IAcquisitionSignal.Str_DATA_Acquisition
      renvoyer buf
     type : 458752
   -
     name : TaillefileBuffer
     procedure_id : 1631009656063354661
     type_code : 12
     code : |1-
      // Redéfinition de la méthode IAcquisitionSignal.TaillefileBuffer
      PROCÉDURE  TaillefileBuffer() : entier
      
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

Mock_ClientSocket.wdc
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
      	tEcoute est un tableau de chaînes
      	nb est entier
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1627186263326469911
     type_code : 27
     code : |1+
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1627186263326535447
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1627186263326600983
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre():booléen
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Arrete
     procedure_id : 1627186263326666519
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete():booléen
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : EcouteSocket
     internal_properties : CAAAAAgAAABLoxn892W5mHHhms9bbaHUibGC+psvlT5AtIg6/3RBeF3LmWq0q9orS6K+Tov7ThQU1/a8cHpW9Aa70c4hiQMWcB3Xd8xbezoF+zx0ELVX3Uco4Zbb9FwG3TUtiyLqfp+guClfHNfToPe9s0IRfgZVaqkzdjW/StWhhxH7EJit3qG0TqqmIpZPy4t+69VnHyklT9OMcsbZf76R+Gc5czumRSOl/acNRolwrD0yvJHQ/lPc099lnwaAO1f4I0TrRyMeubdQQtTG0Iag
     procedure_id : 1627186263326732055
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE   EcouteSocket()
      
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

Mock_SignalDessine.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628665252636558664
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Parametre
     internal_properties : CAAAAAgAAAADBTi1kFxz4gXhpQHhLflPeR/+hUHry48aM9g4kUhpKHg1I6Y0JZo2Be75ynKmDtWbBpqnIfW1FDonFyNKOfL/emoA5pKqw6TFVt/VNCTuSWtzl+dW79WREl2GSf+iiuaD1H2BcbkmKU+37qShFiuqL/lkMOc9Aj1XyjwrFGSOu262kwZGstqjAQBHZVVfZAwgd4q3Z9ZKwsH+R6gKhts8AXS6QTvlQBQQi0FUUVl3Jadj9pR5d9uhEu8ozsj8RogzXkMh+DmlEv/UtSj7e6NWhEr89w==
     procedure_id : 1628665321356575141
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.Parametre
      PROCÉDURE  Parametre(champImage)
      
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1628665329946577019
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() : booléen
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628665334241611617
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() : booléen
      RENVOYER Vrai
     type : 458752
   -
     name : DessineDansImage
     internal_properties : CAAAAAgAAAAMeMKRJSYnv+N7MFbI09Aa2jX0KCjnw9ay7jTGw5AXGTVwrJj2FXJARTq6jgMPDZrPo9wPLzqParxpV9LxiQsWNNUPo/z7q+bZawRgiDXXXZvgYVJ7pLwa0SUtZ3JqZgec4FHLFNFJ7tWPQUxvUAQrIONZqIMJmMNXMXddtgrTpP8GXFxkfOxdic0cFStxpa1zG2HreaTSynMrCrxIcARxpJRroZM96hZEoIjva8rJpZifrPRo9cKqwfpNNRY4bw/e/XbPmhV1Bctl
     procedure_id : 1628665342831613620
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalDessine.DessineDansImage
      PROCÉDURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
      
      RENVOYER Vrai
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644807634925418
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.DebutDessin
      PROCÉDURE  DebutDessin()
      
     type : 458752
   -
     name : SauveImageJpeg
     internal_properties : CAAAAAgAAAAMeMKRJeYnP+N7sNbI09CaWrX0qKhnQ9YybjTGQxCXGTVwLJj2FXLARTo6DgMPDRpPI9yPr7oPajzpV9JxCYsWNNWPo/z7q2bZa4TgiDXX3RtgYdL7JDwaUaUtZ/Lq5gec4NHLFBEJLhVPgYyvEETr4CMZaENJWAOX8bed9soTZL9GHBwkvKydyY1cVWsx5W0zWyGrOeSSCrPrSvyIMEQxZNQrYVN9qlaEYMgvq4oJZVjfbLQoNYLqAbqNdVb4r8+ePTaPmhV1Bctl
     procedure_id : 1631354481865945909
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalDessine.SauveImageJpeg
      PROCÉDURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
      
      renvoyer vrai
     type : 458752
   -
     name : PrepareDessin
     internal_properties : CAAAAAgAAACyAh9Ocv3onOXLS4XRdrCvKrEpZ/gulcIby8mVXAfC7xdy9XNUnDMMagaRwonnHvXF7R+6CothFOhyAIcT6JtfFKRv2uN4EqpsQB8OLLH9w3Jy7vuNd1YXJLUC+Zq185GfHInzMgMnqGYw26/vEBc11mG0Xkk+WP0NOK6R8aw/cgjozqYARZcXzu0TXwZmk+mlODBGP+WVB9nrVe9hKGwOI0N81ExV
     procedure_id : 1894589466762224543
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.PrepareDessin
      PROCÉDURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
     type : 458752
   -
     name : Affichage
     procedure_id : 1894589466762290485
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.Affichage
      PROCÉDURE  Affichage()
      
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

Mock_SignalEnregistrement.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628664977755637944
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1628665119491885315
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalEnregistrement.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() : booléen
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628665123786919804
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalEnregistrement.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() : booléen
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     internal_properties : CAAAAAgAAACht87jPjIBcGu3/3c7mweNv9k8u4chic3Y6YL+W5Yn+QnVK37slwXepwGhSmo+XoWzPvofSS11BGofB1v6iboXmgoItkLKq7yNZldVHIz+WdNLV09uh/Vx4lWm4b9ygs5zVCWx8UkuEdcXwpCdwrfGw92IbFsB3uHrdoCHwBAq96Kap1KqltbXJSz7ofG7APi0K97Di2rmZqXqq3QGsifoVfgOza/5bDjEB2YHPDczyevX6iHDLZXbWKVekIo6gEZxHIFjun/v3LUaC5Jt6T3sOoCqWQ==
     procedure_id : 1628665132376921588
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalEnregistrement.Parametre
      PROCÉDURE  Parametre(nomFichier)
      
     type : 458752
   -
     name : EcritDansFichier
     internal_properties : CAAAAAgAAAAMeMKRJSYnv+N7MFbI09Aa2jX0KCjnw9ay7jTGw5AXGTVwrJj2FXJARTq6jgMPDZrPo9wPLzqParxpV9LxiQsWNNUPo/z7q+bZawRgiDXXXZvgYVJ7pLwa0SUtZ3JqZgec4FHLFNFJ7tWPQUxvUAQrIONZqIMJmMNXMXddtgrTpP8GXFxkfOxdic0cFStxpa1zG2HreaTSynMrCrxIcARxpJRroZM96hZEoIjva8rJpZifrPRo9cKqwfpNNRY4bw/e/XbPmhV1Bctl
     procedure_id : 1628665140966924294
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalEnregistrement.EcritDansFichier
      PROCÉDURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
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

Mock_SignalTraitement.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1628664763006108875
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1628664823136189886
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalTraitement.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() :booléen
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1628664831726126165
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalTraitement.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() :booléen
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1628664836021160606
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalTraitement.Parametre
      PROCÉDURE  Parametre(params)
      
      TypeVar(params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1628664844611162625
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalTraitement.Calcule
      PROCÉDURE  Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
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

Mock_TreuilClient.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1625816864455103956
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Demarre
     procedure_id : 1625816920289756919
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre():booléen
      
      :DemarreEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : Arrete
     procedure_id : 1625816924584791079
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete():booléen
      :ArreteEcouteSocket()
      RENVOYER Vrai
      
     type : 458752
   -
     name : ParseDonnees
     internal_properties : CAAAAAgAAACx+UZQaYf+/SsXqJ955bh4IDEXWh3QQPvpX1XeLx++4wZgVOfkk132HxI7CFk7O1pRsa7YpEkfk6iqD5tkGYxMy9Z+KxPy3p9CCkZr9A9rb1na7sFcxRzOg9KfaR/zltPzbwLkw+1SXZfCc51MZ6LP+dXJsS5SyQ4Bfu9Eh/WtQIZIGPVTSUVQ14wodqyMYVMSo9b7Z6pWOswXM+qwjJ0WNsM22bHTdDxdBm+n5hJuzqXZaxCzXUHFE+Vlw6WlzwtLmDvzy7Kz3w7GYWo=
     procedure_id : 1643878258528745873
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ITreuilClient.ParseDonnees
      PROCÉDURE  ParseDonnees(buf est un Buffer)
      
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

MutexHelper.wdc
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
      	privéé
      		nomMutex est chaine
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623564815485315736
     type_code : 27
     code : |1+
      procédure Constructeur(nomMutex est chaine)
      
      :nomMutex = nomMutex
      MutexDébut(:nomMutex)
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623564815485381272
     type_code : 28
     code : |1+
      procédure Destructeur()
      
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

ServeurConnexion.wdc
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
      procédure privée Constructeur()
      
      Logger.Verbose(ChaîneConstruit("construit ServeurConnectionTreuil, mutex=%1",:mutex))
      
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
      procédure Destructeur()
      
      Logger.Verbose(ChaîneConstruit("détruit ServeurConnectionTreuil, mutex=%1",:mutex))
      
      liberer :acquisition
      libérer :treuils
      libérer :app
      SignalDétruit(:nomThread)
     type : 655360
   -
     name : Demarre
     procedure_id : 1877387727582130481
     type_code : 12
     code : |1+
      procédure Demarre() : booléen
      
      Logger.Log("Demarre ServeurConnectionTreuil")
      si pas :treuils.Demarre() ALORS GOTO ERR
      si pas :acquisition.Demarre() ALORS GOTO ERR
      si pas :app.Demarre() ALORS GOTO ERR
      
      renvoyer vrai
      
      ERR:
      	ExceptionDéclenche(1,"Impossible de démarrer les connecteurs de client")
      	RENVOYER faux
     type : 458752
   -
     name : Arrete
     procedure_id : 1877387727582196017
     type_code : 12
     code : |1+
      procédure Arrete() : booléen
      
      Logger.Log("Arrete ServeurConnectionTreuil")
      SI PAS :treuils.Arrete() ALORS GOTO ERR
      SI PAS :acquisition.Arrete() ALORS GOTO ERR
      SI PAS :app.Arrete() ALORS GOTO ERR
      
      RENVOYER Vrai
      
      ERR:
      	ExceptionDéclenche(1,"Impossible d'arrêter les connecteurs de client")
      	RENVOYER Faux
     type : 458752
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAAAXf/Lxh0VZ1D254mtDXYGcvbEatlOXJbp09FCmp8Q5GClDqZYcY5pPhxIOetNT5qQIh87wSFIuuAIbMRJpOXOenMUvk+TTs56B45QYGL1XnWuI6cr7NMwaoaW15xrKHr9EoJFbHJUNWkkDNcCbzJC/rB8VVEdNXH9rBZvBwhYPmFuC2Bg4oJCpLRGwibf9ETu3Pf0adDRLKVDfcu2r4dU4d/FXS3FrINuycjeIzjd6cL1Kff2/pQDaDZ2yeZZxKX34h/Gu5NpXHNJ8
     procedure_id : 1877387727582261553
     type_code : 12
     code : |1+
      procédure AjouteClient(client IClientSocket dynamique):booléen
      
      si pas :clients[client.id]..Existe ALORS
      	m est un MutexHelper(:mutex)
      	:clients.Insère(client.id, client)
      	Logger.Log(ChaîneConstruit("ServeurConnectionTreuil Ajoute le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      	renvoyer vrai
      FIN
      LoggerErreur.EcritLigne(ChaîneConstruit("ServeurConnectionTreuil Le client[%1]=%2,%3 existe déjà",client.nomThread,client.ip,client.canalSocket))
      RENVOYER faux
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAACs8cybCqLcnhnjHHfbSyStlngaGbHGLUC44IdkNV2L1vraVW0kfE+z1yWFsz4O9tkiV3wFTicsTFrT+wGXOk189nJbV0qDj37+pRH/I9bAwg+iCZJW033McFrf5S0jGqsY4rUt9Mhn82108DF1qDnXRH6Q5UDIsLaxypwk3nxMcpXN/31trwg2XT63XtFBFoLFRA9Bu9YbnPb9AGpJ8WbMQ2bNGw8VVe5ToEMHOmdTaCj0b/VhMBIH0bfG4I4y8IMm7NUsLYUlJA==
     procedure_id : 1877387727582327089
     type_code : 12
     code : |1+
      procédure SupprimeClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      SI client<>null _et_ :clients[client.id]..Existe ALORS
      	Logger.Log(ChaîneConstruit("ServeurConnectionTreuil Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
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
      procédure privee DemarreEcouteSocket()
      
      :arretDemandé=Faux
      SignalModifie(:nomThread,signalFermé)
      
      ThreadExécute(:nomThread,threadNormal,()=>{
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
      procédure privée EcouteSocket()
      
      POUR TOUT c dans :clients
      	SI c<>null _et_ c.estarretDemandé() et PAS c.EstActif()	 ALORS
      		Logger.Verbose(ChaîneConstruit("ServeurConnectionTreuil libère le client[%1]=%2,%3",c.nomThread,c.ip,c.canalSocket))
      		:treuils.SupprimeClient(c)
      		:acquisition.SupprimeClient(c)
      		:app.SupprimeClient(c)
      		:SupprimeClient(c)
      		libérer c
      	FIN
      FIN
     type : 458752
   -
     name : ArreteEcouteSocket
     procedure_id : 1877387727582523697
     type_code : 12
     code : |1+
      procédure ArreteEcouteSocket()
      
      :arretDemandé=Vrai
      SignalModifie(:nomThread,signalOuvert)
      
      ArreteThreadSurTimeout(()=>ThreadEtat(:nomThread)<>threadInexistant, :nomThread)
      :EcouteSocket()
     type : 458752
   -
     name : Init
     procedure_id : 1877387727582589233
     type_code : 12
     code : |1+
      procédure Init()
      
      Logger.Verbose("Init ServeurConnectionTreuil")
      
      :DemarreEcouteSocket()
     type : 458752
   -
     name : Quit
     procedure_id : 1877387727582654769
     type_code : 12
     code : |1+
      procédure Quit()
      
      Logger.Verbose("Quit ServeurConnectionTreuil")
      
      :Arrete()
      :ArreteEcouteSocket()
     type : 458752
   -
     name : RecupereClients
     procedure_id : 1877387727582720305
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IConnexionClientSocket.RecupereClients
      PROCÉDURE  RecupereClients() : tableau de IClientSocket dynamique
      
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
      procédure DemarreAcquisition(params est chaine):booléen
      
      t est un tableau de IClientSocket dynamique <- :treuils.RecupereClients()
      
      tabChamp est un tableau de 0 chaînes
      tabFics est un tableau de 0 chaînes
      extrait_chaine_nomfics(ExtraitChaîne(params,1,RC))
      extrait_chaine_champs(ExtraitChaîne(params,2,RC))
      ind est entier = 1
      POUR TOUT cl IN t
      	tr est un TreuilClient dynamique <- cl
      	SI tr<>null _et_ tr.type=ITreuilClient.eRecepteur alors
      		tr.ParametreNomFichier(tabFics[ind])
      		tr.ParametreChampImage(tabChamp[ind])
      		tr.ParametreTraitement(ExtraitChaîne(params,3,RC))
      		tr.DemarreAcquisition()
      		ind++
      	FIN
      FIN
      
      renvoyer vrai
      
      procédure interne extrait_chaine_nomfics(paramsFics)
      	pour i=1 _a_ ChaîneOccurrence(paramsFics,tab)+1
      		fic est chaine = ExtraitChaîne(paramsFics,i)
      		tabFics.ajoute(fic)
      	FIN
      fin
      
      procédure interne extrait_chaine_champs(paramsImage)
      	champsgraphe est chaine = paramsImage
      	POUR i=1 _a_ ChaîneOccurrence(champsgraphe,tab)+1
      		champ est chaine = ExtraitChaîne(champsgraphe,i)
      		tabChamp.ajoute(champ)
      	FIN
      fin
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1877387727582851377
     type_code : 12
     code : |1+
      procédure ArreteAcquisition()
      
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
         procédure globale publique MonInstance() : ServeurConnexion
         
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

SignalDessine.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1624977340165392435
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Parametre
     procedure_id : 1627191271271955340
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.Parametre
      PROCÉDURE  Parametre(image)
      
      Logger.Debug("Parametre le champ image id=" + :id+", ")
      //:champImage.Hauteur = ExtraitChaîne(image,1,";")
      //:champImage.Largeur = ExtraitChaîne(image,2,";")
      :champGraphe = ExtraitChaîne(image,3,";")
      :nomGrapheVirtuel = "graphe_"+:id
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1627191902632294977
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() : booléen
      
      Logger.Debug("Démarre l'affichage pour l'acquisition id=" + :id+", ")
      arretTimerSysDessin = faux
      :DebutDessin()
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627191915517264901
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() : booléen
      
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
      // Redéfinition de la méthode ISignalDessine.DessineDansImage
      PROCÉDURE  DessineDansImage(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
      
      grSupprimeSérie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'élément "+el.Index)
      pour i=1 _a_ el.tabBuffer..Occurrence
      	grAjouteDonnée(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
      
      grDessine(:nomGrapheVirtuel)
      
      renvoyer vrai
     type : 458752
   -
     name : DebutDessin
     procedure_id : 1630644528460086025
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.DebutDessin
      PROCÉDURE  DebutDessin()
      
      grCrée(:nomGrapheVirtuel,grCourbe)
      si ChampExiste(:champGraphe) ALORS
      	grDestinationChamp(:nomGrapheVirtuel, :champGraphe)	
      FIN
      
     type : 458752
   -
     name : SauveImageJpeg
     procedure_id : 1631354121087298622
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.SauveImageJpeg
      PROCÉDURE  SauveImageJpeg(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
      
      fic est une chaîne = "tmp/"+SansEspace(id)+"_img"+el.Index+".jpg"
      renvoyer dSauveImageJPEG(:champGraphe,fic)
     type : 458752
   -
     name : PrepareDessin
     procedure_id : 1829904725260522718
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.PrepareDessin
      PROCÉDURE  PrepareDessin(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique)
      
      grSupprimeSérie(:nomGrapheVirtuel, 1)
      Logger.Verbose("Dessine dans id=" + :id+", l'élément "+el.Index)
      POUR i=1 _À_ el.tabBuffer..Occurrence
      	grAjouteDonnée(:nomGrapheVirtuel, 1, el.tabBuffer[i])
      FIN
     type : 458752
   -
     name : Affichage
     procedure_id : 1894581293419273211
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalDessine.Affichage
      procédure Affichage()
      
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
      	POUR i=1 _À_ :fileAffichage..Occurrence
      		Défile(:fileAffichage,elem)		
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

SignalEnregistrement.wdc
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
      	
      	nomFichier est une chaîne
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
      procédure Constructeur()
      
      nomthreadEnregistrement ="enregistrementDansFichier"+&fileBuffer 
      SignalCrée(nomthreadEnregistrement,signalManuel,signalFermé,partageAucun)
     type : 589824
   -
     name : Destructeur
     procedure_id : 1624977314395189021
     type_code : 28
     code : |1+
      procédure Destructeur()
      
      SignalDétruit(nomthreadEnregistrement)
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627215404694354351
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalEnregistrement.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() : booléen
      
      :handleFic = fOuvre(:nomFichier,foCréation+foLecture+foEcriture)
      SI :handleFic<0 ALORS
      	LoggerErreur.EcritLigne("Erreur de création du fichier d'acquisition id=" + :id+", " + :nomFichier)
      	RENVOYER Faux
      FIN
      
      Logger.Verbose("Demarre acquisition pour le fichier id=" + :id+", " + :nomFichier)
      
      SignalModifie(nomthreadEnregistrement,signalFermé)
      ThreadExécute(nomthreadEnregistrement,threadNormal,()=>{
      BOUCLE
      	si SignalAttend(nomthreadEnregistrement,10ms) ALORS
      		SupprimeTout(fileBuffer)
      		sortir
      	FIN
      	si pas fileBuffer..Vide ALORS
      		el est IAcquisitionSignal.Str_DATA_Acquisition
      		mbuf est un Buffer 
      		counter est entier = 0
      		tantque Défile(fileBuffer,el) _et_ counter < 10
      			temp est buffer
      			Sérialise(el,temp,psdXML)
      			mbuf+=[RC]+temp
      			counter++
      		fin		
      		SI PAS fEcritLigne(:handleFic, mbuf) ALORS
      			LoggerErreur.EcritLigne("Impossible d'écrire l'élément "+el.Index + " dans le fichier " + :nomFichier)
      			RENVOYER Faux
      		FIN
      		Logger.Verbose("Ecriture de l'élément "+el.Index + " dans le fichier " + :nomFichier)
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
      // Redéfinition de la méthode ISignalEnregistrement.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() : booléen
      
      Logger.Verbose("Arrête acquisition pour le fichier id=" + :id+", " + :nomFichier)
      
      //ThreadExécute(()=>{
      
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
      // Redéfinition de la méthode ISignalEnregistrement.Parametre
      PROCÉDURE  Parametre(nomFichier)
      
      :nomFichier = fabrique_nomfichier(nomFichier)
      :nomFichier = nomFichier
      Logger.Verbose("Paramètre le nom de fichier pour l'acquisition id=" + :id+", " + :nomFichier)
      
      procédure interne fabrique_nomfichier(lecteur_dossiers_nom_extension)
      	lecteur_dossiers est une chaîne = fExtraitChemin(lecteur_dossiers_nom_extension,fDisque+fRépertoire)
      	fichier est une chaîne= SansEspace(id) + "_" + fExtraitChemin(lecteur_dossiers_nom_extension,fFichier)
      	extension est une chaîne = fExtraitChemin(lecteur_dossiers_nom_extension,fExtension)
      	renvoyer lecteur_dossiers + "tmp/" + fichier + extension
      fin
     type : 458752
   -
     name : EcritDansFichier
     procedure_id : 1627989847711167977
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalEnregistrement.EcritDansFichier
      PROCÉDURE  EcritDansFichier(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booléen
      
      enfile(fileBuffer,el)
      renvoyer vrai
      
      mbuf est un Buffer 
      Sérialise(el,mbuf,psdXML)
      si pas fEcritLigne(:handleFic, mbuf) alors
      //hbuf est entier système = &el
      //si fEcrit(:handleFic,hbuf,IAcquisitionSignal.CST_TAILLE_STRUCT)<0 ALORS
      	LoggerErreur.EcritLigne("Impossible d'écrire l'élément "+el.Index + " dans le fichier " + :nomFichier)
      	renvoyer faux
      FIN
      
      Logger.Verbose("Ecriture de l'élément "+el.Index + " dans le fichier " + :nomFichier)
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

SignalTraitement.wdc
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
      procédure Constructeur()
      
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474904632115831
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : DemarreAcquisition
     procedure_id : 1627220739045436891
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalTraitement.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() :booléen
      
      Logger.Verbose("Demarre acquisition pour le traitement id=" + :id+", ")
      
      renvoyer vrai
     type : 458752
   -
     name : ArreteAcquisition
     procedure_id : 1627220747635439003
     type_code : 12
     code : |1-
      // Redéfinition de la méthode ISignalTraitement.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() :booléen
      
      Logger.Verbose("Arrete acquisition pour le traitement id=" + :id+", ")
      
      RENVOYER Vrai
     type : 458752
   -
     name : Parametre
     procedure_id : 1627977052899360823
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ISignalTraitement.Parametre
      PROCÉDURE  Parametre(params)
      
      Logger.Verbose("Parametre acquisition pour le traitement id=" + :id+", p=" + params)
     type : 458752
   -
     name : Calcule
     procedure_id : 1627989401034072149
     type_code : 12
     code : |1+
      procédure Calcule(el est IAcquisitionSignal.Str_DATA_Acquisition dynamique) : booleen
      
      dim est entier = el.tabBuffer..Occurrence
      pour i=1 _a_ dim
      	el.tabBuffer[i] += random(1,5)*100//PartieEntière(sinus(i * (20 / dim) * 2 * 180  ) * 1000) + random(1,5)*100
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

TreuilClient.wdc
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
      	mTreuilVersion				est une chaîne
      	mTreuilVersion_DRV_ACQ		est une chaîne
      	mTreuilVersion_DRV_Arduino	est une chaîne
      	mTreuilVersion_DRV_BP		est une chaîne
      	mTreuilVersion_DRV_Capture	est une chaîne
      	mTreuilVersion_DRV_COD		est une chaîne
      	mTreuilVersion_DRV_Genere	est une chaîne
      	mTreuilVersion_DRV_HeureABS	est une chaîne
      	mTreuilVersion_DRV_Output	est une chaîne
      	mTreuilVersion_DRV_PWM		est une chaîne
      	mTreuilIP					est une chaîne
      	mTreuilType					est une chaîne
      	mTreuilFreq					est un entier
      	mTreuilProfondeur			est un réel
      	mTreuilSens					est une chaîne
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
      procédure Constructeur()
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
      procédure Destructeur()
      Logger.Verbose("détruit TreuilER["+:mutex+"]")
      
      libérer :traitement
      libérer :enregistre
      libérer :dessine
      libérer :gestion
     type : 655360
   -
     name : Arrete
     procedure_id : 1877670637109906045
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE   Arrete():booléen
      
      Logger.Debug("Arrete TreuilER["+:mutex+"]")
      :ArreteEcouteSocket()
      RENVOYER Vrai
     type : 458752
   -
     name : Demarre
     procedure_id : 1877670637109971581
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE   Demarre():booléen
      
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
      // Redéfinition de la méthode ITreuilRecepteur.ParametreNomFichier
      PROCÉDURE  ParametreNomFichier(nomFichier est chaîne)
      
      Logger.Log("Nom fichier acquisition Treuil["+ :mutex + "]"+"="+nomFichier)
      :enregistre.Parametre(nomFichier)
     type : 458752
   -
     name : ParametreChampImage
     procedure_id : 1877670637110102653
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ITreuilRecepteur.ParametreChampImage
      PROCÉDURE  ParametreChampImage(paramsImage)
      
      Logger.Log("Démarre l'acquisition pour le Treuil["+ :mutex + "]")
      :dessine.Parametre(paramsImage)
     type : 458752
   -
     name : ParametreTraitement
     procedure_id : 1877670637110168189
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ITreuilRecepteur.ParametreTraitement
      PROCÉDURE  ParametreTraitement(params)
      
      Logger.Log("Paramètre le traitement de l'acquisition pour le Treuil["+ :mutex + "]")
      :traitement.Parametre(params)
     type : 458752
   -
     name : DemarreAcquisition
     procedure_id : 1877670637110233725
     type_code : 12
     code : |1+
      // Redéfinition de la méthode ITreuilRecepteur.DemarreAcquisition
      PROCÉDURE  DemarreAcquisition() : booléen
      
      Logger.Log("Démarre l'acquisition pour le Treuil["+ :mutex + "]")
      
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
      // Redéfinition de la méthode ITreuilRecepteur.ArreteAcquisition
      PROCÉDURE  ArreteAcquisition() : booléen
      
      Logger.Log("Arrête l'acquisition pour le treuil["+ :mutex + "]")
      
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
      // Redéfinition de la méthode ITreuilClient.ParseDonnees
      PROCÉDURE ParseDonnees(buf est un Buffer)
      
      _TreuilsSockParse_SC(buf)
     type : 458752
   -
     name : threadTraitement
     procedure_id : 1877670637110430333
     type_code : 12
     code : |1+
      procédure privée threadTraitement(id,nomThread est chaine, procArretDemandé est une procédure)
      
      Logger.Debug(ChaîneConstruit("Lance le thread de traitement id=%1",id))
      
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
      	sVariable_Message	est une chaîne
      	sContenu_Message	est une chaîne
      	sVariable_AR		est une chaîne
      	sContenu_AR			est une chaîne
      	
      	POUR i = 1 A nbMsg
      		
      		sVariable_Message = ExtraitChaîne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) A (i*::Size_MSG_Socket_RX)]],1,::CST_SOCKMSG_SEP)
      		sContenu_Message = ExtraitChaîne(_buffer[[((1+(i-1)*::Size_MSG_Socket_RX)) A (i*::Size_MSG_Socket_RX)]],2,::CST_SOCKMSG_SEP)	
      		
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
      				sVariable_AR = ExtraitChaîne(sContenu_Message[[1 A (::Size_MSG_Socket_RX-5)]],1,"=")
      				sContenu_AR = ExtraitChaîne(sContenu_Message[[1 A (::Size_MSG_Socket_RX-5)]],2,"=")
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
      					Logger.Log("Client["+:canalSocket+","+:ip+"]+_COMM_:_"+"Erreur Réception Contenu_Message")
      				FIN
      				
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Variable :"+sVariable_Message)
      				LoggerErreur.EcritLigne("Contenu :"+sContenu_Message)
      				LoggerErreur.EcritLigne(ChaîneConstruit("[%1%][%2]_:_Erreur Réception Contenu_Message",:ip,:id))
      		FIN	
      	FIN
      	
      FAIRE
      	ExceptionActive()
      	LoggerErreur.EcritLigne("exception dans _TreuilsSockParse")
      FIN
      
      	procédure interne M(var)
      		renvoyer ChaîneConstruit("{%1}{%2}{%3}",var,:canalSocket,:ip)
      	FIN
      
      	procédure interne gestion_deconnexion()
      		SELON sContenu_Message					
      			CAS "exit.From.PC"
      				Logger.Message(M(sContenu_Message)+"Sortie de l’application suite à la demande provenant du PC Serveur")	
      			CAS "exit.From.PEGASE"
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite à la demande provenant de l’application Cliente µC")	
      			CAS "exit.From.REBOOT"
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite à la demande provenant d'un Reboot du µC")	
      			AUTRE CAS
      				Logger.Message(M(sContenu_Message)+"Sortie de l'application suite à la demande provenant de source inconnue")	
      		FIN
      	fin
      
      	procédure interne gestion_erreur()
      		
      		sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")	
      		SELON sVariable_AR
      			CAS "ARRT"
      				SI sContenu_AR = "PILE---WIFI" ALORS						
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Erreur de transmission WIFI")
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Erreur de transmission du buffer d’acquisition par la socket, le treuil arrête l’acquisition pour éviter un plantage")
      				FIN
      			CAS "IDENT"
      				SI sContenu_AR = "NoCOMP" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne voit aucun composant compatible")
      				FIN	
      				SI sContenu_AR = "COMPNonAdresse" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"]+Communication 1-Wire ne reconnaît pas le composant")
      				FIN	
      			CAS "MESURE"
      				SI sContenu_AR = "SENS--COD" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Le moteur tourne dans le mauvais sens")
      				FIN	
      			CAS "VITESSE"
      				SI sContenu_AR = "NONSPECIF" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Pas de vitesse configurée")
      				FIN	
      			CAS "MOTEUR"
      				SI sContenu_AR = "BLOQ--MOT" ALORS							
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Blocage moteur, aucune rotation détectée")
      				FIN
      			CAS "GOTO"
      				SI sContenu_AR = "NON-ATTEINT" ALORS
      					LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] Arrêt moteur alors que la destination du GOTO n’a pas été atteinte")
      				FIN
      			AUTRE CAS
      				LoggerErreur.EcritLigne("Client["+:canalSocket+","+:ip+"] var:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN
      	fin
      
      	procédure interne gestion_type_treuil()
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
      
      	procédure interne gestion_ravs()
      		sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")
      		//				SELON sVariable_AR								
      		//					CAS "1"
      		//						si Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilIP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      		//							Param_Interne_Treuils.mTreuilType=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      		//							
      		//							Param_Interne_Treuils.mTreuilVersion=ExtraitChaîne(sContenu_AR,1,"-")
      		//							GG_AVS.iVersionT1 = val(Param_Interne_Treuils.mTreuilVersion)*100
      		//							Param_Interne_Treuils.mTreuilFreq=Val(ExtraitChaîne(sContenu_AR,2,"-"))
      		//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 1 ALORS
      		//								Param_Interne_Treuils.Energie=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      		//							SINON
      		//								Param_Interne_Treuils.Gain=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      		//							FIN							
      		//							si Val(ExtraitChaîne(sContenu_AR,4,"-")) = 0 alors 
      		//								Param_Interne_Treuils.mTreuilSens="Montée"
      		//							sinon
      		//								Param_Interne_Treuils.mTreuilSens="Descente"
      		//							FIN
      		//						sinon
      		//							Param_Interne_Treuils.T2_IP=Gtab_Liste_Client[nIndiceListeClient].IP_Client
      		//							Param_Interne_Treuils.T2_Type=Gtab_Liste_Client[nIndiceListeClient].Type_Client
      		//							
      		//							Param_Interne_Treuils.T2_Version=ExtraitChaîne(sContenu_AR,1,"-")
      		//							GG_AVS.iVersionT2 = Val(Param_Interne_Treuils.T2_Version)*100
      		//							Param_Interne_Treuils.T2_Freq=Val(ExtraitChaîne(sContenu_AR,2,"-"))
      		//							SI GG_treuilss[CST_SOCK_TREUIL_EMETEUR].NumeroTreuil = 2 ALORS
      		//								Param_Interne_Treuils.Energie=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      		//							SINON
      		//								Param_Interne_Treuils.Gain=Val(ExtraitChaîne(sContenu_AR,3,"-"))
      		//							FIN
      		//							SI Val(ExtraitChaîne(sContenu_AR,4,"-")) = 0 ALORS 
      		//								Param_Interne_Treuils.T2_Sens="Montée"
      		//							SINON
      		//								Param_Interne_Treuils.T2_Sens="Descente"
      		//							FIN
      		//						FIN
      		//						
      		//					CAS "2"
      		//						Gn_REP_QAVS++			
      		//						SI Gtab_Liste_Client[nIndiceListeClient].NumeroTreuil = 1 ALORS
      		//							Param_Interne_Treuils.mTreuilProfondeur=Val(ExtraitChaîne(sContenu_AR,1,"*"))/2
      		//							Param_Interne_Treuils.mTreuilVitesseUp=Val(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),2))
      		//							Param_Interne_Treuils.mTreuilVitesseDown=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),4),2))
      		//							Param_Interne_Treuils.mTreuilMesure=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),6),2))	
      		//						SINON
      		//							Param_Interne_Treuils.T2_Profondeur=Val(ExtraitChaîne(sContenu_AR,1,"*"))/2
      		//							Param_Interne_Treuils.T2_VitesseUp=Val(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),2))
      		//							Param_Interne_Treuils.T2_VitesseDown=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),4),2))
      		//							Param_Interne_Treuils.T2_Mesure=Val(Droite(Gauche(ExtraitChaîne(sContenu_AR,2,"*"),6),2))				
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
      
      	procédure interne gestion_rpar()
      		sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")
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
      				t.Index		= Val(ExtraitChaîne(sContenu_AR,1,"-"))
      				t.POS_EMET	= Val(ExtraitChaîne(sContenu_AR,2,"-"))
      				:Param_Interne_Treuils.mTreuilProfondeur = t.POS_EMET
      				Enfile(fileBufferEmet,t)
      				
      			AUTRE CAS
      				//Trace(Gtab_Liste_Client[nIndiceListeClient].Nom_Client+"_AR:_ "+sVariable_AR+"_:_"+sContenu_AR)
      		FIN	
      	fin
      
      	procédure interne gestion_event_stop()
      		StopFROM est entier = Val(Droite(sContenu_AR,2))
      		SELON StopFROM							
      			CAS ::CST_STOPFROM_ARRIVEAFTERGOTO
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : ARRIVED_AFTER_GOTO")
      			CAS	::CST_STOPFROM_FINCALIB
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : FIN_CALIBRATION")
      			CAS	::CST_STOPFROM_PWMNULLE
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : PWM_NULLE")
      			CAS ::CST_STOPFROM_CONFIGSENS
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : CONFIG_SENS")
      			CAS ::CST_STOPFROM_ERRSENS
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Erreur SENS")
      			CAS ::CST_STOPFROM_BP
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Bouton Poussoir")
      			CAS ::CST_STOPFROM_CLOSEPWM
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Fin PWM")
      			CAS ::CST_STOPFROM_AU
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Arret d'Urgence")
      			CAS ::CST_STOPFROM_ERRCALIB
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Erreur CALIBRATION")	
      			CAS ::CST_STOPFROM_ERRGOTO
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Erreur GOTO")
      			CAS ::CST_STOPFROM_CMDSOCKET
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Depuis PC")
      			CAS ::CST_STOPFROM_BLOCAGEMOTEUR
      				Logger.Message(M("EVT_STOP_"+StopFROM)+"Source de l'arrêt moteur : Blocage Moteur")		
      			AUTRE CAS
      				Logger.Message(M("EVT_STOP_")+"Source de l'arrêt moteur inconnu")
      		FIN
      	fin
      
      	procédure interne gestion_msg()
      		sVariable_AR	= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],1,"=")
      		sContenu_AR		= ExtraitChaîne(sContenu_Message[[1 À (::Size_MSG_Socket_RX-5)]],2,"=")
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
      
      	procédure interne gestion_event_btnp()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de montée")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS							
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Appui sur le bouton d’arrêt d’urgence")
      		FIN
      	fin
      
      	procédure interne gestion_event_btnr()
      		SI sContenu_AR = "FROM-----UP" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Relâche du bouton de montée")
      		FIN	
      		SI sContenu_AR = "FROM---DOWN" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Relâche du bouton de descente")
      		FIN	
      		SI sContenu_AR = "FROM-----AU" ALORS
      			Logger.Message(M("EVT_BTNP_"+sContenu_AR)+"Relâche du bouton d’arrêt d’urgence")
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

TreuilConnexion.wdc
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
      privé
      	clients est un tableau associatif de ITreuilClient dynamique
      fin
     type : 131072
  procedures :
   -
     name : Constructeur
     procedure_id : 1623474479427501984
     type_code : 27
     code : |1+
      procédure Constructeur()
      
      Logger.Verbose("construit TreuilConnexion["+:mutex+"]")
     type : 589824
   -
     name : Destructeur
     procedure_id : 1623474479427567520
     type_code : 28
     code : |1+
      procédure Destructeur()
      
      Logger.Verbose("détruit TreuilConnexion["+:mutex+"]")
     type : 655360
   -
     name : AjouteClient
     internal_properties : CAAAAAgAAABYA/3DxBAKpoXP9q0xkayH6OpSAYdI58ywoAG2N89/pNxQ9bVi1jmHX/X/YcT0JuNMNVx19Jk2NJobtTfVIAV+9IyvS0xZ9cIaqfMJXaj4jCHA7cb0FV9gTBbRsyMJNjkOuEE5D4VqwjIZI/56f8q4zU9HTutlU9nqs+OVz2ONy6raovroPE/NehuCi5a4I4eGhd4C+lfeF7OKk9e+KDMhHL80RO5uwCm8Kdrwk0ZwDY0R4gJcVXWm1ow5bUMpgWTXZfxFeDPRUA==
     procedure_id : 1623562577806767593
     type_code : 12
     code : |1+
      procédure AjouteClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      :clients.Insère(client.id, client)
      Logger.Verbose(ChaîneConstruit("TreuilConnexion["+:mutex+"] Insère le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
      renvoyer vrai
     type : 458752
   -
     name : SupprimeClient
     internal_properties : CAAAAAgAAABYA/3DxBAKpoXP9q0xkayH6OpSAYdI58ywoAG2N89/pNxQ9bVi1jmHX/X/YcT0JuNMNVx19Jk2NJobtTfVIAV+9IyvS0xZ9cIaqfMJXaj4jCHA7cb0FV9gTBbRsyMJNjkOuEE5D4VqwjIZI/56f8q4zU9HTutlU9nqs+OVz2ONy6raovroPE/NehuCi5a4I4eGhd4C+lfeF7OKk9e+KDMhHL80RO5uwCm8Kdrwk0ZwDY0R4gJcVXWm1ow5bUMpgWTXZfxFeDPRUA==
     procedure_id : 1623562612166579638
     type_code : 12
     code : |1+
      procédure SupprimeClient(client IClientSocket dynamique):booléen
      
      m est un MutexHelper(:mutex)
      SI :clients[client.id]..Existe ALORS
      	Logger.Verbose(ChaîneConstruit("TreuilConnexion["+:mutex+"] Supprime le client[%1]=%2,%3",client.nomThread,client.ip,client.canalSocket))
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
      // Redéfinition de la méthode IClientSocket.Demarre
      PROCÉDURE Demarre():booléen
      
      Logger.Debug(ChaîneConstruit("TreuilConnexion["+:mutex+"] démarre la socket %1, %2, %3",:NomSocket,:portSocket,:AdresseSocketServer))
      
      si SocketCrée(:NomSocket,:portSocket,:AdresseSocketServer) ALORS
      	SocketChangeModeTransmission(:NomSocket, SocketSansMarqueurFin)
      	Logger.Debug("Création de la socket "+:NomSocket)
      	:DemarreEcouteSocket()
      	RENVOYER Vrai
      fin
      
      LoggerErreur.EcritLigne("Impossible de créer la socket "+ErreurInfo(errMessage))
      RENVOYER Faux
     type : 458752
   -
     name : Arrete
     procedure_id : 1643608238200826745
     type_code : 12
     code : |1+
      // Redéfinition de la méthode IClientSocket.Arrete
      PROCÉDURE VIRTUELLE Arrete():booléen
      
      Logger.Debug(ChaîneConstruit("Arrete TreuilConnexion["+:mutex+"] la socket %1",:NomSocket))
      
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
      // Redéfinition de la méthode IClientSocket.EcouteSocket
      PROCÉDURE VIRTUELLE EcouteSocket()
      
      si pas :arretDemandé alors
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
      procédure privée _VerifieDemande() : booléen
      
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
      // Redéfinition de la méthode IConnexionClientSocket.RecupereClients
      PROCÉDURE  RecupereClients() : tableau de IClientSocket dynamique
      
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
         procédure protégée server() : IConnexionClientSocket
         
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

WD_DualAxisChart.wdc
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
      	
      	// callback (événement)
      	EventCurseur is Procedure(STC_CurseurEventArgs)
      fin
      
      ModeCurseur est une énumération
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
      procédure Constructeur()
      _modeCurseur = ModeCurseur.Aucun
     type : 589824
   -
     name : Destructeur
     procedure_id : 1853203131811381252
     type_code : 28
     code : |1+
      procédure Destructeur()
      
     type : 655360
   -
     name : Init
     procedure_id : 1853203264955464136
     type_code : 12
     code : |1+
      procédure Init(champ)
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
      // Résumé : <indiquez ici ce que fait la procédure>
      // Syntaxe :
      // ConfigurerGraphique ()
      //
      // Paramètres :
      //	Aucun
      // Valeur de retour :
      // 	Aucune
      //
      // Exemple :
      // <Indiquez ici un exemple d'utilisation>
      //
      procédure ConfigurerGraphique(titre, nomY, uniteY, minY, maxY, inverseY)
      
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
      procédure ConfigurerSerie1(nom, minX, maxX, couleur est chaine)//r est un int, g est un int, b est un int)
      
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
      procédure ConfigurerSerie2(nom, minX, maxX, couleur est chaine)
      
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
      procédure AjouterPointSerie1(positionY, valeurX)
      
      serie1:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
     type : 458752
   -
     name : AjouterPointSerie2
     procedure_id : 1853203776057018899
     type_code : 12
     code : |1+
      procédure AjouterPointSerie2(positionY, valeurX)
      
      serie2:Points:AddXY(valeurX, positionY)
      Scrolling(positionY)
     type : 458752
   -
     name : EffacerDonnees
     procedure_id : 1853203831891672143
     type_code : 12
     code : |1-
      procédure EffacerDonnees()
      
      serie1:Points:Clear()
      serie2:Points:Clear()
     type : 458752
   -
     name : ExporterImage
     procedure_id : 1853203874841420732
     type_code : 12
     code : |1+
      procédure ExporterImage(chemin)
      
      chart:SaveImage(chemin, 'System.Windows.Forms.DataVisualization.Charting.ChartImageFormat'::PNG)
      
     type : 458752
   -
     name : Scrolling
     procedure_id : 1853750954891473848
     type_code : 12
     code : |1+
      procédure Scrolling(y)
      
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
      procédure MakeARGB(aa est un int, r est un int, g est un int, b est un int)
      renvoyer bitDécaleGauche(aa, 24) + bitDécaleGauche(r, 16) + bitDécaleGauche(g,8) + b
      
     type : 458752
   -
     name : ActiverCurseur
     procedure_id : 1854077273698485285
     type_code : 12
     code : |1+
      procédure ActiverCurseur(actif est boolean)
      
      curseurActif = actif
      chartarea:CursorY:IsUserEnabled	= actif
     type : 458752
   -
     name : SetModeCurseur
     procedure_id : 1854077492741868568
     type_code : 12
     code : |1+
      procédure SetModeCurseur(mode est ModeCurseur)
      
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
      procédure OnMouseMove(yPixel est entier)
      
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
      procédure MoveCurseur(delta is real)
      
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
      // Résumé : <indiquez ici ce que fait la procédure>
      // Syntaxe :
      // MajCurseurLigne ()
      //
      // Paramètres :
      //	Aucun
      // Valeur de retour :
      // 	Aucune
      //
      // Exemple :
      // <Indiquez ici un exemple d'utilisation>
      //
      procédure MajCurseurLigne(y is real)
      
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
      procédure MajCurseurZone(y1 is real, y2 is real)
      
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
      procédure Interpoler(s is object dynamic, y is real)
      
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
      procédure GetStat(s is 'System.Windows.Forms.DataVisualization.Charting'.Series dynamic, yMin is real, yMax is real, isMin is boolean)
      
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
      procédure GetMoyenne(s is object dynamic, yMin is real, yMax is real)
      
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

