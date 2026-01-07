# Mongoose
---

## 1. Configurazione e Connessione

Il punto di partenza per collegare la tua applicazione al database.

```javascript
const mongoose = require('mongoose');

// Connessione al database (Best Practice: usa async/await)
const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGO_URI);
    console.log(`MongoDB Connesso: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Errore: ${error.message}`);
    process.exit(1);
  }
};

```

---

## 2. Definizione Schema e Model

Definisci la struttura dei tuoi dati.

```javascript
const utenteSchema = new mongoose.Schema({
  nome: { 
    type: String, 
    required: [true, 'Il nome è obbligatorio'], // Messaggio errore custom
    trim: true 
  },
  email: { 
    type: String, 
    unique: true, 
    lowercase: true 
  },
  eta: { 
    type: Number, 
    min: 18, 
    default: 18 
  },
  ruolo: {
    type: String,
    enum: ['user', 'admin'], // Valori permessi
    default: 'user'
  },
  dataCreazione: { 
    type: Date, 
    default: Date.now 
  },
  // Riferimento a un altro modello (Relazione)
  articoli: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Articolo' }]
}, {
  timestamps: true // Aggiunge automaticamente createdAt e updatedAt
});

// Creazione del Modello
const Utente = mongoose.model('Utente', utenteSchema);

```

---

## 3. CRUD: Create (Creazione)

Metodi per inserire documenti nel database.

* **`save()`**: Metodo dell'istanza (più flessibile se devi manipolare i dati prima).
* **`create()`**: Metodo del modello (più diretto).
* **`insertMany()`**: Per inserire array di documenti (performante).

```javascript
// Metodo 1: Create (Shortcut)
const nuovoUtente = await Utente.create({ nome: 'Mario', email: 'mario@test.com' });

// Metodo 2: Save (Istanza)
const utente = new Utente({ nome: 'Luigi', email: 'luigi@test.com' });
// ... puoi fare modifiche qui ...
await utente.save();

// Metodo 3: InsertMany (Bulk)
await Utente.insertMany([{ nome: 'A' }, { nome: 'B' }]);

```

---

## 4. CRUD: Read (Lettura)

Metodi per trovare documenti. Restituiscono una Query che può essere concatenata.

### Metodi Base

* **`findById(id)`**: Cerca per `_id` (veloce e comune).
* **`findOne({ ... })`**: Restituisce il *primo* documento che soddisfa i criteri.
* **`find({ ... })`**: Restituisce un *array* di documenti.

```javascript
// Trova tutti
const utenti = await Utente.find();

// Trova per ID
const utente = await Utente.findById('64f8a...');

// Trova con filtri specifici
const admin = await Utente.findOne({ ruolo: 'admin' });

```

### Query Builders (Concatenazione)

Puoi filtrare, ordinare e limitare i risultati.

```javascript
const risultati = await Utente.find({ eta: { $gte: 18 } }) // Età >= 18
  .select('nome email')    // Seleziona solo questi campi (proiezione)
  .sort({ dataCreazione: -1 }) // Ordine decrescente (più recente)
  .limit(10)               // Prendi solo i primi 10
  .skip(5);                // Salta i primi 5 (paginazione)

```

### Operatori di Query Comuni

| Operatore | Descrizione | Esempio Sintassi |
| --- | --- | --- |
| `$eq` / `$ne` | Uguale / Non Uguale | `{ eta: { $ne: 18 } }` |
| `$gt` / `$gte` | Maggiore / Maggiore o Uguale | `{ eta: { $gte: 21 } }` |
| `$lt` / `$lte` | Minore / Minore o Uguale | `{ eta: { $lt: 65 } }` |
| `$in` / `$nin` | Incluso in / Non in array | `{ ruolo: { $in: ['admin', 'mod'] } }` |
| `$or` / `$and` | Logica OR / AND | `{ $or: [{eta: 18}, {ruolo: 'admin'}] }` |

---

## 5. CRUD: Update (Aggiornamento)

> **Nota:** `findByIdAndUpdate` è spesso preferito per la sua semplicità. Ricorda `{ new: true }` per ottenere il documento aggiornato, non quello vecchio.

