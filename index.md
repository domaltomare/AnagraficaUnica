
API-Puglia
===========
Documentazione dei Servizi e delle API della Regione Puglia
-----------------------------------------------------------

### Documento di Componenti Funzionali delle API esposte da Anagrafica Unica

**Sommario**

[**1. Premessa**](#1-premessa)

[**2. Registrazione API Gateway**](#2-registrazione-api-gateway)

[**3. API AU Stakeholder**](#3-api-au-stakeholder)

&nbsp;&nbsp;&nbsp;&nbsp;[3.1 - Servizio GET /anagrafica/{tipo_soggetto}/{chiave}](#31---servizio-get-anagraficatipo_soggettochiave)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[3.1.1 - Esempio di Request](#311---esempio-di-request)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[3.1.2 - Esempio di Response](#312---esempio-di-response)

# 1. Premessa

L’obiettivo di questo documento è quello di descrivere i servizi messi a disposizione dal sistema informativo Anagrafica Unica (nel proseguo del documento riferiremo con l’acronimo **AU**), realizzato per rispondere a due degli obiettivi previsti dal Piano Triennale di Riorganizzazione Digitale 2022-2024 della Regiona Puglia [**D.G.R. n.791**], al quale è seguito l’aggiornamento relativo al trienno 2024-2026 approvato con **D.G.R. n. 1646 del 28 Novembre 2024**.

Nello specifico, le due azioni sono identificate dalle seguenti denominazioni:
- **_OR_17 Anagrafica Unica Personale Regionale_**
- **_OR_18 Anagrafica Unica Stakeholder Esterni_**
  
Mentre l’**OR_17** si pone l’obiettivo di crere un unico punto di gestione dell’anagrafica dei dipendenti regionali e dell’organigramma regionale, l’**OR_18** si pone l’obiettivo di creare un’unica anagrafica di tutti i soggetti esterni – con qualsiasi forma privata / giuridica e settore di attività economica - che a vario titolo interagiscono con le piattaforme regionali.

Si è deciso di progettare due API distinte per esporre rispettivamente le risorse afferenti l’anagrafica unica del personale regionale [API_AU_DIPENDENTI] e le risorse afferenti l’anagrafica unica degli stakeholder esterni [API_AU_STAKEHOLDER].

Questa versione del documento sarà focalizzata sull’esposizione dell’**API_AU_STAKEHOLDER** ed in particolare su una risorsa che esporrà le anagrafiche  dei liberi professionisti o delle imprese a seconda delle specifiche descritte nei prossimi paragrafi.
 

# 2. Registrazione API Gateway

Le API REST per utilizzare le funzionalità di AU sono esposte sul Gateway regionale API-PUGLIA.

Per utilizzare le API bisogna registrarsi al portale <https://api-portal.regione.puglia.it/devportal/apis> e richiedere la sottoscrizione alle API.

La guida per la registrazione al portale, la sottoscrizione alle api e la generazione dei token di autorizzazione è presente al seguente link:
<https://oros-git.regione.puglia.it/api-puglia/docs/-/tree/master/DevPortal>

Tutte le chiamate alle API di seguito descritte richiedono la generazione del token di accesso OAuth da utilizzare come header di autorizzazione nella forma:
"Authorization: Bearer token_api_puglia_generato"


# 3. API AU Stakeholder

La seguente API descrive le specifiche dei servizi esposti da AU per la componente degli stakeholder esterni.

La specifica openapi è consultabile al file YAML [specifiche-servizi-au-stakeholder.yaml](/stakeholder/specifiche-interfacce/specifiche-servizi-au-stakeholder.yaml).


### 3.1 - Servizio *GET /anagrafica/{tipo_soggetto}/{chiave}*

Il servizio permette di ricercare i dati anagrafici degli stakeholder esterni raggruppati per tipologia di soggetto.

La ricerca sarà effettuata attraverso i seguenti filtri di ricerca:

- **{tipo_soggetto}**: rappresenta la categoria da ricercare. Può assumere i seguenti valori:
	- LIBERO_PROFESSIONISTA: l'anagrafica del libero professionista è recuperata dalla banca dati di Anagrafica Unica
 	- IMPRESA: l'anagrafica dell'impresa è recuperata dal servizio Registro Imprese esposto da Infocamere.
    	Per maggiori dettagli riguardanti il servizio rimandiamo alla documentazione [Servizi Registro Imprese](https://oros-git.regione.puglia.it/api-puglia/docs/-/blob/master/RegistroImprese/index.md?ref_type=heads#41-servizio-di-visura)
    
- **{chiave}**: è il Codice Fiscale del tipo_soggetto idicato. 
  

#### 3.1.1 - Esempio di Request

**GET** `https://api-gw.regione.puglia.it/api/au/stakeholder/2.0/anagrafica/IMPRESA/{codice-fiscale}`

**Header**: `*Authorization: Bearer token_api_puglia_generato*`


#### 3.1.2 - Esempio di Response

La response &egrave; un json strutturato come di seguito:

| **Param Name**          | **Tipo**   | **Descrizione**                                               |
|:------------------------|:-----------|:--------------------------------------------------------------|
| data                    | Object     | Contenuto response con i dati trovati                         |
| status                  | String     | Stato http della richiesta                                    |
| message                 | String     | Stringa contenente eventuali messaggi se presenti             |
| fonte                   | String     | Fonte da cui sono stati recuperati i dati                     |
| fonteCertificata        | String     | Stringa SI-NO indicante se la fonte &egrave; certifica o meno |
| dataUltimoAggiornamento | Data e ora | Data e ora dell'ultimo aggiornamento del dato letto           |

La struttura dell'attributo "data" varia in base al "tipo_soggetto" utilizzato, consultabile nella specifica yaml allegata nel paragrafo [3](#3-api-au-stakeholder).

Esempio di risposta per tipo_soggetto: "LIBERO_PROFESSIONISTA":

```json
{
  "data": {
    "codiceFiscale": "{codice-fiscale}",
    "nome": "NOME",
    "cognome": "COGNOME",
    "partitaIva": "0000000000",
    "telefono": "3333333333",
    "mail": "mail@mail.it",
    "pec": "pec@pec.it"
  },
  "status": "200",
  "fonte": "Puglia Semplice",
  "fonteCertificata": "NO",
  "dataUltimoAggiornamento": "2025-01-10T14:09:02.792228"
}
```
