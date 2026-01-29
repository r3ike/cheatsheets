

## 1. Cosa sono i Design Pattern?

I **Design Pattern** (letteralmente "schemi di progettazione") sono soluzioni riutilizzabili a problemi comuni che si verificano durante la progettazione del software.

Non sono pezzi di codice che puoi copiare e incollare direttamente (come una libreria), ma piuttosto **modelli o descrizioni** di come risolvere un problema, che possono essere adattati a diverse situazioni.

### Perché sono importanti?

* **Linguaggio Comune:** Se un programmatore dice "Qui ho usato un *Observer*", tutti gli altri capiscono immediatamente la struttura del codice senza doverlo leggere riga per riga.
* **Best Practices:** Sono soluzioni collaudate da decenni di esperienza; ti evitano di "reinventare la ruota".
* **Manutenibilità:** Rendono il codice più modulare e facile da modificare.

Si dividono principalmente in tre categorie:

1. **Creazionali:** Riguardano la creazione di oggetti (es. Singleton, Factory).
2. **Strutturali:** Riguardano la composizione di classi e oggetti (es. Adapter).
3. **Comportamentali:** Riguardano la comunicazione tra oggetti (es. Observer, Strategy).

---

## 2. Il Design Pattern Singleton in C++

Il **Singleton** è un pattern *creazionale*.

### Come funziona (Il Concetto)

Il Singleton garantisce che una classe abbia **una e una sola istanza** e fornisce un **punto di accesso globale** a quella istanza.

### Perché usarlo?

Si usa quando è necessario che una risorsa sia condivisa e unica in tutto il programma:

* **Gestione della Configurazione:** Caricare le impostazioni da un file una volta sola e accedervi da ovunque.
* **Logging:** Un unico oggetto che scrive su un file di log per evitare conflitti di scrittura.
* **Connessione al Database:** Mantenere un pool di connessioni condiviso.

### Implementazione in C++ (Modern C++ / C++11 e successivi)

In C++, il modo migliore per implementare un Singleton è il cosiddetto **"Meyers Singleton"** (dal nome di Scott Meyers). È thread-safe (sicuro in ambienti multithread) dalla versione C++11 in poi ed è molto elegante.

Ecco il codice commentato:

```cpp
#include <iostream>

class Singleton {
public:
    // 1. Metodo statico per accedere all'unica istanza
    static Singleton& getInstance() {
        // La variabile 'instance' viene creata solo la prima volta 
        // che questo metodo viene chiamato. 
        // C++11 garantisce che questa inizializzazione sia thread-safe.
        static Singleton instance; 
        return instance;
    }

    // Esempio di metodo di business logic
    void log(const std::string& message) {
        std::cout << "[LOG]: " << message << std::endl;
    }

    // 2. Eliminare il Copy Constructor e l'Assignment Operator
    // Questo impedisce di creare copie del Singleton accidentalmente.
    Singleton(const Singleton&) = delete;
    void operator=(const Singleton&) = delete;

private:
    // 3. Costruttore Privato
    // Nessuno dall'esterno può chiamare "new Singleton()" o creare un oggetto.
    Singleton() {
        std::cout << "Singleton inizializzato!" << std::endl;
    }

    // Distruttore Privato (opzionale, ma buona prassi se non deve essere distrutto esplicitamente)
    ~Singleton() {
        std::cout << "Singleton distrutto!" << std::endl;
    }
};

int main() {
    // Non puoi fare: Singleton s; (Errore: costruttore privato)
    
    std::cout << "Inizio programma." << std::endl;

    // Prima chiamata: il Singleton viene creato qui
    Singleton& s1 = Singleton::getInstance();
    s1.log("Prima operazione");

    // Seconda chiamata: ritorna la STESSA istanza creata prima
    Singleton& s2 = Singleton::getInstance();
    s2.log("Seconda operazione");

    // Prova che sono lo stesso oggetto (stesso indirizzo di memoria)
    if (&s1 == &s2) {
        std::cout << "s1 e s2 sono la stessa istanza." << std::endl;
    }

    return 0;
}

```

### Avvertenze sul Singleton

Sebbene utile, il Singleton è spesso considerato un **"Anti-pattern"** se abusato, perché:

1. Introduce uno **stato globale** (difficile da debuggare).
2. Rende difficile lo **Unit Testing** (le classi che usano il Singleton sono strettamente accoppiate ad esso e difficili da "mockare").

---

## 3. Altri Design Pattern Famosi

Ecco tre degli altri pattern più diffusi e potenti:

### A. Factory Method (Creazionale)

Permette di creare oggetti senza specificare la classe esatta dell'oggetto che verrà creato.

* **Esempio:** Hai una classe base `Logistica`. Puoi avere sottoclassi `LogisticaViaTerra` che crea `Camion` e `LogisticaViaMare` che crea `Navi`. Il codice principale chiede solo un "mezzo di trasporto" senza sapere se riceverà un camion o una nave.
* **Vantaggio:** Disaccoppia la creazione dell'oggetto dal suo utilizzo.

### B. Observer (Comportamentale)

Definisce una dipendenza "uno-a-molti" tra oggetti. Quando un oggetto cambia stato, tutti i suoi dipendenti vengono notificati e aggiornati automaticamente.

* **Esempio:** Il sistema di iscrizioni di YouTube. Quando un canale (il *Subject*) pubblica un video, tutti gli iscritti (gli *Observers*) ricevono una notifica.
* **Vantaggio:** Ottimo per la programmazione ad eventi e interfacce grafiche (es. clicco un bottone e si aggiornano 3 pannelli diversi).

### C. Strategy (Comportamentale)

Permette di definire una famiglia di algoritmi, incapsularli e renderli intercambiabili a runtime.

* **Esempio:** Un navigatore GPS. Puoi scegliere la strategia "Percorso più veloce", "Percorso più breve" o "Evita autostrade". L'input (punto A e punto B) è lo stesso, ma l'algoritmo di calcolo (la *Strategy*) cambia in base alla scelta dell'utente.
* **Vantaggio:** Elimina lunghe catene di `if...else` o `switch` complessi.