* **`updateOne()`**: Aggiorna il primo trovato (non restituisce il doc).
* **`findByIdAndUpdate(id, update, options)`**: Trova per ID e aggiorna.
* **`findOneAndUpdate(filter, update, options)`**: Trova per filtro e aggiorna.

```javascript
// Aggiorna e restituisci il NUOVO documento
const utenteAggiornato = await Utente.findByIdAndUpdate(
  id,
  { nome: 'Nuovo Nome', $inc: { eta: 1 } }, // $inc incrementa un numero
  { 
    new: true,           // IMPORTANTE: Restituisce il dato aggiornato
    runValidators: true  // Esegue le validazioni dello schema anche nell'update
  }
);

```

---

## 6. CRUD: Delete (Cancellazione)

* **`deleteOne()`**: Cancella il primo trovato.
* **`deleteMany()`**: Cancella tutti quelli che corrispondono al filtro.
* **`findByIdAndDelete(id)`**: Trova per ID, cancella e restituisce il documento cancellato.

```javascript
// Cancella per ID
await Utente.findByIdAndDelete(id);

// Cancella tutti gli utenti inattivi
await Utente.deleteMany({ attivo: false });

```

---

## 7. Relazioni e Population

Per collegare dati tra diverse collezioni (simile alle JOIN in SQL).

```javascript
// Nello schema (già definito sopra):
// articoli: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Articolo' }]

// Nella query:
const utenteConArticoli = await Utente.findById(id)
  .populate('articoli') // Sostituisce gli ID con i veri documenti Articolo
  .exec();

// Populate selettivo (solo titolo dell'articolo)
await Utente.find().populate('articoli', 'titolo -_id'); 

```

---

## 8. Middleware (Hooks)

Funzioni che vengono eseguite *prima* (`pre`) o *dopo* (`post`) certi eventi (es. salvataggio).

```javascript
// Esempio: Hash della password prima di salvare
utenteSchema.pre('save', async function(next) {
  // 'this' si riferisce al documento che sta per essere salvato
  if (!this.isModified('password')) {
    next();
  }
  this.password = await hashPassword(this.password);
  next(); // Procedi al salvataggio
});

```

---

## 9. Metodi Custom e Virtuals

Estendi le funzionalità del tuo modello.

```javascript
// Virtuals: Proprietà che non vengono salvate nel DB ma calcolate al volo
utenteSchema.virtual('nomeCompleto').get(function() {
  return `${this.nome} ${this.cognome}`;
});

// Methods: Funzioni su una singola istanza (es. verifica password)
utenteSchema.methods.verificaPassword = async function(pswInserita) {
  return await bcrypt.compare(pswInserita, this.password);
};

// Statics: Funzioni sul modello intero (es. metodo custom di ricerca)
utenteSchema.statics.trovaVIP = function() {
  return this.find({ ruolo: 'vip' });
};

```

---

Certamente! Ecco un **Mongoose Cheatsheet** completo, organizzato per categorie logiche per facilitare la consultazione. Include i metodi più utilizzati per lo sviluppo moderno con Node.js e MongoDB.

---

## 1. Configurazione e Connessione

Il punto di partenza per collegare la tua applicazione al database.

```javascript
const mongoose = require('mongoose');

// Connessione al database (Best Practice: usa async/await)
const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGO_URI);
    console.log(`MongoDB Connesso: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Errore: ${error.message}`);
    process.exit(1);
  }
};

```

---

## 2. Definizione Schema e Model

Definisci la struttura dei tuoi dati.

```javascript
const utenteSchema = new mongoose.Schema({
  nome: { 
    type: String, 
    required: [true, 'Il nome è obbligatorio'], // Messaggio errore custom
    trim: true 
  },
  email: { 
    type: String, 
    unique: true, 
    lowercase: true 
  },
  eta: { 
    type: Number, 
    min: 18, 
    default: 18 
  },
  ruolo: {
    type: String,
    enum: ['user', 'admin'], // Valori permessi
    default: 'user'
  },
  dataCreazione: { 
    type: Date, 
    default: Date.now 
  },
  // Riferimento a un altro modello (Relazione)
  articoli: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Articolo' }]
}, {
  timestamps: true // Aggiunge automaticamente createdAt e updatedAt
});

// Creazione del Modello
const Utente = mongoose.model('Utente', utenteSchema);

```

