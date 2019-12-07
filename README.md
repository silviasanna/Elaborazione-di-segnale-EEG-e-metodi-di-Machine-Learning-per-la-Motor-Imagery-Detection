# Elaborazione-di-segnale-EEG-e-metodi-di-Machine-Learning-per-la-Motor-Imagery-Detection

Il XXI secolo sarà il secolo del cervello, queste erano le parole alcuni anni fa di Steven Rose, un famoso biologo inglese esperto di neurobiologia. Nel 2013 infatti l’Unione Europea stanziò dei fondi per lo “Human Brain Project”, un progetto di ricerca scientifica e industriale in grado di far avanzare la scienza nei campi della neuroscienza, computazione informatica e medicina cerebrale. Pochi mesi dopo il presidente statunitense Barack Obama lanciò il progetto “Brain Initiative” il cui scopo è trovare delle tecnologie emergenti per capire il funzionamento cognitivo del cervello.
Sotto questo scenario inizia a prendere forma la Brain Computer Interface (BCI), un ramo delle neuroscienze e dell’informatica il cui compito è creare un’interfaccia comunicativa tra il cervello e una macchina. Negli anni sono diventati infatti numerosissimi i progetti di BCI, tra cui l’ultimo più noto “Neuralink” presentato nel luglio 2019 da Elon Musk.
In tale contesto, questo lavoro ha l’obiettivo di analizzare alcune misure applicate ad un segnale EEG acquisito mediante un dispositivo a 64 canali, allo scopo di determinarne il potere discriminante nei confronti di particolari azioni oggetto di studio. Abbiamo cercato di rispondere alle seguenti domande:
•	Il segnale contiene informazioni sufficienti a permetterci di riconoscere un’azione intrapresa (o pensata) rispetto a un’altra?
•	Quanto influisce la misura e lo spazio di rappresentazione utilizzato?
•	Tutte le azioni sono individuabili nello stesso modo?
•	Il risultato è dipendente fortemente dal soggetto in esame o è relativamente indipendente?
•	È presente un’evidente separazione o raggruppamento fra i segnali nelle diverse azioni secondo lo spazio delle features?


DATASET UTILIZZATO:
https://archive.physionet.org/pn4/eegmmidb/

ELABORAZIONE SEGNALE:
Ogni segnale è stato filtrato nelle frequenze 7-30 Hz (banda alfa e beta). Ogni segnale è stato suddiviso in epoche della durata di 1 secondo

MISURE APPLICATE:
 - CSP (Common Spatial Pattern): https://mne.tools/dev/generated/mne.decoding.CSP.html scegliendo 4 spazi delle features
 - FOOOF (Fitting Oscillations and One Over F): https://fooof-tools.github.io/fooof/ considerando i parametri di intercetta e pendenza della retta approssimante la componente aperiodica per ogni canale.
 Sulla FOOOF è stata effettuata una selezione dei canali: studio dei 64 canali e studio dei 29 canali corrispondenti alla zona della corteccia motoria.
 
 ESPERIMENTI DI MACHINE LEARNING:
 Sono state discriminate le due azioni contenute in ogni registrazione.
 Su ogni segnale è stata effettuata una classificazione (algoritmo LDA e KNN (n_neighbors=3)). Per classificare è necessario che l'algoritmo abbia imparato da campioni simili. E' necessario suddividere in maniera opportuna il dataset in train e tes.
 Suddivisione dataset per classificazione:
  - Tecnica Hold Out: campionamento random dalle 3 registrazioni omogenee (macro-task). esperimento HO20: test_set = 20%. esperimento HO80: test_set = 80%. Su entrambi ripetizione 10 volte.
  - Tecnica Sessione di Acquisizione: la parte di train è basata sulle registrazioni all'interno dello stesso macro-task. esperimento SA1: 2 registrazioni per ogni macro-task per il train e 1 per il test. esperimento SA2: 1 registrazione per il train e 2 dello stesso macro-task per il test. Esperimento ripetuto con le 3 combinazioni.
  - Tecnica Leave-One-Out: la parte di test è un pattern alla volta per ogni registrazione. L'esperimento è stato ripetuto e mediato selezionando ogni pattern di ogni registrazione per il test-set.
  
Su ogni segnale è stata successivamente effettuata una clusterizzazione (KMeans con 2 e 4 clusters). 
 - Clusterizzazione pura con le sole misure FOOOF a 64 e 29 canali. 
 - Clusterizzazione semisupervisionata per la misura CSP che ha bisogno di consocere i dati e le rispettive etichette per ridimensionarli. E' stata scelta la tecnica HO20 e HO80 per la suddivisione dei dati in train e test. Sul train è stata adattata la misura CSP mentre sul test è stato effettuato il clustering.
 
 
 RISULTATI:
 Per ogni esperimento non si è notata differenza nell’applicare l’algoritmo di classificazione KNN o LDA, come non si notano miglioramenti effettivi nella selezione di 2 o 4 cluster per il K-Means. Per i cluster è stato scelto un numero multiplo rispetto al numero di classi. Questo è possibile affermarlo dai valori medi, ma non si è indagato per ogni soggetto come cambino i valori.
 Al contrario è caratterizzante la misura scelta, infatti la CSP risulta essere più adatta su questo dataset e con questa divisione dei dati, al contrario della FOOOF che ha portato accuratezze molto basse. In questa tesi non si è però indagato su quali siano le 4 dimensioni della CSP.
 Non è presente una distinzione tra le 3 registrazioni di ogni macro-task poiché confrontando gli esperimenti sulle 3 tecniche di suddivisione dei dati non è presente disomogeneità a livello globale. Non è stato indagato se sia presente una differenza tra le tre tecniche per ogni soggetto.
 Applicando lo studio di Combrisson e Jerbi "Exceeding chance level by chance: The caveat of theoretical chance levels in brain signal classification and statistical assessment of decoding accuracy" sui risultati ottenuti, è possibile affermare che con i valori di accuratezza a>=0.8 tali risultati non sono dovuti per caso ma per effettivo funzionamento dell'algoritmo.
 In relazione alla tecnica di hold out, è possibile affermare come il sistema sia molto robusto pur aumentando le dimensioni del train set dal 20% all’80%, per cui si hanno sempre un numero notevole di soggetti con accuratezza elevata non dovuta al caso. Infatti, diminuendo la dimensione di train i dati medi rimangono invariati ma il gruppo di soggetti “migliori” cambia.
 Il sistema sviluppato risulta task specific e user specific. Quest'ultimo è un risultato atteso poiché in generale nelle BCI non si può trovare un algoritmo adatto per ogni soggetto. L'ideale sarebbe trovare un algoritmo che vada bene sulla maggior parte o tarare i parametri per ogni soggetto.
 Analizzando nel complesso tutte le prove si è inoltre notato che per il macro-task caratterizzato dai run 5,9,13 si ha un’accuratezza media più elevata rispetto agli altri tre macro-task. Attualmente non possiamo dare una spiegazione a ciò.

