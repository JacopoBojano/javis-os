# ADR 0001: Confini dei dati e autonomia di Jarvis personale

- **Stato:** accettato
- **Data:** 2026-09-29
- **Ambito:** versione personale di Javis OS

## Contesto

Javis OS offre gia chat, brain Markdown, provider intercambiabili, connettori MCP e lavori in
background. Il prodotto personale deve riusare queste capacita senza inviare dati a servizi non
scelti e senza confondere le modifiche recuperabili al brain con le azioni che producono effetti
verso altre persone o organizzazioni.

## Decisioni

1. **Perimetro esplicito.** Un provider AI o un connettore puo ricevere dati solo dopo essere stato
   scelto e configurato dall'utente. Un servizio collegato non autorizza automaticamente altri
   provider. La configurazione deve rendere visibili provider, modello, connettori e ambito dei dati
   accessibili.
2. **Uso interattivo.** Il percorso iniziale usa Codex autenticato tramite il login ChatGPT
   dell'utente. Il contesto inviato e limitato a quello necessario alla richiesta.
3. **Lavori periodici.** Groq Free e un'opzione per lavori a basso rischio, solo dopo attivazione
   esplicita e configurazione della credenziale nel meccanismo sicuro disponibile. I job devono
   minimizzare e redigere il contesto, rispettare budget locali e registrare provider, modello e
   motivo di eventuali fallback. Quota esaurita o errore sospendono/rinviamo il lavoro; mai passaggio
   automatico a un servizio a pagamento. Dati sensibili restano locali o richiedono un'autorizzazione
   separata.
4. **Brain locale.** Jarvis puo leggere e aggiornare autonomamente il proprio brain Markdown. Ogni
   scrittura automatica deve essere validata, tracciabile e annullabile; il testo originale, i fatti
   estratti e le inferenze restano distinguibili. Il Markdown rimane fonte autorevole e leggibile.
5. **Connettori.** Letture nei servizi configurati sono consentite. Azioni che producono effetti
   verso terzi o irreversibili — inviare messaggi/email, cancellare, acquistare, pubblicare o
   modificare permessi — richiedono conferma esplicita al momento dell'azione. Una notifica inviata
   all'utente stesso e consentita solo su un canale da lui abilitato.
6. **Ambiente iniziale.** Lo sviluppo e l'uso sintetico iniziano sul PC, con bind locale, fixture
   fittizie e senza account reali. L'accesso da telefono/remoto verra progettato in una fase
   successiva con autenticazione gia impostata e un canale remoto protetto; non si espone il server
   direttamente a Internet per comodita.

La fiducia si esprime con provider, connettori, dati e azioni autorizzati; la nazionalita del
fornitore da sola non e un confine tecnico sufficiente.

## Stato verificato nel repository

- Sono presenti auth obbligatoria anche su loopback, guard WebSocket Origin/Host, terminale opt-in,
  radici file configurabili e provider Groq. Le relative aree hanno test dedicati.
- `CLAUDE.md` contiene ancora regole che rendono `full` predefinito per loop e reminder e consentono
  azioni MCP esterne senza conferma. Queste istruzioni contraddicono la decisione 5 e vanno rimosse
  o sostituite; i gate devono essere verificati nel codice, non affidati al solo prompt.
- `docker-compose.yml` pubblica la porta su tutte le interfacce per default; l'uso PC iniziale deve
  limitare il bind host a loopback. La configurazione per accesso remoto va trattata separatamente.
- Non e ancora dimostrato un flusso end-to-end di triage personale, memoria strutturata, watcher e
  pianificatore giornaliero.

## Conseguenze e prossimi passi

- Allineare prompt, permessi effettivi e UI alla politica sopra; nessuna azione esterna diventa
  sicura solo perche compare in un connettore.
- Aggiungere un modulo personale isolato: schemi/fixture, triage idempotente con audit/undo, loop e
  watcher in sola proposta, quindi ranking deterministico per "Che faccio oggi?".
- Dimostrare i casi di accettazione su vault fittizio e senza rete prima di collegare una copia del
  vault reale o account reali.
- Per l'accesso mobile, completare prima i requisiti P0 e la verifica dell'esposizione dati ai
  provider; aggiungere una fonte esterna alla volta con permessi minimi.

## Alternative considerate

- **Usare qualunque provider configurato automaticamente:** rifiutato perche il routing potrebbe
  inviare contesto personale a un servizio non scelto per quel tipo di lavoro.
- **Confermare ogni modifica al brain:** rifiutato perche impedirebbe il ciclo personale; audit e
  annullamento rendono recuperabili le scritture locali.
- **Consentire subito tutte le azioni dei connettori:** rifiutato perche invii, cancellazioni,
  acquisti e pubblicazioni hanno conseguenze esterne.