---

## 3. CRUD: Create (Creazione)

Metodi per inserire documenti nel database.

* **`save()`**: Metodo dell'istanza (più flessibile se devi manipolare i dati prima).
* **`create()`**: Metodo del modello (più diretto).
* **`insertMany()`**: Per inserire array di documenti (performante).

```javascript
// Metodo 1: Create (Shortcut)
const nuovoUtente = await Utente.create({ nome: 'Mario', email: 'mario@test.com' });

// Metodo 2: Save (Istanza)
const utente = new Utente({ nome: 'Luigi', email: 'luigi@test.com' });
// ... puoi fare modifiche qui ...
await utente.save();

// Metodo 3: InsertMany (Bulk)
await Utente.insertMany([{ nome: 'A' }, { nome: 'B' }]);

```

---

## 4. CRUD: Read (Lettura)

Metodi per trovare documenti. Restituiscono una Query che può essere concatenata.

### Metodi Base

* **`findById(id)`**: Cerca per `_id` (veloce e comune).
* **`findOne({ ... })`**: Restituisce il *primo* documento che soddisfa i criteri.
* **`find({ ... })`**: Restituisce un *array* di documenti.

```javascript
// Trova tutti
const utenti = await Utente.find();

// Trova per ID
const utente = await Utente.findById('64f8a...');

// Trova con filtri specifici
const admin = await Utente.findOne({ ruolo: 'admin' });

```

### Query Builders (Concatenazione)

Puoi filtrare, ordinare e limitare i risultati.

```javascript
const risultati = await Utente.find({ eta: { $gte: 18 } }) // Età >= 18
  .select('nome email')    // Seleziona solo questi campi (proiezione)
  .sort({ dataCreazione: -1 }) // Ordine decrescente (più recente)
  .limit(10)               // Prendi solo i primi 10
  .skip(5);                // Salta i primi 5 (paginazione)

```

### Operatori di Query Comuni

| Operatore | Descrizione | Esempio Sintassi |
| --- | --- | --- |
| `$eq` / `$ne` | Uguale / Non Uguale | `{ eta: { $ne: 18 } }` |
| `$gt` / `$gte` | Maggiore / Maggiore o Uguale | `{ eta: { $gte: 21 } }` |
| `$lt` / `$lte` | Minore / Minore o Uguale | `{ eta: { $lt: 65 } }` |
| `$in` / `$nin` | Incluso in / Non in array | `{ ruolo: { $in: ['admin', 'mod'] } }` |
| `$or` / `$and` | Logica OR / AND | `{ $or: [{eta: 18}, {ruolo: 'admin'}] }` |

---

## 5. CRUD: Update (Aggiornamento)

> **Nota:** `findByIdAndUpdate` è spesso preferito per la sua semplicità. Ricorda `{ new: true }` per ottenere il documento aggiornato, non quello vecchio.

* **`updateOne()`**: Aggiorna il primo trovato (non restituisce il doc).
* **`findByIdAndUpdate(id, update, options)`**: Trova per ID e aggiorna.
* **`findOneAndUpdate(filter, update, options)`**: Trova per filtro e aggiorna.

```javascript
// Aggiorna e restituisci il NUOVO documento
const utenteAggiornato = await Utente.findByIdAndUpdate(
  id,
  { nome: 'Nuovo Nome', $inc: { eta: 1 } }, // $inc incrementa un numero
  { 
    new: true,           // IMPORTANTE: Restituisce il dato aggiornato
    runValidators: true  // Esegue le validazioni dello schema anche nell'update
  }
);

```

---

## 6. CRUD: Delete (Cancellazione)

* **`deleteOne()`**: Cancella il primo trovato.
* **`deleteMany()`**: Cancella tutti quelli che corrispondono al filtro.
* **`findByIdAndDelete(id)`**: Trova per ID, cancella e restituisce il documento cancellato.

```javascript
// Cancella per ID
await Utente.findByIdAndDelete(id);

// Cancella tutti gli utenti inattivi
await Utente.deleteMany({ attivo: false });

```

---

## 7. Relazioni e Population

Per collegare dati tra diverse collezioni (simile alle JOIN in SQL).

