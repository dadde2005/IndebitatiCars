# IndebitatiCars

Web app per tenere traccia di chi guida quando si esce la sera a Milano:
per ogni data si segna **chi ha guidato** e **quali persone ha portato**, e la
pagina **Resoconti** mostra per ognuno quante volte ha preso la macchina.

Funziona da telefono e da computer, senza installare niente: è una singola
pagina (`index.html`).

## Cosa fa

- **Uscite**: scegli la data, tocca chi ha guidato e chi è salito in macchina
  (con i 4 posti passeggero disegnati), aggiungi una nota se vuoi. Lo storico
  è diviso per mese e ogni uscita si può modificare o eliminare. Se la stessa
  sera escono due macchine, basta registrare due uscite con la stessa data.
- **Persone**: aggiungi, rinomina o archivia i membri del gruppo. Chi viene
  archiviato sparisce dalla scelta rapida ma il suo storico resta.
- **Resoconti**: classifica di chi ha guidato di più (filtrabile per anno), con
  persone portate e passaggi ricevuti. Toccando un nome vedi chi ha portato e
  da chi si è fatto portare. In alto c'è il suggerimento su chi dovrebbe
  guidare la prossima volta (chi ha guidato meno rispetto alle volte che è
  uscito).

## Dove vengono salvati i dati

L'app sceglie da sola il primo sistema disponibile:

1. **Firebase** (consigliato per usarla in gruppo): se in `config.js` c'è una
   configurazione Firebase, i dati sono condivisi e sincronizzati in tempo
   reale su tutti i dispositivi.
2. **Artifact di claude.ai**: se la pagina è aperta come artifact su claude.ai
   usa il database condiviso dell'artifact.
3. **Solo questo dispositivo**: altrimenti i dati restano nel browser in uso
   (l'etichetta in alto a destra lo indica).

## Metterla online (GitHub Pages + Firebase, gratis)

### 1. Crea il database Firebase

1. Vai su <https://console.firebase.google.com> e crea un progetto
   (Google Analytics non serve).
2. **Build → Authentication → Inizia → Sign-in method**: abilita **Anonimo**.
3. **Build → Firestore Database → Crea database** (modalità produzione, regione
   `eur3` o `europe-west`).
4. Nella scheda **Regole** di Firestore incolla il contenuto di
   [`firestore.rules`](firestore.rules) e pubblica.
5. **Impostazioni progetto → Le tue app → Web (`</>`)**: registra un'app e
   copia l'oggetto `firebaseConfig`.
6. Incollalo in [`config.js`](config.js) al posto di `firebase: null`:

   ```js
   firebase: {
     apiKey: "AIza...",
     authDomain: "tuo-progetto.firebaseapp.com",
     projectId: "tuo-progetto",
     appId: "1:...:web:...",
   },
   ```

   Questa configurazione non è una password: è normale che sia pubblica.
   L'accesso ai dati è regolato dalle regole di Firestore.

### 2. Pubblica il sito

1. Unisci questo codice nel branch `main`.
2. Su GitHub: **Settings → Pages → Build and deployment → Source: GitHub
   Actions**.
3. Il workflow [`pages.yml`](.github/workflows/pages.yml) pubblica il sito a
   ogni push su `main`, all'indirizzo
   `https://<tuo-utente>.github.io/IndebitatiCars/`.
4. Manda il link agli amici. Dal telefono si può aggiungere alla schermata
   Home (Safari: Condividi → Aggiungi a Home; Chrome: menu → Aggiungi a
   schermata Home).

Chi ha il link può leggere e modificare i dati: condividetelo solo nel gruppo.

## Provarla in locale

```sh
python3 -m http.server 8000
```

e apri <http://localhost:8000>.