```javascript
// Nello schema (già definito sopra):
// articoli: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Articolo' }]

// Nella query:
const utenteConArticoli = await Utente.findById(id)
  .populate('articoli') // Sostituisce gli ID con i veri documenti Articolo
  .exec();

// Populate selettivo (solo titolo dell'articolo)
await Utente.find().populate('articoli', 'titolo -_id'); 

```

---

## 8. Middleware (Hooks)

Funzioni che vengono eseguite *prima* (`pre`) o *dopo* (`post`) certi eventi (es. salvataggio).

```javascript
// Esempio: Hash della password prima di salvare
utenteSchema.pre('save', async function(next) {
  // 'this' si riferisce al documento che sta per essere salvato
  if (!this.isModified('password')) {
    next();
  }
  this.password = await hashPassword(this.password);
  next(); // Procedi al salvataggio
});

```

---

## 9. Metodi Custom e Virtuals

Estendi le funzionalità del tuo modello.

```javascript
// Virtuals: Proprietà che non vengono salvate nel DB ma calcolate al volo
utenteSchema.virtual('nomeCompleto').get(function() {
  return `${this.nome} ${this.cognome}`;
});

// Methods: Funzioni su una singola istanza (es. verifica password)
utenteSchema.methods.verificaPassword = async function(pswInserita) {
  return await bcrypt.compare(pswInserita, this.password);
};

// Statics: Funzioni sul modello intero (es. metodo custom di ricerca)
utenteSchema.statics.trovaVIP = function() {
  return this.find({ ruolo: 'vip' });
};

```

---
Certamente. Gli indici sono fondamentali per le performance di un database, specialmente quando i dati crescono. Ecco la sezione dedicata da aggiungere al cheatsheet.

---

## 10. Indici (Indexes)

Gli indici permettono a MongoDB di trovare i documenti in modo efficiente senza dover scansionare l'intera collezione (Collection Scan). Si definiscono solitamente nello Schema.

### Definizione nello Schema

Esistono due modi principali per definire un indice: a livello di campo (per indici semplici) o a livello di schema (per indici composti o configurazioni avanzate).

```javascript
const prodottoSchema = new mongoose.Schema({
  nome: { 
    type: String, 
    index: true // 1. Indice semplice (velocizza le ricerche per nome)
  },
  codiceUnivoco: { 
    type: String, 
    unique: true // 2. Indice Univoco (previene duplicati)
  },
  categoria: String,
  prezzo: Number,
  descrizione: String,
  dataScadenza: Date
});

// 3. Indice Composto (Compound Index)
// Ottimo per query che filtrano/ordinano su ENTRAMBI i campi (es. trova categoria E ordina per prezzo)
// 1 = Ascendente, -1 = Discendente
prodottoSchema.index({ categoria: 1, prezzo: -1 });

// 4. Indice Testuale (Text Index)
// Permette la ricerca full-text su stringhe (es. "motore di ricerca" interno)
prodottoSchema.index({ descrizione: 'text', nome: 'text' });

```

### Tipi Speciali di Indici

* **TTL (Time To Live):** Cancella automaticamente i documenti dopo un certo tempo (utile per log, sessioni, cache).
```javascript
// Il documento verrà cancellato 3600 secondi (1 ora) dopo la 'dataScadenza'
prodottoSchema.index({ dataScadenza: 1 }, { expireAfterSeconds: 3600 });

```


* **Sparse Index:** Indicizza solo i documenti che *hanno* quel campo (utile se il campo è opzionale e vuoi risparmiare spazio nell'indice).
```javascript
prodottoSchema.index({ emailSecondaria: 1 }, { sparse: true });

```



---

## 11. Utilizzo degli Indici nelle Query

Una volta definiti, Mongoose li usa automaticamente per `find`, `findOne`, ecc. Tuttavia, per la ricerca testuale (`text index`), devi usare una sintassi specifica.

```javascript
// Esempio di Ricerca Full-Text (richiede l'indice 'text' definito sopra)
const risultati = await Prodotto.find({ 
  $text: { $search: "smartphone samsung" } 
});

// Ordinare per rilevanza (score) nella ricerca testuale
const risultatiRilevanti = await Prodotto.find(
  { $text: { $search: "smartphone" } },
  { score: { $meta: "textScore" } } // Proiezione del punteggio
).sort(
  { score: { $meta: "textScore" } } // Ordina per punteggio
);

```

---

## 12. Debugging Performance (Explain)

Come fai a sapere se la tua query sta usando un indice o sta facendo una scansione lenta? Usa `.explain()`.

```javascript
// Restituisce statistiche sull'esecuzione invece dei dati
const stats = await Utente.find({ nome: 'Mario' }).explain('executionStats');

console.log(stats.executionStats.totalDocsExamined); 
// Se totalDocsExamined è molto alto ma nReturned è basso, ti manca un indice!

```
Ottima idea. Lavorare con gli array (liste) è una delle operazioni più frequenti in MongoDB. Invece di scaricare l'intero array, modificarlo in JavaScript e risalvarlo (rischiando conflitti), è **molto più efficiente** usare gli operatori atomici di aggiornamento.

Ecco la sezione da aggiungere al cheatsheet.

---

## 13. Manipolazione di Array (Liste)

Questi operatori si usano all'interno del parametro `update` (in metodi come `updateOne`, `findByIdAndUpdate`).

### Aggiungere Elementi

* **`$push`**: Aggiunge un elemento in coda all'array. Permette duplicati.
* **`$addToSet`**: Aggiunge un elemento **solo se non esiste già**. Utile per creare liste univoche (come i tag o gli ID di follower).

```javascript
// 1. $push: Aggiunge 'javascript' anche se c'è già
await Utente.findByIdAndUpdate(id, {
  $push: { interessi: 'javascript' }
});

// 2. $addToSet: Aggiunge 'javascript' SOLO se non è presente
await Utente.findByIdAndUpdate(id, {
  $addToSet: { interessi: 'javascript' }
});

// BONUS: Aggiungere più elementi in una volta ($each)
await Utente.findByIdAndUpdate(id, {
  $addToSet: { interessi: { $each: ['mongodb', 'nodejs', 'react'] } }
});

```

### Rimuovere Elementi

* **`$pull`**: Rimuove **tutte** le istanze di un valore o gli elementi che soddisfano una condizione.
* **`$pop`**: Rimuove l'ultimo elemento (`1`) o il primo (`-1`) dell'array.

```javascript
// 1. $pull (Array Semplice): Rimuove la stringa 'java' dall'array
await Utente.findByIdAndUpdate(id, {
  $pull: { interessi: 'java' }
});

// 2. $pull (Array di Oggetti): Rimuove l'oggetto che ha quel specifico ID
// Immagina: commenti: [{ _id: 1, text: 'A' }, { _id: 2, text: 'B' }]
await Post.findByIdAndUpdate(postId, {
  $pull: { commenti: { _id: 'commentoId_da_rimuovere' } }
});

// 3. $pop: Rimuove l'ultimo elemento della lista
await Utente.findByIdAndUpdate(id, {
  $pop: { interessi: 1 }
});

```

### Modificare un Elemento specifico (`$`)

A volte vuoi aggiornare un campo *dentro* un oggetto che si trova *dentro* un array, senza conoscere l'indice (0, 1, 2...). Si usa l'operatore posizionale `$`.

```javascript
// Scenario: Abbiamo un array 'carrello' con oggetti { prodottoId: '...', quantita: 1 }
// Vogliamo aggiornare la quantità del prodotto X a 5.

await Utente.updateOne(
  { _id: userId, 'carrello.prodottoId': 'prod_123' }, // 1. TROVA l'utente E l'elemento dell'array
  { 
    $set: { 'carrello.$.quantita': 5 } // 2. Il '$' rappresenta l'indice dell'elemento trovato
  }
);

```

---

### Riepilogo rapido Array

| Operatore | Funzione | Simile JS |
| --- | --- | --- |
| `$push` | Aggiunge (con duplicati) | `.push()` |
| `$addToSet` | Aggiunge (no duplicati) | `Set.add()` |
| `$pull` | Rimuove (tramite filtro) | `.filter()` |
| `$pop` | Rimuove estremità | `.pop()` / `.shift()` |
| `$` | Placeholder indice trovato | `arr[i] = ...` |